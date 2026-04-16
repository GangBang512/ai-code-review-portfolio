# Sample: C# Evaluation (Correction Task)

## Objective
Identify the critical flaws in an AI's response to a C# debugging prompt, explain why the model's fix is dangerous, and provide the correct canonical fix.

## The Prompt
> "My web API method throws exceptions under load. Also, I noticed memory usage spiking over time. What's wrong?"
> ```csharp
> [HttpPost("process")]
> public async void ProcessData([FromBody] DataPayload payload)
> {
>     var dataStream = new MemoryStream(payload.Bytes);
>     var processor = new LegacyDataProcessor(dataStream);
>     await processor.RunAsync();
> }
> ```

---

## The AI's Flawed Fix

> *AI Response:* "The memory leak happens because you aren't waiting for the processor to finish correctly in a web context. Also `DataPayload` needs to be cleared. Fix it like this:"
> ```csharp
> [HttpPost("process")]
> public async Task ProcessData([FromBody] DataPayload payload)
> {
>     var dataStream = new MemoryStream(payload.Bytes);
>     var processor = new LegacyDataProcessor(dataStream);
>     await processor.RunAsync().ConfigureAwait(false);
>     payload.Bytes = null; // Clear memory
> }
> ```

---

## My Correction & Justification

The AI model identified one major structural issue (changing `async void` to `async Task`) but completely missed the **Resource Leak**, and hallucinated a false memory fix (`payload.Bytes = null`).

### Flaw 1: Failure to Implement `IDisposable` Boundaries (The True Memory Leak)
*   **Correction Need:** The `MemoryStream` object implements `IDisposable`. The web server will eventually garbage collect it, but unmanaged backing resources or large byte arrays in the Large Object Heap (LOH) require deterministic disposal. 
*   **The Problem:** The AI completely ignored the streams. Over thousands of requests, this leads to LOH fragmentation and high memory baseline.
*   **Correct Fix:** Implement a `using` statement for the stream.

### Flaw 2: Hallucinated Memory Fix
*   **Correction Need:** Setting `payload.Bytes = null` is counterproductive in C#. It does very little to invoke immediate garbage collection and serves as "voodoo programming."

### My Provided Canonical Fix (The Correct Output)

```csharp
[HttpPost("process")]
public async Task<IActionResult> ProcessData([FromBody] DataPayload payload)
{
    // 1. Using declaration for deterministic disposal of IDisposable stream
    using var dataStream = new MemoryStream(payload.Bytes);
    
    var processor = new LegacyDataProcessor(dataStream);
    
    // 2. Await task properly
    await processor.RunAsync();
    
    // 3. Return explicit HTTP codes
    return Ok(); 
}
```

### Evaluation Implication
The original AI response actively misled the user regarding how Garbage Collection and resource disposal work in .NET by trying to manually nullify fields instead of prioritizing the `IDisposable` pattern. Models must strongly favor native framework constructs (`using`) for resource management.

Welcome! this is a WIP guide to tune Java for maximum performance in Minecraft.

Discord for questions and such: https://discord.gg/zeFSR9PnUw, also feel free to make an issue.

> Note: While these flags are easy to copy-paste and forget, they are no substitute for clearing laggy things out with mods like Spark

<br />

# Picking a Java Distributor
Java runtimes from Azul or Microsoft, Adoptium, Amazon and so on are all basically identical as they are based on OpenJDK. Some notable exceptions are:

- **GraalVM**  - features a more aggressive Java compiler.

- **Clear Linux OpenJDK**  - uses the same code as any other OpenJDK (making it highly compatible), but the build process itself and the dependencies are [optimized for newer CPUs](https://www.phoronix.com/review/zen4-clear-linux/2).

- **Platform Prime** - *very* fast since it hooks into llvm, but is currently incompatible with most mods and Linux-only.

- **Red Hat Java 8** - has the Shenandoah garbage collector (It is the only Java 8 with it).

- **OpenJ9** - Consumes less memory at the cost of being *much* slower in Minecraft. It also uses totally different flags than any other Java build. This option is unrecommended

If you dont know what to pick, I recommend GraalVM or Adoptium.

<br />

# Picking the correct Java version
While any Java Distributor will work just fine, you may run into compatibility issues depending on the java version you use. Refer to the following table to choose the correct version:

| Minecraft Version: | Vanilla           | Modded             |
|--------------------|-------------------|--------------------|
| 1.20.5 and above   | Requires Java 21+ | Requires Java 21+  |
| 1.17 to 1.20.4     | Requires Java 17+ | Requires Java 17+  |
| 1.16               | Requires Java 8+  | May Require Java 8 |
| 1.15 and below     | Only Java 8       | Only Java 8        |

# Memory Allocation
Minimum and maximum memory flags (`-xms` and `-xmx`) should be set to the same value as explained here: https://dzone.com/articles/benefits-of-setting-initial-and-maximum-memory-siz with one caveat: if you are on a low-memory system, and Minecraft takes up almost all your RAM, set your minimum memory below your maximum memory to conserve as much as possible.

Sizes are set in megabytes or gigabytes so `-Xms4096M` or `-Xmx8G` are both correct.

> Note: Allocating too much memory can break garbage collection or just slow Minecraft down, even if you have plenty to spare. Allocating too little can also slow down or break the game. Keep a close eye on Task manager (or your DE's system monitor) as Minecraft is running, and allocate only as much as it needs (which is usually less than 8G). `sparkc gcmonitor` will tell you if your allocation is too high (the pauses will be too long) or too low (frequent GC with a low memory warning in the notification).

> Note: If you are using a third-party Minecraft launcher like Prism Launcher or ATLauncher, you shouldn't use memory arguments and instead control memory through the dedicated section in the launcher.

<br/>

# Base Java Flags
These are the majority of the flags. The flags below will work for any ***OpenJDK*** 11+ build. They are the same on both servers and clients:

```
-XX:+UnlockExperimentalVMOptions -XX:+UnlockDiagnosticVMOptions -XX:+AlwaysActAsServerClassMachine -XX:+AlwaysPreTouch -XX:+DisableExplicitGC -XX:NmethodSweepActivity=1 -XX:ReservedCodeCacheSize=400M -XX:NonNMethodCodeHeapSize=12M -XX:ProfiledCodeHeapSize=194M -XX:NonProfiledCodeHeapSize=194M -XX:-DontCompileHugeMethods -XX:MaxNodeLimit=240000 -XX:NodeLimitFudgeFactor=8000 -XX:+UseVectorCmov -XX:+PerfDisableSharedMem -XX:+UseFastUnorderedTimeStamps -XX:+UseCriticalJavaThreadPriority -XX:ThreadPriorityPolicy=1 -XX:AllocatePrefetchStyle=3
```

For GraalVM Java 17+, use the flags below instead:
```
-XX:+UnlockExperimentalVMOptions -XX:+UnlockDiagnosticVMOptions -XX:+AlwaysActAsServerClassMachine -XX:+AlwaysPreTouch -XX:+DisableExplicitGC -XX:AllocatePrefetchStyle=3 -XX:NmethodSweepActivity=1 -XX:ReservedCodeCacheSize=400M -XX:NonNMethodCodeHeapSize=12M -XX:ProfiledCodeHeapSize=194M -XX:NonProfiledCodeHeapSize=194M -XX:-DontCompileHugeMethods -XX:+PerfDisableSharedMem -XX:+UseFastUnorderedTimeStamps -XX:+UseCriticalJavaThreadPriority -XX:+EagerJVMCI -Dgraal.TuneInlinerExploration=1
```
And for Platform Prime, usually no tuning with special flags is necessary, Large Pages are still applicable though.

> Note: I *hightly* recommend not using Java 8 unless it is neccesary, but if it is, these flags will work with OpenJDK 8:
> ```
> -XX:+UnlockExperimentalVMOptions -XX:+UnlockDiagnosticVMOptions -XX:+AlwaysActAsServerClassMachine -XX:+ParallelRefProcEnabled -XX:+DisableExplicitGC -XX:+AlwaysPreTouch -XX:+PerfDisableSharedMem -XX:+AggressiveOpts -XX:+UseFastAccessorMethods -XX:MaxInlineLevel=15 -XX:MaxVectorSize=32 -XX:+UseCompressedOops -XX:ThreadPriorityPolicy=1 -XX:+UseDynamicNumberOfGCThreads -XX:NmethodSweepActivity=1 -XX:ReservedCodeCacheSize=350M -XX:-DontCompileHugeMethods -XX:MaxNodeLimit=240000 -XX:NodeLimitFudgeFactor=8000 -XX:+UseFPUForSpilling
> ```

> Note: You can also get Java 8 versions of GraalVM EE from the [21.X section on the Oracle site](https://www.oracle.com/downloads/graalvm-downloads.html), for that, use these arguments instead:
> ```
> -XX:+UnlockExperimentalVMOptions -XX:+UnlockDiagnosticVMOptions -XX:+AlwaysActAsServerClassMachine -XX:+ParallelRefProcEnabled -XX:+DisableExplicitGC -XX:+AlwaysPreTouch -XX:+AggressiveOpts -XX:+UseFastAccessorMethods -XX:AllocatePrefetchStyle=1 -XX:ThreadPriorityPolicy=1 -XX:+UseDynamicNumberOfGCThreads -XX:NmethodSweepActivity=1 -XX:ReservedCodeCacheSize=350M -XX:-DontCompileHugeMethods -XX:MaxNodeLimit=240000 -XX:NodeLimitFudgeFactor=8000 -XX:+UseFPUForSpilling -XX:+EnableJVMCI -XX:+UseJVMCICompiler -XX:+EagerJVMCI -Dgraal.TuneInlinerExploration=1 -Dgraal.CompilerConfiguration=enterprise -Dgraal.UsePriorityInlining=true -Dgraal.Vectorization=true -Dgraal.OptDuplication=true -Dgraal.DetectInvertedLoopsAsCounted=true -Dgraal.LoopInversion=true -Dgraal.VectorizeHashes=true -Dgraal.EnterprisePartialUnroll=true -Dgraal.VectorizeSIMD=true -Dgraal.StripMineNonCountedLoops=true -Dgraal.SpeculativeGuardMovement=true -Dgraal.InfeasiblePathCorrelation=true
> ```
> Be sure to set `-Dgraal.VectorizeSIMD` to `false` if you run shaders.

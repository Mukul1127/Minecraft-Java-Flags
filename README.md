<div align="center">
  <a href="https://github.com/kyechan99/capsule-render">
    <img src="https://capsule-render.vercel.app/api?type=waving&height=250&color=timeGradient&text=Minecraft%20Java%20Flags&fontAlignY=40&animation=fadeIn">
  </a>
</div>

<br>

Welcome! this is a guide to tune Java for Minecraft.

> [!IMPORTANT]  
> Most of these flags seem to be snake-oil, more information here: https://garden.unascribed.com/jvm-args/
> 
> Community testing would be appreciated to confirm this, especially on lower-end hardware.

> [!NOTE]
> 1. While these tweaks notably reduce some server and client stutters, expect only modest TPS gains + minimal FPS gains at best, and somewhat increased RAM + CPU usage.
> 
> 2. While these flags are easy to copy-paste and forget, they are no substitute for clearing laggy things out with mods like Spark.

<br>

# Performance Mods
You might be thinking ***"This is weird, why are performance mods here?"*** but performance mods will increase your performance more than any Java flags could, but if you want pure performance, don't just use Optifine. There are better performing mods like [Sodium](https://modrinth.com/mod/sodium). Sodium also supports Fabric natively (unlike Optifine) and 0.6 supports NeoForge aswell.

For pure performance, you can take a look at [Simply Optimized](https://modrinth.com/modpack/sop) and [Adrenaline](https://modrinth.com/modpack/adrenaline) for examples.

If you want a up-to-date Optifine replacement on the other hand, [Fabulously Optimized](https://modrinth.com/modpack/fabulously-optimized) and [Additive](https://modrinth.com/modpack/additive) both mimic it very well with better performance and customizability.

<br/>

# Picking a Java Distributor
Java runtimes from Azul or Microsoft, Adoptium, Amazon and so on are all basically identical as they are based on OpenJDK. Some notable exceptions are:

1. **GraalVM**  - features a more aggressive Java compiler.

2. **Clear Linux OpenJDK**  - uses a highly compatible OpenJDK base but it's build process and dependencies are [optimized for newer CPUs](https://www.phoronix.com/review/zen4-clear-linux/2).

3. **Platform Prime** - ***very*** fast since it hooks into llvm, but is currently incompatible with most mods and Linux-only.

4. **Red Hat Java 8** - has the Shenandoah garbage collector (It is the only Java 8 JDK with it).

5. **OpenJ9** - consumes less memory at the cost of being *much* slower in Minecraft. It also uses totally different flags than any other Java build.

If you dont know what to pick, I recommend GraalVM or Adoptium. You can download Adoptium from [here](https://adoptium.net/temurin/releases), and you can download GraalVM from [here](https://www.graalvm.org/downloads).

<br>

# Picking the correct Java version
While any Java Distributor will work just fine, you should choose the correct java version for your game version. You can check the correct version using the [Minecraft Wiki](https://minecraft.wiki). Just search your game version with "Java Edition" at the beginning and check the "Minimum Java Version".

Example:

![Java Version](javaVersion.png)

<br>

# Memory Allocation
Minimum and maximum memory flags (`-xms` and `-xmx`) should be set to the same value as explained [here](https://dzone.com/articles/benefits-of-setting-initial-and-maximum-memory-siz). They are set in both megabytes or gigabytes so `-Xms4096M` and `-Xmx8G` both work.

<details>
  <summary>Low-Memory System where Minecraft takes up almost all your RAM</summary>

Set your minimum memory below your maximum memory to conserve as much as possible. Also try removing these arguments and trying sever garbage collection arguments: 
- `-XX:NmethodSweepActivity=1`
- `-XX:ReservedCodeCacheSize=400M`
- `-XX:NonNMethodCodeHeapSize=12M`
- `-XX:ProfiledCodeHeapSize=194M`
- `-XX:NonProfiledCodeHeapSize=194M`
  
</details>

> [!NOTE]
> 1. Allocating too much memory can break garbage collection or just slow Minecraft down, even if you have plenty to spare. Allocating too little can also slow down or break the game. Keep a close eye on Task manager (or your DE's system monitor) as Minecraft is running, and allocate only as much as it needs (which is usually less than 8G). `sparkc gcmonitor` will tell you if your allocation is too high (the pauses will be too long) or too low (frequent GC with a low memory warning in the notification).
> 
> 2. If you are using a third-party Minecraft launcher like Prism Launcher or ATLauncher, you shouldn't use memory arguments and instead control memory through the dedicated section in the launcher.

<br>

# Base Java Flags
<details>
  <summary>OpenJDK</summary>

### OpenJDK 8- (Unrecommended)

```
-XX:+UnlockExperimentalVMOptions -XX:+UnlockDiagnosticVMOptions -XX:+AlwaysActAsServerClassMachine -XX:+ParallelRefProcEnabled -XX:+DisableExplicitGC -XX:+AlwaysPreTouch -XX:+PerfDisableSharedMem -XX:+AggressiveOpts -XX:+UseFastAccessorMethods -XX:MaxInlineLevel=15 -XX:MaxVectorSize=32 -XX:+UseCompressedOops -XX:ThreadPriorityPolicy=1 -XX:+UseDynamicNumberOfGCThreads -XX:NmethodSweepActivity=1 -XX:ReservedCodeCacheSize=350M -XX:-DontCompileHugeMethods -XX:MaxNodeLimit=240000 -XX:NodeLimitFudgeFactor=8000 -XX:+UseFPUForSpilling
```

### OpenJDK 11+

```
-XX:+UnlockExperimentalVMOptions -XX:+UnlockDiagnosticVMOptions -XX:+AlwaysActAsServerClassMachine -XX:+AlwaysPreTouch -XX:+DisableExplicitGC -XX:NmethodSweepActivity=1 -XX:ReservedCodeCacheSize=400M -XX:NonNMethodCodeHeapSize=12M -XX:ProfiledCodeHeapSize=194M -XX:NonProfiledCodeHeapSize=194M -XX:-DontCompileHugeMethods -XX:MaxNodeLimit=240000 -XX:NodeLimitFudgeFactor=8000 -XX:+UseVectorCmov -XX:+PerfDisableSharedMem -XX:+UseFastUnorderedTimeStamps
```

### Extra Performance Flags (Experimental)[^1]

```
-XX:+UseFastUnorderedTimeStamps -XX:+UseCriticalJavaThreadPriority -XX:+EagerJVMCI -XX:+UseStringDeduplication -XX:+UseAES -XX:+UseAESIntrinsics -XX:+UseFMA -XX:+UseLoopPredicate -XX:+RangeCheckElimination -XX:+OptimizeStringConcat -XX:+UseCompressedOops -XX:+UseThreadPriorities -XX:+OmitStackTraceInFastThrow -XX:+RewriteBytecodes -XX:+RewriteFrequentPairs -XX:+UseFPUForSpilling -XX:+UseNewLongLShift -XX:+UseVectorCmov -XX:+UseXMMForArrayCopy -XX:+UseXmmI2D -XX:+UseXmmI2F -XX:+UseXmmLoadAndClearUpper -XX:+UseXmmRegToRegMoveAll -XX:+EliminateLocks -XX:+DoEscapeAnalysis -XX:+AlignVector -XX:+OptimizeFill -XX:+EnableVectorSupport -XX:+UseCharacterCompareIntrinsics -XX:+UseCopySignIntrinsic -XX:+UseVectorStubs -XX:UseAVX=2 -XX:UseSSE=4 -XX:+UseFastJNIAccessors -XX:+UseInlineCaches -XX:+SegmentedCodeCache -XX:ThreadPriorityPolicy=1
```

</details>

<details>
  <summary>GraalVM</summary>

### GraalVM 11- (Unrecommended)

```
-XX:+UnlockExperimentalVMOptions -XX:+UnlockDiagnosticVMOptions -XX:+AlwaysActAsServerClassMachine -XX:+ParallelRefProcEnabled -XX:+DisableExplicitGC -XX:+AlwaysPreTouch -XX:+AggressiveOpts -XX:+UseFastAccessorMethods -XX:+UseDynamicNumberOfGCThreads -XX:NmethodSweepActivity=1 -XX:ReservedCodeCacheSize=350M -XX:-DontCompileHugeMethods -XX:MaxNodeLimit=240000 -XX:NodeLimitFudgeFactor=8000 -XX:+UseFPUForSpilling -XX:+EnableJVMCI -XX:+UseJVMCICompiler -XX:+EagerJVMCI -Dgraal.TuneInlinerExploration=1 -Dgraal.CompilerConfiguration=enterprise -Dgraal.UsePriorityInlining=true -Dgraal.Vectorization=true -Dgraal.OptDuplication=true -Dgraal.DetectInvertedLoopsAsCounted=true -Dgraal.LoopInversion=true -Dgraal.VectorizeHashes=true -Dgraal.EnterprisePartialUnroll=true -Dgraal.VectorizeSIMD=true -Dgraal.StripMineNonCountedLoops=true -Dgraal.SpeculativeGuardMovement=true -Dgraal.InfeasiblePathCorrelation=true
```

Though make sure to set `-Dgraal.VectorizeSIMD` to `false` if you run shaders as this version causes issues with them. This old version also breaks constellation rendering in 1.16.5 Astral Sorcery. This is possibly related to the shader bug. See: https://github.com/HellFirePvP/AstralSorcery/issues/1963

### GraalVM 17+

```
-XX:+UnlockExperimentalVMOptions -XX:+UnlockDiagnosticVMOptions -XX:+AlwaysActAsServerClassMachine -XX:+AlwaysPreTouch -XX:+DisableExplicitGC -XX:NmethodSweepActivity=1 -XX:ReservedCodeCacheSize=400M -XX:NonNMethodCodeHeapSize=12M -XX:ProfiledCodeHeapSize=194M -XX:NonProfiledCodeHeapSize=194M -XX:-DontCompileHugeMethods -XX:+PerfDisableSharedMem -XX:+UseFastUnorderedTimeStamps -XX:+EagerJVMCI -Djdk.graal.CompilerConfiguration=enterprise -Djdk.graal.TuneInlinerExploration=1
```

### Extra Performance Flags (Experimental)[^1]

```
-XX:+UseFastUnorderedTimeStamps -XX:+UseCriticalJavaThreadPriority -XX:+UseStringDeduplication -XX:+UseAES -XX:+UseAESIntrinsics -XX:+UseFMA -XX:+UseLoopPredicate -XX:+RangeCheckElimination -XX:+OptimizeStringConcat -XX:+UseCompressedOops -XX:+UseThreadPriorities -XX:+OmitStackTraceInFastThrow -XX:+RewriteBytecodes -XX:+RewriteFrequentPairs -XX:+UseFPUForSpilling -XX:+UseNewLongLShift -XX:+UseVectorCmov -XX:+UseXMMForArrayCopy -XX:+UseXmmI2D -XX:+UseXmmI2F -XX:+UseXmmLoadAndClearUpper -XX:+UseXmmRegToRegMoveAll -XX:+EliminateLocks -XX:+DoEscapeAnalysis -XX:+AlignVector -XX:+OptimizeFill -XX:+EnableVectorSupport -XX:+UseCharacterCompareIntrinsics -XX:+UseCopySignIntrinsic -XX:+UseVectorStubs -XX:UseAVX=2 -XX:UseSSE=4 -XX:+UseFastJNIAccessors -XX:+UseInlineCaches -XX:+SegmentedCodeCache -XX:ThreadPriorityPolicy=1
```

</details>

<details>
  <summary>OpenJ9</summary>

### OpenJ9 (Unrecommended)

```
-XX:+IdleTuningGcOnIdle -XX:+UseAggressiveHeapShrink -XX:-OmitStackTraceInFastThrow -XX:+UseFastAccessorMethods -XX:+OptimizeStringConcat -Xshareclasses:allowClasspaths -Xshareclasses:cacheDir=./cache -Xaot -XX:+UseCompressedOops -XX:ObjectAlignmentInBytes=256 -Xshareclasses -XX:SharedCacheHardLimit=800M -Xtune:virtualized -XX:+TieredCompilation -XX:InitialTenuringThreshold=5 -Dlog4j2.formatMsgNoLookups=true -XX:-DisableExplicitGC -XX:InitiatingHeapOccupancyPercent=35 -XX:+UnlockExperimentalVMOptions -XX:MaxGCPauseMillis=6 -Djava.net.preferIPv4Stack=true -XX:-ParallelRefProcEnabled-XX:+UseTLAB -XX:ReservedCodeCacheSize=70M -XX:G1NewSizePercent=20 -XX:G1ReservePercent=20
```

</details>

<details>
  <summary>Platform Prime</summary>

### Platform Prime

Usually no tuning with special flags is necessary, Large Pages are still applicable though. See https://github.com/brucethemoose/Minecraft-Performance-Flags-Benchmarks/issues/49#issuecomment-1716138313.
  
</details>

> [!NOTE]
> Adding `--add-modules=jdk.incubator.vector` can help some server softwares like like Pufferfish and Purpur with explicit support for it.

<br>

# Garbage Collection
**Garbage collection flags should be added to Minecraft servers and clients**, as the default "pauses" to stop and collect garbage manifest as stutters on the client and lag on servers. Use the `/sparkc gcmonitor` command in Spark to observe pauses in-game. *Any* old generation pauses are bad, and young generation G1GC collections should be infrequent, but short enough to be imperceptible.  

<br>

### Non-Proactive ZGC 
Non-Proactive ZGC is great for high memory/high core count servers. It has no server throughput hit I can measure, and absolutely does not stutter. However, it requires more RAM and more cores than other garbage collectors. Enable it with
```
-XX:+UseZGC -XX:AllocatePrefetchStyle=1 -XX:-ZProactive
```

> [!NOTE]
> 1. It has a significant client FPS hit.
> 
> 2. Non-Proactive ZGC is unavailable in Java 8 and much less performant in Java 11 than it is in Java 17+.
> 
> 3. Allocate more RAM and more `ConcGCThreads` than you normally would for other GC.
>
> 4. GraalVM currently disables EnableJVMCI when using ZGC since it doesn't support it. 

<br/>

### Generational ZGC
Generational ZGC is great for high memory/high core count servers and is made to be competitive with the only other Generation GC, G1GC. Enable it with
```
-XX:+UseZGC -XX:AllocatePrefetchStyle=1 -XX:+ZGenerational
```

> [!NOTE]
> 1. Generational ZGC is only available in Java 21+
> 
> 2. Allocate more RAM and more `ConcGCThreads` than you normally would for other GC.
>
> 3. GraalVM currently disables EnableJVMCI when using ZGC since it doesn't support it.
>
> 4. Java 23+ no longer requires `-XX:+ZGenerational` flag. Remove it if you want as it will just be ignored since Generational is the new default.

<br/>

### Shenandoah
Shenandoah performs well on clients, but kills server throughput. Enable it with 
```
-XX:+UseShenandoahGC -XX:ShenandoahGCMode=iu -XX:ShenandoahGuaranteedGCInterval=1000000 -XX:AllocatePrefetchStyle=1
```

See more tuning options [here](https://wiki.openjdk.org/display/shenandoah/Main). The "herustic" and "mode" options don't change much (except for "compact," which you should not use). 

> [!NOTE]
> 1. Red Hat OpenJDK 8 is the only Java 8 JDK that supports Shenandoah.
>
> 2. GraalVM currently doesn't support Shenandoah.

<br/>

### Client G1GC
G1GC is the default garbage collector for all JREs. Aikar's [famous Minecraft server G1GC arguments](https://aikar.co/2018/07/02/tuning-the-jvm-g1gc-garbage-collector-flags-for-minecraft/) run great on clients, with two caveats: they effectively [clamp](https://www.oracle.com/technical-resources/articles/java/g1gc.html) the `MaxGCPauseMillis` parameter by setting `G1NewSizePercent` so high, producing long stutters on some clients, and they collect oldgen garbage too aggressively (as the client produces *far* less than a populated server). 

These are similar to the Aikar flags, but with shorter, more frequent pauses, less aggressive G1 mixed collection and more aggressive background collection: 
```
-XX:+UseG1GC -XX:MaxGCPauseMillis=37 -XX:+PerfDisableSharedMem -XX:G1HeapRegionSize=16M -XX:G1NewSizePercent=23 -XX:G1ReservePercent=20 -XX:SurvivorRatio=32 -XX:G1MixedGCCountTarget=3 -XX:G1HeapWastePercent=20 -XX:InitiatingHeapOccupancyPercent=10 -XX:G1RSetUpdatingPauseTimePercent=0 -XX:MaxTenuringThreshold=1 -XX:G1SATBBufferEnqueueingThresholdPercent=30 -XX:G1ConcMarkStepDurationMillis=5.0 -XX:GCTimeRatio=99 -XX:G1ConcRefinementServiceIntervalMillis=150 -XX:G1ConcRSHotCardLimit=16 -XX:AllocatePrefetchStyle=3
```

> [!NOTE]
> 1. Java 21+ no longer supports `-XX:G1ConcRefinementServiceIntervalMillis=150` flag and the `-XX:G1ConcRSHotCardLimit=16` flag. Remove them if you want as they will just be ignored.
> 
> 2. `G1NewSizePercent` and `MaxGCPauseMillis` can be used to tune the frequency/dureation of your young generation collections. `G1HeapWastePercent=18` should be removed if you are getting any old generation pauses on your setup. Alternatively, you can raise it and set `G1MixedGCCountTarget` to 2 or 1 to make mixed garbage collection even lazier (at the cost of higher memory usage).
>
> 3. If you are using a Java 8 based GraalVM EE, change `-XX:AllocatePrefetchStyle=3` to `-XX:AllocatePrefetchStyle=1`

<br/>

### Server G1GC
Longer pauses are more acceptable on servers. These flags are very close to the aikar defaults:

```
-XX:+UseG1GC -XX:MaxGCPauseMillis=130 -XX:+DisableExplicitGC -XX:+AlwaysPreTouch -XX:G1NewSizePercent=28 -XX:G1HeapRegionSize=16M -XX:G1ReservePercent=20 -XX:G1MixedGCCountTarget=3 -XX:InitiatingHeapOccupancyPercent=10 -XX:G1MixedGCLiveThresholdPercent=90 -XX:G1RSetUpdatingPauseTimePercent=0 -XX:SurvivorRatio=32 -XX:MaxTenuringThreshold=1 -XX:G1SATBBufferEnqueueingThresholdPercent=30 -XX:G1ConcMarkStepDurationMillis=5.0 -XX:G1ConcRefinementServiceIntervalMillis=150 -XX:G1ConcRSHotCardLimit=16 -XX:AllocatePrefetchStyle=3
```

> [!NOTE]
> 1. Java 21+ no longer supports `-XX:G1ConcRefinementServiceIntervalMillis=150` flag and the `-XX:G1ConcRSHotCardLimit=16` flag. Remove them if you want as they will just be ignored.
> 
> 2. `G1NewSizePercent` and `MaxGCPauseMillis` can be used to tune the frequency/dureation of your young generation collections. `G1HeapWastePercent=18` should be removed if you are getting any old generation pauses on your setup. Alternatively, you can raise it and set `G1MixedGCCountTarget` to 2 or 1 to make mixed garbage collection even lazier (at the cost of higher memory usage). 
>
> 3. If you are using a Java 8 based GraalVM EE, change `-XX:AllocatePrefetchStyle=3` to `-XX:AllocatePrefetchStyle=1`

<br/>

### Garbage Collection Threading
`-XX:ConcGCThreads=[Some Number]` controls the [*maximum* number](https://github.com/openjdk/jdk/blob/dd34a4c28da73c798e021c7473ac57ead56c9903/src/hotspot/share/gc/z/zHeuristics.cpp#L96-L104) of background threads the garbage collector is allowed to use, and defaults to `[number of logical (hyperthreaded) cores / 4]`. Recent versions of Java will [reduce the number of gc threads, if needed](https://wiki.openjdk.org/display/zgc/Main#Main-SettingConcurrentGCThreads).

In some cases (especially with ZGC or Shenandoah) you want to increase this thread cap past the default. I recommend `[number of REAL (non-hyperthreaded) cores - 2]` on most CPUs, but you may need to play with this parameter. If its too low, garbage collection can't keep up with Minecraft, and the game will stutter and/or start eating gobs of RAM and crash. If its too high, it might slow the game down, especially if you are running Java 8. Probably keep it under 8 (you can maybe get away with 10 for ZGC and Shenandoah).

No other "threading" flags like `ParallelGCThreads` or `JVMCIThreads` are necessary, as they are enabled by default with good automatic settings in Java 8+.

<br/>

# Large Pages

> [!CAUTION]
> Some guides say that you must run Java and your launcher as administrator on Windows. This has been shown wrong and is a MAJOR SECURITY RISK.

> [!NOTE]
> If you allocate more than 8 GB of memory to Minecraft, Java may crash on Windows. More details in this issue: https://github.com/adoptium/adoptium-support/issues/626
> You can try running `enable-mmagent -pagecombining` in a admin terminal to fix it.

Enabling large pages improves the performance of Minecraft servers and clients by reducing the load on your system. [Here's a good guide](https://kstefanj.github.io/2021/05/19/large-pages-and-java.html)

Add:
```
-XX:+UseLargePages -XX:LargePageSizeInBytes=2m
```

Check and see if large pages is working with the `-Xlog:gc+init` java argument in Java 17. 

In any Java version/platform, if large pages isn't working, you will get a warning in the log similar to this: 

```
Java HotSpot(TM) 64-Bit Server VM warning: JVM cannot use large page memory because it does not have enough privilege to lock pages in memory.
```

> [!NOTE]
> 1. Windows Home doesn't have `gpedit.msc` and thus, can't follow the guide above, intead use [this guide](https://awesomeprojectsxyz.blogspot.com/2017/11/windows-10-home-how-to-enable-lock.html?m=1), also since microsoft took down the tool in mention, you have to download it from [here](https://gist.github.com/eyecatchup/0107bab3d92473cb8a3d3547848fc442).
> 
> 2. On Linux, you generally want to use `-XX:+UseTransparentHugePages`.

<br/>

# SpecialK
A "universal" Windows mod akin to [ReShade](https://reshade.me/), SpecialK has 2 major performance benefits:

- A "smart" frame limiter that reduces stutter, eliminates tearing, saves power, and saves CPU TDP to boost when needed. It even works in conjuction with VRR or Nvidia Reflex. 

- A OpenGL-to-DirectX11 wrapper called OpenGL-IK that eliminates Minecraft's windowed mode overhead, and enables other features (like auto-HDR or a resizable borderless window).

Download it [here](https://www.special-k.info/).

Add your Minecraft launcher. Then navigate to your java bin folder where your javaw.exe is, and create an empty file called `SpecialK.OpenGL32`. Launch your Minecraft launcher with the SpecialK launcher, and the launcher will then "inject" SpecialK into Minecraft.
![SpecialK](specialk.png)

You can create a desktop shortcut to your Minecraft launcher through the SpecialK UI for even more convenience. 

Be sure to turn off VSync and the in-game Minecraft frame limiter.

<br>

# Process Priority
After launching Minecraft, set Java to run at an "Realtime" process priority in Windows with the Task Manager in the details tab:

![Task Manager](taskmgr.png)

Linux users can add  `sudo nice -n -10` to the beginning of the launch command.

> [!CAUTION]
> On Linux, nice levels below 0 (with the "max" being -20) require running nice as `root` which in turn runs Minecraft as `root`. This is a security risk. Here are some Workarounds:
> 1. Start game and run `renice -10 -p $(PID)` as `root` instead with the pid of the minecraft instance. This is safe as Minecraft itself is not running as `root`.
> 
> 2. Modify `/etc/security/limits.conf` to allow non-root users to go to negatives. Then just use the normal nice command without `sudo`. Don't do this one if you don't know what you are doing.
> 
> 3. "Hacky" way to script it: `sudo nice -n -10 su <username> -c`. The easiest way but the first 2 are probably better.

<br/>

# Other Performance Tips
- Run Minecraft on Linux! In pretty much every scenario it runs very good, for servers, RHEL is very well optimized, and for clients, CachyOS/Arch is amazing.

- Make sure the Minecraft client is using your discrete GPU! Check the F3 tab, and force Minecraft to use it in the "**Windows Graphics Settings**", *not* the AMD/Nvidia control panel (as they don't seem to work anymore).

- Linux wayland users should research running Minecraft natively on wayland instead of through xwayland.

- Close everything in the background, including Discord, game launchers and your browser! Minecraft is resource intensive, and does not like other apps generating CPU interrupts or eating disk I/O, RAM and so on.

<br />

[^1]: https://github.com/MeowIce/meowice-flags

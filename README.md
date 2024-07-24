## Minecraft Java Flags
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

- **OpenJ9** - Consumes less memory at the cost of being *much* slower in Minecraft. It also uses totally different flags than any other Java build.

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

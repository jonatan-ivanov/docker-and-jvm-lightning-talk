# Docker and JVM
The short story

---

### Why to use Containers?
- Traffic is usually spiky
- Applications are running on their own boxes/VMs  
  (sized for the peak traffic)
- HW is usually underutilized

---

### Why to use Containers?
- You want to optimize (resources and money)
- Multiple applications on the same box  
- And also isolate things  
  \+ orchestration

---

### JVM Ergonomics
- JVM is good at utilizing HW  
  (wants all the CPUs and Memory)
- The JVM tunes itself based on the HW it's running on
  - Heap size
  - Number of GC threads
  - Number of runtime compiler threads (JIT)
  - Common Pool size  
    ([ForkJoinPool](https://docs.oracle.com/en/java/javase/12/docs/api/java.base/java/util/concurrent/ForkJoinPool.html), [Parallel Streams](https://docs.oracle.com/javase/tutorial/collections/streams/parallelism.html#executing_streams_in_parallel))
  - Libs: `Runtime#availableProcessors()`

---

### JVM Ergonomics
- [Java 8 Ergonomics](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/ergonomics.html)
- [Java 12 Ergonomics](https://docs.oracle.com/en/java/javase/12/gctuning/ergonomics.html)
- Just works! Usually. :)

---

### The multiple JVMs problem
- What if we have multiple JVMs on the same box?
- Can be tricky/hard
- The JVMs can step on each other's toes
- This can be solved by using containers

---

### What is a Container?
- Abstraction to isolate resources  
  (process, mount, network, cpu, memory, etc.)
- Homework
  - [namespaces](https://en.wikipedia.org/wiki/Linux_namespaces)
  - [cgroups](https://en.wikipedia.org/wiki/Cgroups)

---

### Docker awareness
- The JVM is Docker-aware now  
  (namespace-aware + cgroup-aware)
- Java SE `8u121` and before
  - JVM uses the underlying host configuration  
    (CPUs and Memory)
  - See JVM Ergonomics: Heap, GC, JIT, Common Pool

---

### Docker awareness
- Java SE `8u131` and after (Java 9+):  
  JVM is ~Docker aware
  - [Warning: additional flags might needed](https://blogs.oracle.com/java-platform-group/java-se-support-for-docker-cpu-and-memory-limits)
  - [Real Docker awareness: Java 10+](https://blog.docker.com/2018/04/improved-docker-container-integration-with-java-10)
- **If you are touching these JVM args,**  
  **you need to be Docker-aware too**

---

### Demo-Time!
```
docker run -m 32M openjdk:7 java -XshowSettings:vm -version
docker run -m 32M openjdk:8 java -XshowSettings:vm -version
docker run -m 32M openjdk:9 java -XshowSettings:vm -version
docker run -m 32M openjdk:10 java -XshowSettings:vm -version
docker run -m 32M openjdk:11 java -XshowSettings:vm -version
docker run -m 32M openjdk:12 java -XshowSettings:vm -version
docker run -m 32M openjdk:13 java -XshowSettings:vm -version
```

```
docker run --cpus 2 openjdk:9 /bin/bash -c "echo 'Runtime.getRuntime().availableProcessors()' | jshell"
docker run --cpus 2 openjdk:12 /bin/bash -c "echo 'Runtime.getRuntime().availableProcessors()' | jshell"
```

---

### Demo-Time!
```
docker run --cpus 2 -v $HOME/dev/GitHub/java-rt-info/build/libs:/app openjdk:7 java -jar /app/java-rt-info.jar
docker run --cpus 2 -v $HOME/dev/GitHub/java-rt-info/build/libs:/app openjdk:8 java -jar /app/java-rt-info.jar
docker run --cpus 2 -v $HOME/dev/GitHub/java-rt-info/build/libs:/app openjdk:9 java -jar /app/java-rt-info.jar
docker run --cpus 2 -v $HOME/dev/GitHub/java-rt-info/build/libs:/app openjdk:10 java -jar /app/java-rt-info.jar
docker run --cpus 2 -v $HOME/dev/GitHub/java-rt-info/build/libs:/app openjdk:11 java -jar /app/java-rt-info.jar
docker run --cpus 2 -v $HOME/dev/GitHub/java-rt-info/build/libs:/app openjdk:12 java -jar /app/java-rt-info.jar
docker run --cpus 2 -v $HOME/dev/GitHub/java-rt-info/build/libs:/app openjdk:13 java -jar /app/java-rt-info.jar
```

---

### Demo-Time!

https://github.com/jonatan-ivanov/resourceater

```
docker start -i resourceater
docker stats resourceater
```

---

# Don't guess, measure!

[tw: @jonatan_ivanov](https://twitter.com/jonatan_ivanov)  
[gh: @jonatan-ivanov](https://github.com/jonatan-ivanov)

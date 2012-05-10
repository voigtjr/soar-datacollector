soar-datacollector is a benchmarking library for [Soar](http://sitemaker.umich.edu/soar/home) agents.

The library is written in Java and uses the Java SML interface to Soar.

To use soar-datacollector, you need to modify your existing Java environment to make the correct calls at the correct points during the agent's lifecycle, or adapt the [example code](https://github.com/voigtjr/soar-datacollector/blob/master/src/main/java/edu/umich/soar/DataCollectorExample.java) to work with your agent.

General information on soar-datacollector is in the [slides](https://raw.github.com/voigtjr/soar-datacollector/master/doc/sdc-soar-workshop-31.pdf) from the [31st Soar Workshop](https://web.eecs.umich.edu/~soar/workshop/) (2011).

# Jar

[soar-datacollector.jar]() does not include source, example code, or dependency jars (see Runtime below).

# Compilation

soar-datacollector uses the `sml.jar` found in [Soar 9.3.1](http://soar.googlecode.com/).

Tell Eclipse where to find this jar by defining the `SOARBIN` build path (not environment) variable:

* soar-datacollector build path
* Libraries tab,
* Add Variable
* Configure Variables
* New Folder
* `SOARBIN`: `/path/to/soar/bin`
    * Use the browser dialog to find the folder
    * The trailing `bin` is important

# Runtime

soar-datacollector depends on (included in `lib`):
* [guava-r09.jar](https://github.com/voigtjr/soar-datacollector/raw/master/lib/guava-r09.jar)
* [commons-logging-1.1.1.jar](https://github.com/voigtjr/soar-datacollector/raw/master/lib/commons-logging-1.1.1.jar)
* [log4j-1.2.15.jar](https://github.com/voigtjr/soar-datacollector/raw/master/lib/log4j-1.2.15.jar)

soar-datacollector also depends on `sml.jar` found in [Soar 9.3.1](http://soar.googlecode.com/).

soar-datacollector (via SML) needs to link to dynamic libraries in the `bin` subfolder of Soar at runtime.
* If you already have an environment, you will already have dealt with this problem.
* If you are trying to run the example code in this demo, set the appropriate environment variable for your system in the Run/Debug configuration:
    * Run -> Run Configurations...
    * DataCollectorExample
    * Environment tab
    * New...
        * Windows: `PATH` to `C:\path\to\soar\bin`
        * Mac: `DYLD_LIBRARY_PATH` to `/path/to/soar/bin`
        * Linux: `LD_LIBRARY_PATH` to `/path/to/soar/bin`

## UnsatisfiedLinkError

This error means you did not succeed in telling it how to find the Soar dynamic libraries (see above).

    Native code library failed to load. 
    java.lang.UnsatisfiedLinkError: no Java_sml_ClientInterface in java.library.path
    Exception in thread "main" java.lang.UnsatisfiedLinkError: no Java_sml_ClientInterface in java.library.path
    	at java.lang.ClassLoader.loadLibrary(ClassLoader.java:1758)
    	at java.lang.Runtime.loadLibrary0(Runtime.java:823)
    	at java.lang.System.loadLibrary(System.java:1045)
    	at sml.smlJNI.<clinit>(smlJNI.java:15)
    	at sml.Kernel.CreateKernelInNewThread(Kernel.java:133)
    	at edu.umich.soar.DataCollectorExample.main(DataCollectorExample.java:21)

# Library Usage

**Create one DataCollector instance** for all agents: 

    DataCollector dc = new DataCollector;

**Optionally configure** (default: 5000 cycles):

*To collect based on decision cycles*:

    dc.setPeriodCycles(10); // onUpdateEvent returns true every 10 decisions

*To collect based on wall time*:

    dc.setPeriodMillis(500); // onUpdateEvent returns true after every 500ms

**Implement callbacks** (or add to existing implementations):

*Start event*:

    // Kernel.RegisterForSystemEvent()
    // smlSystemEventId.smlEVENT_SYSTEM_START
    dc.onStart();

*After all agents pass output*:

    // Kernel.RegisterForUpdateEvent()
    // smlUpdateEventId.smlEVENT_AFTER_ALL_OUTPUT_PHASES
    if (dc.onUpdateEvent()) // once per decision cycle
    {
        // onUpdateEvent => true means call collect
        for (Agent agent : agents)
        {
            dc.collect(agent); // for each agent
        }
    }
    
    // possibly call to prevent catastrophic loss in event of crash
    // dc.flush()
    // but call it in a period greater than one decision cycle

*Stop event*:

    // Kernel.RegisterForSystemEvent()
    // smlSystemEventId.smlEVENT_SYSTEM_STOP
    dc.stop();
    for (Agent agent : agents)
    {
        dc.collect(agent); // each agent
    }

## Example

A [full example](https://github.com/voigtjr/soar-datacollector/blob/master/src/main/java/edu/umich/soar/DataCollectorExample.java) is included in the project source.

# License

soar-datacollector uses the [BSD](http://www.opensource.org/licenses/bsd-license.php) license, the same as Soar.

Copyright (c) 2012, The Regents of the University of Michigan, Jonathan Voigt
All rights reserved.

Redistribution and use in source and binary forms, with or without modification, are permitted provided that the following conditions are met:

Redistributions of source code must retain the above copyright notice, this list of conditions and the following disclaimer.

Redistributions in binary form must reproduce the above copyright notice, this list of conditions and the following disclaimer in the documentation and/or other materials provided with the distribution.

THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.

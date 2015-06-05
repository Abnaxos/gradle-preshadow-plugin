The Preshadow Gradle Plugin
===========================

The Preshadow plugin for Gradle is very similar to the [Shadow](https://github.com/johnrengelman/shadow) plugin (it's actually based on it), with one big difference: The libraries will be shadowed *before* compilation. You'll get a new "pseudo-dependency" containing all the shadowed code an you'll write your own source code against the shadowed code:

Instead of

    import org.objectweb.asm.ClassWriter
    
you'll write

    import my.shadowed.asm.ClassWriter


How to Use
----------

Simply apply the Plugin:

    buildscript {
        repositories {
            jcentral()
        }
        dependencies {
            classpath group:'ch.raffael.gradlePlugins.preshadow', name:'preshadow', version:'1.0'
        }
    }
    
    apply plugin:'ch.raffael.preshadow'
    
    dependencies {
        preshadow group:'org.ow2.asm', name:'asm-debug-all', version:'5.0.3'
    }

    // You'll probably want to relocate some classes:
    preshadowJar {
        relocate 'org.objectweb.asm', 'my.shadowed.asm'
    }
    
The task `preshadowJar` extends the `ShadowJar` task from the [Shadow](https://github.com/johnrengelman/shadow) plugin, so see there for more information.

The JAR file containing the relocated classes can be found in `$buildDir/preshadow/your-project-name-PRESHADOW.jar`.

### Sources

The task `preshadowSourcesJar` produces a sources JAR of the preshadowed classes in `$buildDir/preshadow/your-project-name-PRESHADOW-sources.jar`.

### IDEA

If the `idea` plugin is loaded, the plugin will add the preshadowJar to the module's *compile* classpath. If the sources JAR is present during synchronization with IDEA, it will be detected automatically.


Why preshadow?
--------------

I once stumbled upon the problem, that I was using an external library in a Java8 project and wanted to relocate and shadow it. Back then, I was still using Maven. Now, the problem was, that the shade plugin didn't support Java8 bytecode at that time, so I was out of luck. As a workaround, I managed to "pre-shade" the library (which was Java6 bytecode) and then write my code against the already relocated version of it.

And I fell in love with that. I prefer this method of shadowing JARs over the "classic" post-compilation method:
 
  * It's more transparent, I feel more in control
  
  * It applies also during debugging without having to add any build steps to the IDE's default compilation
  
So, I stuck with it and wrote this plugin for Gradle.


### Other IDEs

I IDEA, so there's currently support for IDEA only. If you'd like to contribute support for another IDE, feel free to do so and send me a pull request. :)

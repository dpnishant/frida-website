---
layout: docs
title: iOS
permalink: /docs/examples/ios/
---

iOS & macOS Recipes
===========


1. Inject script into process on host via REPL
-
Injecting a frida instrumentation script on host machine can be achieved by the following  command. Here the **-n** switch specifies the fact that the frida-agent and the frida REPL both are on the same host.
```
frida -n Twitter -l demo1.js
```

2. List all running process names, pid
-
The following command shall list all the running processes in a tabular format with name and pid columns
```
frida-ps ai 
```

3. List all running process names on a USB device
-
The following command shall list all the running processes on a **USB device** in a tabular format with name and pid columns. The **-U** specifies that a USB device is being queried.
```
frida-ps -Uai
```

4. List all attached devices running with frida-server
-
The following command shall list all the attached devices that running the frida-server. Processes on these devices can be instrumented via frida.
```
frida-ls-devices
```

5. Tracing Native APIs
-
The following command can be used to trace a native API on a particular process. Function names can be specified using wildcard characters *(as shown below)*, which can be particularly useful while exploration or discovering user-defined functions within the process.
>1. ```frida-trace -n Twitter -i "*URL*"``` 
>2. ```frida-trace -U Twitter -i "*URL*"``` *//USB Device*

6. Tracing Objective-C APIs
-
The following command can be used to trace an Objective-C API on a particular process. Notice the difference in switch, in this case it's **-m** instead of **-i**. Objective-C APIs names, the Class names as well as the method types (class method or instance method) can all be specified using wildcard characters *(as shown below)*. This can be particularly useful while exploration or discovering user-defined methods within the process.

```
frida-trace -U Twitter -m "-[NSURL* *HTTP*]"
``` 

7. Back-tracing an Objective-C API
-
The following command can be used to backtrace an Objective-C API on a particular process.

>**Tip:** Add the following code, to the **onEnter** event-handler in the auto-generated JS of the required API
```
console.log("\tbacktrace: \n\t" + Thread.backtrace(this.context, Backtracer.ACCURATE).map(DebugSymbol.fromAddress).join("\n\t"));
``` 

8. Writing data to File
-
If you want to write some data into a file, that is generated by your frida-script. You need to write a frida using either the NodeJS (recommended by **@oleavr**) or Python bindings and then have to ```send()``` the data back to your app and then write it to file as usual.

> **Tip:** The data that you ```send()``` should JSON serializable.

Code
>Example:
>*script.js*
>```
>var data = {"foo": "bar"};
>send(data);
>```
>*app.py*
>```
>import frida
>
>def on_message(message, data):
>    print message['payload']
>```

9. Calling a Native Function
-
```
var func_sqlite3_sql = Module.findExportByName('libsqlite3.dylib', 'sqlite3_sql');

var sqlite3_sql = new NativeFunction(func_sqlite3_sql, 'char', ['pointer']);
```

Explanation: https://gist.github.com/dpnishant/c7c6b47ebfd8cd671ecf

#Data-Structures

1. Converting NSData to String
-
```
var data = new Objc.Object(args[2]);
Memory.readUtf8String(data.bytes(), data.length());
```
>**Tip**:  2nd argument (number of bytes) is not required if the string data is null terminated. 

2. Converting NSData to Binary Data
-
```
Memory.readByteArray(data.bytes(), data.length());
```

3. Iterating a NSArray
-
```
var size = array.count();
for (var i = 0; i !- size; i++) {
  var element = array.objectAtIndex_(i);
}
```
4. Iterating a NSDictionary
-
```
var enumerator = myDictionary.keyEnumerator();
var key;
while ((key = enumerator.nextObject()) !- null) {
  var value = myDictionary.objectForKey_(key);
}
```
5. Unarchiving a NSKeyedArchiver
-
```
var parsed_value = ObjC.classes.NSKeyedUnarchiver.unarchiveObjectWithData_(value);
```


6. Reading Struct
-
If args[0] is a pointer to a struct, and let's say you want to read the uint32 at offset 4, you can do it as shown below: 
```
Memory.readU32(args[0].add(4));
```

_Please click "Improve this page" above and add an example. Thanks!_

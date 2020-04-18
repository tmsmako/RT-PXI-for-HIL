# Communication
## Message format
### JSON RPC
* [Specification](https://www.jsonrpc.org/specification)
* All message IDs are unique, a Base64 encoded GUID (safe for asynchronous messaging)
* Interface definition of procedures, methods, properties and parameters
* "Wizard-style" GUI tool to generate messages based on interface definitions
* Response automatically parsed based on interface definitions

#### Example

batch request
``` json
[
   { "id": "6A5XcJV36UOKqw5MaOTt2Q", "jsonrpc": "2.0", "method": "readAnalogChannel", "params": { "device_id": "NI_AnaIn", "channel": "ai0:ai3", "mode": "single" } },
   { "id": "RmnZfOrROUW3SJ20T9svCQ", "jsonrpc": "2.0", "method": "writeDigitalChannel", "params": { "device_id": "NI_DigIO", "channel": "line0", "value": true } },
   { "id": "FcGr1EvvWk6At1BI77qISQ", "jsonrpc": "2.0", "method": "execute", "params": { "handler": "MotCTRL", "procedure": "Init", "subParams": { "FSoEinit": "true", "releaseBreaks": "false"}} }
]
```

batch response without errors
``` json
[
   { "id": "6A5XcJV36UOKqw5MaOTt2Q", "jsonrpc": "2.0", "result": { "values": [ 27.083554, 3.343332, 12.154341, 5.071113 ], "unit": "V" } },
   { "id": "RmnZfOrROUW3SJ20T9svCQ", "jsonrpc": "2.0", "result": {} },
   { "id": "FcGr1EvvWk6At1BI77qISQ", "jsonrpc": "2.0", "result": { "value": true } }
]
```

batch response with errors
``` json
[
   { "id": "6A5XcJV36UOKqw5MaOTt2Q", "jsonrpc": "2.0", "error": { "code": -200220, "message": "Device identifier is invalid."  } },
   { "id": "RmnZfOrROUW3SJ20T9svCQ", "jsonrpc": "2.0", "result": {} },
   { "id": "FcGr1EvvWk6At1BI77qISQ", "jsonrpc": "2.0", "error": { "code": -5598, "message": "Procedure execution failed due to an error.", "details": { "err_code": -250893, "err_message": "Failed to initialize FSoE master." } } }
]
```

#### Further readings
* [NI SLSC web API examples](https://www.ni.com/hu-hu/support/documentation/supplemental/18/using-the-slsc-web-api.html)

## Transfer protocol
### ZeroMQ
* [Why ZeroMQ?](https://zeromq.org)
	* [LabVIEW support](http://wiki.zeromq.org/bindings:labview)
	* [.NET support](https://zeromq.org/get-started/?language=csharp#)
	* [C support](https://zeromq.org/get-started/?language=c#)
	* [Python support](https://zeromq.org/get-started/?language=python#)

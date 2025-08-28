### AZ_Printf, printf

```cpp
AZStd::string stringValue = "String test";
AZ_Printf("Just a string %s ", stringValue.c_str());

bool boolValue = true;
AZ_Printf("Just a boolean:", boolValue ? "True" : "False");

size_t i = 0;
AZ_TracePrintf("At some Component", " index: %zu ", i);

int64_t int64Value = 64;
printf("just an int64_t: %lld ", int64Value);

AZ::EntityId entityIdValue = GetEntityId();
AZ_Printf("Just an entityId", "EntityId: %s", entityIdValue.ToString().c_str());

unsigned long long unsignedLongLongValue = 0);
AZ_Printf("At some Component", " unsignedLongLong: %llu ", unsignedLongLongValue);
```

--- 

### Bus

```cpp
// call Somefuncion on anyone connected to this bus on any id, with params 
BusName::Broadcast(&BusName::Events::Somefunction, params);

// call Somefunction on anyone who did BusConnect(id), with params
BusName::Event(id, &BusName::Events::Somefunction, params);

// call Somefunction which returns a result, call operator== on result on each invocation
BusName::BroadcastResult(result, &BusName::Events::Somefunction, params);

// Call Somefunction on anyone who did Busconnect(id), each function that executes call operator== on result
BusName::EventResult(result, id, &BusName::Events::Somefunction, params);
```


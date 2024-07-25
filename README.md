# TP Farming

## DEVELOPMENT API

### EXPORTS

`@HasAttachedEntityBucket`
Why using this export? This export can be used for scripts to prevent breaking the animation and the bucket attachment if the players are breaking it in purpose.

```lua
exports.tp_farming:HasAttachedEntityBucket() -- returns boolean (true / false) and the entity id if boolean is true.
```

### EVENTS


```lua

-- @param plantId : Returns Integer
-- @param plantType : Returns String
-- @param location (coords) : Returns Table

RegisterNetEvent("tp_farming:client:plantedPlantType")
AddEventHandler("tp_farming:client:plantedPlantType", function(plantId, plantType, location)
    -- todo nothing
end)
```

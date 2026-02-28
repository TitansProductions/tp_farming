# TP Farming

## DEVELOPMENT API

### EXPORTS

`@HasAttachedEntityBucket`
Why using this export? This export can be used for scripts to prevent breaking the animation and the bucket attachment if the players are breaking it in purpose or when having river scripts and both are interacting to each other.

Returns nil if there is no Attached Bucket, otherwise it will return the Entity (Client).

```lua
exports.tp_farming:HasAttachedEntityBucket() -- returns boolean (true / false) and the entity id if boolean is true.
```

`@IsBusy`
Returns true if the player does any action related to anything.

```lua
exports.tp_farming:IsBusy() -- returns boolean (true / false) and the entity id if boolean is true.
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


- Use the following event on your stable script for registering the wagon properly for receiving or updating the data related to the water capacity.

```lua
    - @param networkId : requires the networked id of the spawned vehicle (wagon)
    - @param model : requires the model name (not hash)
    - @param water_capacity : requires the water capacity from the sql or any place you are able to receive it.
    - @param id : required the id of the vehicle from the sql  or any place you are able to receive it.
    TriggerServerEvent("tp_farming:updateWagonByNetworkId", networkId, 'REGISTER', { model, water_capacity, id }) -- client > server
```

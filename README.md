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

## How to use / register specific water wagons from stable script to ours

- Use the following event on your stable script for registering the wagon properly for receiving or updating the data related to the water capacity.

```lua
    - @param networkId : requires the networked id of the spawned vehicle (wagon)
    - @param model : requires the model name (not hash)
    - @param water_capacity : requires the water capacity from the sql or any place you are able to receive it.
    - @param id : required the id of the vehicle from the sql  or any place you are able to receive it.
    TriggerServerEvent("tp_farming:updateWagonByNetworkId", networkId, 'REGISTER', { model, water_capacity, id }) -- client > server
```

This is the code it is used on tpz_stables for registering a wagon:

```lua

		if Config.tp_farming.Enabled then

            -- we check before registering if the spawned vehicle is a model that is listed on tp_farming for carrying water capacity.
            for _, _wagon in pairs (Config.tp_farming.WagonModels) do 

                if WagonData.model == _wagon then

                    TriggerServerEvent("tp_farming:updateWagonByNetworkId", network, 'REGISTER', { WagonData.model, WagonData.water_capacity, WagonData.id })
                    break 
                end

            end

        end
```

For more understanding of the code: https://github.com/TPZ-CORE/tpz_stables/blob/main/tpz_stables/client/tp-client_wagons.lua



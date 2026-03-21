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

- Use the following event on your stable script for registering the wagon properly for receiving or updating the data related to the water capacity. This event is triggered only once and must be triggered when spawning the vehicle.

The water capacity must be added on the wagons database and be taken / loaded when spawning the vehicle in order to load it properly through the trigger event.

```lua
    - @param networkId : requires the networked id of the spawned vehicle (wagon)
    - @param model : requires the model name (not hash)
    - @param water_capacity : requires the water capacity from the sql or any place you are able to receive it.
    - @param id : required the id of the vehicle from the sql  or any place you are able to receive it.
    TriggerServerEvent("tp_farming:updateWagonByNetworkId", networkId, 'REGISTER', { model, water_capacity, id }) -- client > server
```

1. This is the code it is used on tpz_stables for registering a wagon:

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

2. After registering properly, the only thing is left is editing `tp_farming/server/tp-server_escrow_allow.lua` file on `UpdateWagonWaterCapacity` function (this is what we also use for tpz_stables).

```lua
/*
    ~ TIP ~

    In case you don't have a wagon_id, you can check through database the model and get from the source the identifier and character identifier
    and update that wagon

    Example: update wagons set capacity where identifier = identifier and charidentifier = charidentifier and model = model.

    (!) Keep in mind, this is a poor way to do it because this wagon might be from someone else player and not his own - if this is the case, it won't work.
*/

-- @param model    : returns the wagon model name.
-- @param wagon_id : returns the registered wagon id from your stable script, if not registered, it will return as 0.
-- @param capacity : returns the updated capacity of the water.

UpdateWagonWaterCapacity = function(source, model, wagon_id, capacity)
    wagon_id = tonumber(wagon_id)
    exports.ghmattimysql:execute("UPDATE `wagons` SET `water_capacity` = @water_capacity WHERE `id` = @id ", { ['id'] = wagon_id,  ['water_capacity'] = capacity })
end
```

## Configuration Explanation

### Plant Configurations

Each plant can have its own growth time duration, temperature min-max values, fertilizers and rewards.


__Growth / Ageing:__

The plant growth - ageing is based in **minutes** and only (not in-game time). The ones you have to modify based on the existing or new plant is `GrowthAge`, `GrowthPlantStages` and `MaxPlantAge`.

1. `GrowthAge` : The growth age is the total age of a plant to reach 100% growth (for receiving - collecting the rewards). For example, if you want the total growth of this plant to be in 1 hour, you set `60` (which is 1 hour in minutes).

2. `MaxPlantAge` : The max age of the plant (not growth) - must be greater than `GrowthAge` ( > 60 ). This `MaxPlantAge` option is to remove completely a plant after reaching 100% in case it is never picked up by any player. So if the value is at `80` (which is greater than 60), the plant will die and be removed completely by the system after 1 hour and 20 minutes.

3. `GrowthPlantStages` : This option is a bit tricky and needs some understanding. I have made this option in order to provide a tricky feature for server owners to be able to create plant growth stages. Unfortunately, there is no other way unless you have multiple plant objects of the same type (custom of course) in your server, then it can be easier, but for those who don't have and want to use in-game plant objects, this is the most easiest way.

__Example & Explanation:__

```lua
        GrowthPlantStages = {
            {Model = 's_inv_wildmint01x', Age = 0,    PlacementHeight = 0.7}, -- Lowest Age
            {Model = 's_inv_wildmint01x', Age = 30,  PlacementHeight = 0.0},
            {Model = 'p_tree_orange_01',  Age = 60,  PlacementHeight = 0.5}, -- Highest Age
        },
```

For creating a plant growth stage, we start from `0` (age) up to `GrowthAge` which is `60` minutes (1 hour) as we mentioned above. Then, in game, since we know what object we want to use, the `PlacementHeight` is the Z position of the object (plant) which makes the plant to be placed higher or lower in the ground - with this way we create a "fake" growth.

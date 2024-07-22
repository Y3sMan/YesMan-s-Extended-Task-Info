
# Compatibility and Monkey Patching

## Example of adding a coefficient to the fetch rewards calculations
```lua
if ym_tri_utils then
    printf("! Monkey Patching ym_tri_utils")
    
    local ym_utils = ym_tri_utils

    local org_get_fetch_reward = ym_utils.ym_get_fetch_reward
    local add_task_func = ym_utils.ym_add_supported_task_func
    local remove_task_func = ym_utils.ym_remove_supported_task_func

    ym_utils.ym_get_fetch_reward = function(task_id)
        printf("!Monkey Patch of ym_get_fetch_reward")

        local org_min, org_max = org_get_fetch_reward(task_id)
        local factor = 10.0
        local min, max = 0, 0

        printf("! org_min = %s, org_max = %s",org_min, org_max)
        min = org_min * factor
        max = org_max * factor

        printf("! min = %s, max = %s",min, max)
        return min, max
    end


    add_task_func("fetch_reward_and_remove,", ym_utils.ym_get_fetch_reward)
end
```

### Important points
* add_task_func("fetch_reward_and_remove,", ym_utils.ym_get_fetch_reward)
    allows adding new reward calculation functions and REPLACING vanilla calculations
    * This is required in order to replace instance inside the list supported_task_funcs in ym_tri_utils.script
    * Also be are of the "," at the end of some of the function-string names. Basically, the on_complete condlist is converted into a string, and the comma prevents false-positives with similar strings(i.e. "fetch_reward_and_remove_for_my_awesome_task"). 

* Name of monkey patch script is not important

## Exmaple of editing captions and other textual information
```lua
local ym_utils = ym_tri_utils

-- The original text line. If other patches edit it, this new patch will append to the end, so take that into mind
-- The base Min string is "%$ylw_textMin: %$clr_number$min", which just translates to 
-- "Min: [some number]"
local org_min  = ym_utils.ym_get_reward_text_min()

-- The information you want to append to the Min string
local min_addendum = "(more information)"
local new_min = ""

new_min = org_min .. min_addendum
local tbl_keys = ym_utils.get_generic_tbl_keys() -- this simply returns a table containing basic formatting keys and values 
-- If you add any formatting to min_addendum, add it as such
tbl_keys["new_color"] = new_color

local s = parse_keys(new_min, tbl_keys)
ym_utils.ym_set_reward_text_min(s)
-- If this is the first edit, then the new line will be:
    "Min: [some number] (more information)" 
```
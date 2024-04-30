
# Compatibility and Monkey Patching

## Example of adding a coefficient to the fetch rewards calculations
     if zz_ym_extended_task_info then
        printf("! Monkey Patching zz_ym_extended_task_info")

        local org_get_fetch_reward = zz_ym_extended_task_info.ym_get_fetch_reward
        local add_task_func = zz_ym_extended_task_info.ym_add_supported_task_func
        local remove_task_func = zz_ym_extended_task_info.ym_remove_supported_task_func

        zz_ym_extended_task_info.ym_get_fetch_reward = function(task_id)
            printf("!Monkey Patch of ym_get_fetch_reward")

            local org_min, org_max = org_get_fetch_reward(task_id)
            local factor = 10.0
            local min, max = 0, 0

            printf("! org_min = %s, org_max = %s",org_min, org_max)
            min = org_min * factor
            max  =org_max * factor

            printf("! min = %s, max = %s",min, max)
            return min, max
        end


        add_task_func("fetch_reward_and_remove,", zz_ym_extended_task_info.ym_get_fetch_reward)
    end

### Important points
* add_task_func("fetch_reward_and_remove,", zz_ym_extended_task_info.ym_get_fetch_reward)
    allows adding new reward calculation functions and REPLACING vanilla calculations
    * This is required in order to replace instance inside the list supported_task_funcs in zz_ym_extended_task_info.script
    * Also be are of the "," at the end of some of the function-string names. Basically, the on_complete condlist is converted into a string, and the comma prevents false-positives with similar strings(i.e. "fetch_reward_and_remove_for_my_awesome_task"). 

* Name of monkey patch script is not important
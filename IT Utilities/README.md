

# Laptop Utilities
When it comes to diagnosing the laptops and adjusting settings, most work
will be done via the mgmt app located at %progradata%\ope\Services\mgmt\mgmt.exe

You can add the mgmt folder to the sytem path (add %programdata%\ope\Service\mgmt)


## MGMT Utility / Credential App

The primary utility to adjust and secure the laptop. From here, you can run service commands (scan_nics, screen_shot) as well as approve nics and adjust settings.

For a full list of possible commands, run mgmt.exe without any commands

mgmt.exe

You can use help and the command name to get more information

mgmt.exe help screen_shot


### Listing System NICS
Show a list of sytem nics and their properties, including if they are approved or not given their current properties.

mgmt.exe list_system_nics

### List Approved NICS
Show a list of approved nics and the networks they are approved for

mgmt.exe list_approved_nics

### Remove Approved NIC
To remove a nic from the approved list, specify its name as well as it's network

mgmt.exe remove_nic "nic device name" "202.5.222"


### Approving a NIC
Approval is based upon the NIC name as well as the network(IP Network) allowed.

If you are using 202.5.222.0 for your netowrk, and your laptops will get a number like 202.5.222.34, then you would approve your network adapter for the first part of the network address:
mgmt.exe approve_nic "Network Device Name" "202.5.222"

This would allow that network card to work only on a network that started with 202.5.222.  It will also strip off the extra number (e.g. second nic could end up "My Network Card #2" - it will strip that off).



## OPEService

The OPEService is responsible for running commands as needed - mostly it uses the mgmt.exe app to do the real work.

Automated commands include
- Screen Shots - grab a screen shot of the desktop periodically
- Scan Nics - See if network devices are present that shouldn't be
- Scan COM Ports - See if com ports are present that shouldn't be
- Set Folder/Registry Permissions - Lock down permissions settings so that the apps can't be tampered with


### Possible Registry Settings
The following settings can be adjusted to make the OPEService perform differently. Most of these can be set via the mgmt utility

HKLM\Software\OPE -- Readonly attributes for students
 - student_user - username of the inmate/student account
 - canvas_access_token - Secure token for canvas access - generated in student account during credential
 - canvas_url - http url for the canvas server
 - smc_url - http url for the SMC server
  
HKLM\Software\OPE\OPELMS -- Readonly attributes for students
 - has_synced_with_canvas - true -- Have we synced at least once?
 - last_sync_with_canvas - date/time, when did we last sync?
 - running - Is the app currently running?

HKLM\Software\OPE\OPELMS\student  -- Read/Write access for student - pulled from sync w canvas
 - id - Canvas ID
 - name - Canvas name
 - short_name
 - sortable_name
 - user_name  (moved - now \OPE:laptop_student_name)

HKLM\Software\OPE\OPEService -- NO ACCESS for student
 - last_smc_admin_user - credentialed using this smc account
 - logs_cleared - true, logs have been cleared during credential - clean slate for student
 - admin_user - username of the admin user setup on the laptop
 - log_level - 0 off, 1 error(only), 2 warnings, 3 info, 4 debug (all)
 - set_default_permissions_timer - default 3600, How often run set permissions commands (registry and folders)
 - reload_settings_timer - default 30 seconds, how often to reload settings from the registry (pick up changes)
 - scan_nics_timer - Default 60, how often to manually check nics (events cause immediate run)
 - screen_shot_timer - Default 30-300 (30 sec to 5 mins), how often to take a screen shot of the computer
 - approved_nics - A list of nics and approved networks to not disable (NOTE - Update this via the MGMT.exe utility - NOT by hand! - any editing errors will cause it to default to the factory settings and will likely disable everything)
 

 


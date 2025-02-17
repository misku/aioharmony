aioharmony
==========

Python library for programmatically using a Logitech Harmony Link or Ultimate Hub.

This library originated from `iandday/pyharmony <https://github.com/iandday/pyharmony>`__ which was a fork
of `bkanuka/pyharmony <https://github.com/bkanuka/pyharmony>`__ with the intent to:

- Make the harmony library asyncio
- Ability to provide one's own custom callbacks to be called
- Automatic reconnect, even if re-connection cannot be established for a time
- More easily get the HUB configuration through API call
- Additional callbacks: connect, disconnect, HUB configuration updated
- Using unique msgid's ensuring that responses from the HUB are correctly managed.

Protocol
--------

As the harmony protocol is being worked out, notes will be in PROTOCOL.md.

Status
------

* Retrieving current activity
* Querying for entire device information
* Querying for activity information only
* Querying for current activity
* Starting Activity
* Sending Command
* Changing channels
* Custom callbacks.

Usage
-----

.. code:: bash

    usage: __main__.py [-h] (--harmony_ip HARMONY_IP | --discover)
                       [--protocol {WEBSOCKETS,XMPP}]
                       [--loglevel {DEBUG,INFO,WARNING,ERROR,CRITICAL}]
                       [--logmodules LOGMODULES]
                       [--show_responses | --no-show_responses] [--wait WAIT]
                       {show_config,show_detailed_config,show_current_activity,start_activity,power_off,sync,listen,activity_monitor,send_command,change_channel}
                       ...

    aioharmony - Harmony device control

    positional arguments:
      {show_config,show_detailed_config,show_current_activity,start_activity,power_off,sync,listen,activity_monitor,send_command,change_channel}
        show_config         Print the Harmony device configuration.
        show_detailed_config
                            Print the detailed Harmony device configuration.
        show_current_activity
                            Print the current activity config.
        start_activity      Switch to a different activity.
        power_off           Stop the activity.
        sync                Sync the harmony.
        listen              Output everything HUB sends out. Use in combination
                            with --wait.
        activity_monitor    Monitor and show when an activity is changing. Use in
                            combination with --wait to keep monitoring
                            foractivities otherwise only current activity will be
                            shown.
        send_command        Send a simple command.
        send_commands       Send a series of simple commands separated by spaces.
        change_channel      Change the channel

    optional arguments:
      -h, --help            show this help message and exit
      --harmony_ip HARMONY_IP
                            IP Address of the Harmony device, multiple IPs can be
                            specified as a comma separated list without spaces.
                            (default: None)
      --discover            Scan for Harmony devices. (default: False)
      --protocol {WEBSOCKETS,XMPP}
                            Protocol to use to connect to HUB. Note for XMPP one
                            has to ensure that XMPP is enabledon the hub.
                            (default: None)
      --loglevel {DEBUG,INFO,WARNING,ERROR,CRITICAL}
                            Logging level for all components to print to the
                            console. (default: ERROR)
      --logmodules LOGMODULES
                            Restrict logging to modules specified. Multiple can be
                            provided as a comma separated list without any spaces.
                            Use * to include any further submodules. (default:
                            None)
      --show_responses      Print out responses coming from HUB. (default: False)
      --no-show_responses   Do not print responses coming from HUB. (default:
                            False)
      --wait WAIT           How long to wait in seconds after completion, useful
                            in combination with --show-responses. Use -1 to wait
                            infinite, otherwise has to be a positive number.
                            (default: 0)


Release Notes
-------------

0.1.0. Initial Release

0.1.2. Fixed:
    - Enable callback connect only once initial connect and initialization is completed.
    - Fix exception when activity/device name/id is None when trying to retrieve name/id.
    - Fixed content type and name of README in setup.py
0.1.3. Fixed:
    - Retry connect on reconnect
0.1.4. Fixed:
    - Exception when retrieve_hub_info failed to retrieve information
    - call_callback helper would never return True on success.
    - Retry connect on reconnect (was not awaited upon)
0.1.5. Fixed:
    - Exception when an invalid command was sent to HUB (or sending command failed on HUB).
    - Messages for failed commands were not printed in main.
0.1.6. Fixed:
    - Ignore response code 200 when for sending commands
    - Upon reconnect, errors will be logged on 1st try only, any subsequent retry until connection is successful will
      only provide DEBUG log entries.
0.1.7. Fixed:
    - Fix traceback if no configuration retrieved or items missing from configuration (i.e. no activities)
    - Retrieve current activity only after retrieving configuration
0.1.8. Fixed:
    NOTE: This version will ONLY work with 4.15.250 or potentially higher. It will not work with lower versions!

    - Fix traceback if HUB info is not received.
    - Fix for new HUB version 4.15.250. (Thanks to `reneboer <https://github.com/reneboer>`__ for providing the quick fix).
0.1.9. Fixed:
    - Fixed "Network unreachable" or "Host unreachable" on certain installations (i.e. in Docker, HassIO)
0.1.10. Changed:
    - On reconnect the wait time will now start at 1 seconds and double every time with a maximum of 30 seconds.
    - Reconnect sometimes might not work if request to close was received over web socket but it never was closed.
0.1.11. Changed:
    - Timeout changed from 30 seconds to 5 seconds for network activity.
    - For reconnect, first wait for 1 second before starting reconnects.
0.1.12. Fixed/Changed:
    - Fixed issue where connection debug messages would not be shown on failed reconnects.
    - Added debug log entry when connected to HUB.
0.1.13.
    - Fixed further potential issue where on some OS's sockets would not be closed by now force closing them.
0.2.0. New:
    - Support for XMPP. If XMPP is enabled on Hub then that will be used, otherwise fallback to web sockets.
      There are no changes to the API for this. XMPP has to be explicitly enabled on the Harmony HUB.
      To do so open the Harmony app and go to: Menu > Harmony Setup > Add/Edit Devices & Activities > Remote & Hub > Enable XMPP
      Same steps can be followed to disable XMPP again.
    - Log entries from responsehandler class will now include ip address of HUB for easier identification
0.2.1 Fixed:
    - Fixed issue in sending commands to HUB wbe using XMPP protocol.
0.2.2 Fixed:
    - Added closing code from aiohttp for web sockets in debug logging if closing code provided.
    - Fixed further potential issue where on some OS's sockets would not be closed by now force closing them (merge from 0.1.13)
    - Fixed listen parameter as it would just exit instead of continuously wait.
0.2.3 Changed:
    - Updated requirement for slixmpp to 1.5.2 as that version works with Home Assistant
0.2.4 Fixed:
    - Friendly Name of Harmony HUB was not retrieved anymore, this is now available again
    - Remote ID was not retrieved anymore, this is now available again
    - If HUB disconnects when retrieving information then retrieval will be retried.
    - Executing aioharmony with option show_detailed_config will now show all config retrieved from HUB
0.2.5
    - Fixed: When using XMPP protocol the switching of an activity was not always discovered.
    - Fixed: Call to stop handlers will now be called when timeout occurs on disconnect
    - Changed: ClientCallbackType is now in aioharmony.const instead of aioharmony.harmonyclient.
    - Changed: default log level for aioharmony main is now ERROR
    - New: callback option new_activity_starting to allow a callback when a new activity is being started (new_activity is called when switching activity is completed)
    - New: 3 new HANDLER types have been added:
        - HANDLER_START_ACTIVITY_NOTIFY_STARTED: activity is being started
        - HANDLER_STOP_ACTIVITY_NOTIFY_STARTED: power off is started
        - HANDLER_START_ACTIVITY_NOTIRY_INPROGRESS: activity switch is in progress
    - New: Protocol to use can be specified (WEBSOCKETS or XMPP) to force specific protocol to be used. If not provided XMPP will be used unless not available then WEBSOCKETS will be used.
    - New: protocol used to connect can now be retrieved. It will return WEBSOCKETS when connected over web sockets or XMPP.
    - New: One can now supply multiple IP addresses for Harmony HUBs when using aioharmony main.
    - New: option activity_monitor for aioharmony main to allow just monitoring of activity changes
    - New: option logmodules for aioharmony main to specify the modules to put logging information for
0.2.6
    - Changed: If XMPP not enabled and no protocol provided then message will be DEBUG instead of Warning to enable XMPP.
    - Fixed: If connect using XMPP fails with for example Connection Refused then it will be logged now and connection marked as failed for reconnection.
0.2.7
    - Fixed: Registered handlers would not be unregistered after a timeout when sending commands to HUB.
    - Updated slixmpp from >= 1.5.2 to >= 1.7.0
    - Updated aiohttp from >= 3.4  to 3.7.3
0.2.8
    - Changed: Get remote_id from Hub without creating websocket connection
0.2.9
    - Fixed: Fixed exception in Pythin 3.10 by removing loop parameter (swolix)

TODO
----

* Redo discovery for asyncio. This will be done once XMPP is re-implemented by Logitech
* More items can be done from the Harmony iOS app; determining what could be done within the library as well
* Is it possible to update device configuration?

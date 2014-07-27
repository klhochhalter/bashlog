Bashlog
========================

Simple logging for bash scripts.

## Requirements ##
* bash => 4.0
* date command ( date format uses strftime )
* logger ( optional, from util-linux/syslog )

## Description ##
Bashlog provides an easy to use logging library that can be sourced from scripts. It allows
logging to an arbitrary file, to STDERR, or to a syslog facility. It supports eight logging
levels.

## Usage ##
Using bashlog only requires setting two variables at the beginning of a script, sourcing
bashlog, and calling the _bashlog function with the desired log level followed by the log message itself.

### Script Examples ###

```bash
#!/bin/bash

LOG_FILE='/var/log/myscript.log'
LOG_LEVEL='INFO'
. /usr/bin/bashlog

_bashlog INFO "This is a message logged at info."

_bashlog DEBUG "This message won't be logged."

```
The above example logs all messages at level INFO and greater to the file /var/log/myscript.log.

```bash
#!/bin/bash

LOG_FILE='STDERR'
LOG_LEVEL='CRITICAL'
LOG_TAG='myscript' # Optional tag/app name
. /usr/bin/bashlog

_bashlog INFO "This message will not log to STDERR."

_bashlog CRIT "But this one will."

_bashlog ALERT "So will this one."

```
Log all messages at level CRIT or greater to STDERR. If not present, LOG_TAG will default to `basename $0`.

```bash
#!/bin/bash

SYSLOG_FACILITY='local7'
LOG_TAG='myscript' # Optional syslog app name
LOG_LEVEL='WARN'
# source bashlog by $PATH environment variable
# rather than an absolute or relative path.
. bashlog

_bashlog INFO "This message will not log to syslog."

_bashlog WARN "This will log to syslog facility local7 with syslog numerical value of 4."

_bashlog CRITICAL "Log to local7 with numerical value of 2."

```
Use syslog facility local7, and set the app name to 'myscript'.

## Log Levels and Script Variables ##

### Log Levels ###
Bashlog supports eight logging levels, combining the levels from the Python logging module
and RFC 5424.

| Level              | Numeric value | Syslog numerical code | Origin                |
| -------------------|---------------|-----------------------|-----------------------|
| DEBUG              | 10            | 7                     | Python and RFC 5424   |
| INFO               | 20            | 6                     | Python and RFC 5424   |
| NOTICE             | 25            | 5                     | RFC 5424 specific     |
| WARN or WARNING    | 30            | 4                     | Python and RFC 5424   |
| ERR or ERROR       | 40            | 3                     | Python and RFC 5424   |
| CRIT or CRITICAL   | 50            | 2                     | Python and RFC 5424   |
| ALERT              | 60            | 1                     | RFC 5424 specific     |
| EMERG or EMERGENCY | 70            | 0                     | RFC 5424 specific     |

Setting the LOG_LEVEL in the script will log subsequent log messages at that value or
higher only.

### Script Variables ###
There are several variables which may be set in a script before sourcing bashlog.

LOG_TAG: sets the tag or application name that will appear in log messages.

LOG_FILE: Sets the full or relative path to the log file, or STDERR if the STDERR
keyword is used.

SYSLOG_FACILITY: Sets the syslog facility to use.

LOG_LEVEL: The default log level to use in the script.

#### LOG_TAG ####
LOG_TAG sets the tag or application name that will appear in log messages.

Example:

```bash
LOG_TAG='myusefulscript'
```

OPTIONAL: defaults to `basename $0`

#### LOG_FILE or SYSLOG_FACILITY ####
Specify the full or relative path to a log file, the STDERR keyword to force logging
to STDERR, or the syslog facility name to use. Bashlog only supports syslog facilities
local0 through local7.

If both LOG_FILE and SYSLOG_FACILITY are set, bashlog will issue a warning on STDERR and
fall back to logging to STDERR.

If neither LOG_FILE nor SYSLOG_FACILITY is set, bashlog will issue a warning to STDERR
and fall back to logging to STDERR.

Examples:

```bash
LOG_FILE='/var/log/mylog.log'
```

```bash
SYSLOG_FACILITY='local2'
```

```bash
# This will cause bashlog to use STDERR
LOG_FILE='/var/log/mylog.log'
SYSLOG_FACILITY='local2'
```

OPTIONAL: If one of these variables is not set, bashlog will fall back to
logging to STDERR.

#### LOG_LEVEL ####
Sets the log level for the script. Any log messages at this level or higher will be logged.
See "Log Levels" above.

OPTIONAL: If not set, LOG_LEVEL will default to 'INFO'.

#### DATETIME_FORMAT ####
Sets the date/time format to use when logging to a file or to STDERR.
Formatting of the date is handled by syslog if SYSLOG_FACILITY is set.

Examples:

```bash
# YYYY-mm-dd HH:MM-HHMM
DATETIME_FORMAT='+%F %R%z'
```
```bash
# YYYY-mm-dd HH:MM:SS-HH:MM
DATETIME_FORMAT='+%F %T%:z'
```
OPTIONAL: The default format is +%FT%T%:z ( ISO-8601 extended, eg. 2014-03-02T13:10:33-08:00 )


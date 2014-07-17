---
layout: post
title:  "Starting God with init.d"
date:   2013-08-17 19:00:00
categories: god init
---

1. Get the default gem set:

        $ rvm list default

2. Create a [wrapper script](http://rvm.io/integration/init-d) for the init script to use

        $ rvmsudo wrapper ruby-1.9.3-p374-railsexpress bootup god

3. Create the directory god will log to:

        $ sudo mkdir /var/log/god

4. Create the <code>/etc/init.d/god</code> init script:

        #!/bin/bash
        #
        # God under multi-user RVM
        #
        # http://god.rubyforge.org
        # http://beginrescueend.com/integration/god/
        #
        # chkconfig: - 85 15
        # description: Control the God gem. Expects the gem to \
        #              to be installed under a multi-user RVM \
        #              installation. Requires RVM wrapper.
        #

        ### BEGIN INIT INFO
        # Provides:          god
        # Required-Start:    $remote_fs $network
        # Required-Stop:     $remote_fs $network
        # Default-Start:
        # Default-Stop:
        # Description:       God under multi-user RVM
        # Short-Description: Control God under multi-user RVM
        ### END INIT INFO

        # Source function library.
        . /etc/rc.d/init.d/functions

        prog=god
        exec=/usr/local/rvm/bin/bootup_$prog

        # Default values.
        CONFIG_FILE=/etc/god.conf.rb
        LOG_FILE=/var/log/god/god.log
        LOG_LEVEL=info
        PID_FILE=/var/run/god/god.pid

        start_god() {
          [ -x $exec ] || exit 5
          echo -n $"Starting $prog: "
          status_god_quiet && echo -n "already running" && warning && echo && exit 0
          daemon --user root "$exec \
            -c $CONFIG_FILE \
            -l $LOG_FILE \
            --log-level $LOG_LEVEL \
            -P $PID_FILE >/dev/null"
          retval=$?
          echo
          return $retval
        }

        stop_god() {
          echo -n $"Stopping $prog: "
          retval=0
          if ! status_god_quiet ; then
            echo -n "already stopped" && warning
          else
            $exec quit >/dev/null
            retval=$?
          fi
          echo
          return $retval
        }

        restart_god() {
          stop_god
          start_god
        }

        status_god() {
          status -p $PID_FILE $prog
        }

        status_god_quiet() {
          status_god >/dev/null 2>&1
        }
          
        case "$1" in
          start)
            start_god
            ;;
          stop)
            stop_god
            ;;
          restart)
            restart_god
            ;;
          status)
            status_god
            ;;
          *)
            echo $"Usage: $0 {start|stop|status|restart}"
            exit 2
        esac

        exit $?

5. Be sure to change the `CONFIG_FILE` setting in the init script to match the location of the god config file you want to use.
6. Make it executable:

        # chmod +x /etc/init.d/god

7. Add it to `chkconfig`:

        $ chkconfig --add god
        $ chkconfig god on

8. Start god:

        # /etc/init.d/god start

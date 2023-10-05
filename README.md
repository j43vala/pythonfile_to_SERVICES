# From python to Daemon 
[link](https://levelup.gitconnected.com/from-python-to-daemon-how-to-turn-your-python-app-into-a-linux-service-controlled-by-systemd-d87b59adfe7a)

## Prerequisites


- A Linux machine with Systemd support (Systemd is available on most modern Linux distributions).

- A Python application that you want to run as a service.

## Create a service script 

        #!/bin/bash

        # Replace 'path_to_your_python_app' with the actual path to your Python application script.
        APP_PATH="/path/to/your/python/app.py"
        PID_FILE="/var/run/my_python_app.pid"

        start() {
            if [ -f $PID_FILE ]; then
                echo "The service is already running."
            else
                nohup python3 $APP_PATH &> /dev/null &
                echo $! > $PID_FILE
                echo "Service started."
            fi
        }

        stop() {
            if [ -f $PID_FILE ]; then
                kill $(cat $PID_FILE)
                rm $PID_FILE
                echo "Service stopped."
            else
                echo "The service is not running."
            fi
        }

        restart() {
            stop
            start
        }

        case "$1" in
            start)
                start
                ;;
            stop)
                stop
                ;;
            restart)
                restart
                ;;
            *)
                echo "Usage: $0 {start|stop|restart}"
        esac

Make the script executable by running:

        chmod +x my_python_app.sh

## 2. test the script

> ./my_python_app.sh start

> ./my_python_app.sh stop

> ./my_python_app.sh restart

## 3. Create the systemd service file 

        [Unit]
        Description=My Python App
        After=network.target

        [Service]
        Type=simple
        User=your_username
        WorkingDirectory=/path/to/your/python/app/directory
        ExecStart=/path/to/your/my_python_app.sh start
        ExecStop=/path/to/your/my_python_app.sh stop
        ExecReload=/path/to/your/my_python_app.sh restart
        Restart=always

        [Install]
        WantedBy=multi-user.target


## 4. Enable and Start the Service

        sudo cp my_python_app.service /etc/systemd/system/
        sudo systemctl enable my_python_app.service
        sudo systemctl start my_python_app.service

# Step 1: Create the Systemd Service Unit File

        [Unit]
        Description=My Python App
        After=network.target

        [Service]
        Type=simple
        User=your_username
        WorkingDirectory=/path/to/your/python/app/directory
        ExecStart=/usr/bin/python3 /path/to/your/python/app.py
        Restart=always

        [Install]
        WantedBy=multi-user.target


# Step 2: Enable and Start the Service

        sudo cp my_python_app.service /etc/systemd/system/
        sudo systemctl enable my_python_app.service
        sudo systemctl start my_python_app.service


# `file location` -

        sudo cp my_python_app.service /etc/systemd/system/
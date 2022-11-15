# inotify-info for Prometheus / Kubernetes

This project was forked from [mikesart/inotify-info](https://github.com/mikesart/inotify-info) and modified to work with [Prometheus](https://prometheus.io). This is a "workaround" since [Node Exporter](https://github.com/prometheus/node_exporter) does not support inotify stats yet. 

## Installation

    make
    make install

## Run the script

    /bin/inotify-info

The output consists of `inotify_max_user_instances`, `inotify_max_user_watches`, `inotify_user_watches_running` and `inotify_user_instances_running` as well as the hostname for each value.
    
    # HELP inotify_max_user_instances Maximum Number of inotify instances
    # TYPE inotify_max_user_instances gauge
    inotify_max_user_instances{hostname="ada"} 1024
    # HELP inotify_max_user_watches Maximum Number of inotify watches
    # TYPE inotify_max_user_watches gauge
    inotify_max_user_watches{hostname="ada"} 8192
    # HELP inotify_user_watches_running The Number of Running inotify watches
    # TYPE inotify_user_watches_running gauge
    inotify_user_watches_running{hostname="ada"} 2105
    # HELP inotify_user_instances_running The Number of running inotify instances
    # TYPE inotify_user_instances_running gauge
    inotify_user_instances_running{hostname="ada"} 195

## Use with Prometheus

You can use the [textfile-collector](https://github.com/prometheus/node_exporter#textfile-collector) to collect the script output. Simply create a cronjob for the root user to run the script. The crontab could look something like this: 

    * * * * * /bin/inotify-info > /some/directory/inotify.prom

Then add the following parameter to your prometheus startup script

    --collector.textfile.directory=/some/directory/

## Alerts and Visualization

You can use these parameters to create prometheus alerts or dashboards. Example alert:

    name: inotify watches limit
    expr: 100 / inotify_max_user_watches * inotify_user_watches_running > 80
    for: 10m
    annotations:
    summary: inotify watches limit reached 80%
# Initia-Monitoring-Tool
Monitor your Initia Node Performance 

To maintain healthy Node and Peers, Monitoring your your validator node through should be automated to trigger timely response. Here is a step-by-step Guide to do that:

# Set up your node, ensure it is running. Use my Guidline here:

         https://medium.com/@workspython/initia-node-set-up-09d7b1e9ca1d 

**Install Prometheus**

         wget https://github.com/prometheus/prometheus/releases/download/v2.30.0/prometheus-2.30.0.linux-amd64.tar.gz

**Extract the tarball:**

          tar xvf prometheus-2.30.0.linux-amd64.tar.gz
          
**Move the Binaries**

          sudo mv prometheus-2.30.0.linux-amd64/prometheus /usr/local/bin/
          sudo mv prometheus-2.30.0.linux-amd64/promtool /usr/local/bin/

**Configure Prometheus**

Open with "nano prometheus.yml" command and edit as follows:
          
              global:
                        scrape_interval: 15s 

                scrape_configs:
              - job_name: 'initia_validator'
             static_configs:
              - targets: ['your_ip:9091']  # Change to your node's metrics endpoint

 **To save the changes**
               PS: CTRL X + Y Enter

 **Start Prometheus:** 
 
                   prometheus --config.file=prometheus.yml

**Install Alertmanager
Download Alertmanager:**

            wget https://github.com/prometheus/alertmanager/releases/download/v0.23.0/alertmanager-0.23.0.linux-amd64.tar.gz

**Extract the tarbal**

            tar xvf alertmanager-0.23.0.linux-amd64.tar.gz

**Move the binaries:**

      sudo mv alertmanager-0.23.0.linux-amd64/alertmanager /usr/local/bin/

**Configure Alertmanager for Telegram**

          global:
            resolve_timeout: 5m

          route:
            group_by: ['alertname']
            group_wait: 30s
            group_interval: 5m
            repeat_interval: 3h
            receiver: 'telegram'

          receivers:
            - name: 'telegram'
              webhook_configs:
                - url: 'https://api.telegram.org/bot<YourBotToken>/sendMessage?chat_id=<YourChatID>'

**Set Up Telegram Bot:**

          Create a new bot on Telegram by talking to the BotFather.
          Get the bot token and your chat ID.
          Replace <YourBotToken> and <YourChatID> in the Alertmanager configuration with your actual bot token and chat ID.

**Set Up Alerts in Prometheus**

          groups:
            - name: initia_alerts
              rules:
                - alert: ValidatorDown
                  expr: up{job="initia_validator"} == 0
                  for: 5m
                  labels:
                    severity: critical
                  annotations:
                    summary: "Validator down"
                    description: "Your Initia validator node is down for more than 5 minutes."

Update **prometheus.yml** to include the alert rules:
          rule_files:
            - "alert_rules.yml"
**Start Alertmanager**

          alertmanager --config.file=alertmanager.yml

**Monitor and Receive Alerts**

              With Prometheus scraping your validator node metrics and Alertmanager configured to send alerts to Telegram, you will receive notifications on Telegram when your validator                                                   node goes down or meets any other alert conditions you have set up.


This setup provides an effective  way to monitor your Initia validator node and receive real-time alerts via Telegram.


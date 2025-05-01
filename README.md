Local to GITHUB (for New individual)  

  cd /path/to/your/project
  git init
  git add .
  git commit -m "Initial commit"
  git remote add origin https://github.com/Satyamdubey024/SafeSquidTask1.git
If the remote repository is empty:
  git push -u origin main

If the remote repository has existing commits (e.g., a README file), you might encounter a rejection. In that case:
  git pull origin main --allow-unrelated-histories
  git push -u origin main
or Force Push
  git push -u origin main --force








🧠 Code Explanation (Line-by-Line Highlights)
This section explains the logic used in each function of the monitoring script. Useful for beginners and contributors.


🧩 top_apps(): Show Top CPU and Memory Processes
Purpose: Lists the top 10 resource-consuming processes sorted by CPU and memory usage.

🔍 Core Commands Used:

ps -eo pid,comm,%cpu,%mem --sort=-%cpu,-%mem

     ps: Lists processes.
     -eo: Specify custom output fields (pid, comm, cpu, mem).
     --sort=-%cpu,-%mem: Sorts processes from highest to lowest CPU and memory usage.
     Then the output is formatted neatly in a table using printf.


🌐 network_monitoring(): Show Network Activity
Purpose: Displays current network connections, packet drops, and total data received/transmitted.

🔍 Core Commands:

ss -tun
cat /proc/net/dev
awk '{sum+=$2} END {print sum}'

      ss -tun: Lists TCP/UDP connections.
     /proc/net/dev: Contains statistics for each network interface.

     awk: Used to sum bytes and drops across all interfaces except lo (loopback).

  Then it converts total bytes to MB using:

awk "BEGIN {printf \"%.2f\", $rx_bytes / 1024 / 1024}"


💽 disk_usage(): Show Disk Space Usage
Purpose: Lists mounted disks with used space and highlights those above 80%.

🔍 Core Commands:

df -h --output=target,used,pcent

     df -h: Shows human-readable disk usage.
     --output: Filters only mount point, used space, and usage percentage.
 It uses if [ "$usage_num" -ge 80 ] to check usage and color the output red if over 80%.


⚙️ system_load(): Show Load Averages and CPU Stats
Purpose: Displays system load averages and CPU usage breakdown.

🔍 Core Concepts:

uptime | awk -F'load average: ' '{print $2}'
cat /proc/stat

      uptime: Shows load average (1, 5, 15 minutes).
      /proc/stat: Contains raw CPU stats like time spent on user/system/idle.

CPU usage percentages are calculated manually using:

bash

awk "BEGIN {printf \"%.1f\", ($user / $total) * 100}"


🧠 memory_usage(): RAM and Swap Usage
Purpose: Displays total, used, and free RAM and swap.

🔍 Core Commands:
bash

grep MemTotal /proc/meminfo

    /proc/meminfo: Contains detailed memory info in KB.
    MemTotal, MemAvailable, SwapTotal, SwapFree fields are extracted.
 Used memory = Total - Free
 
Then it's converted to MB with:

bash

awk "BEGIN {printf \"%.1f\", $value / 1024}"


🔍 process_monitoring(): Top Resource-Hungry Processes
Purpose: Shows:

Total number of processes
Top 5 by CPU
Top 5 by memory
🔍 Core Commands:
bash

ps -eo pid,user,%cpu,%mem,comm --sort=-%cpu
ps -eo pid,user,%mem,%cpu,comm --sort=-%mem

     ps -eo ...: Outputs custom fields
    --sort: Sorts based on CPU or memory
    awk: Formats the output into a clean table

🔧 service_monitoring(): Service Status Check
Purpose: Checks if services like sshd, nginx, apache2, or iptables are running.

🔍 Core Commands:
bash
systemctl is-active sshd

    systemctl is-active: Returns active, inactive, or not found
    Colors are used to indicate status:

      Green = Active
      Red = Inactive
      Yellow = Not found

🔁 Main Execution Logic
If no arguments are passed, the dashboard runs in an infinite loop and shows all sections:
bash

if [ $# -eq 0 ]; then
while true; do
...
done

     If arguments like -memory or -disk are passed, it runs only those sections once:
bash

./monitoring_system_resource.sh -disk -memory     <---run your code through this

     case "$1" handles which function to run based on the argument.



Switches Supported:

-topapps  Show top processes
-memory  Show memory usage
-disk  Show disk usage
-network  Show network activity
-sysload  Show system load
-process  Show process stats
-services  Show service status
-all  Show everything once

📦 Summary of Tools Used
Tool
Purpose
ps - Process info
df - Disk usage
ss - Socket stats (connections)
/proc/ - System stats (load, CPU, mem)
awk, grep, sed - Data parsing
uptime - Load average
systemctl - Service monitoring


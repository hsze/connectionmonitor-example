# connectionmonitor-example
# Using Connection Monitor to measure latency between endpoints
Connection Monitor is a tool within the [Azure Network Watcher](https://docs.microsoft.com/en-us/azure/network-watcher/network-watcher-monitoring-overview) suite of network management tools.  Connection Monitor provides unified end-to-end connection monitoring and supports hybrid and Azure cloud deployments.  The use cases supported are described [here](https://docs.microsoft.com/en-us/azure/network-watcher/connection-monitor-overview).
## Monitoring Scenario
Imagine a scenario where you have a mission-critical OnPrem server, and you would like to monitor the availability and latency of this server from various Azure regions, and you would even like to monitor the availability and latency within Azure.  You can easily set up Connection Monitor “tests” to collect these KPI. 
## Set Up and Configuration:
In order to set up Connection Monitor, you will need two workloads, typically VMs, located at the points of interest, which in our example above would be in an Azure Region and On-Prem.  In general, one of these two VMs will require a Network Watcher extension installed to perform the monitoring tests.  The other workload can have a Network Watcher extension, a Azure Monitor agent, or no agent at all.  There needs to be reachability between the two VMs, otherwise the reachability tests will fail.  (Of course, if the tests fail, that result would represent an issue in the network, which in itself is meaningful.)    
The main steps to set up Connection Monitor from the two workloads are described in the above doc:
1.	Set up a Network Watcher agent on the Azure workloads from which you are initiating monitoring.  If one of the two workloads will act as a responder only, and not initiate monitoring 
2.	Enable Network Watcher on your subscription.  This should automatically be done as of January 2021 in the Region when the VNET is created, but double check.
3.	Create a connection monitor
4.	Set up data analysis and alerts
5.	Diagnose issues in your network
### Setting up Connection Monitor 
In this example we are setting up monitoring from a Azure VM in EastUS to a VM that is connected via simulated VPN to On Prem.  
-	Azure VM = 10.61.1.4, in EastUS VNET
-	On-Prem VM = 172.20.3.4

First, we add the Network Watcher extension on the Azure VM
![Extension](/Images/image1.png)
 
But we will not install the Azure Monitor agent on the On-Prem VM as it does not need to initiate connections for our test.  This also represents an Appliance.
Next we verify that Network Watcher is enabled in the subscription and region of the VM:
![NW Enabled](/Images/image2.png)

Finally, we set up the Connection Monitor, which comprises of source and destination test groups, and test configurations.  In our case, the source test group comprises of only the Azure VM, and the destination comprises of only the On-Prem VM as a “External Address”.  The test configuration we will perform are ICMP and HTTP.  This results in a total of 2 tests between the Azure VM and the On Prem VM.  Note, the test configuration could easily be replicated to VMs in other Region by adding them to the Source Test Group.  For each workload we add into the Source Test Group, two tests will be run – one ICMP and one HTTP – to connect and measure performance and availability to On-Prem.
![Test Group](/Images/image3.png)

Finally we see results of the tests over a one hour period.  Note the period can extend back to 30 days.
For ICMP, we see RTT to On-Prem ranging from about 60 msec to 70 msec, with some spikes as high as 75 msec over a one hour period.
![ICMP Analysis](/Images/image4.png)
 
For HTTP, going higher up the stack, we see it generally ranging around 130 to 135 msec during the one hour interval, with a minute where the measurement spiked slightly higher to 145 msec.  One of the measurements also showed no response to the monitors (at beginning of period).
![HTTP Analysis](/Images/image5.png)
 

### Comparison with IntraRegion, within VNET
For simple comparison with Azure VM connection within the same region, we show 1-2ms from VM to VM:
![IntraRegion](/Images/image6.png)
 


TOPOLOGY VIEW
![Topology](/Images/image7.png)
 

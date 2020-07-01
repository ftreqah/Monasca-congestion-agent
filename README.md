Congestion

This section describes the congestion check performed by monasca-agent. Congestion check collects metrics from special iptable chain created by the agent called congestion. Metric names that are cross-posted to the infrastructure project will have the ecn. prefix.
Configuration

The congestion check requires a configuration file called congestion.yaml to be available in the agent conf.d configuration directory. An example of the configuration is given below.

auth_url is the keystone endpoint for authentication.

cache_dir will be used to cache ecn metrics in a file called congestion_status.json.

enable_ecn optional method that activates ecn marking in each machine. When the transmission equipment in the network encounters a congestion in its queues, it will not mark packets until the ecn is enabled by the sender. enable_ecn method ensures that ecn marking is enabled for both the sender and the receiver. This method is optional because the end user could enable ecn by changing the value of tcp_ecn from 0 to 2 or running 'echo 2 > /proc/sys/net/ipv4/tcp_ecn' in each machine.

enable_vm optional method that gathers ecn metrics ecn.packets, ecn.bytes, and ecn.cong.rate of each VM hosted in a remote compute. By default the agent collects ecn metrics of computes, activating this method add fine-grained control of the congestion.

s_factor Smoothing factor used to compute ecn congestion rate.

collect_period Period of time in sec to collect metrics and used also in ecn congestion rate calculation.

password is the password for the nova user.

project_name is the project/tenant to POST ecn metrics.

region_name is used to add the region dimension to metrics.

username is the username capable of making administrative nova calls.

instances are not used and should be empty in congestion.yaml because the plugin runs against all computes.

Sample config (congestion.yaml):

init_config:
  auth_url: http://10.10.10.10/identity_admin
  cache_dir: /dev/shm
  enable_ecn: true
  enable_vm: true
  password: admin
  project_name: service
  region_name: RegionOne
  username: nova
  collect_period: 30
  s_factor = 0.1

instances:
 - {}

The congestion checks return the following metrics:
Metric Name 	Dimensions 	Semantics
ecn.packets 	hostname, device, component=neutron, service=networking 	Number of packets marked as Congestion Experienced
ecn.bytes 	hostname, device, component=neutron, service=networking 	Number of bytes marked as Congestion Experienced
ecn.cong.rate 	hostname, device, component=neutron, service=networking 	Congestion rate in kbps calculated using ecn.bytes value

There is a detection plugin that should be used to configure this plugin. It is invoked as:

$ monasca-setup -d congestion

You can check the current congestion status of the network by simply running:

$ sudo monasca-collector -v check congestion

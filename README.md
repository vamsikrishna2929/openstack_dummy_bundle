# openstack_dummy_bundle

deployment flow

DEPLOYMENT FLOW: 


juju deploy --debug ./mysql-innodb-cluster.yaml --show-log --map-machines 1=1,2=2,3=3 

juju deploy --debug ./vault.yaml --show-log --map-machines 1=1,2=2,2=3 


VAULT UNSEAL & CONFIGURTION: 

unset no_proxy http_proxy https_proxy 

export VAULT_ADDR="http://<leader lxd ip>:8200" 

vault operator init -key-shares=6 -key-threshold=3 >> vault_data_$(date +%Y%m%dT%H%M%S) 

cat vault_data_$(date +%Y%m%dT%H%M%S) 

Unseal Key 1: <> 

Unseal Key 2: <> 

Unseal Key 3: <> 

Unseal Key 4: <> 

Unseal Key 5: <> 

Unseal Key 6: <> 

vault operator unseal <> 

vault operator unseal <> 

vault operator unseal <> 


export VAULT_TOKEN=”<root_token>” 

juju run vault/leader authorize-charm token=<generated_token> 

juju status vault 

juju run vault/leader generate-root-ca >> root_ca_$(date +%Y%m%dT%H%M%S) 

$OS_CACERT=<cert_path> 

vault operator raft join http://127.0.0.2:8200 

vault operator raft list-peers 

---------------- 
juju deploy --debug ./easyrsa.yaml --show-log --map-machines 1=1,2=2,2=3 

juju deploy --debug ./etcd.yaml --show-log --map-machines 1=1,2=2,2=3 

juju deploy --debug ./keystone.yaml --show-log --map-machines 1=1,2=2,2=3 

juju deploy --debug ./neutron-api.yaml --show-log --map-machines 1=1,2=2,2=3 

juju deploy --debug ./neutron-api-plugin-ovn.yaml --show-log --map-machines 1=1,2=2,2=3 

juju deploy --debug ./ovn-central.yaml --show-log --map-machines 1=1,2=2,2=3 

juju deploy --debug ./ovn-chassis.yaml --show-log --map-machines 1=1,2=2,2=3 

juju deploy --debug ./rabbitmq.yaml --show-log --map-machines 1=1,2=2,2=3 

juju deploy --debug ./designate.yaml --show-log --map-machines 1=1,2=2,2=3 

juju deploy --debug ./memcached.yaml --show-log --map-machines 1=1,2=2,2=3 

juju deploy --debug ./nova-cloud-controller.yaml --show-log --map-machines 1=1,2=2,2=3  

juju deploy --debug ./nova-compute.yaml --show-log --map-machines 1=1,2=2,2=3 

juju deploy --debug ./placement.yaml --show-log --map-machines 1=1,2=2,2=3 

juju deploy --debug ./openstack-dashboard.yaml --show-log --map-machines 1=1,2=2,2=3 

juju deploy --debug ./glance.yaml --show-log --map-machines 1=1,2=2,2=3 

juju deploy --debug ./cinder.yaml --show-log --map-machines 1=1,2=2,2=3 

juju deploy --debug ./ntp.yaml --show-log --map-machines 1=1,2=2,2=3 

juju deploy --debug ./heat.yaml --show-log --map-machines 1=1,2=2,2=3 

juju deploy --debug ./ceph.yaml.PAKKA --show-log --map-machines 1=1,2=2,2=3 

 

 

 

 

 

 

RELATIONS: 

 

Post each deployment we need to run corresponding relations present from  

relations.sh  

 

#!/bin/bash 

juju relate ovn-central:ovsdb-cms octavia:ovsdb-cms 

juju relate octavia-ovn-chassis:ovsdb-subordinate octavia:ovsdb-subordinate 

juju relate octavia-ovn-chassis:certificates vault:certificates 

juju relate octavia-ovn-chassis:ovsdb ovn-central:ovsdb 

juju relate neutron-api:neutron-load-balancer octavia:neutron-api 

juju relate rabbitmq-server:amqp octavia:amqp 

juju relate keystone:identity-service octavia:identity-service 

juju relate octavia-mysql-router:shared-db octavia:shared-db 

juju relate octavia rabbitmq-server 

juju relate octavia mysql-innodb-cluster 

juju relate octavia keystone 

juju relate octavia:neutron-openvswitch octavia-ovn-chassis:nova-compute 

juju relate octavia neutron-api 

juju relate octavia:cluster octavia:cluster 

juju relate octavia-hacluster:ha octavia:ha 

juju relate octavia-dashboard openstack-dashboard 

juju relate mysql-innodb-cluster:db-router octavia-mysql-router:db-router 

juju relate vault:certificates octavia:certificates 

juju relate barbican-hacluster:ha barbican:ha 

juju relate barbican-hacluster:hanode barbican-hacluster:hanode 

juju relate barbican-mysql-router:shared-db barbican:shared-db 

juju relate barbican:cluster barbican:cluster 

juju relate keystone:identity-service barbican:identity-service 

juju relate mysql-innodb-cluster:db-router barbican-mysql-router:db-router 

juju relate rabbitmq-server:amqp barbican:amqp 

juju relate barbican-vault:certificates vault:certificates 

juju relate barbican-vault:secrets barbican:secrets 

juju relate barbican-vault:secrets-storage vault:secrets 

juju relate keystone:identity-credentials cinder-volume:identity-credentials 

juju relate cinder-hacluster:ha cinder:ha                                           

juju relate cinder-hacluster:hanode cinder-hacluster:hanode                                 

juju relate cinder-mysql-router:shared-db cinder:shared-db                                        

juju relate cinder-volume:cluster cinder-volume:cluster                                   

juju relate cinder:cinder-volume-service nova-cloud-controller:cinder-volume-service             

juju relate cinder:cluster cinder:cluster                                          

juju relate dashboard-mysql-router:shared-db openstack-dashboard:shared-db                           

juju relate designate-bind:cluster designate-bind:cluster                                  

juju relate designate-bind:dns-backend designate:dns-backend                                   

juju relate designate-hacluster:ha designate:ha                                            

juju relate designate-hacluster:hanode designate-hacluster:hanode                              

juju relate designate-mysql-router:shared-db designate:shared-db                                     

juju relate designate:cluster designate:cluster                                       

juju relate designate:dnsaas neutron-api:external-dns                                

juju relate easyrsa:client etcd:certificates                                       

juju relate etcd:cluster etcd:cluster                                            

juju relate etcd:db vault:etcd                                              

juju relate glance-hacluster:ha glance:ha                                               

juju relate glance-hacluster:hanode glance-hacluster:hanode                                 

juju relate glance-mysql-router:shared-db glance:shared-db                                        

juju relate glance:cluster glance:cluster                                          

juju relate glance:image-service cinder:image-service                                    

juju relate glance:image-service nova-cloud-controller:image-service                     

juju relate glance:image-service nova-compute:image-service                              

juju relate heat-hacluster:ha heat:ha                                                 

juju relate heat-hacluster:hanode heat-hacluster:hanode                                   

juju relate heat-mysql-router:shared-db heat:shared-db                                          

juju relate heat:cluster heat:cluster                                            

juju relate keystone-hacluster:ha keystone:ha                                             

juju relate keystone-hacluster:hanode keystone-hacluster:hanode                               

juju relate keystone-mysql-router:shared-db keystone:shared-db                                      

juju relate keystone:cluster keystone:cluster                                        

juju relate keystone:identity-service cinder:identity-service                                 

juju relate keystone:identity-service designate:identity-service                              

juju relate keystone:identity-service glance:identity-service                                 

juju relate keystone:identity-service heat:identity-service                                   

juju relate keystone:identity-service neutron-api:identity-service                            

juju relate keystone:identity-service nova-cloud-controller:identity-service                  

juju relate keystone:identity-service octavia:identity-service                                

juju relate keystone:identity-service openstack-dashboard:identity-service                    

juju relate keystone:identity-service placement:identity-service                              

juju relate memcached:cache designate:coordinator-memcached                         

juju relate memcached:cache nova-cloud-controller:memcache                          

juju relate memcached:cluster memcached:cluster                                       

juju relate mysql-innodb-cluster:cluster mysql-innodb-cluster:cluster                            

juju relate mysql-innodb-cluster:coordinator mysql-innodb-cluster:coordinator                        

juju relate mysql-innodb-cluster:db-router cinder-mysql-router:db-router                           

juju relate mysql-innodb-cluster:db-router dashboard-mysql-router:db-router                        

juju relate mysql-innodb-cluster:db-router designate-mysql-router:db-router                        

juju relate mysql-innodb-cluster:db-router glance-mysql-router:db-router                           

juju relate mysql-innodb-cluster:db-router heat-mysql-router:db-router                             

juju relate mysql-innodb-cluster:db-router keystone-mysql-router:db-router                         

juju relate mysql-innodb-cluster:db-router neutron-mysql-router:db-router                          

juju relate mysql-innodb-cluster:db-router nova-mysql-router:db-router                             

juju relate mysql-innodb-cluster:db-router octavia-mysql-router:db-router                          

juju relate mysql-innodb-cluster:db-router placement-mysql-router:db-router                        

juju relate mysql-innodb-cluster:db-router vault-mysql-router:db-router                            

juju relate mysql-innodb-cluster:shared-db cinder-volume:shared-db                                 

juju relate mysql-innodb-cluster:shared-db designate:shared-db                                     

juju relate mysql-innodb-cluster:shared-db vault:shared-db                                         

juju relate neutron-api-hacluster:ha neutron-api:ha                                          

juju relate neutron-api-hacluster:hanode neutron-api-hacluster:hanode                            

juju relate neutron-api-plugin-ovn:neutron-plugin neutron-api:neutron-plugin-api-subordinate      

juju relate neutron-api:cluster neutron-api:cluster                                     

juju relate neutron-api:neutron-api nova-cloud-controller:neutron-api                       

juju relate neutron-api:neutron-load-balancer octavia:neutron-api                                     

juju relate neutron-mysql-router:shared-db neutron-api:shared-db                                   

juju relate nova-cloud-controller-hacluster:ha nova-cloud-controller:ha                                

juju relate nova-cloud-controller-hacluster:hanode nova-cloud-controller-hacluster:hanode                  

juju relate nova-cloud-controller:cluster nova-cloud-controller:cluster                           

juju relate nova-compute:cloud-compute nova-cloud-controller:cloud-compute                     

juju relate nova-compute:compute-peer nova-compute:compute-peer                               

juju relate nova-compute:juju-info ntp:juju-info                                           

juju relate nova-mysql-router:shared-db nova-cloud-controller:shared-db                         

juju relate ntp:ntp-peers ntp:ntp-peers                                           

juju relate octavia-hacluster:ha octavia:ha                                              

juju relate octavia-hacluster:hanode octavia-hacluster:hanode                                

juju relate octavia-mysql-router:shared-db octavia:shared-db                                       

juju relate octavia-ovn-chassis:ovsdb-subordinate octavia:ovsdb-subordinate                              

juju relate octavia:cluster octavia:cluster                                         

juju relate openstack-dashboard-hacluster:ha openstack-dashboard:ha                                  

juju relate openstack-dashboard-hacluster:hanode openstack-dashboard-hacluster:hanode                    

juju relate openstack-dashboard:cluster openstack-dashboard:cluster                             

juju relate openstack-dashboard:dashboard-plugin octavia-dashboard:dashboard                             

juju relate ovn-central:ovsdb octavia-ovn-chassis:ovsdb                               

juju relate ovn-central:ovsdb ovn-chassis:ovsdb                                       

juju relate ovn-central:ovsdb-cms neutron-api-plugin-ovn:ovsdb-cms                        

juju relate ovn-central:ovsdb-cms octavia:ovsdb-cms                                       

juju relate ovn-central:ovsdb-peer ovn-central:ovsdb-peer                                  

juju relate ovn-chassis:nova-compute nova-compute:neutron-plugin                             

juju relate placement-hacluster:ha placement:ha                                            

juju relate placement-hacluster:hanode placement-hacluster:hanode                              

juju relate placement-mysql-router:shared-db placement:shared-db                                     

juju relate placement:cluster placement:cluster                                       

juju relate placement:placement nova-cloud-controller:placement                         

juju relate rabbitmq-server:amqp cinder-volume:amqp                                      

juju relate rabbitmq-server:amqp cinder:amqp                                             

juju relate rabbitmq-server:amqp designate:amqp                                          

juju relate rabbitmq-server:amqp glance:amqp                                             

juju relate rabbitmq-server:amqp heat:amqp                                               

juju relate rabbitmq-server:amqp neutron-api:amqp                                        

juju relate rabbitmq-server:amqp nova-cloud-controller:amqp                              

juju relate rabbitmq-server:amqp nova-compute:amqp                                       

juju relate rabbitmq-server:amqp octavia:amqp                                            

juju relate rabbitmq-server:cluster rabbitmq-server:cluster                                 

juju relate vault-hacluster:ha vault:ha                                                

juju relate vault-hacluster:hanode vault-hacluster:hanode                                  

juju relate vault-mysql-router:shared-db vault:shared-db                                         

juju relate vault:certificates cinder:certificates                                     

juju relate vault:certificates designate:certificates                                  

juju relate vault:certificates glance:certificates                                     

juju relate vault:certificates heat:certificates                                       

juju relate vault:certificates keystone:certificates                                   

juju relate vault:certificates mysql-innodb-cluster:certificates                       

juju relate vault:certificates neutron-api-plugin-ovn:certificates                     

juju relate vault:certificates neutron-api:certificates                                

juju relate vault:certificates nova-cloud-controller:certificates                      

juju relate vault:certificates octavia-ovn-chassis:certificates                        

juju relate vault:certificates octavia:certificates                                    

juju relate vault:certificates openstack-dashboard:certificates                        

juju relate vault:certificates ovn-central:certificates                                

juju relate vault:certificates ovn-chassis:certificates                                

juju relate vault:certificates placement:certificates                                  

juju relate vault:cluster vault:cluster 

juju relate ceph-mon:osd ceph-osd:mon 

juju relate ceph-mon:client nova-compute:ceph 

juju relate ceph-mon:client glance:ceph 

juju relate cinder-ceph:storage-backend cinder:storage-backend 

juju relate cinder-ceph:ceph ceph-mon:client 

juju relate cinder-ceph:ceph-access nova-compute:ceph-access 

 

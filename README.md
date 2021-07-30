# assignmentsehatq
Assignment SehatQ for Lead of Infrastructure Engineer

Section A: System Administrator Questions 

1.	Correlation between HDD out of space and socket file is 
It will be running because the system can’t create log file for socket.
 
2.	By modifying sshd_config (/etc/ssh/sshd_config)
AllowUsers user@host
And don’t forget to restart ssh service

3.	$ ansible all -i inventory -a “uptime”

4.	First step is installing apache than enable SSL.

5.	For redirect https://www.sehatq.com/abc/def by modifying nginx.conf (/etc/nginx/nginx.conf)

Server {
	Location /def {
		Rewrite ^/def(.*)$ https://www.sehatq.com/$1 redirect
	}
}

6.	Error: 413 in nginx, this error usually happens because the upload file is to bigger than defined size in nginx config file.
To resolve it by modify nginx.conf (/etc/nginx/nginx.conf)Change or add if not exist: client_max_body_size 100M
Save it and don’t forget to restart nginx service

7.	The orphan process is the child process still running even though the parent process has terminated.
That happen cause in Linux OS any orphaned process will be immediately adopted by special init system process, the kernel sets the parent to init. A process can be orphaned unintentionally, such as when the parent process terminates or crashes.

Section B: AWS Questions 

1.	Regarding using memory centric (memory can expanded on storage), the best ec2 instance type is i3.
2.	Because two instance in the same region and same az, there is no need to do.
3.	Connecting two VPC in AWS by VPC Peering.
4.	Private Subnet on VPC:
    1.	Select Subnets in the left menu.
    2.	Click Create subnet and enter the following details:
        - VPC ID - Select the VPC you created above.
        - Subnet settings - Click Add new subnet three times to make four new subnet forms.
        - Subnet 1 of 4 - Enter the following details:
            * Subnet name - Enter public-subnet-1
            * Availability Zone - Select the first option (e.g. ap-southeast-2a)
            * Enter a CIDR block for each subnet that fits into your VPC CIDR block (e.g 10.0.0.0/24)
        - Subnet 2 of 4 - Enter the following details:
            * Subnet name - Enter public-subnet-2
            * Availability Zone - Select the second option (e.g. ap-southeast-2b). This must be in a different AZ to public-subnet-1.
            * Enter a CIDR block for each subnet that fits into your VPC CIDR block (e.g 10.0.1.0/24)
        - Subnet 3 of 4 - Enter the following details:
            * Subnet name - Enter private-subnet-1
            * Availability Zone - Select the first option (e.g. ap-southeast-2a)
            * Enter a CIDR block for each subnet that fits into your VPC CIDR block (e.g 10.0.2.0/24)
        - Subnet 4 of 4 - Enter the following details:
            * Subnet name - Enter private-subnet-2
            * Availability Zone - Select the second option (e.g. ap-southeast-2b). This must be in a different AZ to private-subnet-1.
            * Enter a CIDR block for each subnet that fits into your VPC CIDR block (e.g 10.0.3.0/24)
    3.	Click Create subnet.
5.	Subnet for External Load Balancer by using public subnet.
6.	NLB (Network Load Balancer) more faster than ALB (Application Load Balancer), Because the NLB just forward requests whereas the ALB examines the content of the http request header to determine where to route the request, The ALB is performing content based routing.
7.	The difference between S3 and S3 Glacier, S3 allow to store and retrieve any amount of data at any time and from anywhere on the network, S3 Glacier built for the long term storage and digital archiving of data as opposed to frequent utilization, slow speed for retrieving data.
S3 is good for storage solution and S3 Glacier is good for archival purpose.
8.	The difference between EBS and EFS are EBS is high-performance per-instance block storage system designed to act as storage system design to act as storage for a single EC2 instance, EFS is a highly scalable file storage system designed to provide flexible storage for multiple EC2 instances.
9.	By using AWS Management Console or using the iam-servercertupload command.

Section C: Kubernetes Questions 

1.	The difference between ingress controller and ingress resources are
Ingress controller is responsible for reading Ingress Resource information and processing that data accordingly
2.	Reading data information and processing
3.	Problem from memory and cpu request.
4.	- forward the etcd service port and process in the background
      $ kubectl port-forward --namespace default service
    - create directory for backup
      $ mkdir etcd-backup
      $ chmod o+w etcd-backup
      $ cd etcd-backup
    - created snapshot
      $ docker run -it -env ALLOW_NONE_AUTHENTICATION=yes --rm --network host -v $(pwd):/backup bitnami/etcd etcdctl --user root:PASSWORD --endpoints http://127.0.01:2379 snapshot save /tmp/etcd-backup.db
    - running backup
      $ sudo chmod -R 644 mybackup
5.  - Create the POD redis-storage
      $ kubectl apply -f https://sehatq.com/pods/storage/redis.yaml
    - Verify POD
      $ kubectl get pod redis --watch
    - Running 
      $ kubectl exec -it redis -- /bin/bash
    - create a file
      root@redis:/data# cd /data/redis/
      root@redis:/data/redis# echo Hello > test-file
6. $ kubectl get nodes -o json | jq '.items[].status.addresses [] | select (.type=="internalIP") | .address'
7. Taint are the opposite, they allow a node to repel a set of pods. Tolerations are applied to pods, and allow (but do not required) the pods to schedule onto nodes with matching taints. Taints and tolerations work together to ensure that pods are not scheduled onto inappropriate nodes

Section D: Use Case

- redis-config.yaml
  apiVersion: v1
  kind: ConfigMap   metada:
     name: redis-config
  data:
     redis-config:
        maxmemory 2mb
        maxmemory-policy allkey-lru
        
- service-a.yaml
  apiVersion: v1
  kind: Service
  metadata:
     name: service-a
  spec: 
     selector:
        app: nodejs
     ports:
        - name: http
          protocol: TCP
          port: 80
          targetPOrt: 9376
        - name: https
          protocol: TCP
          port: 443
          targetPort: 9377
          
 - service-b.yaml
   apiVersion: v1
   kind: Service
   metadata: 
      name: service-b
   spec: 
      selector:
         app: golang
         
 - service-c.yaml
   apiVersion: v1
   kind: Service
   metadata:
      name: service-c
   spec:
      selector:
         app: golang
   externalIPs:
      - 80.11.12.13
        


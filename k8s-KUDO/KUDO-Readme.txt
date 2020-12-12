#########################################################
    Kubernetes (Minikube) install on MAC OS
    https://kubernetes.io/docs/tasks/tools/install-minikube/
#########################################################

  501  sysctl -a | grep -E --color 'machdep.cpu.features|VMX'
  502  pwd
  503  ls
  504  brew install kubectl

  506  kubectl version --client

  # make sure virtualbox is installed

  508  brew install minikube
  509  minikube version

  514  minikube status


  515  minikube start --driver=virtualbox
  516  minikube status
  
  518  kubectl get pods
  519  kubectl get nodes
  
  
#########################################################
    Upgrade kubectl on MAC OS
#########################################################
  538  curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/darwin/amd64/kubectl
  539  chmod +x ./kubectl
  540  sudo mv ./kubectl /usr/local/bin/kubectl
  541  kubectl version

#########################################################
    Upgrade Minikube on MAC OS
#########################################################
  567  sudo rm -rf /usr/local/bin/minikube
  568  brew update
  577  sudo rm -rf ~/.minikube
  578  brew install minikube
  579  brew link minikube
  580  brew install minikube
  581  minikube get-k8s-versions
  582  minikube version
  583  minikube start
  $ minikube start
😄  minikube v1.15.1 on Darwin 11.0.1
✨  Automatically selected the hyperkit driver
💾  Downloading driver docker-machine-driver-hyperkit:
    > docker-machine-driver-hyperkit.sha256: 65 B / 65 B [---] 100.00% ? p/s 0s
    > docker-machine-driver-hyperkit: 11.44 MiB / 11.44 MiB  100.00% 7.87 MiB p
🔑  The 'hyperkit' driver requires elevated permissions. The following commands will be executed:

    $ sudo chown root:wheel /Users/xxxxx/.minikube/bin/docker-machine-driver-hyperkit
    $ sudo chmod u+s /Users/xxxx/.minikube/bin/docker-machine-driver-hyperkit


💿  Downloading VM boot image ...
    > minikube-v1.15.0.iso.sha256: 65 B / 65 B [-------------] 100.00% ? p/s 0s
    > minikube-v1.15.0.iso: 181.00 MiB / 181.00 MiB [ 100.00% 10.81 MiB p/s 17s
👍  Starting control plane node minikube in cluster minikube
💾  Downloading Kubernetes v1.19.4 preload ...
    > preloaded-images-k8s-v6-v1.19.4-docker-overlay2-amd64.tar.lz4: 486.35 MiB
🔥  Creating hyperkit VM (CPUs=2, Memory=4000MB, Disk=20000MB) ...
🐳  Preparing Kubernetes v1.19.4 on Docker 19.03.13 ...
🔎  Verifying Kubernetes components...
🌟  Enabled addons: storage-provisioner, default-storageclass
🏄  Done! kubectl is now configured to use "minikube" cluster and "default" namespace by default
$

$ kubectl version
Client Version: version.Info{Major:"1", Minor:"20", GitVersion:"v1.20.0", GitCommit:"af46c47ce925f4c4ad5cc8d1fca46c7b77d13b38", GitTreeState:"clean", BuildDate:"2020-12-08T17:59:43Z", GoVersion:"go1.15.5", Compiler:"gc", Platform:"darwin/amd64"}
Server Version: version.Info{Major:"1", Minor:"19", GitVersion:"v1.19.4", GitCommit:"d360454c9bcd1634cf4cc52d1867af5491dc9c5f", GitTreeState:"clean", BuildDate:"2020-11-11T13:09:17Z", GoVersion:"go1.15.2", Compiler:"gc", Platform:"linux/amd64"}
$
  
#########################################################
   KUDO installation
   (on MAC..in this case)
#########################################################

  brew tap kudobuilder/tap
  brew install kudo-cli
  
  # Install a cert-manager
  # On Kubernetes version v1.16+
  kubectl apply -f https://github.com/jetstack/cert-manager/releases/download/v1.1.0/cert-manager.yaml
  
  
  # kubectl-kudo init
  shreyanss-mbp:kudo BytePython$ kubectl-kudo init
  $KUDO_HOME has been configured at /Users/BytePython/.kudo
  ✅ installed crds  
  ✅ installed namespace
  ✅ installed service account
  ✅ installed webhook
  ✅ installed kudo controller
  shreyanss-mbp:$
  
	shreyanss-mbp:kudo BytePython$ kubectl get ns
	NAME              STATUS   AGE
	cert-manager      Active   14d
	default           Active   18d
	kube-node-lease   Active   18d
	kube-public       Active   18d
	kube-system       Active   18d
	kudo-system       Active   14d   <<<<<<<
	shreyanss-mbp:kudo BytePython$

	shreyanss-mbp:kudo BytePython$ kubectl get crd
	NAME                                  CREATED AT
	certificaterequests.cert-manager.io   2020-06-23T23:25:19Z
	certificates.cert-manager.io          2020-06-23T23:25:19Z
	challenges.acme.cert-manager.io       2020-06-23T23:25:19Z
	clusterissuers.cert-manager.io        2020-06-23T23:25:19Z
	instances.kudo.dev                    2020-06-23T23:25:44Z
	issuers.cert-manager.io               2020-06-23T23:25:19Z
	myapp.example.nokia.com               2020-06-25T21:43:36Z
	operators.kudo.dev                    2020-06-23T23:25:44Z <<<<<<<
	operatorversions.kudo.dev             2020-06-23T23:25:44Z
	orders.acme.cert-manager.io           2020-06-23T23:25:19Z
	shreyanss-mbp:kudo BytePython$

	shreyanss-mbp:kudo BytePython$ k get all --all-namespaces | grep kudo
	kudo-system    pod/kudo-controller-manager-0                 1/1     Running   0          14d
	kudo-system    service/kudo-controller-manager-service   ClusterIP   10.111.185.173   <none>        443/TCP                  14d
	kudo-system   statefulset.apps/kudo-controller-manager   1/1     14d
	shreyanss-mbp:kudo BytePython

  	shreyanss-mbp:kudo BytePython$ kubectl get -n kudo-system pod
	NAME                        READY   STATUS    RESTARTS   AGE
	kudo-controller-manager-0   1/1     Running   0          14d
	shreyanss-mbp:kudo BytePython$
	

#########################################################
   KUDO Help
   (on MAC..in this case)
#########################################################
shreyanss-mbp:$ kubectl-kudo help
KUDO CLI and future sub-commands can be used to manipulate, inspect and troubleshoot KUDO-specific CRDs
and serves as an API aggregation layer.

Usage:
  kubectl-kudo [command]

Examples:
  # Install a KUDO package from the official GitHub repo.
  kubectl kudo install <name> [flags]

  # View plan history of a specific package
  kubectl kudo plan history <name> [flags]

  # View all plan history of a specific package
  kubectl kudo plan history [flags]

  # Run integration tests against a Kubernetes cluster or mocked control plane.
  kubectl kudo test

  # Get instances
  kubectl kudo get instances [flags]

  # View plan status
  kubectl kudo plan status [flags]

  # View KUDO version
  kubectl kudo version


Available Commands:
  get         Gets all available instances.
  help        Help about any command
  init        Initialize KUDO on both the client and server
  install     Install an official KUDO package.
  package     package an operator, or understand it's content
  plan        View all available plans.
  repo        Add, list, remove, and index kudo repositories.
  search      Search for operators in repository.
  test        Test KUDO and Operators.
  uninstall   Uninstall a KUDO package.
  update      Update KUDO operator instance.
  upgrade     Upgrade KUDO package.
  version     Print the current KUDO package version.

Flags:
  -h, --help                  help for kubectl-kudo
      --home string           Location of your KUDO config. (default "/Users/BytePython/.kudo")
      --kubeconfig string     Path to your Kubernetes configuration file. (default "/Users/BytePython/.kube/config")
  -n, --namespace string      Target namespace for the object. (default "default")
      --request-timeout int   Request timeout value, in seconds.  Defaults to 0 (unlimited)
  -v, --v                     Log level for V logs
      --validate-install      Validate KUDO installation before running. (default true)

Use "kubectl-kudo [command] --help" for more information about a command.
shreyanss-mbp:$
  
#####################################
	How to Create an Operator from Scratch
	https://kudo.dev/docs/runbooks/admin/create-operator.html#preconditions
#####################################

kudo BytePython$ mkdir first-operator
kudo BytePython$ cd first-operator/
first-operator BytePython$ 

shreyanss-mbp:~ $ kubectl-kudo package new first-operator

tree
.
└── operator
    ├── operator.yaml
    └── params.yaml

1 directory, 2 files

shreyanss-mbp:~ $ kubectl-kudo package add task
Task Name: myapp
Task Resource: deployment
✔ Task Resource: deployment
✗ Add another Resource: 

shreyanss-mbp:~ $ kubectl-kudo package add plan
Plan Name: deploy
✔ serial
Phase 1 name: main
✔ parallel
Step 1 name: everything
✔ myapp
✗ Add another Task: 
✗ Add another Step: 
✗ Add another Phase: 
 

shreyanss-mbp:~ $ kubectl-kudo package add parameter
Parameter Name: replicas
Default Value: 3
Display Name: 
Description: number of app replicas
✔ false
✗ Add Trigger Plan: 
shreyanss-mbp:first-operator BytePython$


shreyanss-mbp:~ $ cat << EOF > operator/templates/deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: {{ .Params.replicas }}
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx:1.7.9
          ports:
            - containerPort: 80
EOF
$ 


################################
 How to package a KUDO operator
 https://kudo.dev/docs/runbooks/admin/create-kudo-package.html#steps
################################

In order to distribute a KUDO operator the files are packaged together in a compressed tarball. The KUDO CLI provides a mechanism to create this package format while verifying the integrity of the operator.


shreyanss-mbp:~ $ mkdir ~/kudo/repo

# Note: added 'kubernetesVersion: 1.10.11' to the ~/kudo/first-operator/operator/operator.yaml to tackle this issue:
	Errors                                            
	"kubernetesVersion" is required and must be semver
	Error: found 1 package verification errors

shreyanss-mbp:~ $ kubectl-kudo package create first-operator/operator/ --destination=~/kudo/repo/
package is valid
Package created: /Users/BytePython/kudo/repo/first-operator-0.1.0.tgz
shreyanss-mbp:kudo BytePython$ ls ~/kudo/repo/
first-operator-0.1.0.tgz
$

shreyanss-mbp:~ $ ls ~/kudo/repo
first-operator-0.1.0.tgz



	shreyanss-mbp:kudo BytePython$ kubectl get all --all-namespaces
	NAMESPACE      NAME                                          READY     STATUS    RESTARTS   AGE
	cert-manager   pod/cert-manager-7747db9d88-gxpl6             1/1       Running   0          47m
	cert-manager   pod/cert-manager-cainjector-87c85c6ff-t7nv7   1/1       Running   0          47m
	cert-manager   pod/cert-manager-webhook-64dc9fff44-7m5ks     1/1       Running   0          47m
	kube-system    pod/coredns-66bff467f8-m4xhj                  1/1       Running   0          3d
	kube-system    pod/coredns-66bff467f8-vf5lm                  1/1       Running   0          3d
	kube-system    pod/etcd-minikube                             1/1       Running   0          3d
	kube-system    pod/kube-apiserver-minikube                   1/1       Running   0          3d
	kube-system    pod/kube-controller-manager-minikube          1/1       Running   0          3d
	kube-system    pod/kube-proxy-sjbwb                          1/1       Running   0          3d
	kube-system    pod/kube-scheduler-minikube                   1/1       Running   0          3d
	kube-system    pod/storage-provisioner                       1/1       Running   0          3d
	kudo-system    pod/kudo-controller-manager-0                 1/1       Running   0          46m

	NAMESPACE      NAME                                      TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                  AGE
	cert-manager   service/cert-manager                      ClusterIP   10.106.230.229   <none>        9402/TCP                 47m
	cert-manager   service/cert-manager-webhook              ClusterIP   10.96.95.182     <none>        443/TCP                  47m
	default        service/kubernetes                        ClusterIP   10.96.0.1        <none>        443/TCP                  3d
	kube-system    service/kube-dns                          ClusterIP   10.96.0.10       <none>        53/UDP,53/TCP,9153/TCP   3d
	kudo-system    service/kudo-controller-manager-service   ClusterIP   10.111.185.173   <none>        443/TCP                  46m

	NAMESPACE     NAME                        DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR            AGE
	kube-system   daemonset.apps/kube-proxy   1         1         1         1            1           kubernetes.io/os=linux   3d

	NAMESPACE      NAME                                      DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
	cert-manager   deployment.apps/cert-manager              1         1         1            1           47m
	cert-manager   deployment.apps/cert-manager-cainjector   1         1         1            1           47m
	cert-manager   deployment.apps/cert-manager-webhook      1         1         1            1           47m
	kube-system    deployment.apps/coredns                   2         2         2            2           3d

	NAMESPACE      NAME                                                DESIRED   CURRENT   READY     AGE
	cert-manager   replicaset.apps/cert-manager-7747db9d88             1         1         1         47m
	cert-manager   replicaset.apps/cert-manager-cainjector-87c85c6ff   1         1         1         47m
	cert-manager   replicaset.apps/cert-manager-webhook-64dc9fff44     1         1         1         47m
	kube-system    replicaset.apps/coredns-66bff467f8                  2         2         2         3d

	NAMESPACE     NAME                                       DESIRED   CURRENT   AGE
	kudo-system   statefulset.apps/kudo-controller-manager   1         1         46m
	shreyanss-mbp:kudo BytePython$ 

####################################################
	How to host an Operator in a local repository
	https://kudo.dev/docs/runbooks/admin/local-repo.html#preconditions
####################################################

Build Local Index File
	shreyanss-mbp:kudo BytePython$ kubectl-kudo repo index ~/kudo/repo/
	index /Users/BytePython/kudo/repo/index.yaml created.

	shreyanss-mbp:kudo BytePython$ cd repo
	shreyanss-mbp:repo BytePython$ ls
	first-operator-0.1.0.tgz	index.yaml
	shreyanss-mbp:repo BytePython$

Run Repository HTTP Server
	shreyanss-mbp:repo BytePython$ python3 -m http.server 3000
	Serving HTTP on :: port 3000 (http://[::]:3000/) ...
	::1 - - [25/Jun/2020 15:19:48] "GET /index.yaml HTTP/1.1" 200 -
	::1 - - [25/Jun/2020 15:31:16] "GET /index.yaml HTTP/1.1" 200 -

Add the local repository to KUDO client
	shreyanss-mbp:kudo BytePython$ kubectl kudo repo add local http://localhost:3000
	"local" has been added to your repositories
	shreyanss-mbp:kudo BytePython$

Set the local repository to default KUDO context
	shreyanss-mbp:kudo BytePython$ kubectl kudo repo context local

Confirm KUDO context using repo list command
Note: The * next to local indicates that it is the default context for the KUDO client.
	shreyanss-mbp:kudo BytePython$ kubectl kudo repo list
	NAME     	URL                                                  
	community	https://kudo-repository.storage.googleapis.com/0.10.0
	*local   	http://localhost:3000                                
	shreyanss-mbp:kudo BytePython$
	
####################################################
	Install the Operator
####################################################
	shreyanss-mbp:kudo BytePython$ pwd
	/Users/BytePython/kudo
	shreyanss-mbp:
	
	shreyanss-mbp:kudo BytePython$ tree
	.
	├── Notes\ -\ creating\ first-operator\ using\ KUDO.txt
	├── first-operator
	│   └── operator
	│       ├── operator.yaml
	│       ├── params.yaml
	│       └── templates
	│           └── deployment.yaml
	└── repo
	    ├── first-operator-0.1.0.tgz
	    └── index.yaml

	4 directories, 6 files
	shreyanss-mbp:kudo BytePython$
	
	shreyanss-mbp:kudo BytePython$ cat first-operator/operator/params.yaml 
	apiVersion: kudo.dev/v1beta1
	parameters:
	- default: "3"
	  description: number of app replicas
	  name: replicas
	shreyanss-mbp:kudo BytePython$

	shreyanss-mbp:kudo BytePython$ cat first-operator/operator/templates/deployment.yaml 
	apiVersion: apps/v1
	kind: Deployment
	metadata:
	  name: nginx-deployment
	spec:
	  selector:
	    matchLabels:
	      app: nginx
	  replicas: {{ .Params.replicas }}
	  template:
	    metadata:
	      labels:
		app: nginx
	    spec:
	      containers:
		- name: nginx
		  image: nginx:1.7.9
		  ports:
		    - containerPort: 80
	shreyanss-mbp:kudo BytePython$
	
  ###### Install:
	shreyanss-mbp:kudo BytePython$ kubectl kudo install first-operator/operator
	operator default/first-operator created
	operatorversion default/first-operator-0.1.0 created
	instance default/first-operator-instance created
	shreyanss-mbp:kudo BytePython$
	
	shreyanss-mbp:kudo BytePython$ k kudo get instances
	List of current installed instances in namespace "default":
	.
	└── first-operator-instance
	shreyanss-mbp:kudo BytePython$
	
	shreyanss-mbp:kudo BytePython$ kubectl get all
	NAME                                    READY   STATUS    RESTARTS   AGE
	pod/nginx-deployment-75f56db4db-bw7ms   1/1     Running   0          13m
	pod/nginx-deployment-75f56db4db-rjfss   1/1     Running   0          13m
	pod/nginx-deployment-75f56db4db-zsprg   1/1     Running   0          13m

	NAME                 TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
	service/kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP          18d

	NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
	deployment.apps/nginx-deployment   3/3     3            3           13m

	NAME                                          DESIRED   CURRENT   READY   AGE
	replicaset.apps/nginx-deployment-75f56db4db   3         3         3       13m
	shreyanss-mbp:kudo BytePython$ 
	

####################################################
	Uninstall the Operator
####################################################
	shreyanss-mbp:kudo BytePython$ k kudo get instances
	List of current installed instances in namespace "default":
	.
	└── first-operator-instance
	shreyanss-mbp:kudo BytePython$

  ###### Uninstall:
	shreyanss-mbp:kudo BytePython$ k kudo uninstall --instance first-operator-instance
	instance.kudo.dev/v1beta1/first-operator-instance deleted

	shreyanss-mbp:kudo BytePython$

	shreyanss-mbp:kudo BytePython$ k get all
	NAME                 TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
	service/kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP          18d
	shreyanss-mbp:kudo BytePython$


####################################################
	Uninstalling KUDO
	https://kudo.dev/docs/runbooks/admin/remove-kudo.html#preconditions
####################################################
Note: 
  Removing KUDO Removes all KUDO resources
  The removal of KUDO from the cluster will remove all KUDO operators on the cluster and all deployments associated with those operators.

	kubectl kudo init --dry-run --output yaml | kubectl delete -f -

  ###### Sample output:
	$ kubectl kudo init --dry-run --output yaml | kubectl delete -f -
	customresourcedefinition.apiextensions.k8s.io "operators.kudo.dev" deleted
	customresourcedefinition.apiextensions.k8s.io "operatorversions.kudo.dev" deleted
	customresourcedefinition.apiextensions.k8s.io "instances.kudo.dev" deleted
	namespace "kudo-system" deleted
	serviceaccount "kudo-manager" deleted
	clusterrolebinding.rbac.authorization.k8s.io "kudo-manager-rolebinding" deleted

#############
  Reference
#############
https://kudo.dev/docs/
https://github.com/kudobuilder/kudo/issues/1557
https://www.cncf.io/wp-content/uploads/2019/10/KUDO-CNCF-Webinar.pdf

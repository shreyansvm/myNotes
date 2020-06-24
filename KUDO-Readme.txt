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
   KUDO installation
   (on MAC..in this case)
#########################################################

  brew tap kudobuilder/tap
  brew install kudo-cli
  
  # Install a cert-manager
  kubectl apply --validate=false -f https://github.com/jetstack/cert-manager/releases/download/v0.15.1/cert-manager.yaml
  
  # kubectl-kudo init
  shreyanss-mbp:kudo BytePython$ kubectl-kudo init
  $KUDO_HOME has been configured at /Users/BytePython/.kudo
  ✅ installed crds  
  ✅ installed namespace
  ✅ installed service account
  ✅ installed webhook
  ✅ installed kudo controller
  shreyanss-mbp:$
  
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


Add the local repository to KUDO client

Set the local repository to default KUDO context

Confirm KUDO context


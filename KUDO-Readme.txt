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
  

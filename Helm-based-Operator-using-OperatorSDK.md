

# Install Operator-sdk
```
	brew install operator-sdk
	operator-sdk version
```

# Create a new Operator project
```
	shreyanss-mbp:operator-sdk BytePython$ operator-sdk new nginx-operator --api-version=example.com/v1alpha1 --kind=Nginx --type=helm
	INFO[0000] Creating new Helm operator 'nginx-operator'. 
	INFO[0000] Created helm-charts/nginx                    
	INFO[0000] Generating RBAC rules                        
	WARN[0000] The RBAC rules generated in deploy/role.yaml are based on the chart's default manifest. Some rules may be missing for resources that are only enabled with custom values, and some existing rules may be overly broad. Double check the rules generated in deploy/role.yaml to ensure they meet the operator's permission requirements. 
	INFO[0000] Created build/Dockerfile                     
	INFO[0000] Created deploy/service_account.yaml          
	INFO[0000] Created deploy/role.yaml                     
	INFO[0000] Created deploy/role_binding.yaml             
	INFO[0000] Created deploy/operator.yaml                 
	INFO[0000] Created deploy/crds/example.com_v1alpha1_nginx_cr.yaml 
	INFO[0000] Generated CustomResourceDefinition manifests. 
	INFO[0000] Project creation complete. 
```

# 'nginx-operator' project is created
This creates the nginx-operator project specifically for watching the Nginx resource with APIVersion example.com/v1apha1 and Kind Nginx.
	
```
	shreyanss-mbp:operator-sdk BytePython$ ls
	nginx-operator

	shreyanss-mbp:operator-sdk BytePython$ tree
	.
	└── nginx-operator
	    ├── build
	    │   └── Dockerfile
	    ├── deploy
	    │   ├── crds
	    │   │   ├── example.com_nginxes_crd.yaml
	    │   │   └── example.com_v1alpha1_nginx_cr.yaml
	    │   ├── operator.yaml
	    │   ├── role.yaml
	    │   ├── role_binding.yaml
	    │   └── service_account.yaml
	    ├── helm-charts
	    │   └── nginx
	    │       ├── Chart.yaml
	    │       ├── charts
	    │       ├── templates
	    │       │   ├── NOTES.txt
	    │       │   ├── _helpers.tpl
	    │       │   ├── deployment.yaml
	    │       │   ├── hpa.yaml
	    │       │   ├── ingress.yaml
	    │       │   ├── service.yaml
	    │       │   ├── serviceaccount.yaml
	    │       │   └── tests
	    │       │       └── test-connection.yaml
	    │       └── values.yaml
	    └── watches.yaml

	9 directories, 18 files
	shreyanss-mbp:operator-sdk BytePython$
```

# Contents of 'nginx-operator' project:
You can see that in the above 'example.com_nginxes_crd.yaml' and 'example.com_v1alpha1_nginx_cr.yaml' are the Custom Resource Definition (CRD) and Custom Resource specifications. 

'operator.yaml' is the Operator manifest which will be run on the cluster along with the Role, RoleBinding and ServiceAccounts used for deploying Operator.

```
	shreyanss-mbp:operator-sdk BytePython$ cat nginx-operator/deploy/crds/example.com_nginxes_crd.yaml 
	apiVersion: apiextensions.k8s.io/v1
	kind: CustomResourceDefinition
	metadata:
	  name: nginxes.example.com
	spec:
	  group: example.com
	  names:
	    kind: Nginx
	    listKind: NginxList
	    plural: nginxes
	    singular: nginx
	  scope: Namespaced
	  versions:
	  - name: v1alpha1
	    schema:
	      openAPIV3Schema:
	        type: object
	        x-kubernetes-preserve-unknown-fields: true
	    served: true
	    storage: true
	    subresources:
	      status: {}
	shreyanss-mbp:operator-sdk BytePython$ 

	shreyanss-mbp:operator-sdk BytePython$ cat nginx-operator/deploy/crds/example.com_v1alpha1_nginx_cr.yaml 
	apiVersion: example.com/v1alpha1
	kind: Nginx
	metadata:
	  name: example-nginx
	spec:
	  # Default values copied from <project_dir>/helm-charts/nginx/values.yaml
	  
	  affinity: {}
	  autoscaling:
	    enabled: false
	    maxReplicas: 100
	    minReplicas: 1
	    targetCPUUtilizationPercentage: 80
	  fullnameOverride: ""
	  image:
	    pullPolicy: IfNotPresent
	    repository: nginx
	    tag: ""
	  imagePullSecrets: []
	  ingress:
	    annotations: {}
	    enabled: false
	    hosts:
	    - host: chart-example.local
	      paths: []
	    tls: []
	  nameOverride: ""
	  nodeSelector: {}
	  podAnnotations: {}
	  podSecurityContext: {}
	  replicaCount: 1
	  resources: {}
	  securityContext: {}
	  service:
	    port: 80
	    type: ClusterIP
	  serviceAccount:
	    annotations: {}
	    create: true
	    name: ""
	  tolerations: []
	  
	shreyanss-mbp:operator-sdk BytePython$

	shreyanss-mbp:operator-sdk BytePython$ cat nginx-operator/deploy/operator.yaml 
	apiVersion: apps/v1
	kind: Deployment
	metadata:
	  name: nginx-operator
	spec:
	  replicas: 1
	  selector:
	    matchLabels:
	      name: nginx-operator
	  template:
	    metadata:
	      labels:
	        name: nginx-operator
	    spec:
	      serviceAccountName: nginx-operator
	      containers:
	        - name: nginx-operator
	          # Replace this with the built image name
	          image: REPLACE_IMAGE
	          imagePullPolicy: Always
	          env:
	            - name: WATCH_NAMESPACE
	              valueFrom:
	                fieldRef:
	                  fieldPath: metadata.namespace
	            - name: POD_NAME
	              valueFrom:
	                fieldRef:
	                  fieldPath: metadata.name
	            - name: OPERATOR_NAME
	              value: "nginx-operator"
	shreyanss-mbp:operator-sdk BytePython$ 
```

# Build the 'nginx-operator' Image and Push it
Make sure you have a Docker daemon running. In my case, I had 'Docker Desktop' running on my MAC. 
Also, create a repo on dockerhub.
	
  
```
	# Ref: https://minikube.sigs.k8s.io/docs/handbook/registry/
	shreyanss-mbp:nginx-operator BytePython$ minikube addons enable registry
	🌟  The 'registry' addon is enabled

	shreyanss-mbp:nginx-operator BytePython$ docker run --rm -it --network=host alpine ash -c "apk add socat && socat TCP-LISTEN:5000,reuseaddr,fork TCP:$(minikube ip):5000"

	# Before building the nginx-operator:
	shreyanss-mbp:nginx-operator BytePython$ docker images
	REPOSITORY                                     TAG                 IMAGE ID            CREATED             SIZE
	smulkutk/nginx-operator                        v0.0.1              1b60da1bfb2c        2 minutes ago       216MB
	registry.hub.docker.com/library/registry       2.7.1               2d4f4b5309b1        7 days ago          26.2MB
	nginx                                          latest              2622e6cca7eb        2 weeks ago         132MB
	quay.io/operator-framework/helm-operator       v0.18.1             811896816122        2 weeks ago         216MB
	.
	.

	shreyanss-mbp:operator-sdk BytePython$ cd nginx-operator/
	shreyanss-mbp:nginx-operator BytePython$

	shreyanss-mbp:nginx-operator BytePython$ operator-sdk build smulkutk/nginx-operator:v0.0.1
	INFO[0000] Building OCI image smulkutk/nginx-operator:v0.0.1 
	Sending build context to Docker daemon  34.82kB
	Step 1/3 : FROM quay.io/operator-framework/helm-operator:v0.18.1
	v0.18.1: Pulling from operator-framework/helm-operator
	e96e3a1df3b2: Pull complete 
	1b99828eddf5: Pull complete 
	d5f96e57b3b1: Pull complete 
	d8a3018af44b: Pull complete 
	325fcca1177b: Pull complete 
	Digest: sha256:bcb18fda746c33e16dd4418fec1df7084979ffa379d944fd82655bc48c1e35b7
	Status: Downloaded newer image for quay.io/operator-framework/helm-operator:v0.18.1
	 ---> 811896816122
	Step 2/3 : COPY watches.yaml ${HOME}/watches.yaml
	 ---> 185139bad07f
	Step 3/3 : COPY helm-charts/ ${HOME}/helm-charts/
	 ---> dba4e81c5567
	Successfully built dba4e81c5567
	Successfully tagged smulkutk/nginx-operator:v0.0.1
	INFO[0025] Operator build complete.                     
	shreyanss-mbp:nginx-operator BytePython$


	shreyanss-mbp:nginx-operator BytePython$ docker tag smulkutk/nginx-operator:v0.0.1 localhost:5000/smulkutk/nginx-operator:v.0.0.1

	shreyanss-mbp:nginx-operator BytePython$ docker images
	REPOSITORY                                     TAG                 IMAGE ID            CREATED             SIZE
	smulkutk/nginx-operator                        v0.0.1              1b60da1bfb2c        4 minutes ago       216MB
	localhost:5000/smulkutk/nginx-operator         v.0.0.1             1b60da1bfb2c        4 minutes ago       216MB
	registry.hub.docker.com/library/registry       2.7.1               2d4f4b5309b1        7 days ago          26.2MB
	nginx                                          latest              2622e6cca7eb        2 weeks ago         132MB
	quay.io/operator-framework/helm-operator       v0.18.1             811896816122        2 weeks ago         216MB
	.
	.

```

# Run the 'nginx-operator'
```
		shreyanss-mbp:operator-sdk BytePython$ cd nginx-operator/

		shreyanss-mbp:nginx-operator BytePython$ cat deploy/operator.yaml | grep image
		          # Replace this with the built image name
		          image: REPLACE_IMAGE
		          imagePullPolicy: Always

		shreyanss-mbp:nginx-operator BytePython$ docker images | grep smulkutk
		smulkutk/nginx-operator                        v0.0.1              1b60da1bfb2c        5 minutes ago       216MB
		localhost:5000/smulkutk/nginx-operator         v.0.0.1             1b60da1bfb2c        5 minutes ago       216MB


		# Replace the 'image' with the recently built image-
		shreyanss-mbp:nginx-operator BytePython$ cat deploy/operator.yaml | grep image
		          # Replace this with the built image name
		          image: localhost:5000/smulkutk/nginx-operator:v.0.0.1
		          imagePullPolicy: IfNotPresent


		shreyanss-mbp:nginx-operator BytePython$ kubectl apply -f deploy/role.yaml 
		role.rbac.authorization.k8s.io/nginx-operator created

		shreyanss-mbp:nginx-operator BytePython$ kubectl apply -f deploy/role_binding.yaml 
		rolebinding.rbac.authorization.k8s.io/nginx-operator created

		shreyanss-mbp:nginx-operator BytePython$ kubectl apply -f deploy/service_account.yaml 
		serviceaccount/nginx-operator created

		shreyanss-mbp:nginx-operator BytePython$ kubectl apply -f deploy/operator.yaml 
		deployment.apps/nginx-operator created

```

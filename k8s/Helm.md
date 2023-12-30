# Why?
___
* Package manager for Kubernetes (think `apt`);
	*  Bundle of YAML files (a **Chart**);
	* Available in some specific Helm Repository;
	* Download and use existing Charts;
* Templating Engine;
	* Reuse YAML files and only change some dynamic parts that you want:

	``` YAML
	apiVersion: v1
	kind: Pod
	metadata:
	  name: {{ .Values.name }}
	spec:
	  containers:
	  - name: {{ .Values.container.name }}
	    image: {{ .Values.container.image }}
	    port: {{ .Values.container.port }}
	```

The external configuration comes from an additional file called (`values.yaml`). For the file above, it could look like:

``` YAML
name: my-app
container:
  name: my-app-container
  image: my-app-image
  port: 9001
```

A helm chart structure looks like:

```
mychart/
  Chart.yaml # metadata about chart, dependencies, name
  values.yaml # configured values for the template files that can be overriden
  charts/ # dependencies
  templates/ # actual template files
```

# Values injection
___
There are different ways to provide the data. Let's consider this scenario:

``` YAML
# values.yaml
imageName: myapp
port: 8080
version: 1.0.0

# my-values.yaml
version: 2.0.0
```

With the following command:

``` Bash
$ helm install --values=my-values.yaml <chartname>
```

The default values and my-values will be merged to form the final `.Values` object:

``` YAML
imageName: myapp
port: 8080
version: 2.0.0
```


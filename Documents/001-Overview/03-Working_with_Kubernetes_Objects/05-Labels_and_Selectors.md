# Labels and Selectors
`

*Labels* are **key/value** pairs that are attached to objects, such as pods. Labels are intended to be used to specify identifying attributes of objects that are meaningful and relevant to users, but do not directly imply semantics to the core system. Labels can be used to organize and to select subsets of objects. Labels can be attached to objects at creation time and subsequently added and modified at any time. Each object can have a set of key/value labels defined. Each Key must be unique for a given object.

```json
"metadata": {
  "labels": {
    "key1" : "value1",
    "key2" : "value2"
  }
}
```

Labels allow for efficient queries and watches and are ideal for use in UIs and CLIs.

## Motivation

Labels enable users to map their own organizational structures onto system objects in a loosely coupled fashion, without requiring clients to store these mappings.

Service deployments and batch processing pipelines are often multi-dimensional entities(e.g. multiple partitions or deployments, multiple release tracks, multiple tiers, multiple micro services per tier). Management often requires cross-cutting operations, which breaks encapsulation of strictly hierarchical representations, especially rigid hierarchies determined by the infrastructure rather than by users.

Example labels:

- `"release" : "stable"`, `"release" : "canary"`

- `"environment" : "dev"`, `"environment" : "qa"`, `"environment" : "production"`

- `"tier" : "frontend"`, `"tier" : "backend"`, `"tier" : "cache"`

- `"partition" : "customerA"`, `"partition" : "customerB"`

- `"track" : "daily"`, `"track" : "weekly"`

These are just examples of commonly used labels; you are free to develop your own conventions. Keep in mind that *label Key must be unique* for a given object.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: label-demo
  labels:
    environment: production
    app: nginx
spec:
  containers:
  - name: nginx
    image: nginx:1.7.9
    ports:
    - containerPort: 80
```

## Label selectors

Via a *label selector*, the client/user can identify a set of objects. The label selector is the core grouping primitive in Kubernetes.

The API currently supports two types of selectors: *equality-based* and *set-based*. A label selector can be made of multiple *requirements* which are comma-separated. In case and multiple requirements, all must be satisfied so the comma separator acts as a logical *AND*(&&) operator.

The semantics of empty or non-specified selectors are dependent on the context, and API types that use selectors should document the validity and meaning of them.

### *Equality-based* requirement

*Equality-* or *inequality-based* requirements allow filtering by label keys and values. Matching objects must satisfy all of the specified label constraints, though they may have additional labels as well. These kinds of operators are admitted `=`, `==`, `!=`. The first two represent *equality*, while the latter represents *inequality* For example:

```bash
environment = production
tier != frontend
```

The former selects all resources with key equal to `environment` and value equal to `production`. The latter selects all resources with key equal to `tier` and value distinct from `frontend`, and all resources with no labels with the `tier` key.

One could filter for resources in `production` excluding `frontend` using the comma operator: `environtment=production,tier!=frontend`.

One usage scenario for equality-based label requirement is for Pods to specify node selection criteria. For example, the sample Pod below selects nodes with the label "`accelerator=nvidia-tesla-p100`".

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: cuda-test
spec:
  containers:
    - name: cuda-test
      image: "k8s.gcr.io/cuda-vector-add:v0.1"
      resources:
        limits:
          nvidia.com/gpu: 1
  nodeSelector:
    accelerator: nvidia-tesla-p100
```

### *Set-base* requirement

*Set-based* label requirements allow filtering keys according to a set of values. Three kinds of operators are supported: `in`, `notin`, and `exists`(only the key identifier). For example:

```bash
environment in (production, qa)
tier notin (frontend, backend)
partition
!partition
```

The first example selects all resources with key equal to `environment` and value equal to `production` or `qa`. The second example selects all resources with key equal to `ier` and values other than `frontend` and `backend`, and all resources with no labels with the `tier` key. Comma separator acts as an *AND* operator.

*Set-based* requirements can be mixed with *equality-based* requirements. For example:

```bash
partition in (customerA, customerB), environtment != qa
```

## API

### LIST and WATCH filtering

LIST and WATCH operatoins may sepcify label selectors to filter the sets of objects returned using query paramter. Both requirements are permitted(presented here as they would appear in a URL query string).

- *equality-based* requirements:
  
  `?labelSelector=environment%3Dproduction,tier%3Dfrontend`

- *set-based* requirements:

  `?labelSelector=environment+in+%28production%2Cqa%29%2Ctier+in+%28frontend%29`

Both label selector styles can be used to list or watch resources via a REST client. For example, targeting `apiserver` with `kubectl` and using *equality-based* one may write:

```bash
kubectl get pods -l environment=production,tier=frontend
```

or using *set-based* requirements:

```bash
kubectl get pods -l 'environment in (production),tier in (frontend)'
```

As already mentioned *set-based* requirements are more expressive. For instance, they can implement the *OR* operator on values:

```bash
kubectl get pods -l 'environment in (production, qa)'
```

or restricting negative matching via *exist* operator:

```bash
kubectl get pods -l 'environment,environment notin (frontend)'
```

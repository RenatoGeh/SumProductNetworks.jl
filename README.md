# SumProductNetworks.jl

## Nodes

### Sum Nodes
tbd
### Product Nodes
tbd
### Indicator Nodes
tbd
## Layers

Sub-types of SPNs, e.g. trees, can be easily defined using layers. The following layers are implemented in this package (assuming tree structured SPNs):

### Sum Layer
Sum Layers consist of a weight matrix of size $Ch \times C$, where $C$ is the number of nodes and $Ch$ is the number of child nodes per node. This layer applies the following computation:

$$
Y_c = \sum_{j \in ch(c)} w_{cj} S_j[X]
$$
where $ch(c)$ are the children of node $c$ and $S_{j}[X]$ denotes the value of node $j$ on input $X$.

#### Example
```
C = 50 # number of nodes
Ch = 5 # number of children

parentId = ... # specify the unique parent id, use -1 the layer has no parent
ids = ... # specify the unique ids, e.g. collect(1:C)
childIds = ... # get a Ch x C matrix of all child ids
weights = rand(Dirichlet([1./Ch for j in 1:Ch]), C) # random weights
layer = SumLayer(ids, childIds, weights, parentId)

```

### Product Layer
Product Layers apply the following computation:

$$
Y_c = \prod_{j \in ch(c)} S_j[X]
$$
where $ch(c)$ are the children of node $c$ and $S_{j}[X]$ denotes the value of node $j$ on input $X$.

#### Example
```
C = 50 # number of nodes
Ch = 5 # number of children

parentId = ... # specify the unique parent id, use -1 the layer has no parent
ids = ... # specify the unique ids, e.g. collect(1:C)
childIds = ... # get a Ch x C matrix of all child ids
layer = ProductLayer(ids, childIds, parentId)

```

### Product Layer with class labels
In Product Layer with class labels each node is additionally asociated with a class label. This layer applies the following computation:

$$
Y_c = \prod_{j \in ch(c)} \mathcal{1}(y_c)  S_j[X]
$$
where $ch(c)$ are the children of node $c$ and $S_{j}[X]$ denotes the value of node $j$ on input $X$ and $\mathcal{1}(y_c)$ is a indicator function retuning one if the class label of node $c$ and those of the observation are equivalent.

#### Example
```
C = 50 # number of nodes
Ch = 5 # number of children

parentId = ... # specify the unique parent id, use -1 the layer has no parent
ids = ... # specify the unique ids, e.g. collect(1:C)
childIds = ... # get a Ch x C matrix of all child ids
clabels = collect(1:C) # class labels have to start at 1
layer = ProductCLayer(ids, childIds, clabels, parentId)

```

### Multivariate Feature Layer (Convolution Layer)
This layer consists of filter matrix of size $C \times D$, where $C$ is the number of nodes and $D$ is the dimensionality. This layer applies the following computation:

$$
Y = \exp( W \cdot X )
$$
where we assume that $X \in \mathcal{R}^{D \times N}$.

#### Example
```
C = 50 # number of nodes
D = 10 # dimensionality

parentId = ... # specify the unique parent id, use -1 the layer has no parent
ids = ... # specify the unique ids, e.g. collect(1:C)
weights = rand(C, D)
scopes = rand(Bool, C, D) # mask
layer = MultivariateFeatureLayer(ids, weights, scopes, parentId)

```


### Developing the source code
To ensure correctness of the implementation, the source code can be developed with while automatically rerunning all test using:

```
find . -name '*.jl' | entr julia test/runtests.jl
```

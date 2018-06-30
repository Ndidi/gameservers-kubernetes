# Running Game Servers on Kubernetes

This is material for a conference talk about running and managing game servers on Kubernetes.

The talk gives an intro on what Kubernetes is and some of the components that are used to run and manage game servers as well as a high-level architecture of the system.

The material concentrate on running two different game servers on Kubernetes:

1. [Nakama](https://github.com/heroiclabs/nakama) game server.
2. Unity headless instances to run authoritative game simulations on the server.

The talk uses [this multiplayer project](https://github.com/JoaoBorks/unity-fastpacedmultiplayer) as a sample Unity project to run in Kubernetes.

The presentation can be compiled using the [Deckset](https://www.decksetapp.com) app.

To run the demo, you'll need:

1. Install Minikube (or full Kubernetes)
2. Install [Weavescope](https://www.weave.works/docs/scope/latest/installing/#k8s):

```
kubectl apply -f "https://cloud.weave.works/k8s/scope.yaml?k8s-version=$(kubectl version | base64 | tr -d '\n')"
```

3. Port-forward weavescope:

```
kubectl port-forward -n weave "$(kubectl get -n weave pod --selector=weave-scope-component=app -o jsonpath='{.items..metadata.name}')" 4040
```

All content and material is released under MIT Licence and can be used freely for any purpose.

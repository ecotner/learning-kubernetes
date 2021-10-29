# Deployments

## ReplicaSets
A ReplicaSet is an abstraction that acts as a manager/controller for a Pod.
If a Pod goes down, the ReplicaSet is responsible for destroying and rebuilding the Pod, and making sure that they are all running smoothly.
It also ensures that the requested number of Pods is available, provides fault-tolerance, and can be used to scale Pods.
Can be managed using YAML like Pods.

## Deployments
Deployments are really just higher-level wrappers around ReplicaSets.
They can create/destroy/scale ReplicaSets with zero downtime, and have rollback functionality.
They can also be created/managed using YAML just like Pods and ReplicaSets.

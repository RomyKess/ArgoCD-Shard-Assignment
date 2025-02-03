# ArgoCD Shard Assignment
This repository includes an ansible role which is meant to provide a solution for a specific use case:
clusters intermittently turning on and off.

This situation leads to an abundance of OOM kills, which in turn causes exccessive resource waste.
For example, a controller with 20 clusters can have 19 which are turned on, and the rest are down.
This controller pod will use all of its memory and possibly get OOM killed many times if resources aren't raised to a disproportionate amount.
On the other hand, a differnet pod with 20 clusters (round-robin) can have 5 which are turned on and 15 which are down.
This pod will barely use half of its memory limits.
In summary, we got an unstable pod and resource waste.
How do we fix this?

This playbook is based on the logic where all controllers need to have the same amount of clusters which are turned on, and the rest have a lesser effect.
In short, I found how many clusters are turned on though the API, how many application controllers the ArgoCD instance has and calculated the appropriate amount for each controller.
By assignig each controller the same amount of turned on clusters, I prevented any OOM kills from happening in production (where at first there were hundrends).
I also managed to lower the needed memory for each controller by 20%, which in total comes up to a ton of gigabytes.

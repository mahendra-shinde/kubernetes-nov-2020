# StatefulSet

1.  Alternative to ReplicaSet and Deployment
2.  Pods are given Ordered indexes, first pod is given suffix `-0` and second pod would be `-1`
3.  Scaling is also ordered. For StatefulSet with 3 pods, scaling up/out would result in new pod `-3`
4.  Uses Headless service (service with ClusterIp:None) for assigning these ordered indexes.

## Components of StatefulSet

1.  Headless Service (with ClusterIp Set to NONE)
2.  StatefulSet 
3.  OPTIONAL: PersistentVolumeClaim for Each POD


1.  Deploy the Statefulset object with Headless service

    ```
    $ kubectl apply -f myapp.yml
    $ kubectl get sts -n mahendra
    $ kubectl get po -l app=myapp -n mahendra
    ```

2.  Try Scale In / Out by changing `replicas:` property.

3.  Deploy the Load Balancer service

    ```
    $ kubectl apply -f appservice.yml
    $ kubectl get svc -n mahendra app-svc
    ```

4.  Use the external-ip of app-svc to make new request!


5.  Adding `PersistentVolumeClaim` makes EACH pod have it's OWN PersistentVolume.

6.  Redeploy the modified StatefulSet and check the PVCs

    ```
    $ kubectl get po,pvc,pv -n mahendra
    ```

7.  Test the Pod replacements and volume binding.

    ```
    $ kubectl exec -n mahendra -it myapp-0 -- sh
    $ cd /data
    $ mkdir temp
    $ cd temp
    $ echo 'Hello World' > file1.txt
    $ exit
    $ kubectl delete po -n mahendra myapp-0
    ## Wait for 2/3 minutes
    $ kubectl exec -n mahendra -it myapp-0 -- sh
    $ cat /data/temp/file1.txt
    $ exit
    ```

8.  Clean_up

    ```
    $ kubectl delete -f .
    ```
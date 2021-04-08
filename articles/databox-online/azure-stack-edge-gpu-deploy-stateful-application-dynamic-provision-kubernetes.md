---
title: Použití kubectl k nasazení stavové aplikace Kubernetes prostřednictvím dynamicky zřízené sdílené složky na zařízení GPU Azure Stack Edge pro | Microsoft Docs
description: Popisuje, jak vytvořit a spravovat nasazení stavové aplikace Kubernetes prostřednictvím dynamicky zřízené sdílené složky pomocí kubectl na zařízení GPU Microsoft Azure Stack Edge pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 160ba6014bbb2d5cb3ed4e8e4b28a61fe5e8d4cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102520689"
---
# <a name="use-kubectl-to-run-a-kubernetes-stateful-application-with-storageclass-on-your-azure-stack-edge-pro-gpu-device"></a>Použití kubectl ke spuštění stavové aplikace Kubernetes s StorageClass na zařízení GPU Azure Stack Edge pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

V tomto článku se dozvíte, jak nasadit stavovou aplikaci s jednou instancí v Kubernetes pomocí StorageClass k dynamickému zřízení úložiště a nasazení. Nasazení používá `kubectl` příkazy v existujícím clusteru Kubernetes a nasadí aplikaci MySQL. 

Tento postup je určený pro uživatele, kteří si zkontrolovali [Kubernetes úložiště na zařízení Azure Stack Edge pro](azure-stack-edge-gpu-kubernetes-storage.md) a jsou obeznámeni s koncepty [úložiště Kubernetes](https://kubernetes.io/docs/concepts/storage/).


## <a name="prerequisites"></a>Požadavky

Než budete moct nasadit stavovou aplikaci, proveďte na svém zařízení a klientovi, který budete používat pro přístup k zařízení, následující požadavky:

### <a name="for-device"></a>Zařízení

- Máte přihlašovací údaje pro přihlášení k zařízení Azure Stack Edge pro s jedním uzlem.
    - Zařízení se aktivuje. Viz [Aktivace zařízení](azure-stack-edge-gpu-deploy-activate.md).
    - Zařízení má výpočetní roli nakonfigurovanou prostřednictvím Azure Portal a má cluster Kubernetes. Viz [Konfigurace výpočtů](azure-stack-edge-gpu-deploy-configure-compute.md).

### <a name="for-client-accessing-the-device"></a>Pro klientský přístup k zařízení

- Máte klientský systém Windows, který se bude používat pro přístup k zařízení Azure Stack Edge pro.
    - Na klientovi běží Windows PowerShell 5,0 nebo novější. Nejnovější verzi Windows PowerShellu si stáhnete tak, že přejdete na [nainstalovat Windows PowerShell](/powershell/scripting/install/installing-windows-powershell).
    
    - Můžete mít i jiné klienty s [podporovaným operačním systémem](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) . Tento článek popisuje postup při použití klienta systému Windows. 
    
    - Dokončili jste postup popsaný v tématu [přístup ke clusteru Kubernetes na zařízení Azure Stack Edge pro](azure-stack-edge-gpu-create-kubernetes-cluster.md). Máte:
      - `userns1`Pomocí příkazu byl vytvořen obor názvů `New-HcsKubernetesNamespace` . 
      - `user1`Pomocí příkazu byl vytvořen uživatel `New-HcsKubernetesUser` . 
      - Byl udělen `user1` přístup `userns1` prostřednictvím `Grant-HcsKubernetesNamespaceAccess` příkazu.       
      - Nainstalováno `kubectl` na straně klienta a soubor byl uložen `kubeconfig` s konfigurací uživatele do jazyka C: \\ Uživatelé \\ &lt; uživatelské_jméno &gt; \\ . Kube. 
    
    - Ujistěte se, že `kubectl` verze klienta není ve verzi Kubernetes Master spuštěná na vašem zařízení Azure Stack Edge pro. 
        - Slouží `kubectl version` ke kontrole verze kubectl spuštěné v klientovi. Poznamenejte si plnou verzi.
        - V místním uživatelském rozhraní zařízení Azure Stack Edge pro se podívejte na **Přehled** a poznamenejte si číslo Kubernetes softwaru. 
        - Ověřte, že tyto dvě verze mají kompatibilitu z mapování uvedeného v podporované verzi Kubernetes.<!-- insert link-->. 


Jste připraveni nasadit stavovou aplikaci na zařízení Azure Stack Edge pro. 


## <a name="deploy-mysql"></a>Nasazení MySQL

Teď budete spouštět stavovou aplikaci vytvořením nasazení Kubernetes a připojením k předdefinovanému StorageClass pomocí PersistentVolumeClaim (PVC). 

Všechny `kubectl` příkazy, které použijete k vytvoření a správě stavových nasazení aplikace, musí určovat obor názvů přidružený k této konfiguraci. Chcete-li zadat obor názvů v příkazu kubectl, použijte `kubectl <command> -n <your-namespace>` .

1. Získejte seznam lusků spuštěných v clusteru Kubernetes ve vašem oboru názvů. Pod je kontejner aplikace nebo proces spuštěný v clusteru Kubernetes.

   ```powershell
   kubectl get pods -n <your-namespace>
   ```
    
   Tady je příklad použití příkazu:
    
   ```powershell
    C:\Users\user>kubectl get pods -n "userns1"
    No resources found in userns1 namespace.    
    C:\Users\user>
   ```
    
   Výstup by měl uvádět, že se nenašly žádné prostředky (lusky), protože v clusteru nejsou spuštěné žádné aplikace.

1. Budete používat následující soubory YAML. Tento `mysql-deployment.yml` soubor popisuje nasazení, které spouští MySQL, a odkazuje na okruh PVC. Soubor definuje připojení svazku pro `/var/lib/mysql` a pak vytvoří virtuální okruh, který bude hledat svazek o velikosti 20 GB. Je zřízena dynamická PV a virtuální okruh je vázán na tuto PV.

    Zkopírujte a uložte následující `mysql-deployment.yml` soubor do složky na klientovi Windows, který používáte pro přístup k zařízení Azure Stack Edge pro.
    
    ```yml
    apiVersion: v1
    kind: Service
    metadata:
      name: mysql
    spec:
      ports:
      - port: 3306
      selector:
        app: mysql
      clusterIP: None
    ---
    apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
    kind: Deployment
    metadata:
      name: mysql
    spec:
      selector:
        matchLabels:
          app: mysql
      strategy:
        type: Recreate
      template:
        metadata:
          labels:
            app: mysql
        spec:
          containers:
          - image: mysql:5.6
            name: mysql
            env:
              # Use secret in real usage
            - name: MYSQL_ROOT_PASSWORD
              value: password
            ports:
            - containerPort: 3306
              name: mysql
            volumeMounts:
            - name: mysql-persistent-storage
              mountPath: /var/lib/mysql
          volumes:
          - name: mysql-persistent-storage
            persistentVolumeClaim:
              claimName: mysql-pv-claim-sc
    ```
    
2. Zkopírujte soubor a uložte `mysql-pvc.yml` ho jako soubor do stejné složky, kam jste uložili soubor `mysql-deployment.yml` . Chcete-li použít vestavěné StorageClass, které Azure Stack Edge pro zařízení na připojeném datovém disku, nastavte `storageClassName` pole v objektu PVC na `ase-node-local` a accessModes `ReadWriteOnce` . 

    > [!NOTE] 
    > Ujistěte se, že soubory YAML mají správné odsazení. Můžete se podívat na [YAML Lint](http://www.yamllint.com/) a ověřit a uložit.
   
    ```yml
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: mysql-pv-claim-sc
    spec:
      storageClassName: ase-node-local
      accessModes:
        - ReadWriteOnce
      resources:
        requests:
          storage: 20Gi
    ```

3. Nasaďte `mysql-pvc.yaml` soubor.

    `kubectl apply -f <URI path to the mysql-pv.yml file> -n <your-user-namespace>`
    
    Zde je ukázkový výstup nasazení.

    
    ```powershell
    C:\Users\user>kubectl apply -f "C:\stateful-application\mysql-pvc.yml" -n userns1
    persistentvolumeclaim/mysql-pv-claim-sc created
    C:\Users\user>
    ```
   Poznamenejte si název virtuálního okruhu, který byl vytvořen v tomto příkladu `mysql-pv-claim-sc` . Budete ho používat v pozdějším kroku.

4. Nasaďte obsah `mysql-deployment.yml` souboru.

    `kubectl apply -f <URI path to mysql-deployment.yml file> -n <your-user-namespace>`

    Zde je ukázkový výstup nasazení.
    
    ```powershell
    C:\Users\user>kubectl apply -f "C:\stateful-application\mysql-deployment.yml" -n userns1
    service/mysql created
    deployment.apps/mysql created
    C:\Users\user>
    ```
    
5. Zobrazí informace o nasazení.

    `kubectl describe deployment <app-label> -n <your-user-namespace>`
    
    ```powershell
    C:\Users\user>kubectl describe deployment mysql -n userns1
    Name:               mysql
    Namespace:          userns1
    CreationTimestamp:  Thu, 20 Aug 2020 11:14:25 -0700
    Labels:             <none>
    Annotations:        deployment.kubernetes.io/revision: 1
                        kubectl.kubernetes.io/last-applied-configuration:
                          {"apiVersion":"apps/v1","kind":"Deployment","metadata":{"annotations":{},"name":"mysql","namespace":"userns1"},"spec":{"selector":{"matchL...
    Selector:           app=mysql
    Replicas:           1 desired | 1 updated | 1 total | 1 available | 0 unavailable
    StrategyType:       Recreate
    MinReadySeconds:    0
    Pod Template:
      Labels:  app=mysql
      Containers:
       mysql:
        Image:      mysql:5.6
        Port:       3306/TCP
        Host Port:  0/TCP
        Environment:
          MYSQL_ROOT_PASSWORD:  password
        Mounts:
          /var/lib/mysql from mysql-persistent-storage (rw)
      Volumes:
       mysql-persistent-storage:
        Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
        ClaimName:  mysql-pv-claim-sc
        ReadOnly:   false
    Conditions:
      Type           Status  Reason
      ----           ------  ------
      Available      True    MinimumReplicasAvailable
      Progressing    True    NewReplicaSetAvailable
    OldReplicaSets:  <none>
    NewReplicaSet:   mysql-695c4d9dcd (1/1 replicas created)
    Events:
      Type    Reason             Age   From                   Message
      ----    ------             ----  ----                   -------
      Normal  ScalingReplicaSet  24s   deployment-controller  Scaled up replica set mysql-695c4d9dcd to 1
    C:\Users\user>
    ```
    

6. Seznam lusků vytvořených nasazením

    `kubectl get pods -l <app=label> -n <your-user-namespace>`

    Zde je ukázkový výstup.

    
    ```powershell
    C:\Users\user>kubectl get pods -l app=mysql -n userns1
    NAME                     READY   STATUS    RESTARTS   AGE
    mysql-695c4d9dcd-rvzff   1/1     Running   0          40s
    C:\Users\user>
    ```
    
7. Zkontrolujte PersistentVolumeClaim.

    `kubectl describe pvc <your-pvc-name>`

    Zde je ukázkový výstup.

    
    ```powershell
    C:\Users\user>kubectl describe pvc mysql-pv-claim-sc -n userns1
    Name:          mysql-pv-claim-sc
    Namespace:     userns1
    StorageClass:  ase-node-local
    Status:        Bound
    Volume:        pvc-dc48253c-82dc-42a4-a7c6-aaddc97c9b8a
    Labels:        <none>
    Annotations:   kubectl.kubernetes.io/last-applied-configuration:
                     {"apiVersion":"v1","kind":"PersistentVolumeClaim","metadata":{"annotations":{},"name":"mysql-pv-claim-sc","namespace":"userns1"},"spec":{"...
                   pv.kubernetes.io/bind-completed: yes
                   pv.kubernetes.io/bound-by-controller: yes
                   volume.beta.kubernetes.io/storage-provisioner: rancher.io/local-path
                   volume.kubernetes.io/selected-node: k8s-3q7lhq2cl-3q7lhq2
    Finalizers:    [kubernetes.io/pvc-protection]
    Capacity:      20Gi
    Access Modes:  RWO
    VolumeMode:    Filesystem
    Mounted By:    mysql-695c4d9dcd-rvzff
    Events:
      Type    Reason                 Age                From                                                                                                Message
      ----    ------                 ----               ----                                                                                                -------
      Normal  WaitForFirstConsumer   71s (x2 over 77s)  persistentvolume-controller                                                                         waiting for first consumer to be created before binding
      Normal  ExternalProvisioning   62s                persistentvolume-controller                                                                         waiting for a volume to be created, either by external provisioner "rancher.io/local-path" or manually created by system administrator
      Normal  Provisioning           62s                rancher.io/local-path_local-path-provisioner-6b84988bf9-tx8mz_1896d824-f862-4cbf-912a-c8cc0ca05574  External provisioner is provisioning volume for claim "userns1/mysql-pv-claim-sc"
      Normal  ProvisioningSucceeded  60s                rancher.io/local-path_local-path-provisioner-6b84988bf9-tx8mz_1896d824-f862-4cbf-912a-c8cc0ca05574  Successfully provisioned volume pvc-dc48253c-82dc-42a4-a7c6-aaddc97c9b8a
    C:\Users\user>
    ```
    

## <a name="verify-mysql-is-running"></a>Ověřte, že je MySQL spuštěný.

Chcete-li ověřit, zda je aplikace spuštěna, zadejte:

`kubectl exec <your-pod-with-the-app> -i -t -n <your-namespace> -- mysql -p`

Po zobrazení výzvy zadejte heslo. Heslo je v `mysql-deployment` souboru.

Zde je ukázkový výstup.

```powershell
C:\Users\user>kubectl exec mysql-695c4d9dcd-rvzff -i -t -n userns1 -- mysql -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 3
Server version: 5.6.49 MySQL Community Server (GPL)

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
mysql>
```

## <a name="delete-a-deployment"></a>Odstranění nasazení

Chcete-li odstranit nasazení, odstraňte nasazené objekty podle názvu. Mezi tyto objekty patří nasazení, služba a okruh PVC.
 
```powershell
kubectl delete deployment <deployment-name>,svc <service-name> -n <your-namespace>
kubectl delete pvc <your-pvc-name> -n <your-namespace>
```

Zde je ukázkový výstup při odstranění nasazení a služby.

```powershell
C:\Users\user>kubectl delete deployment,svc mysql -n userns1
deployment.apps "mysql" deleted
service "mysql" deleted
C:\Users\user>
```
Zde je ukázkový výstup při odstranění trvalého připojení k virtuálnímu okruhu.

```powershell
C:\Users\user>kubectl delete pvc mysql-pv-claim-sc -n userns1
persistentvolumeclaim "mysql-pv-claim-sc" deleted
C:\Users\user>
```                                                                                         


## <a name="next-steps"></a>Další kroky

Informace o tom, jak nakonfigurovat síť přes kubectl, najdete v tématu [nasazení bezstavové aplikace na zařízení Azure Stack Edge pro](azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module.md) .
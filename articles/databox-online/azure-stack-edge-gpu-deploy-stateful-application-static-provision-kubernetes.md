---
title: Použití kubectl k nasazení stavové aplikace Kubernetes prostřednictvím staticky zřízené sdílené složky na zařízení Azure Stack Edge pro | Microsoft Docs
description: Popisuje, jak vytvořit a spravovat nasazení stavové aplikace Kubernetes prostřednictvím staticky zřízené sdílené složky pomocí kubectl na zařízení GPU Azure Stack Edge pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 09/22/2020
ms.author: alkohli
ms.openlocfilehash: 6949b2265e1b22d924b67d0d46e5b2f2bf9c23ac
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91330396"
---
# <a name="use-kubectl-to-run-a-kubernetes-stateful-application-with-a-persistentvolume-on-your-azure-stack-edge-pro-device"></a>Použití kubectl ke spuštění stavové aplikace Kubernetes s PersistentVolume na zařízení Azure Stack Edge pro

V tomto článku se dozvíte, jak nasadit stavovou aplikaci s jednou instancí v Kubernetes pomocí PersistentVolume (PV) a nasazení. Nasazení používá `kubectl` příkazy v existujícím clusteru Kubernetes a nasadí aplikaci MySQL. 

Tento postup je určený pro uživatele, kteří si zkontrolovali [Kubernetes úložiště na zařízení Azure Stack Edge pro](azure-stack-edge-gpu-kubernetes-storage.md) a jsou obeznámeni s koncepty [úložiště Kubernetes](https://kubernetes.io/docs/concepts/storage/).

Azure Stack Edge pro podporuje taky spouštění kontejnerů Azure SQL Edge a ty se dají nasadit podobným způsobem, jak je popsáno zde pro MySQL. Další informace najdete v tématu [Azure SQL Edge](../azure-sql-edge/overview.md).


## <a name="prerequisites"></a>Požadavky

Než budete moct nasadit stavovou aplikaci, ujistěte se, že jste na svém zařízení dokončili následující požadavky a klienta, který budete používat pro přístup k zařízení:

### <a name="for-device"></a>Zařízení

- Máte přihlašovací údaje pro přihlášení k zařízení Azure Stack Edge pro s jedním uzlem.
    - Zařízení se aktivuje. Viz [Aktivace zařízení](azure-stack-edge-gpu-deploy-activate.md).
    - Zařízení má výpočetní roli nakonfigurovanou prostřednictvím Azure Portal a má cluster Kubernetes. Viz [Konfigurace výpočtů](azure-stack-edge-gpu-deploy-configure-compute.md).

### <a name="for-client-accessing-the-device"></a>Pro klientský přístup k zařízení

- Máte klientský systém Windows, který se bude používat pro přístup k zařízení Azure Stack Edge pro.
    - Na klientovi běží Windows PowerShell 5,0 nebo novější. Nejnovější verzi Windows PowerShellu si stáhnete tak, že přejdete na [nainstalovat Windows PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-7).
    
    - Můžete mít i jiné klienty s [podporovaným operačním systémem](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) . Tento článek popisuje postup při použití klienta systému Windows. 
    
    - Dokončili jste postup popsaný v tématu [přístup ke clusteru Kubernetes na zařízení Azure Stack Edge pro](azure-stack-edge-gpu-create-kubernetes-cluster.md). Máte:
      - `userns1`Pomocí příkazu byl vytvořen obor názvů `New-HcsKubernetesNamespace` . 
      - `user1`Pomocí příkazu byl vytvořen uživatel `New-HcsKubernetesUser` . 
      - Byl udělen `user1` přístup `userns1` prostřednictvím `Grant-HcsKubernetesNamespaceAccess` příkazu.       
      - Nainstalováno `kubectl` na straně klienta a soubor byl uložen `kubeconfig` s konfigurací uživatele do jazyka C: \\ Uživatelé \\ &lt; uživatelské_jméno &gt; \\ . Kube. 
    
    - Ujistěte se, že `kubectl` verze klienta není ve verzi Kubernetes Master spuštěná na vašem zařízení Azure Stack Edge pro. 
        - Slouží `kubectl version` ke kontrole verze kubectl spuštěné v klientovi. Poznamenejte si plnou verzi.
        - V místním uživatelském rozhraní zařízení Azure Stack Edge pro se podívejte na **Přehled** a poznamenejte si číslo Kubernetes softwaru. 
        - Ověřte, že tyto dvě verze mají kompatibilitu z mapování uvedeného v podporované verzi Kubernetes. <!-- insert link-->. 


Jste připraveni nasadit stavovou aplikaci na zařízení Azure Stack Edge pro. 

## <a name="provision-a-static-pv"></a>Zřízení statické PV

Pokud chcete staticky zřídit souč_hod, musíte na svém zařízení vytvořit sdílenou složku. Pomocí těchto kroků zřídíte souč_hod pro sdílenou složku SMB. 

> [!NOTE]
> Konkrétní příklad použitý v tomto článku s návody nepracuje se sdílenými složkami NFS. Obecně platí, že sdílené složky NFS je možné zřídit na vašem zařízení Azure Stack Edge s nedatabázovými aplikacemi.

1. Vyberte, zda chcete vytvořit hraniční sdílenou složku nebo místní sdílenou složku Edge. Pokud chcete vytvořit sdílenou složku, postupujte podle pokynů v části [přidání sdílené složky](azure-stack-edge-manage-shares.md#add-a-share) . Nezapomeňte zaškrtnout políčko pro **použití sdílené složky s hraničními výpočty**.

    ![Místní sdílení Edge pro PV](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/edge-local-share-static-provision-1.png)

    1. Pokud se rozhodnete použít existující sdílenou složku, místo vytvoření nové sdílené složky bude nutné sdílenou složku připojit.
    
        V Azure Portal pro prostředek Azure Stack Edge přejít na **sdílené složky**. V existujícím seznamu sdílených složek vyberte a klikněte na sdílenou složku, kterou chcete použít.

        ![Vybrat existující místní sdílenou složku pro PV](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/mount-edge-share-1.png)

    1. Po zobrazení výzvy vyberte **připojit** a potvrďte připojení.  

        ![Připojit stávající místní sdílenou složku pro PV](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/mount-edge-share-2.png)

1. Poznamenejte si název sdílené složky. Při vytvoření této sdílené složky se trvalý objekt svazku automaticky vytvoří v clusteru Kubernetes, který odpovídá sdílené složce protokolu SMB, kterou jste vytvořili. 

## <a name="deploy-mysql"></a>Nasazení MySQL

Nyní budete spouštět stavovou aplikaci vytvořením nasazení Kubernetes a připojením k PV, kterou jste vytvořili v předchozím kroku, pomocí PersistentVolumeClaim (PVC). 

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

1. Budete používat následující soubory YAML. Tento `mysql-deployment.yml` soubor popisuje nasazení, které spouští MySQL, a odkazuje na okruh PVC. Soubor definuje připojení svazku pro `/var/lib/mysql` a pak vytvoří virtuální okruh, který bude hledat svazek o velikosti 20 GB. 

    Tato deklarace se splní všemi existujícími SOUČ_HODy, které se staticky zřídily při vytváření sdílené složky v předchozím kroku. V zařízení se pro každou sdílenou složku vytvoří Velká souč_hod 32 TB. Souč_hod splňuje požadavky stanovené pro okruh PVC a trvalý okruh by měl být vázán na tuto PV.

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
              claimName: mysql-pv-claim
    ```
    
2. Zkopírujte soubor a uložte `mysql-pv.yml` ho jako soubor do stejné složky, kam jste uložili soubor `mysql-deployment.yml` . Pokud chcete použít sdílenou složku protokolu SMB, kterou jste dříve vytvořili `kubectl` , nastavte `volumeName` pole v objektu PVC na název sdílené složky. 

    > [!NOTE] 
    > Ujistěte se, že soubory YAML mají správné odsazení. Můžete se podívat na [YAML Lint](http://www.yamllint.com/) a ověřit a uložit.
   
    ```yml
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: mysql-pv-claim
    spec:
      volumeName: <smb-share-name-here>
      storageClassName: ""
      accessModes:
        - ReadWriteOnce
      resources:
        requests:
          storage: 20Gi
    ```

3. Nasaďte `mysql-pv.yaml` soubor.

    `kubectl apply -f <URI path to the mysql-pv.yml file> -n <your-user-namespace>`
    
    Zde je ukázkový výstup nasazení.

    
    ```powershell
    C:\Users\user>kubectl apply -f "C:\stateful-application\mysql-pv.yml" -n userns1
    persistentvolumeclaim/mysql-pv-claim created
    
    C:\Users\user>
    ```
   Poznamenejte si název virtuálního okruhu, který jste vytvořili. Budete ho používat v pozdějším kroku. 

4. Nasaďte obsah `mysql-deployment.yml` souboru.

    `kubectl apply -f <URI path to mysql-deployment.yml file> -n <your-user-namespace>`

    Zde je ukázkový výstup nasazení.
    
    ```powershell
    C:\Users\user>kubectl apply -f "C:\stateful-application\mysql-deployment.yml" -n userns1
        service/mysql created
        deployment.apps/mysql created
    ```
    
5. Zobrazí informace o nasazení.

    `kubectl describe deployment <app-label> -n <your-user-namespace>`
    
    ```powershell
    C:\Users\user>kubectl describe deployment mysql -n userns1
    Name:               mysql
    Namespace:          userns1
    CreationTimestamp:  Tue, 18 Aug 2020 09:44:58 -0700
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
        ClaimName:  mysql-pv-claim
        ReadOnly:   false
    Conditions:
      Type           Status  Reason
      ----           ------  ------
      Progressing    True    NewReplicaSetAvailable
      Available      True    MinimumReplicasAvailable
    OldReplicaSets:  <none>
    NewReplicaSet:   mysql-c85f7f79c (1/1 replicas created)
    Events:
      Type    Reason             Age   From                   Message
      ----    ------             ----  ----                   -------
      Normal  ScalingReplicaSet  10m   deployment-controller  Scaled up replica set mysql-c85f7f79c to 1
    
    C:\Users\user>
    ```
    

6. Seznam lusků vytvořených nasazením

    `kubectl get pods -l <app=label> -n <your-user-namespace>`

    Zde je ukázkový výstup.

    
    ```powershell
    C:\Users\user>kubectl get pods -l app=mysql -n userns1
    NAME                    READY   STATUS    RESTARTS   AGE
    mysql-c85f7f79c-vzz7j   1/1     Running   1          14m
    
    C:\Users\user>
    ```
    
7. Zkontrolujte PersistentVolumeClaim.

    `kubectl describe pvc <your-pvc-name>`

    Zde je ukázkový výstup.

    
    ```powershell
    C:\Users\user>kubectl describe pvc mysql-pv-claim -n userns1
    Name:          mysql-pv-claim
    Namespace:     userns1
    StorageClass:
    Status:        Bound
    Volume:        mylocalsmbshare1
    Labels:        <none>
    Annotations:   kubectl.kubernetes.io/last-applied-configuration:
                     {"apiVersion":"v1","kind":"PersistentVolumeClaim","metadata":{"annotations":{},"name":"mysql-pv-claim","namespace":"userns1"},"spec":{"acc...
                   pv.kubernetes.io/bind-completed: yes
    Finalizers:    [kubernetes.io/pvc-protection]
    Capacity:      32Ti
    Access Modes:  RWO,RWX
    VolumeMode:    Filesystem
    Mounted By:    mysql-c85f7f79c-vzz7j
    Events:        <none>
    
    C:\Users\user>
    ```
    

## <a name="verify-mysql-is-running"></a>Ověřte, že je MySQL spuštěný.


Chcete-li spustit příkaz proti kontejneru v pod, který používá MySQL, zadejte:

`kubectl exec <your-pod-with-the-app> -i -t -n <your-namespace> -- mysql`

Zde je ukázkový výstup.

```powershell
C:\Users\user>kubectl exec mysql-c85f7f79c-vzz7j -i -t -n userns1 -- mysql
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 1
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
Tady je ukázkový výstup při odstranění trvalého připojení k virtuálnímu okruhu.

```powershell
C:\Users\user>kubectl delete pvc mysql-pv-claim -n userns1
persistentvolumeclaim "mysql-pv-claim" deleted
C:\Users\user>
```                                                                                         

Souč_hod již není vázán na okruh PVC, protože byl odstraněn okruh PVC. Vzhledem k tom, že byla tato PV vytvořena, bude nutné sdílenou složku odstranit. Postupujte takto:

1. Odpojte sdílenou složku. V Azure Portal přejděte na **prostředek Azure Stack Edge > sdílené složky** a vyberte a klikněte na sdílenou složku, kterou chcete odpojit. Vyberte **Odpojit** a potvrďte operaci. Počkejte, než se sdílená složka odpojí. Odpojování uvolní sdílenou složku (a tudíž přidruženou PersistentVolume) z clusteru Kubernetes. 

    ![Odpojit místní sdílenou složku pro PV](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/unmount-edge-local-share-1.png)

1. Teď můžete vybrat **Odstranit** a potvrdit odstranění, aby se odstranila vaše sdílená složka. To by mělo také odstranit sdílenou složku a odpovídající souč_hod.

    ![Odstranit místní sdílenou složku pro PV](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/delete-edge-local-share-1.png)


## <a name="next-steps"></a>Další kroky

Další informace o tom, jak dynamicky zřizovat úložiště, najdete v tématu [nasazení stavové aplikace prostřednictvím dynamického zřizování na zařízení Azure Stack Edge pro](azure-stack-edge-gpu-deploy-stateful-application-dynamic-provision-kubernetes.md) .

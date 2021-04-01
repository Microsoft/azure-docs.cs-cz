---
title: Nasazení kontejneru Edge Azure SQL v Kubernetes – Azure SQL Edge
description: Další informace o nasazení kontejneru Azure SQL Edge v Kubernetes
keywords: SQL Edge, kontejner, Kubernetes
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 31a454c93ad5192f387306a8ec557c4e4d3ae991
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "93395287"
---
# <a name="deploy-an-azure-sql-edge-container-in-kubernetes"></a>Nasazení kontejneru Edge Azure SQL v Kubernetes

Azure SQL Edge můžete nasadit v clusteru Kubernetes jako modul IoT Edge prostřednictvím Azure IoT Edge spuštěného v Kubernetes nebo jako samostatný kontejner pod. Ve zbývající části tohoto článku se zaměříme na samostatné nasazení kontejnerů v clusteru Kubernetes. Informace o nasazení Azure IoT Edge v Kubernetes najdete v tématu [Azure IoT Edge na Kubernetes (Preview)](https://microsoft.github.io/iotedge-k8s-doc/introduction.html).

Tento kurz ukazuje, jak nakonfigurovat instanci Azure SQL Edge s vysokou dostupností v kontejneru v clusteru Kubernetes. 

> [!div class="checklist"]
> * Vytvoření hesla SA
> * Vytvoření úložiště
> * Vytvoření nasazení
> * Připojení pomocí SQL Server Management Studio (SSMS)
> * Ověření selhání a obnovení

Kubernetes 1,6 a novější má podporu [tříd úložiště](https://kubernetes.io/docs/concepts/storage/storage-classes/), [trvalých deklarací svazků](https://kubernetes.io/docs/concepts/storage/storage-classes/#persistentvolumeclaims)a [typu diskového svazku Azure](https://github.com/kubernetes/examples/tree/master/staging/volumes/azure_disk). Instance Azure SQL Edge můžete v Kubernetes vytvářet a spravovat nativně. V příkladu v tomto článku se dozvíte, jak vytvořit [nasazení](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) , abyste dosáhli konfigurace vysoké dostupnosti podobné instanci clusteru převzetí služeb při selhání sdíleného disku. V této konfiguraci Kubernetes hraje roli nástroje Orchestrator clusteru. Když instance Azure SQL Edge v kontejneru dojde k chybě, nástroj Orchestrator spustí jinou instanci kontejneru, která se připojí ke stejnému trvalému úložišti.

![Azure SQL Edge v clusteru Kubernetes](media/deploy-kubernetes/kubernetes-sql-edge.png)

V předchozím diagramu `azure-sql-edge` je kontejner v poli [pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/). Kubernetes orchestruje prostředky v clusteru. [Sada replik](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/) zajišťuje, že se po selhání uzlu automaticky obnoví uzel pod. Aplikace se připojují ke službě. V tomto případě služba představuje nástroj pro vyrovnávání zatížení, který hostuje IP adresu, která zůstává stejná i po selhání `azure-sql-edge` .

V následujícím diagramu `azure-sql-edge` se kontejner nezdařil. Nástroj Orchestrator Kubernetes garantuje správný počet zdravých instancí v sadě replik a spustí nový kontejner podle konfigurace. Nástroj Orchestrator spustí nový uzel pod stejným uzlem a `azure-sql-edge` znovu se připojí ke stejnému trvalému úložišti. Služba se připojí k znovu vytvořenému `azure-sql-edge` .

![Azure SQL Edge v clusteru Kubernetes po selhání pod](media/deploy-kubernetes/kubernetes-sql-edge-after-pod-fail.png)

V následujícím diagramu `azure-sql-edge` se nezdařil uzel hostující kontejner. Nástroj Orchestrator spustí nový uzel pod jiným uzlem a `azure-sql-edge` znovu se připojí ke stejnému trvalému úložišti. Služba se připojí k znovu vytvořenému `azure-sql-edge` .

![Azure SQL Edge v clusteru Kubernetes po selhání uzlu](media/deploy-kubernetes/kubernetes-sql-edge-after-node-fail.png)

## <a name="prerequisites"></a>Požadavky

* **Cluster Kubernetes**
   - Tento kurz vyžaduje cluster Kubernetes. Postup ke správě clusteru pomocí [kubectl](https://kubernetes.io/docs/user-guide/kubectl/) . 

   - Pro účely tohoto kurzu budeme používat službu Azure Kubernetes k nasazení Edge Azure SQL. Další informace najdete v tématu [nasazení clusteru Azure Kubernetes Service (AKS)](../aks/tutorial-kubernetes-deploy-cluster.md) , který umožňuje vytvořit a připojit se k clusteru Kubernetes s jedním uzlem v AKS `kubectl` . 

   >[!NOTE]
   >Pro ochranu proti selhání uzlu vyžaduje cluster Kubernetes více než jeden uzel.

* **Azure CLI**
   - Pokyny v tomto kurzu byly ověřeny proti 2.10.1 Azure CLI.

## <a name="create-a-kubernetes-namespace-for-sql-edge-deployment"></a>Vytvoření oboru názvů Kubernetes pro nasazení SQL Edge

Vytvořte nový obor názvů v clusteru Kubernetes. Tento obor názvů se použije k nasazení okraje SQL Edge a všech potřebných artefaktů. Další informace o oborech názvů Kubernetes najdete v tématu [obory názvů](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/).

   ```azurecli
   kubectl create namespace <namespace name>
   ```  

## <a name="create-an-sa-password"></a>Vytvoření hesla SA

Vytvořte heslo SA v clusteru Kubernetes. Kubernetes může spravovat citlivé informace o konfiguraci, jako jsou hesla jako [tajné klíče](https://kubernetes.io/docs/concepts/configuration/secret/).

Následující příkaz vytvoří heslo pro účet SA:

   ```azurecli
   kubectl create secret generic mssql --from-literal=SA_PASSWORD="MyC0m9l&xP@ssw0rd" -n <namespace name>
   ```  

   Nahraďte `MyC0m9l&xP@ssw0rd` složitým heslem.

## <a name="create-storage"></a>Vytvoření úložiště

Konfigurace [trvalého svazku](https://kubernetes.io/docs/concepts/storage/persistent-volumes/) a [deklarace identity trvalého svazku](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#persistent-volume-claim-protection) v clusteru Kubernetes Dokončete následující kroky: 

1. Vytvořte manifest pro definování třídy úložiště a deklarace identity trvalého svazku.  Manifest určuje zřizování, parametry a [zásady pro vynucení](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#reclaiming)úložiště. Cluster Kubernetes používá tento manifest k vytvoření trvalého úložiště. 

   Následující příklad YAML definuje třídu úložiště a deklaraci trvalého objemu. Zřizovací modul třídy úložiště je `azure-disk` , protože tento cluster Kubernetes je v Azure. Typ účtu úložiště je `Standard_LRS` . Deklarace identity trvalého svazku je pojmenována `mssql-data` . Metadata deklarace trvalého svazku obsahují poznámku, která ji připojuje zpátky do třídy úložiště. 

   ```yaml
   kind: StorageClass
   apiVersion: storage.k8s.io/v1
   metadata:
        name: azure-disk
   provisioner: kubernetes.io/azure-disk
   parameters:
     storageaccounttype: Standard_LRS
     kind: managed
   ---
   kind: PersistentVolumeClaim
   apiVersion: v1
   metadata:
     name: mssql-data
     annotations:
       volume.beta.kubernetes.io/storage-class: azure-disk
   spec:
     accessModes:
     - ReadWriteOnce
     resources:
       requests:
         storage: 8Gi
   ```

   Uložte soubor (například **PVC. yaml**).

2. Vytvořte v Kubernetes deklaraci trvalého svazku.

   ```azurecli
   kubectl apply -f <Path to pvc.yaml file> -n <namespace name>
   ```

   `<Path to pvc.yaml file>` je umístění, kam jste soubor uložili.

   Trvalý svazek se automaticky vytvoří jako účet úložiště Azure a je vázaný na deklarace identity trvalého svazku. 

    ![Snímek obrazovky s příkazem trvalé deklarace svazku](media/deploy-kubernetes/pvc-cmd.png)

3. Ověřte deklaraci trvalého svazku.

   ```azurecli
   kubectl describe pvc <PersistentVolumeClaim>  -n <name of the namespace>
   ```

   `<PersistentVolumeClaim>` je název deklarace identity trvalého svazku.

   V předchozím kroku je pojmenována deklarace identity trvalého svazku `mssql-data` . Pokud chcete zobrazit metadata týkající se deklarace identity trvalého svazku, spusťte následující příkaz:

   ```azurecli
   kubectl describe pvc mssql-data  -n <namespace name>
   ```

   Vrácená metadata obsahují hodnotu s názvem `Volume` . Tato hodnota se mapuje na název objektu BLOB.

   ![Snímek vrácených metadat, včetně svazku](media/deploy-kubernetes/describe-volume.png)

4. Ověřte trvalý svazek.

   ```azurecli
   kubectl describe pv -n <namespace name>
   ```

   `kubectl` Vrátí metadata o trvalém svazku, který byl automaticky vytvořen a svázán s deklarací trvalého svazku. 

## <a name="create-the-deployment"></a>Vytvoření nasazení

V tomto příkladu je kontejner hostující instanci Azure SQL Edge popsaný jako objekt nasazení Kubernetes. Nasazení vytvoří sadu replik. Sada replik vytvoří pod. 

V tomto kroku vytvoříte manifest, který popisuje kontejner na základě image Docker Azure SQL Edge. Manifest odkazuje na `mssql-data` deklaraci trvalého objemu a `mssql` tajný klíč, který jste už použili v clusteru Kubernetes. Manifest také popisuje [službu](https://kubernetes.io/docs/concepts/services-networking/service/). Tato služba je nástroj pro vyrovnávání zatížení. Nástroj pro vyrovnávání zatížení garantuje, že IP adresa přetrvává po obnovení instance Azure SQL Edge. 

1. Vytvořte manifest (soubor YAML) pro popis nasazení. Následující příklad popisuje nasazení, včetně kontejneru založeného na imagi kontejneru Azure SQL Edge.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sqledge-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: sqledge
  template:
    metadata:
      labels:
        app: sqledge
    spec:
      volumes:
        - name: sqldata
          persistentVolumeClaim:
            claimName: mssql-data
      containers:
        - name: azuresqledge
          image: mcr.microsoft.com/azure-sql-edge:latest
          ports:
            - containerPort: 1433
          volumeMounts:
            - name: sqldata
              mountPath: /var/opt/mssql
          env:
            - name: MSSQL_PID
              value: "Developer"
            - name: ACCEPT_EULA
              value: "Y"
            - name: SA_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mssql
                  key: SA_PASSWORD
            - name: MSSQL_AGENT_ENABLED
              value: "TRUE"
            - name: MSSQL_COLLATION
              value: "SQL_Latin1_General_CP1_CI_AS"
            - name: MSSQL_LCID
              value: "1033"
      terminationGracePeriodSeconds: 30
      securityContext:
        fsGroup: 10001
---
apiVersion: v1
kind: Service
metadata:
  name: sqledge-deployment
spec:
  selector:
    app: sqledge
  ports:
    - protocol: TCP
      port: 1433
      targetPort: 1433
      name: sql
  type: LoadBalancer
```

   Zkopírujte předchozí kód do nového souboru s názvem `sqldeployment.yaml` . Aktualizujte následující hodnoty: 

   * MSSQL_PID `value: "Developer"` : nastaví kontejner pro spuštění Azure SQL Edge Developer Edition. Edice Developer Edition není licencovaná pro produkční data. Pokud je nasazení pro produkční použití, nastavte edici na `Premium` . 

      >[!NOTE]
      >Další informace najdete v tématu [jak licencovat Azure SQL Edge](https://azure.microsoft.com/pricing/details/sql-edge/).

   * `persistentVolumeClaim`: Tato hodnota vyžaduje záznam pro `claimName:` , který se mapuje na název použitý pro deklaraci trvalého svazku. Tento kurz používá oblast `mssql-data`. 

   * `name: SA_PASSWORD`: Nakonfiguruje image kontejneru, aby nastavila heslo SA, jak je definováno v této části.

     ```yaml
     valueFrom:
       secretKeyRef:
         name: mssql
         key: SA_PASSWORD 
     ```

     Když Kubernetes nasadí kontejner, odkazuje na tajný kód s názvem `mssql` pro získání hodnoty pro heslo. 

   >[!NOTE]
   >Pomocí `LoadBalancer` typu služby je instance Azure SQL Edge přístupná vzdáleně (přes Internet) na portu 1433.

   Uložte soubor (například **sqledgedeploy. yaml**).

2. Vytvořte nasazení.

   ```azurecli
   kubectl apply -f <Path to sqledgedeploy.yaml file> -n <namespace name>
   ```

   `<Path to sqldeployment.yaml file>` je umístění, kam jste soubor uložili.

   ![Snímek obrazovky s příkazem nasazení](media/deploy-kubernetes/deploy-sql-cmd.png)

   Nasazení a služba jsou vytvořeny. Instance Azure SQL Edge je v kontejneru, který je připojený k trvalému úložišti.

   Chcete-li zobrazit stav pod, zadejte `kubectl get pod -n <namespace name>` .

   ![Snímek obrazovky s příkazem Get pod](media/deploy-kubernetes/get-sql-pod-cmd.png)

   Na předchozím obrázku má pod stavem `Running` . Tento stav označuje, že je kontejner připravený. To může trvat několik minut.

   >[!NOTE]
   >Po vytvoření nasazení může trvat několik minut, než se zobrazí pole pod. Prodleva je způsobená tím, že cluster z dokovacího centra načte image kontejneru Azure SQL Edge. Po prvním pokusu o obnovení bitové kopie mohou být následná nasazení rychlejší, pokud je nasazení na uzel, který již obsahuje uloženou bitovou kopii. 

3. Ověřte, že služba běží. Spusťte následující příkaz:

   ```azurecli
   kubectl get services -n <namespace name>
   ```

   Tento příkaz vrátí služby, které jsou spuštěny, a také interní a externí IP adresy pro služby. Poznamenejte si externí IP adresu `mssql-deployment` služby. Tuto IP adresu použijte pro připojení k Edge SQL Azure. 

   ![Snímek obrazovky s příkazem Get Service](media/deploy-kubernetes/get-service-cmd.png)

   Chcete-li získat další informace o stavu objektů v clusteru Kubernetes, spusťte příkaz:

   ```azurecli
   az aks browse --resource-group <MyResourceGroup> --name <MyKubernetesClustername>
   ```  

## <a name="connect-to-the-azure-sql-edge-instance"></a>Připojit k instanci Azure SQL Edge

Pokud jste nakonfigurovali kontejner, jak je popsáno, můžete se připojit k aplikaci mimo virtuální síť Azure. Použijte `sa` účet a externí IP adresu pro službu. Použijte heslo, které jste nakonfigurovali jako tajný kód Kubernetes. Další informace o připojení k instanci Azure SQL Edge najdete v tématu [připojení k Azure SQL Edge](connect.md).

## <a name="verify-failure-and-recovery"></a>Ověření selhání a obnovení

Chcete-li ověřit selhání a obnovení, můžete odstranit pole pod. Proveďte následující kroky:

1. Uveďte seznam pod běžícím Azure SQL Edge.

   ```azurecli
   kubectl get pods -n <namespace name>
   ```

   Poznamenejte si název pod běžícím Azure SQL Edge.

2. Odstraňte pole pod.

   ```azurecli
   kubectl delete pod sqledge-deployment-7df66c9999-rc9xl
   ```
   `sqledge-deployment-7df66c9999-rc9xl` je hodnota vrácená z předchozího kroku pro název pod. 

Kubernetes automaticky znovu vytvoří objekt pod a obnoví instanci Azure SQL Edge a připojí se k trvalému úložišti. Použijte `kubectl get pods` k ověření, že je nasazen nový pod. Použijte `kubectl get services` k ověření, že IP adresa nového kontejneru je stejná. 

## <a name="summary"></a>Souhrn

V tomto kurzu jste zjistili, jak nasadit kontejnery Azure SQL Edge do clusteru Kubernetes pro zajištění vysoké dostupnosti. 

> [!div class="checklist"]
> * Vytvoření hesla SA
> * Vytvoření úložiště
> * Vytvoření nasazení
> * Připojení k Azure SQL Edge Management studia (SSMS)
> * Ověření selhání a obnovení

## <a name="next-steps"></a>Další kroky

- [Úvod do Kubernetes](../aks/intro-kubernetes.md)
- [Machine Learning a uměle inteligentní informace s ONNXem v SQL Edge](onnx-overview.md).
- [Vytvoření kompletního řešení IoT pomocí SQL Edge pomocí IoT Edge](tutorial-deploy-azure-resources.md).
- [Streamování dat ve službě Azure SQL Edge](stream-data.md)
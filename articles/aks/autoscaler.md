---
title: Kubernetes v Azure – automatického škálování clusteru
description: Další informace o použití automatického škálování clusteru Azure Kubernetes Service (AKS) pro automatické škálování clusteru podle potřeby.
services: container-service
author: sakthivetrivel
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/19/18
ms.author: sakthivetrivel
ms.custom: mvc
ms.openlocfilehash: 6ec39116596c7abb7b1d26f864cdb57d839c88be
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49365131"
---
# <a name="cluster-autoscaler-on-azure-kubernetes-service-aks---preview"></a>Cluster automatického škálování ve službě Azure Kubernetes Service (AKS) – ve verzi Preview

Azure Kubernetes Service (AKS) poskytuje flexibilní řešení umožňujícím nasazení spravovaného clusteru Kubernetes v Azure. Jako zdroj požadavků zvýšit, clusteru automatického škálování umožňuje clusteru k rozvoji tohoto poptávky podle omezení můžete nastavit. Automatického škálování clusteru (CA) to provádí škálování uzly agenta na základě čekajících pody. Prohledá clusteru pravidelně kontrolovat čekající podů nebo prázdné uzly a zvyšuje velikost, pokud je to možné. Ve výchozím nastavení, certifikační Autorita hledá čekající podů každých 10 sekund a odebere uzel, pokud je nepotřebné po dobu více než 10 minut. Při použití s [automatického škálování podů vodorovné](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) (HPA) HPA aktualizuje replik podů a prostředky podle potřeby. Pokud nejsou k dispozici dostatek nebo nepotřebné uzlů po tomto škálování podů, certifikační Autorita bude reagovat a naplánovat podů na novou sadu uzlů.

Tento článek popisuje, jak nasadit cluster automatického škálování na agentské uzly. Ale protože automatického škálování clusteru se nasadit v oboru názvů kube-system, automatického škálování nebude snižovat uzlu spuštěn tohoto podu.

> [!IMPORTANT]
> Integrace automatického škálování clusteru Azure Kubernetes Service (AKS) je aktuálně ve **ve verzi preview**. Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Některé aspekty této funkce se můžou před zveřejněním změnit.
>

## <a name="prerequisites"></a>Požadavky

Tento dokument předpokládá, že máte cluster AKS RBAC povolena. Pokud potřebujete AKS cluster, přečtěte si [rychlý start Azure Kubernetes Service (AKS)][aks-quick-start].

 Použití automatického škálování clusteru, musí váš cluster používat Kubernetes v1.10.X nebo vyšší a musí být povoleny RBAC. K upgradu vašeho clusteru, najdete v článku [upgrade clusteru AKS][aks-upgrade].

## <a name="gather-information"></a>Shromážděte informace

Pokud chcete generovat oprávnění pro váš cluster automatického škálování pro spuštění v clusteru, spusťte tento skript bash:

```sh
#! /bin/bash
ID=`az account show --query id -o json`
SUBSCRIPTION_ID=`echo $ID | tr -d '"' `

TENANT=`az account show --query tenantId -o json`
TENANT_ID=`echo $TENANT | tr -d '"' | base64`

read -p "What's your cluster name? " cluster_name
read -p "Resource group name? " resource_group

CLUSTER_NAME=`echo $cluster_name | base64`
RESOURCE_GROUP=`echo $resource_group | base64`

PERMISSIONS=`az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/$SUBSCRIPTION_ID" -o json`
CLIENT_ID=`echo $PERMISSIONS | sed -e 's/^.*"appId"[ ]*:[ ]*"//' -e 's/".*//' | base64`
CLIENT_SECRET=`echo $PERMISSIONS | sed -e 's/^.*"password"[ ]*:[ ]*"//' -e 's/".*//' | base64`

SUBSCRIPTION_ID=`echo $ID | tr -d '"' | base64 `

NODE_RESOURCE_GROUP=`az aks show --name $cluster_name  --resource-group $resource_group -o tsv --query 'nodeResourceGroup' | base64`

echo "---
apiVersion: v1
kind: Secret
metadata:
    name: cluster-autoscaler-azure
    namespace: kube-system
data:
    ClientID: $CLIENT_ID
    ClientSecret: $CLIENT_SECRET
    ResourceGroup: $RESOURCE_GROUP
    SubscriptionID: $SUBSCRIPTION_ID
    TenantID: $TENANT_ID
    VMType: QUtTCg==
    ClusterName: $CLUSTER_NAME
    NodeResourceGroup: $NODE_RESOURCE_GROUP
---"
```

Po provedení kroků ve skriptu, skript bude výstup své údaje ve formě tajný klíč, například takto:

```yaml
---
apiVersion: v1
kind: Secret
metadata:
  name: cluster-autoscaler-azure
  namespace: kube-system
data:
  ClientID: <base64-encoded-client-id>
  ClientSecret: <base64-encoded-client-secret>$
  ResourceGroup: <base64-encoded-resource-group>  SubscriptionID: <base64-encode-subscription-id>
  TenantID: <base64-encoded-tenant-id>
  VMType: QUtTCg==
  ClusterName: <base64-encoded-clustername>
  NodeResourceGroup: <base64-encoded-node-resource-group>
---
```

Dále získejte název fondu uzel spuštěním následujícího příkazu. 

```console
$ kubectl get nodes --show-labels
```

Výstup:

```console
NAME                       STATUS    ROLES     AGE       VERSION   LABELS
aks-nodepool1-37756013-0   Ready     agent     1h        v1.10.3   agentpool=nodepool1,beta.kubernetes.io/arch=amd64,beta.kubernetes.io/instance-type=Standard_DS1_v2,beta.kubernetes.io/os=linux,failure-domain.beta.kubernetes.io/region=eastus,failure-domain.beta.kubernetes.io/zone=0,kubernetes.azure.com/cluster=MC_[resource-group]\_[cluster-name]_[location],kubernetes.io/hostname=aks-nodepool1-37756013-0,kubernetes.io/role=agent,storageprofile=managed,storagetier=Premium_LRS
 ```

Extrahujte hodnota popisku **Neznámá**. Výchozí název pro fond uzlů clusteru je "nodepool1".

Teď pomocí tajného klíče a uzlu fondu, můžete vytvořit graf nasazení.

## <a name="create-a-deployment-chart"></a>Vytvoření grafu nasazení

Vytvořte soubor s názvem `aks-cluster-autoscaler.yaml`a zkopírujte do něj následující kód YAML.

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  labels:
    k8s-addon: cluster-autoscaler.addons.k8s.io
    k8s-app: cluster-autoscaler
  name: cluster-autoscaler
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: cluster-autoscaler
  labels:
    k8s-addon: cluster-autoscaler.addons.k8s.io
    k8s-app: cluster-autoscaler
rules:
- apiGroups: [""]
  resources: ["events","endpoints"]
  verbs: ["create", "patch"]
- apiGroups: [""]
  resources: ["pods/eviction"]
  verbs: ["create"]
- apiGroups: [""]
  resources: ["pods/status"]
  verbs: ["update"]
- apiGroups: [""]
  resources: ["endpoints"]
  resourceNames: ["cluster-autoscaler"]
  verbs: ["get","update"]
- apiGroups: [""]
  resources: ["nodes"]
  verbs: ["watch","list","get","update"]
- apiGroups: [""]
  resources: ["pods","services","replicationcontrollers","persistentvolumeclaims","persistentvolumes"]
  verbs: ["watch","list","get"]
- apiGroups: ["extensions"]
  resources: ["replicasets","daemonsets"]
  verbs: ["watch","list","get"]
- apiGroups: ["policy"]
  resources: ["poddisruptionbudgets"]
  verbs: ["watch","list"]
- apiGroups: ["apps"]
  resources: ["statefulsets"]
  verbs: ["watch","list","get"]
- apiGroups: ["storage.k8s.io"]
  resources: ["storageclasses"]
  verbs: ["get", "list", "watch"]

---
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: cluster-autoscaler
  namespace: kube-system
  labels:
    k8s-addon: cluster-autoscaler.addons.k8s.io
    k8s-app: cluster-autoscaler
rules:
- apiGroups: [""]
  resources: ["configmaps"]
  verbs: ["create"]
- apiGroups: [""]
  resources: ["configmaps"]
  resourceNames: ["cluster-autoscaler-status"]
  verbs: ["delete","get","update"]

---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: cluster-autoscaler
  labels:
    k8s-addon: cluster-autoscaler.addons.k8s.io
    k8s-app: cluster-autoscaler
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-autoscaler
subjects:
  - kind: ServiceAccount
    name: cluster-autoscaler
    namespace: kube-system

---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: cluster-autoscaler
  namespace: kube-system
  labels:
    k8s-addon: cluster-autoscaler.addons.k8s.io
    k8s-app: cluster-autoscaler
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: cluster-autoscaler
subjects:
  - kind: ServiceAccount
    name: cluster-autoscaler
    namespace: kube-system

---
apiVersion: extensions/v1
kind: Deployment
metadata:
  labels:
    app: cluster-autoscaler
  name: cluster-autoscaler
  namespace: kube-system
spec:
  replicas: 1
  selector:
    matchLabels:
      app: cluster-autoscaler
  template:
    metadata:
      labels:
        app: cluster-autoscaler
    spec:
      serviceAccountName: cluster-autoscaler
      containers:
      - image: gcr.io/google-containers/cluster-autoscaler:v1.2.2
        imagePullPolicy: Always
        name: cluster-autoscaler
        resources:
          limits:
            cpu: 100m
            memory: 300Mi
          requests:
            cpu: 100m
            memory: 300Mi
        command:
        - ./cluster-autoscaler
        - --v=3
        - --logtostderr=true
        - --cloud-provider=azure
        - --skip-nodes-with-local-storage=false
        - --nodes=1:10:nodepool1
        env:
        - name: ARM_SUBSCRIPTION_ID
          valueFrom:
            secretKeyRef:
              key: SubscriptionID
              name: cluster-autoscaler-azure
        - name: ARM_RESOURCE_GROUP
          valueFrom:
            secretKeyRef:
              key: ResourceGroup
              name: cluster-autoscaler-azure
        - name: ARM_TENANT_ID
          valueFrom:
            secretKeyRef:
              key: TenantID
              name: cluster-autoscaler-azure
        - name: ARM_CLIENT_ID
          valueFrom:
            secretKeyRef:
              key: ClientID
              name: cluster-autoscaler-azure
        - name: ARM_CLIENT_SECRET
          valueFrom:
            secretKeyRef:
              key: ClientSecret
              name: cluster-autoscaler-azure
        - name: ARM_VM_TYPE
          valueFrom:
            secretKeyRef:
              key: VMType
              name: cluster-autoscaler-azure
        - name: AZURE_CLUSTER_NAME
          valueFrom:
            secretKeyRef:
              key: ClusterName
              name: cluster-autoscaler-azure
        - name: AZURE_NODE_RESOURCE_GROUP
          valueFrom:
            secretKeyRef:
              key: NodeResourceGroup
              name: cluster-autoscaler-azure
      restartPolicy: Always
```

Zkopírujte a vložte tajného klíče vytvořené v předchozím kroku a vloží na začátek souboru.

Dále nastavte rozsah uzlů, zadejte v argumentu pro `--nodes` pod `command` ve formuláři MIN:MAX:NODE_POOL_NAME. Příklad: `--nodes=3:10:nodepool1` Nastaví minimální počet uzlů na 3, maximální počet uzlů na 10 a název fondu uzel nodepool1.

Vyplňte pole obrázku v rámci **kontejnery** verzi automatického škálování clusteru, kterou chcete použít. AKS vyžaduje v1.2.2 nebo novější. Tento příklad používá v1.2.2.

## <a name="deployment"></a>Nasazení

Nasazení clusteru automatického spuštění

```console
kubectl create -f aks-cluster-autoscaler.yaml
```

Pokud chcete zkontrolovat, zda je spuštěn automatického škálování clusteru, použijte následující příkaz a zkontrolujte seznam pody. Měla by existovat pod předponu "clusteru automatického" systémem. Pokud se zobrazí, váš cluster automatického nasazení.

```console
kubectl -n kube-system get pods
```

Chcete-li zobrazit stav modulu automatického škálování clusteru, spusťte

```console
kubectl -n kube-system describe configmap cluster-autoscaler-status
```

## <a name="interpreting-the-cluster-autoscaler-status"></a>Interpretace stav automatického škálování clusteru

```console
$ kubectl -n kube-system describe configmap cluster-autoscaler-status
Name:         cluster-autoscaler-status
Namespace:    kube-system
Labels:       <none>
Annotations:  cluster-autoscaler.kubernetes.io/last-updated=2018-07-25 22:59:22.661669494 +0000 UTC

Data
====
status:
----
Cluster-autoscaler status at 2018-07-25 22:59:22.661669494 +0000 UTC:
Cluster-wide:
  Health:      Healthy (ready=1 unready=0 notStarted=0 longNotStarted=0 registered=1 longUnregistered=0)
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC
  ScaleUp:     NoActivity (ready=1 registered=1)
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC
  ScaleDown:   NoCandidates (candidates=0)
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC

NodeGroups:
  Name:        nodepool1
  Health:      Healthy (ready=1 unready=0 notStarted=0 longNotStarted=0 registered=1 longUnregistered=0 cloudProviderTarget=1 (minSize=1, maxSize=5))
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC
  ScaleUp:     NoActivity (ready=1 cloudProviderTarget=1)
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC
  ScaleDown:   NoCandidates (candidates=0)
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC


Events:  <none>
```

Stav clusteru automatického škálování vám umožní zobrazit stav modulu automatického škálování clusteru na dvou různých úrovních: celoclusterový a v rámci jednotlivých skupin uzlu. Protože AKS aktuálně podporuje pouze jeden fond uzlů, tyto metriky jsou stejné.

* Stav označuje celkový stav uzlů. Pokud automatického škálování clusteru potýká k vytvoření a odebírá uzly v clusteru, tento stav se změní na "Není v pořádku". K dispozici je také rozdělení stavu různých uzlech:
    * "Připraveno" znamená, že uzel je připraven k mít podů naplánované na něm.
    * "NEPŘIPRAVENÉM" znamená, že uzel, který se podařilo přerušit dolů po jeho spuštění.
    * "NotStarted" znamená, že uzel není úplně spuštěna.
    * "LongNotStarted" znamená, že uzel se nepovedlo spustit ve lhůtě přiměřené.
    * "Registrované znamená, že uzel je registrován ve skupině
    * "Zrušit" znamená, že uzel existuje na straně zprostředkovatele clusteru, ale nepovedlo se zaregistrovat v Kubernetes.
  
* ScaleUp můžete zkontrolovat, když cluster zjistí, že je že vertikálního navýšení se budou objevovat ve vašem clusteru.
    * Přechod se při změně počtu uzlů v clusteru nebo uzlu změně stavu.
    * Počet uzlů připraven je počet uzlů k dispozici a připraveny v clusteru. 
    * CloudProviderTarget je počet uzlů, automatického škálování clusteru bylo zjištěno, že cluster je potřeba zpracovat zatížení.

* ScaleDown můžete zkontrolovat, zda je mimo provoz kandidáty na škálování. 
    * Mezi kandidáty pro vertikální snížení kapacity je uzel, který určil automatického škálování clusteru je možné odebrat, aniž to ovlivní schopnost clusteru zvládnout zatížení. 
    * Časů zadaných zobrazit čas poslední clusteru došlo k zaškrtnutí pro vertikální snížení kapacity kandidáty a jeho poslední čas přechodu.

Nakonec můžete v rámci události, získají až libovolném měřítku nebo vertikálně snížit kapacitu události, neúspěšné nebo úspěšné a jejich časy, které provedl automatického škálování clusteru.

## <a name="next-steps"></a>Další postup

Použití automatického škálování clusteru pomocí automatického škálování podů vodorovné, podívejte se na [škálování aplikace a infrastruktury Kubernetes][aks-tutorial-scale].

Další informace o nasazení a správě AKS najdete v kurzech AKS.

> [!div class="nextstepaction"]
> [Kurz AKS][aks-tutorial-prepare-app]

<!-- LINKS - internal -->
[aks-quick-start]: ./kubernetes-walkthrough.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-scale]: ./tutorial-kubernetes-scale.md
[aks-upgrade]: ./upgrade-cluster.md

<!-- LINKS - external -->
[cluster-autoscale]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md

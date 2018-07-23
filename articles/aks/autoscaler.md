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
ms.openlocfilehash: 4f8df8e7004ca3cee832b6230dc153b21e2a6c18
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2018
ms.locfileid: "39186709"
---
# <a name="cluster-autoscaler-on-azure-kubernetes-service-aks---preview"></a>Cluster automatického škálování ve službě Azure Kubernetes Service (AKS) – ve verzi Preview

Azure Kubernetes Service (AKS) poskytuje flexibilní řešení umožňujícím nasazení spravovaného clusteru Kubernetes v Azure. Jako zdroj požadavků zvýšit, clusteru automatického škálování umožňuje clusteru k rozvoji tohoto poptávky podle omezení můžete nastavit. Automatického škálování clusteru (CA) to provádí škálování uzly agenta na základě čekajících pody. Prohledá clusteru pravidelně kontrolovat čekající podů nebo prázdné uzly a zvyšuje velikost, pokud je to možné. Ve výchozím nastavení, certifikační Autorita hledá čekající podů každých 10 sekund a odebere uzel, pokud je nepotřebné po dobu více než 10 minut. Při použití s automatického škálování podů vodorovné (HPA), aktualizuje HPA replik podů a prostředky podle potřeby. Pokud již nejsou dost nebo nepotřebné uzlů po tomto škálování podů, certifikační Autorita bude reagovat a naplánovat podů na novou sadu uzlů.

> [!IMPORTANT]
> Integrace automatického škálování clusteru Azure Kubernetes Service (AKS) je aktuálně ve **ve verzi preview**. Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Některé aspekty této funkce se můžou před zveřejněním změnit.
>

## <a name="prerequisites"></a>Požadavky

Tento dokument předpokládá, že máte cluster AKS RBAC povolena. Pokud potřebujete AKS cluster, přečtěte si [rychlý start Azure Kubernetes Service (AKS)][aks-quick-start].

 Použití automatického škálování clusteru, musí váš cluster používat Kubernetes v1.10.X nebo vyšší a musí být povoleny RBAC. K upgradu vašeho clusteru, najdete v článku [upgrade clusteru AKS][aks-upgrade].

## <a name="gather-information"></a>Shromážděte informace

Následující seznam obsahuje všechny informace, které je nutné zadat v definici automatického škálování.

- *ID předplatného*: ID odpovídající předplatné použité pro tento cluster
- *Název skupiny prostředků* : název clusteru, patří do skupiny prostředků 
- *Název clusteru*: název clusteru
- *ID klienta*: ID aplikace, které udělují oprávnění generování kroku
- *Tajný kód klienta*: tajný kód aplikace, které udělují oprávnění generování kroku
- *ID tenanta*: ID tenanta (Vlastník účtu)
- *Skupina prostředků uzel*: název skupiny prostředků obsahující agentské uzly v clusteru
- *Název fondu uzlu*: název uzlu fondu, které byste o ni stupnice
- *Minimální počet uzlů*: minimální počet uzlů v clusteru
- *Maximální počet uzlů*: maximální počet uzlů v clusteru
- *Typ virtuálního počítače*: služby sloužící ke generování clusteru Kubernetes

Získejte ID vašeho předplatného: 

``` azurecli
az account show --query id
```

Vytvořte sadu přihlašovacích údajů Azure tím, že spustíte následující příkaz:

```console
$ az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<subscription-id>" --output json

"appId": <app-id>,
"displayName": <display-name>,
"name": <name>,
"password": <app-password>,
"tenant": <tenant-id>
```

ID aplikace, heslo a ID Tenanta bude clientID, clientSecret a ID Tenanta v následujících krocích.

Spuštěním následujícího příkazu získejte název fondu uzlů. 

```console
$ kubectl get nodes --show-labels
```

Výstup:

```console
NAME                       STATUS    ROLES     AGE       VERSION   LABELS
aks-nodepool1-37756013-0   Ready     agent     1h        v1.10.3   agentpool=nodepool1,beta.kubernetes.io/arch=amd64,beta.kubernetes.io/instance-type=Standard_DS1_v2,beta.kubernetes.io/os=linux,failure-domain.beta.kubernetes.io/region=eastus,failure-domain.beta.kubernetes.io/zone=0,kubernetes.azure.com/cluster=MC_[resource-group]\_[cluster-name]_[location],kubernetes.io/hostname=aks-nodepool1-37756013-0,kubernetes.io/role=agent,storageprofile=managed,storagetier=Premium_LRS
 ```

Extrahujte hodnota popisku **Neznámá**. Výchozí název pro fond uzlů clusteru je "nodepool1".

Pokud chcete získat název vaší skupiny prostředků uzlu, extrahovat hodnotu popisku **kubernetes.azure.com<span></span>/cluster**. Název skupiny prostředků uzlu je obvykle ve formátu MC_ [skupiny prostředků]\__ [název clusteru] [umístění].

Parametr vmType odkazuje na službu používá, která následuje AKS.

Teď byste měli mít následující informace:

- ID předplatného
- ResourceGroup
- Název clusteru
- ID klienta
- ClientSecret
- ID Tenanta
- NodeResourceGroup
- VMType

V dalším kroku kódování všechny tyto hodnoty ve formátu base64. Chcete-li například kódování VMType hodnoty ve formátu base64:

```console
$ echo AKS | base64
QUtTCg==
```

## <a name="create-secret"></a>Vytvoření tajného kódu
Na základě těchto dat vytvořte tajný kód k nasazení pomocí na hodnoty zjištěné v předchozích krocích v následujícím formátu:

```yaml
---
apiVersion: v1
kind: Secret
metadata:
  name: cluster-autoscaler-azure
  namespace: kube-system
data:
  ClientID: <base64-encoded-client-id>
  ClientSecret: <base64-encoded-client-secret>
  ResourceGroup: <base64-encoded-resource-group>
  SubscriptionID: <base64-encode-subscription-id>
  TenantID: <base64-encoded-tenant-id>
  VMType: QUtTCg==
  ClusterName: <base64-encoded-clustername>
  NodeResourceGroup: <base64-encoded-node-resource-group>
---
```

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
apiVersion: rbac.authorization.k8s.io/v1beta1
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
apiVersion: rbac.authorization.k8s.io/v1beta1
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
apiVersion: rbac.authorization.k8s.io/v1beta1
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
apiVersion: rbac.authorization.k8s.io/v1beta1
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
apiVersion: extensions/v1beta1
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
      - image: k8s.gcr.io/cluster-autoscaler:{{ ca_version }}
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
kubectl create -f cluster-autoscaler-containerservice.yaml
```

Pokud chcete zkontrolovat, zda je spuštěn automatického škálování clusteru, použijte následující příkaz a zkontrolujte seznam pody. Pokud s předponou "clusteru automatického" spuštěna pod byla nasazena automatického škálování vašeho clusteru.

```console
kubectl -n kube-system get pods
```

Chcete-li zobrazit stav modulu automatického škálování clusteru, spusťte

```console
kubectl -n kube-system describe configmap cluster-autoscaler-status
```

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

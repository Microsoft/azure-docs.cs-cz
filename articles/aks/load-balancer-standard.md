---
title: Ve verzi Preview – použijte nástroj pro vyrovnávání zatížení standardní SKU ve službě Azure Kubernetes Service (AKS)
description: Zjistěte, jak pomocí nástroje pro vyrovnávání zatížení standardní SKU vystavit svoje služby Azure Kubernetes Service (AKS).
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 06/25/2019
ms.author: zarhoads
ms.openlocfilehash: a9cf3db3a15fab5a2f067a146950e02923a20379
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476811"
---
# <a name="preview---use-a-standard-sku-load-balancer-in-azure-kubernetes-service-aks"></a>Ve verzi Preview – použijte nástroj pro vyrovnávání zatížení standardní SKU ve službě Azure Kubernetes Service (AKS)

Pokud chcete poskytnout přístup k aplikacím ve službě Azure Kubernetes Service (AKS), můžete vytvořit a používat služby Azure Load Balancer. Nástroj pro vyrovnávání zatížení, který je spuštěn v AKS je možné jako interní nebo externím vyrovnáváním zatížení. Interní nástroj zpřístupňuje služby Kubernetes pouze pro aplikace spuštěné ve stejné virtuální síti jako clusteru AKS. Externím vyrovnáváním zatížení přijímá jeden nebo více veřejných IP adres pro příchozí přenos dat a je dostupné, externě pomocí veřejných IP adres služby Kubernetes.

Nástroj Azure Load Balancer je k dispozici ve dvou skladových jednotkách - *základní* a *standardní*. Ve výchozím nastavení *základní* SKU se používá při manifestu služby se používá k vytvoření nástroje pro vyrovnávání zatížení v AKS. Použití *standardní* SKU nástroje pro vyrovnávání zatížení poskytuje další funkce a funkce, jako je například větší velikost fondu back-endu a zóny dostupnosti. Je důležité pochopit rozdíly mezi *standardní* a *základní* nástroje pro vyrovnávání zatížení před volbou, která se použije. Po vytvoření clusteru AKS, nemůžete změnit, nástroje pro vyrovnávání zatížení SKU pro daný cluster. Další informace o *základní* a *standardní* skladové položky, naleznete v tématu [porovnání SKU nástroje pro vyrovnávání zatížení Azure][azure-lb-comparison].

Tento článek ukazuje, jak vytvořit a používat služby Azure Load Balancer s *standardní* SKU s Azure Kubernetes Service (AKS).

Tento článek předpokládá základní znalost konceptů Kubernetes a nástroje pro vyrovnávání zatížení Azure. Další informace najdete v tématu [Kubernetes pro Azure Kubernetes Service (AKS) základní koncepty][kubernetes-concepts] and [What is Azure Load Balancer?][azure-lb].

Tato funkce je aktuálně ve verzi Preview.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, tento článek vyžaduje použití Azure CLI verze 2.0.59 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][install-azure-cli].

## <a name="before-you-begin"></a>Než začnete

Objektu služby cluster AKS vyžaduje oprávnění ke správě síťových prostředků, pokud používáte existující podsíť nebo skupinu prostředků. Obecně platí, přiřaďte *Přispěvatel sítě* role instančního objektu služby delegované prostředků. Další informace o oprávněních najdete v části [AKS delegovaného přístupu k další prostředky Azure][aks-sp].

Musíte vytvořit cluster AKS, který nastaví SKU nástroje pro vyrovnávání zatížení do *standardní* místo výchozího *základní*. Vytvoření clusteru AKS je obsažen v pozdějším kroku, ale je nejprve potřeba povolit několik funkcí ve verzi preview.

> [!IMPORTANT]
> Funkce AKS ve verzi preview jsou samoobslužných služeb, vyjádřit výslovný souhlas. Jsou poskytovány shromažďovat zpětnou vazbu a chyb z naší komunitě. Ve verzi preview nejsou tyto funkce určené k použití v produkčním prostředí. Funkce ve verzi public preview spadají pod "co možná nejlepší" podporu. Pomoc od týmů AKS technická podpora je k dispozici během pracovní doby tichomořské časové pásmo (PST) pouze. Další informace najdete v tématu následující články o podpoře:
>
> * [Zásady podpory AKS][aks-support-policies]
> * [Nejčastější dotazy k podpoře Azure][aks-faq]

### <a name="install-aks-preview-cli-extension"></a>Instalace rozšíření aks ve verzi preview rozhraní příkazového řádku

Chcete-li použít nástroje pro vyrovnávání zatížení Azure standardní SKU, je nutné *aks ve verzi preview* CLI verze rozšíření 0.4.1 nebo vyšší. Nainstalujte *aks ve verzi preview* pomocí rozšíření Azure CLI [přidat rozšíření az][az-extension-add] command, then check for any available updates using the [az extension update][az-extension-update] příkaz::

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-aksazurestandardloadbalancer-preview-feature"></a>Zaregistrujte AKSAzureStandardLoadBalancer funkce ve verzi preview

K vytvoření clusteru AKS, můžete použít nástroj pro vyrovnávání zatížení s *standardní* skladovou Položku, musíte povolit *AKSAzureStandardLoadBalancer* funkce příznak v rámci předplatného. *AKSAzureStandardLoadBalancer* funkce také používá *VMSSPreview* při vytváření clusteru pomocí škálovací sady virtuálních počítačů. Tato funkce poskytuje nejnovější sadě vylepšení služby při konfiguraci clusteru. Když to není nutné, doporučujeme povolíte *VMSSPreview* také příznak funkce.

> [!CAUTION]
> Při registraci funkce v rámci předplatného nelze nyní zrušit registraci této funkce. Po povolení některé funkce ve verzi preview se výchozí hodnoty lze pro všechny clustery AKS, pak jste vytvořili v rámci předplatného. Nepovolí funkce ve verzi preview na předplatná pro produkční prostředí. Testování funkce ve verzi preview a shromažďování zpětné vazby pomocí samostatné předplatné.

Zaregistrovat *VMSSPreview* a *AKSAzureStandardLoadBalancer* příznaky funkcí pomocí [az funkce register][az-feature-register] příkaz, jak je znázorněno v následujícím příkladu:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "VMSSPreview"
az feature register --namespace "Microsoft.ContainerService" --name "AKSAzureStandardLoadBalancer"
```

> [!NOTE]
> Libovolný cluster AKS, vytvoříte po úspěšném zaregistrování *VMSSPreview* nebo *AKSAzureStandardLoadBalancer* příznaky funkcí, použijte tento cluster prostředí ve verzi preview. Pokračujte k vytvoření clusterů pravidelných a plně podporované nepovolí funkce ve verzi preview na předplatná pro produkční prostředí. Používejte samostatný testovací nebo vývojové předplatné Azure pro testování funkcí ve verzi preview.

Trvá několik minut, než se stav zobrazíte *registrované*. Vy můžete zkontrolovat stav registrace pomocí [seznam funkcí az][az-feature-list] příkaz:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/VMSSPreview')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSAzureStandardLoadBalancer')].{Name:name,State:properties.state}"
```

Až to budete mít, aktualizujte registraci *Microsoft.ContainerService* poskytovatele prostředků pomocí [az provider register][az-provider-register] příkaz:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="limitations"></a>Omezení

Při vytváření a správě AKS clustery, které podporují nástroj pro vyrovnávání zatížení s platí následující omezení *standardní* SKU:

* Při použití *standardní* SKU nástroje pro vyrovnávání zatížení, musíte také povolit veřejné adresy a vyhněte se vytváření Azure Policy, která zakazuje vytváření IP. Automaticky vytvoří AKS cluster *standardní* SKU veřejné IP adresy ve stejné skupině prostředků vytvořili pro cluster AKS, který se obvykle nazývá *MC_* na začátku. AKS přiřadí veřejnou IP adresu nastavit *standardní* SKU nástroje pro vyrovnávání zatížení. Veřejná IP adresa se vyžaduje pro povolení provozu odchozího přenosu dat z clusteru AKS. Tato veřejná IP adresa je také nutné k údržbě připojení mezi ovládací prvek roviny i agentských uzlů také tak, aby zachovala kompatibilita s předchozími verzemi AKS.
* Při použití *standardní* SKU nástroje load balancer, je nutné použít Kubernetes verze 1.13.5 nebo vyšší.

Tato funkce je ve verzi preview, platí následující další omezení:

* Při použití *standardní* SKU nástroje pro vyrovnávání zatížení ve službě AKS, nelze nastavit vlastní veřejné IP adresy pro výchozí přenos dat pro nástroj pro vyrovnávání zatížení. Musíte použít IP adresu, kterou AKS přiřadí nástroj pro vyrovnávání zatížení.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků Azure je logická skupina, ve které se nasazují a spravují prostředky Azure. Při vytváření skupiny prostředků se zobrazí výzva k zadání umístění. Toto umístění je uložení metadat skupiny prostředků, je také kde prostředky běží v Azure, pokud nezadáte jiné oblasti při vytváření prostředku. Abyste vytvořili skupinu prostředků pomocí [vytvořit skupiny az][az-group-create] příkazu.

Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Následující příklad výstupu ukazuje úspěšně vytvořili skupinu prostředků:

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "eastus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

## <a name="create-aks-cluster"></a>Vytvoření clusteru AKS
Chcete-li spustit cluster AKS, který podporuje nástroj pro vyrovnávání zatížení s *standardní* SKU, váš cluster potřebuje nastavit *zatížení. nástroj pro vyrovnávání sku* parametr *standardní*. Tento parametr vytvoří nástroj pro vyrovnávání zatížení s *standardní* skladové položky při vytváření clusteru. Při spuštění *nástroj pro vyrovnávání zatížení* service ve vašem clusteru, konfigurace *standardní* SK nástroje pro vyrovnávání zatížení se aktualizuje konfiguraci služby. Použití [az aks vytvořit][az-aks-create] příkaz pro vytvoření clusteru AKS s názvem *myAKSCluster*.

> [!NOTE]
> *Zatížení. nástroj pro vyrovnávání sku* vlastnost jde použít jenom při vytvoření clusteru. Nástroj pro vyrovnávání zatížení skladové položky nelze změnit po vytvoření clusteru AKS. Navíc můžete použít pouze jeden typ nástroje pro vyrovnávání zatížení SKU v jednom clusteru.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-vmss \
    --node-count 1 \
    --kubernetes-version 1.14.0 \
    --load-balancer-sku standard \
    --generate-ssh-keys
```

Po několika minutách se příkaz dokončí a vrátí hodnotu ve formátu JSON informace o clusteru.

## <a name="connect-to-the-cluster"></a>Připojení ke clusteru

Ke správě clusteru Kubernetes použijete [kubectl][kubectl], klienta příkazového řádku Kubernetes. Pokud používáte Azure Cloud Shell, `kubectl` je již nainstalována. Chcete-li nainstalovat `kubectl` místně, použijte [az aks install-cli][az-aks-install-cli] příkaz:

```azurecli
az aks install-cli
```

Ke konfiguraci `kubectl` pro připojení k vašemu clusteru Kubernetes, použijte [az aks get-credentials][az-aks-get-credentials] příkazu. Tento příkaz stáhne přihlašovací údaje a nakonfiguruje rozhraní příkazového řádku Kubernetes pro jejich použití.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Pokud chcete ověřit připojení ke clusteru, použijte příkaz [kubectl get][kubectl-get], který vrátí seznam uzlů clusteru.

```azurecli-interactive
kubectl get nodes
```

Následující příklad výstupu ukazuje jeden uzel vytvořený v předchozích krocích. Ujistěte se, zda je stav uzlu *připravené*:

```
NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.14.0
```

## <a name="verify-your-cluster-uses-the-standard-sku"></a>Ověřte váš cluster používá *standardní* SKU

Použití [az aks zobrazit][az-aks-show] zobrazíte konfiguraci clusteru.

```console
$ az aks show --resource-group myResourceGroup --name myAKSCluster

{
  "aadProfile": null,
  "addonProfiles": null,
   ...
   "networkProfile": {
    "dnsServiceIp": "10.0.0.10",
    "dockerBridgeCidr": "172.17.0.1/16",
    "loadBalancerSku": "standard",
    ...
```

Ověření *loadBalancerSku* vlastnost ukazovat *standardní*.

## <a name="use-the-load-balancer"></a>Pomocí nástroje pro vyrovnávání zatížení

Pokud chcete použít nástroj pro vyrovnávání zatížení v clusteru, vytvoření manifestu služby s typem služby *nástroj pro vyrovnávání zatížení*. Chcete-li zobrazit nástroje pro vyrovnávání zatížení funguje, vytvořte jiný manifest s ukázkovou aplikací ke spuštění ve vašem clusteru. Tato ukázková aplikace je k dispozici prostřednictvím nástroje pro vyrovnávání zatížení a je možné zobrazit pomocí prohlížeče.

Vytvoření manifestu s názvem `sample.yaml` jak je znázorněno v následujícím příkladu:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-back
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-back
        image: redis
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 6379
          name: redis
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-back
spec:
  ports:
  - port: 6379
  selector:
    app: azure-vote-back
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-front
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-front
        image: microsoft/azure-vote-front:v1
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 80
        env:
        - name: REDIS
          value: "azure-vote-back"
```

Výše uvedené manifestu nakonfiguruje dvě nasazení: *azure-vote-front* a *azure-vote-back*. Ke konfiguraci *azure-vote-front* nasazení zpřístupní pomocí nástroje pro vyrovnávání zatížení, vytvoření manifestu s názvem `standard-lb.yaml` jak je znázorněno v následujícím příkladu:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

Služba *azure-vote-front* používá *nástroj pro vyrovnávání zatížení* typ konfigurace nástroje pro vyrovnávání zatížení v clusteru AKS pro připojení k *azure-vote-front* nasazení.

Nasazení ukázkové aplikace a pomocí nástroje pro vyrovnávání zatížení [použití kubectl][kubectl-apply] a zadejte název vaší YAML manifestů:

```console
kubectl apply -f sample.yaml
kubectl apply -f standard-lb.yaml
```

*Standardní* SKU nástroje pro vyrovnávání zatížení je nyní nakonfigurováno pro ukázkovou aplikaci vystavit. Zobrazit podrobnosti o službě z *azure-vote-front* pomocí [kubectl get][kubectl-get] zobrazíte veřejné IP adresy nástroje pro vyrovnávání zatížení. Veřejnou IP adresu nástroje pro vyrovnávání zatížení je zobrazena ve *EXTERNAL-IP* sloupce. Může trvat minutu nebo dvě IP adresy v *\<čekající\>* na skutečné externí IP adresu, jak je znázorněno v následujícím příkladu:

```
$ kubectl get service azure-vote-front

NAME                TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)        AGE
azure-vote-front    LoadBalancer   10.0.227.198   52.179.23.131   80:31201/TCP   16s
```

Přejděte na veřejnou IP adresu v prohlížeči a ověřte, že vidíte ukázkové aplikace. V předchozím příkladu je veřejná IP adresa `52.179.23.131`.

![Obrázek přechodu na aplikaci Azure Vote](media/container-service-kubernetes-walkthrough/azure-vote.png)

> [!NOTE]
> Můžete také nakonfigurovat interní nástroj pro vyrovnávání zatížení a bez odkrytí veřejnou IP adresu. Chcete-li nakonfigurovat nástroj pro vyrovnávání zatížení jako interní, přidejte `service.beta.kubernetes.io/azure-load-balancer-internal: "true"` jako poznámka k *nástroj pro vyrovnávání zatížení* služby. Zobrazí se příklad yaml manifest také další podrobnosti o službě interní služby load balancer [tady][internal-lb-yaml].

## <a name="clean-up-the-standard-sku-load-balancer-configuration"></a>Vyčištění konfigurace služby Vyrovnávání zatížení standardní SKU

Chcete odebrat konfiguraci ukázkové aplikace a zatížení nástroje pro vyrovnávání, použijte [odstranit kubectl][kubectl-delete]:

```console
kubectl delete -f sample.yaml
kubectl delete -f standard-lb.yaml
```

## <a name="next-steps"></a>Další postup

Další informace o službách Kubernetes na [dokumentace ke službě services Kubernetes][kubernetes-services].

<!-- LINKS - External -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-create]: /cli/azure/group#az-group-create
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb]: ../load-balancer/load-balancer-overview.md
[azure-lb-comparison]: ../load-balancer/load-balancer-overview.md#skus
[install-azure-cli]: /cli/azure/install-azure-cli
[internal-lb-yaml]: internal-lb.md#create-an-internal-load-balancer
[kubernetes-concepts]: concepts-clusters-workloads.md
[use-kubenet]: configure-kubenet.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update

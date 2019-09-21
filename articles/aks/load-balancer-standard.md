---
title: Použití nástroje pro vyrovnávání zatížení Standard SKU ve službě Azure Kubernetes (AKS)
description: Naučte se používat nástroj pro vyrovnávání zatížení se standardní SKU k vystavování služeb pomocí Azure Kubernetes Service (AKS).
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 09/05/2019
ms.author: zarhoads
ms.openlocfilehash: d2a0ff5db6707c4f765c71937a7d0f0749401959
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/20/2019
ms.locfileid: "71172212"
---
# <a name="use-a-standard-sku-load-balancer-in-azure-kubernetes-service-aks"></a>Použití nástroje pro vyrovnávání zatížení Standard SKU ve službě Azure Kubernetes (AKS)

Pokud chcete zajistit přístup k vašim aplikacím ve službě Azure Kubernetes Service (AKS), můžete vytvořit a použít Azure Load Balancer. Nástroj pro vyrovnávání zatížení, který běží na AKS, se dá použít jako interní nebo externí nástroj pro vyrovnávání zatížení. Interní nástroj pro vyrovnávání zatížení zpřístupňuje službu Kubernetes jenom aplikacím běžícím ve stejné virtuální síti jako cluster AKS. Externí nástroj pro vyrovnávání zatížení přijímá jednu nebo více veřejných IP adres pro příchozí provoz a zpřístupňuje službu Kubernetes externě pomocí veřejných IP adres.

Azure Load Balancer je k dispozici ve dvou SKU – *Basic* a *Standard*. Ve výchozím nastavení se *základní* SKU používá, když se k vytvoření nástroje pro vyrovnávání zatížení v AKS používá manifest služby. Použití nástroje pro vyrovnávání zatížení *Standard* SKU poskytuje další funkce, jako je větší velikost fondu back-endu a zóny dostupnosti. Před výběrem, který se má použít, je důležité porozumět rozdílům mezi *standardními* a *základními* nástroji pro vyrovnávání zatížení. Po vytvoření clusteru AKS již nelze změnit SKLADOVOU položku Nástroje pro vyrovnávání zatížení pro daný cluster. Další informace o jednotkách *Basic* a *Standard* najdete v tématu [porovnání skladové položky služby Vyrovnávání zatížení Azure][azure-lb-comparison].

V tomto článku se dozvíte, jak vytvořit a použít Azure Load Balancer se *standardní* SKU se službou Azure Kubernetes Service (AKS).

Tento článek předpokládá základní znalost konceptů Kubernetes a Azure Load Balancer. Další informace najdete v tématu [Kubernetes Core koncepty pro Azure Kubernetes Service (AKS)][kubernetes-concepts] a [co je Azure Load Balancer?][azure-lb].

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít spuštěnou verzi Azure CLI 2.0.59 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][install-azure-cli].

## <a name="before-you-begin"></a>Před zahájením

Pokud použijete existující podsíť nebo skupinu prostředků, instanční objekt služby AKS potřebuje oprávnění ke správě síťových prostředků. Obecně přiřaďte roli *Přispěvatel sítě* k instančnímu objektu u delegovaných prostředků. Další informace o oprávněních najdete v tématu [delegování přístupu AKS k ostatním prostředkům Azure][aks-sp].

Musíte vytvořit cluster AKS, který nastaví SKU pro nástroj pro vyrovnávání zatížení na *standardní* místo výchozí *Basic*.

### <a name="install-aks-preview-cli-extension"></a>Nainstalovat rozšíření CLI AKS-Preview

Pokud chcete použít standardní SKLADOVOU položku služby Azure Load Balancer, potřebujete rozšíření *AKS-Preview* CLI verze 0.4.12 nebo vyšší. Nainstalujte rozšíření Azure CLI *AKS-Preview* pomocí příkazu [AZ Extension Add][az-extension-add] a potom zkontrolujte, jestli nejsou dostupné aktualizace, pomocí příkazu [AZ Extension Update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>Omezení

Při vytváření a správě clusterů AKS, které podporují Nástroj pro vyrovnávání zatížení se *standardní* SKU, platí následující omezení:

* Pro povolení odchozího provozu z clusteru AKS se vyžaduje aspoň jedna předpona veřejné IP adresy nebo IP adresy. K udržení připojení mezi řídicí rovinou a uzly agenta a také s cílem udržet kompatibilitu s předchozími verzemi AKS se vyžaduje taky předpona veřejných IP adres nebo IP adres. Máte následující možnosti, jak zadat veřejné IP adresy nebo předpony IP pomocí nástroje pro vyrovnávání zatížení *Standard* SKU:
    * Poskytněte vlastní veřejné IP adresy.
    * Zadejte vlastní předpony veřejných IP adres.
    * Zadejte číslo až 100, aby mohl cluster AKS vytvořit tento počet veřejných IP adres *Standard* SKU ve stejné skupině prostředků, která se vytvořila jako cluster AKS, který se obvykle jmenuje s *MC_* na začátku. AKS přiřadí veřejnou IP adresu k nástroji pro vyrovnávání zatížení *Standard* SKU. Ve výchozím nastavení se jedna veřejná IP adresa automaticky vytvoří ve stejné skupině prostředků jako cluster AKS, pokud není zadaná žádná veřejná IP adresa, předpona veřejné IP adresy nebo počet IP adres. Je také nutné, abyste povolili veřejné adresy a nevytvořili Azure Policy, která zakazuje vytváření IP adres.
* Při použití *standardní* skladové položky (SKU) pro nástroj pro vyrovnávání zatížení je nutné použít Kubernetes verze 1,13 nebo vyšší.
* Definování SKU nástroje pro vyrovnávání zatížení se dá provést jenom při vytváření clusteru AKS. SKU nástroje pro vyrovnávání zatížení nelze změnit po vytvoření clusteru AKS.
* V jednom clusteru můžete použít jenom jednu SKU nástroje pro vyrovnávání zatížení.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků Azure je logická skupina, ve které se nasazují a spravují prostředky Azure. Při vytváření skupiny prostředků se zobrazí výzva k zadání umístění. V tomto umístění se ukládají metadata skupin prostředků, a to i v případě, že se vaše prostředky spouštějí v Azure, pokud při vytváření prostředků nezadáte jinou oblast. Vytvořte skupinu prostředků pomocí příkazu [AZ Group Create][az-group-create] .

Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Následující příklad výstupu ukazuje, že skupina prostředků byla úspěšně vytvořena:

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
Aby bylo možné spustit cluster AKS, který podporuje nástroj pro vyrovnávání zatížení se *standardní* SKU, musí váš cluster nastavit parametr *Vyrovnávání zatížení – SKU* na *úroveň Standard*. Tento parametr vytvoří nástroj pro vyrovnávání zatížení se *standardní* SKU při vytvoření clusteru. Když ve svém clusteru *spustíte službu Vyrovnávání* zatížení, konfigurace nástroje Load Balancer *Standard* SKU se aktualizuje s konfigurací služby. Pomocí příkazu [AZ AKS Create][az-aks-create] vytvořte cluster AKS s názvem *myAKSCluster*.

> [!NOTE]
> Vlastnost *pro vyrovnávání zatížení – vlastnost SKU* se dá použít jenom při vytváření clusteru. SKU nástroje pro vyrovnávání zatížení nelze změnit po vytvoření clusteru AKS. V jednom clusteru taky můžete použít jenom jeden typ SKU nástroje pro vyrovnávání zatížení.
> 
> Pokud chcete použít vlastní veřejné IP adresy, použijte parametry *Vyrovnávání zatížení – odchozí IP adresy*nebo *Nástroje pro vyrovnávání zatížení-odchozí-IP-předpony* . Oba tyto parametry lze použít také při [aktualizaci clusteru](#optional---provide-your-own-public-ips-or-prefixes-for-egress).

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys
```

Po několika minutách se příkaz dokončí a vrátí informace o clusteru ve formátu JSON.

## <a name="connect-to-the-cluster"></a>Připojení ke clusteru

Ke správě clusteru Kubernetes použijete klienta příkazového řádku Kubernetes [kubectl][kubectl]. Pokud používáte Azure Cloud Shell, `kubectl` je již nainstalováno. Pokud chcete `kubectl` nainstalovat místně, použijte příkaz [AZ AKS Install-CLI][az-aks-install-cli] :

```azurecli
az aks install-cli
```

Pokud chcete `kubectl` nakonfigurovat připojení ke clusteru Kubernetes, použijte příkaz [AZ AKS Get-Credentials][az-aks-get-credentials] . Tento příkaz stáhne pověření a nakonfiguruje rozhraní příkazového řádku Kubernetes pro jejich použití.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Pokud chcete ověřit připojení ke clusteru, použijte příkaz [kubectl get][kubectl-get], který vrátí seznam uzlů clusteru.

```azurecli-interactive
kubectl get nodes
```

Následující příklad výstupu ukazuje jeden uzel vytvořený v předchozích krocích. Ujistěte se, že stav uzlu je *připravený*:

```
NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.13.10
```

## <a name="verify-your-cluster-uses-the-standard-sku"></a>Ověření, že cluster používá *standardní* SKU

Pro zobrazení konfigurace clusteru použijte příkazem [AZ AKS show][az-aks-show] .

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

Ověřte, že vlastnost *loadBalancerSku* se zobrazuje jako *Standard*.

## <a name="use-the-load-balancer"></a>Použití nástroje pro vyrovnávání zatížení

Chcete-li použít nástroj pro vyrovnávání zatížení v clusteru, vytvořte v něm manifest služby s typem služby pro *Vyrovnávání*zatížení. Chcete-li zobrazit fungování nástroje pro vyrovnávání zatížení, vytvořte další manifest s ukázkovou aplikací pro spuštění v clusteru. Tato ukázková aplikace je vystavena prostřednictvím nástroje pro vyrovnávání zatížení a je možné ji zobrazit v prohlížeči.

Vytvořte manifest s názvem `sample.yaml` , jak je znázorněno v následujícím příkladu:

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

Výše uvedený manifest konfiguruje dvě nasazení: *Azure-hlasování-přední* a *Azure-hlasování*. Pokud chcete nakonfigurovat nasazení *Azure – hlasování* , aby bylo zveřejněné pomocí nástroje pro vyrovnávání zatížení, vytvořte `standard-lb.yaml` manifest s názvem, jak je znázorněno v následujícím příkladu:

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

Služba *Azure – hlasová přední strana* *používá typ vyrovnávání* zatížení pro konfiguraci nástroje pro vyrovnávání zatížení v clusteru AKS pro připojení k nasazení *Azure-hlasování* .

Nasaďte ukázkovou aplikaci a nástroj pro vyrovnávání zatížení pomocí [kubectl použít][kubectl-apply] a zadejte název vašich manifestů YAML:

```console
kubectl apply -f sample.yaml
kubectl apply -f standard-lb.yaml
```

Nástroj pro vyrovnávání zatížení *Standard* SKU je teď nakonfigurovaný tak, aby vystavoval ukázkovou aplikaci. Podívejte se na podrobnosti o službě *Azure – hlasování* s použitím [kubectl Get][kubectl-get] k zobrazení veřejné IP adresy nástroje pro vyrovnávání zatížení. Veřejná IP adresa nástroje pro vyrovnávání zatížení je zobrazena ve sloupci *External-IP* . Změna IP adresy z *\<nedokončené\>* na skutečnou externí IP adresu může trvat minutu nebo dvě, jak je znázorněno v následujícím příkladu:

```
$ kubectl get service azure-vote-front

NAME                TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)        AGE
azure-vote-front    LoadBalancer   10.0.227.198   52.179.23.131   80:31201/TCP   16s
```

V prohlížeči přejděte na veřejnou IP adresu a ověřte, že vidíte ukázkovou aplikaci. Ve výše uvedeném příkladu je `52.179.23.131`veřejná IP adresa.

![Obrázek přechodu na aplikaci Azure Vote](media/container-service-kubernetes-walkthrough/azure-vote.png)

> [!NOTE]
> Nástroj pro vyrovnávání zatížení můžete také nakonfigurovat tak, aby byl interní a nezveřejňuje veřejnou IP adresu. Pokud chcete nástroj pro vyrovnávání zatížení nakonfigurovat jako `service.beta.kubernetes.io/azure-load-balancer-internal: "true"` interní, přidejte jako anotaci do služby *Vyrovnávání* zatížení. [Tady][internal-lb-yaml]vidíte ukázkový manifest YAML a další podrobnosti o interním nástroji pro vyrovnávání zatížení.

## <a name="optional---scale-the-number-of-managed-public-ips"></a>Volitelné – škálování počtu spravovaných veřejných IP adres

Pokud používáte nástroj pro vyrovnávání zatížení *standardní* skladové položky se spravovanými odchozími veřejnými IP adresami, které se ve výchozím nastavení vytvoří, můžete škálovat počet spravovaných odchozích veřejných IP adres pomocí parametru *Vyrovnávání zatížení – spravovaná-IP-Count* .

Chcete-li aktualizovat existující cluster, spusťte následující příkaz. Tento parametr je také možné nastavit v době vytvoření clusteru a mít několik spravovaných odchozích veřejných IP adres.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 2
```

Výše uvedený příklad nastaví počet spravovaných odchozích veřejných IP adres na *2* pro cluster *myAKSCluster* v *myResourceGroup*. 

K nastavení počátečního počtu spravovaných odchozích veřejných IP adres při vytváření clusteru `--load-balancer-managed-outbound-ip-count` a jeho nastavení na požadovanou hodnotu můžete použít taky parametr Managed- *IP-Count nástroje pro vyrovnávání zatížení* . Výchozí počet spravovaných odchozích veřejných IP adres je 1.

## <a name="optional---provide-your-own-public-ips-or-prefixes-for-egress"></a>Volitelné – Poskytněte vlastní veřejné IP adresy nebo předpony pro odchozí přenosy

Při použití *standardního* nástroje pro vyrovnávání zatížení SKU cluster AKS automaticky vytvoří veřejnou IP adresu ve stejné skupině prostředků vytvořené pro cluster AKS a přiřadí veřejnou IP adresu k nástroji pro vyrovnávání zatížení *Standard* SKU. Alternativně můžete přiřadit vlastní veřejnou IP adresu při vytváření clusteru nebo můžete aktualizovat vlastnosti nástroje pro vyrovnávání zatížení existujícího clusteru.

Když nasazujete víc IP adres nebo předpon, můžete při definování IP adresy za jediným objektem nástroje pro vyrovnávání zatížení definovat více služeb. Koncový bod odchozího přenosu konkrétních uzlů bude záviset na službě, ke které jsou přidruženy.

> [!IMPORTANT]
> Pro odchozí přenosy s vaší *standardní* SKU nástroje pro vyrovnávání zatížení musíte použít veřejné IP adresy *Standard* SKU. SKU svých veřejných IP adres můžete ověřit pomocí příkazu [AZ Network Public-IP show][az-network-public-ip-show] :
>
> ```azurecli-interactive
> az network public-ip show --resource-group myResourceGroup --name myPublicIP --query sku.name -o tsv
> ```

Pomocí příkazu [AZ Network Public-IP show zobrazíte][az-network-public-ip-show] seznam ID veřejných IP adres.

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIP --query id -o tsv
```

Výše uvedený příkaz zobrazuje ID veřejné IP adresy *myPublicIP* ve skupině prostředků *myResourceGroup* .

Pomocí příkazu *AZ AKS Update* s parametrem *Load Balancer-Outbound-IPS* aktualizujte svůj cluster pomocí veřejných IP adres.

V následujícím příkladu se používá parametr *Vyrovnávání zatížení – odchozí IP adresy* s ID z předchozího příkazu.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

Můžete také použít předpony veřejných IP adres pro výstup pomocí nástroje pro vyrovnávání zatížení *Standard* SKU. V následujícím příkladu se pomocí příkazu [AZ Network Public-IP prefix show zobrazí][az-network-public-ip-prefix-show] seznam ID předpon veřejných IP adres:

```azurecli-interactive
az network public-ip prefix show --resource-group myResourceGroup --name myPublicIPPrefix --query id -o tsv
```

Výše uvedený příkaz zobrazuje ID předpony veřejné IP adresy *myPublicIPPrefix* ve skupině prostředků *myResourceGroup* .

V následujícím příkladu se používá parametr *Vyrovnávání zatížení-odchozí-IP-předpony* s ID z předchozího příkazu.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

> [!IMPORTANT]
> Veřejné IP adresy a předpony IP adres musí být ve stejné oblasti a v rámci stejného předplatného jako cluster AKS. 

### <a name="define-your-own-public-ip-or-prefixes-at-cluster-create-time"></a>Definování vlastní veřejné IP adresy nebo předpon v době vytvoření clusteru

Možná budete chtít uvést vlastní IP adresy nebo předpony IP pro výstup v době vytváření clusteru pro podporu scénářů, jako je například povolený koncový bod odchozích dat. Přidejte stejné parametry uvedené výše do vašeho kroku vytvoření clusteru a definujte vlastní veřejné IP adresy a předpony IP adres na začátku životního cyklu clusteru.

Pomocí příkazu *AZ AKS Create* s parametrem *Load Balancer-Outbound-IPS* vytvořte nový cluster s vašimi veřejnými IP adresami na začátku.

```
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

Pomocí příkazu *AZ AKS Create* s parametrem *Load Balance-Outbound-IP-prefixs* vytvořte nový cluster s předponami veřejné IP adresy na začátku.

```
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

## <a name="clean-up-the-standard-sku-load-balancer-configuration"></a>Vyčištění konfigurace nástroje pro vyrovnávání zatížení Standard SKU

Pokud chcete odebrat ukázkovou aplikaci a konfiguraci nástroje pro vyrovnávání zatížení, použijte [kubectl Delete][kubectl-delete]:

```console
kubectl delete -f sample.yaml
kubectl delete -f standard-lb.yaml
```

## <a name="next-steps"></a>Další kroky

Další informace o službách Kubernetes Services najdete v [dokumentaci ke službám Kubernetes][kubernetes-services].

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
[az-network-public-ip-show]: /cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-show
[az-network-public-ip-prefix-show]: /cli/azure/network/public-ip/prefix?view=azure-cli-latest#az-network-public-ip-prefix-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb]: ../load-balancer/load-balancer-overview.md
[azure-lb-comparison]: ../load-balancer/load-balancer-overview.md#skus
[install-azure-cli]: /cli/azure/install-azure-cli
[internal-lb-yaml]: internal-lb.md#create-an-internal-load-balancer
[kubernetes-concepts]: concepts-clusters-workloads.md
[use-kubenet]: configure-kubenet.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update


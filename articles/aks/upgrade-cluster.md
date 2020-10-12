---
title: Upgrade clusteru Azure Kubernetes Service (AKS)
description: Zjistěte, jak upgradovat cluster Azure Kubernetes Service (AKS), abyste získali nejnovější funkce a aktualizace zabezpečení.
services: container-service
ms.topic: article
ms.date: 05/28/2020
ms.openlocfilehash: da46c44dc9cc16dfa44aacb15b35b652c0c912a9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87050622"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Upgrade clusteru Azure Kubernetes Service (AKS)

V rámci životního cyklu clusteru AKS je často potřeba upgradovat na nejnovější verzi Kubernetes. Je důležité použít nejnovější verze zabezpečení Kubernetes nebo upgradovat, abyste získali nejnovější funkce. V tomto článku se dozvíte, jak upgradovat hlavní komponenty nebo jeden výchozí fond uzlů v clusteru AKS.

Clustery AKS, které používají více fondů uzlů nebo uzlů Windows serveru, najdete v tématu [upgrade fondu uzlů v AKS][nodepool-upgrade].

## <a name="before-you-begin"></a>Než začnete

Tento článek vyžaduje, abyste spustili Azure CLI verze 2.0.65 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

> [!WARNING]
> Upgrade clusteru AKS spustí Cordon a vyprázdní uzly. Pokud máte k dispozici nízkou kvótu výpočetních prostředků, upgrade může selhat. Další informace najdete v tématu [zvýšení kvót](../azure-portal/supportability/resource-manager-core-quotas-request.md) .

## <a name="check-for-available-aks-cluster-upgrades"></a>Vyhledat dostupné upgrady clusteru AKS

Chcete-li zjistit, které verze Kubernetes jsou pro váš cluster k dispozici, použijte příkaz [AZ AKS Get-Upgrades][az-aks-get-upgrades] . Následující příklad zkontroluje dostupné upgrady pro cluster s názvem *myAKSCluster* ve skupině prostředků s názvem *myResourceGroup*:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> Pokud upgradujete podporovaný cluster AKS, nelze podverze Kubernetes vynechat. Například upgrady mezi *1.12. x*  ->  *1.13. x* nebo *1.13. x*  ->  *1.14. x* jsou povoleny, ale *1.12. x*  ->  *1.14. x* není.
>
> Chcete-li provést upgrade, z *1.12. x*  ->  *1.14. x*, nejprve upgradujte z *1.12. x*  ->  *1.13. x*a potom proveďte upgrade z *1.13. x*  ->  *1.14. x*.
>
> Přeskočení více verzí lze provést pouze při upgradu z nepodporované verze zpět do podporované verze. Například upgradujte z nepodporovaného prvku *1.10. x* --> podporovanou *1.15. x* je možné dokončit.

Následující příklad výstupu ukazuje, že cluster je možné upgradovat na verze *1.13.9* a *1.13.10*:

```console
Name     ResourceGroup     MasterVersion    NodePoolVersion    Upgrades
-------  ----------------  ---------------  -----------------  ---------------
default  myResourceGroup   1.12.8           1.12.8             1.13.9, 1.13.10
```
Pokud upgrade není k dispozici, zobrazí se:
```console
ERROR: Table output unavailable. Use the --query option to specify an appropriate query. Use --debug for more info.
```

## <a name="customize-node-surge-upgrade-preview"></a>Přizpůsobení upgradu uzlu přepětí (Preview)

> [!Important]
> Pro každou operaci upgradu se u uzlů přepětí vyžaduje kvóta předplatného pro požadovaný maximální počet přepětí. Například cluster s pěti fondy uzlů, každý s počtem 4 uzly, má celkem 20 uzlů. Pokud má každý fond uzlů hodnotu maximálního nárůstu 50%, pro dokončení upgradu se vyžaduje další výpočetní a IP kvóta 10 uzlů (2 uzly * 5 fondů).
>
> Pokud používáte Azure CNI, ověřte, že jsou v podsíti dostupné IP adresy, a také [požadavky na IP adresu Azure CNI](configure-azure-cni.md).

Ve výchozím nastavení AKS konfiguruje upgrady na přepětí s jedním dalším uzlem. Výchozí hodnota jednoho pro nastavení maximálního přepětí umožňuje AKS minimalizovat narušení úlohy vytvořením dalšího uzlu předtím, než Cordon/vyprázdní stávající aplikace nahradí starší uzel se správou verzí. Hodnota maximálního přepětí může být přizpůsobená pro každý fond uzlů, aby bylo možné kompromis mezi rychlostí upgradu a přerušením upgradu. Zvýšením maximální hodnoty přepětí dojde k rychlejšímu dokončení procesu upgradu, ale nastavení vysoké hodnoty pro maximální nárůst může způsobit přerušení během procesu upgradu. 

Například maximální hodnota přepětí 100% poskytuje nejrychlejší možný proces upgradu (zdvojnásobuje počet uzlů), ale také způsobí, že všechny uzly ve fondu uzlů budou vyprázdněny současně. Možná budete chtít použít vyšší hodnotu, například pro testovací prostředí. Pro fondy produkčních uzlů doporučujeme nastavení max_surge 33%.

AKS přijímá jak celočíselné hodnoty, tak i procentuální hodnotu pro maximální nárůst. Celé číslo, jako je "5", označuje pět dalších uzlů pro přepětí. Hodnota "50%" označuje hodnotu přepětí na polovinu aktuálního počtu uzlů ve fondu. Maximální procento nárůstu hodnoty může být minimálně 1% a maximálně 100%. Procentuální hodnota se zaokrouhluje na nejbližší počet uzlů. Pokud je maximální hodnota přepětí nižší než aktuální počet uzlů v době upgradu, použije se pro maximální hodnotu přepětí aktuální počet uzlů.

Během upgradu může být maximální hodnota přepětí minimálně 1 a maximální hodnota se rovná počtu uzlů ve fondu uzlů. Můžete nastavit větší hodnoty, ale maximální počet uzlů, které se mají použít pro maximální nárůst, nebude vyšší než počet uzlů ve fondu v době upgradu.

### <a name="set-up-the-preview-feature-for-customizing-node-surge-upgrade"></a>Nastavení funkce Preview pro přizpůsobení upgradu uzlu přepětí

```azurecli-interactive
# register the preview feature
az feature register --namespace "Microsoft.ContainerService" --name "MaxSurgePreview"
```

Registrace bude trvat několik minut. Pomocí níže uvedeného příkazu ověřte, že je funkce zaregistrovaná:

```azurecli-interactive
# Verify the feature is registered:
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/MaxSurgePreview')].{Name:name,State:properties.state}"
```

Během období Preview budete potřebovat rozšíření CLI *AKS-Preview* pro použití maximálního nárůstu. Použijte příkaz [AZ Extension Add][az-extension-add] a potom vyhledejte všechny dostupné aktualizace pomocí příkazu [AZ Extension Update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

> [!Important]
> Nastavení maximálního přepětí ve fondu uzlů je trvalé.  Toto nastavení budou používat následné upgrady Kubernetes nebo upgrady verze uzlu. Maximální hodnotu nárůstu počtu fondů uzlů můžete kdykoli změnit. Pro fondy produkčních uzlů doporučujeme nastavení maximálního přepětí 33%.

Pomocí následujících příkazů nastavte maximální hodnoty přepětí pro nové nebo existující fondy uzlů.

```azurecli-interactive
# Set max surge for a new node pool
az aks nodepool add -n mynodepool -g MyResourceGroup --cluster-name MyManagedCluster --max-surge 33%
```

```azurecli-interactive
# Update max surge for an existing node pool 
az aks nodepool update -n mynodepool -g MyResourceGroup --cluster-name MyManagedCluster --max-surge 5
```

## <a name="upgrade-an-aks-cluster"></a>Upgrade clusteru AKS

Seznam dostupných verzí pro cluster AKS můžete upgradovat pomocí příkazu [AZ AKS upgrade][az-aks-upgrade] . Během procesu upgradu AKS přidá nový uzel do clusteru, na kterém je spuštěná zadaná verze Kubernetes, a pak pečlivě [Cordon a vyprázdní][kubernetes-drain] jeden ze starých uzlů, aby se minimalizovalo přerušení spouštění aplikací. Když se nový uzel potvrdí jako běžící aplikace, Starý uzel se odstraní. Tento proces se opakuje, dokud nebudou upgradovány všechny uzly v clusteru.

```azurecli-interactive
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --kubernetes-version KUBERNETES_VERSION
```

Upgrade clusteru může trvat několik minut v závislosti na tom, kolik uzlů máte.

> [!NOTE]
> Je celkově povolený čas pro dokončení upgradu clusteru. Tento čas se vypočítá pomocí produktu `10 minutes * total number of nodes in the cluster` . Například v clusteru 20 uzlů musí operace upgradu úspěšně probíhat během 200 minut nebo AKS operace, aby se předešlo neopravitelnému stavu clusteru. Chcete-li provést obnovení při selhání upgradu, opakujte operaci upgradu po vypršení časového limitu.

Pokud chcete ověřit, že upgrade proběhl úspěšně, použijte příkaz [AZ AKS show][az-aks-show] :

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

Následující příklad výstupu ukazuje, že cluster teď spouští *1.13.10*:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ---------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.13.10               Succeeded            myaksclust-myresourcegroup-19da35-90efab95.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>Další kroky

Tento článek vám ukázal, jak upgradovat existující cluster AKS. Další informace o nasazení a správě clusterů AKS najdete v tématu o sadě kurzů.

> [!div class="nextstepaction"]
> [Kurzy AKS][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[az-aks-show]: /cli/azure/aks#az-aks-show
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update

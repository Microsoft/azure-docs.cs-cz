---
title: Upgrade clusteru Azure Kubernetes Service (AKS)
description: Zjistěte, jak upgradovat cluster Azure Kubernetes Service (AKS), abyste získali nejnovější funkce a aktualizace zabezpečení.
services: container-service
ms.topic: article
ms.date: 12/17/2020
ms.openlocfilehash: 11218fc0cd754e9793067c449fdcb7589688dc2e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102176344"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Upgrade clusteru Azure Kubernetes Service (AKS)

Součástí životního cyklu clusteru AKS je provádění pravidelných upgradů na nejnovější verzi Kubernetes. Je důležité použít nejnovější verze zabezpečení nebo upgradovat, abyste získali nejnovější funkce. V tomto článku se dozvíte, jak upgradovat hlavní komponenty nebo jeden výchozí fond uzlů v clusteru AKS.

Clustery AKS, které používají více fondů uzlů nebo uzlů Windows serveru, najdete v tématu [upgrade fondu uzlů v AKS][nodepool-upgrade].

## <a name="before-you-begin"></a>Než začnete

Tento článek vyžaduje, abyste spustili Azure CLI verze 2.0.65 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

> [!WARNING]
> Upgrade clusteru AKS spustí Cordon a vyprázdní uzly. Pokud máte k dispozici nízkou kvótu výpočetních prostředků, upgrade může selhat. Další informace najdete v tématu [zvýšení kvót](../azure-portal/supportability/resource-manager-core-quotas-request.md) .

## <a name="check-for-available-aks-cluster-upgrades"></a>Vyhledat dostupné upgrady clusteru AKS

Chcete-li zjistit, které verze Kubernetes jsou pro váš cluster k dispozici, použijte příkaz [AZ AKS Get-Upgrades][az-aks-get-upgrades] . Následující příklad zkontroluje dostupné upgrady na *myAKSCluster* v *myResourceGroup*:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> Pokud upgradujete podporovaný cluster AKS, nelze podverze Kubernetes vynechat. Všechny upgrady se musí provádět postupně podle hlavního čísla verze. Například upgrady mezi *otázku 1.14. x*  ->  *1.15. x* nebo *1.15. x*  ->  *1.16. x* jsou povoleny, ale *1.14. x*  ->  *1.16. x* není povoleno. 
> > Přeskočení více verzí lze provést pouze při upgradu z _nepodporované verze_ zpět na _podporovanou verzi_. Například upgrade z nepodporovaného prvku *1.10. x* --> podporovanou *1.15. x* lze dokončit.

Následující příklad výstupu ukazuje, že cluster je možné upgradovat na verze *1.19.1* a *1.19.3*:

```console
Name     ResourceGroup    MasterVersion    Upgrades
-------  ---------------  ---------------  --------------
default  myResourceGroup  1.18.10          1.19.1, 1.19.3
```

Pokud upgrade není k dispozici, zobrazí se zpráva:

```console
ERROR: Table output unavailable. Use the --query option to specify an appropriate query. Use --debug for more info.
```

## <a name="customize-node-surge-upgrade"></a>Přizpůsobení upgradu uzlu přepětí

> [!Important]
> Pro každou operaci upgradu se u uzlů přepětí vyžaduje kvóta předplatného pro požadovaný maximální počet přepětí. Například cluster s pěti fondy uzlů, každý s počtem 4 uzly, má celkem 20 uzlů. Pokud má každý fond uzlů hodnotu maximálního nárůstu 50%, pro dokončení upgradu se vyžaduje další výpočetní a IP kvóta 10 uzlů (2 uzly * 5 fondů).
>
> Pokud používáte Azure CNI, ověřte, že jsou v podsíti dostupné IP adresy, a také [požadavky na IP adresu Azure CNI](configure-azure-cni.md).

Ve výchozím nastavení AKS konfiguruje upgrady na přepětí s jedním dalším uzlem. Výchozí hodnota jednoho pro maximální nastavení přepětí umožní AKS minimalizovat narušení úlohy vytvořením dalšího uzlu předtím, než Cordon/vyprázdní stávající aplikace nahradí starší uzel s verzí. Hodnota maximálního přepětí může být přizpůsobená pro každý fond uzlů, aby bylo možné kompromis mezi rychlostí upgradu a přerušením upgradu. Zvýšením maximální hodnoty přepětí dojde k rychlejšímu dokončení procesu upgradu, ale nastavení vysoké hodnoty pro maximální nárůst může způsobit přerušení během procesu upgradu. 

Například maximální hodnota přepětí 100% poskytuje nejrychlejší možný proces upgradu (zdvojnásobuje počet uzlů), ale také způsobí, že všechny uzly ve fondu uzlů budou vyprázdněny současně. Možná budete chtít použít vyšší hodnotu, například pro testovací prostředí. Pro fondy produkčních uzlů doporučujeme nastavení max_surge 33%.

AKS přijímá jak celočíselné hodnoty, tak i procentuální hodnotu pro maximální nárůst. Celé číslo, jako je "5", označuje pět dalších uzlů pro přepětí. Hodnota "50%" označuje hodnotu přepětí na polovinu aktuálního počtu uzlů ve fondu. Maximální procento nárůstu hodnoty může být minimálně 1% a maximálně 100%. Procentuální hodnota se zaokrouhluje na nejbližší počet uzlů. Pokud je maximální hodnota přepětí nižší než aktuální počet uzlů v době upgradu, použije se pro maximální hodnotu přepětí aktuální počet uzlů.

Během upgradu může být maximální hodnota přepětí minimálně 1 a maximální hodnota se rovná počtu uzlů ve fondu uzlů. Můžete nastavit větší hodnoty, ale maximální počet uzlů, které se mají použít pro maximální nárůst, nebude vyšší než počet uzlů ve fondu v době upgradu.

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

Seznam dostupných verzí pro cluster AKS můžete upgradovat pomocí příkazu [AZ AKS upgrade][az-aks-upgrade] . Během procesu upgradu bude AKS: 
- Přidejte nový uzel vyrovnávací paměti (nebo tolik uzlů, jak jsou nakonfigurované v [Max. nárůstu](#customize-node-surge-upgrade)) do clusteru, na kterém je spuštěná zadaná verze Kubernetes. 
- [Cordon a vyprázdní][kubernetes-drain] jeden ze starých uzlů, aby se minimalizovalo přerušení spouštění aplikací (Pokud používáte max. nárůst, [Cordon a vyprázdní][kubernetes-drain] tolik uzlů ve stejnou dobu jako počet zadaných uzlů vyrovnávací paměti). 
- Když je starý uzel úplně vyprázdněný, obnoví se jeho image, aby se získala Nová verze, a ta se stane uzlem vyrovnávací paměti pro upgrade následujícího uzlu. 
- Tento proces se opakuje, dokud nebudou upgradovány všechny uzly v clusteru. 
- Na konci procesu se odstraní poslední uzel vyrovnávací paměti, čímž se zachová existující počet uzlů agenta a zůstatek v zóně.

```azurecli-interactive
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --kubernetes-version KUBERNETES_VERSION
```

Upgrade clusteru může trvat několik minut v závislosti na tom, kolik uzlů máte.

> [!IMPORTANT]
> Zajistěte, aby všechny `PodDisruptionBudgets` (soubory PDB) umožňovaly přesun alespoň 1 pod replikou v čase, v opačném případě se operace vyprázdnění/vyřazení nezdaří.
> Pokud operace vyprázdnění selže, operace upgradu neprovede návrh, aby se zajistilo, že nedojde k přerušení aplikací. Opravte prosím, co způsobilo zastavení operace (nesprávné soubory PDB, nedostatek kvóty atd.), a opakujte operaci znovu.

Pokud chcete ověřit, že upgrade proběhl úspěšně, použijte příkaz [AZ AKS show][az-aks-show] :

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

Následující příklad výstupu ukazuje, že cluster teď spouští *1.18.10*:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.18.10              Succeeded            myakscluster-dns-379cbbb9.hcp.eastus.azmk8s.io
```

## <a name="set-auto-upgrade-channel"></a>Nastavení kanálu automatického upgradu

Kromě ručního upgradu clusteru můžete na svém clusteru nastavit kanál automatického upgradu. K dispozici jsou následující kanály upgradu:

|Kanál| Akce | Příklad
|---|---|---|
| `none`| zakáže automatické upgrady a zachová cluster v aktuální verzi Kubernetes.| Výchozí nastavení, pokud zůstane beze změny|
| `patch`| automaticky upgradovat cluster na nejnovější podporovanou verzi opravy, jakmile bude k dispozici, a přitom zachovat dílčí verzi.| Pokud je například v clusteru spuštěná verze *1.17.7* a verze *1.17.9*, *1.18.4*, *1.18.6* a *1.19.1* , váš cluster se upgraduje na *1.17.9*|
| `stable`| automaticky upgradujte cluster na nejnovější podporovanou verzi opravy v podverzi *n-1*, kde *n* je nejnovější podporovaná dílčí verze.| Pokud je například v clusteru spuštěná verze *1.17.7* a verze *1.17.9*, *1.18.4*, *1.18.6* a *1.19.1* , váš cluster se upgraduje na *1.18.6*.
| `rapid`| automaticky upgradovat cluster na nejnovější podporovanou verzi opravy na nejnovější verzi, která je k dispozici.| V případech, kdy je cluster ve verzi Kubernetes, která je v podverzi *n-2* , kde *n* je nejnovější podporovaná dílčí verze, cluster nejprve upgraduje na nejnovější podporovanou verzi opravy v *N-1* dílčí verzi. Pokud je například v clusteru spuštěná verze *1.17.7* a verze *1.17.9*, *1.18.4*, *1.18.6* a *1.19.1* , je váš cluster nejdřív upgradován na *1.18.6* a pak se upgraduje na *1.19.1*.

> [!NOTE]
> Automatický upgrade clusteru se aktualizuje jenom na verze GA Kubernetes a nebude se aktualizovat na verze Preview.

Automatické upgradování clusteru probíhá stejným způsobem jako ruční upgrade clusteru. Další podrobnosti najdete v tématu [upgrade clusteru AKS][upgrade-cluster].

Automatický upgrade clusteru pro clustery AKS je funkce ve verzi Preview.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Příznak funkce Zaregistrujte `AutoUpgradePreview` pomocí příkazu [AZ Feature Register][az-feature-register] , jak je znázorněno v následujícím příkladu:

```azurecli-interactive
az feature register --namespace Microsoft.ContainerService -n AutoUpgradePreview
```

Zobrazení stavu v *registraci* trvá několik minut. Pomocí příkazu [AZ Feature list][az-feature-list] ověřte stav registrace:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AutoUpgradePreview')].{Name:name,State:properties.state}"
```

Až budete připraveni, aktualizujte registraci poskytovatele prostředků *Microsoft. ContainerService* pomocí příkazu [AZ Provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

K nastavení kanálu automatického upgradu při vytváření clusteru použijte parametr pro *automatické upgrady* , podobně jako v následujícím příkladu.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --auto-upgrade-channel stable --generate-ssh-keys
```

Pokud chcete nastavit kanál automatického upgradu na existujícím clusteru, aktualizujte parametr *automatického upgradu na kanál* podobný následujícímu příkladu.

```azurecli-interactive
az aks update --resource-group myResourceGroup --name myAKSCluster --auto-upgrade-channel stable
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
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-provider-register]: /cli/azure/provider#az-provider-register
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[upgrade-cluster]:  #upgrade-an-aks-cluster

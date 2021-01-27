---
title: Použití vyhrazeného fondu ke spuštění úlohy – úkoly
description: Pokud chcete spustit úlohu Azure Container Registry, nastavte v registru vyhrazený fond výpočetních prostředků (fond agentů).
ms.topic: article
ms.date: 10/12/2020
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: eeb9a71854f52da5c1a9f4befae93c377ad67b05
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920303"
---
# <a name="run-an-acr-task-on-a-dedicated-agent-pool"></a>Spuštění úlohy ACR ve vyhrazeném fondu agentů

Nastavte fond virtuálních počítačů spravovaných Azure (*fond agentů*), aby bylo možné spouštět [úlohy Azure Container Registry][acr-tasks] ve vyhrazeném výpočetním prostředí. Po nakonfigurování jednoho nebo více fondů v registru můžete zvolit fond a spustit úlohu místo výchozího výpočetního prostředí služby.

Fond agentů poskytuje:

- **Podpora virtuální sítě** – přiřaďte fond agentů k virtuální síti Azure a poskytněte přístup k prostředkům ve virtuální síti, jako je například registr kontejnerů, Trezor klíčů nebo úložiště.
- **Škálujte podle potřeby** – zvyšte počet instancí ve fondu agentů pro úlohy náročné na výpočetní výkon nebo nastavte škálování na nulu. Fakturace vychází z přidělení fondu. Podrobnosti najdete v tématu [ceny](https://azure.microsoft.com/pricing/details/container-registry/).
- **Flexibilní možnosti** – vyberte si z různých [vrstev fondů](#pool-tiers) a možností škálování, abyste splnili požadavky na úlohy úloh.
- **Správa Azure** – fondy úkolů jsou opravené a udržované v Azure a poskytují rezervované přidělení bez nutnosti udržovat jednotlivé virtuální počítače.

Tato funkce je k dispozici na úrovni služby Registry kontejneru **Premium** . Informace o úrovních a omezeních služby registru najdete v tématu [Azure Container Registry SKU][acr-tiers].

> [!IMPORTANT]
> Tato funkce je aktuálně ve verzi Preview a [platí některá omezení](#preview-limitations). Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití][terms-of-use]. Některé aspekty této funkce se můžou před zveřejněním změnit.
>

## <a name="preview-limitations"></a>Omezení verze Preview

- Fondy agenta úloh aktuálně podporují uzly Linux. Uzly systému Windows nejsou aktuálně podporovány.
- Fondy agentů úloh jsou dostupné ve verzi Preview v těchto oblastech: Západní USA 2, Střed USA – jih, Východní USA 2, Východní USA, Střed USA, USGov Arizona, USGov Texas a USGov) – Virginia.
- Pro každý registr je výchozí celková kvóta vCPU (jádro) 16 pro všechny standardní fondy agentů a pro izolované fondy agentů je 0. Otevřete [žádost o podporu][open-support-ticket] pro další přidělení.
- V tuto chvíli nemůžete zrušit běh úlohy ve fondu agentů.

## <a name="prerequisites"></a>Požadavky

* K používání kroků Azure CLI v tomto článku se vyžaduje Azure CLI verze 2.3.1 nebo novější. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli]. Nebo spusťte v [Azure Cloud Shell](../cloud-shell/quickstart.md).
* Pokud ještě nemáte registr kontejnerů, vytvořte si [ho][create-reg-cli] (je potřeba Premium úrovně) v oblasti verze Preview.

## <a name="pool-tiers"></a>Úrovně fondů

Úrovně fondu agentů poskytují následující prostředky na instanci ve fondu.

|Úroveň    | Typ  |  Procesor  |Paměť (GB)  |
|---------|---------|---------|---------|
|S1     |  standardní    | 2       |    3     |
|S2     |  standardní    | 4       |    8     |
|S3     |  standardní    | 8       |   16     |
|I6     |  izolován    | 64     |   216     |


## <a name="create-and-manage-a-task-agent-pool"></a>Vytvoření a Správa fondu agenta úloh

### <a name="set-default-registry-optional"></a>Nastavit výchozí registr (volitelné)

Pokud chcete zjednodušit příkazy rozhraní příkazového řádku Azure, nastavte výchozí registr spuštěním příkazu [AZ Configure][az-configure] :

```azurecli
az configure --defaults acr=<registryName>
```

V následujících příkladech se předpokládá, že jste nastavili výchozí registr. V takovém případě předejte `--registry <registryName>` parametr v každém `az acr` příkazu.

### <a name="create-agent-pool"></a>Vytvořit fond agentů

Vytvořte fond agentů pomocí příkazu [AZ ACR neznámá Create][az-acr-agentpool-create] . Následující příklad vytvoří fond vrstvy S2 (4 CPU/instance). Ve výchozím nastavení fond obsahuje jednu instanci.

```azurecli
az acr agentpool create \
    --name myagentpool \
    --tier S2
```

> [!NOTE]
> Vytvoření fondu agentů a dalších operací správy fondů trvá několik minut, než se dokončí.

### <a name="scale-pool"></a>Škálování fondu

Horizontální navýšení nebo snížení kapacity velikosti fondu pomocí příkazu [AZ ACR neznámá Update][az-acr-agentpool-update] Následující příklad škáluje fond na 2 instance. Můžete škálovat na 0 instancí.

```azurecli
az acr agentpool update \
    --name myagentpool \
    --count 2
```

## <a name="create-pool-in-a-virtual-network"></a>Vytvoření fondu ve virtuální síti

### <a name="add-firewall-rules"></a>Přidat pravidla brány firewall

Fondy agentů úloh vyžadují přístup k následujícím službám Azure. Následující pravidla brány firewall musí být přidána do stávajících skupin zabezpečení sítě nebo tras definovaných uživatelem.

| Směr | Protokol | Zdroj         | Zdrojový port | Cíl          | Cílový port | Použito    |
|-----------|----------|----------------|-------------|----------------------|-----------|---------|
| Odchozí  | TCP      | VirtualNetwork | Libovolný         | AzureKeyVault        | 443       | Výchozí |
| Odchozí  | TCP      | VirtualNetwork | Libovolný         | Storage              | 443       | Výchozí |
| Odchozí  | TCP      | VirtualNetwork | Libovolný         | Centrum událostí             | 443       | Výchozí |
| Odchozí  | TCP      | VirtualNetwork | Libovolný         | Azureactivedirectory selhala | 443       | Výchozí |
| Odchozí  | TCP      | VirtualNetwork | Libovolný         | AzureMonitor         | 443       | Výchozí |

> [!NOTE]
> Pokud vaše úkoly vyžadují další prostředky z veřejného Internetu, přidejte odpovídající pravidla. Například další pravidla jsou nutná ke spuštění úlohy sestavení Docker, která načte základní image z Docker Hub nebo obnoví balíček NuGet.

### <a name="create-pool-in-vnet"></a>Vytvořit fond ve virtuální síti

Následující příklad vytvoří fond agentů v *mysubnet* podsíti sítě *myvnet*:

```azurecli
# Get the subnet ID
subnetId=$(az network vnet subnet show \
        --resource-grop myresourcegroup \
        --vnet-name myvnet \
        --name mysubnetname \
        --query id --output tsv)

az acr agentpool create \
    --name myagentpool \
    --tier S2 \
    --subnet-id $subnetId
```

## <a name="run-task-on-agent-pool"></a>Spustit úlohu ve fondu agentů

Následující příklady ukazují, jak zadat fond agentů při zařazování úlohy do fronty.

> [!NOTE]
> Pokud chcete použít fond agentů v úloze ACR, ujistěte se, že fond obsahuje alespoň jednu instanci.
>

### <a name="quick-task"></a>Rychlý úkol

Zařadí do fronty rychlý úkol ve fondu agentů pomocí příkazu [AZ ACR Build][az-acr-build] a předejte `--agent-pool` parametr:

```azurecli
az acr build \
    --agent-pool myagentpool \
    --image myimage:mytag \
    --file Dockerfile \
    https://github.com/Azure-Samples/acr-build-helloworld-node.git#main
```

### <a name="automatically-triggered-task"></a>Automaticky aktivovaný úkol

Například vytvořte naplánovanou úlohu ve fondu agentů pomocí [AZ ACR Task Create][az-acr-task-create]a předáním `--agent-pool` parametru.

```azurecli
az acr task create \
    --name mytask \
    --agent-pool myagentpool \
    --image myimage:mytag \
    --schedule "0 21 * * *" \
    --file Dockerfile \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git#main \
    --commit-trigger-enabled false
```

Pokud chcete ověřit nastavení úlohy, spusťte příkaz [AZ ACR Task Run][az-acr-task-run]:

```azurecli
az acr task run \
    --name mytask
```

### <a name="query-pool-status"></a>Stav fondu dotazů

Pokud chcete zjistit počet aktuálně naplánovaných běhů fondu agentů, spusťte příkaz [AZ ACR neznámá show][az-acr-agentpool-show].

```azurecli
az acr agentpool show \
    --name myagentpool \
    --queue-count
```

## <a name="next-steps"></a>Další kroky

Další příklady sestavení a údržby imagí kontejneru v cloudu najdete v [řadě kurzů pro úlohy ACR](container-registry-tutorial-quick-task.md).



[acr-tasks]:           container-registry-tasks-overview.md
[acr-tiers]:           container-registry-skus.md
[azure-cli]:           /cli/azure/install-azure-cli
[open-support-ticket]: https://aka.ms/acr/support/create-ticket
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[az-configure]: /cli/azure#az-configure
[az-acr-agentpool-create]: /cli/azure/acr/agentpool#az-acr-agentpool-create
[az-acr-agentpool-update]: /cli/azure/acr/agentpool#az-acr-agentpool-update
[az-acr-agentpool-show]: /cli/azure/acr/agentpool#az-acr-agentpool-show
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[create-reg-cli]: container-registry-get-started-azure-cli.md

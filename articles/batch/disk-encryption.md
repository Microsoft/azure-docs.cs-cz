---
title: Vytvoření fondu s povoleným šifrováním disků
description: Naučte se používat konfiguraci šifrování disku k šifrování uzlů pomocí klíče spravovaného platformou.
author: pkshultz
ms.topic: how-to
ms.date: 01/27/2021
ms.author: peshultz
ms.custom: references_regions
ms.openlocfilehash: 41fc827459b454e2bcb120a925cdab8fcd46e310
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99055310"
---
# <a name="create-a-pool-with-disk-encryption-enabled"></a>Vytvoření fondu s povoleným šifrováním disků

Když vytváříte fond Azure Batch s použitím [Konfigurace virtuálního počítače](nodes-and-pools.md#virtual-machine-configuration), můžete zašifrovat výpočetní uzly ve fondu pomocí klíče spravovaného platformou zadáním konfigurace šifrování disku.

Tento článek vysvětluje, jak vytvořit fond Batch s povoleným šifrováním disku.

> [!IMPORTANT]
> Podpora šifrování u hostitele pomocí klíče spravovaného platformou v Azure Batch je v současnosti ve verzi Public Preview pro oblasti Východní USA, Západní USA 2, Střed USA – jih, US Gov – Virginie a US Gov – Arizona.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="why-use-a-pool-with-disk-encryption-configuration"></a>Proč používat fond s konfigurací šifrování disku?

Pomocí fondu Batch můžete získat přístup k datům a jejich uložení v operačním systému a na dočasných discích výpočetního uzlu. Šifrování disku na straně serveru pomocí klíče spravovaného platformou bude chránit tato data s nízkou režií a pohodlíí.

Batch použije jednu z těchto technologií pro šifrování disků na výpočetních uzlech na základě konfigurace fondu a regionální podpory.

- [Spravované šifrování disků v klidovém umístění pomocí klíčů spravovaných platformou](../virtual-machines/disk-encryption.md#platform-managed-keys)
- [Šifrování na hostiteli pomocí klíče spravovaného platformou](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)
- [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md)

Nebudete moci určit, která metoda šifrování bude použita pro uzly ve vašem fondu. Místo toho zadáte cílové disky, které chcete šifrovat na svých uzlech, a v dávce si můžete vybrat vhodnou metodu šifrování, která zajistí, že zadané disky budou na výpočetním uzlu šifrované.

> [!IMPORTANT]
> Pokud vytváříte fond s [vlastní imagí](batch-sig-images.md), můžete povolit šifrování disku pouze v případě, že používáte virtuální počítače s Windows.

## <a name="azure-portal"></a>portál Azure

Při vytváření fondu Batch v Azure Portal vyberte v části **Konfigurace šifrování disku** buď **TemporaryDisk** nebo **OsAndTemporaryDisk** .

:::image type="content" source="media/disk-encryption/portal-view.png" alt-text="Snímek obrazovky s možností konfigurace šifrování disku v Azure Portal.":::

Po vytvoření fondu se můžete podívat na cíle konfigurace šifrování disků v oddílu **vlastnosti** fondu.

:::image type="content" source="media/disk-encryption/configuration-target.png" alt-text="Snímek obrazovky znázorňující cíle konfigurace šifrování disku v Azure Portal.":::

## <a name="examples"></a>Příklady

Následující příklady ukazují, jak šifrovat operační systém a dočasné disky ve fondu služby Batch pomocí sady Batch .NET SDK, REST API služby Batch a rozhraní příkazového řádku Azure CLI.

### <a name="batch-net-sdk"></a>Batch .NET SDK

```csharp
pool.VirtualMachineConfiguration.DiskEncryptionConfiguration = new DiskEncryptionConfiguration(
    targets: new List<DiskEncryptionTarget> { DiskEncryptionTarget.OsDisk, DiskEncryptionTarget.TemporaryDisk }
    );
```

### <a name="batch-rest-api"></a>REST API dávky

ADRESA URL REST API:

```
POST {batchURL}/pools?api-version=2020-03-01.11.0
client-request-id: 00000000-0000-0000-0000-000000000000
```

Text požadavku:

```
"pool": {
    "id": "pool2",
    "vmSize": "standard_a1",
    "virtualMachineConfiguration": {
        "imageReference": {
            "publisher": "Canonical",
            "offer": "UbuntuServer",
            "sku": "18.04-LTS"
        },
        "diskEncryptionConfiguration": {
            "targets": [
                "OsDisk",
                "TemporaryDisk"
            ]
        }
        "nodeAgentSKUId": "batch.node.ubuntu 18.04"
    },
    "resizeTimeout": "PT15M",
    "targetDedicatedNodes": 5,
    "targetLowPriorityNodes": 0,
    "taskSlotsPerNode": 3,
    "enableAutoScale": false,
    "enableInterNodeCommunication": false
}
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az batch pool create \
    --id diskencryptionPool \
    --vm-size Standard_DS1_V2 \
    --target-dedicated-nodes 2 \
    --image canonical:ubuntuserver:18.04-LTS \
    --node-agent-sku-id "batch.node.ubuntu 18.04" \
    --disk-encryption-targets OsDisk TemporaryDisk
```

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [šifrování Azure Disk Storage na straně serveru](../virtual-machines/disk-encryption.md).
- Podrobný přehled služby Batch najdete v tématu [pracovní postup služby Batch a prostředky](batch-service-workflow-features.md).

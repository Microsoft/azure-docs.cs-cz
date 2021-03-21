---
title: Vytvoření fondu napříč zónami dostupnosti
description: Přečtěte si, jak vytvořit fond Batch pomocí zásad pro oblast, která vám umožní chránit před chybami.
ms.topic: how-to
ms.date: 01/28/2021
ms.openlocfilehash: 56e718bedf504b8e69598c2d99ab8b889a470b89
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101725284"
---
# <a name="create-an-azure-batch-pool-across-availability-zones"></a>Vytvoření fondu Azure Batch napříč Zóny dostupnosti

Oblasti Azure, které podporují [zóny dostupnosti](https://azure.microsoft.com/global-infrastructure/availability-zones/) mají minimálně tři samostatné zóny, z nichž každý má vlastní nezávislý zdroj napájení, síť a chladicí systém. Když vytváříte fond Azure Batch s použitím konfigurace virtuálního počítače, můžete se rozhodnout zřídit fond Batch napříč Zóny dostupnosti. Vytvoření fondu pomocí této zásady oblasti vám pomůže ochránit výpočetní uzly Batch před selháními na úrovni datacentra Azure.

Můžete například vytvořit fond se zásadami oblastí v oblasti Azure, která podporuje tři Zóny dostupnosti. Pokud v datacentru Azure v jedné zóně dostupnosti dojde k selhání infrastruktury, bude mít fond dávek stále v pořádku uzly v dalších dvou Zóny dostupnosti, takže fond zůstane k dispozici pro plánování úloh.

## <a name="regional-support-and-other-requirements"></a>Regionální podpora a jiné požadavky

Batch udržuje v Azure paritu na podpoře Zóny dostupnosti. Pokud chcete použít možnost oblast, musíte fond vytvořit v [podporované oblasti Azure](../availability-zones/az-region.md).

Aby bylo možné fond dávek přidělit přes zóny dostupnosti, musí oblast Azure, ve které je fond vytvořen, podporovat požadovanou SKLADOVOU položku virtuálního počítače ve více než jedné zóně. Můžete to ověřit voláním [rozhraní API seznam SKU prostředků](/rest/api/compute/resourceskus/list) a zkontrolováním pole **LocationInfo** v [resourceSku](/rest/api/compute/resourceskus/list#resourcesku). Ujistěte se, že pro požadovanou SKU virtuálního počítače je podporována více než jedna zóna.

Pro [účty Batch v režimu předplatného uživatele](accounts.md#batch-accounts)se ujistěte, že předplatné, ve kterém vytváříte fond, nemá omezení nabídky na požadované SKU virtuálního počítače. Potvrďte to tak, že zavoláte [rozhraní API seznamu SKU prostředků](/rest/api/compute/resourceskus/list) a zkontrolujete [ResourceSkuRestrictions](/rest/api/compute/resourceskus/list#resourceskurestrictions). Pokud existuje omezení zóny, můžete odeslat [lístek podpory](/troubleshoot/azure/general/region-access-request-process) pro odebrání omezení zóny.

Všimněte si také, že nemůžete vytvořit fond se zásadami pro oblast, pokud má povolenou komunikaci mezi uzly a používá [SKU virtuálního počítače, který podporuje InfiniBand](../virtual-machines/workloads/hpc/enable-infiniband.md).

## <a name="create-a-batch-pool-across-availability-zones"></a>Vytvoření fondu Batch napříč Zóny dostupnosti

Následující příklady ukazují, jak vytvořit fond dávek v rámci Zóny dostupnosti.

> [!NOTE]
> Při vytváření fondu pomocí zásad Zona se služba Batch pokusí přidělit fond ve všech Zóny dostupnosti ve vybrané oblasti. v rámci zón nemůžete určit konkrétní přidělení.

### <a name="batch-management-client-net-sdk"></a>Sada .NET SDK pro klienta Batch Management

```csharp
pool.DeploymentConfiguration.VirtualMachineConfiguration.NodePlacementConfiguration = new NodePlacementConfiguration()
    {
        Policy = NodePlacementPolicyType.Zonal
    };

```

### <a name="batch-rest-api"></a>REST API dávky

ADRESA URL REST API

```
POST {batchURL}/pools?api-version=2021-01-01.13.0
client-request-id: 00000000-0000-0000-0000-000000000000
```

Text požadavku

```
"pool": {
    "id": "pool2",
    "vmSize": "standard_a1",
    "virtualMachineConfiguration": {
        "imageReference": {
            "publisher": "Canonical",
            "offer": "UbuntuServer",
            "sku": "16.040-LTS"
        },
        "nodePlacementConfiguration": {
            "policy": "Zonal"
        }
        "nodeAgentSKUId": "batch.node.ubuntu 16.04"
    },
    "resizeTimeout": "PT15M",
    "targetDedicatedNodes": 5,
    "targetLowPriorityNodes": 0,
    "maxTasksPerNode": 3,
    "enableAutoScale": false,
    "enableInterNodeCommunication": false
}
```

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [pracovních postupech služby Batch a primárních prostředcích](batch-service-workflow-features.md) , jako jsou fondy, uzly, úlohy a úkoly.
- Seznamte se [s vytvářením fondů v podsíti virtuální sítě Azure](batch-virtual-network.md).
- Přečtěte si o [vytváření Azure Batch fondu bez veřejných IP adres](./batch-pool-no-public-ip-address.md).
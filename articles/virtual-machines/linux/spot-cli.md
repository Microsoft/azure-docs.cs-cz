---
title: Použití rozhraní příkazového řádku k nasazení služby Azure bodový Virtual Machines
description: Naučte se používat rozhraní příkazového řádku k nasazení Virtual Machines Azure na místě, abyste ušetřili náklady.
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 06/26/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 0a7be682f921efdfae486e8f6545758964a941ae
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102098855"
---
# <a name="deploy-azure-spot-virtual-machines-using-the-azure-cli"></a>Nasazení Azure spot Virtual Machines pomocí Azure CLI

Použití [Azure Spot Virtual Machines](../spot-vms.md) umožňuje využít výhod naší nevyužité kapacity s významnou úsporou nákladů. V jakémkoli okamžiku, kdy Azure potřebuje kapacitu zpátky, vyřadí infrastruktura Azure místo Virtual Machines Azure. Proto jsou Azure spot Virtual Machines Skvělé pro úlohy, které mohou zpracovávat přerušení, jako jsou úlohy dávkového zpracování, vývojové a testovací prostředí, velké výpočetní úlohy a další.

Ceny za Azure na místě Virtual Machines jsou proměnné na základě oblastí a SKU. Další informace najdete v tématu ceny virtuálních počítačů pro [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) a [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). 

Máte možnost nastavit maximální cenu, kterou jste ochotni zaplatit za hodinu pro virtuální počítač. Maximální cena pro virtuální počítač se službou Azure se dá nastavit v amerických dolarech (USD), a to s využitím až 5 desetinných míst. Hodnota by měla být například `0.98765` maximální cena $0,98765 USD za hodinu. Pokud nastavíte maximální cenu `-1` , nebude se virtuální počítač vyřadit podle ceny. Cena za virtuální počítač bude aktuální cena za virtuální počítač se službou Azure nebo cenu standardního virtuálního počítače, který je někdy menší, pokud je dostupná kapacita a kvóta. Další informace o nastavení maximální ceny najdete v tématu [Azure Spot Virtual Machines – ceny](../spot-vms.md#pricing).

Postup vytvoření virtuálního počítače se službou Azure na místě pomocí rozhraní příkazového řádku Azure je stejný, jak je uvedeno v [článku rychlý Start](./quick-create-cli.md). Stačí přidat parametr--priority – nastavte na `--eviction-policy` buď zrušit přidělení (Toto je výchozí nastavení) `Delete` , nebo zadejte Max Price nebo `-1` . 


## <a name="install-azure-cli"></a>Instalace rozhraní příkazového řádku Azure CLI

Pokud chcete vytvořit Virtual Machines Azure, musíte mít spuštěnou verzi Azure CLI 2.0.74 nebo novější. Verzi zjistíte spuštěním příkazu **az --version**. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli). 

Přihlaste se k Azure pomocí [AZ Login](/cli/azure/reference-index#az-login).

```azurecli
az login
```

## <a name="create-an-azure-spot-virtual-machine"></a>Vytvoření virtuálního počítače s bodem Azure

Tento příklad ukazuje, jak nasadit virtuální počítač se systémem Linux na platformě Linux, který nebude vyřazení na základě ceny. Zásada vyřazení je nastavená tak, aby se virtuální počítač nastavil jako neplatný, aby se mohl později restartovat. Pokud chcete odstranit virtuální počítač a příslušný disk, když je virtuální počítač vyřazený, nastavte `--eviction-policy` na `Delete` .

```azurecli
az group create -n mySpotGroup -l eastus
az vm create \
    --resource-group mySpotGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1 \
    --eviction-policy Deallocate
```



Po vytvoření virtuálního počítače se můžete dotazovat, aby se zobrazila maximální fakturovaná cena za všechny virtuální počítače ve skupině prostředků.

```azurecli
az vm list \
   -g mySpotGroup \
   --query '[].{Name:name, MaxPrice:billingProfile.maxPrice}' \
   --output table
```

## <a name="simulate-an-eviction"></a>Simulace vyřazení

Můžete [simulovat vyřazení](/rest/api/compute/virtualmachines/simulateeviction) virtuálních počítačů se systémem Azure na místě, abyste mohli otestovat, jak dobře bude vaše aplikace schopná vyřadit do náhlého vyřazení. 

Pro vaše informace nahraďte následující údaje: 

- `subscriptionId`
- `resourceGroupName`
- `vmName`


```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/simulateEviction?api-version=2020-06-01
```
`Response Code: 204` znamená, že simulované vyřazení bylo úspěšné. 

**Další kroky**

Můžete také vytvořit virtuální počítač se službou Azure na místě pomocí [Azure PowerShell](../windows/spot-powershell.md), [portálu](../spot-portal.md)nebo [šablony](spot-template.md).

Dotaz na aktuální informace o cenách pomocí [API maloobchodních cen Azure](/rest/api/cost-management/retail-prices/azure-retail-prices) pro informace o virtuálním počítači Azure na místě `meterName`A `skuName` bude obsahovat oba `Spot` .

Pokud dojde k chybě, přečtěte si [kódy chyb](../error-codes-spot.md).

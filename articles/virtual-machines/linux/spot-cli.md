---
title: Použití rozhraní příkazového řádku k nasazení služby Azure bodový Virtual Machines
description: Naučte se používat rozhraní příkazového řádku k nasazení Virtual Machines Azure na místě, abyste ušetřili náklady.
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 03/22/2021
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 8e8bdaa7a812d8c7accfea59b58b75a58d50e21e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789604"
---
# <a name="deploy-azure-spot-virtual-machines-using-the-azure-cli"></a>Nasazení Azure spot Virtual Machines pomocí Azure CLI

Použití [Azure Spot Virtual Machines](../spot-vms.md) umožňuje využít výhod naší nevyužité kapacity s významnou úsporou nákladů. V jakémkoli okamžiku, kdy Azure potřebuje kapacitu zpátky, vyřadí infrastruktura Azure místo Virtual Machines Azure. Proto jsou Azure spot Virtual Machines Skvělé pro úlohy, které mohou zpracovávat přerušení, jako jsou úlohy dávkového zpracování, vývojové a testovací prostředí, velké výpočetní úlohy a další.

Ceny za Azure na místě Virtual Machines jsou proměnné na základě oblastí a SKU. Další informace najdete v tématu ceny virtuálních počítačů pro [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) a [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). 

Máte možnost nastavit maximální cenu, kterou jste ochotni zaplatit za hodinu pro virtuální počítač. Maximální cena pro virtuální počítač se službou Azure se dá nastavit v amerických dolarech (USD), a to s využitím až 5 desetinných míst. Hodnota by měla být například `0.98765` maximální cena $0,98765 USD za hodinu. Pokud nastavíte maximální cenu `-1` , nebude se virtuální počítač vyřadit podle ceny. Cena za virtuální počítač bude aktuální cena za virtuální počítač se službou Azure nebo cenu standardního virtuálního počítače, který je někdy menší, pokud je dostupná kapacita a kvóta. Další informace o nastavení maximální ceny najdete v tématu [Azure Spot Virtual Machines – ceny](../spot-vms.md#pricing).

Postup vytvoření virtuálního počítače se službou Azure na místě pomocí rozhraní příkazového řádku Azure je stejný, jak je uvedeno v [článku rychlý Start](./quick-create-cli.md). Stačí přidat parametr--priority – nastavte na `--eviction-policy` buď zrušit přidělení (Toto je výchozí nastavení) `Delete` , nebo zadejte Max Price nebo `-1` . 


## <a name="install-azure-cli"></a>Instalace rozhraní příkazového řádku Azure CLI

Pokud chcete vytvořit Virtual Machines Azure, musíte mít spuštěnou verzi Azure CLI 2.0.74 nebo novější. Verzi zjistíte spuštěním příkazu **az --version**. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli). 

Přihlaste se k Azure pomocí [AZ Login](/cli/azure/reference-index#az_login).

```azurecli-interactive
az login
```

## <a name="create-an-azure-spot-virtual-machine"></a>Vytvoření virtuálního počítače s bodem Azure

Tento příklad ukazuje, jak nasadit virtuální počítač se systémem Linux na platformě Linux, který nebude vyřazení na základě ceny. Zásada vyřazení je nastavená tak, aby se virtuální počítač nastavil jako neplatný, aby se mohl později restartovat. Pokud chcete odstranit virtuální počítač a příslušný disk, když je virtuální počítač vyřazený, nastavte `--eviction-policy` na `Delete` .

```azurecli-interactive
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

```azurecli-interactive
az vm list \
   -g mySpotGroup \
   --query '[].{Name:name, MaxPrice:billingProfile.maxPrice}' \
   --output table
```

## <a name="simulate-an-eviction"></a>Simulace vyřazení

Můžete simulovat vyřazení virtuálních počítačů se systémem Azure pomocí REST, PowerShellu nebo rozhraní příkazového řádku a otestovat tak, jak dobře bude aplikace reagovat na náhlé vyřazení.

Ve většině případů budete chtít použít REST API [Virtual Machines-simulovat vyřazení](/rest/api/compute/virtualmachines/simulateeviction) , které vám pomůže s automatizovaným testováním aplikací. Pro REST `Response Code: 204` to znamená, že simulované vyřazení bylo úspěšné. Simulované vyřazení můžete kombinovat s [naplánovanou službou Event Service](scheduled-events.md), abyste mohli automatizovat, jak bude vaše aplikace reagovat po vyřazení virtuálního počítače.

Pokud chcete zobrazit naplánované události v akci, podívejte se na [Azure pátek – s využitím azure Scheduled Events pro přípravu údržby virtuálních počítačů](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance).


### <a name="quick-test"></a>Rychlý test

Pro rychlý test k zobrazení, jak bude simulovaná vyřazení fungovat, si projdeme dotazování naplánované služby událostí, abyste viděli, co vypadá při simulaci vyřazení pomocí Azure CLI.

Naplánovaná služba událostí je pro vaši službu povolená, když poprvé vytvoříte žádost o události. 

Vzdáleně na svůj virtuální počítač a otevřete příkazový řádek. 

Do příkazového řádku na svém VIRTUÁLNÍm počítači zadejte:

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

Tato první odpověď může trvat až 2 minuty. Od této chvíle by se měly zobrazit výstup téměř okamžitě.

Z počítače, ve kterém je nainstalované rozhraní příkazového řádku Azure (jako je váš místní počítač), simulujte vyřazení pomocí [AZ VM simulace-vyřazení](https://docs.microsoft.com/cli/azure/vm#az_vm_simulate_eviction). Název skupiny prostředků a název virtuálního počítače nahraďte vlastními. 

```azurecli-interactive
az vm simulate-eviction --resource-group mySpotRG --name mySpot
```

Výstup odpovědi bude v `Status: Succeeded` případě úspěšného provedení žádosti.

Rychle se vraťte ke vzdálenému připojení k virtuálnímu počítači na místě a znovu spusťte dotaz na Scheduled Events koncový bod. Opakujte následující příkaz, dokud nezískáte výstup, který obsahuje další informace:

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

Když naplánovaná událost získá oznámení vyřazení, dostanete odpověď, která bude vypadat podobně jako v tomto příkladu:

```output
{"DocumentIncarnation":1,"Events":[{"EventId":"A123BC45-1234-5678-AB90-ABCDEF123456","EventStatus":"Scheduled","EventType":"Preempt","ResourceType":"VirtualMachine","Resources":["myspotvm"],"NotBefore":"Tue, 16 Mar 2021 00:58:46 GMT","Description":"","EventSource":"Platform"}]}
```

Můžete vidět, že `"EventType":"Preempt"` a prostředek je prostředek virtuálního počítače `"Resources":["myspotvm"]` . 

Můžete také zjistit, jestli se virtuální počítač vyřadí, a to tak, že zkontrolujete, že `"NotBefore"` virtuální počítač nebude vyřazený před uplynutím této doby, takže to znamená, že vaše okno vaší aplikace bude možné řádně zavřít.


## <a name="next-steps"></a>Další kroky

Můžete také vytvořit virtuální počítač se službou Azure na místě pomocí [Azure PowerShell](../windows/spot-powershell.md), [portálu](../spot-portal.md)nebo [šablony](spot-template.md).

Dotaz na aktuální informace o cenách pomocí [API maloobchodních cen Azure](/rest/api/cost-management/retail-prices/azure-retail-prices) pro informace o virtuálním počítači Azure na místě `meterName`A `skuName` bude obsahovat oba `Spot` .

Pokud dojde k chybě, přečtěte si [kódy chyb](../error-codes-spot.md).

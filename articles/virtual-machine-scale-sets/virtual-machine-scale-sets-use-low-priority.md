---
title: Vytvoření sady škálování Azure, který používá virtuální počítače s nízkou prioritou (Preview) | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit škálovací sady virtuálních počítačů Azure, které používají k uložení nákladů na virtuální počítače s nízkou prioritou
services: virtual-machine-scale-sets
documentationcenter: ''
author: mmccrory
manager: rajraj
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: memccror
ms.openlocfilehash: 861c68ae8163e0ba8c2af2a3d96153ac3e84855f
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55978132"
---
# <a name="low-priority-vms-on-scale-sets-preview"></a>Virtuální počítače s nízkou prioritou ve škálovacích sadách (preview)

Použití virtuálních počítačů s nízkou prioritou ve škálovacích sadách umožňuje využívat naše zemědělsky nevyužitá kapacita v významných úspor. V libovolném okamžiku v čase, když Azure potřebuje kapacity zpět infrastruktura Azure vyřazení virtuálních počítačů s nízkou prioritou. Proto se skvěle hodí pro úlohy, které dokáže zpracovat přerušení, jako jsou dávkové zpracování úloh, prostředí pro vývoj/testování, velkých výpočetních úloh a další virtuální počítače s nízkou prioritou.

Množství dostupné zemědělsky nevyužitá kapacita může lišit v závislosti na velikost, oblast, denní dobu a další. Při nasazování virtuálních počítačů s nízkou prioritou na škálovací sady, Azure se přidělí virtuální počítače, pokud je k dispozici kapacitu, ale neexistuje žádná smlouva SLA pro tyto virtuální počítače. S nízkou prioritou škálovací sady je nasazené v jedné doména a nabízí záruky bez vysoké dostupnosti.

## <a name="eviction-policy"></a>Zásady vyřazení

Při vytváření s nízkou prioritou škálovací sady, můžete nastavit zásadu vyřazení *přidělení* (výchozí) nebo *odstranit*. 

*Přidělení* zásad přesune do stavu Zastaveno, abyste mohli znovu nasadit vyřazené instance vyřazené virtuálních počítačů. Neexistuje ale žádná záruka, že přidělování bude úspěšné. Uvolnění virtuální počítače budou započítávat kvótu instance škálovací sady a vám bude účtovat základní disky. 

Pokud chcete virtuální počítače ve s nízkou prioritou škálovací sadě, která se má odstranit, když se vyřadí, můžete nastavit zásadu vyřazení *odstranit*. Pomocí zásady vyřazení nastavena na hodnotu odstranit můžete vytvořit nové virtuální počítače zvýšením počtu vlastnosti škálovací sady instance. Vyřazené virtuálních počítačů se odstraní spolu s jejich základním diskům, a proto se vám nebude účtovat úložiště. Můžete také použít funkci Automatické škálování škálovací sady automaticky akci a jako kompenzaci za vyřazené virtuálních počítačů, ale neexistuje žádná záruka, že přidělování bude úspěšné. Doporučuje se, že používáte pouze funkce automatického škálování na nízkou prioritu škálovací sady při nastavování zásad vyřazení odstranit, aby náklady na disky a dosažení maximální kvóty. 

> [!NOTE]
> Ve verzi preview, budete moct nastavit zásady vyřazení pomocí [webu Azure portal](#use-the-azure-portal) a [šablon Azure Resource Manageru](#use-azure-resource-manager-templates). 

## <a name="deploying-low-priority-vms-on-scale-sets"></a>Nasazení virtuálních počítačů s nízkou prioritou ve škálování nastaví

Pokud chcete nasadit s nízkou prioritou virtuálních počítačů ve škálovacích sadách, můžete nastavit nové *Priority* příznak, který *nízká*. Všechny virtuální počítače ve škálovací sadě se nastaví na nízkou prioritu. Pokud chcete vytvořit škálovací sadu virtuálních počítačů s nízkou prioritou, použijte jednu z následujících metod:
- [Azure Portal](#use-the-azure-portal)
- Azure CLI
- [Azure PowerShell](#use-azure-powershell)
- [Šablony Azure Resource Manageru](#use-azure-resource-manager-templates)

## <a name="use-the-azure-portal"></a>Použití webu Azure Portal

Proces vytvořit škálovací sadu, která používá virtuální počítače s nízkou prioritou je stejný, jak je uvedeno v [Začínáme článku](quick-create-portal.md). Když nasadíte škálovací sadu, můžete nastavit příznak s nízkou prioritou a zásady vyřazení: ![Vytvoření škálovací sady virtuálních počítačů s nízkou prioritou](media/virtual-machine-scale-sets-use-low-priority/vmss-low-priority-portal.png)

## <a name="use-the-azure-cli"></a>Použití Azure CLI

Proces vytvoření škálovací sady virtuálních počítačů s nízkou prioritou je stejný, jak je uvedeno v [Začínáme článku](quick-create-cli.md). Stačí přidat elementy "--Priority" parametr rozhraní příkazového řádku volání a nastavte ho na *s nízkou* jak je znázorněno v následujícím příkladu:

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Low
```

## <a name="use-azure-powershell"></a>Použití Azure Powershell

Proces vytvoření škálovací sady virtuálních počítačů s nízkou prioritou je stejný, jak je uvedeno v [Začínáme článku](quick-create-powershell.md).
Stačí přidat elementy "-Priority" parametr [AzVmssConfig nový](/powershell/module/az.compute/new-azvmssconfig) a nastavte ho na *nízká* jak je znázorněno v následujícím příkladu:

```powershell
$vmssConfig = New-AzVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Priority "Low"
```

## <a name="use-azure-resource-manager-templates"></a>Pomocí šablony Azure Resource Manageru

Proces vytvořit škálovací sadu, která používá virtuální počítače s nízkou prioritou je stejný, jak je uvedeno v článku rychlý pro [Linux](quick-create-template-linux.md) nebo [Windows](quick-create-template-windows.md). Přidání vlastnosti "priority", která má *Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile* prostředků zadejte v šabloně a *nízká* jako hodnotu. Nezapomeňte použít *2018-03-01* verze rozhraní API nebo vyšší. 

Aby bylo možné nastavit zásady vyřazení pro odstranění, přidejte parametr "evictionPolicy" a nastavte ho na *odstranit*.

Následující příklad vytvoří škálovací sadu s názvem s nízkou prioritou Linuxem *myScaleSet* v *střed USA – západ*, který bude *odstranit* virtuálních počítačů ve škálovací sadě na vyřazení:

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2018-03-01",
  "sku": {
    "name": "Standard_DS2_v2",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
       "priority": "Low",
       "evictionPolicy": "delete",
       "storageProfile": {
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage"
        },
        "imageReference":  {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04-LTS",
          "version": "latest"
        }
      },
      "osProfile": {
        "computerNamePrefix": "myvmss",
        "adminUsername": "azureuser",
        "adminPassword": "P@ssw0rd!"
      }
    }
  }
}
```
## <a name="faq"></a>Nejčastější dotazy

### <a name="can-i-convert-existing-scale-sets-to-low-priority-scale-sets"></a>Můžete převést existující škálovací sady na nízkou prioritu škálovací sady?
Ne, nastavením příznaku s nízkou prioritou je podporována pouze v okamžiku vytvoření.

### <a name="can-i-create-a-scale-set-with-both-regular-vms-and-low-priority-vms"></a>Můžete vytvořit škálovací sadu s běžnými virtuálními počítači a virtuálních počítačů s nízkou prioritou?
Ne, škálovací sadu nepodporuje více než jeden typ priority.

### <a name="how-is-quota-managed-for-low-priority-vms"></a>Jak se spravují kvótu pro virtuální počítače s nízkou prioritou?
Virtuální počítače s nízkou prioritou a běžnými virtuálními počítači sdílejí stejný fond kvóty. 

### <a name="can-i-use-autoscale-with-low-priority-scale-sets"></a>Můžete použít automatické škálování s nízkou prioritou škálovací sady?
Ano, můžete nastavit pravidla automatického škálování na nízkou prioritu škálovací sady. Pokud vaše virtuální počítače se vyřadí, automatického škálování můžete zkusit vytvořit nové virtuální počítače s nízkou prioritou. Mějte na paměti, není zaručeno tuto kapacitu až. 

### <a name="does-autoscale-work-with-both-eviction-policies-deallocate-and-delete"></a>Funguje s automatickým Škálováním pomocí obou zásad vyřazení (uvolnění a odstranit)?
Doporučuje se, že jste nastavili zásadu vyřazení odstranit při použití automatického škálování. Je to proto uvolnění instancí se započítávají i počet vašich kapacitu škálovací sady. Při použití automatického škálování, bude pravděpodobně volání váš cílový počet instancí rychle kvůli uvolnění, vyřazené instance. 

## <a name="next-steps"></a>Další postup
Teď, když vytvoříte škálovací sadu virtuálních počítačů s nízkou prioritou, zkuste nasazení našich [šablony automatické škálování pomocí s nízkou prioritou](https://github.com/Azure/vm-scale-sets/tree/master/preview/lowpri).

Podívejte se [škálovací sadu virtuálních počítačů stránce s cenami](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) podrobnosti o cenách.

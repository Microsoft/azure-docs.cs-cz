---
title: Vytvořit sadu Azure škálování, který používá virtuální počítače s nízkou prioritou (Preview) | Microsoft Docs
description: Informace o vytvoření sady škálování virtuálního počítače Azure, které k uložení na náklady použijte virtuální počítače s nízkou prioritou
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
ms.openlocfilehash: 5c0726ea0da288d5306e28b101e4d3b59605b443
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="low-priority-vms-on-scale-sets-preview"></a>Virtuální počítače s nízkou prioritou na sady škálování (preview)

Pomocí na škálovací sady virtuálních počítačů s nízkou prioritou umožňuje využít výhod naše zemědělsky nevyužitá kapacity na výrazných úspor nákladů. V libovolném bodě v čase, kdy Azure potřebuje kapacitu zpět infrastrukturu Azure vyřazení virtuální počítače s nízkou prioritou. Proto se výborně hodí pro úlohy, které může zpracovat přerušení, jako je dávkového zpracování úloh, prostředí pro vývoj/testování, velkých výpočetních úloh a další virtuální počítače s nízkou prioritou.

Velikost dostupné kapacity zemědělsky nevyužitá může lišit v závislosti na velikosti, oblast, denní dobu a další. Při nasazení virtuálních počítačů s nízkou prioritou na škálování nastaví, bude Azure přidělit virtuálních počítačů, pokud je dostupná kapacita, ale neexistuje žádné SLA pro tyto virtuální počítače. Sada škálování nízkou prioritu je nasazena v domény selhání jednoho a nabízí že zaručuje bez vysoké dostupnosti.

## <a name="eviction-policy"></a>Zásady vyřazení

Při vytváření sady škálování nízkou prioritu, můžete nastavit zásady vyřazení *Deallocate* (výchozí) nebo *odstranit*. 

*Deallocate* zásad přesune vyřazené virtuální počítače do stavu zastavena navrácena umožňuje znovu nasaďte vyřazené instancí. Neexistuje však záruka, že bude úspěšné přidělení. Deallocated virtuální počítače budou započítává vaší škálování nastavení instance kvóty a vám bude účtována pro základní disky. 

Pokud chcete virtuální počítače ve vaší nízkou prioritu měřítku nastavena na odstraněn v případě jejich vyřazování, můžete nastavit zásady vyřazení na *odstranit*. Se zásadami vyřazení nastavena na hodnotu odstranit můžete vytvořit nové virtuální počítače zvýšením počtu vlastnost škálování sady instance. Vyřazené virtuálních počítačů se odstraní spolu s jejich základní disky, a proto vám nebude nic účtováno pro úložiště. Můžete také použít funkci automatického škálování sad škálování pro automatické zkuste a kompenzovat vyřazené virtuálních počítačů, ale neexistuje žádná záruka, který bude úspěšné přidělení. Doporučuje se, že pouze používáte funkci Automatické škálování na nízkou prioritu škálovací sady při nastavení zásad vyřazení odstranit předejdete náklady na vaše disky a stiskne maximální kvóty. 

> [!NOTE]
> Během ve verzi preview bude možné nastavit zásady vyřazení pomocí [portál Azure](#use-the-azure-portal) a [šablon Azure Resource Manageru](#use-azure-resource-manager-templates). 

## <a name="deploying-low-priority-vms-on-scale-sets"></a>Nastaví nízkou prioritu virtuální počítače na škálování nasazení

Chcete-li nasadit nízkou prioritu virtuálních počítačů na sady škálování, můžete nastavit nový *s prioritou* příznak, který *nízká*. Všechny virtuální počítače ve škálovací sadě se nastaví na nízkou prioritu. Vytvořit měřítko nastavit s virtuálními počítači nízkou prioritu, použijte jednu z následujících metod:
- [Azure Portal](#use-the-azure-portal)
- [Azure CLI 2.0](#use-the-azure-cli-20)
- [Azure PowerShell](#use-azure-powershell)
- [Šablony Azure Resource Manageru](#use-azure-resource-manager-templates)

## <a name="use-the-azure-portal"></a>Použití webu Azure Portal

Proces vytvoření sadě škálování, kterou používá virtuální počítače s nízkou prioritou je stejný jako podrobné v [Začínáme článku](quick-create-portal.md). Při nasazení sady škálování, můžete nastavit příznak nízkou prioritu a zásady vyřazení: ![vytvořit měřítko nastavit s virtuálními počítači s nízkou prioritou](media/virtual-machine-scale-sets-use-low-priority/vmss-low-priority-portal.png)

## <a name="use-the-azure-cli-20"></a>Použití Azure CLI 2.0

Proces vytvoření škálování nastavit s nízkou prioritou virtuální počítače je stejný jako podrobné v [Začínáme článku](quick-create-cli.md). Stačí přidat ' – s prioritou se do rozhraní příkazového řádku parametr volání a nastavte ji na *nízká* jak je znázorněno v následujícím příkladu:

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

Proces vytvoření škálování nastavit s nízkou prioritou virtuální počítače je stejný jako podrobné v [Začínáme článku](quick-create-powershell.md).
Stačí přidat '-s prioritou se parametru [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) a nastavte ji na *nízká* jak je znázorněno v následujícím příkladu:

```powershell
$vmssConfig = New-AzureRmVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Priority "Low"
```

## <a name="use-azure-resource-manager-templates"></a>Pomocí šablony Azure Resource Manageru

Proces vytvoření sadě škálování, kterou používá virtuální počítače s nízkou prioritou je stejný jako v článku na získávání Začínáme pro podrobné [Linux](quick-create-template-linux.md) nebo [Windows](quick-create-template-windows.md). Přidat vlastnosti "priority", která má *Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile* prostředků zadejte v šabloně a *nízká* jako hodnotu. Nezapomeňte použít *2018-03-01* verze rozhraní API nebo vyšší. 

Aby bylo možné nastavit zásady vyřazení k odstranění, přidejte parametr "evictionPolicy" a nastavte ji na *odstranit*.

Následující příklad vytvoří škálování nízkou prioritu Linux nastavení s názvem *myScaleSet* v *– Západ střední USA*, který bude *odstranit* virtuální počítače ve škálovací nastavit na vyřazení:

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

### <a name="can-i-convert-existing-scale-sets-to-low-priority-scale-sets"></a>Můžete převést existující sady škálování na nízkou prioritu škálovací sady?
Ne, nastavení příznaku nízkou prioritu je podporována pouze v okamžiku vytvoření.

### <a name="can-i-create-a-scale-set-with-both-regular-vms-and-low-priority-vms"></a>Můžete vytvořit škálování s regulární virtuálních počítačů a virtuálních počítačů s nízkou prioritou
Ne, škálovací sadu nepodporuje více než jeden typ s prioritou.

### <a name="how-is-quota-managed-for-low-priority-vms"></a>Spravováni kvótu pro virtuální počítače s nízkou prioritou
Nízkou prioritu virtuálních počítačů a virtuálních počítačů regulární sdílet stejný fond kvóty. 

### <a name="can-i-use-autoscale-with-low-priority-scale-sets"></a>Můžete použít automatické škálování s nízkou prioritou škálovací sady?
Ano, můžete nastavit automatické škálování pravidla v sadě škálování nízkou prioritu. Pokud vaše virtuální počítače jsou vyřazování, automatické škálování můžete zkusit vytvořit nové virtuální počítače s nízkou prioritou. Nezapomeňte, že jste se nezaručuje, ale tato kapacita. 

### <a name="does-autoscale-work-with-both-eviction-policies-deallocate-and-delete"></a>Škálování funguje s obě zásady vyřazení (zrušit přidělení a odstranit)?
Doporučujeme nastavit zásady vaší vyřazení odstranit při použití automatického škálování. To je proto deallocated instance počítají proti spočítat kapacity v sadě škálování. Při použití automatického škálování, bude pravděpodobně stiskněte váš cílový počet instancí rychle z důvodu deallocated, vyřazené instance. 

## <a name="next-steps"></a>Další postup
Teď, když jste vytvořili škálování nastavit s virtuálními počítači nízkou prioritu, zkuste nasazení naše [automatické škálování šablony pomocí nízkou prioritu](https://github.com/Azure/vm-scale-sets/tree/master/preview/lowpri).

Podívejte se [škálovací sadu virtuálních počítačů stránce s cenami](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) pro podrobné informace o cenách.
---
title: Použití vlastních zásad škálování s škálovacími sadami virtuálních strojů Azure
description: Zjistěte, jak používat vlastní škálovací zásady se škálovacími sadami virtuálních počítačů Azure, které ke správě počtu instancí používají konfiguraci automatického škálování.
services: virtual-machine-scale-sets
author: avirishuv
manager: vashan
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: avverma
ms.openlocfilehash: ffcdaf76bdd08ee5505ddbeff6a6698e231b6171
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919834"
---
# <a name="use-custom-scale-in-policies-with-azure-virtual-machine-scale-sets"></a>Použití vlastních zásad škálování s škálovacími sadami virtuálních strojů Azure

Nasazení škálovací sady virtuálních strojů lze škálovat nebo škálovat na základě pole metrik, včetně vlastních metrik definovaných platformou a uživatelem. Zatímco horizontální navýšení kapacity vytvoří nové virtuální počítače založené na modelu škálovací sady, škálování ovlivňuje spuštěné virtuální počítače, které mohou mít různé konfigurace nebo funkce podle vývoje úlohy škálovací sady. 

Funkce zásad škálování poskytuje uživatelům způsob, jak nakonfigurovat pořadí, ve kterém jsou virtuální počítače škálovány, a to prostřednictvím tří konfigurací škálování: 

1. Výchozí
2. Nejnovější VM
3. Nejstarší VM

### <a name="default-scale-in-policy"></a>Výchozí zásady škálování

Ve výchozím nastavení škálovací sada virtuálních strojů použije tuto zásadu k určení, které instance budou škálovány. Pomocí *zásad Default* jsou virtuální aplikace vybrány pro škálování v následujícím pořadí:

1. Vyvážení virtuálních počítačů mezi zónami dostupnosti (pokud je škálovací sada nasazena v konfiguraci zónové)
2. Vyvážení virtuálních počítačů mezi doménami selhání (nejlepší úsilí)
3. Odstranění virtuálního počítače s nejvyšším ID instance

Uživatelé nemusí zadávat zásady škálování, pokud chtějí pouze dodržet výchozí řazení.

Všimněte si, že vyvažování mezi zónami dostupnosti nebo doménami selhání nepřesouvá instance mezi zónami dostupnosti nebo doménami selhání. Vyvažování je dosaženo odstraněním virtuálních počítačů z nevyvážených zón dostupnosti nebo domén selhání, dokud se distribuce virtuálních počítačů nevyrovná.

### <a name="newestvm-scale-in-policy"></a>Nejnovější zásady škálování v systému VM

Tato zásada odstraní nejnovější vytvořený virtuální počítač ve škálovací sadě po vyvažování virtuálních počítačů napříč zónami dostupnosti (pro zónová nasazení). Povolení této zásady vyžaduje změnu konfigurace v modelu škálovací sady virtuálních počítačů.

### <a name="oldestvm-scale-in-policy"></a>Zásady škálování nejstaršího virtuálního vadou

Tato zásada odstraní nejstarší vytvořený virtuální počítač ve škálovací sadě po vyvažování virtuálních počítačů napříč zónami dostupnosti (pro územní nasazení). Povolení této zásady vyžaduje změnu konfigurace v modelu škálovací sady virtuálních počítačů.

## <a name="enabling-scale-in-policy"></a>Povolení zásad škálování

Zásada škálování je definována v modelu škálovací sady virtuálních strojů. Jak je uvedeno ve výše uvedených částech, definice zásad škálování je potřeba při použití zásad Nejnovější VM a "OldestVM". Škálovací sada virtuálních strojů automaticky použije zásadu škálování výchozího, pokud v modelu škálovací sady není nalezena žádná definice zásad škálování. 

Zásady škálování lze definovat na modelu škálovací sady virtuálních strojů následujícími způsoby:

### <a name="azure-portal"></a>portál Azure
 
Následující kroky definují zásady škálování při vytváření nové škálovací sady. 
 
1. Přejděte na **Škálovací sady virtuálních strojů**.
1. Výběrem **možnosti + Přidat** vytvoříte novou škálovací sadu.
1. Přejděte na kartu **Změna velikosti.** 
1. Vyhledejte oddíl **zásad škálování.**
1. V rozevíracím souboru vyberte zásadu škálování.
1. Po vytvoření nové škálovací sady vyberte **Tlačítko Revize + vytvořit.**

### <a name="using-api"></a>Pomocí rozhraní API

Spusťte put na škálovací sadě virtuálních strojů pomocí rozhraní API 2019-03-01:

```
PUT
https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<myRG>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVMSS>?api-version=2019-03-01

{ 
"location": "<VMSS location>", 
    "properties": { 
        "scaleInPolicy": {  
            "rules": ["OldestVM"]  
        } 
    }    
} 
```
### <a name="azure-powershell"></a>Azure PowerShell

Vytvořte skupinu prostředků a vytvořte novou škálovací sadu s nastavenou zásadou škálování jako *OldestVM*.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName "myResourceGroup" -Location "<VMSS location>"
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "<VMSS location>" `
  -VMScaleSetName "myScaleSet" `
  -ScaleInPolicy “OldestVM”
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

Následující příklad přidá zásadu škálování při vytváření nové škálovací sady. Nejprve vytvořte skupinu prostředků a pak vytvořte novou škálovací sadu s zásadou škálování jako *OldestVM*. 

```azurecli-interactive
az group create --name <myResourceGroup> --location <VMSSLocation>
az vmss create \
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --image UbuntuLTS \
  --admin-username <azureuser> \
  --generate-ssh-keys \
  --scale-in-policy OldestVM
```

### <a name="using-template"></a>Použití šablony

V šabloně v části "vlastnosti" přidejte následující:

```json
"scaleInPolicy": {  
      "rules": ["OldestVM"]  
}
```

Výše uvedené bloky určují, že škálovací sada virtuálního počítače odstraní nejstarší virtuální počítač v škálovací sadě s vyváženou zónou při aktivaci škálování (pomocí automatického škálování nebo ručního odstranění).

Pokud škálovací sada virtuálního počítače není vyvážená zóny, škálovací sada nejprve odstraní virtuální počítače v nevyváženou zónou(s). V rámci nevyvážená zóny škálovací sada použije zásady škálování uvedené výše k určení, který virtuální virtuální virtuální ms škálovat v. V takovém případě v rámci nevyvážené zóny škálovací sada vybere nejstarší virtuální ho dioda v této zóně, která má být odstraněna.

Pro škálovací sadu virtuálních strojů, která není zonální, zásada vybere nejstarší virtuální počítač napříč škálovací sadou pro odstranění.

Stejný proces platí při použití "Nejnovější VM" ve výše škálovat zásady.

## <a name="modifying-scale-in-policies"></a>Úprava zásad škálování

Změna zásad škálování se řídí stejným postupem jako použití zásad škálování. Pokud například ve výše uvedeném příkladu chcete změnit zásady z "OldestVM" na Nejnovější VM, můžete tak učinit takto:

### <a name="azure-portal"></a>portál Azure

Můžete upravit zásady škálování existující škálovací sady prostřednictvím portálu Azure. 
 
1. V existující škálovací sadě virtuálního počítače vyberte **měřítko** z nabídky vlevo.
1. Vyberte kartu **Zásady škálování.**
1. V rozevíracím souboru vyberte zásadu škálování.
1. Po dokončení vyberte **Uložit**. 

### <a name="using-api"></a>Pomocí rozhraní API

Spusťte put na škálovací sadě virtuálních strojů pomocí rozhraní API 2019-03-01:

```
PUT
https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<myRG>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVMSS>?api-version=2019-03-01 

{ 
"location": "<VMSS location>", 
    "properties": { 
        "scaleInPolicy": {  
            "rules": ["NewestVM"]  
        } 
    }    
}
```
### <a name="azure-powershell"></a>Azure PowerShell

Aktualizujte zásady škálování existující škálovací sady:

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -ScaleInPolicy “OldestVM”
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

Následuje příklad pro aktualizaci zásad škálování existující škálovací sady: 

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --scale-in-policy OldestVM
```

### <a name="using-template"></a>Použití šablony

V šabloně v části "vlastnosti" upravte šablonu tak, jak je uvedeno níže, a znovu nasadit: 

```json
"scaleInPolicy": {  
      "rules": ["NewestVM"]  
} 
```

Stejný proces bude platit, pokud se rozhodnete změnit "NejnovějšíVM" na 'Výchozí' nebo "OldestVM"

## <a name="instance-protection-and-scale-in-policy"></a>Zásady ochrany instance a škálování

Škálovací sady virtuálních počítačů poskytují dva typy [ochrany instancí:](./virtual-machine-scale-sets-instance-protection.md#types-of-instance-protection)

1. Ochrana před škálovatanem
2. Ochrana před akcemi škálovací sady

Chráněný virtuální počítač není odstraněn pomocí akce škálování, bez ohledu na zásady škálování. Například pokud VM_0 (nejstarší virtuální počítač v škálovací sadě) je chráněn před škálovat velikosti a škálovací sada má "Nejstarší VM" škálování zásady povolené, VM_0 nebudou považovány za škálování v, i když se jedná o nejstarší virtuální počítač v škálovací sadě. 

Chráněný virtuální počítač může uživatel kdykoli ručně odstranit bez ohledu na zásady škálování povolené v škálovací sadě. 

## <a name="usage-examples"></a>Příklady použití 

Níže uvedené příklady ukazují, jak škálovací sada virtuálních počítačů vybere virtuální počítače, které se mají odstranit, když se aktivuje událost škálování. Virtuální počítače s nejvyšší id instancí se považují za nejnovější virtuální počítače ve škálovací sadě a virtuální počítače s nejmenšími ID instancí se považují za nejstarší virtuální počítače ve škálovací sadě. 

### <a name="oldestvm-scale-in-policy"></a>Zásady škálování nejstaršího virtuálního vadou

| Událost                 | ID instancí v zóně1  | ID instancí v zóně 2  | ID instancí v zóně3  | Výběr škálování v měřítku                                                                                                               |
|-----------------------|------------------------|------------------------|------------------------|----------------------------------------------------------------------------------------------------------------------------------|
| Počáteční               | 3, 4, 5, 10            | 2, 6, 9, 11            | 1, 7, 8                |                                                                                                                                  |
| Škálování na vavina              | 3, 4, 5, 10            | ***2***, 6, 9, 11.      | 1, 7, 8                | Vyberte si mezi zónou 1 a 2, i když zóna 3 má nejstarší virtuální ms. Odstraňte VM2 ze zóny 2, protože se jedná o nejstarší virtuální virtuální ms v této zóně.   |
| Škálování na vavina              | ***3***, 4, 5, 10.      | 6, 9, 11               | 1, 7, 8                | Zvolte zónu 1, i když zóna 3 má nejstarší virtuální ms. Odstraňte VM3 ze zóny 1, protože se jedná o nejstarší virtuální virtuální ms v této zóně.                  |
| Škálování na vavina              | 4, 5, 10               | 6, 9, 11               | ***1***, 7, 8.          | Zóny jsou vyvážené. Odstraňte VM1 v zóně 3, protože se jedná o nejstarší virtuální virtuální ms ve škálovací sadě.                                               |
| Škálování na vavina              | ***4***, 5, 10.         | 6, 9, 11               | 7, 8                   | Vyberte si mezi zónou 1 a zónou 2. Odstraňte VM4 v zóně 1, protože se jedná o nejstarší virtuální virtuální ms přes dvě zóny.                              |
| Škálování na vavina              | 5, 10                  | ***6***, 9, 11.         | 7, 8                   | Zvolte zónu 2, i když zóna 1 má nejstarší virtuální ms. Odstraňte VM6 v zóně 1, protože se jedná o nejstarší virtuální virtuální ms v této zóně.                    |
| Škálování na vavina              | ***5,*** 10.            | 9, 11                  | 7, 8                   | Zóny jsou vyvážené. Odstraňte VM5 v zóně 1, protože se jedná o nejstarší virtuální virtuální ms v škálovací sadě.                                                |

Pro škálovací sady virtuálních strojů, které nejsou zonální, zásada vybere nejstarší virtuální počítač napříč škálovací sadou pro odstranění. Všechny "chráněné" virtuální počítač bude přeskočen k odstranění.

### <a name="newestvm-scale-in-policy"></a>Nejnovější zásady škálování v systému VM

| Událost                 | ID instancí v zóně1  | ID instancí v zóně 2  | ID instancí v zóně3  | Výběr škálování v měřítku                                                                                                               |
|-----------------------|------------------------|------------------------|------------------------|----------------------------------------------------------------------------------------------------------------------------------|
| Počáteční               | 3, 4, 5, 10            | 2, 6, 9, 11            | 1, 7, 8                |                                                                                                                                  |
| Škálování na vavina              | 3, 4, 5, 10            | 2, 6, 9, ***11.***      | 1, 7, 8                | Vyberte si mezi zónou 1 a 2. Odstraňte VM11 ze zóny 2, protože se jedná o nejnovější virtuální virtuální ms napříč dvěma zónami.                                |
| Škálování na vavina              | 3, 4, 5, ***10.***      | 2, 6, 9                | 1, 7, 8                | Zvolte zóna 1, protože má více virtuálních her než ostatní dvě zóny. Odstraňte VM10 ze zóny 1, protože se jedná o nejnovější virtuální virtuální ms v této zóně.          |
| Škálování na vavina              | 3, 4, 5                | 2, 6, ***9***          | 1, 7, 8                | Zóny jsou vyvážené. Odstraňte VM9 v zóně 2, protože se jedná o nejnovější virtuální virtuální ms ve škálovací sadě.                                                |
| Škálování na vavina              | 3, 4, 5                | 2, 6                   | 1, 7, ***8.***          | Vyberte si mezi zónou 1 a zónou 3. Odstraňte VM8 v zóně 3, protože se jedná o nejnovější virtuální virtuální ms v této zóně.                                      |
| Škálování na vavina              | 3, 4, ***5.***          | 2, 6                   | 1, 7                   | Zvolte zónu 1, i když zóna 3 má nejnovější virtuální virtuální ms. Odstraňte VM5 v zóně 1, protože se jedná o nejnovější virtuální virtuální ms v této zóně.                    |
| Škálování na vavina              | 3, 4                   | 2, 6                   | 1, ***7.***             | Zóny jsou vyvážené. Odstraňte VM7 v zóně 3, protože se jedná o nejnovější virtuální virtuální ms ve škálovací sadě.                                                |

Pro škálovací sady virtuálních strojů, které nejsou zonální, zásady vyberou nejnovější virtuální počítač napříč škálovací sadou pro odstranění. Všechny "chráněné" virtuální počítač bude přeskočen k odstranění. 

## <a name="troubleshoot"></a>Řešení potíží

1. Selhání povolit scaleInPolicy Pokud se zobrazí chyba "BadRequest" s chybovou zprávou "Nelze najít člena scaleInPolicy" na objekt typu vlastnosti, zkontrolujte verzi rozhraní API použitou pro škálovací sadu virtuálních strojů. Pro tuto funkci je vyžadována verze rozhraní API 2019-03-01 nebo vyšší.

2. Nesprávný výběr virtuálních zařízení pro škálování viz příklady výše. Pokud je škálovací sada virtuálního počítače zonální nasazení, zásady škálování se použije nejprve na nevyvážené zóny a potom napříč škálovací sadou, jakmile je zóna vyvážená. Pokud pořadí škálování není konzistentní s výše uvedenými příklady, navěšte dotaz s týmem škálovací sady virtuálních strojů pro řešení potíží.

## <a name="next-steps"></a>Další kroky

Zjistěte, jak [nasadit aplikaci](virtual-machine-scale-sets-deploy-app.md) na škálovací sady virtuálních strojů.
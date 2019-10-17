---
title: Použití vlastních zásad škálování v rámci Azure Virtual Machine Scale Sets | Microsoft Docs
description: Naučte se používat vlastní zásady škálování s využitím Azure Virtual Machine Scale Sets, které ke správě počtu instancí používají konfiguraci automatického škálování.
services: virtual-machine-scale-sets
author: avverma
manager: vashan
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.topic: article
ms.date: 10/11/2019
ms.author: avverma
ms.openlocfilehash: c1618c398c0f7c4f0f54647e5232fdacc17de186
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2019
ms.locfileid: "72453157"
---
# <a name="preview-use-custom-scale-in-policies-with-azure-virtual-machine-scale-sets"></a>Verze Preview: použití vlastních zásad škálování v rámci Azure Virtual Machine Scale Sets

Nasazení sady škálování virtuálních počítačů je možné škálovat nebo škálovat na základě pole metrik, včetně platforem a uživatelsky definovaných vlastních metrik. I když škálování na více instancí vytvoří nové Virtual Machines založené na modelu sady škálování, škálování v systému ovlivňuje spouštění virtuálních počítačů, které mohou mít různé konfigurace a/nebo funkce, jako se vyvíjí zatížení sady škálování. 

Funkce zásad škálování poskytuje uživatelům způsob, jak nakonfigurovat pořadí, ve kterém se škálují virtuální počítače. Verze Preview přináší tři konfigurace škálování: 

1. Výchozí
2. NewestVM
3. OldestVM

***Tato funkce ve verzi Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy.***

### <a name="default-scale-in-policy"></a>Výchozí zásada škálování na více součástí

Ve výchozím nastavení tato zásada používá sadu škálování virtuálních počítačů k určení, které instance se budou škálovat. S *výchozími* zásadami jsou virtuální počítače vybrané pro škálování v tomto pořadí:

1. Vyvážení virtuálních počítačů napříč zónami dostupnosti (Pokud je sada škálování nasazená v konfiguraci pro oblast)
2. Vyvážení virtuálních počítačů napříč doménami selhání (nejlepší úsilí)
3. Odstranit virtuální počítač s nejvyšším ID instance

Uživatelé nemusejí zadávat zásadu škálování na úrovni, pokud chcete, aby následovala pouze výchozí řazení.

Všimněte si, že vyrovnání mezi zónami dostupnosti nebo doménami selhání nepřesouvá instance mezi zónami dostupnosti nebo doménami selhání. Vyvážení se dosahuje odstraněním virtuálních počítačů ze zóny nevyvážené dostupnosti nebo domén selhání do chvíle, kdy se distribuce virtuálních počítačů vyrovnává.

### <a name="newestvm-scale-in-policy"></a>Zásady škálování na NewestVM

Tato zásada odstraní nejnovější vytvořený virtuální počítač v sadě škálování po vyvážení virtuálních počítačů napříč zónami dostupnosti (pro různá nasazení). Povolení těchto zásad vyžaduje změnu konfigurace modelu sady škálování virtuálních počítačů.

### <a name="oldestvm-scale-in-policy"></a>Zásady škálování na OldestVM

Tato zásada odstraní nejstarší vytvořený virtuální počítač v sadě škálování po vyvážení virtuálních počítačů napříč zónami dostupnosti (pro různá nasazení). Povolení těchto zásad vyžaduje změnu konfigurace modelu sady škálování virtuálních počítačů.

## <a name="enabling-scale-in-policy"></a>Povoluje se zásada škálování na úrovni

V modelu sady škálování virtuálního počítače je definována zásada škálování na úrovni. Jak je uvedeno v částech výše, při použití zásad ' NewestVM ' a ' OldestVM ' je nutná definice zásad škálování na více verzí. Sada škálování virtuálního počítače automaticky použije výchozí zásadu škálování na více počítačů, pokud se v modelu sady škálování nenajde žádná definice zásad škálování. 

Zásadu škálování na úrovni lze definovat v modelu sady škálování virtuálních počítačů následujícími způsoby:

### <a name="using-api"></a>Pomocí rozhraní API

Spusťte PUT do sady škálování virtuálních počítačů pomocí rozhraní API 2019-03-01:

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

### <a name="using-template"></a>Použití šablony

V šabloně v části vlastnosti přidejte následující:

```json
"scaleInPolicy": {  
      "rules": ["OldestVM"]  
}
```

Výše uvedené bloky určují, že sada škálování virtuálního počítače odstraní nejstarší virtuální počítač v sadě škálování s vyrovnanou zónou, když se aktivuje škálování (prostřednictvím automatického škálování nebo ručního odstranění).

Pokud není sada škálování virtuálního počítače vyvážená v zóně, bude sada škálování nejprve odstraňovat virtuální počítače v rámci nevyrovnaných zón. V rámci nevyvážených zón použije sada škálování výše uvedenou zásadu škálování k určení, který virtuální počítač se má škálovat. V tomto případě v rámci nevyvážené zóny vybere sada škálování nejstarší virtuální počítač v této zóně, který se má odstranit.

V případě sady škálování virtuálních počítačů mimo oblast vybere zásada nejstarší virtuální počítač v rámci sady škálování pro odstranění.

Stejný postup platí při použití ' NewestVM ' v výše popsané zásadě škálování na více verzí.

## <a name="modifying-scale-in-policies"></a>Změny zásad škálování na úrovni

Změna zásad škálování je stejná jako při použití zásady škálování na více míst. Například pokud ve výše uvedeném příkladu chcete změnit zásadu z ' OldestVM ' na ' NewestVM ', můžete tak učinit:

### <a name="using-api"></a>Pomocí rozhraní API

Spusťte PUT do sady škálování virtuálních počítačů pomocí rozhraní API 2019-03-01:

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

### <a name="using-template"></a>Použití šablony

V šabloně v části vlastnosti upravte šablonu následujícím způsobem a znovu nasaďte: 

```json
"scaleInPolicy": {  
      "rules": ["NewestVM"]  
} 
```

Stejný postup se použije, pokud se rozhodnete změnit ' NewestVM ' na ' default ' nebo ' OldestVM '

## <a name="instance-protection-and-scale-in-policy"></a>Ochrana instancí a zásady škálování na úrovni

Virtual Machine Scale Sets nabízí dva typy [ochrany instancí](./virtual-machine-scale-sets-instance-protection.md#types-of-instance-protection):

1. Ochrana před škálováním
2. Ochrana před akcemi nastavenými na úrovni škálování

Chráněný virtuální počítač se neodstraní prostřednictvím akce škálování na více počítačů bez ohledu na to, jakou zásadu škálování na úrovni se používá. Pokud je například VM_0 (nejstarší virtuální počítač v sadě škálování) chráněný před škálováním a v sadě škálování je povolená zásada pro škálování OldestVM, VM_0 se nepovažuje za škálované v, i když se jedná o nejstarší virtuální počítač v sadě škálování. 

Chráněný virtuální počítač může uživatel kdykoli odstranit, a to bez ohledu na zásadu škálování, která je v sadě škálování povolená. 

## <a name="usage-examples"></a>Příklady použití 

Níže uvedené příklady ukazují, jak bude sada škálování virtuálních počítačů při aktivaci události škálování vybrala virtuální počítače, které se mají odstranit. Předpokládá se, že virtuální počítače s nejvyšším ID instance jsou nejnovějšími virtuálními počítači v sadě škálování a virtuální počítače s nejmenším ID instance se považují za nejstarší virtuální počítače v sadě škálování. 

### <a name="oldestvm-scale-in-policy"></a>Zásady škálování na OldestVM

| Událost                 | ID instancí v zóna 1  | ID instancí v zóna 2  | ID instancí v zóna 3  | Výběr se škálováním na více míst                                                                                                               |
|-----------------------|------------------------|------------------------|------------------------|----------------------------------------------------------------------------------------------------------------------------------|
| Pořizovací               | 3, 4, 5, 10            | 2, 6, 9, 11            | 1, 7, 8                |                                                                                                                                  |
| Horizontální navýšení kapacity              | 3, 4, 5, 10            | ***2***, 6, 9, 11      | 1, 7, 8                | Vyberte si mezi Zóna 1 a 2, a to i v případě, že Zóna 3 má nejstarší virtuální počítač. Odstraňte VM2 z Zóna 2, protože se jedná o nejstarší virtuální počítač v této zóně.   |
| Horizontální navýšení kapacity              | ***3***, 4, 5, 10      | 6, 9, 11               | 1, 7, 8                | Vyberte Zóna 1, i když má Zóna 3 nejstarší virtuální počítač. Odstraňte VM3 z Zóna 1, protože se jedná o nejstarší virtuální počítač v této zóně.                  |
| Horizontální navýšení kapacity              | 4, 5, 10               | 6, 9, 11               | ***1***, 7, 8          | Zóny jsou vyvážené. Odstraní VM1 v Zóna 3, protože se jedná o nejstarší virtuální počítač v sadě škálování.                                               |
| Horizontální navýšení kapacity              | ***4***, 5, 10         | 6, 9, 11               | 7, 8                   | Vyberte si mezi Zóna 1 a Zóna 2. Odstraňte VM4 v Zóna 1, protože se jedná o nejstarší virtuální počítač v obou zónách.                              |
| Horizontální navýšení kapacity              | 5, 10                  | ***6***, 9, 11         | 7, 8                   | Vyberte Zóna 2, i když má Zóna 1 nejstarší virtuální počítač. Odstraní VM6 v Zóna 1, protože se jedná o nejstarší virtuální počítač v této zóně.                    |
| Horizontální navýšení kapacity              | ***5***, 10            | 9, 11                  | 7, 8                   | Zóny jsou vyvážené. Odstraní VM5 v Zóna 1, protože se jedná o nejstarší virtuální počítač v sadě škálování.                                                |

U virtuálních počítačů, které nejsou v rozsahu, vybere zásada nejstarší virtuální počítač v rámci sady škálování pro odstranění. Pro odstranění se přeskočí kterýkoli chráněný virtuální počítač.

### <a name="newestvm-scale-in-policy"></a>Zásady škálování na NewestVM

| Událost                 | ID instancí v zóna 1  | ID instancí v zóna 2  | ID instancí v zóna 3  | Výběr se škálováním na více míst                                                                                                               |
|-----------------------|------------------------|------------------------|------------------------|----------------------------------------------------------------------------------------------------------------------------------|
| Pořizovací               | 3, 4, 5, 10            | 2, 6, 9, 11            | 1, 7, 8                |                                                                                                                                  |
| Horizontální navýšení kapacity              | 3, 4, 5, 10            | 2, 6, 9, ***11***      | 1, 7, 8                | Vyberte si mezi Zóna 1 a 2. Odstraňte VM11 z Zóna 2, protože se jedná o nejnovější virtuální počítač v obou zónách.                                |
| Horizontální navýšení kapacity              | 3, 4, 5, ***10***      | 2, 6, 9                | 1, 7, 8                | Vyberte Zóna 1, protože mají více virtuálních počítačů než ostatní dvě zóny. Odstraní VM10 z Zóna 1, protože to je nejnovější virtuální počítač v této zóně.          |
| Horizontální navýšení kapacity              | 3, 4, 5                | 2, 6, ***9***          | 1, 7, 8                | Zóny jsou vyvážené. Odstraní VM9 v Zóna 2, protože to je nejnovější virtuální počítač v sadě škálování.                                                |
| Horizontální navýšení kapacity              | 3, 4, 5                | 2, 6                   | 1, 7, ***8***          | Vyberte si mezi Zóna 1 a Zóna 3. Odstraní VM8 v Zóna 3, protože se jedná o nejnovější virtuální počítač v této zóně.                                      |
| Horizontální navýšení kapacity              | 3, 4, ***5***          | 2, 6                   | 1, 7                   | Vyberte Zóna 1, i když má Zóna 3 nejnovější virtuální počítač. Odstraní VM5 v Zóna 1, protože se jedná o nejnovější virtuální počítač v této zóně.                    |
| Horizontální navýšení kapacity              | 3, 4                   | 2, 6                   | 1, ***7***             | Zóny jsou vyvážené. Odstraní VM7 v Zóna 3, protože to je nejnovější virtuální počítač v sadě škálování.                                                |

U virtuálních počítačů, které nejsou ve více oblastech, vybírá zásada nejnovější virtuální počítač v rámci sady škálování pro odstranění. Pro odstranění se přeskočí kterýkoli chráněný virtuální počítač. 

## <a name="troubleshoot"></a>Řešení potíží

1. Nepovedlo se povolit scaleInPolicy, pokud se zobrazí chyba důvodu chybného požadavku s chybovou zprávou, že se pro objekt typu Properties nepovedlo najít člena scaleInPolicy, a pak zkontrolujte verzi rozhraní API, která se používá pro sadu škálování virtuálního počítače. Pro tuto verzi Preview se vyžaduje rozhraní API verze 2019-03-01 nebo vyšší.

2. Špatný výběr virtuálních počítačů pro škálování – odkazují na výše uvedené příklady. Pokud je vaše virtuální počítačová sada škálování nastavená na více instancí, aplikuje se zásada škálování na více instancí jako první pro nevyvážené zóny a potom napříč nastavenou škálou, jakmile se zóna vyrovnává. Pokud pořadí škálování není v souladu s výše uvedenými příklady, vyvolejte dotaz s týmem sady škálování virtuálního počítače pro řešení potíží.

## <a name="next-steps"></a>Další kroky

Naučte se, jak [nasadit vaši aplikaci do služby](virtual-machine-scale-sets-deploy-app.md) Virtual Machine Scale Sets.
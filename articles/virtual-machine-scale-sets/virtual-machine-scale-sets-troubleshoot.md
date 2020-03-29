---
title: Poradce při potížích s automatickým škálování pomocí škálovacích sad virtuálních strojů
description: Poradce při potížích s automatickým škálování pomocí škálovacích sad virtuálních strojů. Seznamte se s typickými problémy a jak je vyřešit.
author: mayanknayar
tags: azure-resource-manager
ms.assetid: c7d87b72-ee24-4e52-9377-a42f337f76fa
ms.service: virtual-machine-scale-sets
ms.tgt_pltfrm: windows
ms.topic: conceptual
ms.date: 11/16/2017
ms.author: manayar
ms.openlocfilehash: 923967a902f611ce845fbdc096fd2c02e681bb6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76272431"
---
# <a name="troubleshooting-autoscale-with-virtual-machine-scale-sets"></a>Řešení potíží s automatickým škálováním se škálovacími sadami virtuálních počítačů
**Problém** – ve Správci prostředků Azure jste vytvořili infrastrukturu automatického škálování pomocí škálovacích https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale sad virtuálních strojů – například nasazením šablony, jako je tato: – máte definovaná pravidla škálování a funguje to skvěle, s výjimkou bez ohledu na to, kolik zatížení na virtuálnípočítače, není automatické škálování.

## <a name="troubleshooting-steps"></a>Postup při řešení potíží
Některé věci, které je třeba zvážit, zahrnují:

* Kolik virtuálních procesorů má každý virtuální počítače a načítáte každý virtuální procesor?
  Předchozí ukázková šablona Azure Quickstart má skript do_work.php, který načte jeden virtuální procesor. Pokud používáte virtuální počítač větší než velikost virtuálního počítače s jedním virtuálním procesorem, jako je Standard_A1 nebo D1, budete muset toto zatížení spustit vícekrát. Kontrola počtu virtuálních procesorů pro vaše virtuální počítače kontrolou [Velikosti pro virtuální počítače s Windows v Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* Kolik virtuálních počítačů ve škálovací sadě virtuálních strojů, děláte práci na každém z nich?
  
    Událost horizontálního navýšení kapacity se provádí pouze v případě, že průměrný procesor ve **všech** virtuálních počítače ve škálovací sadě překročí prahovou hodnotu v průběhu času interní definované v pravidlech automatického škálování.
* Zmeškal jsi nějaké akce?
  
    Zkontrolujte protokoly auditu na portálu Azure pro škálování událostí. Možná tam byla stupnice nahoru a zmenšení, které chybělo. Můžete filtrovat podle "Měřítko".
  
    ![Protokoly auditu][audit]
* Liší se vaše prahové hodnoty pro škálování a škálování dostatečně odlišné?
  
    Předpokládejme, že nastavíte pravidlo pro horizontální navýšení kapacity, pokud je průměrný procesor větší než 50 % za pět minut, a škálování v případě, že průměrný procesor je menší než 50 %. Toto nastavení by způsobit "mávání" problém, když využití procesoru je blízko k prahové hodnotě, s akce škálování neustále zvyšuje a zmenšuje velikost sady. Z důvodu tohoto nastavení se služba automatického škálování pokusí zabránit "mávání", což se může projevit jako neškálování. Proto se ujistěte, že vaše škálování a škálování prahové hodnoty jsou dostatečně odlišné, aby některé mezery mezi škálování.
* Napsali jste si vlastní šablonu JSON?
  
    Je snadné dělat chyby, takže začněte šablonou, jako je ta výše, která je prokázána, že funguje, a proveďte malé přírůstkové změny. 
* Můžete ručně škálovat nebo odpočitat?
  
    Zkuste znovu nasadit prostředek škálovací sady virtuálních strojů s jiným nastavením "kapacity", chcete-li ručně změnit počet virtuálních počítačů. Příklad šablony je https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing zde: – možná budete muset upravit šablonu, abyste se ujistili, že má stejnou velikost stroje, jakou používá vaše škálovací sada. Pokud můžete úspěšně změnit počet virtuálních zařízení ručně, pak víte, že problém je izolován a automatické škálování.
* Zkontrolujte prostředky Microsoft.Compute/virtualMachineScaleSet a Microsoft.Insights v [Průzkumníku zdrojů Azure](https://resources.azure.com/)
  
    Průzkumník prostředků Azure je nepostradatelný nástroj pro řešení potíží, který zobrazuje stav prostředků Azure Resource Manageru. Klikněte na předplatné a podívejte se na skupinu prostředků, kterou řešíte. V části Poskytovatel prostředků výpočetních prostředků se podívejte na vytvořenou škálovací sadu virtuálních strojů a zkontrolujte zobrazení instance, které zobrazuje stav nasazení. Zkontrolujte také zobrazení instance virtuálních počítačů ve škálovací sadě virtuálních strojů. Potom přejděte k poskytovateli prostředků Microsoft.Insights a zkontrolujte, zda pravidla automatického škálování vypadají správně.
* Funguje rozšíření Diagnostika a vyzařuje údaje o výkonu?
  
    **Aktualizace:** Automatické škálování Azure bylo vylepšeno tak, aby používalo kanál metrik založených na hostiteli, který už nevyžaduje instalaci rozšíření diagnostiky. Několik dalších odstavců již neplatí, pokud vytvoříte aplikaci automatického škálování pomocí nového kanálu. Příklad šablon Azure, které byly převedeny na použití kanálu https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscalehostitele je k dispozici zde: . 
  
    Použití metrik založených na hostiteli pro automatické škálování je lepší z následujících důvodů:
  
  * Méně pohyblivých částí, protože není třeba instalovat žádná rozšíření diagnostiky.
  * Jednodušší šablony. Stačí přidat pravidla automatického škálování přehledů do existující šablony škálovací sady.
  * Spolehlivější vytváření sestav a rychlejší spouštění nových virtuálních připojení.
    
    Jediným důvodem, proč můžete chtít nadále používat diagnostické rozšíření je, pokud potřebujete diagnostiku paměti vykazování nebo škálování. Metriky založené na hostiteli nehlásí paměť.
    
    S ohledem na to postupujte podle zbytku tohoto článku, pokud používáte diagnostická rozšíření pro automatické škálování.
    
    Automatické škálování ve Správci prostředků Azure může fungovat (ale už nemusí) pomocí rozšíření virtuálního počítače s názvem Rozšíření diagnostiky. Vyzařuje data o výkonu do účtu úložiště, který definujete v šabloně. Tato data se pak agreguje službou Azure Monitor.
    
    Pokud služba Insights nemůže číst data z virtuálních společností, má vám poslat e-mail. Například dostanete e-mail, pokud virtuální chod jsou dole. Nezapomeňte zkontrolovat svůj e-mail na e-mailovou adresu, kterou jste zadali při vytváření účtu Azure.
    
    Můžete se také podívat na data sami. Podívejte se na účet úložiště Azure pomocí cloud exploreru. Například pomocí [Visual Studio Cloud Explorer](https://visualstudiogallery.msdn.microsoft.com/aaef6e67-4d99-40bc-aacf-662237db85a2), přihlaste se a vyberte předplatné Azure, které používáte. Potom se podívejte na název účtu úložiště diagnostiky, na který odkazuje definice rozšíření Diagnostika v šabloně nasazení.
    
    ![Průzkumník cloudu][explorer]
    
    Zobrazí se spousta tabulek, kde se ukládají data z každého virtuálního aplikace. Vezmeme-li Linux a metriku procesoru jako příklad, podívejte se na nejnovější řádky. Průzkumník cloudu sady Visual Studio podporuje dotazovací jazyk, takže můžete spustit dotaz. Můžete například spustit dotaz pro "Časové razítko gt datetime'2016-02-02T21:20:00Z'", abyste se ujistili, že získáte nejnovější události. Časové pásmo odpovídá Času UTC. Odpovídají data, která tam vidíte, pravidlům škálování, která jste nastavili? V následujícím příkladu procesor pro počítač 20 začal zvyšovat na 100 % za posledních pět minut.
    
    ![Tabulky úložišť][tables]
    
    Pokud data není k dispozici, znamená to, že problém je s rozšíření diagnostiky spuštěné ve virtuálních discích. Pokud data jsou k dispozici, znamená to, že je problém s pravidly škálování nebo se službou Insights. Zkontrolujte [stav Azure](https://azure.microsoft.com/status/).
    
    Pokud máte problémy s automatickým škálování, můžete vyzkoušet následující zdroje, pokud máte stále problémy s automatickým škálování: 
    * Přečtěte si fóra na [MSDN](https://social.msdn.microsoft.com/forums/azure/home?forum=WAVirtualMachinesforWindows), nebo [zásobníku přetečení](https://stackoverflow.com/questions/tagged/azure) 
    * Protokolovat volání podpory. Buďte připraveni sdílet šablonu a zobrazení údajů o výkonu.

[audit]: ./media/virtual-machine-scale-sets-troubleshoot/image3.png
[explorer]: ./media/virtual-machine-scale-sets-troubleshoot/image1.png
[tables]: ./media/virtual-machine-scale-sets-troubleshoot/image4.png

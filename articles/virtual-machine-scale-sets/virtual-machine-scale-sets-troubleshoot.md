---
title: Řešení potíží s škálování s sady škálování virtuálního počítače | Microsoft Docs
description: Řešení potíží s škálování s sady škálování virtuálního počítače. Pochopení typické problémy vzniklé a způsob jejich řešení.
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: c7d87b72-ee24-4e52-9377-a42f337f76fa
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: windows
ms.devlang: na
ms.topic: article
ms.date: 11/16/2017
ms.author: negat
ms.openlocfilehash: ea634ea8bcb4fed1ed63dc8d1e17d215a00758c6
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
ms.locfileid: "34161018"
---
# <a name="troubleshooting-autoscale-with-virtual-machine-scale-sets"></a>Řešení potíží s škálování s sady škálování virtuálního počítače
**Problém** – jste vytvořili automatické škálování infrastruktury v správce Azure Resource Manager pomocí sady škálování virtuálního počítače – například nasazením šablonu tohoto typu: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale – máte definované pravidel škálování a vyhovující postup, s výjimkou ne vás kolik zatížení vložíte na virtuálních počítačích, nepodporuje automatické škálování.

## <a name="troubleshooting-steps"></a>Postup při řešení potíží
Některé věci vzít v úvahu, patří:

* Kolik Vcpu každý virtuální počítač nemá a jsou načítání každý virtuální procesor?
  Šablona Azure Quickstart, předchozí ukázka má do_work.php skript, který načte jeden virtuální procesor. Pokud používáte virtuálního počítače větší než velikost virtuálního počítače virtuální jeden procesor jako Standard_A1 nebo D1, musíte spustit tento zatížení vícekrát. Zkontrolujte, kolik Vcpu pro virtuální počítače kontrolou [velikosti pro systém Windows virtuálních počítačů v Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* Kolik virtuálních počítačů v sadě škálování virtuálního počítače, můžete pracujeme na každé z nich?
  
    Škálováním na více systémů událostí jen dojde, když průměrné využití procesoru mezi **všechny** virtuální počítače ve škálovací sadě překračuje prahovou hodnotu, časem interní definované v pravidlech automatického škálování.
* Vynechali jste žádné události škálování?
  
    Zkontrolujte, zda že protokolů auditu na portálu Azure pro škálování události. Možná se škálování nahoru a vertikálně, nebyla provedena. Můžete filtrovat podle "Škálování".
  
    ![Protokoly auditu][audit]
* Jsou vaše škálování in a škálování prahové hodnoty dostatečně neliší?
  
    Předpokládejme, že nastavíte pravidlo pro horizontální rozšíření kapacity, když průměrné využití procesoru je větší než 50 % více než pět minut a měřítka ve při průměrné využití procesoru je menší než 50 %. Toto nastavení by způsobilo "flapping" problém, když využití procesoru je blízko prahovou hodnotu, s akcí škálování neustále zvýšení a snížení velikosti sady. Z důvodu toto nastavení škálování služby se pokusí zabránit "netřepotá", který můžete manifest jako není škálování. Proto ujistěte se, že vaše Škálováním na více systémů a škálování prahové hodnoty jsou dostatečně neliší umožňující nějaké místo mezi škálování.
* Můžete psát vlastní šablonu JSON?
  
    Je snadné uděláte chyby, takže spuštění pomocí šablony jako ten, nad kterou je ověřené k práci a ujistěte se, malé přírůstkové změny. 
* Můžete je ručně škálovat, příchozí nebo odchozí?
  
    Zkuste opětovného nasazení, které škálovací sady virtuálních počítačů prostředků o kapacitě různých"" nastavení ručně změnit počet virtuálních počítačů. Zde jsou k šabloně příklad: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing – možná budete muset upravit šablonu a ujistit se, že má stejnou velikost počítače jako sady škálování používá. Pokud počet virtuálních počítačů můžete úspěšně ručně změnit, pak víte, že problém je izolovaný chcete používat automatické škálování.
* Zkontrolujte vaše Microsoft.Compute/virtualMachineScaleSet a Microsoft.Insights prostředky v [Průzkumníka prostředků Azure](https://resources.azure.com/)
  
    Průzkumníka prostředků Azure je nepostradatelné řešení potíží nástroj, který ukazuje stav svých prostředků Azure Resource Manager. Klikněte na vaše předplatné a podívejte se na skupinu prostředků, řešení potíží. V části poskytovatele výpočetních prostředků podívejte se na škálovací sadu virtuálních počítačů jste vytvořili a zkontrolujte zobrazení Instance, která ukazuje stav nasazení. Zkontrolujte také, zobrazení instance virtuálních počítačů v sadě škálování virtuálního počítače. Pak přejděte do zprostředkovatele prostředků Microsoft.Insights a zkontrolujte vzhled pravidel škálování.
* Je rozšíření diagnostiky práce a generování údaje o výkonu?
  
    **Aktualizace:** Azure škálování je vylepšená tak, aby pomocí kanálu metriky na hostiteli, který už vyžaduje rozšíření diagnostiky k instalaci. Další několik odstavců přestanou platit, pokud vytvoříte automatické škálování aplikace pomocí nový kanál. Příklad šablony Azure, které byly převedeny na použití kanálu hostitele je k dispozici zde: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale. 
  
    Použití metriky na hostiteli pro škálování je lepší z následujících důvodů:
  
  * Méně přesunutí části jako žádná rozšíření diagnostiky je potřeba nainstalovat.
  * Jednodušší šablony. Stačí přidáte pravidla škálování Statistika do stávající šablony sady škálování.
  * Vytváření sestav spolehlivější a rychlejší spouštění nové virtuální počítače.
    
    Pouze z důvodů, které chcete používat příponu diagnostiky je, pokud je třeba paměť diagnostiky reporting/škálování. Metriky na hostiteli není sestavy paměti.
    
    Si uvědomit pouze podle zbývající části tohoto článku Pokud používáte rozšíření diagnostiky pro vaše automatické škálování.
    
    Škálování ve službě Správce prostředků Azure můžete pracovat (ale už má k) prostřednictvím virtuálního počítače rozšíření nazývá rozšíření diagnostiky. Ho vysílá údaje o výkonu pro účet úložiště, který definujete v šabloně. Tato data se shromažďují potom pomocí služby Azure monitorování.
    
    Pokud služba statistika nelze číst data z virtuálních počítačů, by mělo e-mailem. Například e-mailu získáte, pokud jsou virtuální počítače vypnuté. Ujistěte se, že zkontrolujte e-mailu, v e-mailovou adresu, kterou jste zadali při vytvoření účtu Azure.
    
    Můžete také prohlédnout data sami. Podívejte se na účtu úložiště Azure pomocí Průzkumníku cloudu. Například pomocí [cloudu Průzkumníka Visual Studio](https://visualstudiogallery.msdn.microsoft.com/aaef6e67-4d99-40bc-aacf-662237db85a2), přihlaste se a vyberte předplatné Azure, které používáte. Potom podívejte se na název účtu úložiště diagnostiky, kterou se odkazuje v definici rozšíření diagnostiky do šablony nasazení.
    
    ![Průzkumník cloudu][explorer]
    
   Zobrazí bunch tabulek, které ukládají data z jednotlivých virtuálních počítačů. Převádět Linux a metriku procesoru jako příklad, podívejte se na poslední řádky. Průzkumník cloudu Visual Studio podporuje dotazovací jazyk, takže je možné spustit dotaz. Například můžete spuštěním dotazu pro "časové razítko gt data a času ' 2016-02-02T21:20:00Z'" a ujistěte se, získat nejnovější události. Odpovídá časové pásmo UTC. Podporuje dat, které vidíte, že existuje odpovídají pravidlům škálování při nastavování? V následujícím příkladu procesoru pro počítač 20 spuštění, zvýšit na 100 % za posledních pět minut.
    
    ![Úložiště tabulek][tables]
    
    Pokud není data, znamená to, že je problém s příponou diagnostiky spuštěna ve virtuálních počítačích. Pokud data existuje, znamená to, jestli existuje problém s vaší pravidel škálování, nebo se službou Statistika. Zkontrolujte [Azure stav](https://azure.microsoft.com/status/).
    
    Jakmile jste byl pomocí těchto kroků, pokud máte pořád problémy škálování, zkuste následující prostředky: 
    * Přečtěte si ve fórech [MSDN](https://social.msdn.microsoft.com/forums/azure/home?forum=WAVirtualMachinesforWindows), nebo [přetečení zásobníku](http://stackoverflow.com/questions/tagged/azure) 
    * Přihlaste se volání podpory. Připravte se na sdílet šablony a zobrazení dat výkonu.

[audit]: ./media/virtual-machine-scale-sets-troubleshoot/image3.png
[explorer]: ./media/virtual-machine-scale-sets-troubleshoot/image1.png
[tables]: ./media/virtual-machine-scale-sets-troubleshoot/image4.png

---
title: Řešení potíží s automatickým Škálováním se Škálovacími sadami virtuálních počítačů | Dokumentace Microsoftu
description: Řešení potíží s automatickým Škálováním se Škálovacími sadami virtuálních počítačů. Vysvětlení typické problémy a jejich řešení.
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
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
ms.author: manayar
ms.openlocfilehash: e4b1153e46625f88c717fd9b7a5336ffe4ca7f6a
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2018
ms.locfileid: "50739545"
---
# <a name="troubleshooting-autoscale-with-virtual-machine-scale-sets"></a>Řešení potíží s automatickým Škálováním se Škálovacími sadami virtuálních počítačů
**Problém** – jste vytvořili automatické škálování infrastruktury v Azure Resource Manageru pomocí škálovací sady virtuálních počítačů – například tím, že nasazení šablony, jako je ten: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale – máte vaše škálovací pravidla definovaná a funguje to fungovalo, s výjimkou ne důležité, jakým je zatížením vložíte na virtuálních počítačích, nebude automatického škálování.

## <a name="troubleshooting-steps"></a>Postup při řešení potíží
Některé věci k uvážení patří:

* Kolik virtuálních procesorů je každý virtuální počítač a se načítají každý virtuální procesor?
  Předchozí ukázkové šablony Azure pro rychlý Start obsahuje do_work.php skript, který načte jeden virtuální procesor. Pokud používáte virtuální počítač větší než velikost jeden virtuální procesor virtuálního počítače jako Standard_A1 nebo D1, musíte spustit toto zatížení více než jednou. Zkontrolujte, kolik virtuálních procesorů pro virtuální počítače kontrolou [velikosti pro Windows virtual machines v Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* Kolik virtuálních počítačů ve škálovací sadě virtuálních počítačů to děláš práci na každé z nich?
  
    Horizontální navýšení kapacity události pouze probíhá, když průměrné využití procesoru napříč **všechny** virtuálních počítačů ve škálovací sadě překračuje prahovou hodnotu, časem interní definované v pravidlech automatického škálování.
* Zmeškali jste všechny události škálování?
  
    Kontrola protokolů auditu na webu Azure Portal pro škálovací události. Možná škálovací došlo nahoru a vertikální snížení, která nebyla dodržena. Můžete filtrovat podle "Škálovací".
  
    ![Protokoly auditu][audit]
* Jsou dostatečně neliší stanovených mezních hodnot škálování na méně instancí a horizontální navýšení kapacity?
  
    Předpokládejme, že nastavíte pravidlo pro horizontální navýšení kapacity, když průměrné využití procesoru je větší než 50 % více než pět minut a škálování v když průměrné využití procesoru je menší než 50 %. Toto nastavení by způsobilo "flapping" problém, když využití procesoru blíží prahové hodnoty, s akcí škálování neustále zvyšování a snižování velikosti sady. Z důvodu tohoto nastavení automatického škálování služby se pokusí o zabránit "netřepotá", který může manifestovat jako není škálování. Proto se ujistěte, že stanovených mezních hodnot horizontální navýšení kapacity a škálování na méně instancí se dostatečně neliší umožňující nějaké místo mezi škálování.
* Napsali jste vlastní šablony JSON?
  
    Je snadné dělat chyby, proto začněte šablonou jako ten výše je prověřené práci a ujistěte se, malé přírůstkové změny. 
* Můžete je ručně škálovat snížení nebo navýšení kapacity?
  
    Pokusí se znovu nasadit, který škálovací sady virtuálních počítačů resource s jinou "kapacitu" nastavení ručně změnit počet virtuálních počítačů. Tady je Ukázková šablona: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing – možná budete muset upravit šablonu, kterou chcete Ujistěte se, že má stejnou velikost počítače, protože Škálovací sada používá. Pokud se počet virtuálních počítačů může úspěšně změnit ručně, pak víte, že problém je izolovaná na automatické škálování.
* Zkontrolujte vaše Microsoft.Compute/virtualMachineScaleSet a prostředků Microsoft.Insights ve [Průzkumníku prostředků Azure](https://resources.azure.com/)
  
    Azure Resource Exploreru je nepostradatelným řešení problémů s nástrojem, který vám ukáže stav svých prostředků Azure Resource Manageru. Klikněte na předplatné a podívejte se na skupinu prostředků, řešení potíží. V poskytovateli prostředků Compute podívejte se na škálovací sadu virtuálních počítačů jste vytvořili a zkontrolujte zobrazení Instance, která zobrazuje stav nasazení. Zkontrolujte taky, zobrazení instance virtuálních počítačů ve škálovací sadě virtuálních počítačů. Potom přejděte do poskytovatele prostředků Microsoft.Insights a zkontrolujte, že pravidla automatického škálování tak, jak.
* Diagnostické rozšíření je práce a generování dat výkonu?
  
    **Aktualizace:** automatické škálování v Azure je vylepšená použití kanálu metriky hostitele, který už nevyžaduje diagnostické rozšíření k instalaci. Pár odstavců dále už nebude platit, pokud vytvoříte aplikaci automatické škálování pomocí nový kanál. Příklad šablony Azure, které byly převedeny na použití kanálu hostitele je k dispozici zde: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale. 
  
    Pomocí automatického škálování využívající metriky hostitele je lepší z následujících důvodů:
  
  * Méně pohyblivých částí jako žádná rozšíření diagnostiky potřeba nainstalovat.
  * Jednodušší šablony. Stačí přidáte pravidla automatického škálování insights k existující šablony škálovací sady.
  * Vytváření sestav spolehlivější a rychlejší spouštění nových virtuálních počítačů.
    
    Pouze z důvodů, které můžete dál používat diagnostického rozšíření je, pokud je třeba paměť diagnostiky reporting/škálování. Metriky hostitele nehlásí paměti.
    
    To na paměti pouze postupujte podle zbývajících částí tohoto článku Pokud používáte rozšíření diagnostiky pro vaše automatické škálování.
    
    Automatické škálování v Azure Resource Manageru můžete pracovat (ale není k dispozici pro) pomocí virtuálního počítače s názvem rozšíření diagnostického rozšíření. Vysílá údaje o výkonu do účtu úložiště, které definujete v šabloně. Tato data pak agregují podle služby Azure Monitor.
    
    Pokud služba Insights nelze číst data z virtuálních počítačů, má k odeslání e-mailu. Například dostanete e-mail, pokud jsou virtuální počítače vypnuté. Ujistěte se, abyste zkontrolovali svůj e-mail na e-mailovou adresu, kterou jste zadali při vytváření účtu Azure.
    
    Můžete se také podívat na data sami. Podívejte se na účtu služby Azure storage pomocí Průzkumníka cloudu. Například použití [Visual Studio Cloud Explorer](https://visualstudiogallery.msdn.microsoft.com/aaef6e67-4d99-40bc-aacf-662237db85a2), přihlaste se a vyberte předplatné Azure, které používáte. Pak se podívejte na název účtu úložiště diagnostiky odkazuje v definici rozšíření diagnostiky do šablony nasazení.
    
    ![Průzkumník cloudu][explorer]
    
   Zobrazí spousta tabulek je právě ukládat data z každého virtuálního počítače. Vezměte Linuxu a metriky procesoru jako příklad a podívejte se na poslední řádky. Průzkumníka cloudu sady Visual Studio podporuje dotazovací jazyk, abyste mohli spustit dotaz. Například můžete spustit dotaz pro "časové razítko gt data a času" 2016-02-02T21:20:00Z "" k Ujistěte se, že získáte nejnovější události. Odpovídá časové pásmo UTC. Podporuje data, která se zobrazí, že existuje odpovídají pravidel škálování můžete nastavit? V následujícím příkladu procesor pro počítač 20 spuštěna, zvětšení na 100 % za posledních pět minut.
    
    ![Úložiště tabulek][tables]
    
    Pokud není data, znamená to, že je problém s diagnostického rozšíření ve virtuálních počítačích. Pokud jsou data existuje, znamená to, že je problém pravidlům škálování nebo službou Insights. Zkontrolujte [stav Azure](https://azure.microsoft.com/status/).
    
    Jakmile jste tyto kroky, pokud stále máte potíže s automatickým Škálováním můžete vyzkoušet následující prostředky: 
    * Přečtěte si fóra na [MSDN](https://social.msdn.microsoft.com/forums/azure/home?forum=WAVirtualMachinesforWindows), nebo [přetečení zásobníku](http://stackoverflow.com/questions/tagged/azure) 
    * Volání podpory protokolu. Buďte připraveni sdílet šablony a zobrazení dat výkonu.

[audit]: ./media/virtual-machine-scale-sets-troubleshoot/image3.png
[explorer]: ./media/virtual-machine-scale-sets-troubleshoot/image1.png
[tables]: ./media/virtual-machine-scale-sets-troubleshoot/image4.png

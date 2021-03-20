---
title: Řešení potíží s AutoScale pomocí Virtual Machine Scale Sets
description: Řešení potíží s AutoScale pomocí Virtual Machine Scale Sets. Pochopení typických problémů a jejich řešení.
author: avirishuv
ms.author: avverma
ms.topic: troubleshooting
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 06/25/2020
ms.reviwer: jushiman
ms.custom: avverma
ms.openlocfilehash: 11302c301bee466f678d544d0c4838c39cec9c8e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91818544"
---
# <a name="troubleshooting-autoscale-with-virtual-machine-scale-sets"></a>Řešení potíží s automatickým škálováním se škálovacími sadami virtuálních počítačů
**Problém** – pomocí služby Virtual Machine Scale Sets jste v Azure Resource Manager vytvořili infrastrukturu automatického škálování – například nasazením šablony, jako je tato: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale  – máte definovaná pravidla škálování a funguje Skvělé, ale bez ohledu na to, kolik dat jste do virtuálních počítačů umístili, nefunguje automatické škálování.

## <a name="troubleshooting-steps"></a>Postup při řešení potíží
Mezi ty, které je potřeba vzít v úvahu, patří:

* Kolik vCPU má každý virtuální počítač a načítají se jednotlivé vCPUy?
  Předchozí Ukázková šablona Azure pro rychlý Start obsahuje skript do_work. php, který načte jeden vCPU. Pokud používáte virtuální počítač, který je větší než vCPU velikost virtuálního počítače, jako je například Standard_A1 nebo D1, je nutné spustit toto zatížení několikrát. Kontrola [velikosti virtuálních počítačů s Windows v Azure](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) pomocí kontroly počtu vCPU pro vaše virtuální počítače
* Kolik virtuálních počítačů v sadě škálování virtuálních počítačů je u každé z nich práce prováděno?
  
    Událost škálování na více instancí probíhá pouze v případě, že průměrný procesor napříč **všemi** virtuálními počítači v sadě škálování překračuje prahovou hodnotu v čase interní definovaném v pravidlech automatického škálování.
* Chybíš jste nějaké události škálování?
  
    Zkontrolujte protokoly auditu v Azure Portal pro události škálování. Možná jsme nastavili horizontální navýšení kapacity a horizontální snížení kapacity. Můžete filtrovat podle "Scale".
  
    ![Protokoly auditu][audit]
* Jsou prahové hodnoty škálování a škálování na více instancí dostatečně rozdílné?
  
    Předpokládejme, že jste nastavili pravidlo pro horizontální navýšení kapacity, pokud je průměrný procesor větší než 50% za pět minut a pokud chcete škálovat, v případě, že průměrná hodnota CPU je menší než 50%. Toto nastavení způsobí problém "přepíná", pokud je využití procesoru blízko prahové hodnoty, a akce škálování neustále zvětšuje a zmenšuje velikost sady. Kvůli tomuto nastavení se služba automatického škálování pokusí zabránit "přepíná", což může být v manifestu Neškálovatelné. Proto se ujistěte, že vaše prahové hodnoty škálování a škálování na více instancí jsou dostatečně rozdílné, což umožňuje určit místo mezi škálováním.
* Napsali jste vlastní šablonu JSON?
  
    Je snadné provést chyby, takže začněte se šablonou, jako je ta, která je výše, která je prověřena pro práci, a udělat malé přírůstkové změny. 
* Můžete ruční horizontální navýšení nebo zmenšení kapacity?
  
    Zkuste znovu nasadit prostředek sady škálování virtuálních počítačů s jiným nastavením Capacity, aby se počet virtuálních počítačů změnil ručně. Příklad šablony je: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing – možná budete muset šablonu upravit, abyste se ujistili, že má stejnou velikost počítače jako vaše sada škálování. Pokud se počet virtuálních počítačů dá úspěšně změnit ručně, pak víte, že je problém izolovaný na automatické škálování.
* Podívejte se na prostředky Microsoft. COMPUTE/virtualMachineScaleSet a Microsoft. Insights v [Azure Resource Explorer](https://resources.azure.com/)
  
    Azure Resource Explorer je nepostradatelný nástroj pro řešení potíží, který vám ukáže stav vašich Azure Resource Managerch prostředků. Klikněte na své předplatné a podívejte se na skupinu prostředků, kterou řešíte. Ve zprostředkovateli prostředků COMPUTE se podívejte na sadu škálování virtuálního počítače, kterou jste vytvořili, a zkontrolujte zobrazení instance, které zobrazuje stav nasazení. Zkontrolujte také zobrazení instancí virtuálních počítačů v sadě škálování virtuálního počítače. Pak přejdete do poskytovatele prostředků Microsoft. Insights a zkontrolujete, jestli pravidla automatického škálování vypadají správně.
* Funguje diagnostické rozšíření a generuje data o výkonu?
  
    **Aktualizace:** Automatické škálování Azure se rozšířilo na použití kanálu metriky založeného na hostiteli, který už nevyžaduje instalaci diagnostického rozšíření. Další z následujících odstavců již nejsou k dispozici, pokud vytvoříte aplikaci automatického škálování pomocí nového kanálu. Příklad šablon Azure, které se převedly na použití kanálu hostitele, je k dispozici zde: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale . 
  
    Použití metriky hostitele pro automatické škálování je lepší z následujících důvodů:
  
  * Méně přesouvaných částí, protože není nutné instalovat žádné diagnostické rozšíření.
  * Jednodušší šablony. Stačí přidat pravidla automatického škálování Insights do existující šablony sady škálování.
  * Spolehlivější generování sestav a rychlejší spouštění nových virtuálních počítačů.
    
    V případě, že budete potřebovat vytváření sestav a škálování diagnostiky paměti, stačí, když budete chtít dál používat diagnostické rozšíření. Metriky založené na hostiteli nehlásí paměť.
    
    V takovém případě použijte pouze zbytek tohoto článku, pokud používáte diagnostické rozšíření pro automatické škálování.
    
    Automatické škálování v Azure Resource Manager může fungovat (ale už se k tomu nemusí) pomocí rozšíření virtuálního počítače, které se nazývá diagnostické rozšíření. Vygeneruje údaje o výkonu do účtu úložiště, který definujete v šabloně. Tato data se pak agreguje pomocí služby Azure Monitor.
    
    Pokud služba Insights nemůže číst data z virtuálních počítačů, doporučujeme vám poslat e-mail. Můžete například získat e-mail, pokud jsou virtuální počítače mimo provoz. Nezapomeňte zkontrolovat e-mail na e-mailovou adresu, kterou jste zadali při vytváření účtu Azure.
    
    Můžete se také podívat na data sami. Podívejte se na účet služby Azure Storage pomocí Průzkumníka cloudu. Například pomocí [Průzkumníka cloudu sady Visual Studio](https://visualstudiogallery.msdn.microsoft.com/aaef6e67-4d99-40bc-aacf-662237db85a2)se přihlaste a vyberte předplatné Azure, které používáte. Pak se podívejte na název účtu úložiště diagnostiky, na který odkazuje definice rozšíření diagnostiky v šabloně nasazení.
    
    ![Průzkumník cloudu][explorer]
    
    Zobrazí se spousta tabulek, ve kterých se ukládají data z každého virtuálního počítače. Pokud chcete jako příklad využít Linux a metriku procesoru, podívejte se na nejnovější řádky. Visual Studio Cloud Explorer podporuje dotazovací jazyk, takže můžete spustit dotaz. Můžete například spustit dotaz pro "timestamp gt DateTime" 2016-02-02T21:20:00Z "", abyste se ujistili, že jste získali nejaktuálnější události. Časové pásmo odpovídá času UTC. Odpovídají data zobrazená v části pravidla škálování, která jste nastavili? V následujícím příkladu se procesor pro počítač 20 začal zvyšovat na 100% za posledních pět minut.
    
    ![Tabulky úložiště][tables]
    
    Pokud tato data nejsou k dispozici, znamená to, že se jedná o problém s diagnostickým rozšířením spuštěným ve virtuálních počítačích. Pokud jsou tam tato data, znamená to, že došlo k potížím s pravidly škálování nebo se službou Insights. Ověřte [stav Azure](https://azure.microsoft.com/status/).
    
    Pokud máte i nadále problémy s automatickém škálováním, můžete po provedení těchto kroků vyzkoušet následující zdroje: 
    * Přečtěte si fóra na [stránce s dotazem&Microsoft Q](/answers/topics/azure-virtual-machines.html)nebo [přetečení zásobníku](https://stackoverflow.com/questions/tagged/azure) . 
    * Protokoluje volání podpory. Připravte se na sdílení šablony a zobrazení údajů o výkonu.

[audit]: ./media/virtual-machine-scale-sets-troubleshoot/image3.png
[explorer]: ./media/virtual-machine-scale-sets-troubleshoot/image1.png
[tables]: ./media/virtual-machine-scale-sets-troubleshoot/image4.png

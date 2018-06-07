---
title: Sadách škálování virtuálních počítačů škálování na portálu Azure | Microsoft Docs
description: Postup vytvoření pravidla automatického škálování pro škálování virtuálních počítačů se nastaví na portálu Azure
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 88886cad-a2f0-46bc-8b58-32ac2189fc93
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2018
ms.author: iainfou
ms.openlocfilehash: c9386f7dd0ba390a5f089be058c7f3edd6e33cf9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34652368"
---
# <a name="automatically-scale-a-virtual-machine-scale-set-in-the-azure-portal"></a>Automatické škálování škálování virtuálních počítačů, nastavte na portálu Azure
Při vytváření škálovací sady definujete počet instancí virtuálních počítačů, které chcete spouštět. S měnícími se požadavky na aplikaci můžete počet instancí virtuálních počítačů automaticky zvyšovat nebo snižovat. Možnost automatického škálování umožňuje držet krok s požadavky zákazníků nebo reagovat na změny výkonu aplikace v průběhu jejího životního cyklu.

Tento článek ukazuje, jak vytvořit na portálu Azure, který sledovat výkon instancí virtuálního počítače ve škálovací sadě pravidel škálování. Tato pravidla škálování zvyšte nebo snižte počet instancí virtuálního počítače v reakci na tyto metriky výkonu. Můžete také provést tyto kroky s [prostředí Azure PowerShell](tutorial-autoscale-powershell.md) nebo [Azure CLI 2.0](tutorial-autoscale-cli.md).


## <a name="prerequisites"></a>Požadavky
K vytvoření pravidla automatického škálování, budete potřebovat existujícího virtuálního počítače sady škálování. Můžete vytvořit s měřítkem [portál Azure](quick-create-portal.md), [prostředí Azure PowerShell](quick-create-powershell.md), nebo [Azure CLI 2.0](quick-create-cli.md).


## <a name="create-a-rule-to-automatically-scale-out"></a>Vytvořit pravidlo, které automaticky škálovat
Pokud se požadavky na vaši aplikaci zvýší, zvýší se i zatížení instancí virtuálních počítačů ve škálovací sadě. Pokud je toto zvýšené zatížení konzistentní, a nejedná se pouze o krátkou poptávku, můžete nakonfigurovat pravidla automatického škálování pro zvýšení počtu instancí virtuálních počítačů ve škálovací sadě. Po vytvoření těchto instancí virtuálních počítačů a nasazení aplikací do nich začne škálovací sada distribuovat provoz prostřednictvím nástroje pro vyrovnávání zatížení. Můžete řídit, které metriky se mají monitorovat, například CPU nebo disk, jak dlouho musí zatížení aplikace dosahovat dané prahové hodnoty a kolik instancí virtuálních počítačů se má do škálovací sady přidat.

1. Otevřete Azure portálu a vyberte možnost **skupiny prostředků** z nabídky na levé straně řídicího panelu.
2. Vyberte skupinu prostředků, která obsahuje škálovací sadu, a potom vyberte vaše sad škálování ze seznamu prostředků.
3. Zvolte **škálování** z nabídky na levé straně měřítka nastavit okno. Kliknutím na tlačítko **povolit škálování**:

    ![Povolit automatické škálování na portálu Azure](media/virtual-machine-scale-sets-autoscale-portal/enable-autoscale.png)

4. Zadejte název pro nastavení, jako například *škálování*, pak vyberte možnost **přidat pravidlo**.

5. Umožňuje vytvořit pravidlo, které zvýší se počet instancí virtuálního počítače v škálování nastavená, pokud průměrná zatížení procesoru je větší než 70 % po dobu 10 minut. Pokud toto pravidlo aktivuje, je 20 % zvýšit počet instancí virtuálního počítače. V sady škálování s malým počtem instancí virtuálních počítačů, můžete nastavit **operace** k *zvýšení počtu podle* a pak zadejte *1* nebo *2* pro *Instance počet*. V sady škálování s velký počet instancí virtuálního počítače, zvýšení o 10 % nebo 20 % může být vhodnější instance virtuálních počítačů.

    Zadejte následující nastavení pro pravidla:
    
    | Parametr              | Vysvětlení                                                                                                         | Hodnota          |
    |------------------------|---------------------------------------------------------------------------------------------------------------------|----------------|
    | *Čas agregace*     | Definuje způsob agregace shromážděných metrik pro účely analýzy.                                                | Průměr        |
    | *Název metriky*          | Metrika výkonu, která se má monitorovat a na kterou se mají použít akce škálovací sady.                                                   | Procento CPU |
    | *Čas intervalem statistiky* | Definuje, jak by měla být agregován shromažďovat metriky v každé časovým intervalem pro analýzu.                             | Průměr        |
    | *Operátor*             | Operátor sloužící k porovnání dat metriky s prahovou hodnotou.                                                     | Více než   |
    | *Prahová hodnota*            | Procento, které způsobí, že pravidlo škálování akci aktivovat.                                                 | 70             |
    | *Doba trvání*             | Doba, která se monitoruje před porovnáním metrik a prahových hodnot.                                   | 10 minut     |
    | *Operace*            | Určuje, zda byly sadou škálování by měl škálovat nahoru nebo dolů, když se pravidlo vztahuje a jaké přírůstku                        | Zvýšit procento o |
    | *Počet instancí*       | Procento instancí virtuálních počítačů, které se mají po aktivaci pravidla změnit.                                            | 20             |
    | *Cool dolů (minuty)*  | Doba, po kterou se má počkat před opětovným použitím pravidla, aby akce automatického škálování měly dostatek času se projevit. | 5 minut      |

    Následující příklady ukazují pravidlo vytvořené na portálu Azure, který odpovídá tato nastavení:

    ![Vytvořit pravidlo automatického škálování pro zvýšení počtu instancí virtuálních počítačů](media/virtual-machine-scale-sets-autoscale-portal/rule-increase.png)

6. Chcete-li vytvořit pravidlo, vyberte **přidat**


## <a name="create-a-rule-to-automatically-scale-in"></a>Vytvořit pravidlo, které automaticky škálovat v
Večer nebo o víkendu se požadavky na vaši aplikaci můžou snížit. Pokud je toto snížené zatížení po určitou dobu konzistentní, můžete nakonfigurovat pravidla automatického škálování pro snížení počtu instancí virtuálních počítačů ve škálovací sadě. Tato akce horizontálního snížení kapacity sníží náklady na provoz škálovací sady, protože budete spouštět pouze takový počet instancí, který je potřeba ke zpracování aktuálních požadavků.

1. Zvolit **přidat pravidlo** znovu.
2. Vytvořte pravidlo, které sníží se počet instancí virtuálního počítače v škálování nastavená, pokud průměrná zatížení procesoru pak klesne pod 30 % po dobu 10 minut. Pokud toto pravidlo aktivuje, je počet instancí virtuálního počítače snížena o 20 %.

    Stejně jako u předchozí pravidlo, použijte stejný postup. Upravte následující nastavení pro pravidla:
    
    | Parametr              | Vysvětlení                                                                                                          | Hodnota          |
    |------------------------|----------------------------------------------------------------------------------------------------------------------|----------------|
    | *Operátor*             | Operátor sloužící k porovnání dat metriky s prahovou hodnotou.                                                      | Méně než   |
    | *Prahová hodnota*            | Procento, které způsobí, že pravidlo škálování akci aktivovat.                                                 | 30             |
    | *Operace*            | Určuje, zda byly sadou škálování by měl škálovat nahoru nebo dolů, když se pravidlo vztahuje a jaké přírůstku                         | Snížit procento o |
    | *Počet instancí*       | Procento instancí virtuálních počítačů, které se mají po aktivaci pravidla změnit.                                             | 20             |

3. Chcete-li vytvořit pravidlo, vyberte **přidat**


## <a name="define-autoscale-instance-limits"></a>Definovat limity škálování instance
Váš profil škálování musí definovat minimální, maximální a výchozí počet instancí virtuálního počítače. Pokud jsou použity pravidel škálování, ujistěte se, že vám horizontální rozšíření kapacity překračuje maximální počet instancí, nebo určený počet číslic v nad rámec minimální instancí těchto omezení instance.

1. Nastavte následující instance omezení:

    | Minimální | Maximum | Výchozí|
    |---------|---------|--------|
    | 2       | 10      | 2      |

2. Chcete-li použít automatické škálování pravidla a omezení instance, vyberte **Uložit**.


## <a name="monitor-number-of-instances-in-a-scale-set"></a>Monitorování počtu instancí v sadě škálování
Pokud chcete zobrazit počet a stav instancí virtuálních počítačů, vyberte **instance** z nabídky na levé straně měřítka nastavit okno. Stav označuje, zda instance virtuálního počítače *vytváření* měřítka nastavit automaticky horizontálně navýší kapacitu, nebo je *odstranění* jako měřítka automaticky přizpůsobí v.

![Zobrazit seznam instancí virtuálních počítačů sady škálování](media/virtual-machine-scale-sets-autoscale-portal/view-instances.png)


## <a name="autoscale-based-on-a-schedule"></a>Automatické škálování podle plánu
V předchozích příkladech automaticky škálovat škálování nastavit příchozí nebo odchozí metriky základní hostitele, jako je například využití procesoru. Můžete také vytvořit pravidla automatické škálování podle plánů. Tato pravidla na základě plánu umožňují automaticky škálovat počet instancí virtuálního počítače před očekávaný nárůst vyžádání aplikace, jako je základní pracovní hodiny a poté automaticky škálovat počet instancí v čase, který předpokládáte méně vyžádání, jako je například víkendu.

1. Zvolte **škálování** z nabídky na levé straně měřítka nastavit okno. Chcete-li odstranit existující pravidla škálování vytvořená v předchozích příkladech, zvolte ikonu koše.

    ![Odstraňte existující pravidla automatického škálování](media/virtual-machine-scale-sets-autoscale-portal/delete-rules.png)

2. Zvolit **přidat podmínku škálování**. Vyberte ikonu tužky vedle názvu pravidla a zadejte název, jako *škálovat během každý pracovní den*.

    ![Přejmenování výchozí pravidlo automatického škálování](media/virtual-machine-scale-sets-autoscale-portal/rename-rule.png)

3. Vyberte přepínač na **škálování na konkrétní instanci počet**.
4. Škálování počtu instancí, zadejte *10* jako počet instancí.
5. Zvolte **opakujte konkrétní dny** pro **plán** typu.
6. Vyberte všechny pracovní dny, od pondělí do pátku.
7. Zvolte odpovídající časové pásmo, a poté zadejte **počáteční čas** z *09:00*.
8. Zvolit **přidat podmínku škálování** znovu. Opakujte tento postup vytvoření plánu s názvem *škálování v průběhu večer* , lze škálovat na *3* instance, se opakuje každý den v týdnu a spustí na *18:00*.
9. Chcete-li použít pravidla na základě plánu škálování, vyberte **Uložit**.

    ![Vytvoření pravidla automatického škálování, které škálování podle plánu](media/virtual-machine-scale-sets-autoscale-portal/schedule-autoscale.PNG)

Pokud chcete zobrazit, jak se používají pravidel škálování, vyberte **historie spouštění** v horní části **škálování** okno. Graf a události seznamu zobrazí aktivační událost pravidel škálování a číslo instance virtuálních počítačů ve vaší škálování nastavena zvýšení nebo snížení.


## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak používat automatické škálování pravidla můžete škálovat horizontálně a zvýšit nebo snížit *číslo* instance virtuálních počítačů ve vaší škálování nastavit. Můžete taky škálovat svisle zvýšení nebo snížení instance virtuálního počítače *velikost*. Další informace najdete v tématu [svislé škálování s sady škálování virtuálního počítače](virtual-machine-scale-sets-vertical-scale-reprovision.md).

Informace o tom, jak spravovat instancím virtuálních počítačů najdete v tématu [sadách škálování virtuálních počítačů spravovat pomocí prostředí Azure PowerShell](virtual-machine-scale-sets-windows-manage.md).

Zjistěte, jak vygenerovat upozornění, když vaše škálování pravidla aktivační událost, najdete v tématu [používat akce škálování k odesílání e-mailu a webhooku oznámení výstrah v monitorování Azure](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md). Můžete také [protokoly auditu použijte k odesílání e-mailu a webhooku oznámení výstrah v monitorování Azure](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md).

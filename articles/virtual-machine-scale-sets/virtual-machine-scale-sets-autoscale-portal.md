---
title: Automatické škálování škálovací sady virtuálních počítačů na webu Azure Portal | Dokumentace Microsoftu
description: Vytvoření pravidla automatického škálování pro škálovací virtuálních počítačů se nastaví na webu Azure Portal
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
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
ms.author: cynthn
ms.openlocfilehash: a93467404232b0fff51136cb7648d84a81165bdb
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38697955"
---
# <a name="automatically-scale-a-virtual-machine-scale-set-in-the-azure-portal"></a>Automatické škálování virtuálního počítače škálovací sady na webu Azure Portal
Při vytváření škálovací sady definujete počet instancí virtuálních počítačů, které chcete spouštět. S měnícími se požadavky na aplikaci můžete počet instancí virtuálních počítačů automaticky zvyšovat nebo snižovat. Možnost automatického škálování umožňuje držet krok s požadavky zákazníků nebo reagovat na změny výkonu aplikace v průběhu jejího životního cyklu.

Tento článek ukazuje, jak vytvořit pravidla automatického škálování na portálu Azure monitorovat výkon instancí virtuálních počítačů ve škálovací sadě. Tato pravidla automatického škálování zvýšit nebo snížit počet instancí virtuálních počítačů v reakci na tyto metriky výkonu. Můžete také dokončit tyto kroky [prostředí Azure PowerShell](tutorial-autoscale-powershell.md) nebo [příkazového řádku Azure CLI 2.0](tutorial-autoscale-cli.md).


## <a name="prerequisites"></a>Požadavky
K vytvoření pravidla automatického škálování, budete potřebovat existujícího virtuálního počítače škálovací sady. Můžete vytvořit škálovací sadu s [webu Azure portal](quick-create-portal.md), [prostředí Azure PowerShell](quick-create-powershell.md), nebo [příkazového řádku Azure CLI 2.0](quick-create-cli.md).


## <a name="create-a-rule-to-automatically-scale-out"></a>Vytvoření pravidla pro automatické horizontální navýšení kapacity
Pokud se požadavky na vaši aplikaci zvýší, zvýší se i zatížení instancí virtuálních počítačů ve škálovací sadě. Pokud je toto zvýšené zatížení konzistentní, a nejedná se pouze o krátkou poptávku, můžete nakonfigurovat pravidla automatického škálování pro zvýšení počtu instancí virtuálních počítačů ve škálovací sadě. Po vytvoření těchto instancí virtuálních počítačů a nasazení aplikací do nich začne škálovací sada distribuovat provoz prostřednictvím nástroje pro vyrovnávání zatížení. Můžete řídit, které metriky se mají monitorovat, například CPU nebo disk, jak dlouho musí zatížení aplikace dosahovat dané prahové hodnoty a kolik instancí virtuálních počítačů se má do škálovací sady přidat.

1. Otevřete Azure portal a vyberte **skupiny prostředků** z nabídky na levé straně řídicího panelu.
2. Vyberte skupinu prostředků, která obsahuje vaši škálovací sadu a pak zvolte ze seznamu prostředků škálovací sady.
3. Zvolte **škálování** z nabídky na levé straně stupnice nastavujete časové období. Vyberte tlačítko **povolit automatické škálování**:

    ![Povolit automatické škálování na portálu Azure portal](media/virtual-machine-scale-sets-autoscale-portal/enable-autoscale.png)

4. Zadejte název pro nastavení, jako například *automatického škálování*, vyberte možnost **přidat pravidlo**.

5. Teď vytvořte pravidlo, které zvýší počet instancí virtuálních počítačů ve škálovací sadě, pokud je průměrné zatížení CPU vyšší než 70 % po dobu 10 minut. Když se pravidlo aktivuje, počet instancí virtuálních počítačů se zvýší o 20 %. Škálovací sady s malý počet instancí virtuálních počítačů můžete nastavit **operace** k *zvýšit počet o* a pak zadejte *1* nebo *2* pro *počet instancí*. Ve škálovacích sadách s velký počet instancí virtuálních počítačů, zvýšení 10 % nebo 20 % instancí virtuálních počítačů může být vhodnější.

    Zadejte následující nastavení pro pravidlo:
    
    | Parametr              | Vysvětlení                                                                                                         | Hodnota          |
    |------------------------|---------------------------------------------------------------------------------------------------------------------|----------------|
    | *Časová agregace*     | Definuje způsob agregace shromážděných metrik pro účely analýzy.                                                | Průměr        |
    | *Název metriky*          | Metrika výkonu, která se má monitorovat a na kterou se mají použít akce škálovací sady.                                                   | Procento CPU |
    | *Statistika agregačního intervalu* | Definuje způsob agregace shromážděných metrik v každé časový interval pro analýzu.                             | Průměr        |
    | *– Operátor*             | Operátor sloužící k porovnání dat metriky s prahovou hodnotou.                                                     | Více než   |
    | *Prahová hodnota*            | Procentuální hodnotu, která způsobí aktivaci akce pravidlem automatického škálování.                                                 | 70             |
    | *Doba trvání*             | Doba, která se monitoruje před porovnáním metrik a prahových hodnot.                                   | 10 minut     |
    | *Operace*            | Definuje, jestli se škálovací sada by se měly škálovat nahoru nebo dolů po použití pravidla kapacita a jaké přírůstku                        | Zvýšit procento o |
    | *Počet instancí*       | Procento instancí virtuálních počítačů, které se mají po aktivaci pravidla změnit.                                            | 20             |
    | *Přestávka (minuty)*  | Doba, po kterou se má počkat před opětovným použitím pravidla, aby akce automatického škálování měly dostatek času se projevit. | 5 minut      |

    Následující příklady ukazují pravidlo vytvořené na portálu Azure portal, který odpovídá tato nastavení:

    ![Vytvoření pravidla automatického škálování pro zvýšení počtu instancí virtuálních počítačů](media/virtual-machine-scale-sets-autoscale-portal/rule-increase.png)

6. Chcete-li vytvořit pravidla, vyberte **přidat**


## <a name="create-a-rule-to-automatically-scale-in"></a>Vytvoření pravidla automatického škálování v
Večer nebo o víkendu se požadavky na vaši aplikaci můžou snížit. Pokud je toto snížené zatížení po určitou dobu konzistentní, můžete nakonfigurovat pravidla automatického škálování pro snížení počtu instancí virtuálních počítačů ve škálovací sadě. Tato akce horizontálního snížení kapacity sníží náklady na provoz škálovací sady, protože budete spouštět pouze takový počet instancí, který je potřeba ke zpracování aktuálních požadavků.

1. Zvolit **přidat pravidlo** znovu.
2. Vytvořte pravidlo, které sníží počet instancí virtuálních počítačů ve škálovací sadě, pokud je průměrné zatížení CPU sníží pod 30 % po dobu 10 minut. Když se pravidlo aktivuje, počet instancí virtuálních počítačů se sníží o 20 %.

    Stejně jako u předchozí pravidlo použijte stejným způsobem. Upravte následující nastavení pro pravidlo:
    
    | Parametr              | Vysvětlení                                                                                                          | Hodnota          |
    |------------------------|----------------------------------------------------------------------------------------------------------------------|----------------|
    | *– Operátor*             | Operátor sloužící k porovnání dat metriky s prahovou hodnotou.                                                      | Méně než   |
    | *Prahová hodnota*            | Procentuální hodnotu, která způsobí aktivaci akce pravidlem automatického škálování.                                                 | 30             |
    | *Operace*            | Definuje, jestli se škálovací sada by se měly škálovat nahoru nebo dolů po použití pravidla kapacita a jaké přírůstku                         | Snížit procento o |
    | *Počet instancí*       | Procento instancí virtuálních počítačů, které se mají po aktivaci pravidla změnit.                                             | 20             |

3. Chcete-li vytvořit pravidla, vyberte **přidat**


## <a name="define-autoscale-instance-limits"></a>Definování omezení pro automatické škálování instance
Váš profil automatického škálování musí definovat minimální, maximální a výchozí počet instancí virtuálních počítačů. Při použití pravidla automatického škálování, ujistěte se, že vám horizontální rozšíření kapacity mimo maximální počet instancí nebo horizontálně nad rámec minimálně instance tyto limity instancí.

1. Nastavte následující limity instancí:

    | Minimální | Maximum | Výchozí|
    |---------|---------|--------|
    | 2       | 10      | 2      |

2. Chcete-li použít pravidla automatického škálování a limity instancí, vyberte **Uložit**.


## <a name="monitor-number-of-instances-in-a-scale-set"></a>Monitorování počtu instancí ve škálovací sadě
Pokud chcete zobrazit počet a stav instancí virtuálních počítačů, vyberte **instance** z nabídky na levé straně stupnice nastavujete časové období. Stav označuje, zda je instance virtuálního počítače *vytváření* škálovací sady automaticky horizontálně navýší kapacitu, nebo je *odstranění* jako měřítko se automaticky škáluje v.

![Zobrazit seznam instancí škálovací sady virtuálních počítačů](media/virtual-machine-scale-sets-autoscale-portal/view-instances.png)


## <a name="autoscale-based-on-a-schedule"></a>Automatické škálování podle plánu
V předchozích příkladech automaticky škálovat škálovací sadu snížení nebo navýšení kapacity s hostiteli základní metriky, jako je využití procesoru. Můžete také vytvořit pravidla automatického škálování podle plánů. Tato pravidla podle plánu umožňují automaticky škálovat počet instancí virtuálních počítačů před očekávané zvýšení poptávky aplikací, jako je základní pracovní hodiny a potom automatické škálování počtu instancí v čase, který očekáváte, že méně vyžádání, jako je například víkendu.

1. Zvolte **škálování** z nabídky na levé straně stupnice nastavujete časové období. Chcete-li odstranit existující pravidla automatického škálování, které jsou vytvořené v předchozích příkladech, zvolte ikonu koše.

    ![Odstranění existujícího pravidla automatického škálování](media/virtual-machine-scale-sets-autoscale-portal/delete-rules.png)

2. Zvolit **přidat podmínku škálování**. Vyberte ikonu tužky vedle názvu pravidla a zadejte název, jako *horizontální navýšení kapacity za každý pracovní den*.

    ![Přejmenujte výchozí pravidlo automatického škálování](media/virtual-machine-scale-sets-autoscale-portal/rename-rule.png)

3. Vyberte přepínač **škálovat na konkrétní počet instancí**.
4. Pokud chcete vertikálně navýšit kapacitu počtu instancí, zadejte *10* jako počet instancí.
5. Zvolte **opakovat konkrétní dny** pro **plán** typu.
6. Vyberte všechny pracovní dny, od pondělí do pátku.
7. Zvolte odpovídající časové pásmo a potom zadejte **počáteční čas** z *09:00*.
8. Zvolit **přidat podmínku škálování** znovu. Postupujte stejně jako můžete vytvořit plán s názvem *horizontálně během večer* , která se škáluje, aby *3* instance, se opakuje každý den v týdnu a začíná *18:00*.
9. Chcete-li použít pravidla automatického škálování na základě plánu, vyberte **Uložit**.

    ![Vytvoření pravidla automatického škálování, které se škálují podle plánu](media/virtual-machine-scale-sets-autoscale-portal/schedule-autoscale.PNG)

Pokud chcete zobrazit, jak se používají pravidla automatického škálování, vyberte **historie spuštění** v horní části **škálování** okna. Graf a události seznam obsahuje při nastavení triggeru pravidla automatického škálování a počet instancí virtuálních počítačů ve škálovací zvýšení nebo snížení.


## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak pomocí pravidla automatického škálování horizontálně škálovat a zvýšit nebo snížit *číslo* instancí virtuálních počítačů ve škálovací nastavit. Můžete také škálovat vertikálně zvyšte nebo snižte instance virtuálního počítače *velikost*. Další informace najdete v tématu [vertikální automatické škálování s Virtual Machine Scale sets](virtual-machine-scale-sets-vertical-scale-reprovision.md).

Informace o tom, jak spravovat vaše instance virtuálních počítačů najdete v tématu [Správa škálovacích sad virtuálních počítačů pomocí Azure Powershellu](virtual-machine-scale-sets-windows-manage.md).

Zjistěte, jak generovat výstrahy, když vaše automatické škálování pravidla aktivační událost, najdete v článku [pomocí akcí automatického škálování můžete poslat e-mail a webhook oznámení výstrah ve službě Azure Monitor](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md). Můžete také [pomocí protokolů auditu odesílat emailová a webhooková oznámení výstrah ve službě Azure Monitor](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md).

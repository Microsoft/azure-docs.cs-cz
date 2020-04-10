---
title: Škálovací sady virtuálních strojů na webu Azure Portal s automatickým škálování
description: Jak vytvořit pravidla automatického škálování pro škálovací sady virtuálních strojů na webu Azure Portal
author: ju-shim
tags: azure-resource-manager
ms.assetid: 88886cad-a2f0-46bc-8b58-32ac2189fc93
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: jushiman
ms.openlocfilehash: 1915b144aec5a5447504c70d18dbf420d255a08e
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010286"
---
# <a name="automatically-scale-a-virtual-machine-scale-set-in-the-azure-portal"></a>Automatické škálování škálovací sady virtuálních strojů na webu Azure Portal
Při vytváření škálovací sady definujete počet instancí virtuálních počítačů, které chcete spouštět. S měnícími se požadavky na aplikaci můžete počet instancí virtuálních počítačů automaticky zvyšovat nebo snižovat. Možnost automatického škálování umožňuje držet krok s požadavky zákazníků nebo reagovat na změny výkonu aplikace v průběhu jejího životního cyklu.

Tento článek ukazuje, jak vytvořit pravidla automatického škálování na webu Azure Portal, které monitorují výkon instancí virtuálních počítače ve vaší škálovací sadě. Tato pravidla automatického škálování zvyšují nebo snižují počet instancí virtuálních virtuálních virtuálních lidí v reakci na tyto metriky výkonu. Tyto kroky můžete taky provést pomocí [Azure PowerShellu](tutorial-autoscale-powershell.md) nebo [azure cli](tutorial-autoscale-cli.md).


## <a name="prerequisites"></a>Požadavky
Chcete-li vytvořit pravidla automatického škálování, potřebujete existující škálovací sadu virtuálních strojů. Škálovací sadu můžete vytvořit pomocí [portálu Azure Portal](quick-create-portal.md), [Azure PowerShellu](quick-create-powershell.md)nebo [Azure CLI](quick-create-cli.md).


## <a name="create-a-rule-to-automatically-scale-out"></a>Vytvoření pravidla pro automatické horizontální navýšení kapacity
Pokud se požadavky na vaši aplikaci zvýší, zvýší se i zatížení instancí virtuálních počítačů ve škálovací sadě. Pokud je toto zvýšené zatížení konzistentní, a nejedná se pouze o krátkou poptávku, můžete nakonfigurovat pravidla automatického škálování pro zvýšení počtu instancí virtuálních počítačů ve škálovací sadě. Po vytvoření těchto instancí virtuálních počítačů a nasazení aplikací do nich začne škálovací sada distribuovat provoz prostřednictvím nástroje pro vyrovnávání zatížení. Můžete řídit, které metriky se mají monitorovat, například CPU nebo disk, jak dlouho musí zatížení aplikace dosahovat dané prahové hodnoty a kolik instancí virtuálních počítačů se má do škálovací sady přidat.

1. Otevřete portál Azure a v yberte **skupiny prostředků** z nabídky na levé straně řídicího panelu.
2. Vyberte skupinu prostředků, která obsahuje škálovací sadu, a pak ze seznamu zdrojů vyberte škálovací sadu.
3. Z nabídky na levé straně okna škálovací sady zvolte **Změna velikosti.** Vyberte tlačítko **pro povolení automatického škálování**:

    ![Povolení automatického škálování na webu Azure Portal](media/virtual-machine-scale-sets-autoscale-portal/enable-autoscale.png)

4. Zadejte název nastavení, například *automatické škálování*, a vyberte možnost **přidání pravidla**.

5. Pojďme vytvořit pravidlo, které zvyšuje počet instancí virtuálních počítače ve škálovací sadě, když je průměrné zatížení procesoru větší než 70 % za období 10 minut. Když se pravidlo aktivuje, počet instancí virtuálních virtuálních byl zvýšen o 20 %. V škálovacích sadách s malým počtem instancí virtuálních virtuálních mitek můžete nastavit **operaci** na *zvýšení počtu a* pak zadat *hodnotu 1* nebo *2* pro *počet instancí*. V škálovacích sadách s velkým počtem instancí virtuálních minců může být vhodnější zvýšení o 10 % nebo 20 % instancí virtuálních virtuálních byl.

    Pro pravidlo zadejte následující nastavení:
    
    | Parametr              | Vysvětlení                                                                                                         | Hodnota          |
    |------------------------|---------------------------------------------------------------------------------------------------------------------|----------------|
    | *Agregace času*     | Definuje způsob agregace shromážděných metrik pro účely analýzy.                                                | Průměr        |
    | *Název metriky*          | Metrika výkonu, která se má monitorovat a na kterou se mají použít akce škálovací sady.                                                   | Procento CPU |
    | *Statistika agregačního intervalu* | Definuje, jak shromážděné metriky v každém okamžiku zrna by měly být agregovány pro analýzu.                             | Průměr        |
    | *Operátor*             | Operátor sloužící k porovnání dat metriky s prahovou hodnotou.                                                     | Větší než   |
    | *Práh*            | Procento, které způsobí, že pravidlo automatického škálování spustí akci.                                                 | 70             |
    | *Doba trvání*             | Doba, která se monitoruje před porovnáním metrik a prahových hodnot.                                   | 10 minut     |
    | *Operace*            | Definuje, zda má škálovací sada škálovat nahoru nebo dolů, když se pravidlo použije a jaký přírůstek                        | Zvýšení o procento |
    | *Počet instancí*       | Procento instancí virtuálních počítačů, které se mají po aktivaci pravidla změnit.                                            | 20             |
    | *Přestávka (minuty)*  | Doba, po kterou se má počkat před opětovným použitím pravidla, aby akce automatického škálování měly dostatek času se projevit. | 5 minut      |

    Následující příklady ukazují pravidlo vytvořené na webu Azure Portal, které odpovídá těmto nastavením:

    ![Vytvoření pravidla automatického škálování pro zvýšení počtu instancí virtuálních virtuálních min](media/virtual-machine-scale-sets-autoscale-portal/rule-increase.png)

6. Chcete-li vytvořit pravidlo, vyberte **Přidat**


## <a name="create-a-rule-to-automatically-scale-in"></a>Vytvoření pravidla pro automatické škálování
Večer nebo o víkendu se požadavky na vaši aplikaci můžou snížit. Pokud je toto snížené zatížení po určitou dobu konzistentní, můžete nakonfigurovat pravidla automatického škálování pro snížení počtu instancí virtuálních počítačů ve škálovací sadě. Tato akce horizontálního snížení kapacity sníží náklady na provoz škálovací sady, protože budete spouštět pouze takový počet instancí, který je potřeba ke zpracování aktuálních požadavků.

1. **Zvolte, zda** chcete pravidlo přidat znovu.
2. Vytvořte pravidlo, které snižuje počet instancí virtuálních počítače ve škálovací sadě, když průměrné zatížení procesoru pak klesne pod 30 % za období 10 minut. Když se pravidlo aktivuje, počet instancí virtuálních virtuálních byl snížen o 20 %.

    Použijte stejný přístup jako u předchozího pravidla. Upravte následující nastavení pravidla:
    
    | Parametr              | Vysvětlení                                                                                                          | Hodnota          |
    |------------------------|----------------------------------------------------------------------------------------------------------------------|----------------|
    | *Operátor*             | Operátor sloužící k porovnání dat metriky s prahovou hodnotou.                                                      | Menší než   |
    | *Práh*            | Procento, které způsobí, že pravidlo automatického škálování spustí akci.                                                 | 30             |
    | *Operace*            | Definuje, zda má škálovací sada škálovat nahoru nebo dolů, když se pravidlo použije a jaký přírůstek                         | Snížení o procento |
    | *Počet instancí*       | Procento instancí virtuálních počítačů, které se mají po aktivaci pravidla změnit.                                             | 20             |

3. Chcete-li vytvořit pravidlo, vyberte **Přidat**


## <a name="define-autoscale-instance-limits"></a>Definování omezení instancí automatického škálování
Profil automatického škálování musí definovat minimální, maximální a výchozí počet instancí virtuálních počítače. Při použití pravidel automatického škálování, tyto limity instance ujistěte se, že není horizontální navýšení kapacity nad rámec maximální počet instancí nebo škálování v nad rámec minimální instance.

1. Nastavte následující limity instancí:

    | Minimální | Maximum | Výchozí|
    |---------|---------|--------|
    | 2       | 10      | 2      |

2. Chcete-li použít pravidla automatického škálování a omezení instancí, vyberte **Uložit**.


## <a name="monitor-number-of-instances-in-a-scale-set"></a>Sledování počtu instancí ve škálovací sadě
Pokud chcete zobrazit počet a stav instancí virtuálních počítačů, vyberte **instance** z nabídky na levé straně okna škálovací sady. Stav označuje, jestli je instance virtuálního aplikace *Vytváření,* protože škálovací sada se automaticky škáluje, nebo se *odstranění* při automatickém škálování škáluje.

![Zobrazení seznamu instancí virtuálních<amů škálovací sady](media/virtual-machine-scale-sets-autoscale-portal/view-instances.png)


## <a name="autoscale-based-on-a-schedule"></a>Automatické škálování na základě plánu
Předchozí příklady automaticky škálovat škálovací sadu v nebo mimo pomocí základní metriky hostitele, jako je využití procesoru. Můžete také vytvořit pravidla automatického škálování na základě plánů. Tato pravidla založená na plánu umožňují automaticky škálovat počet instancí virtuálních aplikací před očekávaným zvýšením poptávky po aplikaci, jako je základní pracovní doba, a pak automaticky škálovat počet instancí v době, kdy očekáváte nižší poptávku, například víkend.

1. Z nabídky na levé straně okna škálovací sady zvolte **Změna velikosti.** Chcete-li odstranit existující pravidla automatického škálování vytvořená v předchozích příkladech, zvolte ikonu koše.

    ![Odstranění existujících pravidel automatického škálování](media/virtual-machine-scale-sets-autoscale-portal/delete-rules.png)

2. Zvolte, zda chcete **přidat podmínku měřítka**. Vyberte ikonu tužky vedle názvu pravidla a zadejte název, jako je *měřítko během každého pracovního dne*.

    ![Přejmenování výchozího pravidla automatického škálování](media/virtual-machine-scale-sets-autoscale-portal/rename-rule.png)

3. Vyberte přepínací tlačítko **pro měřítko pro určitý počet instancí**.
4. Chcete-li navýšit kapacitu počtu instancí, zadejte jako počet instancí *hodnotu 10.*
5. Zvolte **Opakovat určité dny** pro typ **plánu.**
6. Vyberte všechny pracovní dny od pondělí do pátku.
7. Zvolte vhodné časové pásmo a zadejte **čas zahájení** *09:00*.
8. Zvolte, zda chcete znovu **přidat podmínku měřítka.** Tento postup opakujte a vytvořte plán s názvem *Měřítko v průběhu večera,* který se škáluje na *3* instance, opakuje se každý všední den a začíná v *18:00*.
9. Chcete-li použít pravidla automatického škálování založená na plánu, vyberte **uložit**.

    ![Vytvoření pravidel automatického škálování, která se škálují podle plánu](media/virtual-machine-scale-sets-autoscale-portal/schedule-autoscale.PNG)

Chcete-li zjistit, jak se pravidla automatického škálování používají, vyberte **Spustit historii** v horní části okna **Škálování.** Graf a události seznam ukazuje, když se aktivuje pravidla automatického škálování a počet instancí virtuálních počítače ve vaší škálovací sadě se zvyšuje nebo snižuje.


## <a name="next-steps"></a>Další kroky
V tomto článku jste se dozvěděli, jak pomocí pravidel automatického škálování škálovat vodorovně a zvýšit nebo snížit *počet* instancí virtuálních počítače ve vaší škálovací sadě. Můžete také škálovat svisle a zvětšit nebo zmenšit *velikost*instance virtuálního počítače . Další informace najdete [v tématu Vertikální automatické škálování pomocí sad škálování virtuálních strojů](virtual-machine-scale-sets-vertical-scale-reprovision.md).

Informace o tom, jak spravovat instance virtuálních počítačů, najdete v [tématu Správa škálovacích sad virtuálních strojů pomocí Azure PowerShellu](virtual-machine-scale-sets-windows-manage.md).

Informace o tom, jak generovat výstrahy při aktivaci pravidel automatického škálování, najdete v [tématu Odesílání e-mailů a upozornění webhooku v Azure Monitoru pomocí akcí automatického škálování.](../azure-monitor/platform/autoscale-webhook-email.md) Protokoly auditování můžete taky [použít k odesílání e-mailů a oznámení upozornění webhooku v Azure Monitoru](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md).

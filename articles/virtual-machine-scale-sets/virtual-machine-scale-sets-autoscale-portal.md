---
title: Automatické škálování virtuálních počítačů ve službě Virtual Machine Scale Sets v Azure Portal
description: Vytvoření pravidel automatického škálování pro sady škálování virtuálních počítačů v Azure Portal
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 05/29/2018
ms.reviewer: avverma
ms.custom: avverma
ms.openlocfilehash: 2ee2db62cf43dc191da2b92f7d4b67ff775f628f
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537523"
---
# <a name="automatically-scale-a-virtual-machine-scale-set-in-the-azure-portal"></a>Automatické škálování sady škálování virtuálních počítačů v Azure Portal
Při vytváření škálovací sady definujete počet instancí virtuálních počítačů, které chcete spouštět. S měnícími se požadavky na aplikaci můžete počet instancí virtuálních počítačů automaticky zvyšovat nebo snižovat. Možnost automatického škálování umožňuje držet krok s požadavky zákazníků nebo reagovat na změny výkonu aplikace v průběhu jejího životního cyklu.

V tomto článku se dozvíte, jak vytvořit pravidla automatického škálování v Azure Portal, která sledují výkon instancí virtuálních počítačů ve vaší sadě škálování. Tato pravidla automatického škálování zvyšují nebo snižují počet instancí virtuálních počítačů v reakci na tyto metriky výkonu. Tento postup můžete provést také pomocí [Azure PowerShell](tutorial-autoscale-powershell.md) nebo rozhraní příkazového [řádku Azure CLI](tutorial-autoscale-cli.md).


## <a name="prerequisites"></a>Požadavky
K vytvoření pravidel automatického škálování potřebujete existující sadu škálování virtuálního počítače. Sadu škálování můžete vytvořit pomocí [Azure Portal](quick-create-portal.md), [Azure PowerShell](quick-create-powershell.md)nebo rozhraní příkazového [řádku Azure](quick-create-cli.md).


## <a name="create-a-rule-to-automatically-scale-out"></a>Vytvoření pravidla pro automatické horizontální navýšení kapacity
Pokud se požadavky na vaši aplikaci zvýší, zvýší se i zatížení instancí virtuálních počítačů ve škálovací sadě. Pokud je toto zvýšené zatížení konzistentní, a nejedná se pouze o krátkou poptávku, můžete nakonfigurovat pravidla automatického škálování pro zvýšení počtu instancí virtuálních počítačů ve škálovací sadě. Po vytvoření těchto instancí virtuálních počítačů a nasazení aplikací do nich začne škálovací sada distribuovat provoz prostřednictvím nástroje pro vyrovnávání zatížení. Můžete řídit, které metriky se mají monitorovat, například CPU nebo disk, jak dlouho musí zatížení aplikace dosahovat dané prahové hodnoty a kolik instancí virtuálních počítačů se má do škálovací sady přidat.

1. Otevřete Azure Portal a v nabídce na levé straně řídicího panelu vyberte **skupiny prostředků** .
2. Vyberte skupinu prostředků, která obsahuje vaši sadu škálování, a pak vyberte ze seznamu prostředků sadu škálování.
3. V nabídce na levé straně okna škála nastavení vyberte možnost **škálování** . Výběrem tlačítka **povolíte automatické škálování**:

    ![Povolit automatické škálování v Azure Portal](media/virtual-machine-scale-sets-autoscale-portal/enable-autoscale.png)

4. Zadejte název nastavení, například *Automatické škálování*, a pak vyberte možnost **Přidat pravidlo**.

5. Pojďme vytvořit pravidlo, které zvýší počet instancí virtuálních počítačů v sadě škálování, pokud je průměrné zatížení procesoru vyšší než 70% po dobu 10 minut. Po aktivaci pravidla se počet instancí virtuálních počítačů zvýší o 20%. V sadě škálování s malým počtem instancí virtuálních počítačů můžete nastavit **operaci** na *zvýšení počtu o* a pak pro *počet instancí* zadat *1* nebo *2* . V sadě škálování se velký počet instancí virtuálních počítačů může být vhodnější zvýšit počet instancí virtuálních počítačů o 10% nebo 20%.

    Pro pravidlo zadejte následující nastavení:
    
    | Parametr              | Vysvětlení                                                                                                         | Hodnota          |
    |------------------------|---------------------------------------------------------------------------------------------------------------------|----------------|
    | *Časová agregace*     | Definuje způsob agregace shromážděných metrik pro účely analýzy.                                                | Průměr        |
    | *Název metriky*          | Metrika výkonu, která se má monitorovat a na kterou se mají použít akce škálovací sady.                                                   | Procento CPU |
    | *Statistika agregačního intervalu* | Definuje, jak by měly být shromážděné metriky v jednotlivých intervalech agregovány pro účely analýzy.                             | Průměr        |
    | *Operátor*             | Operátor sloužící k porovnání dat metriky s prahovou hodnotou.                                                     | Větší než   |
    | *Prahová hodnota*            | Procentuální hodnota, která způsobí, že pravidlo automatického škálování aktivuje akci.                                                 | 70             |
    | *Doba trvání*             | Doba, která se monitoruje před porovnáním metrik a prahových hodnot. Nezahrnuje dobu chladnutí.                                   | 10 minut     |
    | *Operace*            | Definuje, jestli se má sada škálování škálovat nahoru nebo dolů při použití pravidla a podle toho, co se zvyšuje.                        | Zvýšit procento o |
    | *Počet instancí*       | Procento instancí virtuálních počítačů, které se mají po aktivaci pravidla změnit.                                            | 20             |
    | *Přestávka (minuty)*  | Doba, po kterou se má počkat před opětovným použitím pravidla, aby akce automatického škálování měly dostatek času se projevit. | 5 minut      |

    Následující příklady ukazují pravidlo vytvořené v Azure Portal, které odpovídá těmto nastavením:

    ![Vytvořte pravidlo automatického škálování, které zvýší počet instancí virtuálních počítačů.](media/virtual-machine-scale-sets-autoscale-portal/rule-increase.png)

6. Pravidlo vytvoříte tak, že vyberete **Přidat** .


## <a name="create-a-rule-to-automatically-scale-in"></a>Vytvoření pravidla pro automatické horizontální navýšení kapacity
Večer nebo o víkendu se požadavky na vaši aplikaci můžou snížit. Pokud je toto snížené zatížení po určitou dobu konzistentní, můžete nakonfigurovat pravidla automatického škálování pro snížení počtu instancí virtuálních počítačů ve škálovací sadě. Tato akce horizontálního snížení kapacity sníží náklady na provoz škálovací sady, protože budete spouštět pouze takový počet instancí, který je potřeba ke zpracování aktuálních požadavků.

1. Vyberte, **že chcete pravidlo přidat** znovu.
2. Vytvořte pravidlo, které sníží počet instancí virtuálních počítačů v sadě škálování, když se průměrné zatížení procesoru sníží pod 30% po dobu 10 minut. Když se pravidlo aktivuje, počet instancí virtuálních počítačů se sníží o 20%.

    Použijte stejný přístup jako u předchozího pravidla. Upravte následující nastavení pravidla:
    
    | Parametr              | Vysvětlení                                                                                                          | Hodnota          |
    |------------------------|----------------------------------------------------------------------------------------------------------------------|----------------|
    | *Operátor*             | Operátor sloužící k porovnání dat metriky s prahovou hodnotou.                                                      | Menší než   |
    | *Prahová hodnota*            | Procentuální hodnota, která způsobí, že pravidlo automatického škálování aktivuje akci.                                                 | 30             |
    | *Operace*            | Definuje, jestli se má při použití pravidla a o tom, jaký přírůstek má změnit horizontální navýšení nebo snížení kapacity sady škálování.                         | Snížit procento o |
    | *Počet instancí*       | Procento instancí virtuálních počítačů, které se mají po aktivaci pravidla změnit.                                             | 20             |

3. Pravidlo vytvoříte tak, že vyberete **Přidat** .


## <a name="define-autoscale-instance-limits"></a>Definování omezení instancí automatického škálování
Váš profil automatického škálování musí definovat minimální, maximální a výchozí počet instancí virtuálních počítačů. Při použití pravidel automatického škálování se tato omezení instance zajišťují, že nebudete škálovat nad rámec maximálního počtu instancí, nebo můžete škálovat mimo minimální počet instancí.

1. Nastavte následující limity instancí:

    | Minimum | Maximum | Výchozí|
    |---------|---------|--------|
    | 2       | 10      | 2      |

2. Pokud chcete použít pravidla automatického škálování a omezení instancí, vyberte **Uložit**.


## <a name="monitor-number-of-instances-in-a-scale-set"></a>Monitorování počtu instancí v sadě škálování
Pokud chcete zobrazit počet a stav instancí virtuálních počítačů, vyberte **instance** v nabídce na levé straně okna sady škálování. Stav označuje, jestli se instance virtuálního počítače *vytváří* , protože se sada škálování automaticky škáluje, nebo se *odstraňuje* , protože se měřítko automaticky škáluje.

![Zobrazit seznam instancí virtuálních počítačů sady škálování](media/virtual-machine-scale-sets-autoscale-portal/view-instances.png)


## <a name="autoscale-based-on-a-schedule"></a>Automatické škálování podle plánu
Předchozí příklady automaticky škálují měřítko nastavené na základní metriky hostitele, jako je například využití procesoru. Můžete také vytvořit pravidla automatického škálování na základě plánů. Tato pravidla založená na plánech umožňují automatické horizontálního navýšení kapacity počtu instancí virtuálních počítačů na základě očekávaného nárůstu poptávky s aplikacemi, jako jsou základní pracovní hodiny, a pak automatické škálování počtu instancí v době, kdy předpokládáte menší poptávku, jako je například víkend.

1. V nabídce na levé straně okna škála nastavení vyberte možnost **škálování** . Pokud chcete odstranit existující pravidla automatického škálování vytvořená v předchozích příkladech, vyberte ikonu odpadkového koše.

    ![Odstranit existující pravidla automatického škálování](media/virtual-machine-scale-sets-autoscale-portal/delete-rules.png)

2. Vyberte, chcete-li **Přidat podmínku škálování**. Vyberte ikonu tužky vedle pole název pravidla a zadejte název, například horizontální navýšení *kapacity během každého pracovního dne*.

    ![Přejmenovat výchozí pravidlo automatického škálování](media/virtual-machine-scale-sets-autoscale-portal/rename-rule.png)

3. Vyberte přepínač, který se má **škálovat na konkrétní počet instancí**.
4. Pro horizontální navýšení kapacity počtu instancí zadejte jako počet instancí hodnotu *10* .
5. Pro typ **plánu** vyberte **Opakovat konkrétní dny** .
6. Vyberte všechny pracovní dny, pondělí až pátek.
7. Zvolte příslušné časové pásmo a pak zadejte **počáteční čas** *09:00*.
8. Vyberte možnost **Přidat podmínku škálování** znovu. Opakujte postup pro vytvoření plánu s názvem *škálovat v průběhu večer* , který se škáluje na *3* instance, opakuje každý den v týdnu a začíná na *18:00*.
9. Pokud chcete použít pravidla automatického škálování na základě plánu, vyberte **Uložit**.

    ![Vytváření pravidel automatického škálování, která se škálují podle plánu](media/virtual-machine-scale-sets-autoscale-portal/schedule-autoscale.PNG)

Pokud chcete zjistit, jak se používají pravidla automatického škálování, vyberte v horní části okna **škálování** **historii spuštění** . Seznam grafů a událostí zobrazuje, když se aktivují pravidla automatického škálování a počet instancí virtuálních počítačů ve vaší sadě škálování se zvyšuje nebo snižuje.


## <a name="next-steps"></a>Další kroky
V tomto článku jste zjistili, jak používat pravidla automatického škálování pro horizontální škálování a zvýšení nebo snížení *počtu* instancí virtuálních počítačů ve vaší sadě škálování. *Velikost* instance virtuálního počítače můžete zvýšit nebo snížit také vertikálně. Další informace najdete v tématu [vertikální automatické škálování pomocí služby Virtual Machine Scale Sets](virtual-machine-scale-sets-vertical-scale-reprovision.md).

Informace o tom, jak spravovat instance virtuálních počítačů, najdete v tématu [Správa služby Virtual Machine Scale Sets pomocí Azure PowerShell](./virtual-machine-scale-sets-manage-powershell.md).

Informace o tom, jak generovat výstrahy při aktivaci pravidel automatického škálování, najdete [v tématu použití akcí automatického škálování k odesílání oznámení o výstrahách e-mailu a Webhooku v Azure monitor](../azure-monitor/autoscale/autoscale-webhook-email.md). [Protokoly auditu můžete použít také k posílání oznámení o výstrahách e-mailu a Webhooku v Azure monitor](../azure-monitor/alerts/alerts-log-webhook.md).

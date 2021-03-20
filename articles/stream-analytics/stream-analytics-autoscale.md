---
title: Automatické škálování Stream Analytics úloh
description: Tento článek popisuje, jak Stream Analytics úlohy automatického škálování na základě předdefinovaného plánu nebo hodnot metrik úloh.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/03/2020
ms.openlocfilehash: a8e089e302e9d40c69cf7ff2a3480c17894e1463
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98016282"
---
# <a name="autoscale-stream-analytics-jobs-using-azure-automation"></a>Automatické škálování Stream Analytics úloh pomocí Azure Automation

Náklady na Stream Analytics úlohy můžete optimalizovat konfigurací automatického škálování. Automatické škálování zvyšuje nebo snižuje jednotky streamování vaší úlohy (SUs) tak, aby odpovídaly změně ve vstupním zatížení. Místo nadměrného zřizování úlohy můžete podle potřeby škálovat směrem nahoru nebo dolů. Existují dva způsoby, jak nakonfigurovat úlohy pro automatické škálování:
1. **Předem Definujte plán** , když máte předvídatelné vstupní zatížení. Například očekáváte vyšší míru vstupních událostí v době od Daytime a chcete, aby byla úloha spuštěna s více službami SUs.
2. Pokud nemáte předvídatelné vstupní zatížení, **aktivovat můžete horizontální navýšení kapacity a škálování operací na základě metriky úlohy** . Můžete dynamicky měnit počet služby SUs na základě metriky úlohy, jako je počet vstupních událostí nebo nevyřízených událostí vstupu.

## <a name="prerequisites"></a>Předpoklady
Než začnete konfigurovat automatické škálování pro vaši úlohu, proveďte následující kroky.
1. Vaše úloha je optimalizovaná tak, aby měla [paralelní topologii](./stream-analytics-parallelization.md). Pokud můžete změnit měřítko úlohy, když je spuštěná, má vaše úloha paralelní topologii a je možné ji nakonfigurovat na automatické škálování.
2. [Vytvořte účet Azure Automation](../automation/automation-create-standalone-account.md) s povolenou možností "RunAsAccount". Tento účet musí mít oprávnění ke správě úloh Stream Analytics.

## <a name="set-up-azure-automation"></a>Nastavit Azure Automation
### <a name="configure-variables"></a>Konfigurace proměnných
Do účtu Azure Automation přidejte následující proměnné. Tyto proměnné budou použity v sadách Runbook, které jsou popsány v následujících krocích.

| Název | Typ | Hodnota |
| --- | --- | --- |
| **jobName** | Řetězec | Název Stream Analytics úlohy, kterou chcete automatické škálování. |
| **resourceGroupName** | Řetězec | Název skupiny prostředků, ve které se vaše úloha nachází |
| **subId** | Řetězec | ID předplatného, ve kterém je vaše úloha k dispozici. |
| **increasedSU** | Integer | Vyšší hodnota SU, na kterou se má vaše úloha škálovat podle plánu. Tato hodnota musí být jedna z platných možností SU, které vidíte v nastavení **škálování** úlohy, když je spuštěná. |
| **decreasedSU** | Integer | Hodnota snížení hodnoty SU, na kterou se má vaše úloha škálovat podle plánu. Tato hodnota musí být jedna z platných možností SU, které vidíte v nastavení **škálování** úlohy, když je spuštěná. |
| **maxSU** | Integer | Maximální hodnota SU, na kterou se má vaše úloha škálovat, v krocích při automatickém škálování podle zatížení. Tato hodnota musí být jedna z platných možností SU, které vidíte v nastavení **škálování** úlohy, když je spuštěná. |
| **Minuty** | Integer | Minimální hodnota SU, na kterou se má vaše úloha škálovat, v krocích při automatickém škálování podle zatížení. Tato hodnota musí být jedna z platných možností SU, které vidíte v nastavení **škálování** úlohy, když je spuštěná. |

![Přidat proměnné v Azure Automation](./media/autoscale/variables.png)

### <a name="create-runbooks"></a>Vytváření runbooků
Dalším krokem je vytvoření dvou runbooků PowerShellu. Jednu pro horizontální navýšení kapacity a druhou pro operace horizontálního navýšení kapacity.
1. V Azure Automation účtu v části **Automatizace procesu** klikněte na **Runbooky** a vyberte **vytvořit Runbook**.
2. Pojmenujte první sadu Runbook *ScaleUpRunbook* s typem nastaveným na PowerShell. Použijte [skript prostředí PowerShell ScaleUpRunbook](https://github.com/Azure/azure-stream-analytics/blob/master/Autoscale/ScaleUpRunbook.ps1) , který je k dispozici na GitHubu. Uložte a publikujte ho.
3. Pomocí typu PowerShellu vytvořte další sadu Runbook s názvem *ScaleDownRunbook* . Použijte [skript prostředí PowerShell ScaleDownRunbook](https://github.com/Azure/azure-stream-analytics/blob/master/Autoscale/ScaleDownRunbook.ps1) , který je k dispozici na GitHubu. Uložte a publikujte ho.

![Automatické škálování runbooků v Azure Automation](./media/autoscale/runbooks.png)

Nyní máte Runbooky, které mohou automaticky aktivovat horizontální navýšení kapacity a škálování operací na Stream Analytics úlohy. Tyto Runbooky lze aktivovat pomocí předem definovaného plánu nebo je lze nastavit dynamicky na základě metriky úlohy.

## <a name="autoscale-based-on-a-schedule"></a>Automatické škálování podle plánu
Azure Automation umožňuje nakonfigurovat plán, který bude aktivovat vaše Runbooky.
1. V účtu Azure Automation v části **sdílené prostředky** vyberte **plány** . Pak vyberte **Přidat plán**.
2. Můžete například vytvořit dva plány. Ten, který představuje, když chcete, aby byla vaše úloha zvětšená a jiná, která představuje, když chcete, aby se změnila velikost úlohy. Pro tyto plány můžete definovat opakování.

   ![Plány v Azure Automation](./media/autoscale/schedules.png)

3. Otevřete své **ScaleUpRunbook** a potom v části **prostředky** vyberte **plány** . Runbook pak můžete propojit s plánem, který jste vytvořili v předchozích krocích. Můžete mít vazbu na více plánů propojených se stejnou sadou Runbook, což může být užitečné, pokud chcete spustit stejnou operaci škálování v různou denní dobu.

![Plánování runbooků v Azure Automation](./media/autoscale/schedulerunbook.png)

1. Opakujte předchozí krok pro **ScaleDownRunbook**.

## <a name="autoscale-based-on-load"></a>Automatické škálování na základě zatížení
Můžou nastat případy, kdy nemůžete odhadnout zatížení vstupu. V takových případech je lepší škálovat nahoru/dolů v krocích v rámci minimální a maximální vazby. Můžete nakonfigurovat pravidla upozornění v úlohách Stream Analytics pro aktivaci runbooků, pokud metriky úlohy přejdou nad nebo pod prahovou hodnotou.
1. Ve vašem účtu Azure Automation vytvořte dvě více celočíselných proměnných s názvem **min** a **maxSU**. Tím se nastaví rozsahy, ve kterých se vaše úloha bude škálovat podle kroků.
2. Vytvořte dvě nové Runbooky. Můžete použít [skript prostředí PowerShell StepScaleUp](https://github.com/Azure/azure-stream-analytics/blob/master/Autoscale/StepScaleUp.ps1) , který zvýší službu SUs vaší úlohy v přírůstcích až do hodnoty **maxSU** . Můžete také použít [skript prostředí PowerShell StepScaleDown](https://github.com/Azure/azure-stream-analytics/blob/master/Autoscale/StepScaleDown.ps1) , který zmenší službu SUs v rámci kroků do dosažení hodnoty **min** . Případně můžete použít Runbooky z předchozí části, pokud máte specifické hodnoty SU, na které chcete škálovat.
3. V Stream Analytics úlohy vyberte v části **sledování** možnost **pravidla výstrah** . 
4. Vytvořte dvě skupiny akcí. Ten, který se má použít pro operaci horizontálního navýšení kapacity a další pro operaci horizontálního rozšíření kapacity. Vyberte **Spravovat akce** a pak klikněte na **Přidat skupinu akcí**. 
5. Vyplňte požadovaná pole. Když vyberete **typ akce**, zvolte **Runbook Automation** . Vyberte sadu Runbook, kterou chcete aktivovat, když se výstraha aktivuje. Pak vytvořte skupinu akcí.

   ![Vytvoření skupiny akcí](./media/autoscale/create-actiongroup.png)
6. Vytvořte [**nové pravidlo upozornění**](./stream-analytics-set-up-alerts.md#set-up-alerts-in-the-azure-portal) v rámci úlohy. Určete podmínku na základě zvolené metriky. [ *Vstupní události*: *Su% využití* nebo *nevyřízené události vstupu*](./stream-analytics-monitoring.md#metrics-available-for-stream-analytics) jsou doporučené metriky, které se použijí pro definování logiky automatického škálování. Při aktivaci operací horizontálního navýšení kapacity se taky doporučuje použít *hustotu agregace* s 1 minutou a *frekvencí vyhodnocení* . Tím zajistíte, že vaše úloha bude mít dostatek prostředků na práci s velkými špičkami ve vstupním svazku.
7. Vyberte skupinu akcí vytvořenou v posledním kroku a vytvořte výstrahu.
8. Opakujte kroky 2 až 4 pro všechny další operace škálování, které chcete aktivovat na základě podmínek metriky úlohy.

Je osvědčeným postupem, jak spustit testy škálování před spuštěním úlohy v produkčním prostředí. Když testujete úlohu proti různým vstupním zátěžům, získáte představu o tom, kolik SUs potřebuje vaše úloha pro jinou vstupní propustnost. To může informovat o podmínkách, které definujete v pravidlech upozornění, které aktivují operace horizontálního navýšení kapacity a škálování. 

## <a name="next-steps"></a>Další kroky
* [Vytváření dotazů paralelizovat v Azure Stream Analytics](stream-analytics-parallelization.md)
* [Škálování úloh Azure Stream Analytics pro zvýšení propustnosti](stream-analytics-scale-jobs.md)
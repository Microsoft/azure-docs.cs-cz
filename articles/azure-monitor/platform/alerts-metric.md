---
title: Vytvoření, zobrazení a správa metriky upozornění pomocí Azure monitoru
description: Zjistěte, jak pomocí webu Azure portal nebo rozhraní příkazového řádku k vytvoření, zobrazení a správě pravidla upozornění na metriky.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: d871cecda8e7957e3d0d3c37078b8a1fa2ab5214
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54451251"
---
# <a name="create-view-and-manage-metric-alerts-using-azure-monitor"></a>Vytvořit, zobrazit a spravovat upozornění na metriku pomocí Azure monitoru

Upozornění metrik ve službě Azure Monitor poskytují způsob, jak chcete dostávat oznámení, když jeden metriky překročí prahovou hodnotu. Upozornění na metriku pracovat na celou řadu multidimenzionální platformy metriky, vlastní metriky, Application Insights standardní a vlastní metriky. V tomto článku jsme popisuje, jak vytvořit, zobrazit a spravovat pravidla upozornění na metriky prostřednictvím webu Azure portal a rozhraní příkazového řádku Azure. Můžete také vytvořit pravidla upozornění na metriky pomocí šablon Azure Resource Manageru, která je popsána v [věnovaný samostatný článek](../../azure-monitor/platform/alerts-enable-template.md).

Přečtěte si víc o tom, jak metriky upozornění fungují z [přehled výstrah metrika](alerts-metric-overview.md).

## <a name="create-with-azure-portal"></a>Vytvořit pomocí webu Azure portal

Následující postup popisuje, jak vytvořit pravidlo upozornění metriky na webu Azure portal:

1. V [webu Azure portal](https://portal.azure.com), klikněte na **monitorování**. V okně Monitor konsoliduje všechny vaše monitorování nastavení a data v jednom zobrazení.

2. Klikněte na tlačítko **výstrahy** klikněte **+ nové pravidlo upozornění**.

    > [!TIP]
    > Většina oken prostředků také mít **výstrahy** ve své nabídce prostředků v rámci **monitorování**, upozornění můžete například vytvořit i z něj.

3. Klikněte na tlačítko **Výběr cíle**, v podokně kontext, který načítá, vyberte cílový prostředek, který chcete upozornit na. Použití **předplatné** a **typ prostředku** rozevírací seznamy se najít prostředek, kterou chcete monitorovat. Na panelu hledání můžete také najít váš prostředek.

4. Pokud vybraný prostředek má, můžete vytvářet upozornění metrik **dostupné signály** na dolní pravé bude obsahovat metriky. Můžete zobrazit úplný seznam typů prostředků, které jsou podporovány pro upozornění metrik v tomto [článku](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported).

5. Jakmile vyberete cílový prostředek, klikněte na **přidat podmínku**.

6. Se zobrazí seznam signálů, které jsou podporovány pro prostředek, vyberte metriku, kterou chcete vytvořit upozornění na.

7. Volitelně můžete upřesnit metriku úpravou **období** a **agregace**. Pokud metrika má rozměrů, zobrazí se **dimenze** tabulky zobrazené. Vyberte jednu nebo více hodnot na dimenzi. Upozornění metriky se spustí vyhodnotit podmínku pro všechny kombinace vybraných hodnot. [Další informace o tom, jak funguje upozorňování na vícedimenzionálních metrik](alerts-metric-overview.md). Můžete také **vyberte \***  pro libovolný počet rozměrů. **Vyberte \***  bude dynamicky škálování výběru pro všechny aktuální a budoucí hodnoty pro dimenzi.

8. Zobrazí se graf metriky za posledních 6 hodin. Definovat parametry upozornění; **Typ podmínky**, **frekvence**, **operátor** a **prahová hodnota** nebo **citlivosti**, tím se určení logiku, která se vyhodnotí jako pravidlo upozornění metriky. [Další informace o možnostech typu a citlivosti podmínku dynamickými prahovými hodnotami](alerts-dynamic-thresholds.md).

9. Pokud používáte statické prahové hodnoty, grafu metriky, vám pomůže určit, co může být rozumné prahovou hodnotu. Pokud používáte dynamické prahové hodnoty, metriky grafu se zobrazí vypočítané prahové hodnoty podle poslední data.

10. Klikněte na **Hotovo**.

11. Volitelně můžete přidáte další kritéria, pokud chcete sledovat komplexní pravidla výstrahy. Uživatelé nyní mohou mít pravidla upozornění s dynamickými prahovými hodnotami kritéria jako jediné kritérium.

12. Vyplňte **podrobnosti výstrahy** jako **název pravidla upozornění**, **popis** a **závažnost**

13. Přidání skupiny akcí k výstraze tak, že vyberete existující skupinu akcí nebo vytvořit novou skupinu akcí.

14. Klikněte na tlačítko **provádí** se uložit pravidlo upozornění metriky.

> [!NOTE]
> Upozornění na metriku pravidel vytvořených prostřednictvím portálu se vytvoří ve stejné skupině prostředků jako cílový prostředek.

## <a name="view-and-manage-with-azure-portal"></a>Umožňuje zobrazit a spravovat pomocí webu Azure portal

Můžete zobrazit a spravovat pravidla upozornění na metriky pomocí okna Spravovat pravidla v upozornění. Následující postup ukazuje, jak zobrazit pravidla upozornění metrik a upravit jeden z nich.

1. Na webu Azure portal, přejděte na **monitorování**

2. Klikněte na **výstrahy** a **spravovat pravidla**

3. V **spravovat pravidla** okně můžete zobrazit všechna pravidla výstrah napříč předplatnými. Dále můžete filtrovat pravidla pomocí **skupiny prostředků**, **typ prostředku** a **prostředků**. Pokud chcete zobrazit pouze upozornění metrik, vyberte **signalizuje, že typ** jako metriky.

    > [!TIP]
    > V **spravovat pravidla** okně můžete vybrat více pravidel upozornění a povolit nebo zakázat, je. To může být užitečné při určité cílové prostředky potřeba zavést ještě probíhá údržba

4. Klikněte na název metriky pravidlo výstrahy, kterou chcete upravit

5. V pravidle upravit, klikněte na **upozornění kritéria** chcete upravit. Můžete změnit metriky, podmínku prahové hodnoty a další pole, podle potřeby

    > [!NOTE]
    > Nelze upravovat **cílový prostředek** a **název pravidla upozornění** po vytvoření upozornění metriky.

6. Klikněte na tlačítko **provádí** uložit provedené úpravy.

## <a name="with-azure-cli"></a>S využitím rozhraní příkazového řádku Azure

V předchozích částech popsané, jak vytvořit, zobrazit a spravovat pravidla upozornění na metriky pomocí webu Azure portal. Tato část popisuje, jak provádět to stejné napříč platformami pomocí [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest). Nejrychlejší způsob, abyste mohli začít používat Azure CLI je prostřednictvím [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest). Pro účely tohoto článku budeme používat Cloud shell.

1. Přejděte na web Azure portal, klikněte na **Cloud shell**.

2. Do příkazového řádku můžete použít spolu s ``--help`` možnosti získat další informace o příkazu a jak ji používat. Například následující příkaz ukazuje, můžete seznam příkazů, které jsou k dispozici pro vytváření, zobrazení a Správa upozornění na metriku

    ```azurecli
    az monitor metrics alert --help
    ```

3. Můžete vytvořit jednoduché pravidlo metriky výstrahy, které monitoruje Pokud průměrné využití CPU na virtuálním počítači je větší než 90

    ```azurecli
    az monitor metrics alert create -n {nameofthealert} -g {ResourceGroup} --scopes {VirtualMachineResourceID} --condition "avg Percentage CPU > 90" --description {descriptionofthealert}
    ```

4. Ve skupině prostředků pomocí následujícího příkazu můžete zobrazit všechna upozornění metrik

    ```azurecli
    az monitor metrics alert list  -g {ResourceGroup}
    ```

5. Zobrazí se podrobnosti o konkrétní metriky upozornění pravidla s použitím názvu nebo ID prostředku pravidla.

    ```azurecli
    az monitor metrics alert show -g {ResourceGroup} -n {AlertRuleName}
    ```

    ```azurecli
    az monitor metrics alert show --ids {RuleResourceId}
    ```

6. Metriky pravidlo upozornění pomocí následujícího příkazu můžete zakázat.

    ```azurecli
    az monitor metrics alert update -g {ResourceGroup} -n {AlertRuleName} --enabled false
    ```

7. Metriky pravidlo upozornění pomocí následujícího příkazu můžete odstranit.

    ```azurecli
    az monitor metrics alert delete -g {ResourceGroup} -n {AlertRuleName}
    ```

## <a name="next-steps"></a>Další postup

- [Vytvoření upozornění na metriku pomocí šablony Azure Resource Manageru](../../azure-monitor/platform/alerts-enable-template.md).
- [Vysvětlení, jak metrika výstrahy pracovní](alerts-metric-overview.md).
- [Vysvětlení, jak metriky upozornění s dynamickými prahovými hodnotami podmínky práce](alerts-dynamic-thresholds.md).
- [Pochopení schématu webových hook pro upozornění na metriku](../../azure-monitor/platform/alerts-metric-near-real-time.md#payload-schema)


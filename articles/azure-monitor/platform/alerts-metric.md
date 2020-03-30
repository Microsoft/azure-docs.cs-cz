---
title: Vytváření, zobrazení a správa upozornění na metriky pomocí Azure Monitoru
description: Přečtěte si, jak pomocí portálu Azure nebo příkazového příkazu k použití příkazového příkazu k vytváření, zobrazení a správě pravidel upozornění na metriky.
author: harelbr
ms.author: harelbr
ms.topic: conceptual
ms.date: 03/13/2020
ms.subservice: alerts
ms.openlocfilehash: cefccd08ea66638f08f00e280fe2704444a7f916
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79369382"
---
# <a name="create-view-and-manage-metric-alerts-using-azure-monitor"></a>Vytváření, zobrazení a správa upozornění na metriky pomocí služby Azure Monitor

Upozornění metrik y ve Službě Azure Monitor poskytují způsob, jak se dostat upozorněni, když jedna z vašich metrik překročí prahovou hodnotu. Upozornění metrik fungují pro celou řadu vícedimenzionálních metrik platformy, vlastních metrik a standardních i vlastních metrik služby Application Insights. V tomto článku popíšeme, jak vytvořit, zobrazit a spravovat pravidla upozornění metriky prostřednictvím portálu Azure portal a Azure CLI. Pravidla upozornění metriky můžete také vytvořit pomocí šablon Azure Resource Manager, které jsou popsány v [samostatném článku](alerts-metric-create-templates.md).

Další informace o tom, jak upozornění na metriky fungují, najdete v [přehledu upozornění na metriky](alerts-metric-overview.md).

## <a name="create-with-azure-portal"></a>Vytvoření s využitím webu Azure Portal

Následující postup popisuje, jak vytvořit pravidlo upozornění metriky na webu Azure Portal:

1. Na [webu Azure Portal](https://portal.azure.com)klikněte na **Monitor**. Okno Monitor konsoliduje všechna nastavení monitorování a data v jednom zobrazení.

2. Klepněte na tlačítko **Výstrahy** a potom klepněte na tlačítko **+ Nové pravidlo výstrah**.

    > [!TIP]
    > Většina nožů prostředků má také **výstrahy** v jejich nabídce prostředků v části **Monitorování**, můžete také vytvořit výstrahy odtud.

3. Klikněte na **Vybrat cíl**, v podokně kontextu, které se načte, vyberte cílový prostředek, na který chcete upozornit. Pomocí rozevíracích seznamů **Typu odběr** a **Prostředek** vyhledejte prostředek, který chcete monitorovat. K vyhledání zdroje můžete také použít vyhledávací panel.

4. Pokud vybraný prostředek obsahuje metriky, na které můžete vytvářet výstrahy, budou **dostupné signály** v pravém dolním rohu obsahovat metriky. Úplný seznam typů prostředků podporovaných pro upozornění na metriky můžete zobrazit v tomto [článku](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported).

5. Po výběru cílového zdroje klikněte na **Přidat podmínku**.

6. Zobrazí se seznam signálů podporovaných pro prostředek, vyberte metriku, na kterou chcete vytvořit výstrahu.

7. Zobrazí se graf metriky za posledních šest hodin. Pomocí rozevíracího nabídky **Období grafu** vyberte zobrazení delší historie metriky.

8. Pokud metrika obsahuje dimenze, zobrazí se tabulka dimenzí. Vyberte jednu nebo více hodnot pro jednu dimenzi.
    - Zobrazené hodnoty dimenzí jsou založeny na metrických datech za poslední tři dny.
    - Pokud se nezobrazuje hodnota dimenze, kterou hledáte, kliknutím na tlačítko +přidejte vlastní hodnotu.
    - Můžete také ** \* vybrat** pro libovolnou kótu. **Výběr \* ** dynamicky změní velikost výběru na všechny aktuální a budoucí hodnoty dimenze.

    Pravidlo upozornění metriky vyhodnotí podmínku pro všechny kombinace vybraných hodnot. [Přečtěte si další informace o tom, jak funguje upozornění na vícerozměrné metriky](alerts-metric-overview.md).

9. Vyberte typ **prahová hodnota,** **operátor**a **typ agregace**. To určí logiku, kterou vyhodnotí pravidlo upozornění metriky.
    - Pokud používáte **statickou** prahovou hodnotu, pokračujte v definování **prahové hodnoty**. Graf metriky může pomoci určit, co může být přiměřená prahová hodnota.
    - Pokud používáte **dynamickou** prahovou hodnotu, pokračujte v definování **citlivosti prahu**. V tabulce metrik se zobrazí vypočtené prahové hodnoty na základě posledních dat. [Další informace o typu podmínky dynamické prahové hodnoty a možnosti citlivosti](alerts-dynamic-thresholds.md).

10. Volitelně můžete upravit podmínku úpravou **rozlišovací schopnosti agregace** a **četnosti hodnocení**. 

11. Klikněte na **Done** (Hotovo).

12. Volitelně můžete přidat další kritéria, pokud chcete sledovat složité pravidlo výstrahy. V současné době mohou uživatelé mít pravidla výstrah s kritérii dynamických prahových hodnot jako jediné kritérium.

13. Vyplňte **podrobnosti výstrahy,** jako **je název pravidla výstrahy**, **Popis**a **Závažnost**.

14. Přidejte skupinu akcí do výstrahy výběrem existující skupiny akcí nebo vytvořením nové skupiny akcí.

15. Kliknutím na **Hotovo** uložte pravidlo upozornění metriky.

> [!NOTE]
> Pravidla upozornění metriky vytvořená prostřednictvím portálu jsou vytvořena ve stejné skupině prostředků jako cílový prostředek.

## <a name="view-and-manage-with-azure-portal"></a>Zobrazení a správa pomocí portálu Azure

Pravidla upozornění na metriky můžete zobrazit a spravovat pomocí okna Spravovat pravidla v části Výstrahy. Následující postup ukazuje, jak zobrazit pravidla upozornění metriky a upravit jednu z nich.

1. Na webu Azure Portal přejděte na **Monitor.**

2. Klikněte na **Upozornění** a **správa pravidel**

3. V okně **Spravovat pravidla** můžete zobrazit všechna pravidla výstrah napříč předplatnými. Pravidla můžete dále filtrovat pomocí **skupiny prostředků**, **typu zdroje**a **zdroje**. Pokud chcete zobrazit jenom upozornění na metriky, vyberte **Typ signálu** jako metriky.

    > [!TIP]
    > V okně **Spravovat pravidla** můžete vybrat více pravidel výstrah a povolit nebo zakázat. To může být užitečné, pokud je třeba určité cílové zdroje uchovat

4. Klikněte na název pravidla upozornění metriky, které chcete upravit.

5. V pravidle pro úpravy klikněte na **kritéria výstrahy,** která chcete upravit. Metriku, prahovou podmínku a další pole můžete podle potřeby změnit.

    > [!NOTE]
    > Po vytvoření upozornění na metriku nelze upravit **cílový prostředek** a název **pravidla výstrahy.**

6. Kliknutím na **Hotovo** uložte úpravy.

## <a name="with-azure-cli"></a>S využitím rozhraní příkazového řádku Azure

V předchozích částech je popsáno, jak vytvořit, zobrazit a spravovat pravidla upozornění metriky pomocí portálu Azure Portal. Tato část popisuje, jak provést totéž pomocí [příkazového příkazu Azure pro](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)více platforem . Nejrychlejší způsob, jak začít používat Azure CLI je přes [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest). Pro tento článek použijeme Cloud Shell.

1. Přejděte na portál Azure a klikněte na **Cloud Shell**.

2. Na výzvu můžete pomocí příkazů ``--help`` s možností získat další informace o příkazu a jeho použití. Následující příkaz například zobrazuje seznam příkazů, které jsou k dispozici pro vytváření, prohlížení a správu upozornění na metriky.

    ```azurecli
    az monitor metrics alert --help
    ```

3. Můžete vytvořit jednoduché pravidlo upozornění metriky, které monitoruje, jestli je průměrný procentuální procesor na virtuálním počítači větší než 90

    ```azurecli
    az monitor metrics alert create -n {nameofthealert} -g {ResourceGroup} --scopes {VirtualMachineResourceID} --condition "avg Percentage CPU > 90" --description {descriptionofthealert}
    ```

4. Všechny upozornění metriky ve skupině prostředků můžete zobrazit pomocí následujícího příkazu.

    ```azurecli
    az monitor metrics alert list  -g {ResourceGroup}
    ```

5. Můžete zobrazit podrobnosti o konkrétní pravidlo upozornění metriky pomocí názvu nebo ID prostředku pravidla.

    ```azurecli
    az monitor metrics alert show -g {ResourceGroup} -n {AlertRuleName}
    ```

    ```azurecli
    az monitor metrics alert show --ids {RuleResourceId}
    ```

6. Pravidlo upozornění metriky můžete zakázat pomocí následujícího příkazu.

    ```azurecli
    az monitor metrics alert update -g {ResourceGroup} -n {AlertRuleName} --enabled false
    ```

7. Pravidlo upozornění metriky můžete odstranit pomocí následujícího příkazu.

    ```azurecli
    az monitor metrics alert delete -g {ResourceGroup} -n {AlertRuleName}
    ```

## <a name="next-steps"></a>Další kroky

- [Vytvářejte upozornění na metriky pomocí šablon Azure Resource Manager .](../../azure-monitor/platform/alerts-metric-create-templates.md)
- [Zjistěte, jak fungují upozornění na metriky](alerts-metric-overview.md).
- [Zjistěte, jak fungují upozornění na metriky s podmínkou dynamické prahové hodnoty](alerts-dynamic-thresholds.md).
- [Principy schématu webového háku pro upozornění na metriky](../../azure-monitor/platform/alerts-metric-near-real-time.md#payload-schema)


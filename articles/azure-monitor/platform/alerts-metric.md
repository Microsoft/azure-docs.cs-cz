---
title: Vytváření, zobrazování a správa výstrah metrik pomocí Azure Monitor
description: Naučte se pomocí Azure Portal nebo rozhraní příkazového řádku vytvářet, zobrazovat a spravovat pravidla výstrah metrik.
author: harelbr
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: harelbr
ms.subservice: alerts
ms.openlocfilehash: 00f5f37591ed2ed250cb756c686ea15136921512
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705526"
---
# <a name="create-view-and-manage-metric-alerts-using-azure-monitor"></a>Vytváření, zobrazování a správa výstrah metrik pomocí Azure Monitor

Upozornění metrik ve službě Azure Monitor poskytují způsob, jak dostávat upozornění, když nějaká metrika překročí prahovou hodnotu. Upozornění metrik fungují pro celou řadu vícedimenzionálních metrik platformy, vlastních metrik a standardních i vlastních metrik služby Application Insights. V tomto článku popisujeme postup vytvoření, zobrazení a správy pravidel upozornění metrik prostřednictvím webu Azure Portal a Azure CLI. Pravidla upozornění na metriky můžete vytvořit také pomocí šablon Azure Resource Manager, které jsou popsány v [samostatném článku](alerts-metric-create-templates.md).

Přečtěte si další informace o tom, jak výstrahy metrik fungují z [výstrah metriky](alerts-metric-overview.md).

## <a name="create-with-azure-portal"></a>Vytvořit pomocí Azure Portal

Následující postup popisuje, jak vytvořit pravidlo upozornění na metriku v Azure Portal:

1. V [Azure Portal](https://portal.azure.com)klikněte na **monitorování**. Okno monitor slučuje všechna nastavení monitorování a data v jednom zobrazení.

2. Klikněte na **výstrahy** a pak klikněte na **+ nové pravidlo výstrahy**.

    > [!TIP]
    > Většina oken prostředků má také **výstrahy** v nabídce prostředků v části **monitorování**, můžete také vytvořit výstrahy.

3. Klikněte na **vybrat cíl**a v kontextovém podokně, které se načte, vyberte cílový prostředek, na kterém chcete upozornit. Pro vyhledání prostředku, který chcete monitorovat, použijte rozevírací seznam pro **předplatné** a **typ prostředku** . K vyhledání prostředku můžete použít také panel hledání.

4. Pokud u vybraného prostředku existují metriky, na které můžete vytvářet výstrahy, budou **dostupné signály** v pravém dolním rohu obsahovat metriky. Úplný seznam typů prostředků podporovaných pro výstrahy metrik v tomto [článku](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported)si můžete prohlédnout.

5. Po výběru cílového prostředku klikněte na **Přidat podmínku**.

6. Zobrazí se seznam signálů podporovaných pro daný prostředek, vyberte metriku, na které chcete vytvořit výstrahu.

7. Volitelně můžete metriku Upřesnit úpravou **period** a **agregace**. Pokud má metrika rozměry, zobrazí se tabulka **Dimensions** . Vyberte jednu nebo více hodnot na dimenzi. Výstraha metriky spustí vyhodnotit podmínku pro všechny kombinace hodnot, které jsou vybrány. [Přečtěte si další informace o tom, jak funguje upozorňování na multidimenzionální metriky](alerts-metric-overview.md). Můžete také **vybrat \*** pro libovolnou dimenzi. **Vyberte možnost \*** bude dynamicky škálovat výběr na všechny aktuální a budoucí hodnoty pro dimenzi.

8. Zobrazí se graf metriky za posledních 6 hodin. Zadejte parametry výstrahy; **Typ podmínky**, **frekvence**, **operátor** a **prahová hodnota** nebo **Citlivost**, tím se určí logika, kterou bude pravidlo výstrahy metriky vyhodnocovat. [Přečtěte si další informace o typu dynamické prahové hodnoty a možnosti citlivosti](alerts-dynamic-thresholds.md).

9. Pokud používáte statickou prahovou hodnotu, graf metriky vám pomůže určit, co může být přiměřenou prahovou hodnotou. Pokud používáte dynamické prahové hodnoty, v grafu metriky se zobrazí vypočtené prahové hodnoty na základě nedávných dat.

10. Klikněte na **Hotovo**.

11. Případně můžete přidat další kritéria, pokud chcete monitorovat složité pravidlo výstrahy. Aktuálně uživatelé mohou mít pravidla upozornění s dynamickými kritérii prahové hodnoty jako jedno kritérium.

12. Vyplnit **Podrobnosti výstrahy** , jako **je název pravidla výstrahy**, **Popis** a **závažnost**

13. Přidejte skupinu akcí k výstraze buď výběrem existující skupiny akcí, nebo vytvořením nové skupiny akcí.

14. Kliknutím na **Hotovo** uložte pravidlo upozornění na metriku.

> [!NOTE]
> Pravidla upozornění na metriky vytvořená prostřednictvím portálu se vytvoří ve stejné skupině prostředků jako cílový prostředek.

## <a name="view-and-manage-with-azure-portal"></a>Zobrazení a správa pomocí Azure Portal

Pravidla upozornění na metriky můžete zobrazit a spravovat pomocí okna spravovat pravidla v části výstrahy. Následující postup ukazuje, jak zobrazit pravidla upozornění na metriky a upravit jednu z nich.

1. V Azure Portal přejděte na **monitorování** .

2. Kliknutí na **výstrahy** a **Spravovat pravidla**

3. V okně **Spravovat pravidla** můžete zobrazit všechna pravidla výstrah v rámci předplatných. Pravidla můžete dál filtrovat pomocí **skupiny prostředků**, **typu prostředku** a **prostředku**. Pokud chcete zobrazit jenom výstrahy metriky, vyberte **typ signálu** jako metriky.

    > [!TIP]
    > V okně **Spravovat pravidla** můžete vybrat více pravidel upozornění a povolit nebo zakázat. To může být užitečné, když je potřeba zařadit do údržby určité cílové prostředky.

4. Klikněte na název pravidla upozornění metrik, které chcete upravit.

5. V pravidle úprav klikněte na **kritéria výstrahy** , která chcete upravit. Metriky, podmínky prahové hodnoty a další pole můžete změnit podle potřeby.

    > [!NOTE]
    > Po vytvoření výstrahy metriky nemůžete upravit název **cílového prostředku** a **pravidla výstrahy** .

6. Kliknutím na **Hotovo** uložte úpravy.

## <a name="with-azure-cli"></a>S využitím rozhraní příkazového řádku Azure

Předchozí části popisují, jak vytvářet, zobrazovat a spravovat pravidla upozornění metrik pomocí Azure Portal. V této části se dozvíte, jak to samé provést pomocí [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)pro různé platformy. Nejrychlejší způsob, jak začít používat Azure CLI, je prostřednictvím [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest). V tomto článku použijeme Cloud Shell.

1. Přejděte na Azure Portal, klikněte na **Cloud Shell**.

2. V příkazovém řádku můžete k získání dalších informací o příkazu a jeho použití použít příkazy s možností ``--help``. Například následující příkaz zobrazí seznam příkazů, které jsou k dispozici pro vytváření, zobrazování a správu upozornění na metriky.

    ```azurecli
    az monitor metrics alert --help
    ```

3. Můžete vytvořit jednoduché pravidlo výstrahy metriky, které monitoruje, jestli je průměrný procentuální procesor na virtuálním počítači větší než 90.

    ```azurecli
    az monitor metrics alert create -n {nameofthealert} -g {ResourceGroup} --scopes {VirtualMachineResourceID} --condition "avg Percentage CPU > 90" --description {descriptionofthealert}
    ```

4. Všechny výstrahy metriky ve skupině prostředků můžete zobrazit pomocí následujícího příkazu.

    ```azurecli
    az monitor metrics alert list  -g {ResourceGroup}
    ```

5. Podrobnosti konkrétního pravidla výstrahy metriky můžete zobrazit pomocí názvu nebo ID prostředku pravidla.

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

- [Vytvářejte výstrahy metriky pomocí šablon Azure Resource Manager](../../azure-monitor/platform/alerts-enable-template.md).
- [Pochopte, jak fungují výstrahy metrik](alerts-metric-overview.md).
- [Seznamte se s tím, jak výstrahy metrik s podmínkou dynamického prahu fungují](alerts-dynamic-thresholds.md)
- [Princip schématu webového zavěšení pro výstrahy metrik](../../azure-monitor/platform/alerts-metric-near-real-time.md#payload-schema)


---
title: Začínáme se službou Azure Monitor
description: Začněte používat Azure Monitor, získejte přehled o provozu svých prostředků a provádějte akce podle dat.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/25/2018
ms.author: johnkem
ms.component: ''
ms.openlocfilehash: 70807db256f72b77bb29db3f6f59474a892f2939
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35263357"
---
# <a name="get-started-with-azure-monitor"></a>Začínáme se službou Azure Monitor
Azure Monitor je služba platformy, která poskytuje jeden zdroj pro monitorování prostředků Azure. S monitorováním Azure můžete vizualizovat, dotaz, směrovat, archivaci a v opačném případě proveďte akce na metriky a protokoly pocházejících z prostředků v Azure. Můžete pracovat s dat pomocí pomocí portálu Azure [rutiny prostředí PowerShell monitorování](insights-powershell-samples.md), [rozhraní příkazového řádku a platformy](insights-cli-samples.md), nebo [rozhraní REST API Azure monitorování](https://msdn.microsoft.com/library/dn931943.aspx). V tomto článku vás seznámíme s několika klíčovými komponentami služby Azure Monitor a pro ukázky budeme používat portál.

## <a name="walkthrough"></a>Názorný postup
1. Na portálu, přejděte na **všechny služby** a najděte **monitorování** možnost. Kliknutím na ikonu hvězdičky si tuto možnost přidejte do seznamu oblíbených položek, abyste k ní neustále měli snadný přístup z levého navigačního panelu.

    ![Služba Monitor v seznamu služeb](./media/monitoring-get-started/monitor-more-services.png)
2. Klikněte **monitorování** možnost otevře **monitorování** stránky. Tato stránka spojuje veškeré monitorování nastavení a data do jednoho konsolidované zobrazení. Nejprve otevře se **přehled** části. V přehledu zobrazuje souhrn výstrah monitorování, chyb a informační služby stavu zpravodaje, které se vztahují k prostředkům ve vašem předplatném.  

    ![Monitorování navigace](./media/monitoring-get-started/monitor-blade-nav.png)

    Azure Monitor nabízí tři základní kategorie monitorování dat: **protokol aktivit**, **metriky** a **diagnostické protokoly**.
3. Kliknutím na **Protokol aktivit** zajistíte zobrazení části s protokolem aktivit.

    [**Protokol aktivit**](monitoring-overview-activity-logs.md) popisuje všechny operace prováděné s prostředky v rámci vašeho předplatného. Na základě protokolu aktivit můžete určit „co, kdo a kdy“ pro každou operaci vytvoření, aktualizace nebo odstranění týkající se prostředků v rámci vašeho předplatného. Protokol aktivit například obsahuje informace o tom, kdy byla webová aplikace zastavena a kdo ji zastavil. Události protokolu aktivit se ukládají v rámci platformy a pro dotazy jsou k dispozici po dobu 90 dní.

    ![Protokol aktivit](./media/monitoring-get-started/monitor-act-log-blade.png)

    Můžete vytvářet a ukládat dotazy pro běžné filtry a pak připnout nejdůležitější dotazy na řídicí panel portálu. Díky tomu se dozvíte o všech výskytech událostí, které splňují příslušná kritéria.
4. V zobrazení nastavte filtrování konkrétní skupiny prostředků za poslední týden. Pak klikněte na tlačítko **Uložit**. Zadejte název dotazu.

    ![Uložení dotazu protokolu aktivit](./media/monitoring-get-started/monitor-act-log-save.png)
5. Nyní klikněte na tlačítko **Pin** (Připnout).

    ![Kliknutí na tlačítko Pin (Připnout) pro protokol aktivit](./media/monitoring-get-started/monitor-act-log-pin.png)

    Většinu zobrazení v tomto návodu je možné připnout na řídicí panel. Díky tomu si můžete vytvořit jediný zdroj informací o provozních datech pro služby.
6. Vraťte se na řídicí panel. Nyní je vidět, že se na řídicím panelu zobrazuje příslušný dotaz (a počet výsledků). To je užitečné, pokud chcete rychle zobrazit všechny vysoce profil akce, které nedávno došlo v rámci vašeho předplatného, například byl přiřazen novou roli nebo virtuální počítač byl odstraněn.

    ![Protokoly aktivity připnuli k řídicímu panelu](./media/monitoring-get-started/monitor-act-log-db.png)
7. Vraťte se na dlaždici **Monitor** a klikněte na sekci **Metriky**. Je nutné nejprve vybrat prostředek filtrování a výběrem pomocí možností v rozevírací seznam v horní části stránky.

    ![Filtr prostředků pro metriky](./media/monitoring-get-started/monitor-met-filter.png)

    Všechny prostředky Azure generují [**metriky**](monitoring-overview-metrics.md). V toto zobrazení jsou všechny metriky uvedeny společně v podokně ze skla. Díky tomu je možné snadno zjistit, jak prostředky fungují. Také, podívejte se na naše brand [nové metrika grafů prostředí](https://aka.ms/azuremonitor/new-metrics-charts) kliknutím na **metriky (preview)** kartě.
8. Po výběru prostředku, zobrazí se všechny dostupné metriky na levé straně stránky. V grafu můžete najednou zobrazit několik metrik, pokud je vyberete a upravíte typ grafu a časový rozsah. Můžete také zobrazit všechna upozornění na metriky, která jsou pro tento prostředek nastavená.

    ![Okno Metrika](./media/monitoring-get-started/monitor-metric-blade.png)

   > [!NOTE]
   > Některé metriky jsou k dispozici pouze povolením [Application Insights](../application-insights/app-insights-overview.md) nebo Windows nebo Linux Azure Diagnostics rozšíření na vašem prostředku.
   >
   >

9. Až budete s grafem spokojeni, můžete ho připnout na řídicí panel pomocí tlačítka **Pin** (Připnout).
10. Vraťte se do **monitorování** a klikněte na tlačítko **diagnostické protokoly**.

    ![Okno Diagnostické protokoly](./media/monitoring-get-started/monitor-diaglogs-blade.png)

    [**Diagnostické protokoly**](monitoring-overview-of-diagnostic-logs.md) jsou protokoly generované *prostředkem* a poskytují data o provozu příslušného prostředku. Mezi typy diagnostických protokolů patří například počítadla pravidel skupin zabezpečení sítě nebo protokoly pracovního postupu aplikací logiky. Tyto protokoly se můžou ukládat v rámci účtu úložiště, streamovat do Centra událostí a případně odesílat do [Log Analytics](../log-analytics/log-analytics-overview.md). Log Analytics je produkt Microsoftu, který pracuje s provozními analytickými informacemi a umožňuje pokročilé hledání a generování upozornění.

    Na portálu můžete zobrazit a filtrovat seznam všech prostředků v rámci svého předplatného a ověřit, jestli jsou pro ně povolené diagnostické protokoly.
    > [!NOTE]
    > Odesílání vícedimenzionálních metrik přes nastavení diagnostiky se v současné době nepodporuje. Metriky s dimenzemi se exportují jako ploché jednodimenzionální metriky agregované napříč hodnotami dimenzí.
    >
    > *Příklad:* Metriku Příchozí zprávy v centru událostí je možné zkoumat a převést na graf na úrovni jednotlivých front. Pokud se však metrika exportuje přes nastavení diagnostiky, bude reprezentovaná jako všechny příchozí zprávy ve všech frontách v centru událostí.
    >
    >

11. Klikněte na prostředek na stránce diagnostické protokoly. Pokud se diagnostické protokoly ukládají v rámci účtu úložiště, zobrazí se seznam protokolů po hodinách. Tyto protokoly můžete přímo stahovat.

    ![Diagnostické protokoly pro jeden prostředek](./media/monitoring-get-started/monitor-diaglogs-detail.png)

    Můžete také kliknout na **Nastavení diagnostiky**. Tímto způsobem můžete nastavit nebo změnit nastavení pro archivaci do účtu úložiště, streamování do služby Event Hubs nebo odesílání do pracovního prostoru Log Analytics.

    ![Povolení diagnostických protokolů](./media/monitoring-get-started/monitor-diaglogs-enable.png)

    Pokud jste nastavili odesílání diagnostických protokolů do Log Analytics, můžete v nich pak hledat na portálu v části **Hledání v protokolu**.
12. Přejděte na **výstrahy (klasické)** části stránky monitorování.

    ![Okno upozornění pro veřejnost](./media/monitoring-get-started/monitor-alerts-nopp.png)

    Tady můžete spravovat všechny [ **classic výstrahy** ](monitoring-overview-alerts.md) na vašich prostředků Azure. To zahrnuje výstrahy na metriky, aktivity protokolu události, testy webu Application Insights (umístění) a proaktivní diagnostics Application Insights. Výstrahy se připojit ke skupinám akce. [Akce skupiny](monitoring-action-groups.md) poskytnout způsob, jak upozornit uživatele, kteří nebo provádět určité akce, když se aktivuje výstrahu.

13. Klikněte na **Add metric alert** (Přidat upozornění metriky) a vytvořte upozornění.

    ![Přidání upozornění metriky](./media/monitoring-get-started/monitor-alerts-add.png)

    Pak můžete upozornění připnout na řídicí panel, abyste mohli kdykoli snadno zjistit jeho stav.

    Azure monitorování teď má také [ **novější výstrahy** ](https://aka.ms/azuremonitor/near-real-time-alerts) který lze vyhodnotit frekvencí co nejnižší každou minutu.

14. Sekce Monitor obsahuje také odkazy na aplikace [Application Insights](../application-insights/app-insights-overview.md) a na řešení správy [Log Analytics](../log-analytics/log-analytics-overview.md). Tyto produkty společnosti Microsoft jsou hluboce integrovány se službou Azure Monitor.
15. Pokud nepoužíváte Application Insights nebo Log Analytics, je pravděpodobné, že je služba Azure Monitor propojená s vašimi aktuálními produkty zajišťujícími monitorování, protokolování a generování upozornění. Úplný seznam spolu s pokyny k integraci najdete na naší [stránce s přehledem partnerů](monitoring-partners.md).

Provedením těchto kroků a připnutím všech relevantních dlaždic na řídicí panel můžete vytvářet komplexní zobrazení svých aplikací a infrastruktury, například takto:

![Řídicí panel služby Azure Monitor](./media/monitoring-get-started/monitor-final-dash.png)

## <a name="next-steps"></a>Další postup
* Pro čtení [přehled nástroje pro monitorování všech Azure](monitoring-overview.md) pochopit, jak funguje Azure monitorování s nimi.

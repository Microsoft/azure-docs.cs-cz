---
title: Export do Power BI ze služby Azure Application Insights | Dokumentace Microsoftu
description: Analytické dotazy můžete zobrazit v Power BI.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 7f13ea66-09dc-450f-b8f9-f40fdad239f2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 08/10/2018
ms.author: mbullwin
ms.openlocfilehash: 1a5f756c4fac92260e77b24ca2cde6abccf0797a
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54042780"
---
# <a name="feed-power-bi-from-application-insights"></a>Informační kanál Power BI ze služby Application Insights
[Power BI](https://www.powerbi.com/) je sada nástrojů pro obchodní, která pomáhá analyzovat data a sdílet přehledy. Bohaté řídicí panely jsou k dispozici na všech zařízeních. Můžete kombinovat data z mnoha zdrojů, včetně analytických dotazů z [Azure Application Insights](app-insights-overview.md).

Export dat Application Insights do Power BI třemi způsoby:

* [**Export analytických dotazů**](#export-analytics-queries). Toto je upřednostňovaná metoda. Zapisovat všechny dotazu má a exportovat je do Power BI. Tento dotaz můžete umístit na řídicím panelu, spolu s ostatními daty.
* [**Průběžný export a Azure Stream Analytics**](../azure-monitor/app/export-stream-analytics.md). Tato metoda je užitečná, pokud chcete ukládat data na dlouhou dobu. Pokud nemáte požadavek na uchování Rozšířená data, použijte metodu dotazu analytics exportu. Průběžný export a Stream Analytics zahrnuje další práce pro nastavení a režijní náklady na další úložiště.
* [**Power BI adaptér**](#power-pi-adapter). Předdefinovaná sada grafy, ale můžete přidat vlastní dotazy z jiných zdrojů.

> [!NOTE]
> Adaptér pro Power BI je nyní **zastaralé**. Předdefinované grafy pro toto řešení se vyplní statické nelze upravovat dotazy. Nemáte možnost upravit tyto dotazy a v závislosti na určitých vlastností objektu data je možné pro připojení k Power BI a být úspěšné, ale je vyplněný žádná data. To je způsobeno kritéria vyloučení, které jsou nastavené v rámci pevně zakódované dotazu. Když toto řešení může i nadále fungovat pro některé zákazníky, vzhledem k absenci flexiblity adaptéru doporučené řešení je použít [ **exportovat dotaz Analytics** ](#export-analytics-queries) funkce.

## <a name="export-analytics-queries"></a>Export analytických dotazů
Tato trasa umožňuje psát jakýkoli dotaz Analytics nebo exportovat z trychtýře využití a export, který na řídicí panel Power BI. (Můžete přidat na řídicí panel vytvořený adaptér.)

### <a name="one-time-install-power-bi-desktop"></a>Jednou: instalace Power BI Desktopu
Chcete-li importovat dotaz Application Insights, použijete desktopovou verzi Power BI. Můžete ho potom publikovat na web nebo do pracovního prostoru Power BI cloudu. 

Nainstalujte [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).

### <a name="export-an-analytics-query"></a>Export dotazu Analytics
1. [Otevřít analýzu a napsat dotaz](../azure-monitor/log-query/get-started-portal.md).
2. Testování a až budete spokojení s výsledky upřesněte dotaz. Ujistěte se, že dotaz běží správně v Analytics předtím, než ho exportovat.
3. Na **exportovat** nabídce zvolte **Power BI (M)**. Uložte tento textový soubor.
   
    ![Snímek obrazovky služby Analytics se zvýrazněnou nabídky Export](./media/app-insights-export-power-bi/analytics-export-power-bi.png)
4. V Power BI Desktopu, vyberte **získat Data** > **prázdný dotaz**. Potom v editoru dotazů v rámci **zobrazení**vyberte **rozšířený Editor**.

    Vložte do rozšířeného editoru exportovaný skript jazyka M.

    ![Snímek obrazovky s Power BI Desktopu, se zvýrazněným rozšířený Editor](./media/app-insights-export-power-bi/power-bi-import-analytics-query.png)

5. Povolte Power BI přístup k Azure, bude pravděpodobně k zadání přihlašovacích údajů. Použití **účet organizace** se přihlásit pomocí účtu Microsoft.
   
    ![Dialogové okno nastavení snímku obrazovky z Power BI dotazu](./media/app-insights-export-power-bi/power-bi-import-sign-in.png)

    Pokud potřebujete k ověření přihlašovacích údajů, použijte **nastavení zdroje dat** příkazu nabídky v editoru dotazů. Nezapomeňte zadat přihlašovací údaje, které používáte pro Azure, který se může lišit od přihlašovacích údajů pro Power BI.
6. Zvolte vizualizaci pro váš dotaz a vyberte pole pro osu x, y a segmentace dimenze.
   
    ![Snímek obrazovky s Power BI Desktopu Možnosti vizualizace](./media/app-insights-export-power-bi/power-bi-analytics-visualize.png)
7. Publikování sestavy do pracovního prostoru Power BI cloudu. Odtud můžete vkládat synchronizované verze do jiných webových stránek.
   
    ![Snímek obrazovky s Power BI Desktopu, se zvýrazněným tlačítkem Publikovat](./media/app-insights-export-power-bi/publish-power-bi.png)
8. Aktualizovat sestavu ručně v intervalech nebo nastavit plánovanou aktualizaci na stránce Možnosti.

### <a name="export-a-funnel"></a>Export trychtýře
1. [Ujistěte se, svém trychtýři](../azure-monitor/app/usage-funnels.md).
2. Vyberte **Power BI**.

   ![Snímek obrazovky s Power BI tlačítko](./media/app-insights-export-power-bi/button.png)

3. V Power BI Desktopu, vyberte **získat Data** > **prázdný dotaz**. Potom v editoru dotazů v rámci **zobrazení**vyberte **rozšířený Editor**.

   ![Snímek obrazovky s Power BI Desktopu, se zvýrazněným tlačítkem prázdný dotaz](./media/app-insights-export-power-bi/blankquery.png)

   Vložte do rozšířeného editoru exportovaný skript jazyka M. 

   ![Snímek obrazovky s Power BI Desktopu, se zvýrazněným rozšířený Editor](./media/app-insights-export-power-bi/advancedquery.png)

4. Vybrat položky z dotazu a zvolte vizualizaci trychtýřového grafu.

   ![Snímek obrazovky s Power BI Desktopu Možnosti vizualizace](./media/app-insights-export-power-bi/selectsequence.png)

5. Změna názvu, aby byl smysluplný a publikování sestavy do pracovního prostoru Power BI cloudu. 

   ![Snímek obrazovky s Power BI Desktopu, se zvýrazněným změnit název](./media/app-insights-export-power-bi/changetitle.png)

## <a name="troubleshooting"></a>Řešení potíží

Mohou se vyskytnout chyby vztahující se k přihlašovacím údajům nebo velikost datové sady. Zde jsou některé informace o tom, co chcete o těchto chybách.

### <a name="unauthorized-401-or-403"></a>Neoprávněné (401 nebo 403)
To může nastat, pokud ještě není aktualizovaný obnovovací token. Proveďte následující kroky k zajištění, že budete mít dál přístup:

1. Přihlaste se na webu Azure portal a ujistěte se, že umožňuje přístup k prostředku.
2. Pokuste se aktualizovat přihlašovací údaje pro řídicí panel.

 Pokud máte přístup a aktualizovat přihlašovací údaje nelze použít, otevřete prosím lístek podpory.

### <a name="bad-gateway-502"></a>Chybná brána (502)
To je obvykle způsobeno dotazu Analytics, která vrací data příliš mnoho. Zkuste použít menší časový rozsah dotazu. 

Pokud snižování datové sady z dotazu Analytics nesplňuje vaše požadavky, zvažte použití [API](https://dev.applicationinsights.io/documentation/overview) přetahování větší datové sady. Tady je postup převedení export M dotaz k používání rozhraní API.

1. Vytvoření [klíč rozhraní API](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).
2. Aktualizace Power BI M skript, který jste exportovali z Analytics tak, že nahradíte adresu URL Azure Resource Manageru pomocí rozhraní API pro Application Insights.
   * Nahraďte  **https://management.azure.com/subscriptions/...**
   * s  **https://api.applicationinsights.io/beta/apps/...**
3. Nakonec aktualizujte přihlašovací údaje na základní a použijte svůj klíč rozhraní API.

**Existujícího skriptu**
 ```
 Source = Json.Document(Web.Contents("https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups//providers/microsoft.insights/components//api/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```
**Aktualizovaný skript**
 ```
 Source = Json.Document(Web.Contents("https://api.applicationinsights.io/beta/apps/<APPLICATION_ID>/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```

## <a name="about-sampling"></a>Informace o vzorkování
V závislosti na množství dat odeslaných vaší aplikací můžete chtít použít funkce adaptivního vzorkování, který odesílá pouze procento vaší telemetrie. Totéž platí, pokud jste ručně nastavili vzorkování v sadě SDK nebo na základě ingestování. [Další informace o vzorkování](../azure-monitor/app/sampling.md).

## <a name="power-bi-adapter-deprecated"></a>Adaptér Power BI (zastaralé)
Tato metoda vytvoří kompletní řídicí panel telemetrických dat za vás. Počáteční datová sada je předdefinovaná, ale můžete přidat další data k němu.

### <a name="get-the-adapter"></a>Získat adaptéru
1. Přihlaste se k [Power BI](https://app.powerbi.com/).
2. Otevřít **získat Data** ![snímek obrazovky GetData ikony v levém dolním rohu](./media/app-insights-export-power-bi/001.png), **služby**.

    ![Snímky obrazovky získat ze zdroje dat Application Insights](./media/app-insights-export-power-bi/002.png)

3. Vyberte **získat** v Application Insights.

   ![Snímky obrazovky získat ze zdroje dat Application Insights](./media/app-insights-export-power-bi/003.png)
4. Zadejte podrobnosti o prostředku Application Insights a potom **přihlášení**.

    ![Snímek obrazovky získat ze zdroje dat Application Insights](./media/app-insights-export-power-bi/005.png)

     Tyto informace můžete najít v podokně s přehledem Application Insights:

     ![Snímek obrazovky získat ze zdroje dat Application Insights](./media/app-insights-export-power-bi/004.png)

5. Otevřete nově vytvořené aplikace Application Insights Power BI.

6. Počkejte minutu nebo dvě data, která mají být importována.

    ![Snímek obrazovky s Power BI adaptéru](./media/app-insights-export-power-bi/010.png)

Řídicí panel, můžete upravit kombinaci grafy Application Insights s uživateli z jiných zdrojů a pomocí analytických dotazů. Můžete získat další grafy v galerii vizualizací a každý graf obsahuje parametry, které můžete nastavit.

Po počátečním importu se pokračovat řídicí panel a sestavy denně aktualizovat. Můžete nastavit plán aktualizace datové sady.

## <a name="next-steps"></a>Další postup
* [Power BI – Další informace](https://www.powerbi.com/learning/)
* [Kurz analýzy](../azure-monitor/log-query/get-started-portal.md)


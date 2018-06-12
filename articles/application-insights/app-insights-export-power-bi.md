---
title: Export do služby Power BI ze služby Azure Application Insights | Microsoft Docs
description: Analytické dotazy lze zobrazit v Power BI.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 7f13ea66-09dc-450f-b8f9-f40fdad239f2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 10/18/2016
ms.author: mbullwin
ms.openlocfilehash: dee3313082fbe75d76bf27105979cf7e869fafad
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294118"
---
# <a name="feed-power-bi-from-application-insights"></a>Kanálu Power BI z Application Insights
[Power BI](http://www.powerbi.com/) je sada nástrojů, firmy, která pomáhá analyzovat data a sdílet uplatnitelné. Bohaté řídicí panely jsou k dispozici na každé zařízení. Můžete kombinovat data z mnoha zdrojů, včetně analytické dotazy z [Azure Application Insights](app-insights-overview.md).

Existují tři doporučené metody export dat Application Insights do Power BI. Můžete je používat samostatně nebo společně.

* [**Power BI adaptér**](#power-pi-adapter). Nastavte kompletní řídicí panel telemetrie z vaší aplikace. Předdefinovaná sadu grafy, ale můžete přidat vlastní dotazy z jiných zdrojů.
* [**Export analytické dotazy**](#export-analytics-queries). Zapsat všechny dotazu má a exportovat je do Power BI. Můžete napsat dotaz pomocí analýzy, nebo můžete zapsat je z nálevky využití. Tento dotaz můžete umístit na řídicím panelu, společně s dalšími daty.
* [**Průběžné exportu a Azure Stream Analytics**](app-insights-export-stream-analytics.md). Tato metoda je užitečná, pokud chcete zachovat data dlouhou dobu. Pokud to neuděláte, použijte jednu z jiné metody, protože tato zahrnuje další práci nastavit.

## <a name="power-bi-adapter"></a>Adaptér Power BI
Tato metoda vytvoří kompletní řídicí panel telemetrie. Předdefinovaná počátečních datových sad, ale do něj může přidat další data.

### <a name="get-the-adapter"></a>Získat adaptéru
1. Přihlaste se k [Power BI](https://app.powerbi.com/).
2. Otevřete **získat Data**, **služby**a potom **Application Insights**.
   
    ![Snímky obrazovky získat ze zdroje dat Application Insights](./media/app-insights-export-power-bi/power-bi-adapter.png)
3. Zadejte podrobnosti o prostředku Application Insights.
   
    ![Snímek obrazovky získat ze zdroje dat Application Insights](./media/app-insights-export-power-bi/azure-subscription-resource-group-name.png)
4. Počkejte, než minutu nebo dvě pro data, která bude importována.
   
    ![Snímek obrazovky Power BI adaptéru](./media/app-insights-export-power-bi/010.png)

Můžete upravit řídicí panel, kombinace Application Insights grafy s u jiných zdrojů a se analytické dotazy. Můžete získat další grafy v galerii vizualizace a každý graf obsahuje parametry, které můžete nastavit.

Po počáteční importu nadále denně aktualizovat řídicí panel a sestavy. Můžete řídit plán aktualizace na datovou sadu.

## <a name="export-analytics-queries"></a>Export analytické dotazy
Tato trasa umožňuje zápis jakýkoli dotaz analýzy nebo exportovat z nálevky využití a export, na řídicí panel Power BI. (Můžete přidat na řídicí panel vytvořený adaptér.)

### <a name="one-time-install-power-bi-desktop"></a>Jednou: Nainstalujte Power BI Desktop
Chcete-li importovat dotaz Application Insights, použijete verze aplikace Power BI. Můžete jej pak publikovat na web nebo do pracovního prostoru cloudu Power BI. 

Nainstalujte [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).

### <a name="export-an-analytics-query"></a>Export dotaz Analytics
1. [Otevřete analýzy a napsat dotaz](app-insights-analytics-tour.md).
2. Testování a upřesnění dotazu, až budete s výsledky spokojeni. Ujistěte se, že dotaz běží správně v Analytics předtím, než ho exportovat.
3. Na **exportovat** nabídce zvolte **Power BI (M)**. Uložte tento textový soubor.
   
    ![Snímek obrazovky z Analytics se zvýrazněnou nabídky Export](./media/app-insights-export-power-bi/analytics-export-power-bi.png)
4. V Power BI Desktop, vyberte **načíst Data** > **prázdné dotazu**. Potom v editoru dotazů v rámci **zobrazení**, vyberte **Upřesnit**.

    Vložte do editoru Advanced exportovaný skript jazyka M.

    ![Snímek obrazovky z Power BI Desktop s Advanced Editor zvýrazněná](./media/app-insights-export-power-bi/power-bi-import-analytics-query.png)

1. Aby se mohl Power BI pro přístup k Azure, možná muset zadat přihlašovací údaje. Použití **účet organizace** se přihlásit pomocí účtu Microsoft.
   
    ![Dialogové okno snímek obrazovky z Power BI dotazu nastavení](./media/app-insights-export-power-bi/power-bi-import-sign-in.png)

    Pokud potřebujete ověření přihlašovacích údajů, použijte **nastavení zdroje dat** příkazu nabídky v editoru dotazů. Nezapomeňte zadat přihlašovací údaje, které používáte pro Azure, což může být liší od přihlašovacích údajů pro Power BI.
2. Zvolte vizualizaci dotazu a vyberte pole pro osy x, y a segmentace dimenze.
   
    ![Snímek obrazovky Power BI Desktop vizualization možnosti](./media/app-insights-export-power-bi/power-bi-analytics-visualize.png)
3. Publikujte sestavy do pracovního prostoru cloudu Power BI. Odtud můžete synchronizované verze vložit do jiných webových stránek.
   
    ![Snímek obrazovky z Power BI Desktop s tlačítkem publikování](./media/app-insights-export-power-bi/publish-power-bi.png)
4. Sestavu v intervalech aktualizovat ručně, nebo nastavit plánovaná aktualizace na stránce Možnosti.

### <a name="export-a-funnel"></a>Export trychtýřového grafu
1. [Ujistěte se, vaše trychtýřového grafu](usage-funnels.md).
2. Vyberte **Power BI**. 

   ![Tlačítko – snímek obrazovky Power BI](./media/app-insights-export-power-bi/button.png)
   
3. V Power BI Desktop, vyberte **načíst Data** > **prázdné dotazu**. Potom v editoru dotazů v rámci **zobrazení**, vyberte **Upřesnit**.

   ![Snímek obrazovky z Power BI Desktop s tlačítkem prázdné dotazu](./media/app-insights-export-power-bi/blankquery.png)

   Vložte do editoru Advanced exportovaný skript jazyka M. 

   ![Snímek obrazovky z Power BI Desktop s Advanced Editor zvýrazněná](./media/app-insights-export-power-bi/advancedquery.png)

4. Vyberte položky z dotazu a vyberte vizualizace trychtýřového grafu.

   ![Snímek obrazovky Power BI Desktop vizualization možnosti](./media/app-insights-export-power-bi/selectsequence.png)

5. Změňte název, aby byl smysluplný a publikovat sestavy do pracovního prostoru cloudu Power BI. 

   ![Snímek obrazovky z Power BI Desktop se zvýrazněnou změnu názvu](./media/app-insights-export-power-bi/changetitle.png)

## <a name="troubleshooting"></a>Řešení potíží

Můžete se setkat chyby týkající se přihlašovací údaje nebo velikost datové sady. Tady jsou některé informace o tom, jak se o tyto chyby.

### <a name="unauthorized-401-or-403"></a>Neoprávněné (401 nebo 403)
To může dojít, pokud zatím není aktualizovaná obnovovací token. Opakujte tyto kroky, které zajišťují, že máte pořád přístup:

1. Přihlaste se k portálu Azure a ujistěte se, že má přístup k prostředku.
2. Pokuste se aktualizovat přihlašovací údaje pro řídicí panel.

 Pokud máte přístup a aktualizovat přihlašovací údaje se nedají použít, otevřete prosím lístek podpory.

### <a name="bad-gateway-502"></a>Chybná brána (502)
To je obvykle způsobeno dotazu analýzy, který vrátí příliš mnoho dat. Zkuste použít menší časový interval pro dotaz. 

Pokud snižuje datovou sadu pocházejících z dotazu Analytics nesplňuje vaše požadavky, zvažte použití [rozhraní API](https://dev.applicationinsights.io/documentation/overview) vyžádat větší datové sady. Zde je postup převedení export M dotazu použít rozhraní API.

1. Vytvoření [klíč rozhraní API](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).
2. Aktualizujte Power BI M skript, který jste exportovali z Analytics nahrazením adresu URL správce prostředků Azure Application Insights API.
   * Nahraďte  **https://management.azure.com/subscriptions/...**
   * s  **https://api.applicationinsights.io/beta/apps/...**
3. Nakonec aktualizujte přihlašovací údaje k základní a používání vašeho klíče rozhraní API.
  

**Existující skriptu**
 ```
 Source = Json.Document(Web.Contents("https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups//providers/microsoft.insights/components//api/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```
**Aktualizované skriptu**
 ```
 Source = Json.Document(Web.Contents("https://api.applicationinsights.io/beta/apps/<APPLICATION_ID>/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```

## <a name="about-sampling"></a>O vzorkování
Pokud vaše aplikace odešle velké množství dat, můžete použít funkci adaptivního vzorkování, která odesílá pouze procento vaší telemetrie. Totéž platí, pokud jste ručně nastavili vzorkování buď v sadě SDK, nebo na přijímání. [Další informace o vzorkování](app-insights-sampling.md).


## <a name="next-steps"></a>Další postup
* [Power BI - Další informace](http://www.powerbi.com/learning/)
* [Analýza kurzu](app-insights-analytics-tour.md)


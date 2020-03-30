---
title: Export do Power BI z Přehledů aplikací Azure | Dokumenty společnosti Microsoft
description: Analytické dotazy se můžou zobrazovat v Power BI.
ms.topic: conceptual
ms.date: 08/10/2018
ms.openlocfilehash: 0e17ca6e07ec76f0a7a1cb04f7aa13619fb9970c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663993"
---
# <a name="feed-power-bi-from-application-insights"></a>Napájení Power BI z přehledů aplikací
[Power BI](https://www.powerbi.com/) je sada obchodních nástrojů, které vám pomohou analyzovat data a sdílet přehledy. Bohaté řídicí panely jsou k dispozici na všech zařízeních. Můžete kombinovat data z mnoha zdrojů, včetně dotazů Analytics z [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md).

Existují tři způsoby exportu dat Application Insights do Power BI:

* [**Exportovat analytické dotazy**](#export-analytics-queries). Toto je upřednostňovaná metoda. Napište libovolný dotaz a exportujte ho do Power BI. Tento dotaz můžete umístit na řídicí panel spolu s dalšími daty.
* [**Nepřetržitý export a Azure Stream Analytics**](../../azure-monitor/app/export-stream-analytics.md). Tato metoda je užitečná, pokud chcete ukládat data po dlouhou dobu. Pokud nemáte rozšířený požadavek na uchovávání dat, použijte metodu dotazu analýzy exportu. Nepřetržitý export a Stream Analytics vyžaduje více práce na nastavení a další režii úložiště.
* **Adaptér Power BI**. Sada grafů je předdefinována, ale můžete přidat vlastní dotazy z jiných zdrojů.

> [!NOTE]
> Adaptér Power BI je teď **zastaralá**. Předdefinované grafy pro toto řešení jsou naplněny statickými neupravitelnými dotazy. Nemáte možnost upravovat tyto dotazy a v závislosti na určitých vlastnostech dat je možné, aby připojení k Power BI bylo úspěšné, ale žádná data nejsou naplněna. To je způsobeno kritéria vyloučení, které jsou nastaveny v rámci pevně zakódovaný dotaz. I když toto řešení může stále fungovat pro některé zákazníky, z důvodu nedostatečné flexibility adaptéru je doporučeným řešením použití funkce [**dotazu exportu Analytics.**](#export-analytics-queries)

## <a name="export-analytics-queries"></a>Exportovat analytické dotazy
Tato trasa umožňuje napsat libovolný dotaz Analytics, který se vám líbí, nebo exportovat z trychtýřů využití a pak ho exportovat na řídicí panel Power BI. (Můžete přidat do řídicího panelu vytvořeného adaptérem.)

### <a name="one-time-install-power-bi-desktop"></a>Jednou: instalace Power BI Desktopu
K importu dotazu Application Insights použijte desktopovou verzi Power BI. Pak ho můžete publikovat na webu nebo v cloudovém pracovním prostoru Power BI. 

Nainstalujte [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).

### <a name="export-an-analytics-query"></a>Export dotazu analytics
1. [Otevřete Analytics a napište svůj dotaz](../../azure-monitor/log-query/get-started-portal.md).
2. Otestujte a upřesněte dotaz, dokud nebudete spokojeni s výsledky. Před exportem dotazu zkontrolujte, zda je v Analytics správně spuštěn.
3. V nabídce **Export** zvolte **Power BI (M).** Uložte textový soubor.
   
    ![Snímek obrazovky s analytickou položkou se zvýrazněnou nabídkou Export](./media/export-power-bi/analytics-export-power-bi.png)
4. V Power BI Desktopu vyberte **Získat prázdný dotaz .** > **Blank Query** Potom v editoru dotazů v části **Zobrazení**vyberte **Rozšířený editor**.

    Vložte exportovaný skript jazyka M do rozšířeného editoru.

    ![Snímek obrazovky s Power BI Desktopem se zvýrazněným rozšířeným editorem](./media/export-power-bi/power-bi-import-analytics-query.png)

5. Pokud chcete Power BI povolit přístup k Azure, možná budete muset zadat přihlašovací údaje. K přihlášení pomocí účtu Microsoft použijte **účet Organizace.**
   
    ![Snímek obrazovky s dialogovým oknem Nastavení dotazu Power BI](./media/export-power-bi/power-bi-import-sign-in.png)

    Pokud potřebujete ověřit pověření, použijte příkaz nabídky **Nastavení zdroje dat** v editoru dotazů. Nezapomeňte zadat přihlašovací údaje, které používáte pro Azure, které se mohou lišit od vašich přihlašovacích údajů pro Power BI.
6. Vyberte vizualizaci pro dotaz a vyberte pole pro osu x, osu y a segmentační kótu.
   
    ![Snímek obrazovky s možnostmi vizualizace desktopu Power BI](./media/export-power-bi/power-bi-analytics-visualize.png)
7. Publikujte sestavu do cloudového pracovního prostoru Power BI. Odtud můžete vložit synchronizovanou verzi do jiných webových stránek.
   
    ![Snímek obrazovky s Power BI Desktopem se zvýrazněným tlačítkem Publikovat](./media/export-power-bi/publish-power-bi.png)
8. Aktualizujte sestavu ručně v intervalech nebo nastavte plánovanou aktualizaci na stránce možností.

### <a name="export-a-funnel"></a>Export trychtýře
1. [Vytvořte si trychtýř](../../azure-monitor/app/usage-funnels.md).
2. Vyberte **Power BI**.

   ![Snímek obrazovky s tlačítkem Power BI](./media/export-power-bi/button.png)

3. V Power BI Desktopu vyberte **Získat prázdný dotaz .** > **Blank Query** Potom v editoru dotazů v části **Zobrazení**vyberte **Rozšířený editor**.

   ![Snímek obrazovky s Power BI Desktopem se zvýrazněným tlačítkem Prázdný dotaz](./media/export-power-bi/blankquery.png)

   Vložte exportovaný skript jazyka M do rozšířeného editoru. 

   ![Snímek obrazovky s Power BI Desktopem se zvýrazněným rozšířeným editorem](./media/export-power-bi/advancedquery.png)

4. Vyberte položky z dotazu a zvolte vizualizaci cesty.

   ![Snímek obrazovky s možnostmi vizualizace desktopu Power BI](./media/export-power-bi/selectsequence.png)

5. Změňte název tak, aby byl smysluplný, a publikujte sestavu do cloudového pracovního prostoru Power BI. 

   ![Snímek obrazovky s Power BI Desktopem se zvýrazněnou změnou názvu](./media/export-power-bi/changetitle.png)

## <a name="troubleshooting"></a>Řešení potíží

Může dojít k chybám týkajícím se pověření nebo velikosti datové sady. Zde je několik informací o tom, co dělat s těmito chybami.

### <a name="unauthorized-401-or-403"></a>Neautorizováno (401 nebo 403)
K tomu může dojít, pokud nebyl aktualizován obnovovací token. Vyzkoušejte následující kroky, abyste měli stále přístup:

1. Přihlaste se k portálu Azure a ujistěte se, že máte přístup k prostředku.
2. Pokuste se aktualizovat přihlašovací údaje pro řídicí panel.
3. Pokuste se vymazat mezipaměť z počítače PowerBI.


   Pokud máte přístup a aktualizace přihlašovacích údajů nefunguje, otevřete lístek podpory.

### <a name="bad-gateway-502"></a>Špatná brána (502)
To je obvykle způsobeno dotazem Analytics, který vrací příliš mnoho dat. Zkuste pro dotaz použít menší časový rozsah. 

Pokud snížení datové sady pocházející z dotazu Analytics nesplňuje vaše požadavky, zvažte použití [rozhraní API](https://dev.applicationinsights.io/documentation/overview) k vytažení větší datové sady. Tady je postup, jak převést export M-Query na použití rozhraní API.

1. Vytvořte [klíč rozhraní API](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).
2. Aktualizujte skript Power BI M, který jste exportovali ze služby Analytics, nahrazením adresy URL Správce prostředků Azure rozhraním API pro přehledy aplikací.
   * Nahradit **https:\//management.azure.com/subscriptions/...**
   * s, **https:\//api.applicationinsights.io/beta/apps/...**
3. Nakonec aktualizujte pověření na základní a použijte klíč rozhraní API.

**Existující skript**
 ```
 Source = Json.Document(Web.Contents("https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups//providers/microsoft.insights/components//api/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```
**Aktualizovaný skript**
 ```
 Source = Json.Document(Web.Contents("https://api.applicationinsights.io/beta/apps/<APPLICATION_ID>/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```

## <a name="about-sampling"></a>O vzorkování
V závislosti na množství dat odeslaných vaší aplikací můžete použít funkci adaptivního vzorkování, která odesílá pouze procento telemetrie. Totéž platí, pokud jste ručně nastavili vzorkování v sadě SDK nebo při požití. [Další informace o vzorkování](../../azure-monitor/app/sampling.md).

## <a name="power-bi-adapter-deprecated"></a>Adaptér Power BI (zastaralé)
Tato metoda vytvoří kompletní řídicí panel telemetrie pro vás. Počáteční datová sada je předdefinována, ale můžete do ní přidat další data.

### <a name="get-the-adapter"></a>Získání adaptéru
1. Přihlaste se k [Power BI](https://app.powerbi.com/).
2. Otevřete **možnost Získat data** ![Snímek](./media/export-power-bi/001.png)obrazovky ikony GetData v levém dolním rohu , **Služby**.

    ![Snímky obrazovky získat ze zdroje dat Application Insights](./media/export-power-bi/002.png)

3. V části Application Insights **vyberte Získat.**

   ![Snímky obrazovky získat ze zdroje dat Application Insights](./media/export-power-bi/003.png)
4. Zadejte podrobnosti o prostředku Application Insights a potom **se přihlaste**.

    ![Snímek obrazovky se zdrojem dat Get from Application Insights](./media/export-power-bi/005.png)

     Tyto informace naleznete v podokně Přehled přehledů aplikací:

     ![Snímek obrazovky se zdrojem dat Get from Application Insights](./media/export-power-bi/004.png)

5. Otevřete nově vytvořenou aplikaci Application Insights Power BI App.

6. Počkejte minutu nebo dvě pro data, která mají být importována.

    ![Snímek obrazovky s adaptérem Power BI](./media/export-power-bi/010.png)

Řídicí panel můžete upravit kombinací grafů Application Insights s grafy z jiných zdrojů a s dotazy Analytics. V galerii vizualizací můžete získat další grafy a každý graf má parametry, které můžete nastavit.

Po počátečním importu se řídicí panel a sestavy budou denně aktualizovat. Plán aktualizace v datové sadě můžete řídit.

## <a name="next-steps"></a>Další kroky
* [Power BI – další informace](https://www.powerbi.com/learning/)
* [Kurz analytics](../../azure-monitor/log-query/get-started-portal.md)


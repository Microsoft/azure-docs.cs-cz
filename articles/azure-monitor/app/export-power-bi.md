---
title: Export do Power BI z Azure Application Insights | Microsoft Docs
description: Analytické dotazy se dají zobrazit v Power BI.
ms.topic: conceptual
ms.date: 08/10/2018
ms.openlocfilehash: 804da7d3aaa2fd1b7973a686bc9ca5cb5ee973a2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90979157"
---
# <a name="feed-power-bi-from-application-insights"></a>Power BI informačního kanálu od Application Insights
[Power BI](https://www.powerbi.com/) je sada obchodních nástrojů, která pomáhá analyzovat data a sdílet přehledy. V každém zařízení jsou k dispozici řídicí panely s bohatou dostupností. Můžete kombinovat data z mnoha zdrojů, včetně analytických dotazů z [Azure Application Insights](./app-insights-overview.md).

Existují tři způsoby, jak exportovat data Application Insights do Power BI:

* [**Exportujte analytické dotazy**](#export-analytics-queries). Toto je upřednostňovaná metoda. Napište libovolný dotaz, který chcete, a exportujte ho do Power BI. Tento dotaz můžete umístit na řídicí panel spolu s dalšími daty.
* [**Průběžný export a Azure Stream Analytics**](./export-stream-analytics.md). Tato metoda je užitečná v případě, že chcete ukládat data po dlouhou dobu. Pokud nemáte požadavek na rozšířené uchovávání dat, použijte metodu export Analytics Query. Průběžný export a Stream Analytics zahrnují více práce na nastavení a další režijní náklady na úložiště.
* **Power BI adaptér**. Sada grafů je předdefinovaná, ale můžete přidat vlastní dotazy z jiných zdrojů.

> [!NOTE]
> Adaptér Power BI je nyní **zastaralý**. Předdefinované grafy pro toto řešení jsou vyplněny statickými neupravitelnými dotazy. Nemůžete upravovat tyto dotazy a v závislosti na určitých vlastnostech vašich dat je možné, aby připojení Power BI úspěšné, ale naplnila se žádná data. Důvodem je kritéria vyloučení, která jsou nastavena v rámci dotazu pevně zakódované. I když toto řešení může pro některé zákazníky pořád fungovat, kvůli nedostatku flexibility adaptéru doporučujeme použít funkci [**Export Analytics Query**](#export-analytics-queries) .

## <a name="export-analytics-queries"></a>Exportovat analytické dotazy
Tato trasa vám umožní napsat libovolný analytický dotaz, který chcete, nebo exportovat z trychtýřů využití a pak ho exportovat do Power BIho řídicího panelu. (Můžete přidat do řídicího panelu vytvořeného adaptérem.)

### <a name="one-time-install-power-bi-desktop"></a>Jednou: Nainstalujte Power BI Desktop
Pokud chcete importovat Application Insights dotaz, použijte desktopovou verzi Power BI. Pak ho můžete publikovat na webu nebo do pracovního prostoru Power BI v cloudu. 

Nainstalujte [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).

### <a name="export-an-analytics-query"></a>Export analytického dotazu
1. [Otevřete Analytics a napište dotaz](../log-query/get-started-portal.md).
2. Otestujte a zužte dotaz, dokud nebudete spokojeni s výsledky. Před exportem se ujistěte, že dotaz běží v analýze správně.
3. V nabídce **Export** vyberte možnost **Power BI (M)**. Uložte textový soubor.
   
    ![Snímek obrazovky s analýzou se zvýrazněnou nabídkou export](./media/export-power-bi/analytics-export-power-bi.png)
4. V Power BI Desktop vyberte **získat data**  >  **prázdný dotaz**. Pak v editoru dotazů v části **zobrazení**vyberte **Rozšířený editor**.

    Vložte exportovaný skript jazyka M do Rozšířený editor.

    ![Snímek obrazovky Power BI Desktop se zvýrazněnou Rozšířený editor](./media/export-power-bi/power-bi-import-analytics-query.png)

5. Abyste povolili Power BI přístup k Azure, možná budete muset zadat přihlašovací údaje. Pomocí **účtu organizace** se přihlaste pomocí účet Microsoft.
   
    ![Snímek obrazovky s dialogovým oknem nastavení dotazu Power BI](./media/export-power-bi/power-bi-import-sign-in.png)

    Pokud potřebujete ověřit přihlašovací údaje, použijte příkaz v nabídce **Nastavení zdroje dat** v editoru dotazů. Nezapomeňte zadat přihlašovací údaje, které používáte pro Azure, což se může lišit od vašich přihlašovacích údajů pro Power BI.
6. Zvolte vizualizaci pro dotaz a vyberte pole pro osu x, osu y a dimenzi segmentace.
   
    ![Snímek obrazovky s možnostmi vizualizace Power BI Desktop](./media/export-power-bi/power-bi-analytics-visualize.png)
7. Publikujte sestavu do svého pracovního prostoru Power BI Cloud. Odtud můžete na jiné webové stránky vložit synchronizovanou verzi.
   
    ![Snímek obrazovky Power BI Desktop se zvýrazněným tlačítkem publikovat](./media/export-power-bi/publish-power-bi.png)
8. Aktualizujte sestavu ručně v intervalech nebo na stránce Možnosti nastavte naplánovanou aktualizaci.

### <a name="export-a-funnel"></a>Export trychtýře
1. [Nastavte trychtýř](./usage-funnels.md).
2. Vyberte **Power BI**.

   ![Snímek obrazovky s tlačítkem Power BI](./media/export-power-bi/button.png)

3. V Power BI Desktop vyberte **získat data**  >  **prázdný dotaz**. Pak v editoru dotazů v části **zobrazení**vyberte **Rozšířený editor**.

   ![Snímek obrazovky Power BI Desktop s zvýrazněným tlačítkem pro prázdné dotazy](./media/export-power-bi/blankquery.png)

   Vložte exportovaný skript jazyka M do Rozšířený editor. 

   ![Snímek obrazovky zobrazující Power BI Desktop se zvýrazněnou Rozšířený editor](./media/export-power-bi/advancedquery.png)

4. Vyberte položky z dotazu a vyberte vizualizaci trychtýře.

   ![Snímek obrazovky s Power BI Desktop možností vizualizace trychtýřového grafu](./media/export-power-bi/selectsequence.png)

5. Změňte název tak, aby byl smysluplný, a publikujte sestavu do svého pracovního prostoru Power BI cloudu. 

   ![Snímek obrazovky Power BI Desktop se zvýrazněnou změnou názvu](./media/export-power-bi/changetitle.png)

## <a name="troubleshooting"></a>Řešení potíží

Může dojít k chybám souvisejícím s přihlašovacími údaji nebo velikostí datové sady. Zde jsou některé informace o tom, co dělat o těchto chybách.

### <a name="unauthorized-401-or-403"></a>Neautorizováno (401 nebo 403)
K tomu může dojít, pokud obnovovací token nebyl aktualizován. Vyzkoušejte si tento postup, abyste měli jistotu, že máte stále přístup:

1. Přihlaste se k Azure Portal a ujistěte se, že máte k tomuto prostředku přístup.
2. Pokuste se aktualizovat přihlašovací údaje pro řídicí panel.
3. Zkuste vymazat mezipaměť z Power BI Desktop.


   Pokud máte přístup a aktualizace přihlašovacích údajů nefunguje, otevřete prosím lístek podpory.

### <a name="bad-gateway-502"></a>Chybná brána (502)
To je obvykle způsobeno dotazem Analytics, který vrací příliš mnoho dat. Zkuste použít pro dotaz menší časový rozsah. 

Pokud omezení datové sady přicházející z analytického dotazu nesplňuje vaše požadavky, zvažte použití [rozhraní API](https://dev.applicationinsights.io/documentation/overview) k vyžádání větší datové sady. Tady je postup, jak převést export dotazů M na použití rozhraní API.

1. Vytvořte [klíč rozhraní API](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).
2. Aktualizujte skript Power BIu M, který jste exportovali z analýzy, a to tak, že nahradíte Azure Resource Manager adresu URL rozhraní API Application Insights.
   * Nahraďte **https: \/ /Management.Azure.com/Subscriptions/...**
   * s, **https: \/ /API.ApplicationInsights.IO/beta/Apps/...**
3. Nakonec aktualizujte přihlašovací údaje na Basic a použijte svůj klíč rozhraní API.

**Existující skript**
 ```
 Source = Json.Document(Web.Contents("https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups//providers/microsoft.insights/components//api/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```
**Aktualizovaný skript**
 ```
 Source = Json.Document(Web.Contents("https://api.applicationinsights.io/beta/apps/<APPLICATION_ID>/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```

## <a name="about-sampling"></a>O vzorkování
V závislosti na množství dat odesílaných vaší aplikací můžete použít funkci adaptivního vzorkování, která posílá pouze procento telemetrie. Totéž platí, pokud jste ručně nastavili vzorkování buď v sadě SDK, nebo na ingestování. [Přečtěte si další informace o vzorkování](./sampling.md).

## <a name="power-bi-adapter-deprecated"></a>Power BI adaptér (zastaralé)
Tato metoda vytvoří úplný řídicí panel telemetrie za vás. Počáteční datová sada je předdefinovaná, ale do ní můžete přidat další data.

### <a name="get-the-adapter"></a>Získání adaptéru
1. Přihlaste se k [Power BI](https://app.powerbi.com/).
2. Otevřete snímek obrazovky **získat data** ![ ikony GetData v levém dolním ](./media/export-power-bi/001.png) rohu **služby**.

    ![Snímky obrazovky zobrazí tlačítko získat v okně služby.](./media/export-power-bi/002.png)

3. Vyberte možnost **získat nyní** v části Application Insights.

   ![Snímky obrazovky získat z Application Insights zdroji dat](./media/export-power-bi/003.png)
4. Zadejte podrobnosti prostředku Application Insights a potom se **přihlaste**.

    ![Snímek obrazovky se zobrazí okno připojit k Application Insights.](./media/export-power-bi/005.png)

     Tyto informace najdete v podokně s přehledem Application Insights:

     ![Snímek obrazovky získat z Application Insights zdroje dat](./media/export-power-bi/004.png)

5. Otevřete nově vytvořenou aplikaci Application Insights Power BI.

6. Pro import dat Počkejte minutu nebo dvě.

    ![Snímek obrazovky s adaptérem Power BI](./media/export-power-bi/010.png)

Řídicí panel můžete upravit kombinací Application Insightsch grafů s ostatními zdroji a pomocí analytických dotazů. V galerii vizualizací můžete získat více grafů a každý graf má parametry, které lze nastavit.

Po počátečním importu se řídicí panel a sestavy budou dál aktualizovat denně. Můžete řídit plán aktualizace pro datovou sadu.

## <a name="next-steps"></a>Další kroky
* [Power BI – informace](https://www.powerbi.com/learning/)
* [Kurz analýzy](../log-query/get-started-portal.md)


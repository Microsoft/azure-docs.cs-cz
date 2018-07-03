---
title: Pomocí Analytics – výkonné vyhledávací nástroj služby Azure Application Insights | Dokumentace Microsoftu
description: 'Použití analýz, nástroj výkonné diagnostické vyhledávání služby Application Insights. '
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: c3b34430-f592-4c32-b900-e9f50ca096b3
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 07/02/2018
ms.reviewer: danha
ms.author: mbullwin
ms.openlocfilehash: aa86e2f3b1fb147ab167c948475a5207693143c2
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2018
ms.locfileid: "37341558"
---
# <a name="using-analytics-in-application-insights"></a>Pomocí analýzy ve službě Application Insights
[Analytics](app-insights-analytics.md) je součástí výkonné vyhledávání [Application Insights](app-insights-overview.md). Tyto stránky popisují dotazovací jazyk Log Analytics.

* **[Podívejte se na úvodní video](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**.
* **[Vyzkoušejte si analýzy na naše Simulovaná data](https://analytics.applicationinsights.io/demo)**  Pokud vaše aplikace neodesílá data do Application Insights ještě.

## <a name="open-analytics"></a>Otevřít analýzu
Z domovské prostředek vaší aplikace ve službě Application Insights klikněte na tlačítko Analytics.

![Otevřené stránce portal.azure.com otevřete prostředek Application Insights a klikněte na tlačítko Analytics.](./media/app-insights-analytics-using/001.png)

Vložené kurzu vám získali představu o tom, co můžete dělat.

Je [rozsáhlejší tour zde](app-insights-analytics-tour.md).

## <a name="query-your-telemetry"></a>Dotaz na telemetrii
### <a name="write-a-query"></a>Napsat dotaz
![Zobrazení schématu](./media/app-insights-analytics-using/150.png)

Začněte s názvy kterékoli z tabulek uvedené na levé straně (nebo [rozsah](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/range-operator) nebo [sjednocení](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/union-operator) operátory). Použití `|` vytvořit tak kanál [operátory](https://docs.loganalytics.io/docs/Learn/References/Useful-operators). 

Technologie IntelliSense zobrazí výzvu s operátory a prvky výrazů, které můžete použít. Klikněte na ikonu informace (nebo stiskněte kombinaci kláves CTRL + MEZERNÍK) Chcete-li získat podrobnější popis a příklady použití jednotlivých prvků.

Najdete v článku [prohlídka jazyka Analytics](app-insights-analytics-tour.md) a [referenční informace k jazyku](app-insights-analytics-reference.md).

### <a name="run-a-query"></a>Spuštění dotazu
![Spuštění dotazu](./media/app-insights-analytics-using/130.png)

1. Použití jedné konců řádků v dotazu.
2. Umístěte kurzor dovnitř nebo na konci dotazu, který chcete spustit.
3. Zkontrolujte časový rozsah vašeho dotazu. (Můžete ho změnit nebo přepsat včetně vašich vlastních [ `where...timestamp...` ](https://docs.loganalytics.io/docs/Learn/Tutorials/Date-and-time-operations) klauzule v dotazu.)
3. Klikněte na Přejít ke spuštění dotazu.
4. Neumisťujte prázdných řádků v dotazu. Více oddělených dotazů možné ponechat v jedné karty dotazů oddělených prázdné řádky. Spouští pouze na dotaz, který má kurzor.

### <a name="save-a-query"></a>Uložit dotaz
![Uložení dotazu](./media/app-insights-analytics-using/140.png)

1. Uložte aktuální soubor dotazu.
2. Otevření souboru uloženého dotazu.
3. Vytvořte nový soubor dotazu.

## <a name="see-the-details"></a>Přečtěte si k tomu víc
Rozbalte všechny řádky v výsledky chcete zobrazit úplný seznam vlastností. Můžete dále rozšířit, jakákoli vlastnost, která je strukturovaných hodnota – například, vlastní dimenze nebo zásobníku uvedení na výjimku.

![Rozbalit řádek](./media/app-insights-analytics-using/070.png)

## <a name="arrange-the-results"></a>Uspořádání výsledků
Můžete řadit, filtrování, stránkování a seskupení výsledků vrácená z dotazu.

> [!NOTE]
> Řazení, seskupování a filtrování v prohlížeči není spusťte znovu dotaz. Jejich uspořádání pouze výsledky, které byly vráceny posledního dotazu. 
> 
> Chcete-li provádět tyto úlohy na serveru, než jsou vráceny výsledky, napsat dotaz s [řazení](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/sort-operator), [shrnout](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator) a [kde](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/where-operator) operátory.
> 
> 

Vyberte sloupce, které byste chtěli naleznete v tématu, přetáhněte záhlaví sloupců k změně jejich uspořádání a změna velikosti sloupců přetažením jejich ohraničení.

![Uspořádání sloupců](./media/app-insights-analytics-using/030.png)

### <a name="sort-and-filter-items"></a>Řazení a filtrování položek
Vaše výsledky seřaďte klepnutím na hlavičku sloupce. Klikněte na tlačítko znovu seřadit jiným způsobem a klikněte na třetí čas se vrátit k původní pořadí vrácené dotazem.

Použijte ikonu filtru můžete zpřesnit hledání.

![Sloupců pro řazení a filtrování](./media/app-insights-analytics-using/040.png)

### <a name="group-items"></a>Seskupení položek
Seřadit podle více než jeden sloupec, seskupování. Ho nejdřív povolit a potom přetáhněte záhlaví sloupce do prostoru výše v tabulce.

![Skupina](./media/app-insights-analytics-using/060.png)

### <a name="missing-some-results"></a>Chybí některé výsledky?

Pokud se domníváte, že se nezobrazují všechny výsledky, které jste očekávali, máte několik z možných důvodů.

* **Časový rozsah filtr**. Ve výchozím nastavení budou zobrazovat jenom výsledky z posledních 24 hodin. Existuje automatického filtru, která omezuje rozsah výsledky, které se načítají ze zdrojových tabulek. 

    Můžete však změnit časový rozsah filtrování pomocí rozevírací nabídky.

    Nebo můžete přepsat automatické rozsahu včetně vašich vlastních [ `where  ... timestamp ...` klauzule](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/where-operator) do dotazu. Příklad:

    `requests | where timestamp > ago('2d')`

* **Limit výsledků**. Platí limit asi 10 tisíc řádků na výsledky vrácené z portálu. Upozornění se zobrazí, pokud překročíte limit. Pokud k tomu dojde, řazení výsledků v tabulce nebudou vždy zobrazí všechny skutečné první nebo poslední výsledky. 

    Je vhodné vyhnout se dosažení limitu. Použít filtr rozsahu času nebo používejte operátory jako:

  * [prvních 100 pomocí časového razítka](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/top-operator) 
  * [Využijte 100](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/take-operator)
  * [shrnutí ](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator) 
  * [kde časové razítko > ago(3d)](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/where-operator)

(Má více než 10 tisíc řádků? Zvažte použití [průběžný Export](app-insights-export-telemetry.md) místo. Analytics byla navržena pro analýzy, spíše než načítání nezpracovaných dat.)

## <a name="diagrams"></a>Diagramy
Vyberte typ diagramu, který chcete:

![Vyberte typ diagramu](./media/app-insights-analytics-using/230.png)

Pokud máte několik sloupců správné typy, můžete x a osy y a sloupec k rozdělení výsledky podle dimenzí.

Ve výchozím nastavení výsledky se zpočátku zobrazují jako tabulky a ručně vyberte diagram. Ale můžete použít [vykreslení směrnice](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/render-operator) na konci dotaz pro výběr diagramu.

### <a name="analytics-diagnostics"></a>Diagnostika Analytics


Na časový graf Pokud náhlá Špička nebo kroku ve vašich datech, může se zobrazit bod zvýrazněný na řádku. To znamená, že Diagnostika Analytics zjistila kombinací vlastností, které odfiltrovat náhlé změny. Klikněte na bod, abyste získali více podrobností na filtr a zobrazíte filtrované verze. To může pomoci zjistit, co způsobilo změny. 

[Další informace o diagnostice Analytics](app-insights-analytics-diagnostics.md)


![Diagnostika Analytics](./media/app-insights-analytics-using/analytics-diagnostics.png)

## <a name="pin-to-dashboard"></a>Připnutí na řídicí panel
Můžete připnout diagramu nebo tabulku k jedné z vašich [sdílené řídicí panely](app-insights-dashboards.md) – stačí kliknout na kód pin. 

![Klikněte na tlačítko PIN kód](./media/app-insights-analytics-using/pin-01.png)

To znamená, že při uvádění společně řídicí panel vám pomohou monitorovat výkon a využití vašich webových služeb, můžete zahrnout poměrně složité analýzy společně s další metriky. 

Tabulku na řídicí panel můžete připnout, pokud má čtyři nebo méně sloupců. Zobrazí se jenom prvních řádků sedm.

### <a name="dashboard-refresh"></a>Aktualizace řídicího panelu
Graf připnout na řídicí panel se aktualizují automaticky podle opětovné spuštění dotazu přibližně každou hodinu. Můžete také kliknout na tlačítko Aktualizovat.

### <a name="automatic-simplifications"></a>Automatické zjednodušení

Některé zjednodušení se použijí na graf při Připnutí na řídicí panel.

**Čas omezení:** dotazy jsou automaticky omezeny na posledních 30 dní. Efekt je stejný jako v případě, že váš dotaz obsahuje `where timestamp > ago(30d)`.

**Omezení počtu Bin:** zobrazíte graf, který obsahuje mnoho intervalů diskrétní (obvykle pruhový graf), budou automaticky seskupeny méně mají údaj vyplněný přihrádky do jednoho "ostatní" bin. Tento dotaz:

    requests | summarize count_search = count() by client_CountryOrRegion

v Analytics bude vypadat takto:

![Graf se dlouho zakončením](./media/app-insights-analytics-using/pin-07.png)

ale po jejím Připnutí na řídicí panel, vypadá takto:

![Graf s omezenou přihrádky](./media/app-insights-analytics-using/pin-08.png)

## <a name="export-to-excel"></a>Exportovat do Excelu
Po spuštění dotazu, můžete stáhnout soubor CSV. Klikněte na tlačítko **Export Excelu**.

## <a name="export-to-power-bi"></a>Export do Power BI
Umístěte kurzor v dotazu a zvolte **exportovat, Power BI**.

![Export z analýzy do Power BI](./media/app-insights-analytics-using/240.png)

Spuštění dotazu v Power BI. Můžete ho aktualizovat podle plánu nastavit.

S Power BI můžete vytvořit řídicí panely, které dávají dohromady data z nejrůznějších zdrojů.

[Další informace o export do Power BI](app-insights-export-power-bi.md)

## <a name="deep-link"></a>Přímý odkaz

Získejte odkaz pod **export, Zajišťujeme sdílet odkaz** , který můžete poslat na jiného uživatele. Pokud má uživatel [přístup do vaší skupiny prostředků](app-insights-resources-roles-access-control.md), otevře se v Uživatelském rozhraní Analytics dotazu.

(V odkazu, který se zobrazí text dotazu po "? q =", komprimované gzip a kódování base-64. Můžete napsat kód ke generování přímých odkazů, které poskytujete uživatelům. Doporučeným způsobem, jak spouštět analýzy z kódu je však pomocí [rozhraní REST API](https://dev.applicationinsights.io/).)


## <a name="automation"></a>Automation

Použití [REST API služby Data Access](https://dev.applicationinsights.io/) pro spouštění analytických dotazů. [Například](https://dev.applicationinsights.io/apiexplorer/query?appId=DEMO_APP&apiKey=DEMO_KEY&query=requests%0A%7C%20where%20timestamp%20%3E%3D%20ago%2824h%29%0A%7C%20count) (pomocí Powershellu):

```PS
curl "https://api.applicationinsights.io/beta/apps/DEMO_APP/query?query=requests%7C%20where%20timestamp%20%3E%3D%20ago(24h)%7C%20count" -H "x-api-key: DEMO_KEY"
```

Na rozdíl od analýzy uživatelského rozhraní rozhraní REST API automaticky nepřidá žádné omezení časové razítko k dotazům. Nezapomeňte přidat vlastní – klauzule where, nechcete-li získat odpovědi na velké.



## <a name="import-data"></a>Import dat

Můžete importovat data ze souboru CSV. Typické použití je importovat statická data, která se může připojit k s tabulkami z vaší telemetrie. 

Například ověření uživatelé jsou označeny v telemetrii obfuskovaný id nebo alias, by mohl importovat tabulku, která se mapuje na skutečné názvy aliasů. Provedením spojení na telemetrie žádostí můžete identifikovat uživatele podle svých skutečných názvů v sestavách analýzy.

### <a name="define-your-data-schema"></a>Definování schématu dat

1. Klikněte na tlačítko **nastavení** (nahoře vlevo) a potom **zdroje dat**. 
2. Přidáte zdroj dat, postupujte podle pokynů. Zobrazí se výzva k zadání ukázková data, která by měla obsahovat alespoň 10 řádků. Potom opravit schématu.

Definuje zdroj dat, který pak můžete použít k importu jednotlivých tabulek.

### <a name="import-a-table"></a>Import tabulky

1. Otevřete vaše definice zdroje dat ze seznamu.
2. Klikněte na tlačítko "Odeslat" a postupujte podle pokynů k odeslání v tabulce. To zahrnuje volání rozhraní REST API, a proto je snadno zautomatizovat. 

Tabulka je teď k dispozici pro použití v analytické dotazy. Zobrazí se v Analytics 

### <a name="use-the-table"></a>Tabulka

Předpokládejme, že vaše definice zdroje dat se nazývá `usermap`, a že má dvě pole, `realName` a `user_AuthenticatedId`. `requests` Tabulka má také pole s názvem `user_AuthenticatedId`, takže je snadné k nim:

```AIQL

    requests
    | where notempty(user_AuthenticatedId) | take 10
    | join kind=leftouter ( usermap ) on user_AuthenticatedId 
```
Výsledná tabulka žádosti obsahuje sloupec s další `realName`.

### <a name="import-from-logstash"></a>Importovat z LogStash

Pokud používáte [LogStash](https://www.elastic.co/guide/en/logstash/current/getting-started-with-logstash.html), Analytics můžete použít k dotazování protokolů. Použití [plugin, který prostřednictvím kanálu předá data do analýzy](https://github.com/Microsoft/logstash-output-application-insights). 

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 

[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]


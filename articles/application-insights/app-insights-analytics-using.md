---
title: Pomocí analýzy – nástroj výkonné vyhledávání systému Azure Application Insights | Microsoft Docs
description: 'Pomocí analýzy, nástroj výkonné diagnostické vyhledávání služby Application Insights. '
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
ms.date: 03/14/2017
ms.reviewer: danha
ms.author: mbullwin
ms.openlocfilehash: 7f8f49cf88bda8e485d2365281c13680ef796196
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295515"
---
# <a name="using-analytics-in-application-insights"></a>Pomocí analýzy ve službě Application Insights
[Analýza](app-insights-analytics.md) je výkonný vyhledávání funkcí [Application Insights](app-insights-overview.md). Tyto stránek popisují dotazovací jazyk analýzy protokolů.

* **[Podívejte se na úvodní video](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**.
* **[Vyzkoušejte Analytics na naše simulované data](https://analytics.applicationinsights.io/demo)**  Pokud aplikace není odesílání dat do služby Application Insights ještě.

## <a name="open-analytics"></a>Otevřete Analytics
Z domovské prostředek vaší aplikace ve službě Application Insights klikněte na tlačítko Analytics.

![Otevřete portal.azure.com otevřete prostředek Application Insights a klikněte na Analytics.](./media/app-insights-analytics-using/001.png)

Vložené kurzu vám dává některé nápady, o co můžete dělat.

Je [rozsáhlejší prohlídka zde](app-insights-analytics-tour.md).

## <a name="query-your-telemetry"></a>Dotaz telemetrie
### <a name="write-a-query"></a>Napsat dotaz
![Zobrazení schématu](./media/app-insights-analytics-using/150.png)

Začněte s názvy uvedené na levé straně tabulky (nebo [rozsah](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/range-operator) nebo [sjednocení](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/union-operator) operátory). Použití `|` vytvořit kanál z [operátory](https://docs.loganalytics.io/docs/Learn/References/Useful-operators). 

IntelliSense zobrazí výzvu s operátory a výraz prvky, které můžete použít. Klikněte na ikonu informace (nebo stiskněte klávesu CTRL + MEZERNÍK) získat delší popis a příklady, jak používat jednotlivé prvky.

Najdete v článku [Analytics jazyk prohlídka](app-insights-analytics-tour.md) a [referenční příručka jazyka](app-insights-analytics-reference.md).

### <a name="run-a-query"></a>Spuštění dotazu
![Spuštění dotazu](./media/app-insights-analytics-using/130.png)

1. Jeden řádek zalomení můžete použít v dotazu.
2. Umístěte kurzor uvnitř nebo na konci tohoto dotazu, který chcete spustit.
3. Zkontrolujte časové rozmezí dotazu. (Můžete ho změnit nebo potlačit včetně vlastní [ `where...timestamp...` ](https://docs.loganalytics.io/docs/Learn/Tutorials/Date-and-time-operations) klauzule v dotazu.)
3. Kliknutím na Přejít do spusťte dotaz.
4. Nevkládejte prázdné řádky v dotazu. Několik oddělených dotazů můžete ponechat v jedné karty dotazu jejich oddělením prázdné řádky. Spustí se pouze na dotaz, který má kurzor.

### <a name="save-a-query"></a>Uložení dotazu
![Uložení dotazu](./media/app-insights-analytics-using/140.png)

1. Uložte soubor aktuální dotaz.
2. Otevřete soubor uložený dotaz.
3. Vytvořte nový soubor dotazu.

## <a name="see-the-details"></a>Přečtěte si k tomu víc
Rozbalte všechny řádek ve výsledcích zobrazíte jeho úplný seznam vlastností. Můžete dále rozšířit jakákoli vlastnost, která je strukturovaných hodnota – například, vlastní dimenze nebo zásobníku výpis výjimku.

![Rozbalte řádek](./media/app-insights-analytics-using/070.png)

## <a name="arrange-the-results"></a>Uspořádání výsledků
Můžete řadit, filtrovat, stránkování a seskupení výsledků vrácená z dotazu.

> [!NOTE]
> Řazení, seskupování a filtrování v prohlížeči není spusťte znovu dotaz. Jejich pouze změna uspořádání výsledky, které byly vráceny poslední dotaz. 
> 
> K provedení těchto úloh na serveru, než budou vráceny výsledky, napsat dotaz s [řazení](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/sort-operator), [shrnout](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator) a [kde](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/where-operator) operátory.
> 
> 

Vyberte sloupce, které byste chtěli vidět, přetáhněte záhlaví sloupce můžete přeuspořádat jejich a změna velikosti sloupců přetažením jejich ohraničení.

![Uspořádání sloupců](./media/app-insights-analytics-using/030.png)

### <a name="sort-and-filter-items"></a>Třídění a filtrování položek
Seřaďte výsledky klepnutím na hlavičku sloupce. Klikněte na tlačítko znovu seřadit jiným způsobem a klikněte na třetí dobu vrátit se do původního pořadí vrácených v dotazu.

Upřesněte hledání pomocí na ikonu filtru.

![Sloupce řadit a filtrovat.](./media/app-insights-analytics-using/040.png)

### <a name="group-items"></a>Seskupení položek
Chcete-li seřadit podle více než jeden sloupec, použijte seskupení. Nejprve povolte a poté přetáhněte záhlaví sloupců do prostoru nad tabulkou.

![Skupina](./media/app-insights-analytics-using/060.png)

### <a name="missing-some-results"></a>Chybí některé výsledky?

Pokud se domníváte, že nevidíte všechny výsledky, které jste očekávali, existuje několik možných příčin.

* **Filtr času rozsah**. Ve výchozím nastavení uvidíte jenom výsledky z posledních 24 hodin. Není automatické filtr, který omezuje řadu výsledky, které jsou načteny z zdrojové tabulky. 

    Můžete však změnit časový rozsah filtr pomocí rozevírací nabídky.

    Nebo můžete přepsat automatického rozsahu včetně vlastní [ `where  ... timestamp ...` klauzule](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/where-operator) do dotazu. Příklad:

    `requests | where timestamp > ago('2d')`

* **Limit výsledků**. Existuje limit asi 10 kB řádků na výsledky vrácené z portálu. Upozornění se zobrazí, pokud přejdete přesahuje limit. Pokud k tomu dojde, řazení výsledky v tabulce nebude vždy zobrazí všechny skutečné první nebo poslední výsledky. 

    Je vhodné, aby se zabránilo nedosáhli limitu. Použijte filtr času rozsah, nebo použijte například operátory:

  * [prvních 100 pomocí časového razítka](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/top-operator) 
  * [trvat 100](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/take-operator)
  * [Shrnutí ](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator) 
  * [kde časové razítko > ago(3d)](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/where-operator)

(Má více než 10 TIS řádků? Zvažte použití [průběžné exportovat](app-insights-export-telemetry.md) místo. Analýza je určená pro analýzy, nikoli načítání nezpracovaná data.)

## <a name="diagrams"></a>Diagramy
Vyberte typ diagramu, který chcete:

![Vyberte typ diagramu](./media/app-insights-analytics-using/230.png)

Pokud máte několik sloupců správné typy, můžete x a osy y a sloupec dimenzí rozdělit výsledky podle.

Ve výchozím nastavení výsledky se zpočátku zobrazují jako tabulku a vyberete diagramu ručně. Ale můžete použít [vykreslení – direktiva](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/render-operator) na konci tohoto dotazu a vyberte diagram.

### <a name="analytics-diagnostics"></a>Analýza diagnostiky


Na timechart Pokud nečekané Špička nebo krok v datech, může se zobrazit bod zvýrazněných v řádku. To znamená, že Analytics diagnostiky zjistil kombinaci vlastnosti, které vyfiltrovat náhlé změny. Klepněte na bod Získejte další informace o filtru a naleznete v části filtrované verze. To může pomoct zjistit, co způsobilo změnu. 

[Další informace o analýzy diagnostiky](app-insights-analytics-diagnostics.md)


![Analýza diagnostiky](./media/app-insights-analytics-using/analytics-diagnostics.png)

## <a name="pin-to-dashboard"></a>Připnutí na řídicí panel
Budete moct připnout diagram nebo tabulka, která se mezi vaší [sdílet řídicí panely](app-insights-dashboards.md) – stačí kliknout na kód pin. 

![Klikněte na tlačítko PIN kód](./media/app-insights-analytics-using/pin-01.png)

To znamená, že když jste sestavili řídicí panel vám pomohu při monitorování výkonu a využití webových služeb, můžete zahrnout poměrně složité analysis spolu s další metriky. 

Tabulka, která se řídicím panelu můžete připnout, pokud má čtyři nebo méně sloupců. Zobrazí se pouze horních sedm řádků.

### <a name="dashboard-refresh"></a>Obnovení řídicího panelu
Graf připnuli k řídicímu panelu, se automaticky aktualizují podle opětovné spuštění dotazu přibližně za hodinu. Můžete také kliknutím na tlačítko Aktualizovat.

### <a name="automatic-simplifications"></a>Automatické zjednodušení

Některé zjednodušení se použijí na graf při připnout na řídicí panel.

**Čas omezení:** dotazy jsou automaticky omezeno na posledních 14 dnů. Účinek je stejný jako v případě, že váš dotaz obsahuje `where timestamp > ago(14d)`.

**Omezení počtu Bin:** -li si zobrazit graf, který obsahuje mnoho diskrétní přihrádek (obvykle pruhový graf), méně vyplněná přihrádek budou automaticky seskupeny do jednoho "ostatní" bin. Tento dotaz:

    requests | summarize count_search = count() by client_CountryOrRegion

Vypadá to v Analytics:

![Graf s dlouhou tail](./media/app-insights-analytics-using/pin-07.png)

ale když připnout na řídicí panel, vypadá takto:

![Graf s omezenou přihrádek](./media/app-insights-analytics-using/pin-08.png)

## <a name="export-to-excel"></a>Exportovat do Excelu
Po spuštění dotazu, můžete stáhnout soubor .csv. Klikněte na tlačítko **exportovat, aplikace Excel**.

## <a name="export-to-power-bi"></a>Export do Power BI
Umístěte kurzor v dotazu a vyberte **exportovat, Power BI**.

![Export z Analytics k Power BI.](./media/app-insights-analytics-using/240.png)

Spusťte dotaz v Power BI. Můžete ho aktualizovat podle plánu nastavit.

S Power BI můžete vytvořit řídicí panely, které shromažďování dat z různých zdrojů.

[Další informace o exportu do služby Power BI](app-insights-export-power-bi.md)

## <a name="deep-link"></a>Přímý odkaz

Získejte odkaz v části **exportu, sdílené složky odkaz** , které můžete zaslat na jiného uživatele. Pokud má uživatel [přístup k vaší skupiny prostředků](app-insights-resources-roles-access-control.md), otevře se v uživatelském rozhraní Analytics dotazu.

(V odkazu, který je text dotazu se zobrazí po "? q =", gzip komprimované a s kódováním base-64. Můžete napsat kód pro generování přímých odkazů, které poskytují uživatelům. Doporučeným způsobem, jak spustit Analytics z kódu je však pomocí [REST API](https://dev.applicationinsights.io/).)


## <a name="automation"></a>Automation

Použití [REST API služby Data Access](https://dev.applicationinsights.io/) ke spouštění dotazů Analytics. [Například](https://dev.applicationinsights.io/apiexplorer/query?appId=DEMO_APP&apiKey=DEMO_KEY&query=requests%0A%7C%20where%20timestamp%20%3E%3D%20ago%2824h%29%0A%7C%20count) (pomocí prostředí PowerShell):

```PS
curl "https://api.applicationinsights.io/beta/apps/DEMO_APP/query?query=requests%7C%20where%20timestamp%20%3E%3D%20ago(24h)%7C%20count" -H "x-api-key: DEMO_KEY"
```

Na rozdíl od rozhraní Analytics rozhraní REST API automaticky nepřidá žádné omezení časové razítko do dotazů. Nezapomeňte přidat vlastní-klauzule where, aby se zabránilo získání obrovské odpovědi.



## <a name="import-data"></a>Import dat

Data můžete importovat ze souboru CSV. Typická využití se neimportují statických dat, které se můžete připojit s tabulkami z telemetrie. 

Například pokud ověřený uživatelé se budou identifikovat telemetrie alias nebo zkomolené id, může importovat tabulka, která se mapuje na skutečné názvy aliasů. Provedením spojení na telemetrická žádost můžete identifikovat uživatele, jejich skutečné názvy v sestavách analýzy.

### <a name="define-your-data-schema"></a>Definování schématu dat

1. Klikněte na tlačítko **nastavení** (v horní části vlevo) a potom **zdroje dat**. 
2. Přidání zdroje dat, postupujte podle pokynů. Zobrazí se výzva k zadání ukázkových dat, která by měla obsahovat alespoň 10 řádků. Je pak opravit schématu.

Definuje zdroj dat, který pak můžete importovat jednotlivé tabulky.

### <a name="import-a-table"></a>Import tabulky

1. Otevřete vaše definice zdroje dat ze seznamu.
2. Klikněte na tlačítko "Odeslat" a postupujte podle pokynů k nahrání v tabulce. To zahrnuje volání rozhraní REST API, a proto je snadno automatizovat. 

Vaše tabulka je nyní k dispozici pro použití v analytické dotazy. Zobrazí se vám v Analytics 

### <a name="use-the-table"></a>Pomocí tabulky

Předpokládejme, že vaše definice zdroje dat se nazývá `usermap`, a že má dvě pole `realName` a `user_AuthenticatedId`. `requests` Tabulka má také pole s názvem `user_AuthenticatedId`, takže je snadné připojení je:

```AIQL

    requests
    | where notempty(user_AuthenticatedId) | take 10
    | join kind=leftouter ( usermap ) on user_AuthenticatedId 
```
Výsledná tabulka požadavků má sloupec, `realName`.

### <a name="import-from-logstash"></a>Importovat z LogStash

Pokud používáte [LogStash](https://www.elastic.co/guide/en/logstash/current/getting-started-with-logstash.html), Analytics můžete použít k dotazování protokolů. Použití [modul plug-in, který prostřednictvím kanálu předá data do Analytics](https://github.com/Microsoft/logstash-output-application-insights). 

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 

[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]


---
title: 'Kurz: Prozkoumejte Azure čas Series Insights Javascriptovou klientskou knihovnu | Dokumentace Microsoftu'
description: Seznamte se s javascriptovou klientskou knihovnou pro službu Azure Time Series Insights a se souvisejícím programovacím modelem.
author: ashannon7
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 05/06/2019
ms.author: dpalled
ms.custom: seodec18
ms.openlocfilehash: c6cfd2069851138d738b1533eaab74d9d7aedda6
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2019
ms.locfileid: "66243986"
---
# <a name="tutorial-explore-the-azure-time-series-insights-javascript-client-library"></a>Kurz: Zkoumání javascriptové klientské knihovny pro službu Azure Time Series Insights

Klientské knihovny založené na jazyce JavaScript D3 Azure Time Series Insights byla vyvinuta a webové vývojáře v dotazu a vizualizovat data uložená v Time Series Insights. Tento kurz vás provede klientské knihovny služby Time Series Insights a programovací model s použitím prostředí ukázkovou aplikaci.

Tento kurz podrobně popisuje, jak pracovat s knihovnou, jak přistupovat k datům v Time Series Insights a použití ovládacích prvků graf k vykreslení a vizualizovat data. Zobrazí se také dozvíte, jak experimentovat s různými druhy grafů k vizualizaci dat. Na konci tohoto kurzu budete moct používat klientské knihovny k začlenění funkcí Time Series Insights do své vlastní webové aplikace.

Konkrétně se dozvíte o:

> [!div class="checklist"]
> * Ukázková aplikace služby Time Series Insights
> * Čas Series Insights Javascriptovou klientskou knihovnu
> * Jak ukázková aplikace používá knihovny k vizualizaci dat Time Series Insights

> [!NOTE]
> * V tomto kurzu použijete bezplatnou, hostované [ukázkové webové služby Time Series Insights](https://insights.timeseries.azure.com/clientsample).
> * Time Series Insights ukázkové aplikace zdrojové soubory jsou k dispozici v [ukázkového úložiště Githubu](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial).
> * Přečtěte si [Time Series Insights klienta referenční dokumentaci](https://github.com/microsoft/tsiclient/blob/master/docs/API.md).

## <a name="video"></a>Video

V tomto videu zavedeme open source čas Series Insights JavaScript SDK:
<br /><br />

> [!VIDEO https://www.youtube.com/embed/X8sSm7Pl9aA]

## <a name="prerequisites"></a>Požadavky

Tento kurz využívá v prohlížeči **vývojářské nástroje** funkce. Moderní webové prohlížeče ([Microsoft Edge](/microsoft-edge/devtools-guide), [Chrome](https://developers.google.com/web/tools/chrome-devtools/), [Firefox](https://developer.mozilla.org/en-US/docs/Learn/Common_questions/What_are_browser_developer_tools), [Safari](https://developer.apple.com/safari/tools/)a další) obvykle poskytují přístup k **Webové zobrazení inspektoru** prostřednictvím klávesová zkratka F12 na klávesnici. Je další způsob, jak přístup k zobrazení klikněte pravým tlačítkem na webovou stránku, a potom vyberte **zkontrolovat Element**.

## <a name="time-series-insights-sample-application"></a>Ukázková aplikace Time Series Insights

V celém tomto kurzu používáme zadarmo si vyzkoušejte prostředí Time Series Insights ukázkovou aplikaci prozkoumejte zdrojového kódu aplikace a prozkoumat čas Series Insights Javascriptovou klientskou knihovnu. S využitím ukázkové aplikace, se dozvíte, jak pracovat s Time Series Insights v jazyce JavaScript a vizualizovat data pomocí diagramů a grafů.

1. Přejděte [Time Series Insights ukázkovou aplikaci](https://insights.timeseries.azure.com/clientsample). Zobrazí se následující výzva přihlášení:

   [![Čas Series Insights klienta ukázka výzvě přihlášení](media/tutorial-explore-js-client-lib/tcs-sign-in.png)](media/tutorial-explore-js-client-lib/tcs-sign-in.png#lightbox)

1. Vyberte **přihlášení** zadejte nebo vyberte svoje přihlašovací údaje. Použijte účet organizace podnikové sítě (Azure Active Directory) nebo osobní účet (účet Microsoft).

   [![Ukázkové přihlašovací údaje pro klienta Series Insights čas výzvu](media/tutorial-explore-js-client-lib/tcs-sign-in-enter-account.png)](media/tutorial-explore-js-client-lib/tcs-sign-in-enter-account.png#lightbox)

1. Po přihlášení se zobrazí stránka, která zobrazuje grafy naplněný daty Time Series Insights. V pravém horním rohu se zobrazí váš uživatelský účet a možnost **Log out** (Odhlásit se):

   [![Čas Series Insights klienta ukázka hlavní stránku po přihlášení](media/tutorial-explore-js-client-lib/tcs-main-after-signin.png)](media/tutorial-explore-js-client-lib/tcs-main-after-signin.png#lightbox)

### <a name="page-source-and-structure"></a>Zdrojový kód a struktura stránky

Nejprve se podívejme se [HTML a JavaScript zdrojový kód](https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/index.html) vykreslené webové stránky:

1. Otevřete v prohlížeči **Vývojářské nástroje**. Prozkoumejte elementy HTML, ze kterých se skládá aktuální stránka (označují se také jako strom HTML nebo DOM).

1. Rozbalte `<head>` a `<body>` prvků a podívejte se v následujících částech:

   * V části `<head>` elementu, najdete stránce metadat a závislosti, které umožňují spuštění aplikace:
     * A `<script>` element, který slouží k odkazu na soubor Azure Active Directory Authentication Library (ADAL) *adal.min.js*. ADAL je javascriptová knihovna, která umožňuje ověřování OAuth 2.0 (přihlášení) a získávání tokenů pro přístup k rozhraním API.
     * Více `<link>` prvky pro šablony stylů (označované také jako *šablon stylů CSS*) jako *sampleStyles.css* a *tsiclient.css*. Šablony stylů řízení používání stylů pro podrobnosti visual stránky, jako jsou barvy, písma a mezery.
     * A `<script>` element, který se použije k odkazování čas Series Insights Javascriptovou klientskou knihovnu *tsiclient.js*. Na stránce používá knihovnu pro volání rozhraní API služby Time Series Insights a pro vykreslení ovládacích prvků grafu na stránce.

     >[!NOTE]
     > * Zdrojový kód pro knihovnu ADAL JavaScript je k dispozici v [úložiště azure-activedirectory knihovny pro js](https://github.com/AzureAD/azure-activedirectory-library-for-js).
     > * Zdrojový kód klientské knihovny čas Series Insights JavaScript je k dispozici v [tsiclient úložiště](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial).

   * V části `<body>` elementu, najdete tu `<div>` prvky, které umožňují definovat rozložení položek na stránce a jiné `<script>` element:
     * První `<div>` určuje element **přihlášení** dialogové okno (`id="loginModal"`).
     * Druhý element `<div>` slouží jako nadřazený prvek pro:
       * Element `<div>` hlavičky, který se používá pro stavové zprávy a informace o přihlášení v horní části stránky (`class="header"`).
       * A `<div>` – element pro zbývající prvky těla stránky, včetně grafů (`class="chartsWrapper"`).
       * A `<script>` části, která obsahuje JavaScript, který se používá k řízení stránky.

   [![Čas Series Insights Client sample s nástroji pro vývoj](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png#lightbox)

1. Rozbalte `<div class="chartsWrapper">` elementu a zjistíte další podřízené `<div>` elementy. Tyto prvky se používají k umístění jednotlivých ovládacích prvků ukázkových grafů. Existuje několik dvojice `<div>` prvky, jeden pro každý příklad grafu:

   * První element (`class="rowOfCardsTitle"`) obsahuje popisný název, který shrnuje, co graf znázorňuje. Příklad: `Static Line Charts With Full-Size Legends.`
   * Druhý (`class="rowOfCards"`) element je nadřazeného objektu, který obsahuje další podřízené `<div>` prvky, které umístit ovládací prvky všechno schéma na řádku.

   [![Prvky div těla](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-divs.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-divs.png#lightbox)

1. Rozbalte `<script type="text/javascript">` element, který je přímo pod `<div class="chartsWrapper">` elementu. Začátku části JavaScript úrovni stránky se používá ke zpracování veškerou logiku stránky (ověřování, volání rozhraní API, vykreslení ovládacích prvků grafu a další služby Time Series Insights):

   [![Text skriptu](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-script.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-script.png#lightbox)

## <a name="time-series-insights-javascript-client-library-concepts"></a>Čas Series Insights JavaScript klienta knihovny koncepty

Klientská knihovna služby Time Series Insights (*tsclient.js*) poskytuje abstrakci pro dvě důležité funkce jazyka JavaScript:

* **Metody obálky volání API pro dotazy čas Series Insights**: Rozhraní REST API, které můžete použít k dotazování pro Time Series Insights data pomocí agregační výrazy. Metody jsou uspořádány v rámci oboru názvů TsiClient.Server knihovny.

* **Metody vytvoření a naplnění několik typů grafů ovládací prvky**: Můžete použít k vykreslení Time Series Insights metod agregovat data na webové stránce. Metody jsou uspořádány v rámci oboru názvů TsiClient.UX knihovny.

Prostřednictvím těchto zjednodušení vývojářům snadněji vytvářet komponenty uživatelského rozhraní pro graf a graf, které se využívají data služby Time Series Insights.

### <a name="authentication"></a>Authentication

[Time Series Insights ukázkovou aplikaci](https://insights.timeseries.azure.com/clientsample) je jednostránkové aplikace s podporou ověřování uživatelů ADAL OAuth 2.0:

1. Při použití knihovny ADAL pro ověřování, klientská aplikace musí být zaregistrovaný ve službě Azure Active Directory (Azure AD). Ve skutečnosti je jednostránkové aplikace zaregistrované k používání [OAuth 2.0 implicitní tok poskytování](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-implicit-grant-flow).
1. Aplikaci musíte zadat některé vlastnosti registrace za běhu. Mezi vlastnosti patří identifikátor GUID klienta (`clientId`) a identifikátor URI pro přesměrování (`postLogoutRedirectUri`).
1. Později, že aplikace vyžaduje *přístupový token* ze služby Azure AD. Přístupový token je vydaný pro omezené sadu oprávnění pro konkrétní službu nebo identifikátor rozhraní API (protokol https:\//api.timeseries.azure.com). Oprávnění tokenu se vystavují jménem přihlášeného uživatele. Identifikátor pro službu nebo rozhraní API je další vlastnost, která je součástí registrace Azure AD vaší aplikace.
1. Po ADAL vrátí do aplikace přístupový token, je jí předán jako *nosný token* když přistupuje k rozhraním API služby Time Series Insights.

   [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=147-204&highlight=3-7,34-37)]

> [!TIP]
> Další informace o knihovnách ověřování nepodporuje Microsoft Azure AD, najdete v článku [referenční dokumentace k Azure Active Directory Authentication Library](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries#microsoft-supported-client-libraries).

### <a name="control-identification"></a>Identifikace ovládacího prvku

V tomto příkladu zadaná `<div>` prvky jsou uspořádány do nadřazené `<body>` element představují rozumné rozložení grafu ovládací prvky tohoto vykreslit na stránce.

Každý `<div>` prvek určuje vlastnosti umístění a vizuální atributy prvků grafu. HTML element `id` vlastnosti slouží jako jedinečné identifikátory pro vazbu k určité ovládací prvky k vykreslení a aktualizaci vizualizují data.

### <a name="aggregate-expressions"></a>Agregační výrazy

Klientské knihovny rozhraní API služby Time Series Insights pomocí agregační výrazy:

* Agregační výraz poskytuje možnost vytvořit jeden nebo více *hledané termíny*.

* Klientské rozhraní API jsou navržená poskytuje podobné funkce pro další ukázkovou aplikaci ( [Průzkumníka služby Time Series Insights](https://insights.timeseries.azure.com/demo)), který používá vyhledávání značka span, `where` predikáty, míry, a `splitBy` hodnoty.

* Většina klientské knihovny rozhraní API využít celou řadu agregační výrazy, které služba používá k vytvoření dotazu dat Time Series Insights.

### <a name="call-pattern"></a>Vzor volání

Sestavování a vykreslení ovládacích prvků grafu Následuje obecný vzor. Obecný vzor v celé ukázkové aplikace můžete sledovat a vám můžou pomoct při použití klientské knihovny:

1. Deklarovat `array` k uchování jednoho nebo více agregačních výrazů Time Series Insights:

   ```javascript
   var aes =  [];
   ```

1. Sestavení *1* k *n* agregovat objekty výrazů. Potom je přidáte do pole agregační výraz:

   ```javascript
   var ae = new tsiClient.ux.aggregateExpression(predicateObject, measureObject, measureTypes, searchSpan, splitByObject, color, alias, contextMenuActions);
   aes.push(ae);
   ```

   **Parametry aggregateExpression**

   | Parametr | Popis | Příklad: |
   | --------- | ----------- | ------- |
   | `predicateObject` | Výraz filtru dat |`{predicateString: "Factory = 'Factory3'"}` |
   | `measureObject`   | Název míry, který se používá | `{property: 'Temperature', type: "Double"}` |
   | `measureTypes`    | Agregace požadovanou vlastnost míry | `['avg', 'min']` |
   | `searchSpan`      | Doba trvání a intervalu velikosti agregační výraz | `{from: startDate, to: endDate, bucketSize: '2m'}` |
   | `splitByObject`   | Řetězcová vlastnost chcete rozdělit podle (volitelné: může mít hodnotu null) | `{property: 'Station', type: 'String'}` |
   | `color`         | Barva objektů, který chcete vykreslit | `'pink'` |
   | `alias`           | Popisný název pro agregační výraz | `'Factory3Temperature'` |
   | `contextMenuActions` | Pole akcí, které mají být vázány s objekty řady času ve vizualizaci (volitelné) | Další informace najdete v tématu [rozbalovací kontextové nabídky](#pop-up-context-menus). |

1. Volání dotazů Time Series Insights pomocí rozhraní API TsiClient.Server pro agregovaná data žádosti:

   ```javascript
   tsiClient.server.getAggregates(token, envFQDN, aeTsxArray);
   ```

   **Parametry getAggregates**

   | Parametr | Popis | Příklad: |
   | --------- | ----------- | ------- |
   | `token`     | Přístupový token pro rozhraní API čas Series Insights |  `authContext.getTsiToken()`<br />Další informace najdete v tématu [Ověřování](#authentication). |
   | `envFQDN`   | Plně kvalifikovaný název domény (FQDN) pro prostředí Time Series Insights | Na webu Azure Portal. Například: `10000000-0000-0000-0000-100000000108.env.timeseries.azure.com`. |
   | `aeTsxArray` | Pole Time Series Insights – výrazy dotazů | Použití `aes` proměnné, jak je popsáno výše: `aes.map(function(ae){return ae.toTsx()}`. |

1. Transformace komprimované výsledek, který je vrácen z dotazu služby Time Series Insights do formátu JSON pro vizualizaci:

   ```javascript
   var transformedResult = tsiClient.ux.transformAggregatesForVisualization(result, aes);
   ```

1. Vytvoření ovládacího prvku grafu pomocí rozhraní API TsiClient.UX. Vázat na jeden z `<div>` elementy na stránce:

   ```javascript
   var barChart = new tsiClient.ux.BarChart(document.getElementById('chart3'));
   ```

1. Naplnění ovládacího prvku grafu s transformovaných dat objektů JSON a vykreslit ovládací prvek na stránce:

   ```javascript
   barChart.render(transformedResult, {grid: true, legend: 'compact', theme: 'light'}, aes);
   ```

## <a name="render-controls"></a>Vykreslení ovládacích prvků

Klientská knihovna služby Time Series Insights poskytuje osm jedinečné, out-of-the-box analytics ovládacích prvků:

* **Spojnicový graf**
* **Výsečový graf**
* **Pruhový graf**
* **heatmap**
* **ovládací prvky hierarchie**
* **přístupné mřížky**
* **diskrétní události časové osy**
* **Přechod stavu časové osy**

### <a name="line-chart-bar-chart-and-pie-chart-examples"></a>Spojnicový graf, sloupcový graf a příklady výsečový graf

Podívejte se na ukázku kódu, který slouží k vykreslení některé ovládací prvky standardní grafu. Poznámka: programovací model a vzory pro vytváření těchto ovládacích prvků. Konkrétně zkontrolujte HTML v části `// Example 3/4/5` komentář, který vykreslí ovládací prvky s HTML `id` hodnoty `chart3`, `chart4`, a `chart5`.

Pamatujete z kroku 3 [stránce části zdroje a struktura](#page-source-and-structure) , graf ovládací prvky jsou uspořádány do řádků na stránce. Každý graf – ovládací prvek má řádek popisný název. V tomto příkladu tři grafy zaplnění pod `Multiple Chart Types From the Same Data` title `<div>` element a jsou vázána na tři `<div>` prvky, které jsou pod názvem:

[!code-html[code-sample1-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=59-73&highlight=1,5,9,13)]

Následující části kódu jazyka JavaScript používá vzory, které byly dříve uvedených: agregační výrazy Time Series Insights, použijte k dotazování na data služby Time Series Insights a následnému vykreslení tři grafy. Z oboru názvů tsiClient.ux se používají tři typy grafů: `LineChart`, `BarChart`, a `PieChart`. Typy grafů se používají k vytváření a vykreslování odpovídajících grafů. Všechny tři grafy můžete použít stejná data agregační výraz `transformedResult`:

[!code-javascript[code-sample2-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=241-262&highlight=13-14,16-17,19-20)]

Po vykreslení budou tři grafy vypadat následovně:

[![Několik typů grafů s použitím stejných dat](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png)](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png#lightbox)

## <a name="advanced-features"></a>Pokročilé funkce

Klientská knihovna služby Time Series Insights má několik dalších funkcí, které můžete použít k implementaci vizualizace dat uzdu svým snům.

### <a name="states-and-events"></a>Stavy a události

Pokročilé funkce je možnost přidání přechodů mezi stavy a diskrétní události do grafů. Tato funkce je užitečná pro zvýraznění incidenty, výstrahy a vytváření stavu přepínačů (zapnuto/vypnuto přepínače, například).

Podívejte se na kód kolem `// Example 10` komentář. Kód vykreslí ovládací prvek řádku pod nadpisem `Line Charts with Multiple Series Types` a připojí ho k `<div>` element s HTML `id` hodnota `chart10`.

Následující kroky popisují proces:

1. Struktura s názvem `events4` je definována pro změnu stavu prvky pro sledování. Struktura obsahuje:

   * Řetězcový klíč `Component States`.
   * Pole objektů hodnot představujících stavy. Každý objekt obsahuje:
     * Řetězcový klíč obsahující javascriptové časové razítko ve formátu ISO
     * Pole, které obsahuje charakteristiky stavu: barvu a popis

1. `events5` Struktura je definována pro `Incidents`, který obsahuje celou řadu událostí prvky pro sledování. Struktura pole má stejný tvar jako výše uvedená struktura pole `events4`.

1. Spojnicový graf se vykreslí a předává do dvou struktur se možnosti grafu parametry: `events:` a `states:`. Všimněte si ostatní parametry možností pro zadání `tooltip:`, `theme:`, nebo `grid:`.

[!code-javascript[code-sample-states-events](~/samples-javascript/pages/tutorial/index.html?range=337-389&highlight=5,26,51)]

Kosočtverce značky/pop-registrace systému windows, které slouží k označení incidenty a barevné pruhy/pop-registrace windows v časovém rozsahu označují, změny stavu:

[![Spojnicové grafy s více typy datových řad](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png)](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png#lightbox)

### <a name="pop-up-context-menus"></a>Automaticky otevírané místní nabídky

Další pokročilé funkce je možnost vytvářet vlastní místní nabídky (klikněte pravým tlačítkem na místní nabídky). Vlastní místní nabídky jsou užitečné pro umožnění akcí a logických dalších kroků v rámci aplikace.

Prohlédněte si kód kolem `// Example 13/14/15` komentář. Tento kód vykreslení spojnicový graf pod nadpisem `Line Chart with Context Menu to Create Pie/Bar Chart`. Graf je vázán na `<div>` element s HTML `id` hodnota `chart13`.

Spojnicový graf prostřednictvím místních nabídek umožňuje dynamicky vytvořit výsečový a pruhový graf s vazbou na elementy `<div>` s ID `chart14` a `chart15`. Výsečový graf a pruhový graf také použít kontextové nabídky pro povolení vlastních funkcí: možnost pro kopírování dat z výsečového grafu na pruhový graf a tisknout data pruhového grafu do okna konzoly prohlížeče, v uvedeném pořadí.

Následující kroky popisují proces:

1. Řadu vlastní akce jsou definovány. Každá akce obsahuje pole s jedním nebo více elementy. Každý element definuje jednu položku místní nabídky:

   * `barChartActions`: Tato akce definuje kontextovou nabídku pro výsečový graf, který obsahuje jeden prvek. Chcete-li definovat jednu položku:
     * `name`: Text, který se používá pro položku nabídky: "Print parametry do konzoly."
     * `action`: Akce přidružené položky nabídky. Akce je vždy anonymní funkce, která přebírá tři argumenty založené na agregačním výrazu použitém k vytvoření grafu. V tomto případě se argumenty zapisují do okna konzoly v prohlížeči:
       * `ae`: Agregační výraz pole.
       * `splitBy`: `splitBy` Hodnotu.
       * `timestamp`: Časové razítko.

   * `pieChartActions`: Tato akce definuje kontextovou nabídku pro pruhový graf, který obsahuje jeden prvek. Chcete-li definovat jednu položku. Tvar a schématu je stejné jako `barChartActions` element je popsáno výše, ale `action` funkce se značně liší: vytvoří instanci a vykreslí pruhový graf. `ae` Argument se používá k určení pole agregační výraz, který je předán za běhu, když se otevře položku nabídky. Funkce také nastaví vlastnost `ae.contextMenu` s místní nabídkou `barChartActions`.
   * `contextMenuActions`: Tato akce definuje kontextovou nabídku pro spojnicový graf, který obsahuje tři prvky k definování tři položky nabídky. Tvar a schéma pro každý prvek je stejný jako prvky, které bylo popsáno dříve. První položka stejně jako v případě elementu `barChartActions` zapíše tři argumenty funkce do okna konzoly v prohlížeči. Podobně jako `pieChartActions` elementu, druhé dvě položky vytvořit instanci a vykreslování výsečový graf a pruhový graf, v uvedeném pořadí. Další dvě položky také nastaví ve svých vlastnostech `ae.contextMenu` místní nabídky `pieChartActions` a `barChartActions` (v uvedeném pořadí).

1. Dva agregační výrazy jsou vloženy do `aes` agregační výraz pole. Určují `contextMenuActions` pro každou položku pole. Tyto výrazy se používají v ovládacím prvku spojnicového grafu.

1. Spojnicový graf je zpočátku vykresleno, ze kterého může být vykreslen výsečový graf a pruhový graf v době běhu.

[!code-javascript[code-sample-context-menus](~/samples-javascript/pages/tutorial/index.html?range=461-540&highlight=7,16,29,61-64,78)]

Následující snímek obrazovky ukazuje grafy s jejich odpovídajících rozbalovací kontextové nabídky. Výsečový graf a pruhový graf vytvořil dynamicky pomocí možností v nabídce grafu kontextu řádku.

[![Spojnicový graf s místní nabídka pro vytvoření výsečový graf a pruhový graf](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png)](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png#lightbox)

### <a name="brushes"></a>Štětce

Štětce můžete použít k určení rozsahu časový rozsah pro definování akcí, jako jsou přiblížení a prozkoumat.

Kód, který se používá ke znázornění štětce je zobrazena ve `Line Chart with Context Menu to Create Pie/Bar Chart` příklad, který popisuje místní kontextové nabídky.

* Akce štětců jsou podobné místní nabídce v tom, že pro štětec definují řadu vlastních akcí. Každá akce obsahuje pole, která má jeden nebo více prvků. Každý element definuje jednu položku místní nabídky:
   * `name`: Text, který se používá pro položku nabídky: "Print parametry do konzoly."
   * `action`: Akce, která je přidružená položka nabídky, který je vždycky anonymní funkce, která přebírá dva argumenty. V tomto případě se argumenty zapisují do okna konzoly v prohlížeči:
     * `fromTime`: `from` Časové razítko výběr stopy.
     * `toTime`: `to` Časové razítko výběr stopy.

* Akce štětců se přidávají jako další vlastnost možnosti grafu. `brushContextMenuActions: brushActions` Vlastnost předána `linechart.Render` volání.

[!code-javascript[code-sample-brushes](~/samples-javascript/pages/tutorial/index.html?range=526-540&highlight=1,13)]

[![Spojnicový graf s místní nabídkou vytvořte výsečový graf a pruhový graf pomocí štětců](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart-brushes.png)](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart-brushes.png#lightbox)

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Přihlaste se a prozkoumejte Time Series Insights ukázkovou aplikaci a její zdroj
> * Použití rozhraní API v klientské knihovně pro čas Series Insights JavaScript
> * Pomocí JavaScriptu můžete vytvořit a naplnit ovládacích prvků grafu pomocí dat služby Time Series Insights

Time Series Insights ukázková aplikace používá ukázkové datové sady. Informace o tom, jak můžete vytvořit vlastní prostředí Time Series Insights a datové sady, najdete v následujícím článku:

> [!div class="nextstepaction"]
> [Kurz: Vytvoření prostředí Azure Time Series Insights](tutorial-create-populate-tsi-environment.md)

Nebo zobrazení souborů zdrojového Time Series Insights ukázkové aplikace:

> [!div class="nextstepaction"]
> [Úložiště ukázkové aplikace čas Series Insights](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial)

Přečtěte si referenční dokumentaci rozhraní API klienta služby Time Series Insights:

> [!div class="nextstepaction"]
> [Dokumentace k rozhraní API Series Insights čas](https://github.com/microsoft/tsiclient/blob/master/docs/API.md)

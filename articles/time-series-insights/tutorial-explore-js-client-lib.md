---
title: 'Kurz: Prozkoumejte knihovnu klienta Azure Time Series Insights JavaScript | Microsoft Docs'
description: V tomto kurzu se dozvíte o knihovně klienta Azure Time Series Insights JavaScript a souvisejícím programovacím modelu.
author: ashannon7
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 07/29/2019
ms.author: dpalled
ms.custom: seodec18
ms.openlocfilehash: 3e56792673c33ac641ce23213f2b3662b889ec2a
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68725666"
---
# <a name="tutorial-explore-the-azure-time-series-insights-javascript-client-library"></a>Kurz: Zkoumání javascriptové klientské knihovny pro službu Azure Time Series Insights

Klientská knihovna Azure Time Series Insights založená na jazyce JavaScript D3 byla vyvinuta za účelem usnadnění dotazů webových vývojářů a vizualizací dat uložených v Time Series Insights. Tento kurz vás provede procesem Time Series Insights klientské knihovny a programovacího modelu pomocí hostované ukázkové aplikace.

Kurz podrobně popisuje, jak pracovat s knihovnou, jak získat přístup k Time Series Insights dat a jak používat ovládací prvky grafu k vykreslování a vizualizaci dat. Naučíte se také, jak experimentovat s různými druhy grafů k vizualizaci dat. Na konci tohoto kurzu budete moct pomocí klientské knihovny začlenit funkce Time Series Insights do své vlastní webové aplikace.

Konkrétně se dozvíte o:

> [!div class="checklist"]
> * Ukázková aplikace Time Series Insights
> * Klientská knihovna Time Series Insights JavaScript
> * Jak ukázková aplikace používá knihovnu k vizualizaci Time Series Insights dat

> [!NOTE]
> * V tomto kurzu se používá bezplatná, hostovaná [Time Series Insights webová ukázka](https://insights.timeseries.azure.com/clientsample).
> * Zdrojové soubory ukázkové aplikace Time Series Insights jsou k dispozici v [úložišti ukázek GitHubu](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial).
> * Přečtěte si [referenční dokumentaci Time Series Insights klienta](https://github.com/microsoft/tsiclient/blob/master/docs/API.md).

Zaregistrujte si [bezplatné předplatné Azure](https://azure.microsoft.com/free/) , pokud ho ještě nemáte.

## <a name="prerequisites"></a>Požadavky

* V tomto kurzu se používá funkce **vývojářské nástroje** vašeho prohlížeče. Moderní webové prohlížeče ([Microsoft Edge](/microsoft-edge/devtools-guide), [Chrome](https://developers.google.com/web/tools/chrome-devtools/), [Firefox](https://developer.mozilla.org/en-US/docs/Learn/Common_questions/What_are_browser_developer_tools), [Safari](https://developer.apple.com/safari/tools/)a další) obvykle poskytují přístup k **zobrazení webové kontroly** přes klávesovou zkratku F12 na klávesnici. Dalším způsobem, jak získat přístup k zobrazení, je kliknout pravým tlačítkem na webovou stránku a pak vybrat **zkontrolovat element**.

## <a name="review-video"></a>Zkontrolovat video

V tomto videu zavádíme Open-Source Time Series Insights JavaScript SDK:
<br /><br />

> [!VIDEO https://www.youtube.com/embed/X8sSm7Pl9aA]

## <a name="sample-application"></a>Ukázková aplikace

V celém tomto kurzu používáme bezplatnou a hostovanou ukázkovou aplikaci Time Series Insights k prozkoumání zdrojového kódu za aplikací a k prozkoumání Time Series Insights klientské knihovny JavaScript. Pomocí ukázkové aplikace se naučíte pracovat s Time Series Insights v JavaScriptu a vizualizovat data prostřednictvím grafů a grafů.

1. Přejít na [ukázkovou aplikaci Time Series Insights](https://insights.timeseries.azure.com/clientsample). Zobrazí se následující výzva k přihlášení:

   [![Ukázka výzvy k přihlášení k ukázce klienta Time Series Insights](media/tutorial-explore-js-client-lib/tcs-sign-in.png)](media/tutorial-explore-js-client-lib/tcs-sign-in.png#lightbox)

1. Vyberte **Přihlásit** se a zadejte nebo vyberte svoje přihlašovací údaje. Použijte buď účet organizace organizace (Azure Active Directory), nebo osobní účet (účet Microsoft).

   [![Výzva k zadání ukázkových přihlašovacích údajů klienta Time Series Insights](media/tutorial-explore-js-client-lib/tcs-sign-in-enter-account.png)](media/tutorial-explore-js-client-lib/tcs-sign-in-enter-account.png#lightbox)

1. Po přihlášení se zobrazí stránka, která zobrazuje grafy naplněné Time Series Insightsmi daty. V pravém horním rohu se zobrazí váš uživatelský účet a možnost **Log out** (Odhlásit se):

   [![Hlavní stránka ukázky Time Series Insights klienta po přihlášení](media/tutorial-explore-js-client-lib/tcs-main-after-signin.png)](media/tutorial-explore-js-client-lib/tcs-main-after-signin.png#lightbox)

### <a name="page-source-and-structure"></a>Zdrojový kód a struktura stránky

Nejdřív si Podívejme [zdrojový kód HTML a JavaScript](https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/index.html) vykreslené webové stránky:

1. Otevřete v prohlížeči **Vývojářské nástroje**. Prozkoumejte elementy HTML, ze kterých se skládá aktuální stránka (označují se také jako strom HTML nebo DOM).

1. Rozbalte prvky `<body>` a a sledujte následující části: `<head>`

   * V rámci `<head>` elementu najdete metadata stránky a závislosti, které umožňují spuštění aplikace:
     * Element, který se používá k odkazování na soubor ADAL (Azure Active Directory Authentication Library) *ADAL. min. js.* `<script>` ADAL je javascriptová knihovna, která umožňuje ověřování OAuth 2.0 (přihlášení) a získávání tokenů pro přístup k rozhraním API.
     * Více `<link>` prvků pro šablony stylů (označované také jako *CSS*), jako je *sampleStyles. CSS* a *tsiclient. CSS*. Šablony stylů určují podrobnosti o stylu vizuální stránky, jako jsou barvy, písma a mezery.
     * Element, který slouží k odkazování na Time Series Insights JavaScript Client Library *tsiclient. js.* `<script>` Stránka používá knihovnu k volání rozhraní API služby Time Series Insights a vykreslení ovládacích prvků grafu na stránce.

     >[!NOTE]
     > * Zdrojový kód pro knihovnu JavaScriptu knihovny ADAL je k dispozici v [úložišti Azure-Active Directory-Library-for-js](https://github.com/AzureAD/azure-activedirectory-library-for-js).
     > * Zdrojový kód klientské knihovny Time Series Insights JavaScript je k dispozici v [úložišti tsiclient](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial).

   * V rámci `<div>`elementunajdete prvky, které vám pomůžou definovat rozložení položek na stránce a další `<script>` prvek: `<body>`
     * První `<div>` prvek určuje dialogové okno **přihlášení** (`id="loginModal"`).
     * Druhý element `<div>` slouží jako nadřazený prvek pro:
       * Element `<div>` hlavičky, který se používá pro stavové zprávy a informace o přihlášení v horní části stránky (`class="header"`).
       * Prvek pro zbytek prvků textu stránky, včetně grafů (`class="chartsWrapper"`). `<div>`
       * `<script>` Oddíl obsahující JavaScript, který se používá k řízení stránky.

   [![Ukázka Time Series Insights klienta s Vývojářské nástroje](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png#lightbox)

1. Rozbalte element a získáte více podřízených `<div>` elementů. `<div class="chartsWrapper">` Tyto prvky se používají k umístění jednotlivých ovládacích prvků ukázkových grafů. Existuje několik párů `<div>` prvků, jeden pro každý příklad grafu:

   * První element (`class="rowOfCardsTitle"`) obsahuje popisný název, který shrnuje, co graf znázorňuje. Příklad: `Static Line Charts With Full-Size Legends.`
   * Druhý prvek (`class="rowOfCards"`) je nadřazený objekt, který obsahuje další podřízené `<div>` prvky, které umístí vlastní ovládací prvky grafu v rámci řádku.

   [![Prvky DIV textu](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-divs.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-divs.png#lightbox)

1. Rozbalte prvek, který je přímo `<div class="chartsWrapper">` pod prvkem. `<script type="text/javascript">` Začátek oddílu JavaScriptu na úrovni stránky se používá ke zpracování celé stránky logiky (ověřování, volání rozhraní API služby Time Series Insights, vykreslování ovládacích prvků grafu a dalších):

   [![Skript textu](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-script.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-script.png#lightbox)

## <a name="javascript-client-library"></a>Klientská knihovna JavaScript

Klientská knihovna Time Series Insights (*tsiclient. js*) poskytuje abstrakce pro dvě důležité funkce JavaScriptu:

* **Metody obálky pro volání rozhraní API pro Time Series Insights dotazy**: Rozhraní REST API můžete použít k dotazování na data Time Series Insights pomocí agregačních výrazů. Metody jsou uspořádány pod oborem názvů TsiClient. Server knihovny.

* **Metody pro vytvoření a naplnění několika typů ovládacích prvků pro vytváření grafů**: Metody, které lze použít k vykreslení Time Series Insights agregovaná data na webové stránce. Metody jsou uspořádány pod oborem názvů TsiClient. UX knihovny.

Prostřednictvím těchto zjednodušení můžou vývojáři snadněji vytvářet komponenty grafu uživatelského rozhraní a grafů, které jsou napájené s Time Series Insightsmi daty.

### <a name="authentication"></a>Ověřování

[Ukázková aplikace Time Series Insights](https://insights.timeseries.azure.com/clientsample) je jednostránkové aplikace s podporou ověřování uživatelů ADAL OAuth 2,0:

1. Když použijete ADAL pro ověřování, klientská aplikace musí být zaregistrovaná ve službě Azure Active Directory (Azure AD). Jedna stránková aplikace je ve skutečnosti zaregistrovaná tak, aby používala [tok implicitního udělení OAuth 2,0](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-implicit-grant-flow).
1. Aplikace musí určit některé vlastnosti registrace za běhu. Mezi vlastnosti patří identifikátor GUID klienta (`clientId`) a identifikátor URI přesměrování (`postLogoutRedirectUri`).
1. Později aplikace požádá o *přístupový token* z Azure AD. Přístupový token je vydaný pro konečnou sadu oprávnění pro konkrétní službu nebo identifikátor rozhraní API (https:\//API.timeseries.Azure.com). Oprávnění tokenu se vystavují jménem přihlášeného uživatele. Identifikátor služby nebo rozhraní API je jiná vlastnost, která je obsažena v registraci služby Azure AD v aplikaci.
1. Poté, co ADAL vrátí přístupový token do aplikace, se předává jako *nosný token* při přístupu k rozhraním api služby Time Series Insights.

   [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=147-204&highlight=3-7,34-37)]

> [!TIP]
> Další informace o knihovnách ověřování Azure AD podporovaných Microsoftem najdete v [referenční dokumentaci ke knihovně](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries#microsoft-supported-client-libraries)ověřování v Azure Active Directory.

### <a name="control-identification"></a>Identifikace ovládacího prvku

V uvedeném příkladu `<div>` jsou prvky uspořádány v nadřazeném `<body>` elementu pro poskytnutí rozumné rozložení pro ovládací prvky grafu, které vykreslují na stránce.

Každý `<div>` prvek určuje vlastnosti umístění a vizuálních atributů ovládacích prvků grafu. Vlastnosti elementu `id` HTML slouží jako jedinečné identifikátory pro vytvoření vazby na konkrétní ovládací prvky pro vykreslování a aktualizaci vizuálních dat.

### <a name="aggregate-expressions"></a>Agregační výrazy

Rozhraní API klientské knihovny Time Series Insights používají agregační výrazy:

* Agregační výraz poskytuje možnost vytvořit jeden nebo více *hledaných výrazů*.

* Rozhraní API klienta jsou navržená tak, aby poskytovala podobnou funkci jiné ukázkové aplikaci ( [Time Series Insights Exploreru](https://insights.timeseries.azure.com/demo)), která používá `where` vyhledávací rozsah, predikáty, `splitBy` míry a hodnoty.

* Většina rozhraní API klientské knihovny převezme pole agregačních výrazů, které služba používá k vytvoření Time Series Insightsho dotazu na data.

### <a name="call-pattern"></a>Vzor volání

Sestavování a vykreslování ovládacích prvků grafu následuje po obecném vzoru. V rámci ukázkové aplikace můžete sledovat obecný vzor a může vám pomoct při použití klientské knihovny:

1. `array` Deklarovat pro uložení jednoho nebo více Time Series Insights agregačních výrazů:

   ```javascript
   var aes =  [];
   ```

1. Umožňuje sestavit *1* až *n* objektů agregačních výrazů. Pak je přidejte do pole agregační výraz:

   ```javascript
   var ae = new tsiClient.ux.aggregateExpression(predicateObject, measureObject, measureTypes, searchSpan, splitByObject, color, alias, contextMenuActions);
   aes.push(ae);
   ```

   **Parametry aggregateExpression**

   | Parametr | Popis | Příklad |
   | --------- | ----------- | ------- |
   | `predicateObject` | Výraz filtrování dat |`{predicateString: "Factory = 'Factory3'"}` |
   | `measureObject`   | Název vlastnosti použité míry | `{property: 'Temperature', type: "Double"}` |
   | `measureTypes`    | Agregace požadované vlastnosti míry | `['avg', 'min']` |
   | `searchSpan`      | Velikost trvání a interval agregačního výrazu | `{from: startDate, to: endDate, bucketSize: '2m'}` |
   | `splitByObject`   | Řetězcová vlastnost, podle které chcete rozdělit (volitelné: může mít hodnotu null) | `{property: 'Station', type: 'String'}` |
   | `color`         | Barva objektů, které chcete vykreslit | `'pink'` |
   | `alias`           | Popisný název agregačního výrazu | `'Factory3Temperature'` |
   | `contextMenuActions` | Pole akcí, které se mají svázat s objekty časové řady ve vizualizaci (volitelné) | Další informace najdete v tématu místní [nabídky](#pop-up-context-menus). |

1. Zavolejte Time Series Insights dotaz pomocí rozhraní API TsiClient. Server pro vyžádání agregovaných dat:

   ```javascript
   tsiClient.server.getAggregates(token, envFQDN, aeTsxArray);
   ```

   **Parametry getAggregates**

   | Parametr | Popis | Příklad |
   | --------- | ----------- | ------- |
   | `token`     | Přístupový token pro rozhraní Time Series Insights API |  `authContext.getTsiToken()`<br />Další informace najdete v tématu [Ověřování](#authentication). |
   | `envFQDN`   | Plně kvalifikovaný název domény (FQDN) pro prostředí Time Series Insights | Z Azure Portal. Například: `10000000-0000-0000-0000-100000000108.env.timeseries.azure.com`. |
   | `aeTsxArray` | Pole Time Series Insights výrazů dotazů | Použijte proměnnou `aes` , jak je popsáno výše `aes.map(function(ae){return ae.toTsx()}`:. |

1. Transformujte komprimovaný výsledek, který je vrácen z Time Series Insights dotaz do formátu JSON pro vizualizaci:

   ```javascript
   var transformedResult = tsiClient.ux.transformAggregatesForVisualization(result, aes);
   ```

1. Vytvořte ovládací prvek grafu pomocí rozhraní API TsiClient. UX. Vytvořte jeho vazby k jednomu z `<div>` prvků na stránce:

   ```javascript
   var barChart = new tsiClient.ux.BarChart(document.getElementById('chart3'));
   ```

1. Naplňte ovládací prvek grafu transformovanými datovými objekty JSON a vykreslete ovládací prvek na stránce:

   ```javascript
   barChart.render(transformedResult, {grid: true, legend: 'compact', theme: 'light'}, aes);
   ```

## <a name="render-controls"></a>Ovládací prvky vykreslení

Klientská knihovna Time Series Insights poskytuje osm jedinečných a předem připravených analytických ovládacích prvků:

* **Spojnicový graf**
* **výsečový graf**
* **pruhový graf**
* **heatmap**
* **ovládací prvky hierarchie**
* **dostupná mřížka**
* **samostatné časové osy událostí**
* **časové osy přechodu do stavu**

### <a name="line-chart-bar-chart-and-pie-chart-examples"></a>Příklady spojnicového grafu, pruhového grafu a výsečového grafu

Podívejte se na ukázku kódu, který se používá k vykreslení některých standardních ovládacích prvků Chart. Poznamenejte si programovací model a vzory pro vytváření těchto ovládacích prvků. Konkrétně prověřte kód `// Example 3/4/5` HTML pod komentářem, který vykresluje ovládací prvky s hodnotami `id` `chart3`jazyka HTML `chart4`, a `chart5`.

Odvolání z kroku 3 [oddílu zdroj a struktura stránky](#page-source-and-structure) , které ovládací prvky grafu jsou uspořádány do řádků na stránce. Každý ovládací prvek grafu má popisný řádek s nadpisem. V tomto příkladu jsou tři grafy naplněny pod `Multiple Chart Types From the Same Data` prvkem nadpis `<div>` a jsou svázány se třemi `<div>` prvky, které jsou pod názvem:

[!code-html[code-sample1-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=59-73&highlight=1,5,9,13)]

Následující část kódu jazyka JavaScript používá vzory, které byly vydány dříve: sestavení Time Series Insights agregační výrazy, jejich použití pro dotazování na Time Series Insights data a pak vykreslení tří grafů. Tři typy grafů se používají z oboru názvů tsiClient. ux: `LineChart`, `BarChart`a `PieChart`. Typy grafů slouží k vytvoření a vykreslení příslušných grafů. Všechny tři grafy mohou použít stejná data `transformedResult`agregačního výrazu:

[!code-javascript[code-sample2-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=241-262&highlight=13-14,16-17,19-20)]

Po vykreslení budou tři grafy vypadat následovně:

[![Několik typů grafů s použitím stejných dat](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png)](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png#lightbox)

## <a name="learn-about-advanced-features"></a>Další informace o rozšířených funkcích

Klientská knihovna Time Series Insights obsahuje několik dalších funkcí, které můžete použít k tvůrčímu nasazení vizualizací dat.

### <a name="states-and-events"></a>Stavy a události

Pokročilá funkce je možnost přidávat přechody stavu a diskrétní události do grafů. Tato funkce je užitečná pro zvýraznění incidentů, upozorňování a vytváření přepínačů stavu (například zapnutých a vypnutých přepínačů).

Podívejte se na kód, který `// Example 10` komentář obklopuje. Kód vykresluje ovládací prvek čára pod nadpisem `Line Charts with Multiple Series Types` a váže jej `<div>` k elementu s hodnotou `chart10`HTML `id` .

Tento postup popisují následující kroky:

1. Struktura s názvem `events4` je definována tak, aby obsahovala prvky změny stavu, které mají být sledovány. Struktura obsahuje:

   * Řetězcový klíč `Component States`.
   * Pole objektů hodnot představujících stavy. Každý objekt obsahuje:
     * Řetězcový klíč obsahující javascriptové časové razítko ve formátu ISO
     * Pole, které obsahuje charakteristiky stavu: barvu a popis

1. Struktura je definována pro `Incidents`, který obsahuje pole prvků události, které mají být sledovány. `events5` Struktura pole má stejný tvar jako výše uvedená struktura pole `events4`.

1. Spojnicový graf se vykreslí a předá do dvou struktur pomocí parametrů možností grafu: `events:` a `states:`. Všimněte si dalších parametrů možností pro zadání `tooltip:`, `theme:`nebo `grid:`.

[!code-javascript[code-sample-states-events](~/samples-javascript/pages/tutorial/index.html?range=337-389&highlight=5,26,51)]

Kosočtvercové značky a automaticky otevíraná okna, které se používají k označení incidentů a barevných pruhů a překryvných oken v časovém měřítku indikující změny stavu:

[![Spojnicové grafy s více typy řad](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png)](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png#lightbox)

### <a name="pop-up-context-menus"></a>Automaticky otevírané místní nabídky

Další pokročilou funkcí je možnost vytvářet vlastní kontextové nabídky (místní nabídky klikněte pravým tlačítkem myši). Vlastní místní nabídky jsou užitečné pro umožnění akcí a logických dalších kroků v rámci aplikace.

Podívejte se na kód kolem `// Example 13/14/15` komentáře. Tento kód zpočátku vykreslí spojnicový graf pod nadpisem `Line Chart with Context Menu to Create Pie/Bar Chart`. Graf je svázán `<div>` s prvkem s hodnotou `chart13`HTML `id` .

Spojnicový graf prostřednictvím místních nabídek umožňuje dynamicky vytvořit výsečový a pruhový graf s vazbou na elementy `<div>` s ID `chart14` a `chart15`. Výsečové graf i pruhový graf také používají kontextové nabídky k povolení jejich vlastních funkcí: možnost Kopírovat data z výsečového grafu do pruhového grafu a vytisknout data pruhového grafu do okna konzoly prohlížeče v uvedeném pořadí.

Tento postup popisují následující kroky:

1. Je definována řada vlastních akcí. Každá akce obsahuje pole s jedním nebo více elementy. Každý element definuje jednu položku místní nabídky:

   * `barChartActions`: Tato akce definuje kontextovou nabídku výsečového grafu, který obsahuje jeden prvek pro definování jedné položky:
     * `name`: Text, který se používá pro položku nabídky: "Tisk parametrů do konzoly"
     * `action`: Akce, která je přidružena k položce nabídky. Akce je vždy anonymní funkce, která přebírá tři argumenty založené na agregačním výrazu použitém k vytvoření grafu. V tomto případě se argumenty zapisují do okna konzoly v prohlížeči:
       * `ae`: Pole agregačního výrazu
       * `splitBy`: `splitBy` Hodnota.
       * `timestamp`: Časové razítko.

   * `pieChartActions`: Tato akce definuje kontextovou nabídku pro pruhový graf, který obsahuje jeden prvek pro definování jedné položky. Tvar a schéma jsou stejné jako u `barChartActions` elementu popsaného výše, `action` ale funkce je výrazně odlišná: vytváří a vykresluje pruhový graf. `ae` Argument slouží k určení pole agregačního výrazu, které je předáno za běhu při otevření položky nabídky. Funkce také nastaví vlastnost `ae.contextMenu` s místní nabídkou `barChartActions`.
   * `contextMenuActions`: Tato akce definuje kontextovou nabídku pro spojnicový graf, která obsahuje tři prvky pro definování tří položek nabídky. Tvar a schéma pro každý prvek jsou stejné jako prvky, které byly popsány dříve. První položka stejně jako v případě elementu `barChartActions` zapíše tři argumenty funkce do okna konzoly v prohlížeči. Podobně jako u prvku druhá dvě položky vytvoří instanci a vykreslí výsečový graf a pruhový graf. `pieChartActions` Další dvě položky také nastaví ve svých vlastnostech `ae.contextMenu` místní nabídky `pieChartActions` a `barChartActions` (v uvedeném pořadí).

1. Dva agregační výrazy jsou vloženy `aes` do pole agregačního výrazu. Určují `contextMenuActions` pole pro každou položku. Tyto výrazy se používají v ovládacím prvku spojnicového grafu.

1. Zpočátku se vykreslí jenom spojnicový graf, ze kterého se dá vykreslit výsečový graf i pruhový graf za běhu.

[!code-javascript[code-sample-context-menus](~/samples-javascript/pages/tutorial/index.html?range=461-540&highlight=7,16,29,61-64,78)]

Na následujícím snímku obrazovky se zobrazí grafy odpovídající místní nabídce. Výsečový graf a pruhový graf se vytvořily dynamicky pomocí možností kontextové nabídky spojnicový graf.

[![Spojnicový graf s místní nabídkou pro vytvoření výsečového grafu a pruhového grafu](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png)](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png#lightbox)

### <a name="brushes"></a>Štětce

Pomocí štětců můžete obor časového rozsahu definovat akce, jako je přiblížení a prozkoumávání.

Kód, který se používá k ilustraci štětců, je zobrazen `Line Chart with Context Menu to Create Pie/Bar Chart` v příkladu, který popisuje místní nabídky.

* Akce štětců jsou podobné místní nabídce v tom, že pro štětec definují řadu vlastních akcí. Každá akce obsahuje pole, které má jeden nebo více prvků. Každý element definuje jednu položku místní nabídky:
   * `name`: Text, který se používá pro položku nabídky: "Tisk parametrů do konzoly"
   * `action`: Akce, která je přidružena k položce nabídky, která je vždy anonymní funkce, která přijímá dva argumenty. V tomto případě se argumenty zapisují do okna konzoly v prohlížeči:
     * `fromTime`: `from` Časové razítko výběru štětce.
     * `toTime`: `to` Časové razítko výběru štětce.

* Akce štětců se přidávají jako další vlastnost možnosti grafu. Vlastnost je předána `linechart.Render`volání. `brushContextMenuActions: brushActions`

[!code-javascript[code-sample-brushes](~/samples-javascript/pages/tutorial/index.html?range=526-540&highlight=1,13)]

[![Spojnicový graf s místní nabídkou pro vytvoření výsečového grafu a pruhového grafu pomocí štětců](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart-brushes.png)](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart-brushes.png#lightbox)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Teď, když jste dokončili kurz, vyčistěte prostředky, které jste vytvořili:

1. V nabídce vlevo v [Azure Portal](https://portal.azure.com)vyberte **všechny prostředky**a vyhledejte svoji Azure Time Series Insights skupinu prostředků.
1. Odstraňte celou skupinu prostředků (a všechny prostředky v ní obsažené) tak, že vyberete možnost **Odstranit** nebo odebrat jednotlivé prostředky zvlášť.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Přihlaste se a prozkoumejte ukázkovou aplikaci Time Series Insights a její zdroj.
> * Použití rozhraní API v klientské knihovně Time Series Insights JavaScript
> * Použití JavaScriptu k vytvoření a naplnění ovládacích prvků grafu pomocí Time Series Insights dat

Ukázková aplikace Time Series Insights používá ukázkovou datovou sadu. Další informace o tom, jak můžete vytvořit vlastní Time Series Insights prostředí a datovou sadu, najdete v následujícím článku:

> [!div class="nextstepaction"]
> [Kurz: Vytvoření prostředí Azure Time Series Insights](tutorial-create-populate-tsi-environment.md)

Nebo zobrazte zdrojové soubory ukázkové aplikace Time Series Insights:

> [!div class="nextstepaction"]
> [Time Series Insights ukázkové úložiště aplikace](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial)

Přečtěte si referenční dokumentaci k rozhraní API Time Series Insights klienta:

> [!div class="nextstepaction"]
> [Referenční dokumentace k rozhraní Time Series Insights API](https://github.com/microsoft/tsiclient/blob/master/docs/API.md)

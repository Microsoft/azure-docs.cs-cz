---
title: 'Kurz: Prozkoumejte Azure čas Series Insights Javascriptovou klientskou knihovnu | Dokumentace Microsoftu'
description: Seznamte se s javascriptovou klientskou knihovnou pro službu Azure Time Series Insights a se souvisejícím programovacím modelem.
author: ashannon7
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 04/23/2019
ms.author: anshan
ms.custom: seodec18
ms.openlocfilehash: a91afdbeaa2ced37b237b4f2b80a8dbbe2c4a05c
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64717218"
---
# <a name="tutorial-explore-the-azure-time-series-insights-javascript-client-library"></a>Kurz: Zkoumání javascriptové klientské knihovny pro službu Azure Time Series Insights

Abychom vývojářům webů pomohli dotazovat a vizualizovat data uložená ve službě Time Series Insights (TSI), vytvořili jsme pro ni javascriptovou klientskou knihovnu založenou na D3. Tento kurz vás provede TSI klientské knihovny a programovacího modelu s použitím prostředí ukázkovou aplikaci.

Tento kurz podrobně popisuje, jak pracovat s knihovnou, přístup k datům služby TSI a použití ovládacích prvků graf k vykreslení a vizualizovat data. Zobrazí se také dozvíte, jak experimentovat s různými druhy grafů k vizualizaci dat. Na závěr kurzu budete moct použít knihovnu klienta služby TSI funkce začlenit do vlastní webové aplikace.

Konkrétně se dozvíte o:

> [!div class="checklist"]
> * Ukázková aplikace TSI
> * Javascriptová klientská knihovna pro TSI
> * Použití knihovny v ukázkové aplikaci k vizualizaci dat TSI

> [!NOTE]
> * V tomto kurzu použijete bezplatnou, hostovaný [ukázkové webové služby Time Series Insights](https://insights.timeseries.azure.com/clientsample).
> * Time Series Insights ukázkové aplikace zdrojové soubory jsou k dispozici v [ukázkového úložiště Githubu](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial).

## <a name="video"></a>Video

### <a name="in-this-video-we-introduce-the-open-source-time-series-insights-javascript-sdkbr"></a>V tomto videu představíme open source sadu JavaScript SDK Time Series Insights.</br>

> [!VIDEO https://www.youtube.com/embed/X8sSm7Pl9aA]

## <a name="prerequisites"></a>Požadavky

Tento kurz využívá v prohlížeči **vývojářské nástroje** funkce. Moderní webové prohlížeče ([Microsoft Edge](/microsoft-edge/devtools-guide), [Chrome](https://developers.google.com/web/tools/chrome-devtools/), [FireFox](https://developer.mozilla.org/en-US/docs/Learn/Common_questions/What_are_browser_developer_tools), [Safari](https://developer.apple.com/safari/tools/)a další) obvykle poskytují přístup k **Webové zobrazení inspektoru** prostřednictvím `F12` klávesové zkratky. V opačném případě je přístupný kliknutím pravým tlačítkem na webovou stránku a výběr **zkontrolovat Element**.

## <a name="time-series-insights-sample-application"></a>Ukázková aplikace Time Series Insights

V celém tomto kurzu se používá bezplatná, hostovaný Time Series Insights ukázkovou aplikaci prozkoumat zdrojového kódu aplikace a služby TSI Javascriptovou klientskou knihovnu. Prostřednictvím, se dozvíte, jak pracovat s TSI v jazyce JavaScript a vizualizovat data pomocí diagramů a grafů.

1. Přejděte do [ukázkové aplikace Time Series Insights](https://insights.timeseries.azure.com/clientsample). Zobrazí se následující znak na řádku:

   [![Klient služby TSI ukázka přihlášení řádku](media/tutorial-explore-js-client-lib/tcs-sign-in.png)](media/tutorial-explore-js-client-lib/tcs-sign-in.png#lightbox)

1. Vyberte **přihlášení** zadejte nebo vyberte svoje přihlašovací údaje. Použijte účet organizace podnikové sítě (Azure Active Directory) nebo osobní účet (Account Microsoft nebo MSA).

   [![Přihlašovací údaje pro klienta služby TSI ukázka výzvu](media/tutorial-explore-js-client-lib/tcs-sign-in-enter-account.png)](media/tutorial-explore-js-client-lib/tcs-sign-in-enter-account.png#lightbox)

1. Po přihlášení, uvidíte na stránce s několika druhů grafů naplněný daty TSI. V pravém horním rohu se zobrazí váš uživatelský účet a možnost **Log out** (Odhlásit se):

   [![Hlavní stránka ukázkové TSI klienta po přihlášení](media/tutorial-explore-js-client-lib/tcs-main-after-signin.png)](media/tutorial-explore-js-client-lib/tcs-main-after-signin.png#lightbox)

### <a name="page-source-and-structure"></a>Zdrojový kód a struktura stránky

<div id="page-source-and-structure"></div>

První, Podívejme se [HTML a JavaScript zdrojový kód](https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/index.html) vykreslené webu stránkovaného fondu:

1. Otevřete v prohlížeči **Vývojářské nástroje**. Prozkoumejte elementy HTML, ze kterých se skládá aktuální stránka (označují se také jako strom HTML nebo DOM).

1. Rozbalte `<head>` a `<body>` prvků a podívejte se v následujících částech:

   * V části `<head>` elementu, najdete stránce meta-data a závislosti, které umožňují spouštění aplikace:
     * Element `<script>`, který se používá k odkazování na soubor **adal.min.js** knihovny Azure Active Directory Authentication Library (označované také jako ADAL). ADAL je javascriptová knihovna, která umožňuje ověřování OAuth 2.0 (přihlášení) a získávání tokenů pro přístup k rozhraním API.
     * Několik elementů `<link>` pro šablony stylů (CSS), například **sampleStyles.css** a **tsiclient.css**. Šablony stylů se používají k ovládání detailů vizuálního stylu stránky, například barev, písem, mezer a tak dále.
     * Element `<script>`, který slouží k odkazování na javascriptovou klientskou knihovnu TSI: **tsiclient.js**. Stránka knihovnu využívá k volání rozhraní API služby TSI a vykreslování ovládacích prvků grafů na stránce.

     >[!NOTE]
     > * Zdrojový kód javascriptové knihovny ADAL je k dispozici v [úložišti azure-activedirectory-library-for-js](https://github.com/AzureAD/azure-activedirectory-library-for-js).
     > * Zdrojový kód javascriptové klientské knihovny pro TSI je k dispozici v [úložišti tsiclient](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial).

   * V části `<body>` elementu, najdete tu `<div>` prvky, které umožňují definovat rozložení položek na stránce a jiné `<script>` element:
     * První element `<div>` specifikuje přihlašovací dialogové okno **Log in** (`id="loginModal"`).
     * Druhý element `<div>` slouží jako nadřazený prvek pro:
       * Element `<div>` hlavičky, který se používá pro stavové zprávy a informace o přihlášení v horní části stránky (`class="header"`).
       * Element `<div>` pro zbytek elementů obsahu stránky, včetně všech grafů (`class="chartsWrapper"`).
       * Část `<script>`, která obsahuje veškerý JavaScript sloužící k ovládání stránky.

   [![Ukázka klienta TSI s Vývojářskými nástroji](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png#lightbox)

1. Rozbalte `<div class="chartsWrapper">` elementu a zjistíte další podřízené `<div>` elementy. Tyto prvky se používají k umístění jednotlivých ovládacích prvků ukázkových grafů. Všimněte si několika párů elementů `<div>`, jednoho pro každý příklad grafu:

   * První element (`class="rowOfCardsTitle"`) obsahuje popisný název, který shrnuje, co graf znázorňuje. Příklad: `Static Line Charts With Full-Size Legends.`
   * Druhý element (`class="rowOfCards"`) je nadřazený prvek obsahující další podřízené elementy `<div>`, které určují pozici vlastních ovládacích prvků grafů na řádku.

   [![Prvky div těla](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-divs.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-divs.png#lightbox)

1. Teď rozbalte element `<script type="text/javascript">` přímo pod elementem `<div class="chartsWrapper">`. Všimněte si, že na začátek části JavaScript úrovni stránky, která se používá ke zpracování veškerou logiku stránky (ověřování, volání rozhraní API, vykreslení ovládacích prvků grafu a další služby TSI):

   [![Text skriptu](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-script.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-script.png#lightbox)

## <a name="tsi-javascript-client-library-concepts"></a>Koncepty javascriptové klientské knihovna pro TSI

TSI klientské knihovny (**tsclient.js**) poskytuje abstrakci pro dvě důležité funkce jazyka JavaScript:

* **Metody obálky volání API pro dotazy služby TSI**: Rozhraní REST API, která umožňuje zadat dotaz pro TSI data pomocí agregační výrazy. Tyto metody jsou uspořádané v rámci oboru názvů `TsiClient.Server` knihovny.

* **Metody vytvoření a naplnění několik typů grafů ovládací prvky**: Metody, které jsou používané k vykreslování TSI agregovaná data na webové stránce. Tyto metody jsou uspořádané v rámci oboru názvů `TsiClient.UX` knihovny.

Díky těmto zjednodušení můžou vývojáři vytvářet komponenty uživatelského rozhraní pro graf a graf, které se využívají TSI data snadněji.

### <a name="authentication"></a>Authentication

[Time Series Insights ukázkovou aplikaci](https://insights.timeseries.azure.com/clientsample) je jednostránkové aplikace s podporou ověřování uživatelů ADAL OAuth 2.0:

1. Při použití knihovny ADAL pro ověřování, klientská aplikace musí být zaregistrovaný ve službě Azure Active Directory. Ve skutečnosti je jednostránkové aplikace zaregistrované k používání [OAuth 2.0 implicitní tok poskytování](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-implicit-grant-flow).
1. V důsledku toho aplikace musíte zadat některé vlastnosti registrace za běhu. Patří mezi ně identifikátor GUID klienta (`clientId`) a identifikátor URI pro přesměrování (`postLogoutRedirectUri`).
1. Později, že aplikace vyžaduje **přístupový token** ze služby Azure Active Directory. Přístupový token je vydaný pro omezené sadu oprávnění pro konkrétní službu nebo rozhraní API identifikátor (`https://api.timeseries.azure.com`). Oprávnění tokenu se vystavují jménem přihlášeného uživatele. Identifikátor pro službu nebo rozhraní API je další vlastnost, která je součástí registrace aplikace Azure Active Directory.
1. Po ADAL vrátí do aplikace přístupový token, je jí předán jako **nosný token** při přístupu k TSI služeb rozhraní API.

   [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=147-204&highlight=3-7,34-37)]

### <a name="control-identification"></a>Identifikace ovládacího prvku

V tomto příkladu zadaná `<div>` prvky jsou uspořádány v nadřazeném prvku `<body>` element poskytují rozumné rozložení pro všechny ovládací prvky grafu vykreslen na stránce.

Každý `<div>` prvek určuje vlastnosti umístění a vizuální atributy prvků grafu. HTML element `id` vlastnosti slouží jako jedinečné identifikátory pro vazbu k určité ovládací prvky pro vykreslování a aktualizaci zobrazit vizualizovaná data.

### <a name="aggregate-expressions"></a>Agregační výrazy

TSI klientské knihovny rozhraní API pomocí agregační výrazy:

* Agregační výraz poskytuje možnost vytvořit jeden nebo více **hledané termíny**.

* Rozhraní API klienta je navrženo pro zajištění podobné funkce jako jiný zadejte ukázkovou aplikaci ( [Průzkumníka služby Time Series Insights](https://insights.timeseries.azure.com/demo)), který používá rozsah hledání, predikát where, míry a rozdělit podle hodnoty.

* Většina rozhraní API klientské knihovny vezměte si pole agregačních výrazů, které používají službu k vytvoření dotazu TSI data.

### <a name="call-pattern"></a>Vzor volání

Sestavování a vykreslení ovládacích prvků grafu Následuje obecný vzor. Tento obecný vzor může být dodržen v celé ukázkové aplikace a pomůže vám při použití klientské knihovny:

1. Deklarujte pole `array`, které bude obsahovat jeden nebo několik agregačních výrazů TSI:

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
   | `predicateObject` | Výraz filtru data. |`{predicateString: "Factory = 'Factory3'"}` |
   | `measureObject`   | Název vlastnosti použité míry | `{property: 'Temperature', type: "Double"}` |
   | `measureTypes`    | Požadované agregace vlastnosti míry | `['avg', 'min']` |
   | `searchSpan`      | Doba trvání a velikost intervalu agregačního výrazu. | `{from: startDate, to: endDate, bucketSize: '2m'}` |
   | `splitByObject`   | Řetězcová vlastnost, podle které chcete provést rozdělení (volitelné – může být null) | `{property: 'Station', type: 'String'}` |
   | `color`         | Barva objektů, které chcete vykreslit | `'pink'` |
   | `alias`           | Popisný název agregačního výrazu. | `'Factory3Temperature'` |
   | `contextMenuActions` | Pole akcí, které se navážou na objekty časové řady ve vizualizaci (volitelné). | Další informace najdete v části [rozbalovací kontextové nabídky](#contextMenu) |

1. Vyžádejte agregovaná data zavoláním dotazu na TSI pomocí rozhraní API `TsiClient.Server`.

   ```javascript
   tsiClient.server.getAggregates(token, envFQDN, aeTsxArray);
   ```

   **Parametry getAggregates**

   | Parametr | Popis | Příklad: |
   | --------- | ----------- | ------- |
   | `token`     | Přístupový token pro rozhraní API služby TSI |  `authContext.getTsiToken()` Další informace najdete v oddílu [Ověřování](#authentication). |
   | `envFQDN`   | Plně kvalifikovaný název domény pro prostředí TSI | Z webu Azure Portal, například: `10000000-0000-0000-0000-100000000108.env.timeseries.azure.com` |
   | `aeTsxArray` | Pole výrazů dotazu na TSI | Použijte proměnnou `aes`, jak je popsáno výše: `aes.map(function(ae){return ae.toTsx()}` |

1. Pro účely vizualizace transformujte komprimovaný výsledek vrácený z dotazu na TSI do formátu JSON:

   ```javascript
   var transformedResult = tsiClient.ux.transformAggregatesForVisualization(result, aes);
   ```

1. Pomocí rozhraní API `TsiClient.UX` vytvořte ovládací prvek grafu a vytvořte jeho vazbu na jeden z elementů `<div>` na stránce:

   ```javascript
   var barChart = new tsiClient.ux.BarChart(document.getElementById('chart3'));
   ```

1. Naplňte ovládací prvek grafu transformovanými datovými objekty JSON a vykreslete ho na stránce:

   ```javascript
   barChart.render(transformedResult, {grid: true, legend: 'compact', theme: 'light'}, aes);
   ```

## <a name="rendering-controls"></a>Vykreslování ovládacích prvků

TSI Klientská knihovna poskytuje osm analytics jedinečné, out-of-the-box, ovládací prvky:

* **Spojnicový graf**
* **Výsečový graf**
* **Pruhový graf**
* **heatmap**
* **ovládací prvky hierarchie**
* **přístupné mřížky**
* **diskrétní události časové osy**
* **Přechod stavu časové osy**

### <a name="line-bar-pie-chart-examples"></a>Příklady spojnicového, pruhového a výsečového grafu

Podívejte se na ukázku kódu použije k vykreslení některé standardní graf ovládacího prvku. Všimněte si, že programovací model a vzory pro vytváření těchto ovládacích prvků. Konkrétně zkontrolujte část HTML v části `// Example 3/4/5` komentář, který vykreslí ovládací prvky s HTML `id` hodnoty `chart3`, `chart4`, a `chart5`.

Pamatujete z **kroku 3** z [stránce části zdroje a struktura](#page-source-and-structure) , graf ovládací prvky jsou uspořádány do řádků na stránce, z nichž každá má řádek popisný název. V tomto příkladu tři grafy zaplnění pod nadpisem `"Multiple Chart Types From the Same Data"` `<div>` element a jsou vázána na tři `<div>` prvky, které jsou pod názvem:

[!code-html[code-sample1-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=59-73&highlight=1,5,9,13)]

V následující části kódu JavaScriptu se podle výše popsaného vzoru vytvoří agregační výrazy TSI, použijí se k odeslání dotazu na data TSI a vykreslí se tři grafy. Všimněte si použití tří typů z oboru názvů `tsiClient.ux` (`LineChart`, `BarChart` a `PieChart`) k vytvoření a vykreslení příslušných grafů. Všimněte si také, že všechny tři grafy můžou využívat stejná data agregačního výrazu `transformedResult`:

[!code-javascript[code-sample2-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=241-262&highlight=13-14,16-17,19-20)]

Po vykreslení budou tři grafy vypadat následovně:

[![Několik typů grafů s použitím stejných dat](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png)](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png#lightbox)

## <a name="advanced-features"></a>Pokročilé funkce

Knihovna klienta služby TSI má několik dalších funkcí, které můžete použít k implementaci vizualizace dat uzdu svým snům.

### <a name="states-and-events"></a>Stavy a události

Jeden pokročilé funkce je možnost přidání přechodů mezi stavy a diskrétní události do grafů. Tato funkce je užitečná pro zvýraznění incidenty, výstrahy a vytváření stavu přepínačů (zapnuto/vypnuto přepínače příkladu).

Podívejte se na kód kolem `// Example 10` komentář. Kód vykreslí ovládací prvek řádku pod nadpisem `"Line Charts with Multiple Series Types"`a připojí ho k `<div>` element s HTML `id` hodnota `chart10`.

1. Nejprve, s názvem struktury `events4` je definována pro změnu stavu prvky pro sledování. Struktura obsahuje:

   * Řetězcový klíč `Component States`.
   * Pole objektů hodnot představujících stavy. Každý objekt obsahuje:
     * Řetězcový klíč obsahující javascriptové časové razítko ve formátu ISO
     * Pole, které obsahuje charakteristiky stavu: barvu a popis

2. Dále `events5` struktura je definována pro `Incidents`, který obsahuje celou řadu událostí prvky pro sledování. Struktura pole má stejný tvar jako výše uvedená struktura pole `events4`.

3. Nakonec se vykreslí spojnicový graf a výše uvedené dvě struktury se předají v parametrech možností grafu: `events:` a `states:`. Všimněte si dalších parametrů možností `tooltip:`, `theme:` a `grid:`, které umožňují zadat popis, motiv nebo mřížku.

[!code-javascript[code-sample-states-events](~/samples-javascript/pages/tutorial/index.html?range=337-389&highlight=5,26,51)]

K vizuálnímu vyznačení incidentů se používají kosočtvercové značky a automaticky otevíraná okna. Barevné pruhy a automaticky otevíraná okna u časové osy označují změny stavu:

[![Spojnicové grafy s několika typy řad](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png)](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png#lightbox)

### <a name="pop-up-context-menus"></a>Automaticky otevírané místní nabídky

<div id="contextMenu"></div>

Další pokročilé funkce je možnost vytvářet vlastní místní nabídky (klikněte pravým tlačítkem na místní nabídky). Vlastní místní nabídky jsou užitečné pro umožnění akcí a logických dalších kroků v rámci aplikace.

Vyhledejte kód `// Example 13/14/15` komentář. Tento kód vykreslení spojnicový graf pod nadpisem `"Line Chart with Context Menu to Create Pie/Bar Chart"` a graf je vázán na `<div>` element s HTML `id` hodnota `chart13`.

Spojnicový graf prostřednictvím místních nabídek umožňuje dynamicky vytvořit výsečový a pruhový graf s vazbou na elementy `<div>` s ID `chart14` a `chart15`. Kromě toho výsečový i pruhový graf také používají vlastní místní nabídky, prostřednictvím kterých povolují vlastní funkce: možnost kopírovat data z výsečového do pruhového grafu a tisknout data pruhového grafu do okna konzoly v prohlížeči (v uvedeném pořadí).

1. Nejprve se definuje řada vlastních akcí. Každá akce obsahuje pole s jedním nebo více elementy. Každý element definuje jednu položku místní nabídky:

   * `barChartActions`: Tato akce definuje kontextovou nabídku pro výsečový graf, který obsahuje jeden prvek. Chcete-li definovat jednu položku:
     * `name`: Text, který se používá pro položku nabídky: "Print parametry do konzoly."
     * `action`: Akce přidružené položky nabídky. Akce je vždy anonymní funkce, která přebírá tři argumenty založené na agregačním výrazu použitém k vytvoření grafu. V tomto případě se argumenty zapisují do okna konzoly v prohlížeči:
       * `ae`: Agregační výraz pole.
       * `splitBy`: `splitBy` Hodnotu.
       * `timestamp`: Časové razítko.

   * `pieChartActions`: Tato akce definuje kontextovou nabídku pro pruhový graf, který obsahuje jeden prvek. Chcete-li definovat jednu položku. Tvar a schéma jsou stejné jako u předchozího elementu `barChartActions`, ale všimněte si výrazného rozdílu ve funkci `action`, která vytvoří instanci pruhového grafu a vykreslí ho. Také si všimněte, že se pomocí argumentu `ae` určuje pole agregačních výrazů, které se předává za běhu při otevření položky nabídky. Funkce také nastaví vlastnost `ae.contextMenu` s místní nabídkou `barChartActions`.
   * `contextMenuActions`: Tato akce definuje kontextovou nabídku pro spojnicový graf, který obsahuje tři prvky k definování tři položky nabídky. Tvar a schéma jednotlivých elementů jsou stejné jako u předchozích elementů. První položka stejně jako v případě elementu `barChartActions` zapíše tři argumenty funkce do okna konzoly v prohlížeči. Další dvě položky podobně jako v případě elementu `pieChartActions` vytvoří instance výsečového a pruhového grafu (v uvedeném pořadí) a vykreslí je. Další dvě položky také nastaví ve svých vlastnostech `ae.contextMenu` místní nabídky `pieChartActions` a `barChartActions` (v uvedeném pořadí).

2. Pak se do pole agregačních výrazů `aes` odešlou dva agregační výrazy, které pro obě položky určí pole `contextMenuActions`. Tyto výrazy se používají v ovládacím prvku spojnicového grafu.

3. Nakonec se vykreslí pouze počáteční spojnicový graf, na jehož základě je možné za běhu vykreslit výsečový i pruhový graf.

[!code-javascript[code-sample-context-menus](~/samples-javascript/pages/tutorial/index.html?range=461-540&highlight=7,16,29,61-64,78)]

Snímek obrazovky ukazuje grafy s odpovídajícími automaticky otevíranými místními nabídkami. Výsečový a pruhový graf se vytvořily dynamicky pomocí možností místní nabídky spojnicového grafu.

[![Spojnicový graf s místní nabídkou umožňující vytvoření výsečového nebo pruhového grafu](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png)](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png#lightbox)

### <a name="brushes"></a>Štětce

Pomocí štětců můžete omezit časový rozsah a definovat akce, jako jsou přiblížení a prozkoumání.

Kód, který se používá ke znázornění štětce se zobrazí v předchozím příkladu "Řádku grafu s místní nabídky k vytvořit výsečový/pruhový graf", která popisuje místní kontextové nabídky.

1. Akce štětců jsou podobné místní nabídce v tom, že pro štětec definují řadu vlastních akcí. Každá akce obsahuje pole s jedním nebo více elementy. Každý element definuje jednu položku místní nabídky:
   * `name`: Text, který se používá pro položku nabídky: "Print parametry do konzoly."
   * `action`: Akce, která je přidružená položka nabídky, který je vždycky anonymní funkce, která přebírá dva argumenty. V tomto případě se argumenty zapisují do okna konzoly v prohlížeči:
      * `fromTime`: `from` Časové razítko výběr stopy.
      * `toTime`: `to` Časové razítko výběr stopy.

2. Akce štětců se přidávají jako další vlastnost možnosti grafu. Všimněte si vlastnosti `brushContextMenuActions: brushActions` předávané do volání `linechart.Render`.

[!code-javascript[code-sample-brushes](~/samples-javascript/pages/tutorial/index.html?range=526-540&highlight=1,13)]

[![Spojnicový graf s místní nabídka pro vytvoření výsečovém nebo pruhovém grafu pomocí štětců](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart-brushes.png)](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart-brushes.png#lightbox)

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Přihlášení k ukázkové aplikaci TSI a prozkoumání aplikace a jejího zdrojového kódu
> * Použití rozhraní API v javascriptové klientské knihovně pro TSI
> * Použití JavaScriptu k vytvoření ovládacích prvků grafů a jejich naplnění daty TSI

Jak je vidět, TSI ukázková aplikace používá ukázkové datové sady. Další informace o tom, jak vytvořit vlastní prostředí TSI a sadu dat, najdete v následujícím článku:

> [!div class="nextstepaction"]
> [Kurz: Vytvoření prostředí Azure Time Series Insights](tutorial-create-populate-tsi-environment.md)

Nebo zobrazení souborů zdrojového TSI ukázkové aplikace:

> [!div class="nextstepaction"]
> [TSI úložiště ukázkové aplikace](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial)

---
title: Zkoumání javascriptové klientské knihovny pro službu Azure Time Series Insights
description: Seznamte se s javascriptovou klientskou knihovnou pro službu Azure Time Series Insights a se souvisejícím programovacím modelem.
author: ashannon7
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 06/05/2018
ms.author: anshan
ms.openlocfilehash: 445584ef8f9a2534d7f183a4452df19ee87d0025
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39626990"
---
# <a name="tutorial-explore-the-azure-time-series-insights-javascript-client-library"></a>Kurz: Zkoumání javascriptové klientské knihovny pro službu Azure Time Series Insights

Abychom vývojářům webů pomohli dotazovat a vizualizovat data uložená ve službě Time Series Insights (TSI), vytvořili jsme pro ni javascriptovou klientskou knihovnu založenou na D3.  Tento kurz vás na příkladu ukázkové webové aplikace provede zkoumáním klientské knihovny pro TSI a souvisejícím programovacím modelem.

Témata v tomto kurzu vám umožní experimentovat s knihovnou, pochopit, jak funguje přístup k datům TSI, a vykreslovat a vizualizovat data pomocí ovládacích prvků grafů. Cílem je poskytnout vám dostatek podrobných informací, abyste mohli knihovnu využít ve vlastní webové aplikaci.

V tomto kurzu získáte informace o těchto tématech:

> [!div class="checklist"]
> * Ukázková aplikace TSI
> * Javascriptová klientská knihovna pro TSI
> * Použití knihovny v ukázkové aplikaci k vizualizaci dat TSI

## <a name="prerequisites"></a>Požadavky

V tomto kurzu se používá funkce Vývojářské nástroje (označovaná také jako DevTools nebo F12), kterou obsahuje většina moderních webových prohlížečů, jako jsou [Edge](/microsoft-edge/devtools-guide), [Chrome](https://developers.google.com/web/tools/chrome-devtools/), [FireFox](https://developer.mozilla.org/en-US/docs/Learn/Common_questions/What_are_browser_developer_tools), [Safari](https://developer.apple.com/safari/tools/) a další. Pokud tuto funkci ještě neznáte, bylo by vhodné, abyste ji prozkoumali ve svém prohlížeči, než budete pokračovat.

## <a name="time-series-insights-sample-application"></a>Ukázková aplikace Time Series Insights

V tomto kurzu se k prozkoumání zdrojového kódu aplikace, včetně použití javascriptové klientské knihovny pro TSI, používá ukázková aplikace Time Series Insights. Ukázka je jednostránková webová aplikace (SPA), která předvádí použití knihovny. Dozvíte se, jak dotazovat a vizualizovat data z ukázkového prostředí TSI.

1. Přejděte do [ukázkové aplikace Time Series Insights](https://insights.timeseries.azure.com/clientsample). Zobrazí se vám stránka podobná následujícímu obrázku s výzvou k přihlášení:

   ![Výzva k přihlášení k ukázkovému klientovi TSI](media/tutorial-explore-js-client-lib/tcs-sign-in.png)

2. Vyberte **Log in** (Přihlásit se) a zadejte nebo vyberte své přihlašovací údaje. Můžete použít účet podniku nebo organizace (Azure Active Directory) nebo osobní účet (účet Microsoft neboli MSA).

   ![Výzva k zadání přihlašovacích údajů v ukázce klienta TSI](media/tutorial-explore-js-client-lib/tcs-sign-in-enter-account.png)

3. Po úspěšném přihlášení se vám zobrazí stránka podobná následujícímu obrázku. Na této stránce se zobrazuje několik stylů ukázkových grafů naplněných daty ze služby TSI. V pravém horním rohu se zobrazí váš uživatelský účet a možnost **Log out** (Odhlásit se):

   ![Hlavní stránka ukázkového klienta TSI po přihlášení](media/tutorial-explore-js-client-lib/tcs-main-after-signin.png)

### <a name="page-source-and-structure"></a>Zdrojový kód a struktura stránky

Nejprve se podívejme na zdrojový kód v HTML a JavaScriptu, který zajišťuje fungování stránky vykreslené v prohlížeči. Nebudeme procházet všechny elementy, ale seznámíte se s hlavními částmi a získáte představu, jak stránka funguje:

1. Otevřete v prohlížeči **Vývojářské nástroje**. Prozkoumejte elementy HTML, ze kterých se skládá aktuální stránka (označují se také jako strom HTML nebo DOM).

2. Rozbalte elementy `<head>` a `<body>` a všimněte si následujících částí:

   - Pod elementem `<head>` najdete elementy, které získávají další soubory napomáhající fungování stránky:
     - Element `<script>`, který se používá k odkazování na soubor **adal.min.js** knihovny Azure Active Directory Authentication Library (označované také jako ADAL). ADAL je javascriptová knihovna, která umožňuje ověřování OAuth 2.0 (přihlášení) a získávání tokenů pro přístup k rozhraním API.
     - Několik elementů `<link>` pro šablony stylů (CSS), například **sampleStyles.css** a **tsiclient.css**. Šablony stylů se používají k ovládání detailů vizuálního stylu stránky, například barev, písem, mezer a tak dále.
     - Element `<script>`, který slouží k odkazování na javascriptovou klientskou knihovnu TSI: **tsiclient.js**. Stránka knihovnu využívá k volání rozhraní API služby TSI a vykreslování ovládacích prvků grafů na stránce.

     >[!NOTE]
     > Zdrojový kód javascriptové knihovny ADAL je k dispozici v [úložišti azure-activedirectory-library-for-js](https://github.com/AzureAD/azure-activedirectory-library-for-js).
     > Zdrojový kód javascriptové klientské knihovny pro TSI je k dispozici v [úložišti tsiclient](https://github.com/Microsoft/tsiclient).

   - Pod elementem `<body>` najdete elementy `<div>`, které fungují jako kontejnery definující rozložení položek na stránce, a další element `<script>`:
     - První element `<div>` specifikuje přihlašovací dialogové okno **Log in** (`id="loginModal"`).
     - Druhý element `<div>` slouží jako nadřazený prvek pro:
       - Element `<div>` hlavičky, který se používá pro stavové zprávy a informace o přihlášení v horní části stránky (`class="header"`).
       - Element `<div>` pro zbytek elementů obsahu stránky, včetně všech grafů (`class="chartsWrapper"`).
       - Část `<script>`, která obsahuje veškerý JavaScript sloužící k ovládání stránky.

   [![Ukázka klienta TSI s Vývojářskými nástroji](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png)](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-head-body.png#lightbox)

3. Když rozbalíte element `<div class="chartsWrapper">`, najdete další podřízené elementy `<div>`. Tyto prvky se používají k umístění jednotlivých ovládacích prvků ukázkových grafů. Všimněte si několika párů elementů `<div>`, jednoho pro každý příklad grafu:

   - První element (`class="rowOfCardsTitle"`) obsahuje popisný název, který shrnuje, co graf znázorňuje. Například Static Line Charts With Full-Size Legends (Statické spojnicové grafy s legendami v plné velikosti).
   - Druhý element (`class="rowOfCards"`) je nadřazený prvek obsahující další podřízené elementy `<div>`, které určují pozici vlastních ovládacích prvků grafů na řádku.

   ![Prvky div v oddílu body](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-divs.png)

4. Teď rozbalte element `<script type="text/javascript">` přímo pod elementem `<div class="chartsWrapper">`. Podívejte se na začátek části JavaScriptu na úrovni stránky, který slouží ke zpracování veškeré logiky stránky: ověřování, volání rozhraní API služby TSI, vykreslování ovládacích prvků grafů a další:

   ![Skript v oddílu body](media/tutorial-explore-js-client-lib/tcs-devtools-callouts-body-script.png)

## <a name="tsi-javascript-client-library-concepts"></a>Koncepty javascriptové klientské knihovna pro TSI

Klientskou knihovnu pro TSI (**tsclient.js**) nebudeme podrobně popisovat, ale v zásadě poskytuje abstrakci pro dvě důležité kategorie:

- **Obálkové metody pro volání dotazovacích rozhraní API pro TSI:** Rozhraní REST API umožňující dotazovat data TSI pomocí agregačních výrazů. Tyto metody jsou uspořádané v rámci oboru názvů `TsiClient.Server` knihovny.
- **Metody pro vytváření a naplňování různých typů ovládacích prvků grafů:** Metody používané k vykreslování agregovaných dat TSI na webové stránce. Tyto metody jsou uspořádané v rámci oboru názvů `TsiClient.UX` knihovny.

Následující koncepty jsou univerzální a vztahují se obecně na všechna rozhraní API klientské knihovny pro TSI.

### <a name="authentication"></a>Authentication

Jak jsme zmínili dříve, tato ukázka je jednostránková aplikace, která k ověřování uživatelů využívá podporu OAuth 2.0 v knihovně ADAL. Tady je několik zajímavých bodů v této části skriptu:

1. Při použití knihovny ADAL k ověřování se klientská aplikace musí zaregistrovat do registru aplikací Azure Active Directory (Azure AD). Jako jednostránková aplikace se tato aplikace zaregistruje k používání implicitního toku udělování autorizace OAuth 2.0. Aplikace odpovídajícím způsobem za běhu určuje některé vlastnosti registrace, které se zapojí do toku, například GUID ID klienta (`clientId`) a identifikátor URI přesměrování (`postLogoutRedirectUri`).

2. Později si aplikace vyžádá přístupový token ze služby Azure AD. Přístupový token se vystavuje pro omezenou sadu oprávnění a pro konkrétní identifikátor služby nebo rozhraní API (https://api.timeseries.azure.com). Identifikátor služby nebo rozhraní API se také označuje jako „cílová skupina“ tokenu. Oprávnění tokenu se vystavují jménem přihlášeného uživatele. Identifikátor služby nebo rozhraní API je další vlastností obsaženou v registraci aplikace ve službě Azure AD. Jakmile knihovna ADAL vrátí do aplikace přístupový token, předává se pak při přístupu k rozhraním API služby TSI jako nosný token.

   [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=145-204&highlight=4-9,36-39)]

### <a name="control-identification"></a>Identifikace ovládacího prvku

Jak jsme si probrali výše, elementy `<div>` v oddílu `<body>` zajišťují rozložení všech ovládacích prvků grafů předvedených na stránce. Každý element `<div>` specifikuje vlastnosti pro umístění a vizuální atributy ovládacího prvku grafu, včetně vlastnosti `id`. Vlastnost `id` obsahuje jedinečný identifikátor, který se v kódu JavaScriptu používá k identifikaci a vazbě jednotlivých ovládacích prvků pro účely vykreslování a aktualizace.

### <a name="aggregate-expressions"></a>Agregační výrazy

Rozhraní API klientské knihovny pro TSI ve velké míře využívají agregační výrazy. Agregační výraz poskytuje možnost vytvořit jeden nebo několik hledaných termínů. Rozhraní API jsou navržená podobným způsobem jako [průzkumník služby Time Series Insights](https://insights.timeseries.azure.com/demo), který používá rozsah hledání, predikát where, míry a hodnotu split-by (rozdělit podle). Většina rozhraní API knihovny přebírá pole agregačních výrazů, pomocí kterých služba sestaví dotaz na data TSI.

### <a name="call-pattern"></a>Vzor volání

Naplňování a vykreslování ovládacích prvků grafů se řídí obecným vzorem. Tento vzor se používá na celé stránce v kódu JavaScriptu, který vytváří instance ovládacích prvků ukázkové aplikace TSI a načítá je:

1. Deklarujte pole `array`, které bude obsahovat jeden nebo několik agregačních výrazů TSI:

   ```javascript
   var aes =  [];
   ```

2. Vytvořte 1 až N objektů agregačních výrazů a přidejte je do pole agregačních výrazů.

   ```javascript
   var ae = new tsiClient.ux.aggregateExpression(predicateObject, measureObject, measureTypes, searchSpan, splitByObject, color, alias, contextMenuActions);
   aes.push(ae);
   ```

   **Parametry aggregateExpression**

   | Parametr | Popis | Příklad |
   | --------- | ----------- | ------- |
   | `predicateObject` | Výraz pro filtrování dat. |`{predicateString: "Factory = 'Factory3'"}` |
   | `measureObject`   | Název vlastnosti použité míry | `{property: 'Temperature', type: "Double"}` |
   | `measureTypes`    | Požadované agregace vlastnosti míry | `['avg', 'min']` |
   | `searchSpan`      | Doba trvání a velikost intervalu agregačního výrazu. | `{from: startDate, to: endDate, bucketSize: '2m'}` |
   | `splitByObject`   | Řetězcová vlastnost, podle které chcete provést rozdělení (volitelné – může být null) | `{property: 'Station', type: 'String'}` |
   | `color`         | Barva objektů, které chcete vykreslit | `'pink'` |
   | `alias`           | Popisný název agregačního výrazu. | `'Factory3Temperature'` |
   | `contextMenuActions` | Pole akcí, které se navážou na objekty časové řady ve vizualizaci (volitelné). | Další informace najdete v části [Automaticky otevírané místní nabídky v oddílu Pokročilé funkce](#popup-context-menus). |

3. Vyžádejte agregovaná data zavoláním dotazu na TSI pomocí rozhraní API `TsiClient.Server`.

   ```javascript
   tsiClient.server.getAggregates(token, envFQDN, aeTsxArray);
   ```

   **Parametry getAggregates**

   | Parametr | Popis | Příklad |
   | --------- | ----------- | ------- |
   | `token`     | Přístupový token pro rozhraní API služby TSI |  `authContext.getTsiToken()` Další informace najdete v oddílu [Ověřování](#authentication). |
   | `envFQDN`   | Plně kvalifikovaný název domény pro prostředí TSI | Z webu Azure Portal, například: `10000000-0000-0000-0000-100000000108.env.timeseries.azure.com` |
   | `aeTsxArray` | Pole výrazů dotazu na TSI | Použijte proměnnou `aes`, jak je popsáno výše: `aes.map(function(ae){return ae.toTsx()}` |

4. Pro účely vizualizace transformujte komprimovaný výsledek vrácený z dotazu na TSI do formátu JSON:

   ```javascript
   var transformedResult = tsiClient.ux.transformAggregatesForVisualization(result, aes);
   ```

5. Pomocí rozhraní API `TsiClient.UX` vytvořte ovládací prvek grafu a vytvořte jeho vazbu na jeden z elementů `<div>` na stránce:

   ```javascript
   var lineChart = new tsiClient.ux.BarChart(document.getElementById('chart3'));
   ```

6. Naplňte ovládací prvek grafu transformovanými datovými objekty JSON a vykreslete ho na stránce:

   ```javascript
   lineChart.render(transformedResult, {grid: true, legend: 'compact', theme: 'light'}, aes);
   ```

## <a name="rendering-controls"></a>Vykreslování ovládacích prvků

Klientská knihovna TSI v současné době poskytuje osm jedinečných ovládacích prvků analýzy: spojnicový graf, výsečový graf, pruhový graf, heat mapu, ovládací prvky hierarchie, přístupnou mřížku, časové osy diskrétních událostí a časové osy přechodu stavu.

### <a name="line-bar-pie-chart-examples"></a>Příklady spojnicového, pruhového a výsečového grafu

Podívejte se na kód několika standardních ovládacích prvků grafů ukázaných v aplikaci a na programovací modely a vzory použité k jejich vytvoření. Konkrétně prozkoumejte část HTML pod komentářem `// Example 3/4/5`, která vykresluje ovládací prvky s hodnotami ID `chart3`, `chart4` a `chart5`.

Jak je popsané v kroku 3 v části [Zdrojový kód a struktura stránky](#page-source-and-structure), ovládací prvky grafů jsou na stránce uspořádané do řádků, z nichž každý obsahuje řádek s popisným názvem. V tomto příkladu se všechny tři naplňované grafy nacházejí pod elementem `<div>` s nadpisem Multiple Chart Types From the Same Data (Několik typů grafů s použitím stejných dat) a jsou svázané se třemi elementy `<div>` pod ním:

[!code-javascript[code-sample1-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=59-73&highlight=1,5,9,13)]

V následující části kódu JavaScriptu se podle výše popsaného vzoru vytvoří agregační výrazy TSI, použijí se k odeslání dotazu na data TSI a vykreslí se tři grafy. Všimněte si použití tří typů z oboru názvů `tsiClient.ux` (`LineChart`, `BarChart` a `PieChart`) k vytvoření a vykreslení příslušných grafů. Všimněte si také, že všechny tři grafy můžou využívat stejná data agregačního výrazu `transformedResult`:

[!code-javascript[code-sample2-line-bar-pie](~/samples-javascript/pages/tutorial/index.html?range=241-262&highlight=13-14,16-17,19-20)]

Po vykreslení budou tři grafy vypadat následovně:

[![Několik typů grafů s použitím stejných dat](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png)](media/tutorial-explore-js-client-lib/tcs-multiple-chart-types-from-the-same-data.png#lightbox)

## <a name="advanced-features"></a>Pokročilé funkce

Klientská knihovna TSI poskytuje také několik volitelných pokročilých funkcí, jejichž výhody můžete chtít využít.

### <a name="states-and-events"></a>Stavy a události

Jedním z příkladů nabízených pokročilých funkcí je možnost přidat do grafů přechody stavu a diskrétní události. Tato funkce je vhodná k zvýrazňování incidentů, upozorňování a pro přepínače stavu, například zapnuto/vypnuto.

Prohlédněte si kód části HTML pod komentářem `// Example 10`. Kód pod nadpisem Line Charts with Multiple Series Types (Spojnicové grafy s několika typy řad) vykreslí spojnicový graf a vytvoří jeho vazbu na element `<div>` s hodnotou ID `chart10`.

1. Nejprve se definuje struktura `events4`, která bude uchovávat elementy změny stavu, které se mají sledovat. Struktura obsahuje:

   - Řetězcový klíč `Component States`.
   - Pole objektů hodnot představujících stavy. Každý objekt obsahuje:
     - Řetězcový klíč obsahující javascriptové časové razítko ve formátu ISO
     - Pole, které obsahuje charakteristiky stavu: barvu a popis

2. Pak se definuje struktura `events5` pro incidenty, která uchovává pole elementů událostí, které se mají sledovat. Struktura pole má stejný tvar jako výše uvedená struktura pole `events4`.

3. Nakonec se vykreslí spojnicový graf a výše uvedené dvě struktury se předají v parametrech možností grafu: `events:` a `states:`. Všimněte si dalších parametrů možností `tooltip:`, `theme:` a `grid:`, které umožňují zadat popis, motiv nebo mřížku.

[!code-javascript[code-sample-states-events](~/samples-javascript/pages/tutorial/index.html?range=337-389&highlight=5,26,51)]

K vizuálnímu vyznačení incidentů se používají kosočtvercové značky a automaticky otevíraná okna. Barevné pruhy a automaticky otevíraná okna u časové osy označují změny stavu:

[![Spojnicové grafy s několika typy řad](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png)](media/tutorial-explore-js-client-lib/tcs-line-charts-with-multiple-series-types.png#lightbox)

### <a name="pop-up-context-menus"></a>Automaticky otevírané místní nabídky

Dalším příkladem pokročilé funkce jsou vlastní kontextové nabídky (nabídky automaticky otevírané po kliknutí pravým tlačítkem myši). Vlastní místní nabídky jsou užitečné pro umožnění akcí a logických dalších kroků v rámci aplikace.

Prohlédněte si kód části HTML pod komentářem `// Example 13/14/15`. Tento kód nejprve pod nadpisem Line Chart with Context Menu to Create Pie/Bar Chart (Spojnicový graf s místní nabídkou pro vytvoření výsečového nebo pruhového grafu) vykreslí spojnicový graf a vytvoří jeho vazbu na element `<div>` s hodnotou ID `chart13`. Spojnicový graf prostřednictvím místních nabídek umožňuje dynamicky vytvořit výsečový a pruhový graf s vazbou na elementy `<div>` s ID `chart14` a `chart15`. Kromě toho výsečový i pruhový graf také používají vlastní místní nabídky, prostřednictvím kterých povolují vlastní funkce: možnost kopírovat data z výsečového do pruhového grafu a tisknout data pruhového grafu do okna konzoly v prohlížeči (v uvedeném pořadí).

1. Nejprve se definuje řada vlastních akcí. Každá akce obsahuje pole s jedním nebo více elementy. Každý element definuje jednu položku místní nabídky:

   - `barChartActions`: Tato akce definuje místní nabídku pro výsečový graf, která obsahuje jeden element definující jednu položku:
     - `name`: Text, který se použije pro položku nabídky: Print parameters to console (Vytisknout parametry do konzoly)
     - `action`: Akce přiřazená k položce nabídky. Akce je vždy anonymní funkce, která přebírá tři argumenty založené na agregačním výrazu použitém k vytvoření grafu. V tomto případě se argumenty zapisují do okna konzoly v prohlížeči:
       - `ae`: Pole agregačního výrazu
       - `splitBy`: Hodnota, podle které se provede rozdělení
       - `timestamp`: Časové razítko

   - `pieChartActions`: Tato akce definuje místní nabídku pro pruhový graf, která obsahuje jeden element definující jednu položku: Tvar a schéma jsou stejné jako u předchozího elementu `barChartActions`, ale všimněte si výrazného rozdílu ve funkci `action`, která vytvoří instanci pruhového grafu a vykreslí ho. Také si všimněte, že se pomocí argumentu `ae` určuje pole agregačních výrazů, které se předává za běhu při otevření položky nabídky. Funkce také nastaví vlastnost `ae.contextMenu` s místní nabídkou `barChartActions`.
   - `contextMenuActions`: Tato akce definuje místní nabídku pro spojnicový graf, která obsahuje tři elementy definující tři položky nabídky. Tvar a schéma jednotlivých elementů jsou stejné jako u předchozích elementů. První položka stejně jako v případě elementu `barChartActions` zapíše tři argumenty funkce do okna konzoly v prohlížeči. Další dvě položky podobně jako v případě elementu `pieChartActions` vytvoří instance výsečového a pruhového grafu (v uvedeném pořadí) a vykreslí je. Další dvě položky také nastaví ve svých vlastnostech `ae.contextMenu` místní nabídky `pieChartActions` a `barChartActions` (v uvedeném pořadí).

2. Pak se do pole agregačních výrazů `aes` odešlou dva agregační výrazy, které pro obě položky určí pole `contextMenuActions`. Tyto výrazy se používají v ovládacím prvku spojnicového grafu.

3. Nakonec se vykreslí pouze počáteční spojnicový graf, na jehož základě je možné za běhu vykreslit výsečový i pruhový graf.

[!code-javascript[code-sample-context-menus](~/samples-javascript/pages/tutorial/index.html?range=461-540&highlight=7,16,29,61-64,78)]

Snímek obrazovky ukazuje grafy s odpovídajícími automaticky otevíranými místními nabídkami. Výsečový a pruhový graf se vytvořily dynamicky pomocí možností místní nabídky spojnicového grafu.

[![Spojnicový graf s místní nabídkou umožňující vytvoření výsečového nebo pruhového grafu](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png)](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart.png#lightbox)

### <a name="brushes"></a>Štětce

Pomocí štětců můžete omezit časový rozsah a definovat akce, jako jsou přiblížení a prozkoumání.

Kód sloužící k ilustraci štětců je zobrazený také v předchozím příkladu spojnicového grafu s místní nabídkou pro vytvoření výsečového nebo pruhového grafu, který popisoval [automaticky otevírané místní nabídky](#popup-context-menus-section).

1. Akce štětců jsou podobné místní nabídce v tom, že pro štětec definují řadu vlastních akcí. Každá akce obsahuje pole s jedním nebo více elementy. Každý element definuje jednu položku místní nabídky:
   - `name`: Text, který se použije pro položku nabídky: Print parameters to console (Vytisknout parametry do konzoly)
   - `action`: Akce přidružená k položce nabídky, která je vždy anonymní funkcí, která přebírá dva argumenty. V tomto případě se argumenty zapisují do okna konzoly v prohlížeči:
      - `fromTime`: Časové razítko „od“ pro výběr štětcem
      - `toTime`: Časové razítko „do“ pro výběr štětcem

2. Akce štětců se přidávají jako další vlastnost možnosti grafu. Všimněte si vlastnosti `brushContextMenuActions: brushActions` předávané do volání `linechart.Render`.

[!code-javascript[code-sample-brushes](~/samples-javascript/pages/tutorial/index.html?range=526-540&highlight=1,13)]

![Spojnicový graf s místní nabídkou pro vytvoření výsečového nebo pruhového grafu a se štětci](media/tutorial-explore-js-client-lib/tcs-line-chart-with-context-menu-to-create-pie-bar-chart-brushes.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Přihlášení k ukázkové aplikaci TSI a prozkoumání aplikace a jejího zdrojového kódu
> * Použití rozhraní API v javascriptové klientské knihovně pro TSI
> * Použití JavaScriptu k vytvoření ovládacích prvků grafů a jejich naplnění daty TSI

Jak je popsáno výše, ukázková aplikace TSI využívá ukázkovou sadu dat. Další informace o tom, jak vytvořit vlastní prostředí TSI a sadu dat, najdete v následujícím článku:

> [!div class="nextstepaction"]
> [Kurz: Vytvoření prostředí Azure Time Series Insights](tutorial-create-populate-tsi-environment.md)



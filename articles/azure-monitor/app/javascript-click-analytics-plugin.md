---
title: Klikněte na Analytics modul plug-in pro automatické shromažďování pro Application Insights JavaScript SDK
description: Jak nainstalovat a používat modul plug-in automatických kolekcí pro analýzu pro Application Insights JavaScript SDK
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: lagayhar
ms.openlocfilehash: b2e9c267b0a3723c9ac7b3edd49e23b95741962f
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2021
ms.locfileid: "98660449"
---
# <a name="click-analytics-auto-collection-plugin-for-application-insights-javascript-sdk"></a>Klikněte na Analytics modul plug-in pro automatické shromažďování pro Application Insights JavaScript SDK

Tento modul plug-in automaticky sleduje události Click na webových stránkách a používá atributy data-* u elementů HTML k naplnění telemetrie událostí.

## <a name="getting-started"></a>Začínáme

Uživatelé můžou nastavit modul plug-in pro automatické shromažďování Click Analytics přes npm.

### <a name="npm-setup"></a>nastavení npm

Nainstalovat balíček npm:

```bash
npm install --save @microsoft/applicationinsights-clickanalytics-js @microsoft/applicationinsights-web
```

```js

import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { ClickAnalyticsPlugin } from '@microsoft/applicationinsights-clickanalytics-js';

const clickPluginInstance = new ClickAnalyticsPlugin();
// Click Analytics configuration
const clickPluginConfig = {
  autoCapture: true
};
// Application Insights Configuration
const configObj = {
  instrumentationKey: "YOUR INSTRUMENTATION KEY",
  extensions: [clickPluginInstance],
  extensionConfig: {
    [clickPluginInstance.identifier]: clickPluginConfig
  },
};

const appInsights = new ApplicationInsights({ config: configObj });
appInsights.loadAppInsights();
```

## <a name="how-to-effectively-use-the-plugin"></a>Jak efektivně používat modul plug-in

1. Data telemetrie generovaná z událostí Click se ukládají jako `customEvents` v části Application Insights Azure Portal.
2. `name`CustomEvent se naplní na základě následujících pravidel:
    1.  `id`Název zadaný v poli `data-*-id` bude použit jako název customEvent. Pokud například kliknuto v prvku HTML má atribut "data-Sample-ID" = "Button1", pak "Button1" bude customEvent název.
    2. Pokud žádný takový atribut neexistuje a pokud `useDefaultContentNameOrId` je v konfiguraci nastavená na `true` , pak se `id` jako název CUSTOMEVENT použije atribut HTML (nebo název obsahu elementu).
    3. Pokud `useDefaultContentNameOrId` má hodnotu false, bude název customEvent "not_specified".

    > [!TIP]
    > Naše doporučení je nastavená `useDefaultContentNameOrId` na hodnotu true pro generování smysluplných dat.  
3. `parentDataTag` provede dvě věci:
    1. Pokud je tato značka přítomna, modul plug-in načte `data-*` atributy a hodnoty ze všech nadřazených prvků HTML kliknutí na element.
    2. Za účelem zvýšení efektivity používá modul plug-in tuto značku jako příznak, pokud se zjistí, že se zastaví z dalšího zpracování modelu DOM (model DOM (Document Object Model)) nahoru.
    
    > [!CAUTION]
    > Po `parentDataTag` použití sady SDK začne hledat nadřazené značky napříč celou aplikací a ne jenom HTML element, ve kterém jste ji použili.
4. `customDataPrefix` uživatel, který poskytuje uživatel, by měl vždycky začínat `data-` například `data-sample-` . V jazyce HTML `data-*` globální atributy tvoří třídu atributů označované jako vlastní atributy dat, které umožňují výměnu speciálních informací mezi HTML a jeho reprezentací DOM pomocí skriptů. Starší prohlížeče (Internet Explorer, Safari) zahodí atributy, které nerozumí, pokud nezačínají na `data-` .

    `*`V `data-*` nástroji může být nahrazen libovolným názvem za [produkčním pravidlem názvů XML](https://www.w3.org/TR/REC-xml/#NT-Name) s těmito omezeními:
    - Název nesmí začínat řetězcem "XML", a to bez ohledu na to, jak se u těchto písmen používá.
    - Název nesmí obsahovat středník (U + 003A).
    - Název nesmí obsahovat velká písmena.

## <a name="configuration"></a>Konfigurace

| Název                  | Typ                               | Výchozí | Description                                                                                                                              |
| --------------------- | -----------------------------------| --------| ---------------------------------------------------------------------------------------------------------------------------------------- |
| autocapture           | boolean                            | true    | Automatická konfigurace zachycení.                                                                                                         |
| onCuePoint              | [IValueCallback](#ivaluecallback)  | null    | Konfigurace zpětných volání.                                                                                                                 |
| pageTags              | řetězec                             | null    | Značky stránky.                                                                                                                               |
| datatags              | [ICustomDataTags](#icustomdatatags)| null    | K dispozici jsou vlastní datové značky pro přepsání výchozích značek používaných k zachytávání kliknutí na data.                                                           |
| urlCollectHash        | boolean                            | false (nepravda)   | Povolí protokolování hodnot po znaku "#" v adrese URL.                                                                          |
| urlCollectQuery       | boolean                            | false (nepravda)   | Povolí protokolování řetězce dotazu adresy URL.                                                                                      |
| behaviorValidator     | Funkce                           | null  | Funkce zpětného volání, která se má použít pro `data-*-bhvr` ověření hodnoty Další informace najdete v [části behaviorValidator](#behaviorvalidator).|
| defaultRightClickBhvr | číslo řetězce (nebo)                 | ''      | Výchozí hodnota chování, když došlo k události pravého kliknutí Tato hodnota bude přepsána v případě, že element má `data-*-bhvr` atribut. |
| dropInvalidEvents     | boolean                            | false (nepravda)   | Příznak pro odkládací události, které nemají užitečné data kliknutí                                                                                   |

### <a name="ivaluecallback"></a>IValueCallback

| Název               | Typ     | Výchozí | Popis                                                                             |
| ------------------ | -------- | ------- | --------------------------------------------------------------------------------------- |
| pageName           | Funkce | null    | Funkce pro přepsání výchozího chování zachycení stránky.                           |
| pageActionPageTags | Funkce | null    | Funkce zpětného volání pro rozšíření výchozích pageTags shromážděných během události pageAction  |
| contentName        | Funkce | null    | Funkce zpětného volání, která naplní přizpůsobený obsah.                                 |

### <a name="icustomdatatags"></a>ICustomDataTags

| Název                      | Typ    | Výchozí   | Výchozí značka pro použití v HTML |   Description                                                                                |
|---------------------------|---------|-----------|-------------|----------------------------------------------------------------------------------------------|
| useDefaultContentNameOrId | boolean | false (nepravda)     | –         |Shromažďuje standardní atribut HTML pro obsah atributu Attribute, pokud konkrétní element není označený jako výchozí customDataPrefix nebo pokud uživatel neposkytuje customDataPrefix. |
| customDataPrefix          | řetězec  | `data-`   | `data-*`| Název automatického zachycení obsahu a hodnotu prvků, které jsou označeny poskytnutou předponou. Například `data-*-id` `data-<yourcustomattribute>` lze použít v HTML značek.   |
| aiBlobAttributeTag        | řetězec  | `ai-blob` |  `data-ai-blob`| Modul plug-in podporuje atribut BLOB JSON místo jednotlivých `data-*` atributů. |
| metaDataPrefix            | řetězec  | null      | –  | Automatické zachycení názvu a obsahu elementu meta HTML v případě zachycení s použitím poskytnuté předpony Například `custom-` lze použít v metaznačky značky HTML. |
| captureAllMetaDataContent | boolean | false (nepravda)     | –   | Automatické zachycení všech názvů a obsahu meta elementu HTML Head. Výchozí hodnota je false. Pokud je tato možnost povolená, přepíše zadané metaDataPrefix. |
| parentDataTag             | řetězec  | null      |  –  | Zastaví procházení modelu DOM při zachycení názvu obsahu a hodnoty prvků, pokud se tato značka objevila. Například `data-<yourparentDataTag>` lze použít ve značkách jazyka HTML.|
| dntDataTag                | řetězec  | `ai-dnt`  |  `data-ai-dnt`| Prvky HTML s tímto atributem bude modul plug-in ignorovat pro záznam dat telemetrie.|

### <a name="behaviorvalidator"></a>behaviorValidator

Funkce behaviorValidator automaticky kontroluje, zda je označení chování v kódu v souladu s předem definovaným seznamem. Tím se zajistí, že se v rámci zavedené taxonomie vaší firmy budou mít označená chování. Není nutné ani očekávat, že většina Azure Monitor zákazníci tuto službu použijí, ale jsou k dispozici pro pokročilé scénáře. V rámci tohoto rozšíření jsou k dispozici tři různé funkce zpětného volání behaviorValidator. Uživatelé ale můžou použít vlastní funkce zpětného volání, pokud vystavené funkce nevyřeší váš požadavek. Záměrem je využít vlastní strukturu dat chování, modul plug-in používá tuto funkci validátoru při extrakci chování z datových značek.

| Název                   | Description                                                                        |
| ---------------------- | -----------------------------------------------------------------------------------|
| BehaviorValueValidator | Tuto funkci zpětného volání použijte v případě, že struktura dat chování je pole řetězců.|
| BehaviorMapValidator   | Tuto funkci zpětného volání použijte v případě, že struktura dat chování je slovník.       |
| BehaviorEnumValidator  | Tuto funkci zpětného volání použijte v případě, že struktura dat chování je výčet.            |

#### <a name="sample-usage-with-behaviorvalidator"></a>Ukázka použití pomocí behaviorValidator

```js
var clickPlugin = Microsoft.ApplicationInsights.ClickAnalyticsPlugin;
var clickPluginInstance = new clickPlugin();

// Behavior enum values
var behaviorMap = {
  UNDEFINED: 0, // default, Undefined

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Page Experience [1-19]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  NAVIGATIONBACK: 1, // Advancing to the previous index position within a webpage
  NAVIGATION: 2, // Advancing to a specific index position within a webpage
  NAVIGATIONFORWARD: 3, // Advancing to the next index position within a webpage
  APPLY: 4, // Applying filter(s) or making selections
  REMOVE: 5, // Applying filter(s) or removing selections
  SORT: 6, // Sorting content
  EXPAND: 7, // Expanding content or content container
  REDUCE: 8, // Sorting content
  CONTEXTMENU: 9, // Context Menu
  TAB: 10, // Tab control
  COPY: 11, // Copy the contents of a page
  EXPERIMENTATION: 12, // Used to identify a third party experimentation event
  PRINT: 13, // User printed page
  SHOW: 14, //  Displaying an overlay
  HIDE: 15, //  Hiding an overlay
  MAXIMIZE: 16, //  Maximizing an overlay
  MINIMIZE: 17, // Minimizing an overlay
  BACKBUTTON: 18, //  Clicking the back button

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Scenario Process [20-39]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  STARTPROCESS: 20, // Initiate a web process unique to adopter
  PROCESSCHECKPOINT: 21, // Represents a checkpoint in a web process unique to adopter
  COMPLETEPROCESS: 22, // Page Actions that complete a web process unique to adopter
  SCENARIOCANCEL: 23, // Actions resulting from cancelling a process/scenario

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Download [40-59]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  DOWNLOADCOMMIT: 40, // Initiating an unmeasurable off-network download
  DOWNLOAD: 41, // Initiating a download

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Search [60-79]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  SEARCHAUTOCOMPLETE: 60, // Auto-completing a search query during user input
  SEARCH: 61, // Submitting a search query
  SEARCHINITIATE: 62, // Initiating a search query
  TEXTBOXINPUT: 63, // Typing or entering text in the text box

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Commerce [80-99]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  VIEWCART: 82, // Viewing the cart
  ADDWISHLIST: 83, // Adding a physical or digital good or services to a wishlist
  FINDSTORE: 84, // Finding a physical store
  CHECKOUT: 85, // Before you fill in credit card info
  REMOVEFROMCART: 86, // Remove an item from the cart
  PURCHASECOMPLETE: 87, // Used to track the pageView event that happens when the CongratsPage or Thank You page loads after a successful purchase
  VIEWCHECKOUTPAGE: 88, // View the checkout page
  VIEWCARTPAGE: 89, // View the cart page
  VIEWPDP: 90, // View a PDP
  UPDATEITEMQUANTITY: 91, // Update an item's quantity
  INTENTTOBUY: 92, // User has the intent to buy an item
  PUSHTOINSTALL: 93, // User has selected the push to install option

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Authentication [100-119]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  SIGNIN: 100, // User sign-in
  SIGNOUT: 101, // User sign-out

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Social [120-139]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  SOCIALSHARE: 120, // "Sharing" content for a specific social channel
  SOCIALLIKE: 121, // "Liking" content for a specific social channel
  SOCIALREPLY: 122, // "Replying" content for a specific social channel
  CALL: 123, // Click on a "call" link
  EMAIL: 124, // Click on an "email" link
  COMMUNITY: 125, // Click on a "community" link

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Feedback [140-159]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  VOTE: 140, // Rating content or voting for content
  SURVEYCHECKPOINT: 145, // reaching the survey page/form

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Registration, Contact [160-179]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  REGISTRATIONINITIATE: 161, // Initiating a registration process
  REGISTRATIONCOMPLETE: 162, // Completing a registration process
  CANCELSUBSCRIPTION: 163, // Canceling a subscription
  RENEWSUBSCRIPTION: 164, // Renewing a subscription
  CHANGESUBSCRIPTION: 165, // Changing a subscription
  REGISTRATIONCHECKPOINT: 166, // Reaching the registration page/form

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Chat [180-199]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  CHATINITIATE: 180, // Initiating a chat experience
  CHATEND: 181, // Ending a chat experience

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Trial [200-209]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  TRIALSIGNUP: 200, // Signing-up for a trial
  TRIALINITIATE: 201, // Initiating a trial

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Signup [210-219]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  SIGNUP: 210, // Signing-up for a notification or service
  FREESIGNUP: 211, // Signing-up for a free service

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Referals [220-229]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  PARTNERREFERRAL: 220, // Navigating to a partner's web property

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Intents [230-239]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  LEARNLOWFUNNEL: 230, // Engaging in learning behavior on a commerce page (ex. "Learn more click")
  LEARNHIGHFUNNEL: 231, // Engaging in learning behavior on a non-commerce page (ex. "Learn more click")
  SHOPPINGINTENT: 232, // Shopping behavior prior to landing on a commerce page

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Video [240-259]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  VIDEOSTART: 240, // Initiating a video
  VIDEOPAUSE: 241, // Pausing a video
  VIDEOCONTINUE: 242, // Pausing or resuming a video.
  VIDEOCHECKPOINT: 243, // Capturing predetermined video percentage complete.
  VIDEOJUMP: 244, // Jumping to a new video location.
  VIDEOCOMPLETE: 245, // Completing a video (or % proxy)
  VIDEOBUFFERING: 246, // Capturing a video buffer event
  VIDEOERROR: 247, // Capturing a video error
  VIDEOMUTE: 248, // Muting a video
  VIDEOUNMUTE: 249, // Unmuting a video
  VIDEOFULLSCREEN: 250, // Making a video full screen
  VIDEOUNFULLSCREEN: 251, // Making a video return from full screen to original size
  VIDEOREPLAY: 252, // Making a video replay
  VIDEOPLAYERLOAD: 253, // Loading the video player
  VIDEOPLAYERCLICK: 254, //  Click on a button within the interactive player
  VIDEOVOLUMECONTROL: 255, //  Click on video volume control
  VIDEOAUDIOTRACKCONTROL: 256, // Click on audio control within a video
  VIDEOCLOSEDCAPTIONCONTROL: 257, //  Click on the closed caption control
  VIDEOCLOSEDCAPTIONSTYLE: 258, //  Click to change closed caption style
  VIDEORESOLUTIONCONTROL: 259, //  Click to change resolution

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  //    Advertisement Engagement [280-299]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  ADBUFFERING: 283, // Ad is buffering
  ADERROR: 284, // Ad error
  ADSTART: 285, // Ad start
  ADCOMPLETE: 286, // Ad complete
  ADSKIP: 287, // Ad skipped
  ADTIMEOUT: 288, // Ad timed-out
  OTHER: 300 // Other
};

// Application Insights Configuration
var configObj = {
  instrumentationKey: "YOUR INSTRUMENTATION KEY",
  extensions: [clickPluginInstance],
  extensionConfig: {
    [clickPluginInstance.identifier]: {
      behaviorValidator: Microsoft.ApplicationInsights.BehaviorMapValidator(behaviorMap),
      defaultRightClickBhvr: 9
    },
  },
};
var appInsights = new Microsoft.ApplicationInsights.ApplicationInsights({
  config: configObj
});
appInsights.loadAppInsights();
```

## <a name="sample-app"></a>Ukázková aplikace

[Jednoduchá webová aplikace s povoleným modulem plug-in automatických kolekcí na základě analýz](https://go.microsoft.com/fwlink/?linkid=2152871)

## <a name="next-steps"></a>Další kroky

- Podívejte se na [úložiště GitHub](https://github.com/microsoft/ApplicationInsights-JS/tree/master/extensions/applicationinsights-clickanalytics-js) a [balíček npm](https://www.npmjs.com/package/@microsoft/applicationinsights-clickanalytics-js) pro modul plug-in automatických kolekcí pro analýzu.
- [Analýza událostí v prostředí používání](usage-segmentation.md) slouží k analýze horních kliknutí a řezů podle dostupných dimenzí.
- V poli customDimensions v tabulce CustomEvents v [Log Analytics](../log-query/log-analytics-tutorial.md#write-a-query)klikněte na data v oblasti obsah v části atribut. Další pokyny najdete v [ukázkové aplikaci](https://go.microsoft.com/fwlink/?linkid=2152871) .
- Sestavte [sešit](../platform/workbooks-overview.md) , abyste mohli vytvářet vlastní vizualizace kliknutí na data.

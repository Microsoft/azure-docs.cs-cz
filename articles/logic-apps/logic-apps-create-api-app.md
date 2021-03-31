---
title: Vytváření webových rozhraní API & rozhraní REST API pro Azure Logic Apps
description: Vytváření webových rozhraní API & rozhraní REST API pro volání rozhraní API, služeb nebo systémů pro integraci systému v Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 05/26/2017
ms.openlocfilehash: 3fe98160cc10eb3607b8309a9a263d63380dcfb5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "89073212"
---
# <a name="create-custom-apis-you-can-call-from-azure-logic-apps"></a>Vytváření vlastních rozhraní API, která můžete volat z Azure Logic Apps

I když Azure Logic Apps nabízí [stovky konektorů](../connectors/apis-list.md) , které můžete použít v pracovních postupech aplikace logiky, možná budete chtít volat rozhraní API, systémy a služby, které nejsou k dispozici jako konektory. Můžete vytvořit vlastní rozhraní API, která zajišťují akce a triggery, které se mají použít ve službě Logic Apps. Tady jsou další důvody, proč byste mohli chtít vytvořit vlastní rozhraní API, které můžete volat z pracovních postupů aplikací logiky:

* Rozšíříte své stávající pracovní postupy integrace systému a integrace dat.
* Zákazníci můžou pomocí vaší služby spravovat profesionální nebo osobní úkoly.
* Rozšiřte dostupnost, zjistitelnost a použití pro vaši službu.

V podstatě jsou konektory webová rozhraní API, která používají REST pro připojitelná rozhraní, [formát metadat Swagger](https://swagger.io/specification/) pro dokumentaci a formát JSON jako formát výměny dat. Vzhledem k tomu, že konektory jsou rozhraní REST API, která komunikují prostřednictvím koncových bodů HTTP, můžete pro vytváření konektorů použít libovolný jazyk, jako je .NET, Java, Python nebo Node.js. Můžete také hostovat rozhraní API na [Azure App Service](../app-service/overview.md), jako je nabídka typu platforma jako služba (PaaS), která poskytuje jeden z nejlepších, nejjednodušších a nejškálovatelných způsobů pro hostování rozhraní API. 

Aby mohla vlastní rozhraní API spolupracovat s Logic Apps, může vaše rozhraní API poskytovat [*Akce*](./logic-apps-overview.md#logic-app-concepts) , které provádějí konkrétní úkoly v pracovních postupech aplikace logiky. Vaše rozhraní API může fungovat taky jako [*Trigger*](./logic-apps-overview.md#logic-app-concepts) , který spouští pracovní postup aplikace logiky, když nová data nebo událost splňují zadanou podmínku. Toto téma popisuje běžné vzory, které můžete provést při vytváření akcí a triggerů v rozhraní API na základě chování, které má vaše rozhraní API poskytovat.

Své rozhraní API můžete hostovat na [Azure App Service](../app-service/overview.md), jako je nabídka typu platforma jako služba (PaaS), která poskytuje vysoce škálovatelné a jednoduché hostování rozhraní API.

> [!TIP] 
> I když můžete nasadit rozhraní API jako webové aplikace, zvažte nasazení rozhraní API jako aplikací API, které vám umožní usnadnit práci při sestavování, hostování a využívání rozhraní API v cloudu i místně. Nemusíte měnit žádný kód v rozhraních API – stačí kód nasadit do aplikace API. Přečtěte si například, jak vytvářet aplikace API vytvořené pomocí těchto jazyků: 
> 
> * [ASP.NET](../app-service/quickstart-dotnetcore.md). 
> * [Java](../app-service/quickstart-java.md)
> * [Node.js](../app-service/quickstart-nodejs.md)
> * [PHP](../app-service/quickstart-php.md)
> * [Python](../app-service/quickstart-python.md)
> * [Ruby](../app-service/quickstart-ruby.md)
>
> Ukázky pro aplikace API vytvořené pro Logic Apps najdete v [úložišti Azure Logic Apps GitHubu](https://github.com/logicappsio).

## <a name="how-do-custom-apis-differ-from-custom-connectors"></a>Jak se vlastní rozhraní API liší od vlastních konektorů?

Vlastní rozhraní API a [vlastní konektory](../logic-apps/custom-connector-overview.md) jsou webová rozhraní API, která používají REST pro připojitelná rozhraní, [formát metadat Swagger](https://swagger.io/specification/) pro dokumentaci a formát JSON jako formát výměny dat. A vzhledem k tomu, že tato rozhraní API a konektory jsou rozhraní REST API, která komunikují prostřednictvím koncových bodů HTTP, můžete pro vytváření vlastních rozhraní API a konektorů použít libovolný jazyk, třeba .NET, Java, Python nebo Node.js.

Vlastní rozhraní API umožňují volat rozhraní API, která nejsou konektory, a poskytnout koncové body, které můžete volat pomocí protokolu HTTP + Swagger, Azure API Management nebo App Services. Vlastní konektory fungují jako vlastní rozhraní API, ale mají také tyto atributy:

* Registrováno jako Logic Apps prostředků konektoru v Azure.
* Zobrazuje se ikonami vedle konektorů spravovaných Microsoftem v Návrháři Logic Apps.
* K dispozici pouze pro autory konektorů a uživatele aplikace logiky, kteří mají stejné Azure Active Directory tenanta a předplatné Azure v oblasti, kde jsou nasazené Logic Apps.

Můžete také jmenovat zaregistrované konektory pro certifikaci Microsoftu. Tento proces ověří, že registrované konektory splňují kritéria pro veřejné použití a zpřístupňuje tyto konektory uživatelům v Power automatu a Microsoft Power Apps.

Další informace o vlastních konektorech najdete v tématu. 

* [Přehled vlastních konektorů](../logic-apps/custom-connector-overview.md)
* [Vytváření vlastních konektorů z webových rozhraní API](/connectors/custom-connectors/create-web-api-connector)
* [Registrace vlastních konektorů v Azure Logic Apps](/connectors/custom-connectors/)

## <a name="helpful-tools"></a>Užitečné nástroje

Vlastní rozhraní API funguje nejlépe s Logic Apps, když rozhraní API má také [dokument Swagger](https://swagger.io/specification/) , který popisuje operace a parametry rozhraní API.
Mnoho knihoven, jako je [swashbuckle](https://github.com/domaindrivendev/Swashbuckle), může automaticky vygenerovat soubor Swagger. Chcete-li přidat poznámku do souboru Swagger pro zobrazované názvy, typy vlastností a tak dále, můžete použít také [TRex](https://github.com/nihaue/TRex) , aby soubor Swagger dobře fungoval s Logic Apps.

<a name="actions"></a>

## <a name="action-patterns"></a>Vzorce akcí

Aby Logic Apps prováděly úlohy, měla by vaše vlastní rozhraní API poskytovat [*Akce*](./logic-apps-overview.md#logic-app-concepts). Každá operace v rozhraní API se mapuje na akci. Základní akce je kontroler, který přijímá požadavky HTTP a vrací odpovědi HTTP. Například aplikace logiky pošle požadavek HTTP do vaší webové aplikace nebo aplikace API. Vaše aplikace pak vrátí odpověď HTTP spolu s obsahem, který může aplikace logiky zpracovat.

U standardní akce můžete napsat metodu žádosti HTTP do rozhraní API a popsat tuto metodu v souboru Swagger. Své rozhraní API pak můžete volat přímo pomocí [akce http](../connectors/connectors-native-http.md) nebo akce [http + Swagger](../connectors/connectors-native-http-swagger.md) . Ve výchozím nastavení musí být odpovědi vráceny v rámci [časového](./logic-apps-limits-and-config.md)limitu požadavku. 

![Standardní vzorec akce](./media/logic-apps-create-api-app/standard-action.png)

<a name="pattern-overview"></a> Aby aplikace logiky čekala na dokončení probíhajících úloh, vaše rozhraní API může postupovat podle [vzorce asynchronního cyklického dotazování](#async-pattern) nebo pomocí [asynchronního vzoru Webhooku](#webhook-actions) popsaného v tomto tématu. V případě analogového způsobu, který vám pomůže vizualizovat Tato vzorová chování, Představte si proces pro objednávání vlastního dortíku z pekařství. Vzor cyklického dotazování zrcadlí chování, kdy zavoláte do pekařství každých 20 minut, a zkontrolujete, jestli je dortík připravený. Vzorek Webhooku zrcadlí chování, ve kterém se vám bude připojovat k telefonnímu číslu, aby se mohli zavolat na to, kdy je dortík připravený.

Ukázky najdete v [Logic Apps úložišti GitHubu](https://github.com/logicappsio). Přečtěte si taky další informace o [měření využití pro akce](logic-apps-pricing.md).

<a name="async-pattern"></a>

### <a name="perform-long-running-tasks-with-the-polling-action-pattern"></a>Provádění dlouhotrvajících úloh pomocí vzoru akce cyklického dotazování

Chcete-li, aby vaše rozhraní API provádělo úlohy, které by mohly běžet déle než [časový limit žádosti](./logic-apps-limits-and-config.md), můžete použít model asynchronního cyklického dotazování. Tento model rozhraní API funguje v samostatném vlákně, ale zachová aktivní připojení k modulu Logic Apps. Díky tomuto postupu aplikace logiky nevypršel časový limit nebo v dalším kroku pracovního postupu pokračovala, než vaše rozhraní API dokončí práci.

Tady je obecný vzor:

1. Ujistěte se, že stroj ví, že vaše rozhraní API přijalo požadavek a začal pracovat.
2. Když modul provede další požadavky na stav úlohy, poznáte modul, když vaše rozhraní API dokončí úlohu.
3. Vrátí relevantní data modulu, aby mohl pracovní postup aplikace logiky pokračovat.

<a name="bakery-polling-action"></a> Teď použijte předchozí pekařskou analogii na vzor cyklického dotazování a Představte si, že voláte a objednat si vlastní dortík za doručení. Proces pro zajištění, že dortík přetrvá čas, nechcete čekat na telefon, zatímco pekařace působí na dort. Baker potvrdí vaši objednávku a zavolá se každé 20 minut na stav dortíku. Po 20 minutách budete volat pekařství, ale dáme vám vědět, že se váš dort nedokončil a že byste měli zavolat za další 20 minut. Tento proces back-and se pokračuje, dokud nebudete volat, a přípravné informace o tom, že vaše objednávka je připravená a dává vám svůj dortík. 

Pojďme tedy tento vzor cyklického dotazování namapovat zpátky. Pekař představuje vaše vlastní rozhraní API, zatímco vy, což je zákazník, představuje Logic Apps Engine. Když stroj zavolá vaše rozhraní API s požadavkem, rozhraní API potvrdí požadavek a odpoví na časový interval, kdy může modul kontrolovat stav úlohy. Modul pokračuje v kontrole stavu úlohy, dokud vaše rozhraní API neodpoví na to, že se úloha dokončila, a vrátí data do vaší aplikace logiky, které pak pokračuje v pracovním postupu. 

![Vzor akce cyklického dotazování](./media/logic-apps-create-api-app/custom-api-async-action-pattern.png)

Tady jsou konkrétní kroky, které má vaše rozhraní API sledovat, a to popsaných v perspektivě rozhraní API:

1. Když rozhraní API vrátí požadavek HTTP na spuštění práce, okamžitě vrátí `202 ACCEPTED` odpověď HTTP s `location` hlavičkou popsanou níže v tomto kroku. Tato odpověď umožňuje, aby modul Logic Apps ví, že vaše rozhraní API získalo požadavek, přijalo datovou část požadavku (vstup dat) a právě zpracovává. 
   
   `202 ACCEPTED`Odpověď by měla obsahovat tyto hlavičky:
   
   * *Požadováno*: `location` záhlaví, které určuje absolutní cestu k adrese URL, kde Logic Apps modul může kontrolovat stav úlohy rozhraní API.

   * *Volitelné*: `retry-after` Hlavička, která určuje počet sekund, po které má modul čekat před kontrolou `location` adresy URL pro stav úlohy. 

     Ve výchozím nastavení modul kontroluje každých 20 sekund. Pokud chcete zadat jiný interval, zahrňte `retry-after` hlavičku a počet sekund do dalšího cyklického dotazování.

2. Po uplynutí zadaného času se modul Logic Apps dotazuje na `location` adresu URL, aby zkontroloval stav úlohy. Vaše rozhraní API by mělo provádět tyto kontroly a vracet tyto odpovědi:
   
   * Pokud se úloha dokončí, vraťte odpověď HTTP `200 OK` spolu s datovou částí Response (vstup pro další krok).

   * Pokud se úloha stále zpracovává, vraťte jinou odpověď HTTP `202 ACCEPTED` , ale se stejnými hlavičkami jako původní odpověď.

Když je tento model rozhraní API, nemusíte dělat nic v definici pracovního postupu aplikace logiky, aby bylo možné pokračovat v kontrole stavu úlohy. Když modul získá `202 ACCEPTED` odpověď HTTP a platnou `location` hlavičku, modul respektuje asynchronní vzor a zkontroluje `location` hlavičku, dokud rozhraní API nevrátí odpověď, která není 202.

> [!TIP]
> Příklad asynchronního vzoru najdete [v této ukázce asynchronního řadiče v GitHubu](https://github.com/logicappsio/LogicAppsAsyncResponseSample).

<a name="webhook-actions"></a>

### <a name="perform-long-running-tasks-with-the-webhook-action-pattern"></a>Provádění dlouhotrvajících úloh pomocí vzoru akce Webhooku

Alternativně můžete použít vzor Webhooku pro dlouhotrvající úlohy a asynchronní zpracování. V tomto modelu se aplikace logiky pozastavuje a před pokračováním pracovního postupu čeká na zpětné volání z rozhraní API, aby se dokončilo zpracování. Toto zpětné volání je příspěvek HTTP, který pošle zprávu na adresu URL, když dojde k události. 

<a name="bakery-webhook-action"></a> Teď použijte předchozí pekařii na vzor Webhooku a Představte si, že jste volali pekařství a obvedli vlastní dortíky na doručení. Proces pro zajištění, že dortík přetrvá čas, nechcete čekat na telefon, zatímco pekařace působí na dort. Baker potvrdí vaši objednávku, ale tentokrát jim poskytnete své telefonní číslo, aby vás mohli zavolat, až se dortík dokončí. Tentokrát vám oznamujeme, že je vaše objednávka připravená a že je doručí váš dort.

Když jsme tento vzor Webhooku namapovali zpátky, pracovní postup představuje vaše vlastní rozhraní API, zatímco vy, což je zákazník v dortu, představuje modul Logic Apps. Stroj volá vaše rozhraní API s požadavkem a obsahuje adresu URL zpětného volání.
Po dokončení úlohy používá vaše rozhraní API adresu URL, která upozorní modul a vrátí data do vaší aplikace logiky, což pak pokračuje v pracovním postupu. 

Pro tento model nastavte na řadiči dva koncové body: `subscribe` a `unsubscribe`

*  `subscribe` koncový bod: když provádění dosáhne akce vašeho rozhraní API v pracovním postupu, modul Logic Apps volá `subscribe` koncový bod. Tento krok způsobí, že aplikace logiky vytvoří adresu URL zpětného volání, kterou vaše rozhraní API uloží, a po dokončení práce počká na zpětné volání od rozhraní API. Rozhraní API pak zavolá zpět s HTTP POST na adresu URL a předá do aplikace logiky libovolný vrácený obsah a záhlaví jako vstup.

* `unsubscribe` koncový bod: Pokud je aplikace logiky zrušena, modul Logic Apps volá `unsubscribe` koncový bod. Vaše rozhraní API pak může zrušit registraci adresy URL zpětného volání a v případě potřeby zastavit všechny procesy.

![Vzor akce Webhooku](./media/logic-apps-create-api-app/custom-api-webhook-action-pattern.png)

Návrhář aplikace logiky v současné době nepodporuje zjišťování koncových bodů webhooků prostřednictvím Swagger. Pro tento model tedy musíte přidat [akci **Webhooku**](../connectors/connectors-native-webhook.md) a zadat adresu URL, záhlaví a text pro vaši žádost. Viz také [akce a triggery pracovního postupu](logic-apps-workflow-actions-triggers.md#apiconnection-webhook-action). Příklad vzoru Webhooku najdete v tomto příkladu [triggeru Webhooku na GitHubu](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs).

Tady jsou některé další tipy a poznámky:

* Chcete-li předat adresu URL zpětného volání, můžete v případě `@listCallbackUrl()` potřeby použít funkci pracovního postupu v libovolném z předchozích polí.

* Pokud vlastníte aplikaci logiky i předplacenou službu, nemusíte volat `unsubscribe` koncový bod po volání adresy URL zpětného volání. V opačném případě musí modul runtime Logic Apps volat `unsubscribe` koncový bod k signalizaci, že nejsou očekávána žádná další volání a aby bylo možné vyčistit prostředky na straně serveru.

<a name="triggers"></a>

## <a name="trigger-patterns"></a>Vzory triggerů

Vaše vlastní rozhraní API může fungovat jako [*Trigger*](./logic-apps-overview.md#logic-app-concepts) , který spouští aplikaci logiky, když nová data nebo událost splňují zadanou podmínku. Tato aktivační událost může buď pravidelně kontrolovat, nebo čekat a naslouchat pro nová data nebo události na koncovém bodu služby. Pokud nová data nebo událost splní zadanou podmínku, Trigger se aktivuje a spustí aplikaci logiky, která naslouchá této aktivační události. Aby bylo možné spustit Logic Apps tímto způsobem, může vaše rozhraní API sledovat [*Trigger cyklického dotazování*](#polling-triggers) nebo vzor [*triggeru Webhooku*](#webhook-triggers) . Tyto vzory jsou podobné jejich protějškům při [dotazování akcí](#async-pattern) a [akcí Webhooku](#webhook-actions). Přečtěte si taky další informace o [měření využití pro aktivační události](logic-apps-pricing.md).

<a name="polling-triggers"></a>

### <a name="check-for-new-data-or-events-regularly-with-the-polling-trigger-pattern"></a>Pravidelné zjišťování nových dat nebo událostí pomocí vzoru triggeru cyklického dotazování

*Aktivační událost cyklického dotazování* funguje podobně jako [Akce cyklického dotazování](#async-pattern) dříve popsané v tomto tématu. Modul Logic Apps pravidelně volá a kontroluje koncový bod triggeru pro nová data nebo události. Pokud modul najde nová data nebo událost, která splňuje zadanou podmínku, aktivuje se Trigger. Modul potom vytvoří instanci aplikace logiky, která zpracovává data jako vstup. 

![Vzor triggeru cyklického dotazování](./media/logic-apps-create-api-app/custom-api-polling-trigger-pattern.png)

> [!NOTE]
> Každá žádost o cyklické dotazování se počítá jako provedení akce, a to i v případě, že se nevytvoří žádná instance aplikace logiky. Aby se zabránilo tomu, že se stejná data zpracovávají víckrát, měla by aktivační událost vyčistit data, která již byla přečtena a předána do aplikace logiky.

Tady jsou konkrétní kroky pro aktivační událost cyklického dotazování, která je popsaná v perspektivě rozhraní API:

| Našla se nová data nebo událost?  | Odpověď rozhraní API | 
| ------------------------- | ------------ |
| Found | Vrátí stav HTTP `200 OK` s datovou částí Response (vstup pro další krok). <br/>Tato odpověď vytvoří instanci aplikace logiky a spustí pracovní postup. | 
| Nenalezeno | Vrátí stav HTTP `202 ACCEPTED` s `location` hlavičkou a `retry-after` hlavičkou. <br/>V případě aktivačních událostí `location` by záhlaví mělo obsahovat také `triggerState` parametr dotazu, což je obvykle "časové razítko". Rozhraní API může pomocí tohoto identifikátoru sledovat čas poslední aktivace aplikace logiky. | 
||| 

Pokud třeba chcete pravidelně kontrolovat službu pro nové soubory, můžete vytvořit Trigger cyklického dotazování s tímto chováním:

| Obsahuje požadavek `triggerState` ? | Odpověď rozhraní API | 
| -------------------------------- | -------------| 
| Ne | Vrátí stav HTTP a `202 ACCEPTED` `location` hlavičku s `triggerState` nastavenou na aktuální čas a `retry-after` interval na 15 sekund. | 
| Ano | Ověřte službu pro soubory přidané po `DateTime` pro `triggerState` . | 
||| 

| Počet nalezených souborů | Odpověď rozhraní API | 
| --------------------- | -------------| 
| Jeden soubor | Vraťte stav HTTP `200 OK` a datovou část obsahu, aktualizujte `triggerState` `DateTime` hodnotu pro vrácený soubor a nastavte `retry-after` interval na 15 sekund. | 
| Více souborů | V jednom okamžiku vraťte soubor a `200 OK` stav HTTP, aktualizujte `triggerState` a nastavte `retry-after` interval na 0 sekund. </br>Pomocí těchto kroků stroj ví, že jsou k dispozici další data a že by měl modul hned požádat o data z adresy URL v `location` hlavičce. | 
| Žádné soubory | Vrátí stav HTTP `202 ACCEPTED` , neměňte `triggerState` a nastavte `retry-after` interval na 15 sekund. | 
||| 

> [!TIP]
> Ukázkový vzor triggeru cyklického dotazování najdete [v ukázce tohoto kontroleru triggeru cyklického dotazování v GitHubu](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/PollTriggerController.cs).

<a name="webhook-triggers"></a>

### <a name="wait-and-listen-for-new-data-or-events-with-the-webhook-trigger-pattern"></a>Vyčkejte a poslouchejte nová data nebo události pomocí vzoru triggeru Webhooku.

Trigger Webhooku je *Trigger push* , který čeká a naslouchá novým datům nebo událostem na koncovém bodu služby. Pokud nová data nebo událost splní zadanou podmínku, Trigger se aktivuje a vytvoří instanci aplikace logiky, která pak data zpracuje jako vstup.
Triggery Webhooku fungují podobně jako [Akce Webhooku](#webhook-actions) dříve popsané v tomto tématu a jsou nastavené `subscribe` pomocí `unsubscribe` koncových bodů a. 

* `subscribe` koncový bod: když do aplikace logiky přidáte a uložíte Trigger Webhooku, modul Logic Apps volá `subscribe` koncový bod. Tento krok způsobí, že aplikace logiky vytvoří adresu URL zpětného volání, které vaše rozhraní API ukládá. Pokud jsou k dispozici nová data nebo událost, která splňuje zadanou podmínku, rozhraní API se vrátí pomocí HTTP POST na adresu URL. Datová část obsahu a hlavičky přecházejí jako vstup do aplikace logiky.

* `unsubscribe` koncový bod: Pokud se Trigger Webhooku nebo celá aplikace logiky odstraní, modul Logic Apps volá `unsubscribe` koncový bod. Vaše rozhraní API pak může zrušit registraci adresy URL zpětného volání a v případě potřeby zastavit všechny procesy.

![Vzor triggeru Webhooku](./media/logic-apps-create-api-app/custom-api-webhook-trigger-pattern.png)

Návrhář aplikace logiky v současné době nepodporuje zjišťování koncových bodů webhooků prostřednictvím Swagger. Pro tento model proto musíte přidat [Trigger **Webhooku**](../connectors/connectors-native-webhook.md) a zadat adresu URL, záhlaví a text vaší žádosti. Viz také [Trigger HTTPWebhook](logic-apps-workflow-actions-triggers.md#httpwebhook-trigger). Příklad vzoru Webhooku najdete [v ukázce tohoto kontroleru triggeru triggeru Webhooku na GitHubu](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs).

Tady jsou některé další tipy a poznámky:

* Chcete-li předat adresu URL zpětného volání, můžete v případě `@listCallbackUrl()` potřeby použít funkci pracovního postupu v libovolném z předchozích polí.

* Aby se zabránilo tomu, že se stejná data zpracovávají víckrát, měla by aktivační událost vyčistit data, která již byla přečtena a předána do aplikace logiky.

* Pokud vlastníte aplikaci logiky i předplacenou službu, nemusíte volat `unsubscribe` koncový bod po volání adresy URL zpětného volání. V opačném případě musí modul runtime Logic Apps volat `unsubscribe` koncový bod k signalizaci, že nejsou očekávána žádná další volání a aby bylo možné vyčistit prostředky na straně serveru.

## <a name="improve-security-for-calls-to-your-apis-from-logic-apps"></a>Vylepšení zabezpečení volání rozhraní API z Logic Apps

Po vytvoření vlastních rozhraní API nastavte ověřování pro vaše rozhraní API, abyste je mohli bezpečně volat z Logic Apps. Naučte [se zlepšovat zabezpečení volání vlastních rozhraní API z Logic Apps](../logic-apps/logic-apps-custom-api-authentication.md).

## <a name="deploy-and-call-your-apis"></a>Nasazení a volání rozhraní API

Po nastavení ověřování nastavte nasazení pro vaše rozhraní API. Naučte [se nasazovat a volat vlastní rozhraní API z Logic Apps](../logic-apps/logic-apps-custom-api-host-deploy-call.md).

## <a name="publish-custom-apis-to-azure"></a>Publikování vlastních rozhraní API do Azure

Aby vaše vlastní rozhraní API byla dostupná pro ostatní Logic Apps uživatele v Azure, musíte přidat zabezpečení a zaregistrovat je jako konektory aplikace logiky. Další informace najdete v tématu [Přehled vlastních konektorů](../logic-apps/custom-connector-overview.md). 

Pokud chcete, aby vaše vlastní rozhraní API byla dostupná pro všechny uživatele v Logic Apps, automatizaci a aplikacích Microsoft Power, musíte přidat zabezpečení, zaregistrovat vaše rozhraní API jako konektory aplikací logiky a pojmenovat konektory pro [program Microsoft Azure Certified](https://azure.microsoft.com/marketplace/programs/certified/logic-apps/). 

## <a name="get-support"></a>Získání podpory

* Pro konkrétní nápovědu k vlastním rozhraním API kontaktujte [customapishelp@microsoft.com](mailto:customapishelp@microsoft.com) .

* Otázky najdete na stránce s [dotazem na&Microsoft Q pro Azure Logic Apps](/answers/topics/azure-logic-apps.html).

* Pokud chcete pomoci při vylepšování Logic Apps, hlasujte nebo zanechte své nápady na [webu zpětné vazby uživatelů Logic Apps](https://aka.ms/logicapps-wish). 

## <a name="next-steps"></a>Další kroky

* [Zpracování chyb a výjimek](../logic-apps/logic-apps-exception-handling.md)
* [Volání, triggery nebo vnořování Logic Apps s koncovými body HTTP](../logic-apps/logic-apps-http-endpoint.md)
* [Měření využití pro akce a triggery](../logic-apps/logic-apps-pricing.md)

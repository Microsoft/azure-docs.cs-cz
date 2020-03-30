---
title: Vytvoření webových api & REST API pro aplikace Logika Azure
description: Vytvoření webových api & REST API pro volání vašich API, služeb nebo systémů pro systémové integrace v Aplikacích Logika Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, jehollan, logicappspm
ms.topic: article
ms.date: 05/26/2017
ms.openlocfilehash: bb6c99ea12e5b53631d42a04b36b7bfef2337e42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270534"
---
# <a name="create-custom-apis-you-can-call-from-azure-logic-apps"></a>Vytvoření vlastních api, která můžete volat z Aplikací Azure Logic Apps

Přestože Azure Logic Apps nabízí [stovky konektorů,](../connectors/apis-list.md) které můžete použít v pracovních postupech aplikace logiky, můžete chtít volat rozhraní API, systémy a služby, které nejsou k dispozici jako konektory. Můžete vytvořit vlastní api, které poskytují akce a aktivační události pro použití v aplikacích logiky. Tady jsou další důvody, proč můžete chtít vytvořit vlastní api, která můžete volat z pracovních postupů aplikace logiky:

* Rozšiřte své aktuální pracovní postupy integrace systému a integrace dat.
* Pomozte zákazníkům používat vaše služby ke správě profesionálních nebo osobních úkolů.
* Rozšiřte dosah, zjistitelnost a používejte pro své služby.

V podstatě konektory jsou webová rozhraní API, která používají REST pro připojitelná rozhraní, [formát metadat Swagger](https://swagger.io/specification/) pro dokumentaci a JSON jako formát výměny dat. Vzhledem k tomu, že konektory jsou rozhraní REST API, které komunikují prostřednictvím koncových bodů HTTP, můžete pro vytváření konektorů použít libovolný jazyk, jako je .NET, Java, Python nebo Node.js. Můžete také hostovat rozhraní API ve [službě Azure App Service](../app-service/overview.md), což je nabídka platformy jako služby (PaaS), která poskytuje jeden z nejlepších, nejjednodušších a nejškálovatelnějších způsobů pro hostování rozhraní API. 

Pro vlastní rozhraní API pro práci s aplikacemi logiky, vaše rozhraní API můžete poskytnout [*akce,*](./logic-apps-overview.md#logic-app-concepts) které provádějí konkrétní úkoly v pracovních postupech aplikace logiky. Vaše rozhraní API může také fungovat jako [*aktivační událost,*](./logic-apps-overview.md#logic-app-concepts) která spustí pracovní postup aplikace logiky, když nová data nebo událost splňuje zadanou podmínku. Toto téma popisuje běžné vzory, které můžete sledovat pro vytváření akcí a aktivačních událostí v rozhraní API, na základě chování, které chcete, aby vaše rozhraní API poskytovat.

Rozhraní API můžete hostovat ve [službě Azure App Service](../app-service/overview.md), což je nabídka paas (platforma jako služba), která poskytuje vysoce škálovatelný a snadný hosting rozhraní API.

> [!TIP] 
> I když rozhraní API můžete nasadit jako webové aplikace, zvažte nasazení rozhraní API jako aplikace rozhraní API, což může usnadnit vaši práci při vytváření, hostování a využívání rozhraní API v cloudu a místně. Není třeba měnit žádný kód ve vašich rozhraních API – stačí nasadit kód do aplikace rozhraní API. Najdete například, jak vytvářet aplikace rozhraní API vytvořené pomocí těchto jazyků: 
> 
> * [ASP.NET](../app-service/app-service-web-get-started-dotnet.md). 
> * [Java](../app-service/app-service-web-get-started-java.md)
> * [Node.js](../app-service/app-service-web-get-started-nodejs.md)
> * [PHP](../app-service/app-service-web-get-started-php.md)
> * [Python](../app-service/containers/quickstart-python.md)
> * [Ruby](../app-service/containers/quickstart-ruby.md)
>
> Ukázky aplikací API vytvořené pro aplikace logiky najdete v [úložišti GitHub azure logic apps](https://github.com/logicappsio) nebo [blogu](https://aka.ms/logicappsblog).

## <a name="how-do-custom-apis-differ-from-custom-connectors"></a>Jak se vlastní rozhraní API liší od vlastních konektorů?

Vlastní rozhraní API a [vlastní konektory](../logic-apps/custom-connector-overview.md) jsou webová rozhraní API, která používají REST pro připojitelná rozhraní, [formát metadat Swagger](https://swagger.io/specification/) pro dokumentaci a JSON jako formát výměny dat. A protože tato rozhraní API a konektory jsou rozhraní REST API, které komunikují prostřednictvím koncových bodů PROTOKOLU HTTP, můžete použít libovolný jazyk, jako je .NET, Java, Python nebo Node.js, pro vytváření vlastních rozhraní API a konektorů.

Vlastní rozhraní API umožňují volat rozhraní API, která nejsou konektory, a poskytovat koncové body, které můžete volat pomocí HTTP + Swagger, Azure API Management nebo App Services. Vlastní konektory fungují jako vlastní rozhraní API, ale mají také tyto atributy:

* Registrované jako prostředky Logic Apps Connector v Azure.
* Zobrazí se s ikonami vedle konektorů spravovaných společností Microsoft v Návrháři logických aplikací.
* K dispozici jenom pro autory konektorů a uživatele aplikace logiky, kteří mají stejného klienta Azure Active Directory a předplatné Azure v oblasti, kde se nasazují aplikace logiky.

Můžete také nominovat registrované konektory pro certifikaci Microsoftu. Tento proces ověří, zda registrované konektory splňují kritéria pro veřejné použití, a zpřístupní tyto konektory uživatelům v power automatech a aplikacích Microsoft Power Apps.

Další informace o vlastních konektorech naleznete v tématu 

* [Přehled vlastních konektorů](../logic-apps/custom-connector-overview.md)
* [Vytvoření vlastních konektorů z webových rozhraní API](../logic-apps/custom-connector-build-web-api-app-tutorial.md)
* [Registrace vlastních konektorů v aplikacích Azure Logic Apps](../logic-apps/logic-apps-custom-connector-register.md)

## <a name="helpful-tools"></a>Užitečné nástroje

Vlastní rozhraní API funguje nejlépe s aplikacemi logiky, když rozhraní API má také [dokument Swagger,](https://swagger.io/specification/) který popisuje operace a parametry rozhraní API.
Mnoho knihoven, jako [je Swashbuckle](https://github.com/domaindrivendev/Swashbuckle), může automaticky generovat soubor Swagger pro vás. Chcete-li okomentovat soubor Swagger pro zobrazované názvy, typy vlastností a tak dále, můžete také použít [TRex,](https://github.com/nihaue/TRex) aby váš soubor Swagger fungoval dobře s aplikacemi logiky.

<a name="actions"></a>

## <a name="action-patterns"></a>Vzory akcí

Pro aplikace logiky provádět úkoly, vlastní rozhraní API by měl poskytnout [*akce*](./logic-apps-overview.md#logic-app-concepts). Každá operace v rozhraní API se mapuje na akci. Základní akce je řadič, který přijímá požadavky HTTP a vrací odpovědi HTTP. Takže například aplikace logiky odešle požadavek HTTP do webové aplikace nebo aplikace rozhraní API. Vaše aplikace pak vrátí odpověď HTTP spolu s obsahem, který aplikace logiky může zpracovat.

Pro standardní akce můžete napsat metodu požadavku HTTP do rozhraní API a popsat tuto metodu v souboru Swagger. Potom můžete volat rozhraní API přímo pomocí [akce HTTP](../connectors/connectors-native-http.md) nebo akce HTTP [+ Swagger.](../connectors/connectors-native-http-swagger.md) Ve výchozím nastavení musí být odpovědi vráceny v rámci [časového limitu požadavku](./logic-apps-limits-and-config.md). 

![Standardní vzor akce](./media/logic-apps-create-api-app/standard-action.png)

<a name="pattern-overview"></a>Chcete-li, aby aplikace logiky čekala, zatímco vaše rozhraní API dokončí déle běžící úlohy, vaše rozhraní API můžete sledovat [asynchronní dotazování vzor](#async-pattern) nebo [asynchronní webhookvzor](#webhook-actions) popsaný v tomto tématu. Pro analogii, která vám pomůže vizualizovat různé chování těchto vzorů, představte si proces objednání vlastního dortu z pekárny. Vzor dotazování odráží chování, kde voláte pekárnu každých 20 minut, abyste zkontrolovali, zda je koláč připraven. Webhook vzor odráží chování, kde pekárna vás požádá o vaše telefonní číslo, aby vám mohli zavolat, když je dort připraven.

Ukázky najdete v [úložišti GitHub logic Apps](https://github.com/logicappsio). Další informace o [měření využití akcí](logic-apps-pricing.md).

<a name="async-pattern"></a>

### <a name="perform-long-running-tasks-with-the-polling-action-pattern"></a>Provádění dlouhotrvajících úloh se vzorem akce dotazování

Chcete-li, aby rozhraní API provádělo úlohy, které by mohly běžet déle než [časový limit požadavku](./logic-apps-limits-and-config.md), můžete použít vzor asynchronního dotazování. Tento vzor má vaše rozhraní API pracovat v samostatném vlákně, ale zachovat aktivní připojení k modulu Logic Apps. Tímto způsobem aplikace logiky není časový čas nebo pokračovat s dalším krokem v pracovním postupu před dokončením práce rozhraní API.

Zde je obecný vzor:

1. Ujistěte se, že modul ví, že vaše rozhraní API přijal požadavek a začal pracovat.
2. Když modul provede následné požadavky na stav úlohy, dejte modulu vědět, kdy vaše rozhraní API dokončí úlohu.
3. Vraťte příslušná data do modulu tak, aby pracovní postup aplikace logiky můžete pokračovat.

<a name="bakery-polling-action"></a>Nyní aplikujte předchozí analogii pekárny na volební vzor a představte si, že zavoláte pekárnu a objednáte si vlastní dort pro doručení. Proces výroby dortu nějakou dobu trvá a nechcete čekat na telefonu, zatímco pekárna pracuje na dortu. Pekárna potvrzuje vaši objednávku a každých 20 minut vám zavolá na stav dortu. Po 20 minutách, zavoláte do pekárny, ale oni vám řeknou, že váš dort není hotový a že byste měli zavolat za dalších 20 minut. Tento proces tam a zpět pokračuje, dokud nezavoláte, a pekárna vám řekne, že vaše objednávka je připravena a doručí váš dort. 

Takže pojďme mapovat tento volební vzor zpět. Pekárna představuje vlastní rozhraní API, zatímco vy, zákazník dortu, představujete modul Logic Apps. Když motor volá vaše rozhraní API s požadavkem, vaše rozhraní API potvrdí požadavek a odpoví časovým intervalem, kdy může modul zkontrolovat stav úlohy. Modul pokračuje v kontrole stavu úlohy, dokud vaše rozhraní API neodpoví, že úloha je hotová a vrátí data do aplikace logiky, která pak pokračuje v pracovním postupu. 

![Vzor akce dotazování](./media/logic-apps-create-api-app/custom-api-async-action-pattern.png)

Tady jsou konkrétní kroky, které má vaše rozhraní API sledovat, popsané z pohledu rozhraní API:

1. Když vaše rozhraní API získá požadavek HTTP `202 ACCEPTED` pro zahájení `location` práce, okamžitě vrátí odpověď HTTP s hlavičkou popsanou dále v tomto kroku. Tato odpověď umožňuje modul u nakládací aplikace vědět, že vaše rozhraní API získal požadavek, přijal datové části požadavku (vstup dat) a nyní zpracovává. 
   
   Odpověď `202 ACCEPTED` by měla obsahovat tyto hlavičky:
   
   * *Povinné:* `location` Záhlaví, které určuje absolutní cestu k adrese URL, kde modul Logic Apps může zkontrolovat stav úlohy vašeho rozhraní API.

   * *Volitelné*: `retry-after` Záhlaví, které určuje počet sekund, po které `location` by měl modul čekat, než zkontroluje adresu URL pro stav úlohy. 

     Ve výchozím nastavení motor kontroluje každých 20 sekund. Chcete-li zadat jiný `retry-after` interval, zahrňte záhlaví a počet sekund do dalšího dotazování.

2. Po uplynutí zadaného času modul Logic `location` Apps vyhledá adresu URL pro kontrolu stavu úlohy. Rozhraní API by mělo provádět tyto kontroly a vrátit tyto odpovědi:
   
   * Pokud je úloha hotová, vraťte odpověď HTTP `200 OK` spolu s datovou částí odpovědi (vstup pro další krok).

   * Pokud úloha stále zpracovává, `202 ACCEPTED` vraťte další odpověď HTTP, ale se stejnými záhlavími jako původní odpověď.

Když vaše rozhraní API následuje tento vzor, nemusíte dělat nic v definici pracovního postupu aplikace logiky pokračovat v kontrole stavu úlohy. Když modul získá `202 ACCEPTED` odpověď HTTP `location` a platné záhlaví, modul respektuje asynchronní `location` vzor a zkontroluje záhlaví, dokud vaše rozhraní API vrátí non-202 odpověď.

> [!TIP]
> Příklad asynchronní vzor, zkontrolujte tuto [ukázku odezvy asynchronní horečné kontroly v GitHub](https://github.com/logicappsio/LogicAppsAsyncResponseSample).

<a name="webhook-actions"></a>

### <a name="perform-long-running-tasks-with-the-webhook-action-pattern"></a>Provádění dlouhotrvajících úloh pomocí vzoru akce webhooku

Jako alternativu můžete použít webhooku vzor pro dlouhotrvající úlohy a asynchronní zpracování. Tento vzor má pozastavit aplikaci logiky a čekat na "zpětné volání" z vašeho rozhraní API k dokončení zpracování před pokračováním pracovního postupu. Toto zpětné volání je HTTP POST, který odešle zprávu na adresu URL, když dojde k události. 

<a name="bakery-webhook-action"></a>Nyní aplikujte předchozí analogii pekárny na webhookový vzor a představte si, že zavoláte pekárnu a objednáte si vlastní dort pro doručení. Proces výroby dortu nějakou dobu trvá a nechcete čekat na telefonu, zatímco pekárna pracuje na dortu. Pekárna potvrzuje vaši objednávku, ale tentokrát jim dáte své telefonní číslo, aby vám mohli zavolat, až bude dort hotový. Tentokrát vám pekárna řekne, kdy je vaše objednávka připravena a doručí váš dort.

Když mapujeme tento webhookvzor zpět, pekárna představuje vaše vlastní rozhraní API, zatímco vy, zákazník dort, představují modul Logic Apps. Modul volá vaše rozhraní API s požadavkem a obsahuje adresu URL zpětného volání.
Po dokončení úlohy, rozhraní API používá adresu URL upozornit motor a vrátit data do aplikace logiky, která pak pokračuje v pracovním postupu. 

Pro tento vzor nastavte na ovladači `subscribe` dva koncové body: a`unsubscribe`

*  `subscribe`koncový bod: Když spuštění dosáhne akce vašeho rozhraní API v pracovním `subscribe` postupu, modul Logic Apps volá koncový bod. Tento krok způsobí, že aplikace logiky k vytvoření adresy URL zpětného volání, které vaše rozhraní API ukládá a pak čekat na zpětné volání z vašeho rozhraní API po dokončení práce. Vaše rozhraní API pak volá zpět s HTTP POST na adresu URL a předá všechny vrácený obsah a záhlaví jako vstup do aplikace logiky.

* `unsubscribe`koncový bod: Pokud je spuštění aplikace logiky zrušeno, `unsubscribe` modul Logic Apps volá koncový bod. Vaše rozhraní API pak můžete zrušit registraci adresu URL zpětného volání a zastavit všechny procesy podle potřeby.

![Vzor akce Webhooku](./media/logic-apps-create-api-app/custom-api-webhook-action-pattern.png)

> [!NOTE]
> V současné době návrhář aplikace logiky nepodporuje zjišťování koncových bodů webhooku prostřednictvím Swagger. Takže pro tento vzor, budete muset přidat akci [ **Webhook** ](../connectors/connectors-native-webhook.md) a zadejte adresu URL, záhlaví a tělo pro váš požadavek. Viz také [akce a aktivační události pracovního postupu](logic-apps-workflow-actions-triggers.md#apiconnection-webhook-action). Chcete-li předat adresu URL zpětného `@listCallbackUrl()` volání, můžete použít funkci pracovního postupu v libovolném z předchozích polí podle potřeby.

> [!TIP]
> Příklad vzoru webhooku, zkontrolujte tuto [ukázku aktivační události webhooku v GitHubu](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs).

<a name="triggers"></a>

## <a name="trigger-patterns"></a>Spouštěcí vzory

Vaše vlastní rozhraní API může fungovat jako [*aktivační událost,*](./logic-apps-overview.md#logic-app-concepts) která spustí aplikaci logiky, když nová data nebo událost splňuje zadanou podmínku. Tato aktivační událost můžete buď pravidelně kontrolovat, nebo čekat a poslouchat, pro nová data nebo události v koncovém bodě služby. Pokud nová data nebo událost splňuje zadanou podmínku, aktivační událost se spustí a spustí aplikaci logiky, která naslouchá této aktivační události. Chcete-li spustit aplikace logiky tímto způsobem, vaše rozhraní API můžete sledovat [*dotazování aktivační události*](#polling-triggers) nebo [*webhooku aktivační model.*](#webhook-triggers) Tyto vzory jsou podobné jejich protějšky pro [dotazování akce](#async-pattern) a [webhooku akce](#webhook-actions). Další informace o [měření využití aktivačních událostí](logic-apps-pricing.md).

<a name="polling-triggers"></a>

### <a name="check-for-new-data-or-events-regularly-with-the-polling-trigger-pattern"></a>Pravidelně kontrolujte nová data nebo události pomocí způsobu spouště ní dotazování

*Aktivační událost dotazování* se chová podobně jako [akce dotazování,](#async-pattern) která byla dříve popsána v tomto tématu. Modul Logic Apps pravidelně volá a kontroluje koncový bod aktivační události pro nová data nebo události. Pokud modul najde nová data nebo událost, která splňuje zadanou podmínku, spustí aktivační událost. Potom modul vytvoří instanci aplikace logiky, která zpracovává data jako vstup. 

![Vzor aktivační události dotazování](./media/logic-apps-create-api-app/custom-api-polling-trigger-pattern.png)

> [!NOTE]
> Každý požadavek dotazování se počítá jako spuštění akce, i když je vytvořena žádná instance aplikace logiky. Chcete-li zabránit zpracování stejných dat vícekrát, aktivační událost by měla vyčistit data, která již byla přečtena a předána do aplikace logiky.

Tady jsou konkrétní kroky pro aktivační událost dotazování, popsané z pohledu rozhraní API:

| Našli jste nová data nebo událost?  | Odpověď rozhraní API | 
| ------------------------- | ------------ |
| Found | Vrátí stav `200 OK` HTTP s datovou částí odpovědi (vstup pro další krok). <br/>Tato odpověď vytvoří instanci aplikace logiky a spustí pracovní postup. | 
| Nebyl nalezen. | Vrátí stav `202 ACCEPTED` HTTP `location` s hlavičkou a záhlavím. `retry-after` <br/>Pro aktivační události `location` by záhlaví `triggerState` mělo také obsahovat parametr dotazu, což je obvykle "časové razítko". Vaše rozhraní API můžete použít tento identifikátor ke sledování poslední, kdy byla spuštěna aplikace logiky. | 
||| 

Chcete-li například pravidelně kontrolovat službu pro nové soubory, můžete vytvořit aktivační událost dotazování, která má tyto chování:

| Žádost `triggerState`obsahuje ? | Odpověď rozhraní API | 
| -------------------------------- | -------------| 
| Ne | Vrátí stav `202 ACCEPTED` HTTP `location` plus `triggerState` záhlaví s nastaveným `retry-after` na aktuální čas a interval na 15 sekund. | 
| Ano | Zkontrolujte, zda služba `DateTime` nenaschla soubory přidané po aplikaci for `triggerState`. | 
||| 

| Počet nalezených souborů | Odpověď rozhraní API | 
| --------------------- | -------------| 
| Jeden soubor | Vraťte `200 OK` stav PROTOKOLU HTTP a `triggerState` datovou `DateTime` část obsahu, aktualizujte na vrácený soubor a nastavte `retry-after` interval na 15 sekund. | 
| Více souborů | Vraťte jeden soubor najednou `200 OK` a stav `triggerState`HTTP, `retry-after` aktualizujte a nastavte interval na 0 sekund. </br>Tyto kroky umožňují modulu vědět, že je k dispozici více dat a `location` že modul by měl okamžitě požadovat data z adresy URL v záhlaví. | 
| Žádné soubory | Vraťte `202 ACCEPTED` stav PROTOKOLU HTTP, `triggerState`neměňte jej a nastavte `retry-after` interval na 15 sekund. | 
||| 

> [!TIP]
> Příklad dotazování spouštěcí vzor, zkontrolujte tuto [ukázku řadiče aktivační události dotazování v GitHubu](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/PollTriggerController.cs).

<a name="webhook-triggers"></a>

### <a name="wait-and-listen-for-new-data-or-events-with-the-webhook-trigger-pattern"></a>Čekání a naslouchání novým datům nebo událostem pomocí spouštěcího vzoru webhooku

Aktivační událost webhooku je *aktivační událost push,* která čeká a naslouchá novým datům nebo událostem v koncovém bodě služby. Pokud nová data nebo událost splňuje zadanou podmínku, aktivační událost se aktivuje a vytvoří instanci aplikace logiky, která pak zpracuje data jako vstup.
Webhook aktivační události se chovají podobně jako [akce webhooku](#webhook-actions) dříve `subscribe` popsané `unsubscribe` v tomto tématu a jsou nastaveny s a koncové body. 

* `subscribe`koncový bod: Když přidáte a uložíte aktivační událost webhooku v `subscribe` aplikaci logiky, modul Logic Apps zavolá koncový bod. Tento krok způsobí, že aplikace logiky k vytvoření adresy URL zpětného volání, které ukládá vaše rozhraní API. Pokud jsou nová data nebo událost, která splňuje zadanou podmínku, vaše rozhraní API volá zpět s HTTP POST na adresu URL. Datová část obsahu a záhlaví předat jako vstup do aplikace logiky.

* `unsubscribe`koncový bod: Pokud se odstraní aktivační událost webhooku `unsubscribe` nebo celá aplikace logiky, modul Logic Apps zavolá koncový bod. Vaše rozhraní API pak můžete zrušit registraci adresu URL zpětného volání a zastavit všechny procesy podle potřeby.

![Vzor aktivační události webhooku](./media/logic-apps-create-api-app/custom-api-webhook-trigger-pattern.png)

> [!NOTE]
> V současné době návrhář aplikace logiky nepodporuje zjišťování koncových bodů webhooku prostřednictvím Swagger. Takže pro tento vzor, budete muset přidat [ **Webhook** aktivační událost](../connectors/connectors-native-webhook.md) a zadejte ADRESU URL, záhlaví a tělo pro váš požadavek. Viz také [HTTPWebhook trigger](logic-apps-workflow-actions-triggers.md#httpwebhook-trigger). Chcete-li předat adresu URL zpětného `@listCallbackUrl()` volání, můžete použít funkci pracovního postupu v libovolném z předchozích polí podle potřeby.
>
> Chcete-li zabránit zpracování stejných dat vícekrát, aktivační událost by měla vyčistit data, která již byla přečtena a předána do aplikace logiky.

> [!TIP]
> Příklad vzoru webhooku, zkontrolujte tuto [ukázku řadiče aktivační události webhooku v GitHubu](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs).

## <a name="improve-security-for-calls-to-your-apis-from-logic-apps"></a>Zlepšení zabezpečení pro volání do vašich api z aplikací logiky

Po vytvoření vlastních api nastavte ověřování pro vaše api, abyste je mohli bezpečně volat z aplikací logiky. [Zjistěte, jak zlepšit zabezpečení pro volání vlastních api z aplikací logiky](../logic-apps/logic-apps-custom-api-authentication.md).

## <a name="deploy-and-call-your-apis"></a>Nasazení a volání vašich api

Po nastavení ověřování nastavte nasazení pro vaše api. Přečtěte [si, jak nasadit a volat vlastní api z aplikací logiky](../logic-apps/logic-apps-custom-api-host-deploy-call.md).

## <a name="publish-custom-apis-to-azure"></a>Publikování vlastních api do Azure

Chcete-li zpřístupnit vlastní rozhraní API pro ostatní uživatele logic apps v Azure, musíte přidat zabezpečení a zaregistrovat je jako konektory aplikace logiky. Další informace najdete v tématu [Přehled vlastních konektorů](../logic-apps/custom-connector-overview.md). 

Chcete-li zpřístupnit vlastní rozhraní API všem uživatelům v aplikacích Logic Apps, Power Automate a Microsoft Power Apps, musíte přidat zabezpečení, zaregistrovat rozhraní API jako konektory aplikace logiky a nominovat konektory pro [program Microsoft Azure Certified](https://azure.microsoft.com/marketplace/programs/certified/logic-apps/). 

## <a name="get-support"></a>Získat podporu

* Chcete-li požádat o [customapishelp@microsoft.com](mailto:customapishelp@microsoft.com)konkrétní pomoc s vlastními řešeními API, obraťte se na společnost .

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* Pokud chcete pomoci při vylepšování Logic Apps, hlasujte nebo zanechte své nápady na [webu zpětné vazby uživatelů Logic Apps](https://aka.ms/logicapps-wish). 

## <a name="next-steps"></a>Další kroky

* [Zpracování chyb a výjimek](../logic-apps/logic-apps-exception-handling.md)
* [Volání, aktivace nebo vnoření logických aplikací s koncovými body HTTP](../logic-apps/logic-apps-http-endpoint.md)
* [Měření využití akcí a aktivačních událostí](../logic-apps/logic-apps-pricing.md)

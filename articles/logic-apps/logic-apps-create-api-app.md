---
title: Vytvoření webového rozhraní API a rozhraní REST API pro Azure Logic Apps | Dokumentace Microsoftu
description: Vytvoření webového rozhraní API a rozhraní REST API pro volání rozhraní API, služby nebo systémy pro systém integrace v Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, jehollan, LADocs
ms.topic: article
ms.assetid: bd229179-7199-4aab-bae0-1baf072c7659
ms.date: 05/26/2017
ms.openlocfilehash: a3f837b41ba6ec7ecadb3e34917a8088e4d1e2d9
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50233510"
---
# <a name="create-custom-apis-you-can-call-from-azure-logic-apps"></a>Vytvoření vlastních rozhraní API můžete volat z Azure Logic Apps

I když Azure Logic Apps nabízí [více než 100 integrovaných konektorů](../connectors/apis-list.md) , můžete použít v pracovních postupů aplikace logiky, můžete chtít volat rozhraní API, systémy a služby, které nejsou k dispozici jako konektory. Můžete vytvořit vaše vlastní rozhraní API, které poskytují akcí a triggerů pro použití v aplikacích logiky. Tady jsou další důvody, proč můžete chtít vytvořit vlastní rozhraní API, která můžete volat z pracovních postupů aplikace logiky:

* Rozšiřte váš aktuální systém pracovní postupy integrace integrace a data.
* Pomozte zákazníkům vaši službu používat ke správě úloh edice professional nebo osobní.
* Rozšiřte dosah, vyhledatelnost a použití pro vaši službu.

V podstatě konektory jsou webové rozhraní API, která použití služby REST pro připojitelné rozhraní [formát metadat Swagger](http://swagger.io/specification/) dokumentaci a formát JSON jako jejich formát dat systému exchange. Vzhledem k tomu, že konektory jsou rozhraní REST API, které komunikují prostřednictvím koncových bodů HTTP, můžete použít libovolný jazyk, jako je .NET, Java nebo Node.js, pro tvorbu konektorů. Můžete také hostování svých rozhraní API na [služby Azure App Service](../app-service/app-service-web-overview.md), platform-as-a-service (PaaS) nabídka, která poskytuje jeden ze způsobů, jak nejlepší, nejjednodušší a největší škálovatelné pro hostování rozhraní API. 

Pro vlastní rozhraní API pro práci s logic apps, můžete zadat vaše rozhraní API [ *akce* ](./logic-apps-overview.md#logic-app-concepts) , které provádějí konkrétní úlohy v pracovních postupů aplikace logiky. Vaše rozhraní API se mohou chovat i jako [ *aktivační událost* ](./logic-apps-overview.md#logic-app-concepts) , který spouští pracovní postup aplikace logiky, když nová data nebo událost splňují zadanou podmínku. Toto téma popisuje běžné vzory, které můžete použít pro vytváření akcí a triggerů v rozhraní API založené na chování, které chcete, aby vaše rozhraní API pro poskytnutí.

Rozhraní API můžete hostovat na [služby Azure App Service](../app-service/app-service-web-overview.md), platform-as-a-service (PaaS) nabídka, která poskytuje vysoce škálovatelnou a jednoduché rozhraní API hostování.

> [!TIP] 
> I když můžete nasadit svoje rozhraní API jako webové aplikace, zvažte nasazení vašich rozhraní API jako aplikace rozhraní API, které může usnadnit práci při vytváření, hostování a používání rozhraní API v cloudu i v místním prostředí. Nemusíte měnit žádný kód v rozhraní API – stačí nasazení kódu do aplikace API. Například informace o vývoji aplikací API vytvořených pomocí těchto jazycích: 
> 
> * [ASP.NET](../app-service/app-service-web-get-started-dotnet.md). 
> * [Java](../app-service/app-service-web-get-started-java.md)
> * [Node.js](../app-service/app-service-web-get-started-nodejs.md)
> * [PHP](../app-service/app-service-web-get-started-php.md)
> * [Python](../app-service/containers/quickstart-python.md)
> * [Ruby](../app-service/containers/quickstart-ruby.md)
>
> Rozhraní API ukázky krásných aplikací s pro logic apps, najdete [úložiště Azure Logic Apps GitHub](http://github.com/logicappsio) nebo [blogu](https://aka.ms/logicappsblog).

## <a name="how-do-custom-apis-differ-from-custom-connectors"></a>Jak vlastní rozhraní API se liší od vlastní konektory?

Vlastní rozhraní API a [vlastních konektorů](../logic-apps/custom-connector-overview.md) jsou webové rozhraní API, která použití služby REST pro připojitelné rozhraní [formát metadat Swagger](http://swagger.io/specification/) dokumentaci a formát JSON jako jejich formát dat systému exchange. A vzhledem k tomu, že tato rozhraní API a konektory jsou rozhraní REST API, které komunikují prostřednictvím koncových bodů HTTP, můžete použít libovolný jazyk, jako je .NET, Java nebo Node.js pro vývoj vlastních rozhraní API a konektory.

Vlastní rozhraní API umožňují volat rozhraní API, která nejsou konektory a poskytují koncové body, které lze volat pomocí protokolu HTTP + Swagger, Azure API Management nebo App Services. Vlastní konektory fungují jako vlastní rozhraní API, ale také mít tyto atributy:

* Zaregistruje jako prostředky konektor Logic Apps v Azure.
* Zobrazí se ikony vedle konektorů spravovaných microsoftem v návrháři pro Logic Apps.
* K dispozici pouze pro autory konektorů a logiky aplikace, kteří mají stejným tenantem Azure Active Directory a předplatným Azure v oblasti, ve které jsou nasazené aplikace logiky.

Můžete nominovat k registrované konektory Microsoftu k certifikaci. Tento proces ověří, jestli registrované konektory splňují kritéria pro veřejně přístupný, zpřístupní tyto konektory pro uživatele v Microsoft Flow a Microsoft PowerApps.

Další informace o vlastních konektorech najdete v tématu 

* [Přehled vlastních konektorů](../logic-apps/custom-connector-overview.md)
* [Vytváření vlastních konektorů z webových rozhraní API](../logic-apps/custom-connector-build-web-api-app-tutorial.md)
* [Registrace vlastních konektorů v Azure Logic Apps](../logic-apps/logic-apps-custom-connector-register.md)

## <a name="helpful-tools"></a>Užitečné nástroje

Vlastní rozhraní API funguje nejlépe s logic apps při rozhraní API má také [dokument Swagger](http://swagger.io/specification/) , který popisuje operace a parametry rozhraní API.
Mnoho knihoven, jako jsou [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle), můžete automaticky vygenerovat soubor Swagger za vás. Chcete-li přidat poznámku k souboru Swagger pro zobrazované názvy, typy vlastností a tak dále, můžete použít také [TRex](https://github.com/nihaue/TRex) tak, aby vašeho souboru Swagger dobře funguje pro aplikace logiky.

<a name="actions"></a>

## <a name="action-patterns"></a>Vzory akce

Pro aplikace logiky k provádění úloh, by měly poskytnout vlastní rozhraní API [ *akce*](./logic-apps-overview.md#logic-app-concepts). Každá operace v rozhraní API se mapuje na akci. Základní akce je kontroler, který přijímá požadavky protokolu HTTP a vrátí odpovědi protokolu HTTP. Takže například aplikace logiky odešle požadavek protokolu HTTP pro webovou aplikaci nebo aplikaci API. Aplikace pak vrátí odpověď HTTP, spolu s obsahem, která dokáže zpracovávat aplikace logiky.

Pro standardní akci můžete napsat metodu požadavku HTTP v rozhraní API a popisují metody v souboru Swagger. Pak můžete volat rozhraní API přímo pomocí [akce HTTP](../connectors/connectors-native-http.md) nebo [HTTP + Swagger](../connectors/connectors-native-http-swagger.md) akce. Ve výchozím nastavení, musí být v rámci vrácena odpovědi [časový limit požadavku](./logic-apps-limits-and-config.md). 

![Akce Standard vzor](./media/logic-apps-create-api-app/standard-action.png)

<a name="pattern-overview"></a> Chcete-li aplikaci logiky, počkejte na dokončení vašeho rozhraní API déle běžících úloh, můžete postupovat podle vašeho rozhraní API [asynchronního dotazování vzor](#async-pattern) nebo [webhooku asynchronní vzor](#webhook-actions) popsané v tomto tématu. Analogicky, která umožňuje vizualizovat různé chování tyto vzory imagine procesu pro řazení vlastní dort z pekárny. Model dotazování zrcadlí chování, kdy zavoláte pekárny každých 20 minut a zkontrolujte, zda dort je připraven. Vzor webhooku zrcadlí chování, kde pekařství ve se vás zeptá na vaše telefonní číslo, můžou vám volat až dort připravený.

Ukázky, najdete [úložiště Logic Apps GitHub](https://github.com/logicappsio). Také se dozvíte více o [měření využití pro akce](logic-apps-pricing.md).

<a name="async-pattern"></a>

### <a name="perform-long-running-tasks-with-the-polling-action-pattern"></a>Provádění dlouhotrvajících úloh se vzorem akcí dotazování

Má vaše rozhraní API, provádět úlohy, které by mohly spouštět déle než [časový limit požadavku](./logic-apps-limits-and-config.md), můžete použít model asynchronního dotazování. Tento model má vaše rozhraní API práce v samostatném vlákně, ale zachová aktivní připojení k modul Logic Apps. Tímto způsobem, aplikace logiky nemá časový limit nebo pokračovat dalším krokem v pracovním postupu před dokončením pracovní rozhraní API.

Tady je obecný vzor:

1. Ujistěte se, že modul ví, že vaše rozhraní API přijmout žádost a začnete pracovat.
2. Modul provádí odeslání dalších žádostí o stav úlohy, dejte vědět, až se dokončí úkol vašeho rozhraní API modulu.
3. Vrátíte relevantní data do modulu tak, aby pracovní postup aplikace logiky může pokračovat.

<a name="bakery-polling-action"></a> Nyní použít předchozí analogie pekařství ve vzoru cyklického dotazování a Představte si, že zavoláte pekárny a pořadí vlastní dort pro doručení. Proces vytváření dort využívá čas a nechcete čekat pekárny funguje na dort na telefonu. Pekárny potvrdí vaši objednávku a má vám volání každých 20 minut, než dort stav. Po 20 minutách předat, volání pekařství ve, ale upozorní vás, že vaše dort podmínka není splněna a, že byste měli volat v jiném 20 minut. Tento proces a zpět pokračuje, dokud voláte, a pekárny zjistíte, že vaši objednávku připravený a poskytuje vaše dort. 

Mapování tak tento model dotazování zpět. Pekárny představuje vlastní rozhraní API, zatímco jste zákazník dort představují modul Logic Apps. Když modul volá rozhraní API s žádostí, vaše rozhraní API potvrdí žádosti a odpovědí s časovým intervalem, pokud modul můžete zkontrolovat stav úlohy. Modul pokračuje, dokud vaše rozhraní API odpoví, že se úloha provádí kontrola stavu úloh a vrací data do aplikace logiky, který pak bude pokračovat pracovního postupu. 

![Vzor akcí dotazování](./media/logic-apps-create-api-app/custom-api-async-action-pattern.png)

Tady jsou konkrétní kroky pro vaše rozhraní API použít, je popsáno z hlediska rozhraní API:

1. Vaše rozhraní API získá požadavku HTTP k zahájení práce, ihned vrátit HTTP `202 ACCEPTED` odpověď `location` záhlaví je popsáno dále v tomto kroku. Tato odpověď umožní modul Logic Apps vědět, že vaše rozhraní API je teď žádost, poměr přijetí datová část požadavku (vstupní data) a nyní zpracovává. 
   
   `202 ACCEPTED` Odpověď by měla obsahovat tato záhlaví:
   
   * *Vyžaduje*: A `location` hlavičku, která určuje absolutní cesta na adresu URL, kde modul Logic Apps můžete zkontrolovat stav úlohy vašeho rozhraní API

   * *Volitelné*: A `retry-after` hlavičku, která určuje počet sekund, po které modul by měl čekat před vrácením se změnami `location` adresu URL pro stav úlohy. 

     Ve výchozím nastavení modul kontroluje každých 20 sekund. Chcete-li určit jiný interval, zahrňte `retry-after` záhlaví a počet sekund, dokud nebude další dotazování.

2. Po uplynutí zadané doby modul Logic Apps dotazuje `location` adresu URL ke kontrole stavu úlohy. Vaše rozhraní API by měl provádět tyto kontroly a vrátit tyto odpovědi:
   
   * Pokud se úloha provádí, vrátí HTTP `200 OK` odpověď, a to společně s datové části odpovědi (vstup na další krok).

   * Pokud stále zpracovává úlohu, vrátí jiný HTTP `202 ACCEPTED` odpověď, ale s záhlaví stejný jako původní odpovědi.

Vaše rozhraní API má následující formát tohoto, není nutné dělat nic v definici pracovního postupu aplikace logiky pokračovat v kontrole stavu úlohy. Když modul získá HTTP `202 ACCEPTED` odpovědi a platný `location` záhlaví, modul respektuje asynchronního vzoru a kontroluje `location` záhlaví, dokud vaše rozhraní API vrátí odpověď bez 202.

> [!TIP]
> Příklad asynchronního vzoru, projděte si to [asynchronní kontroler odpovědi ukázku v Githubu](https://github.com/logicappsio/LogicAppsAsyncResponseSample).

<a name="webhook-actions"></a>

### <a name="perform-long-running-tasks-with-the-webhook-action-pattern"></a>Dlouho běžící úlohy pomocí vzoru akce webhooku

Jako alternativu můžete použít vzor webhooku pro asynchronní zpracování a dlouho běžící úlohy. Tento model má z vašeho rozhraní API dokončete zpracování před pokračováním pracovní postup aplikace logiky pozastavit a počkejte "zpětné volání". Je toto zpětné volání HTTP POST, který odešle zprávu na adresu URL v případě určité události. 

<a name="bakery-webhook-action"></a> Nyní použít předchozí analogie pekařství ve vzoru webhook a Představte si, že zavoláte pekárny a pořadí vlastní dort pro doručení. Proces vytváření dort využívá čas a nechcete čekat pekárny funguje na dort na telefonu. Pekárny potvrdí vaši objednávku, ale tentokrát, které jim určíte své telefonní číslo, můžou vám volat po dokončení dort. Tentokrát pekárny zjistíte, když vaši objednávku připravený a poskytuje vaše dort.

Když jsme mapovat zpět tento webhook vzor, představuje pekárny vlastního rozhraní API, zatímco jste zákazník dort představují modul Logic Apps. Modul volá rozhraní API ve službě žádost a zahrne adresu URL "zpětné volání".
Po dokončení úlohy se vaše rozhraní API používá adresu URL a upozornit modul vrací data do aplikace logiky, který pak bude pokračovat pracovního postupu. 

Pro tento model nastavit dva koncové body na vašem řadiči: `subscribe` a `unsubscribe`

*  `subscribe` koncový bod: když spuštění dosáhne akce vaše rozhraní API v pracovním postupu, modul Logic Apps volání `subscribe` koncového bodu. Tento krok způsobí, že aplikace logiky k vytvoření adresy URL zpětného volání, která ukládá vaše rozhraní API a potom počkejte zpětného volání z rozhraní API po dokončení práce. Vaše rozhraní API potom zavolá zpět pomocí HTTP POST na adresu URL a předává všechny vráceného obsahu a záhlaví jako vstup do aplikace logiky.

* `unsubscribe` koncový bod: Pokud se zruší běh aplikace logiky, modul Logic Apps volání `unsubscribe` koncového bodu. Vaše rozhraní API můžete zrušit registraci adresu URL zpětného volání a zastavte všechny procesy, podle potřeby.

![Vzor akce Webhooku](./media/logic-apps-create-api-app/custom-api-webhook-action-pattern.png)

> [!NOTE]
> Návrhář aplikace logiky v současné době nepodporuje zjišťování koncových bodů webhooku prostřednictvím Swagger. Takže pro tento model je nutné přidat [ **Webhooku** akce](../connectors/connectors-native-webhook.md) a zadejte adresu URL, záhlaví a text pro vaši žádost. Viz také [triggery a akce pracovních postupů](logic-apps-workflow-actions-triggers.md#apiconnection-webhook-action). Chcete-li předat adresu URL zpětného volání, můžete použít `@listCallbackUrl()` funkce workflowu v některém z předchozích polí podle potřeby.

> [!TIP]
> Vzor příkladu webhooku, najdete v tématu to [ukázka trigger webhooku githubu](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs).

<a name="triggers"></a>

## <a name="trigger-patterns"></a>Vzory aktivační událost

Vlastní rozhraní API může fungovat jako [ *aktivační událost* ](./logic-apps-overview.md#logic-app-concepts) nová data nebo událost splňují zadanou podmínku, která aplikace logiky začíná. Tato aktivační událost můžete buď zkontrolujte pravidelně, nebo počkejte a naslouchání pro nová data nebo události na váš koncový bod služby. Pokud nová data nebo událost splňuje zadanou podmínku, aktivuje se a spustí aplikaci logiky, která naslouchá na tento trigger. Spuštění aplikace logiky tímto způsobem, můžete postupovat podle vašeho rozhraní API [ *cyklického dotazování aktivační událost* ](#polling-triggers) nebo [ *trigger webhooku* ](#webhook-triggers) vzor. Tyto vzory jsou podobné na své ekvivalenty pro [akcí dotazování](#async-pattern) a [akce webhooku](#webhook-actions). Také se dozvíte více o [měření využití pro aktivační události](logic-apps-pricing.md).

<a name="polling-triggers"></a>

### <a name="check-for-new-data-or-events-regularly-with-the-polling-trigger-pattern"></a>Zkontrolujte události pravidelně se vzorem cyklického dotazování aktivační událost nebo nová data

A *cyklického dotazování aktivační událost* funguje stejně jako [dotazování akce](#async-pattern) výše popsaný v tomto tématu. Modul Logic Apps pravidelně volá a zkontroluje koncový bod aktivační událost pro nová data nebo události. Pokud modul najde nová data nebo událost, která splňuje zadanou podmínku, trigger se spustí. Modul vytvoří instanci aplikace logiky, která zpracovává data jako vstup. 

![Cyklického dotazování aktivační událost vzorku](./media/logic-apps-create-api-app/custom-api-polling-trigger-pattern.png)

> [!NOTE]
> Každý požadavek dotazování se počítá jako spuštění akce, i v případě, že je vytvořen žádný instanci aplikace logiky. Aby zpracování stejných dat více než jednou, by měl trigger odstranit data, která byla již přečetla a zpracovala aplikace logiky.

Tady jsou konkrétní kroky cyklického dotazování aktivační události, je popsáno z hlediska rozhraní API:

| Najít nová data nebo událost?  | Odpověď rozhraní API | 
| ------------------------- | ------------ |
| Najít | Vrátí HTTP `200 OK` stavu datové části odpovědi (vstup pro další krok). <br/>Tato odpověď vytvoří instanci aplikace logiky a spustí pracovní postup. | 
| Nenalezeno | Vrátí HTTP `202 ACCEPTED` stavu `location` záhlaví a `retry-after` záhlaví. <br/>Aktivačních událostí `location` by měl také obsahovat záhlaví `triggerState` parametr dotazu, což je obvykle "timestamp." Vaše rozhraní API můžete použít tento identifikátor pro sledování posledního času, který se aktivuje aplikace logiky. | 
||| 

Například aby pravidelně kontrolovaly vaši službu pro nové soubory, může sestavení cyklického dotazování aktivační událost, která má tyto chování:

| Požadavek zahrnuje `triggerState`? | Odpověď rozhraní API | 
| -------------------------------- | -------------| 
| Ne | Vrátí HTTP `202 ACCEPTED` stav navíc `location` záhlaví s `triggerState` nastavena na aktuální čas a `retry-after` interval na 15 sekund. | 
| Ano | Zkontrolujte vaši službu pro soubory, které vkládají `DateTime` pro `triggerState`. | 
||| 

| Počet nalezených souborů | Odpověď rozhraní API | 
| --------------------- | -------------| 
| Jeden soubor | Vrátí HTTP `200 OK` aktualizace stavu a datové části obsahu `triggerState` k `DateTime` pro vrácený souboru a nastavte `retry-after` interval na 15 sekund. | 
| Více souborů | Vrátí jeden soubor na čas a HTTP `200 OK` stav, aktualizace `triggerState`a nastavte `retry-after` interval na 0 sekund. </br>Tyto kroky umožní modul vědět, že je k dispozici více dat a že modul by měl bezprostředně požádat o data z adresy URL v `location` záhlaví. | 
| Žádné soubory | Vrátí HTTP `202 ACCEPTED` stav, neměňte `triggerState`a nastavte `retry-after` interval na 15 sekund. | 
||| 

> [!TIP]
> Příklad cyklického dotazování aktivační událost vzor, přečtěte si [cyklického dotazování aktivační událost kontroleru ukázku v Githubu](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/PollTriggerController.cs).

<a name="webhook-triggers"></a>

### <a name="wait-and-listen-for-new-data-or-events-with-the-webhook-trigger-pattern"></a>Počkejte a naslouchání pro nová data nebo události se vzorem aktivační událost webhooku

Aktivační událost webhooku je *triggeru nabízeného* , které vyčká a čeká na nová data nebo události na váš koncový bod služby. Pokud nová data nebo událost splňuje zadanou podmínku, aktivuje se a vytvoří instanci aplikace logiky, který pak tato data jako vstup zpracovává.
Triggerů Webhooků fungují podobně jako [akce webhooku](#webhook-actions) dříve popsané v tomto tématu a jsou nastavené s `subscribe` a `unsubscribe` koncových bodů. 

* `subscribe` koncový bod: Pokud chcete přidat a uložit trigger webhooku v aplikaci logiky, modul Logic Apps volání `subscribe` koncového bodu. Tento krok způsobí, že aplikace logiky a vytvoří adresu URL zpětného volání, které uloží vaše rozhraní API. Když je nová data nebo událost, která splňuje zadanou podmínku, volání rozhraní API zpět s HTTP POST na adresu URL. Datová část obsahu a záhlaví předat jako vstup do aplikace logiky.

* `unsubscribe` koncový bod: Pokud se odstraní trigger webhooku nebo celý logiky aplikace, modul Logic Apps volání `unsubscribe` koncového bodu. Vaše rozhraní API můžete zrušit registraci adresu URL zpětného volání a zastavte všechny procesy, podle potřeby.

![Vzor aktivační událost Webhooku](./media/logic-apps-create-api-app/custom-api-webhook-trigger-pattern.png)

> [!NOTE]
> Návrhář aplikace logiky v současné době nepodporuje zjišťování koncových bodů webhooku prostřednictvím Swagger. Takže pro tento model je nutné přidat [ **Webhooku** aktivační událost](../connectors/connectors-native-webhook.md) a zadejte adresu URL, záhlaví a text pro vaši žádost. Viz také [HTTPWebhook trigger](logic-apps-workflow-actions-triggers.md#httpwebhook-trigger). Chcete-li předat adresu URL zpětného volání, můžete použít `@listCallbackUrl()` funkce workflowu v některém z předchozích polí podle potřeby.
>
> Aby zpracování stejných dat více než jednou, by měl trigger odstranit data, která byla již přečetla a zpracovala aplikace logiky.

> [!TIP]
> Vzor příkladu webhooku, najdete v tématu to [ukázka kontroleru aktivační událost webhooku githubu](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs).

## <a name="secure-calls-to-your-apis-from-logic-apps"></a>Zabezpečené volání rozhraní API z aplikace logiky

Po vytvoření vlastních rozhraní API, takže můžete volat bezpečně z aplikací logiky nastavení ověřování pro vaše rozhraní API. Přečtěte si [zabezpečení volání vlastních rozhraní API z aplikace logiky](../logic-apps/logic-apps-custom-api-authentication.md).

## <a name="deploy-and-call-your-apis"></a>Nasazení a volání rozhraní API

Po nastavení ověřování nasazení pro vaše rozhraní API. Přečtěte si [o nasazení a volání vlastních rozhraní API z aplikace logiky](../logic-apps/logic-apps-custom-api-host-deploy-call.md).

## <a name="publish-custom-apis-to-azure"></a>Publikování vlastních rozhraní API do Azure

Pokud chcete zpřístupnit vaše vlastní rozhraní API pro ostatní uživatele Logic Apps v Azure, musíte přidat zabezpečení a zaregistrujte se jako konektory aplikací logiky. Další informace najdete v tématu [Přehled vlastních konektorů](../logic-apps/custom-connector-overview.md). 

Zpřístupnit vaše vlastní rozhraní API pro všechny uživatele v Logic Apps, Microsoft Flow a Microsoft PowerApps, musíte přidat zabezpečení, zaregistrujte svoje rozhraní API jako konektory aplikací logiky a své konektory pro nominace [programu Microsoft Azure Certified](https://azure.microsoft.com/marketplace/programs/certified/logic-apps/). 

## <a name="get-support"></a>Získat podporu

* Obraťte se na nápovědu s vlastní rozhraní API, [ customapishelp@microsoft.com ](mailto:customapishelp@microsoft.com).

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* Pokud chcete pomoci při vylepšování Logic Apps, hlasujte nebo zanechte své nápady na [webu zpětné vazby uživatelů Logic Apps](https://aka.ms/logicapps-wish). 

## <a name="next-steps"></a>Další postup

* [Zpracování chyb a výjimek](../logic-apps/logic-apps-exception-handling.md)
* [Volání triggeru, nebo vnořené aplikace logiky pomocí koncových bodů HTTP](../logic-apps/logic-apps-http-endpoint.md)
* [Měření využití pro akce a triggery](../logic-apps/logic-apps-pricing.md)

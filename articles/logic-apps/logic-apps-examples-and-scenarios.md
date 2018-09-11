---
title: Příklady a běžné scénáře – Azure Logic Apps | Dokumentace Microsoftu
description: Příklady, scénáře, kurzy a názorné postupy pro Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.assetid: e06311bc-29eb-49df-9273-1f05bbb2395c
ms.date: 01/31/18
ms.openlocfilehash: 0d5a7cb97636b9ca3aabf6c4199ce9e41bc1665d
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2018
ms.locfileid: "44304420"
---
# <a name="common-scenarios-examples-tutorials-and-walkthroughs-for-azure-logic-apps"></a>Běžné scénáře, příklady, kurzy a názorné postupy pro Azure Logic Apps

[Služba Azure Logic Apps](../logic-apps/logic-apps-overview.md) vám pomůže organizovat a integrují různé služby tím, že poskytuje [konektory připravené k použití více než 100](../connectors/apis-list.md), rozsahu z místního SQL serveru nebo SAP do služby Microsoft Cognitive Services. Služba Logic Apps je "bez serveru", aby nemuseli si dělat starosti o škálování nebo instance. Všechno, co musíte udělat, je definování pracovního postupu pomocí aktivační události a akce, které provádí pracovní postup. Základní platforma zpracovává škálování, dostupnost a výkon. Logic Apps je obzvláště užitečné pro scénáře, které je potřeba koordinovat akce více mezi různými systémy a případy použití.

Můžete získat další informace o mnoha způsoby a funkce, která [Azure Logic Apps](../logic-apps/logic-apps-overview.md) podporuje, tady jsou běžných příkladů a scénářů.

## <a name="popular-starting-points-for-logic-app-workflows"></a>Oblíbené počáteční body pro pracovních postupů aplikace logiky

Každá aplikace logiky začíná [ *aktivační událost*](../logic-apps/logic-apps-overview.md#logic-app-concepts)a pouze jednu aktivační událost, která spustí pracovní postup aplikace logiky a předají všechna data jako součást této aktivační události. Některé konektory poskytují triggery, které se dělí na tyto typy:

* *Triggery*: pravidelně kontroluje koncový bod služby pro nová data. Když existovala nová data, trigger vytvoří a spustí novou instanci pracovního postupu pomocí data jako vstup.

* *Triggery nabízených oznámení*: naslouchá dat u koncového bodu služby a počká, až se stane konkrétní události. Pokud k události dojde, trigger spustí okamžitě, vytvoření a spuštění nové instance pracovního postupu, který používá všechna dostupná data jako vstup.

Tady je pár příkladů oblíbený trigger:

* Interval dotazování: 

  * [**Plán – opakování** aktivační událost](../connectors/connectors-native-recurrence.md) umožňuje nastavit počáteční datum a čas plus opakování pro aktivaci vaší aplikace logiky. 
  Můžete například vybrat dny v týdnu a času pro spuštění aplikace logiky.

  * Umožňuje "Při je doručení e-mailu" trigger aplikace logiky kontrolovat nové e-maily ze zprostředkovatele všechny e-mailu, který je podporovaný v Logic Apps, například [Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md), [Gmail](https://docs.microsoft.com/connectors/gmail/), [ Outlook.com](https://docs.microsoft.com/connectors/outlook/), a tak dále.

  * [ **HTTP** aktivační událost](../connectors/connectors-native-http.md) umožní aplikaci logiky zkontrolovat zadaný služby endpoint tím, že komunikuje přes protokol HTTP.
  
* Nabízených oznámení:

  * [ **Požadavku / odpovědi – žádost o** aktivační událost](../connectors/connectors-native-reqres.md) umožňuje vaší aplikaci logiky přijímat požadavky HTTP a reagovat na události nějakým způsobem v reálném čase.

  * [ **HTTP Webhook** aktivační událost](../connectors/connectors-native-webhook.md) se přihlásí k odběru koncového bodu služby tak, že zaregistrujete *adresu URL zpětného volání* s touto službou. 
  Tímto způsobem služby mohou pouze odesílat upozornění na aktivační událost zadané události dojde, tak, aby se aktivační událost nemusí dotazovat služby.

Po přijetí oznámení o nových dat nebo události, trigger aktivuje, vytvoří novou instanci pracovního postupu aplikace logiky a spustí akce pracovního postupu. Přístup k žádným datům z aktivační události v průběhu pracovního postupu. Například aktivační událost "na nový tweet" předá obsah tweetu do běh aplikace logiky. 

## <a name="respond-to-triggers-and-extend-actions"></a>Reagovat na triggery a akce rozšíření

Pro systémy a služby, které nemusí být publikovány konektory můžete taky rozšířit aplikace logiky.

* [Vytváření vlastních aktivačních procedur a akcí](../logic-apps/logic-apps-create-api-app.md)
* [Nastavit dlouhotrvající akce spuštění pracovního postupu](../logic-apps/logic-apps-create-api-app.md)
* [Reakce na vnější události a akce pomocí webhooků](../logic-apps/logic-apps-create-api-app.md)
* [Volání triggeru, nebo vnořené pracovní postupy pomocí synchronní odpovědí na požadavky HTTP](../logic-apps/logic-apps-http-endpoint.md)
* [Kurz: Sestavení s využitím AI řídicího panelu sociálních sítí díky Logic Apps a Power BI během několika minut](http://aka.ms/logicappsdemo)
* [Video: Reagovat na webhooky Twilio SMS a odeslání text odpovědi](https://channel9.msdn.com/Blogs/Windows-Azure/Azure-Logic-Apps-Walkthrough-Webhook-Functions-and-an-SMS-Bot)

## <a name="control-flow-error-handling-and-logging-capabilities"></a>Tok řízení, zpracování chyb a možnosti protokolování

Aplikace logiky zahrnují bohaté možnosti pro pokročilé řízení toku, například podmínky, přepínače, smyčky a oborů. Aby odolné řešení, můžete taky implementovat chyb a zpracování výjimek v vašich pracovních postupů. Pro oznámení a diagnostické protokoly pro spuštění stav pracovního postupu Azure Logic Apps také poskytuje monitorování a výstrah.

* Provádět různé akce na základě [podmíněné příkazy](../logic-apps/logic-apps-control-flow-conditional-statement.md) a [příkazy switch](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Opakujte kroky nebo proces položky pole a kolekce pomocí smyček](../logic-apps/logic-apps-control-flow-loops.md)
* [Akce skupiny spolu s obory](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* [Autor chyb a zpracování výjimek v pracovním postupu](../logic-apps/logic-apps-exception-handling.md)
* [Případ použití: jak společnost poskytující zdravotní používá zpracování pro pracovní postupy HL7 FHIR výjimek aplikace logiky](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [Zapnout sledování, protokolování a výstrahy pro existující aplikace logiky](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [Zapněte monitorování a protokolování diagnostiky vytváření aplikací logiky](../logic-apps/logic-apps-monitor-your-logic-apps-oms.md)

## <a name="deploy-and-manage-logic-apps"></a>Nasazení a Správa aplikací logiky

Můžete plně vývoj a nasazení aplikací logiky s využitím sady Visual Studio, Azure DevOps, nebo jakékoli jiné správy zdrojového kódu a automatické sestavení nástroje. Pro podporu nasazení pro pracovní postupy a závislé připojení v šabloně prostředku, aplikace logiky pomocí šablony nasazení Azure resource. Nástroje sady Visual Studio automaticky vygenerovat tyto šablony, které můžete vrátit se změnami do správy zdrojového kódu pro správu verzí.

* [Vytvoření a nasazení aplikací logiky s využitím sady Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Zapnout sledování, protokolování a výstrahy pro existující aplikace logiky](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [Vytvořit šablonu automatického nasazení](../logic-apps/logic-apps-create-deploy-template.md)

## <a name="content-types-conversions-and-transformations-within-a-run"></a>Typy obsahu, převod a transformace v rámci spuštění

Přístup, převod a transformace více typů obsahu pomocí mnoho funkcí v Azure Logic Apps [jazyka definice pracovního postupu](http://aka.ms/logicappsdocs). Například můžete převod mezi řetězci, JSON a XML s `@json()` a `@xml()` výrazy pracovního postupu. Modul Logic Apps zachová typy obsahu pro podporu obsahu přenos beze ztrát způsobem mezi službami.

* [Jak fungují výrazy pracovního postupu ve službě logic apps](../logic-apps/logic-apps-author-definitions.md)
* [Zpracování typů obsahu bez JSON](../logic-apps/logic-apps-content-type.md), třeba `application/xml`, `application/octet-stream`, a `multipart/formdata`
* [Schéma pro jazyk pro definování pracovních postupů pro Azure Logic Apps](http://aka.ms/logicappsdocs)

## <a name="other-integrations-and-capabilities"></a>Jiné integrace a možnosti

Logic apps nabízí také integrace s mnoha službami, jako je Azure Functions, Azure API Management, Azure App Services a vlastní koncové body HTTP, třeba REST a SOAP.

* [Vytvoření řídicího panelu v reálném čase sociálních sítí s architekturou bez serveru Azure](../logic-apps/logic-apps-scenario-social-serverless.md)
* [Volání z aplikace logiky Azure Functions](../logic-apps/logic-apps-azure-functions.md)
* [Kurz: Trigger aplikace logiky s využitím Azure Functions](../logic-apps/logic-apps-scenario-function-sb-trigger.md)
* [Kurz: Monitorování změn virtuálního počítače pomocí Azure Event Grid a Logic Apps](../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md)
* [Kurz: Vytvoření funkce, která se integruje s Azure Logic Apps a Microsoft Cognitive Services analýza sentimentu Twitter příspěvku](../azure-functions/functions-twitter-email.md)
* [Kurz: Vzdáleného sledování IoT a oznámení pomocí Azure Logic Apps propojení vaší služby IoT hub a poštovní schránky](../iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps.md)
* [Blog: Volání koncových bodů protokolu SOAP z aplikací logiky](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)

## <a name="end-to-end-scenarios"></a>Kompletní scénáře

* [Dokument White Paper: Začátku do konce správu případu, integrace se službami Azure, jako je například Logic Apps](https://aka.ms/enterprise-integration-e2e-case-management-utilities-logic-apps)

## <a name="customer-stories"></a>Příběhy zákazníků

Zjistěte, jak Azure Logic Apps, spolu s dalšími službami Azure a produkty Microsoftu pomohla [těchto společnostech](https://aka.ms/logic-apps-customer-stories) zvýšení své agility a zaměřit se na hlavní obchodní aktivity zjednodušení, uspořádání, automatizace a Orchestrace komplexní procesy.

## <a name="next-steps"></a>Další postup

* [Vytváření definic aplikací logiky pomocí kódu JSON](../logic-apps/logic-apps-author-definitions.md)
* [Zpracování chyb a výjimek v logic apps](../logic-apps/logic-apps-exception-handling.md)
* [Odeslat komentáře, dotazy, svůj názor nebo Navrhnout vylepšení Azure Logic Apps](https://feedback.azure.com/forums/287593-logic-apps)
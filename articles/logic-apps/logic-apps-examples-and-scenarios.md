---
title: "Příklady & běžné scénáře - Azure Logic Apps | Microsoft Docs"
description: "Další informace o aplikacích logiky s příklady, scénáře, kurzy a návody"
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: e06311bc-29eb-49df-9273-1f05bbb2395c
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: logic-apps
ms.date: 01/31/18
ms.author: LADocs; jehollan
ms.openlocfilehash: 02690284e5487dba8e5fb5d973958f944bfa339f
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="common-scenarios-examples-tutorials-and-walkthroughs-for-azure-logic-apps"></a>Běžné scénáře, příklady, kurzy a návody pro Azure Logic Apps

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) vám pomůže orchestraci a integrují různé služby tím, že poskytuje [100 + připravené k použití konektorů](../connectors/apis-list.md), rozsahu z místní SQL Server nebo SAP ke kognitivní službám společnosti Microsoft. Služba Logic Apps je "bez serveru", takže není nutné se obávat škálování nebo instance. Všechny, které musíte udělat, je definice pracovního postupu se aktivační událost a akcí, které provádí pracovní postup. Základní platformě zpracovává škálování, dostupnosti a výkonu. Služba Logic Apps je zvláště užitečná pro případy použití a scénáře, kde je potřeba koordinovat více akcí mezi několika systémy.

Můžete získat další informace o mnoho vzory a možnosti, [Azure Logic Apps](../logic-apps/logic-apps-overview.md) podporuje, tady jsou běžných příkladů a scénářů.

## <a name="popular-starting-points-for-logic-app-workflows"></a>Oblíbené výchozí body pro pracovní postupy aplikace logiky

Začíná každou aplikaci logiky [ *aktivační událost*](../logic-apps/logic-apps-overview.md#logic-app-concepts)a jenom jedna aktivační událost, která spustí pracovní postup aplikace logiky a předá u všech dat v rámci této aktivační události. Několik konektorů poskytuje triggery, které mají tyto typy:

* *Cyklického dotazování aktivační události*: pravidelně kontroluje koncového bodu služby pro nová data. Jestliže existuje nová data, aktivační události vytvoří a spustí novou instanci pracovního postupu s daty jako vstup.

* *Push aktivační události*: naslouchá data na koncový bod služby a čeká, dokud se stane konkrétní události. Když k události dojde, aktivační událost aktivuje okamžitě, vytvoření a spuštění nové instance pracovního postupu, který používá všechna dostupná data jako vstup.

Zde jsou příklady několika oblíbených aktivační události:

* Dotazování: 

  * [**Plán - opakování** aktivační událost](../connectors/connectors-native-recurrence.md) umožňuje nastavit počáteční datum a čas plus opakování pro aktivuje aplikaci logiky. 
  Můžete například vybrat dny v týdnu a čas den pro spuštění aplikace logiky.

  * Aktivační události "Pokud přijat email" umožní aplikaci logiky kontrolovat nové e-maily z kteréhokoli zprostředkovatele e-mailu, kterou podporuje Logic Apps, například [Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md), [z Gmailu](https://docs.microsoft.com/connectors/gmail/), [ Outlook.com](https://docs.microsoft.com/connectors/outlook/)a tak dále.

  * [ **HTTP** aktivační událost](../connectors/connectors-native-http.md) umožní aplikaci logiky komunikaci přes protokol HTTP a zkontroluje koncový bod zadaná služba.
  
* Push:

  * [ **Požadavku nebo odpověď na žádost –** aktivační událost](../connectors/connectors-native-reqres.md) umožní aplikaci logiky přijímat požadavky HTTP a reakce v reálném čase na události nějakým způsobem.

  * [ **Webhooku protokolu HTTP** aktivační událost](../connectors/connectors-native-webhook.md) tak, že zaregistrujete jako odběratel u koncového bodu služby *adresu URL zpětné volání* s touto službou. 
  Tímto způsobem službu můžete právě zprávu, aktivační událost se stane zadanou událost, tak, aby aktivační událost nemusí dotazovat služby.

Po přijetí oznámení o nových dat nebo událost, aktivační událost aktivuje, vytvoří novou instanci pracovního postupu aplikace logiky a spouští akce v pracovním postupu. Přístup k žádným datům z aktivační událost v celém pracovním postupu. Například aktivační události "na nové tweet" předá tweet obsah do aplikace logiky spustit. 

## <a name="respond-to-triggers-and-extend-actions"></a>Reakce na aktivační události a rozšířit akce

Pro systémy a služby, které nemusí být publikovány konektory můžete také rozšířit aplikace logiky.

* [Vytvořit vlastní aktivační události nebo akce](../logic-apps/logic-apps-create-api-app.md)
* [Nastavit dlouhotrvající akce pro spuštění pracovního postupu](../logic-apps/logic-apps-create-api-app.md)
* [Reakce na externí události a akce s webhooky](../logic-apps/logic-apps-create-api-app.md)
* [Volání, aktivaci nebo vnořit pracovních s synchronní odpovědí na požadavky HTTP](../logic-apps/logic-apps-http-endpoint.md)
* [Kurz: Sestavení používá technologii AI sociálních řídicím v minutách s Logic Apps a Power BI](http://aka.ms/logicappsdemo)
* [Video: Reakce na webhooky Twilio SMS a odeslání text odpovědi](https://channel9.msdn.com/Blogs/Windows-Azure/Azure-Logic-Apps-Walkthrough-Webhook-Functions-and-an-SMS-Bot)

## <a name="control-flow-error-handling-and-logging-capabilities"></a>Tok řízení, zpracování chyb a možnosti protokolování

Aplikace logiky patří bohaté možnosti pro pokročilé řízení toku, jako jsou podmínky, přepínače, smyčky a oborů. Aby odolné řešení, můžete taky implementovat chyb a zpracování výjimek v vaše pracovní postupy. Azure Logic Apps pro oznámení a diagnostické protokoly pro pracovní postup spustit stavu, poskytuje také monitorování a výstrahy.

* Provádění různých akcí na základě [podmíněné příkazy](../logic-apps/logic-apps-control-flow-conditional-statement.md) a [přepínače – příkazy](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Opakujte kroky nebo proces položky v poli a kolekce s smyčky](../logic-apps/logic-apps-control-flow-loops.md)
* [Akce skupiny společně s obory](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* [Chyba autora a zpracování výjimek v pracovním postupu](../logic-apps/logic-apps-exception-handling.md)
* [Případ použití: jak zdravotní péče společnost používá zpracování pro pracovní postupy HL7 FHIR výjimek aplikace logiky](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [Zapnout sledování, protokolování a výstrahy pro existující aplikace logiky](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [Zapnout sledování a protokolování diagnostiky při vytváření aplikací logiky](../logic-apps/logic-apps-monitor-your-logic-apps-oms.md)

## <a name="deploy-and-manage-logic-apps"></a>Nasazení a Správa aplikací logiky

Můžete plně vývoj a nasazení aplikací logiky pomocí sady Visual Studio, Visual Studio Team Services, nebo jakékoli jiné zdrojového kódu a automatizované sestavovací nástroje. Pro podporu nasazení pro pracovní postupy a závislé připojení v šabloně prostředků, pomocí aplikací logiky šablony nasazení prostředků Azure. Nástroje sady Visual Studio automaticky generovat tyto šablony, které můžete vrátit se změnami do správy zdrojového kódu pro správu verzí.

* [Vytvářet a nasazovat aplikace logiky pomocí sady Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Zapnout sledování, protokolování a výstrahy pro existující aplikace logiky](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [Vytvořit šablonu automatického nasazení](../logic-apps/logic-apps-create-deploy-template.md)

## <a name="content-types-conversions-and-transformations-within-a-run"></a>Typy obsahu, převody a transformace v rámci spustit

Můžete používat, převod a transformace více typů obsahu pomocí mnoho funkcí v Azure Logic Apps [jazyk definic workflowů](http://aka.ms/logicappsdocs). Například můžete převést mezi řetězec, JSON a XML s `@json()` a `@xml()` výrazy pracovního postupu. Modul Logic Apps zachovává typy obsahu pro podporu obsahu přenos beze ztrát způsobem mezi službami.

* [Jak fungují výrazy pracovního postupu v aplikace logiky](../logic-apps/logic-apps-author-definitions.md)
* [Zpracování typy obsahu bez JSON](../logic-apps/logic-apps-content-type.md), například `application/xml`, `application/octet-stream`, a `multipart/formdata`
* [Schema Definition Language pracovního postupu pro Azure Logic Apps](http://aka.ms/logicappsdocs)

## <a name="other-integrations-and-capabilities"></a>Další integrace a funkce

Aplikace logiky také nabízí integraci s mnoha služeb, jako jsou funkce Azure, Azure API Management, Azure App Services a vlastní koncové body protokolu HTTP, například REST a protokolu SOAP.

* [Vytvořte v reálném čase sociálních řídicí bez serveru Azure](../logic-apps/logic-apps-scenario-social-serverless.md)
* [Azure Functions volání z aplikace logiky](../logic-apps/logic-apps-azure-functions.md)
* [Kurz: Aktivační událost logiku aplikace s Azure Functions](../logic-apps/logic-apps-scenario-function-sb-trigger.md)
* [Kurz: Sledování změn virtuálního počítače se mřížka událostí Azure a Logic Apps](../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md)
* [Kurz: Vytvoření funkci, která se integruje se službou Azure Logic Apps a kognitivní služby Microsoft analýza sentimentu Twitter post](../azure-functions/functions-twitter-email.md)
* [Kurz: Vzdáleného sledování IoT a oznámení s Azure Logic Apps připojení služby IoT hub a poštovní schránky](../iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps.md)
* [Blog: Volání koncových bodů protokolu SOAP z aplikace logiky](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)

## <a name="end-to-end-scenarios"></a>Kompletní scénáře

* [Dokument White Paper: Začátku do konce případu správu integrace se službami Azure, jako je například Logic Apps](https://aka.ms/enterprise-integration-e2e-case-management-utilities-logic-apps)

## <a name="customer-stories"></a>Příběhy zákazníků

Zjistěte, jak Azure Logic Apps, společně s dalším službám Azure a produkty společnosti Microsoft, pomohl [tyto společnosti](https://aka.ms/logic-apps-customer-stories) zlepšit jejich flexibility a zaměřit se na svého podnikání základní zjednodušit, uspořádání, automatizaci a Orchestrace komplexní procesy.

## <a name="next-steps"></a>Další postup

* [Sestavení v definicích aplikaci logiky s JSON](../logic-apps/logic-apps-author-definitions.md)
* [Zpracování chyb a výjimek v aplikacích logiky](../logic-apps/logic-apps-exception-handling.md)
* [Odesílat dotazy, komentáře, zpětnou vazbu nebo návrhy pro zlepšení Azure Logic Apps](https://feedback.azure.com/forums/287593-logic-apps)
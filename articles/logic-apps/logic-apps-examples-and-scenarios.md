---
title: Příklady & běžných scénářích – Azure Logic Apps
description: Najděte si příklady, běžné scénáře, kurzy a návody pro Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/31/2019
ms.openlocfilehash: 9e245d1a0054d9de5b8b524416c8c92eb02353a7
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2019
ms.locfileid: "73025524"
---
# <a name="common-scenarios-examples-tutorials-and-walkthroughs-for-azure-logic-apps"></a>Běžné scénáře, ukázky, kurzy a návody pro Azure Logic Apps

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) vám pomůže s orchestrací a integrací různých služeb tím, že poskytuje [stovky připravených konektorů](../connectors/apis-list.md), od místních SQL Server nebo SAP až po Azure Cognitive Services. Služba Logic Apps je bez serveru, takže se nemusíte starat o škálování nebo instance. Vše, co musíte udělat, je definovat pracovní postup s triggerem a akcemi, které pracovní postup provádí. Základní platforma zpracovává škálování, dostupnost a výkon. Logic Apps je zvláště užitečné pro případy použití a scénáře, kdy potřebujete koordinovat více akcí v různých systémech.

Další informace o mnoha vzorech a možnostech, které Azure Logic Apps podporuje, najdete v tématu běžné příklady a scénáře.

## <a name="popular-starting-points-for-logic-app-workflows"></a>Populární počáteční body pro pracovní postupy aplikace logiky

Každá aplikace logiky se spouští [*triggerem*](../logic-apps/logic-apps-overview.md#logic-app-concepts)a jenom s jednou triggerem, který spustí pracovní postup aplikace logiky a předá data v rámci této aktivační události. Některé konektory poskytují triggery, které jsou k dispozici v těchto typech:

* *Triggery cyklického dotazování*: pravidelně kontrolují koncový bod služby pro nová data. Když jsou k dispozici nová data, aktivační událost vytvoří a spustí novou instanci pracovního postupu s daty jako vstup.

* *Triggery nabízených oznámení*: naslouchají datům v koncovém bodě služby a čekají, dokud nedojde ke konkrétní události. Když dojde k události, Trigger se spustí hned a vytvoří a spustí novou instanci pracovního postupu, která jako vstup použije všechna dostupná data.

Tady je několik oblíbených příkladů triggerů:

* Dotazování

  * [Trigger **opakování** ](../connectors/connectors-native-recurrence.md) umožňuje nastavit počáteční datum a čas plus opakování pro vypálení aplikace logiky. Můžete například vybrat dny v týdnu a denní dobu, kdy se má aktivovat aplikace logiky. Další informace najdete v těchto tématech:

    * [Plánování a spouštění opakujících se automatizovaných úloh, procesů a pracovních postupů pomocí Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)
    * [Kurz: zjištění provozu podle plánu pomocí Azure Logic Apps](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)

  * Aktivační událost po přijetí e-mailu umožní vaší aplikaci logiky kontrolovat nové e-maily od libovolného poskytovatele pošty, který podporuje Logic Apps, například [Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md), [Gmail](https://docs.microsoft.com/connectors/gmail/), [Outlook.com](https://docs.microsoft.com/connectors/outlook/)atd. Další informace najdete v těchto tématech: 

    * [Kurz: Správa požadavků na seznam adresátů pomocí Azure Logic Apps](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
    * [Kurz: automatizace zpracování e-mailů a příloh pomocí Azure Logic Apps](../logic-apps/tutorial-process-email-attachments-workflow.md)

  * Trigger [ **http** ](../connectors/connectors-native-http.md) umožňuje vaší aplikaci logiky ověřit zadaný koncový bod služby prostřednictvím komunikace přes protokol HTTP.
  
* Replik

  * [Aktivační událost **žádosti** ](../connectors/connectors-native-reqres.md) umožňuje, aby aplikace logiky přijímala požadavky HTTP a reagovala v reálném čase na události.

  * Trigger [ **Webhooku protokolu HTTP** ](../connectors/connectors-native-webhook.md) se přihlásí k odběru koncovému bodu služby registrací *adresy URL zpětného volání* s touto službou. Díky tomu může služba vyvolat Trigger jenom v případě, že dojde k zadané události, takže Trigger nepotřebuje dotazovat službu.

Po přijetí oznámení o nových datech nebo události se Trigger aktivuje, vytvoří novou instanci pracovního postupu aplikace logiky a spustí akce v pracovním postupu. K jakýmkoli datům z triggeru můžete přistupovat v průběhu pracovního postupu. Například aktivační událost "na novém systému" předává obsah v aplikaci logiky do spuštění. Pokud chcete začít s Azure Logic Apps, zkuste tato témata pro rychlý Start:

* [Rychlý Start: vytvoření prvního automatizovaného pracovního postupu pomocí Azure Logic Apps v Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Rychlý Start: vytvoření automatizovaných úloh, procesů a pracovních postupů pomocí Azure Logic Apps pomocí sady Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Rychlý Start: vytvoření a Správa pracovních postupů automatizované aplikace logiky pomocí Visual Studio Code](../logic-apps/quickstart-create-logic-apps-visual-studio-code.md)

## <a name="respond-to-triggers-and-extend-actions"></a>Reakce na triggery a akce rozšiřování

U systémů a služeb, které pravděpodobně nemají publikované konektory, můžete také aplikace Logic Apps zvětšit.

* [Vytváření vlastních aktivačních událostí nebo akcí](../logic-apps/logic-apps-create-api-app.md)
* [Nastavení dlouhotrvajících akcí pro spouštění pracovních postupů](../logic-apps/logic-apps-create-api-app.md)
* [Reakce na externí události a akce pomocí webhooků](../logic-apps/logic-apps-create-api-app.md)
* [Volání, triggery nebo vnořování pracovních postupů s synchronními odpověďmi na požadavky HTTP](../logic-apps/logic-apps-http-endpoint.md)
* [Kurz: Vytvoření řídicího panelu pro sociální zařízení AI během několika minut pomocí Logic Apps a Power BI](https://aka.ms/logicappsdemo)
* [Video: reakce na Twilio Webhooky SMS a odeslání odpovědi na text](https://channel9.msdn.com/Blogs/Windows-Azure/Azure-Logic-Apps-Walkthrough-Webhook-Functions-and-an-SMS-Bot)

## <a name="control-flow-error-handling-and-logging-capabilities"></a>Řízení toku, zpracování chyb a možnosti protokolování

Logic Apps zahrnují bohatých funkcí pro pokročilý tok řízení, jako jsou podmínky, přepínače, smyčky a obory. Chcete-li zajistit odolná řešení, můžete také implementovat zpracování chyb a výjimek v pracovních postupech. Pro oznamování a diagnostické protokoly pro stav spuštění pracovního postupu Azure Logic Apps také nabízí monitorování a výstrahy.

* Provádění různých akcí v závislosti na [podmíněných příkazech](../logic-apps/logic-apps-control-flow-conditional-statement.md) a [příkazech Switch](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Opakování kroků nebo zpracování položek v polích a kolekcích pomocí smyček](../logic-apps/logic-apps-control-flow-loops.md)
* [Seskupit akce společně s obory](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* [Vytváření chyb a zpracování výjimek v pracovním postupu](../logic-apps/logic-apps-exception-handling.md)
* [Případ použití: jak společnost zdravotnictví používá zpracování výjimek aplikace logiky pro pracovní postupy změněného HL7 FHIR](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [Zapnout monitorování, protokolování a výstrahy pro existující aplikace logiky](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [Zapnout monitorování a diagnostické protokolování při vytváření aplikací logiky](../logic-apps/logic-apps-monitor-your-logic-apps-oms.md)

## <a name="deploy-and-manage-logic-apps"></a>Nasazení a Správa aplikací logiky

Můžete plně vyvíjet a nasazovat aplikace logiky pomocí sady Visual Studio, Azure DevOps nebo jakéhokoli jiného nástroje pro správu zdrojového kódu a automatizovaných nástrojů sestavení. Pro podporu nasazení pro pracovní postupy a závislá připojení v šabloně prostředků Logic Apps používá šablony nasazení prostředků Azure. Nástroje sady Visual Studio automaticky generují tyto šablony, které lze vrátit se změnami do správy zdrojového kódu.

* [Vytvoření a nasazení aplikací logiky pomocí sady Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Zapnout monitorování, protokolování a výstrahy pro existující aplikace logiky](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [Automatizace nasazení aplikace logiky](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [Ukázka: připojení k Azure Service Bus frontám z Azure Logic Apps a nasazení pomocí Azure Pipelines ve službě Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Ukázka: připojení k Azure Storage účtům z Azure Logic Apps a nasazení pomocí Azure Pipelines ve službě Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Ukázka: nastavení akce aplikace funkcí pro Azure Logic Apps a nasazení pomocí Azure Pipelines ve službě Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Ukázka: připojení k účtu pro integraci z Azure Logic Apps a nasazení pomocí Azure Pipelines ve službě Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)

## <a name="content-types-conversions-and-transformations-within-a-run"></a>Typy obsahu, převody a transformace v rámci spuštění

Pomocí mnoha funkcí v [jazyce Azure Logic Apps definice pracovního postupu](https://aka.ms/logicappsdocs)můžete získat přístup k více typům obsahu, převést je a transformovat. Například můžete převádět mezi řetězci, JSON a XML pomocí výrazů pracovního postupu `@json()` a `@xml()`. Modul Logic Apps zachovává typy obsahu pro zajištění bezztrátového přenosu obsahu mezi službami.

* [Jak fungují výrazy pracovního postupu v Logic Apps](../logic-apps/logic-apps-author-definitions.md)
* [Zpracovává typy obsahu jiné než JSON](../logic-apps/logic-apps-content-type.md), například `application/xml`, `application/octet-stream`a `multipart/formdata`
* [Schéma jazyka definice pracovního postupu pro Azure Logic Apps](https://aka.ms/logicappsdocs)

## <a name="other-integrations-and-capabilities"></a>Další integrace a možnosti

Logic Apps také nabízejí integraci s mnoha službami, například Azure Functions, Azure API Management, Azure App Services a vlastními koncovými body HTTP, například REST a SOAP.

* [Vytvoření řídicího panelu pro sociální sítě v reálném čase s využitím Azure bez serveru](../logic-apps/logic-apps-scenario-social-serverless.md)
* [Volání Azure Functions z Logic Apps](../logic-apps/logic-apps-azure-functions.md)
* [Kurz: spuštění Logic Apps s Azure Functions](../logic-apps/logic-apps-scenario-function-sb-trigger.md)
* [Kurz: monitorování změn virtuálních počítačů pomocí Azure Event Grid a Logic Apps](../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md)
* [Kurz: vytvoření funkce, která se integruje s Azure Logic Apps a Azure Cognitive Services k analýze služby Twitter post mínění](../azure-functions/functions-twitter-email.md)
* [Kurz: vzdálené monitorování a oznámení IoT pomocí Azure Logic Apps připojení ke službě IoT Hub a poštovní schránce](../iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps.md)
* [Blog: voláním koncových bodů SOAP z Logic Apps](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)

## <a name="end-to-end-scenarios"></a>Kompletní scénáře

* [Dokument White Paper: komplexní integrace správy případů se službami Azure, například Logic Apps](https://aka.ms/enterprise-integration-e2e-case-management-utilities-logic-apps)

## <a name="customer-stories"></a>Případové studie

Přečtěte si, jak Azure Logic Apps společně s dalšími službami Azure a produkty Microsoftu, pomohly [tyto společnosti](https://aka.ms/logic-apps-customer-stories) zlepšit flexibilitu a soustředit se na své základní firmy díky zjednodušení, uspořádání, automatizaci a orchestraci složitých procesů.

## <a name="next-steps"></a>Další kroky

* Další informace o [konektorech pro Logic Apps](../connectors/apis-list.md)
* Přečtěte si o [scénářích integrace B2B Enterprise pomocí Azure Logic Apps](../logic-apps/logic-apps-enterprise-integration-overview.md)

---
title: Příklady & běžných scénářů
description: Najděte příklady, běžné scénáře, kurzy a návody pro Aplikace Logika Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: c64483f18b2deb061704a4eb7171e575fdd561e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164623"
---
# <a name="common-scenarios-examples-tutorials-and-walkthroughs-for-azure-logic-apps"></a>Běžné scénáře, příklady, kurzy a návody pro Azure Logic Apps

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) vám pomůže organizovat a integrovat různé služby tím, že poskytuje [stovky konektorů připravených k použití](../connectors/apis-list.md), od místního SQL Serveru nebo SAP až po služby Azure Cognitive Services. Služba Logic Apps je "bez serveru", takže se nemusíte starat o škálování nebo instance. Jediné, co musíte udělat, je definovat pracovní postup pomocí aktivační události a akcí, které pracovní postup provádí. Základní platforma zpracovává škálování, dostupnost a výkon. Logic Apps je užitečné zejména pro případy použití a scénáře, kde je třeba koordinovat akce napříč více systémy a službami.

Tento článek popisuje běžné výchozí body, příklady a scénáře, které vám pomohou získat informace o možnostech a vzorech, které azure logic apps podporuje.

## <a name="common-starting-points-for-logic-app-workflows"></a>Společné výchozí body pro pracovní postupy aplikace logiky

Každá aplikace logiky začíná [*aktivační událostí*](../logic-apps/logic-apps-overview.md#logic-app-concepts)a pouze jednou aktivační událostí, která spustí pracovní postup aplikace logiky a předá všechna data jako součást této aktivační události. Některé konektory poskytují aktivační události, které přicházejí v těchto typech:

* *Aktivační události dotazování*: Pravidelně kontroluje koncový bod služby pro nová data. Pokud existují nová data, aktivační událost vytvoří a spustí novou instanci pracovního postupu s daty jako vstup.

* *Nabízené aktivační události*: Naslouchá datům v koncovém bodě služby a čeká, dokud nedojde k určité události. Když dojde k události, aktivační událost se spustí okamžitě, vytvoření a spuštění nové instance pracovního postupu, která používá všechna dostupná data jako vstup.

Zde jsou příklady, které popisují běžně používané aktivační události:

* *Aktivační události dotazování:*

  * [Aktivační **událost opakování** ](../connectors/connectors-native-recurrence.md) umožňuje nastavit počáteční datum a čas plus opakování pro spuštění aplikace logiky. Můžete například vybrat dny v týdnu a denní dobu pro aktivaci aplikace logiky. Další informace najdete v těchto tématech:<p>

    * [Plánování a spouštění opakujících se automatizovaných úloh, procesů a pracovních postupů pomocí Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)
    * [Kurz: Vytváření automatických opakovaných pracovních postupů založených na plánu pomocí aplikací Azure Logic Apps](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)

  * Aktivační **událost Při přijetí e-mailu** umožňuje aplikaci logiky zkontrolovat nové e-maily od libovolného poskytovatele pošty, který je podporován logic apps, například Office [365 Outlook](../connectors/connectors-create-api-office365-outlook.md), [Gmail](https://docs.microsoft.com/connectors/gmail/), [Outlook.com](https://docs.microsoft.com/connectors/outlook/)a tak dále. Další informace najdete v těchto tématech:<p>

    * [Kurz: Vytváření automatických pracovních postupů založených na schválení pomocí aplikací Azure Logic Apps](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
    * [Kurz: Automatizace úloh pro zpracování e-mailů pomocí Azure Logic Apps, Azure Functions a Azure Storage](../logic-apps/tutorial-process-email-attachments-workflow.md)

  * Aktivační událost [ **HTTP** ](../connectors/connectors-native-http.md) může volat koncový bod služby přes PROTOKOL HTTP nebo HTTPS. Další informace naleznete v tématu [Volání, aktivační události nebo vnoření pracovních postupů pomocí koncových bodů HTTP](../logic-apps/logic-apps-http-endpoint.md).

* *Push* spouště:

  * Aktivační [ **událost požadavku** ](../connectors/connectors-native-reqres.md) může přijímat příchozí požadavky HTTPS.

  * Aktivační událost [ **HTTP Webhook** ](../connectors/connectors-native-webhook.md) se přihlásí ke koncovému bodu služby registrací *adresy URL zpětného volání* s tou službou. Tímto způsobem služba může pouze upozornit aktivační událost, když dojde k zadané události, takže aktivační událost nemusí dotazovat službu.

Po zadaný událost se stane, aktivační událost požáry, která vytvoří novou instanci pracovního postupu aplikace logiky a spustí akce v pracovním postupu. V celém pracovním postupu můžete přistupovat k libovolným datům z aktivační události. Například twitter **na nový tweet** aktivační událost předá obsah pípání do spuštění aplikace logiky. Pokud chcete začít s Azure Logic Apps, vyzkoušejte tato témata pro rychlý start:

* [Úvodní příručka: Vytvořte svůj první automatizovaný pracovní postup pomocí Azure Logic Apps – portál Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Úvodní příručka: Vytváření automatizovaných úloh, procesů a pracovních postupů pomocí Azure Logic Apps – Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Úvodní příručka: Vytvoření a správa pracovních postupů automatických aplikací logiky pomocí kódu Visual Studia](../logic-apps/quickstart-create-logic-apps-visual-studio-code.md)

## <a name="control-flow-and-error-handling-capabilities"></a>Možnosti řízení toku a zpracování chyb

Aplikace logiky zahrnují bohaté funkce pro pokročilý tok řízení, jako jsou podmínky, přepínače, smyčky a obory. Chcete-li zajistit odolná řešení, můžete také implementovat zpracování chyb a výjimek ve vašich pracovních postupech.

* Provádění různých akcí založených na [podmíněných příkazech](../logic-apps/logic-apps-control-flow-conditional-statement.md) a [příkazech switch](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Opakování kroků nebo zpracování položek v polích a kolekcích se smyčkami](../logic-apps/logic-apps-control-flow-loops.md)
* [Akce skupiny společně s obory](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* [Přidání zpracování chyb a výjimek do pracovního postupu](../logic-apps/logic-apps-exception-handling.md)
* [Případ použití: Jak zdravotnická společnost používá zpracování výjimek aplikace logiky pro pracovní postupy HL7 FHIR](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)

## <a name="create-custom-apis-and-connectors"></a>Vytvoření vlastních rozhraní API a konektorů

Pro systémy a služby, které nemají publikované konektory, můžete také rozšířit aplikace logiky.

* [Vytvoření vlastních api pro volání z aplikací Azure Logic Apps](../logic-apps/logic-apps-create-api-app.md)
* [Pravidelně kontrolujte nová data nebo události pomocí vzoru aktivační události dotazování](../logic-apps/logic-apps-create-api-app.md#polling-triggers)
* [Čekání a naslouchání novým datům nebo událostem pomocí spouštěcího vzoru webhooku](../logic-apps/logic-apps-create-api-app.md#webhook-triggers)
* [Provádění dlouhých běžících úloh pomocí vzoru akce dotazování](../logic-apps/logic-apps-create-api-app.md#async-pattern)
* [Provádění dlouhotrvajících úloh pomocí vzoru akce webhooku](../logic-apps/logic-apps-create-api-app.md#webhook-actions)
* [Vlastní konektory v aplikacích Azure Logic Apps](../logic-apps/custom-connector-overview.md)

## <a name="build-business-to-business-b2b-solutions"></a>Vytváření řešení mezi podniky (B2B)

Pro řešení podnikové integrace a bezproblémovou komunikaci mezi organizacemi můžete vytvářet automatizované škálovatelné pracovní postupy pro tyto scénáře pomocí sady Enterprise Integration Pack (EIP) s Azure Logic Apps. Přestože organizace používají různé protokoly a formáty, mohou si vyměňovat zprávy elektronicky. EIP transformuje různé formáty do formátu, který mohou systémy vašich organizací zpracovávat a podporuje standardní protokoly, včetně AS2, X12, EDIFACT a RosettaNet. Chcete-li vytvořit tato řešení, vytvořte účet integrace, což je samostatný prostředek Azure, který poskytuje zabezpečený, škálovatelný a spravovatelný kontejner pro artefakty, které definujete a používáte s pracovními postupy aplikace logiky. Artefakty zahrnují například obchodní partnery, smlouvy, mapy, schémata, certifikáty a dávkové konfigurace.

* [Přehled: Řešení pro podnikovou integraci B2B s Azure Logic Apps a Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Vytváření a správa účtů integrace pro podnikové integrace B2B v Aplikacích Azure Logic Apps](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

## <a name="access-azure-virtual-network-resources"></a>Přístup k prostředkům virtuální sítě Azure

Vaše aplikace logiky a účty integrace někdy potřebují přístup k zabezpečeným prostředkům, jako jsou virtuální počítače (VM) a další systémy nebo služby, které jsou ve virtuální síti Azure. Chcete-li nastavit tento přístup, můžete vytvořit prostředí služby integrace (ISE), kde můžete vytvářet a spouštět aplikace logiky. ISE je soukromá a izolovaná instance služby Logic Apps, která používá vyhrazené prostředky, jako je například úložiště, a běží odděleně od veřejné, "globální" služby Logic Apps s více tenanty. Oddělení izolované soukromé instance a veřejné globální instance také pomáhá snížit dopad, který mohou mít ostatní klienti Azure na výkon vašich aplikací, což se také označuje jako efekt "hlučných sousedů".

* [Přehled: Přístup k prostředkům virtuální sítě Azure z Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)
* [Připojení k virtuálním sítím Azure z Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

## <a name="deploy-manage-and-monitor-logic-apps"></a>Nasazení, správa a monitorování aplikací logiky

Aplikace logiky můžete plně vyvíjet a nasazovat pomocí Visual Studia, Azure DevOps nebo jiných nástrojů pro správu zdrojového kódu a automatizovaných nástrojů sestavení. Pro podporu nasazení pro pracovní postupy a závislá připojení v šabloně prostředků používají aplikace logiky šablony nasazení prostředků Azure. Nástroje sady Visual Studio automaticky generují tyto šablony, které můžete se změnami pro správu zdrojového kódu pro správu verzí. Pro protokoly oznámení a diagnostiky pro stav spuštění pracovního postupu Azure Logic Apps také poskytuje monitorování a výstrahy.

### <a name="deploy"></a>Nasazení

* [Úvodní příručka: Vytváření automatizovaných úloh, procesů a pracovních postupů pomocí Azure Logic Apps – Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Přehled: Automatizace nasazení aplikace logiky](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [Vytvoření šablon Azure Resource Managerpro automatizaci nasazení pro Azure Logic Apps](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)
* [Nasazení šablon Azure Resource Manageru pro aplikace Azure Logic Apps](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)
* [Ukázka: Připojení k frontám Azure Service Bus z Azure Logic Apps a nasazení s Azure Pipelines v Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Ukázka: Připojení k účtům Azure Storage z Azure Logic Apps a nasazení s Azure Pipelines v Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Ukázka: Nastavení akce aplikace funkce pro Azure Logic Apps a nasazení s Azure Pipelines v Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Ukázka: Připojení k účtu integrace z Azure Logic Apps a nasazení s Azure Pipelines v Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)
* [Ukázka: Orchestrace Azure Pipelines pomocí Azure Logic Apps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-pipeline-orchestration/azure-devops-orchestration-with-logic-apps/)

### <a name="manage"></a>Správa

* [Správa aplikací logiky pomocí Visual Studia](../logic-apps/manage-logic-apps-with-visual-studio.md)
* [Vytvoření a správa účtů integrace pro podnikové integrace B2B](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)
* [Správa prostředí integračních služeb (ISE) v Aplikacích Azure Logic Apps](../logic-apps/ise-manage-integration-service-environment.md)

### <a name="monitor"></a>Monitorování

* [Sledování stavu spuštění, kontrola historie aktivačních událostí a nastavení výstrah pro aplikace Azure Logic Apps](../logic-apps/monitor-logic-apps.md)
* [Nastavení protokolů Azure Monitoru a shromažďování diagnostických dat pro aplikace Azure Logic Apps](../logic-apps/monitor-logic-apps-log-analytics.md)
* [Nastavení protokolů Azure Monitoru a shromažďování diagnostických dat pro b2B zprávy v Aplikacích Azure Logic Apps](../logic-apps/monitor-b2b-messages-log-analytics.md)
* [Zobrazení a vytváření dotazů pro monitorování a sledování v protokolech Azure Monitoru pro Azure Logic Apps](../logic-apps/create-monitoring-tracking-queries.md)

## <a name="handle-content-types-conversions-and-transformations"></a>Zpracování typů obsahu, konverzí a transformací

Můžete přistupovat, převést a transformovat více typů obsahu pomocí mnoha funkcí v [jazyce definice pracovního postupu](https://aka.ms/logicappsdocs)Azure Logic Apps . Můžete například převést mezi řetězcem, Jazykem JSON a XML pomocí výrazů `@json()` pracovního `@xml()` postupu. Modul Logic Apps zachová typy obsahu pro podporu přenosu obsahu bezztrátovým způsobem mezi službami.

* [Zpracování typů obsahu v Aplikacích Azure Logic ,](../logic-apps/logic-apps-content-type.md)jako `application/`jsou aplikace `application/octet-stream`,`multipart/formdata`
* [Referenční příručka pro používání funkcí ve výrazech pro Azure Logic Apps a Power Automate](../logic-apps/workflow-definition-language-functions-reference.md)
* [Schéma jazyka definice pracovního postupu pro aplikace Logiky Azure](../logic-apps/logic-apps-workflow-definition-language.md)

## <a name="other-integrations-and-capabilities"></a>Další integrace a možnosti

Azure Logic Apps se integruje s mnoha službami, jako jsou Azure Functions, Azure API Management, Azure App Service a vlastní koncové body HTTP, například REST a SOAP.

* [Volání funkcí Azure z aplikací Azure Logic Apps](../logic-apps/logic-apps-azure-functions.md)
* [Kurz: Volání nebo aktivace logických aplikací pomocí Azure Functions a Azure Service Bus](../logic-apps/logic-apps-scenario-function-sb-trigger.md)
* [Kurz: Vytvoření řídicího panelu přehledů zákazníků streamování pomocí aplikací Azure Logic Apps a funkcí Azure](../logic-apps/logic-apps-scenario-social-serverless.md)
* [Kurz: Vytvořte funkci, která se integruje s Azure Logic Apps a Azure Cognitive Services pro analýzu mínění příspěvku na Twitteru](../azure-functions/functions-twitter-email.md)
* [Kurz: Vytvoření sociálního řídicího panelu s umělou intelidou pomocí Power BI a Azure Logic Apps](https://aka.ms/logicappsdemo)
* [Kurz: Sledování změn virtuálních strojů pomocí Azure Event Grid a Logic Apps](../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md)
* [Kurz: Vzdálené monitorování ioT a oznámení pomocí aplikací Azure Logic Apps, které propojují vaše centrum IoT hub a poštovní schránku](../iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps.md)
* [Blog: Volání služeb SOAP pomocí aplikací Azure Logic Apps](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)

## <a name="end-to-end-scenarios"></a>Kompletní scénáře

* [Whitepaper: Integrace komplexní správy případů se službami Azure, jako jsou Logic Apps](https://aka.ms/enterprise-integration-e2e-case-management-utilities-logic-apps)

## <a name="customer-stories"></a>Příběhy zákazníků

Zjistěte, jak Azure Logic Apps spolu s dalšími službami Azure a produkty Microsoftu pomohly [těmto společnostem](https://aka.ms/logic-apps-customer-stories) zlepšit jejich flexibilitu a zaměřit se na své hlavní podnikání zjednodušením, uspořádáním, automatizací a orchestrací složitých procesů.

## <a name="next-steps"></a>Další kroky

* Další informace o [konektorech pro logic aplikace](../connectors/apis-list.md)
* Další informace o [scénářích podnikové integrace B2B pomocí Aplikací Logika Azure](../logic-apps/logic-apps-enterprise-integration-overview.md)

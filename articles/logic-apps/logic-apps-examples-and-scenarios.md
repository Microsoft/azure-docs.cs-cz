---
title: Příklady & běžných scénářů
description: Najděte si příklady, běžné scénáře, kurzy a návody pro Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: 5f7afe044ab4f782f2028598c2c56e75edf95860
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92317377"
---
# <a name="common-scenarios-examples-tutorials-and-walkthroughs-for-azure-logic-apps"></a>Běžné scénáře, ukázky, kurzy a návody pro Azure Logic Apps

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) vám pomůže s orchestrací a integrací různých služeb tím, že poskytuje [stovky připravených konektorů](../connectors/apis-list.md)od SQL Server nebo SAP až po Azure Cognitive Services. Služba Logic Apps je bez serveru, takže se nemusíte starat o škálování nebo instance. Vše, co musíte udělat, je definovat pracovní postup s triggerem a akcemi, které pracovní postup provádí. Základní platforma zpracovává škálování, dostupnost a výkon. Logic Apps je zvláště užitečné pro případy použití a scénáře, kdy potřebujete koordinovat akce napříč různými systémy a službami.

V tomto článku se dozvíte o schopnostech a vzorech, které Azure Logic Apps podporuje. Tento článek popisuje běžné úvodní body, příklady a scénáře.

## <a name="common-starting-points-for-logic-app-workflows"></a>Běžné počáteční body pro pracovní postupy aplikace logiky

Každá aplikace logiky se spouští [*triggerem*](../logic-apps/logic-apps-overview.md#logic-app-concepts)a jenom s jednou triggerem, který spustí pracovní postup aplikace logiky a předá data v rámci této aktivační události. Některé konektory poskytují triggery, které jsou k dispozici v těchto typech:

* *Triggery cyklického dotazování*: pravidelně kontrolují koncový bod služby pro nová data. Když jsou k dispozici nová data, aktivační událost vytvoří a spustí novou instanci pracovního postupu s daty jako vstup.

* *Triggery nabízených oznámení*: naslouchají datům v koncovém bodě služby a čekají, dokud nedojde ke konkrétní události. Když dojde k události, Trigger se spustí hned a vytvoří a spustí novou instanci pracovního postupu, která jako vstup použije všechna dostupná data.

Tady jsou příklady, které popisují běžně používané triggery:

* Triggery *cyklického dotazování* :

  * [Trigger **opakování**](../connectors/connectors-native-recurrence.md) umožňuje nastavit počáteční datum a čas plus opakování pro vypálení aplikace logiky. Můžete například vybrat dny v týdnu a denní dobu, kdy se má aktivovat aplikace logiky. Další informace najdete v těchto tématech:<p>

    * [Plánování a spouštění opakujících se automatizovaných úloh, procesů a pracovních postupů pomocí Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)
    * [Kurz: vytvoření automatických opakovaných pracovních postupů založených na plánu pomocí Azure Logic Apps](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)

  * Aktivační událost **po přijetí e-mailu** umožní vaší aplikaci logiky kontrolovat nové e-maily od libovolného poskytovatele pošty, který podporuje Logic Apps, například [Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md), [Gmail](/connectors/gmail/), [Outlook.com](/connectors/outlook/)atd.

    > [!IMPORTANT]
    > Pokud chcete použít konektor Gmail, můžou tento konektor používat jenom obchodní účty G-Suite bez omezení v Logic Apps. Pokud máte účet příjemce Gmail, můžete tento konektor použít jenom pro konkrétní služby schválené v Google, nebo můžete [vytvořit klientskou aplikaci Google pro ověřování pomocí konektoru Gmail](/connectors/gmail/#authentication-and-bring-your-own-application). Další informace najdete v tématu [zásady zabezpečení a ochrany osobních údajů pro konektory Google v Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md).

    Další informace najdete v těchto tématech:<p>

    * [Kurz: vytváření pracovních postupů pro automatizované schvalování pomocí Azure Logic Apps](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
    * [Kurz: automatizace úloh pro zpracování e-mailů pomocí Azure Logic Apps, Azure Functions a Azure Storage](../logic-apps/tutorial-process-email-attachments-workflow.md)

  * [Aktivační procedura **http**](../connectors/connectors-native-http.md) může zavolat koncový bod služby přes protokol HTTP nebo HTTPS. Další informace najdete v tématu [pracovní postupy volání, triggeru nebo vnořování pomocí koncových bodů http](../logic-apps/logic-apps-http-endpoint.md).

* Aktivační události *nabízených oznámení* :

  * [Aktivační událost **žádosti**](../connectors/connectors-native-reqres.md) může přijímat příchozí požadavky HTTPS.

  * Trigger [ **Webhooku protokolu HTTP**](../connectors/connectors-native-webhook.md) se přihlásí k odběru koncovému bodu služby registrací *adresy URL zpětného volání* s touto službou. Díky tomu může služba vyvolat Trigger jenom v případě, že dojde k zadané události, takže Trigger nepotřebuje dotazovat službu.

Jakmile dojde k zadané události, Trigger se aktivuje, čímž se vytvoří nová instance pracovního postupu aplikace logiky a spustí akce v pracovním postupu. K jakýmkoli datům z triggeru můžete přistupovat v průběhu pracovního postupu. Například Twitter **na novém triggeru v systému** se předá do spuštění aplikace logiky. Pokud chcete začít s Azure Logic Apps, zkuste tato témata pro rychlý Start:

* [Rychlý Start: vytvoření prvního automatizovaného pracovního postupu pomocí Azure Logic Apps-Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Rychlý Start: vytváření automatizovaných úloh, procesů a pracovních postupů pomocí Azure Logic Apps – Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Rychlý Start: vytvoření a Správa pracovních postupů automatizované aplikace logiky pomocí Visual Studio Code](../logic-apps/quickstart-create-logic-apps-visual-studio-code.md)

## <a name="control-flow-and-error-handling-capabilities"></a>Možnosti toku řízení a zpracování chyb

Logic Apps obsahují bohatých funkcí pro pokročilý tok řízení, jako jsou podmínky, přepínače, smyčky a obory. Chcete-li zajistit odolná řešení, můžete také implementovat zpracování chyb a výjimek v pracovních postupech.

* Provádění různých akcí v závislosti na [podmíněných příkazech](../logic-apps/logic-apps-control-flow-conditional-statement.md) a [příkazech Switch](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Opakování kroků nebo zpracování položek v polích a kolekcích pomocí smyček](../logic-apps/logic-apps-control-flow-loops.md)
* [Seskupit akce společně s obory](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* [Přidání zpracování chyb a výjimek do pracovního postupu](../logic-apps/logic-apps-exception-handling.md)
* [Případ použití: jak společnost zdravotnictví používá zpracování výjimek aplikace logiky pro pracovní postupy změněného HL7 FHIR](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)

## <a name="create-custom-apis-and-connectors"></a>Vytváření vlastních rozhraní API a konektorů

U systémů a služeb, které nemají publikované konektory, můžete také využívat Logic Apps.

* [Vytvoření vlastních rozhraní API pro volání z Azure Logic Apps](../logic-apps/logic-apps-create-api-app.md)
* [Pravidelné kontroly nových dat nebo událostí pomocí vzoru triggeru cyklického dotazování](../logic-apps/logic-apps-create-api-app.md#polling-triggers)
* [Vyčkejte a poslouchejte nová data nebo události pomocí vzoru triggeru Webhooku.](../logic-apps/logic-apps-create-api-app.md#webhook-triggers)
* [Provádění dlouhodobě spuštěných úloh pomocí vzoru akce cyklického dotazování](../logic-apps/logic-apps-create-api-app.md#async-pattern)
* [Provádění dlouhotrvajících úloh pomocí vzoru akce Webhooku](../logic-apps/logic-apps-create-api-app.md#webhook-actions)
* [Vlastní konektory v Azure Logic Apps](../logic-apps/custom-connector-overview.md)

## <a name="build-business-to-business-b2b-solutions"></a>Vytváření řešení B2B (Business-to-Business)

V případě řešení pro podnikovou integraci a bezproblémové komunikace mezi organizacemi můžete vytvářet automatizované škálovatelné pracovní postupy pro tyto scénáře pomocí Enterprise Integration Pack (EIP) s Azure Logic Apps. I když organizace používají různé protokoly a formáty, můžou vyměňovat zprávy elektronicky. EIP transformuje různé formáty do formátu, který mohou systémy vaší organizace zpracovávat, a podporuje standardní protokoly, včetně AS2, X12, EDIFACT a RosettaNet. K sestavení těchto řešení vytvoříte účet pro integraci, což je samostatný prostředek Azure, který poskytuje zabezpečený, škálovatelný a spravovatelný kontejner pro artefakty, které definujete a používáte s pracovními postupy aplikace logiky. Mezi artefakty patří například obchodní partneři, smlouvy, mapy, schémata, certifikáty a konfigurace služby Batch.

* [Přehled: řešení B2B Enterprise Integration s Azure Logic Apps a Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Vytváření a správa účtů integrace pro podnikové integrace B2B v Azure Logic Apps](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

## <a name="access-azure-virtual-network-resources"></a>Přístup k prostředkům virtuální sítě Azure

V některých případech vaše aplikace logiky a účty pro integraci potřebují přístup k zabezpečeným prostředkům, jako jsou virtuální počítače (VM) a další systémy nebo služby, které jsou ve virtuální síti Azure. K nastavení tohoto přístupu můžete vytvořit prostředí ISE (Integration Service Environment), kde můžete vytvářet a spouštět aplikace logiky. ISE je soukromá a izolovaná instance služby Logic Apps, která využívá vyhrazené prostředky, jako je úložiště, a spouští se samostatně z veřejné služby Logic Apps s více klienty. Oddělení vaší izolované privátní instance a veřejné globální instance také pomáhá snižovat dopad, který mohou mít jiní klienti Azure na výkon vašich aplikací, což se označuje také jako "nepříznivých sousedních".

* [Přehled: přístup k prostředkům virtuální sítě Azure z Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)
* [Připojení k virtuálním sítím Azure z Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

## <a name="deploy-manage-and-monitor-logic-apps"></a>Nasazení, Správa a monitorování aplikací logiky

Můžete plně vyvíjet a nasazovat aplikace logiky pomocí sady Visual Studio, Azure DevOps nebo jakéhokoli jiného nástroje pro správu zdrojového kódu a automatizovaných nástrojů sestavení. Pro podporu nasazení pro pracovní postupy a závislá připojení v šabloně prostředků Logic Apps používá šablony nasazení prostředků Azure. Nástroje sady Visual Studio automaticky generují tyto šablony, které lze vrátit se změnami do správy zdrojového kódu. Pro oznamování a diagnostické protokoly pro stav spuštění pracovního postupu Azure Logic Apps také nabízí monitorování a výstrahy.

### <a name="deploy"></a>Nasadit

* [Rychlý Start: vytváření automatizovaných úloh, procesů a pracovních postupů pomocí Azure Logic Apps – Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Přehled: Automatizace nasazení aplikace logiky](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [Vytváření šablon Azure Resource Manageru pro automatizaci nasazení pro Azure Logic Apps](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)
* [Nasazování šablon Azure Resource Manageru pro Azure Logic Apps](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)
* [Ukázka: připojení k Azure Service Bus frontám z Azure Logic Apps a nasazení pomocí Azure Pipelines ve službě Azure DevOps](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Ukázka: připojení k Azure Storage účtům z Azure Logic Apps a nasazení pomocí Azure Pipelines ve službě Azure DevOps](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Ukázka: nastavení akce aplikace funkcí pro Azure Logic Apps a nasazení pomocí Azure Pipelines ve službě Azure DevOps](/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Ukázka: připojení k účtu pro integraci z Azure Logic Apps a nasazení pomocí Azure Pipelines ve službě Azure DevOps](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)
* [Ukázka: orchestrace Azure Pipelines pomocí Azure Logic Apps](/samples/azure-samples/azure-logic-apps-pipeline-orchestration/azure-devops-orchestration-with-logic-apps/)

### <a name="manage"></a>Spravovat

* [Správa aplikací logiky pomocí sady Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)
* [Vytváření a Správa účtů pro integraci pro podniková integrace B2B](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)
* [Správa prostředí ISE (Integration Service Environment) v Azure Logic Apps](../logic-apps/ise-manage-integration-service-environment.md)

### <a name="monitor"></a>Monitor

* [Monitorování stavu spuštění, zobrazení historie aktivačních událostí a nastavení upozornění pro Azure Logic Apps](../logic-apps/monitor-logic-apps.md)
* [Nastavení protokolů Azure Monitor a shromažďování diagnostických dat pro Azure Logic Apps](../logic-apps/monitor-logic-apps-log-analytics.md)
* [Nastavení protokolů služby Azure Monitor a shromažďování diagnostických dat o zprávách B2B v Azure Logic Apps](../logic-apps/monitor-b2b-messages-log-analytics.md)
* [Zobrazení a vytváření dotazů pro monitorování a sledování v protokolech Azure Monitor pro Azure Logic Apps](../logic-apps/create-monitoring-tracking-queries.md)

## <a name="handle-content-types-conversions-and-transformations"></a>Zpracování typů obsahu, převodů a transformací

Pomocí mnoha funkcí v [jazyce Azure Logic Apps definice pracovního postupu](./logic-apps-workflow-definition-language.md)můžete získat přístup k více typům obsahu, převést je a transformovat. Například můžete převádět mezi řetězci, JSON a XML pomocí `@json()` `@xml()` výrazů pracovního postupu and. Modul Logic Apps zachovává typy obsahu pro zajištění bezztrátového přenosu obsahu mezi službami.

* [Zpracování typů obsahu v Azure Logic Apps](../logic-apps/logic-apps-content-type.md), například `application/` , `application/octet-stream` a `multipart/formdata`
* [Referenční příručka k používání funkcí ve výrazech pro Azure Logic Apps a automatizaci](../logic-apps/workflow-definition-language-functions-reference.md)
* [Schéma jazyka definice pracovního postupu pro Azure Logic Apps](../logic-apps/logic-apps-workflow-definition-language.md)

## <a name="other-integrations-and-capabilities"></a>Další integrace a možnosti

Azure Logic Apps se integruje s mnoha službami, například Azure Functions, Azure API Management, Azure App Service a vlastními koncovými body HTTP, například REST a SOAP.

* [Volat Azure Functions z Azure Logic Apps](../logic-apps/logic-apps-azure-functions.md)
* [Kurz: volání nebo Aktivace Logic Apps pomocí Azure Functions a Azure Service Bus](../logic-apps/logic-apps-scenario-function-sb-trigger.md)
* [Kurz: Vytvoření řídicího panelu pro streamování zákaznických přehledů pomocí Azure Logic Apps a Azure Functions](../logic-apps/logic-apps-scenario-social-serverless.md)
* [Kurz: vytvoření funkce, která se integruje s Azure Logic Apps a Azure Cognitive Services k analýze služby Twitter post mínění](../azure-functions/functions-twitter-email.md)
* [Kurz: Vytvoření řídicího panelu pro sociální zařízení AI pomocí Power BI a Azure Logic Apps](https://aka.ms/logicappsdemo)
* [Kurz: Monitorování změn virtuálních počítačů s využitím služeb Azure Event Grid a Logic Apps](../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md)
* [Kurz: vzdálené monitorování a oznámení IoT pomocí Azure Logic Apps připojení ke službě IoT Hub a poštovní schránce](../iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps.md)
* [Blog: volejte služby SOAP pomocí Azure Logic Apps](/archive/blogs/logicapps/using-soap-services-with-logic-apps)

## <a name="end-to-end-scenarios"></a>Kompletní scénáře

* [Dokument White Paper: komplexní integrace správy případů se službami Azure, například Logic Apps](https://aka.ms/enterprise-integration-e2e-case-management-utilities-logic-apps)

## <a name="customer-stories"></a>Příběhy zákazníků

Přečtěte si, jak Azure Logic Apps společně s dalšími službami Azure a produkty Microsoftu, pomohly [tyto společnosti](https://aka.ms/logic-apps-customer-stories) zlepšit flexibilitu a soustředit se na své základní firmy díky zjednodušení, uspořádání, automatizaci a orchestraci složitých procesů.

## <a name="next-steps"></a>Další kroky

* Další informace o [konektorech pro Logic Apps](../connectors/apis-list.md)
* Přečtěte si o [scénářích integrace B2B Enterprise pomocí Azure Logic Apps](../logic-apps/logic-apps-enterprise-integration-overview.md)
---
title: Scénář bez serveru – vytvořit řídicí panel přehledů o zákazníka se službami Azure | Dokumentace Microsoftu
description: Správa zpětné vazby od zákazníků, sociálních médií, data a využívat další vytvořením řídicího panelu zákazníků s Azure Logic Apps a Azure Functions
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: jeffhollan
ms.author: jehollan
ms.reviewer: estfan, LADocs
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.topic: article
ms.date: 03/15/2018
ms.openlocfilehash: 6ba274bb3ff3679b4a44950db168215c54f3ade6
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2018
ms.locfileid: "44299742"
---
# <a name="create-streaming-customer-insights-dashboard-with-azure-logic-apps-and-azure-functions"></a>Vytvoření datových proudů řídicí panel přehledů o zákazníků s Azure Logic Apps a Azure Functions

Azure nabízí nástroje bez serverů, které vám pomůžou rychle sestavování a hostování aplikací v cloudu, aniž byste museli uvažovat o infrastrukturu. V tomto kurzu můžete vytvořit řídicí panel, který aktivuje na zpětnou vazbu od zákazníků, zpětné vazby pomocí strojového učení analyzuje a publikuje insights ke zdroji, jako je například Power BI nebo Azure Data Lake.

Pro tohle řešení použijete tyto klíčové komponenty Azure pro aplikace bez serveru: [Azure Functions](https://azure.microsoft.com/services/functions/) a [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/).
Služba Azure Logic Apps poskytuje modul pracovních postupů bez serveru v cloudu, aby mohli k vytváření orchestrací komponentami bez serveru a připojení k více než 200 služeb a rozhraní API. Služba Azure Functions poskytuje architekturu bez serverů v cloudu. Toto řešení používá k nastavení příznaku zákazníka tweety na základě předdefinovaných klíčových slov pro Azure Functions.

V tomto scénáři vytvoříte aplikaci logiky, která aktivuje na hledání zpětnou vazbu od zákazníků. Některé konektory, zahrnují pomoc reagovat na názory zákazníků služby Outlook.com, Office 365, opic průzkumu, Twitter a [požadavku HTTP z webového formuláře](https://blogs.msdn.microsoft.com/logicapps/2017/01/30/calling-a-logic-app-from-an-html-form/). Pracovní postup, který vytvoříte monitoruje hashtagu na Twitteru.

Je možné [sestavte celé řešení v sadě Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) a [řešení můžete nasadit pomocí šablony Azure Resource Manageru](../logic-apps/logic-apps-create-deploy-template.md). Video návod, který ukazuje, jak vytvořit toto řešení [v tomto videu Channel 9](http://aka.ms/logicappsdemo). 

## <a name="trigger-on-customer-data"></a>Aktivovat podle zákaznická data

1. Webu Azure portal nebo Visual Studio vytvořte prázdné aplikace logiky. 

   Pokud se službou logic Apps teprve začínáte, přečtěte si [rychlý start pro Azure portal](../logic-apps/quickstart-create-first-logic-app-workflow.md) nebo [rychlý start pro Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

2. V návrháři aplikace logiky, najděte a přidejte trigger Twitteru, který má tuto akci: **při publikuje nový tweet**

3. Nastavte aktivační událost pro naslouchání tweety na základě – klíčové slovo nebo hashtagem.

   Na základě dotazování triggery, třeba trigger Twitteru vlastnost opakování určuje, jak často aplikace logiky kontrolovat nové položky.

   ![Příklad trigger Twitteru][1]

Tato aplikace logiky je teď vyvoláno na všechny nové tweety. Můžete pak provést a analyzovat data tweetu, tak, aby lépe porozumíte zabarvení vyjádřit. 

## <a name="analyze-tweet-text"></a>Analyzovat text tweetu

K rozpoznávání mínění za nějaký text, můžete použít [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/).

1. V návrháři aplikace logiky, pod triggerem zvolte **nový krok**.

2. Najít **rozhraní Text Analytics** konektoru.

3. Vyberte **zjistit mínění** akce.

4. Pokud se zobrazí výzva, zadejte platný klíč pro analýzu textu služby Cognitive Services.

5. V části **tělo požadavku**, vyberte **Text Tweetu** pole, které poskytuje text tweetu jako vstup pro analýzu.

Po získání tweet dat a přehledů o daném tweetu, můžete nyní použít několik dalších relevantní konektory a jejich akce:

* **Power BI – přidání řádků do datové sady streamování**: Zobrazit příchozí tweety na řídicí panel Power BI.
* **Azure Data Lake – připojit soubor**: Přidat zákaznická data k datové sadě služby Azure Data Lake mají být zahrnuty úlohy analýzy.
* **SQL – přidání řádků**: Store data do databáze pro pozdější načtení.
* **Slack – poslat zprávu**: oznámení o negativní zpětnou vazbu, které můžou vyžadovat akci kanál Slack.

Můžete také vytvořit a funkce Azure tak, aby vlastní zpracování můžete provést s vašimi daty. 

## <a name="process-data-with-azure-functions"></a>Zpracování dat pomocí Azure Functions

Než vytvoříte funkci, vytvořte aplikaci function app ve vašem předplatném Azure. Pro svou aplikaci logiky přímo volat funkci, funkce musí mít také HTTP aktivovat vazby, například, použijte **HttpTrigger** šablony. Přečtěte si [vytvoření vaší první aplikace function app a funkce na webu Azure Portal](../azure-functions/functions-create-first-azure-function-azure-portal.md).

V tomto scénáři použijte text tweetu jako datovou část požadavku pro vaši funkci Azure functions. V kódu funkce definujte logiku, která určuje, zda text tweetu obsahuje klíčové slovo nebo frázi. Nechte funkci jako jednoduché nebo složité podle potřeby pro scénář.
Na konci funkce vrátí odpověď aplikace logiky s daty, třeba, jednoduchý logická hodnota jako například `containsKeyword` nebo komplexního objektu.

> [!TIP]
> Chcete-li získat přístup k komplexní odpovědi z funkce v aplikaci logiky, použijte **Parsovat JSON** akce.

Jakmile budete hotovi, uložte funkci a pak přidejte funkce jako akci v aplikaci logiky, kterou vytváříte.

## <a name="add-azure-function-to-logic-app"></a>Přidat funkci Azure pro aplikace logiky

1. V návrháři aplikace logiky v části **zjistit mínění** akce, zvolte **nový krok**.

2. Najít **Azure Functions** konektoru a pak vyberte funkce, kterou jste vytvořili.

3. V části **tělo požadavku**vyberte **Text Tweetu**.

![Nakonfigurované funkce Azure Functions kroku][2]

## <a name="run-and-monitor-your-logic-app"></a>Spuštění a monitorování aplikace logiky

Zkontrolovat všechny aktuální nebo předchozí běhy pro vaši aplikaci logiky, můžete využívat bohatá ladění a monitorování funkce, které poskytuje Azure Logic Apps na webu Azure Portal, Visual Studio, nebo prostřednictvím rozhraní Azure REST API a sad SDK.

Snadno testovat svou aplikaci logiky v návrháři aplikace logiky, vyberte **spuštění aktivační události**. Aktivační událost se dotazuje na tweety podle zadaného plánu, dokud nebude nalezen tweet, který splňuje vaše kritéria. V návrháři se zobrazí při spuštění postupuje okamžitým efektem příslušného spuštění.

K zobrazení předchozích historií spuštění v sadě Visual Studio nebo na webu Azure portal: 

* Otevřete Průzkumníka cloudu sady Visual Studio. Najít aplikaci logiky, otevřete místní nabídku aplikace. Vyberte **Otevřít historii spouštění**.

* Na webu Azure Portal vyhledejte svou aplikaci logiky. V nabídce aplikace logiky zvolte **přehled**. 

## <a name="create-automated-deployment-templates"></a>Vytvoření šablon pro automatické nasazení

Po vytvoření řešení aplikace logiky můžete zachytit a nasazení aplikace jako [šablony Azure Resource Manageru](../azure-resource-manager/resource-group-overview.md#template-deployment) do libovolné oblasti Azure v celém světě. Tato funkce obou můžete upravit parametry při vytváření různých verzí aplikace a pro své řešení integrujete kanálech Azure. Můžete použít také Azure Functions do šablony nasazení, můžete spravovat celé řešení se všemi závislostmi jako jediné šabloně. Přečtěte si [postup vytvoření šablony nasazení aplikací logiky](../logic-apps/logic-apps-create-deploy-template.md).

Pro šablonu nasazení příklad pomocí funkce Azure, zkontrolujte, [úložiště šablon rychlý start Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-function-app-create-dynamic).

## <a name="next-steps"></a>Další postup

* [Najít další příklady a scénáře pro Azure Logic Apps](logic-apps-examples-and-scenarios.md)

<!-- Image References -->
[1]: ./media/logic-apps-scenario-social-serverless/twitter.png
[2]: ./media/logic-apps-scenario-social-serverless/function.png
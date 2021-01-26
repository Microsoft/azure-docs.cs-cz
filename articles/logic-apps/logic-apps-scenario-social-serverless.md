---
title: Vytvořit řídicí panel Customer Insights
description: Spravujte názory zákazníků, data sociálních médií a další informace vytvořením řídicího panelu zákazníka pomocí Azure Logic Apps a Azure Functions
services: logic-apps
ms.suite: integration
author: jeffhollan
ms.author: jehollan
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 03/15/2018
ms.openlocfilehash: 22e0c7304f7a53a86bc5c6739a2061352d738d29
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2021
ms.locfileid: "98784808"
---
# <a name="create-a-streaming-customer-insights-dashboard-with-azure-logic-apps-and-azure-functions"></a>Vytvoření řídicího panelu pro streamování zákaznických přehledů pomocí Azure Logic Apps a Azure Functions

Azure nabízí nástroje bez [serveru](https://azure.microsoft.com/solutions/serverless/) , které vám pomůžou rychle vytvářet a hostovat aplikace v cloudu, aniž byste se museli zabývat infrastrukturou. V tomto kurzu můžete vytvořit řídicí panel, který se aktivuje na základě zpětné vazby od zákazníků, analyzuje zpětnou vazbu pomocí machine learningu a zveřejňuje přehledy na zdroj, jako je Power BI nebo Azure Data Lake.

Pro toto řešení použijete tyto klíčové součásti Azure pro aplikace bez serveru: [Azure Functions](https://azure.microsoft.com/services/functions/) a [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/).
Azure Logic Apps poskytuje modul pracovního postupu bez serveru v cloudu, takže můžete vytvářet orchestraci napříč komponentami bez serveru a připojit se k 200 službám a rozhraním API. Azure Functions poskytuje výpočetní prostředí bez serveru v cloudu. Toto řešení používá Azure Functions pro označení zákaznických tweety na základě předdefinovaných klíčových slov.

V tomto scénáři vytvoříte aplikaci logiky, která se aktivuje při hledání zpětné vazby od zákazníků. Mezi konektory, které vám pomůžou reagovat na názory zákazníků, patří Outlook.com, Office 365, opice průzkumu, Twitter a [požadavek HTTP z webového formuláře](/archive/blogs/logicapps/calling-a-logic-app-from-an-html-form). Pracovní postup, který vytvoříte, monitoruje hashtag na Twitteru.

Můžete [sestavit celé řešení v aplikaci Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) a [nasadit řešení pomocí šablony Azure Resource Manager](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md). Návod pro video, který ukazuje, jak vytvořit toto řešení, najdete v [tomto videu kanálu 9](https://aka.ms/logicappsdemo). 

## <a name="trigger-on-customer-data"></a>Aktivovat zákaznická data

1. V Azure Portal nebo Visual Studiu vytvořte prázdnou aplikaci logiky. 

   Pokud s Logic Apps začínáte, přečtěte si [rychlý Start pro Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md) nebo [rychlý Start pro Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

2. V návrháři aplikace logiky vyhledejte a přidejte Trigger Twitteru, který má tuto akci: **při zveřejnění nového** přístavu

3. Nastavte Trigger tak, aby naslouchal tweety na základě klíčového slova nebo hashtagu.

   U triggerů založených na dotazech, jako je například Trigger Twitteru, vlastnost opakování určuje, jak často aplikace logiky kontroluje nové položky.

   ![Příklad triggeru Twitteru][1]

Tato aplikace logiky se teď aktivuje pro všechny nové tweety. Pak můžete data probírat a analyzovat, abyste lépe porozuměli zabarvení vyjádřené. 

## <a name="analyze-tweet-text"></a>Analyzovat text v poli

K detekci mínění za nějakým textem můžete použít [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/).

1. V návrháři aplikace logiky pod triggerem vyberte **Nový krok**.

2. Najděte konektor **Analýza textu** .

3. Vyberte akci **zjistit mínění** .

4. Po zobrazení výzvy zadejte platný klíč Cognitive Services služby Analýza textu.

5. V části text **žádosti** vyberte **textové** pole v poli, které jako vstup pro analýzu poskytne text.

Po získání dat v poli se základními informacemi o promoci teď můžete použít několik dalších relevantních konektorů a jejich akce:

* **Power BI – přidání řádků do datové sady streamování**: zobrazení příchozích tweety na řídicím panelu Power BI.
* **Azure Data Lake – připojit soubor**: přidejte zákaznická Data do Azure Data Lake datové sady, která se mají zahrnout do úloh analýzy.
* **SQL – přidání řádků**: uložení dat v databázi pro pozdější načtení.
* **Časová rezerva – odeslání zprávy**: oznámení kanálu časové rezervy o negativní zpětné vazbě, která může vyžadovat akci.

Můžete také vytvořit a funkci Azure Function, abyste mohli provádět vlastní zpracování dat. 

## <a name="process-data-with-azure-functions"></a>Zpracování dat pomocí Azure Functions

Před vytvořením funkce vytvořte v předplatném Azure aplikaci Function App. Pro aplikaci logiky, aby přímo volala funkci, musí mít funkce vazbu triggeru HTTP, například pomocí šablony **HttpTrigger** . Naučte [se, jak vytvořit svou první aplikaci funkcí a funkci v Azure Portal](../azure-functions/functions-get-started.md).

V tomto scénáři jako text žádosti pro funkci Azure použijte text. V kódu funkce definujte logiku, která určuje, zda text příznaku obsahuje klíčové slovo nebo frázi. Tuto funkci nechte v případě potřeby ve scénáři jednoduché nebo složitější.
Na konci funkce vrátí odpověď do aplikace logiky s některými daty, například jednoduchou logickou hodnotu, jako je například `containsKeyword` nebo složitý objekt.

> [!TIP]
> Pro přístup ke složitou odezvě z funkce v aplikaci logiky použijte akci **analyzovat JSON** .

Až to budete mít, uložte funkci a pak funkci přidejte jako akci do aplikace logiky, kterou vytváříte.

## <a name="add-azure-function-to-logic-app"></a>Přidat funkci Azure Functions do aplikace logiky

1. V návrháři aplikace logiky v akci **detekovat mínění** vyberte možnost **Nový krok**.

2. Najděte konektor **Azure Functions** a pak vyberte funkci, kterou jste vytvořili.

3. V části text **žádosti** vyberte **text**.

![Nakonfigurovaný krok Azure Functions][2]

## <a name="run-and-monitor-your-logic-app"></a>Spuštění a monitorování aplikace logiky

Pokud si chcete projít jakékoli aktuální nebo předchozí běhy aplikace logiky, můžete použít bohatou možnost ladění a monitorování, kterou Azure Logic Apps poskytuje v Azure Portal, Visual Studiu nebo v rozhraních Azure REST API a sadách SDK.

Pro snadné testování aplikace logiky v návrháři aplikace logiky vyberte možnost **Spustit Trigger**. Aktivační událost se spustí dotazování pro tweety podle zadaného plánu až do doby, kdy se najde ta, která splňuje vaše kritéria. Během běhu Návrhář zobrazuje živé zobrazení pro tento běh.

Chcete-li zobrazit předchozí historie spuštění v aplikaci Visual Studio nebo Azure Portal: 

* Otevřete Průzkumníka cloudu sady Visual Studio. Najděte aplikaci logiky a otevřete místní nabídku aplikace. Vyberte **otevřít historii spuštění**.

  > [!TIP]
  > Pokud tento příkaz v aplikaci Visual Studio 2019 nemáte, ověřte, že máte nejnovější aktualizace pro Visual Studio.

* V Azure Portal Najděte aplikaci logiky. V nabídce aplikace logiky klikněte na **Přehled**. 

## <a name="create-automated-deployment-templates"></a>Vytvoření šablon pro automatické nasazení

Po vytvoření řešení aplikace logiky můžete aplikaci zachytit a nasadit jako [šablonu Azure Resource Manager](../azure-resource-manager/templates/overview.md) do jakékoli oblasti Azure na světě. Tuto možnost můžete použít jak pro úpravu parametrů pro vytváření různých verzí aplikace a pro integraci svého řešení do Azure Pipelines. Můžete také zahrnout Azure Functions do šablony nasazení, abyste mohli spravovat celé řešení se všemi závislostmi jako s jedinou šablonou. Naučte se [automatizovat nasazení aplikace logiky](logic-apps-azure-resource-manager-templates-overview.md).

Ukázkovou šablonu nasazení s funkcí Azure Functions najdete v [úložišti šablon Azure pro rychlý Start](https://github.com/Azure/azure-quickstart-templates/tree/master/101-function-app-create-dynamic).

## <a name="next-steps"></a>Další kroky

* [Najít další příklady a scénáře pro Azure Logic Apps](logic-apps-examples-and-scenarios.md)

<!-- Image References -->
[1]: ./media/logic-apps-scenario-social-serverless/twitter.png
[2]: ./media/logic-apps-scenario-social-serverless/function.png
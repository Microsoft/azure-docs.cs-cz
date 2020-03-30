---
title: Vytvoření řídicího panelu přehledů zákazníků
description: Spravujte zpětnou vazbu od zákazníků, data sociálních médií a další funkce vytvořením řídicího panelu zákazníků pomocí aplikací Azure Logic Apps a funkcí Azure
services: logic-apps
ms.suite: integration
author: jeffhollan
ms.author: jehollan
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 03/15/2018
ms.openlocfilehash: e300bf9c9aa0acf0bed6426eb73f690f9a38bd74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75980428"
---
# <a name="create-a-streaming-customer-insights-dashboard-with-azure-logic-apps-and-azure-functions"></a>Vytvoření řídicího panelu přehledů zákazníků streamování pomocí Aplikací logiky Azure a funkcí Azure

Azure nabízí nástroje [bez serveru,](https://azure.microsoft.com/solutions/serverless/) které vám pomůžou rychle vytvářet a hostovat aplikace v cloudu, aniž byste museli přemýšlet o infrastruktuře. V tomto kurzu můžete vytvořit řídicí panel, který se aktivuje na zpětnou vazbu od zákazníků, analyzuje zpětnou vazbu pomocí strojového učení a publikuje přehledy do zdroje, jako je Power BI nebo Azure Data Lake.

Pro toto řešení použijete tyto klíčové součásti Azure pro aplikace bez serveru: [Azure Functions](https://azure.microsoft.com/services/functions/) a Azure [Logic Apps](https://azure.microsoft.com/services/logic-apps/).
Azure Logic Apps poskytuje modul pracovních postupů bez serveru v cloudu, takže můžete vytvářet orchestrace napříč komponentami bez serveru a připojit se k více než 200 službám a virtuálním i hraničním určovatčasům. Funkce Azure poskytují v cloudu bezserverové výpočty. Toto řešení používá Funkce Azure pro označování tweetů zákazníků na základě předdefinovaných klíčových slov.

V tomto scénáři vytvoříte aplikaci logiky, která se aktivuje při hledání zpětné vazby od zákazníků. Některé konektory, které vám pomohou reagovat na zpětnou vazbu od zákazníků, zahrnují Outlook.com, Office 365, Survey Monkey, Twitter a [požadavek HTTP z webového formuláře](https://blogs.msdn.microsoft.com/logicapps/2017/01/30/calling-a-logic-app-from-an-html-form/). Pracovní postup, který vytvoříte, sleduje hashtag na Twitteru.

Můžete [vytvořit celé řešení v Sadě Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) a [nasadit řešení pomocí šablony Azure Resource Manager](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md). Video návod, který ukazuje, jak toto řešení vytvořit, [najdete v tomto videu kanálu 9](https://aka.ms/logicappsdemo). 

## <a name="trigger-on-customer-data"></a>Aktivační událost u dat zákazníků

1. Na webu Azure Portal nebo Visual Studiu vytvořte prázdnou aplikaci logiky. 

   Pokud s aplikacemi logiky tečte, přečtěte si [úvodní příručku pro portál Azure nebo](../logic-apps/quickstart-create-first-logic-app-workflow.md) úvodní start pro Visual [Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

2. V Aplikaci Logika Designer, najít a přidat Twitter aktivační událost, která má tuto akci: **Když je vyslán nový tweet**

3. Nastavte aktivační událost tak, aby poslouchala tweety na základě klíčového slova nebo hashtagu.

   Na aktivačních událostech založených na dotazování, jako je například aktivační událost Twitter, vlastnost opakování určuje, jak často aplikace logiky kontroluje nové položky.

   ![Příklad twitterové spouštěče][1]

Tato logická aplikace nyní požáry na všechny nové tweets. Potom můžete vzít a analyzovat data tweetu, abyste lépe porozuměli vyjádřeným pocitům. 

## <a name="analyze-tweet-text"></a>Analýza textu tweetu

Chcete-li zjistit mínění za některé text, můžete použít [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/).

1. V Návrháři aplikací logiky pod aktivační událostí zvolte **Nový krok**.

2. Najděte konektor **Analýzy textu.**

3. Vyberte akci **Rozpoznat mínění.**

4. Pokud se zobrazí výzva, zadejte platný klíč služeb Cognitive Services pro službu Analýza textu.

5. V části **Text požadavku**vyberte pole **Tweet Text,** které poskytuje text tweetu jako vstup pro analýzu.

Poté, co získáte data tweetu a přehledy o tweetu, můžete nyní použít několik dalších relevantních konektorů a jejich akcí:

* **Power BI – Přidání řádků do datové sady datových proudů**: Zobrazení příchozích tweetů na řídicím panelu Power BI.
* **Azure Data Lake – připojit soubor**: Přidání zákaznických dat do datové sady Azure Data Lake, která bude zahrnuta do úloh analýzy.
* **SQL - Přidat řádky**: Uložte data v databázi pro pozdější načtení.
* **Časová rezerva – odeslat zprávu**: Upozorněte kanál Slack na negativní zpětnou vazbu, která může vyžadovat akci.

Můžete také vytvořit a funkce Azure, takže můžete provádět vlastní zpracování na vaše data. 

## <a name="process-data-with-azure-functions"></a>Zpracování dat pomocí funkcí Azure

Před vytvořením funkce vytvořte ve svém předplatném Azure aplikaci funkcí. Také pro aplikaci logiky přímo volat funkci, funkce musí mít aktivační vazby HTTP, například použít šablonu **HttpTrigger.** Přečtěte [si, jak vytvořit první funkční aplikaci a funkci na webu Azure Portal](../azure-functions/functions-create-first-azure-function-azure-portal.md).

V tomto scénáři použijte tweet text jako tělo požadavku pro funkci Azure. V kódu funkce definujte logiku, která určuje, zda text tweetu obsahuje klíčové slovo nebo frázi. Udržujte funkci tak jednoduché nebo složité, jak je nezbytné pro scénář.
Na konci funkce vrátí odpověď do aplikace logiky s některá data, například `containsKeyword` jednoduché logické hodnoty, jako je například nebo komplexní objekt.

> [!TIP]
> Chcete-li získat přístup ke složité odpovědi z funkce v aplikaci logiky, použijte akci **Analyzovat JSON.**

Až budete hotovi, uložte funkci a pak přidejte funkci jako akci v aplikaci logiky, kterou vytváříte.

## <a name="add-azure-function-to-logic-app"></a>Přidání funkce Azure do aplikace logiky

1. V Návrháři aplikací logiky v části **Akce Rozpoznat mínění** zvolte Nový **krok**.

2. Najděte konektor **Azure Functions** a vyberte funkci, kterou jste vytvořili.

3. V části **Text žádosti**vyberte **Tweet Text**.

![Krok nakonfigurované funkce Azure][2]

## <a name="run-and-monitor-your-logic-app"></a>Spuštění a monitorování aplikace logiky

Chcete-li zkontrolovat všechny aktuální nebo předchozí spuštění pro vaši aplikaci logiky, můžete použít bohaté možnosti ladění a monitorování, které Azure Logic Apps poskytuje na webu Azure Portal, Visual Studio nebo prostřednictvím Azure REST API a SDKs.

Chcete-li snadno otestovat aplikaci logiky, v návrháři aplikací logiky zvolte **Spustit aktivační událost**. Aktivační událost se provádí na základě zadaného plánu, dokud nebude nalezen tweet, který splňuje vaše kritéria. Zatímco spuštění postupuje, návrhář zobrazí živé zobrazení pro toto spuštění.

Zobrazení předchozích historie spuštění ve Visual Studiu nebo na webu Azure Portal: 

* Otevřete Průzkumníka Visual Studio Cloud Explorer. Najděte aplikaci logiky a otevřete místní nabídku aplikace. Vyberte **Možnost Otevřít historii spuštění**.

  > [!TIP]
  > Pokud tento příkaz nemáte v Sadě Visual Studio 2019, zkontrolujte, zda máte nejnovější aktualizace pro Visual Studio.

* Na webu Azure Portal najděte aplikaci logiky. V nabídce aplikace logiky zvolte **Přehled**. 

## <a name="create-automated-deployment-templates"></a>Vytvoření šablon pro automatické nasazení

Po vytvoření řešení aplikace logiky můžete zachytit a nasadit aplikaci jako [šablonu Azure Resource Manager](../azure-resource-manager/templates/overview.md) do libovolné oblasti Azure na světě. Tuto možnost můžete použít jak k úpravě parametrů pro vytváření různých verzí aplikace, tak k integraci vašeho řešení do Azure Pipelines. Funkce Azure můžete také zahrnout do šablony nasazení, abyste mohli spravovat celé řešení se všemi závislostmi jako jednu šablonu. Přečtěte si, jak [automatizovat nasazení aplikace logiky](logic-apps-azure-resource-manager-templates-overview.md).

Příklad šablony nasazení s funkcí Azure najdete v [úložišti šablon Azure quickstart](https://github.com/Azure/azure-quickstart-templates/tree/master/101-function-app-create-dynamic).

## <a name="next-steps"></a>Další kroky

* [Najít další příklady a scénáře pro Azure Logic Apps](logic-apps-examples-and-scenarios.md)

<!-- Image References -->
[1]: ./media/logic-apps-scenario-social-serverless/twitter.png
[2]: ./media/logic-apps-scenario-social-serverless/function.png

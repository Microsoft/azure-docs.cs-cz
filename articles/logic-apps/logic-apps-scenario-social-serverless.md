---
title: "Bez serveru scénář – vytvoření zákazníka přehledný řídicí panel s Azure | Microsoft Docs"
description: "Zjistěte, jak můžete spravovat názory zákazníků, sociální média dat a další podle budovy zákazníka řídicí panel s funkcemi Azure a Azure Logic Apps"
keywords: 
services: logic-apps
author: jeffhollan
manager: SyntaxC4
editor: 
documentationcenter: 
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2018
ms.author: jehollan; LADocs
ms.openlocfilehash: 0a31a71305a4729575c5266b3a6138004d2dbdc6
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="create-a-streaming-customer-insights-dashboard-with-azure-logic-apps-and-azure-functions"></a>Vytvoření datových proudů přehledný řídicí panel zákazníků s funkcemi Azure a Azure Logic Apps

Azure nabízí bez serveru nástroje, které vám pomohou rychle sestavení a hostitele aplikací v cloudu, aniž by museli vezměte v úvahu infrastruktury. V tomto kurzu můžete vytvořit řídicí panel, který aktivuje názorů zákazníků, analyzuje zpětnou vazbu pomocí machine learning a publikuje Statistika na zdroj, například Power BI nebo Azure Data Lake.

Pro tohle řešení použijete tyto klíčové komponenty Azure pro aplikace, bez serveru: [Azure Functions](https://azure.microsoft.com/services/functions/) a [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/).
Azure Logic Apps poskytuje modul bez serveru pracovního postupu v cloudu, takže můžete vytvořit orchestrations mezi komponentami bez serveru a připojení k 200 + služeb a rozhraní API. Azure Functions nabízí bez serveru computing v cloudu. Toto řešení používá Azure Functions pro označování tweetů zákazníka na základě předdefinovaných klíčových slov.

V tomto scénáři vytvoříte aplikaci logiky, která aktivuje na hledání zpětné vazby od zákazníků. Některé konektory, že nápovědy reagovat na připomínky zákazníků jsou Outlook.com, Office 365, opic průzkum, Twitter a [požadavku HTTP z webového formuláře](https://blogs.msdn.microsoft.com/logicapps/2017/01/30/calling-a-logic-app-from-an-html-form/). Pracovní postup, který vytvoříte monitoruje hashtag na Twitteru.

Můžete [sestavte celé řešení v sadě Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) a [nasazení řešení pomocí šablony Azure Resource Manageru](../logic-apps/logic-apps-create-deploy-template.md). Video návod, který ukazuje, jak vytvořit toto řešení [přehrát toto video Channel 9](http://aka.ms/logicappsdemo). 

## <a name="trigger-on-customer-data"></a>Aktivovat na data zákazníků

1. V portálu Azure nebo v sadě Visual Studio vytvoření aplikace logiky prázdné. 

   Pokud jste nové aplikace logiky, přečtěte si [rychlý úvodní kurz pro Azure portal](../logic-apps/quickstart-create-first-logic-app-workflow.md) nebo [rychlý start pro sadu Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

2. V návrháři aplikace logiky, najít a přidejte Twitter aktivační událost, která má tato akce: **při odeslání nové tweet**

3. Nastavte aktivační událost pro naslouchání na základě – klíčové slovo nebo hashtag tweetů.

   V aktivační události na základě dotazování, jako jsou aktivační události služby Twitter vlastnost opakování určuje, jak často se aplikace logiky vyhledává nové položky.

   ![Příklad aktivační události služby Twitter.][1]

Tato aplikace logiky nyní na všechny nové tweetů aktivuje. Potom můžete provést a analyzovat tweet data, takže můžete lépe pochopit chráněny vyjádřené. 

## <a name="analyze-tweet-text"></a>Analýza textu tweet

Chcete-li zjistit postojích za část textu, můžete použít [kognitivní služeb Azure](https://azure.microsoft.com/services/cognitive-services/).

1. V návrháři aplikace logiky, vyberte v části aktivační událost, **nový krok**.

2. Najít **Analýza textu** konektor.

3. Vyberte **zjistit postojích** akce.

4. Pokud se zobrazí výzva, zadejte platný klíč kognitivní služby pro službu Analýza textu.

5. V části **text žádosti**, vyberte **Tweet Text** pole, která poskytuje tweet text jako vstup pro analýzu.

Můžete po načtení dat tweet a přehledy o tweet, můžete teď můžete použít několik dalších příslušných konektorů a jejich akce:

* **Power BI - přidávání řádků do datové sady streamování**: Zobrazit příchozí tweetů na řídicí panel Power BI.
* **Azure Data Lake - připojit soubor**: přidat data zákazníků k datové sadě služby Azure Data Lake pro zahrnutí do úlohy analytics.
* **SQL - přidávání řádků**: ukládání dat v databázi pro pozdější načtení.
* **Slack – poslat**: oznámit Slack kanál o záporné zpětnou vazbu, která můžou vyžadovat akci.

Můžete také vytvořit a funkce Azure, která může provádět vlastní zpracování na vaše data. 

## <a name="process-data-with-azure-functions"></a>Zpracování dat pomocí Azure Functions

Před vytvořením funkce vytvořte aplikaci funkce ve vašem předplatném Azure. Pro svou aplikaci logiky přímo volat funkci navíc funkce musí být HTTP aktivovat vazby pro příklad, použijte **HttpTrigger** šablony. Další informace [vytvoření první funkce aplikace a funkce na portálu Azure](../azure-functions/functions-create-first-azure-function-azure-portal.md).

V tomto scénáři použijte tweet text jako text žádosti pro funkce Azure. Ve vašem kódu funkce definujte logiku, která určuje, zda tweet text obsahuje klíčové slovo nebo frázi. Zachovat funkce jako jednoduché nebo komplexní podle potřeby pro scénář.
Na konci funkce, vrátí reakci na aplikaci logiky s některá data, například, hodnota jednoduchou logickou hodnotu, jako `containsKeyword` nebo komplexní objekt.

> [!TIP]
> Chcete-li získat přístup k komplexní odpověď z funkce v aplikaci logiky, použijte **analyzovat JSON** akce.

Když jste hotovi, funkce Uložit a poté přidejte funkce jako akce v aplikaci logiky, kterou vytváříte.

## <a name="add-azure-function-to-logic-app"></a>Přidat do aplikace logiky Azure – funkce

1. V návrháři aplikace logiky v části **zjistit postojích** akce, zvolte **nový krok**.

2. Najít **Azure Functions** konektoru a pak vyberte funkce, kterou jste vytvořili.

3. V části **text žádosti**, vyberte **Tweet Text**.

![Nakonfigurované krok funkce Azure][2]

## <a name="run-and-monitor-your-logic-app"></a>Spuštění a monitorování aplikace logiky

Chcete-li zkontrolovat všechny aktuálního nebo předchozího spuštění pro svou aplikaci logiky, můžete použít bohaté ladění a monitorování funkce, které poskytuje Azure Logic Apps na portálu Azure, Visual Studio, nebo prostřednictvím rozhraní API REST služby Azure a sady SDK.

Chcete-li snadno testovat svou aplikaci logiky v návrháři aplikace logiky, zvolte **spustit aktivační událost**. Aktivační událost se dotazuje na tweetů podle zadaného plánu, dokud nebude nalezen tweet, které splňují zadaná kritéria. Při spuštění postupuje návrháře zobrazuje živý pro kterých běží.

Zobrazení předchozího spuštění historie v sadě Visual Studio nebo portálu Azure: 

* Otevřete Průzkumník cloudu sady Visual Studio. Najít aplikaci logiky, otevřete nabídku zástupce aplikace. Vyberte **historie spouštění otevřete**.

* Na portálu Azure najdete aplikaci logiky. V nabídce aplikace logiky zvolte **přehled**. 

## <a name="create-automated-deployment-templates"></a>Vytvoření šablon pro automatické nasazení

Po vytvoření řešení aplikace logiky můžete zaznamenat a nasadit vaši aplikaci jako [šablony Azure Resource Manageru](../azure-resource-manager/resource-group-overview.md#template-deployment) všechny oblasti Azure na světě. Této funkci můžete použít jak k upravte parametry pro vytvoření různé verze vaší aplikace a pro integraci řešení do sestavení a verze kanálu. Můžete použít také Azure Functions v šabloně nasazení, aby mohl spravovat celé řešení s všechny závislosti jako jediné šabloně. Další informace [postup vytvoření šablony pro nasazení aplikace logiky](../logic-apps/logic-apps-create-deploy-template.md).

Šablonu příklad nasazení s Azure funkce, najdete [úložiště šablony Azure rychlý Start](https://github.com/Azure/azure-quickstart-templates/tree/master/101-function-app-create-dynamic).

## <a name="next-steps"></a>Další postup

* [Najít další příkladů a scénářů pro Azure Logic Apps](logic-apps-examples-and-scenarios.md)

<!-- Image References -->
[1]: ./media/logic-apps-scenario-social-serverless/twitter.png
[2]: ./media/logic-apps-scenario-social-serverless/function.png
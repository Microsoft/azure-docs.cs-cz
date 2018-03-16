---
title: "Sestavení bez serveru aplikace v sadě Visual Studio | Microsoft Docs"
description: "Začínáme s první aplikaci bez serveru v této příručce na vytváření, nasazování a správě aplikací v sadě Visual Studio."
keywords: 
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: d3cd509a0073e45bf910e9a74a279d678ff03b63
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="build-a-serverless-app-in-visual-studio-with-logic-apps-and-functions"></a>Sestavení bez serveru aplikace v sadě Visual Studio s Logic Apps a funkce

Pro rychlý vývoj a nasazení cloudových aplikací umožňují bez serveru nástroje a možnosti v Azure.  Tento dokument se zaměřuje na tom, jak začít pracovat v sadě Visual Studio vytváření aplikaci bez serveru.  Přehled bez serveru v Azure [najdete v tomto článku](logic-apps-serverless-overview.md).

## <a name="getting-everything-ready"></a>Příprava vše

Zde jsou součásti potřebné k vytvoření aplikace bez serveru ze sady Visual Studio:

* [Visual Studio 2017](https://www.visualstudio.com/vs/) nebo Visual Studio 2015 – Community, Professional nebo Enterprise
* [Logiku aplikace nástrojů pro Visual Studio](https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio-18551)
* [Nejnovější sadu Azure SDK](https://azure.microsoft.com/downloads/) (2.9.1 nebo vyšší)
* [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)
* [Nástroje Azure základní funkce](https://www.npmjs.com/package/azure-functions-core-tools) k ladění funkcí místně
* Přístup k webovému při použití návrháře embedded aplikace logiky

## <a name="getting-started-with-a-deployment-template"></a>Začínáme s šablony nasazení

Správa prostředků v Azure se provádějí v rámci skupiny prostředků.  Skupina prostředků je logické seskupení prostředků.  Skupiny prostředků povolit nasazení a správu kolekcí prostředků.  Pro aplikaci bez serveru v Azure obsahuje naše skupiny prostředků Azure Logic Apps i Azure Functions.  Pomocí projektu skupiny prostředků v sadě Visual Studio jsou možné k vývoji, správě a nasazení bude celá aplikace jako jednoho datového zdroje.

### <a name="create-a-resource-group-project-in-visual-studio"></a>Vytvoření projektu skupiny prostředků v sadě Visual Studio

1. V sadě Visual Studio, klikněte na tlačítko Přidat **nový projekt**
1. V **cloudu** kategorie, vyberte pro vytvoření Azure **skupiny prostředků** projektu  
 * Pokud nevidíte kategorie nebo projektu uvedené, ujistěte se, že máte sadu Azure SDK pro Visual Studio nainstalována
1. Název a umístění poskytnout projekt a vyberte **Ok** k vytvoření sady Visual Studio vyzve k výběru šablony.  Můžete třeba vybrat spuštění z prázdné, spustí se aplikace logiky nebo jiný prostředek.  Ale v tomto případě používáme k šabloně pro rychlý start Azure získat nám začít s bez serveru aplikace.
1. Po výběru zobrazí šablony z **Azure Quickstart** ![výběr Azure Quickstart šablony][1]
1. Vyberte šablonu, bez serveru rychlý start: **101-logic-app-and-function-app** a klikněte na tlačítko **Ok**

Rychlý start šablona vytvoří šablonu nasazení ve vašem projektu skupiny prostředků.  Šablona obsahuje jednoduchou aplikaci logiky, která volá Azure Functions a vrátí výsledek.  Pokud otevřete `azuredeploy.json` soubor v Průzkumníku řešení, se zobrazí prostředky pro aplikaci bez serveru.

## <a name="deploying-the-serverless-application"></a>Nasazení aplikace bez serveru

Před vizuálního návrháře aplikace logiky můžete otevřít v sadě Visual Studio, je potřeba předem nasazené skupiny prostředků Azure.  To umožňuje návrháři k vytváření a používání připojení k prostředkům a službám v aplikaci logiky.  Abyste mohli začít, potřebujeme jednoduše nasadit řešení vytvořili.

1. Klikněte pravým tlačítkem na projekt v sadě Visual Studio, vyberte **nasadit**a vytvořte **nový** nasazení ![výběr nového nasazení prostředků][2]
1. Vyberte platné předplatné Azure a skupiny prostředků
1. Vyberte pro **nasadit** řešení
1. Zadejte název pro funkce aplikace Azure a logiku aplikace.  Název funkce Azure musí být globálně jedinečné

Bez serveru řešení se nasadí do zadaná skupina prostředků.  Pokud se podíváte **výstup** v sadě Visual Studio se zobrazí stav nasazení.

## <a name="editing-the-logic-app-in-visual-studio"></a>Úpravy aplikace logiky v sadě Visual Studio

Po nasazení řešení do libovolné skupině prostředků vizuálního návrháře lze upravit a proveďte změny do aplikace logiky.

1. Klikněte pravým tlačítkem myši `azuredeploy.json` soubor v Průzkumníku řešení a vyberte **otevřít s Logic Apps Návrhář**
1. Vyberte **skupiny prostředků** a **umístění** řešení byl nasazen na a vyberte **OK**

Aplikace logiky vizuálního návrháře by teď měly být viditelné pomocí sady Visual Studio.  Můžete přidat kroky, upravte pracovní postup a uložte změny.  Aplikace logiky můžete také vytvořit ze sady Visual Studio.  Pokud kliknete pravým tlačítkem na **prostředky** Navigátor šablony můžete přidat **aplikace logiky** do projektu.  Načíst prázdný logiku aplikace v Návrháři visual bez předem nasadit do skupiny prostředků.

### <a name="managing-and-viewing-run-history-for-a-deployed-logic-app"></a>Správa a zobrazování historie spouštění aplikace nasazené logiky

Můžete také spravovat a zobrazit historii spouštění logiky aplikace nasazené v Azure.  Pokud otevřete **Průzkumník cloudu** nástroje v sadě Visual Studio, klikněte pravým tlačítkem na všechny aplikace logiky a zvolte Upravit, zakázat, zobrazit vlastnosti nebo zobrazení historie spouštění.  Kliknutím na tlačítko Upravit umožňuje také ke stažení aplikace logiky publikované do projektu skupiny prostředků Visual Studio.  To znamená, že i v případě, že jste spustili, vytvářet aplikaci logiky na portálu Azure, můžete přesto ho importovat a spravovat ze sady Visual Studio.

## <a name="developing-an-azure-function-in-visual-studio"></a>Vývoj Azure funkce v sadě Visual Studio

Všechny funkce Azure, které jsou obsaženy v řešení pro úložiště git, zadaný v nasadí šablonu nasazení `azuredeploy.json` proměnné.  Pokud vytváříte projekt funkce v rámci řešení, zkontrolujte jej do správy zdrojového kódu (GitHub, Visual Studio Team Services atd.) a aktualizace `repo` proměnné, budou šablony nasazení funkce Azure.

### <a name="creating-an-azure-function-project"></a>Vytvoření projektu Azure – funkce

Pokud pomocí jazyka JavaScript, Python, F #, Bash, Batch nebo prostředí PowerShell, postupujte podle kroků [kroky v rozhraní příkazového řádku funkce](../azure-functions/functions-run-local.md) k vytvoření projektu.  Pokud vývoj funkce v jazyce C#, můžete použít [knihovny tříd jazyka C#](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/16/publishing-a-net-class-library-as-a-function-app/) v aktuálním řešení pro funkci Azure.

## <a name="next-steps"></a>Další postup

* [Naučte se vytvářet bez serveru sociálních řídicí panel](logic-apps-scenario-social-serverless.md)
* [Správa aplikace logiky z cloudu Průzkumníka Visual Studio](manage-logic-apps-with-visual-studio.md)
* [Jazyk definic workflowů funkce Logic aplikace](logic-apps-workflow-definition-language.md)

<!-- Image references -->
[1]: ./media/logic-apps-serverless-get-started-vs/select-template.png
[2]: ./media/logic-apps-serverless-get-started-vs/deploy.png

---
title: Vytvářejte aplikace bez serveru s využitím Azure Logic Apps a Azure Functions v sadě Visual Studio
description: Vytvořit, nasadit a spravovat svou první aplikaci bez serveru pomocí Azure Logic Apps a Azure Functions v sadě Visual Studio
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.custom: vs-azure
ms.topic: article
ms.date: 06/20/2019
ms.openlocfilehash: b7af4fc731d01bb666165655baa2f1d6c64d4071
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444875"
---
# <a name="build-your-first-serverless-app-by-using-azure-logic-apps-and-azure-functions-in-visual-studio"></a>Vytvořte svoji první aplikaci bez serveru s využitím Azure Logic Apps a Azure Functions v sadě Visual Studio

Můžete rychle vyvíjet a nasazení cloudových aplikací pomocí nástroje bez serverů a možnosti v Azure, jako například [Azure Logic Apps](../logic-apps/logic-apps-overview.md) a [Azure Functions](../azure-functions/functions-overview.md). Tento článek ukazuje, jak začít vytvářet aplikace bez serveru, který používá aplikace logiky, která volá funkci Azure, v sadě Visual Studio. Další informace o řešeních bez serveru v Azure najdete v tématu [Azure bez serveru s využitím Functions a Logic Apps](../logic-apps/logic-apps-serverless-overview.md).

## <a name="prerequisites"></a>Požadavky

K sestavení aplikace bez serveru v sadě Visual Studio, budete potřebovat:

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Následující nástroje. Pokud je ještě nemáte, stáhněte a nainstalujte je.

  * [Visual Studio 2019, 2017 nebo 2015 (Community nebo jinou edici)](https://aka.ms/download-visual-studio). 
  V tomto kurzu se používá sada Visual Studio Community 2017, která je zdarma.

    > [!IMPORTANT]
    > Když instalujete Visual Studio 2019 nebo 2017, ujistěte se, že jste vybrali **vývoj pro Azure** pracovního vytížení.

  * [Microsoft Azure SDK pro .NET (verze 2.9.1 nebo novější)](https://azure.microsoft.com/downloads/). 
  Další informace o sadě [Azure SDK pro .NET](https://docs.microsoft.com/dotnet/azure/dotnet-tools?view=azure-dotnet).

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation).

  * Azure Logic Apps Tools pro verzi sady Visual Studio, které chcete:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)
  
    Azure Logic Apps Tools můžete stáhnout a nainstalovat přímo z Visual Studio Marketplace nebo si můžete přečíst, [jak toto rozšíření nainstalovat v rámci sady Visual Studio](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions). 
    Po dokončení instalace nezapomeňte sadu Visual Studio restartovat.

  * [Nástroje Azure Functions Core](https://www.npmjs.com/package/azure-functions-core-tools) pro místní ladění funkce.

* Přístup k webu při používání vloženého návrháře aplikace logiky.

  Návrhář k vytváření prostředků v Azure a ke čtení vlastností a dat z konektorů ve vaší aplikaci logiky vyžaduje připojení k internetu. 
  Pokud například použijete konektor Dynamics CRM Online, návrhář zkontroluje ve vaší instanci CRM dostupné výchozí a vlastní vlastnosti.

## <a name="create-a-resource-group-project"></a>Vytvoření projektu skupiny prostředků

Pokud chcete začít, vytvořte [projekt skupiny prostředků Azure](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) pro vaši aplikaci bez serveru. V Azure, můžete vytvářet prostředky v rámci *skupiny prostředků*, což je logická kolekce, můžete použít k uspořádání, Správa a nasazování prostředků pro celou aplikaci jako jeden prostředek. Pro aplikace bez serveru v Azure vaše skupina prostředků obsahuje prostředky pro Azure Logic Apps a Azure Functions. Další informace o [skupinách prostředků a prostředcích Azure](../azure-resource-manager/resource-group-overview.md).

1. Spusťte sadu Visual Studio a přihlaste se pomocí svého účtu Azure.

1. V nabídce **Soubor** vyberte **Nový** > **Projekt**.

   ![Vytvoření nového projektu v sadě Visual Studio](./media/logic-apps-serverless-get-started-vs/create-new-project-visual-studio.png)

1. V části **Nainstalováno**, vyberte **Visual C#** nebo **Visual Basic**. Vyberte **cloudu** > **skupiny prostředků Azure**.

   > [!NOTE]
   > Pokud **cloudu** kategorie nebo **skupiny prostředků Azure** projekt neexistuje, ujistěte se, že jste nainstalovali sadu Azure SDK pro Visual Studio.

   Pokud používáte Visual Studio 2019, postupujte podle těchto kroků:

   1. V **vytvořte nový projekt** vyberte **skupiny prostředků Azure** šablona projektu pro buď Visual C# nebo Visual Basic a pak vyberte **Další**.

   1. Zadejte název a další informace o projektu, který chcete použít pro skupinu prostředků Azure. Až to budete mít, vyberte **Vytvořit**.

1. Dejte projektu název a umístění a pak vyberte **OK**.

   Visual Studio vás vyzve, abyste ze seznamu šablon vyberte šablonu. 
   Tento příklad používá šablony QuickStart pro Azure, takže můžete vytvářet aplikace bez serveru, který obsahuje aplikace logiky a volání funkce Azure.

   > [!TIP]
   > Ve scénářích, kde nechcete k nasazení řešení do skupiny prostředků Azure, můžete použít prázdnou **aplikace logiky** šablony, které právě vytvoří prázdný logiku aplikace.

1. Z **zobrazit šablony z tohoto umístění** seznamu vyberte **QuickStart pro Azure (github.com/Azure/azure-quickstart-templates)** .

1. Do vyhledávacího pole zadejte jako filtr "aplikace logiky". Ve výsledcích vyberte **101-logic-app-and-function-app** šablony.

   ![Vyberte šablonu pro rychlý start Azure](./media/logic-apps-serverless-get-started-vs/select-template.png)

   Visual Studio vytvoří a otevře řešení pro projekt skupiny prostředků. 
   Šablony rychlý start Azure, kterou jste vybrali vytvoří šablonu nasazení s názvem azuredeploy.json v projektu skupiny prostředků. Tato šablona nasazení obsahuje definici pro jednoduchou aplikaci logiky, která se aktivuje požadavkem HTTP, volá funkci Azure a vrátí výsledek jako odpověď HTTP.

   ![Nové řešení bez serveru](./media/logic-apps-serverless-get-started-vs/create-serverless-solution.png)

1. V dalším kroku nasazení svého řešení do Azure. Musíte to provést, aby bylo možné otevřít šablonu nasazení a zkontrolujte prostředky pro vaši aplikaci bez serveru.

## <a name="deploy-your-solution"></a>Nasazení řešení

Předtím, než aplikace logiky můžete otevřít v návrháři aplikace logiky v sadě Visual Studio, musí mít skupinu prostředků Azure, který je již nasazené v Azure. Návrhář poté můžete vytvořit připojení k prostředkům a službám ve vaší aplikaci logiky. Pro tuto úlohu postupujte podle těchto kroků nasadíte řešení v sadě Visual Studio na webu Azure portal:

1. V Průzkumníku řešení v místní nabídce projektu prostředků, vyberte **nasadit** > **nový**.

   ![Vytvořte nové nasazení pro skupinu prostředků](./media/logic-apps-serverless-get-started-vs/deploy.png)

1. Pokud jste ještě není vybraná, vyberte předplatné a skupinu prostředků, do které chcete nasadit. Vyberte **nasadit**.

   ![Nastavení nasazení](./media/logic-apps-serverless-get-started-vs/deploy-to-resource-group.png)

1. Pokud **upravit parametry** pole se zobrazí, zadejte názvy prostředků pro vaše aplikace logiky a aplikace Azure function app na nasazení a nastavení následně uložte. Ujistěte se, že používáte globálně jedinečný název pro vaši aplikaci function app.

   ![Zadejte názvy pro vaše aplikace logiky a aplikace function app](./media/logic-apps-serverless-get-started-vs/logic-function-app-name-parameters.png)

   Když Visual Studio spustí nasazení tak, aby vaše zadané skupině prostředků, se zobrazí stav nasazení vašeho řešení v sadě Visual Studio **výstup** okna. 
   Po dokončení nasazení vaše aplikace logiky je za provozu na webu Azure Portal.

## <a name="edit-your-logic-app-in-visual-studio"></a>Upravit svou aplikaci logiky v sadě Visual Studio

Chcete-li upravit svou aplikaci logiky po nasazení, otevření aplikace logiky pomocí návrháře aplikace logiky v sadě Visual Studio.

1. V Průzkumníku řešení, vyberte z místní nabídky souboru azuredeploy.json **otevřít pomocí návrháře aplikace logiky**.

   ![Otevřete azuredeploy.json v návrháři aplikace logiky](./media/logic-apps-serverless-get-started-vs/open-logic-app-designer.png)

   > [!TIP]
   > Pokud tento příkaz nemáte k dispozici v aplikaci Visual Studio 2019, zkontrolujte, že máte nejnovější aktualizace pro sadu Visual Studio.

1. Po **vlastnosti aplikace logiky** se objeví okno, v části **předplatné**, vyberte své předplatné Azure, pokud ještě není vybraná. V části **skupiny prostředků**, vyberte skupinu prostředků a umístění, kam jste nasadili řešení a pak vyberte **OK**.

   ![Vlastnosti aplikace logiky](./media/logic-apps-serverless-get-started-vs/logic-app-properties.png)

   Po otevře se návrhář aplikace logiky, můžete pokračovat v přidávání kroky nebo změnit pracovní postup a uložte tyto aktualizace.

   ![Aplikace logiky otevřen v návrháři aplikace logiky](./media/logic-apps-serverless-get-started-vs/opened-logic-app.png)

## <a name="create-your-azure-functions-project"></a>Vytvoření projektu Azure Functions

K vytvoření projektu funkce a funkce pomocí jazyka JavaScript, Python, F#, prostředí PowerShell, Batch nebo prostředí Bash, postupujte podle kroků v [pracovat s Azure Functions Core Tools](../azure-functions/functions-run-local.md). Vývoj s využitím funkce Azure C# uvnitř vašeho řešení, použijte C# knihovny tříd pomocí následujících kroků v [publikovat jako aplikaci Function App knihovny tříd .NET](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/16/publishing-a-net-class-library-as-a-function-app/).

## <a name="deploy-functions-from-visual-studio"></a>Nasazení služby functions v sadě Visual Studio

Nasazení šablony nasadí jakékoli Azure functions, které máte ve vašem řešení z úložiště Git, která je zadána proměnné v souboru azuredeploy.json. Pokud vytvoříte a vytvoření projektu funkce ve vašem řešení, můžete zkontrolovat tento projekt do Gitu (například GitHub nebo Azure DevOps) správy zdrojového kódu a pak aktualizujte `repo` proměnné tak, aby šablona nasadí funkce Azure.

## <a name="manage-logic-apps-and-view-run-history"></a>Správa aplikací logiky a zobrazení historie spuštění

Pro logic apps již nasazené v Azure stále můžete upravit, spravovat, zobrazte historii spuštění a zakázat tyto aplikace ze sady Visual Studio.

1. Z **zobrazení** nabídky v sadě Visual Studio, otevřete **Průzkumníka cloudu**.

1. V části **Všechna předplatná**, vyberte předplatné Azure spojené s logic apps, které chcete spravovat a pak vyberte **použít**.

1. V části **Logic Apps**, vyberte svou aplikaci logiky. Tuto aplikaci místní nabídce vyberte **otevřít pomocí editoru aplikace logiky**.

   > [!TIP]
   > Pokud tento příkaz nemáte k dispozici v aplikaci Visual Studio 2019, zkontrolujte, že máte nejnovější aktualizace pro sadu Visual Studio.

Teď si můžete stáhnout aplikaci logiky už byla publikována do projektu skupiny prostředků. Ano i když je možné, že jste začali aplikace logiky na webu Azure Portal, můžete stále importovat a spravovat aplikace v sadě Visual Studio. Další informace najdete v tématu [Správa aplikací logiky pomocí sady Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).

## <a name="next-steps"></a>Další postup

* [Správa aplikací logiky s využitím sady Visual Studio](manage-logic-apps-with-visual-studio.md)

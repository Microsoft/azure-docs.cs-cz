---
title: Vytvoření první aplikace bez serveru ve Visual Studiu
description: Vytváření, nasazování a správa aplikace bez serveru pomocí Azure Logic Apps a Azure Functions ve Visual Studiu
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 06/20/2019
ms.openlocfilehash: 2e27958dd9379a26ca7e7f4d7e427e5afa216e29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981151"
---
# <a name="build-your-first-serverless-app-by-using-azure-logic-apps-and-azure-functions-in-visual-studio"></a>Vytvoření první aplikace bez serveru pomocí Azure Logic Apps a Azure Functions ve Visual Studiu

Cloudové aplikace můžete rychle vyvíjet a nasazovat pomocí nástrojů a funkcí bez serveru v Azure, jako jsou [Azure Logic Apps](../logic-apps/logic-apps-overview.md) a Azure [Functions](../azure-functions/functions-overview.md). Tento článek ukazuje, jak začít vytvářet aplikaci bez serveru, která používá aplikaci logiky, která volá funkci Azure, v Sadě Visual Studio. Další informace o řešeních bez serveru v Azure najdete v [tématu Azure Serverless s funkcemi a logic apps](../logic-apps/logic-apps-serverless-overview.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li vytvořit aplikaci bez serveru ve Visual Studiu, potřebujete:

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Následující nástroje. Pokud je ještě nemáte, stáhněte si je a nainstalujte.

  * [Visual Studio 2019, 2017 nebo 2015 (Společenství nebo jiné vydání)](https://aka.ms/download-visual-studio). 
  V tomto kurzu se používá sada Visual Studio Community 2017, která je zdarma.

    > [!IMPORTANT]
    > Při instalaci Visual Studia 2019 nebo 2017 se ujistěte, že vyberete vývojové úlohy **Azure.**

  * [Sada Microsoft Azure SDK pro rozhraní .NET (verze 2.9.1 nebo novější).](https://azure.microsoft.com/downloads/) 
  Další informace o sadě [Azure SDK pro .NET](https://docs.microsoft.com/dotnet/azure/dotnet-tools?view=azure-dotnet).

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation).

  * Nástroje azure logic apps pro verzi Visual Studia, kterou chcete:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)
  
    Azure Logic Apps Tools můžete stáhnout a nainstalovat přímo z Visual Studio Marketplace nebo si můžete přečíst, [jak toto rozšíření nainstalovat v rámci sady Visual Studio](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions). 
    Po dokončení instalace nezapomeňte sadu Visual Studio restartovat.

  * [Základní nástroje Azure Functions](https://www.npmjs.com/package/azure-functions-core-tools) pro místně ladící funkce.

* Přístup k webu při použití integrovaného návrháře aplikací logiky.

  Návrhář k vytváření prostředků v Azure a ke čtení vlastností a dat z konektorů ve vaší aplikaci logiky vyžaduje připojení k internetu. 
  Pokud například použijete konektor Dynamics CRM Online, návrhář zkontroluje ve vaší instanci CRM dostupné výchozí a vlastní vlastnosti.

## <a name="create-a-resource-group-project"></a>Vytvoření projektu skupiny zdrojů

Chcete-li začít, vytvořte [projekt skupiny prostředků Azure](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) pro aplikaci bez serveru. V Azure vytvoříte prostředky v rámci *skupiny prostředků*, což je logická kolekce, kterou používáte k uspořádání, správě a nasazování prostředků pro celou aplikaci jako jeden prostředek. Pro aplikaci bez serveru v Azure zahrnuje vaše skupina prostředků prostředky pro Azure Logic Apps i Azure Functions. Další informace o [skupinách prostředků a prostředcích Azure](../azure-resource-manager/management/overview.md).

1. Spusťte Visual Studio a přihlaste se pomocí svého účtu Azure.

1. V nabídce **Soubor** vyberte **Nový** > **Projekt**.

   ![Vytvoření nového projektu v sadě Visual Studio](./media/logic-apps-serverless-get-started-vs/create-new-project-visual-studio.png)

1. V části **Nainstalováno**, vyberte **Visual C#** nebo **Visual Basic**. Potom vyberte **Cloud** > **Azure Resource Group**.

   > [!NOTE]
   > Pokud kategorie **Cloud** nebo projekt **Skupiny prostředků Azure** neexistuje, ujistěte se, že jste nainstalovali Azure SDK pro Visual Studio.

   Pokud používáte Visual Studio 2019, postupujte takto:

   1. V poli **Vytvořit nový projekt** vyberte šablonu projektu **Skupiny prostředků Azure** pro Visual C# nebo Visual Basic a pak vyberte **Další**.

   1. Zadejte název a další informace o projektu, které chcete použít pro skupinu prostředků Azure. Až to budete mít, vyberte **Vytvořit**.

1. Pojmenujte projekt a lokací a pak vyberte **OK**.

   Visual Studio vás vyzve k výběru šablony ze seznamu šablon. 
   Tento příklad používá šablonu Azure QuickStart, takže můžete vytvořit aplikaci bez serveru, která obsahuje aplikaci logiky a volání funkce Azure.

   > [!TIP]
   > Ve scénářích, kde nechcete předem nasadit vaše řešení do skupiny prostředků Azure, můžete použít prázdnou šablonu **aplikace logiky,** která právě vytvoří prázdnou aplikaci logiky.

1. V seznamu **Zobrazit šablony z tohoto** seznamu umístění vyberte Azure **QuickStart (github.com/Azure/azure-quickstart-templates)**.

1. Do vyhledávacího pole zadejte jako filtr "logic-app". Z výsledků vyberte šablonu **101-logic-app-and-function-app.**

   ![Vybrat šablonu Azure QuickStart](./media/logic-apps-serverless-get-started-vs/select-template.png)

   Visual Studio vytvoří a otevře řešení pro projekt skupiny zdrojů. 
   Vybraná šablona Azure QuickStart vytvoří šablonu nasazení s názvem azuredeploy.json v rámci projektu skupiny prostředků. Tato šablona nasazení obsahuje definici jednoduché aplikace logiky, která se aktivuje požadavek HTTP, volá funkci Azure a vrátí výsledek jako odpověď HTTP.

   ![Nové řešení bez serveru](./media/logic-apps-serverless-get-started-vs/create-serverless-solution.png)

1. Dále nasaďte své řešení do Azure. Musíte to udělat před otevřením šablony nasazení a zkontrolujte prostředky pro aplikaci bez serveru.

## <a name="deploy-your-solution"></a>Nasazení řešení

Než budete moci otevřít aplikaci logiky v návrháři aplikací logiky ve Visual Studiu, musíte mít skupinu prostředků Azure, která už je nasazená v Azure. Návrhář pak můžete vytvořit připojení k prostředkům a službám ve vaší aplikaci logiky. Pro tuto úlohu postupujte takto, jak nasadit řešení z Visual Studia na portál Azure:

1. V Průzkumníku řešení vyberte v místní nabídce projektu zdrojů **možnost Nasadit** > **nový**.

   ![Vytvořit nové nasazení pro skupinu prostředků](./media/logic-apps-serverless-get-started-vs/deploy.png)

1. Pokud ještě nejsou vybrané, vyberte předplatné Azure a skupinu prostředků, do které chcete nasadit. Potom vyberte **Nasadit**.

   ![Nastavení nasazení](./media/logic-apps-serverless-get-started-vs/deploy-to-resource-group.png)

1. Pokud se zobrazí pole **Upravit parametry,** zadejte názvy prostředků, které chcete použít pro vaši aplikaci logiky a aplikaci funkce Azure při nasazení a pak uložte nastavení. Ujistěte se, že pro aplikaci funkcí používáte globálně jedinečný název.

   ![Zadejte názvy aplikace logiky a aplikace funkcí.](./media/logic-apps-serverless-get-started-vs/logic-function-app-name-parameters.png)

   Když Visual Studio spustí nasazení do zadané skupiny prostředků, stav nasazení vašeho řešení se zobrazí v okně Visual Studio **Output.** 
   Po dokončení nasazení je vaše aplikace logiky aktivní na webu Azure Portal.

## <a name="edit-your-logic-app-in-visual-studio"></a>Úprava aplikace logiky ve Visual Studiu

Chcete-li upravit aplikaci logiky po nasazení, otevřete aplikaci logiky pomocí Návrháře aplikací logiky v sadě Visual Studio.

1. V Průzkumníku řešení vmístní nabídce souboru azuredeploy.json vyberte **Otevřít pomocí návrháře aplikací logiky**.

   ![Otevření souboru azuredeploy.json v Návrháři aplikací logiky](./media/logic-apps-serverless-get-started-vs/open-logic-app-designer.png)

   > [!TIP]
   > Pokud tento příkaz nemáte v Sadě Visual Studio 2019, zkontrolujte, zda máte nejnovější aktualizace pro Visual Studio.

1. Po zobrazení pole **Vlastnosti aplikace logiky** vyberte v části **Předplatné**předplatné Azure, pokud ještě není vybrané. V části **Skupina prostředků**vyberte skupinu prostředků a umístění, kam jste nasadili řešení, a pak vyberte **OK**.

   ![Vlastnosti aplikace logiky](./media/logic-apps-serverless-get-started-vs/logic-app-properties.png)

   Po otevření Návrháře aplikace logiky můžete pokračovat v přidávání kroků nebo změnit pracovní postup a uložit aktualizace.

   ![Otevřená aplikace logiky v Návrháři aplikací logiky](./media/logic-apps-serverless-get-started-vs/opened-logic-app.png)

## <a name="create-your-azure-functions-project"></a>Vytvoření projektu Azure Functions

Chcete-li vytvořit projekt a funkci funkce funkcí pomocí jazyka JavaScript, Python, F#, PowerShell, Batch nebo Bash, postupujte podle pokynů v [části Práce s nástroji Core functions Azure](../azure-functions/functions-run-local.md). Chcete-li rozvíjet funkci Azure pomocí jazyka C# uvnitř vašeho řešení, použijte knihovnu tříd jazyka C# podle kroků v [části Publikovat knihovnu tříd .NET jako funkční aplikaci](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/16/publishing-a-net-class-library-as-a-function-app/).

## <a name="deploy-functions-from-visual-studio"></a>Nasazení funkcí z Visual Studia

Šablona nasazení nasazuje všechny funkce Azure, které máte ve svém řešení z úložiště Git, které je určeno proměnnými v souboru azuredeploy.json. Pokud vytvoříte a vytvoříte projekt Functions ve vašem řešení, můžete tento projekt zkontrolovat do správy zdrojového kódu `repo` Gitu (například GitHub nebo Azure DevOps) a pak aktualizovat proměnnou tak, aby šablona nasadila vaši funkci Azure.

## <a name="manage-logic-apps-and-view-run-history"></a>Správa aplikací logiky a zobrazení historie spuštění

U aplikací logiky, které už jsou v Azure nasazené, můžete pořád upravovat, spravovat, zobrazovat historii spouštění a zakázat tyto aplikace z Visual Studia.

1. V nabídce **Zobrazení** v sadě Visual Studio **otevřete Průzkumníka Cloud .**

1. V části Všechna předplatná vyberte předplatné Azure přidružené k aplikacím **logiky,** které chcete spravovat, a pak vyberte **Použít**.

1. V části **Logic Apps**vyberte aplikaci logiky. V místní nabídce této aplikace vyberte **Otevřít pomocí Editoru aplikací logiky**.

   > [!TIP]
   > Pokud tento příkaz nemáte v Sadě Visual Studio 2019, zkontrolujte, zda máte nejnovější aktualizace pro Visual Studio.

Nyní můžete stáhnout již publikovanou aplikaci logiky do projektu skupiny zdrojů. Takže i když jste možná spustili aplikaci logiky na webu Azure Portal, můžete tuto aplikaci importovat a spravovat ve Visual Studiu. Další informace najdete [v tématu Správa aplikací logiky pomocí sady Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).

## <a name="next-steps"></a>Další kroky

* [Správa aplikací logiky s využitím sady Visual Studio](manage-logic-apps-with-visual-studio.md)

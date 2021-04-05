---
title: Sestavení první aplikace bez serveru v aplikaci Visual Studio
description: Sestavování, nasazování a Správa aplikace bez serveru pomocí Azure Logic Apps a Azure Functions v aplikaci Visual Studio
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 06/20/2019
ms.openlocfilehash: 1758cca902eb77ffc66824cb56b8add9446fabf9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96749143"
---
# <a name="build-your-first-serverless-app-by-using-azure-logic-apps-and-azure-functions-in-visual-studio"></a>Sestavte svou první aplikaci bez serveru pomocí Azure Logic Apps a Azure Functions v aplikaci Visual Studio

Cloudové aplikace můžete rychle vyvíjet a nasazovat pomocí nástrojů a možností bez serveru v Azure, například [Azure Logic Apps](../logic-apps/logic-apps-overview.md) a [Azure Functions](../azure-functions/functions-overview.md). Tento článek ukazuje, jak začít vytvářet aplikace bez serveru, která používá aplikaci logiky, která volá funkci Azure ve Visual Studiu. Další informace o řešeních bez serveru v Azure najdete v tématu [Azure bez serveru s funkcemi a Logic Apps](../logic-apps/logic-apps-serverless-overview.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li vytvořit aplikaci bez serveru v aplikaci Visual Studio, budete potřebovat:

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Následující nástroje: Pokud je ještě nemáte, Stáhněte a nainstalujte je.

  * [Visual Studio 2019, 2017 nebo 2015 (komunita nebo jiné edice)](https://aka.ms/download-visual-studio). 
  V tomto kurzu se používá sada Visual Studio Community 2017, která je zdarma.

    > [!IMPORTANT]
    > Když nainstalujete Visual Studio 2019 nebo 2017, ujistěte se, že jste vybrali úlohu **vývoj pro Azure** .

  * [Microsoft Azure SDK pro .NET (verze 2.9.1 nebo novější)](https://azure.microsoft.com/downloads/). 
  Další informace o sadě [Azure SDK pro .NET](/dotnet/azure/intro).

  * [Azure PowerShell.](https://github.com/Azure/azure-powershell#installation)

  * Azure Logic Apps nástroje pro verzi sady Visual Studio, kterou chcete:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)
  
    Azure Logic Apps Tools můžete stáhnout a nainstalovat přímo z Visual Studio Marketplace nebo si můžete přečíst, [jak toto rozšíření nainstalovat v rámci sady Visual Studio](/visualstudio/ide/finding-and-using-visual-studio-extensions). 
    Po dokončení instalace nezapomeňte sadu Visual Studio restartovat.

  * [Azure Functions Core Tools](https://www.npmjs.com/package/azure-functions-core-tools) pro funkce místního ladění.

* Přístup k webu při použití vloženého návrháře aplikace logiky

  Návrhář k vytváření prostředků v Azure a ke čtení vlastností a dat z konektorů ve vaší aplikaci logiky vyžaduje připojení k internetu. 
  Pokud například použijete konektor Dynamics CRM Online, návrhář zkontroluje ve vaší instanci CRM dostupné výchozí a vlastní vlastnosti.

## <a name="create-a-resource-group-project"></a>Vytvoření projektu skupiny prostředků

Začněte tím, že vytvoříte [projekt skupiny prostředků Azure](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) pro vaši aplikaci bez serveru. V Azure vytvoříte prostředky v rámci *skupiny prostředků*, což je logická kolekce, kterou používáte k organizování, správě a nasazování prostředků celé aplikace jako jediného assetu. V případě aplikace bez serveru v Azure zahrnuje vaše skupina prostředků prostředky pro Azure Logic Apps i Azure Functions. Další informace o [skupinách prostředků a prostředcích Azure](../azure-resource-manager/management/overview.md).

1. Spusťte Visual Studio a přihlaste se pomocí svého účtu Azure.

1. V nabídce **Soubor** vyberte **Nový** > **Projekt**.

   ![Vytvořit nový projekt v aplikaci Visual Studio](./media/logic-apps-serverless-get-started-vs/create-new-project-visual-studio.png)

1. V části **Nainstalováno**, vyberte **Visual C#** nebo **Visual Basic**. Pak vyberte **cloudová**  >  **Skupina prostředků Azure**.

   > [!NOTE]
   > Pokud neexistuje kategorie **cloudu** nebo projekt **skupiny prostředků Azure** , ujistěte se, že jste nainstalovali sadu Azure SDK pro Visual Studio.

   Pokud používáte Visual Studio 2019, postupujte podle následujících kroků:

   1. V poli **vytvořit nový projekt** vyberte šablonu projektu **skupiny prostředků Azure** pro Visual C# nebo Visual Basic a pak vyberte **Další**.

   1. Zadejte název a další informace o projektu, které chcete použít pro skupinu prostředků Azure. Po dokončení vyberte **Vytvořit**.

1. Zadejte název vašeho projektu a jeho umístění a pak vyberte **OK**.

   Visual Studio vás vyzve k výběru šablony ze seznamu šablon. 
   V tomto příkladu se používá šablona Azure pro rychlý Start, pomocí které můžete vytvořit aplikaci bez serveru, která obsahuje aplikaci logiky a volání funkce Azure Functions.

   > [!TIP]
   > Ve scénářích, kdy nechcete řešení předvádět do skupiny prostředků Azure, můžete použít šablonu prázdná **Aplikace logiky** , která pouze vytvoří prázdnou aplikaci logiky.

1. V seznamu **Zobrazit šablony z tohoto umístění** vyberte možnost **rychlý start Azure (GitHub.com/Azure/Azure-Quickstart-templates)**.

1. Do vyhledávacího pole zadejte jako filtr "Logic-App". Z výsledků vyberte šablonu **aplikace 101-Logic-App-and-Function-App** .

   ![Vybrat šablonu pro rychlý Start Azure](./media/logic-apps-serverless-get-started-vs/select-template.png)

   Visual Studio vytvoří a otevře řešení pro váš projekt skupiny prostředků. 
   Vámi zvolená šablona Azure pro rychlý Start vytvoří šablonu nasazení s názvem azuredeploy.jsv rámci projektu skupiny prostředků. Tato šablona nasazení zahrnuje definici pro jednoduchou aplikaci logiky, která se aktivuje požadavkem HTTP, volá funkci Azure a vrátí výsledek jako odpověď HTTP.

   ![Nové řešení bez serveru](./media/logic-apps-serverless-get-started-vs/create-serverless-solution.png)

1. V dalším kroku nasaďte řešení do Azure. Tuto možnost je třeba provést, než budete moci otevřít šablonu nasazení a zkontrolovat prostředky aplikace bez serveru.

## <a name="deploy-your-solution"></a>Nasazení řešení

Než budete moct aplikaci logiky otevřít v návrháři aplikace logiky v aplikaci Visual Studio, musíte mít skupinu prostředků Azure, která je už nasazená v Azure. Návrhář pak může vytvořit připojení k prostředkům a službám ve vaší aplikaci logiky. Pro tuto úlohu použijte následující postup k nasazení řešení ze sady Visual Studio do Azure Portal:

1. V Průzkumník řešení v místní nabídce vašeho projektu prostředku vyberte **nasadit**  >  **Nový**.

   ![Vytvořit nové nasazení pro skupinu prostředků](./media/logic-apps-serverless-get-started-vs/deploy.png)

1. Pokud ještě nejsou vybrané, vyberte své předplatné Azure a skupinu prostředků, které chcete nasadit. Pak vyberte **nasadit**.

   ![Nastavení nasazení](./media/logic-apps-serverless-get-started-vs/deploy-to-resource-group.png)

1. Pokud se zobrazí pole pro **úpravu parametrů** , zadejte názvy prostředků, které se mají použít pro vaši aplikaci logiky a aplikaci Azure Functions při nasazení, a pak nastavení uložte. Ujistěte se, že pro aplikaci Function App používáte globálně jedinečný název.

   ![Zadání názvů vaší aplikace logiky a aplikace Function App](./media/logic-apps-serverless-get-started-vs/logic-function-app-name-parameters.png)

   Když Visual Studio spustí nasazení do zadané skupiny prostředků, v okně **výstupu** sady Visual Studio se zobrazí stav nasazení vašeho řešení. 
   Po dokončení nasazení je vaše aplikace logiky živá v Azure Portal.

## <a name="edit-your-logic-app-in-visual-studio"></a>Úprava aplikace logiky v aplikaci Visual Studio

Pokud chcete aplikaci logiky upravit po nasazení, otevřete aplikaci logiky pomocí návrháře aplikace logiky v aplikaci Visual Studio.

1. V Průzkumník řešení v místní nabídce azuredeploy.jsv souboru vyberte **otevřít v návrháři aplikace logiky**.

   ![Otevřít azuredeploy.jsv návrháři aplikace logiky](./media/logic-apps-serverless-get-started-vs/open-logic-app-designer.png)

   > [!TIP]
   > Pokud tento příkaz v aplikaci Visual Studio 2019 nemáte, ověřte, že máte nejnovější aktualizace pro Visual Studio.

1. Po zobrazení pole **Vlastnosti aplikace logiky** v části **předplatné** vyberte předplatné Azure, pokud ještě není vybrané. V části **Skupina prostředků** vyberte skupinu prostředků a umístění, kam jste nasadili řešení, a pak vyberte **OK**.

   ![Vlastnosti aplikace logiky](./media/logic-apps-serverless-get-started-vs/logic-app-properties.png)

   Po otevření návrháře aplikace logiky můžete pokračovat v přidávání kroků nebo měnit pracovní postup a uložit aktualizace.

   ![Otevřená aplikace logiky v návrháři aplikace logiky](./media/logic-apps-serverless-get-started-vs/opened-logic-app.png)

## <a name="create-your-azure-functions-project"></a>Vytvoření projektu Azure Functions

Pokud chcete vytvořit projekt funkcí a funkci pomocí JavaScriptu, Pythonu, F #, PowerShellu, Batch nebo bash, postupujte podle kroků v části [práce s Azure Functions Core Tools](../azure-functions/functions-run-local.md). Chcete-li vyvíjet funkci Azure Functions v rámci vašeho řešení pomocí jazyka C#, použijte knihovnu tříd C# podle kroků v části [publikování knihovny tříd .NET jako Function App](https://azure.microsoft.com/blog/).

## <a name="deploy-functions-from-visual-studio"></a>Nasazení funkcí ze sady Visual Studio

Vaše šablona nasazení nasadí všechny služby Azure Functions, které máte ve vašem řešení, z úložiště Git, které je určené proměnnými v azuredeploy.jsv souboru. Při vytváření a vytváření projektu Functions ve vašem řešení si můžete tento projekt ověřit ve správě zdrojového kódu Git (například GitHub nebo Azure DevOps) a pak aktualizovat `repo` proměnnou tak, aby šablona nasadila funkci Azure Functions.

## <a name="manage-logic-apps-and-view-run-history"></a>Správa aplikací logiky a zobrazení historie spuštění

Pro aplikace logiky, které jsou už nasazené v Azure, můžete i nadále upravovat, spravovat, zobrazovat historii spouštění pro a tyto aplikace z aplikace Visual Studio zakázat.

1. V nabídce **zobrazení** v aplikaci Visual Studio otevřete **Průzkumníka cloudu**.

1. V části **všechna předplatná** vyberte předplatné Azure přidružené k Logic Apps, které chcete spravovat, a pak vyberte **použít**.

1. V části **Logic Apps** vyberte svou aplikaci logiky. V místní nabídce této aplikace vyberte **otevřít pomocí programu Logic App Editor**.

   > [!TIP]
   > Pokud tento příkaz v aplikaci Visual Studio 2019 nemáte, ověřte, že máte nejnovější aktualizace pro Visual Studio.

Teď si můžete stáhnout již publikovanou aplikaci logiky do projektu skupiny prostředků. Takže i když jste mohli spustit aplikaci logiky v Azure Portal, můžete tuto aplikaci i nadále importovat a spravovat v aplikaci Visual Studio. Další informace najdete v tématu [Správa aplikací logiky pomocí sady Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).

## <a name="next-steps"></a>Další kroky

* [Správa aplikací logiky s využitím sady Visual Studio](manage-logic-apps-with-visual-studio.md)

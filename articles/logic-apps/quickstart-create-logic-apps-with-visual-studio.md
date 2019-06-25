---
title: Vytvořte automatizované pracovní postupy pomocí sady Visual Studio – Azure Logic Apps
description: Automatizace úloh, obchodních procesů a pracovních postupů pro podnikovou integraci pomocí Azure Logic Apps a sady Visual Studio
services: logic-apps
ms.service: logic-apps
ms.workload: azure-vs
author: ecfan
ms.author: estfan
ms.topic: quickstart
ms.custom: mvc
ms.reviewer: klam, LADocs
ms.suite: integration
ms.date: 04/25/2019
ms.openlocfilehash: a8857d62b3078d78bdd0a339ebadf766ddb2fb43
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295822"
---
# <a name="quickstart-create-automated-tasks-processes-and-workflows-with-azure-logic-apps---visual-studio"></a>Rychlý start: Vytváření automatizovaných úloh, procesů a pracovních postupů s Azure Logic Apps – Visual Studio

Pomocí [Azure Logic Apps](../logic-apps/logic-apps-overview.md) a sady Visual Studio můžete vytvářet pracovní postupy automatizující úlohy a procesy pro integraci aplikací, dat, systémů a služeb napříč podniky a organizacemi. Tento rychlý start ukazuje, jak můžete navrhovat a sestavovat tyto pracovní postupy vytvářením aplikací logiky v sadě Visual Studio a nasazováním těchto aplikací do Azure. I když můžete provádět tyto úlohy na portálu Azure, Visual Studio vám umožňuje přidávat aplikace logiky do správy zdrojového kódu, publikovat různé verze a vytvářet šablony Azure Resource Manageru pro různá prostředí nasazení.

Pokud se službou Azure Logic Apps teprve začínáte a zajímají o základních konceptech, zkuste [rychlý start k vytvoření aplikace logiky na webu Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md). Návrhář aplikace logiky funguje podobně webu Azure portal a sady Visual Studio.

V tomto rychlém startu vytvoříte stejnou aplikaci logiky pomocí sady Visual Studio jako rychlý start Azure portal. Tato aplikace logiky monitoruje informační kanál RSS webu a odešle e-mail za každou novou položku v tomto kanálu. Vaše hotová aplikace logiky bude vypadat jako tento pracovní postup vysoké úrovně:

![Hotová aplikace logiky](./media/quickstart-create-logic-apps-with-visual-studio/overview.png)

<a name="prerequisites"></a>

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Pokud je ještě nemáte, stáhněte a nainstalujte tyto nástroje:

  * [Edice sady Visual Studio. 2019 2017 nebo 2015 – Community nebo vyšší](https://aka.ms/download-visual-studio). 
  Tento rychlý start používá Visual Studio Community 2017.

    > [!IMPORTANT]
    > Když instalujete Visual Studio 2019 nebo 2017, ujistěte se, že jste vybrali **vývoj pro Azure** pracovního vytížení.

  * [Microsoft Azure SDK pro .NET (2.9.1 nebo novější)](https://azure.microsoft.com/downloads/). 
  Další informace o sadě [Azure SDK pro .NET](https://docs.microsoft.com/dotnet/azure/dotnet-tools?view=azure-dotnet).

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

  * Azure Logic Apps Tools pro verzi sady Visual Studio, které chcete:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)
  
    Azure Logic Apps Tools můžete stáhnout a nainstalovat přímo z Visual Studio Marketplace nebo si můžete přečíst, [jak toto rozšíření nainstalovat v rámci sady Visual Studio](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions). 
    Po dokončení instalace nezapomeňte sadu Visual Studio restartovat.

* Přístup k webu při používání vloženého Návrháře aplikace logiky

  Návrhář potřebuje připojení k Internetu vytvářet prostředky v Azure a ke čtení vlastností a dat z konektorů ve vaší aplikaci logiky. 
  Například pro připojení k Dynamics CRM Online Návrhář zkontroluje ve vaší instanci CRM výchozí a vlastní vlastnosti.

* E-mailový účet podporovaný v Logic Apps, jako je Office 365 Outlook, Outlook.com nebo Gmail. Pokud máte jiného poskytovatele, [tady se podívejte na seznam konektorů](https://docs.microsoft.com/connectors/). Tento příklad používá Office 365 Outlook. Pokud použijete jiného poskytovatele, celkový postup bude stejný, ale vaše uživatelské rozhraní se může mírně lišit.

## <a name="create-azure-resource-group-project"></a>Vytvoření projektu skupiny prostředků Azure

Začněte vytvořením [projektu skupiny prostředků Azure](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md). Další informace o [skupinách prostředků a prostředcích Azure](../azure-resource-manager/resource-group-overview.md).

1. Spusťte Visual Studio. Přihlaste se pomocí svého účtu Azure.

1. V nabídce **Soubor** vyberte **Nový** > **Projekt**. (Klávesnice: Ctrl+Shift+N)

   ![Výběr možnosti Nový > Projekt v nabídce Soubor](./media/quickstart-create-logic-apps-with-visual-studio/create-new-visual-studio-project.png)

1. V části **Nainstalováno**, vyberte **Visual C#** nebo **Visual Basic**. Vyberte **Cloud** > **Skupina prostředků Azure**. Pojmenujte svůj projekt, například:

   ![Vytvoření projektu skupiny prostředků Azure](./media/quickstart-create-logic-apps-with-visual-studio/create-azure-cloud-service-project.png)

   > [!NOTE]
   > Pokud **cloudu** nebo **skupiny prostředků Azure** nebude nezobrazí, ujistěte se, že instalaci sady Azure SDK pro Visual Studio.

   Pokud používáte Visual Studio 2019, postupujte podle těchto kroků:

   1. V **vytvořte nový projekt** vyberte **skupiny prostředků Azure** projektu vizuálu C# nebo Visual Basic. Zvolte **Další**.

   1. Zadejte název skupiny prostředků Azure, které chcete používat a další informace o projektu. Zvolte **Vytvořit**.

1. V seznamu šablon vyberte **aplikace logiky** šablony. Zvolte **OK**.

   ![Výběr šablony Aplikace logiky](./media/quickstart-create-logic-apps-with-visual-studio/select-logic-app-template.png)

   Jakmile sada Visual Studio vytvoří projekt, otevře se Průzkumník řešení, ve kterém se zobrazí vaše řešení. 
   Ve vašem řešení **LogicApp.json** soubor nejen ukládá definici aplikace logiky, ale je také šablonu Azure Resource Manageru, který používáte pro nasazení.

   ![Průzkumník řešení se zobrazeným novým řešením aplikace logiky a souborem nasazení](./media/quickstart-create-logic-apps-with-visual-studio/logic-app-solution-created.png)

## <a name="create-blank-logic-app"></a>Vytvoření prázdné aplikace logiky

Pokud máte projekt, vytvořte aplikaci logiky s skupinu prostředků Azure **prázdná aplikace logiky** šablony.

1. V Průzkumníku řešení otevřete **LogicApp.json** místní nabídky souboru. Vyberte **Otevřít pomocí Návrháře aplikace logiky**. (Klávesnice: Ctrl+L)

   ![Otevření souboru .json aplikace logiky pomocí Návrháře aplikace logiky](./media/quickstart-create-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Pokud tento příkaz nemáte k dispozici v aplikaci Visual Studio 2019, zkontrolujte, že máte nejnovější aktualizace pro sadu Visual Studio.

   Visual Studio vás vyzve k zadání vašeho předplatného Azure a skupinu prostředků Azure pro vytváření a nasazování prostředků pro vaši aplikaci logiky a připojením.

1. Pro **předplatné**, vyberte své předplatné Azure. Pro **skupiny prostředků**vyberte **vytvořit nový** vytvořit novou skupinu prostředků Azure.

   ![Výběr předplatného Azure, skupiny prostředků a umístění prostředků](./media/quickstart-create-logic-apps-with-visual-studio/select-azure-subscription-resource-group-location.png)

   | Nastavení | Příklad hodnoty | Popis |
   | ------- | ------------- | ----------- |
   | Výpis profilu uživatele | Contoso <br> jamalhartnett@contoso.com | Ve výchozím nastavení se jedná o účet, který jste použili k přihlášení |
   | **Předplatné** | Průběžné platby <br> (jamalhartnett@contoso.com) | Název vašeho předplatného Azure a přidruženého účtu |
   | **Skupina prostředků** | MyLogicApp-RG <br> (Západní USA) | Skupina prostředků Azure a umístění pro ukládání a nasazování prostředků vaší aplikace logiky |
   | **Location** | MyLogicApp-RG2 <br> (Západní USA) | Jiné umístění, pokud nechcete použít umístění skupiny prostředků |
   ||||

1. Návrhář pro Logic Apps se otevře stránka, která zobrazí video úvod a běžně používanými triggery. Posunout dolů za video a triggery máme **šablony**a vyberte **prázdná aplikace logiky**.

   ![Výběr možnosti Prázdná aplikace logiky](./media/quickstart-create-logic-apps-with-visual-studio/choose-blank-logic-app-template.png)

## <a name="build-logic-app-workflow"></a>Sestavení pracovního postupu aplikace logiky

V dalším kroku přidejte RSS [aktivační událost](../logic-apps/logic-apps-overview.md#logic-app-concepts) , který je vyvoláno, když se objeví nová položka informačního kanálu. Každá aplikace logiky začíná triggerem, který se spustí při určitých kritérií je splněna. Pokaždé, když se trigger aktivuje, vytvoří modul Logic Apps instanci aplikace logiky pro spuštění vašeho pracovního postupu.

1. V návrháři aplikace logiky zvolte pod vyhledávacím polem **všechny**.
Do vyhledávacího pole zadejte "rss". Ze seznamu triggerů vyberte tento trigger: **Když se publikuje položka kanálu - RSS**

   ![Sestavení aplikace logiky přidáním triggeru a akcí](./media/quickstart-create-logic-apps-with-visual-studio/add-trigger-logic-app.png)

1. Po aktivační událost se zobrazí v návrháři, Dokončit sestavování aplikace logiky pomocí následujícího postupu kroky [rychlém startu pro Azure portal](../logic-apps/quickstart-create-first-logic-app-workflow.md#add-rss-trigger)a pak se vraťte k tomuto článku. Jakmile budete hotovi, vaše aplikace logiky bude vypadat jako v tomto příkladu:

   ![Hotová aplikace logiky](./media/quickstart-create-logic-apps-with-visual-studio/finished-logic-app.png)

1. Uložte řešení sady Visual Studio. (Klávesnice: Ctrl + S)

## <a name="deploy-logic-app-to-azure"></a>Nasazení aplikace logiky do Azure

Aby bylo možné spustit a otestujte aplikaci logiky, aplikaci nasaďte do Azure ze sady Visual Studio.

1. V Průzkumníku řešení v místní nabídce vašeho projektu vyberte **Nasadit** > **Nový**. Pokud se zobrazí výzva, přihlaste se pomocí svého účtu Azure.

   ![Vytvoření nasazení aplikace logiky](./media/quickstart-create-logic-apps-with-visual-studio/create-logic-app-deployment.png)

1. Pro toto nasazení ponechte výchozí předplatné Azure, prostředek skupiny a další nastavení. Zvolte **nasazení**.

   ![Nasazení aplikace logiky do skupiny prostředků Azure](./media/quickstart-create-logic-apps-with-visual-studio/select-azure-subscription-resource-group-deployment.png)

1. Pokud **upravit parametry** pole se zobrazí, zadejte název prostředku aplikace logiky. Uložte nastavení.

   ![Zadání názvu nasazení pro aplikaci logiky](./media/quickstart-create-logic-apps-with-visual-studio/edit-parameters-deployment.png)

   Po zahájení nasazování se v okně **Výstup** sady Visual Studio zobrazí stav nasazení vaší aplikace. Pokud se stav nezobrazí, otevřete seznam **Zobrazit výstup z** a vyberte svou skupinu prostředků Azure.

   ![Výstup stavu nasazení](./media/quickstart-create-logic-apps-with-visual-studio/logic-app-output-window.png)

   Pokud vybrané konektory potřebovat vstup od uživatele, okno prostředí PowerShell se otevře na pozadí a vyzve k zadání jakékoli nezbytné hesla nebo tajných klíčů. Po zadání těchto informací bude nasazení pokračovat.

   ![Okno prostředí PowerShell](./media/quickstart-create-logic-apps-with-visual-studio/logic-apps-powershell-window.png)

   Po dokončení nasazení vaší aplikace logiky spuštěná na webu Azure portal a spustí podle zadaného plánu (každou minutu). Pokud se aktivační událost najde nové položky informačního kanálu, trigger se spustí, která vytvoří instanci pracovního postupu, na kterém běží akce aplikace logiky. Aplikace logiky odešle e-mail za každou novou položku. V opačném případě pokud trigger nenajde nové položky, neaktivuje trigger a "přeskočí" vytvoření instance pracovního postupu. Aplikace logiky počká na další interval před vrácením se změnami.

   Tady jsou ukázkové e-maily, které tato aplikace logiky odešle. 
   Pokud neobdržíte žádné e-maily, zkontrolujte složku s nevyžádanými e-maily.

   ![Outlook odešle e-mail za každou novou položku RRSS](./media/quickstart-create-logic-apps-with-visual-studio/outlook-email.png)

Blahopřejeme, právě jste úspěšně vytvořili a nasadili svou aplikaci logiky pomocí sady Visual Studio. Informace o správě aplikace logiky a kontrole historie jejího spuštění najdete v tématu [Správa aplikací logiky s využitím sady Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Jakmile budete hotovi s vaší aplikací logiky, odstraňte skupinu prostředků, která obsahuje vaši aplikaci logiky a související prostředky.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí stejného účtu, který jste použili k vytvoření své aplikace logiky.

1. V hlavní nabídce Azure vyberte **Skupiny prostředků**.
Vyberte skupinu prostředků vaší aplikace logiky a vyberte **přehled**.

1. Na stránce **Přehled** zvolte **Odstranit skupinu prostředků**. Pro ověření zadejte název skupiny prostředků a zvolte **Odstranit**.

   ![Skupiny prostředků > Přehled > Odstranit skupinu prostředků](./media/quickstart-create-logic-apps-with-visual-studio/delete-resource-group.png)

1. Odstraňte řešení sady Visual Studio ze svého místního počítače.

## <a name="next-steps"></a>Další postup

V tomto článku jste pomocí sady Visual Studio sestavili, nasadili a spustili aplikaci logiky. Další informace o správě a provádění pokročilého nasazení aplikací logiky pomocí sady Visual Studio, najdete v těchto článcích:

> [!div class="nextstepaction"]
> * [Správa aplikací logiky s využitím sady Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)
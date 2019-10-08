---
title: Vytváření automatizovaných pracovních postupů pomocí sady Visual Studio – Azure Logic Apps
description: Automatizace úloh, obchodních procesů a pracovních postupů pro podnikovou integraci pomocí Azure Logic Apps a sady Visual Studio
services: logic-apps
ms.service: logic-apps
ms.suite: integration
ms.workload: azure-vs
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.manager: carmonm
ms.topic: quickstart
ms.custom: mvc
ms.date: 04/25/2019
ms.openlocfilehash: 194004362449f2f59a53d45e18e8dd72a29cc79a
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029563"
---
# <a name="quickstart-create-automated-tasks-processes-and-workflows-with-azure-logic-apps---visual-studio"></a>Rychlý Start: vytvoření automatizovaných úloh, procesů a pracovních postupů pomocí Azure Logic Apps – Visual Studio

Pomocí [Azure Logic Apps](../logic-apps/logic-apps-overview.md) a sady Visual Studio můžete vytvářet pracovní postupy pro automatizaci úloh a procesů, které integrují aplikace, data, systémy a služby napříč podniky a organizacemi. V tomto rychlém startu se dozvíte, jak můžete navrhovat a sestavovat tyto pracovní postupy vytvořením Logic Apps v aplikaci Visual Studio a nasazením těchto aplikací do Azure. I když můžete provádět tyto úlohy v Azure Portal, Visual Studio umožňuje přidat vaše aplikace logiky do správy zdrojových kódů, publikovat různé verze a vytvářet Azure Resource Manager šablony pro různá prostředí nasazení.

Pokud s Azure Logic Apps teprve začínáte a chcete jenom základní koncepty, vyzkoušejte si [rychlý Start pro vytvoření aplikace logiky v Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md). Návrhář aplikace logiky funguje podobně jak v Azure Portal, tak i v sadě Visual Studio.

V tomto rychlém startu vytvoříte stejnou aplikaci logiky pomocí sady Visual Studio jako rychlý Start Azure Portal. Tato aplikace logiky monitoruje informační kanál RSS webu a odesílá e-maily pro každou novou položku v tomto informačním kanálu. Vaše dokončená aplikace logiky bude vypadat jako tento pracovní postup vysoké úrovně:

![Přehled pracovního postupu vysoké úrovně aplikace logiky](./media/quickstart-create-logic-apps-with-visual-studio/high-level-workflow-overview.png)

<a name="prerequisites"></a>

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [Zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Stáhněte si a nainstalujte tyto nástroje, pokud je ještě nemáte:

  * [Visual Studio 2019, 2017 nebo 2015 – Community Edition nebo vyšší](https://aka.ms/download-visual-studio). 
  V tomto rychlém startu se používá Visual Studio Community 2017.

    > [!IMPORTANT]
    > Když nainstalujete Visual Studio 2019 nebo 2017, ujistěte se, že jste vybrali úlohu **vývoj pro Azure** .

  * [Microsoft Azure SDK pro .NET (2.9.1 nebo novější)](https://azure.microsoft.com/downloads/). 
  Přečtěte si další informace o [sadě Azure SDK pro .NET](https://docs.microsoft.com/dotnet/azure/dotnet-tools?view=azure-dotnet).

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

  * Azure Logic Apps nástroje pro verzi sady Visual Studio, kterou chcete:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)
  
    Můžete si buď stáhnout a nainstalovat Azure Logic Apps nástroje přímo z Visual Studio Marketplace, nebo zjistit, [jak toto rozšíření nainstalovat ze sady Visual Studio](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions). 
    Po dokončení instalace je nutné restartovat aplikaci Visual Studio.

* Přístup k webu při použití vloženého návrháře aplikace logiky

  Návrhář potřebuje připojení k Internetu, aby mohl vytvářet prostředky v Azure a číst vlastnosti a data z konektorů ve vaší aplikaci logiky. 
  Například pro připojení Dynamics CRM Online Návrhář kontroluje vaši instanci CRM na výchozí a vlastní vlastnosti.

* E-mailový účet, který podporuje Logic Apps, například Office 365 Outlook, Outlook.com nebo Gmail. Pro jiné poskytovatele [Zkontrolujte seznam konektorů](https://docs.microsoft.com/connectors/). V tomto příkladu se používá Office 365 Outlook. Pokud používáte jiného poskytovatele, je celkový postup stejný, ale vaše uživatelské rozhraní se může mírně lišit.

<a name="create-resource-group-project"></a>

## <a name="create-azure-resource-group-project"></a>Vytvoření projektu skupiny prostředků Azure

Začněte tím, že vytvoříte [projekt skupiny prostředků Azure](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md). Přečtěte si další informace o [skupinách prostředků a prostředcích Azure](../azure-resource-manager/resource-group-overview.md).

1. Spusťte Visual Studio. Přihlaste se pomocí svého účtu Azure.

1. V nabídce **soubor** vyberte **Nový** **projekt** > . (Klávesnice: Ctrl + Shift + N)

   ![Vytvořit nový projekt sady Visual Studio](./media/quickstart-create-logic-apps-with-visual-studio/create-new-visual-studio-project.png)

1. V části **nainstalováno**vyberte možnost **Visual C#**  nebo **Visual Basic**. Vyberte **Cloud** > **Skupina prostředků Azure**. Pojmenujte projekt, například:

   ![Vytvoření projektu skupiny prostředků Azure](./media/quickstart-create-logic-apps-with-visual-studio/create-azure-cloud-service-project.png)

   > [!NOTE]
   > Názvy skupin prostředků můžou obsahovat jenom písmena, číslice, tečky (`.`), podtržítka (`_`), spojovníky (`-`) a kulaté závorky (`(`, `)`), ale nemůžou *končit* tečkami (`.`).
   >
   > Pokud se **Cloud** nebo **Skupina prostředků Azure** nezobrazí, ujistěte se, že jste nainstalovali sadu Azure SDK pro Visual Studio.

   Pokud používáte Visual Studio 2019, postupujte podle následujících kroků:

   1. V poli **vytvořit nový projekt** vyberte projekt **skupiny prostředků Azure** pro Visual C# nebo Visual Basic. Klikněte na tlačítko **Další**.

   1. Zadejte název skupiny prostředků Azure, kterou chcete použít, a další informace o projektu. Vyberte **vytvořit**.

1. V seznamu šablon vyberte šablonu **Aplikace logiky** . Klikněte na **tlačítko OK**.

   ![Vyberte šablonu aplikace logiky pro vytvoření projektu.](./media/quickstart-create-logic-apps-with-visual-studio/select-logic-app-template.png)

   Poté, co Visual Studio vytvoří projekt, Průzkumník řešení otevře a zobrazí vaše řešení. 
   Ve vašem řešení soubor **LogicApp. JSON** nejen ukládá definici aplikace logiky, ale je také šablonou Azure Resource Manager, kterou můžete použít pro nasazení.

   ![Průzkumník řešení zobrazuje nové řešení aplikace logiky a soubor nasazení](./media/quickstart-create-logic-apps-with-visual-studio/logic-app-solution-created.png)

## <a name="create-blank-logic-app"></a>Vytvoření prázdné aplikace logiky

Když máte projekt skupiny prostředků Azure, vytvořte aplikaci logiky pomocí **prázdné šablony aplikace logiky** .

1. V Průzkumník řešení otevřete místní nabídku souboru **LogicApp. JSON** . Vyberte **otevřít pomocí návrháře aplikace logiky**. (Klávesnice: CTRL + L)

   ![Otevření souboru Logic App. JSON pomocí návrháře aplikace logiky](./media/quickstart-create-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Pokud tento příkaz v aplikaci Visual Studio 2019 nemáte, ověřte, že máte nejnovější aktualizace pro Visual Studio.

   Visual Studio vás vyzve k zadání předplatného Azure a skupiny prostředků Azure pro vytváření a nasazování prostředků pro vaši aplikaci logiky a připojení.

1. Jako **předplatné**vyberte své předplatné Azure. V případě **skupiny prostředků**vyberte **vytvořit novou** a vytvořte novou skupinu prostředků Azure.

   ![Výběr předplatného Azure, skupiny prostředků a umístění prostředku](./media/quickstart-create-logic-apps-with-visual-studio/select-azure-subscription-resource-group-location.png)

   | Nastavením | Ukázková hodnota | Popis |
   | ------- | ------------- | ----------- |
   | Seznam profilů uživatelů | Contoso <br> jamalhartnett@contoso.com | Ve výchozím nastavení se jedná o účet, který jste použili k přihlášení. |
   | **Formě** | Průběžné platby <br> (jamalhartnett@contoso.com) | Název vašeho předplatného Azure a přidruženého účtu |
   | **Skupina prostředků** | MyLogicApp – RG <br> (Západní USA) | Skupina prostředků Azure a umístění pro ukládání a nasazování prostředků vaší aplikace logiky |
   | **Poloha** | MyLogicApp – RG2 <br> (Západní USA) | Jiné umístění, pokud nechcete použít umístění skupiny prostředků |
   ||||

1. Návrhář Logic Apps otevře stránku se zobrazeným úvodním videem a běžně používanými triggery. Posuňte se dolů za video a triggery do **šablon**a vyberte **prázdná aplikace logiky**.

   ![Výběr možnosti prázdná aplikace logiky](./media/quickstart-create-logic-apps-with-visual-studio/choose-blank-logic-app-template.png)

## <a name="build-logic-app-workflow"></a>Vytvoření pracovního postupu aplikace logiky

Dále přidejte [aktivační událost](../logic-apps/logic-apps-overview.md#logic-app-concepts) RSS, která se aktivuje při zobrazení nové položky informačního kanálu. Každá aplikace logiky se spouští triggerem, který se aktivuje při splnění určitých kritérií. Pokaždé, když se Trigger aktivuje, modul Logic Apps vytvoří instanci aplikace logiky, která spouští váš pracovní postup.

1. V návrháři aplikace logiky pod vyhledávacím polem vyberte **vše**.
Do vyhledávacího pole zadejte "RSS". V seznamu triggery vyberte tuto aktivační událost: **když je publikovaná položka informačního kanálu – RSS**

   ![Sestavte aplikaci logiky přidáním triggeru a akcí.](./media/quickstart-create-logic-apps-with-visual-studio/add-trigger-logic-app.png)

1. Po zobrazení triggeru v Návrháři dokončete vytváření aplikace logiky pomocí kroků pracovního postupu v [rychlém startu Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md#add-rss-trigger)a pak se vraťte k tomuto článku. Až budete hotovi, vaše aplikace logiky bude vypadat jako v tomto příkladu:

   ![Dokončení ukázkového pracovního postupu aplikace logiky](./media/quickstart-create-logic-apps-with-visual-studio/finished-logic-app-workflow.png)

1. Uložte řešení sady Visual Studio. (Klávesnice: CTRL + S)

<a name="deploy-to-Azure"></a>

## <a name="deploy-logic-app-to-azure"></a>Nasazení aplikace logiky do Azure

Předtím, než budete moci spustit a otestovat aplikaci logiky, nasaďte aplikaci do Azure ze sady Visual Studio.

1. V místní nabídce projektu v Průzkumník řešení vyberte **nasadit** > **nové**. Pokud se zobrazí výzva, přihlaste se pomocí svého účtu Azure.

   ![Vytvořit nové nasazení aplikace logiky](./media/quickstart-create-logic-apps-with-visual-studio/create-logic-app-deployment.png)

1. Pro toto nasazení ponechte výchozí předplatné Azure, skupinu prostředků a další nastavení. Vyberte **nasadit**.

   ![Nasazení aplikace logiky do skupiny prostředků Azure](./media/quickstart-create-logic-apps-with-visual-studio/select-azure-subscription-resource-group-deployment.png)

1. Pokud se zobrazí pole **Upravit parametry** , zadejte název prostředku aplikace logiky. Uložte nastavení.

   ![Zadat název nasazení pro aplikaci logiky](./media/quickstart-create-logic-apps-with-visual-studio/edit-parameters-deployment.png)

   Po zahájení nasazení se v okně **výstup** sady Visual Studio zobrazí stav nasazení vaší aplikace. Pokud se stav nezobrazí, otevřete seznam **Zobrazit výstup ze** a vyberte skupinu prostředků Azure.

   ![Stav nasazení v okně výstup aplikace Visual Studio](./media/quickstart-create-logic-apps-with-visual-studio/logic-app-output-window.png)

   Pokud vaše vybrané konektory potřebují vstup z vaší aplikace, otevře se okno PowerShellu na pozadí a zobrazí se výzva k zadání potřebných hesel nebo tajných klíčů. Po zadání těchto informací bude nasazení pokračovat.

   ![Příkazový řádek PowerShellu pro hesla nebo tajné klíče](./media/quickstart-create-logic-apps-with-visual-studio/logic-apps-powershell-window.png)

   Po dokončení nasazení vaše aplikace logiky bude v Azure Portal živá a spustí se podle zadaného plánu (každou minutu). Pokud aktivační událost najde nové položky informačního kanálu, aktivuje se Trigger, který vytvoří instanci pracovního postupu, která spustí akce aplikace logiky. Vaše aplikace logiky posílá e-mail pro každou novou položku. V opačném případě, pokud aktivační procedura nenajde nové položky, aktivační událost se neaktivuje a "přeskočí" vytváření instancí pracovního postupu. Vaše aplikace logiky počká do dalšího intervalu před kontrolou.

   Tady jsou ukázkové e-maily, které tato aplikace logiky posílá. 
   Pokud neobdržíte žádné e-maily, podívejte se do složky Nevyžádaná pošta.

   ![Outlook pošle e-maily pro každou novou položku RSS.](./media/quickstart-create-logic-apps-with-visual-studio/example-outlook-email.png)

Gratulujeme, úspěšně jste vytvořili a nasadili aplikaci logiky pomocí sady Visual Studio. Chcete-li spravovat aplikaci logiky a zkontrolovat historii spuštění, přečtěte si téma [Správa aplikací logiky pomocí sady Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).

## <a name="add-new-logic-app"></a>Přidat novou aplikaci logiky

Pokud máte existující projekt skupiny prostředků Azure, můžete do tohoto projektu přidat novou prázdnou aplikaci logiky pomocí okna osnova JSON.

1. V Průzkumník řešení otevřete soubor `<logic-app-name>.json`.

1. V nabídce **zobrazení** vyberte jiné @no__t **Windows**-2 –**Osnova JSON**.

1. Chcete-li přidat prostředek do souboru šablony, vyberte možnost **Přidat prostředek** v horní části okna osnovy JSON. V okně Osnova JSON klikněte pravým tlačítkem na **prostředky**a vyberte **Přidat nový prostředek**.

   ![V okně osnovy JSON přidejte nový prostředek.](./media/quickstart-create-logic-apps-with-visual-studio/json-outline-window-add-resource.png)

1. V dialogovém okně **Přidat prostředek** Najděte a vyberte **Aplikace logiky**. Pojmenujte aplikaci logiky a klikněte na tlačítko **Přidat**.

   ![Přidat nový prostředek aplikace logiky do projektu](./media/quickstart-create-logic-apps-with-visual-studio/add-logic-app-resource.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete s aplikací logiky hotovi, odstraňte skupinu prostředků, která obsahuje vaši aplikaci logiky a související prostředky.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí stejného účtu, který jste použili k vytvoření vaší aplikace logiky.

1. V hlavní nabídce Azure vyberte **skupiny prostředků**.
Vyberte skupinu prostředků vaší aplikace logiky a vyberte **Přehled**.

1. Na stránce **Přehled** vyberte možnost **Odstranit skupinu prostředků**. Jako potvrzení zadejte název skupiny prostředků a klikněte na **Odstranit**.

   ![Odstranit skupinu prostředků aplikace logiky](./media/quickstart-create-logic-apps-with-visual-studio/delete-resource-group.png)

1. Odstraňte řešení sady Visual Studio z místního počítače.

## <a name="next-steps"></a>Další kroky

V tomto článku jste vytvořili, nasadili a spustili aplikaci logiky pomocí sady Visual Studio. Další informace o správě a provádění pokročilého nasazení Logic Apps se sadou Visual Studio najdete v těchto článcích:

> [!div class="nextstepaction"]
> * [Správa aplikací logiky pomocí sady Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)
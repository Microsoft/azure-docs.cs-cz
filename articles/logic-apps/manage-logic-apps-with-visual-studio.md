---
title: Úprava a Správa aplikací logiky pomocí sady Visual Studio s Průzkumníkem cloudu
description: Úprava, aktualizace, Správa, přidání do správy zdrojového kódu a nasazení aplikací logiky pomocí sady Visual Studio s Průzkumníkem cloudu
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: article
ms.custom: mvc
ms.date: 04/29/2020
ms.openlocfilehash: cde8db5310e3ede2721ba327b28c789ccd0b7dd0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87280762"
---
# <a name="manage-logic-apps-with-visual-studio"></a>Správa aplikací logiky s využitím sady Visual Studio

I když můžete vytvářet, upravovat, spravovat a nasazovat Logic Apps v [Azure Portal](https://portal.azure.com), můžete také použít Visual Studio, pokud chcete přidat své aplikace logiky do správy zdrojových kódů, publikovat různé verze a vytvářet [Azure Resource Manager](../azure-resource-manager/management/overview.md) šablony pro různá prostředí nasazení. Pomocí Průzkumníka cloudu sady Visual Studio můžete najít a spravovat aplikace logiky spolu s dalšími prostředky Azure. Můžete například otevřít, stáhnout, upravit, spustit, zobrazit historii spuštění, zakázat a povolit aplikace logiky, které jsou již nasazeny v Azure Portal. Pokud s Azure Logic Apps v aplikaci Visual Studio začínáte, přečtěte si, [jak vytvářet aplikace logiky pomocí sady Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

[Aplikace logiky můžete spravovat také v Azure Portal](manage-logic-apps-with-azure-portal.md).

> [!IMPORTANT]
> Nasazení nebo publikování aplikace logiky ze sady Visual Studio přepíše verzi této aplikace v Azure Portal. Takže pokud provedete změny v Azure Portal, které chcete zachovat, nezapomeňte [aktualizovat aplikaci logiky v aplikaci Visual Studio](#refresh) z Azure Portal před dalším nasazením nebo publikováním ze sady Visual Studio.

<a name="requirements"></a>

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Pokud je ještě nemáte, stáhněte a nainstalujte tyto nástroje:

  * [Visual Studio 2019, 2017 nebo 2015 – Community Edition nebo vyšší](https://aka.ms/download-visual-studio). V tomto kurzu se používá sada Visual Studio Community 2017, která je zdarma.

    > [!IMPORTANT]
    > Když nainstalujete Visual Studio 2019 nebo 2017, ujistěte se, že jste vybrali úlohu **vývoj pro Azure** .
    > Další informace najdete v tématu [Správa prostředků přidružených k vašim účtům Azure v Průzkumníkovi cloudu sady Visual Studio](/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer).

    Chcete-li nainstalovat Průzkumníka cloudu pro Visual Studio 2015, [Stáhněte si z Visual Studio Marketplace Průzkumníka cloudu](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVisualStudio2015). Další informace najdete v tématu [Správa prostředků přidružených k vašim účtům Azure v Průzkumníkovi cloudu sady Visual Studio (2015)](/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer?view=vs-2015).

  * [Sada Azure SDK (2.9.1 nebo novější)](https://azure.microsoft.com/downloads/)

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

  * Nejnovější Azure Logic Apps nástroje pro rozšíření sady Visual Studio pro požadovanou verzi:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)

    Azure Logic Apps Tools můžete stáhnout a nainstalovat přímo z Visual Studio Marketplace nebo si můžete přečíst, [jak toto rozšíření nainstalovat v rámci sady Visual Studio](/visualstudio/ide/finding-and-using-visual-studio-extensions). Po dokončení instalace nezapomeňte sadu Visual Studio restartovat.

  * Chcete-li použít předplatná Azure Government se sadou Visual Studio, přečtěte si následující témata pro další nastavení:

    * Visual Studio 2019: [rychlý Start: připojení k Azure Government pomocí sady Visual Studio](../azure-government/documentation-government-connect-vs.md)

    * Visual Studio 2017: [Představujeme rozšíření sady Visual Studio pro selektory prostředí Azure](https://devblogs.microsoft.com/azuregov/introducing-the-azure-environment-selector-visual-studio-extension/), které si můžete stáhnout a nainstalovat z [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=SteveMichelotti.AzureEnvironmentSelector).

* Přístup k webu při použití vloženého návrháře Logic Apps

  Návrhář k vytváření prostředků v Azure a ke čtení vlastností a dat z konektorů ve vaší aplikaci logiky vyžaduje připojení k internetu.

<a name="find-logic-apps-vs"></a>

## <a name="find-logic-apps"></a>Hledání aplikací logiky

V aplikaci Visual Studio můžete najít všechny aplikace logiky, které jsou přidruženy k vašemu předplatnému Azure a které jsou nasazeny v Azure Portal pomocí Průzkumníka cloudu.

1. Otevřete sadu Visual Studio. V nabídce **zobrazení** vyberte **Průzkumník cloudu**.

1. V Průzkumníku cloudu vyberte ikonu **Správa účtů** . Vyberte předplatné Azure přidružené k vašim Logic Apps a vyberte **použít**. Například:

   ![Vyberte Správa účtů.](./media/manage-logic-apps-with-visual-studio/account-management-select-Azure-subscription.png)

1. Vedle ikony **Správa účtů** vyberte **typy prostředků**. V rámci vašeho předplatného Azure rozbalte **Logic Apps** , abyste mohli zobrazit všechny nasazené aplikace logiky, které jsou přidružené k vašemu předplatnému.

V dalším kroku otevřete aplikaci logiky v editoru aplikace logiky.

<a name="open-designer"></a>

## <a name="open-logic-apps-in-visual-studio"></a>Otevřené aplikace logiky v aplikaci Visual Studio

V aplikaci Visual Studio můžete otevřít aplikace logiky, které byly dříve vytvořeny a nasazeny přímo prostřednictvím Azure Portal nebo jako projekty skupiny prostředků Azure pomocí sady Visual Studio.

1. [Otevřete Průzkumníka cloudu a najděte svoji aplikaci logiky](#find-logic-apps-vs).

1. V místní nabídce aplikace logiky vyberte **otevřít pomocí programu Logic App Editor**.

   > [!TIP]
   > Pokud tento příkaz v aplikaci Visual Studio 2019 nemáte, ověřte, že máte nejnovější aktualizace pro Visual Studio.

   ![Otevřít nasazenou aplikaci logiky z Azure Portal](./media/manage-logic-apps-with-visual-studio/open-logic-app-in-editor.png)

   Jakmile se aplikace logiky otevře v Návrháři Logic Apps, můžete v dolní části návrháře vybrat **zobrazení kódu** , abyste mohli zkontrolovat základní strukturu definice aplikace logiky. Pokud chcete vytvořit šablonu nasazení pro aplikaci logiky, přečtěte si, [Jak stáhnout šablonu Azure Resource Manager](#download-logic-app) pro tuto aplikaci logiky. Přečtěte si další informace o [Správce prostředků šablonách](../azure-resource-manager/templates/overview.md).

<a name="download-logic-app"></a>

## <a name="download-from-azure"></a>Stáhnout z Azure

Můžete [si stáhnout](../azure-resource-manager/templates/export-template-portal.md#export-template-from-a-resource) Logic apps z [Azure Portal](https://portal.azure.com) a uložit je jako šablonu [Azure Resource Manager](../azure-resource-manager/management/overview.md) . Pak můžete místně upravit šablony pomocí sady Visual Studio a přizpůsobit Logic Apps pro různá prostředí nasazení.  Automatické stahování aplikací logiky *parameterizes* jejich definice v [šablonách správce prostředků](../azure-resource-manager/templates/overview.md), které také používají JavaScript Object Notation (JSON).

1. V aplikaci Visual Studio pomocí Průzkumníka cloudu [otevřete aplikaci logiky, kterou chcete stáhnout z Azure](#open-designer).

1. V místní nabídce aplikace logiky vyberte **otevřít pomocí programu Logic App Editor**.

   > [!TIP]
   > Pokud tento příkaz v aplikaci Visual Studio 2019 nemáte, ověřte, že máte nejnovější aktualizace pro Visual Studio.

   Aplikace logiky se otevře v návrháři aplikace logiky.

1. Na panelu nástrojů návrháře vyberte **Stáhnout**.

   ![Stažení aplikace logiky z Azure Portal](./media/manage-logic-apps-with-visual-studio/download-logic-app-from-portal.png)

1. Po zobrazení výzvy k zadání umístění přejděte do tohoto umístění a uložte šablonu Správce prostředků pro definici aplikace logiky ve formátu JSON (. JSON).

   Vaše definice aplikace logiky se zobrazí v `resources` podčásti uvnitř šablony Správce prostředků. Nyní můžete upravit definici aplikace logiky a šablonu Správce prostředků pomocí sady Visual Studio. Šablonu můžete přidat také jako [projekt skupiny prostředků Azure](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) do řešení sady Visual Studio. Přečtěte si o [projektech skupin prostředků Azure pro Logic Apps v aplikaci Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

<a name="link-integration-account"></a>

## <a name="link-to-integration-account"></a>Odkaz na účet pro integraci

Pokud chcete vytvářet aplikace logiky pro podnikové integrace B2B (Business-to-Business), můžete aplikaci logiky propojit s dříve vytvořeným [integračním účtem](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) , který existuje ve stejné oblasti jako aplikace logiky. Účet pro integraci obsahuje artefakty B2B, jako jsou obchodní partneři, smlouvy, schémata a mapy, a umožňuje aplikaci logiky používat konektory B2B k ověřování XML a kódování a dekódování plochého souboru. I když [Tento odkaz můžete vytvořit pomocí Azure Portal](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account), můžete také použít Visual Studio po splnění [požadavků](#requirements)a vaše aplikace logiky existuje jako soubor JSON (. JSON) v [projektu skupiny prostředků Azure](../azure-resource-manager/templates/create-visual-studio-deployment-project.md). Přečtěte si o [projektech skupin prostředků Azure pro Logic Apps v aplikaci Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#create-resource-group-project).

1. V aplikaci Visual Studio otevřete projekt skupiny prostředků Azure, který obsahuje vaši aplikaci logiky.

1. V Průzkumník řešení otevřete místní nabídku souboru **<Logic-App-Name # C1.js** a v **Návrháři aplikace logiky vyberte otevřít**. (Klávesnice: CTRL + L)

   ![Otevření souboru. JSON aplikace logiky pomocí návrháře aplikace logiky](./media/manage-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Pokud tento příkaz v aplikaci Visual Studio 2019 nemáte, ověřte, že máte nejnovější aktualizace sady Visual Studio a rozšíření Azure Logic Apps Tools.

1. Ujistěte se, že návrhář aplikace logiky má fokus, a to tak, že vybere kartu nebo plochu návrháře, takže okno Vlastnosti zobrazí vlastnost **účet pro integraci** pro vaši aplikaci logiky.

   ![Okno Vlastnosti – vlastnost "účet pro integraci"](./media/manage-logic-apps-with-visual-studio/open-logic-app-properties-integration-account.png)

   > [!TIP]
   > Pokud okno Vlastnosti ještě není otevřený, v nabídce **zobrazení** vyberte **okno Vlastnosti**. (Klávesnice: stiskněte F4)

1. Otevřete seznam vlastností **účtu pro integraci** a vyberte účet pro integraci, který chcete propojit s vaší aplikací logiky, například:

   ![Otevřete seznam vlastností "účet pro integraci".](./media/manage-logic-apps-with-visual-studio/select-integration-account.png)

1. Až budete hotovi, nezapomeňte uložit řešení sady Visual Studio.

Když nastavíte vlastnost **integrační účet** v sadě Visual Studio a uložíte aplikaci logiky jako šablonu Azure Resource Manager, tato šablona obsahuje také deklaraci parametru pro vybraný účet integrace. Další informace o parametrech šablony a Logic Apps najdete v tématu [Přehled: Automatizace nasazení aplikace logiky](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters).

<a name="change-location"></a>

## <a name="change-deployment-location"></a>Změnit umístění nasazení

Pokud v sadě Visual Studio existuje vaše aplikace logiky jako soubor JSON (. JSON) v rámci [projektu skupiny prostředků Azure](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) , který používáte k automatizaci nasazení, je tato aplikace logiky nastavená na typ umístění a konkrétní umístění. Toto umístění je buď oblast Azure, nebo existující [prostředí služby Integration Service (ISE)](connect-virtual-network-vnet-isolated-environment.md).

Chcete-li změnit typ nebo umístění vaší aplikace logiky, je nutné otevřít soubor definice pracovního postupu aplikace logiky (. JSON) z Průzkumník řešení pomocí návrháře aplikace logiky. Tyto vlastnosti nemůžete změnit pomocí Průzkumníka cloudu.

> [!IMPORTANT]
> Změna typu umístění z **oblasti** na [**prostředí integrační služby**](connect-virtual-network-vnet-isolated-environment-overview.md) má vliv na [cenový model](logic-apps-pricing.md#fixed-pricing) vaší aplikace logiky, který se používá pro fakturaci, [omezení](logic-apps-limits-and-config.md#integration-account-limits), [podporu účtu pro integraci](connect-virtual-network-vnet-isolated-environment-overview.md#ise-skus)atd. Než začnete vybírat jiný typ umístění, ujistěte se, že chápete výsledný dopad na vaši aplikaci logiky.

1. V aplikaci Visual Studio otevřete projekt skupiny prostředků Azure, který obsahuje vaši aplikaci logiky.

1. V Průzkumník řešení otevřete `<logic-app-name>.json` místní nabídku souboru a vyberte **otevřít v návrháři aplikace logiky**. (Klávesnice: CTRL + L)

   ![Otevření souboru. JSON aplikace logiky pomocí návrháře aplikace logiky](./media/manage-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Pokud tento příkaz v aplikaci Visual Studio 2019 nemáte, ověřte, že máte nejnovější aktualizace sady Visual Studio a rozšíření Azure Logic Apps Tools.

1. Ujistěte se, že návrhář aplikace logiky má fokus, a to tak, že vybere kartu nebo plochu daného návrháře, takže okno Vlastnosti zobrazuje **typ umístění** a vlastnosti **umístění** pro vaši aplikaci logiky. Typ umístění projektu je nastaven na hodnotu **region** nebo **prostředí integrační služby**.

   ![Okno Vlastnosti – Volba možnosti typ umístění & umístění](./media/manage-logic-apps-with-visual-studio/open-logic-app-properties-location.png)

   > [!TIP]
   > Pokud okno Vlastnosti ještě není otevřený, v nabídce **zobrazení** vyberte **okno Vlastnosti**. (Klávesnice: stiskněte F4)

1. Chcete-li změnit typ umístění, otevřete seznam **zvolit vlastnost typu umístění** a vyberte požadovaný typ umístění.

   Například pokud je typ umístění **prostředí integrační služby**, můžete vybrat **oblast**.

   !["Volba typu umístění" – změnit typ umístění](./media/manage-logic-apps-with-visual-studio/change-location-type.png)

1. Chcete-li změnit konkrétní umístění, otevřete seznam vlastností **umístění** . V závislosti na typu umístění vyberte umístění, které chcete, například:

   * Vyberte jinou oblast Azure:

     ![Otevřete seznam vlastností Location (umístění), vyberte jinou oblast Azure.](./media/manage-logic-apps-with-visual-studio/change-azure-resource-group-region.png)

   * Vyberte jiné ISE:

     ![Otevřete seznam vlastností Location (umístění), vyberte jiný ISE.](./media/manage-logic-apps-with-visual-studio/change-integration-service-environment.png)

1. Až budete hotovi, nezapomeňte uložit řešení sady Visual Studio.

Když změníte typ umístění nebo umístění v aplikaci Visual Studio a uložíte aplikaci logiky jako šablonu Azure Resource Manager, tato šablona také obsahuje deklarace parametrů pro daný typ umístění a umístění. Další informace o parametrech šablony a Logic Apps najdete v tématu [Přehled: Automatizace nasazení aplikace logiky](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters).

<a name="refresh"></a>

## <a name="refresh-from-azure"></a>Aktualizace z Azure

Pokud aplikaci logiky upravíte v Azure Portal a chcete tyto změny zachovat, ujistěte se, že jste aktualizovali verzi aplikace v aplikaci Visual Studio s těmito změnami.

* V sadě Visual Studio na panelu nástrojů návrháře aplikace logiky vyberte **aktualizovat**.

  -nebo-

* V Průzkumníku cloudu sady Visual Studio otevřete místní nabídku aplikace logiky a vyberte **aktualizovat**.

![Aktualizace aplikace logiky s aktualizacemi](./media/manage-logic-apps-with-visual-studio/refresh-logic-app-with-updates-from-portal.png)

## <a name="publish-logic-app-updates"></a>Publikování aktualizací aplikací logiky

Až budete připraveni nasadit aktualizace aplikace logiky ze sady Visual Studio do Azure, klikněte na panelu nástrojů návrháře aplikací logiky na **publikovat**.

![Publikování aktualizované aplikace logiky na Azure Portal](./media/manage-logic-apps-with-visual-studio/publish-logic-app-to-azure-portal.png)

## <a name="manually-run-your-logic-app"></a>Ruční spuštění aplikace logiky

Aplikaci logiky nasazenou v Azure můžete ručně aktivovat ze sady Visual Studio. Na panelu nástrojů návrháře aplikace logiky vyberte **Spustit Trigger**.

![Ruční spuštění triggeru pro vaši aplikaci logiky](./media/manage-logic-apps-with-visual-studio/manually-run-logic-app.png)

## <a name="review-run-history"></a>Kontrola historie spuštění

Chcete-li zkontrolovat stav a diagnostikovat problémy s spuštěním aplikace logiky, můžete zkontrolovat podrobnosti, jako jsou vstupy a výstupy, pro tyto běhy v aplikaci Visual Studio.

1. V Průzkumníku cloudu otevřete místní nabídku aplikace logiky a vyberte **otevřít historii spuštění**.

   ![Otevření historie spuštění aplikace logiky](./media/manage-logic-apps-with-visual-studio/open-run-history-for-logic-app.png)

1. Chcete-li zobrazit podrobnosti o konkrétním spuštění, dvakrát klikněte na spuštění. Například:

   ![Zobrazit informace o konkrétním běhu](./media/manage-logic-apps-with-visual-studio/view-run-history-details.png)
  
   > [!TIP]
   > Chcete-li seřadit tabulku podle vlastnosti, vyberte záhlaví sloupce pro danou vlastnost.

1. Rozbalte kroky, jejichž vstupy a výstupy chcete zkontrolovat, například:

   ![Zobrazit vstupy a výstupy pro jednotlivé kroky](./media/manage-logic-apps-with-visual-studio/view-run-history-inputs-outputs.png)

## <a name="disable-or-enable-logic-app"></a>Zakázání nebo povolení aplikace logiky

Bez odstranění aplikace logiky můžete zastavit aktivaci triggerem při dalším spuštění podmínky triggeru. Zakázáním aplikace logiky znemožníte Logic Appsmu stroji vytvářet a spouštět budoucí instance pracovního postupu pro aplikaci logiky. V Průzkumníku cloudu otevřete místní nabídku aplikace logiky a vyberte **Zakázat**.

![Zakázání aplikace logiky v Průzkumníku cloudu](./media/manage-logic-apps-with-visual-studio/disable-logic-app-cloud-explorer.png)

> [!NOTE]
> Když aplikaci logiky zakážete, nevytvoří se žádná nová spuštění. Všechny probíhající a probíhající běhy budou pokračovat, dokud nebudou dokončeny, což může trvat déle.

Pokud chcete aplikaci logiky znovu aktivovat, v Průzkumníku cloudu otevřete místní nabídku vaší aplikace logiky a vyberte **Povolit**.

![Povolit aplikaci logiky v Průzkumníku cloudu](./media/manage-logic-apps-with-visual-studio/enable-logic-app-cloud-explorer.png)

## <a name="delete-your-logic-app"></a>Odstranění aplikace logiky

Pokud chcete aplikaci logiky odstranit z Azure Portal, v Průzkumníku cloudu otevřete místní nabídku vaší aplikace logiky a vyberte **Odstranit**.

![Odstranění aplikace logiky z Azure Portal](./media/manage-logic-apps-with-visual-studio/delete-logic-app-from-azure-portal.png)

> [!NOTE]
> Pokud odstraníte aplikaci logiky, nebudou se vytvářet žádné nové instance spuštění. Všechna probíhající a čekající spuštění se zruší. Pokud máte tisíce spuštění, jejich zrušení může trvat značnou dobu.

> [!NOTE]
> Pokud odstraníte a znovu vytvoříte podřízenou aplikaci logiky, musíte znovu uložit nadřazenou aplikaci logiky. Znovu vytvořená podřízená aplikace bude mít odlišná metadata.
> Pokud neuložíte znovu nadřazenou aplikaci logiky po opětovném vytvoření podřízené aplikace, vaše volání podřízené aplikace logiky se nezdaří s chybou "Neautorizováno". Toto chování se vztahuje na aplikace logiky nadřazeného a podřízeného objektu, například na ty, které používají artefakty v integračních účtech nebo volají službu Azure Functions.

## <a name="troubleshooting"></a>Řešení potíží

Když otevřete projekt aplikace logiky v Návrháři Logic Apps, nebudete mít možnost pro výběr vašeho předplatného Azure. Místo toho se aplikace logiky otevře s předplatným Azure, které není ten, který chcete použít. K tomuto chování dochází, protože po otevření souboru. JSON aplikace logiky Visual Studio uloží první vybrané předplatné do mezipaměti pro budoucí použití. Chcete-li tento problém vyřešit, zkuste jeden z následujících kroků:

* Přejmenujte soubor. JSON aplikace logiky. Mezipaměť odběrů závisí na názvu souboru.

* Pokud chcete odebrat dříve vybraná předplatná pro *všechny* aplikace logiky ve vašem řešení, odstraňte složku nastavení skryté sady Visual Studio (. vs) v adresáři vašeho řešení. V tomto umístění se ukládají informace o vašem předplatném.

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak spravovat nasazené aplikace logiky pomocí sady Visual Studio. Další informace o přizpůsobení definic aplikace logiky pro nasazení:

> [!div class="nextstepaction"]
> [Vytváření definic aplikací logiky ve formátu JSON](../logic-apps/logic-apps-author-definitions.md)

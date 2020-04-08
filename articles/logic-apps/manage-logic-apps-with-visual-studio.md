---
title: Úpravy a správa aplikací logiky pomocí Visual Studia s Průzkumníkem Cloudu
description: Úpravy, aktualizace, správa, přidání do správy zdrojového kódu a nasazení aplikací logiky pomocí Visual Studia s Průzkumníkem Cloud
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.custom: mvc
ms.date: 10/29/2019
ms.openlocfilehash: 73df5b7f10e038b6894996eb83dec7b6914a4536
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2020
ms.locfileid: "80803186"
---
# <a name="manage-logic-apps-with-visual-studio"></a>Správa aplikací logiky s využitím sady Visual Studio

I když můžete vytvářet, upravovat, spravovat a nasazovat aplikace logiky na [webu Azure Portal](https://portal.azure.com), můžete také použít Visual Studio, když chcete přidat aplikace logiky do správy zdrojového kódu, publikovat různé verze a vytvářet šablony Azure Resource [Manager](../azure-resource-manager/management/overview.md) pro různá prostředí nasazení. Pomocí Průzkumníka Visual Studio Cloud Explorer můžete najít a spravovat aplikace logiky spolu s dalšími prostředky Azure. Můžete například otevřít, stáhnout, upravit, spustit, zobrazit historii spuštění, zakázat a povolit aplikace logiky, které už jsou nasazené na webu Azure Portal. Pokud s aplikací Azure Logic Apps tečuse tevete v Visual Studiu, přečtěte si, [jak pomocí Visual Studia vytvářet aplikace logiky.](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)

Můžete také [spravovat aplikace logiky na webu Azure Portal](manage-logic-apps-with-azure-portal.md).

> [!IMPORTANT]
> Nasazení nebo publikování aplikace logiky z Visual Studia přepíše verzi této aplikace na webu Azure Portal. Pokud tedy na webu Azure portal uděláte změny, které chcete zachovat, ujistěte se, že [aktualizujete aplikaci logiky ve Visual Studiu](#refresh) z webu Azure, než se při příštím nasazení nebo publikování z Visual Studia aktualizujete.

<a name="requirements"></a>

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Pokud je ještě nemáte, stáhněte a nainstalujte tyto nástroje:

  * [Visual Studio 2019, 2017 nebo 2015 – verze komunity nebo vyšší](https://aka.ms/download-visual-studio). V tomto kurzu se používá sada Visual Studio Community 2017, která je zdarma.

    > [!IMPORTANT]
    > Při instalaci Visual Studia 2019 nebo 2017 se ujistěte, že vyberete vývojové úlohy **Azure.**
    > Další informace najdete [v tématu Správa prostředků přidružených k vašim účtům Azure v Aplikaci Visual Studio Cloud Explorer](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer?view).

    Chcete-li nainstalovat Průzkumníka Cloud pro Visual Studio 2015, [stáhněte si Průzkumníka Cloudz webu Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVisualStudio2015). Další informace najdete [v tématu Správa prostředků přidružených k vašim účtům Azure v Průzkumníkovi Visual Studia (2015).](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer?view=vs-2015)

  * [Azure SDK (2.9.1 nebo novější)](https://azure.microsoft.com/downloads/)

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

  * Nejnovější nástroje Azure Logic Apps pro rozšíření Visual Studio pro požadovanou verzi:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)

    Azure Logic Apps Tools můžete stáhnout a nainstalovat přímo z Visual Studio Marketplace nebo si můžete přečíst, [jak toto rozšíření nainstalovat v rámci sady Visual Studio](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions). Po dokončení instalace nezapomeňte sadu Visual Studio restartovat.

* Přístup k webu při používání integrovaného návrháře aplikací logiky

  Návrhář k vytváření prostředků v Azure a ke čtení vlastností a dat z konektorů ve vaší aplikaci logiky vyžaduje připojení k internetu. Pokud například použijete konektor Dynamics CRM Online, návrhář zkontroluje ve vaší instanci CRM dostupné výchozí a vlastní vlastnosti.

<a name="find-logic-apps-vs"></a>

## <a name="find-your-logic-apps"></a>Vyhledání aplikací logiky

Ve Visual Studiu najdete všechny aplikace logiky, které jsou přidružené k vašemu předplatnému Azure a nasazují se na webu Azure Portal pomocí Cloud Exploreru.

1. Otevřete sadu Visual Studio. V nabídce **View** vyberte **Průzkumníka cloudu**.

1. V Průzkumníkovi Cloudvyberte **Správa účtů**. Vyberte předplatné Azure přidružené k vašim aplikacím logiky a pak vyberte **Použít**. Příklad:

   ![Vyberte možnost Správa účtu.](./media/manage-logic-apps-with-visual-studio/account-management-select-Azure-subscription.png)

1. Na základě toho, zda hledáte **podle skupin prostředků** nebo typů **prostředků**, postupujte takto:

   * **Skupiny prostředků:** V rámci předplatného Azure Cloud Explorer zobrazuje všechny skupiny prostředků, které jsou přidruženy k tomuto předplatnému. Rozbalte skupinu prostředků, která obsahuje aplikaci logiky, a vyberte aplikaci logiky.

   * **Typy prostředků:** V rámci předplatného Azure rozbalte **Logic Apps**. Až Cloud Explorer zobrazí všechny nasazené aplikace logiky, které jsou přidružené k vašemu předplatnému, vyberte aplikaci logiky.

<a name="open-designer"></a>

## <a name="open-in-visual-studio"></a>Open in Visual Studio

Ve Visual Studiu můžete otevřít aplikace logiky, které byly dříve vytvořené a nasazené, buď přímo prostřednictvím portálu Azure, nebo jako projekty Skupiny prostředků Azure pomocí Sady Visual Studio.

1. Otevřete Průzkumníka Cloud a najděte aplikaci logiky.

1. V místní nabídce aplikace logiky vyberte **Otevřít pomocí Editoru aplikace logiky**.

   > [!TIP]
   > Pokud tento příkaz nemáte v Sadě Visual Studio 2019, zkontrolujte, zda máte nejnovější aktualizace pro Visual Studio.

   Tento příklad zobrazuje aplikace logiky podle typu prostředku, takže se aplikace logiky zobrazí v části **Logic Apps.**

   ![Otevření nasazené aplikace logiky z webu Azure Portal](./media/manage-logic-apps-with-visual-studio/open-logic-app-in-editor.png)

   Po otevření aplikace logiky v Návrháři aplikací logiky v dolní části návrháře můžete vybrat **zobrazení kódu,** abyste mohli zkontrolovat základní strukturu definice aplikace logiky. Pokud chcete vytvořit šablonu nasazení pro aplikaci logiky, přečtěte si, [jak stáhnout šablonu Azure Resource Manager](#download-logic-app) pro tuto aplikaci logiky. Další informace o [šablonách Správce prostředků](../azure-resource-manager/templates/overview.md).

<a name="download-logic-app"></a>

## <a name="download-from-azure"></a>Stažení z Azure

Aplikace logiky si můžete stáhnout z [webu Azure Portal](https://portal.azure.com) a uložit je jako šablony Azure Resource [Manageru.](../azure-resource-manager/management/overview.md) Potom můžete místně upravit šablony pomocí sady Visual Studio a přizpůsobit aplikace logiky pro různá prostředí nasazení.  Stahování aplikací *logiky* automaticky parametrizuje jejich definice v [šablonách Správce prostředků](../azure-resource-manager/templates/overview.md), které také používají JavaScript Object Notation (JSON).

1. V Sadě Visual Studio otevřete Průzkumníka Cloud. Najděte a vyberte aplikaci logiky, kterou chcete stáhnout z Azure.

1. V místní nabídce této aplikace vyberte **Otevřít pomocí Editoru aplikací logiky**.

   > [!TIP]
   > Pokud tento příkaz nemáte v Sadě Visual Studio 2019, zkontrolujte, zda máte nejnovější aktualizace pro Visual Studio.

   Návrhář aplikace logiky otevře a zobrazí aplikaci logiky. Chcete-li zkontrolovat základní definici a strukturu aplikace **logiky,** v dolní části návrháře vyberte zobrazení kódu .

1. Na panelu nástrojů návrháře vyberte **Stáhnout**.

   ![Stažení aplikace logiky z webu Azure Portal](./media/manage-logic-apps-with-visual-studio/download-logic-app-from-portal.png)

1. Až se zobrazí výzva k zadání umístění, přejděte do tohoto umístění a uložte šablonu Správce prostředků pro definici aplikace logiky ve formátu souboru JSON (.json).

   Definice aplikace logiky `resources` se zobrazí v podčásti v šabloně Správce prostředků. Teď můžete upravit definici aplikace logiky a šablonu Správce prostředků pomocí sady Visual Studio. Šablonu můžete přidat také jako [projekt skupiny prostředků Azure](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) do řešení Visual Studia. Další informace o [projektech Skupiny prostředků Azure pro aplikace logiky ve Visual Studiu](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

<a name="link-integration-account"></a>

## <a name="link-to-integration-account"></a>Odkaz na účet integrace

Chcete-li vytvořit aplikace logiky pro scénáře podnikové integrace (B2B), můžete propojit aplikaci logiky s dříve [vytvořeným účtem integrace,](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) který existuje ve stejné oblasti jako vaše aplikace logiky. Účet integrace obsahuje artefakty B2B, jako jsou obchodní partneři, dohody, schémata a mapy a umožňuje aplikaci logiky používat konektory B2B pro ověřování XML a kódování nebo dekódování plochých souborů. I když můžete [vytvořit tento odkaz pomocí portálu Azure](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account), můžete také použít Visual Studio po splnění [požadavků](#requirements)a aplikace logiky existuje jako soubor JSON (.json) uvnitř [projektu Skupiny prostředků Azure](../azure-resource-manager/templates/create-visual-studio-deployment-project.md). Další informace o [projektech Skupiny prostředků Azure pro aplikace logiky ve Visual Studiu](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#create-resource-group-project).

1. Ve Visual Studiu otevřete projekt Skupiny prostředků Azure, který obsahuje vaši aplikaci logiky.

1. V Průzkumníku řešení otevřete místní nabídku **souboru<logika-app-name>.json** a vyberte **Otevřít pomocí návrháře aplikací logiky**. (Klávesnice: Ctrl + L)

   ![Otevření souboru JSON aplikace logiky pomocí Návrháře aplikací logiky](./media/manage-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Pokud tento příkaz nemáte v Visual Studiu 2019, zkontrolujte, jestli máte nejnovější aktualizace Visual Studia a rozšíření Nástroje logiky Azure.

1. Ujistěte se, že Návrhář aplikace logiky má fokus výběrem karty návrháře nebo povrchu tak, aby vlastnosti okno zobrazuje **vlastnost účet integrace** pro aplikaci logiky.

   ![Okno Vlastnosti – vlastnost "Účet integrace"](./media/manage-logic-apps-with-visual-studio/open-logic-app-properties-integration-account.png)

   > [!TIP]
   > Pokud ještě není otevřené okno Vlastnosti, vyberte v nabídce **Zobrazení** **okno Vlastnosti**. (Klávesnice: Stiskněte klávesu F4)

1. Otevřete seznam **vlastností účtu integrace** a vyberte účet integrace, který chcete propojit s aplikací logiky, například:

   ![Otevřít seznam vlastností "Integrační účet"](./media/manage-logic-apps-with-visual-studio/select-integration-account.png)

1. Až budete hotovi, nezapomeňte uložit řešení sady Visual Studio.

Když nastavíte vlastnost **Účet integrace** ve Visual Studiu a uložíte aplikaci logiky jako šablonu Azure Resource Manager, tato šablona obsahuje také deklaraci parametru pro vybraný účet integrace. Další informace o parametrech šablony a aplikacích logiky najdete v [tématu Přehled: Automatizace nasazení aplikace logiky](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters).

<a name="change-location"></a>

## <a name="change-deployment-location"></a>Změna umístění nasazení

V sadě Visual Studio, pokud vaše aplikace logiky existuje jako soubor JSON (.json) v rámci [projektu skupiny prostředků Azure,](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) který používáte k automatizaci nasazení, tato aplikace logiky je nastavena na typ umístění a konkrétní umístění. Toto umístění je buď oblast Azure nebo existující [prostředí služby integrace (ISE)](connect-virtual-network-vnet-isolated-environment.md).

Chcete-li změnit typ umístění nebo umístění aplikace logiky, musíte otevřít soubor definice pracovního postupu aplikace logiky (.json) z Průzkumníka řešení pomocí Návrháře aplikací logiky. Tyto vlastnosti nelze změnit pomocí Průzkumníka cloudu.

> [!IMPORTANT]
> Změna typu umístění z **oblasti** na [**prostředí služby integrace**](connect-virtual-network-vnet-isolated-environment-overview.md) ovlivňuje [cenový model](logic-apps-pricing.md#fixed-pricing) aplikace logiky, který se používá pro fakturaci, [omezení](logic-apps-limits-and-config.md#integration-account-limits), podporu [účtu integrace](connect-virtual-network-vnet-isolated-environment-overview.md#ise-skus)a tak dále. Než vyberete jiný typ umístění, ujistěte se, že rozumíte výsledný dopad na aplikaci logiky.

1. Ve Visual Studiu otevřete projekt Skupiny prostředků Azure, který obsahuje vaši aplikaci logiky.

1. V Průzkumníku řešení `<logic-app-name>.json` otevřete místní nabídku souboru a vyberte **Otevřít pomocí návrháře aplikací logiky**. (Klávesnice: Ctrl + L)

   ![Otevření souboru JSON aplikace logiky pomocí Návrháře aplikací logiky](./media/manage-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Pokud tento příkaz nemáte v Visual Studiu 2019, zkontrolujte, jestli máte nejnovější aktualizace Visual Studia a rozšíření Nástroje logiky Azure.

1. Ujistěte se, že Návrhář aplikace logiky má fokus výběrem karty návrháře nebo povrchu tak, aby vlastnosti zobrazí **zvolit typ umístění** a **umístění** vlastnosti pro aplikaci logiky. Typ umístění projektu je nastaven na **oblast** nebo **prostředí integrační služby**.

   ![Okno Vlastnosti – "Zvolit typ umístění" & vlastnosti "Umístění"](./media/manage-logic-apps-with-visual-studio/open-logic-app-properties-location.png)

   > [!TIP]
   > Pokud ještě není otevřené okno Vlastnosti, vyberte v nabídce **Zobrazení** **okno Vlastnosti**. (Klávesnice: Stiskněte klávesu F4)

1. Chcete-li změnit typ umístění, otevřete seznam **vlastností Zvolit typ umístění** a vyberte požadovaný typ umístění.

   Pokud je například typem umístění prostředí **integration service**, můžete vybrat **možnost Oblast**.

   ![Vlastnost "Zvolit typ umístění" – změna typu umístění](./media/manage-logic-apps-with-visual-studio/change-location-type.png)

1. Chcete-li změnit konkrétní umístění, otevřete seznam vlastností **Umístění.** Na základě typu umístění vyberte požadované umístění, například:

   * Vyberte jinou oblast Azure:

     ![Otevřete seznam vlastností Umístění, vyberte jinou oblast Azure](./media/manage-logic-apps-with-visual-studio/change-azure-resource-group-region.png)

   * Vyberte jinou ise:

     ![Otevřete seznam vlastností Umístění, vyberte jinou službu ISE](./media/manage-logic-apps-with-visual-studio/change-integration-service-environment.png)

1. Až budete hotovi, nezapomeňte uložit řešení sady Visual Studio.

Když změníte typ umístění nebo umístění ve Visual Studiu a uložíte aplikaci logiky jako šablonu Azure Resource Manager, tato šablona také obsahuje deklarace parametrů pro tento typ umístění a umístění. Další informace o parametrech šablony a aplikacích logiky najdete v [tématu Přehled: Automatizace nasazení aplikace logiky](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters).

<a name="refresh"></a>

## <a name="refresh-from-azure"></a>Aktualizace z Azure

Pokud upravíte aplikaci logiky na webu Azure Portal a chcete zachovat tyto změny, ujistěte se, že aktualizujete verzi této aplikace ve Visual Studiu s těmito změnami.

* V sadě Visual Studio vyberte na panelu nástrojů Návrhář aplikace logiky **možnost Aktualizovat**.

  -nebo-

* V Průzkumníkovi Visual Studia otevřete místní nabídku aplikace logiky a vyberte **Aktualizovat**.

![Aktualizace aplikace logiky s aktualizacemi](./media/manage-logic-apps-with-visual-studio/refresh-logic-app-with-updates-from-portal.png)

## <a name="publish-logic-app-updates"></a>Publikování aktualizací aplikací logiky

Až budete připraveni nasadit aktualizace aplikace logiky z Visual Studia do Azure, na panelu nástrojů Návrhář epona aplikace logiky vyberte **Publikovat**.

![Publikování aktualizované aplikace logiky na portál Azure](./media/manage-logic-apps-with-visual-studio/publish-logic-app-to-azure-portal.png)

## <a name="manually-run-your-logic-app"></a>Ruční spuštění aplikace logiky

Můžete ručně aktivovat aplikaci logiky nasazenou v Azure z Visual Studia. Na panelu nástrojů Návrhář epoje vyberte **spustit aktivační událost**.

![Ruční spuštění aktivační události pro aplikaci logiky](./media/manage-logic-apps-with-visual-studio/manually-run-logic-app.png)

## <a name="review-run-history"></a>Kontrola historie spuštění

Chcete-li zkontrolovat stav a diagnostikovat problémy s spuštěním aplikace logiky, můžete zkontrolovat podrobnosti, jako jsou vstupy a výstupy, pro tyto spuštění v sadě Visual Studio.

1. V Průzkumníkovi Cloudotevřete místní nabídku aplikace logiky a vyberte **Otevřít historii spuštění**.

   ![Otevřená historie spuštění aplikace logiky](./media/manage-logic-apps-with-visual-studio/open-run-history-for-logic-app.png)

1. Chcete-li zobrazit podrobnosti pro konkrétní spuštění, poklepejte na spuštění. Příklad:

   ![Zobrazit informace o konkrétním spuštění](./media/manage-logic-apps-with-visual-studio/view-run-history-details.png)
  
   > [!TIP]
   > Chcete-li tabulku seřadit podle vlastnosti, vyberte záhlaví sloupce pro tuto vlastnost.

1. Rozbalte kroky, jejichž vstupy a výstupy chcete zkontrolovat, například:

   ![Zobrazení vstupů a výstupů pro každý krok](./media/manage-logic-apps-with-visual-studio/view-run-history-inputs-outputs.png)

## <a name="disable-or-enable-logic-app"></a>Zakázání nebo povolení aplikace logiky

Bez odstranění aplikace logiky můžete zastavit aktivační událost z spouštění při příštím splnění podmínky aktivační události. Zakázání aplikace logiky zabrání modulu Logic Apps ve vytváření a spouštění budoucích instancí pracovního postupu pro vaši aplikaci logiky. V Průzkumníkovi Cloudotevřete místní nabídku aplikace logiky a vyberte **Zakázat**.

![Zakázání aplikace logiky v Průzkumníkovi Cloud](./media/manage-logic-apps-with-visual-studio/disable-logic-app-cloud-explorer.png)

> [!NOTE]
> Když zakážete aplikaci logiky, žádné nové spuštění jsou instance. Všechna probíhající a čekající spuštění budou pokračovat až do dokončení, což může nějakou dobu trvat.

Pokud chcete aplikaci logiky znovu aktivovat, otevřete v Průzkumníkovi cloudu místní nabídku aplikace logiky a vyberte **Povolit**.

![Povolení aplikace logiky v Průzkumníkovi Cloud](./media/manage-logic-apps-with-visual-studio/enable-logic-app-cloud-explorer.png)

## <a name="delete-your-logic-app"></a>Odstranění aplikace logiky

Pokud chcete odstranit aplikaci logiky z portálu Azure, otevřete v Cloud Exploreru místní nabídku aplikace logiky a vyberte **Odstranit**.

![Odstranění aplikace logiky z webu Azure Portal](./media/manage-logic-apps-with-visual-studio/delete-logic-app-from-azure-portal.png)

> [!NOTE]
> Pokud odstraníte aplikaci logiky, nebudou se vytvářet žádné nové instance spuštění. Všechna probíhající a čekající spuštění se zruší. Pokud máte tisíce spuštění, jejich zrušení může trvat značnou dobu. 

## <a name="troubleshooting"></a>Řešení potíží

Když otevřete projekt aplikace logiky v Návrháři aplikací logiky, nemusí se vám zobrazit možnost výběru předplatného Azure. Místo toho se otevře aplikace logiky s předplatným Azure, které není ten, který chcete použít. K tomuto chování dochází, protože po otevření souboru JSON aplikace logiky visual studio uloží první vybrané předplatné pro budoucí použití. Chcete-li tento problém vyřešit, vyzkoušejte jeden z těchto kroků:

* Přejmenujte soubor JSON aplikace logiky. Mezipaměť odběrů závisí na názvu souboru.

* Chcete-li odebrat dříve vybraná předplatná pro *všechny* aplikace logiky ve vašem řešení, odstraňte skrytou složku nastavení sady Visual Studio (.vs) v adresáři vašeho řešení. Toto umístění ukládá informace o předplatném.

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli, jak spravovat nasazené aplikace logiky pomocí Sady Visual Studio. Další informace o přizpůsobení definice aplikace logiky pro nasazení:

> [!div class="nextstepaction"]
> [Definice aplikace logiky autora v JSON](../logic-apps/logic-apps-author-definitions.md)

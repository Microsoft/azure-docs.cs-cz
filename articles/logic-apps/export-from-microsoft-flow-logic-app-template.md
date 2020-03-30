---
title: Exportovat toky z power automatu do aplikací Azure Logic Apps
description: Migrace toků z Power Automate do Azure Logic Apps exportem jako šablony Azure Resource Manageru
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 616f10b32d0a9c1a05d759a0e27550cd2808808b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75428882"
---
# <a name="export-flows-from-power-automate-and-deploy-to-azure-logic-apps"></a>Export toků z Power Automate a nasazení do Azure Logic Apps

Chcete-li rozšířit a rozšířit možnosti toku, můžete tento tok migrovat z [Power Automate](https://flow.microsoft.com) do [Azure Logic Apps](../logic-apps/logic-apps-overview.md). Tok můžete exportovat jako šablonu Azure Resource Manager pro aplikaci logiky, nasadit tuto šablonu aplikace logiky do skupiny prostředků Azure a pak otevřít tuto aplikaci logiky v Návrháři aplikací logiky.

> [!NOTE]
> Ne všechny konektory Power Automate jsou dostupné v Aplikacích Logika Azure. Můžete importovat toky, které mají [ekvivalentní konektory](../connectors/apis-list.md) v Azure Logic Apps. Například button trigger, konektor Schválení a Konektor oznámení jsou specifické pro Power Automate.
>
> Toky založené na OpenAPI exportované z Power Automate nejsou aktuálně podporovány pro nasazení jako šablony aplikací logiky. 

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Tok, který chcete exportovat z Power Automate

## <a name="export-a-flow"></a>Export toku

1. Přihlaste se k [Power Automate](https://flow.microsoft.com)a vyberte **Moje toky**. Najděte a vyberte svůj tok. Na panelu nástrojů vyberte tlačítko elipsy (**...**). Vyberte **exportovat** > **šablonu aplikace logiky (.json)**.

   ![Tok exportu](./media/export-from-microsoft-flow-logic-app-template/export-flow.png)

1. Uložte šablonu do požadovaného umístění.

Další informace najdete [v tématu Grow up to Azure Logic Apps](https://flow.microsoft.com/blog/grow-up-to-logic-apps/).

## <a name="deploy-template-by-using-the-azure-portal"></a>Nasazení šablony pomocí portálu Azure

1. Přihlaste se na [portál Azure](https://portal.azure.com) pomocí svého účtu Azure.

1. V hlavní nabídce Azure vyberte **Vytvořit prostředek**. Do vyhledávacího pole zadejte "nasazení šablony". Vyberte **nasazení šablony (nasazení pomocí vlastních šablon)** a pak vyberte **Vytvořit**.

   ![Vyberte možnost "Nasazení šablony"](./media/export-from-microsoft-flow-logic-app-template/select-template-deployment.png)

1. V části **Vlastní nasazení**vyberte **v editoru možnost Vytvořit vlastní šablonu**.

   ![Vyberte "Vytvořit si vlastní šablonu v editoru"](./media/export-from-microsoft-flow-logic-app-template/build-template-in-editor.png)

1. Na panelu nástrojů **Upravit předlohu** vyberte **Načíst soubor**. Najděte a vyberte šablonu JSON, kterou jste exportovali z Power Automate, a vyberte **Otevřít**.

   ![Vyberte možnost "Načíst soubor"](./media/export-from-microsoft-flow-logic-app-template/load-file.png)

1. Až editor zobrazí json, parametry a prostředky v šabloně, vyberte **Uložit**.
  
   ![Uložení šablony](./media/export-from-microsoft-flow-logic-app-template/save-template.png)

1. Nyní zadejte tyto vstupní parametry pro šablonu:

   * Předplatné Azure, které se má použít pro fakturaci
   * Skupina prostředků Azure
   * Umístění pro skupinu prostředků Azure
   * Název prostředku aplikace logiky
   * Umístění prostředku aplikace logiky, pokud se liší od skupiny prostředků Azure
   * Název všech dříve vytvořených připojení, která aplikace logiky může znovu použít

      Pokud vytváříte první aplikaci logiky, všechna připojení se vytvoří jako nová, takže můžete přijmout výchozí názvy. V opačném případě můžete zadat názvy dříve vytvořených připojení, které můžete použít ve více aplikacích logiky.

   Po zadání těchto informací pro šablonu si zkontrolujte a odsouhlasíte smluvní podmínky Azure Marketplace pro vytvoření potřebných prostředků Azure a odpovídajícím způsobem fakturaci předplatného Azure a pak vyberte **Koupit**.
  
   ![Určení vstupních parametrů pro šablonu](./media/export-from-microsoft-flow-logic-app-template/template-input-parameters.png)

   Azure nasadí vaši šablonu jako aplikaci logiky do zadané skupiny prostředků. Všechny aplikace logiky, které migrujete z Power Automate, se nasazují v zakázaném stavu.

1. Před aktivací aplikace logiky autorizujte všechna nová připojení následujícím postupem:

   1. Otevřete aplikaci logiky, kterou jste vytvořili. V nabídce aplikace logiky vyberte **Návrhář aplikace Logika**.

      Každé připojení, které vyžaduje autorizaci, zobrazuje výstražnou ikonu:

      ![Ikona upozornění](./media/export-from-microsoft-flow-logic-app-template/authorize-connections.png)

   1. Pro každý krok, který vyžaduje autorizované připojení, rozbalte tento krok a vyberte **Přidat nový**.

      ![Přidat nové připojení](./media/export-from-microsoft-flow-logic-app-template/add-new-connection.png)

   1. Přihlaste se ke každé službě nebo zadejte potřebná pověření k autorizaci připojení.

1. Uložte svou aplikaci logiky. Až budete připraveni aktivovat aplikaci logiky, v nabídce aplikace logiky vyberte **Přehled**a pak vyberte **Povolit**.

   ![Povolení aplikace logiky](./media/export-from-microsoft-flow-logic-app-template/enable-logic-app.png)

1. Chcete-li se vyhnout spuštění duplicitních pracovních postupů, ujistěte se, že deaktivujete nebo odstraníte původní tok.

## <a name="deploy-template-by-using-visual-studio"></a>Nasazení šablony pomocí Visual Studia

Pokud jste nastavili Visual Studio s požadavky pro vytváření aplikací [logiky,](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites) můžete nasadit exportovnou šablonu z Visual Studia do Azure Logic Apps.

1. V Sadě Visual Studio otevřete soubor šablony, který jste exportovali z Power Automate.

1. Ve Visual Studiu vytvořte projekt Skupiny prostředků Azure a vyberte šablonu **aplikace Logika** podle kroků v [úvodním panelu: Vytváření automatizovaných úloh, procesů a pracovních postupů pomocí Azure Logic Apps – Visual Studio ,](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)například:

   ![Vytvoření projektu skupiny prostředků Azure](./media/export-from-microsoft-flow-logic-app-template/create-azure-resource-group-project.png)

1. V Průzkumníku řešení otevřete soubor **LogicApp.json,** pokud soubor ještě není otevřený.

1. Zkopírujte obsah z exportované šablony a přepište obsah v souboru **LogicApp.json.**

1. Před nasazením aplikace logiky autorizujte všechna nová připojení takto:

   1. Otevřete místní nabídku **LogicApp.json** a pak vyberte **Otevřít pomocí návrháře aplikací logiky**.

      ![Otevřít šablonu pomocí Návrháře aplikací logiky](./media/export-from-microsoft-flow-logic-app-template/open-logic-app-designer.png)

   1. Pokud se zobrazí výzva, vyberte předplatné Azure a skupinu prostředků, kterou chcete použít k nasazení aplikace logiky.

      ![Vyberte předplatné Azure a skupinu prostředků.](./media/export-from-microsoft-flow-logic-app-template/select-azure-subscription-resource-group-deployment.png)

      Po zobrazení aplikace logiky v návrháři všechna připojení, která vyžadují autorizaci, zobrazují varovné ikony:

      ![Připojení s ikonami upozornění](./media/export-from-microsoft-flow-logic-app-template/authorize-connections-vs.png)

   1. Pro každý krok, který vyžaduje autorizované připojení, rozbalte tento krok a vyberte **Přidat nový**.

      ![Přidat nové připojení](./media/export-from-microsoft-flow-logic-app-template/add-new-connection-vs.png)

   1. Přihlaste se ke každé službě nebo zadejte potřebná pověření k autorizaci připojení.

   1. Před nasazením aplikace logiky uložte řešení.

1. V Průzkumníku řešení otevřete místní nabídku projektu a vyberte **Nasadit** > **nový**. Pokud se zobrazí výzva, přihlaste se pomocí svého účtu Azure.

1. Po zobrazení výzvy potvrďte předplatné Azure, skupinu prostředků Azure a všechna další nastavení, která chcete použít pro nasazení, jako je [například soubor parametrů,](../azure-resource-manager/templates/parameter-files.md) který se má použít pro předávání hodnot parametrů šablony, a pak vyberte **Nasadit**.

   ![Potvrdit nastavení nasazení](./media/export-from-microsoft-flow-logic-app-template/confirm-azure-subscription-resource-group-deployment.png)

1. Pokud se zobrazí pole **Upravit parametry,** zadejte název prostředku aplikace logiky v Azure a vyberte **Uložit**.  

   ![Upravit parametry nasazení](./media/export-from-microsoft-flow-logic-app-template/edit-parameters-deployment.png)

   Po zahájení nasazování se v okně **Výstup** sady Visual Studio zobrazí stav nasazení vaší aplikace. Pokud se stav nezobrazí, otevřete seznam **Zobrazit výstup z** a vyberte svou skupinu prostředků Azure. Například:

   ![Okno Výstup](./media/export-from-microsoft-flow-logic-app-template/output-window.png)

   Pokud všechna připojení ve vaší aplikaci logiky potřebují vstup od vás, otevře se na pozadí okno PowerShellu a zobrazí výzvu k zadání potřebných hesel nebo tajných klíčů. Po zadání těchto informací bude nasazení pokračovat.

   ![Ověření připojení](./media/export-from-microsoft-flow-logic-app-template/logic-apps-powershell-window.png)

   Po dokončení nasazení se vaše aplikace logiky publikuje, ale není aktivovaná na webu Azure Portal.

1. Až budete připraveni aktivovat aplikaci logiky na webu Azure Portal, najděte a otevřete aplikaci logiky v Návrháři aplikací logiky. V nabídce aplikace logiky vyberte **Přehled**a pak vyberte **Povolit**.

1. Chcete-li se vyhnout spuštění duplicitních pracovních postupů, ujistěte se, že deaktivujete nebo odstraníte původní tok.

Další informace o těchto krocích nasazení najdete [v tématu Úvodní příručka: Vytváření automatizovaných úloh, procesů a pracovních postupů pomocí Azure Logic Apps – Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-to-Azure)

## <a name="next-steps"></a>Další kroky

* Další informace o [konektorech pro aplikace Azure Logic Apps](../connectors/apis-list.md)
* Další informace o [aplikacích Azure Logic Apps](../logic-apps/logic-apps-overview.md)

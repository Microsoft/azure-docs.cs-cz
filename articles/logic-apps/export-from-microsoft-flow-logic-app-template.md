---
title: Export toků z Power automatu do Azure Logic Apps
description: Migrace toků z Power automatu na Azure Logic Apps exportováním jako šablon Azure Resource Manager
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 616f10b32d0a9c1a05d759a0e27550cd2808808b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75428882"
---
# <a name="export-flows-from-power-automate-and-deploy-to-azure-logic-apps"></a>Export toků z Power Automate a nasazení do Azure Logic Apps

Pokud chcete rozšířit a rozšířit možnosti toku, můžete tento tok migrovat z [Power](https://flow.microsoft.com) Automata na [Azure Logic Apps](../logic-apps/logic-apps-overview.md). Tok můžete exportovat jako šablonu Azure Resource Manager pro aplikaci logiky, nasadit tuto šablonu aplikace logiky do skupiny prostředků Azure a pak tuto aplikaci logiky otevřít v návrháři aplikace logiky.

> [!NOTE]
> V Azure Logic Apps nejsou k dispozici všechny konektory Power Automate. Můžete importovat toky, které mají [ekvivalentní konektory](../connectors/apis-list.md) v Azure Logic Apps. Například Trigger tlačítka, konektor pro schvalování a konektor oznámení jsou specifické pro automatizaci.
>
> Toky založené na OpenAPI vyexportované z Power automatu se v současné době nepodporují pro nasazení jako šablony aplikace logiky. 

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Tok, který chcete exportovat z automatizace výkonu

## <a name="export-a-flow"></a>Export toku

1. Přihlaste se k [Možnosti automatizace](https://flow.microsoft.com)a vyberte **Moje toky**. Najděte a vyberte svůj tok. Na panelu nástrojů vyberte tlačítko se třemi tečkami ( **...** ). Vyberte **exportovat** > **Logic Apps Template (. JSON)** .

   ![Tok exportu](./media/export-from-microsoft-flow-logic-app-template/export-flow.png)

1. Uložte šablonu do umístění, které chcete.

Další informace najdete v tématu [o nárůstu až Azure Logic Apps](https://flow.microsoft.com/blog/grow-up-to-logic-apps/).

## <a name="deploy-template-by-using-the-azure-portal"></a>Nasazení šablony pomocí Azure Portal

1. Přihlaste se [Azure Portal](https://portal.azure.com) pomocí svého účtu Azure.

1. V hlavní nabídce Azure vyberte **Vytvořit prostředek**. Do vyhledávacího pole zadejte "Deployment Template". Vyberte **template Deployment (nasadit pomocí vlastních šablon)** a pak vyberte **vytvořit**.

   ![Vyberte Template deployment](./media/export-from-microsoft-flow-logic-app-template/select-template-deployment.png)

1. V části **vlastní nasazení**vyberte **v editoru vytvořit vlastní šablonu**.

   ![V editoru vyberte sestavit vlastní šablonu.](./media/export-from-microsoft-flow-logic-app-template/build-template-in-editor.png)

1. Z panelu nástrojů **Upravit šablonu** vyberte **načíst soubor**. Vyhledejte a vyberte šablonu JSON, kterou jste exportovali z Power automatu, a vyberte **otevřít**.

   ![Vyberte načíst soubor.](./media/export-from-microsoft-flow-logic-app-template/load-file.png)

1. Poté, co editor zobrazí JSON, parametry a prostředky v šabloně, vyberte Save ( **Uložit**).
  
   ![Uložit šablonu](./media/export-from-microsoft-flow-logic-app-template/save-template.png)

1. Nyní zadejte tyto vstupní parametry pro šablonu:

   * Předplatné Azure, které se má použít pro fakturaci
   * Skupina prostředků Azure
   * Umístění skupiny prostředků Azure
   * Název pro prostředek aplikace logiky
   * Umístění prostředku aplikace logiky, pokud se liší od skupiny prostředků Azure
   * Název pro všechna dříve vytvořená připojení, která aplikace logiky může znovu použít

      Pokud vytváříte svou první aplikaci logiky, budou se všechna připojení vytvářet jako nová, takže můžete přijmout výchozí názvy. V opačném případě můžete zadat názvy pro dříve vytvořená připojení, která můžete použít v několika aplikacích logiky.

   Po zadání těchto informací pro šablonu si přečtěte a potvrďte Azure Marketplace podmínky a ujednání pro vytvoření nezbytných prostředků Azure a příslušným vyúčtováním svého předplatného Azure a pak vyberte **koupit**.
  
   ![Zadat vstupní parametry pro šablonu](./media/export-from-microsoft-flow-logic-app-template/template-input-parameters.png)

   Azure nasadí vaši šablonu jako aplikaci logiky do zadané skupiny prostředků. Všechny Logic Apps, které migrujete z Power Automate, se nasazují v zakázaném stavu.

1. Před aktivací aplikace logiky autorizujte všechna nová připojení pomocí následujících kroků:

   1. Otevřete aplikaci logiky, kterou jste vytvořili. V nabídce aplikace logiky vyberte **Návrhář aplikace logiky**.

      Každé připojení, které vyžaduje autorizaci, se zobrazuje ikona upozornění:

      ![Ikona upozornění](./media/export-from-microsoft-flow-logic-app-template/authorize-connections.png)

   1. Pro každý krok, který vyžaduje autorizované připojení, rozbalte tento krok a vyberte **Přidat nový**.

      ![Přidat nové připojení](./media/export-from-microsoft-flow-logic-app-template/add-new-connection.png)

   1. Přihlaste se ke každé službě nebo poskytněte potřebná pověření pro autorizaci připojení.

1. Uložte svou aplikaci logiky. Až budete připraveni k aktivaci aplikace logiky, v nabídce aplikace logiky vyberte **Přehled**a pak vyberte **Povolit**.

   ![Povolit aplikaci logiky](./media/export-from-microsoft-flow-logic-app-template/enable-logic-app.png)

1. Abyste se vyhnuli spouštění duplicitních pracovních postupů, ujistěte se, že jste původní tok deaktivovali nebo odstranili.

## <a name="deploy-template-by-using-visual-studio"></a>Nasazení šablony pomocí sady Visual Studio

Pokud jste nastavili sadu Visual Studio s [požadavky](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites) na vytváření aplikací logiky, můžete nasadit exportovanou šablonu ze sady Visual studio na Azure Logic Apps.

1. V aplikaci Visual Studio otevřete soubor šablony, který jste exportovali z automatizace Power automatu.

1. V aplikaci Visual Studio vytvořte projekt skupiny prostředků Azure a vyberte šablonu **Aplikace logiky** podle kroků v části [rychlý Start: vytvoření automatizovaných úloh, procesů a pracovních postupů pomocí Azure Logic Apps – Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md), například:

   ![Vytvoření projektu skupiny prostředků Azure](./media/export-from-microsoft-flow-logic-app-template/create-azure-resource-group-project.png)

1. Z Průzkumník řešení otevřete soubor **LogicApp. JSON** , pokud soubor ještě není otevřený.

1. Zkopírujte obsah z exportované šablony a přepište obsah v souboru **LogicApp. JSON** .

1. Před nasazením aplikace logiky autorizujte všechna nová připojení pomocí následujících kroků:

   1. Otevřete místní nabídku **LogicApp. JSON** a potom vyberte **otevřít v návrháři aplikace logiky**.

      ![Otevření šablony pomocí návrháře aplikace logiky](./media/export-from-microsoft-flow-logic-app-template/open-logic-app-designer.png)

   1. Po zobrazení výzvy vyberte předplatné Azure a skupinu prostředků, které chcete použít pro nasazení aplikace logiky.

      ![Výběr předplatného Azure a skupiny prostředků](./media/export-from-microsoft-flow-logic-app-template/select-azure-subscription-resource-group-deployment.png)

      Jakmile se aplikace logiky objeví v návrháři, všechna připojení, která vyžadují autorizaci, zobrazují ikony upozornění:

      ![Připojení s ikonami upozornění](./media/export-from-microsoft-flow-logic-app-template/authorize-connections-vs.png)

   1. Pro každý krok, který vyžaduje autorizované připojení, rozbalte tento krok a vyberte **Přidat nový**.

      ![Přidat nové připojení](./media/export-from-microsoft-flow-logic-app-template/add-new-connection-vs.png)

   1. Přihlaste se ke každé službě nebo poskytněte potřebná pověření pro autorizaci připojení.

   1. Před nasazením aplikace logiky uložte své řešení.

1. V Průzkumník řešení otevřete místní nabídku projektu a vyberte **nasadit** > **Nový**. Pokud se zobrazí výzva, přihlaste se pomocí svého účtu Azure.

1. Po zobrazení výzvy potvrďte předplatné Azure, skupinu prostředků Azure a všechna další nastavení, která chcete použít pro nasazení, například [soubor parametrů](../azure-resource-manager/templates/parameter-files.md) , který se použije pro předávání hodnot parametrů šablony, a pak vyberte **nasadit**.

   ![Potvrdit nastavení nasazení](./media/export-from-microsoft-flow-logic-app-template/confirm-azure-subscription-resource-group-deployment.png)

1. Pokud se zobrazí pole **Upravit parametry** , zadejte název vašeho prostředku aplikace logiky v Azure a vyberte **Uložit**.  

   ![Upravit parametry nasazení](./media/export-from-microsoft-flow-logic-app-template/edit-parameters-deployment.png)

   Po zahájení nasazování se v okně **Výstup** sady Visual Studio zobrazí stav nasazení vaší aplikace. Pokud se stav nezobrazí, otevřete seznam **Zobrazit výstup z** a vyberte svou skupinu prostředků Azure. Příklad:

   ![Okno Výstup](./media/export-from-microsoft-flow-logic-app-template/output-window.png)

   Pokud nějaká připojení ve vaší aplikaci logiky potřebuje vstup z vaší aplikace, otevře se okno PowerShellu na pozadí a zobrazí se výzva k zadání potřebných hesel nebo tajných klíčů. Po zadání těchto informací bude nasazení pokračovat.

   ![Ověřování připojení](./media/export-from-microsoft-flow-logic-app-template/logic-apps-powershell-window.png)

   Po dokončení nasazení se vaše aplikace logiky publikuje, ale neaktivuje se v Azure Portal.

1. Až budete připraveni aktivovat aplikaci logiky v Azure Portal, najděte a otevřete aplikaci logiky v návrháři aplikace logiky. V nabídce aplikace logiky vyberte **Přehled**a pak vyberte **Povolit**.

1. Abyste se vyhnuli spouštění duplicitních pracovních postupů, ujistěte se, že jste původní tok deaktivovali nebo odstranili.

Další informace o těchto krocích nasazení najdete v tématu [rychlý Start: vytvoření automatizovaných úloh, procesů a pracovních postupů pomocí Azure Logic Apps – Visual Studio.](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-to-Azure)

## <a name="next-steps"></a>Další kroky

* Další informace o [konektorech pro Azure Logic Apps](../connectors/apis-list.md)
* Další informace o [Azure Logic Apps](../logic-apps/logic-apps-overview.md)

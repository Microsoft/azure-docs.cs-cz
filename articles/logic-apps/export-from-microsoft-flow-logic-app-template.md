---
title: Export toků z Power automatu do Azure Logic Apps
description: Migrace toků z Power automatu na Azure Logic Apps exportováním jako šablon Azure Resource Manager
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, sneshaf, pinath, logicappspm
ms.topic: conceptual
ms.date: 02/22/2021
ms.openlocfilehash: f2b4e09ec9b50bb6993c89d90b0f33c0c905cbf0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101699092"
---
# <a name="export-flows-from-power-automate-and-deploy-to-azure-logic-apps"></a>Export toků z Power Automate a nasazení do Azure Logic Apps

Pokud chcete rozšířit a rozšířit možnosti toku, můžete tento tok migrovat z [Power](https://flow.microsoft.com) Automata na [Azure Logic Apps](../logic-apps/logic-apps-overview.md). Tok můžete exportovat jako šablonu Azure Resource Manager pro aplikaci logiky, nasadit tuto šablonu aplikace logiky do skupiny prostředků Azure a pak tuto aplikaci logiky otevřít v návrháři aplikace logiky.

> [!IMPORTANT]
> Export do Logic Apps není k dispozici pro automatizaci toků vytvořené po 2020. srpna. V říjnu 2020 Power Automate automatizuje vytváření standardních toků v [protokolu OpenAPI 2,0](https://swagger.io/specification/v2/). Nové toky založené na tomto protokolu jsou nekompatibilní s Logic Apps pracovními postupy, takže export těchto toků do Logic Apps byl zakázán. Místo toho musíte pro tyto toky ručně [vytvořit Logic Apps](quickstart-create-first-logic-app-workflow.md) .

V Azure Logic Apps nejsou k dispozici všechny konektory Power Automate. Můžete migrovat pouze toky Power Automate, které mají ekvivalentní konektory v Azure Logic Apps. Například Trigger tlačítka, konektor pro schvalování a konektor oznámení jsou specifické pro automatizaci. 

* Pokud chcete zjistit, které napájecí konektory nemají Logic Apps ekvivalenty, přečtěte si téma [Automatizace konektorů](/connectors/connector-reference/connector-reference-powerautomate-connectors).

* Pokud chcete zjistit, které Logic Apps konektory nemají ekvivalenty vypnutí napájení, přečtěte si téma [Logic Apps konektory](/connectors/connector-reference/connector-reference-logicapps-connectors).

## <a name="prerequisites"></a>Předpoklady

* Účet a předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Tok, který chcete exportovat z automatizace výkonu

## <a name="export-your-flow"></a>Exportujte tok

1. Přihlaste se k [Možnosti automatizace](https://flow.microsoft.com)a vyberte **Moje toky**. Najděte a vyberte svůj tok. Na panelu nástrojů vyberte tlačítko se třemi tečkami (**...**) > **exportovat**  >  **Logic Apps Template (. JSON)**.

   ![Exportovat tok z automatizace napájení](./media/export-from-microsoft-flow-logic-app-template/export-flow.png)

1. Uložte soubor. JSON vaší šablony do umístění, které chcete.

Další informace najdete v tématu [o nárůstu až Azure Logic Apps](https://flow.microsoft.com/blog/grow-up-to-logic-apps/).

## <a name="deploy-template-by-using-the-azure-portal"></a>Nasazení šablony pomocí Azure Portal

1. Přihlaste se [Azure Portal](https://portal.azure.com) pomocí svého účtu Azure.

1. Na domovské stránce Azure do vyhledávacího pole zadejte `custom template` . Z výsledků vyberte **nasadit vlastní šablonu**  >  **vytvořit**.

   ![Vyhledejte a vyberte "Template deployment"](./media/export-from-microsoft-flow-logic-app-template/select-template-deployment.png)

1. V části **vlastní nasazení** vyberte **v editoru vytvořit vlastní šablonu**.

   ![V editoru vyberte sestavit vlastní šablonu.](./media/export-from-microsoft-flow-logic-app-template/build-template-in-editor.png)

1. Na panelu nástrojů pro **úpravu šablony** vyberte **načíst soubor**.

   ![Vyberte načíst soubor.](./media/export-from-microsoft-flow-logic-app-template/load-file.png)

1. Přejděte do umístění, kam jste uložili soubor šablony JSON, který jste exportovali z automatizace Power automatu. Vyberte soubor šablony > **otevřít**.

1. Poté, co editor zobrazí JSON, parametry a prostředky v šabloně, vyberte Save ( **Uložit**).

   ![Uložení šablony](./media/export-from-microsoft-flow-logic-app-template/save-template.png)

1. Nyní zadejte další informace o aplikaci logiky.

   1. Vyberte nebo zadejte hodnoty vstupních parametrů pro šablonu.

      | Vlastnost | Popis |
      |----------|-------------|
      | **Předplatné** | Předplatné Azure, které se má použít pro fakturaci |
      | **Skupina prostředků** | Skupina prostředků Azure, která se má použít pro vaši aplikaci logiky Můžete použít existující skupinu nebo vytvořit novou. |
      | **Umístění** | Oblast Azure, která se má použít, pokud vytvoříte novou skupinu prostředků |
      | **Název aplikace logiky** | Název, který se má použít pro prostředek aplikace logiky |
      | **Umístění aplikace logiky** | Oblast Azure, ve které chcete vytvořit prostředek aplikace logiky, pokud se liší od skupiny prostředků Azure |
      | <*název připojení*> | Jeden nebo více názvů pro všechna dříve vytvořená připojení, která aplikace logiky může znovu použít <p><p>**Poznámka**: Pokud je tato aplikace logiky vaše první, vytvoří se všechna připojení jako nová, takže můžete přijmout výchozí názvy. V opačném případě můžete zadat názvy pro dříve vytvořená připojení, která můžete použít v několika aplikacích logiky. |
      |||

      Například:

      ![Zadat vstupní parametry pro šablonu](./media/export-from-microsoft-flow-logic-app-template/template-input-parameters.png)

   1. Až budete hotovi, přečtěte si **podmínky a ujednání** o vytváření potřebných prostředků Azure a podle potřeby vyfakturujte své předplatné Azure.

   1. Až budete připraveni, vyberte **Souhlasím s podmínkami a ujednáními uvedenými nad**  >  **nákupem**.

      Azure nasadí vaši šablonu jako aplikaci logiky do zadané skupiny prostředků.

1. Všechny Logic Apps, které migrujete z Power Automate, se nasazují v zakázaném stavu. Než povolíte aplikaci logiky, autorizujte všechna nová připojení pomocí následujících kroků:

   1. V Azure Portal otevřete aplikaci logiky, kterou jste vytvořili. V nabídce aplikace logiky vyberte **Návrhář aplikace logiky**.

      Každé připojení, které vyžaduje autorizaci, se zobrazuje ikona upozornění:

      ![Ikona upozornění](./media/export-from-microsoft-flow-logic-app-template/authorize-connections.png)

   1. Pro každý krok, který vyžaduje autorizované připojení, rozbalte tento krok a vyberte **Přidat nový**.

      ![Snímek obrazovky, který zobrazuje tlačítko pro přidání nového, které je vybrané v okně připojení](./media/export-from-microsoft-flow-logic-app-template/add-new-connection.png)

   1. Přihlaste se ke každé službě nebo poskytněte potřebná pověření pro autorizaci připojení.

   1. Po aktualizaci připojení vyberte na panelu nástrojů návrháře možnost **Uložit**.

1. Až budete připraveni k aktivaci aplikace logiky, v nabídce aplikace logiky vyberte **Přehled** a pak vyberte **Povolit**.

   ![Povolit aplikaci logiky](./media/export-from-microsoft-flow-logic-app-template/enable-logic-app.png)

1. Abyste se vyhnuli spouštění duplicitních pracovních postupů, ujistěte se, že jste původní tok deaktivovali nebo odstranili.

## <a name="deploy-template-by-using-visual-studio"></a>Nasazení šablony pomocí sady Visual Studio

Pokud jste nastavili sadu Visual Studio s [požadavky](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites) na vytváření aplikací logiky, můžete nasadit exportovanou šablonu pro Azure Logic Apps pomocí sady Visual Studio.

1. V aplikaci Visual Studio vyhledejte a otevřete soubor. JSON pro šablonu aplikace logiky, kterou jste exportovali z automatizace Power automatu.

1. V aplikaci Visual Studio vytvořte projekt **skupiny prostředků Azure** , který používá šablonu **Aplikace logiky** podle kroků v části [rychlý Start: vytvoření automatizovaných úloh, procesů a pracovních postupů pomocí Azure Logic Apps – Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

   Tento příklad vytvoří řešení sady Visual Studio s názvem "ImportedLogicApp".

   ![Vytvoření projektu skupiny prostředků Azure](./media/export-from-microsoft-flow-logic-app-template/create-azure-resource-group-project.png)

1. Po vytvoření řešení v Průzkumník řešení otevřete **LogicApp.jsv** souboru, pokud soubor ještě není otevřený.

1. Zkopírujte obsah z exportované šablony a přepište obsah v **LogicApp.js** souboru.

1. Před nasazením aplikace logiky autorizujte všechna nová připojení pomocí následujících kroků:

   1. Otevřete místní nabídku **LogicApp.js** a pak vyberte **otevřít v návrháři aplikace logiky**.

      ![Otevření šablony pomocí návrháře aplikace logiky](./media/export-from-microsoft-flow-logic-app-template/open-logic-app-designer.png)

   1. Po zobrazení výzvy vyberte předplatné Azure a skupinu prostředků, které chcete použít pro nasazení aplikace logiky.

      ![Výběr předplatného Azure a skupiny prostředků](./media/export-from-microsoft-flow-logic-app-template/select-azure-subscription-resource-group-deployment.png)

      Jakmile se aplikace logiky objeví v návrháři, všechna připojení, která vyžadují autorizaci, zobrazují ikony upozornění:

      ![Připojení s ikonami upozornění](./media/export-from-microsoft-flow-logic-app-template/authorize-connections-vs.png)

   1. Pro každý krok, který vyžaduje autorizované připojení, rozbalte tento krok a vyberte **Přidat nový**.

      ![Přidat nové připojení](./media/export-from-microsoft-flow-logic-app-template/add-new-connection-vs.png)

   1. Přihlaste se ke každé službě nebo poskytněte potřebná pověření pro autorizaci připojení.

   1. Před nasazením aplikace logiky uložte své řešení.

1. V Průzkumník řešení otevřete místní nabídku projektu a vyberte **nasadit**  >  **Nový**. Pokud se zobrazí výzva, přihlaste se pomocí svého účtu Azure.

1. Po zobrazení výzvy potvrďte předplatné Azure, skupinu prostředků Azure a všechna další nastavení, která chcete použít pro nasazení, například [soubor parametrů](../azure-resource-manager/templates/parameter-files.md) , který se použije pro předávání hodnot parametrů šablony, a pak vyberte **nasadit**.

   ![Potvrdit nastavení nasazení](./media/export-from-microsoft-flow-logic-app-template/confirm-azure-subscription-resource-group-deployment.png)

1. Pokud se zobrazí pole **Upravit parametry** , zadejte název vašeho prostředku aplikace logiky v Azure a vyberte **Uložit**.  

   ![Upravit parametry nasazení](./media/export-from-microsoft-flow-logic-app-template/edit-parameters-deployment.png)

   Po zahájení nasazování se v okně **Výstup** sady Visual Studio zobrazí stav nasazení vaší aplikace. Pokud se stav nezobrazí, otevřete seznam **Zobrazit výstup z** a vyberte svou skupinu prostředků Azure. Například:

   ![Výstup – okno](./media/export-from-microsoft-flow-logic-app-template/output-window.png)

   Pokud nějaká připojení ve vaší aplikaci logiky potřebuje vstup z vaší aplikace, otevře se okno PowerShellu na pozadí a zobrazí se výzva k zadání potřebných hesel nebo tajných klíčů. Po zadání těchto informací bude nasazení pokračovat.

   ![Ověřování připojení](./media/export-from-microsoft-flow-logic-app-template/logic-apps-powershell-window.png)

   Po dokončení nasazení se vaše aplikace logiky publikuje, ale neaktivuje se v Azure Portal.

1. Až budete připraveni aktivovat aplikaci logiky v Azure Portal, najděte a otevřete aplikaci logiky v návrháři aplikace logiky. V nabídce aplikace logiky vyberte **Přehled** a pak vyberte **Povolit**.

1. Abyste se vyhnuli spouštění duplicitních pracovních postupů, ujistěte se, že jste původní tok deaktivovali nebo odstranili.

Další informace o těchto krocích nasazení najdete v tématu [rychlý Start: vytvoření automatizovaných úloh, procesů a pracovních postupů pomocí Azure Logic Apps – Visual Studio.](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-to-Azure)

## <a name="next-steps"></a>Další kroky

* Další informace o [konektorech pro Azure Logic Apps](../connectors/apis-list.md)
* Další informace o [Azure Logic Apps](../logic-apps/logic-apps-overview.md)

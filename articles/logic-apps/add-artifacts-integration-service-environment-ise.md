---
title: Přidání prostředků do prostředí integrační služby
description: Přidání Logic Apps, integračních účtů, vlastních konektorů a spravovaných konektorů do prostředí Integration Service Environment (ISE)
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: dc4798df05b760074ff06d95d9712204a3cf3e5a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91269739"
---
# <a name="add-resources-to-your-integration-service-environment-ise-in-azure-logic-apps"></a>Přidání prostředků do prostředí ISE (Integration Service Environment) v Azure Logic Apps

Po vytvoření [prostředí ISE (Integration Service Environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)přidejte prostředky, jako jsou Logic Apps, integrační účty a konektory, aby měly přístup k prostředkům ve službě Azure Virtual Network. Například spravované ISE konektory, které jsou k dispozici po vytvoření ISE, se v návrháři aplikace logiky nezobrazí automaticky. Než budete moct tyto konektory ISE používat, musíte je ručně [Přidat a nasadit do ISE](#add-ise-connectors-environment) , aby se zobrazily v návrháři aplikace logiky.

> [!IMPORTANT]
> Aby Logic Apps a integrační účty fungovaly společně v ISE, musí jako své umístění používat *stejný ISE* .

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* ISE, který jste vytvořili ke spouštění aplikací logiky. Pokud nemáte ISE, [vytvořte nejprve ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

* Pokud chcete vytvořit, přidat nebo aktualizovat prostředky, které jsou nasazené na ISE, musíte mít přiřazenou roli vlastníka nebo přispěvatele na daném ISE nebo máte zděděná oprávnění prostřednictvím předplatného Azure nebo skupiny prostředků Azure přidružené k tomuto ISE. Pro uživatele, kteří nemají oprávnění vlastníka, přispěvatele nebo zděděná oprávnění, se jim dá přiřadit role prostředí integrační služby přispěvatel nebo role vývojář prostředí integrační služby. Další informace najdete v tématu [co je řízení přístupu na základě role Azure (Azure RBAC)](../role-based-access-control/overview.md)?

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-apps"></a>Vytváření aplikací logiky

Pokud chcete sestavovat aplikace logiky, které běží ve vašem prostředí ISE (Integration Service Environment), postupujte podle následujících kroků:

1. Vyhledejte a otevřete své ISE, pokud ještě není otevřený. V nabídce ISE v části **Nastavení**vyberte **Logic Apps**  >  **Přidat**.

   ![Přidání nové aplikace logiky do ISE](./media/add-artifacts-integration-service-environment-ise/add-logic-app-to-ise.png)

1. Zadejte informace o aplikaci logiky, kterou chcete vytvořit, například:

   ![Snímek obrazovky, který zobrazuje okno "vytvořit" aplikaci logiky s příklady zadaných informací.](./media/add-artifacts-integration-service-environment-ise/create-logic-app-integration-service-environment.png)

   | Vlastnost | Požaduje se | Popis |
   |----------|----------|-------------|
   | **Název** | Yes | Název aplikace logiky, která se má vytvořit |
   | **Předplatné** | Yes | Název předplatného Azure, které se má použít |
   | **Skupina prostředků** | Yes | Název skupiny prostředků Azure (nová nebo existující), která se má použít |
   | **Umístění** | Yes | V části **prostředí integrační služby**vyberte ISE, který se má použít, pokud ještě není vybraný. <p><p> **Důležité**: Pokud chcete používat vaše aplikace logiky s účtem pro integraci, musí oba používat stejný ISE. |
   ||||

1. Po dokončení vyberte **Vytvořit**.

1. Pokračujte [v vytváření aplikace logiky obvyklým způsobem](../logic-apps/quickstart-create-first-logic-app-workflow.md).

   Rozdíly ve fungování aktivačních událostí a akcí a způsobu jejich označení při použití ISE ve srovnání s více tenantů služby Logic Apps najdete v tématu věnovaném [izolaci oproti více tenantům v přehledu ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference).

1. Pokud chcete spravovat Logic Apps a připojení k rozhraní API v ISE, přečtěte si téma [Správa prostředí integrační služby](../logic-apps/ise-manage-integration-service-environment.md).

<a name="create-integration-account-environment"></a>

## <a name="create-integration-accounts"></a>Vytváření účtů pro integraci

V závislosti na [ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) vybrané při vytváření vaše ISE zahrnuje konkrétní využití účtu pro integraci bez dalších nákladů. Logic Apps, které existují v prostředí ISE (Integration Service Environment), můžou odkazovat jenom na účty pro integraci, které existují ve stejném ISE. Aby mohl účet pro integraci spolupracovat s Logic Apps v ISE, musí účet pro integraci i aplikace logiky používat *stejné prostředí* jako jejich umístění. Další informace o integračních účtech a ISEs najdete v tématu [účty pro integraci s ISE](connect-virtual-network-vnet-isolated-environment-overview.md#create-integration-account-environment).

Pokud chcete vytvořit účet pro integraci, který používá ISE, postupujte podle následujících kroků:

1. Vyhledejte a otevřete své ISE, pokud ještě není otevřený. V nabídce ISE v části **Nastavení**vyberte účty pro **integraci**  >  **Přidat**.

   ![Přidat nový účet pro integraci do ISE](./media/add-artifacts-integration-service-environment-ise/add-integration-account-to-ise.png)

1. Zadejte informace o aplikaci logiky, kterou chcete vytvořit, například:

   ![Vybrat prostředí integrační služby](./media/add-artifacts-integration-service-environment-ise/create-integration-account-integration-service-environment.png)

   | Vlastnost | Požaduje se | Popis |
   |----------|----------|-------------|
   | **Název** | Yes | Název účtu pro integraci, který chcete vytvořit. |
   | **Předplatné** | Yes | Název předplatného Azure, které chcete použít |
   | **Skupina prostředků** | Yes | Název skupiny prostředků Azure (nová nebo existující), která se má použít |
   | **Cenová úroveň** | Yes | Cenová úroveň, která se má použít pro účet pro integraci |
   | **Umístění** | Yes | V části **prostředí integrační služby**vyberte stejný ISE, které vaše aplikace logiky používají, pokud ještě není vybraná. <p><p> **Důležité**: Pokud chcete použít účet pro integraci s Logic Apps, musí oba používat stejný ISE. |
   ||||

1. Po dokončení vyberte **Vytvořit**.

1. [Obvyklým způsobem propojte aplikaci logiky s vaším integračním účtem](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account).

1. Pokračujte přidáním prostředků do účtu pro integraci, jako jsou [obchodní partneři](../logic-apps/logic-apps-enterprise-integration-partners.md) a [smlouvy](../logic-apps/logic-apps-enterprise-integration-agreements.md).

1. Pokud chcete spravovat účty pro integraci v ISE, přečtěte si téma [Správa prostředí integrační služby](../logic-apps/ise-manage-integration-service-environment.md).

<a name="add-ise-connectors-environment"></a>

## <a name="add-ise-connectors"></a>Přidat konektory ISE

Konektory spravované Microsoftem, které jsou k dispozici po vytvoření ISE, se nezobrazují automaticky v ovládacím prvku pro výběr konektoru v návrháři aplikace logiky. Než budete moct používat tyto konektory ISE, musíte je ručně přidat a nasadit do ISE, aby se zobrazily v návrháři aplikace logiky.

1. V nabídce ISE v části **Nastavení**vyberte **spravované konektory**. Na panelu nástrojů vyberte **Přidat**.

   ![Zobrazit spravované konektory](./media/add-artifacts-integration-service-environment-ise/ise-view-managed-connectors.png)

1. V podokně **Přidat nový spravovaný konektor** otevřete seznam **najít konektor** . Vyberte konektor ISE, který chcete použít, ale ve vašem ISE ještě není nasazený. Vyberte **Vytvořit**.

   ![Vyberte konektor ISE, který chcete nasadit v ISE.](./media/add-artifacts-integration-service-environment-ise/add-managed-connector.png)

   Pro vás budou dostupné jenom ISE konektory, které jsou pro vás vhodné, ale ještě nenasazené do vašeho ISEu. Konektory, které jsou už nasazené ve vašem ISE, nejsou dostupné pro výběr.

<a name="create-custom-connectors-environment"></a>

## <a name="create-custom-connectors"></a>Vytváření vlastních konektorů

Pokud chcete používat vlastní konektory v ISE, vytvářejte tyto vlastní konektory přímo v rámci vaší ISE.

1. Vyhledejte a otevřete své ISE, pokud ještě není otevřený. V nabídce ISE v části **Nastavení**vyberte **vlastní konektory**  >  **Přidat**.

   ![Vytvoření vlastního konektoru](./media/add-artifacts-integration-service-environment-ise/add-custom-connector-to-ise.png)

1. Zadejte název, předplatné Azure a skupinu prostředků Azure (nová nebo existující), která se použije pro vlastní konektor.

1. V seznamu **umístění** v části **prostředí integrační služby** vyberte stejný ISE, které vaše aplikace logiky používají, a vyberte **vytvořit**, například:

   ![Snímek obrazovky s oknem vytvořit Logic Apps vlastní konektor, kde jsou vybrané příklady informací.](./media/add-artifacts-integration-service-environment-ise/create-custom-connector-integration-service-environment.png)

1. Vyberte svůj nový vlastní konektor a pak vyberte **Upravit**, například:

   ![Vybrat a upravit vlastní konektor](./media/add-artifacts-integration-service-environment-ise/edit-custom-connectors.png)

1. Pokračujte vytvořením konektoru obvyklým způsobem z [definice openapi](/connectors/custom-connectors/define-openapi-definition#import-the-openapi-definition) nebo [protokolu SOAP](/connectors/custom-connectors/create-register-logic-apps-soap-connector#2-define-your-connector).

1. Pokud chcete spravovat vlastní konektory v ISE, přečtěte si téma [Správa prostředí integrační služby](../logic-apps/ise-manage-integration-service-environment.md).

## <a name="next-steps"></a>Další kroky

* [Správa prostředí integrační služby](../logic-apps/ise-manage-integration-service-environment.md)

---
title: Přidání prostředků do prostředí integračních služeb
description: Přidání aplikací logiky, účtů integrace, vlastních konektorů a spravovaných konektorů do prostředí integrační služby (ISE)
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: 58d2efd0c61045739930ce36ba317b1aa6a40ce8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79164875"
---
# <a name="add-resources-to-your-integration-service-environment-ise-in-azure-logic-apps"></a>Přidání prostředků do prostředí integračních služeb (ISE) v Aplikacích Azure Logic Apps

Po vytvoření [prostředí služby integrace (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)přidejte prostředky, jako jsou aplikace logiky, účty integrace a konektory, aby měly přístup k prostředkům ve vaší virtuální síti Azure. Například spravované konektory ISE, které budou k dispozici po vytvoření služby ISE, se automaticky nezobrazí v Návrháři aplikací logiky. Než budete moci použít tyto konektory ISE, budete muset ručně [přidat a nasadit tyto konektory do ise](#add-ise-connectors-environment) tak, aby se zobrazí v návrháři aplikace logiky.

> [!IMPORTANT]
> Aby aplikace logiky a účty integrace spolupracovaly v ise, obě musí používat *stejnou ise* jako jejich umístění.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* ISE, které jste vytvořili ke spuštění aplikace logiky. Pokud nemáte ISE, [vytvořte nejprve ise](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

* Chcete-li vytvořit, přidat nebo aktualizovat prostředky, které jsou nasazeny do služby ISE, musíte být přiřazeni k roli vlastníka nebo přispěvatele na této službě ISE, nebo máte oprávnění zděděná prostřednictvím předplatného Azure nebo skupiny prostředků Azure přidružené k ise. Pro uživatele, kteří nemají vlastníka, přispěvatele nebo zděděná oprávnění, může být jim přiřazena role Přispěvatel prostředí služby integrace nebo Role vývojáře prostředí služby integrace. Další informace o řízení přístupu na základě rolí (RBAC) najdete [v tématu Co je řízení přístupu na základě rolí (RBAC) pro prostředky Azure?](../role-based-access-control/overview.md)

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-apps"></a>Vytváření aplikací logiky

Chcete-li vytvářet aplikace logiky, které běží v prostředí služby integrace (ISE), postupujte takto:

1. Najděte a otevřete ise, pokud již není otevřena. V nabídce ISE vyberte v části **Nastavení**položku **Logické aplikace** > **Přidat**.

   ![Přidání nové aplikace logiky do ise](./media/add-artifacts-integration-service-environment-ise/add-logic-app-to-ise.png)

1. Zadejte informace o aplikaci logiky, kterou chcete vytvořit, například:

   ![Vybrat prostředí integrační služby](./media/add-artifacts-integration-service-environment-ise/create-logic-app-integration-service-environment.png)

   | Vlastnost | Požaduje se | Popis |
   |----------|----------|-------------|
   | **Název** | Ano | Název aplikace logiky k vytvoření |
   | **Předplatné** | Ano | Název předplatného Azure, které má být používáno |
   | **Skupina prostředků** | Ano | Název skupiny prostředků Azure (nové nebo existující) pro použití |
   | **Umístění** | Ano | V části **Prostředí služby integrace**vyberte službu ISE, která se má použít, pokud již není vybrána. <p><p> **Důležité:** Chcete-li používat aplikace logiky s účtem integrace, musí oba používat stejnou službu ISE. |
   ||||

1. Až to budete mít, vyberte **Vytvořit**.

1. Pokračujte ve [vytváření aplikace logiky obvyklým způsobem](../logic-apps/quickstart-create-first-logic-app-workflow.md).

   Rozdíly v tom, jak aktivační události a akce fungují a jak jsou označeny při použití služby ISE ve srovnání se službou Logic Apps s více tenanty, naleznete [v tématu Izolované versus více klientů v přehledu služby ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference).

1. Pokud chcete spravovat aplikace logiky a připojení rozhraní API ve vašem ISE, přečtěte si informace [o správě prostředí integračních služeb](../logic-apps/ise-manage-integration-service-environment.md).

<a name="create-integration-account-environment"></a>

## <a name="create-integration-accounts"></a>Vytvoření účtů integrace

Na základě [skladové položky ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) vybrané při vytváření zahrnuje vaše ISE konkrétní využití účtu integrace bez dalších nákladů. Aplikace logiky, které existují v prostředí služby integrace (ISE) můžete odkazovat pouze na účty integrace, které existují ve stejné ISE. Takže pro účet integrace pro práci s aplikacemi logiky v ISE, účet integrace a aplikace logiky musí používat *stejné prostředí* jako jejich umístění. Další informace o integračních účtech a ises naleznete [v tématu Integrace účtů s ISE](connect-virtual-network-vnet-isolated-environment-overview.md#create-integration-account-environment).

Chcete-li vytvořit účet integrace, který používá ise, postupujte takto:

1. Najděte a otevřete ise, pokud již není otevřena. V nabídce ISE vyberte v části **Nastavení** **položku Integration accounts** > **Add**.

   ![Přidání nového integračního účtu do ISE](./media/add-artifacts-integration-service-environment-ise/add-integration-account-to-ise.png)

1. Zadejte informace o aplikaci logiky, kterou chcete vytvořit, například:

   ![Vybrat prostředí integrační služby](./media/add-artifacts-integration-service-environment-ise/create-integration-account-integration-service-environment.png)

   | Vlastnost | Požaduje se | Popis |
   |----------|----------|-------------|
   | **Název** | Ano | Název účtu integrace, který chcete vytvořit |
   | **Předplatné** | Ano | Název předplatného Azure, který chcete použít |
   | **Skupina prostředků** | Ano | Název skupiny prostředků Azure (nové nebo existující) pro použití |
   | **Cenová úroveň** | Ano | Cenová úroveň, která se má použít pro účet integrace |
   | **Umístění** | Ano | V části Prostředí služby integrace vyberte stejnou službu ISE, kterou používají vaše aplikace **logiky,** pokud již není vybrána. <p><p> **Důležité:** Chcete-li použít účet integrace s aplikacemi logiky, musí oba používat stejnou ise. |
   ||||

1. Až to budete mít, vyberte **Vytvořit**.

1. [Propojte aplikaci logiky s účtem integrace obvyklým způsobem](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account).

1. Pokračujte přidáním zdrojů na svůj integrační účet, jako jsou [obchodní partneři](../logic-apps/logic-apps-enterprise-integration-partners.md) a [dohody](../logic-apps/logic-apps-enterprise-integration-agreements.md).

1. Informace o správě účtů integrace ve vaší službě ISE naleznete v [tématu Správa prostředí integračních služeb](../logic-apps/ise-manage-integration-service-environment.md).

<a name="add-ise-connectors-environment"></a>

## <a name="add-ise-connectors"></a>Přidání konektorů ISE

Konektory spravované společností Microsoft, které budou k dispozici po vytvoření služby ISE, se automaticky nezobrazí ve výběru konektorů v Návrháři aplikací logiky. Než budete moci použít tyto konektory ISE, budete muset ručně přidat a nasadit tyto konektory do ise tak, aby se zobrazí v návrháři aplikace logiky.

1. V nabídce ISE vyberte v části **Nastavení** **spravované konektory**. Na panelu nástrojů vyberte **Přidat**.

   ![Zobrazit spravované konektory](./media/add-artifacts-integration-service-environment-ise/ise-view-managed-connectors.png)

1. V podokně **Přidat nový spravovaný konektor** otevřete seznam Najít **konektor.** Vyberte konektor ISE, který chcete použít, ale ještě není nasazen ve vaší ISE. Vyberte **Vytvořit**.

   ![Vyberte konektor ISE, který chcete nasadit ve své ise.](./media/add-artifacts-integration-service-environment-ise/add-managed-connector.png)

   K dispozici jsou pouze konektory ISE, které jsou způsobilé, ale ještě nejsou nasazené do služby ISE. Konektory, které jsou již nasazeny ve vaší ise se zobrazí není k dispozici pro výběr.

<a name="create-custom-connectors-environment"></a>

## <a name="create-custom-connectors"></a>Vytvoření vlastních konektorů

Chcete-li ve své ise používat vlastní konektory, vytvořte tyto vlastní konektory přímo z ise.

1. Najděte a otevřete ise, pokud již není otevřena. V nabídce ISE vyberte v části **Nastavení** **položku Vlastní konektory** > **Přidat**.

   ![Vytvoření vlastního konektoru](./media/add-artifacts-integration-service-environment-ise/add-custom-connector-to-ise.png)

1. Zadejte název, předplatné Azure a skupinu prostředků Azure (nové nebo existující), které chcete použít pro vlastní konektor.

1. V seznamu **Umístění** vyberte v části Prostředí služby integrace stejnou službu ISE, kterou používají vaše aplikace **logiky,** a vyberte **Vytvořit**, například:

   ![Vybrat prostředí integrační služby](./media/add-artifacts-integration-service-environment-ise/create-custom-connector-integration-service-environment.png)

1. Vyberte nový vlastní konektor a pak vyberte **Upravit**, například:

   ![Výběr a úprava vlastního konektoru](./media/add-artifacts-integration-service-environment-ise/edit-custom-connectors.png)

1. Pokračujte vytvořením konektoru obvyklým způsobem z [definice OpenAPI](https://docs.microsoft.com/connectors/custom-connectors/define-openapi-definition#import-the-openapi-definition) nebo [SOAP](https://docs.microsoft.com/connectors/custom-connectors/create-register-logic-apps-soap-connector#2-define-your-connector).

1. Informace o správě vlastních konektorů ve službě ISE naleznete v [tématu Správa prostředí integračních služeb](../logic-apps/ise-manage-integration-service-environment.md).

## <a name="next-steps"></a>Další kroky

* [Správa prostředí integrační služby](../logic-apps/ise-manage-integration-service-environment.md)

---
title: Přidání artefaktů do prostředí integrační služby – Azure Logic Apps
description: Přidání Logic Apps, integračních účtů a vlastních konektorů do prostředí ISE (Integration Service Environment) pro přístup k virtuálním sítím Azure (virtuální sítě)
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 266c942adb62b187d39d2eac4a47455482bfadaa
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2019
ms.locfileid: "72680514"
---
# <a name="add-artifacts-to-your-integration-service-environment-ise-in-azure-logic-apps"></a>Přidání artefaktů do prostředí ISE (Integration Service Environment) v Azure Logic Apps

Po vytvoření [prostředí ISE (Integration Service Environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)přidejte artefakty, jako jsou Logic Apps, integrační účty a konektory, aby měly přístup k prostředkům ve službě Azure Virtual Network.

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* ISE, který jste vytvořili ke spouštění aplikací logiky. Pokud nemáte ISE, [vytvořte nejprve ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-apps"></a>Vytváření aplikací logiky

Pokud chcete sestavovat aplikace logiky, které běží ve vašem prostředí ISE (Integration Service Environment), postupujte podle následujících kroků:

1. Vyhledejte a otevřete své ISE, pokud ještě není otevřený. V nabídce ISE v části **Nastavení**vyberte **Logic Apps**  > **Přidat**.

   ![Přidání nové aplikace logiky do ISE](./media/add-artifacts-integration-service-environment-ise/add-logic-app-to-ise.png)

   -nebo-

   V hlavní nabídce Azure vyberte **vytvořit prostředek** > **integrace** **Aplikace logiky** > .

1. Zadejte název, předplatné Azure a skupinu prostředků Azure (nová nebo existující), která se má použít pro vaši aplikaci logiky.

1. V seznamu **umístění** v části **prostředí integrační služby** vyberte své ISE, například:

   ![Vybrat prostředí integrační služby](./media/add-artifacts-integration-service-environment-ise/create-logic-app-with-integration-service-environment.png)

   > [!IMPORTANT]
   > Pokud chcete používat aplikace logiky s účtem pro integraci, musí tyto aplikace logiky a účet pro integraci používat stejný ISE.

1. Pokračujte [v vytváření aplikace logiky obvyklým způsobem](../logic-apps/quickstart-create-first-logic-app-workflow.md).

   Rozdíly ve fungování aktivačních událostí a akcí a způsobu jejich označení při použití ISE v porovnání s globálním Logic Apps službou najdete v tématu věnovaném [izolaci a globálnímu přehledu ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference).

1. Pokud chcete spravovat Logic Apps a připojení k rozhraní API v ISE, přečtěte si téma [Správa prostředí integrační služby](../logic-apps/ise-manage-integration-service-environment.md).

<a name="create-integration-account-environment"></a>

## <a name="create-integration-accounts"></a>Vytváření účtů pro integraci

V závislosti na [ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) vybrané při vytváření vaše ISE zahrnuje konkrétní využití účtu pro integraci bez dalších nákladů. Logic Apps, které existují v prostředí ISE (Integration Service Environment), můžou odkazovat jenom na účty pro integraci, které existují ve stejném ISE. Aby mohl účet pro integraci spolupracovat s Logic Apps v ISE, musí účet pro integraci i aplikace logiky používat *stejné prostředí* jako jejich umístění. Další informace o integračních účtech a ISEs najdete v tématu [účty pro integraci s ISE](connect-virtual-network-vnet-isolated-environment-overview.md#create-integration-account-environment).

Pokud chcete vytvořit účet pro integraci, který používá ISE, postupujte podle následujících kroků:

1. Vyhledejte a otevřete své ISE, pokud ještě není otevřený. V nabídce ISE v části **Nastavení**vyberte účty pro **integraci**  > **Přidat**.

   ![Přidat nový účet pro integraci do ISE](./media/add-artifacts-integration-service-environment-ise/add-integration-account-to-ise.png)

   -nebo-

   V hlavní nabídce Azure vyberte **vytvořit prostředek**  > **Integration**  > **účet integrace**.

1. Zadejte název, předplatné Azure, skupinu prostředků Azure (novou nebo existující) a cenovou úroveň, kterou chcete použít pro účet pro integraci.

1. V seznamu **umístění** v části **prostředí integrační služby** vyberte stejný ISE, které vaše aplikace logiky používají, například:

   ![Vybrat prostředí integrační služby](./media/add-artifacts-integration-service-environment-ise/create-integration-account-with-integration-service-environment.png)

1. [Obvyklým způsobem propojte aplikaci logiky s vaším integračním účtem](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account).

1. Pokračujte tím, že do svého účtu pro integraci přidáte artefakty, jako jsou [obchodní partneři](../logic-apps/logic-apps-enterprise-integration-partners.md) a [smlouvy](../logic-apps/logic-apps-enterprise-integration-agreements.md).

1. Pokud chcete spravovat účty pro integraci v ISE, přečtěte si téma [Správa prostředí integrační služby](../logic-apps/ise-manage-integration-service-environment.md).

<a name="add-ise-connectors-environment"></a>

## <a name="add-ise-connectors"></a>Přidat konektory ISE

Můžete přidat konektory spravované Microsoftem, které jsou k dispozici pro použití ve vašem ISE, ale nejsou nasazeny ve vašich ISE.

1. V nabídce ISE v části **Nastavení**vyberte **spravované konektory**. Na panelu nástrojů vyberte **Přidat**.

   ![Zobrazit spravované konektory](./media/add-artifacts-integration-service-environment-ise/ise-view-managed-connectors.png)

1. V podokně **Přidat nový spravovaný konektor** otevřete seznam **najít konektor** . Pokud je požadovaný konektor k dispozici, vyberte tento konektor a pak vyberte **vytvořit**.

   V seznamu se zobrazují jenom konektory, které mají nárok, ale nejsou nasazené v ISE. Konektory, které jsou už nasazené ve vašem ISE, nejsou dostupné pro výběr.

   ![Vybrat způsobilý konektor](./media/add-artifacts-integration-service-environment-ise/add-managed-connector.png)

<a name="create-custom-connectors-environment"></a>

## <a name="create-custom-connectors"></a>Vytváření vlastních konektorů

Pokud chcete používat vlastní konektory v ISE, vytvářejte tyto vlastní konektory přímo v rámci vaší ISE.

1. Vyhledejte a otevřete své ISE, pokud ještě není otevřený. V nabídce ISE v části **Nastavení**vyberte **vlastní konektory**  > **Přidat**.

   ![Vytvoření vlastního konektoru](./media/add-artifacts-integration-service-environment-ise/add-custom-connector-to-ise.png)

1. Zadejte název, předplatné Azure a skupinu prostředků Azure (nová nebo existující), která se použije pro vlastní konektor.

1. V seznamu **umístění** v části **prostředí integrační služby** vyberte stejný ISE, které vaše aplikace logiky používají, a vyberte **vytvořit**, například:

   ![Vybrat prostředí integrační služby](./media/add-artifacts-integration-service-environment-ise/create-custom-connector-with-integration-service-environment.png)

1. Vyberte svůj nový vlastní konektor a pak vyberte **Upravit**, například:

   ![Vybrat a upravit vlastní konektor](./media/add-artifacts-integration-service-environment-ise/edit-custom-connectors.png)

1. Pokračujte vytvořením konektoru obvyklým způsobem z [definice openapi](https://docs.microsoft.com/connectors/custom-connectors/define-openapi-definition#import-the-openapi-definition) nebo [protokolu SOAP](https://docs.microsoft.com/connectors/custom-connectors/create-register-logic-apps-soap-connector#2-define-your-connector).

1. Pokud chcete spravovat vlastní konektory v ISE, přečtěte si téma [Správa prostředí integrační služby](../logic-apps/ise-manage-integration-service-environment.md).

## <a name="next-steps"></a>Další kroky

* [Správa prostředí integrační služby](../logic-apps/ise-manage-integration-service-environment.md)

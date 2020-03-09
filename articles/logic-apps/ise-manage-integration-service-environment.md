---
title: Správa prostředí integračních služeb v Azure Logic Apps
description: Projděte si stav sítě a spravujte aplikace logiky, připojení, vlastní konektory a účty pro integraci v prostředí ISE (Integration Service Environment) pro Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 1d91813e0f39207bcf7768de89600a6bdee0fc53
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78358870"
---
# <a name="manage-your-integration-service-environment-ise-in-azure-logic-apps"></a>Správa prostředí ISE (Integration Service Environment) v Azure Logic Apps

Pokud chcete zjistit stav sítě pro [prostředí ISE (Integration Service Environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) a spravovat Logic Apps, připojení, účty pro integraci a konektory, které existují ve vaší ISE, postupujte podle kroků v tomto tématu. Pokud chcete přidat tyto artefakty do svého ISEu, přečtěte si téma [Přidání artefaktů do prostředí integrační služby](../logic-apps/add-artifacts-integration-service-environment-ise.md).

## <a name="view-your-ise"></a>Zobrazení ISE

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. Do vyhledávacího pole na portálu zadejte "prostředí integračních služeb" a pak vyberte **prostředí integrační služby**.

   ![Hledání prostředí integrační služby](./media/ise-manage-integration-service-environment/find-integration-service-environment.png)

1. V seznamu výsledků vyberte prostředí integrační služby.

   ![Vybrat prostředí integrační služby](./media/ise-manage-integration-service-environment/select-integration-service-environment.png)

1. Přejděte k dalším oddílům, kde najdete Logic Apps, připojení, konektory nebo účty pro integraci v ISE.

<a name="check-network-health"></a>

## <a name="check-network-health"></a>Ověřit stav sítě

V nabídce ISE v části **Nastavení**vyberte **stav sítě**. V tomto podokně se zobrazuje stav podsítí a odchozí závislosti v jiných službách.

![Ověřit stav sítě](./media/ise-manage-integration-service-environment/ise-check-network-health.png)

<a name="find-logic-apps"></a>

## <a name="manage-your-logic-apps"></a>Správa aplikací logiky

Můžete zobrazit a spravovat aplikace logiky, které jsou ve vaší ISE.

1. V nabídce ISE v části **Nastavení**vyberte **Logic Apps**.

   ![Zobrazit Logic Apps](./media/ise-manage-integration-service-environment/ise-find-logic-apps.png)

1. Pokud chcete odebrat Logic Apps, které už v ISE nepotřebujete, vyberte tyto aplikace logiky a pak vyberte **Odstranit**. Pokud chcete potvrdit, že chcete odstranit, vyberte **Ano**.

<a name="find-api-connections"></a>

## <a name="manage-api-connections"></a>Správa připojení rozhraní API

Můžete zobrazit a spravovat připojení, která vytvořila aplikace logiky spuštěné v ISE.

1. V nabídce ISE v části **Nastavení**vyberte **připojení rozhraní API**.

   ![Zobrazit připojení rozhraní API](./media/ise-manage-integration-service-environment/ise-find-api-connections.png)

1. Pokud chcete odebrat připojení, která už nepotřebujete v ISE, vyberte Tato připojení a pak vyberte **Odstranit**. Pokud chcete potvrdit, že chcete odstranit, vyberte **Ano**.

<a name="manage-api-connectors"></a>

## <a name="manage-ise-connectors"></a>Správa konektorů ISE

Můžete zobrazit a spravovat konektory rozhraní API, které jsou nasazené do vaší ISE.

1. V nabídce ISE v části **Nastavení**vyberte **spravované konektory**.

   ![Zobrazit spravované konektory](./media/ise-manage-integration-service-environment/ise-view-managed-connectors.png)

1. Pokud chcete odebrat konektory, které nechcete mít k dispozici ve vašem ISE, vyberte tyto konektory a pak vyberte **Odstranit**. Pokud chcete potvrdit, že chcete odstranit, vyberte **Ano**.

<a name="find-custom-connectors"></a>

## <a name="manage-custom-connectors"></a>Správa vlastních konektorů

Můžete zobrazit a spravovat vlastní konektory, které jste nasadili do ISE.

1. V nabídce ISE v části **Nastavení**vyberte **vlastní konektory**.

   ![Vyhledání vlastních konektorů](./media/ise-manage-integration-service-environment/ise-find-custom-connectors.png)

1. Pokud chcete z ISE odebrat vlastní konektory, které už nepotřebujete, vyberte tyto konektory a pak vyberte **Odstranit**. Pokud chcete potvrdit, že chcete odstranit, vyberte **Ano**.

<a name="find-integration-accounts"></a>

## <a name="manage-integration-accounts"></a>Správa účtů pro integraci

1. V nabídce ISE v části **Nastavení**vyberte účty pro **integraci**.

   ![Najít účty pro integraci](./media/ise-manage-integration-service-environment/ise-find-integration-accounts.png)

1. Chcete-li odebrat účty pro integraci z ISE, pokud už je nepotřebujete, vyberte tyto účty pro integraci a pak vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

* Přečtěte si, jak se [připojit k virtuálním sítím Azure z izolovaných aplikací logiky](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) .

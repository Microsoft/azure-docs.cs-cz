---
title: Správa prostředí integračních služeb v Azure Logic Apps
description: Projděte si stav sítě a spravujte aplikace logiky, připojení, vlastní konektory a účty pro integraci v prostředí ISE (Integration Service Environment) pro Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/26/2019
ms.openlocfilehash: 8f10e3d3fd7c67d1e803e8f85c9918c91bb81d59
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68517459"
---
# <a name="manage-your-integration-service-environment-ise-in-azure-logic-apps"></a>Správa prostředí ISE (Integration Service Environment) v Azure Logic Apps

Pokud chcete zjistit stav sítě pro [prostředí ISE (Integration Service Environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) a spravovat Logic Apps, připojení, účty pro integraci a vlastní konektory, které existují ve vaší ISE, postupujte podle kroků v tomto tématu.

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

1. V nabídce ISE v části **Nastavení**vyberte **Logic Apps**.

   ![Hledání aplikací logiky](./media/ise-manage-integration-service-environment/ise-find-logic-apps.png)

1. Pokud už nepotřebujete aplikace logiky odebrat z ISE, vyberte je a pak vyberte **Odstranit**.

<a name="find-api-connections"></a>

## <a name="manage-api-connections"></a>Správa připojení rozhraní API

1. Pokud chcete zobrazit připojení rozhraní API vytvořená aplikace logiky, která běží ve vašem ISE, v nabídce ISE v části **Nastavení**vyberte **připojení rozhraní API**.

   ![Najít připojení rozhraní API](./media/ise-manage-integration-service-environment/ise-find-api-connections.png)

1. Pokud už je nepotřebujete, chcete připojení z ISE odebrat, vyberte Tato připojení a pak vyberte **Odstranit**.

<a name="find-custom-connectors"></a>

## <a name="manage-custom-connectors"></a>Správa vlastních konektorů

1. Pokud chcete zobrazit vlastní konektory, které jste vytvořili v ISE, v nabídce ISE v části **Nastavení**vyberte **vlastní konektory**.

   ![Vyhledání vlastních konektorů](./media/ise-manage-integration-service-environment/ise-find-custom-connectors.png)

1. Pokud už je nepotřebujete, můžete z ISE odebrat vlastní konektory, vybrat tyto konektory a pak vybrat **Odstranit**.

<a name="find-integration-accounts"></a>

## <a name="manage-integration-accounts"></a>Správa účtů pro integraci

1. V nabídce ISE v části **Nastavení**vyberte účty pro **integraci**.

   ![Najít účty pro integraci](./media/ise-manage-integration-service-environment/ise-find-integration-accounts.png)

1. Chcete-li odebrat účty pro integraci z ISE, pokud už je nepotřebujete, vyberte tyto účty pro integraci a pak vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

* Přečtěte si, jak se [připojit k virtuálním sítím Azure z izolovaných aplikací logiky](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) .

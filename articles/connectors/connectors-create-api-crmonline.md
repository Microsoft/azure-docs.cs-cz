---
title: Připojení k Dynamics 365
description: Vytváření a Správa záznamů Dynamics 365 pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jdaly, logicappspm
ms.topic: conceptual
ms.date: 05/09/2020
tags: connectors
ms.openlocfilehash: 00bf8ea2b783e09711a95f203bdfcce0e6b90b2c
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/09/2020
ms.locfileid: "82994310"
---
# <a name="create-and-manage-records-in-dynamics-365-by-using-azure-logic-apps"></a>Vytváření a Správa záznamů v Dynamics 365 pomocí Azure Logic Apps

Dynamics 365 používá [Common data Service](https://docs.microsoft.com/powerapps/maker/common-data-service/data-platform-intro). Pro připojení k Dynamics 365 použijte [konektor Common data Service](https://docs.microsoft.com/connectors/commondataservice/).

> [!IMPORTANT]
> [Konektor Dynamics 365](https://docs.microsoft.com/connectors/dynamicscrmonline/) je zastaralý, ale nadále funguje, dokud ho neodeberete. Pro nové Logic Apps Nepoužívejte konektor Dynamics 365. Pro odebrání konektoru Dynamics 365 se ještě neoznámila žádná časová osa. Nemusíte převádět existující Logic Apps na konektor Common Data Service ani na jiný plánovaný nový konektor, ale při odebrání konektoru je potřeba převést aplikace logiky. Další informace najdete v tématu [konektor pro Dynamics 365 je zastaralý](https://docs.microsoft.com/power-platform/important-changes-coming).
>
> [Konektor Common data Service](https://docs.microsoft.com/connectors/commondataservice/) poskytuje stejné možnosti jako zastaralé konektory Dynamics 365, ale obsahuje vylepšení, která zvyšují spolehlivost. Informace o použití konektoru Common Data Service ve službě Logic Apps najdete v tématu [vytváření a správa Common data Servicech záznamů pomocí Azure Logic Apps](../connectors/connect-common-data-service.md).

Další informace o Common Data Service najdete v těchto tématech:

* [Další informace: Začínáme s Common Data Service](https://docs.microsoft.com/learn/modules/get-started-with-powerapps-common-data-service/)
* [Naučte se: Připojte a analyzujte data Dynamics 365 pomocí Power Platform a Common Data Service](https://docs.microsoft.com/learn/wwl/connect-analyze-dynamics-365-data/)
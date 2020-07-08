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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "82994310"
---
# <a name="create-and-manage-records-in-dynamics-365-by-using-azure-logic-apps"></a>Vytváření a Správa záznamů v Dynamics 365 pomocí Azure Logic Apps

Dynamics 365 používá [Common data Service](https://docs.microsoft.com/powerapps/maker/common-data-service/data-platform-intro). Pro připojení k Dynamics 365 použijte [konektor Common data Service](https://docs.microsoft.com/connectors/commondataservice/).

> [!IMPORTANT]
> [Konektor Dynamics 365](https://docs.microsoft.com/connectors/dynamicscrmonline/) je zastaralý, ale nadále funguje, dokud ho neodeberete. Pro nové Logic Apps Nepoužívejte konektor Dynamics 365. Pro odebrání konektoru Dynamics 365 se ještě neoznámila žádná časová osa. Stávající aplikace nemusíte převádět na konektor Common Data Service ani žádný jiný plánovaný nový konektor, ale budete to muset udělat po odebrání konektoru. Další informace najdete v tématu [konektor pro Dynamics 365 je zastaralý](https://docs.microsoft.com/power-platform/important-changes-coming).
>
> [Konektor Common Data Service](https://docs.microsoft.com/connectors/commondataservice/) nabízí stejné funkce jako zastaralý konektor Dynamics 365, ale obsahuje vylepšení, která zvyšují spolehlivost. Informace o používání konektoru Common Data Service v aplikacích logiky najdete v tématu [Vytváření a správa záznamů Common Data Service s využitím Azure Logic Apps](../connectors/connect-common-data-service.md).

Další informace o Common Data Service najdete v těchto tématech:

* [Další informace: Začínáme s Common Data Service](https://docs.microsoft.com/learn/modules/get-started-with-powerapps-common-data-service/)
* [Naučte se: Připojte a analyzujte data Dynamics 365 pomocí Power Platform a Common Data Service](https://docs.microsoft.com/learn/wwl/connect-analyze-dynamics-365-data/)
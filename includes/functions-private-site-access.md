---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: 2d66e7f497f85141de172c59b67676e1bb93955e
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578809"
---
Služba [Azure Private Endpoint](../articles/private-link/private-endpoint-overview.md) je síťové rozhraní, které umožňuje soukromé a bezpečné připojení ke službě, která používá technologii Azure Private Link.  Privátní koncový bod používá privátní IP adresu vaší virtuální sítě a tím vlastně přináší službu do vaší virtuální sítě.

Pro své funkce hostované v plánech [Premium](../articles/azure-functions/functions-premium-plan.md) a [App Service](../articles/azure-functions/functions-scale.md#app-service-plan) můžete použít soukromý koncový bod.

Když vytváříte příchozí připojení privátního koncového bodu pro funkce, budete k překladu privátní adresy potřebovat záznam DNS.  Ve výchozím nastavení se při vytváření privátního koncového bodu pomocí Azure Portal vytvoří privátní záznam DNS.

Další informace najdete v tématu [použití privátních koncových bodů pro Web Apps](../articles/app-service/networking/private-endpoint.md).
---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: 3c9679f3d66d58c7a6c847b54c84438c979ecd39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97936854"
---
Služba [Azure Private Endpoint](../articles/private-link/private-endpoint-overview.md) je síťové rozhraní, které umožňuje soukromé a bezpečné připojení ke službě, která používá technologii Azure Private Link.  Privátní koncový bod používá privátní IP adresu vaší virtuální sítě a tím vlastně přináší službu do vaší virtuální sítě.

Pro své funkce hostované v plánech [Premium](../articles/azure-functions/functions-premium-plan.md) a [App Service](../articles/azure-functions/dedicated-plan.md) můžete použít soukromý koncový bod.

Když vytváříte příchozí připojení privátního koncového bodu pro funkce, budete k překladu privátní adresy potřebovat záznam DNS.  Ve výchozím nastavení se při vytváření privátního koncového bodu pomocí Azure Portal vytvoří privátní záznam DNS.

Další informace najdete v tématu [použití privátních koncových bodů pro Web Apps](../articles/app-service/networking/private-endpoint.md).
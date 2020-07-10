---
title: Oblasti, které podporují Zóny dostupnosti v Azure
description: Pokud chcete vytvářet vysoce dostupné a odolné aplikace v Azure, Zóny dostupnosti poskytovat fyzicky samostatná umístění, která můžete použít ke spouštění svých prostředků.
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 07/06/2020
ms.author: cynthn
ms.custom: fasttrack-edit, mvc, references_regions
ms.openlocfilehash: 2e337628542475c081a59bffd25368de313011f8
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2020
ms.locfileid: "86206209"
---
# <a name="regions-that-support-availability-zones-in-azure"></a>Oblasti, které podporují Zóny dostupnosti v Azure

Zóny dostupnosti je nabídka s vysokou dostupností, která chrání vaše aplikace a data při selhání datacentra. Další informace o Zóny dostupnosti najdete v tématu [oblasti a zóny dostupnosti v Azure](az-overview.md).

## <a name="services-support-by-region"></a>Podpora služeb podle oblasti

V této části jsou uvedené služby a oblasti Azure, které podporují Zóny dostupnosti.

Služby, které jsou dostupné v jednotlivých oblastech, společně s nadcházejícím plánem dostupnosti, najdete v [dostupných produktech](https://azure.microsoft.com/global-infrastructure/services/)v jednotlivých oblastech.

| Služba |Severní Amerika: Střed USA|Severní Amerika: Východní USA|Severní Amerika: Východní USA 2|Severní Amerika: Západní USA 2|Evropa: Francie – střed|Evropa: Severní Evropa|Evropa: Velká Británie – jih|Evropa: Západní Evropa|Asie a Tichomoří: Japonsko – východ|Asie a Tichomoří: jihovýchodní Asie|Asie a Tichomoří: Austrálie – východ|
|----------------------------|----------|----------|---------|---------|--------------|------------|--------|----------|----------|-------------|-------------|
| **Výpočetní služby**                         |            |              |           |           |                |              |          |             |            |                |                |
| Linux Virtual Machines          | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       | &#10003;               |
| Windows Virtual Machines        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       | &#10003;               |
| Virtual Machine Scale Sets      | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       | &#10003;               |
| INTERNÍHO nástroje prostředí Azure App Service | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| Azure Kubernetes Service        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| **Storage**   |            |              |           |           |                |              |          |             |            |                |                |
| Spravované disky                   | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       | &#10003;               |
| Zóna – redundantní úložiště          | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       | &#10003;               |
| **Sítě**                     |            |              |           |           |                |              |          |             |            |                |                |
| Standardní IP adresa        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       | &#10003;               |
| Standard Load Balancer     | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       | &#10003;               |
| VPN Gateway            | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       | &#10003;               |
| Brána ExpressRoute   | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       | &#10003;               |
| Application Gateway (v2)    | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       | &#10003;               |
| Azure Firewall           | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    |  &#10003;       | &#10003;       |                |
| **Databáze**                     |            |              |           |           |                |              |          |             |            |                |                |
| Průzkumník dat Azure                   | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;        | &#10003;       |                |
| Databáze SQL                    | &#10003;   | &#10003;     | &#10003;  | &#10003; (Preview) | &#10003;       | &#10003; (Preview)     | &#10003; | &#10003;    | &#10003;       | &#10003;       |&#10003;        |
| Azure Cache for Redis           | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    |  &#10003;       | &#10003;       |                |
| Azure Cosmos DB      | &#10003;   |  &#10003;  |  &#10003; | &#10003; |  &#10003;  | &#10003;    | &#10003; |  &#10003;   |     &#10003;       | &#10003;    | &#10003;  |
| **Analýzy**                       |            |              |           |           |                |              |          |             |            |                |                |
| Event Hubs                      | &#10003;   |   &#10003; | &#10003;  | &#10003;  | &#10003; | &#10003; | &#10003; | &#10003; | &#10003; | &#10003;       |  &#10003;              |
| **Integrace**                     |            |              |           |           |                |              |          |             |            |                |                |
| Service Bus (jenom úroveň Premium) | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |  &#10003;              |
| Event Grid | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |                |
| **Identita**                     |            |              |           |           |                |              |          |             |            |                |                |
| Azure AD Domain Services | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |                |

Azure také nabízí podporu Zóny dostupnosti v následujících oblastech:

- USA (Gov) – Virginia
- Austrálie – východ
- Jižní Afrika – sever
- Středojižní USA
- Střední Kanada

Pokud chcete získat další informace o podpoře Zóny dostupnosti v těchto pěti oblastech, obraťte se na prodejce od Microsoftu nebo zákazníka.

## <a name="next-steps"></a>Další kroky

- [Oblasti a zóny dostupnosti v Azure](az-overview.md)

---
title: Security controls for Azure Load Balancer
description: A checklist of security controls for evaluating Load Balancer
services: load-balancer
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: allensu
ms.openlocfilehash: 6043e574697489b6566641c352bc21a2b6d87f51
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74214898"
---
# <a name="security-controls-for-azure-load-balancer"></a>Security controls for Azure Load Balancer

This article documents the security controls built into Azure Load Balancer.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Síť

| Security control | Ano/Ne | Poznámky |
|---|---|--|
| Service endpoint support| Nevztahuje se | |
| VNet injection support| Nevztahuje se | |
| Network Isolation and Firewalling support| Nevztahuje se |  |
| Forced tunneling support| Nevztahuje se | |

## <a name="monitoring--logging"></a>Monitoring & logging

| Security control | Ano/Ne | Poznámky|
|---|---|--|
| Azure monitoring support (Log analytics, App insights, etc.)| Ano | See [Azure Monitor logs for public Basic Load Balancer](load-balancer-monitor-log.md). |
| Control and management plane logging and audit| Ano | See [Azure Monitor logs for public Basic Load Balancer](load-balancer-monitor-log.md). |
| Data plane logging and audit | Nevztahuje se |  |

## <a name="identity"></a>Identita

| Security control | Ano/Ne | Poznámky|
|---|---|--|
| Ověření| Nevztahuje se |  |
| Autorizace| Nevztahuje se |  |

## <a name="data-protection"></a>Ochrana dat

| Security control | Ano/Ne | Poznámky |
|---|---|--|
| Server-side encryption at rest: Microsoft-managed keys | Nevztahuje se | |
| Encryption in transit (such as ExpressRoute encryption, in VNet encryption, and VNet-VNet encryption )| Nevztahuje se | |
| Server-side encryption at rest: customer-managed keys (BYOK) | Nevztahuje se | |
| Column level encryption (Azure Data Services)| Nevztahuje se | |
| API calls encrypted| Ano | Via the [Azure Resource Manager](../azure-resource-manager/index.yml). |

## <a name="configuration-management"></a>Správa konfigurace

| Security control | Ano/Ne | Poznámky|
|---|---|--|
| Configuration management support (versioning of configuration, etc.)| Nevztahuje se |  | 

## <a name="next-steps"></a>Další kroky

- Learn more about the [built-in security controls across Azure services](../security/fundamentals/security-controls.md).
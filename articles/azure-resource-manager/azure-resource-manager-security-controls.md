---
title: Ovládací prvky zabezpečení pro Azure Resource Manager
description: Kontrolní seznam ovládacích prvků zabezpečení pro vyhodnocení Azure Resource Manager
services: azure-resource-manager
author: msmbaldwin
manager: rkarlin
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 97fd6611d7e2a2787b865365c4c7579f89f17d01
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886792"
---
# <a name="security-controls-for-azure-resource-manager"></a>Ovládací prvky zabezpečení pro Azure Resource Manager

Tento článek popisuje ovládací prvky zabezpečení integrované do Azure Resource Manager.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="data-protection"></a>Ochrana dat

| Řízení zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování na straně serveru v klidovém umístění: Klíče spravované společností Microsoft | Ano |  |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě)| Ano | HTTPS/TLS. |
| Šifrování na straně serveru v klidovém umístění: klíče spravované zákazníkem (BYOK) | Není k dispozici | Azure Resource Manager neukládá žádný zákaznický obsah, řídí se jenom data. |
| Šifrování na úrovni sloupce (Azure Data Services)| Ano | |
| Zašifrovaná volání rozhraní API| Ano | |

## <a name="network"></a>Síť

| Řízení zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Ne | |
| Podpora vkládání virtuální sítě| Ano | |
| Izolace sítě a podpora brány firewall| Ne |  |
| Podpora vynuceného tunelování| Ne |  |

## <a name="monitoring--logging"></a>Monitorování protokolování &

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora monitorování Azure (Log Analytics, App Insights atd.)| Ne | |
| Protokolování a audit roviny řízení a správy| Ano | Protokoly aktivit zpřístupňují všechny operace zápisu (PUT, POST, DELETE) provedené na vašich prostředcích. v tématu [zobrazení protokolů aktivit můžete auditovat akce u prostředků](resource-group-audit.md). |
| Protokolování a audit roviny dat| Není k dispozici | |

## <a name="identity"></a>Identita

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ověřování| Ano | Na základě [Azure Active Directory](/azure/active-directory) .|
| Authorization| Ano | |

## <a name="configuration-management"></a>Správa konfigurace

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (Správa verzí konfigurace atd.)| Ano |  |

## <a name="next-steps"></a>Další postup

- Přečtěte si další informace o [integrovaných kontrolních prvcích zabezpečení napříč službami Azure](../security/fundamentals/security-controls.md).
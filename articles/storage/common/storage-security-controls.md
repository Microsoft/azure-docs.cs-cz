---
title: Ovládací prvky zabezpečení pro Azure Storage
description: Kontrolní seznam ovládacích prvků zabezpečení pro vyhodnocení Azure Storage
services: storage
documentationcenter: ''
author: msmbaldwin
manager: rkarlin
ms.service: storage
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 2cc54077456fce1e7e0f47843a762beee8e715f7
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2020
ms.locfileid: "77526752"
---
# <a name="security-controls-for-azure-storage"></a>Ovládací prvky zabezpečení pro Azure Storage

Tento článek popisuje ovládací prvky zabezpečení integrované do Azure Storage. 

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>Ochrana dat

| Řízení zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování na straně serveru v klidovém umístění: klíče spravované společností Microsoft | Ano |  |
| Šifrování na straně serveru v klidovém umístění: klíče spravované zákazníkem (BYOK) | Ano | Viz [šifrování služby Storage použití klíčů spravovaných zákazníkem v Azure Key Vault](storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).|
| Šifrování na úrovni sloupce (Azure Data Services)| NEUŽÍVÁ SE. |  |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě)| Ano | Podporuje standardní mechanismy protokolu HTTPS/TLS.  Uživatelé mohou také šifrovat data před jejich přenosem do služby. |
| Zašifrovaná volání rozhraní API| Ano |  |

## <a name="network"></a>Síť

| Řízení zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Ano |  |
| Podpora vkládání virtuální sítě| NEUŽÍVÁ SE. |  |
| Izolace sítě a podpora brány firewall| Ano | |
| Podpora vynuceného tunelování| NEUŽÍVÁ SE. |  |

## <a name="monitoring--logging"></a>Monitorování protokolování &

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora monitorování Azure (Log Analytics, App Insights atd.)| Ano | Azure Monitor metriky|
| Protokolování a audit roviny řízení a správy | Ano | Protokol aktivit Azure Resource Manager |
| Protokolování a audit roviny dat| Ano | Protokoly diagnostiky služby.|

## <a name="identity"></a>Identita

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ověřování| Ano | Azure Active Directory, sdílený klíč, sdílený přístupový token. |
| Autorizace| Ano | Podpora autorizace přes RBAC, seznamy ACL v POSIX a tokeny SAS |

## <a name="configuration-management"></a>Správa konfigurace

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (Správa verzí konfigurace atd.)| Ano | Podpora správy verzí poskytovatele prostředků prostřednictvím rozhraní API Azure Resource Manager |

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [integrovaných kontrolních prvcích zabezpečení napříč službami Azure](../../security/fundamentals/security-controls.md).
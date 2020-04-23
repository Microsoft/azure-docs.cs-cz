---
title: Ovládací prvky zabezpečení
titleSuffix: Azure Storage
description: Kontrolní seznam ovládacích prvků zabezpečení pro vyhodnocení Azure Storage.
services: storage
author: msmbaldwin
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: mbaldwin
ms.openlocfilehash: f03f497051367d36bd229a3f358d28a1130ec620
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2020
ms.locfileid: "82082352"
---
# <a name="security-controls-for-azure-storage"></a>Ovládací prvky zabezpečení pro Azure Storage

Tento článek dokumentuje ovládací prvky zabezpečení integrované do Služby Azure Storage.

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>Ochrana dat

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování na straně serveru v klidovém stavu: Klíče spravované společností Microsoft | Ano |  |
| Šifrování na straně serveru v klidovém stavu: klíče spravované zákazníkem (BYOK) | Ano | Viz [Šifrování služby Storage Service pomocí klíčů spravovaných zákazníkem v trezoru klíčů Azure](storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).|
| Šifrování na úrovni sloupců (Azure Data Services)| – |  |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě)| Ano | Podpora standardních mechanismů HTTPS/TLS.  Uživatelé mohou také šifrovat data před jejich přenosem do služby. |
| Zašifrovaná volání rozhraní API| Ano |  |

## <a name="network"></a>Síť

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Ano |  |
| Podpora značek služeb| Ano | Další informace o značkách služeb podporovaných službou Azure Storage najdete v tématu [Přehled značek služeb Azure.](../../virtual-network/service-tags-overview.md) |
| Podpora vstřikování virtuální sítě| – |  |
| Podpora izolace sítě a brány firewall| Ano | |
| Podpora vynuceného tunelování| – |  |

## <a name="monitoring--logging"></a>Sledování & protokolování

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora monitorování Azure (analýza protokolů, přehledy aplikací atd.)| Ano | Metriky azure monitoru|
| Protokolování a audit roviny řízení a správy | Ano | Protokol aktivit Správce prostředků Azure |
| Protokolování a audit roviny dat| Ano | Diagnostické protokoly služby.|

## <a name="identity"></a>Identita

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Authentication| Ano | Azure Active Directory, Sdílený klíč, Sdílený přístupový token. |
| Autorizace| Ano | Autorizace podpory prostřednictvím aklů RBAC, POSIX a tokenů SAS |

## <a name="configuration-management"></a>Správa konfigurace

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (správa verzí konfigurace atd.)| Ano | Správa verzí zprostředkovatele prostředků podpory prostřednictvím api Správce prostředků Azure |

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [integrovaných ovládacích prvcích zabezpečení napříč službami Azure](../../security/fundamentals/security-controls.md).
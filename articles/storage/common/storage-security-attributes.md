---
title: Běžné atributy zabezpečení pro službu Azure Storage
description: Kontrolní seznam společné atributy zabezpečení za vaše rozhodnutí vyzkoušet služby Azure Storage
services: storage
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: storage
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 33765d57f1a0e5788011b2d9d9c2f57d06713ddb
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2019
ms.locfileid: "59680685"
---
# <a name="common-security-attributes-for-azure-storage"></a>Běžné atributy zabezpečení pro službu Azure Storage

Zabezpečení je integrované do všech oblastí služby Azure. Tento článek popisuje běžné atributy zabezpečení integrované do služby Azure Storage. 

[!INCLUDE [Security Attributes Header](../../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Preventivní

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování v klidovém stavu:<ul><li>Šifrování na straně serveru</li><li>Šifrování na straně serveru pomocí klíčů spravovaných zákazníkem</li><li>Další funkce šifrování (například klientů, funkce always encrypted, atd.)</ul>| Ano |  |
| Šifrování během přenosu:<ul><li>Express route šifrování</li><li>Ve virtuální síti šifrování</li><li>Šifrování virtuálními sítěmi</ul>| Ano | Podpora standardních mechanismů protokolu HTTPS/TLS.  Uživatelé můžou taky k šifrování dat před odesláním do služby. |
| Šifrovací klíč zpracování (CMK, BYOK, atd.)| Ano | Zobrazit [šifrování služby Storage pomocí klíčů spravovaných zákazníkem ve službě Azure Key Vault](storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).|
| Šifrování na úrovni sloupce (datových služeb Azure)| neuvedeno |  |
| Šifrované volání rozhraní API| Ano |  |

## <a name="network-segmentation"></a>Segmentace sítě

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Koncový bod služby podpory| Ano |  |
| Vkládání podpory virtuálních sítí| neuvedeno |  |
| Izolace sítě / funkce Podpora brány firewall| Ano | |
| Podpora pro vynucené tunelování | neuvedeno |  |

## <a name="detection"></a>Detection (Detekce)

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora (Log analytics, App insights atd.) pro monitorování Azure| Ano | Metriky Azure Monitor dostupné nyní, protokoly počáteční verzi preview |

## <a name="iam-support"></a>Podpora IAM

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Řízení přístupu – ověření| Ano | Azure Active Directory, sdílený klíč, sdílený přístupový token. |
| Řízení přístupu – ověření| Ano | Podporu ověřování přes RBAC, seznamech ACL POSIX a tokeny SAS |


## <a name="audit-trail"></a>Záznam pro audit

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Plánování a řízení protokolování a auditování | Ano | Protokol aktivit v Azure Resource Manageru |
| Protokolování a auditování RP roviny dat| Ano | Diagnostické protokoly služby a Azure Monitor protokolování počáteční verzi preview  |

## <a name="configuration-management"></a>Správa konfigurace

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (verze konfigurace atd.)| Ano | Podpora správy verzí zprostředkovatele prostředků prostřednictvím rozhraní API Azure Resource Manageru |
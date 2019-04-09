---
author: msmbaldwin
ms.service: storage
ms.topic: include
ms.date: 03/15/2019
ms.author: mbaldwin
ms.openlocfilehash: b242bda524c747b28453061c797afde02cf6f455
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/04/2019
ms.locfileid: "59007513"
---
## <a name="preventative"></a>Preventivní

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování v klidovém stavu:<ul><li>Šifrování na straně serveru</li><li>Šifrování na straně serveru pomocí klíčů spravovaných zákazníkem</li><li>Další funkce šifrování (například klientů, funkce always encrypted, atd.)</ul>| Ano |  |
| Šifrování během přenosu:<ul><li>Express route šifrování</li><li>Ve virtuální síti šifrování</li><li>Šifrování virtuálními sítěmi</ul>| Ano | Podpora standardních mechanismů protokolu HTTPS/TLS.  Uživatelé můžou taky k šifrování dat před odesláním do služby. |
| Šifrovací klíč zpracování (CMK, BYOK, atd.)| Ano | Zobrazit [šifrování služby Storage pomocí klíčů spravovaných zákazníkem ve službě Azure Key Vault](../articles/storage/common/storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).|
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
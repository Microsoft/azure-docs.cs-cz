---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 03/15/2019
ms.author: mbaldwin
ms.openlocfilehash: 0a52b7a5234b292fa3f8c3e19fe7f62f989d639c
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/04/2019
ms.locfileid: "59007237"
---
## <a name="preventative"></a>Preventivní

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování v klidovém stavu:<ul><li>Šifrování na straně serveru</li><li>Šifrování na straně serveru pomocí klíčů spravovaných zákazníkem</li><li>Další funkce šifrování (například klientů, funkce always encrypted, atd.)</ul>| Ano | Všechny objekty jsou šifrovaná. |
| Šifrování během přenosu:<ul><li>Express route šifrování</li><li>Ve virtuální síti šifrování</li><li>Šifrování virtuálními sítěmi</ul>| Ano | Veškerá komunikace probíhá přes šifrované volání rozhraní API |
| Šifrovací klíč zpracování (CMK, BYOK, atd.)| Ano | Zákazník řídí všechny klíče ve své službě Key Vault. Pokud jsou zadány klíče modulu (HSM) zajišťuje hardwarové zabezpečení, HSM FIPS úroveň 2 chrání klíče, certifikát nebo tajný klíč. |
| Šifrování na úrovni sloupce (datových služeb Azure)| neuvedeno |  |
| Šifrované volání rozhraní API| Ano | Pomocí protokolu HTTPS. |

## <a name="network-segmentation"></a>Segmentace sítě

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Koncový bod služby podpory| Ano | Pomocí koncových bodů služby Virtual Network (Vnet). |
| Vkládání podpory virtuálních sítí| Ne |  |
| Izolace sítě / funkce Podpora brány firewall| Ano | Pomocí pravidel brány firewall virtuální sítě. |
| Podpora pro vynucené tunelování | Ne |  |

## <a name="detection"></a>Detection (Detekce)

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora (Log analytics, App insights atd.) pro monitorování Azure| Ano | Používat službu Log Analytics. |

## <a name="iam-support"></a>Podpora IAM

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Řízení přístupu – ověření| Ano | Ověřování je prostřednictvím Azure Active Directory. |
| Řízení přístupu – ověření| Ano | Pomocí zásady přístupu trezoru klíčů. |


## <a name="audit-trail"></a>Záznam pro audit

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Řízení a rovinou protokolování a auditování| Ano | Používat službu Log Analytics. |
| Protokolování a auditování RP roviny dat| Ano | Používat službu Log Analytics. |

## <a name="access-controls"></a>Ovládací prvky přístupu

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Řízení přístupu roviny řízení a správy | Ano | Řízení přístupu podle role (RBAC) služby Azure Resource Manager |
| Řízení přístupu roviny dat (na všech úrovních služby) | Ano | Zásady přístupu trezoru klíčů |

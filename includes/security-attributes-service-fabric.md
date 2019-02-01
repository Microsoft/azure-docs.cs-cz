---
author: msmbaldwin
ms.service: service-fabric
ms.topic: include
ms.date: 01/31/2019
ms.author: mbaldwin
ms.openlocfilehash: a20311a0285bf8fda5498241a60b85093039ad19
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55513481"
---
## <a name="preventative"></a>Preventivní

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování v klidovém stavu:<ul><li>Šifrování na straně serveru</li><li>Šifrování na straně serveru pomocí klíčů spravovaných zákazníkem</li><li>Další funkce šifrování (například klientů, funkce always encrypted, atd.)</ul>| Ano | Zákazník je vlastníkem služby clusteru a škálovací sady virtuálních počítačů (VM), které clusteru je postavená na. Azure disk encryption je možné povolit ve škálovací sadě virtuálních počítačů. |
| Šifrování během přenosu:<ul><li>Express route šifrování</li><li>Ve virtuální síti šifrování</li><li>Šifrování virtuálními sítěmi</ul>| Ano |  |
| Šifrovací klíč zpracování (CMK, BYOK, atd.)| Ano | Zákazník je vlastníkem služby clusteru a škálovací sady virtuálních počítačů (VM), které clusteru je postavená na. Azure disk encryption je možné povolit ve škálovací sadě virtuálních počítačů. |
| Šifrování na úrovni sloupce (datových služeb Azure)| neuvedeno |  |
| Šifrované volání rozhraní API| Ano | Volání rozhraní API Service Fabric provádí prostřednictvím Azure Resource Manageru. Vyžaduje se platný webový token JSON (JWT). |

## <a name="network-segmentation"></a>Segmentace sítě

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Koncový bod služby podpory| Ano |  |
| Vkládání podpory virtuálních sítí| Ano |  |
| Izolace sítě / funkce Podpora brány firewall| Ano | Použití skupin zabezpečení sítě (NSG). |
| Podpora pro vynucené tunelování | Ano | Sítě Azure poskytují vynucené tunelování. |

## <a name="detection"></a>Detection (Detekce)

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora (Log analytics, App insights atd.) pro monitorování Azure| Ano | Pomocí Azure monitoring podporu a podporu třetích stran. |

## <a name="iam-support"></a>Podpora IAM

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Řízení přístupu – ověření| Ano | Ověřování je prostřednictvím Azure Active Directory. |
| Řízení přístupu – ověření| Ano | Správa identit a přístupu (IAM) pro volání prostřednictvím SFRP. Volání přímo na koncový bod clusteru podporuje dvě role: Uživatele a správce. Zákazník může mapování rozhraní API na kteroukoli z těchto rolí. |


## <a name="audit-trail"></a>Záznam pro audit

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Plánování a řízení protokolování a auditování| Ano | Spustit přes procesy pro auditování a jejich schvalování, všechny operace s roviny řízení. |
| Protokolování a auditování RP roviny dat| neuvedeno | Označuje zákazníka vlastnícího clusteru.  |

## <a name="configuration-management"></a>Správa konfigurace

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (verzí konfigurace atd.)| Ano | Konfigurace služby se systémovou správou verzí a nasazených pomocí nasazení Azure. Kód (aplikace a modul runtime) je označené verzí využitím sestavování služby Azure.
 |
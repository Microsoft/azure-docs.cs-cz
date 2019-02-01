---
author: msmbaldwin
ms.service: backup
ms.topic: include
ms.date: 01/31/2019
ms.author: mbaldwin
ms.openlocfilehash: 5a4cc3ea822c5613fc7a50b8e370d6846b683721
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55513509"
---
## <a name="preventative"></a>Preventivní

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování v klidovém stavu:<ul><li>Šifrování na straně serveru</li><li>Šifrování na straně serveru pomocí klíčů spravovaných zákazníkem</li><li>Další funkce šifrování (například klientů, funkce always encrypted, atd.)</ul>| Ano | Pomocí šifrování služby storage pro účty úložiště. |
| Šifrování během přenosu:<ul><li>Express route šifrování</li><li>Ve virtuální síti šifrování</li><li>Šifrování virtuálními sítěmi</ul>| Ne | Pomocí protokolu HTTPS. |
| Šifrovací klíč zpracování (CMK, BYOK, atd.)| Ne |  |
| Šifrování na úrovni sloupce (datových služeb Azure)| Ne |  |
| Šifrované volání rozhraní API| Ano |  |

## <a name="network-segmentation"></a>Segmentace sítě

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Koncový bod služby podpory| Ne |  |
| Vkládání podpory virtuálních sítí| Ne |  |
| Izolace sítě / funkce Podpora brány firewall| Ano | Vynucené tunelování se podporuje pro zálohování virtuálních počítačů. Vynucené tunelování se nepodporuje pro pracovní postupy spouštěné ve virtuálních počítačích. |
| Podpora pro vynucené tunelování | Ne |  |

## <a name="detection"></a>Detection (Detekce)

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora (Log analytics, App insights atd.) pro monitorování Azure| Ano | Log Analytics je podporované prostřednictvím diagnostické protokoly. Naleznete v tématu monitorování Azure Backup chráněných pracovních vytížení používat službu Log Analytics (https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/) Další informace. |

## <a name="iam-support"></a>Podpora IAM

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Řízení přístupu – ověření| Ano | Ověřování je prostřednictvím Azure Active Directory. |
| Řízení přístupu – ověření| Ano | Zákazník byl vytvořen a předdefinované role RBAC se používají. Zobrazit Use Role-Based řízení přístupu ke správě body obnovení Azure Backup (https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault) Další informace. |


## <a name="audit-trail"></a>Záznam pro audit

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Plánování a řízení protokolování a auditování| Ano | Všechny akce aktivuje zákazníků na webu Azure Portal se zaznamenávají do protokolů aktivit. |
| Protokolování a auditování RP roviny dat| Ne | Rovina dat služby Azure Backup je nedostupné přímo.  |

## <a name="configuration-management"></a>Správa konfigurace

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (verzí konfigurace atd.)| Ano|  |
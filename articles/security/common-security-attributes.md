---
title: Atributy zabezpečení pro služby Azure
description: Kontrolní seznam společné atributy zabezpečení za vaše rozhodnutí vyzkoušet Azure Service Fabric
services: security
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: security
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: mbaldwin
ms.openlocfilehash: 2eb480e10ca3b674895d2d22cc44fb52f305f988
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/04/2019
ms.locfileid: "59007512"
---
# <a name="common-security-attributes-for-azure-services"></a>Běžné atributy zabezpečení pro služby Azure

Zabezpečení je integrované do všech oblastí služby Azure. Tento článek shromažďuje společné atributy zabezpečení pro vybrané služby Azure. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]


## [<a name="azure-backup"></a>Azure Backup](../backup/backup-security-attributes.md)

### <a name="preventative"></a>Preventivní

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování v klidovém stavu:<ul><li>Šifrování na straně serveru</li><li>Šifrování na straně serveru pomocí klíčů spravovaných zákazníkem</li><li>Další funkce šifrování (například klientů, funkce always encrypted, atd.)</ul>| Ano | Pomocí šifrování služby storage pro účty úložiště. |
| Šifrování během přenosu:<ul><li>Express route šifrování</li><li>Ve virtuální síti šifrování</li><li>Šifrování virtuálními sítěmi</ul>| Ne | Pomocí protokolu HTTPS. |
| Šifrovací klíč zpracování (CMK, BYOK, atd.)| Ne |  |
| Šifrování na úrovni sloupce (datových služeb Azure)| Ne |  |
| Šifrované volání rozhraní API| Ano |  |

### <a name="network-segmentation"></a>Segmentace sítě

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Koncový bod služby podpory| Ne |  |
| Vkládání podpory virtuálních sítí| Ne |  |
| Izolace sítě / funkce Podpora brány firewall| Ano | Vynucené tunelování se podporuje pro zálohování virtuálních počítačů. Vynucené tunelování se nepodporuje pro pracovní postupy spouštěné ve virtuálních počítačích. |
| Podpora pro vynucené tunelování | Ne |  |

### <a name="detection"></a>Detection (Detekce)

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora (Log analytics, App insights atd.) pro monitorování Azure| Ano | Log Analytics je podporované prostřednictvím diagnostické protokoly. Naleznete v tématu monitorování Azure Backup chráněných pracovních vytížení používat službu Log Analytics (https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/) Další informace. |

### <a name="iam-support"></a>Podpora IAM

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Řízení přístupu – ověření| Ano | Ověřování je prostřednictvím Azure Active Directory. |
| Řízení přístupu – ověření| Ano | Zákazník byl vytvořen a předdefinované role RBAC se používají. Zobrazit Use Role-Based řízení přístupu ke správě Azure Backup bodů obnovení (/ azure / / backup-rbac-rs – trezoru služby backup) pro další informace. |


### <a name="audit-trail"></a>Záznam pro audit

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Plánování a řízení protokolování a auditování| Ano | Všechny akce aktivuje zákazníků na webu Azure Portal se zaznamenávají do protokolů aktivit. |
| Protokolování a auditování RP roviny dat| Ne | Rovina dat služby Azure Backup je nedostupné přímo.  |

### <a name="configuration-management"></a>Správa konfigurace

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (verze konfigurace atd.)| Ano|  |

## [<a name="azure-key-vault"></a>Azure Key Vault](../key-vault/key-vault-security-attributes.md)

### <a name="preventative"></a>Preventivní

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování v klidovém stavu:<ul><li>Šifrování na straně serveru</li><li>Šifrování na straně serveru pomocí klíčů spravovaných zákazníkem</li><li>Další funkce šifrování (například klientů, funkce always encrypted, atd.)</ul>| Ano | Všechny objekty jsou šifrovaná. |
| Šifrování během přenosu:<ul><li>Express route šifrování</li><li>Ve virtuální síti šifrování</li><li>Šifrování virtuálními sítěmi</ul>| Ano | Veškerá komunikace probíhá přes šifrované volání rozhraní API |
| Šifrovací klíč zpracování (CMK, BYOK, atd.)| Ano | Zákazník řídí všechny klíče ve své službě Key Vault. Pokud jsou zadány klíče modulu (HSM) zajišťuje hardwarové zabezpečení, HSM FIPS úroveň 2 chrání klíče, certifikát nebo tajný klíč. |
| Šifrování na úrovni sloupce (datových služeb Azure)| neuvedeno |  |
| Šifrované volání rozhraní API| Ano | Pomocí protokolu HTTPS. |

### <a name="network-segmentation"></a>Segmentace sítě

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Koncový bod služby podpory| Ano | Pomocí koncových bodů služby Virtual Network (Vnet). |
| Vkládání podpory virtuálních sítí| Ne |  |
| Izolace sítě / funkce Podpora brány firewall| Ano | Pomocí pravidel brány firewall virtuální sítě. |
| Podpora pro vynucené tunelování | Ne |  |

### <a name="detection"></a>Detection (Detekce)

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora (Log analytics, App insights atd.) pro monitorování Azure| Ano | Používat službu Log Analytics. |

### <a name="iam-support"></a>Podpora IAM

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Řízení přístupu – ověření| Ano | Ověřování je prostřednictvím Azure Active Directory. |
| Řízení přístupu – ověření| Ano | Pomocí zásady přístupu trezoru klíčů. |


### <a name="audit-trail"></a>Záznam pro audit

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Řízení a rovinou protokolování a auditování| Ano | Používat službu Log Analytics. |
| Protokolování a auditování RP roviny dat| Ano | Používat službu Log Analytics. |

### <a name="access-controls"></a>Ovládací prvky přístupu

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Řízení přístupu roviny řízení a správy | Ano | Řízení přístupu podle role (RBAC) služby Azure Resource Manager |
| Řízení přístupu roviny dat (na všech úrovních služby) | Ano | Zásady přístupu trezoru klíčů |

## [<a name="azure-service-fabric"></a>Azure Service Fabric](../service-fabric/service-fabric-security-attributes.md)

### <a name="preventative"></a>Preventivní

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování v klidovém stavu:<ul><li>Šifrování na straně serveru</li><li>Šifrování na straně serveru pomocí klíčů spravovaných zákazníkem</li><li>Další funkce šifrování (například klientů, funkce always encrypted, atd.)</ul>| Ano | Zákazník je vlastníkem služby clusteru a škálovací sady virtuálních počítačů (VM), které clusteru je postavená na. Azure disk encryption je možné povolit na škálovací sadu virtuálních počítačů. |
| Šifrování během přenosu:<ul><li>Express route šifrování</li><li>Ve virtuální síti šifrování</li><li>Šifrování virtuálními sítěmi</ul>| Ano |  |
| Šifrovací klíč zpracování (CMK, BYOK, atd.)| Ano | Zákazník je vlastníkem služby clusteru a škálovací sady virtuálních počítačů (VM), které clusteru je postavená na. Azure disk encryption je možné povolit na škálovací sadu virtuálních počítačů. |
| Šifrování na úrovni sloupce (datových služeb Azure)| neuvedeno |  |
| Šifrované volání rozhraní API| Ano | Volání rozhraní API Service Fabric provádí prostřednictvím Azure Resource Manageru. Vyžaduje se platný webový token JSON (JWT). |

### <a name="network-segmentation"></a>Segmentace sítě

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Koncový bod služby podpory| Ano |  |
| Vkládání podpory virtuálních sítí| Ano |  |
| Izolace sítě / funkce Podpora brány firewall| Ano | Použití skupin zabezpečení sítě (NSG). |
| Podpora pro vynucené tunelování | Ano | Sítě Azure poskytují vynucené tunelování. |

### <a name="detection"></a>Detection (Detekce)

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora (Log analytics, App insights atd.) pro monitorování Azure| Ano | Pomocí Azure monitoring podporu a podporu třetí strany. |

### <a name="iam-support"></a>Podpora IAM

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Řízení přístupu – ověření| Ano | Ověřování je prostřednictvím Azure Active Directory. |
| Řízení přístupu – ověření| Ano | Správa identit a přístupu (IAM) pro volání prostřednictvím SFRP. Volání přímo na koncový bod clusteru podporuje dvě role: Uživatele a správce. Zákazník může mapování rozhraní API na kteroukoli z těchto rolí. |


### <a name="audit-trail"></a>Záznam pro audit

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Plánování a řízení protokolování a auditování| Ano | Spustit přes procesy pro auditování a jejich schvalování, všechny operace s roviny řízení. |
| Protokolování a auditování RP roviny dat| neuvedeno | Označuje zákazníka vlastnícího clusteru.  |

### <a name="configuration-management"></a>Správa konfigurace

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (verze konfigurace atd.)| Ano | Konfigurace služby se systémovou správou verzí a nasazených pomocí nasazení Azure. Kód (aplikace a modul runtime) je označené verzí využitím sestavování služby Azure.
 |

## <a name="azure-storage"></a>Azure Storage

### <a name="preventative"></a>Preventivní

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování v klidovém stavu:<ul><li>Šifrování na straně serveru</li><li>Šifrování na straně serveru pomocí klíčů spravovaných zákazníkem</li><li>Další funkce šifrování (například klientů, funkce always encrypted, atd.)</ul>| Ano |  |
| Šifrování během přenosu:<ul><li>Express route šifrování</li><li>Ve virtuální síti šifrování</li><li>Šifrování virtuálními sítěmi</ul>| Ano | Podpora standardních mechanismů protokolu HTTPS/TLS.  Uživatelé můžou taky k šifrování dat před odesláním do služby. |
| Šifrovací klíč zpracování (CMK, BYOK, atd.)| Ano | Zobrazit [šifrování služby Storage pomocí klíčů spravovaných zákazníkem ve službě Azure Key Vault](../storage/common/storage-service-encryption-customer-managed-keys.md).|
| Šifrování na úrovni sloupce (datových služeb Azure)| neuvedeno |  |
| Šifrované volání rozhraní API| Ano |  |

### <a name="network-segmentation"></a>Segmentace sítě

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Koncový bod služby podpory| Ano |  |
| Vkládání podpory virtuálních sítí| neuvedeno |  |
| Izolace sítě / funkce Podpora brány firewall| Ano | |
| Podpora pro vynucené tunelování | neuvedeno |  |

### <a name="detection"></a>Detection (Detekce)

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora (Log analytics, App insights atd.) pro monitorování Azure| Ano | Metriky Azure Monitor dostupné nyní, protokoly počáteční verzi preview |

### <a name="iam-support"></a>Podpora IAM

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Řízení přístupu – ověření| Ano | Azure Active Directory, sdílený klíč, sdílený přístupový token. |
| Řízení přístupu – ověření| Ano | Podporu ověřování přes RBAC, seznamech ACL POSIX a tokeny SAS |


### <a name="audit-trail"></a>Záznam pro audit

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Plánování a řízení protokolování a auditování | Ano | Protokol aktivit v Azure Resource Manageru |
| Protokolování a auditování RP roviny dat| Ano | Diagnostické protokoly služby a Azure Monitor protokolování počáteční verzi preview  |

### <a name="configuration-management"></a>Správa konfigurace

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (verze konfigurace atd.)| Ano | Podpora správy verzí zprostředkovatele prostředků prostřednictvím rozhraní API Azure Resource Manageru |
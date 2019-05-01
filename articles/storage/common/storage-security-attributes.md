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
ms.openlocfilehash: 7868b52fee991d4b9323fa0b7969aeca4dc83cdb
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64711950"
---
# <a name="common-security-attributes-for-azure-storage"></a>Běžné atributy zabezpečení pro službu Azure Storage

Zabezpečení je integrované do všech oblastí služby Azure. Tento článek popisuje běžné atributy zabezpečení integrované do služby Azure Storage. 

[!INCLUDE [Security Attributes Header](../../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Preventivní

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování v klidovém stavu:<ul><li>Šifrování na straně serveru</li><li>Šifrování na straně serveru pomocí klíčů spravovaných zákazníkem</li><li>Další funkce šifrování (například klientů, funkce always encrypted, atd.)</ul>| Ano |  |
| Šifrování během přenosu:<ul><li>Express route šifrování</li><li>Ve virtuální síti šifrování</li><li>Šifrování virtuálními sítěmi</ul>| Ano | Podpora standardních mechanismů protokolu HTTPS/TLS.  Uživatelé můžou taky k šifrování dat před odesláním do služby. |
| Šifrování klíče zpracování (CMK, BYOK, atd.)| Ano | Zobrazit [šifrování služby Storage pomocí klíčů spravovaných zákazníkem ve službě Azure Key Vault](storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).|
| Šifrování na úrovni sloupce (Azure Data Services)| neuvedeno |  |
| Šifrované volání rozhraní API| Ano |  |

## <a name="network-segmentation"></a>Segmentace sítě

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Ano |  |
| Vkládání podpory virtuálních sítí| neuvedeno |  |
| Izolace sítě a podporu funkce brány firewall| Ano | |
| Vynucené tunelování podpory| neuvedeno |  |

## <a name="detection"></a>Detection (Detekce)

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora (Log analytics, App insights atd.) pro monitorování Azure| Ano | Metriky Azure Monitor dostupné nyní, protokoly počáteční verzi preview |

## <a name="identity-and-access-management"></a>Správa identit a přístupu

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Authentication| Ano | Azure Active Directory, sdílený klíč, sdílený přístupový token. |
| Autorizace| Ano | Podporu ověřování přes RBAC, seznamech ACL POSIX a tokeny SAS |


## <a name="audit-trail"></a>Záznam pro audit

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ovládací prvek a správu roviny protokolování a auditování | Ano | Protokol aktivit v Azure Resource Manageru |
| Protokolování roviny dat a auditu| Ano | Diagnostické protokoly služby a Azure Monitor protokolování počáteční verzi preview  |

## <a name="configuration-management"></a>Správa konfigurace

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (verze konfigurace atd.)| Ano | Podpora správy verzí zprostředkovatele prostředků prostřednictvím rozhraní API Azure Resource Manageru |
---
title: Atributy zabezpečení pro Azure Storage
description: Kontrolní seznam atributů zabezpečení pro vyhodnocení Azure Storage
services: storage
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: storage
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 16ec2757955b53a8bfa73ba724100f7fa61d2867
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444306"
---
# <a name="security-attributes-for-azure-storage"></a>Atributy zabezpečení pro Azure Storage

Tento článek popisuje atributy zabezpečení integrované do Azure Storage. 

[!INCLUDE [Security Attributes Header](../../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Preventivní

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování v klidovém formátu (například šifrování na straně serveru, šifrování na straně serveru pomocí klíčů spravovaných zákazníkem a další funkce šifrování)| Ano |  |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě)| Ano | Podporuje standardní mechanismy protokolu HTTPS/TLS.  Uživatelé mohou také šifrovat data před jejich přenosem do služby. |
| Zpracování šifrovacích klíčů (CMK, BYOK atd.)| Ano | Viz [šifrování služby Storage použití klíčů spravovaných zákazníkem v Azure Key Vault](storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).|
| Šifrování na úrovni sloupce (Azure Data Services)| Není k dispozici |  |
| Zašifrovaná volání rozhraní API| Ano |  |

## <a name="network-segmentation"></a>Segmentace sítě

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Ano |  |
| Podpora vkládání virtuální sítě| Není k dispozici |  |
| Izolace sítě a podpora brány firewall| Ano | |
| Podpora vynuceného tunelování| Není k dispozici |  |

## <a name="detection"></a>Detekce

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora monitorování Azure (Log Analytics, App Insights atd.)| Ano | Azure Monitor metriky, které jsou teď dostupné, se zaprotokolují verze Preview. |

## <a name="identity-and-access-management"></a>Správa identit a přístupu

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ověřování| Ano | Azure Active Directory, sdílený klíč, sdílený přístupový token. |
| Authorization| Ano | Podpora autorizace přes RBAC, seznamy ACL v POSIX a tokeny SAS |


## <a name="audit-trail"></a>Záznam pro audit

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Protokolování a audit roviny řízení a správy | Ano | Protokol aktivit Azure Resource Manager |
| Protokolování a audit roviny dat| Ano | Protokoly diagnostiky služby a Azure Monitor protokolování počáteční verze Preview  |

## <a name="configuration-management"></a>Správa konfigurace

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (Správa verzí konfigurace atd.)| Ano | Podpora správy verzí poskytovatele prostředků prostřednictvím rozhraní API Azure Resource Manager |
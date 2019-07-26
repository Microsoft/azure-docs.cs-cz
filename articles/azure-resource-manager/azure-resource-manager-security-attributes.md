---
title: Atributy zabezpečení pro Azure Resource Manager
description: Kontrolní seznam atributů zabezpečení pro vyhodnocení Azure Resource Manager
services: azure-resource-manager
author: msmbaldwin
manager: barbkess
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: mbaldwin
ms.openlocfilehash: e3bfb79c54ff57adfa947f2dd0100f6c05c7af9f
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444157"
---
# <a name="security-attributes-for-azure-resource-manager"></a>Atributy zabezpečení pro Azure Resource Manager

Tento článek popisuje atributy zabezpečení integrované do Azure Resource Manager.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Preventivní

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování v klidovém formátu (například šifrování na straně serveru, šifrování na straně serveru pomocí klíčů spravovaných zákazníkem a další funkce šifrování)| Ano |  |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě)| Ano | HTTPS/TLS. |
| Zpracování šifrovacích klíčů (CMK, BYOK atd.)| Není k dispozici | Azure Resource Manager neukládá žádný zákaznický obsah, řídí se jenom data. |
| Šifrování na úrovni sloupce (Azure Data Services)| Ano | |
| Zašifrovaná volání rozhraní API| Ano | |

## <a name="network-segmentation"></a>Segmentace sítě

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Ne | |
| Podpora vkládání virtuální sítě| Ano | |
| Izolace sítě a podpora brány firewall| Ne |  |
| Podpora vynuceného tunelování| Ne |  |

## <a name="detection"></a>Detekce

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora monitorování Azure (Log Analytics, App Insights atd.)| Ne | |

## <a name="identity-and-access-management"></a>Správa identit a přístupu

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ověřování| Ano | Na základě [Azure Active Directory](/azure/active-directory) .|
| Authorization| Ano | |


## <a name="audit-trail"></a>Záznam pro audit

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Protokolování a audit roviny řízení a správy| Ano | Protokoly aktivit zpřístupňují všechny operace zápisu (PUT, POST, DELETE) provedené na vašich prostředcích. v tématu [zobrazení protokolů aktivit můžete auditovat akce u prostředků](resource-group-audit.md). |
| Protokolování a audit roviny dat| Není k dispozici | |

## <a name="configuration-management"></a>Správa konfigurace

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (Správa verzí konfigurace atd.)| Ano |  |

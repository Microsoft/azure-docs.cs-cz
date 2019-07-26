---
title: Atributy zabezpečení pro Azure Key Vault
description: Kontrolní seznam atributů zabezpečení pro vyhodnocení Azure Key Vault
services: key-vault
author: msmbaldwin
manager: barbkess
ms.service: key-vault
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 836d025c5bc69da9606c9a6172ac6a43caaaf29b
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444022"
---
# <a name="security-attributes-for-azure-key-vault"></a>Atributy zabezpečení pro Azure Key Vault

Tento článek popisuje atributy zabezpečení integrované do Azure Key Vault. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Preventivní

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování v klidovém formátu (například šifrování na straně serveru, šifrování na straně serveru pomocí klíčů spravovaných zákazníkem a další funkce šifrování)| Ano | Všechny objekty jsou zašifrovány. |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě)| Ano | Veškerá komunikace probíhá prostřednictvím šifrovaných volání rozhraní API. |
| Zpracování šifrovacích klíčů (CMK, BYOK atd.)| Ano | Zákazník řídí všechny klíče v jejich Key Vault. Když jsou zadané klíče v modulu hardwarového zabezpečení (HSM), chrání klíč, certifikát nebo tajný kód úrovně FIPS úrovně 2. |
| Šifrování na úrovni sloupce (Azure Data Services)| Není k dispozici |  |
| Zašifrovaná volání rozhraní API| Ano | Pomocí protokolu HTTPS. |

## <a name="network-segmentation"></a>Segmentace sítě

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Ano | Použití koncových bodů služby Virtual Network (VNet). |
| Podpora vkládání virtuální sítě| Ne |  |
| Izolace sítě a podpora brány firewall| Ano | Používají se pravidla brány firewall virtuální sítě. |
| Podpora vynuceného tunelování| Ne |  |

## <a name="detection"></a>Detekce

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora monitorování Azure (Log Analytics, App Insights atd.)| Ano | Použití Log Analytics. |

## <a name="identity-and-access-management"></a>Správa identit a přístupu

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ověřování| Ano | Ověřování probíhá prostřednictvím Azure Active Directory. |
| Authorization| Ano | Používají se zásady přístupu Key Vault. |


## <a name="audit-trail"></a>Záznam pro audit

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Protokolování a audit roviny řízení a správy| Ano | Použití Log Analytics. |
| Protokolování a audit roviny dat| Ano | Použití Log Analytics. |

## <a name="access-controls"></a>Ovládací prvky přístupu

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ovládací prvky pro řízení přístupu roviny řízení/správy | Ano | Řízení přístupu podle role (RBAC) služby Azure Resource Manager |
| Řízení přístupu roviny dat (na každé úrovni služby) | Ano | Zásada přístupu Key Vault |
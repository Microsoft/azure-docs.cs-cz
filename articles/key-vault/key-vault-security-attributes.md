---
title: Běžné atributy zabezpečení pro Azure Key Vault
description: Kontrolní seznam společné atributy zabezpečení za vaše rozhodnutí vyzkoušet Azure Key Vault
services: key-vault
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: key-vault
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 3ccfc38136ba3e8ec7c6130658032b7565988e5c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60461399"
---
# <a name="common-security-attributes-for-azure-key-vault"></a>Běžné atributy zabezpečení pro Azure Key Vault

Zabezpečení je integrované do všech oblastí služby Azure. Tento článek popisuje běžné atributy zabezpečení integrované do Azure Key Vault. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Preventivní

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování v klidovém stavu:<ul><li>Šifrování na straně serveru</li><li>Šifrování na straně serveru pomocí klíčů spravovaných zákazníkem</li><li>Další funkce šifrování (například klientů, funkce always encrypted, atd.)</ul>| Ano | Všechny objekty jsou šifrovaná. |
| Šifrování během přenosu:<ul><li>Express route šifrování</li><li>Ve virtuální síti šifrování</li><li>Šifrování virtuálními sítěmi</ul>| Ano | Veškerá komunikace probíhá přes šifrované volání rozhraní API |
| Šifrování klíče zpracování (CMK, BYOK, atd.)| Ano | Zákazník řídí všechny klíče ve své službě Key Vault. Pokud jsou zadány klíče modulu (HSM) zajišťuje hardwarové zabezpečení, HSM FIPS úroveň 2 chrání klíče, certifikát nebo tajný klíč. |
| Šifrování na úrovni sloupce (Azure Data Services)| neuvedeno |  |
| Šifrované volání rozhraní API| Ano | Pomocí protokolu HTTPS. |

## <a name="network-segmentation"></a>Segmentace sítě

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Ano | Pomocí koncových bodů služby Virtual Network (Vnet). |
| vkládání podpory virtuálních sítí| Ne |  |
| Izolace sítě a podporu funkce brány firewall| Ano | Pomocí pravidel brány firewall virtuální sítě. |
| Vynucené tunelování podpory| Ne |  |

## <a name="detection"></a>Detection (Detekce)

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora (Log analytics, App insights atd.) pro monitorování Azure| Ano | Používat službu Log Analytics. |

## <a name="identity-and-access-management"></a>Správa identit a přístupu

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Authentication| Ano | Ověřování je prostřednictvím Azure Active Directory. |
| Autorizace| Ano | Pomocí zásady přístupu trezoru klíčů. |


## <a name="audit-trail"></a>Záznam pro audit

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Řízení a rovinou protokolování a auditování| Ano | Používat službu Log Analytics. |
| Protokolování roviny dat a auditu| Ano | Používat službu Log Analytics. |

## <a name="access-controls"></a>Ovládací prvky přístupu

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Řízení přístupu roviny řízení a správy | Ano | Řízení přístupu podle role (RBAC) služby Azure Resource Manager |
| Řízení přístupu roviny dat (na všech úrovních služby) | Ano | Zásady přístupu trezoru klíčů |
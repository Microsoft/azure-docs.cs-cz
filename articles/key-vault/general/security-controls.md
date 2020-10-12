---
title: Ovládací prvky zabezpečení pro Azure Key Vault
description: Kontrolní seznam ovládacích prvků zabezpečení pro vyhodnocení Azure Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: cd6602f68b63e2c236e7f3905d33b88fbda36ed2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "81429860"
---
# <a name="security-controls-for-azure-key-vault"></a>Ovládací prvky zabezpečení pro Azure Key Vault

Tento článek popisuje ovládací prvky zabezpečení integrované do Azure Key Vault. 

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="network"></a>Síť

| Řízení zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Ano | Použití koncových bodů služby Virtual Network (VNet). |
| Podpora vkládání virtuální sítě| No |  |
| Izolace sítě a podpora brány firewall| Ano | Používají se pravidla brány firewall virtuální sítě. |
| Podpora vynuceného tunelování| No |  |

## <a name="monitoring--logging"></a>Monitorování protokolování &

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora monitorování Azure (Log Analytics, App Insights atd.)| Ano | Použití Log Analytics. |
| Protokolování a audit roviny řízení a správy| Ano | Použití Log Analytics. |
| Protokolování a audit roviny dat| Ano | Použití Log Analytics. |

## <a name="identity"></a>Identita

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Authentication| Ano | Ověřování probíhá prostřednictvím Azure Active Directory. |
| Autorizace| Ano | Používají se zásady přístupu Key Vault. |

## <a name="data-protection"></a>Ochrana dat

| Řízení zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování na straně serveru v klidovém umístění: klíče spravované společností Microsoft | Ano | Všechny objekty jsou zašifrovány. |
| Šifrování na straně serveru v klidovém umístění: klíče spravované zákazníkem (BYOK) | Ano | Zákazník řídí všechny klíče v jejich Key Vault. Když jsou zadané klíče v modulu hardwarového zabezpečení (HSM), chrání klíč, certifikát nebo tajný kód úrovně FIPS úrovně 2. |
| Šifrování na úrovni sloupce (Azure Data Services)| Není k dispozici |  |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování VNet-VNet)| Ano | Veškerá komunikace probíhá prostřednictvím šifrovaných volání rozhraní API. |
| Zašifrovaná volání rozhraní API| Ano | Pomocí protokolu HTTPS. |

## <a name="access-controls"></a>Řízení přístupu

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ovládací prvky pro řízení přístupu roviny řízení/správy | Ano | Řízení přístupu podle role (RBAC) služby Azure Resource Manager |
| Řízení přístupu roviny dat (na každé úrovni služby) | Ano | Zásada přístupu Key Vault |

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [integrovaných kontrolních prvcích zabezpečení napříč službami Azure](../../security/fundamentals/security-controls.md).
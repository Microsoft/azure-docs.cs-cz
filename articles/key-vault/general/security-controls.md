---
title: Ovládací prvky zabezpečení pro Azure Key Vault
description: Kontrolní seznam ovládacích prvků zabezpečení pro vyhodnocení úložiště klíčů Azure
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: cd6602f68b63e2c236e7f3905d33b88fbda36ed2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429860"
---
# <a name="security-controls-for-azure-key-vault"></a>Ovládací prvky zabezpečení pro Azure Key Vault

Tento článek dokumentuje ovládací prvky zabezpečení integrované do úložiště klíčů Azure. 

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="network"></a>Síť

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Ano | Použití koncových bodů služby virtuální sítě (VNet). |
| Podpora vstřikování virtuální sítě| Ne |  |
| Podpora izolace sítě a brány firewall| Ano | Použití pravidel brány firewall virtuální sítě. |
| Podpora vynuceného tunelování| Ne |  |

## <a name="monitoring--logging"></a>Sledování & protokolování

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora monitorování Azure (analýza protokolů, přehledy aplikací atd.)| Ano | Pomocí analýzy protokolů. |
| Řídicí/správní rovina protokolování a audit| Ano | Pomocí analýzy protokolů. |
| Protokolování a audit roviny dat| Ano | Pomocí analýzy protokolů. |

## <a name="identity"></a>Identita

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Authentication| Ano | Ověřování probíhá prostřednictvím služby Azure Active Directory. |
| Autorizace| Ano | Použití zásad přístupu trezoru klíčů. |

## <a name="data-protection"></a>Ochrana dat

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování na straně serveru v klidovém stavu: Klíče spravované společností Microsoft | Ano | Všechny objekty jsou zašifrovány. |
| Šifrování na straně serveru v klidovém stavu: klíče spravované zákazníkem (BYOK) | Ano | Zákazník řídí všechny klíče v trezoru klíčů. Pokud jsou zadány klíče podporované modulem hardwarového zabezpečení (HSM), modul hardwarového zabezpečení úrovně 2 chrání klíč, certifikát nebo tajný klíč. |
| Šifrování na úrovni sloupců (Azure Data Services)| – |  |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě)| Ano | Veškerá komunikace probíhá prostřednictvím šifrovaných volání API |
| Zašifrovaná volání rozhraní API| Ano | Pomocí protokolu HTTPS. |

## <a name="access-controls"></a>Řízení přístupu

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ovládací prvky přístupu k rovině řízení/správy | Ano | Řízení přístupu podle role (RBAC) služby Azure Resource Manager |
| Ovládací prvky přístupu k rovině dat (na každé úrovni služby) | Ano | Zásady přístupu k trezoru klíčů |

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [integrovaných ovládacích prvcích zabezpečení napříč službami Azure](../../security/fundamentals/security-controls.md).
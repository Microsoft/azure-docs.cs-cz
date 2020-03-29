---
title: Ovládací prvky zabezpečení pro Centra událostí Azure
description: Tento článek obsahuje kontrolní seznam ovládacích prvků zabezpečení pro vyhodnocení Azure Event Hubs (síť, identita, ochrana dat atd.).
services: event-hubs
ms.service: event-hubs
author: spelluru
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: spelluru
ms.openlocfilehash: 0769e88eb72b5b347dd9ebf4b1634501ca54098e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76309502"
---
# <a name="security-controls-for-azure-event-hubs"></a>Ovládací prvky zabezpečení pro Centra událostí Azure

Tento článek dokumentuje ovládací prvky zabezpečení integrované do Azure Event Hubs.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Network (Síť)

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky | Dokumentace |
|---|---|--|--|
| Podpora koncového bodu služby| Ano |  |  |
| Podpora vstřikování virtuální sítě| Ne | |  |
| Podpora izolace sítě a brány firewall| Ano |  |  |
| Podpora vynuceného tunelování| Ne |  |  |

## <a name="monitoring--logging"></a>Sledování & protokolování

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky| Dokumentace |
|---|---|--|--|
| Podpora monitorování Azure (analýza protokolů, přehledy aplikací atd.)| Ano | |  |
| Protokolování a audit roviny řízení a správy| Ano |  |  |
| Protokolování a audit roviny dat| Ano |   |  |

## <a name="identity"></a>Identita

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky| Dokumentace |
|---|---|--|--|
| Ověřování| Ano | | [Autorizace přístupu k Centru událostí Azure](authorize-access-event-hubs.md), [autorizace přístupu k prostředkům Centra událostí pomocí Služby Azure Active Directory](authorize-access-azure-active-directory.md), [autorizace přístupu k prostředkům Centra událostí pomocí sdílených přístupových podpisů](authorize-access-shared-access-signature.md) |
| Autorizace|  Ano | | [Ověření spravované identity pomocí služby Azure Active Directory pro přístup k prostředkům centra událostí](authenticate-managed-identity.md), [Ověření aplikace pomocí služby Azure Active Directory pro přístup k prostředkům centra událostí](authenticate-application.md), Ověření [přístupu k prostředkům centra událostí pomocí sdílených přístupových podpisů (SAS)](authenticate-shared-access-signature.md) |

## <a name="data-protection"></a>Ochrana dat

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky | Dokumentace |
|---|---|--|--|
| Šifrování na straně serveru v klidovém stavu: Klíče spravované společností Microsoft |  Ano | |  |
| Šifrování na straně serveru v klidovém stavu: klíče spravované zákazníkem (BYOK) | Ano. K dispozici pro vyhrazené clustery. | Klíč spravovaný zákazníkem v Azure KeyVault lze použít k šifrování dat v centru událostí v klidovém stavu. | [Konfigurace klíčů spravovaných zákazníkem pro šifrování dat služby Azure Event Hubs v klidovém stavu pomocí portálu Azure](configure-customer-managed-key.md) |
| Šifrování na úrovni sloupců (Azure Data Services)| Není dostupné. | |  |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě)| Ano | |  |
| Zašifrovaná volání rozhraní API| Ano |  |  |

## <a name="configuration-management"></a>Správa konfigurace

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky| Dokumentace |
|---|---|--|--|
| Podpora správy konfigurace (správa verzí konfigurace atd.)| Ano | |  |

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [integrovaných ovládacích prvcích zabezpečení napříč službami Azure](../security/fundamentals/security-controls.md).

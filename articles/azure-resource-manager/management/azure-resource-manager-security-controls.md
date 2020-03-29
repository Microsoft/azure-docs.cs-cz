---
title: Ovládací prvky zabezpečení
description: Kontrolní seznam integrovaných ovládacích prvků zabezpečení pro vyhodnocení služby Azure Resource Manager.
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: d0a0625153e428a0d261e52d40b31ef5142eddfd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75485621"
---
# <a name="security-controls-for-azure-resource-manager"></a>Ovládací prvky zabezpečení pro Správce prostředků Azure

Tento článek dokumentuje ovládací prvky zabezpečení integrované do Správce prostředků Azure.

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>Ochrana dat

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování na straně serveru v klidovém stavu: Klíče spravované společností Microsoft | Ano |  |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě)| Ano | HTTPS/TLS. |
| Šifrování na straně serveru v klidovém stavu: klíče spravované zákazníkem (BYOK) | Není dostupné. | Azure Resource Manager ukládá žádný zákaznický obsah, pouze data řízení. |
| Šifrování na úrovni sloupců (Azure Data Services)| Ano | |
| Zašifrovaná volání rozhraní API| Ano | |

## <a name="network"></a>Network (Síť)

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Ne | |
| Podpora vstřikování virtuální sítě| Ano | |
| Podpora izolace sítě a brány firewall| Ne |  |
| Podpora vynuceného tunelování| Ne |  |

## <a name="monitoring--logging"></a>Sledování & protokolování

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora monitorování Azure (analýza protokolů, přehledy aplikací atd.)| Ne | |
| Protokolování a audit roviny řízení a správy| Ano | Protokoly aktivit vystavit všechny operace zápisu (PUT, POST, DELETE) provádí na vašich prostředků; Viz [Zobrazení protokolů aktivit k auditování akcí na zdrojích](view-activity-logs.md). |
| Protokolování a audit roviny dat| Není dostupné. | |

## <a name="identity"></a>Identita

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ověřování| Ano | Na základě [služby Azure Active Directory.](/azure/active-directory)|
| Autorizace| Ano | |

## <a name="configuration-management"></a>Správa konfigurace

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (správa verzí konfigurace atd.)| Ano |  |

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [integrovaných ovládacích prvcích zabezpečení napříč službami Azure](../../security/fundamentals/security-controls.md).
---
title: Ovládací prvky zabezpečení
description: Kontrolní seznam předdefinovaných ovládacích prvků zabezpečení pro vyhodnocení Azure Resource Manager služby.
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: bb8742c38fae88dc1fd1fd1ec175b248f30df3a0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86054453"
---
# <a name="security-controls-for-azure-resource-manager"></a>Ovládací prvky zabezpečení pro Azure Resource Manager

Tento článek popisuje ovládací prvky zabezpečení integrované do Azure Resource Manager.

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>Ochrana dat

| Řízení zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování na straně serveru v klidovém umístění: klíče spravované společností Microsoft | Yes |  |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování VNet-VNet)| Yes | HTTPS/TLS. |
| Šifrování na straně serveru v klidovém umístění: klíče spravované zákazníkem (BYOK) | Není k dispozici | Azure Resource Manager neukládá žádný zákaznický obsah, řídí se jenom data. |
| Šifrování na úrovni sloupce (Azure Data Services)| Yes | |
| Zašifrovaná volání rozhraní API| Yes | |

## <a name="network"></a>Síť

| Řízení zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| No | |
| Podpora vkládání virtuální sítě| Yes | |
| Izolace sítě a podpora brány firewall| No |  |
| Podpora vynuceného tunelování| No |  |

## <a name="monitoring--logging"></a>Monitorování protokolování &

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora monitorování Azure (Log Analytics, App Insights atd.)| No | |
| Protokolování a audit roviny řízení a správy| Yes | Protokoly aktivit zpřístupňují všechny operace zápisu (PUT, POST, DELETE) provedené na vašich prostředcích. v tématu [zobrazení protokolů aktivit můžete auditovat akce u prostředků](view-activity-logs.md). |
| Protokolování a audit roviny dat| Není k dispozici | |

## <a name="identity"></a>Identita

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Authentication| Yes | Na základě [Azure Active Directory](../../active-directory/index.yml) .|
| Autorizace| Yes | |

## <a name="configuration-management"></a>Správa konfigurace

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (Správa verzí konfigurace atd.)| Ano |  |

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [integrovaných kontrolních prvcích zabezpečení napříč službami Azure](../../security/fundamentals/security-controls.md).

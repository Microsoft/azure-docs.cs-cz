---
title: Ovládací prvky zabezpečení
description: Kontrolní seznam předdefinovaných ovládacích prvků zabezpečení pro vyhodnocení Azure Resource Manager služby.
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: d0a0625153e428a0d261e52d40b31ef5142eddfd
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75485621"
---
# <a name="security-controls-for-azure-resource-manager"></a>Ovládací prvky zabezpečení pro Azure Resource Manager

Tento článek popisuje ovládací prvky zabezpečení integrované do Azure Resource Manager.

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>Ochrana dat

| Řízení zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování na straně serveru v klidovém umístění: klíče spravované společností Microsoft | Ano |  |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě)| Ano | HTTPS/TLS. |
| Šifrování na straně serveru v klidovém umístění: klíče spravované zákazníkem (BYOK) | Nevztahuje se | Azure Resource Manager neukládá žádný zákaznický obsah, řídí se jenom data. |
| Šifrování na úrovni sloupce (Azure Data Services)| Ano | |
| Zašifrovaná volání rozhraní API| Ano | |

## <a name="network"></a>Network (Síť)

| Řízení zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Ne | |
| Podpora vkládání virtuální sítě| Ano | |
| Izolace sítě a podpora brány firewall| Ne |  |
| Podpora vynuceného tunelování| Ne |  |

## <a name="monitoring--logging"></a>Monitorování protokolování &

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora monitorování Azure (Log Analytics, App Insights atd.)| Ne | |
| Protokolování a audit roviny řízení a správy| Ano | Protokoly aktivit zpřístupňují všechny operace zápisu (PUT, POST, DELETE) provedené na vašich prostředcích. v tématu [zobrazení protokolů aktivit můžete auditovat akce u prostředků](view-activity-logs.md). |
| Protokolování a audit roviny dat| Nevztahuje se | |

## <a name="identity"></a>Identita

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ověření| Ano | Na základě [Azure Active Directory](/azure/active-directory) .|
| Autorizace| Ano | |

## <a name="configuration-management"></a>Správa konfigurace

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (Správa verzí konfigurace atd.)| Ano |  |

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [integrovaných kontrolních prvcích zabezpečení napříč službami Azure](../../security/fundamentals/security-controls.md).
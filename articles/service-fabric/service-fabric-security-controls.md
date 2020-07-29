---
title: Řízení zabezpečení pro Azure Service Fabric
description: Přečtěte si o ovládacích prvcích zabezpečení pro Azure Service Fabric. Obsahuje kontrolní seznam předdefinovaných ovládacích prvků zabezpečení.
author: msmbaldwin
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: a8bb49e20ec5812a4882966c6918cf2bd59f36a0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "75645425"
---
# <a name="security-controls-for-azure-service-fabric"></a>Řízení zabezpečení pro Azure Service Fabric

Tento článek popisuje ovládací prvky zabezpečení integrované do Azure Service Fabric. 

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Síť

| Řízení zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Yes |  |
| Podpora vkládání virtuální sítě| Yes |  |
| Izolace sítě a podpora brány firewall| Yes | Používání skupin zabezpečení sítě (NSG). |
| Podpora vynuceného tunelování| Yes | Sítě Azure poskytují vynucené tunelové propojení. |

## <a name="monitoring--logging"></a>Monitorování protokolování &

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora monitorování Azure (Log Analytics, App Insights atd.)| Yes | Používání Azure Monitoring support a podpory třetích stran. |
| Protokolování a audit roviny řízení a správy| Yes | Všechny operace roviny ovládacího prvku se spouštějí prostřednictvím procesů pro auditování a schválení. |
| Protokolování a audit roviny dat| Není k dispozici | Zákazník vlastní cluster.  |

## <a name="identity"></a>Identita

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Authentication| Yes | Ověřování probíhá prostřednictvím Azure Active Directory. |
| Autorizace| Yes | Správa identit a přístupu (IAM) pro volání prostřednictvím SFRP. Volání přímo do koncového bodu clusteru podporují dvě role: uživatel a správce. Zákazník může namapovat rozhraní API na jednu roli. |

## <a name="data-protection"></a>Ochrana dat

| Řízení zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování na straně serveru v klidovém umístění: klíče spravované společností Microsoft | Yes | Zákazník vlastní cluster a sadu škálování virtuálního počítače, na kterých je cluster sestaven. V sadě škálování virtuálního počítače můžete povolit službu Azure Disk Encryption. |
| Šifrování na straně serveru v klidovém umístění: klíče spravované zákazníkem (BYOK) | Yes | Zákazník vlastní cluster a sadu škálování virtuálního počítače, na kterých je cluster sestaven. V sadě škálování virtuálního počítače můžete povolit službu Azure Disk Encryption. |
| Šifrování na úrovni sloupce (Azure Data Services)| Není k dispozici |  |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě)| Yes |  |
| Zašifrovaná volání rozhraní API| Yes | Service Fabric volání rozhraní API se provádí prostřednictvím Azure Resource Manager. Vyžaduje se platný webový token JSON (JWT). |

## <a name="configuration-management"></a>Správa konfigurace

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (Správa verzí konfigurace atd.)| Ano | |

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [integrovaných kontrolních prvcích zabezpečení napříč službami Azure](../security/fundamentals/security-controls.md).
---
title: Řízení zabezpečení pro Azure Service Fabric
description: Přečtěte si o ovládacích prvcích zabezpečení pro Azure Service Fabric. Obsahuje kontrolní seznam předdefinovaných ovládacích prvků zabezpečení.
author: msmbaldwin
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: a8bb49e20ec5812a4882966c6918cf2bd59f36a0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "75645425"
---
# <a name="security-controls-for-azure-service-fabric"></a>Řízení zabezpečení pro Azure Service Fabric

Tento článek popisuje ovládací prvky zabezpečení integrované do Azure Service Fabric. 

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Síť

| Řízení zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Ano |  |
| Podpora vkládání virtuální sítě| Ano |  |
| Izolace sítě a podpora brány firewall| Ano | Používání skupin zabezpečení sítě (NSG). |
| Podpora vynuceného tunelování| Ano | Sítě Azure poskytují vynucené tunelové propojení. |

## <a name="monitoring--logging"></a>Monitorování protokolování &

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora monitorování Azure (Log Analytics, App Insights atd.)| Ano | Používání Azure Monitoring support a podpory třetích stran. |
| Protokolování a audit roviny řízení a správy| Ano | Všechny operace roviny ovládacího prvku se spouštějí prostřednictvím procesů pro auditování a schválení. |
| Protokolování a audit roviny dat| Není k dispozici | Zákazník vlastní cluster.  |

## <a name="identity"></a>Identita

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Authentication| Ano | Ověřování probíhá prostřednictvím Azure Active Directory. |
| Autorizace| Ano | Správa identit a přístupu (IAM) pro volání prostřednictvím SFRP. Volání přímo do koncového bodu clusteru podporují dvě role: uživatel a správce. Zákazník může namapovat rozhraní API na jednu roli. |

## <a name="data-protection"></a>Ochrana dat

| Řízení zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování na straně serveru v klidovém umístění: klíče spravované společností Microsoft | Ano | Zákazník vlastní cluster a sadu škálování virtuálního počítače, na kterých je cluster sestaven. V sadě škálování virtuálního počítače můžete povolit službu Azure Disk Encryption. |
| Šifrování na straně serveru v klidovém umístění: klíče spravované zákazníkem (BYOK) | Ano | Zákazník vlastní cluster a sadu škálování virtuálního počítače, na kterých je cluster sestaven. V sadě škálování virtuálního počítače můžete povolit službu Azure Disk Encryption. |
| Šifrování na úrovni sloupce (Azure Data Services)| Není k dispozici |  |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování VNet-VNet)| Ano |  |
| Zašifrovaná volání rozhraní API| Ano | Service Fabric volání rozhraní API se provádí prostřednictvím Azure Resource Manager. Vyžaduje se platný webový token JSON (JWT). |

## <a name="configuration-management"></a>Správa konfigurace

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (Správa verzí konfigurace atd.)| Ano | |

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [integrovaných kontrolních prvcích zabezpečení napříč službami Azure](../security/fundamentals/security-controls.md).
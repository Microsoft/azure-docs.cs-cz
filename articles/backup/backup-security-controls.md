---
title: Ovládací prvky zabezpečení
description: Seznamte se s ovládacími prvky zabezpečení použitými ve službě Azure Backup. Tyto ovládací prvky umožňují službě zabránit, zjišťovat a reagovat na ohrožení zabezpečení.
ms.topic: conceptual
ms.date: 09/23/2019
ms.openlocfilehash: 40bd35bdf83d336aebd37cdda0a0b59164ac9f7a
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2020
ms.locfileid: "92055881"
---
# <a name="security-controls-for-azure-backup"></a>Ovládací prvky zabezpečení pro Azure Backup

Tento článek popisuje ovládací prvky zabezpečení integrované do Azure Backup.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Síť

| Řízení zabezpečení | Ano/Ne | Poznámky | Dokumentace
|---|---|--|--|
| Podpora koncového bodu služby| No |  |  |
| Podpora vkládání virtuální sítě| No |  |  |
| Izolace sítě a podpora brány firewall| Yes | |  |
| Vynucená podpora tunelování pro virtuální počítače Azure | Yes  |  |  |
| Vynucená podpora tunelování pro aplikace běžící v rámci virtuálních počítačů Azure| No  |  |  |

## <a name="monitoring--logging"></a>Monitorování protokolování &

| Řízení zabezpečení | Ano/Ne | Poznámky| Dokumentace
|---|---|--|--|
| Podpora monitorování Azure (například Log Analytics, App Insights)| Yes | Log Analytics se podporují prostřednictvím protokolů zdrojů. Další informace najdete v tématu [monitorování Azure Backup chráněných úloh pomocí Log Analytics](backup-azure-diagnostics-mode-data-model.md). |  |
| Protokolování a audit roviny řízení a správy| Yes | Všechny akce aktivované zákazníkem z Azure Portal jsou protokolovány do protokolů aktivit. |  |
| Protokolování a audit roviny dat| No | Azure Backup rovině dat nelze přímo získat.  |  |

## <a name="identity"></a>Identita

| Řízení zabezpečení | Ano/Ne | Poznámky| Dokumentace
|---|---|--|--|
| Ověřování| Yes | Ověřování probíhá prostřednictvím Azure Active Directory. |  |
| Autorizace| Yes | Používají se vytvořené zákazníky a předdefinované role Azure. Další informace najdete v tématu [použití Role-Based Access Control ke správě Azure Backup bodů obnovení](./backup-rbac-rs-vault.md). |  |

## <a name="data-protection"></a>Ochrana dat

| Řízení zabezpečení | Ano/Ne | Poznámky | Dokumentace
|---|---|--|--|
| Šifrování na straně serveru v klidovém umístění: klíče spravované společností Microsoft | Yes | Používá se šifrování služby Storage pro účty úložiště. |  |
| Šifrování na straně serveru v klidovém umístění: klíče spravované zákazníkem (BYOK) | No |  |  |
| Šifrování na úrovni sloupce (Azure Data Services)| No |  |  |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování VNet-VNet)| No | Pomocí protokolu HTTPS. |  |
| Zašifrovaná volání rozhraní API| Yes |  |  |

## <a name="configuration-management"></a>Správa konfigurace

| Řízení zabezpečení | Ano/Ne | Poznámky| Dokumentace
|---|---|--|--|
| Podpora správy konfigurace (Správa verzí a podobně)| Ano|  |  |

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [integrovaných kontrolních prvcích zabezpečení napříč službami Azure](../security/fundamentals/security-controls.md).

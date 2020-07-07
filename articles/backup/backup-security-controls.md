---
title: Ovládací prvky zabezpečení
description: Seznamte se s ovládacími prvky zabezpečení použitými ve službě Azure Backup. Tyto ovládací prvky umožňují službě zabránit, zjišťovat a reagovat na ohrožení zabezpečení.
ms.topic: conceptual
ms.date: 09/23/2019
ms.openlocfilehash: 0e3f5ce942ea8aef9bf5eb98883ae1e72a7ab239
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "74172141"
---
# <a name="security-controls-for-azure-backup"></a>Ovládací prvky zabezpečení pro Azure Backup

Tento článek popisuje ovládací prvky zabezpečení integrované do Azure Backup.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Síť

| Řízení zabezpečení | Ano/Ne | Poznámky | Dokumentace
|---|---|--|
| Podpora koncového bodu služby| No |  |  |
| Podpora vkládání virtuální sítě| No |  |  |
| Izolace sítě a podpora brány firewall| Yes | Pro zálohování virtuálního počítače se podporuje vynucené tunelování. Pro úlohy běžící uvnitř virtuálních počítačů se vynucené tunelování nepodporuje. |  |
| Podpora vynuceného tunelování| No |  |  |

## <a name="monitoring--logging"></a>Monitorování protokolování &

| Řízení zabezpečení | Ano/Ne | Poznámky| | Dokumentace
|---|---|--|
| Podpora monitorování Azure (Log Analytics, App Insights atd.)| Yes | Log Analytics se podporují prostřednictvím protokolů zdrojů. Další informace najdete v tématu [monitorování Azure Backup chráněných úloh pomocí Log Analytics](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/). |  |
| Protokolování a audit roviny řízení a správy| Yes | Všechny akce aktivované zákazníkem z Azure Portal jsou protokolovány do protokolů aktivit. |  |
| Protokolování a audit roviny dat| No | Azure Backup rovině dat nelze přímo získat.  |  |

## <a name="identity"></a>Identita

| Řízení zabezpečení | Ano/Ne | Poznámky| | Dokumentace
|---|---|--|
| Authentication| Yes | Ověřování probíhá prostřednictvím Azure Active Directory. |  |
| Autorizace| Yes | Používají se vytvořené a předdefinované role RBAC. Další informace najdete v tématu [použití Access Control na základě rolí ke správě Azure Backup bodů obnovení](/azure/backup/backup-rbac-rs-vault). |  |

## <a name="data-protection"></a>Ochrana dat

| Řízení zabezpečení | Ano/Ne | Poznámky | | Dokumentace
|---|---|--|
| Šifrování na straně serveru v klidovém umístění: klíče spravované společností Microsoft | Yes | Používá se šifrování služby Storage pro účty úložiště. |  |
| Šifrování na straně serveru v klidovém umístění: klíče spravované zákazníkem (BYOK) | No |  |  |
| Šifrování na úrovni sloupce (Azure Data Services)| No |  |  |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě)| No | Pomocí protokolu HTTPS. |  |
| Zašifrovaná volání rozhraní API| Yes |  |  |

## <a name="configuration-management"></a>Správa konfigurace

| Řízení zabezpečení | Ano/Ne | Poznámky| | Dokumentace
|---|---|--|
| Podpora správy konfigurace (Správa verzí konfigurace atd.)| Ano|  |  |

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [integrovaných kontrolních prvcích zabezpečení napříč službami Azure](../security/fundamentals/security-controls.md).

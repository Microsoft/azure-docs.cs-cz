---
title: Ovládací prvky zabezpečení
description: Seznamte se s ovládacími prvky zabezpečení použitými ve službě Azure Backup. Tyto ovládací prvky umožňují službě zabránit, zjišťovat a reagovat na ohrožení zabezpečení.
ms.topic: conceptual
ms.date: 09/23/2019
ms.openlocfilehash: e69053d4646207b34e8df31b949410994c967303
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/25/2020
ms.locfileid: "88827286"
---
# <a name="security-controls-for-azure-backup"></a>Ovládací prvky zabezpečení pro Azure Backup

Tento článek popisuje ovládací prvky zabezpečení integrované do Azure Backup.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Síť

| Řízení zabezpečení | Ano/Ne | Poznámky | Dokumentace
|---|---|--|--|
| Podpora koncového bodu služby| Ne |  |  |
| Podpora vkládání virtuální sítě| Ne |  |  |
| Izolace sítě a podpora brány firewall| Ano | Pro zálohování virtuálního počítače se podporuje vynucené tunelování. Vynucené tunelování se nepodporuje pro úlohy běžící uvnitř virtuálních počítačů. |  |
| Podpora vynuceného tunelování| Ne |  |  |

## <a name="monitoring--logging"></a>Monitorování protokolování &

| Řízení zabezpečení | Ano/Ne | Poznámky| Dokumentace
|---|---|--|--|
| Podpora monitorování Azure (Log Analytics, App Insights atd.)| Ano | Log Analytics se podporují prostřednictvím protokolů zdrojů. Další informace najdete v tématu [monitorování Azure Backup chráněných úloh pomocí Log Analytics](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/). |  |
| Protokolování a audit roviny řízení a správy| Ano | Všechny akce aktivované zákazníkem z Azure Portal jsou protokolovány do protokolů aktivit. |  |
| Protokolování a audit roviny dat| Ne | Azure Backup rovině dat nelze přímo získat.  |  |

## <a name="identity"></a>Identita

| Řízení zabezpečení | Ano/Ne | Poznámky| Dokumentace
|---|---|--|--|
| Ověřování| Ano | Ověřování probíhá prostřednictvím Azure Active Directory. |  |
| Autorizace| Ano | Používají se vytvořené zákazníky a předdefinované role Azure. Další informace najdete v tématu [použití Access Control na základě rolí ke správě Azure Backup bodů obnovení](./backup-rbac-rs-vault.md). |  |

## <a name="data-protection"></a>Ochrana dat

| Řízení zabezpečení | Ano/Ne | Poznámky | Dokumentace
|---|---|--|--|
| Šifrování na straně serveru v klidovém umístění: klíče spravované společností Microsoft | Ano | Používá se šifrování služby Storage pro účty úložiště. |  |
| Šifrování na straně serveru v klidovém umístění: klíče spravované zákazníkem (BYOK) | Ne |  |  |
| Šifrování na úrovni sloupce (Azure Data Services)| Ne |  |  |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě)| Ne | Pomocí protokolu HTTPS. |  |
| Zašifrovaná volání rozhraní API| Ano |  |  |

## <a name="configuration-management"></a>Správa konfigurace

| Řízení zabezpečení | Ano/Ne | Poznámky| Dokumentace
|---|---|--|--|
| Podpora správy konfigurace (Správa verzí konfigurace atd.)| Ano|  |  |

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [integrovaných kontrolních prvcích zabezpečení napříč službami Azure](../security/fundamentals/security-controls.md).

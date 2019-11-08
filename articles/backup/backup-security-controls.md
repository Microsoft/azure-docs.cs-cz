---
title: Ovládací prvky zabezpečení pro Azure Backup
description: Seznamte se s ovládacími prvky zabezpečení použitými ve službě Azure Backup. Tyto ovládací prvky umožňují službě zabránit, zjišťovat a reagovat na ohrožení zabezpečení.
ms.reviewer: mbaldwin
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: dacurwin
ms.openlocfilehash: 9d7bb2e9ea2d2cf9cce6c4f6d3b4f3346e7ce363
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747146"
---
# <a name="security-controls-for-azure-backup"></a>Ovládací prvky zabezpečení pro Azure Backup

Tento článek popisuje ovládací prvky zabezpečení integrované do Azure Backup.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Síť

| Řízení zabezpečení | Ano/Ne | Poznámky | Dokumentace
|---|---|--|
| Podpora koncového bodu služby| Ne |  |  |
| Podpora vkládání virtuální sítě| Ne |  |  |
| Izolace sítě a podpora brány firewall| Ano | Pro zálohování virtuálního počítače se podporuje vynucené tunelování. Pro úlohy běžící uvnitř virtuálních počítačů se vynucené tunelování nepodporuje. |  |
| Podpora vynuceného tunelování| Ne |  |  |

## <a name="monitoring--logging"></a>Monitorování protokolování &

| Řízení zabezpečení | Ano/Ne | Poznámky| | Dokumentace
|---|---|--|
| Podpora monitorování Azure (Log Analytics, App Insights atd.)| Ano | Log Analytics se podporují prostřednictvím protokolů zdrojů. Další informace najdete v tématu [monitorování Azure Backup chráněných úloh pomocí Log Analytics](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/). |  |
| Protokolování a audit roviny řízení a správy| Ano | Všechny akce aktivované zákazníkem z Azure Portal jsou protokolovány do protokolů aktivit. |  |
| Protokolování a audit roviny dat| Ne | Azure Backup rovině dat nelze přímo získat.  |  |

## <a name="identity"></a>Identita

| Řízení zabezpečení | Ano/Ne | Poznámky| | Dokumentace
|---|---|--|
| Ověřování| Ano | Ověřování probíhá prostřednictvím Azure Active Directory. |  |
| Autorizace| Ano | Používají se vytvořené a předdefinované role RBAC. Další informace najdete v tématu [použití Access Control na základě rolí ke správě Azure Backup bodů obnovení](/azure/backup/backup-rbac-rs-vault). |  |

## <a name="data-protection"></a>Ochrana dat

| Řízení zabezpečení | Ano/Ne | Poznámky | | Dokumentace
|---|---|--|
| Šifrování na straně serveru v klidovém umístění: klíče spravované společností Microsoft | Ano | Používá se šifrování služby Storage pro účty úložiště. |  |
| Šifrování na straně serveru v klidovém umístění: klíče spravované zákazníkem (BYOK) | Ne |  |  |
| Šifrování na úrovni sloupce (Azure Data Services)| Ne |  |  |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě)| Ne | Pomocí protokolu HTTPS. |  |
| Zašifrovaná volání rozhraní API| Ano |  |  |

## <a name="configuration-management"></a>Správa konfigurace

| Řízení zabezpečení | Ano/Ne | Poznámky| | Dokumentace
|---|---|--|
| Podpora správy konfigurace (Správa verzí konfigurace atd.)| Ano|  |  |

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [integrovaných kontrolních prvcích zabezpečení napříč službami Azure](../security/fundamentals/security-controls.md).

---
title: Ovládací prvky zabezpečení
description: Přečtěte si o ovládacích prvcích zabezpečení používaných ve službě Azure Backup. Tyto ovládací prvky pomáhají službě předcházet chybám zabezpečení, zjišťovat je a reagovat na ně.
ms.topic: conceptual
ms.date: 09/23/2019
ms.openlocfilehash: 0e3f5ce942ea8aef9bf5eb98883ae1e72a7ab239
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74172141"
---
# <a name="security-controls-for-azure-backup"></a>Ovládací prvky zabezpečení pro azure backup

Tento článek dokumentuje ovládací prvky zabezpečení integrované do azure backupu.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Network (Síť)

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky | Dokumentace
|---|---|--|
| Podpora koncového bodu služby| Ne |  |  |
| Podpora vstřikování virtuální sítě| Ne |  |  |
| Podpora izolace sítě a brány firewall| Ano | Vynucené tunelové propojení je podporováno pro zálohování virtuálních počítače. Vynucené tunelové propojení není podporováno pro úlohy spuštěné uvnitř virtuálních zařízení. |  |
| Podpora vynuceného tunelování| Ne |  |  |

## <a name="monitoring--logging"></a>Sledování & protokolování

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky| | Dokumentace
|---|---|--|
| Podpora monitorování Azure (analýza protokolů, přehledy aplikací atd.)| Ano | Log Analytics je podporovánprostřednictvím protokolů prostředků. Další informace najdete [v tématu Monitorování chráněných úloh Azure Backup pomocí analýzy protokolů](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/). |  |
| Protokolování a audit roviny řízení a správy| Ano | Všechny akce aktivované zákazníkem z portálu Azure se zaznamenávají do protokolů aktivit. |  |
| Protokolování a audit roviny dat| Ne | Rovina dat Azure Backup není přístupná přímo.  |  |

## <a name="identity"></a>Identita

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky| | Dokumentace
|---|---|--|
| Ověřování| Ano | Ověřování probíhá prostřednictvím služby Azure Active Directory. |  |
| Autorizace| Ano | Zákazník vytvořil a předdefinované Role RBAC se používají. Další informace najdete [v tématu Správa bodů obnovení azure backup pomocí řízení přístupu na základě rolí](/azure/backup/backup-rbac-rs-vault). |  |

## <a name="data-protection"></a>Ochrana dat

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky | | Dokumentace
|---|---|--|
| Šifrování na straně serveru v klidovém stavu: Klíče spravované společností Microsoft | Ano | Použití šifrování služby úložiště pro účty úložiště. |  |
| Šifrování na straně serveru v klidovém stavu: klíče spravované zákazníkem (BYOK) | Ne |  |  |
| Šifrování na úrovni sloupců (Azure Data Services)| Ne |  |  |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě)| Ne | Pomocí protokolu HTTPS. |  |
| Zašifrovaná volání rozhraní API| Ano |  |  |

## <a name="configuration-management"></a>Správa konfigurace

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky| | Dokumentace
|---|---|--|
| Podpora správy konfigurace (správa verzí konfigurace atd.)| Ano|  |  |

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [integrovaných ovládacích prvcích zabezpečení napříč službami Azure](../security/fundamentals/security-controls.md).

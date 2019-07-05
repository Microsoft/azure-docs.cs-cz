---
title: Běžné atributy zabezpečení pro službu Azure Backup
description: Kontrolní seznam společné atributy zabezpečení za vaše rozhodnutí vyzkoušet Azure Backup
services: backup
author: utraghuv
manager: barbkess
ms.service: backup
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 595cc4eff70e2df4cb6d7f1d6a0c1a2748b34bf2
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565623"
---
# <a name="security-attributes-for-azure-backup"></a>Atributy zabezpečení pro službu Azure Backup

Tento článek popisuje atributy zabezpečení integrované do Azure Backup. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Preventivní

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování v klidovém stavu:<ul><li>Šifrování na straně serveru</li><li>Šifrování na straně serveru pomocí klíčů spravovaných zákazníkem</li><li>Další funkce šifrování (například klientů, funkce always encrypted, atd.)</ul>| Ano | Pomocí šifrování služby storage pro účty úložiště. |
| Šifrování během přenosu:<ul><li>Express route šifrování</li><li>Ve virtuální síti šifrování</li><li>Šifrování virtuálními sítěmi</ul>| Ne | Pomocí protokolu HTTPS. |
| Šifrování klíče zpracování (CMK, BYOK, atd.)| Ne |  |
| Šifrování na úrovni sloupce (Azure Data Services)| Ne |  |
| Šifrované volání rozhraní API| Ano |  |

## <a name="network-segmentation"></a>Segmentace sítě

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Ne |  |
| Vkládání podpory virtuálních sítí| Ne |  |
| Izolace sítě a podporu funkce brány firewall| Ano | Vynucené tunelování se podporuje pro zálohování virtuálních počítačů. Vynucené tunelování se nepodporuje pro pracovní postupy spouštěné ve virtuálních počítačích. |
| Vynucené tunelování podpory| Ne |  |

## <a name="detection"></a>Detection (Detekce)

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora (Log analytics, App insights atd.) pro monitorování Azure| Ano | Log Analytics je podporované prostřednictvím diagnostické protokoly. Zobrazit [monitorování Azure Backup chráněných pracovních vytížení používat službu Log Analytics](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/) Další informace. |

## <a name="identity-and-access-management"></a>Správa identit a přístupu

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Authentication| Ano | Ověřování je prostřednictvím Azure Active Directory. |
| Autorizace| Ano | Zákazník byl vytvořen a předdefinované role RBAC se používají. Zobrazit [Use Role-Based řízení přístupu ke správě body obnovení Azure Backup](/azure/backup/backup-rbac-rs-vault) Další informace. |


## <a name="audit-trail"></a>Záznam pro audit

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ovládací prvek a správu roviny protokolování a auditování| Ano | Všechny akce aktivuje zákazníků na webu Azure Portal se zaznamenávají do protokolů aktivit. |
| Protokolování roviny dat a auditu| Ne | Rovina dat služby Azure Backup je nedostupné přímo.  |

## <a name="configuration-management"></a>Správa konfigurace

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (verze konfigurace atd.)| Ano|  |
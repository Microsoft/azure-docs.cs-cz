---
title: Atributy zabezpečení pro Azure Backup
description: Kontrolní seznam atributů zabezpečení pro vyhodnocení Azure Backup
ms.reviewer: mbaldwin
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: dacurwin
ms.openlocfilehash: e6ea701840b201c2794d81be5019d57ca531cd00
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2019
ms.locfileid: "68688512"
---
# <a name="security-attributes-for-azure-backup"></a>Atributy zabezpečení pro Azure Backup

Tento článek popisuje atributy zabezpečení integrované do Azure Backup. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Preventivní

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování v klidovém formátu (například šifrování na straně serveru, šifrování na straně serveru pomocí klíčů spravovaných zákazníkem a další funkce šifrování)| Ano | Používá se šifrování služby Storage pro účty úložiště. |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě)| Ne | Pomocí protokolu HTTPS. |
| Zpracování šifrovacích klíčů (CMK, BYOK atd.)| Ne |  |
| Šifrování na úrovni sloupce (Azure Data Services)| Ne |  |
| Zašifrovaná volání rozhraní API| Ano |  |

## <a name="network-segmentation"></a>Segmentace sítě

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Ne |  |
| Podpora vkládání virtuální sítě| Ne |  |
| Izolace sítě a podpora brány firewall| Ano | Pro zálohování virtuálního počítače se podporuje vynucené tunelování. Pro úlohy běžící uvnitř virtuálních počítačů se vynucené tunelování nepodporuje. |
| Podpora vynuceného tunelování| Ne |  |

## <a name="detection"></a>Detekce

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora monitorování Azure (Log Analytics, App Insights atd.)| Ano | Log Analytics se podporují prostřednictvím diagnostických protokolů. Další informace najdete v tématu [monitorování Azure Backup chráněných úlohami pomocí Log Analytics](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/) . |

## <a name="identity-and-access-management"></a>Správa identit a přístupu

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ověřování| Ano | Ověřování probíhá prostřednictvím Azure Active Directory. |
| Authorization| Ano | Používají se vytvořené a předdefinované role RBAC. Další informace najdete v tématu [použití Access Control založeného na rolích ke správě Azure Backup bodů obnovení](/azure/backup/backup-rbac-rs-vault) . |


## <a name="audit-trail"></a>Záznam pro audit

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Protokolování a audit roviny řízení a správy| Ano | Všechny akce aktivované zákazníkem z Azure Portal jsou protokolovány do protokolů aktivit. |
| Protokolování a audit roviny dat| Ne | Azure Backup rovině dat nelze přímo získat.  |

## <a name="configuration-management"></a>Správa konfigurace

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (Správa verzí konfigurace atd.)| Ano|  |
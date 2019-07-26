---
title: Atributy zabezpečení pro Azure Service Fabric
description: Kontrolní seznam atributů zabezpečení pro vyhodnocení Service Fabric Azure
services: service-fabric
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: service-fabric
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 23c7f8bdcf67d59ccdd5cd0b00bc0e0960ba1d8f
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443854"
---
# <a name="security-attributes-for-azure-service-fabric"></a>Atributy zabezpečení pro Azure Service Fabric

Tento článek popisuje atributy zabezpečení integrované do Azure Service Fabric. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Preventivní

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování v klidovém formátu (například šifrování na straně serveru, šifrování na straně serveru pomocí klíčů spravovaných zákazníkem a další funkce šifrování)| Ano | Zákazník vlastní cluster a sadu škálování virtuálního počítače, na kterých je cluster sestaven. V sadě škálování virtuálního počítače můžete povolit službu Azure Disk Encryption. |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě)| Ano |  |
| Zpracování šifrovacích klíčů (CMK, BYOK atd.)| Ano | Zákazník vlastní cluster a sadu škálování virtuálního počítače, na kterých je cluster sestaven. V sadě škálování virtuálního počítače můžete povolit službu Azure Disk Encryption. |
| Šifrování na úrovni sloupce (Azure Data Services)| Není k dispozici |  |
| Zašifrovaná volání rozhraní API| Ano | Service Fabric volání rozhraní API se provádí prostřednictvím Azure Resource Manager. Vyžaduje se platný webový token JSON (JWT). |

## <a name="network-segmentation"></a>Segmentace sítě

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Ano |  |
| Podpora vkládání virtuální sítě| Ano |  |
| Izolace sítě a podpora brány firewall| Ano | Používání skupin zabezpečení sítě (NSG). |
| Podpora vynuceného tunelování| Ano | Sítě Azure poskytují vynucené tunelové propojení. |

## <a name="detection"></a>Detekce

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora monitorování Azure (Log Analytics, App Insights atd.)| Ano | Používání Azure Monitoring support a podpory třetích stran. |

## <a name="identity-and-access-management"></a>Správa identit a přístupu

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ověřování| Ano | Ověřování probíhá prostřednictvím Azure Active Directory. |
| Authorization| Ano | Správa identit a přístupu (IAM) pro volání prostřednictvím SFRP. Volání přímo do koncového bodu clusteru podporují dvě role: Uživatel a správce. Zákazník může namapovat rozhraní API na jednu roli. |


## <a name="audit-trail"></a>Záznam pro audit

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Protokolování a audit roviny řízení a správy| Ano | Všechny operace roviny ovládacího prvku se spouštějí prostřednictvím procesů pro auditování a schválení. |
| Protokolování a audit roviny dat| Není k dispozici | Zákazník vlastní cluster.  |

## <a name="configuration-management"></a>Správa konfigurace

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (Správa verzí konfigurace atd.)| Ano | |
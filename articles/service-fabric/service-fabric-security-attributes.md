---
title: Běžné atributy zabezpečení pro Azure Service Fabric
description: Kontrolní seznam společné atributy zabezpečení za vaše rozhodnutí vyzkoušet Azure Service Fabric
services: service-fabric
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: service-fabric
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 7c1718298c3f7c3fea28fa0b18569085f071696f
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/22/2019
ms.locfileid: "66003057"
---
# <a name="security-attributes-for-azure-service-fabric"></a>Atributy zabezpečení pro Azure Service Fabric

Tento článek popisuje atributy zabezpečení integrované do Azure Service Fabric. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Preventivní

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování v klidovém stavu:<ul><li>Šifrování na straně serveru</li><li>Šifrování na straně serveru pomocí klíčů spravovaných zákazníkem</li><li>Další funkce šifrování (například klientů, funkce always encrypted, atd.)</ul>| Ano | Zákazník je vlastníkem služby clusteru a škálovací sady na cluster je sestavena virtuálních počítačů. Azure disk encryption je možné povolit na škálovací sadu virtuálních počítačů. |
| Šifrování během přenosu:<ul><li>Express route šifrování</li><li>Ve virtuální síti šifrování</li><li>Šifrování virtuálními sítěmi</ul>| Ano |  |
| Šifrování klíče zpracování (CMK, BYOK, atd.)| Ano | Zákazník je vlastníkem služby clusteru a škálovací sady na cluster je sestavena virtuálních počítačů. Azure disk encryption je možné povolit na škálovací sadu virtuálních počítačů. |
| Šifrování na úrovni sloupce (Azure Data Services)| neuvedeno |  |
| Šifrované volání rozhraní API| Ano | Volání rozhraní API Service Fabric provádí prostřednictvím Azure Resource Manageru. Vyžaduje se platný webový token JSON (JWT). |

## <a name="network-segmentation"></a>Segmentace sítě

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Ano |  |
| Vkládání podpory virtuálních sítí| Ano |  |
| Izolace sítě a podporu funkce brány firewall| Ano | Použití skupin zabezpečení sítě (NSG). |
| Vynucené tunelování podpory| Ano | Sítě Azure poskytují vynucené tunelování. |

## <a name="detection"></a>Detekce

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora (Log analytics, App insights atd.) pro monitorování Azure| Ano | Pomocí Azure monitoring podporu a podporu třetí strany. |

## <a name="identity-and-access-management"></a>Správa identit a přístupu

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Authentication| Ano | Ověřování je prostřednictvím Azure Active Directory. |
| Autorizace| Ano | Správa identit a přístupu (IAM) pro volání prostřednictvím SFRP. Volání přímo na koncový bod clusteru podporuje dvě role: Uživatele a správce. Zákazník může mapování rozhraní API na kteroukoli z těchto rolí. |


## <a name="audit-trail"></a>Záznam pro audit

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ovládací prvek a správu roviny protokolování a auditování| Ano | Spustit přes procesy pro auditování a jejich schvalování, všechny operace s roviny řízení. |
| Protokolování roviny dat a auditu| neuvedeno | Označuje zákazníka vlastnícího clusteru.  |

## <a name="configuration-management"></a>Správa konfigurace

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (verze konfigurace atd.)| Ano | |
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
ms.openlocfilehash: b25400f21e24b18a0a21b9d849bdd505f6d47aac
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2019
ms.locfileid: "63765755"
---
# <a name="common-security-attributes-for-azure-service-fabric"></a>Běžné atributy zabezpečení pro Azure Service Fabric

Zabezpečení je integrované do všech oblastí služby Azure. Tento článek popisuje běžné atributy zabezpečení integrované do Azure Service Fabric. 

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
| vkládání podpory virtuálních sítí| Ano |  |
| Izolace sítě a podporu funkce brány firewall| Ano | Použití skupin zabezpečení sítě (NSG). |
| Vynucené tunelování podpory| Ano | Sítě Azure poskytují vynucené tunelování. |

## <a name="detection"></a>Detekce

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora (Log analytics, App insights atd.) pro monitorování Azure| Ano | Pomocí Azure monitoring podporu a podporu třetí strany. |

## <a name="identity-and-access-management"></a>Správa identit a přístupu

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
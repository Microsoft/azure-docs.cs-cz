---
title: Běžné atributy zabezpečení pro Azure Resource Manageru
description: Kontrolní seznam společné atributy zabezpečení za vaše rozhodnutí vyzkoušet Azure Resource Manageru
services: azure-resource-manager
author: msmbaldwin
manager: barbkess
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: mbaldwin
ms.openlocfilehash: a771d4c2ae22b7bf149c13c80fe5286ef52a4545
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/22/2019
ms.locfileid: "66002261"
---
# <a name="security-attributes-for-azure-resource-manager"></a>Atributy zabezpečení pro Azure Resource Manageru

Tento článek popisuje atributy zabezpečení integrované do Azure Resource Manageru.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Preventivní

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování v klidovém stavu:<ul><li>Šifrování na straně serveru</li><li>Šifrování na straně serveru pomocí klíčů spravovaných zákazníkem</li><li>Další funkce šifrování (například klientů, funkce always encrypted, atd.)</ul>| Ano |  |
| Šifrování během přenosu:<ul><li>Express route šifrování</li><li>Ve virtuální síti šifrování</li><li>Šifrování virtuálními sítěmi</ul>| Ano | HTTPS/TLS. |
| Šifrování klíče zpracování (CMK, BYOK, atd.)| neuvedeno | Azure Resource Manager ukládá žádný obsah zákazníka, jenom data ovládacího prvku. |
| Šifrování na úrovni sloupce (Azure Data Services)| Ano | |
| Šifrované volání rozhraní API| Ano | |

## <a name="network-segmentation"></a>Segmentace sítě

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Ne | |
| Vkládání podpory virtuálních sítí| Ano | |
| Izolace sítě a podporu funkce brány firewall| Ne |  |
| Vynucené tunelování podpory| Ne |  |

## <a name="detection"></a>Detekce

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora (Log analytics, App insights atd.) pro monitorování Azure| Ne | |

## <a name="identity-and-access-management"></a>Správa identit a přístupu

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Authentication| Ano | [Azure Active Directory](/azure/active-directory) založené.|
| Autorizace| Ano | |


## <a name="audit-trail"></a>Záznam pro audit

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ovládací prvek a správu roviny protokolování a auditování| Ano | Všechny operace (PUT, POST, DELETE) prováděné s vašimi prostředky; zápisu zpřístupňují protokoly aktivit Zobrazit [zobrazení protokolů aktivit pro auditování akcí u prostředků](resource-group-audit.md). |
| Protokolování roviny dat a auditu| neuvedeno | |

## <a name="configuration-management"></a>Správa konfigurace

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (verze konfigurace atd.)| Ano |  |

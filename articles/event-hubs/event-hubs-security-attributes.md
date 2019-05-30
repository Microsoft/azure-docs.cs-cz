---
title: Běžné atributy zabezpečení pro Azure Event Hubs
description: Kontrolní seznam společné atributy zabezpečení za vaše rozhodnutí vyzkoušet Azure Event Hubs
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: mbaldwin
ms.openlocfilehash: 50f19d50f211972c2042c69a1e3177ffb4a47624
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2019
ms.locfileid: "66247196"
---
# <a name="common-security-attributes-for-azure-event-hubs"></a>Běžné atributy zabezpečení pro Azure Event Hubs

Tento článek popisuje běžné atributy zabezpečení integrované do služby Azure Event Hubs.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Preventivní

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování v klidovém stavu:<ul><li>Šifrování na straně serveru</li><li>Šifrování na straně serveru pomocí klíčů spravovaných zákazníkem</li><li>Další funkce šifrování (například klientů, funkce always encrypted, atd.)</ul>|  Ano | |
| Šifrování během přenosu:<ul><li>Express route šifrování</li><li>Ve virtuální síti šifrování</li><li>Šifrování virtuálními sítěmi</ul>| Ano | |
| Šifrování klíče zpracování (CMK, BYOK, atd.)| Ne |  |
| Šifrování na úrovni sloupce (Azure Data Services)| neuvedeno | |
| Šifrované volání rozhraní API| Ano |  |

## <a name="network-segmentation"></a>Segmentace sítě

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Ano |  |
| vkládání podpory virtuálních sítí| Ne | |
| Izolace sítě a podporu funkce brány firewall| Ano |  |
| Vynucené tunelování podpory| Ne |  |

## <a name="detection"></a>Detection (Detekce)

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora (Log analytics, App insights atd.) pro monitorování Azure| Ano | |

## <a name="identity-and-access-management"></a>Správa identit a přístupu

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Authentication| Ano | |
| Autorizace|  Ano | |


## <a name="audit-trail"></a>Záznam pro audit

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ovládací prvek a správu roviny protokolování a auditování| Ano |  |
| Protokolování roviny dat a auditu| Ano |   |

## <a name="configuration-management"></a>Správa konfigurace

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (verze konfigurace atd.)| Ano | |

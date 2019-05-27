---
title: Atributy zabezpečení pro službu Azure API Management
description: Kontrolní seznam společné atributy zabezpečení za vaše rozhodnutí vyzkoušet API Management
services: api-management
author: msmbaldwin
manager: barbkess
ms.service: api-management
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 3b5826d472b80179c5eb76e0e3a6b1c7ee282487
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/22/2019
ms.locfileid: "66001096"
---
# <a name="security-attributes-for-api-management"></a>Atributy zabezpečení pro službu API Management

Tento článek popisuje atributy zabezpečení integrované do API managementu.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Preventivní

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování v klidovém stavu:<ul><li>Šifrování na straně serveru</li><li>Šifrování na straně serveru pomocí klíčů spravovaných zákazníkem</li><li>Další funkce šifrování (například klientů, funkce always encrypted, atd.)</ul>| Ano (pouze šifrování na straně služby) | Jsou zašifrované citlivá data, jako jsou certifikáty, klíče a hodnoty s názvem tajný klíč spravovaný službou, za klíčů instance služby. |
| Šifrování během přenosu:<ul><li>Express route šifrování</li><li>Ve virtuální síti šifrování</li><li>Šifrování virtuálními sítěmi</ul>| Ano | [Express Route](../expressroute/index.yml) a zajišťuje šifrování pro virtuální síť [sítě Azure](../virtual-network/index.yml). |
| Šifrování klíče zpracování (CMK, BYOK, atd.)| Ne | Všechny šifrovací klíče jsou za instanci služby a spravované služby. |
| Šifrování na úrovni sloupce (Azure Data Services)| neuvedeno | |
| Šifrované volání rozhraní API| Ano | Volání rovina správy se provádějí přes [Azure Resource Manageru](../azure-resource-manager/index.yml) přes protokol TLS. Vyžaduje se platný webový token JSON (JWT).  Volání roviny dat je možné svázat s TLS a jeden z podporovaných ověřovací mechanismy, které (například klientský certifikát nebo tokenů JWT).
 |

## <a name="network-segmentation"></a>Segmentace sítě

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Ne | |
| Vkládání podpory virtuálních sítí| Ano | |
| Izolace sítě a podporu funkce brány firewall| Ano | Použití skupin zabezpečení sítě (NSG) a Azure Application Gateway (nebo jiné softwarové zařízení) v uvedeném pořadí. |
| Vynucené tunelování podpory| Ano | Sítě Azure poskytují vynucené tunelování. |

## <a name="detection"></a>Detekce

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora (Log analytics, App insights atd.) pro monitorování Azure| Ano | |

## <a name="identity-and-access-management"></a>Správa identit a přístupu

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Authentication| Ano | |
| Autorizace| Ano | |


## <a name="audit-trail"></a>Záznam pro audit

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ovládací prvek a správu roviny protokolování a auditování| Ano | [Protokoly aktivit Azure Monitor](../azure-monitor/platform/activity-logs-overview.md) |
| Protokolování roviny dat a auditu| Ano | [Diagnostické protokoly Azure monitoru](../azure-monitor/platform/diagnostic-logs-overview.md) a (volitelně) [Azure Application Insights](../azure-monitor/app/app-insights-overview.md).  |

## <a name="configuration-management"></a>Správa konfigurace

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (verze konfigurace atd.)| Ano | Použití [Azure API Management DevOps Resource Kit](https://aka.ms/apimdevops) |

## <a name="vulnerability-scans-false-positives"></a>Počet falešně pozitivních výsledků kontrol ohrožení zabezpečení

Tato část popisuje běžné chyby, které nemají vliv na Azure API Management.

| Chyba zabezpečení               | Popis                                                                                                                                                                                                                                                                                                               |
|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ticketbleed (CVE 2016 9244) | Ticketbleed je chyba v implementaci TLS SessionTicket rozšíření v některé produkty F5. To umožňuje úniku ("vykrvení") až 31 bajtů dat z neinicializované paměti. To je způsobeno zásobníku TLS odsazení ID relace, předat z klienta, se data tak, aby 32 bitů. |

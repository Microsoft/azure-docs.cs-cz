---
title: Ovládací prvky zabezpečení pro správu rozhraní AZURE API
description: Kontrolní seznam bezpečnostních ovládacích prvků pro vyhodnocení správy rozhraní API
services: api-management
author: vladvino
ms.service: api-management
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: vlvinogr
ms.openlocfilehash: 670050efe01fb658fab52a43914f193e9798b828
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75751137"
---
# <a name="security-controls-for-api-management"></a>Ovládací prvky zabezpečení pro správu rozhraní API

Tento článek dokumentuje ovládací prvky zabezpečení integrované do správy rozhraní API.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Network (Síť)

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky | Dokumentace |
|---|---|--|--|
| Podpora koncového bodu služby| Ne | |  |
| Podpora vstřikování virtuální sítě| Ano | |  |
| Podpora izolace sítě a brány firewall| Ano | Pomocí skupin zabezpečení sítě (NSG) a Azure Application Gateway (nebo jiné softwarové zařízení) v uvedeném pořadí. |  |
| Podpora vynuceného tunelování| Ano | Azure networking poskytuje vynucené tunelové propojení. |  |

## <a name="monitoring--logging"></a>Sledování & protokolování

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky| Dokumentace |
|---|---|--|--|
| Podpora monitorování Azure (analýza protokolů, přehledy aplikací atd.)| Ano | | |
| Protokolování a audit roviny řízení a správy| Ano | [Protokoly aktivit Azure Monitor](../azure-monitor/platform/platform-logs-overview.md) | |
| Protokolování a audit roviny dat| Ano | [Diagnostické protokoly Azure Monitor](../azure-monitor/platform/platform-logs-overview.md) a (volitelně) [Přehledy aplikací Azure](../azure-monitor/app/app-insights-overview.md).  | |


## <a name="identity"></a>Identita

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky| Dokumentace |
|---|---|--|--|
| Ověřování| Ano | |  |
| Autorizace| Ano | |  |

## <a name="data-protection"></a>Ochrana dat

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky | Dokumentace |
|---|---|--|--|
| Šifrování na straně serveru v klidovém stavu: Klíče spravované společností Microsoft | Ano | Citlivá data, jako jsou certifikáty, klíče a hodnoty s názvem tajného klíče, jsou šifrována pomocí klíčů instancí spravované službou. |  |
| Šifrování na straně serveru v klidovém stavu: klíče spravované zákazníkem (BYOK) | Ne | Všechny šifrovací klíče jsou na instanci služby a jsou spravovány službou. |  |
| Šifrování na úrovni sloupců (Azure Data Services)| Není dostupné. | |  |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě)| Ano | [Šifrování express route](../expressroute/index.yml) a virtuální sítě poskytuje [síť Azure](../virtual-network/index.yml). |  |
| Zašifrovaná volání rozhraní API| Ano | Volání roviny správy se uskutečňují prostřednictvím [Správce prostředků Azure](../azure-resource-manager/index.yml) přes TLS. Je vyžadován platný webový token JSON (JWT).  Volání roviny dat lze zabezpečit pomocí TLS a jednoho z podporovaných mechanismů ověřování (například klientský certifikát nebo JWT). |   |
 |

## <a name="configuration-management"></a>Správa konfigurace

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky| Dokumentace |
|---|---|--|--|
| Podpora správy konfigurace (správa verzí konfigurace atd.)| Ano | Použití sady [Azure API Management DevOps Resource Kit](https://aka.ms/apimdevops) |  |

## <a name="vulnerability-scans-false-positives"></a>Chyba zabezpečení prohledává falešně pozitivní výsledky

Tato část dokumentuje běžné chyby zabezpečení, které nemají vliv na správu rozhraní Azure API.

| Ohrožení zabezpečení               | Popis                                                                                                                                                                                                                                                                                                               |
|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Proslýchá se (CVE-2016-9244) | Ticketbleed je chyba zabezpečení při implementaci rozšíření TLS SessionTicket, které se nachází v některých produktech F5. Umožňuje únik ("krvácení") až 31 bajtů dat z neinicializované paměti. To je způsobeno odsazení zásobníku TLS id relace, předané z klienta, s daty, aby bylo 32 bitů dlouhé. |

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [integrovaných ovládacích prvcích zabezpečení napříč službami Azure](../security/fundamentals/security-controls.md).
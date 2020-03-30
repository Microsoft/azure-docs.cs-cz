---
title: Úvod do brány firewall webových aplikací Azure
description: Tento článek obsahuje přehled brány firewall webových aplikací Azure (WAF)
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 03/18/2020
ms.author: victorh
ms.topic: overview
ms.openlocfilehash: 49024c86d09f5cdd9e8b04d5a49f60021660b0c4
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79475783"
---
# <a name="what-is-azure-web-application-firewall"></a>Co je Firewall webových aplikací Azure?

Firewall webových aplikací (WAF) poskytuje centralizovanou ochranu webových aplikací před běžným zneužitím a ohrožením zabezpečení. Webové aplikace jsou stále více terčem škodlivých útoků, které zneužívají běžně známé chyby zabezpečení. Vkládání SQL a skriptování mezi lokalitami patří mezi nejběžnější útoky.

![WAF – přehled](media/overview/wafoverview.png)

Předcházení takovým útokům v kódu aplikace je náročné. To může vyžadovat přísnou údržbu, opravy a monitorování na více vrstvách topologie aplikace. Centralizovaná brána firewall webových aplikací usnadňuje správu zabezpečení. Waf také poskytuje správcům aplikací lepší záruku ochrany před hrozbami a vniknutím.

Řešení WAF může rychleji reagovat na bezpečnostní hrozbu centrálním opravam známé chyby zabezpečení namísto zabezpečení jednotlivých webových aplikací.

## <a name="supported-service"></a>Podporovaná služba

WAF se dá nasadit pomocí Azure Application Gateway, Azure Front Door a Azure Content Delivery Network (CDN) od Microsoftu. WAF na Azure CDN je aktuálně ve verzi Public Preview.  WAF má funkce, které jsou přizpůsobeny pro každou konkrétní službu. Další informace o funkcích WAF pro každou službu naleznete v přehledu jednotlivých služeb.

## <a name="next-steps"></a>Další kroky

- Další informace o bráně firewall webových aplikací v bráně aplikace naleznete v [tématu Webová brána firewall v bráně aplikace Azure](./ag/ag-overview.md).
- Další informace o bráně firewall webových aplikací ve službě Azure Front Door Service naleznete [v tématu Firewall webových aplikací ve službě Azure Front Door Service](./afds/afds-overview.md).
- Další informace o bráně firewall webových aplikací ve službě Azure CDN service naleznete [v tématu Firewall webových aplikací ve službě Azure CDN Service](./cdn/cdn-overview.md)

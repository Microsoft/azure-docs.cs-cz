---
title: Úvod do firewallu webových aplikací Azure
description: Tento článek poskytuje přehled firewallu webových aplikací Azure (WAF).
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 03/18/2020
ms.author: victorh
ms.topic: overview
ms.openlocfilehash: 49024c86d09f5cdd9e8b04d5a49f60021660b0c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "79475783"
---
# <a name="what-is-azure-web-application-firewall"></a>Co je Firewall webových aplikací Azure?

Firewall webových aplikací (WAF) poskytuje centralizovanou ochranu webových aplikací před běžným zneužitím a ohrožením zabezpečení. Webové aplikace jsou stále více zaměřené na škodlivé útoky, které využívají často známá ohrožení zabezpečení. Vkládání SQL a skriptování mezi weby patří mezi nejběžnější útoky.

![WAF – přehled](media/overview/wafoverview.png)

Prevence takových útoků v kódu aplikace je náročné. Může vyžadovat přísnou údržbu, opravy a monitorování ve více vrstvách topologie aplikace. Centralizované brány firewall webových aplikací pomáhají významně zjednodušit správu zabezpečení. WAF také poskytuje správcům aplikací lepší zabezpečení před hrozbami a vniknutími.

Řešení WAF může reagovat na bezpečnostní hrozbu rychleji tím, že centrálně opraví známou chybu zabezpečení, místo abyste museli zabezpečit každou jednotlivou webovou aplikaci.

## <a name="supported-service"></a>Podporovaná služba

WAF se dají nasadit s využitím Azure Application Gateway, předními dveřmi Azure a službou Azure Content Delivery Network (CDN) od Microsoftu. WAF v Azure CDN je v současnosti ve verzi Public Preview.  WAF obsahuje funkce, které jsou přizpůsobené pro každou konkrétní službu. Další informace o funkcích WAF pro jednotlivé služby najdete v přehledu jednotlivých služeb.

## <a name="next-steps"></a>Další kroky

- Další informace o firewallu webových aplikací v Application Gateway najdete v tématu [Brána Firewall webových aplikací v Azure Application Gateway](./ag/ag-overview.md).
- Další informace o firewallu webových aplikací ve službě Azure front-dveří najdete v tématu [Služba Firewall webových aplikací ve službě Azure front-dveří](./afds/afds-overview.md).
- Další informace o bráně firewall webových aplikací ve službě Azure CDN najdete v tématu [služba Azure CDN Firewall webových aplikací](./cdn/cdn-overview.md) .

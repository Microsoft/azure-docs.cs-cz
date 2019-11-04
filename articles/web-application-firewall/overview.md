---
title: Úvod do firewallu webových aplikací Azure
description: Tento článek poskytuje přehled firewallu webových aplikací Azure (WAF).
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 10/04/2019
ms.author: victorh
ms.topic: overview
ms.openlocfilehash: 7b43a6bdac254493da8693b55158e15746e76dc3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73502041"
---
# <a name="what-is-azure-web-application-firewall"></a>Co je Firewall webových aplikací Azure?

Firewall webových aplikací (WAF) zajišťuje centralizovanou ochranu webových aplikací před běžným zneužitím a ohrožením zabezpečení. Webové aplikace jsou stále více zaměřené na škodlivé útoky, které využívají často známá ohrožení zabezpečení. Vkládání SQL a skriptování mezi weby patří mezi nejběžnější útoky.

![WAF – přehled](media/overview/wafoverview.png)

Prevence takových útoků v kódu aplikace je náročné. Může vyžadovat přísnou údržbu, opravy a monitorování ve více vrstvách topologie aplikace. Centralizované brány firewall webových aplikací pomáhají významně zjednodušit správu zabezpečení. WAF také poskytuje správcům aplikací lepší zabezpečení před hrozbami a vniknutími.

Řešení WAF může reagovat na bezpečnostní hrozbu rychleji tím, že centrálně opraví známou chybu zabezpečení, místo abyste museli zabezpečit každou jednotlivou webovou aplikaci.

WAF se dají nasadit s využitím Azure Application Gateway a služby front-dveří Azure. V současné době má WAF funkce, které jsou přizpůsobené pro každou konkrétní službu. Další informace o funkcích WAF pro jednotlivé služby najdete v přehledu jednotlivých služeb.

## <a name="next-steps"></a>Další kroky

- Další informace o firewallu webových aplikací v Application Gateway najdete v tématu [Brána Firewall webových aplikací v Azure Application Gateway](./ag/ag-overview.md).
- Další informace o firewallu webových aplikací na službě Azure front-dveří najdete v tématu [Služba Firewall webových aplikací ve službě Azure front-dveří](./afds/afds-overview.md).

---
title: Úvod do firewallu webových aplikací Azure
description: Tento článek poskytuje přehled firewallu webových aplikací Azure (WAF).
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 03/06/2020
ms.author: victorh
ms.topic: overview
ms.openlocfilehash: 44bc8db5d8ada0378f8f9d0911ed398ba491d289
ms.sourcegitcommit: f5e4d0466b417fa511b942fd3bd206aeae0055bc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78851189"
---
# <a name="what-is-azure-web-application-firewall"></a>Co je Firewall webových aplikací Azure?

Firewall webových aplikací (WAF) poskytuje centralizovanou ochranu webových aplikací před běžným zneužitím a ohrožením zabezpečení. Webové aplikace jsou stále více zaměřené na škodlivé útoky, které využívají často známá ohrožení zabezpečení. Vkládání SQL a skriptování mezi weby patří mezi nejběžnější útoky.

![WAF – přehled](media/overview/wafoverview.png)

Prevence takových útoků v kódu aplikace je náročné. Může vyžadovat přísnou údržbu, opravy a monitorování ve více vrstvách topologie aplikace. Centralizované brány firewall webových aplikací pomáhají významně zjednodušit správu zabezpečení. WAF také poskytuje správcům aplikací lepší zabezpečení před hrozbami a vniknutími.

Řešení WAF může reagovat na bezpečnostní hrozbu rychleji tím, že centrálně opraví známou chybu zabezpečení, místo abyste museli zabezpečit každou jednotlivou webovou aplikaci.

## <a name="supported-services"></a>Podporované služby

WAF se dají nasadit s využitím [azure Application Gateway](../application-gateway/overview.md) a [služby front-dveří Azure](../frontdoor/front-door-overview.md). Obě služby jsou nástroje pro vyrovnávání zatížení vrstvy 7 (HTTP/S), ale Application Gateway je místní služba a přední dveře je globální služba. WAF obsahuje funkce, které jsou přizpůsobené pro každou konkrétní službu.

Další informace najdete v přehledu WAF pro jednotlivé služby.

## <a name="next-steps"></a>Další kroky

- Další informace o firewallu webových aplikací v Application Gateway najdete v tématu [Brána Firewall webových aplikací v Azure Application Gateway](./ag/ag-overview.md).
- Další informace o firewallu webových aplikací ve službě Azure front-dveří najdete v tématu [Služba Firewall webových aplikací ve službě Azure front-dveří](./afds/afds-overview.md).

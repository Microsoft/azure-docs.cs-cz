---
title: Přehled přesměrování pro Azure Application Gateway
description: Přečtěte si o schopnostech přesměrování v Azure Application Gateway pro přesměrování provozu přijatého na jednom naslouchací službě do jiného naslouchacího procesu nebo na externí Web.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: conceptual
ms.date: 11/16/2019
ms.author: amsriva
ms.openlocfilehash: fb01d5a4923410f693b682d66be8d5d09f9019d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "90561574"
---
# <a name="application-gateway-redirect-overview"></a>Přehled přesměrování Application Gateway

K přesměrování provozu můžete použít Aplikační bránu.  Obsahuje obecný mechanismus přesměrování, který umožňuje přesměrování provozu přijatého v jednom naslouchacím procesu do jiného naslouchacího procesu nebo na externí web. Tato funkce zjednodušuje konfiguraci aplikace, optimalizuje využití prostředků a podporuje nové scénáře přesměrování včetně globálního přesměrování a přesměrování na základě cest.

Běžný scénář přesměrování pro mnoho webových aplikací je podporovat přesměrování automatického HTTP na HTTPS, aby se zajistilo, že veškerá komunikace mezi aplikací a jejími uživateli probíhá přes šifrovanou cestu. V minulosti zákazníci používali techniky, jako je vytvoření vyhrazeného fondu back-end, jehož jediným účelem je přesměrování požadavků, které přijímá v HTTP na HTTPS. Díky podpoře přesměrování v Application Gateway můžete to provést jednoduše přidáním nové konfigurace přesměrování do pravidla směrování a zadáním dalšího naslouchacího procesu s protokolem HTTPS jako cílový naslouchací proces.

Podporovány jsou následující typy přesměrování:

- 301 Trvalé přesměrování
- 302 Nalezeno
- 303 viz další
- 307 dočasné přesměrování

Podpora přesměrování Application Gateway nabízí následující možnosti:

-  **Globální přesměrování**

   Přesměrování z jednoho naslouchacího procesu na jiný naslouchací proces v bráně. To umožňuje přesměrování z HTTP na HTTPS na webu.
- **Přesměrování na základě cest**

   Tento typ přesměrování povoluje přesměrování protokolu HTTP na HTTPS pouze v určité oblasti lokality, například v oblasti nákupního košíku označeného/Cart/*.
- **Přesměrovat na externí web**

![Diagram znázorňuje uživatele a aplikační bránu a připojení mezi nimi, včetně odemčené šipky H T T + Red, která není povolená, 301 přímá červená šipka a uzamčené H T T P S zelenou šipkou.](./media/redirect-overview/redirect.png)

V důsledku této změny musí zákazníci vytvořit nový objekt konfigurace přesměrování, který určuje cílový naslouchací proces nebo externí web, na který je požadováno přesměrování. Konfigurační prvek také podporuje možnosti, které umožňují připojení cesty URI a řetězce dotazu k přesměrované adrese URL. Můžete také zvolit typ přesměrování. Po vytvoření se tato konfigurace přesměrování připojí ke zdrojovému naslouchacího procesu prostřednictvím nového pravidla. Při použití základního pravidla je konfigurace přesměrování přidružená ke zdrojovému naslouchacího procesu a je globální přesměrování. Při použití pravidla založeného na cestách je konfigurace přesměrování definována v mapě cesty URL. Proto se vztahuje pouze na konkrétní oblast cesty lokality.

### <a name="next-steps"></a>Další kroky

[Konfigurace přesměrování adresy URL ve aplikační bráně](tutorial-url-redirect-powershell.md)

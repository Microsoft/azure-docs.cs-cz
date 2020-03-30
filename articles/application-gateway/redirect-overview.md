---
title: Přehled přesměrování pro Azure Application Gateway
description: Další informace o funkci přesměrování v Azure Application Gateway přesměrovat provoz přijatý na jeden naslouchací proces na jiný naslouchací proces nebo na externí web.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 11/16/2019
ms.author: amsriva
ms.openlocfilehash: 5943d8aad4d5dd0d981fae9b2325dd3fc75b31e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74129880"
---
# <a name="application-gateway-redirect-overview"></a>Přehled přesměrování brány aplikace

K přesměrování provozu můžete použít aplikační bránu.  Obsahuje obecný mechanismus přesměrování, který umožňuje přesměrování provozu přijatého v jednom naslouchacím procesu do jiného naslouchacího procesu nebo na externí web. To zjednodušuje konfiguraci aplikace, optimalizuje využití prostředků a podporuje nové scénáře přesměrování, včetně globálního přesměrování a přesměrování založeného na cestě.

Běžným scénářem přesměrování pro mnoho webových aplikací je podpora automatického přesměrování http na https, aby byla zajištěna veškerá komunikace mezi aplikací a jejími uživateli přes šifrovanou cestu. V minulosti zákazníci používali techniky, jako je například vytvoření vyhrazeného back-endového fondu, jehož jediným účelem je přesměrovat požadavky, které obdrží v protokolu HTTP na protokol HTTPS. S podporou přesměrování v application gateway, můžete to provést jednoduše přidáním nové konfigurace přesměrování do pravidla směrování a určením jiného naslouchacího procesu s protokolem HTTPS jako cílový mstivým procesem.

Podporovány jsou následující typy přesměrování:

- 301 Trvalé přesměrování
- 302 Nalezeno
- 303 Zobrazit ostatní
- 307 Dočasné přesměrování

Podpora přesměrování Application Gateway nabízí následující možnosti:

-  **Globální přesměrování**

   Přesměruje z jednoho naslouchací proces na jiný naslouchací proces v bráně. To umožňuje přesměrování z HTTP na HTTPS na webu.
- **Přesměrování založené na cestě**

   Tento typ přesměrování umožňuje přesměrování HTTP na HTTPS pouze v určité oblasti webu, například oblast nákupního košíku označenou /cart/*.
- **Přesměrovat na externí web**

![Přesměrování](./media/redirect-overview/redirect.png)

Při této změně musí zákazníci vytvořit nový objekt konfigurace přesměrování, který určuje cílový naslouchací proces nebo externí web, do kterého je požadováno přesměrování. Konfigurační prvek také podporuje možnosti, které umožňují připojení cesty URI a řetězce dotazu k přesměrované adrese URL. Můžete také zvolit typ přesměrování. Po vytvoření je tato konfigurace přesměrování připojena ke zdrojovému naslouchací procesu prostřednictvím nového pravidla. Při použití základního pravidla je konfigurace přesměrování přidružena ke zdrojovému naslouchací procesu a je globálním přesměrováním. Při použití pravidla založeného na cestě je konfigurace přesměrování definována na mapě cesty URL. Takže se vztahuje pouze na konkrétní oblast cesty webu.

### <a name="next-steps"></a>Další kroky

[Konfigurace přesměrování adresy URL v aplikační bráně](tutorial-url-redirect-powershell.md)

---
title: Přehled přesměrování pro službu Azure Application Gateway
description: Další informace o funkci přesměrování ve službě Azure Application Gateway
services: application-gateway
documentationcenter: na
author: amsriva
manager: jpconnock
tags: azure-resource-manager
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 3/19/2018
ms.author: amsriva
ms.openlocfilehash: 8e88e0e11b3ccab7cc2c68b2617df2d588680780
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60715792"
---
# <a name="application-gateway-redirect-overview"></a>Přehled služby Application Gateway přesměrování

Služba application gateway můžete použít k přesměrování provozu.  Obsahuje obecný mechanismus přesměrování, který umožňuje přesměrování provozu přijatého v jednom naslouchacím procesu do jiného naslouchacího procesu nebo na externí web. To zjednodušuje konfiguraci aplikací, optimalizuje využití prostředků a podporuje nové scénáře přesměrování, včetně globální a na základě cest přesměrování.

Běžný scénář přesměrování pro mnoho webových aplikací je podpora automatického HTTP do HTTPS přesměrování zajistit, že se že veškerá komunikace mezi aplikací a jejími uživateli probíhá přes šifrované cestu. V minulosti zákazníci využili techniky, jako je například vytváření vyhrazenou back-endový fond, jehož jediným účelem je pro přesměrování požadavků, které obdrží na protokolu HTTP na HTTPS. S podporou přesměrování ve službě Application Gateway můžete to provést jednoduše tak, že přidává se nová konfigurace přesměrování pro směrování pravidlo a zadání jiné naslouchací proces s protokolem HTTPS jako cílový naslouchací proces.

Jsou podporovány následující typy přesměrování:

- 301 Trvalé přesměrování
- 302 Found
- 303 zobrazit jiné
- 307 Dočasné přesměrování

Podpora přesměrování Application Gateway nabízí následující možnosti:

-  **Globální přesměrování**

   Provede přesměrování z jeden naslouchací proces jiný naslouchací proces na bráně. To umožňuje přesměrování z HTTP na HTTPS na webu.
- **Přesměrování na základě cest**

   Tento typ přesměrování umožňuje HTTP na HTTPS přesměrování pouze na určitém webovém serveru oblast, třeba nákupní košík oblasti udávají/košík / *.
- **Přesměrování na externí web**

![přesměrování](./media/redirect-overview/redirect.png)

Díky této změně zákazníci musí vytvořit nový objekt konfigurace přesměrování, který určuje cílový naslouchací proces nebo externí web, ke kterému je žádoucí přesměrování. Element konfigurace také podporuje možnosti, jak povolit připojení řetězec identifikátoru URI cestu a dotaz na přesměrované adresu URL. Můžete také zvolit typ přesměrování. Po vytvoření této konfigurace přesměrování je připojen k zdroj naslouchací proces prostřednictvím nové pravidlo. Při použití základního pravidla, konfigurace přesměrování je přidružený zdroj naslouchací proces a je globální přesměrování. Při použití pravidla na základě cest, přesměrování konfigurace je definována v mapování cestu adresy URL. To platí jen pro konkrétní cesty oblasti lokality.

### <a name="next-steps"></a>Další postup

[Nakonfigurovat přesměrování adresy URL ve službě application gateway](tutorial-url-redirect-powershell.md)

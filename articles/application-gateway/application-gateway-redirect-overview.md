---
title: Přehled přesměrování pro službu Azure Application Gateway | Dokumentace Microsoftu
description: Další informace o funkci přesměrování ve službě Azure Application Gateway
services: application-gateway
documentationcenter: na
author: amsriva
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/18/2017
ms.author: amsriva
ms.openlocfilehash: d05d509b67fd26c958e0e2fa2bbd877db26e6521
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2019
ms.locfileid: "54232205"
---
# <a name="application-gateway-redirect-overview"></a>Přehled služby Application Gateway přesměrování

Je běžným scénářem, mnoho webových aplikací pro podporu automatického HTTP do HTTPS přesměrování zajistit, že se že veškerá komunikace mezi aplikací a jejími uživateli probíhá přes šifrované cestu. V minulosti zákazníci využili techniky, jako je například vytváření vyhrazenou back-endový fond, jehož jediným účelem je pro přesměrování požadavků, které obdrží na protokolu HTTP na HTTPS.  Služba Application gateway nyní podporuje možnost přesměrovat provoz ve službě Application Gateway. To zjednodušuje konfiguraci aplikací, optimalizuje využití prostředků a podporuje nové scénáře přesměrování, včetně globální a na základě cest přesměrování. Podpora služby Application Gateway přesměrování není omezený na HTTP -> HTTPS přesměrování samostatně. Toto je obecný přesměrování mechanismus, který umožňuje přesměrování provozu, paket jeden naslouchací proces na jiné naslouchací proces ve službě Application Gateway. Také podporuje přesměrování na externí web. Podpora přesměrování Application Gateway nabízí následující možnosti:

1. Globální přesměrování z jeden naslouchací proces na jiné naslouchací proces na bráně. To umožňuje přesměrování z HTTP na HTTPS na webu.
2. Přesměrování na základě cesty. Tento typ přesměrování umožňuje HTTP na HTTPS přesměrování pouze na určitém webovém serveru oblast, třeba nákupní košík oblasti udávají/košík / *.
3. Přesměrování na externí web.

![přesměrování](./media/application-gateway-redirect-overview/redirect.png)

Díky této změně byste museli vytvořit nový objekt konfigurace přesměrování, který určuje cílový naslouchací proces nebo externí web, ke kterému je žádoucí přesměrování. Element konfigurace také podporuje možnosti, jak povolit připojení řetězec identifikátoru URI cestu a dotaz na přesměrované adresu URL. Zákazníci můžou vybrat, zda přesměrování je dočasný (stavový kód HTTP 302) nebo trvalé přesměrování (kód stavu protokolu HTTP 301). Po vytvoření této konfigurace přesměrování je připojen k naslouchacímu procesu zdroj prostřednictvím nové pravidlo. Při použití základního pravidla, konfigurace přesměrování je přidružený zdroj naslouchací proces a je globální přesměrování. Při použití pravidla na základě cest konfigurace přesměrování je definován na mapě cestu adresy URL a tedy platí jenom pro konkrétní cesty oblasti lokality.

### <a name="next-steps"></a>Další postup

[Konfigurace HTTP na HTTPS přesměrování ve službě application gateway](redirect-http-to-https-portal.md)

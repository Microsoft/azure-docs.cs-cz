---
title: Přehled přesměrování Azure Application Gateway.
description: Další informace o funkci přesměrování v Azure Application Gateway
services: application-gateway
documentationcenter: na
author: amsriva
manager: timlt
tags: azure-resource-manager
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 3/19/2018
ms.author: amsriva
ms.openlocfilehash: f503998668f35ec5bc17db74ee74c4a26465ab04
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="application-gateway-redirect-overview"></a>Přehled přesměrování brány aplikace

Běžný scénář pro mnoho webových aplikací je podpora automatického HTTP do HTTPS přesměrování zajistit, že veškerou komunikaci mezi aplikací a svým uživatelům probíhá přes šifrované cestu. V minulosti zákazníci použili techniky, jako je například vytváření vyhrazené back-end fondu, jehož jediným účelem je pro přesměrování požadavků, které obdrží na protokolu HTTP do HTTPS.

Aplikační brána teď podporuje možnost přesměrovat provoz na bráně. To zjednodušuje konfiguraci aplikací, optimalizuje využití prostředků a podporuje nové scénáře přesměrování včetně globální a na základě cestu přesměrování. Podpora aplikací brány přesměrování není omezeno na HTTP -> HTTPS přesměrování samostatně. Obsahuje obecné přesměrování mechanismus, který umožňuje přesměrování provozu přijme jeden naslouchací proces pro jiné naslouchací proces ve Application Gateway. Přesměrování na externího webu je také podporována.

Podpora přesměrování brány aplikace nabízí následující možnosti:

-  **Globální přesměrování**

   Provede přesměrování z jeden naslouchací proces jiný naslouchací proces na bráně. To umožňuje HTTP do HTTPS přesměrování na lokalitu.
- **Na základě cesta přesměrování**

   Tento typ přesměrování umožňuje HTTP do HTTPS přesměrování pouze v konkrétní lokalitě oblast, třeba nákupní košík oblasti odlišené/košíku / *.
- **Přesměrování na externí web**

![přesměrování](./media/redirect-overview/redirect.png)

Díky této změně zákazníci muset vytvořit nový objekt přesměrování konfigurace, který určuje naslouchací proces cílové nebo externí web, ke kterému se požaduje přesměrování. Konfigurační element také podporuje možnosti Povolit připojení řetězce identifikátoru URI cesta a dotaz k přesměrované adresy URL. Můžete také, zda je přesměrování dočasného (kód stavu HTTP 302) nebo trvalé přesměrování (kód stavu protokolu HTTP 301). Po vytvoření této konfigurace přesměrování je připojený k zdroj naslouchací proces prostřednictvím nové pravidlo. Pokud používáte základní pravidlo, konfigurace přesměrování je přidružený naslouchací proces zdroje a globální přesměrování. Při použití pravidla na základě cesty konfigurace přesměrování je definována v mapě cestu adresy URL. Proto vztahuje se pouze konkrétní cestu oblasti lokality.

### <a name="next-steps"></a>Další postup

[Konfigurace adresy URL přesměrování na služby application gateway](tutorial-url-redirect-powershell.md)

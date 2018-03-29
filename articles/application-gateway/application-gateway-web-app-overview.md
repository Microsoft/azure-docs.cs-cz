---
title: Přehled back-endů s více tenanty s použitím služby Azure Application Gateway | Dokumentace Microsoftu
description: Tato stránka poskytuje přehled podpory služby Application Gateway pro back-endy s více tenanty.
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: ''
ms.service: application-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: davidmu
ms.openlocfilehash: f5ec916fcf45734ab85751e749bd6bb312f05b1a
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2018
---
# <a name="application-gateway-support-for-multi-tenant-back-ends"></a>Podpora služby Application Gateway pro back-endy s více tenanty

Azure Application Gateway jako součást fondů back-end podporuje škálovací sady virtuálních počítačů, síťová rozhraní, veřejné a privátní IP adresy nebo plně kvalifikovaný název domény. Ve výchozím nastavení služba Application Gateway nemění hlavičku hostitele příchozího požadavku HTTP od klienta a nezměněnou hlavičku posílá na back-end. Existuje řada služeb jako [Azure Web Apps](../app-service/app-service-web-overview.md), které jsou ze své podstaty s více tenanty a při překládání na správný koncový bod se spoléhají na konkrétní hlavičku hostitele nebo rozšíření SNI. Application Gateway nyní podporuje možnost uživatelů přepsat hlavičku hostitele příchozího požadavku HTTP na základě nastavení HTTP na straně back-endu. Tato schopnost umožňuje podporu back-endů s více tenanty Azure Web Apps a API Management. Tato schopnost je dostupná pro standardní skladové položky i pro skladové položky WAF. Podpora back-endu s více tenanty funguje také s ukončováním protokolu SSL a scénáři koncového šifrování protokolu SSL.

![scénář webové aplikace](./media/application-gateway-web-app-overview/scenario.png)

Možnost určit přepsání hostitele se definuje v nastavení HTTP a během vytváření pravidla je možné ji použít na jakýkoli fond back-end. Back-endy s více tenanty podporují následující dva způsoby přepsání hlavičky hostitele a rozšíření SNI.

1. Možnost nastavit název hostitele na pevnou hodnotu v nastavení HTTP. Tato možnost zajišťuje, že se hlavička hostitele přepíše na tuto hodnotu u veškerého provozu přicházejícího do fondu back-end, na který se nastavení HTTP použilo. Při použití koncového šifrování protokolu SSL se přepsaný název hostitele použije v rozšíření SNI. Tato možnost umožňuje scénáře, kdy farma fondu back-end očekává hlavičku hostitele, která je jiná než příchozí zákaznická hlavička hostitele.

2. Schopnost odvodit název hostitele z IP adresy nebo plně kvalifikovaného názvu domény členů fondu back-end. Nastavení HTTP nabízí také možnost vybrat název hostitele z plně kvalifikovaného názvu domény člena fondu back-end, pokud je nakonfigurovaná možnost odvodit název hostitele z jednotlivých členů fondu back-end. Při použití koncového šifrování protokolu SSL se tento název hostitele odvodí z plně kvalifikovaného názvu domény a použije v rozšíření SNI. Tato možnost umožňuje scénáře, kdy může mít fond back-end dvě nebo více služeb PaaS s více tenanty, jako jsou například webové aplikace Azure, a hlavička hostitele požadavku každého člena obsahuje název hostitele odvozený z jeho plně kvalifikovaného názvu domény.

> [!NOTE]
> V obou předchozích případech má nastavení vliv pouze na chování živého provozu a ne na chování sondy stavu. Vlastní sondy už podporují možnost zadat hlavičku hostitele v konfiguraci sondy. Vlastní sondy nyní podporují také možnost odvodit chování hlavičky hostitele z aktuálně nakonfigurovaného nastavení HTTP. Tuto konfiguraci je možné zadat pomocí parametru `PickHostNameFromBackendHttpSettings` v konfiguraci sondy. Aby fungovala funkce koncového šifrování, sondu i nastavení HTTP je potřeba upravit tak, aby odrážely správnou konfiguraci.

Díky této schopnosti můžou zákazníci zadat možnosti v nastavení HTTP a vlastních sondách na odpovídající konfiguraci. Toto nastavení se pak pomocí pravidla naváže na naslouchací proces a fond back-end.

## <a name="next-steps"></a>Další kroky

Zjistěte, jak nastavit službu Application Gateway s webovou aplikací jako členem fondu back-end, v tématu [Konfigurace webových aplikací App Service pomocí služby Application Gateway](application-gateway-web-app-powershell.md).

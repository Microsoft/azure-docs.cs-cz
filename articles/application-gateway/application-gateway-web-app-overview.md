---
title: Přehled víceklientské zpět končí Azure Application Gateway
description: Tato stránka poskytuje přehled podpory služby Application Gateway pro back-endy s více tenanty.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 8/1/2018
ms.author: victorh
ms.openlocfilehash: 8e5f48e42a2a677622dae0b733f9d5af484d1c51
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53605797"
---
# <a name="application-gateway-support-for-multi-tenant-back-ends"></a>Podpora služby Application Gateway pro back-endy s více tenanty

Azure Application Gateway podporuje škálovací sady virtuálních počítačů, síťová rozhraní, veřejné a privátní IP adresu nebo plně kvalifikovaný název (plně kvalifikovaný název domény) jako součást své fondy back-end. Ve výchozím nastavení služba Application Gateway nemění hlavičku hostitele příchozího požadavku HTTP od klienta a nezměněnou hlavičku posílá na back-end. Existuje řada služeb jako [služby Azure App Service](../app-service/overview.md) , které jsou ze své podstaty s více tenanty a Spolehněte se na konkrétní hlavičku hostitele nebo rozšíření SNI překládání na správný koncový bod. Služba Application Gateway nyní podporuje možnost uživatelů přepsat na základě nastavení HTTP back-end hlavičku hostitele příchozího požadavku HTTP. Tato schopnost umožňuje podporu back-endů s více tenanty Azure Web Apps a API Management. Tato schopnost je dostupná pro standardní skladové položky i pro skladové položky WAF. Podpora více tenantů back endu funguje také s ukončováním protokolu SSL a koncového SSL scénáře.

> [!NOTE]
> Instalace certifikátu ověřování se nevyžaduje pro důvěryhodného služby Azure, jako je Azure Web Apps.

![scénář webové aplikace](./media/application-gateway-web-app-overview/scenario.png)

Možnost určit přepsání hostitele je definován v nastavení HTTP a lze použít libovolný back endového fondu při vytváření pravidla. Back-endy s více tenanty podporují následující dva způsoby přepsání hlavičky hostitele a rozšíření SNI.

1. Možnost nastavit název hostitele na pevnou hodnotu v nastavení HTTP. Tato funkce zajišťuje, že hlavička hostitele přepíše na tuto hodnotu pro veškerý provoz do back endový fond, ve kterém se nastavení HTTP použilo. Při použití koncového šifrování protokolu SSL se přepsaný název hostitele použije v rozšíření SNI. Tato možnost umožňuje scénáře, kdy farma fondu back-end očekává hlavičku hostitele, který se liší od příchozí zákaznická Hlavička hostitele.

2. Schopnost odvodit název hostitele z IP adresy nebo plně kvalifikovaného názvu domény členů fondu back-end. Nastavení HTTP nabízí také možnost vybrat název hostitele z plně kvalifikovaného názvu domény člena fondu back-end, pokud je nakonfigurovaná možnost odvodit název hostitele z člen typu jednotlivých back endového fondu. Při použití koncového šifrování protokolu SSL se tento název hostitele odvodí z plně kvalifikovaného názvu domény a použije v rozšíření SNI. Tato možnost umožňuje scénáře, kde a back endový fond může obsahovat dva nebo více služeb PaaS více tenantů, jako je webové aplikace Azure a Hlavička hostitele požadavku každého člena obsahuje název hostitele odvozený z jeho plně kvalifikovaný název domény.

> [!NOTE]
> V obou předchozích případech má nastavení vliv pouze na chování živého provozu a ne na chování sondy stavu. Vlastní sondy už podporují možnost zadat hlavičku hostitele v konfiguraci sondy. Vlastní sondy nyní podporují také možnost odvodit chování hlavičky hostitele z aktuálně nakonfigurovaného nastavení HTTP. Tuto konfiguraci je možné zadat pomocí parametru `PickHostNameFromBackendHttpSettings` v konfiguraci sondy. Aby fungovala funkce koncového šifrování, sondu i nastavení HTTP je potřeba upravit tak, aby odrážely správnou konfiguraci.

Díky této schopnosti můžou zákazníci zadat možnosti v nastavení HTTP a vlastních sondách na odpovídající konfiguraci. Toto nastavení se potom váže naslouchací proces a fond back-end pomocí pravidla.

## <a name="next-steps"></a>Další postup

Zjistěte, jak nastavit službu application gateway s webovou aplikací jako členem fondu back-end pomocí navštívit: [Konfigurace webových aplikací služby App Service web apps pomocí služby Application Gateway](application-gateway-web-app-powershell.md)

---
title: Přehled víceklientské back-EndY, jako jsou služby Azure App service pomocí služby Azure Application Gateway
description: Tato stránka poskytuje přehled podpory služby Application Gateway pro back-endy s více tenanty.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 03/18/2019
ms.author: victorh
ms.openlocfilehash: 256fb42be8fec056ed7d10cfc4197a1b5a33fac1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66807180"
---
# <a name="application-gateway-support-for-multi-tenant-back-ends-such-as-app-service"></a>Podpora služby Application Gateway pro více tenantů back skončí, jako je App service

Ve víceklientské architektuře návrhů v webových serverů běží více webů ve stejné instanci serveru web. Názvy hostitelů se používá k rozlišení mezi různými aplikacemi, které jsou hostované. Ve výchozím nastavení služba Application Gateway nemění hlavičku hostitele příchozího požadavku HTTP od klienta a nezměněnou hlavičku posílá na back-end. Toto se dobře hodí u členů fondu back-end, jako jsou síťové karty, škálovacích sad virtuálních počítačů, veřejné IP adresy, interní IP adresy a plně kvalifikovaný název domény jako tyto nespoléhejte na konkrétní hlavičku hostitele nebo rozšíření SNI překládání na správný koncový bod. Existují však mnoho služeb, jako jsou webové aplikace Azure App service a Azure API management, které jsou ze své podstaty s více tenanty a Spolehněte se na konkrétní hlavičku hostitele nebo rozšíření SNI překládání na správný koncový bod. Název DNS aplikace, který je přidružený k službě application gateway název DNS, je obvykle liší od názvu domény služby back-endu. Proto hlavičku hostitele v původní požadavek přijatý službou application gateway není stejný jako název hostitele služby back-endu. Z tohoto důvodu Pokud hlavička hostitele v žádosti z aplikační brány pro back-endu se změní na název hostitele služby back-endu, back-EndY víceklientské nejsou schopni vyřešit požadavek na správný koncový bod. 

Služba Application Gateway nabízí uživatelům možnost přepsat hlavičku hostitele požadavku HTTP v závislosti na názvu hostitele back-endu. Tato schopnost umožňuje podporu back-endů s více tenanty, jako jsou Azure App Service Web Apps a API Management. Tato schopnost je dostupná pro standardní skladové položky i pro skladové položky WAF verze v1 i v2. 

![Přepsání hostitele](./media/application-gateway-web-app-overview/host-override.png)

> [!NOTE]
> To neplatí pro služby Azure App service environment (ASE) vzhledem k tomu, že služba ASE je zdroj vyhrazený na rozdíl od Azure App service, která je prostředek více tenantů.

## <a name="override-host-header-in-the-request"></a>Přepsat hlavičku hostitele v žádosti

Možnost určit přepsání hostitele je definována v [nastavení HTTP](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) a lze použít libovolný back endového fondu při vytváření pravidla. Následující dva způsoby přepsání hlavičky hostitele a rozšíření SNI pro více tenantů back-endů se podporuje:

- Možnost nastavit název hostitele na pevnou hodnotu explicitně zadali v nastavení protokolu HTTP. Tato funkce zajišťuje, že hlavička hostitele přepíše na tuto hodnotu pro veškerý provoz do back endový fond, ve kterém se konkrétní nastavení HTTP použilo. Při použití koncového šifrování protokolu SSL se přepsaný název hostitele použije v rozšíření SNI. Tato možnost umožňuje scénáře, kdy farma fondu back-end očekává hlavičku hostitele, který se liší od příchozí zákaznická Hlavička hostitele.

- Schopnost odvodit název hostitele z IP adresy nebo plně kvalifikovaného názvu domény členů fondu back-end. Nastavení HTTP nabízí také možnost dynamicky vybrat název hostitele z plně kvalifikovaného názvu domény člena fondu back-end, pokud je nakonfigurovaná možnost odvodit název hostitele z člen typu jednotlivých back endového fondu. Při použití koncového šifrování protokolu SSL se tento název hostitele odvodí z plně kvalifikovaného názvu domény a použije v rozšíření SNI. Tato možnost umožňuje scénáře, kde a back endový fond může obsahovat dva nebo více služeb PaaS více tenantů, jako je webové aplikace Azure a Hlavička hostitele požadavku každého člena obsahuje název hostitele odvozený z jeho plně kvalifikovaný název domény. Implementace této situace, používáme přepínače v nastavení protokolu HTTP, volá [vybrat název hostitele z back-endová adresa](https://docs.microsoft.com/azure/application-gateway/configuration-overview#pick-host-name-from-back-end-address) která se dynamicky přepsat hlavičku hostitele v původní požadavek na doporučenou v back-endový fond.  Například pokud váš fond back-end plně kvalifikovaný název domény obsahuje "contoso11.azurewebsites.net" a "contoso22.azurewebsites.net", původní požadavek hlavičku hostitele, což je contoso.com bude být potlačena za účelem contoso11.azurewebsites.net nebo contoso22.azurewebsites.net Když je žádost odeslána odpovídající back-end server. 

  ![scénář webové aplikace](./media/application-gateway-web-app-overview/scenario.png)

Díky této schopnosti můžou zákazníci zadat možnosti v nastavení HTTP a vlastních sondách na odpovídající konfiguraci. Toto nastavení se potom váže naslouchací proces a fond back-end pomocí pravidla.

## <a name="special-considerations"></a>Zvláštní upozornění

### <a name="ssl-termination-and-end-to-end-ssl-with-multi-tenant-services"></a>Ukončení protokolu SSL a koncového šifrování protokolu SSL pomocí služeb s více tenanty

Ukončení protokolu SSL a koncové šifrování protokolu SSL se podporuje s služeb s více tenanty. Pro ukončení protokolu SSL ve službě application gateway certifikát SSL je nadále potřeba přidat k naslouchacímu procesu application gateway. V případě kompletního protokolu SSL, ale Azure pro důvěryhodného služby, například webových aplikací Azure App service web apps nevyžadují, aby na seznam povolených back-endy ve službě application gateway. Proto není nutné přidat žádné ověřovací certifikáty. 

![kompletního protokolu SSL](./media/application-gateway-web-app-overview/end-to-end-ssl.png)

Všimněte si, že na obrázku výše, není potřeba, chcete-li přidat certifikáty pro ověřování při výběru služby App service jako back-endu.

### <a name="health-probe"></a>Sonda stavu

Hlavička hostitele v přepsání **nastavení HTTP** má vliv pouze žádost a směrování. nemá vliv na chování sondy stavu. Aby fungovala funkce koncového šifrování, sondu i nastavení HTTP je potřeba upravit tak, aby odrážely správnou konfiguraci. Kromě toho, že možnost zadat hlavičku hostitele v konfiguraci sondy, vlastní sondy podporují také možnost odvodit hlavičku hostitele z aktuálně nakonfigurovaného nastavení HTTP. Tuto konfiguraci je možné zadat pomocí parametru `PickHostNameFromBackendHttpSettings` v konfiguraci sondy.

### <a name="redirection-to-app-services-url-scenario"></a>Přesměrování na adresu URL scénáře služby App Service

Může existovat scénáře, kde název hostitele v odpovědi ze služby App service může směrovat prohlížeči koncového uživatele *. azurewebsites.net hostname místo domény přidružené k Application Gateway. Tento problém může dojít, když:

- Máte přesměrování nakonfigurované ve službě App Service. Přesměrování může být stejně jednoduché jako přidání koncové lomítko k požadavku.
- Máte ověřování Azure AD, což způsobí, že přesměrování.

Chcete-li vyřešit tyto případy, naleznete v tématu [řešení potíží s chybou služby App service adresy URL přesměrování](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url).

## <a name="next-steps"></a>Další postup

Zjistěte, jak nastavit službu application gateway s aplikaci s více tenanty, jako je například Azure App service webovou aplikaci jako členem fondu back-end tématu [konfigurace služby App Service web apps s Application Gateway](https://docs.microsoft.com/azure/application-gateway/create-web-app)

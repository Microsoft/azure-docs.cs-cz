---
title: Víceklientské back-endy
titleSuffix: Azure Application Gateway
description: Tato stránka poskytuje přehled podpory služby Application Gateway pro back-endy s více tenanty.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: efa2885ce0534c5d78bb08bbf24da59850f6ea22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74075177"
---
# <a name="application-gateway-support-for-multi-tenant-back-ends-such-as-app-service"></a>Podpora aplikační brány pro víceklientské back-endy, jako je například služba App

Ve víceklientských architektonických návrzích na webových serverech je na stejné instanci webového serveru spuštěno více webů. Názvy hostitelů se používají k rozlišení mezi různými aplikacemi, které jsou hostovány. Ve výchozím nastavení služba Application Gateway nemění hlavičku hostitele příchozího požadavku HTTP od klienta a nezměněnou hlavičku posílá na back-end. To funguje dobře pro členy back-endového fondu, jako jsou síťové karty, škálovací sady virtuálních počítačů, veřejné IP adresy, interní IP adresy a hlavní název domény, protože tyto nespoléhají na konkrétní hlavičku hostitele nebo rozšíření SNI k vyřešení správného koncového bodu. Existuje však mnoho služeb, jako jsou webové aplikace služby Azure App service a správa rozhraní API Azure, které jsou víceklientské povahy a spoléhají na konkrétní hlavičku hostitele nebo rozšíření SNI, které se vyřeší na správný koncový bod. Název DNS aplikace, který je zase název DNS přidružený k aplikační bráně, se obvykle liší od názvu domény back-endové služby. Hlavička hostitele v původním požadavku přijatém aplikační bránou proto není stejná jako název hostitele back-endové služby. Z tohoto důvodu, pokud záhlaví hostitele v požadavku z aplikační brány do back-endu je změněn na název hostitele back-endové služby, víceklientské back-endy nejsou schopni přeložit požadavek na správný koncový bod. 

Služba Application Gateway nabízí uživatelům možnost přepsat hlavičku hostitele požadavku HTTP v závislosti na názvu hostitele back-endu. Tato schopnost umožňuje podporu back-endů s více tenanty, jako jsou Azure App Service Web Apps a API Management. Tato schopnost je dostupná pro standardní skladové položky i pro skladové položky WAF verze v1 i v2. 

![přepsání hostitele](./media/application-gateway-web-app-overview/host-override.png)

> [!NOTE]
> To se nevztahuje na prostředí služby Azure App (ASE), protože služba ASE je vyhrazený prostředek na rozdíl od služby Azure App, která je prostředek více klientů.

## <a name="override-host-header-in-the-request"></a>Přepsat záhlaví hostitele v požadavku

Možnost zadat přepsání hostitele je definována v [nastavení protokolu HTTP](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) a lze ji použít pro libovolný back-endový fond během vytváření pravidel. Následující dva způsoby přepsání hlavičky hostitele a rozšíření SNI pro víceklientské back-endy je podporována:

- Možnost nastavit název hostitele na pevnou hodnotu explicitně zadanou v nastavení protokolu HTTP. Tato funkce zajišťuje, že záhlaví hostitele je přepsána na tuto hodnotu pro veškerý provoz do back-endového fondu, kde jsou použita konkrétní nastavení HTTP. Při použití koncového šifrování protokolu SSL se přepsaný název hostitele použije v rozšíření SNI. Tato funkce umožňuje scénáře, kde farma back-endového fondu očekává hlavičku hostitele, která se liší od hlavičky hostitele příchozího zákazníka.

- Možnost odvodit název hostitele z IP nebo ReQDN členů back-endového fondu. Nastavení protokolu HTTP také poskytují možnost dynamicky vybrat název hostitele z fQDN člena back-endového fondu, pokud je nakonfigurován s možností odvodit název hostitele od jednotlivého člena fondu back-end. Při použití koncového šifrování protokolu SSL se tento název hostitele odvodí z plně kvalifikovaného názvu domény a použije v rozšíření SNI. Tato funkce umožňuje scénáře, kde back-endový fond může mít dva nebo více víceklientských služeb PaaS, jako jsou webové aplikace Azure a hlavička hostitele požadavku pro každého člena obsahuje název hostitele odvozený z jeho hlavního názvu sítě. Pro implementaci tohoto scénáře používáme přepínač v nastavení HTTP s názvem [Pick hostname z back-endové adresy,](https://docs.microsoft.com/azure/application-gateway/configuration-overview#pick-host-name-from-back-end-address) který dynamicky přepíše hlavičku hostitele v původním požadavku na ten, který je uveden v back-endovém fondu.  Například pokud váš fond back-endu, který je v ykonané masy, obsahuje "contoso11.azurewebsites.net" a "contoso22.azurewebsites.net", bude záhlaví hostitele původní žádosti, které je contoso.com, přepsáno na contoso11.azurewebsites.net nebo contoso22.azurewebsites.net při odeslání požadavku na příslušný server back-end. 

  ![scénář webové aplikace](./media/application-gateway-web-app-overview/scenario.png)

Díky této schopnosti můžou zákazníci zadat možnosti v nastavení HTTP a vlastních sondách na odpovídající konfiguraci. Toto nastavení je pak vázána na naslouchací proces a fond back-end pomocí pravidla.

## <a name="special-considerations"></a>Zvláštní aspekty

### <a name="ssl-termination-and-end-to-end-ssl-with-multi-tenant-services"></a>Ukončení SSL a ukončení ssl s víceklientské služby

SSL ukončení a end-to-end SSL šifrování je podporováno víceklientské služby. Pro ukončení SSL v bráně aplikace ssl certifikát i nadále nutné přidat do naslouchací proces brány aplikace. Však v případě, že konec do konce SSL, důvěryhodné služby Azure, jako jsou webové aplikace služby Azure App služby nevyžadují whitelisting back-endy v aplikační bráně. Proto není nutné přidávat žádné ověřovací certifikáty. 

![ssl od konce ke konci](./media/application-gateway-web-app-overview/end-to-end-ssl.png)

Všimněte si, že ve výše uvedeném obrázku není žádný požadavek na přidání ověřovacích certifikátů, když je služba App vybrána jako back-end.

### <a name="health-probe"></a>Sonda stavu

Přepsání hlavičky hostitele v **nastavení protokolu HTTP** ovlivní pouze požadavek a jeho směrování. nemá vliv na chování sondy stavu. Aby fungovala funkce koncového šifrování, sondu i nastavení HTTP je potřeba upravit tak, aby odrážely správnou konfiguraci. Kromě toho, že poskytují možnost zadat hlavičku hostitele v konfiguraci sondy, vlastní sondy také podporují možnost odvodit hlavičku hostitele z aktuálně nakonfigurovaného nastavení HTTP. Tuto konfiguraci je možné zadat pomocí parametru `PickHostNameFromBackendHttpSettings` v konfiguraci sondy.

### <a name="redirection-to-app-services-url-scenario"></a>Přesměrování na scénář adresy URL služby App Service

Mohou existovat scénáře, kde název hostitele v odpovědi ze služby App může nasměrovat prohlížeč koncového uživatele na název hostitele *.azurewebsites.net namísto domény přidružené k aplikační bráně. K tomuto problému může dojít, když:

- Máte přesměrování nakonfigurované ve službě App Service. Přesměrování může být stejně jednoduché jako přidání koncového lomítka do požadavku.
- Máte ověřování Azure AD, které způsobuje přesměrování.

Tyto případy můžete [vyřešit v tématu Poradce při potížích s přesměrováním na problém s adresou URL služby App service](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url).

## <a name="next-steps"></a>Další kroky

Zjistěte, jak nastavit aplikační bránu s víceklientovou aplikací, jako je webová aplikace Služby Azure App Service jako člena fondu back-end, a to tak, že navštívíte [Konfigurovat webové aplikace služby App Service pomocí application gateway](https://docs.microsoft.com/azure/application-gateway/configure-web-app-portal)

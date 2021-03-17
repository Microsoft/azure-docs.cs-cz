---
title: Back-endy s více klienty
titleSuffix: Azure Application Gateway
description: Tato stránka poskytuje přehled podpory služby Application Gateway pro back-endy s více tenanty.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 06/09/2020
ms.author: victorh
ms.openlocfilehash: fe6ea6f348d796962141bd39ff858d891a29a2f6
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397684"
---
# <a name="application-gateway-support-for-multi-tenant-back-ends-such-as-app-service"></a>Podpora Application Gateway pro více tenantů back-end, jako je App Service

V návrzích architektury víceklientské architektury na webových serverech běží na stejné instanci webového serveru víc webů. Názvy hostitelů slouží k odlišení různých aplikací, které jsou hostovány. Ve výchozím nastavení služba Application Gateway nemění hlavičku hostitele příchozího požadavku HTTP od klienta a nezměněnou hlavičku posílá na back-end. Tato možnost funguje dobře pro členy fondu back-end, jako jsou síťové adaptéry, virtuální počítače a služby škálování virtuálních počítačů, veřejné IP adresy, interní IP adresy a plně kvalifikovaný název domény, protože se nespoléhají na konkrétní hlavičku hostitele nebo rozšíření SNI, aby se vyřešilo na správný koncový bod. Existuje však mnoho služeb, jako je například služba Azure App Service Web Apps a Azure API Management, které jsou ve více tenantůch a využívají konkrétní hlavičku hostitele nebo rozšíření SNI k vyřešení správného koncového bodu. Název DNS aplikace, který je zase název DNS přidružený k aplikační bráně, se obvykle liší od názvu domény služby back-end. Proto Hlavička hostitele v původní žádosti přijatá aplikační bránou není stejná jako název hostitele back-end služby. Z tohoto důvodu se v případě, že Hlavička hostitele v požadavku od služby Application Gateway do back-endu změní na název hostitele back-end služby, nedokáže aplikace back-end s více klienty přeložit požadavek na správný koncový bod. 

Služba Application Gateway nabízí uživatelům možnost přepsat hlavičku hostitele požadavku HTTP v závislosti na názvu hostitele back-endu. Tato schopnost umožňuje podporu back-endů s více tenanty, jako jsou Azure App Service Web Apps a API Management. Tato schopnost je dostupná pro standardní skladové položky i pro skladové položky WAF verze v1 i v2. 

![přepsání hostitele](./media/application-gateway-web-app-overview/host-override.png)

> [!NOTE]
> Neplatí to pro Azure App Service Environment (pomocného mechanismu), protože pomocného prostředku je na rozdíl od služby Azure App Service, která je prostředkem pro více tenantů.

## <a name="override-host-header-in-the-request"></a>Přepsat hlavičku hostitele v žádosti

Možnost zadat přepsání hostitele je definovaná v [Nastavení http](./configuration-overview.md#http-settings) a dá se použít pro libovolný fond back-end během vytváření pravidla. Podporovány jsou následující dva způsoby přepsání hlaviček hostitele a rozšíření SNI pro back-end s více klienty:

- Možnost nastavit název hostitele na pevnou hodnotu explicitně zadaný v nastavení HTTP. Tato možnost zajistí, že se hlavička hostitele přepíše na tuto hodnotu pro veškerý provoz do fondu back-end, kde se používají konkrétní nastavení protokolu HTTP. Při použití koncového TLS se tento přepsaný název hostitele používá v rozšíření SNI. Tato možnost umožňuje scénářům, kdy skupina back-end fondu očekává hlavičku hostitele, která se liší od hlavičky příchozího hostitele zákazníka.

- Schopnost odvodit název hostitele z IP adresy nebo plně kvalifikovaného názvu domény členů fondu back-end. Nastavení HTTP taky nabízí možnost dynamicky vybrat název hostitele z plně kvalifikovaného názvu domény člena fondu back-endu, pokud je nakonfigurovaný parametr s možností odvodit název hostitele od jednotlivých členů fondu back-end. Při použití koncového šifrování TLS je tento název hostitele odvozený od plně kvalifikovaného názvu domény a používá se v rozšíření SNI. Tato možnost umožňuje scénářům, kdy fond back-endu může mít dvě nebo víc PaaS služeb pro více tenantů, jako jsou webové aplikace Azure, a záhlaví hostitele žádosti pro každého člena obsahuje název hostitele odvozený z jeho plně kvalifikovaného názvu domény. V případě implementace tohoto scénáře používáme v nastavení protokolu HTTP přepínač s názvem [Vybrat název hostitele z back-endu adresy](./configuration-http-settings.md#pick-host-name-from-back-end-address) , ve kterém bude dynamicky přepsána Hlavička hostitele v původní žádosti na tu, která je uvedena ve fondu back-end.  Pokud Váš plně kvalifikovaný název domény ve fondu back-end obsahuje například "contoso11.azurewebsites.net" a "contoso22.azurewebsites.net", Hlavička hostitele původní žádosti, která je contoso.com, bude přepsána na contoso11.azurewebsites.net nebo contoso22.azurewebsites.net při odeslání požadavku na příslušný back-end Server. 

  ![scénář webové aplikace](./media/application-gateway-web-app-overview/scenario.png)

Díky této schopnosti můžou zákazníci zadat možnosti v nastavení HTTP a vlastních sondách na odpovídající konfiguraci. Toto nastavení se pak váže na naslouchací proces a fond back-end pomocí pravidla.

## <a name="special-considerations"></a>Zvláštní požadavky

### <a name="tls-termination-and-end-to-end-tls-with-multi-tenant-services"></a>Ukončení protokolu TLS a koncové šifrování TLS pomocí víceklientské služby

U více tenantů se podporuje ukončení protokolu TLS i koncové koncové šifrování TLS. V případě ukončení protokolu TLS ve službě Application Gateway bude certifikát TLS i nadále vyžadován k přidání do naslouchacího procesu služby Application Gateway. V případě koncového protokolu TLS ale důvěryhodné služby Azure, jako například Azure App Service Web Apps, nevyžadují povolení back-endu ve službě Application Gateway. Proto není nutné přidávat žádné ověřovací certifikáty. 

![koncové šifrování TLS](./media/application-gateway-web-app-overview/end-to-end-ssl.png)

Všimněte si, že ve výše uvedeném obrázku není nutné přidávat ověřovací certifikáty, pokud je služba App Service vybrána jako back-end.

### <a name="health-probe"></a>Sonda stavu

Přepsání hlavičky hostitele v **Nastavení http** ovlivní pouze požadavek a jeho směrování. nemá vliv na chování sondy stavu. Aby fungovala funkce koncového šifrování, sondu i nastavení HTTP je potřeba upravit tak, aby odrážely správnou konfiguraci. Kromě toho, že možnost zadat hlavičku hostitele v konfiguraci sondy, vlastní sondy také podporují schopnost odvodit hlavičku hostitele z aktuálně nakonfigurovaných nastavení HTTP. Tuto konfiguraci je možné zadat pomocí parametru `PickHostNameFromBackendHttpSettings` v konfiguraci sondy.

### <a name="redirection-to-app-services-url-scenario"></a>Přesměrování na scénář URL App Service

Můžou nastat scénáře, kdy název hostitele v odpovědi ze služby App Service může přesměrovat prohlížeč koncových uživatelů na název hostitele *. azurewebsites.net namísto domény přidružené k Application Gateway. K tomuto problému může dojít v těchto případech:

- Nakonfigurovali jste přesměrování na App Service. Přesměrování může být stejně jednoduché jako přidání koncového lomítka do požadavku.
- Máte ověřování Azure AD, které způsobuje přesměrování.

Pokud chcete tyto případy vyřešit, přečtěte si téma [řešení potíží s přesměrování do problému s adresou URL služby App Service](./troubleshoot-app-service-redirection-app-service-url.md).

## <a name="next-steps"></a>Další kroky

Naučte se, jak nastavit Aplikační bránu s více klienty, jako je například webová aplikace služby Azure App Service jako člen fondu back-end, v tématu [konfigurace App Service webových aplikací pomocí Application Gateway](./configure-web-app-portal.md)

---
title: Zabezpečení v Azure App Service a Azure Functions | Dokumentace Microsoftu
description: Další informace o App Service pomáhá jak zabezpečené aplikace a jak můžete dále uzamknout aplikace před hrozbami.
keywords: služby Azure app service webovou aplikaci, mobilní aplikace, aplikace api, aplikace function app, zabezpečení, zabezpečení, zabezpečené, dodržování předpisů, předpisy, certifikátů, certifikáty, https, ftps, tls, vztah důvěryhodnosti, šifrování, šifrování, šifrované, omezení IP adres, ověřování, autorizace, ověřovacích, autho, msi, identita spravované služby, spravovanou identitu, tajné kódy, tajný klíč, opravy, opravy, opravy, verze, izolaci, izolace sítě, před útoky ddos, mitm
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/24/2018
ms.author: cephalin
ms.openlocfilehash: 3bacc2bf253a6b8c3b869b7a6d4952d982de3ee6
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2018
ms.locfileid: "48857495"
---
# <a name="security-in-azure-app-service-and-azure-functions"></a>Zabezpečení v Azure App Service a Azure Functions

Tento článek vám ukáže, jak [služby Azure App Service](app-service-web-overview.md) pomáhá zabezpečit webovou aplikaci, back-endu mobilní aplikace, aplikace API a [Azure Functions](/azure/azure-functions/). Profil také ukazuje, jak můžete dále zabezpečit svou aplikaci pomocí integrované funkce služby App Service.

Komponenty platformy služby App Service, včetně virtuálních počítačů Azure, úložiště, síťová připojení, webové architektury, funkce správy a integrace, které jsou aktivně zabezpečené a Posílená. App Service prochází kontrolovat dodržování předpisů náročným průběžně, abyste měli jistotu, že:

- Prostředky aplikace jsou [zabezpečené](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) z prostředků Azure ostatních zákazníků.
- [Instance virtuálních počítačů a softwaru runtime se pravidelně aktualizují](app-service-patch-os-runtime.md) slabiny adresu nově zjištěno. 
- Komunikace tajných kódů (například připojovací řetězce) mezi vaší aplikací a dalších prostředků Azure (například [SQL Database](https://azure.microsoft.com/services/sql-database/)) zůstává v rámci Azure a není pro různé všechna ohraničení sítě. Tajné kódy se vždy šifrují, pokud uložená.
- Veškerá komunikace přes připojení služby App Service funkce, jako [hybridní připojení](app-service-hybrid-connections.md), je zašifrovaný. 
- Připojení pomocí nástroje pro vzdálenou správu, jako je Azure PowerShell, rozhraní příkazového řádku Azure, sady SDK služby Azure, rozhraní REST API, všechny šifrovaná.
- 24 hodin před internetovými útoky management chrání infrastruktury a platformy před malwarem, distributed denial of service (DDoS), man-in-the-middle typu MITM () a dalšími hrozbami.

Další informace o zabezpečení infrastruktury a platform v Azure najdete v tématu [Centrum zabezpečení Azure](https://azure.microsoft.com/overview/trusted-cloud/).

Následující části ukazují, jak rozšířit ochranu před hrozbami aplikace app Service.

## <a name="https-and-certificates"></a>Protokol HTTPS a certifikátů

App Service umožňuje Zabezpečte svoje aplikace pomocí [HTTPS](https://wikipedia.org/wiki/HTTPS). Pokud vaše aplikace je vytvořená, jeho výchozí název domény (\<app_name >. azurewebsites.net) už je přístupný pomocí protokolu HTTPS. Pokud jste [konfigurace vlastní domény pro vaši aplikaci](app-service-web-tutorial-custom-domain.md), měli byste také [Zabezpečte ji pomocí vlastního certifikátu](app-service-web-tutorial-custom-ssl.md) , tak klientský prohlížeč můžete vytvořit zabezpečené připojení prostřednictvím protokolu HTTPS pro vlastní doménu. Existují dva způsoby, jak to udělat:

- **Certifikát App Service** – vytvořit certifikát přímo v Azure. Certifikát je zabezpečený v [Azure Key Vault](/azure/key-vault/)a mohou být naimportovány do vaší aplikace služby App Service. Další informace najdete v tématu [koupě a konfigurace certifikátu SSL pro službu Azure App Service](web-sites-purchase-ssl-web-site.md).
- **Certifikát třetí strany** – nahrání vlastního certifikátu SSL, který jste si koupili od důvěryhodné certifikační autority a jeho vazbu na vaše aplikace app Service. App Service podporuje jednou doménou certifikáty a certifikáty se zástupnými znaky. Podporuje také certifikáty podepsané svým držitelem pro testovací účely. Další informace najdete v tématu [vytvoření vazby existujícího vlastního certifikátu SSL k Azure Web Apps](app-service-web-tutorial-custom-ssl.md).

## <a name="insecure-protocols-http-tls-10-ftp"></a>Protokoly nezabezpečené (HTTP, TLS 1.0, FTP)

Služba App Service zabezpečit svou aplikaci proti všechna nezašifrovaná připojení (HTTP), poskytuje konfiguraci jedním kliknutím pro vynucení protokolu HTTPS. Nezabezpečené požadavky se zapnou okamžitě, než dosáhnou i kód aplikace. Další informace najdete v tématu [vynucení HTTPS](app-service-web-tutorial-custom-ssl.md#enforce-https).

[Protokol TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.0 je již nejsou považované za bezpečné hlediska oborových standardů, jako například [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard). App Service vám umožní zakázat pomocí zastaralého protokolů [vynucení protokolu TLS 1.1/1.2](app-service-web-tutorial-custom-ssl.md#enforce-tls-versions).

App Service podporuje pro nasazování souborů protokolu FTP a FTPS. Však FTPS by měl použít namísto FTP, pokud je to možné. Pokud jeden nebo oba tyto protokoly nejsou používány, měli byste [je zakázat](app-service-deploy-ftp.md#enforce-ftps).

## <a name="static-ip-restrictions"></a>Omezení statických IP adres

Ve výchozím nastavení vaše aplikace app Service bude přijímat žádosti ze všech IP adres z Internetu, ale můžete omezit, která přístup k malou část IP adresy. App Service ve Windows umožňuje definovat seznam IP adres, které můžou přistupovat k vaší aplikace. Seznam povolených položek může obsahovat jednotlivé IP adresy nebo rozsah IP adres určené maskou podsítě. Další informace najdete v tématu [Azure App Service statické omezení IP adres](app-service-ip-restrictions.md).

Pro službu App Service na Windows, můžete taky omezit IP adresy dynamicky tím, že nakonfigurujete _web.config_. Další informace najdete v tématu [dynamické zabezpečení protokolu IP <dynamicIpSecurity> ](https://docs.microsoft.com/iis/configuration/system.webServer/security/dynamicIpSecurity/).

## <a name="client-authentication-and-authorization"></a>Klient ověřování a autorizace

Azure App Service poskytuje klíč ověřování a autorizace uživatelů nebo klientské aplikace. Pokud povolená, můžete podepsat v modulu Uživatelé a klientské aplikace s kódem nebo vůbec žádné aplikace. Můžete implementovat vlastní řešení pro ověřování a autorizace nebo Povolit službě App Service, aby to zvládnul za vás. Modul ověřování a autorizace zpracovává webové požadavky před jejich předání do kódu aplikace a zakazuje neoprávněné požadavky dřív, než dorazí váš kód.

App Service ověřování a autorizace podporují více zprostředkovatelů ověřování, včetně Azure Active Directory, účty Microsoft, Facebook, Google a Twitter. Další informace najdete v tématu [ověřování a autorizace ve službě Azure App Service](app-service-authentication-overview.md).

## <a name="service-to-service-authentication"></a>Ověřování služba-služba

Při ověřování proti back-end služby App Service poskytuje dva různé mechanismy v závislosti na vaše potřeby:

- **Služba identity** – Přihlaste se k vzdálený prostředek pomocí identity aplikace. App Service umožňuje snadné vytváření [se identita spravované](app-service-managed-service-identity.md), který můžete použít k ověření s ostatními službami, jako například [Azure SQL Database](/azure/sql-database/) nebo [Azure Key Vault](/azure/key-vault/). Kurz začátku do konce tohoto přístupu, najdete v tématu [připojení zabezpečení Azure SQL Database ze služby App Service pomocí spravované identity](app-service-web-tutorial-connect-msi.md).
- **On-behalf-of (OBO)** – Ujistěte se, Delegovaný přístup ke vzdáleným prostředkům jménem uživatele. S Azure Active Directory jako zprostředkovatele ověřování, aplikace app Service můžete provádět delegované přihlášení vzdálené služby, jako například [Azure Active Directory Graph API](../active-directory/develop/active-directory-graph-api.md) nebo vzdálenou aplikaci API ve službě App Service. Kurz začátku do konce tohoto přístupu, najdete v tématu [ověřování a autorizaci uživatelů začátku do konce ve službě Azure App Service](app-service-web-tutorial-auth-aad.md).

## <a name="connectivity-to-remote-resources"></a>Připojení ke vzdáleným prostředkům

Existují tři typy vzdálených prostředků, které vaše aplikace může potřebovat pro přístup: 

- [Prostředky Azure](#azure-resources)
- [Prostředky ve službě Azure Virtual Network](#resources-inside-an-azure-virtual-network)
- [Místní prostředky](#on-premises-resources)

Ve všech těchto případech služba App Service poskytuje způsob, jak můžete vytvořit zabezpečené připojení, ale i nadále dodržovalo osvědčené postupy zabezpečení. Například vždy použijte šifrovaná připojení i v případě, že prostředek back-end umožňuje nezašifrované připojení. Kromě toho se ujistěte, že back endu služby Azure umožňuje minimální sadu IP adres. Můžete najít odchozí IP adresy pro vaši aplikaci v [příchozí a odchozí IP adresy ve službě Azure App Service](app-service-ip-addresses.md).

### <a name="azure-resources"></a>Prostředky Azure

Když vaše aplikace se připojí k prostředky Azure, jako například [SQL Database](https://azure.microsoft.com/services/sql-database/) a [služby Azure Storage](/azure/storage/), připojení zůstane v rámci Azure a není pro různé všechna ohraničení sítě. Ale připojení prochází sdílené sítě v systému Azure, proto vždy ujistěte se, že se šifrují vaše připojení. 

Pokud je vaše aplikace hostovaná v [App Service environment](environment/intro.md), měli byste [připojení k podporované služby Azure s využitím koncové body služeb virtuální sítě](../virtual-network/virtual-network-service-endpoints-overview.md).

### <a name="resources-inside-an-azure-virtual-network"></a>Prostředky ve službě Azure Virtual Network

Vaše aplikace přístup k prostředkům v [Azure Virtual Network](/azure/virtual-network/) prostřednictvím [integrace služby Virtual Network](web-sites-integrate-with-vnet.md). Integrace se naváže s virtuální síti pomocí sítě VPN point-to-site. Aplikaci pak mají přístup k prostředkům ve virtuální síti pomocí jejich privátní IP adresy. Připojení point-to-site, ale stále procházejí sdílené sítě v Azure. 

K izolaci prostředků připojení zcela ze sdílené sítě v Azure, vytvořte aplikaci v [App Service environment](environment/intro.md). Protože služby App Service environment se vždy nasadit do vyhrazené virtuální sítě, připojení mezi vaší aplikací a prostředků v rámci virtuální sítě je plně izolované. Další aspekty zabezpečení sítě ve službě App Service environment najdete v části [izolace sítě](#network-isolation).

### <a name="on-premises-resources"></a>Místní prostředky

Mít bezpečný přístup k místním prostředkům, jako jsou databáze, třemi způsoby: 

- [Hybridní připojení](app-service-hybrid-connections.md) -naváže připojení typu point-to-point vzdálený prostředek přes tunelové připojení protokolu TCP. TCP tunelu TLS 1.2 pomocí klíče sdíleného přístupového podpisu (SAS).
- [Integrace Virtual Network](web-sites-integrate-with-vnet.md) pomocí sítě VPN site-to-site – jak je popsáno v [prostředky ve virtuální síti Azure](#resources-inside-an-azure-virtual-network), ale může být připojen virtuální sítě pro vaši místní síť prostřednictvím sítě [ Site-to-site VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md). V této topologii sítě můžete aplikaci připojit k místním prostředkům, jako ostatní prostředky ve virtuální síti.
- [App Service environment](environment/intro.md) pomocí sítě VPN site-to-site – jak je popsáno v [prostředky ve virtuální síti Azure](#resources-inside-an-azure-virtual-network), ale může být připojen virtuální sítě pro vaši místní síť prostřednictvím sítě [site-to-site VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md). V této topologii sítě můžete aplikaci připojit k místním prostředkům, jako ostatní prostředky ve virtuální síti.

## <a name="application-secrets"></a>Tajných klíčů aplikací

Neukládejte tajných klíčů aplikací, jako je například přihlašovací údaje databáze, tokeny rozhraní API a privátní klíče do kódu nebo konfiguračního souboru. Obecně uznávaný přístup je pro přístup k nim jako [proměnné prostředí](https://wikipedia.org/wiki/Environment_variable) standardním způsobem v jazyce podle výběru. Ve službě App Service, je způsob, jak definovat proměnné prostředí prostřednictvím [nastavení aplikace](web-sites-configure.md#app-settings) (a hlavně pro aplikace .NET, [připojovací řetězce](web-sites-configure.md#connection-strings)). Nastavení aplikace a připojovacích řetězců jsou uloženy v zašifrované podobě v Azure a máte dešifrovat jenom před se vloží do paměti procesu vaší aplikace při spuštění aplikace. Šifrovací klíče jsou pravidelně otočen.

Alternativně můžete integrovat aplikace služby App Service s využitím [Azure Key Vault](/azure/key-vault/) pro správu rozšířené tajných kódů. Podle [přístup k trezoru klíčů na spravovanou identitu](../key-vault/tutorial-web-application-keyvault.md), vaše aplikace app Service mít bezpečný přístup, tajné kódy, které potřebujete.

## <a name="network-isolation"></a>Izolace sítě

S výjimkou **izolované** cenovou úroveň, u všech úrovní aplikace můžete spouštět ve sdílené síťové infrastruktury ve službě App Service. Veřejné IP adresy a nástroje pro vyrovnávání zatížení front-endový jsou sdíleny s jinými tenanty. **Izolované** úroveň poskytuje kompletní síťovou izolaci spuštěním vaší aplikace v rámci vyhrazeného [App Service environment](environment/intro.md). Služby App Service environment běží ve vlastní instanci [Azure Virtual Network](/azure/virtual-network/). To vám umožní: 

- Omezení síťového přístupu s [skupiny zabezpečení sítě](../virtual-network/virtual-networks-dmz-nsg.md). 
- Poskytování aplikací prostřednictvím vyhrazené veřejný koncový bod, s vyhrazenou front-endů.
- Slouží k interní aplikaci pomocí interní nástroj pro vyrovnávání zatížení (ILB), který umožňuje přístup jenom v rámci vaší virtuální sítě Azure. ILB má IP adresu z vaší privátní podsítě, která poskytuje celkový počet izolace aplikací z Internetu.
- [Pomocí ILB za bránou firewall webových aplikací (WAF)](environment/integrate-with-application-gateway.md). WAF nabízí ochranu podnikové úrovni pro vaše veřejné webové aplikace, například DDoS protection, identifikátor URI filtrování a prevence prostřednictvím injektáže SQL.

Další informace najdete v tématu [Úvod do služby Azure App Service Environment](environment/intro.md). 

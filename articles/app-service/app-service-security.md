---
title: Zabezpečení v Azure App Service a Azure Functions | Microsoft Docs
description: Další informace o jak služby App Service pomáhá zabezpečení vaší aplikace a jak můžete dále uzamknout aplikace před hrozbami.
keywords: služby Azure app service, webové aplikace, mobilní aplikace, aplikace api, funkce aplikací, zabezpečení, zabezpečení, zabezpečená, dodržování předpisů, kompatibilní, certifikátů, certifikáty, https, ftps tls, vztah důvěryhodnosti, šifrování, šifrování, šifrování, omezení ip adres, ověřování, autorizace, ověřovacího, autho, msi, identita spravované služby, tajné klíče, tajný klíč, opravy, opravy, opravy, verze, izolaci, izolace sítě, ddos, mitm
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
ms.date: 05/24/2018
ms.author: cephalin
ms.openlocfilehash: 2ca1c1518589e60a03570e1c2063381f749ed9aa
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34654789"
---
# <a name="security-in-azure-app-service-and-azure-functions"></a>Zabezpečení v Azure App Service a Azure Functions

Tento článek ukazuje, jak [Azure App Service](app-service-web-overview.md) pomáhá zabezpečit webovou aplikaci, back-end mobilní aplikace, aplikace API a [Azure Functions](/azure/azure-functions/). Také ukazuje, jak můžete zabezpečit ještě víc vaší aplikace pomocí integrované funkce služby App Service.

Komponenty platformy služby App Service, včetně virtuálních počítačích Azure, úložiště, připojení k síti, webové rozhraní, funkce správy a integrace, které jsou aktivně zabezpečené a zesílené zabezpečení. Služby App Service prochází kontroly dodržování předpisů intenzivního plynule Ujistěte se, že:

- Vaše aplikace prostředky jsou [zabezpečené](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) z ostatních zákazníků prostředků Azure.
- [Instance virtuálních počítačů a runtime softwaru jsou pravidelně aktualizovány](app-service-patch-os-runtime.md) na adresu nově zjištěných slabá místa zabezpečení. 
- Komunikace tajné klíče (například připojovací řetězce) mezi vaší aplikace a dalším prostředkům služby Azure (například [SQL Database](/services/sql-database/)) zůstává v rámci Azure a nemá křížové žádné hranice sítě. Tajné klíče jsou šifrované vždy, když uložené.
- Veškerá komunikace přes připojení služby App Service funkce, jako [hybridní připojení](app-service-hybrid-connections.md), jsou zašifrovaná. 
- Připojení pomocí nástrojů pro vzdálenou správu prostředí Azure PowerShell, rozhraní příkazového řádku Azure, SDK služby Azure, rozhraní REST API, všechny šifrována.
- 24 hodin threat management chrání infrastruktury a platformy před škodlivým softwarem, distributed denial of service (DDoS), man-in-the-middle typu MITM () a dalšími hrozbami.

Další informace o infrastruktury a platformy zabezpečení v Azure najdete v tématu [Centrum zabezpečení Azure](https://azure.microsoft.com/overview/trusted-cloud/).

Následující části ukazují, jak aplikaci aplikační služby dál chránit před hrozbami.

## <a name="https-and-certificates"></a>Protokol HTTPS a certifikáty

App Service umožňuje zabezpečit aplikace s [HTTPS](https://wikipedia.org/wiki/HTTPS). Když vaše aplikace je vytvořena, jeho výchozí název domény (\<app_name >. azurewebsites.net) je již přístupný pomocí protokolu HTTPS. Pokud jste [nakonfigurovat vlastní doménu pro vaši aplikaci](app-service-web-tutorial-custom-domain.md), měli byste také [Zabezpečte ji pomocí vlastního certifikátu](app-service-web-tutorial-custom-ssl.md) tak, aby klient prohlížeče mohou zasílat zabezpečené připojení prostřednictvím protokolu HTTPS pro vaši vlastní doménu. Chcete-li to provést dvěma způsoby:

- **Certifikát služby App Service** -vytvořit certifikát přímo v Azure. Certifikát je zabezpečená [Azure Key Vault](/azure/key-vault/)a mohou být naimportovány do vaší aplikace služby App Service. Další informace najdete v tématu [zakoupení a konfigurace certifikátu protokolu SSL pro Azure App Service](web-sites-purchase-ssl-web-site.md).
- **Certifikát třetích stran** – nahrát vlastní certifikát SSL, které jste zakoupili od důvěryhodné certifikační autority a navázat jej aplikaci aplikační služby. App Service podporuje jednou doménou certifikátů a certifikáty se zástupnými znaky. Podporuje také certifikáty podepsané svým držitelem pro účely testování. Další informace najdete v tématu [vazby stávající vlastní certifikát SSL pro službu Azure Web Apps](app-service-web-tutorial-custom-ssl.md).

## <a name="insecure-protocols-http-tls-10-ftp"></a>Protokoly nezabezpečené (HTTP, TLS 1.0, FTP)

Zabezpečit vaši aplikaci pro všechna nezašifrované připojení (HTTP), služba App Service poskytuje konfigurace jedním kliknutím k vynucení HTTPS. Zabezpečená požadavky jsou zapnuté rychle než dosáhnou i kód aplikace. Další informace najdete v tématu [vynutit HTTPS](app-service-web-tutorial-custom-ssl.md#enforce-https).

[Protokol TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.0 je už považované za bezpečné podle oborových standardů, jako například [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard). App Service umožňuje zakázat zastaralé protokoly podle [vynucování protokolu TLS 1.1 nebo 1.2](app-service-web-tutorial-custom-ssl.md#enforce-tls-1112).

Služby App Service podporuje pro nasazení vaše soubory FTP a FTPS. Však FTPS by měl použít místo FTP, pokud je to možné. Když jedna nebo obě tyto protokoly nejsou používány, měli byste [je zakázat](app-service-deploy-ftp.md#enforce-ftps).

## <a name="static-ip-restrictions"></a>Omezení statických IP adres

Ve výchozím nastavení aplikaci aplikační služby přijímá požadavky od všechny IP adresy z Internetu, ale můžete omezit tento přístup k malou část IP adresy. Aplikační služby v systému Windows umožňuje definovat seznam IP adres, které jsou povoleny pro přístup k aplikaci. Seznam povolených položek můžete zahrnout jednotlivé IP adresy nebo rozsahu IP adres definované masku podsítě. Další informace najdete v tématu [Azure App Service statické omezení IP adres](app-service-ip-restrictions.md).

Pro službu App Service v systému Windows, můžete taky omezit IP adresy dynamicky nakonfigurováním _web.config_. Další informace najdete v tématu [zabezpečení dynamické IP <dynamicIpSecurity> ](https://docs.microsoft.com/iis/configuration/system.webServer/security/dynamicIpSecurity/).

## <a name="client-authentication-and-authorization"></a>Klient ověřování a autorizace

Aplikační služba Azure poskytuje klíč ověřování a autorizaci uživatelů nebo klientské aplikace. Když je povolené, se můžete přihlásit uživatele a klientské aplikace s minimální nebo žádný kód aplikace. Můžete implementovat vlastní řešení ověřování a autorizaci, nebo povolit aplikaci služby pro zpracování pro vás. Modul ověřování a autorizace zpracovává požadavky webové před jejich předáním do kódu aplikace a odmítne neoprávněné žádosti, než dosáhnou kódu.

Aplikační služby ověřování a autorizace podporovat více zprostředkovatelů ověření, včetně služby Azure Active Directory, účty Microsoft, Facebook, Google a Twitter. Další informace najdete v tématu [ověřování a autorizace ve službě Azure App Service](app-service-authentication-overview.md).

## <a name="service-to-service-authentication"></a>Ověřování služba-služba

Při ověřování v back endové službě, služba App Service poskytuje dva různé mechanismy v případě potřeby:

- **Služba identity** -Přihlaste se k vzdálený prostředek pomocí identity aplikace. App Service umožňuje snadno vytvářet [identita spravované služby](app-service-managed-service-identity.md), který můžete použít k ověření s jinými službami, jako například [Azure SQL Database](/azure/sql-database/) nebo [Azure Key Vault](/azure/key-vault/). Kurz začátku do konce tohoto přístupu, najdete v části [zabezpečení Azure SQL Database připojení ze služby App Service pomocí identita spravované služby](app-service-web-tutorial-connect-msi.md).
- **On-behalf-of (OBO)** -zkontrolujte Delegovaný přístup ke vzdáleným prostředkům jménem uživatele. S Azure Active Directory jako zprostředkovatel ověřování, aplikaci aplikační služby mohou provádět delegovanou přihlášení ke vzdálené služby, například [Azure Active Directory Graph API](../active-directory/develop/active-directory-graph-api.md) nebo vzdálené aplikace API v App Service. Kurz začátku do konce tohoto přístupu, najdete v části [ověřit a autorizovat uživatele klient server v Azure App Service](app-service-web-tutorial-auth-aad.md).

## <a name="connectivity-to-remote-resources"></a>Připojení ke vzdáleným prostředkům

Existují tři typy vzdálených prostředků, které může potřebovat přístup k vaší aplikaci: 

- [Prostředky Azure](#azure-resources)
- [Prostředky uvnitř virtuální sítě Azure](#resources-inside-an-azure-virtual-network)
- [Místní prostředky](#on-premises-resources)

V každém z těchto případech se služba App Service poskytuje způsob, jak vám umožní provádět zabezpečené připojení, ale stále měli zjistit osvědčené postupy zabezpečení. Například vždy použijte šifrované připojení i v případě, že prostředek back-end umožňuje nezašifrované připojení. Kromě toho se ujistěte, že váš back-end služby Azure umožňuje minimální sadu IP adres. Můžete najít odchozí IP adresy pro vaši aplikaci v [příchozí a odchozí IP adres ve službě Azure App Service](app-service-ip-addresses.md).

### <a name="azure-resources"></a>Prostředky Azure

Až aplikaci se připojí k prostředky Azure, jako například [databáze SQL](/services/sql-database/) a [Azure Storage](/azure/storage/), připojení zůstane v rámci Azure a nemá křížové žádné hranice sítě. Však připojení prochází sdílené sítě v Azure, takže vždy se ujistěte, že připojení je zašifrovaná. 

Pokud vaše aplikace je hostována v [služby App Service environment](environment/intro.md), měli byste [připojení do služby Azure pomocí koncových bodů služby virtuální sítě podporované](../virtual-network/virtual-network-service-endpoints-overview.md).

### <a name="resources-inside-an-azure-virtual-network"></a>Prostředky uvnitř virtuální sítě Azure

Přístup k prostředkům ve vaší aplikaci [Azure Virtual Network](/azure/virtual-network/) prostřednictvím [integrace virtuální sítě](web-sites-integrate-with-vnet.md). Integrace se naváže virtuální sítě pomocí sítě VPN point-to-site. Aplikace pak může přístup k prostředkům ve virtuální síti pomocí jejich privátních IP adres. Připojení point-to-site, ale stále prochází sdílené sítě v Azure. 

Když Pokud chcete izolovat připojení k prostředku zcela od sdílené sítí v Azure, vytvoření aplikace v [služby App Service environment](environment/intro.md). Vzhledem k tomu, že služby App Service environment je vždy nasazen na vyhrazené virtuální sítě, je plně izolovaném připojení mezi vaší aplikace a prostředky v rámci virtuální sítě. Další aspekty zabezpečení sítě v prostředí služby App Service naleznete v části [izolace sítě](#network-isolation).

### <a name="on-premises-resources"></a>Místní prostředky

Mít bezpečný přístup k místním prostředkům, jako jsou třeba databáze, třemi způsoby: 

- [Hybridní připojení](app-service-hybrid-connections.md) -naváže připojení typu point-to-point do vzdáleného zdroje přes tunelové TCP. Tunelové propojení protokolu TCP je vytvořeno pomocí protokolu TLS 1.2 klíče sdíleného přístupového podpisu (SAS).
- [Integrace virtuální sítě](web-sites-integrate-with-vnet.md) s site-to-site VPN – jak je popsáno v [prostředků v Azure Virtual Network](#resources-inside-an-azure-virtual-network), ale virtuální sítě může být připojen k síti na pracovišti prostřednictvím [ Site-to-site VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md). V této topologii sítě vaše aplikace může připojit k místním prostředkům jako další prostředky ve virtuální síti.
- [Služba App Service environment](environment/intro.md) s site-to-site VPN – jak je popsáno v [prostředků v Azure Virtual Network](#resources-inside-an-azure-virtual-network), ale virtuální sítě může být připojen k síti na pracovišti prostřednictvím [site-to-site VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md). V této topologii sítě vaše aplikace může připojit k místním prostředkům jako další prostředky ve virtuální síti.

## <a name="application-secrets"></a>Tajné klíče aplikace

Neukládejte tajné klíče aplikace, jako je například přihlašovací údaje databáze, rozhraní API tokenů a privátní klíče v souborech kódu nebo konfigurace. Běžně přijatý přístup je pro přístup k nim jako [proměnné prostředí](https://wikipedia.org/wiki/Environment_variable) pomocí standardní vzor ve vámi zvolený jazyk. Ve službě App Service je způsob, jak definovat proměnné prostředí prostřednictvím [nastavení aplikace](web-sites-configure.md#app-settings) (a hlavně pro aplikace .NET, [připojovací řetězce](web-sites-configure.md#connection-strings)). Nastavení aplikace a připojovací řetězce jsou uloženy v zašifrované podobě v Azure a že dešifrovat jenom před se vložit do vaší aplikace proces paměti při spuštění aplikace. Šifrovací klíče jsou pravidelně otočen.

Alternativně můžete integrovat aplikace služby App Service pomocí [Azure Key Vault](/azure/key-vault/) pro správu pokročilé tajných klíčů. Podle [přístupu k trezoru klíčů identita spravované služby](../key-vault/tutorial-web-application-keyvault.md), vaše aplikace služby App Service můžete bezpečný přístup k tajné klíče, je nutné.

## <a name="network-isolation"></a>Izolace sítě

S výjimkou **izolovaná** cenovou úroveň, všechny úrovně aplikace spustit ve sdílené síťové infrastruktuře ve službě App Service. Veřejné IP adresy a nástroje pro vyrovnávání zatížení front-end jsou sdíleny s jinými klienty. **Izolovaná** vrstvy vám izolace sítě dokončení spuštěním aplikace uvnitř vyhrazená [služby App Service environment](environment/intro.md). Služby App Service environment běží ve vlastní instanci [Azure Virtual Network](/azure/virtual-network/). Umožňuje: 

- Omezit přístup k síti s [skupin zabezpečení sítě](../virtual-network/virtual-networks-nsg.md). 
- Obsluhovat aplikace prostřednictvím vyhrazené veřejný koncový bod, se vyhrazené front-end.
- Sloužit interní aplikace pomocí interní nástroj (ILB), který umožňuje přístup pouze z uvnitř virtuální sítě Azure. ILB, obsahuje IP adresu z vaší privátní podsítě, které poskytuje celkový izolace aplikací z Internetu.
- [Pomocí ILB za bránou firewall webových aplikací (firewall webových aplikací)](environment/integrate-with-application-gateway.md). Firewall webových aplikací nabízí ochranu podnikové úrovni pro veřejné aplikace, například Ochrana proti útoku DDoS, identifikátor URI filtrování a prevenci Injektáž SQL.

Další informace najdete v tématu [Úvod do prostředí Azure App Service](environment/intro.md).
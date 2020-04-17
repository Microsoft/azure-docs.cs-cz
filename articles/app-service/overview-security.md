---
title: Zabezpečení
description: Přečtěte si, jak služba App Service pomáhá zabezpečit vaši aplikaci a jak můžete aplikaci dále uzamknout před hrozbami.
keywords: azure app service, web app, mobilní aplikace, api app, funkční aplikace, zabezpečení, zabezpečení, zabezpečené, zabezpečené, dodržování předpisů, kompatibilní, certifikát, certifikáty, https, ftps, tls, důvěra, šifrování, šifrování, šifrované, omezení IP, ověřování, autorizace, authn, autho, msi, identita spravované služby, spravovaná identita, tajné kódy, tajný, tajný, patching, patch, záplaty, verze, izolace, izolace sítě, ddos, mitm
ms.topic: article
ms.date: 08/24/2018
ms.custom: seodec18
ms.openlocfilehash: 8a098b1924bf7c2866f6afd7452b8dd3b93f3109
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535651"
---
# <a name="security-in-azure-app-service"></a>Zabezpečení ve službě Azure App Service

Tento článek ukazuje, jak [služba Azure App Service](overview.md) pomáhá zabezpečit vaši webovou aplikaci, back-end mobilní aplikace, aplikaci API a [aplikaci funkcí](/azure/azure-functions/). Také ukazuje, jak můžete dále zabezpečit aplikaci pomocí integrovaných funkcí služby App Service.

Součásti platformy služby App Service, včetně virtuálních stránek Azure, úložiště, síťových připojení, webových architektur, funkcí pro správu a integraci, jsou aktivně zabezpečené a zpevněné. Služba App Service průběžně prochází intenzivními kontrolami dodržování předpisů, aby se ujistila, že:

- Prostředky aplikace jsou [zabezpečené](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) z prostředků Azure ostatních zákazníků.
- [Instance virtuálních počítačů a runtime software jsou pravidelně aktualizovány,](overview-patch-os-runtime.md) aby řešily nově zjištěné chyby zabezpečení. 
- Komunikace tajných kódů (například připojovacích řetězců) mezi vaší aplikací a dalšími prostředky Azure (například [SQL Database)](https://azure.microsoft.com/services/sql-database/)zůstane v Azure a nepřekročí žádné hranice sítě. Tajné klíče jsou při uložení vždy zašifrovány.
- Veškerá komunikace prostředně prostředně funkcí připojení služby App Service, jako je [například hybridní připojení](app-service-hybrid-connections.md), je šifrována. 
- Připojení s nástroji pro vzdálenou správu, jako je Azure PowerShell, Azure CLI, Azure SDK, REST API, jsou šifrované.
- 24hodinová správa hrozeb chrání infrastrukturu a platformu před malwarem, distribuovaným odmítnutím služby (DDoS), prostředníkem ve středu (MITM) a dalšími hrozbami.

Další informace o infrastruktuře a zabezpečení platformy v Azure najdete v [tématu Azure Trust Center](https://azure.microsoft.com/overview/trusted-cloud/).

V následujících částech se ukazuje, jak dále chránit aplikaci App Service před hrozbami.

## <a name="https-and-certificates"></a>HTTPS a certifikáty

Služba App Service umožňuje zabezpečit aplikace pomocí [protokolu HTTPS](https://wikipedia.org/wiki/HTTPS). Po vytvoření aplikace je její výchozí\<název domény (app_name>.azurewebsites.net) již přístupný pomocí protokolu HTTPS. Pokud [nakonfigurujete vlastní doménu pro vaši aplikaci](app-service-web-tutorial-custom-domain.md), měli byste ji také [zabezpečit pomocí certifikátu TLS/SSL,](configure-ssl-bindings.md) aby klientské prohlížeče mohly vytvářet zabezpečené připojení HTTPS k vaší vlastní doméně. Služba App Service podporuje několik typů certifikátů:

- Certifikát spravovaný službou App Service zdarma
- Certifikát služby App Service
- Certifikát třetí strany
- Certifikát importovaný z trezoru klíčů Azure

Další informace najdete [v tématu Přidání certifikátu TLS/SSL ve službě Azure App Service](configure-ssl-certificate.md).

## <a name="insecure-protocols-http-tls-10-ftp"></a>Nezabezpečené protokoly (HTTP, TLS 1.0, FTP)

Chcete-li zabezpečit aplikaci proti všem nešifrovaným (HTTP) připojením, služba App Service poskytuje konfiguraci jedním kliknutím k vynucení protokolu HTTPS. Nezabezpečené požadavky jsou odvráceny ještě předtím, než se dostanou ke kódu aplikace. Další informace naleznete v [tématu Enforce HTTPS](configure-ssl-bindings.md#enforce-https).

[TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.0 již není považován za bezpečný podle oborových standardů, jako je [například PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard). Služba App Service umožňuje zakázat zastaralé protokoly [vynucením protokolu TLS 1.1/1.2](configure-ssl-bindings.md#enforce-tls-versions).

Služba App Service podporuje ftp i FTPS pro nasazení souborů. Ftps by však měl být použit místo FTP, pokud je to možné. Pokud jeden nebo oba tyto protokoly nejsou používány, měli byste [je zakázat](deploy-ftp.md#enforce-ftps).

## <a name="static-ip-restrictions"></a>Statická omezení IP

Ve výchozím nastavení aplikace App Service přijímá požadavky ze všech IP adres z internetu, ale tento přístup můžete omezit na malou podmnožinu IP adres. Služba App Service ve Windows umožňuje definovat seznam IP adres, které mají povolený přístup k vaší aplikaci. Povolený seznam může obsahovat jednotlivé adresy IP nebo rozsah adres IP definovaných maskou podsítě. Další informace najdete v [tématu Azure App Service Statická omezení IP](app-service-ip-restrictions.md).

V případě služby App Service v systému Windows můžete také dynamicky omezit adresy IP konfigurací _souboru web.config_. Další informace naleznete [v \<tématu Dynamic IP Security dynamicIpSecurity>](https://docs.microsoft.com/iis/configuration/system.webServer/security/dynamicIpSecurity/).

## <a name="client-authentication-and-authorization"></a>Ověřování a autorizace klienta

Služba Azure App Service poskytuje ověřování na klíč a autorizaci uživatelů nebo klientských aplikací. Pokud je povoleno, může přihlašovat uživatele a klientské aplikace s malým nebo žádným kódem aplikace. Můžete implementovat vlastní řešení ověřování a autorizace nebo povolit app service s ním zpracovat za vás místo. Modul ověřování a autorizace zpracovává webové požadavky před jejich předáním kódu aplikace a odepře neoprávněné požadavky dříve, než se dostanou k vašemu kódu.

Ověřování a autorizace služby App Service podporuje více poskytovatelů ověřování, včetně Azure Active Directory, účtů Microsoft, Facebooku, Googlu a Twitteru. Další informace najdete v tématu [Ověřování a autorizace ve službě Azure App Service](overview-authentication-authorization.md).

## <a name="service-to-service-authentication"></a>Ověřování služba-služba

Při ověřování proti back-endové službě poskytuje služba App Service dva různé mechanismy v závislosti na vaší potřebě:

- **Identita služby** – přihlaste se ke vzdálenému prostředku pomocí identity samotné aplikace. Služba App Service umožňuje snadno vytvořit [spravovanou identitu](overview-managed-identity.md), kterou můžete použít k ověření pomocí jiných služeb, jako je [Azure SQL Database](/azure/sql-database/) nebo Azure Key [Vault](/azure/key-vault/). Kurz tohoto přístupu na začátku do konce najdete v [tématu Zabezpečené připojení Azure SQL Database ze služby App Service pomocí spravované identity](app-service-web-tutorial-connect-msi.md).
- **Jménem (OBO)** – umožňuje delegovaný přístup ke vzdáleným prostředkům jménem uživatele. S Azure Active Directory jako poskytovatelem ověřování, vaše aplikace App Service můžete provádět delegované přihlášení ke vzdálené službě, jako je [Microsoft Graph API](../active-directory/develop/microsoft-graph-intro.md) nebo vzdálené rozhraní API aplikace v App Service. Pro end-to-end kurz tohoto přístupu, najdete [v tématu ověřování a autorizace uživatelů end-to-end ve službě Azure App Service](app-service-web-tutorial-auth-aad.md).

## <a name="connectivity-to-remote-resources"></a>Připojení ke vzdáleným prostředkům

Aplikace může potřebovat přístup ke třem typům vzdálených prostředků: 

- [Prostředky Azure](#azure-resources)
- [Prostředky uvnitř virtuální sítě Azure](#resources-inside-an-azure-virtual-network)
- [Místní prostředky](#on-premises-resources)

V každém z těchto případů služba App Service poskytuje způsob, jak vytvořit zabezpečená připojení, ale stále byste měli dodržovat osvědčené postupy zabezpečení. Například vždy používejte šifrovaná připojení i v případě, že prostředek back-end umožňuje nešifrovaná připojení. Dále se ujistěte, že vaše back-endová služba Azure umožňuje minimální sadu IP adres. Odchozí IP adresy pro vaši aplikaci najdete na [příchozích a odchozích IP adresách ve službě Azure App Service](overview-inbound-outbound-ips.md).

### <a name="azure-resources"></a>Prostředky Azure

Když se vaše aplikace připojí k prostředkům Azure, jako je [SQL Database](https://azure.microsoft.com/services/sql-database/) a [Azure Storage](/azure/storage/), připojení zůstane v Azure a nepřekročí žádné hranice sítě. Připojení však prochází sdílené sítě v Azure, takže vždy ujistěte se, že vaše připojení je šifrované. 

Pokud je vaše aplikace hostovaná v [prostředí služby App Service](environment/intro.md), měli byste se připojit k [podporovaným službám Azure pomocí koncových bodů služby Virtuální síť](../virtual-network/virtual-network-service-endpoints-overview.md).

### <a name="resources-inside-an-azure-virtual-network"></a>Prostředky uvnitř virtuální sítě Azure

Vaše aplikace má přístup k prostředkům ve [virtuální síti Azure](/azure/virtual-network/) prostřednictvím integrace virtuální [sítě](web-sites-integrate-with-vnet.md). Integrace je vytvořena pomocí virtuální sítě pomocí sítě VPN z bodu na místo. Aplikace pak může přistupovat k prostředkům ve virtuální síti pomocí svých privátních IP adres. Připojení point-to-site však stále prochází sdílenými sítěmi v Azure. 

Chcete-li připojení prostředků zcela izolovat od sdílených sítí v Azure, vytvořte aplikaci v [prostředí služby App Service](environment/intro.md). Vzhledem k tomu, že prostředí služby App Service se vždy nasazuje do vyhrazené virtuální sítě, připojení mezi vaší aplikací a prostředky v rámci virtuální sítě je plně izolované. Další aspekty zabezpečení sítě v prostředí služby App Service naleznete v tématu [Izolace sítě](#network-isolation).

### <a name="on-premises-resources"></a>Místní prostředky

K místním prostředkům, jako jsou databáze, můžete bezpečně přistupovat třemi způsoby: 

- [Hybridní připojení](app-service-hybrid-connections.md) – vytvoří připojení k vzdálenému prostředku z bodu do bodu prostřednictvím tunelového propojení TCP. Tunelové propojení TCP je vytvořeno pomocí tls 1.2 s klíči sdíleného přístupového podpisu (SAS).
- [Integrace virtuální sítě](web-sites-integrate-with-vnet.md) s vpn site-to-site – jak je popsáno v [části Prostředky uvnitř virtuální sítě Azure](#resources-inside-an-azure-virtual-network), ale virtuální síť může být připojena k místní síti prostřednictvím sítě VPN [site-to-site](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md). V této topologii sítě se vaše aplikace může připojit k místním prostředkům, jako jsou jiné prostředky ve virtuální síti.
- [Prostředí služby App Service](environment/intro.md) s vpn site-to-site – jak je popsáno v [části Prostředky uvnitř virtuální sítě Azure](#resources-inside-an-azure-virtual-network), ale virtuální síť může být připojena k místní síti prostřednictvím sítě VPN [site-to-site](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md). V této topologii sítě se vaše aplikace může připojit k místním prostředkům, jako jsou jiné prostředky ve virtuální síti.

## <a name="application-secrets"></a>Tajné klíče aplikace

Neukládejte tajné klíče aplikací, jako jsou přihlašovací údaje databáze, tokeny rozhraní API a soukromé klíče ve vašem kódu nebo konfiguračních souborech. Běžně přijímaný přístup je přístup k nim jako [proměnné prostředí](https://wikipedia.org/wiki/Environment_variable) pomocí standardní vzor ve vašem jazyce volby. Ve službě App Service je způsob, jak definovat proměnné prostředí, prostřednictvím [nastavení aplikace](configure-common.md#configure-app-settings) (a zejména pro aplikace .NET [připojovací řetězce](configure-common.md#configure-connection-strings)). Nastavení aplikací a připojovací řetězce se ukládají šifrované v Azure a dešifrují se jenom před vložením do procesní paměti vaší aplikace při spuštění aplikace. Šifrovací klíče se pravidelně otáčejí.

Případně můžete integrovat aplikaci App Service s [Azure Key Vault](/azure/key-vault/) pro pokročilou správu tajných klíčů. [Přístup k trezoru klíčů se spravovanou identitou](../key-vault/tutorial-web-application-keyvault.md)může vaše aplikace App Service bezpečně přistupovat k tajným klíčům, které potřebujete.

## <a name="network-isolation"></a>Izolace sítě

S výjimkou **izolované** cenové úrovně, všechny úrovně spustit aplikace na sdílené síťové infrastruktury ve službě App Service. Například veřejné IP adresy a front-end ové vykladače zatížení jsou sdíleny s ostatními klienty. Úroveň **Izolované** poskytuje úplnou izolaci sítě spuštěním aplikací v prostředí vyhrazené [služby App Service](environment/intro.md). Prostředí služby App Service běží ve vaší vlastní instanci [virtuální sítě Azure](/azure/virtual-network/). Umožňuje vám: 

- Obsluhujte své aplikace prostřednictvím vyhrazeného veřejného koncového bodu s vyhrazenými front-endy.
- Obsluhujte interní aplikaci pomocí interního nástroje pro vyrovnávání zatížení (ILB), který umožňuje přístup jenom z virtuální sítě Azure. ILB má IP adresu z vaší privátní podsítě, která poskytuje úplnou izolaci vašich aplikací od internetu.
- [Použijte ilb za bránou firewall webové aplikace (WAF)](environment/integrate-with-application-gateway.md). WAF nabízí ochranu na podnikové úrovni pro vaše veřejné aplikace, jako je ochrana DDoS, filtrování URI a prevence vkládání SQL.

Další informace najdete [v tématu Úvod do prostředí služby Azure App Service](environment/intro.md). 

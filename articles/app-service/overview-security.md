---
title: Zabezpečení
description: Přečtěte si, jak App Service pomáhá zabezpečit vaši aplikaci a jak můžete aplikaci dál uzamknout před hrozbami.
keywords: Azure App Service, Webová aplikace, mobilní aplikace, aplikace API, aplikace Function App, zabezpečení, zabezpečení, zabezpečení, dodržování předpisů, kompatibilní, certifikát, certifikáty, https, FTPS, TLS, důvěryhodnost, šifrování, šifrování, šifrování, omezení IP adres, ověřování, autorizace, Authn, autho, MSI, identita spravované služby, spravovaná identita, tajné kódy, zabezpečení, DDoS, MITM
ms.topic: article
ms.date: 08/24/2018
ms.custom: seodec18
ms.openlocfilehash: 61bffcfa8b98ed666e450c344023258b752e4880
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98736101"
---
# <a name="security-in-azure-app-service"></a>Zabezpečení ve službě Azure App Service

V tomto článku se dozvíte, jak [Azure App Service](overview.md) pomáhá zabezpečit webovou aplikaci, back-end mobilní aplikace, aplikaci API a [aplikaci Function App](../azure-functions/index.yml). Také ukazuje, jak můžete aplikaci dále zabezpečit pomocí integrovaných funkcí App Service.

[!INCLUDE [app-service-security-intro](../../includes/app-service-security-intro.md)]

V následujících částech se dozvíte, jak dále chránit aplikaci App Service před hrozbami.

## <a name="https-and-certificates"></a>HTTPS a certifikáty

App Service vám umožní zabezpečit aplikace pomocí [protokolu HTTPS](https://wikipedia.org/wiki/HTTPS). Po vytvoření aplikace je její výchozí název domény ( \<app_name> . azurewebsites.NET) již přístupný pomocí protokolu HTTPS. Pokud [pro svou aplikaci nakonfigurujete vlastní doménu](app-service-web-tutorial-custom-domain.md), měli byste [ji také ZABEZPEČIT pomocí certifikátu TLS/SSL](configure-ssl-bindings.md) , aby klientské prohlížeče mohli vytvořit zabezpečená připojení HTTPS k vaší vlastní doméně. App Service podporuje několik typů certifikátů:

- Free App Service spravovaný certifikát
- App Service certifikát
- Certifikát třetí strany
- Certifikát se importoval z Azure Key Vault

Další informace najdete v tématu [Přidání certifikátu TLS/SSL v Azure App Service](configure-ssl-certificate.md).

## <a name="insecure-protocols-http-tls-10-ftp"></a>Nezabezpečené protokoly (HTTP, TLS 1,0, FTP)

Pokud chcete svoji aplikaci zabezpečit před všemi nezašifrovanými připojeními (HTTP), App Service poskytuje konfiguraci jedním kliknutím pro vymáhání protokolu HTTPS. Nezabezpečené žádosti jsou vypnuté předtím, než budou mít přístup k vašemu kódu aplikace. Další informace najdete v tématu [vymáhání protokolu HTTPS](configure-ssl-bindings.md#enforce-https).

Protokol [TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1,0 již není považován za zabezpečený oborovým standardem, jako je například [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard). App Service umožňuje zakázat zastaralé protokoly [vynucením TLS 1.1/1.2](configure-ssl-bindings.md#enforce-tls-versions).

App Service podporuje FTP i FTPS pro nasazování souborů. Nicméně FTPS by se měl používat místo FTP, pokud je to možné. Pokud se jeden nebo oba tyto protokoly nepoužívají, měli byste [je zakázat](deploy-ftp.md#enforce-ftps).

## <a name="static-ip-restrictions"></a>Omezení statických IP adres

Ve výchozím nastavení vaše aplikace App Service přijímá požadavky ze všech IP adres z Internetu, ale můžete tento přístup omezit na malou podmnožinu IP adres. App Service ve Windows vám umožní definovat seznam IP adres, které mají povolený přístup k vaší aplikaci. Seznam povolených adres může zahrnovat jednotlivé IP adresy nebo rozsah IP adres definovaných maskou podsítě. Další informace najdete v tématu [Azure App Service omezení statických IP adres](app-service-ip-restrictions.md).

Pro App Service ve Windows můžete také dynamicky omezit IP adresy tím, že nakonfigurujete _web.config_. Další informace najdete v tématu [dynamické zabezpečení \<dynamicIpSecurity> protokolu IP](/iis/configuration/system.webServer/security/dynamicIpSecurity/).

## <a name="client-authentication-and-authorization"></a>Ověřování a autorizace klienta

Azure App Service poskytuje ověřování a autorizaci uživatelů nebo klientských aplikací. Když je tato možnost povolená, může se přihlašovat uživatelům a klientským aplikacím s malým nebo žádným kódem aplikace. Můžete implementovat vlastní řešení pro ověřování a autorizaci nebo App Service, abyste ho mohli místo toho zpracovat. Modul ověřování a autorizace zpracovává webové požadavky před jejich předáním do kódu aplikace a zamítnutí neautorizovaných žádostí před tím, než dorazí na váš kód.

Ověřování a autorizace App Service podporují více poskytovatelů ověřování, včetně Azure Active Directory, účtů Microsoft, Facebooku, Google a Twitteru. Další informace najdete v tématu [Ověřování a autorizace ve službě Azure App Service](overview-authentication-authorization.md).

## <a name="service-to-service-authentication"></a>Ověřování služba-služba

Při ověřování proti back-endové službě poskytuje App Service v závislosti na vašich potřebách dva různé mechanismy:

- **Identita služby** – Přihlaste se ke vzdálenému prostředku pomocí samotné identity aplikace. App Service vám umožní snadno vytvořit [spravovanou identitu](overview-managed-identity.md), kterou můžete použít k ověření pomocí jiných služeb, jako je například [Azure SQL Database](/azure/sql-database/) nebo [Azure Key Vault](../key-vault/index.yml). Ucelený kurz tohoto přístupu najdete v tématu [zabezpečené Azure SQL Database připojení z App Service pomocí spravované identity](app-service-web-tutorial-connect-msi.md).
- **OBO (za běhu)** – zajistěte delegovaný přístup ke vzdáleným prostředkům jménem uživatele. V případě Azure Active Directory jako poskytovatele ověřování může vaše aplikace App Service provádět delegované přihlašování ke vzdálené službě, jako je například [rozhraní api Microsoft Graph](../active-directory/develop/microsoft-graph-intro.md) nebo Vzdálená aplikace api v App Service. Ucelený kurz tohoto přístupu najdete v tématu [ověřování a autorizace uživatelů v Azure App Service](tutorial-auth-aad.md).

## <a name="connectivity-to-remote-resources"></a>Připojení ke vzdáleným prostředkům

Existují tři typy vzdálených prostředků, které vaše aplikace může potřebovat pro přístup: 

- [Prostředky Azure](#azure-resources)
- [Prostředky v rámci Azure Virtual Network](#resources-inside-an-azure-virtual-network)
- [Místní prostředky](#on-premises-resources)

V každém z těchto případů App Service poskytuje způsob, jak provádět zabezpečená připojení, ale přesto byste měli pozorovat osvědčené postupy zabezpečení. Například vždy používejte šifrovaná připojení, i když prostředek back-end umožňuje nešifrované připojení. Dále se ujistěte, že vaše back-end služba Azure umožňuje minimální sadu IP adres. Odchozí IP adresy pro vaši aplikaci najdete na [příchozích a odchozích IP adresách v Azure App Service](overview-inbound-outbound-ips.md).

### <a name="azure-resources"></a>Prostředky Azure

Když se vaše aplikace připojí k prostředkům Azure, jako jsou [SQL Database](https://azure.microsoft.com/services/sql-database/) a [Azure Storage](../storage/index.yml), připojení zůstane v Azure a nepřekračuje žádné hranice sítě. Připojení se ale prochází přes sdílené sítě v Azure, takže se vždycky ujistěte, že je vaše připojení šifrované. 

Pokud je vaše aplikace hostována v [prostředí App Service](environment/intro.md), měli byste [se připojit k podporovaným službám Azure pomocí koncových bodů služby Virtual Network](../virtual-network/virtual-network-service-endpoints-overview.md).

### <a name="resources-inside-an-azure-virtual-network"></a>Prostředky v rámci Azure Virtual Network

Vaše aplikace má přístup k prostředkům v [Azure Virtual Network](../virtual-network/index.yml) prostřednictvím [integrace Virtual Network](web-sites-integrate-with-vnet.md). Integrace je navázána s Virtual Network pomocí sítě VPN typu Point-to-site. Aplikace pak může získat přístup k prostředkům v Virtual Network pomocí jejich privátních IP adres. Připojení Point-to-site ale pořád projde sdílené sítě v Azure. 

Pokud chcete zcela izolovat připojení k prostředkům ze sdílených sítí v Azure, vytvořte si aplikaci v [prostředí App Service](environment/intro.md). Vzhledem k tomu, že App Service prostředí je vždycky nasazené do vyhrazené Virtual Network, je připojení mezi vaší aplikací a prostředky v rámci Virtual Network plně izolované. Další aspekty zabezpečení sítě v prostředí App Service najdete v tématu [izolace sítě](#network-isolation).

### <a name="on-premises-resources"></a>Místní prostředky

K místním prostředkům, jako jsou databáze, můžete bezpečně přistupovat třemi způsoby: 

- [Hybridní připojení](app-service-hybrid-connections.md) – naváže připojení typu Point-to-Point ke vzdálenému prostředku prostřednictvím tunelu TCP. Tunel TCP je vytvořen pomocí klíčů TLS 1,2 s klíči sdíleného přístupového podpisu (SAS).
- [Virtual Network integrace](web-sites-integrate-with-vnet.md) s VPN typu Site-to-site – jak je popsáno v tématu [prostředky v rámci Azure Virtual Network](#resources-inside-an-azure-virtual-network), ale Virtual Network se dá připojit k místní síti prostřednictvím [sítě VPN typu Site-to-site](../vpn-gateway/tutorial-site-to-site-portal.md). V této topologii sítě se může vaše aplikace připojit k místním prostředkům, jako jsou jiné prostředky v Virtual Network.
- [App Service prostředí](environment/intro.md) s VPN typu Site-to-site – jak je popsáno v tématu [prostředky v rámci Azure Virtual Network](#resources-inside-an-azure-virtual-network), ale Virtual Network se dá připojit k místní síti prostřednictvím [sítě VPN typu Site-to-site](../vpn-gateway/tutorial-site-to-site-portal.md). V této topologii sítě se může vaše aplikace připojit k místním prostředkům, jako jsou jiné prostředky v Virtual Network.

## <a name="application-secrets"></a>Tajné klíče aplikace

Neukládejte tajné klíče aplikace, jako jsou přihlašovací údaje databáze, tokeny API a privátní klíče, do kódu nebo konfiguračních souborů. Běžně přijímaným přístupem je přístup k těmto [proměnným prostředí](https://wikipedia.org/wiki/Environment_variable) pomocí standardního vzoru ve zvoleném jazyce. V App Service způsob definování proměnných prostředí je prostřednictvím [nastavení aplikace](configure-common.md#configure-app-settings) (a zejména pro aplikace .NET, [připojovací řetězce](configure-common.md#configure-connection-strings)). Nastavení aplikace a připojovací řetězce se ukládají v Azure jako šifrované a dešifrují se jenom předtím, než se vloží do paměti procesu vaší aplikace při spuštění aplikace. Šifrovací klíče se pravidelně otáčí.

Alternativně můžete svou aplikaci App Service integrovat s [Azure Key Vault](../key-vault/index.yml) pro pokročilou správu tajných klíčů. Když [přístup k Key Vault ke spravované identitě](../key-vault/general/tutorial-net-create-vault-azure-web-app.md), vaše aplikace App Service může zabezpečeně přistupovat k tajným údajům, které potřebujete.

## <a name="network-isolation"></a>Izolace sítě

S výjimkou cenové úrovně **inizolace** všechny úrovně spouštějí vaše aplikace na sdílené síťové infrastruktuře v App Service. Například veřejné IP adresy a nástroje pro vyrovnávání zatížení front-end jsou sdíleny s ostatními klienty. **Izolovaná** vrstva poskytuje kompletní izolaci sítě spuštěním aplikací v [prostředí vyhrazené App Service](environment/intro.md). Prostředí App Service běží ve vaší vlastní instanci [Azure Virtual Network](../virtual-network/index.yml). Umožňuje vám: 

- Obsluhujte své aplikace prostřednictvím vyhrazeného veřejného koncového bodu s vyhrazenými front-endy.
- Obsluhovat interní aplikace s využitím interního nástroje pro vyrovnávání zatížení (interního nástroje), který umožňuje přístup pouze zevnitř vaší služby Azure Virtual Network. INTERNÍHO nástroje má IP adresu z vaší privátní podsítě, která poskytuje celkovou izolaci vašich aplikací z Internetu.
- [Použijte interního nástroje za bránou firewall webových aplikací (WAF)](environment/integrate-with-application-gateway.md). WAF nabízí ochranu na podnikové úrovni pro vaše veřejné aplikace, jako je například ochrana DDoS, filtrování identifikátorů URI a prevence injektáže SQL.

Další informace najdete v tématu [Úvod do Azure App Service prostředí](environment/intro.md).
---
title: 'Azure AD Connect: požadavky a hardware | Microsoft Docs'
description: Toto téma popisuje předpoklady a požadavky na hardware pro Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 91b88fda-bca6-49a8-898f-8d906a661f07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/27/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc76f8edc8520ca50cd4c9527b037d99d24ce63c
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79261460"
---
# <a name="prerequisites-for-azure-ad-connect"></a>Předpoklady pro Azure AD Connect
Toto téma popisuje předpoklady a požadavky na hardware pro Azure AD Connect.

## <a name="before-you-install-azure-ad-connect"></a>Než nainstalujete Azure AD Connect
Než nainstalujete Azure AD Connect, budete potřebovat několik věcí.

### <a name="azure-ad"></a>Azure AD
* Tenanta Azure AD. Získáte ji s [bezplatnou zkušební verzí Azure](https://azure.microsoft.com/pricing/free-trial/). Ke správě Azure AD Connect můžete použít jeden z následujících portálů:
  * [Azure Portal](https://portal.azure.com).
  * [Portál Office](https://portal.office.com).  
* [Přidejte a ověřte doménu](../active-directory-domains-add-azure-portal.md) , kterou plánujete použít ve službě Azure AD. Pokud například plánujete používat contoso.com pro uživatele, ujistěte se, že je tato doména ověřená a že nepoužíváte pouze výchozí doménu contoso.onmicrosoft.com.
* Tenant Azure AD umožňuje standardně 50 tis objekty. Při ověřování domény se tento limit zvyšuje na 300k objekty. Pokud potřebujete ještě více objektů v Azure AD, musíte otevřít případ podpory, aby se tento limit ještě dál zvýšil. Pokud potřebujete více než 500 000 objekty, budete potřebovat licenci, jako je například Office 365, Azure AD Basic, Azure AD Premium nebo Enterprise mobility and Security.

### <a name="prepare-your-on-premises-data"></a>Příprava místních dat
* Použijte [IdFix](https://support.office.com/article/Install-and-run-the-Office-365-IdFix-tool-f4bd2439-3e41-4169-99f6-3fabdfa326ac) k identifikaci chyb, jako jsou duplicity a problémy s formátováním v adresáři, ještě než se synchronizujete do Azure AD a Office 365.
* Podívejte se [na volitelné funkce synchronizace, které můžete ve službě Azure AD povolit](how-to-connect-syncservice-features.md) , a vyhodnoťte, které funkce byste měli povolit.

### <a name="on-premises-active-directory"></a>Místní služby Active Directory
* Úroveň funkčnosti schématu AD a doménové struktury musí být Windows Server 2003 nebo novější. Řadiče domény můžou spouštět libovolnou verzi, pokud jsou splněné požadavky na úroveň schématu a doménové struktury.
* Pokud máte v úmyslu použít **zpětný zápis hesla**k funkci, musí být řadiče domény v systému Windows Server 2008 R2 nebo novějším.
* Řadič domény, který používá Azure AD, musí být zapisovatelný. Není **podporováno** použití řadiče domény jen pro čtení (řadič domény jen pro čtení) a Azure AD Connect nedodržuje žádné přesměrování zápisu.
* Používání místních doménových struktur a domén pomocí teček (název obsahuje tečku) se **nepodporuje** . Názvy rozhraní NetBios.
* Doporučuje se [Povolit Koš služby Active Directory](how-to-connect-sync-recycle-bin.md).

### <a name="azure-ad-connect-server"></a>Server Azure AD Connect
>[!IMPORTANT]
>Azure AD Connect Server obsahuje kritická data identity a měla by se považovat za součást vrstvy 0, jak je popsáno v [modelu vrstvy správy služby Active Directory](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material) .

* Azure AD Connect nejde nainstalovat na Small Business Server nebo Windows Server Essentials před 2019 (podporuje se Windows Server Essentials 2019). Server musí používat Windows Server Standard nebo vyšší.
* Instalace Azure AD Connect v řadiči domény se nedoporučuje z důvodu postupů zabezpečení a více omezujících nastavení, která můžou zabránit tomu, aby se Azure AD Connect správně nainstalovala.
* Azure AD Connect Server musí mít nainstalované úplné grafické rozhraní (GUI). Instalace na jádro serveru není **podporovaná** .
>[!IMPORTANT]
>Instalace Azure AD Connect na Small Business Server, Server Essentials nebo jádro serveru není podporovaná.

* Azure AD Connect musí být nainstalovaný na Windows Serveru 2012 nebo novějším. Tento server musí být připojený k doméně a může se jednat o řadič domény nebo členský server.
* Pokud ke správě konfigurace služby ADFS používáte Průvodce Azure AD Connect, Azure AD Connect Server nesmí mít povolený Zásady skupiny prostředí PowerShell přepis. Pokud ke správě konfigurace synchronizace používáte Azure AD Connect průvodce, můžete povolit PowerShellový přepis.
* Pokud je nasazen Active Directory Federation Services (AD FS), musí být servery, na kterých AD FS nebo proxy webových aplikací nainstalovány, systémy Windows Server 2012 R2 nebo novější. Pro vzdálenou instalaci musí být na těchto serverech povolená [Vzdálená správa systému Windows](#windows-remote-management) .
* Pokud nasazujete Active Directory Federation Services (AD FS), budete potřebovat [certifikáty SSL](#ssl-certificate-requirements).
* Pokud probíhá nasazení Active Directory Federation Services (AD FS), je nutné nakonfigurovat [Překlad adres IP](#name-resolution-for-federation-servers).
* Pokud mají globální správci povolené MFA, musí být adresa URL **https://secure.aadcdn.microsoftonline-p.com** v seznamu důvěryhodných serverů. Po zobrazení výzvy k zadání dotazu MFA se zobrazí výzva k přidání tohoto webu do seznamu důvěryhodných webů. Aplikaci Internet Explorer můžete použít k jejímu přidání do důvěryhodných webů.
* Společnost Microsoft doporučuje posílit Azure AD Connect Server, aby se snížila plocha pro útok na zabezpečení pro tuto kritickou součást vašeho IT prostředí.  Podle následujících doporučení se sníží rizika zabezpečení vaší organizace.

* Nasaďte Azure AD Connect na serveru připojeném k doméně a omezte přístup pro správu na správce domény nebo na jiné pevně kontrolované skupiny zabezpečení.

Další informace naleznete v tématu: 

* [Zabezpečení skupin správců](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-g--securing-administrators-groups-in-active-directory)

* [Zabezpečení předdefinovaných účtů správců](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-d--securing-built-in-administrator-accounts-in-active-directory)

* [Vylepšení a udržování zabezpečení snížením počtu napadených ploch](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access#2-reduce-attack-surfaces )

* [Snížení prostoru pro útoky na službu Active Directory](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface)

### <a name="sql-server-used-by-azure-ad-connect"></a>SQL Server používané Azure AD Connect
* Azure AD Connect vyžaduje k ukládání dat identity databázi SQL Serveru. Ve výchozím nastavení je nainstalovaná SQL Server 2012 Express LocalDB (světlá verze SQL Server Express). SQL Server Express má limit velikosti 10GB, který umožňuje spravovat přibližně 100 000 objektů. Pokud potřebujete spravovat větší objem objektů adresáře, musíte Průvodce instalací nasměrovat na jinou instalaci SQL Server. Typ SQL Server instalace může mít vliv na [výkon Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-performance-factors#sql-database-factors).
* Pokud používáte jinou instalaci SQL Server, platí tyto požadavky:
  * Azure AD Connect podporuje všechny verze Microsoft SQL Server z 2012 (s nejnovější aktualizací Service Pack) do SQL Server 2019. Microsoft Azure SQL Database není **podporován** jako databáze.
  * Je nutné použít kolaci SQL nerozlišující malá a velká písmena. Tyto kolace se identifikují pomocí \_CI_ v názvu. Použití kolace citlivého **na velká** a malá písmena, které identifikuje \_CS_ v názvu.
  * Můžete mít jenom jeden synchronizační modul na jednu instanci SQL. Pro sdílení instance SQL s nástrojem FIM/MIM Sync, DirSync nebo Azure AD Sync **není podporována** .

### <a name="accounts"></a>Účty
* Účet globálního správce Azure AD pro tenanta Azure AD, se kterým se chcete integrovat. Tento účet musí být **školním nebo organizačním účtem** a nemůže být **účet Microsoft**.
* Pokud používáte [expresní nastavení](reference-connect-accounts-permissions.md#express-settings-installation) nebo upgrade z DirSync, musíte mít účet správce rozlehlé sítě pro vaši místní službu Active Directory.
* Pokud použijete instalační cestu vlastní nastavení, zobrazí se vám další možnosti zobrazit [účty ve službě Active Directory](reference-connect-accounts-permissions.md#custom-installation-settings) .

### <a name="connectivity"></a>Připojení
* Azure AD Connect server potřebuje překlad DNS pro intranet i Internet. Server DNS musí být schopný přeložit názvy do vaší místní služby Active Directory a koncových bodů Azure AD.
* Pokud máte v intranetu brány firewall a potřebujete otevřít porty mezi Azure AD Connect servery a řadiči domény, další informace najdete v tématu [Azure AD Connect porty](reference-connect-ports.md) .
* Pokud váš proxy server nebo brána firewall omezují přistup k adresám URL, musí se otevřít adresy URL dokumentované v [adresách URL a rozsahech IP adres Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) .
  * Pokud používáte Microsoft Cloud v Německu nebo v cloudu Microsoft Azure Government, najdete informace v tématu [Azure AD Connect synchronizace instancí služby](reference-connect-instances.md) pro adresy URL.
* Azure AD Connect (verze 1.1.614.0 a After) ve výchozím nastavení používá protokol TLS 1,2 pro šifrování komunikace mezi synchronizačním modulem a službou Azure AD. Pokud není v podkladovém operačním systému k dispozici protokol TLS 1,2, Azure AD Connect přírůstkové ke starším protokolům (TLS 1,1 a TLS 1,0).
* Před verzí 1.1.614.0 Azure AD Connect ve výchozím nastavení používá TLS 1,0 pro šifrování komunikace mezi synchronizačním modulem a službou Azure AD. Pokud chcete přejít na TLS 1,2, postupujte podle kroků v části [Povolení TLS 1,2 pro Azure AD Connect](#enable-tls-12-for-azure-ad-connect).
* Pokud používáte pro připojení k Internetu odchozí proxy server, je třeba přidat následující nastavení v souboru **C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config** pro Průvodce instalací a Azure AD Connect synchronizaci, aby bylo možné se připojit k Internetu a službě Azure AD. Tento text musí být zadaný na konci souboru. V tomto kódu &lt;PROXYADDRESS&gt; představuje skutečnou IP adresu proxy serveru nebo název hostitele.

```
    <system.net>
        <defaultProxy>
            <proxy
            usesystemdefault="true"
            proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
            bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

* Pokud vaše proxy server vyžaduje ověření, musí se [účet služby](reference-connect-accounts-permissions.md#adsync-service-account) nacházet v doméně a k určení [vlastního účtu služby](how-to-connect-install-custom.md#install-required-components)musíte použít instalační cestu přizpůsobená nastavení. Také potřebujete jinou změnu souboru Machine. config. V rámci této změny v souboru Machine. config reaguje Průvodce instalací a synchronizační modul na žádosti o ověření z proxy server. V případě všech stránek průvodce instalací, kromě stránky **Konfigurace** , se použijí přihlašovací údaje přihlášeného uživatele. Na stránce **Konfigurace** na konci Průvodce instalací je kontext přepnut na [účet služby](reference-connect-accounts-permissions.md#adsync-service-account) , který byl vytvořen vámi. Oddíl Machine. config by měl vypadat takto.

```
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
            usesystemdefault="true"
            proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
            bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

* Když Azure AD Connect odešle webový požadavek do služby Azure AD jako součást synchronizace adresářů, může Azure AD trvat až 5 minut, než bude reagovat. U proxy serverů je běžné, že mají konfiguraci časového limitu nečinnosti připojení. Ujistěte se prosím, že je konfigurace nastavená aspoň na 6 minut nebo víc.

Další informace najdete v tématu MSDN o [výchozím elementu proxy serveru](https://msdn.microsoft.com/library/kd3cf2ex.aspx).  
Další informace o potížích s připojením najdete v tématu [řešení problémů s připojením](tshoot-connect-connectivity.md).

### <a name="other"></a>Další
* Volitelné: testovací uživatelský účet pro ověření synchronizace.

## <a name="component-prerequisites"></a>Předpoklady součásti
### <a name="powershell-and-net-framework"></a>PowerShell a .NET Framework
Azure AD Connect závisí na prostředí Microsoft PowerShell a .NET Framework 4.5.1. Budete potřebovat tuto verzi nebo novější verzi nainstalovanou na vašem serveru. V závislosti na verzi Windows serveru udělejte toto:

* Windows Server 2012R2
  * Prostředí Microsoft PowerShell je nainstalováno ve výchozím nastavení. Nevyžaduje se žádná akce.
  * K dispozici jsou .NET Framework 4.5.1 a novější verze prostřednictvím web Windows Update. Ujistěte se, že máte v Ovládacích panelech nainstalované nejnovější aktualizace Windows serveru.
* Windows Server 2012
  * Nejnovější verze prostředí Microsoft PowerShell je k dispozici v **rozhraní Windows Management Framework 4,0**, které je k dispozici na [webu Microsoft Download Center](https://www.microsoft.com/downloads).
  * .NET Framework 4.5.1 a novější verze jsou k dispozici na [webu Microsoft Download Center](https://www.microsoft.com/downloads).


### <a name="enable-tls-12-for-azure-ad-connect"></a>Povolit TLS 1,2 pro Azure AD Connect
Před verzí 1.1.614.0 Azure AD Connect ve výchozím nastavení používá TLS 1,0 pro šifrování komunikace mezi serverem synchronizačního stroje a službou Azure AD. To můžete změnit tak, že nakonfigurujete aplikace .NET tak, aby ve výchozím nastavení používaly TLS 1,2 standardně na serveru. Další informace o protokolu TLS 1,2 najdete v [zpravodaji zabezpečení společnosti Microsoft 2960358](https://technet.microsoft.com/security/advisory/2960358).

1.  Ujistěte se, že máte nainstalovanou opravu hotfix rozhraní .NET 4.5.1 pro váš operační systém, viz [Microsoft Security advisor 2960358](https://technet.microsoft.com/security/advisory/2960358). Je možné, že je tato oprava hotfix nebo novější verze v serveru již nainstalována.
    ```
2. For all operating systems, set this registry key and restart the server.
    ```
    HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft\.NETFramework\v4.0.30319 "do schusestrongcrypto" = DWORD: 00000001
    ```
4. If you also want to enable TLS 1.2 between the sync engine server and a remote SQL Server, then make sure you have the required versions installed for [TLS 1.2 support for Microsoft SQL Server](https://support.microsoft.com/kb/3135244).

## Prerequisites for federation installation and configuration
### Windows Remote Management
When using Azure AD Connect to deploy Active Directory Federation Services or the Web Application Proxy, check these requirements:

* If the target server is domain joined, then ensure that Windows Remote Managed is enabled
  * In an elevated PSH command window, use command `Enable-PSRemoting –force`
* If the target server is a non-domain joined WAP machine, then there are a couple of additional requirements
  * On the target machine (WAP machine):
    * Ensure the winrm (Windows Remote Management / WS-Management) service is running via the Services snap-in
    * In an elevated PSH command window, use command `Enable-PSRemoting –force`
  * On the machine on which the wizard is running (if the target machine is non-domain joined or untrusted domain):
    * In an elevated PSH command window, use the command `Set-Item WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate`
    * In Server Manager:
      * add DMZ WAP host to machine pool (server manager -> Manage -> Add Servers...use DNS tab)
      * Server Manager All Servers tab: right click WAP server and choose Manage As..., enter local (not domain) creds for the WAP machine
      * To validate remote PSH connectivity, in the Server Manager All Servers tab: right click WAP server and choose Windows PowerShell. A remote PSH session should open to ensure remote PowerShell sessions can be established.

### SSL Certificate Requirements
* It’s strongly recommended to use the same SSL certificate across all nodes of your AD FS farm and all Web Application proxy servers.
* The certificate must be an X509 certificate.
* You can use a self-signed certificate on federation servers in a test lab environment. However, for a production environment, we recommend that you obtain the certificate from a public CA.
  * If using a certificate that is not publicly trusted, ensure that the certificate installed on each Web Application Proxy server is trusted on both the local server and on all federation servers
* The identity of the certificate must match the federation service name (for example, sts.contoso.com).
  * The identity is either a subject alternative name (SAN) extension of type dNSName or, if there are no SAN entries, the subject name specified as a common name.  
  * Multiple SAN entries can be present in the certificate, provided one of them matches the federation service name.
  * If you are planning to use Workplace Join, an additional SAN is required with the value **enterpriseregistration.** followed by the User Principal Name (UPN) suffix of your organization, for example, **enterpriseregistration.contoso.com**.
* Certificates based on CryptoAPI next generation (CNG) keys and key storage providers are not supported. This means you must use a certificate based on a CSP (cryptographic service provider) and not a KSP (key storage provider).
* Wild-card certificates are supported.

### Name resolution for federation servers
* Set up DNS records for the AD FS federation service name (for example sts.contoso.com) for both the intranet (your internal DNS server) and the extranet (public DNS through your domain registrar). For the intranet DNS record, ensure that you use A records and not CNAME records. This is required for windows authentication to work correctly from your domain joined machine.
* If you are deploying more than one AD FS server or Web Application Proxy server, then ensure that you have configured your load balancer and that the DNS records for the AD FS federation service name (for example sts.contoso.com) point to the load balancer.
* For windows integrated authentication to work for browser applications using Internet Explorer in your intranet, ensure that the AD FS federation service name (for example sts.contoso.com) is added to the intranet zone in IE. This can be controlled via group policy and deployed to all your domain joined computers.

## Azure AD Connect supporting components
The following is a list of components that Azure AD Connect installs on the server where Azure AD Connect is installed. This list is for a basic Express installation. If you choose to use a different SQL Server on the Install synchronization services page, then SQL Express LocalDB is not installed locally.

* Azure AD Connect Health
* Microsoft SQL Server 2012 Command Line Utilities
* Microsoft SQL Server 2012 Express LocalDB
* Microsoft SQL Server 2012 Native Client
* Microsoft Visual C++ 2013 Redistribution Package

## Hardware requirements for Azure AD Connect
The table below shows the minimum requirements for the Azure AD Connect sync computer.

| Number of objects in Active Directory | CPU | Memory | Hard drive size |
| --- | --- | --- | --- |
| Fewer than 10,000 |1.6 GHz |4 GB |70 GB |
| 10,000–50,000 |1.6 GHz |4 GB |70 GB |
| 50,000–100,000 |1.6 GHz |16 GB |100 GB |
| For 100,000 or more objects the full version of SQL Server is required | | | |
| 100,000–300,000 |1.6 GHz |32 GB |300 GB |
| 300,000–600,000 |1.6 GHz |32 GB |450 GB |
| More than 600,000 |1.6 GHz |32 GB |500 GB |

The minimum requirements for computers running AD FS or Web Application Proxy Servers is the following:

* CPU: Dual core 1.6 GHz or higher
* MEMORY: 2 GB or higher
* Azure VM: A2 configuration or higher

## Next steps
Learn more about [Integrating your on-premises identities with Azure Active Directory](whatis-hybrid-identity.md).

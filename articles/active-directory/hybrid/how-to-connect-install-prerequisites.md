---
title: 'Azure AD Connect: Požadavky a hardware | Dokumenty společnosti Microsoft'
description: Toto téma popisuje předpoklady a hardwarové požadavky pro Azure AD Connect
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
ms.openlocfilehash: 6446b039d90e04c9fe7fca28b361f620183a0292
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875737"
---
# <a name="prerequisites-for-azure-ad-connect"></a>Požadavky pro Azure AD Connect
Toto téma popisuje předpoklady a hardwarové požadavky pro Azure AD Connect.

## <a name="before-you-install-azure-ad-connect"></a>Před instalací služby Azure AD Connect
Před instalací Služby Azure AD Connect potřebujete několik věcí.

### <a name="azure-ad"></a>Azure AD
* Tenanta Azure AD. Získáte jednu s [bezplatnou zkušební verzí Azure](https://azure.microsoft.com/pricing/free-trial/). Ke správě Služby Azure AD Connect můžete použít jeden z následujících portálů:
  * Portál [Azure](https://portal.azure.com).
  * [Portál Office](https://portal.office.com).  
* [Přidejte a ověřte doménu,](../active-directory-domains-add-azure-portal.md) kterou chcete použít ve službě Azure AD. Pokud například plánujete použít contoso.com pro uživatele, ujistěte se, že tato doména byla ověřena a nepoužíváte pouze výchozí doménu contoso.onmicrosoft.com.
* Klient Azure AD umožňuje ve výchozím nastavení 50 kB objekty. Při ověření domény se limit zvýší na 300 tisíc objektů. Pokud potřebujete ještě více objektů ve službě Azure AD, pak je potřeba otevřít případ podpory, aby se limit ještě zvýšil. Pokud potřebujete více než 500 tisíc objektů, pak potřebujete licenci, jako je Office 365, Azure AD Basic, Azure AD Premium nebo Enterprise Mobility and Security.

### <a name="prepare-your-on-premises-data"></a>Příprava místních dat
* [IdFix](https://support.office.com/article/Install-and-run-the-Office-365-IdFix-tool-f4bd2439-3e41-4169-99f6-3fabdfa326ac) slouží k identifikaci chyb, jako jsou duplicity a problémy s formátováním ve vašem adresáři před synchronizací s Azure AD a Office 365.
* Projděte si [volitelné funkce synchronizace, které můžete povolit ve službě Azure AD,](how-to-connect-syncservice-features.md) a vyhodnoťte, které funkce byste měli povolit.

### <a name="on-premises-active-directory"></a>Místní služby Active Directory
* Verze schématu služby AD a úroveň funkčnosti doménové struktury musí být Windows Server 2003 nebo novější. Řadiče domény mohou spouštět libovolnou verzi, pokud jsou splněny požadavky na úroveň schématu a doménové struktury.
* Pokud chcete použít **funkci vrácení hesla zpět**, musí být řadiče domény v systému Windows Server 2008 R2 nebo novějším.
* Řadič domény používaný službou Azure AD musí být zapisovatelný. Není **podporováno** použití řadiče domény jen pro čtení (řadič domény jen pro čtení) a Azure AD Connect nesleduje žádné přesměrování zápisu.
* Není **podporováno** použití místních doménových struktur nebo domén pomocí tečkované (název obsahuje tečku "."). NetBios názvy.
* Doporučujeme [povolit koš služby Active Directory](how-to-connect-sync-recycle-bin.md).

### <a name="azure-ad-connect-server"></a>Server Azure AD Connect
>[!IMPORTANT]
>Server Azure AD Connect obsahuje důležitá data identity a měl by být považován za komponentu Tier 0 jako zdokumentopozincouvanou v [modelu vrstvy správy služby Active Directory.](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)

* Azure AD Connect nejde nainstalovat na Small Business Server nebo Windows Server Essentials před rokem 2019 (windows server Essentials 2019 je podporovaný). Server musí používat standardní nebo lepší systém Windows Server.
* Instalace služby Azure AD Connect na řadič domény se nedoporučuje z důvodu postupů zabezpečení a restriktivnějších nastavení, která mohou bránit správné instalaci služby Azure AD Connect.
* Na serveru Azure AD Connect musí být nainstalováno úplné grafické uživatelské rozhraní. Instalace na jádro serveru **není podporována.**
>[!IMPORTANT]
>Instalace služby Azure AD Connect na serveru pro malé firmy, základní informace o serveru nebo jádru serveru není podporována.

* Azure AD Connect musí být nainstalované na Windows Server 2012 nebo novější. Tento server musí být připojen k doméně a může být řadičem domény nebo členským serverem.
* Server Azure AD Connect nesmí mít povolené zásady skupiny Přepisování prostředí PowerShell, pokud ke správě konfigurace Služby ADFS používáte průvodce Azure AD Connect. Přepis prostředí PowerShell můžete povolit, pokud ke správě konfigurace synchronizace používáte průvodce Azure AD Connect.
* Pokud se nasazuje služba AD FS, musí být servery, na kterých je nainstalována služba AD FS nebo proxy webové aplikace, windows server 2012 R2 nebo novější. Pro vzdálenou instalaci musí být na těchto serverech [povolena vzdálená správa](#windows-remote-management) systému Windows.
* Pokud se nasazuje služba AD FS, potřebujete [certifikáty TLS/SSL](#tlsssl-certificate-requirements).
* Pokud se nasazuje služba AD FS, je třeba nakonfigurovat [překlad názvů](#name-resolution-for-federation-servers).
* Pokud mají globální správci povolenou vícefaktorovou autoritu, musí být adresa URL **https://secure.aadcdn.microsoftonline-p.com** uvedena v seznamu důvěryhodných serverů. Pokud budete vyzváni k zadání výzvy k žádosti o vícefaktorové řešení a dosud nebyla přidána, budete vyzváni k přidání tohoto webu do seznamu důvěryhodných serverů. Pomocí aplikace Internet Explorer ji můžete přidat na důvěryhodné servery.
* Společnost Microsoft doporučuje zpevnění serveru Azure AD Connect snížit úroveň útoku zabezpečení pro tuto důležitou součást vašeho IT prostředí.  Podle níže uvedených doporučení snížíte bezpečnostní rizika pro vaši organizaci.

* Nasazení služby Azure AD Connect na serveru připojení k doméně a omezení přístupu správce pro správu na správce domény nebo jiné přísně řízené skupiny zabezpečení.

Další informace naleznete v tématu: 

* [Zabezpečení skupin správců](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-g--securing-administrators-groups-in-active-directory)

* [Zabezpečení předdefinovaných účtů správce](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-d--securing-built-in-administrator-accounts-in-active-directory)

* [Zlepšení zabezpečení a udržení snížením útočných ploch](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access#2-reduce-attack-surfaces )

* [Zmenšení povrchu útoku služby Active Directory](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface)

### <a name="sql-server-used-by-azure-ad-connect"></a>SQL Server používaný službou Azure AD Connect
* Azure AD Connect vyžaduje k ukládání dat identity databázi SQL Serveru. Ve výchozím nastavení je nainstalován SQL Server 2012 Express LocalDB (light verze SQL Server Express) je nainstalován. SQL Server Express má limit velikosti 10 GB, který umožňuje spravovat přibližně 100 000 objektů. Pokud potřebujete spravovat vyšší objem objektů adresáře, je třeba nasměrovat průvodce instalací na jinou instalaci serveru SQL Server. Typ instalace serveru SQL Server může mít vliv na [výkon služby Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-performance-factors#sql-database-factors).
* Pokud používáte jinou instalaci sql serveru, platí tyto požadavky:
  * Azure AD Connect podporuje všechny verze Microsoft SQL Serveru od roku 2012 (s nejnovější aktualizací Service Pack) až po SQL Server 2019. Microsoft Azure SQL Database **není podporována** jako databáze.
  * Je nutné použít řazení SQL bez rozlišování velkých a malých písmen. Tyto kolace jsou označeny \_CI_ v jejich jménu. Není **podporováno** použití řazení s rozlišování \_velkých a malých písmen, identifikované CS_ v jejich názvu.
  * Můžete mít pouze jeden modul synchronizace na instanci SQL. Není **podporováno** pro sdílení instance SQL s FIM/MIM Sync, DirSync nebo Azure AD Sync.

### <a name="accounts"></a>Účty
* Účet globálního správce Azure AD pro klienta Azure AD, se kterým chcete integrovat. Tento účet musí být **školní nebo organizační účet** a nemůže být **účtem Microsoft**.
* Pokud používáte [expresní nastavení](reference-connect-accounts-permissions.md#express-settings-installation) nebo upgradujete z DirSync, musíte mít účet enterprise administrator pro místní službu Active Directory.
* Pokud používáte vlastní nastavení instalační cesty pak máte více možností. Další informace naleznete [v tématu Vlastní nastavení instalace](reference-connect-accounts-permissions.md#custom-installation-settings).

### <a name="connectivity"></a>Připojení
* Server Azure AD Connect potřebuje překlad DNS pro intranet i internet. Dns server musí být schopen přeložit názvy do místní služby Active Directory a koncových bodů Azure AD.
* Pokud máte v intranetu brány firewall a potřebujete otevřít porty mezi servery Azure AD Connect a řadiči domény, přečtěte si další informace najdete v tématu [Porty připojení Azure AD.](reference-connect-ports.md)
* Pokud váš proxy server nebo brána firewall omezují přístup k adresám URL, musí být otevřeny adresy URL zdokumentované v [adresách URL Office 365 a rozsahy IP adres.](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
  * Pokud používáte Microsoft Cloud v Německu nebo cloud Microsoft Azure Government, přečtěte si informace [o aspektech instance synchronizační služby Azure AD Connect](reference-connect-instances.md) pro adresy URL.
* Azure AD Connect (verze 1.1.614.0 a po) ve výchozím nastavení používá TLS 1.2 pro šifrování komunikace mezi synchronizačním strojem a Azure AD. Pokud TLS 1.2 není k dispozici v základním operačním systému, Azure AD Connect postupně přejde zpět na starší protokoly (TLS 1.1 a TLS 1.0).
* Před verzí 1.1.614.0 azure ad připojení ve výchozím nastavení používá TLS 1.0 pro šifrování komunikace mezi synchronizačním strojem a Azure AD. Chcete-li přejít na TLS 1.2, postupujte podle pokynů v [části Povolit TLS 1.2 pro Azure AD Connect](#enable-tls-12-for-azure-ad-connect).
* Pokud pro připojení k Internetu používáte odchozí proxy server, musí být pro průvodce instalací a synchronizace Azure AD Connect možné připojení k Internetu a Azure AD přidáno následující nastavení v souboru **C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config.** Tento text musí být zadán v dolní části souboru. V tomto &lt;kódu&gt; proxyaddress představuje skutečnou adresu IP proxy nebo název hostitele.

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

* Pokud proxy server vyžaduje ověření, musí být [účet služby](reference-connect-accounts-permissions.md#adsync-service-account) umístěn v doméně a k určení [vlastního účtu služby](how-to-connect-install-custom.md#install-required-components)je nutné použít vlastní instalační cestu nastavení . Potřebujete také jinou změnu machine.config. Při této změně v souboru machine.config průvodce instalací a synchronizační modul reagují na požadavky na ověření z proxy serveru. Na všech stránkách průvodce instalací, s výjimkou stránky **Konfigurovat,** se používají přihlašovací údaje přihlášených uživatelů. Na stránce **Konfigurovat** na konci průvodce instalací je kontext přepnut na [účet služby,](reference-connect-accounts-permissions.md#adsync-service-account) který jste vytvořili. Sekce machine.config by měla vypadat takto.

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

* Když Azure AD Connect odešle webový požadavek do Azure AD jako součást synchronizace adresářů, Azure AD může trvat až 5 minut reagovat. Je běžné, že proxy servery mají konfiguraci časového limitu nečinnosti připojení. Zkontrolujte, zda je konfigurace nastavena na nejméně 6 minut nebo více.

Další informace naleznete v tématu MSDN o [výchozím prvku proxy](https://msdn.microsoft.com/library/kd3cf2ex.aspx).  
Další informace v případě problémů s připojením naleznete v [tématu Poradce při potížích](tshoot-connect-connectivity.md)s připojením .

### <a name="other"></a>Ostatní
* Volitelné: Testovací uživatelský účet pro ověření synchronizace.

## <a name="component-prerequisites"></a>Požadavky komponent
### <a name="powershell-and-net-framework"></a>Prostředí PowerShell a rozhraní .NET Framework
Azure AD Connect závisí na Microsoft PowerShell a .NET Framework 4.5.1. Tuto verzi nebo novější verzi potřebujete nainstalovanou na serveru. V závislosti na verzi systému Windows Server postupujte takto:

* Windows Server 2012R2
  * Microsoft PowerShell je nainstalován ve výchozím nastavení. Nevyžaduje se žádná akce.
  * Rozhraní .NET Framework 4.5.1 a novější verze jsou nabízeny prostřednictvím služby Windows Update. Zkontrolujte, zda jste v Ovládacích panelech nainstalovali nejnovější aktualizace systému Windows Server.
* Windows Server 2012
  * Nejnovější verze prostředí Microsoft PowerShell je k dispozici v **rozhraní Windows Management Framework 4.0**, který je k dispozici na webu [Služby stažení softwaru](https://www.microsoft.com/downloads).
  * Rozhraní .NET Framework 4.5.1 a novější verze jsou k dispozici na [webu Služby stažení softwaru](https://www.microsoft.com/downloads).


### <a name="enable-tls-12-for-azure-ad-connect"></a>Povolení TLS 1.2 pro Azure AD Connect
Před verzí 1.1.614.0 azure ad připojení ve výchozím nastavení používá TLS 1.0 pro šifrování komunikace mezi serverem synchronizačního modulu a Azure AD. Tuto možnost můžete změnit konfigurací aplikací .NET tak, aby ve výchozím nastavení používaly tls 1.2 na serveru. Další informace o protokolu TLS 1.2 naleznete v [informačním zpravodaji zabezpečení společnosti Microsoft 2960358](https://technet.microsoft.com/security/advisory/2960358).

1.  Zkontrolujte, zda je pro operační systém nainstalována oprava hotfix .NET 4.5.1, viz [Informační zpravodaj zabezpečení společnosti Microsoft 2960358](https://technet.microsoft.com/security/advisory/2960358). Je možné, že tato oprava hotfix nebo novější verze již nainstalována na serveru.
    ```
2. For all operating systems, set this registry key and restart the server.
    ```
    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319 "SchUseStrongCrypto"=dword:00000001
    ```
4. If you also want to enable TLS 1.2 between the sync engine server and a remote SQL Server, then make sure you have the required versions installed for [TLS 1.2 support for Microsoft SQL Server](https://support.microsoft.com/kb/3135244).

## Prerequisites for federation installation and configuration
### Windows Remote Management
When using Azure AD Connect to deploy Active Directory Federation Services or the Web Application Proxy, check these requirements:

* If the target server is domain joined, then ensure that Windows Remote Managed is enabled
  * In an elevated PowerShell command window, use command `Enable-PSRemoting –force`
* If the target server is a non-domain joined WAP machine, then there are a couple of additional requirements
  * On the target machine (WAP machine):
    * Ensure the winrm (Windows Remote Management / WS-Management) service is running via the Services snap-in
    * In an elevated PowerShell command window, use command `Enable-PSRemoting –force`
  * On the machine on which the wizard is running (if the target machine is non-domain joined or untrusted domain):
    * In an elevated PowerShell command window, use the command `Set-Item WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate`
    * In Server Manager:
      * add DMZ WAP host to machine pool (server manager -> Manage -> Add Servers...use DNS tab)
      * Server Manager All Servers tab: right click WAP server and choose Manage As..., enter local (not domain) creds for the WAP machine
      * To validate remote PowerShell connectivity, in the Server Manager All Servers tab: right click WAP server and choose Windows PowerShell. A remote PowerShell session should open to ensure remote PowerShell sessions can be established.

### TLS/SSL Certificate Requirements
* It's strongly recommended to use the same TLS/SSL certificate across all nodes of your AD FS farm and all Web Application proxy servers.
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

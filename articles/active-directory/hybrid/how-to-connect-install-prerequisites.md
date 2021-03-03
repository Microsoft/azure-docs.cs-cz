---
title: 'Azure AD Connect: požadavky a hardware | Microsoft Docs'
description: Tento článek popisuje požadavky a požadavky na hardware pro Azure AD Connect.
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
ms.topic: how-to
ms.date: 02/16/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 000b91529b3b02f2c7d03b50ddc841c68625dada
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101644799"
---
# <a name="prerequisites-for-azure-ad-connect"></a>Požadavky pro Azure AD Connect
Tento článek popisuje požadavky a požadavky na hardware pro službu Azure Active Directory (Azure AD) Connect.

## <a name="before-you-install-azure-ad-connect"></a>Než nainstalujete Azure AD Connect
Než nainstalujete Azure AD Connect, budete potřebovat několik věcí.

### <a name="azure-ad"></a>Azure AD
* Potřebujete tenanta Azure AD. Získáte ji s [bezplatnou zkušební verzí Azure](https://azure.microsoft.com/pricing/free-trial/). Ke správě Azure AD Connect můžete použít jeden z následujících portálů:
  * [Azure Portal](https://portal.azure.com).
  * [Portál Office](https://portal.office.com).
* [Přidejte a ověřte doménu](../fundamentals/add-custom-domain.md) , kterou plánujete použít ve službě Azure AD. Pokud například plánujete používat contoso.com pro uživatele, ujistěte se, že je tato doména ověřená a že nepoužíváte pouze výchozí doménu contoso.onmicrosoft.com.
* Tenant Azure AD umožňuje ve výchozím nastavení 50 000 objektů. Při ověřování domény se limit zvyšuje na 300 000 objektů. Pokud ve službě Azure AD potřebujete ještě více objektů, otevřete případ podpory, abyste tento limit ještě víc zvýšili. Potřebujete-li více než 500 000 objektů, budete potřebovat licenci, například Microsoft 365, Azure AD Premium nebo Enterprise Mobility + Security.

### <a name="prepare-your-on-premises-data"></a>Příprava místních dat
* Použijte [IdFix](https://support.office.com/article/Install-and-run-the-Office-365-IdFix-tool-f4bd2439-3e41-4169-99f6-3fabdfa326ac) k identifikaci chyb, jako jsou duplicity a problémy s formátováním v adresáři, ještě než se synchronizujete do Azure AD a Microsoft 365.
* Podívejte se [na volitelné funkce synchronizace, které můžete ve službě Azure AD povolit](how-to-connect-syncservice-features.md), a vyhodnoťte, které funkce byste měli povolit.

### <a name="on-premises-active-directory"></a>Místní služby Active Directory
* Úroveň funkčnosti schématu Active Directory a doménové struktury musí být Windows Server 2003 nebo novější. Řadiče domény můžou spouštět libovolnou verzi, pokud jsou splněné verze schématu a požadavky na úrovni doménové struktury.
* Pokud plánujete použít *zpětný zápis hesla*, musí být řadiče domény v systému Windows Server 2012 nebo novějším.
* Řadič domény, který používá Azure AD, musí být zapisovatelný. Použití řadiče domény jen pro čtení (RODC) *není podporované* a Azure AD Connect nedodržují žádné přesměrování zápisu.
* Použití místních doménových struktur nebo domén pomocí teček (název obsahuje tečku ".") Názvy rozhraní NetBIOS *nejsou podporovány*.
* Doporučujeme [Povolit složku Koš služby Active Directory](how-to-connect-sync-recycle-bin.md).

### <a name="powershell-execution-policy"></a>Zásady spouštění skriptu PowerShell
Azure Active Directory Connect v rámci instalace spouští podepsané skripty PowerShellu. Ujistěte se, že zásady spouštění prostředí PowerShell umožní spouštění skriptů.

Doporučené zásady spouštění během instalace jsou "RemoteSigned".

Další informace o nastavení zásad spouštění prostředí PowerShell najdete v tématu [Set-ExecutionPolicy](/powershell/module/microsoft.powershell.security/set-executionpolicy).


### <a name="azure-ad-connect-server"></a>Server Azure AD Connect
Server Azure AD Connect obsahuje kritická data identity. Je důležité, aby byl přístup pro správu k tomuto serveru správně zabezpečený. Postupujte podle pokynů v části [zabezpečení privilegovaného přístupu](/windows-server/identity/securing-privileged-access/securing-privileged-access). 

Azure AD Connect Server musí být považován za součást vrstvy 0, jak je popsáno v [modelu vrstvy správy služby Active Directory](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material) . 

Další informace o zabezpečení prostředí Active Directory najdete v tématu [osvědčené postupy pro zabezpečení služby Active Directory](/windows-server/identity/ad-ds/plan/security-best-practices/best-practices-for-securing-active-directory).

#### <a name="installation-prerequisites"></a>Instalační požadavky

- Azure AD Connect musí být nainstalovaný na Windows Serveru 2012 nebo novějším připojeném k doméně. 
- Azure AD Connect nejde nainstalovat na Small Business Server nebo Windows Server Essentials před 2019 (podporuje se Windows Server Essentials 2019). Server musí používat Windows Server Standard nebo vyšší. 
- Azure AD Connect Server musí mít nainstalované úplné grafické rozhraní (GUI). Instalace Azure AD Connect v jádru Windows serveru se nepodporuje. 
- Pokud používáte Průvodce Azure AD Connect ke správě konfigurace Active Directory Federation Services (AD FS) (AD FS), Azure AD Connect Server nesmí mít povolený přepis prostředí PowerShell Zásady skupiny. Přepisy prostředí PowerShell můžete povolit, pokud ke správě konfigurace synchronizace použijete Průvodce Azure AD Connect. 
- Při nasazení AD FS: 
    - Servery, kde jsou nainstalované AD FS nebo proxy webových aplikací, musí být Windows Server 2012 R2 nebo novější. Pro vzdálenou instalaci musí být na těchto serverech povolená Vzdálená správa systému Windows. 
    - Je nutné nakonfigurovat certifikáty TLS/SSL. Další informace najdete v tématu [Správa protokolů SSL/TLS a šifrovacích sad pro AD FS](/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs) a [správu certifikátů SSL v AD FS](/windows-server/identity/ad-fs/operations/manage-ssl-certificates-ad-fs-wap).
    - Je nutné nakonfigurovat překlad adres IP. 
- Pokud mají globální správci povolené MFA, https://secure.aadcdn.microsoftonline-p.com *musí* být adresa URL v seznamu důvěryhodných serverů. Po zobrazení výzvy k zadání dotazu MFA se zobrazí výzva k přidání tohoto webu do seznamu důvěryhodných webů. Aplikaci Internet Explorer můžete použít k jejímu přidání do důvěryhodných webů.
- Pokud plánujete použít Azure AD Connect Health pro synchronizaci, zajistěte, aby byly splněny také předpoklady pro Azure AD Connect Health. Další informace najdete v tématu [instalace agenta Azure AD Connect Health](how-to-connect-health-agent-install.md).

#### <a name="harden-your-azure-ad-connect-server"></a>Posílení zabezpečení Azure AD Connect serveru 
Doporučujeme, abyste Server Azure AD Connect, abyste snížili plochu pro útok na zabezpečení pro tuto kritickou součást vašeho IT prostředí. Následující doporučení vám pomůžou zmírnit rizika zabezpečení vaší organizace.

- Považovat Azure AD Connect za stejný jako řadič domény a další prostředky vrstvy 0. Další informace najdete v tématu [model vrstvy správy služby Active Directory](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material).
- Omezte přístup pro správu serveru Azure AD Connect jenom na správce domény nebo na jiné skupiny zabezpečení, které jsou pod kontrolou.
- Vytvořte [vyhrazený účet pro všechny pracovníky s privilegovaným přístupem](/windows-server/identity/securing-privileged-access/securing-privileged-access). Správci by neměli procházet web, kontrolovat jejich e-maily a provádět každodenní úkoly s vysoce privilegovanými účty.
- Postupujte podle pokynů uvedených v části [zabezpečení privilegovaného přístupu](/windows-server/identity/securing-privileged-access/securing-privileged-access). 
- Zamítnout použití ověřování NTLM se serverem AADConnect Tady je několik způsobů, jak to provést: [omezení NTLM na serveru AADConnect](/windows/security/threat-protection/security-policy-settings/network-security-restrict-ntlm-outgoing-ntlm-traffic-to-remote-servers) a [omezení NTLM v doméně](/windows/security/threat-protection/security-policy-settings/network-security-restrict-ntlm-ntlm-authentication-in-this-domain) .
- Zajistěte, aby měl každý počítač jedinečné heslo místního správce. Další informace najdete v tématu [řešení hesla místního správce (LAPS)](https://support.microsoft.com/help/3062591/microsoft-security-advisory-local-administrator-password-solution-laps) může konfigurovat jedinečná náhodná hesla na každé pracovní stanici a na serveru, kde jsou v ní chráněná seznamem ACL. Resetovat hesla těchto účtů místních správců můžou číst nebo žádat jenom oprávnění autorizovaní uživatelé. LAPS můžete získat pro použití na pracovních stanicích a serverech z [webu Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=46899). Další pokyny pro provoz prostředí s LAPS a privilegovaným přístupem (Privileged Access Workstations) najdete v [provozních standardech na základě principu čistého zdroje](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material#operational-standards-based-on-clean-source-principle). 
- Implementujte vyhrazené [pracovní stanice s privilegovaným přístupem](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) pro všechny pracovníky s privilegovaným přístupem k informačním systémům vaší organizace. 
- Pomocí těchto [dalších pokynů](/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface) můžete omezit plochu útoku prostředí Active Directory.


### <a name="sql-server-used-by-azure-ad-connect"></a>SQL Server používaný službou Azure AD Connect
* Azure AD Connect vyžaduje k ukládání dat identity databázi SQL Serveru. Ve výchozím nastavení je nainstalovaná SQL Server 2012 Express LocalDB (světlá verze SQL Server Express). SQL Server Express má limit velikosti 10 GB, který umožňuje správu přibližně 100 000 objektů. Pokud potřebujete spravovat větší objem objektů adresáře, nastavte Průvodce instalací na jinou instalaci SQL Server. Typ SQL Server instalace může mít vliv na [výkon Azure AD Connect](./plan-connect-performance-factors.md#sql-database-factors).
* Pokud používáte jinou instalaci SQL Server, platí tyto požadavky:
  * Azure AD Connect podporuje všechny verze SQL Server od 2012 (s nejnovější aktualizací Service Pack) až do SQL Server 2019. Azure SQL Database se jako databáze *nepodporuje* .
  * Je nutné použít kolaci SQL nerozlišující malá a velká písmena. Tyto kolace se identifikují pomocí \_ CI_ v názvu. Použití kolace citlivého na velká a malá písmena, která identifikuje \_ CS_ ve svém názvu, se *nepodporuje*.
  * Můžete mít pouze jeden synchronizační modul na jednu instanci SQL. Sdílení instance SQL s nástrojem FIM/MIM Sync, DirSync nebo Azure AD Sync se *nepodporuje*.

### <a name="accounts"></a>Účty
* Musíte mít účet globálního správce služby Azure AD pro tenanta Azure AD, se kterým chcete integrovat. Tento účet musí být *školním nebo organizačním účtem* a nemůže být *účet Microsoft*.
* Pokud používáte [expresní nastavení](reference-connect-accounts-permissions.md#express-settings-installation) nebo upgrade z DirSync, musíte mít účet správce rozlehlé sítě pro vaši místní službu Active Directory.
* Pokud použijete instalační cestu k vlastnímu nastavení, máte více možností. Další informace najdete v tématu [vlastní nastavení instalace](reference-connect-accounts-permissions.md#custom-installation-settings).

### <a name="connectivity"></a>Připojení
* Azure AD Connect server potřebuje překlad DNS pro intranet i Internet. Server DNS musí být schopný přeložit názvy do vaší místní služby Active Directory a koncových bodů Azure AD.
* Pokud máte v intranetu brány firewall a potřebujete otevřít porty mezi Azure AD Connect servery a řadiči domény, přečtěte si další informace v tématu [Azure AD Connect porty](reference-connect-ports.md) .
* Pokud váš proxy server nebo brána firewall omezují pøístup k adresám URL, musí se otevřít adresy URL dokumentované v [adresách URL Office 365 a rozsahy IP adres](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) . Viz také [Safelist adresy url Azure Portal v bráně firewall nebo proxy server](../../azure-portal/azure-portal-safelist-urls.md?tabs=public-cloud).
  * Pokud používáte Cloud Microsoftu v Německu nebo Microsoft Azure Government cloudu, přečtěte si téma [informace o Azure AD Connect synchronizace instancí služby](reference-connect-instances.md) pro adresy URL.
* Azure AD Connect (verze 1.1.614.0 a After) ve výchozím nastavení používá protokol TLS 1,2 pro šifrování komunikace mezi synchronizačním modulem a službou Azure AD. Pokud není v podkladovém operačním systému k dispozici protokol TLS 1,2, Azure AD Connect přírůstkové ke starším protokolům (TLS 1,1 a TLS 1,0).
* Před verzí 1.1.614.0 Azure AD Connect ve výchozím nastavení používá TLS 1,0 pro šifrování komunikace mezi synchronizačním modulem a službou Azure AD. Pokud chcete přejít na TLS 1,2, postupujte podle kroků v části [Povolení TLS 1,2 pro Azure AD Connect](#enable-tls-12-for-azure-ad-connect).
* Pokud používáte pro připojení k Internetu odchozí proxy server, je nutné pro Průvodce instalací přidat následující nastavení **C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config** souboru a Azure AD Connect synchronizovat, aby bylo možné se připojit k Internetu a službě Azure AD. Tento text musí být zadaný na konci souboru. V tomto kódu *&lt; PROXYADDRESS &gt;* představuje skutečnou IP adresu proxy serveru nebo název hostitele.

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

* Pokud vaše proxy server vyžaduje ověření, musí být [účet služby](reference-connect-accounts-permissions.md#adsync-service-account) umístěný v doméně. K určení [vlastního účtu služby](how-to-connect-install-custom.md#install-required-components)použijte cestu k instalaci přizpůsobených nastavení. Také potřebujete jinou změnu machine.config. Při této změně v machine.config reaguje Průvodce instalací a synchronizační modul na žádosti o ověření z proxy server. V případě všech stránek průvodce instalací, kromě stránky **Konfigurace** , se použijí přihlašovací údaje přihlášeného uživatele. Na stránce **Konfigurace** na konci Průvodce instalací je kontext přepnut na [účet služby](reference-connect-accounts-permissions.md#adsync-service-account) , který jste vytvořili. Oddíl machine.config by měl vypadat takto:

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

* Pokud se konfigurace proxy provádí v existující instalaci, je třeba restartovat **službu Microsoft Azure AD Sync** , jakmile Azure AD Connect načte konfiguraci proxy serveru a aktualizuje chování. 
* Když Azure AD Connect odešle webový požadavek do služby Azure AD jako součást synchronizace adresářů, může Azure AD trvat až 5 minut, než bude reagovat. U proxy serverů je běžné, že mají konfiguraci časového limitu nečinnosti připojení. Ujistěte se, že je konfigurace nastavená aspoň na 6 minut nebo víc.

Další informace najdete v tématu MSDN o [výchozím elementu proxy serveru](/dotnet/framework/configure-apps/file-schema/network/defaultproxy-element-network-settings).
Další informace o potížích s připojením najdete v tématu [řešení problémů s připojením](tshoot-connect-connectivity.md).

### <a name="other"></a>Jiné
Volitelné: k ověření synchronizace použijte účet testovacího uživatele.

## <a name="component-prerequisites"></a>Předpoklady součásti
### <a name="powershell-and-net-framework"></a>PowerShell a .NET Framework
Azure AD Connect závisí na prostředí Microsoft PowerShell a .NET Framework 4.5.1. Budete potřebovat tuto verzi nebo novější verzi nainstalovanou na vašem serveru. V závislosti na verzi Windows serveru proveďte tyto akce:

* Windows Server 2012 R2
  * Prostředí Microsoft PowerShell je nainstalováno ve výchozím nastavení. Nevyžaduje se žádná akce.
  * K dispozici jsou .NET Framework 4.5.1 a novější verze prostřednictvím web Windows Update. Ujistěte se, že jste nainstalovali nejnovější aktualizace Windows serveru v Ovládacích panelech.
* Windows Server 2012
  * Nejnovější verze prostředí Microsoft PowerShell je k dispozici v rozhraní Windows Management Framework 4,0, které je k dispozici na [webu Microsoft Download Center](https://www.microsoft.com/downloads).
  * .NET Framework 4.5.1 a novějších verzích jsou k dispozici na [webu Microsoft Download Center](https://www.microsoft.com/downloads).


### <a name="enable-tls-12-for-azure-ad-connect"></a>Povolit TLS 1,2 pro Azure AD Connect
Před verzí 1.1.614.0 Azure AD Connect ve výchozím nastavení používá TLS 1,0 pro šifrování komunikace mezi serverem synchronizačního stroje a službou Azure AD. Aplikace .NET můžete nakonfigurovat tak, aby ve výchozím nastavení používaly TLS 1,2 standardně na serveru. Další informace o TLS 1,2 najdete v tématu [Microsoft Security advisor 2960358](/security-updates/SecurityAdvisories/2015/2960358).

1. Ujistěte se, že máte nainstalovanou opravu hotfix rozhraní .NET 4.5.1 pro váš operační systém. Další informace najdete v tématu [Microsoft Security advisor 2960358](/security-updates/SecurityAdvisories/2015/2960358). Je možné, že je tato oprava hotfix nebo novější verze v serveru již nainstalována.

1. Pro všechny operační systémy nastavte tento klíč registru a restartujte server.
    ```
    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319
    "SchUseStrongCrypto"=dword:00000001
    ```
1. Pokud také chcete povolit TLS 1,2 mezi serverem synchronizace a vzdáleným SQL Server, ujistěte se, že máte nainstalované požadované verze pro [podporu TLS 1,2 pro Microsoft SQL Server](https://support.microsoft.com/kb/3135244).

### <a name="dcom-prerequisites-on-the-synchronization-server"></a>Požadavky modelu DCOM na synchronizační Server
Během instalace synchronizační služby Azure AD Connect zkontroluje přítomnost následujícího klíče registru:

- HKEY_LOCAL_MACHINE: Software\Microsoft\Ole

V rámci tohoto klíče registru Azure AD Connect zkontroluje, jestli jsou k dispozici následující hodnoty a nepoškozená: 

- [MachineAccessRestriction](/windows/win32/com/machineaccessrestriction)
- [MachineLaunchRestriction](/windows/win32/com/machinelaunchrestriction)
- [DefaultLaunchPermission](/windows/win32/com/defaultlaunchpermission)

## <a name="prerequisites-for-federation-installation-and-configuration"></a>Předpoklady pro instalaci a konfiguraci federace
### <a name="windows-remote-management"></a>Vzdálená správa systému Windows
Při použití Azure AD Connect k nasazení AD FS nebo proxy webové aplikace (WAP) si Projděte tyto požadavky:

* Pokud je cílový server připojený k doméně, ujistěte se, že je povolená Vzdálená správa systému Windows.
  * V okně příkazového řádku PowerShellu se zvýšenými oprávněními použijte příkaz `Enable-PSRemoting –force` .
* Pokud je cílový Server počítač WAP, který není připojený k doméně, existuje několik dalších požadavků:
  * Na cílovém počítači (WAP Machine):
    * Zajistěte, aby služba Vzdálená správa systému Windows/WS-Management (WinRM) běžela prostřednictvím modulu snap-in služby.
    * V okně příkazového řádku PowerShellu se zvýšenými oprávněními použijte příkaz `Enable-PSRemoting –force` .
  * V počítači, na kterém je spuštěný Průvodce (Pokud cílový počítač není připojený k doméně nebo se jedná o nedůvěryhodnou doménu):
    * V okně příkazového řádku PowerShellu se zvýšenými oprávněními použijte příkaz `Set-Item.WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate` .
    * Ve Správci serveru:
      * Přidejte hostitele WAP DMZ do fondu počítačů. Ve Správci serveru vyberte **Spravovat**  >  **Přidat servery** a pak použijte kartu **DNS** .
      * Na kartě **Správce serveru všechny servery** klikněte pravým tlačítkem na server WAP a vyberte **Spravovat jako**. Zadejte místní (nedoménová) pověření pro počítač WAP.
      * Pokud chcete ověřit vzdálené připojení k PowerShellu, na kartě **Správce serveru všechny servery** klikněte pravým tlačítkem na server WAP a vyberte **Windows PowerShell**. Měla by se otevřít Vzdálená relace PowerShellu, která zajistí, že se můžou vytvořit vzdálené relace PowerShellu.

### <a name="tlsssl-certificate-requirements"></a>Požadavky na certifikát TLS/SSL
* Doporučujeme použít stejný certifikát TLS/SSL na všech uzlech AD FS farmy a všech proxy serverech webových aplikací.
* Certifikát musí být certifikát x509.
* Certifikát podepsaný svým držitelem můžete použít na federačních serverech v prostředí testovací laboratoře. V produkčním prostředí doporučujeme získat certifikát od veřejné certifikační autority.
  * Pokud používáte certifikát, který není veřejně důvěryhodný, ujistěte se, že certifikát nainstalovaný na každém proxy serveru webových aplikací je důvěryhodný na místním serveru i na všech federačních serverech.
* Identita certifikátu se musí shodovat s názvem federační služby (například sts.contoso.com).
  * Identita je rozšířením alternativní název subjektu (SAN) typu dNSName nebo, pokud nejsou k dispozici žádné záznamy v síti SAN, bude název subjektu zadán jako běžný název.
  * V certifikátu může být k dispozici více položek sítě SAN, které jsou součástí názvu služby FS (Federation Service).
  * Pokud plánujete použít Workplace Join, vyžaduje se další síť SAN s hodnotou **enterpriseregistration.** Následuje přípona hlavního názvu uživatele (UPN) vaší organizace, například enterpriseregistration.contoso.com.
* Certifikáty založené na klíčích nové generace (CNG) CryptoAPI a zprostředkovatelích úložiště klíčů (KSP) se nepodporují. V důsledku toho je nutné použít certifikát založený na zprostředkovateli kryptografických služeb (CSP), nikoli na KSP.
* Podporují se i certifikáty se zástupnými kartami.

### <a name="name-resolution-for-federation-servers"></a>Překlad názvů pro federační servery
* Nastavte záznamy DNS pro AD FS název (například sts.contoso.com) pro intranet (interní server DNS) a extranet (veřejné DNS prostřednictvím vašeho registrátora domény). Pro intranetový záznam DNS se ujistěte, že používáte záznamy a nikoli záznamy CNAME. Aby ověřování systému Windows fungovalo správně z počítače připojeného k doméně, je nutné použít záznamy.
* Pokud nasazujete více než jeden server AD FS nebo proxy server webové aplikace, ujistěte se, že jste nakonfigurovali Nástroj pro vyrovnávání zatížení a že záznamy DNS pro AD FS název (například sts.contoso.com) ukazují na nástroj pro vyrovnávání zatížení.
* Aby integrované ověřování systému Windows fungovalo pro prohlížeče aplikací pomocí aplikace Internet Explorer v intranetu, ujistěte se, že název AD FS (například sts.contoso.com) se přidá do zóny intranet v aplikaci Internet Explorer. Tento požadavek se dá řídit pomocí Zásady skupiny a nasadit na všechny počítače připojené k doméně.

## <a name="azure-ad-connect-supporting-components"></a>Azure AD Connect podpůrné komponenty
Azure AD Connect nainstaluje na server, na kterém je nainstalovaná Azure AD Connect, následující součásti. Tento seznam je určen pro základní expresní instalaci. Pokud se rozhodnete použít jiný SQL Server na stránce **instalace synchronizačních služeb** , SQL Express LocalDB není nainstalovaný místně.

* Azure AD Connect Health
* Nástroje příkazového řádku Microsoft SQL Server 2012
* Microsoft SQL Server 2012 Express LocalDB
* Nativní klient Microsoft SQL Server 2012
* Balíček pro redistribuci Microsoft Visual C++ 2013

## <a name="hardware-requirements-for-azure-ad-connect"></a>Požadavky na hardware pro Azure AD Connect
Následující tabulka uvádí minimální požadavky na počítač Azure AD Connect Sync.

| Počet objektů ve službě Active Directory | Procesor | Memory (Paměť) | Velikost pevného disku |
| --- | --- | --- | --- |
| Méně než 10 000 |1,6 GHz |4 GB |70 GB |
| 10000 – 50000 |1,6 GHz |4 GB |70 GB |
| 50000 – 100000 |1,6 GHz |16 GB |100 GB |
| Pro 100 000 nebo více objektů se vyžaduje plná verze SQL Server. | | | |
| 100000 – 300 000 |1,6 GHz |32 GB |300 GB |
| 300 000 – 600 000 |1,6 GHz |32 GB |450 GB |
| Více než 600 000 |1,6 GHz |32 GB |500 GB |

Minimální požadavky na počítače, na kterých běží AD FS nebo proxy servery webových aplikací, jsou:

* Procesor: Dual Core 1,6 GHz nebo vyšší
* Paměť: 2 GB nebo vyšší
* Virtuální počítač Azure: konfigurace a2 nebo vyšší

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).
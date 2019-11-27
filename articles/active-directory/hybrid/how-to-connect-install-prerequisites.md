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
ms.date: 05/08/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d54ef06fd63a1064962aea6099a2289d04ff658
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2019
ms.locfileid: "74462007"
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

* Azure AD Connect musí být nainstalovaný na Windows serveru 2008 R2 nebo novějším. Tento server musí být připojený k doméně a může se jednat o řadič domény nebo členský server.
* Pokud nainstalujete Azure AD Connect do systému Windows Server 2008 R2, nezapomeňte použít nejnovější opravy hotfix z web Windows Update. Instalaci není možné spustit s neopraveným serverem.
* Pokud plánujete použití funkce **Synchronizace hesel**, musí být server Azure AD Connect v systému Windows Server 2008 R2 SP1 nebo novějším.
* Pokud máte v úmyslu použít **skupinový účet spravované služby**, musí být server Azure AD Connect v systému Windows Server 2012 nebo novějším.
* Azure AD Connect Server musí mít nainstalovanou [.NET Framework 4.5.1](#component-prerequisites) nebo novějším a [Microsoft PowerShell 3,0](#component-prerequisites) nebo novější.
* Pokud ke správě konfigurace služby ADFS používáte Průvodce Azure AD Connect, Azure AD Connect Server nesmí mít povolený Zásady skupiny prostředí PowerShell přepis. Pokud ke správě konfigurace synchronizace používáte Azure AD Connect průvodce, můžete povolit PowerShellový přepis.
* Pokud je nasazen Active Directory Federation Services (AD FS), musí být servery, na kterých AD FS nebo proxy webových aplikací nainstalovány, systémy Windows Server 2012 R2 nebo novější. Pro vzdálenou instalaci musí být na těchto serverech povolená [Vzdálená správa systému Windows](#windows-remote-management) .
* Pokud nasazujete Active Directory Federation Services (AD FS), budete potřebovat [certifikáty SSL](#ssl-certificate-requirements).
* Pokud probíhá nasazení Active Directory Federation Services (AD FS), je nutné nakonfigurovat [Překlad adres IP](#name-resolution-for-federation-servers).
* Pokud mají globální správci povolené MFA, musí být adresa URL **https://secure.aadcdn.microsoftonline-p.com** v seznamu důvěryhodných serverů. Po zobrazení výzvy k zadání dotazu MFA se zobrazí výzva k přidání tohoto webu do seznamu důvěryhodných webů. Aplikaci Internet Explorer můžete použít k jejímu přidání do důvěryhodných webů.
* Společnost Microsoft doporučuje posílit Azure AD Connect Server, aby se snížila plocha pro útok na zabezpečení pro tuto kritickou součást vašeho IT prostředí.  Podle následujících doporučení se sníží rizika zabezpečení vaší organizace.

* Nasaďte Azure AD Connect na serveru připojeném k doméně a omezte přístup pro správu na správce domény nebo na jiné pevně kontrolované skupiny zabezpečení.

Další informace naleznete v tématu: 

* [Zabezpečení skupin správců](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-g--securing-administrators-groups-in-active-directory)

* [Zabezpečení předdefinovaných účtů správců](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-d--securing-built-in-administrator-accounts-in-active-directory)

* [Vylepšení a udržování zabezpečení snížením počtu napadených ploch](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access#2-reduce-attack-surfaces )

* [Snížení prostoru pro útoky na službu Active Directory](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface)

### <a name="sql-server-used-by-azure-ad-connect"></a>SQL Server používané Azure AD Connect
* Azure AD Connect vyžaduje k ukládání dat identity databázi SQL Serveru. Ve výchozím nastavení je nainstalovaná SQL Server 2012 Express LocalDB (světlá verze SQL Server Express). SQL Server Express má limit velikosti 10GB, který umožňuje spravovat přibližně 100 000 objektů. Pokud potřebujete spravovat větší objem objektů adresáře, musíte Průvodce instalací nasměrovat na jinou instalaci SQL Server. Typ SQL Server instalace může mít vliv na [výkon Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-performance-factors#sql-database-factors).
* Pokud používáte jinou instalaci SQL Server, platí tyto požadavky:
  * Azure AD Connect podporuje všechny verze Microsoft SQL Server z 2008 R2 (s nejnovější aktualizací Service Pack) do SQL Server 2019. Microsoft Azure SQL Database není **podporován** jako databáze.
  * Je nutné použít kolaci SQL nerozlišující malá a velká písmena. Tyto kolace se identifikují pomocí \_CI_ v názvu. Použití kolace citlivého **na velká** a malá písmena, které identifikuje \_CS_ v názvu.
  * Můžete mít jenom jeden synchronizační modul na jednu instanci SQL. Pro sdílení instance SQL s nástrojem FIM/MIM Sync, DirSync nebo Azure AD Sync **není podporována** .

### <a name="accounts"></a>Účty
* Účet globálního správce Azure AD pro tenanta Azure AD, se kterým se chcete integrovat. Tento účet musí být **školním nebo organizačním účtem** a nemůže být **účet Microsoft**.
* Pokud používáte expresní nastavení nebo upgrade z DirSync, musíte mít účet správce rozlehlé sítě pro vaši místní službu Active Directory.
* [Účty ve službě Active Directory](reference-connect-accounts-permissions.md) , pokud použijete instalační cestu pro vlastní nastavení nebo účet správce rozlehlé sítě pro místní službu Active Directory.

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

### <a name="other"></a>Ostatní
* Volitelné: testovací uživatelský účet pro ověření synchronizace.

## <a name="component-prerequisites"></a>Předpoklady součásti
### <a name="powershell-and-net-framework"></a>PowerShell a .NET Framework
Azure AD Connect závisí na prostředí Microsoft PowerShell a .NET Framework 4.5.1. Budete potřebovat tuto verzi nebo novější verzi nainstalovanou na vašem serveru. V závislosti na verzi Windows serveru udělejte toto:

* Windows Server 2012R2
  * Prostředí Microsoft PowerShell je nainstalováno ve výchozím nastavení. Nevyžaduje se žádná akce.
  * K dispozici jsou .NET Framework 4.5.1 a novější verze prostřednictvím web Windows Update. Ujistěte se, že máte v Ovládacích panelech nainstalované nejnovější aktualizace Windows serveru.
* Windows Server 2008 R2 a Windows Server 2012
  * Nejnovější verze prostředí Microsoft PowerShell je k dispozici v **rozhraní Windows Management Framework 4,0**, které je k dispozici na [webu Microsoft Download Center](https://www.microsoft.com/downloads).
  * .NET Framework 4.5.1 a novější verze jsou k dispozici na [webu Microsoft Download Center](https://www.microsoft.com/downloads).


### <a name="enable-tls-12-for-azure-ad-connect"></a>Povolit TLS 1,2 pro Azure AD Connect
Před verzí 1.1.614.0 Azure AD Connect ve výchozím nastavení používá TLS 1,0 pro šifrování komunikace mezi serverem synchronizačního stroje a službou Azure AD. To můžete změnit tak, že nakonfigurujete aplikace .NET tak, aby ve výchozím nastavení používaly TLS 1,2 standardně na serveru. Další informace o protokolu TLS 1,2 najdete v [zpravodaji zabezpečení společnosti Microsoft 2960358](https://technet.microsoft.com/security/advisory/2960358).

1. Protokol TLS 1,2 nelze povolit před systémem Windows Server 2008 R2 nebo starším. Ujistěte se, že máte nainstalovanou opravu hotfix rozhraní .NET 4.5.1 pro váš operační systém, viz [Microsoft Security advisor 2960358](https://technet.microsoft.com/security/advisory/2960358). Je možné, že je tato oprava hotfix nebo novější verze v serveru již nainstalována.
2. Pokud používáte systém Windows Server 2008 R2, zajistěte, aby byl povolen protokol TLS 1,2. V systému Windows Server 2012 Server a novějších verzích by měl být protokol TLS 1,2 již povolen.
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    ```
3. Pro všechny operační systémy nastavte tento klíč registru a restartujte server.
    ```
    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319
    "SchUseStrongCrypto"=dword:00000001
    ```
4. Pokud chcete také povolit TLS 1,2 mezi serverem synchronizace a vzdáleným SQL Server, ujistěte se, že máte nainstalované požadované verze pro [podporu TLS 1,2 pro Microsoft SQL Server](https://support.microsoft.com/kb/3135244).

## <a name="prerequisites-for-federation-installation-and-configuration"></a>Předpoklady pro instalaci a konfiguraci federace
### <a name="windows-remote-management"></a>Vzdálená správa systému Windows
Při použití Azure AD Connect k nasazení Active Directory Federation Services (AD FS) nebo proxy webových aplikací, Projděte si tyto požadavky:

* Pokud je cílový server připojený k doméně, zajistěte, aby byla povolená Vzdálená správa systému Windows.
  * V příkazovém okně se zvýšenými oprávněními PSH použijte příkaz `Enable-PSRemoting –force`
* Pokud je cílový Server počítač WAP, který není připojený k doméně, existuje několik dalších požadavků.
  * Na cílovém počítači (WAP Machine):
    * Zajistěte, aby služba WinRM (Vzdálená správa systému Windows/WS-Management) běžela prostřednictvím modulu snap-in služby.
    * V příkazovém okně se zvýšenými oprávněními PSH použijte příkaz `Enable-PSRemoting –force`
  * V počítači, na kterém je spuštěný Průvodce (Pokud je cílový počítač připojený k doméně nebo nedůvěryhodnou doménu):
    * V okně příkazového řádku se zvýšenými oprávněními PSH použijte příkaz `Set-Item WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate`
    * V Správce serveru:
      * Přidat hostitele WAP DMZ do fondu počítačů (Správce serveru-> Spravovat-> přidat servery... použít kartu DNS)
      * Správce serveru kartě všechny servery: klikněte pravým tlačítkem na možnost Server WAP a vyberte možnost spravovat jako..., zadejte místní (nikoli doménu) přihlašovací údaje pro počítač WAP.
      * Pokud chcete ověřit připojení vzdáleného PSH, na kartě Správce serveru všechny servery: klikněte pravým tlačítkem na možnost Server WAP a vyberte Windows PowerShell. Vzdálená relace PSH by se měla otevřít, aby se zajistilo, že se můžou vytvořit vzdálené relace PowerShellu.

### <a name="ssl-certificate-requirements"></a>Požadavky na certifikát SSL
* Důrazně se doporučuje použít stejný certifikát SSL ve všech uzlech AD FS farmy a všech proxy serverech webových aplikací.
* Certifikát musí být certifikát x509.
* Certifikát podepsaný svým držitelem můžete použít na federačních serverech v prostředí testovací laboratoře. V produkčním prostředí ale doporučujeme získat certifikát od veřejné certifikační autority.
  * Pokud používáte certifikát, který není veřejně důvěryhodný, ujistěte se, že certifikát nainstalovaný na každém proxy serveru webových aplikací je důvěryhodný na místním serveru i na všech federačních serverech.
* Identita certifikátu se musí shodovat s názvem federační služby (například sts.contoso.com).
  * Identita je rozšířením alternativní název subjektu (SAN) typu dNSName nebo, pokud nejsou k dispozici žádné záznamy v síti SAN, název subjektu zadaný jako běžný název.  
  * V certifikátu může být k dispozici více záznamů sítě SAN, pokud je jedna z nich shodná s názvem federační služby.
  * Pokud plánujete použít Workplace Join, vyžaduje se další síť SAN s hodnotou **enterpriseregistration.** Následuje přípona hlavního názvu uživatele (UPN) vaší organizace, například **enterpriseregistration.contoso.com**.
* Certifikáty založené na klíčích služby CryptoAPI Next Generation (CNG) a zprostředkovatelích úložiště klíčů nejsou podporovány. To znamená, že musíte použít certifikát založený na zprostředkovateli kryptografických služeb (CSP), nikoli KSP (Zprostředkovatel úložiště klíčů).
* Podporují se i certifikáty se zástupnými kartami.

### <a name="name-resolution-for-federation-servers"></a>Překlad názvů pro federační servery
* Nastavte záznamy DNS pro název služby AD FS FS (například sts.contoso.com) pro intranet (interní server DNS) a extranet (veřejný DNS prostřednictvím vašeho registrátora domény). Pro intranetový záznam DNS se ujistěte, že používáte záznamy a nikoli záznamy CNAME. K tomu je potřeba, aby ověřování Windows fungovalo správně z počítače připojeného k doméně.
* Pokud nasazujete více než jeden server AD FS nebo proxy server webové aplikace, ujistěte se, že jste nakonfigurovali Nástroj pro vyrovnávání zatížení a že záznamy DNS pro název služby AD FS FS (například sts.contoso.com) odkazuje na nástroj pro vyrovnávání zatížení.
* Aby integrované ověřování systému Windows fungovalo pro prohlížeče aplikací pomocí aplikace Internet Explorer v intranetu, ujistěte se, že název služby AD FS Federation Service (například sts.contoso.com) se přidá do zóny intranet v IE. Dá se řídit pomocí zásad skupiny a nasazených na všechny počítače připojené k doméně.

## <a name="azure-ad-connect-supporting-components"></a>Azure AD Connect podpůrné komponenty
Níže je seznam komponent, které Azure AD Connect nainstalovat na server, na kterém je nainstalovaná Azure AD Connect. Tento seznam je určen pro základní expresní instalaci. Pokud se rozhodnete použít jiný SQL Server na stránce instalovat synchronizační služby, pak není SQL Express LocalDB nainstalován místně.

* Azure AD Connect Health
* Nástroje příkazového řádku Microsoft SQL Server 2012
* Microsoft SQL Server 2012 Express LocalDB
* Microsoft SQL Server 2012 Native Client
* Balíček pro C++ redistribuci Microsoft Visual 2013

## <a name="hardware-requirements-for-azure-ad-connect"></a>Požadavky na hardware pro Azure AD Connect
Následující tabulka uvádí minimální požadavky na počítač Azure AD Connect Sync.

| Počet objektů ve službě Active Directory | Procesor | Memory (Paměť) | Velikost pevného disku |
| --- | --- | --- | --- |
| Méně než 10 000 |1,6 GHz |4 GB |70 GB |
| 10,000–50,000 |1,6 GHz |4 GB |70 GB |
| 50,000–100,000 |1,6 GHz |16 GB |100 GB |
| Pro 100 000 nebo více objektů se vyžaduje plná verze SQL Server. | | | |
| 100,000–300,000 |1,6 GHz |32 GB |300 GB |
| 300,000–600,000 |1,6 GHz |32 GB |450 GB |
| Více než 600 000 |1,6 GHz |32 GB |500 GB |

Minimální požadavky na počítače se systémem AD FS nebo proxy servery webových aplikací jsou následující:

* Procesor: Dual Core 1,6 GHz nebo vyšší
* PAMĚŤ: 2 GB nebo vyšší
* Virtuální počítač Azure: konfigurace a2 nebo vyšší

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).

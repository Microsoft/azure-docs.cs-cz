---
title: 'Azure AD Connect: Požadavky a hardware | Dokumentace Microsoftu'
description: Toto téma popisuje požadavky a požadavky na hardware pro Azure AD Connect
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
ms.openlocfilehash: 1d17823c4ef4917f9f312b8f2f327e2b0395cfa3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67109437"
---
# <a name="prerequisites-for-azure-ad-connect"></a>Požadavky pro Azure AD Connect
Toto téma popisuje požadavky a požadavky na hardware pro Azure AD Connect.

## <a name="before-you-install-azure-ad-connect"></a>Před instalací Azure AD Connect
Před instalací Azure AD Connect, existuje několik věcí, které potřebujete.

### <a name="azure-ad"></a>Azure AD
* Tenanta Azure AD. Můžete získat pomocí [bezplatnou zkušební verzi Azure](https://azure.microsoft.com/pricing/free-trial/). Ke správě služby Azure AD Connect můžete použít jednu z následujících portály:
  * [Webu Azure portal](https://portal.azure.com).
  * [Portál Office](https://portal.office.com).  
* [Přidání a ověření domény](../active-directory-domains-add-azure-portal.md) plánujete používat ve službě Azure AD. Například pokud máte v úmyslu používat contoso.com pro vaše uživatele a ujistěte se, že tato doména se ověřila a pouze nepoužíváte výchozí doménu contoso.onmicrosoft.com.
* Klient služby Azure AD umožňuje ve výchozích 50 tisíc objektů. Při ověřování domény limitu je zvýšena na 300 kB objekty. Pokud budete potřebovat ještě více objektů ve službě Azure AD, musíte otevřít případ podpory využití ještě dále zvýšit. Pokud potřebujete více než 500 tisíc objektů, potřebujete licenci, jako je Office 365, Azure AD Basic, Azure AD Premium nebo Enterprise Mobility + Security.

### <a name="prepare-your-on-premises-data"></a>Příprava místních dat.
* Použití [IdFix](https://support.office.com/article/Install-and-run-the-Office-365-IdFix-tool-f4bd2439-3e41-4169-99f6-3fabdfa326ac) k identifikaci chyby jako např. duplicitní položky a formátování problémy ve vašem adresáři předtím, než je synchronizujete s Azure AD a Office 365.
* Kontrola [volitelné synchronizačních funkcí ve službě Azure AD můžete povolit](how-to-connect-syncservice-features.md) a vyhodnocení funkcí, které byste měli povolit.

### <a name="on-premises-active-directory"></a>Místní služby Active Directory
* AD schématu verze a doménová struktura úroveň funkčnosti musí být Windows Server 2003 nebo novější. Řadiče domény můžete spustit všechny verze, tak dlouho, dokud jsou splněny požadavky úrovni schématu a doménové struktury.
* Pokud plánujete použít funkci **zpětný zápis hesla**, řadiče domény musí být v systému Windows Server 2008 R2 nebo novější.
* Musí být zapisovatelný řadič domény používá Azure AD. Je **nepodporuje** řadiče jen pro čtení (řadič domény jen pro čtení) a Azure AD Connect není postupujte podle jakékoli přesměrování zápisu.
* Je **nepodporuje** používat s místními doménovými strukturami nebo doménami pomocí "tečkované" (název obsahuje tečku ".") Názvy NetBios.
* Doporučuje se [povolit odpadkový koš služby Active Directory](how-to-connect-sync-recycle-bin.md).

### <a name="azure-ad-connect-server"></a>Server Azure AD Connect
>[!IMPORTANT]
>Server Azure AD Connect obsahuje data kritické identity a mají být považována za součást vrstvy 0, jak je uvedeno v [model vrstvy pro správu služby Active Directory](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material#ADATM_BM)

* Azure AD Connect nejde nainstalovat na Small Business Server nebo Windows serveru Essentials před 2019 (podporuje se Windows Server Essentials 2019). Server musí používat Windows Server standard nebo vyšší.
* Instalace služby Azure AD Connect na řadiči domény se nedoporučuje kvůli postupy zabezpečení a víc omezující nastavení, které mohou zabránit v instalaci správně Azure AD Connect
* Server Azure AD Connect musí mít úplným grafickým uživatelským rozhraním nainstalované. Je **nepodporuje** instalace jádra serveru.
>[!IMPORTANT]
>Instalace služby Azure AD Connect small business server, server essentials nebo server core není podporována.

* Azure AD Connect musí být nainstalována v systému Windows Server 2008 R2 nebo novější. Tento server musí být připojené k doméně a může být řadič domény nebo členském serveru.
* Pokud Azure AD Connect instalujete na Windows Server 2008 R2, pak nezapomeňte použít nejnovější opravy hotfix z webu Windows Update. Instalace není možné spustit pomocí bez opravy zabezpečení serveru.
* Pokud plánujete použít funkci **synchronizace hesel**, server Azure AD Connect musí být v systému Windows Server 2008 R2 SP1 nebo novějším.
* Pokud chcete používat **skupinový účet spravované služby**, server Azure AD Connect musí být ve Windows serveru 2012 nebo novější.
* Server Azure AD Connect musí mít [rozhraní .NET Framework 4.5.1](#component-prerequisites) nebo novější a [Microsoft PowerShell 3.0](#component-prerequisites) nebo novější.
* Server Azure AD Connect nesmí mít povolené zásady skupiny určené k transkripci prostředí PowerShell.
* Pokud je nasazení služby AD FS, servery, kde jsou nainstalovány služby AD FS nebo Proxy webových aplikací musí být Windows Server 2012 R2 nebo novější. [Vzdálená správa Windows](#windows-remote-management) musí být povolené na těchto serverech pro vzdálenou instalaci.
* Pokud je nasazení služby AD FS, musíte [certifikáty SSL](#ssl-certificate-requirements).
* Pokud je nasazení služby AD FS, pak budete muset nakonfigurovat [překlad názvů](#name-resolution-for-federation-servers).
* Pokud globální Správci mají zapnuté MFA, pak adresu URL **https://secure.aadcdn.microsoftonline-p.com** musí být v seznamu důvěryhodných serverů. Zobrazí se výzva přidat tento web do seznamu důvěryhodných webů, pokud se výzva k zadání ověřovacím testem MFA a nebyla přidána před. Můžete ho přidat do důvěryhodných serverů Internet Exploreru.
* Společnost Microsoft doporučuje posílit zabezpečení serveru služby Azure AD Connect se útoky zabezpečení pro tato zásadní součástí sady řešení IT prostředí.  Následující doporučení níže se sníží bezpečnostní rizika pro vaši organizaci.

* Nasazení služby Azure AD Connect na server připojený k doméně a omezit přístup pro správu pro správce domény nebo jiné skupiny přísně řízenými zabezpečení.

Další informace naleznete v tématu: 

* [Skupiny zabezpečení administrators](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-g--securing-administrators-groups-in-active-directory)

* [Zabezpečení účtů předdefinovaného účtu administrator](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-d--securing-built-in-administrator-accounts-in-active-directory)

* [Zvýšení zabezpečení a sustainment snížením rovin útoku](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access#2-reduce-attack-surfaces )

* [Omezení prostoru pro útok služby Active Directory](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface)

### <a name="sql-server-used-by-azure-ad-connect"></a>SQL Server používá Azure AD Connect
* Azure AD Connect vyžaduje k ukládání dat identity databázi SQL Serveru. Ve výchozím nastavení je nainstalovaný SQL Server 2012 Express LocalDB (světle verze systému SQL Server Express). SQL Server Express má omezení velikosti 10GB, která umožňuje spravovat přibližně 100 000 objektů. Pokud je potřeba spravovat větší objem adresářových objektů, je potřeba nasměrovat Průvodce instalací na jinou instalaci systému SQL Server. Typ instalace systému SQL Server může mít vliv [výkonu služby Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-performance-factors#sql-database-factors).
* Pokud používáte jinou instalaci systému SQL Server, pak platí tyto požadavky:
  * Azure AD Connect podporuje všechny verze Microsoft SQL serveru z 2008 R2 (s nejnovější aktualizací Service Pack) pro SQL Server 2019. Microsoft Azure SQL Database je **nepodporuje** jako databáze.
  * Je nutné použít malá a velká písmena kolace SQL. Tyto kolace jsou označeny ikonou \_CI_ v názvu. Je **nepodporuje** pro používání malá a velká písmena kolace, identifikovaný \_cs_ – v názvu.
  * Můžete mít jenom jeden synchronizační modul za instanci SQL. Je **nepodporuje** sdílení SQL instance s FIM nebo MIM Sync nebo DirSync, Azure AD Sync.

### <a name="accounts"></a>Účty
* Účet globálního správce služby Azure AD pro tenanta Azure AD, kterou chcete integrovat s. Tento účet musí být **školní nebo organizaci účet** a nemůže být **účtu Microsoft**.
* Je-li použít Expresní nastavení nebo upgradu z nástroje DirSync, musíte mít účet správce podnikové sítě pro vaši místní službu Active Directory.
* [Účty ve službě Active Directory](reference-connect-accounts-permissions.md) Pokud použijete vlastní nastavení Instalační cesta nebo účet správce podnikové sítě pro vaši místní službu Active Directory.

### <a name="connectivity"></a>Připojení
* Server Azure AD Connect musí překlad názvů DNS pro intranetu i Internetu. DNS server musí být schopné překládat názvy do koncových bodů služby Azure AD a místní Active Directory.
* Pokud máte brány firewall na vašem intranetu a je potřeba otevřít porty mezi servery služby Azure AD Connect a řadiče domény a pak naleznete v tématu [Azure AD Connect porty](reference-connect-ports.md) Další informace.
* Pokud proxy server nebo brána firewall omezit adresy URL, které jsou přístupné, pak uvedené adresy URL v [Office 365 – adresy URL a rozsahy IP adres](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) musí být otevřeny.
  * Pokud používáte Microsoft cloudu v Německu nebo v cloudu Microsoft Azure Government, přejděte na téma [služby synchronizace Azure AD Connect instance aspekty](reference-connect-instances.md) pro adresy URL.
* Azure AD Connect (verze 1.1.614.0 a po provedení) ve výchozím nastavení používá TLS 1.2 pro šifrování komunikace mezi synchronizačního modulu a Azure AD. Pokud není k dispozici na příslušný operační systém TLS 1.2, Azure AD Connect postupně vrací zpět ke starším protokolům (TLS 1.1 a TLS 1.0).
* Starší než verze 1.1.614.0 Azure AD Connect ve výchozím nastavení používá protokol TLS 1.0 pro šifrování komunikace mezi synchronizačního modulu a Azure AD. Chcete-li změnit na TLS 1.2, postupujte podle kroků v [povolení protokolu TLS 1.2 pro Azure AD Connect](#enable-tls-12-for-azure-ad-connect).
* Pokud používáte odchozího proxy serveru pro připojení k Internetu, následující nastavení **C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config** soubor musí být přidán pro Průvodce instalací a Azure AD Synchronizace bude moct připojit k Internetu a Azure AD Connect. Tento text je třeba zadat v dolní části souboru. V tomto kódu &lt;PROXYADDRESS&gt; představuje skutečné proxy IP adresu nebo název hostitele.

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

* Pokud váš proxy server vyžaduje ověření, pak bude [účet služby](reference-connect-accounts-permissions.md#adsync-service-account) se musí nacházet v doméně a je třeba použít cestu instalace vlastního nastavení k určení [vlastní účet služby](how-to-connect-install-custom.md#install-required-components). Budete také potřebovat jiné změny souboru Machine.config. Díky této změně v souboru machine.config modul průvodce a synchronizace instalace reagovat na žádosti o ověření z proxy serveru. Na všech stránkách Průvodce instalací, s výjimkou **konfigurovat** stránku, podepsané uživatele se používají přihlašovací údaje. Na **konfigurovat** stránky na konci Průvodce instalací, kontext je přepnuta do [účet služby](reference-connect-accounts-permissions.md#adsync-service-account) , který je vytvořil. V části machine.config by měl vypadat takto.

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

* Pokud Azure AD Connect na webový požadavek odešle do služby Azure AD jako součást synchronizace adresářů, Azure AD může trvat až 5 minut, než reagovat. Je běžné mít konfigurace časový limit nečinnosti připojení proxy servery. Ujistěte se prosím, že konfiguraci je nastaven alespoň 6 minut nebo i déle.

Další informace najdete v tématu MSDN [výchozí proxy server Element](https://msdn.microsoft.com/library/kd3cf2ex.aspx).  
Další informace najdete v případě, že máte problémy s připojením, [řešení problémů s připojením](tshoot-connect-connectivity.md).

### <a name="other"></a>Ostatní
* Volitelné: Testovací uživatelský účet k ověření synchronizace.

## <a name="component-prerequisites"></a>Požadavky na komponenty
### <a name="powershell-and-net-framework"></a>Prostředí PowerShell a rozhraní .NET Framework
Azure AD Connect, závisí na Microsoft PowerShell a rozhraní .NET Framework 4.5.1. Je nutné tuto verzi nebo novější verze na serveru nainstalovaný. V závislosti na vaší verzi Windows serveru postupujte takto:

* Windows Server 2012R2
  * Ve výchozím nastavení je nainstalovaný Microsoft PowerShell. Nevyžaduje se žádná akce.
  * Rozhraní .NET framework 4.5.1 a novějších verzí se nabízejí prostřednictvím služby Windows Update. Ujistěte se, že máte nainstalované nejnovější aktualizace na Windows Server v Ovládacích panelech.
* Windows Server 2008 R2 a Windows Server 2012
  * Je k dispozici v nejnovější verzi Powershellu Microsoft **Windows Management Framework 4.0**, která je dostupná na [Microsoft Download Center](https://www.microsoft.com/downloads).
  * Jsou k dispozici v rozhraní .NET framework 4.5.1 a novějších verzí [Microsoft Download Center](https://www.microsoft.com/downloads).


### <a name="enable-tls-12-for-azure-ad-connect"></a>Povolení protokolu TLS 1.2 pro služby Azure AD Connect
Starší než verze 1.1.614.0 Azure AD Connect ve výchozím nastavení používá protokol TLS 1.0 pro šifrování komunikace mezi serverem modul synchronizace a Azure AD. Toto můžete změnit tím, že nakonfigurujete aplikace .NET pro použití protokolu TLS 1.2 ve výchozím nastavení na serveru. Další informace o protokolu TLS 1.2 nacházely v [Microsoft Security Advisory 2960358](https://technet.microsoft.com/security/advisory/2960358).

1. Před Windows serverem 2008 R2 nebo novější není možné TLS 1.2. Ujistěte se, že máte .NET 4.5.1 naleznete v tématu oprava hotfix nainstalovaná pro váš operační systém, [Microsoft Security Advisory 2960358](https://technet.microsoft.com/security/advisory/2960358). Můžete mít tato oprava hotfix nebo novější verzi na serveru již nainstalovány.
2. Pokud používáte Windows Server 2008 R2, ujistěte se, že je povolený protokol TLS 1.2. Na serveru Windows Server 2012 a novější verze TLS 1.2 již povolena.
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
4. Pokud chcete také protokol TLS 1.2 mezi serverem modul synchronizace a vzdálený SQL Server a ujistěte se, že máte nainstalované pro požadované verze [podpora protokolu TLS 1.2 pro Microsoft SQL Server](https://support.microsoft.com/kb/3135244).

## <a name="prerequisites-for-federation-installation-and-configuration"></a>Předpoklady pro federační instalace a konfigurace
### <a name="windows-remote-management"></a>Vzdálená správa systému Windows
Při nasazení služby AD FS nebo Proxy webových aplikací pomocí služby Azure AD Connect, zkontrolujte tyto požadavky:

* Pokud je cílový server k doméně, zajistěte, že je povoleno vzdálené spravované Windows
  * V okně se zvýšenými oprávněními příkaz PSH použijte příkaz `Enable-PSRemoting –force`
* Pokud je cílový server mimo doménu připojená k počítači WAP, pak máte několik dalších požadavků
  * Na cílovém počítači (počítač WAP):
    * Zkontrolujte, služba winrm (Vzdálená správa Windows / WS-Management) je služba spuštěná přes modul snap-in služby
    * V okně se zvýšenými oprávněními příkaz PSH použijte příkaz `Enable-PSRemoting –force`
  * Na počítači, na kterém je spuštěný Průvodce (Pokud je cílový počítač domény připojené k doméně nebo nedůvěryhodné doméně):
    * V okně se zvýšenými oprávněními příkaz PSH použijte příkaz `Set-Item WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate`
    * Ve Správci serveru:
      * Přidat hostitele hraniční sítě WAP do fondu počítačů (Správce serverů -> Správa -> Přidat servery... karta DNS)
      * Karta servery všechny správce serveru: klikněte pravým tlačítkem na WAP server a zvolte spravovat jako, zadejte přihlašovací údaje pro místní (nikoli domény) pro počítač WAP
      * K ověření vzdálené připojení PSH, na kartě servery všechny správce serveru: klikněte pravým tlačítkem na WAP server a zvolte prostředí Windows PowerShell. Mělo by se vzdálenou relaci PSH otevřít Ujistěte se, že je možné navázat vzdálené relace prostředí PowerShell.

### <a name="ssl-certificate-requirements"></a>Požadavky na certifikát SSL
* Důrazně doporučujeme použít stejný certifikát protokolu SSL ve všech uzlech farmu služby AD FS a všechny proxy servery webových aplikací.
* Certifikát musí být x X509 certifikátu.
* Na federačních serverech v testovacím prostředí můžete použít certifikát podepsaný svým držitelem. Pro produkční prostředí, ale doporučujeme získat certifikát z veřejnou certifikační Autoritou.
  * Pokud používáte certifikát, který není veřejně důvěryhodný, ujistěte se, že certifikát nainstalovaný na každém serveru Proxy webových aplikací je důvěryhodný na místním serveru a na všech federačních serverech
* Identita certifikátu musí odpovídat názvu federační služby (například sts.contoso.com).
  * Identita je buď subjektu alternativní název (SAN) rozšířením typu dNSName nebo, pokud neexistují žádné položky po síti SAN, zadaný v názvu subjektu jako běžný název.  
  * Více položek SAN může být k dispozici v certifikátu, pokud jeden z nich odpovídá názvu federační služby.
  * Pokud máte v úmyslu používat připojení pracovního místa, další sítě SAN je vyžadována s hodnotou **enterpriseregistration.** Následuje hlavní název uživatele (UPN) příponu vaší organizace, například **enterpriseregistration.contoso.com**.
* Certifikáty na základě klíčů další generace (CNG) rozhraní CryptoAPI a zprostředkovatelé úložiště klíčů nejsou podporovány. To znamená, že je nutné použít certifikát založený na CSP (zprostředkovatel kryptografických služeb) a ne KSP (Zprostředkovatel úložiště klíčů).
* Zástupný znak – certifikáty jsou podporovány.

### <a name="name-resolution-for-federation-servers"></a>Překlad názvů pro federační servery
* Nastavte záznamy DNS pro název služby federation (například sts.contoso.com) služby AD FS pro podnikové síti (interní server DNS) a z extranetu (veřejném DNS prostřednictvím vašeho registrátora domény). Pro záznam DNS intranetu, ujistěte se, že používáte A záznamy a nikoli záznamy CNAME. To je potřeba pro ověřování systému windows fungovalo správně z počítače připojeného k doméně.
* Pokud provádíte nasazení více než jeden server služby AD FS nebo Proxy server webové aplikace, zkontrolujte, že jste nakonfigurovali nástroj pro vyrovnávání zatížení a zda záznamy DNS pro název federační služby AD FS (například sts.contoso.com) přejděte na nástroje pro vyrovnávání zatížení.
* Pro integrované ověřování systému windows pro aplikace prohlížeče v aplikaci Internet Explorer v síti intranet Ujistěte se, že název federační služby AD FS (například sts.contoso.com) se přidá do zóny intranetu v Internet Exploreru. To můžete řídit pomocí zásad skupiny a nasadit do všech počítačů připojených k doméně.

## <a name="azure-ad-connect-supporting-components"></a>Azure AD Connect podporuje komponenty
Následuje seznam komponent, které Azure AD Connect nainstaluje na server, kde je nainstalovaný Azure AD Connect. Tento seznam je pro základní Expresní instalace. Pokud se rozhodnete použít jiný SQL Server na stránce instalace synchronizační služby, není místně nainstalovaný SQL Express LocalDB.

* Azure AD Connect Health
* Nástroje příkazového řádku Microsoft SQL Server 2012
* Microsoft SQL Server 2012 Express LocalDB
* Microsoft SQL Server 2012 Native Client
* Balíček Microsoft Visual C++ 2013 automatické distribuce signatur

## <a name="hardware-requirements-for-azure-ad-connect"></a>Požadavky na hardware pro Azure AD Connect
Následující tabulka uvádí minimální požadavky na počítač synchronizace Azure AD Connect.

| Počet objektů ve službě Active Directory | Procesor | Memory (Paměť) | Velikost pevného disku |
| --- | --- | --- | --- |
| Méně než 10 000 operací |1.6 GHz |4 GB |70 GB |
| 10,000–50,000 |1.6 GHz |4 GB |70 GB |
| 50,000–100,000 |1.6 GHz |16 GB |100 GB |
| Pro 100 000 nebo více objektů se vyžaduje plná verze systému SQL Server | | | |
| 100,000–300,000 |1.6 GHz |32 GB |300 GB |
| 300,000–600,000 |1.6 GHz |32 GB |450 GB |
| Víc než 600 000 |1.6 GHz |32 GB |500 GB |

Minimální požadavky na počítače se systémem služby AD FS nebo serverů webových aplikací je následující:

* CPU: Duální základní 1,6 GHz nebo vyšší
* PAMĚŤ: 2 GB nebo vyšší
* Virtuální počítač Azure: Konfigurace a2 nebo vyšší

## <a name="next-steps"></a>Další postup
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).

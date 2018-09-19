---
title: 'Azure AD Connect: Požadované součásti a hardware | Dokumentace Microsoftu'
description: Toto téma popisuje požadavky a požadavky na hardware pro Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 91b88fda-bca6-49a8-898f-8d906a661f07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/09/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: addb99478025757257bce465a02287ebedd40bb1
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46314976"
---
# <a name="prerequisites-for-azure-ad-connect"></a>Požadavky pro Azure AD Connect
Toto téma popisuje požadavky a požadavky na hardware pro Azure AD Connect.

## <a name="before-you-install-azure-ad-connect"></a>Před instalací Azure AD Connect
Před instalací Azure AD Connect, existuje několik věcí, které potřebujete.

### <a name="azure-ad"></a>Azure AD
* Předplatné Azure nebo [zkušebního předplatného Azure](https://azure.microsoft.com/pricing/free-trial/). Toto předplatné je jenom nutné pro přístup k portálu Azure a ne pomocí služby Azure AD Connect. Pokud používáte PowerShell nebo Office 365, není nutné předplatného Azure k použití služby Azure AD Connect. Pokud máte licenci Office 365, můžete také použít portál Office 365. S placenou licenci Office 365 můžete také získat na webu Azure portal z portálu Office 365.
  * Můžete také použít [webu Azure portal](https://portal.azure.com). Tento portál nevyžaduje licenci Azure AD.
* [Přidání a ověření domény](../active-directory-domains-add-azure-portal.md) plánujete používat ve službě Azure AD. Například pokud máte v úmyslu používat contoso.com pro vaše uživatele a ujistěte se, že tato doména se ověřila a pouze nepoužíváte výchozí doménu contoso.onmicrosoft.com.
* Klient služby Azure AD umožňuje ve výchozích 50 tisíc objektů. Při ověřování domény limitu je zvýšena na 300 kB objekty. Pokud budete potřebovat ještě více objektů ve službě Azure AD, musíte otevřít případ podpory využití ještě dále zvýšit. Pokud potřebujete více než 500 tisíc objektů, potřebujete licenci, jako je Office 365, Azure AD Basic, Azure AD Premium nebo Enterprise Mobility + Security.
* ADSyncPrep je modul skriptu Powershellu, který poskytuje funkce, které slouží k přípravě svého prostředí služby Active Directory pro Azure AD Connect.  Vyžaduje ADSyncPrep [Online v1.1 Azure AD Microsoft PowerShell Module](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0).  Verze 2 nebude fungovat. Můžete nainstalovat pomocí modulu `Install-Module` rutiny.  Další informace najdete v článku uvedený odkaz.

### <a name="prepare-your-on-premises-data"></a>Příprava místních dat.
* Použití [IdFix](https://support.office.com/article/Install-and-run-the-Office-365-IdFix-tool-f4bd2439-3e41-4169-99f6-3fabdfa326ac) k identifikaci chyby jako např. duplicitní položky a formátování problémy ve vašem adresáři předtím, než je synchronizujete s Azure AD a Office 365.
* Kontrola [volitelné synchronizačních funkcí ve službě Azure AD můžete povolit](how-to-connect-syncservice-features.md) a vyhodnocení funkcí, které byste měli povolit.

### <a name="on-premises-active-directory"></a>Místní služby Active Directory
* AD schématu verze a doménová struktura úroveň funkčnosti musí být Windows Server 2003 nebo novější. Řadiče domény můžete spustit všechny verze, tak dlouho, dokud jsou splněny požadavky úrovni schématu a doménové struktury.
* Pokud plánujete použít funkci **zpětný zápis hesla**, řadiče domény musí být v systému Windows Server 2008 (s nejnovější aktualizací SP) nebo novější. Pokud vaše řadiče domény jsou 2008 (starší verzi než R2), pak musíte také použít [opravu hotfix KB2386717](http://support.microsoft.com/kb/2386717).
* Musí být zapisovatelný řadič domény používá Azure AD. Je **nepodporuje** řadiče jen pro čtení (řadič domény jen pro čtení) a Azure AD Connect není postupujte podle jakékoli přesměrování zápisu.
* Je **nepodporuje** používat s místními doménovými strukturami nebo doménami pomocí "tečkované" (název obsahuje tečku ".") Názvy NetBios.
* Doporučuje se [povolit odpadkový koš služby Active Directory](how-to-connect-sync-recycle-bin.md).

### <a name="azure-ad-connect-server"></a>Server Azure AD Connect
* Azure AD Connect nejde nainstalovat na Small Business Server nebo Windows serveru Essentials. Server musí používat Windows Server standard nebo vyšší.
* Server Azure AD Connect musí mít úplným grafickým uživatelským rozhraním nainstalované. Je **nepodporuje** instalace jádra serveru.
* Azure AD Connect musí být nainstalována v systému Windows Server 2008 nebo novější. Tento server může být řadič domény nebo členském serveru, při použití expresního nastavení. Pokud použijete vlastní nastavení, server může být také samostatná a nemusí být připojený k doméně.
* Pokud Azure AD Connect instalujete na Windows Server 2008 nebo Windows Server 2008 R2, pak nezapomeňte použít nejnovější opravy hotfix z webu Windows Update. Instalace není možné spustit pomocí bez opravy zabezpečení serveru.
* Pokud plánujete použít funkci **synchronizace hesel**, server Azure AD Connect musí být v systému Windows Server 2008 R2 SP1 nebo novějším.
* Pokud chcete používat **skupinový účet spravované služby**, server Azure AD Connect musí být ve Windows serveru 2012 nebo novější.
* Server Azure AD Connect musí mít [rozhraní .NET Framework 4.5.1](#component-prerequisites) nebo novější a [Microsoft PowerShell 3.0](#component-prerequisites) nebo novější.
* Server Azure AD Connect nesmí mít povolené zásady skupiny určené k transkripci prostředí PowerShell.
* Pokud je nasazení služby AD FS, servery, kde jsou nainstalovány služby AD FS nebo Proxy webových aplikací musí být Windows Server 2012 R2 nebo novější. [Vzdálená správa Windows](#windows-remote-management) musí být povolené na těchto serverech pro vzdálenou instalaci.
* Pokud je nasazení služby AD FS, musíte [certifikáty SSL](#ssl-certificate-requirements).
* Pokud je nasazení služby AD FS, pak budete muset nakonfigurovat [překlad názvů](#name-resolution-for-federation-servers).
* Pokud globální Správci mají zapnuté MFA, pak adresu URL **https://secure.aadcdn.microsoftonline-p.com** musí být v seznamu důvěryhodných serverů. Zobrazí se výzva přidat tento web do seznamu důvěryhodných webů, pokud se výzva k zadání ověřovacím testem MFA a nebyla přidána před. Můžete ho přidat do důvěryhodných serverů Internet Exploreru.

### <a name="sql-server-used-by-azure-ad-connect"></a>SQL Server používá Azure AD Connect
* Azure AD Connect vyžaduje k ukládání dat identity databázi SQL Serveru. Ve výchozím nastavení je nainstalovaný SQL Server 2012 Express LocalDB (světle verze systému SQL Server Express). SQL Server Express má omezení velikosti 10GB, která umožňuje spravovat přibližně 100 000 objektů. Pokud je potřeba spravovat větší objem adresářových objektů, je potřeba nasměrovat Průvodce instalací na jinou instalaci systému SQL Server.
* Pokud budete používat samostatný SQL Server, pak platí tyto požadavky:
  * Azure AD Connect podporuje všechny verze systému Microsoft SQL Server ze systému SQL Server 2008 (s nejnovější aktualizací Service Pack) do systému SQL Server 2016 SP1. Microsoft Azure SQL Database je **nepodporuje** jako databáze.
  * Je nutné použít malá a velká písmena kolace SQL. Tyto kolace jsou označeny ikonou \_CI_ v názvu. Je **nepodporuje** pro používání malá a velká písmena kolace, identifikovaný \_cs_ – v názvu.
  * Můžete mít jenom jeden synchronizační modul za instanci SQL. Je **nepodporuje** sdílení SQL instance s FIM nebo MIM Sync nebo DirSync, Azure AD Sync.

### <a name="accounts"></a>Účty
* Účet globálního správce služby Azure AD pro tenanta Azure AD, kterou chcete integrovat s. Tento účet musí být **školní nebo organizaci účet** a nemůže být **účtu Microsoft**.
* Je-li použít Expresní nastavení nebo upgradu z nástroje DirSync, musíte mít účet správce podnikové sítě pro vaše místní služby Active Directory.
* [Účty ve službě Active Directory](reference-connect-accounts-permissions.md) Pokud použijete instalační cesta vlastní nastavení.

### <a name="connectivity"></a>Připojení
* Server Azure AD Connect musí překlad názvů DNS pro intranetu i Internetu. DNS server musí být schopné překládat názvy do koncových bodů služby Azure AD a místní Active Directory.
* Pokud máte brány firewall na vašem intranetu a je potřeba otevřít porty mezi servery služby Azure AD Connect a řadiče domény a pak naleznete v tématu [Azure AD Connect porty](reference-connect-ports.md) Další informace.
* Pokud proxy server nebo brána firewall omezit adresy URL, které jsou přístupné, pak uvedené adresy URL v [Office 365 – adresy URL a rozsahy IP adres](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) musí být otevřeny.
  * Pokud používáte Microsoft cloudu v Německu nebo v cloudu Microsoft Azure Government, přejděte na téma [služby synchronizace Azure AD Connect instance aspekty](reference-connect-instances.md) pro adresy URL.
* Azure AD Connect (verze 1.1.614.0 a po provedení) ve výchozím nastavení používá TLS 1.2 pro šifrování komunikace mezi synchronizačního modulu a Azure AD. Pokud není k dispozici na příslušný operační systém TLS 1.2, Azure AD Connect postupně vrací zpět ke starším protokolům (TLS 1.1 a TLS 1.0). Azure AD Connect ve Windows serveru 2008 například používá protokol TLS 1.0, protože Windows Server 2008 nepodporuje protokol TLS 1.1 a TLS 1.2.
* Starší než verze 1.1.614.0 Azure AD Connect ve výchozím nastavení používá protokol TLS 1.0 pro šifrování komunikace mezi synchronizačního modulu a Azure AD. Chcete-li změnit na TLS 1.2, postupujte podle kroků v [povolení protokolu TLS 1.2 pro Azure AD Connect](#enable-tls-12-for-azure-ad-connect).
* Pokud používáte odchozího proxy serveru pro připojení k Internetu, následující nastavení **C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config** soubor musí být přidán pro Průvodce instalací a Azure AD Synchronizace bude moct připojit k Internetu a Azure AD Connect. Tento text je třeba zadat v dolní části souboru. V tomto kódu &lt;PROXYADRESS&gt; představuje skutečné proxy IP adresu nebo název hostitele.

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
### <a name="powershell-and-net-framework"></a>Prostředí PowerShell a rozhraní .net Framework
Azure AD Connect, závisí na Microsoft PowerShell a rozhraní .NET Framework 4.5.1. Je nutné tuto verzi nebo novější verze na serveru nainstalovaný. V závislosti na vaší verzi Windows serveru postupujte takto:

* Windows Server 2012R2
  * Ve výchozím nastavení je nainstalovaný Microsoft PowerShell. Nemusíte nic dělat.
  * Rozhraní .NET framework 4.5.1 a novějších verzí se nabízejí prostřednictvím služby Windows Update. Ujistěte se, že máte nainstalované nejnovější aktualizace na Windows Server v Ovládacích panelech.
* Windows Server 2008 R2 a Windows Server 2012
  * Je k dispozici v nejnovější verzi Powershellu Microsoft **Windows Management Framework 4.0**, která je dostupná na [Microsoft Download Center](http://www.microsoft.com/downloads).
  * Jsou k dispozici v rozhraní .NET framework 4.5.1 a novějších verzí [Microsoft Download Center](http://www.microsoft.com/downloads).
* Windows Server 2008
  * Je k dispozici v nejnovější podporovanou verzi prostředí PowerShell **Windows Management Framework 3.0**, která je dostupná na [Microsoft Download Center](http://www.microsoft.com/downloads).
  * Jsou k dispozici v rozhraní .NET framework 4.5.1 a novějších verzí [Microsoft Download Center](http://www.microsoft.com/downloads).

### <a name="enable-tls-12-for-azure-ad-connect"></a>Povolení protokolu TLS 1.2 pro služby Azure AD Connect
Starší než verze 1.1.614.0 Azure AD Connect ve výchozím nastavení používá protokol TLS 1.0 pro šifrování komunikace mezi serverem modul synchronizace a Azure AD. Toto můžete změnit tím, že nakonfigurujete aplikace .net pro použití protokolu TLS 1.2 ve výchozím nastavení na serveru. Další informace o protokolu TLS 1.2 nacházely v [Microsoft Security Advisory 2960358](https://technet.microsoft.com/security/advisory/2960358).

1. Protokol TLS 1.2 není možné v systému Windows Server 2008. Je třeba Windows Server 2008 R2 nebo novější. Ujistěte se, že je nainstalována .net 4.5.1 oprava hotfix pro váš operační systém, najdete v článku [Microsoft Security Advisory 2960358](https://technet.microsoft.com/security/advisory/2960358). Můžete mít tato oprava hotfix nebo novější verzi na serveru již nainstalovány.
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
### <a name="windows-remote-management"></a>Vzdálená správa Windows
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
* Microsoft Online Services přihlášení Pomocníka pro IT profesionály (nainstalované ale nejsou závislé na ni)
* Nástroje příkazového řádku Microsoft SQL Server 2012
* Microsoft SQL Server 2012 Express LocalDB
* Nativní klient systému Microsoft SQL Server 2012
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

* CPU: Jádro duální 1,6 GHz nebo vyšší
* PAMĚŤ: 2 GB nebo vyšší
* Virtuálního počítače Azure: Konfigurace A2 nebo vyšší

## <a name="next-steps"></a>Další postup
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).

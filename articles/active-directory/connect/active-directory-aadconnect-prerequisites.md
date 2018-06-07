---
title: 'Azure AD Connect: Požadavky a hardware | Microsoft Docs'
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
ms.openlocfilehash: 23d2c858fc51e35948bf83c6b5824b35020cb2e9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34593363"
---
# <a name="prerequisites-for-azure-ad-connect"></a>Požadavky pro Azure AD Connect
Toto téma popisuje požadavky a požadavky na hardware pro Azure AD Connect.

## <a name="before-you-install-azure-ad-connect"></a>Před instalací Azure AD Connect
Před instalací Azure AD Connect, existuje pár věcí, které potřebujete.

### <a name="azure-ad"></a>Azure AD
* Předplatné Azure nebo [zkušební předplatné](https://azure.microsoft.com/pricing/free-trial/). Toto předplatné je pouze požadované pro přístup k portálu Azure a ne pro pomocí služby Azure AD Connect. Pokud používáte prostředí PowerShell nebo Office 365, není nutné předplatné Azure používat Azure AD Connect. Pokud máte licenci pro Office 365, můžete také použít na portálu Office 365. S placenou licencí Office 365 můžete také získat na portálu Azure z portálu Office 365.
  * Můžete také [portál Azure](https://portal.azure.com). Tento portál nevyžaduje licenci Azure AD.
* [Přidání a ověření domény](../active-directory-domains-add-azure-portal.md) budete používat ve službě Azure AD. Například pokud máte v úmyslu použít pro vaše uživatele contoso.com, zkontrolujte tato doména byla ověřena a pouze nepoužíváte výchozí doménu contoso.onmicrosoft.com.
* Klient služby Azure AD umožňuje výchozích 50 tisíc objektů. Když je ověřit doménu, limit je zvýšena na 300 tisíc objektů. Pokud potřebujete ještě další objekty ve službě Azure AD, budete muset otevřít případ podpory tak, aby měl tento limit zvýšit ještě víc. Pokud potřebujete více než 500 tisíc objektů, potřebujete licenci, jako je například Office 365, Azure AD Basic, Azure AD Premium nebo Enterprise Mobility a zabezpečení.
* ADSyncPrep je modul skriptu prostředí PowerShell, který poskytuje funkce, které se používají při přípravě svého prostředí služby Active Directory pro Azure AD Connect.  Vyžaduje ADSyncPrep [Azure AD Microsoft Online v1.1 modulu PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0).  Verze 2 nebude fungovat.  Bude můžete nainstalovat pomocí modulu `Install-Module` rutiny.  Další informace najdete v části uvedeného odkazu.

### <a name="prepare-your-on-premises-data"></a>Příprava vaše místní data
* Použití [IdFix](https://support.office.com/article/Install-and-run-the-Office-365-IdFix-tool-f4bd2439-3e41-4169-99f6-3fabdfa326ac) k identifikaci chyby jako např. duplicitní položky a formátování problémy ve vašem adresáři před synchronizovat do Azure AD a Office 365.
* Zkontrolujte [funkce volitelné sync můžete povolit ve službě Azure AD](active-directory-aadconnectsyncservice-features.md) a vyhodnocení funkcí, které byste měli povolit.

### <a name="on-premises-active-directory"></a>Místní služby Active Directory
* AD schéma verze a doménová struktura úroveň funkčnosti musí být Windows Server 2003 nebo novější. Řadiče domény můžete spustit žádné verze tak dlouho, dokud jsou splněny požadavky úrovně schématu a doménovou strukturu.
* Pokud plánujete použít funkci **zpětný zápis hesla**, řadiče domény musí být v systému Windows Server 2008 (s nejnovější SP) nebo novější. Pokud jsou vaše řadiče domény v 2008 (pre-R2), pak musíte také použít [opravu hotfix KB2386717](http://support.microsoft.com/kb/2386717).
* Musí být zapisovatelný řadič domény používané Azure AD. Je **nepodporuje** řadiče jen pro čtení (řadič domény jen pro čtení) a Azure AD Connect není postupujte podle jakékoli zápisu přesměrování.
* Je **nepodporuje** používat místními doménovými strukturami nebo doménami pomocí domény SLD (jeden štítek domény).
* Je **nepodporuje** používat místními doménovými strukturami nebo doménami pomocí "s tečkami" (název obsahuje dobou ".") Názvy pro rozhraní NetBios.
* Doporučuje se [povolit odpadkový koš služby Active Directory](active-directory-aadconnectsync-recycle-bin.md).

### <a name="azure-ad-connect-server"></a>Server Azure AD Connect
* Azure AD Connect nejde nainstalovat na Small Business Server nebo Windows Server Essentials. Server musí používat systém Windows Server standard nebo vyšší.
* Server Azure AD Connect musí mít úplným grafickým uživatelským rozhraním nainstalována. Je **nepodporuje** instalace na jádro serveru.
* Azure AD Connect musí být nainstalován v systému Windows Server 2008 nebo novějším. Tento server může být řadič domény nebo členském serveru, při použití Expresní nastavení. Pokud chcete použít vlastní nastavení, server může být také samostatné a nemá být připojen k doméně.
* Pokud Azure AD Connect instalujete na Windows Server 2008 nebo Windows Server 2008 R2, pak je třeba použít nejnovější opravy hotfix z webu Windows Update. Instalace není možné spustit pomocí serveru bez nainstalované opravy.
* Pokud plánujete použít funkci **synchronizace hesel**, pak musí být server Azure AD Connect na Windows Server 2008 R2 SP1 nebo novější.
* Pokud chcete používat **skupiny účet spravované služby**, pak musí být server Azure AD Connect na Windows Server 2012 nebo novější.
* Musí mít server Azure AD Connect [rozhraní .NET Framework 4.5.1](#component-prerequisites) nebo novější a [Microsoft prostředí PowerShell 3.0](#component-prerequisites) nainstalovaný nebo novější.
* Server Azure AD Connect nesmí mít zásady skupiny přepis prostředí PowerShell povolené.
* Pokud se nasazuje Active Directory Federation Services, servery, kde jsou nainstalovány služby AD FS nebo Proxy webových aplikací, musí být Windows Server 2012 R2 nebo novější. [Vzdálená správa systému Windows](#windows-remote-management) musí být povolené na těchto serverech pro vzdálenou instalaci.
* Pokud se nasazuje Active Directory Federation Services, je třeba [certifikáty SSL](#ssl-certificate-requirements).
* Pokud se nasazuje Active Directory Federation Services, pak budete muset nakonfigurovat [překlad názvů](#name-resolution-for-federation-servers).
* Pokud globální Správci mají MFA povolená, pak adresu URL **https://secure.aadcdn.microsoftonline-p.com** musí být v seznamu důvěryhodných serverů. Zobrazí se výzva k přidat tento web do seznamu důvěryhodných serverů, když se zobrazí výzva k výzvu MFA a nebyl přidaný před. Můžete přidat do důvěryhodných serverů aplikace Internet Explorer.

### <a name="sql-server-used-by-azure-ad-connect"></a>SQL Server používá Azure AD Connect
* Azure AD Connect vyžaduje k ukládání dat identity databázi SQL Serveru. Ve výchozím nastavení je nainstalována SQL Server 2012 Express LocalDB (světla verze systému SQL Server Express). SQL Server Express má limit velikosti 10GB, která umožňuje spravovat přibližně 100 000 objektů. Pokud potřebujete spravovat větší rozsah objektů adresáře, budete muset bodu Průvodce instalací na jinou instalaci systému SQL Server.
* Pokud budete používat samostatný SQL Server, potom použijte tyto požadavky:
  * Azure AD Connect podporuje všechny verze systému Microsoft SQL Server ze systému SQL Server 2008 (s nejnovější aktualizaci Service Pack) do systému SQL Server 2016 SP1. Microsoft Azure SQL Database je **nepodporuje** jako databáze.
  * Je nutné použít velká a malá písmena kolace SQL. Tyto kolace jsou označeny \_CI_ v názvu. Je **nepodporuje** pro používání kolace malá a velká písmena, identifikovaný \_CS_ v názvu.
  * Může mít pouze jeden synchronizační modul na jednu instanci SQL. Je **nepodporuje** sdílení SQL instance s FIM nebo MIM Sync, DirSync nebo Azure AD Sync.

### <a name="accounts"></a>Účty
* Účet Azure AD globálního správce pro tenanta Azure AD, kterou chcete integrovat. Tento účet musí být **školní nebo organizace účet** a nemůže být **účtu Microsoft**.
* Pokud používáte Expresní nastavení nebo upgradu z nástroje DirSync, musí mít účet správce podnikové sítě pro místní služby Active Directory.
* [Účty ve službě Active Directory](active-directory-aadconnect-accounts-permissions.md) Pokud použijete vlastní nastavení Instalační cesta.

### <a name="connectivity"></a>Připojení
* Server Azure AD Connect musí překlad názvů DNS pro intranetu i Internetu. DNS server musí umět překládat názvy do služby Active Directory místní i koncové body Azure AD.
* Pokud máte brány firewall v intranetu a budete muset otevřít porty mezi servery Azure AD Connect a řadičů domény a pak najdete v části [Azure AD Connect porty](active-directory-aadconnect-ports.md) Další informace.
* Pokud server proxy nebo brány firewall omezit adresy URL, které jsou přístupné, pak adresy URL zdokumentována [Office 365 adresy URL a rozsahy IP adres](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) musí být otevřen.
  * Pokud používáte Microsoft Cloud v Německu nebo cloudu Microsoft Azure Government, pak v tématu [služba Azure AD Connect sync instance aspekty](active-directory-aadconnect-instances.md) pro adresy URL.
* Azure AD Connect (verze 1.1.614.0 a po) ve výchozím nastavení používá TLS 1.2 pro šifrování komunikace mezi synchronizační modul a Azure AD. Pokud není k dispozici na příslušný operační systém TLS 1.2, Azure AD Connect postupně vrací zpět ke starším protokolům (TLS 1.1 a TLS 1.0). Například Azure AD Connect a systémem Windows Server 2008 používá TLS 1.0, protože systém Windows Server 2008 nepodporuje protokolu TLS 1.1 a TLS 1.2.
* Starší než verze 1.1.614.0 Azure AD Connect ve výchozím nastavení používá protokol TLS 1.0 pro šifrování komunikace mezi synchronizační modul a Azure AD. Chcete-li změnit na TLS 1.2, postupujte podle kroků v [povolení TLS 1.2 pro Azure AD Connect](#enable-tls-12-for-azure-ad-connect).
* Pokud používáte odchozího proxy serveru pro připojení k Internetu, následující nastavení **C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config** soubor musí být přidán pro Průvodce instalací a Azure AD Synchronizace být schopni připojit k Internetu a Azure AD Connect. Tento text je třeba zadat v dolní části souboru. V tomto kódu &lt;PROXYADRESS&gt; představuje skutečné proxy IP adresu nebo název hostitele.

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

* Pokud proxy server vyžaduje ověřování, pak se [účet služby](active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-account) se musí nacházet v doméně a Instalační cesta vlastní nastavení musíte použít k určení [vlastního účtu služby](active-directory-aadconnect-get-started-custom.md#install-required-components). Musíte také jiné změny do souboru machine.config. Díky této změně v souboru machine.config instalace průvodce a synchronizační modul reagovat na žádosti o ověření z proxy serveru. Na všech stránkách Průvodce instalací, s výjimkou **konfigurace** stránky, podepsané v uživatelské přihlašovací údaje se používají. Na **konfigurace** stránky na konci tohoto průvodce instalací, kontext je přepnutá na [účet služby](active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-account) který byl vytvořen vy. Oddíl machine.config by měla vypadat takto.

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

* Pokud Azure AD Connect odešle žádost webové do služby Azure AD jako součást synchronizace adresářů, Azure AD může trvat až 5 minut reagovat. Je obvyklé, proxy servery tak, aby měl konfigurace časový limit nečinnosti připojení. Zkontrolujte, zda že konfigurace je nastavena na alespoň 6 minut nebo déle.

Další informace najdete v tématu MSDN [výchozí proxy Element](https://msdn.microsoft.com/library/kd3cf2ex.aspx).  
Další informace najdete v případě, že máte problémy s připojením, [řešení problémů s připojením](active-directory-aadconnect-troubleshoot-connectivity.md).

### <a name="other"></a>Ostatní
* Volitelné: Testovací uživatelský účet, chcete-li ověřit synchronizace.

## <a name="component-prerequisites"></a>Součást požadavky
### <a name="powershell-and-net-framework"></a>Prostředí PowerShell a rozhraní .net Framework
Azure AD Connect, závisí na Microsoft PowerShell a rozhraní .NET Framework 4.5.1. Je nutné tuto verzi nebo novější verze na serveru nainstalována. V závislosti na vaší verze systému Windows Server postupujte takto:

* Windows Server 2012R2
  * Microsoft PowerShell je nainstalován ve výchozím nastavení. Není vyžadována žádná akce.
  * Rozhraní .NET framework 4.5.1 a pozdějších verzích jsou nabízena prostřednictvím služby Windows Update. Zkontrolujte, zda že jsou nainstalovány nejnovější aktualizace na Windows Server v Ovládacích panelech.
* Windows Server 2008 R2 a Windows Server 2012
  * Je k dispozici v nejnovější verzi Microsoft PowerShell **Windows Management Framework 4.0**, k dispozici na [Microsoft Download Center](http://www.microsoft.com/downloads).
  * Rozhraní .NET framework 4.5.1 a pozdějších verzích jsou dostupné na [Microsoft Download Center](http://www.microsoft.com/downloads).
* Windows Server 2008
  * Podporované verze prostředí PowerShell je k dispozici v **Windows Management Framework 3.0**, k dispozici na [Microsoft Download Center](http://www.microsoft.com/downloads).
  * Rozhraní .NET framework 4.5.1 a pozdějších verzích jsou dostupné na [Microsoft Download Center](http://www.microsoft.com/downloads).

### <a name="enable-tls-12-for-azure-ad-connect"></a>Povolit protokol TLS 1.2 pro Azure AD Connect
Starší než verze 1.1.614.0 Azure AD Connect ve výchozím nastavení používá protokol TLS 1.0 pro šifrování komunikace mezi synchronizační modul server a Azure AD. Toto můžete změnit tak, že nakonfigurujete aplikace .net pomocí protokolu TLS 1.2 ve výchozím nastavení na serveru. Další informace o protokolu TLS 1.2 naleznete v [2960358 informační zpravodaj zabezpečení společnosti Microsoft](https://technet.microsoft.com/security/advisory/2960358).

1. Protokol TLS 1.2 nelze povolit v systému Windows Server 2008. Je třeba Windows Server 2008 R2 nebo novější. Ověřte, že je nainstalována rozhraní .net 4.5.1 oprava hotfix pro operační systém najdete v tématu [2960358 informační zpravodaj zabezpečení společnosti Microsoft](https://technet.microsoft.com/security/advisory/2960358). Můžete mít tato oprava hotfix nebo novější verze na serveru již nainstalován.
2. Pokud používáte Windows Server 2008 R2, zkontrolujte, jestli že je zapnutá TLS 1.2. Na server Windows Server 2012 a novějších verzích by měl být již povolena TLS 1.2.
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
4. Pokud také chcete povolit protokol TLS 1.2 mezi synchronizační modul server a vzdálený SQL Server, tak zkontrolujte, zda máte požadovaná verze nainstalované pro [podpora protokolu TLS 1.2 pro Microsoft SQL Server](https://support.microsoft.com/kb/3135244).

## <a name="prerequisites-for-federation-installation-and-configuration"></a>Předpoklady pro federační instalace a konfigurace
### <a name="windows-remote-management"></a>Vzdálená správa systému Windows
Při nasazení služby AD FS nebo Proxy webových aplikací pomocí Azure AD Connect, zkontrolujte tyto požadavky:

* Pokud cílový server připojený k doméně, pak se ujistěte, zda je povoleno vzdálené spravované Windows
  * V PSH příkaz okno se zvýšenými oprávněními použijte příkaz `Enable-PSRemoting –force`
* Pokud je cílový server nepřipojená k doméně připojený počítač WAP, pak existuje několik dalších požadavků
  * V cílovém počítači (WAP počítači):
    * Ujistěte se, winrm (Vzdálená správa systému Windows / WS-Management) prostřednictvím modulu snap-in Services je spuštěna služba
    * V PSH příkaz okno se zvýšenými oprávněními použijte příkaz `Enable-PSRemoting –force`
  * Na počítači, na kterém je spuštěn Průvodce (Pokud cílový počítač je připojený k nebo nedůvěryhodné doméně nepřipojená k doméně):
    * V PSH příkaz okno se zvýšenými oprávněními použijte příkaz `Set-Item WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate`
    * Ve Správci serveru:
      * Přidat hostitele hraniční sítě WAP do fondu počítače (Správce serveru -> Správa -> Přidat servery..., použijte kartu DNS)
      * Karta servery všechny správce serveru: klikněte pravým tlačítkem na WAP server a zvolte spravovat jako..., zadejte přihlašovací údaje pro místní (domény) pro počítač WAP
      * K ověření vzdáleného připojení PSH, na kartě servery všechny správce serveru: klikněte pravým tlačítkem na WAP server a zvolte prostředí Windows PowerShell. Vzdálené relace PSH by měla otevřít zajistit, že by bylo možné navázat vzdálené relace prostředí PowerShell.

### <a name="ssl-certificate-requirements"></a>Požadavky na certifikát SSL
* Důrazně se doporučuje použít stejný certifikát protokolu SSL ve všech uzlech farmu služby AD FS a všechny proxy servery webových aplikací.
* Certifikát musí být X509 certifikátu.
* Certifikát podepsaný svým držitelem můžete použít na federačních serverech v prostředí testovací laboratoře. Pro produkční prostředí, ale doporučujeme získat certifikát z veřejné certifikační Autority.
  * Pokud používá certifikát, který není veřejně důvěryhodný, ujistěte se, že certifikát nainstalovaný na každém serveru Proxy webových aplikací je důvěryhodný na místním serveru a na všech federačních serverech
* Identita certifikátu musí odpovídat název federační služby (např. sts.contoso.com).
  * Identita je buď subjektu alternativní (SAN) příponu názvu typu dNSName nebo, pokud nejsou žádné položky sítě SAN, v názvu subjektu zadán jako běžný název.  
  * Více položek sítě SAN může být obsažené v certifikátu, pokud jeden z nich odpovídá název federační služby.
  * Pokud máte v úmyslu používat funkci připojení k síti na pracovišti, je síť SAN s další požadované s hodnotou **enterpriseregistration.** Následuje hlavní název uživatele (UPN) příponu vaší organizace, například **enterpriseregistration.contoso.com**.
* Na základě klíčů další nové generace (CNG) rozhraní CryptoAPI a poskytovatelů úložiště klíčů certifikátů nejsou podporovány. To znamená, že musíte použít certifikát na základě CSP (zprostředkovatel kryptografických služeb) a ne na KSP (Zprostředkovatel úložiště klíčů).
* Zástupný znak-certifikáty jsou podporovány.

### <a name="name-resolution-for-federation-servers"></a>Překlad názvů pro federační servery
* Nastavte záznamy DNS pro název služby federation (např. sts.contoso.com) služby AD FS pro (vaše interní server DNS) intranetu a extranetu (veřejném DNS prostřednictvím doménového registrátora). Záznam DNS intranetu, ujistěte se, že používáte A záznamů a není záznamy CNAME. To je potřeba pro ověřování systému windows správně pracovat na vašem počítači připojeném k doméně.
* Pokud nasazujete víc než jeden server služby AD FS nebo služby proxy webové aplikace, pak se ujistěte, že jste nakonfigurovali nástroj pro vyrovnávání zatížení a že záznamy DNS pro název federační služby AD FS (např. sts.contoso.com) přejděte na nástroje pro vyrovnávání zatížení.
* Pro integrované ověřování systému windows pro aplikace prohlížeče Internet Explorer v intranetu Ujistěte se, že název federační služby AD FS (např. sts.contoso.com) se přidá do zóny intranetu v aplikaci Internet Explorer. To můžete řídit prostřednictvím zásad skupiny a nasazené na všech počítačích připojených k doméně.

## <a name="azure-ad-connect-supporting-components"></a>Podpora součásti Azure AD Connect
Následuje seznam součástí, které Azure AD Connect nainstaluje na server, kde je nainstalován Azure AD Connect. Tento seznam je pro základní Expresní instalace. Pokud chcete použít jiný Server SQL na stránce instalace synchronizační služby, není místně nainstalovaný SQL Express LocalDB.

* Azure AD Connect Health
* Microsoft Online Services Sign-In Pomocníka pro IT profesionály (nainstalovanou ale žádná závislost na něm)
* Nástroje příkazového řádku Microsoft SQL Server 2012
* Microsoft SQL Server 2012 Express LocalDB
* Nativní klient Microsoft SQL Server 2012
* Microsoft Visual C++ 2013 opětovnou distribuci balíčku

## <a name="hardware-requirements-for-azure-ad-connect"></a>Požadavky na hardware pro Azure AD Connect
Následující tabulka uvádí minimální požadavky pro počítač synchronizace Azure AD Connect.

| Počet objektů ve službě Active Directory | Procesor | Memory (Paměť) | Velikost pevného disku |
| --- | --- | --- | --- |
| Méně než 10 000 |1,6 GHz |4 GB |70 GB |
| 10,000–50,000 |1,6 GHz |4 GB |70 GB |
| 50,000–100,000 |1,6 GHz |16 GB |100 GB |
| U 100 000 nebo více objektů se vyžaduje plnou verzi systému SQL Server | | | |
| 100,000–300,000 |1,6 GHz |32 GB |300 GB |
| 300,000–600,000 |1,6 GHz |32 GB |450 GB |
| Více než 600 000 |1,6 GHz |32 GB |500 GB |

Minimální požadavky na počítačích se systémem služby AD FS nebo servery webových aplikací je následující:

* Využití procesoru: Duální základní 1,6 GHz nebo vyšší
* PAMĚŤ: 2 GB nebo vyšší
* Virtuálního počítače Azure: Konfigurace A2 nebo vyšší

## <a name="next-steps"></a>Další postup
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](active-directory-aadconnect.md).

---
title: Implementace synchronizace hodnot hash hesel pomocí Azure AD Connect synchronizace | Microsoft Docs
description: Poskytuje informace o tom, jak funguje synchronizace hodnot hash hesel a jak se nastavuje.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 05f16c3e-9d23-45dc-afca-3d0fa9dbf501
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
search.appverid:
- MET150
ms.collection: M365-identity-device-management
ms.openlocfilehash: c16882f35c9ca79644cd2b51ce4cd88bba516ed2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89652076"
---
# <a name="implement-password-hash-synchronization-with-azure-ad-connect-sync"></a>Implementace synchronizace hodnot hash hesel pomocí synchronizace Azure AD Connect
Tento článek poskytuje informace, které potřebujete k synchronizaci uživatelských hesel z místní instance služby Active Directory s instancí cloudové Azure Active Directory (Azure AD).

## <a name="how-password-hash-synchronization-works"></a>Jak funguje synchronizace hodnot hash hesel
Služba Active Directory Domain Services ukládá hesla ve formě reprezentace hodnoty hash skutečného hesla uživatele. Hodnota hash je výsledkem jednosměrné matematické funkce ( *algoritmus hash*). Neexistuje žádný způsob, jak výsledek jednosměrné funkce převést zpět na heslo v prostém textu. 

Pokud chcete synchronizovat heslo, Azure AD Connect Sync extrahuje hodnotu hash hesla z místní instance služby Active Directory. Dodatečné zpracování zabezpečení se aplikuje na hodnotu hash hesla předtím, než se synchronizuje do služby Azure Active Directory Authentication Service. Hesla se synchronizují podle jednotlivých uživatelů a v chronologickém pořadí.

Skutečný tok dat procesu synchronizace hodnot hash hesel je podobný synchronizaci uživatelských dat. Hesla jsou ale synchronizovaná častěji než standardní okno synchronizace adresářů pro jiné atributy. Proces synchronizace hodnot hash hesel se spouští každé 2 minuty. Frekvence tohoto procesu se nedá změnit. Při synchronizaci hesla přepíše existující heslo cloudu.

Při prvním povolení funkce synchronizace hodnot hash hesel provádí počáteční synchronizaci hesel všech uživatelů v oboru. Nelze explicitně definovat podmnožinu hesel uživatelů, které chcete synchronizovat. Pokud však existuje více konektorů, je možné zakázat synchronizaci hodnot hash hesel pro některé konektory, ale ne jiné pomocí rutiny [set-ADSyncAADPasswordSyncConfiguration](../../active-directory-domain-services/tutorial-configure-password-hash-sync.md) .

Když změníte místní heslo, aktualizované heslo se synchronizuje častěji během několika minut.
Funkce synchronizace hodnot hash hesel automaticky opakuje neúspěšné pokusy o synchronizaci. Pokud při pokusu o synchronizaci hesla dojde k chybě, do prohlížeče událostí se zaznamená chyba.

Synchronizace hesla nemá žádný vliv na uživatele, který je aktuálně přihlášený.
Vaše aktuální relace cloudové služby není okamžitě ovlivněna synchronizovanou změnou hesla, ke které dojde, když jste přihlášeni ke cloudové službě. Pokud ale cloudová služba vyžaduje, abyste znovu ověřili, musíte zadat nové heslo.

Uživatel musí zadat své podnikové přihlašovací údaje podruhé k ověření ve službě Azure AD, a to bez ohledu na to, jestli jsou přihlášeni k podnikové síti. Tento model může být minimalizován, ale pokud uživatel po přihlášení vybere políčko zůstat přihlášeni (políčko zůstat přihlášeni). Tento výběr nastaví soubor cookie relace, který obchází ověřování po dobu 180 dnů. Správce Azure AD může povolit nebo zakázat chování políčko zůstat přihlášeni. Kromě toho můžete zkrátit výzvy k zadání hesla zapnutím [bezproblémového jednotného přihlašování](how-to-connect-sso.md), které automaticky podepisuje uživatele v případě, že jsou na podnikových zařízeních, která jsou připojená k podnikové síti.

> [!NOTE]
> Synchronizace hesla je podporována pouze pro uživatele typu objektu ve službě Active Directory. Není podporován pro typ objektu iNetOrgPerson.

### <a name="detailed-description-of-how-password-hash-synchronization-works"></a>Podrobný popis způsobu, jakým funguje synchronizace hodnot hash hesel

V následující části jsou popsány podrobné informace o tom, jak funguje synchronizace hodnot hash hesel mezi službou Active Directory a službou Azure AD.

![Podrobný tok hesla](./media/how-to-connect-password-hash-synchronization/arch3b.png)

1. Každé dvě minuty agent synchronizace hodnot hash hesel na serveru služby AD Connect požaduje uložené hodnoty hash hesel (atribut unicodePwd) z řadiče domény.  Tato žádost je přes standardní protokol replikace [MS-DRSR](/openspecs/windows_protocols/ms-drsr/f977faaa-673e-4f66-b9bf-48c640241d47) , který se používá k synchronizaci dat mezi řadiči domény. Aby bylo možné získat hodnoty hash hesla, musí mít účet služby replikované změny adresáře a replikovat změny adresáře všechna oprávnění služby AD (ve výchozím nastavení udělená při instalaci).
2. Před odesláním řadič domény zašifruje hodnotu hash hesla MD4 pomocí klíče, který je hash [MD5](https://www.rfc-editor.org/rfc/rfc1321.txt) klíče relace RPC a Salt. Pak pošle výsledek do agenta synchronizace hodnoty hash hesla přes RPC. Řadič domény také předá sůl agentovi synchronizace pomocí protokolu replikace řadiče domény, takže Agent bude moci dešifrovat obálku.
3. Po tom, co agent synchronizace hodnot hash hesel obsahuje šifrovanou obálku, použije [MD5CryptoServiceProvider](/dotnet/api/system.security.cryptography.md5cryptoserviceprovider?view=netcore-3.1) a sůl k vygenerování klíče k dešifrování přijatých dat zpět do původního formátu MD4. Agent synchronizace hodnoty hash hesla nikdy nemá přístup k heslu nešifrovaných textů. Použití MD5 agenta synchronizace hodnot hash hesla je výhradně pro kompatibilitu replikačního protokolu s řadičem domény a používá se pouze pro místní počítače mezi řadičem domény a agentem synchronizace hodnot hash hesel.
4. Agent synchronizace hodnot hash hesel rozbalí 16bajtový binární hodnotu hash hesla na 64 bajtů, a to tak, že nejprve převede hodnotu hash na šestnáctkový řetězec 32-Byte a pak tento řetězec převede zpátky do binárního formátu UTF-16.
5. Agent synchronizace hodnot hash hesla přidá na uživatele hodnotu Salt, která se skládá z 10 bajtů o velikosti soli 64 bajtů, aby bylo možné dále chránit původní hodnotu hash.
6. Agent synchronizace hodnot hash hesel pak zkombinuje hodnotu hash MD4 Plus pro každou uživatelskou sůl a zaznamená vstup do funkce [PBKDF2](https://www.ietf.org/rfc/rfc2898.txt) . 1000 iterací algoritmu hash s klíčem [HMAC-SHA256](/dotnet/api/system.security.cryptography.hmacsha256?view=netcore-3.1) se používají. 
7. Agent synchronizace hodnot hash hesla převezme výsledný 32 bajtový algoritmus hash, zřetězí na uživatele hodnotu Salt a počet SHA256ch iterací (pro použití službou Azure AD) a pak přenáší řetězec z Azure AD Connect do služby Azure AD prostřednictvím protokolu TLS.</br> 
8. Když se uživatel pokusí přihlásit ke službě Azure AD a zadá heslo, heslo se spustí pomocí stejného procesu MD4 + Salt + PBKDF2 + HMAC-SHA256. Pokud výsledný algoritmus hash odpovídá hodnotě hash uložené ve službě Azure AD, zadal uživatel správné heslo a bude ověřený.

> [!NOTE]
> Původní algoritmus hash MD4 se nepřenáší do služby Azure AD. Místo toho se přenáší hodnota hash SHA256 původního algoritmu hash MD4. Výsledkem je, že pokud se získá hodnota hash uložená v Azure AD, nedá se použít v rámci útoku typu Pass-the-hash.

### <a name="security-considerations"></a>Důležité informace o zabezpečení

Při synchronizaci hesel není verze ve formátu prostého textu hesla vystavena funkci synchronizace hodnot hash hesel, službě Azure AD ani žádné z přidružených služeb.

Ověřování uživatelů probíhá na Azure AD, nikoli na vlastní instanci služby Active Directory organizace. Data SHA256 hesla uložená v Azure AD – hodnota hash původního algoritmu hash MD4 je bezpečnější než úložiště, které je uložené ve službě Active Directory. Vzhledem k tomu, že hodnota hash SHA256 nemůže být dešifrována, nelze ji převést do prostředí Active Directory organizace a v rámci útoku pass-the-hash je uvedena jako platné uživatelské heslo.

### <a name="password-policy-considerations"></a>Pokyny k zásadám hesel

Existují dva typy zásad hesel, které jsou ovlivněny povolením synchronizace hodnot hash hesel:

* Zásada složitosti hesla
* Zásady vypršení platnosti hesla

#### <a name="password-complexity-policy"></a>Zásada složitosti hesla

Když je povolená synchronizace hodnot hash hesel, zásady složitosti hesla v místní instanci služby Active Directory přepíšou zásady složitosti v cloudu pro synchronizované uživatele. Pro přístup ke službám Azure AD můžete použít všechna platná hesla z vaší místní instance služby Active Directory.

> [!NOTE]
> Hesla pro uživatele, kteří se vytvářejí přímo v cloudu, podléhají zásadám hesel definovaným v cloudu.

#### <a name="password-expiration-policy"></a>Zásady vypršení platnosti hesla

Pokud je uživatel v rozsahu synchronizace hodnot hash hesel, ve výchozím nastavení je heslo účtu cloudu nastavené na hodnotu *nikdy nevyprší platnost*.

Můžete se nadále přihlašovat ke cloudovým službám pomocí synchronizovaného hesla, jehož platnost vypršela v místním prostředí. Heslo vašeho cloudu se aktualizuje při příštím změně hesla v místním prostředí.

##### <a name="enforcecloudpasswordpolicyforpasswordsyncedusers"></a>EnforceCloudPasswordPolicyForPasswordSyncedUsers

Pokud jsou k dispozici synchronizující uživatelé, kteří komunikují pouze s integrovanými službami Azure AD a musí splňovat i zásady vypršení platnosti hesla, můžete vynutit dodržování zásad vypršení platnosti hesel Azure AD tím, že povolíte funkci *EnforceCloudPasswordPolicyForPasswordSyncedUsers* .

Je-li *EnforceCloudPasswordPolicyForPasswordSyncedUsers* zakázán (což je výchozí nastavení), Azure AD Connect nastaví atribut PasswordPolicies synchronizovaných uživatelů na hodnotu "DisablePasswordExpiration". To se provádí při každém synchronizaci hesla uživatele a instruuje službu Azure AD, aby pro tohoto uživatele ignorovala zásady vypršení platnosti hesla cloudu. Hodnotu atributu můžete zjistit pomocí modulu Azure AD PowerShell pomocí tohoto příkazu:

`(Get-AzureADUser -objectID <User Object ID>).passwordpolicies`

Pokud chcete povolit funkci EnforceCloudPasswordPolicyForPasswordSyncedUsers, spusťte následující příkaz pomocí modulu MSOnline PowerShell, jak je znázorněno níže. Pro parametr enable musíte zadat Ano, jak je znázorněno níže:

```
Set-MsolDirSyncFeature -Feature EnforceCloudPasswordPolicyForPasswordSyncedUsers
cmdlet Set-MsolDirSyncFeature at command pipeline position 1
Supply values for the following parameters:
Enable: yes
Confirm
Continue with this operation?
[Y] Yes [N] No [S] Suspend [?] Help (default is "Y"): y
```

Po povolení Služba Azure AD nepřejde na každého synchronizovaného uživatele, aby odebral `DisablePasswordExpiration` hodnotu z atributu PasswordPolicies. Místo toho je hodnota nastavena na `None` během příští synchronizace hesla pro každého uživatele při dalším změně hesla v místní službě AD.  

Doporučuje se povolit EnforceCloudPasswordPolicyForPasswordSyncedUsers před povolením synchronizace hodnot hash hesel, aby počáteční synchronizace hodnot hash hesel nepřidala `DisablePasswordExpiration` hodnotu do atributu PasswordPolicies pro uživatele.

Výchozí zásady hesel Azure AD vyžadují, aby uživatelé změnili hesla každých 90 dní. Pokud je vaše zásada ve službě AD také 90 dní, obě tyto zásady by se měly shodovat. Pokud však zásada AD není 90 dní, můžete aktualizovat zásady hesel služby Azure AD tak, aby odpovídaly pomocí příkazu Set-MsolPasswordPolicy PowerShellu.

Azure AD podporuje pro každou registrovanou doménu samostatné zásady vypršení platnosti hesla.

Upozornění: Pokud jsou v Azure AD synchronizované účty, u kterých je potřeba mít hesla bez vypršení platnosti, musíte explicitně přidat `DisablePasswordExpiration` hodnotu do atributu PasswordPolicies objektu User v Azure AD.  To můžete provést spuštěním následujícího příkazu.

`Set-AzureADUser -ObjectID <User Object ID> -PasswordPolicies "DisablePasswordExpiration"`

> [!NOTE]
> Příkaz Set-MsolPasswordPolicy PowerShellu nebude fungovat u federovaných domén. 

#### <a name="synchronizing-temporary-passwords-and-force-password-change-on-next-logon"></a>Synchronizace dočasných hesel a "vynucení změny hesla při příštím přihlášení"

Je typický vynutit, aby uživatel při prvním přihlášení změnil heslo, zejména když dojde k resetování hesla správce.  Obvykle se označuje jako nastavení "dočasného" hesla a je dokončený zaškrtnutím příznaku "uživatel musí změnit heslo při příštím přihlášení" u objektu uživatele ve službě Active Directory (AD).
  
Funkce dočasného hesla pomáhá zajistit, že přenos vlastnictví přihlašovacích údajů se při prvním použití dokončí, aby se minimalizovala doba, během které má více než jedna osoba znalosti o těchto přihlašovacích údajích.

Aby bylo možné v Azure AD podporovat dočasná hesla pro synchronizované uživatele, můžete povolit funkci *ForcePasswordChangeOnLogOn* spuštěním následujícího příkazu na vašem serveru Azure AD Connect:

`Set-ADSyncAADCompanyFeature -ForcePasswordChangeOnLogOn $true`

> [!NOTE]
> Vynucení, když uživatel změní heslo při příštím přihlášení, vyžaduje změnu hesla.  Azure AD Connect nezačne příznak změny hesla vynutit sám o sobě; je doplněné ke zjištěné změně hesla, ke které dochází během synchronizace hodnot hash hesel.

> [!CAUTION]
> Tuto funkci byste měli používat jenom v případě, že je v tenantovi povolený zpětný zápis hesla SSPR a hesla.  To znamená, že pokud uživatel změní heslo pomocí SSPR, bude synchronizován se službou Active Directory.

#### <a name="account-expiration"></a>Vypršení platnosti účtu

Pokud vaše organizace používá atribut accountExpires jako součást správy uživatelských účtů, tento atribut není synchronizovaný do Azure AD. V důsledku toho bude v Azure AD stále aktivní účet Active Directory s vypršenou platností v prostředí nakonfigurovaném pro synchronizaci hodnot hash hesel. Doporučujeme, aby v případě vypršení platnosti účtu měla akce pracovního postupu aktivovat skript PowerShellu, který zakáže účet Azure AD uživatele (použijte rutinu [set-AzureADUser](/powershell/module/azuread/set-azureaduser?view=azureadps-2.0) ). Pokud je účet zapnutý, měla by se zapnout instance služby Azure AD.

### <a name="overwrite-synchronized-passwords"></a>Přepsat synchronizovaná hesla

Správce může ručně resetovat heslo pomocí Windows PowerShellu.

V takovém případě nové heslo přepíše vaše synchronizované heslo a všechny zásady hesel definované v cloudu se uplatní na nové heslo.

Pokud znovu změníte své místní heslo, nové heslo se synchronizuje do cloudu a přepíše ručně aktualizované heslo.

Synchronizace hesla nemá žádný vliv na uživatele Azure, který je přihlášený. Synchronizovaná Změna hesla, ke které dojde při přihlášení ke cloudové službě, nemá okamžitou vliv na vaši aktuální relaci cloudové služby. POLÍČKO zůstat přihlášeni rozšiřuje dobu trvání tohoto rozdílu. Když cloudová služba vyžaduje, abyste znovu ověřili, musíte zadat nové heslo.

### <a name="additional-advantages"></a>Další výhody

- Obecně platí, že synchronizace hodnot hash hesel je jednodušší k implementaci než služba FS (Federation Service). Nevyžaduje žádné další servery a eliminuje závislost na vysoce dostupné federační službě k ověřování uživatelů.
- Synchronizaci hodnot hash hesel je také možné povolit kromě federace. Dá se použít jako záložní, pokud vaše federační služba funguje jako výpadek.

## <a name="password-hash-sync-process-for-azure-ad-domain-services"></a>Proces synchronizace hodnot hash hesel pro Azure AD Domain Services

Pokud používáte Azure AD Domain Services k poskytování staršího ověřování pro aplikace a služby, které potřebují používat protokol Kerberos, protokol LDAP nebo NTLM, jsou některé další procesy součástí toku synchronizace hodnot hash hesel. Azure AD Connect používá další postup k synchronizaci hodnot hash hesel do služby Azure AD pro použití v Azure AD Domain Services:

> [!IMPORTANT]
> Azure AD Connect by měl být nainstalovaný a nakonfigurovaný jenom pro synchronizaci s místními služba AD DS prostředími. Pro synchronizaci objektů zpět do Azure AD se nepodporuje instalace Azure AD Connect ve spravované doméně Azure služba AD DS.
>
> Pokud povolíte Azure služba AD DS pro vašeho tenanta Azure AD, Azure AD Connect synchronizuje jenom starší hodnoty hash hesel. Následující kroky se nepoužívají, pokud k synchronizaci místního prostředí služba AD DS pomocí Azure AD používáte jenom Azure AD Connect.
>
> Pokud vaše starší aplikace nepoužívají ověřování NTLM nebo jednoduché vazby LDAP, doporučujeme pro Azure služba AD DS zakázat synchronizaci hodnot hash hesel protokolu NTLM. Další informace najdete v tématu [zakázání slabých šifrovacích sad a synchronizace hodnot hash přihlašovacích údajů NTLM](../../active-directory-domain-services/secure-your-domain.md).

1. Azure AD Connect načte veřejný klíč pro instanci Azure AD Domain Services klienta.
1. Když uživatel změní heslo, uloží místní řadič domény výsledek změny hesla (hodnoty hash) ve dvou atributech:
    * *unicodePwd* pro hodnotu hash hesla protokolu NTLM.
    * *supplementalCredentials* pro hodnotu hash hesla protokolu Kerberos.
1. Azure AD Connect detekuje změny hesel prostřednictvím kanálu replikace adresářů (změny atributů, které se musí replikovat do jiných řadičů domény).
1. Pro každého uživatele, jehož heslo bylo změněno, Azure AD Connect provede následující kroky:
    * Generuje náhodný symetrický klíč AES 256.
    * Generuje náhodnou inicializační vektor potřebný pro první kolo šifrování.
    * Extrahuje hodnoty hash hesla protokolu Kerberos z atributů *supplementalCredentials* .
    * Kontroluje nastavení *SyncNtlmPasswords* zabezpečení konfigurace Azure AD Domain Services.
        * Pokud je toto nastavení zakázané, vygeneruje náhodnou a vysokou entropii hodnotu hash NTLM (odlišnou od hesla uživatele). Tato hodnota hash je pak kombinována s přesnou hodnotou hash hesla protokolu Kerberos z atributu *supplementalCrendetials* do jedné struktury dat.
        * Pokud je povoleno, kombinuje hodnotu atributu *unicodePwd* s extrahovanou hodnotou hash hesla protokolu Kerberos z atributu *supplementalCredentials* do jedné struktury dat.
    * Šifruje jedinou datovou strukturu pomocí symetrického klíče AES.
    * Šifruje symetrický klíč AES pomocí Azure AD Domain Services veřejného klíče klienta.
1. Azure AD Connect přenáší zašifrovaný symetrický klíč AES, šifrovanou strukturu dat obsahující hodnoty hash hesla a inicializační vektor do Azure AD.
1. Azure AD ukládá šifrovaný symetrický klíč AES, šifrovanou strukturu dat a inicializační vektor pro uživatele.
1. Azure AD přenáší zašifrovaný symetrický klíč AES, šifrovanou strukturu dat a inicializační vektor pomocí mechanismu interní synchronizace přes šifrovanou relaci HTTP, aby Azure AD Domain Services.
1. Azure AD Domain Services načte privátní klíč pro instanci tenanta z trezoru klíčů Azure.
1. Pro každou zašifrovanou sadu dat (představující změnu hesla jednoho uživatele) Azure AD Domain Services pak proveďte následující kroky:
    * K dešifrování symetrického klíče AES používá jeho privátní klíč.
    * Používá symetrický klíč AES s inicializačním vektorem k dešifrování šifrované datové struktury, která obsahuje hodnoty hash hesla.
    * Zapíše hodnoty hash hesla protokolu Kerberos, které obdrží, do Azure AD Domain Services řadiče domény. Hodnoty hash jsou uloženy do atributu *supplementalCredentials* objektu uživatele, který je zašifrovaný do veřejného klíče řadiče domény Azure AD Domain Services.
    * Azure AD Domain Services zapíše hodnotu hash hesla protokolu NTLM získanou do řadiče domény Azure AD Domain Services. Hodnota hash se uloží do atributu *unicodePwd* objektu uživatele, který se zašifruje do veřejného klíče řadiče domény Azure AD Domain Services.

## <a name="enable-password-hash-synchronization"></a>Povolení synchronizace hodnoty hash hesel

>[!IMPORTANT]
>Pokud migrujete z AD FS (nebo jiných federačních technologií) na synchronizaci hodnot hash hesel, důrazně doporučujeme, abyste provedli náš podrobný průvodce nasazením, který jste publikovali [tady](https://aka.ms/adfstophsdpdownload).

Když nainstalujete Azure AD Connect pomocí možnosti **expresní nastavení** , synchronizace hodnot hash hesel se automaticky aktivuje. Další informace najdete v tématu [Začínáme s Azure AD Connect pomocí expresního nastavení](how-to-connect-install-express.md).

Pokud při instalaci Azure AD Connect použijete vlastní nastavení, je synchronizace hodnoty hash hesla k dispozici na přihlašovací stránce uživatele. Další informace najdete v tématu [vlastní instalace Azure AD Connect](how-to-connect-install-custom.md).

![Povolení synchronizace hodnot hash hesel](./media/how-to-connect-password-hash-synchronization/usersignin2.png)

### <a name="password-hash-synchronization-and-fips"></a>Synchronizace hodnot hash hesel a FIPS
Pokud byl server uzamčen podle standardu FIPS (Federal Information Processing Standard), je algoritmus MD5 zakázán.

**Pokud chcete povolit MD5 pro synchronizaci hodnot hash hesel, proveďte následující kroky:**

1. Přejít na%programfiles%\Azure AD Sync\Bin.
2. Otevřete miiserver.exe.config.
3. Na konci souboru přejít na uzel Configuration/runtime.
4. Přidejte následující uzel: `<enforceFIPSPolicy enabled="false"/>`
5. Uložte provedené změny.

Pro referenci by tento fragment kódu vypadal takto:

```
    <configuration>
        <runtime>
            <enforceFIPSPolicy enabled="false"/>
        </runtime>
    </configuration>
```

Informace o zabezpečení a standardu FIPS najdete v článku o [synchronizaci hodnot hash hesel Azure AD, šifrování a dodržování standardu FIPS](https://blogs.technet.microsoft.com/enterprisemobility/2014/06/28/aad-password-sync-encryption-and-fips-compliance/).

## <a name="troubleshoot-password-hash-synchronization"></a>Řešení potíží se synchronizací hodnot hash hesel
Pokud máte problémy se synchronizací hodnoty hash hesla, přečtěte si téma [řešení potíží se synchronizací hodnot hash hesel](tshoot-connect-password-hash-synchronization.md).

## <a name="next-steps"></a>Další kroky
* [Azure AD Connect synchronizace: přizpůsobení možností synchronizace](how-to-connect-sync-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)
* [Podrobný plán nasazení pro migraci z AD FS na synchronizaci hodnot hash hesel](https://aka.ms/authenticationDeploymentPlan)

---
title: Implementace synchronizace hodnot hash hesel pomocí synchronizace Azure AD Connect | Dokumentace Microsoftu
description: Poskytuje informace o fungování synchronizaci hodnot hash hesel a jak nastavit.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 05f16c3e-9d23-45dc-afca-3d0fa9dbf501
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
search.appverid:
- MET150
ms.collection: M365-identity-device-management
ms.openlocfilehash: f6451507eb5a25f432c73468d0da0db1838c8c9a
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79261382"
---
# <a name="implement-password-hash-synchronization-with-azure-ad-connect-sync"></a>Implementace synchronizace hodnot hash hesel pomocí synchronizace Azure AD Connect
Tento článek obsahuje informace, které potřebujete k synchronizaci hesel uživatelů mezi místní instancí Active Directory a instanci cloudové služby Azure Active Directory (Azure AD).

## <a name="how-password-hash-synchronization-works"></a>Jak funguje synchronizace hodnot hash hesel
Active Directory domain Services ukládá ve formě reprezentaci hodnoty hash hesla skutečného uživatelského hesla. Hodnota hash je výsledkem jednosměrné matematické funkce ( *algoritmus hash*). Neexistuje žádný způsob, jak výsledek jednosměrné funkce převést zpět na heslo v prostém textu. 

Synchronizace Azure AD Connect synchronizaci hesla, extrahuje vaše hodnoty hash hesla z místní instance Active Directory. Zpracování dodatečné zabezpečení se použije pro hodnoty hash hesla předtím, než se synchronizují do služby ověřování Azure Active Directory. Hesla se nesynchronizují na jednotlivé uživatele a v chronologickém pořadí.

Samotný datový tok proces synchronizace hodnot hash hesel se podobá synchronizace dat uživatele. Hesla se však nesynchronizují častěji než časový interval synchronizace standardní adresář pro jiné atributy. Proces synchronizace hodnot hash hesel se spouští každé dvě minuty. Nelze změnit frekvenci tohoto procesu. Při synchronizaci hesla, přepíše stávající heslo cloudu.

Při prvním povolení funkce synchronizace hodnot hash hesla, provede počáteční synchronizaci hesel všech uživatelů v oboru. Nelze explicitně definovat podmnožinu hesla uživatelů, které se mají synchronizovat. Pokud však existuje více konektorů, je možné zakázat synchronizaci hodnot hash hesel pro některé konektory, ale ne jiné pomocí rutiny [set-ADSyncAADPasswordSyncConfiguration](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant) .

Pokud změníte místní heslo, aktualizované heslo synchronizována, nejčastěji v řádu minut.
Funkce synchronizace hodnot hash hesel automaticky opakovat pokusy o synchronizaci se nezdařilo. Pokud dojde k chybě při pokusu o synchronizaci hesla, je zaznamenána chyba do prohlížeče událostí.

Synchronizace hesla nemá žádný vliv na uživatele, který je aktuálně přihlášený.
Aktuální relace cloudové služby není okamžitě ovlivněny změnou synchronizovaných hesel, ke které dochází, když se přihlásíte, cloudové službě. Ale při Cloudová služba vyžaduje, abyste znovu provést ověření, budete muset zadat nové heslo.

Uživatel musí zadat své podnikové přihlašovací údaje podruhé k ověření do služby Azure AD, bez ohledu na to, zda jsou přihlášeni k podnikové síti. Tento vzor lze minimalizovat, ale pokud si uživatel vybere zůstat přihlášeni (políčko zůstat Přihlášeni) políčko při přihlášení. Tento výběr nastaví soubor cookie relace, který obchází ověřování po dobu 180 dnů. Políčko zůstat Přihlášeni chování můžete povolit nebo zakázat pomocí Správce Azure AD. Kromě toho můžete zkrátit výzvy k zadání hesla zapnutím [bezproblémového jednotného přihlašování](how-to-connect-sso.md), které automaticky podepisuje uživatele v případě, že jsou na podnikových zařízeních, která jsou připojená k podnikové síti.

> [!NOTE]
> Synchronizace hesla je podporována pouze pro typ objektu uživatele ve službě Active Directory. Není podporována pro typ objektu iNetOrgPerson.

### <a name="detailed-description-of-how-password-hash-synchronization-works"></a>Podrobný popis toho, jak funguje synchronizace hodnot hash hesel

Následující část popisuje, podrobné, jak funguje synchronizace hodnot hash hesel mezi službami Active Directory a Azure AD.

![Podrobné heslo toku](./media/how-to-connect-password-hash-synchronization/arch3b.png)

1. Každé dvě minuty, agent synchronizace hodnot hash hesel na žádosti serveru AD Connect uložených hodnot hash hesel (atributu unicodePwd) z řadiče domény.  Tato žádost je přes standardní protokol replikace [MS-DRSR](https://msdn.microsoft.com/library/cc228086.aspx) , který se používá k synchronizaci dat mezi řadiči domény. Účet služby musí mít replikace změn adresáře a replikovat všechny změny adresáře AD oprávněním (ve výchozím nastavení na instalaci) k získání hodnoty hash hesla.
2. Před odesláním řadič domény zašifruje hodnotu hash hesla MD4 pomocí klíče, který je hash [MD5](https://www.rfc-editor.org/rfc/rfc1321.txt) klíče relace RPC a Salt. Potom pošle výsledek k agentovi synchronizace hodnoty hash hesla přes RPC. Řadič domény taky předá sůl agenta synchronizace, aby pomocí protokolu replikace řadiče domény, tak agent bude schopná dešifrovat obálky.
3. Po tom, co agent synchronizace hodnot hash hesel obsahuje šifrovanou obálku, použije [MD5CryptoServiceProvider](https://msdn.microsoft.com/library/System.Security.Cryptography.MD5CryptoServiceProvider.aspx) a sůl k vygenerování klíče k dešifrování přijatých dat zpět do původního formátu MD4. Agent synchronizace hodnot hash hesel nikdy má přístup k hesla v nešifrovaném textu. Heslo hash agenta synchronizace, aby pro použití algoritmu MD5 je určené výhradně pro kompatibilitu protokolů replikace s řadiči domény a používá se pouze v místním prostředí mezi řadiči domény a agenta synchronizace hodnot hash hesel.
4. Agent synchronizace hodnot hash hesel rozšíří hodnotu hash hesla binární 16 bajtů 64 bajtů převedením první-the-hash na 32 bajtů šestnáctkový řetězec, pak převedete tento řetězec zpět do binárního souboru s kódováním UTF-16.
5. Přidá agenta synchronizace hodnot hash hesel za uživatele hodnota salt, skládající se z hodnota salt délku 10 bajtů na binární 64 bajtů dalším stupněm ochrany původní hodnoty hash.
6. Agent synchronizace hodnot hash hesel pak zkombinuje hodnotu hash MD4 Plus pro každou uživatelskou sůl a zaznamená vstup do funkce [PBKDF2](https://www.ietf.org/rfc/rfc2898.txt) . 1000 iterací algoritmu hash s klíčem [HMAC-SHA256](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx) se používají. 
7. Agent synchronizace hodnot hash hesel trvá výslednou hodnotu hash 32 bajtů, zřetězí obojí hodnota salt uživatele a počtu SHA256 iterace k němu (pro použití službou Azure AD), pak přenáší řetězec, ze služby Azure AD Connect ke službě Azure AD přes protokol SSL.</br> 
8. Když se uživatel pokusí přihlásit k Azure AD a zadá své heslo, heslo se spustí prostřednictvím stejné MD4 + řetězce salt + PBKDF2 + HMAC SHA256 procesu. Pokud výsledná hodnota hash odpovídající hodnotě hash uložené ve službě Azure AD, uživatel zadá správné heslo a je ověřený.

> [!NOTE]
> Původní hodnota hash MD4 se nebudou přenášet do služby Azure AD. Místo toho se přenášejí hash SHA256 objektu původní hodnota hash MD4. V důsledku toho pokud je hodnota hash uložené ve službě Azure AD, ho nelze použít v místních útoku pass-the-hash.

### <a name="security-considerations"></a>Aspekty zabezpečení

Při synchronizaci hesel heslo ve formátu prostého textu se nevystaví funkci synchronizace hodnoty hash hesla ke službě Azure AD, nebo žádné související služby.

Ověření uživatele se provádí pomocí služby Azure AD, nikoli s instancí služby Active Directory organizace. SHA256 heslo data uložená ve službě Azure AD – hodnoty hash původní hodnota hash MD4 – je bezpečnější, než se ukládá ve službě Active Directory. Dále protože tato hodnota hash SHA256 nelze dešifrovat, nemohou být vrátil do prostředí služby Active Directory organizace a zobrazí jako platného uživatele heslo v rámci útoku pass-the-hash.

### <a name="password-policy-considerations"></a>Požadavky zásad hesel

Existují dva druhy zásady pro hesla, které jsou ovlivněny povolení synchronizace hodnot hash hesel:

* Zásadám složitosti hesel
* Zásady vypršení platnosti hesla

#### <a name="password-complexity-policy"></a>Zásadám složitosti hesel

Pokud je povolená synchronizace hodnot hash hesel, zásady složitost hesel v místní instanci Active Directory přepsat zásady složitosti v cloudu pro synchronizovaní uživatelé. Můžete všechny platné hesel z vaší místní instance Active Directory pro přístup ke službám Azure AD.

> [!NOTE]
> Hesla pro uživatele, které jsou vytvořené přímo v cloudu se stále mohou zásady pro hesla, jak jsou definovány v cloudu.

#### <a name="password-expiration-policy"></a>Zásady vypršení platnosti hesla

Pokud je uživatel v rozsahu synchronizace hodnot hash hesel, ve výchozím nastavení je heslo účtu cloudu nastavené na hodnotu *nikdy nevyprší platnost*.

Můžete pokračovat k přihlášení k vašim cloudovým službám pomocí synchronizovaných heslo, které vypršelo ve vašem místním prostředí. Vaše heslo cloudu se aktualizuje při další změně hesla v místním prostředí.

##### <a name="public-preview-of-the-enforcecloudpasswordpolicyforpasswordsyncedusers-feature"></a>Verze Public Preview funkce *EnforceCloudPasswordPolicyForPasswordSyncedUsers*

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

Po povolení Azure AD nepřejde ke každému synchronizovanému uživateli, aby z atributu PasswordPolicies odebral hodnotu `DisablePasswordExpiration`. Místo toho je hodnota nastavená na `None` při příští synchronizaci hesla pro každého uživatele při dalším změně hesla v místní službě AD.  

Doporučuje se povolit EnforceCloudPasswordPolicyForPasswordSyncedUsers před tím, než povolíte synchronizaci hodnot hash hesel, takže počáteční synchronizace hodnot hash hesel nepřidá hodnotu `DisablePasswordExpiration` do atributu PasswordPolicies pro uživatele.

Výchozí zásady hesel Azure AD vyžadují, aby uživatelé změnili hesla každých 90 dní. Pokud je vaše zásada ve službě AD také 90 dní, obě tyto zásady by se měly shodovat. Pokud ale zásada AD není 90 dní, můžete aktualizovat zásady hesel Azure AD tak, aby odpovídaly pomocí příkazu Set-MsolPasswordPolicy prostředí PowerShell.

Azure AD podporuje pro každou registrovanou doménu samostatné zásady vypršení platnosti hesla.

Upozornění: Pokud jsou v Azure AD synchronizované účty, u kterých je potřeba mít hesla bez vypršení platnosti, musíte explicitně přidat `DisablePasswordExpiration` hodnoty do atributu PasswordPolicies objektu User v Azure AD.  To můžete provést spuštěním následujícího příkazu.

`Set-AzureADUser -ObjectID <User Object ID> -PasswordPolicies "DisablePasswordExpiration"`

> [!NOTE]
> Tato funkce je teď v Public Preview.
> Příkaz prostředí PowerShell Set-MsolPasswordPolicy nebude fungovat u federovaných domén. 

#### <a name="public-preview-of-synchronizing-temporary-passwords-and-force-password-change-on-next-logon"></a>Public Preview synchronizace dočasných hesel a "vynucení změny hesla při příštím přihlášení"

Je typický vynutit, aby uživatel při prvním přihlášení změnil heslo, zejména když dojde k resetování hesla správce.  Obvykle se označuje jako nastavení "dočasného" hesla a je dokončený zaškrtnutím příznaku "uživatel musí změnit heslo při příštím přihlášení" u objektu uživatele ve službě Active Directory (AD).
  
Funkce dočasného hesla pomáhá zajistit, že přenos vlastnictví přihlašovacích údajů se při prvním použití dokončí, aby se minimalizovala doba, během které má více než jedna osoba znalosti o těchto přihlašovacích údajích.

Aby bylo možné v Azure AD podporovat dočasná hesla pro synchronizované uživatele, můžete povolit funkci *ForcePasswordChangeOnLogOn* spuštěním následujícího příkazu na vašem serveru Azure AD Connect:

`Set-ADSyncAADCompanyFeature  -ForcePasswordChangeOnLogOn $true`

> [!NOTE]
> Vynucení, když uživatel změní heslo při příštím přihlášení, vyžaduje změnu hesla.  Azure AD Connect nezačne příznak změny hesla vynutit sám o sobě; je doplněné ke zjištěné změně hesla, ke které dochází během synchronizace hodnot hash hesel.

> [!CAUTION]
> Tuto funkci byste měli používat jenom v případě, že je v tenantovi povolený zpětný zápis hesla SSPR a hesla.  To znamená, že pokud uživatel změní heslo pomocí SSPR, bude synchronizován se službou Active Directory.

> [!NOTE]
> Tato funkce je ve verzi Public Preview hned teď.

#### <a name="account-expiration"></a>Vypršení platnosti účtu

Pokud vaše organizace používá atribut AccountExpires zadává jako součást správy uživatelských účtů, není tento atribut synchronizovaný do služby Azure AD. V důsledku toho účet služby Active Directory vypršela platnost v prostředí nakonfigurovaná pro synchronizaci hodnot hash hesel bude nadále aktivní ve službě Azure AD. Doporučujeme, aby v případě vypršení platnosti účtu měla akce pracovního postupu aktivovat skript PowerShellu, který zakáže účet Azure AD uživatele (použijte rutinu [set-AzureADUser](https://docs.microsoft.com/powershell/module/azuread/set-azureaduser?view=azureadps-2.0) ). Naopak pokud účet je zapnuté, instance služby Azure AD by být zapnutý.

### <a name="overwrite-synchronized-passwords"></a>Přepište synchronizovaných hesel

Správce může ručně resetovat heslo pomocí prostředí Windows PowerShell.

V takovém případě nové heslo přepíše synchronizované heslo a nové heslo se použijí všechny zásady hesel, které jsou definovány v cloudu.

Pokud změníte svoje heslo v místním znovu nové heslo se synchronizují do cloudu, a přepíše ručně aktualizované heslo.

Synchronizace hesla nemá žádný vliv na uživatele Azure, který je přihlášen. Aktuální relace cloudové služby není okamžitě ovlivněny změnou synchronizovaných hesel, ke které dochází, když jste přihlášeni s cloudovou službou. Políčko zůstat Přihlášeni rozšiřuje dobu trvání tohoto rozdílu. Cloudová služba vyžaduje, abyste znovu provést ověření, budete muset zadat nové heslo.

### <a name="additional-advantages"></a>Další výhody

- Obecně platí je jednodušší než federační služby implementace synchronizace hodnot hash hesel. Nevyžaduje žádné další servery a eliminuje závislost na vysoce dostupných federačních služeb k ověřování uživatelů.
- Synchronizace hodnot hash hesel se dá povolit i kromě federace. To může sloužit jako záložní, pokud vaše služba federation service dojde k výpadku.

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

## <a name="enable-password-hash-synchronization"></a>Povolit synchronizaci hodnot hash hesel

>[!IMPORTANT]
>Pokud migrujete z AD FS (nebo jiných federačních technologií) na synchronizaci hodnot hash hesel, důrazně doporučujeme, abyste provedli náš podrobný průvodce nasazením, který jste publikovali [tady](https://aka.ms/adfstophsdpdownload).

Když nainstalujete Azure AD Connect pomocí možnosti **expresní nastavení** , synchronizace hodnot hash hesel se automaticky aktivuje. Další informace najdete v tématu [Začínáme s Azure AD Connect pomocí expresního nastavení](how-to-connect-install-express.md).

Pokud použijete vlastní nastavení při instalaci Azure AD Connect, je k dispozici na na přihlašovací stránku uživatele synchronizace hodnot hash hesel. Další informace najdete v tématu [vlastní instalace Azure AD Connect](how-to-connect-install-custom.md).

![Povolení synchronizace hodnot hash hesel](./media/how-to-connect-password-hash-synchronization/usersignin2.png)

### <a name="password-hash-synchronization-and-fips"></a>Synchronizace hodnot hash hesel a FIPS
Pokud váš server byl uzamčen podle federální informace o zpracování Standard (FIPS), je zakázané MD5.

**Pokud chcete povolit MD5 pro synchronizaci hodnot hash hesel, proveďte následující kroky:**

1. Přejdete na Sync\Bin %programfiles%\Azure AD.
2. Otevřete miiserver.exe.config.
3. Přejděte do uzlu Konfigurace nebo modulu runtime na konci souboru.
4. Přidejte následující uzel: `<enforceFIPSPolicy enabled="false"/>`
5. Uložte provedené změny.

Tento fragment kódu pro informaci je to, co by měl vypadat jako:

```
    <configuration>
        <runtime>
            <enforceFIPSPolicy enabled="false"/>
        </runtime>
    </configuration>
```

Informace o zabezpečení a standardu FIPS najdete v článku o [synchronizaci hodnot hash hesel Azure AD, šifrování a dodržování standardu FIPS](https://blogs.technet.microsoft.com/enterprisemobility/2014/06/28/aad-password-sync-encryption-and-fips-compliance/).

## <a name="troubleshoot-password-hash-synchronization"></a>Řešení potíží s synchronizaci hodnot hash hesel
Pokud máte problémy se synchronizací hodnoty hash hesla, přečtěte si téma [řešení potíží se synchronizací hodnot hash hesel](tshoot-connect-password-hash-synchronization.md).

## <a name="next-steps"></a>Další kroky
* [Azure AD Connect synchronizace: přizpůsobení možností synchronizace](how-to-connect-sync-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)
* [Podrobný plán nasazení pro migraci z AD FS na synchronizaci hodnot hash hesel](https://aka.ms/authenticationDeploymentPlan)

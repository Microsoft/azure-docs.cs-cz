---
title: Implementace synchronizace hash hesel pomocí synchronizace Azure AD Connect | Dokumenty společnosti Microsoft
description: Obsahuje informace o tom, jak funguje synchronizace hodnot hash hesel a jak nastavit.
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
ms.openlocfilehash: c41b11ab65f5710d338ce0041579e1eb4678ec42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331370"
---
# <a name="implement-password-hash-synchronization-with-azure-ad-connect-sync"></a>Implementace synchronizace hodnot hash hesel pomocí synchronizace Azure AD Connect
Tento článek obsahuje informace, které potřebujete k synchronizaci uživatelských hesel z místní instance služby Active Directory do instance služby Azure Active Directory (Azure AD).

## <a name="how-password-hash-synchronization-works"></a>Jak funguje synchronizace hodnot hash hesel
Služba domény služby Active Directory ukládá hesla skutečného uživatelského hesla ve formě reprezentace hodnoty hash. Hodnota hash je výsledkem jednosměrné matematické funkce *(algoritmus hash).* Neexistuje žádný způsob, jak výsledek jednosměrné funkce převést zpět na heslo v prostém textu. 

Chcete-li synchronizovat heslo, Azure AD Connect synchronizace extrahuje vaše heslo hash z místní instance služby Active Directory. Další zpracování zabezpečení se použije na hash hesla před jeho synchronizací se službou ověřování Azure Active Directory. Hesla jsou synchronizována pro jednotlivé uživatele a v chronologickém pořadí.

Skutečný tok dat procesu synchronizace hodnot hash hesla je podobný synchronizaci uživatelských dat. Hesla jsou však synchronizovány častěji než standardní okno synchronizace adresářů pro ostatní atributy. Proces synchronizace hodnot hash hesla se spouští každé 2 minuty. Četnost tohoto procesu nelze změnit. Při synchronizaci hesla přepíše existující heslo cloudu.

Při prvním povolení funkce synchronizace hodnot hash hesla provede počáteční synchronizaci hesel všech uživatelů v oboru. Nelze explicitně definovat podmnožinu uživatelských hesel, která chcete synchronizovat. Pokud však existuje více konektorů, je možné zakázat synchronizaci hash hesel pro některé konektory, ale ne pro jiné pomocí rutiny [Set-ADSyncAADPasswordSyncConfiguration.](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant)

Při změně místního hesla je aktualizované heslo synchronizováno, nejčastěji během několika minut.
Funkce synchronizace hodnot hash hesla automaticky opakuje neúspěšné pokusy o synchronizaci. Pokud dojde k chybě během pokusu o synchronizaci hesla, je v prohlížeči událostí zaznamenána chyba.

Synchronizace hesla nemá žádný vliv na uživatele, který je aktuálně přihlášen.
Vaše aktuální relace cloudové služby není okamžitě ovlivněna synchronizovanou změnou hesla, ke které dojde, když jste přihlášeni, ke cloudové službě. Pokud však cloudová služba vyžaduje, abyste se znovu ověřili, musíte zadat nové heslo.

Uživatel musí zadat svá podniková pověření podruhé k ověření ve službě Azure AD, bez ohledu na to, zda jsou přihlášeni k podnikové síti. Tento vzor lze minimalizovat, ale pokud uživatel zaškrtne zaškrtnout zachovat přihlášení (KMSI) zaškrtávací políčko při přihlášení. Tento výběr nastaví soubor cookie relace, který obchází ověřování po dobu 180 dnů. Kmsi chování může být povoleno nebo zakázáno správcem Služby Azure AD. Kromě toho můžete snížit výzvy k zadání hesla zapnutím [bezproblémové jednotné přihlašovací zabezpečení](how-to-connect-sso.md), které automaticky přihlásí uživatele, když jsou na svých podnikových zařízeních připojených k podnikové síti.

> [!NOTE]
> Synchronizace hesel je podporována pouze pro uživatele typu objektu ve službě Active Directory. Není podporován pro typ objektu iNetOrgPerson.

### <a name="detailed-description-of-how-password-hash-synchronization-works"></a>Podrobný popis fungování synchronizace hodnot hash hesel

V následující části je podrobně popsáno, jak funguje synchronizace hodnot hash hesel mezi službou Active Directory a službou Azure AD.

![Podrobný tok hesla](./media/how-to-connect-password-hash-synchronization/arch3b.png)

1. Každé dvě minuty agent synchronizace hash hesel na serveru Služby AD Connect požaduje uložené hodnoty hash hesla (atribut unicodePwd) z řadiče domény.  Tento požadavek je prostřednictvím standardního replikačního protokolu [MS-DRSR,](https://msdn.microsoft.com/library/cc228086.aspx) který slouží k synchronizaci dat mezi řadiči domény. Účet služby musí mít replikovat změny adresáře a replikovat změny adresáře všechna oprávnění služby AD (udělená ve výchozím nastavení při instalaci), aby bylo možné získat hodnoty hash hesel.
2. Před odesláním řadič domény zašifruje hash hesla MD4 pomocí klíče, který je [hash MD5](https://www.rfc-editor.org/rfc/rfc1321.txt) klíče relace Vzdálené volání procedur a soli. Potom odešle výsledek agentovi synchronizace hash hesla přes Vzdálené volání procedur. Řadič domény také předá sůl synchronizačnímu agentovi pomocí protokolu replikace řadiče domény, takže agent bude moci dešifrovat obálku.
3. Poté, co má agent synchronizace hash hesla šifrovanou obálku, použije [MD5CryptoServiceProvider](https://msdn.microsoft.com/library/System.Security.Cryptography.MD5CryptoServiceProvider.aspx) a sůl ke generování klíče k dešifrování přijatých dat zpět do původního formátu MD4. Agent synchronizace hash hesla nikdy nemá přístup k heslu prostého textu. Použití md5 agenta synchronizace hash hesla je výhradně pro kompatibilitu replikačního protokolu s řadičem domény a používá se pouze v prostorách mezi řadičem domény a agentem synchronizace hash hesel.
4. Agent synchronizace hash hesla rozbalí 16bajtové binární heslo hash na 64 bajtů nejprve převede hash na 32bajtový šestnáctkový řetězec a poté převede tento řetězec zpět na binární s kódováním UTF-16.
5. Agent synchronizace hash hesla přidá sůl pro jednotlivé uživatele, která se skládá z 10bajtové soli, do binárního souboru o velikosti 64 bajtů, aby byla dále chráněna původní hash.
6. Agent synchronizace hash hesla pak kombinuje hash MD4 plus sůl pro jednotlivé uživatele a zadá jej do funkce [PBKDF2.](https://www.ietf.org/rfc/rfc2898.txt) Používá se 1000 iterací algoritmu hash [hmac-SHA256.](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx) 
7. Agent synchronizace hash hesla přebírá výsledný 32bajtový hash, zřetězí sůl na uživatele a počet iterací SHA256 na něj (pro použití službou Azure AD), pak přenáší řetězec z Azure AD Connect do Azure AD přes TLS.</br> 
8. Když se uživatel pokusí přihlásit k Azure AD a zadá své heslo, heslo se spustí prostřednictvím stejného procesu MD4 + sůl + PBKDF2 + HMAC-SHA256. Pokud výsledná hash odpovídá hash uložené ve službě Azure AD, uživatel zadal správné heslo a je ověřen.

> [!NOTE]
> Původní hash MD4 se nepřenáší do Azure AD. Místo toho sha256 hash je přenášen. V důsledku toho pokud je získán hash uložený ve službě Azure AD, nelze použít v útoku místní pass-the-hash.

### <a name="security-considerations"></a>Důležité informace o zabezpečení

Při synchronizaci hesel není verze hesla ve formátu prostého textu vystavena funkci synchronizace hash hesla, službě Azure AD nebo žádné přidružené službě.

Ověřování uživatelů probíhá proti Azure AD, nikoli proti vlastní instanci služby Active Directory organizace. Data hesla SHA256 uložená ve službě Azure AD – hash původní hodu MD4 – jsou bezpečnější než data uložená ve službě Active Directory. Dále vzhledem k tomu, že tuto hash SHA256 nelze dešifrovat, nelze ji přenést zpět do prostředí služby Active Directory organizace a prezentovat jako platné uživatelské heslo při útoku pass-the-hash.

### <a name="password-policy-considerations"></a>Důležité informace o zásadách hesel

Povolení synchronizace hodnot hash hesel má vliv na dva typy zásad hesel:

* Zásady složitosti hesla
* Zásady vypršení platnosti hesla

#### <a name="password-complexity-policy"></a>Zásady složitosti hesla

Pokud je povolena synchronizace hodnot hash hesel, zásady složitosti hesla v místní instanci služby Active Directory přepíší zásady složitosti v cloudu pro synchronizované uživatele. Ke službám Azure AD můžete použít všechna platná hesla z místní instance služby Active Directory.

> [!NOTE]
> Hesla pro uživatele, kteří jsou vytvořeni přímo v cloudu, stále podléhají zásadám hesel definovaným v cloudu.

#### <a name="password-expiration-policy"></a>Zásady vypršení platnosti hesla

Pokud je uživatel v oboru synchronizace hodnot hash hesel, je ve výchozím nastavení heslo cloudového účtu nastaveno na *nikdy nevyprší*.

Ke cloudovým službám se můžete dál přihlašovat pomocí synchronizovaného hesla, jehož platnost vypršela v místním prostředí. Vaše cloudové heslo se aktualizuje při příští změně hesla v místním prostředí.

##### <a name="public-preview-of-the-enforcecloudpasswordpolicyforpasswordsyncedusers-feature"></a>Veřejná předběžná verze funkce *EnforceCloudPasswordPolicyForPasswordSyncedUsers*

Pokud existují synchronizované uživatele, kteří jsou pouze interakci s integrovanými službami Azure AD a musí také v souladu se zásadami vypršení platnosti hesla, můžete je vynutit, aby v souladu s vaší zásady vypršení platnosti hesla Azure AD povolením *EnforceCloudPasswordPolicyForPasswordSyncedUsers* funkce.

Když *enforceCloudPasswordPolicyForPasswordSyncedUživatelé* je zakázáno (což je výchozí nastavení), Azure AD Connect nastaví PasswordPolicies atribut synchronizovaných uživatelů na "DisablePasswordExpiration". To se provádí při každé synchronizaci hesla uživatele a pokyn Azure AD ignorovat zásady vypršení platnosti hesla cloud u tohoto uživatele. Hodnotu atributu můžete zkontrolovat pomocí modulu Azure AD PowerShell pomocí následujícího příkazu:

`(Get-AzureADUser -objectID <User Object ID>).passwordpolicies`


Chcete-li povolit funkci EnforceCloudPasswordPolicyForPasswordSyncedUsers, spusťte následující příkaz pomocí modulu MSOnline PowerShell, jak je znázorněno níže. Budete muset zadat ano pro Enable parametr, jak je znázorněno níže :

```
Set-MsolDirSyncFeature -Feature EnforceCloudPasswordPolicyForPasswordSyncedUsers
cmdlet Set-MsolDirSyncFeature at command pipeline position 1
Supply values for the following parameters:
Enable: yes
Confirm
Continue with this operation?
[Y] Yes [N] No [S] Suspend [?] Help (default is "Y"): y
```

Po povolení Azure AD nepřejde na každého `DisablePasswordExpiration` synchronizovaného uživatele odebrat hodnotu z PasswordPolicies atribut. Místo toho je hodnota `None` nastavena na během další synchronizace hesel pro každého uživatele při další změně hesla v místním ad.  

Před povolením synchronizace hodnot hash hesel se doporučuje povolit funkci EnforceCloudPasswordPolicyForPasswordSyncedUsers, `DisablePasswordExpiration` aby počáteční synchronizace hodnot hash hesel nepřidala hodnotu atributu PasswordPolicies pro uživatele.

Výchozí zásady hesel Azure AD vyžaduje, aby uživatelé měnit svá hesla každých 90 dní. Pokud je vaše zásada ve ad je také 90 dnů, obě zásady by měly odpovídat. Pokud však zásada služby AD není 90 dní, můžete aktualizovat zásady hesla služby Azure AD tak, aby odpovídaly pomocí příkazu Set-MsolPasswordPolicy PowerShell.

Azure AD podporuje samostatné zásady vypršení platnosti hesla na registrovanou doménu.

Upozornění: Pokud existují synchronizované účty, které potřebují mít non-expiring hesla ve službě `DisablePasswordExpiration` Azure AD, musíte explicitně přidat hodnotu PasswordPolicies atribut objektu uživatele ve službě Azure AD.  To lze provést spuštěním následujícího příkazu.

`Set-AzureADUser -ObjectID <User Object ID> -PasswordPolicies "DisablePasswordExpiration"`

> [!NOTE]
> Tato funkce je právě teď ve verzi Public Preview.
> Příkaz Set-MsolPasswordPolicy PowerShell nebude fungovat ve federovaných doménách. 

#### <a name="public-preview-of-synchronizing-temporary-passwords-and-force-password-change-on-next-logon"></a>Veřejný náhled synchronizace dočasných hesel a "Vynutit změnu hesla při příštím přihlášení"

Je typické vynutit uživateli změnit své heslo během prvního přihlášení, zejména poté, co dojde k resetování hesla správce.  Běžně se označuje jako nastavení "dočasného" hesla a je doplněno kontrolou příznaku "Uživatel musí změnit heslo při příštím přihlášení" u objektu uživatele ve službě AD (Active Directory).
  
Funkce dočasného hesla pomáhá zajistit, že převod vlastnictví pověření je dokončen při prvním použití, aby se minimalizovala doba, po kterou více než jedna osoba zná toto pověření.

Chcete-li podporovat dočasná hesla ve službě Azure AD pro synchronizované uživatele, můžete povolit funkci *ForcePasswordChangeOnLogOn* spuštěním následujícího příkazu na serveru Azure AD Connect:

`Set-ADSyncAADCompanyFeature  -ForcePasswordChangeOnLogOn $true`

> [!NOTE]
> Vynucení uživatele změnit heslo při příštím přihlášení vyžaduje změnu hesla současně.  Azure AD Connect nepřevezme příznak změny hesla force sám; je doplňkem zjištěné změny hesla, ke které dochází během synchronizace hodnot hash hesla.

> [!CAUTION]
> Tuto funkci byste měli používat pouze v případě, že jsou v tenantovi povoleny zpětné zápisy z vlastních pr a hesel.  Je to tak, že pokud uživatel změní své heslo prostřednictvím programu SSPR, bude synchronizován se službou Active Directory.

> [!NOTE]
> Tato funkce je právě teď ve verzi Public Preview.

#### <a name="account-expiration"></a>Vypršení platnosti účtu

Pokud vaše organizace používá atribut accountExpires jako součást správy uživatelských účtů, tento atribut není synchronizován s Azure AD. V důsledku toho vypršela platnost účtu služby Active Directory v prostředí nakonfigurované pro synchronizaci hodnot hash hesla bude stále aktivní ve službě Azure AD. Pokud vypršela platnost účtu, akce pracovního postupu by měla aktivovat skript prostředí PowerShell, který zakáže účet Azure AD uživatele (použijte rutinu [Set-AzureADUser).](https://docs.microsoft.com/powershell/module/azuread/set-azureaduser?view=azureadps-2.0) Naopak při zapnutí účtu, měla by být zapnutá instance Azure AD.

### <a name="overwrite-synchronized-passwords"></a>Přepsání synchronizovaných hesel

Správce může ručně obnovit heslo pomocí prostředí Windows PowerShell.

V takovém případě nové heslo přepíše synchronizované heslo a všechny zásady hesel definované v cloudu se použijí na nové heslo.

Pokud znovu změníte místní heslo, nové heslo se synchronizuje do cloudu a přepíše ručně aktualizované heslo.

Synchronizace hesla nemá žádný vliv na uživatele Azure, který je přihlášen. Vaše aktuální relace cloudové služby není okamžitě ovlivněna synchronizovanou změnou hesla, ke které dochází při přihlášení ke cloudové službě. KMSI prodlužuje dobu trvání tohoto rozdílu. Pokud cloudová služba vyžaduje, abyste se znovu ověřili, musíte zadat nové heslo.

### <a name="additional-advantages"></a>Další výhody

- Obecně platí, že synchronizace hodnot hash hesel je jednodušší než federace služby. Nevyžaduje žádné další servery a eliminuje závislost na vysoce dostupné federační službě k ověření uživatelů.
- Synchronizaci hodnot hash hesel lze také povolit kromě federace. Může být použit jako záložní, pokud vaše federační služba dojde k výpadku.

## <a name="password-hash-sync-process-for-azure-ad-domain-services"></a>Proces synchronizace hash hesel pro služby Azure AD Domain Services

Pokud používáte služby Azure AD Domain Services k poskytování starších verzí ověřování pro aplikace a služby, které je potřeba používat protokol Kerberos, LDAP nebo NTLM, některé další procesy jsou součástí toku synchronizace hodnot hash hesla. Azure AD Connect používá další následující proces k synchronizaci hashe hesel do Azure AD pro použití ve službě Azure AD Domain Services:

> [!IMPORTANT]
> Azure AD Connect by se měl nainstalovat a nakonfiguroval jenom pro synchronizaci s místními prostředími služby AD DS. Není podporováno instalace Azure AD Connect ve spravované doméně Azure AD DS k synchronizaci objektů zpět do Azure AD.
>
> Azure AD Connect synchronizuje pouze starší nastavení hashe hesla, když povolíte Azure AD DS pro vašeho klienta Azure AD. Následující kroky se nepoužívají, pokud používáte jenom Azure AD Connect k synchronizaci místního prostředí Služby AD DS s Azure AD.
>
> Pokud starší aplikace nepoužívají ověřování NTLM nebo jednoduché vazby LDAP, doporučujeme zakázat synchronizaci hash hesel NTLM pro Azure AD DS. Další informace naleznete [v tématu Disable weak cipher suites and NTLM credential hash synchronization](../../active-directory-domain-services/secure-your-domain.md).

1. Azure AD Connect načte veřejný klíč pro instanci klienta služby Azure AD Domain Services.
1. Když uživatel změní své heslo, místní řadič domény uloží výsledek změny hesla (hodnoty hash) do dvou atributů:
    * *unicodePwd* pro hash hesla NTLM.
    * *doplňková pověření* pro hodnotu hash hesla protokolu Kerberos.
1. Azure AD Connect detekuje změny hesel prostřednictvím kanálu replikace adresáře (změny atributů, které je nutné replikovat do jiných řadičů domény).
1. Pro každého uživatele, jehož heslo se změnilo, Azure AD Connect provádí následující kroky:
    * Generuje náhodný 256bitový symetrický klíč AES.
    * Generuje náhodný inicializační vektor potřebný pro první kolo šifrování.
    * Extrahuje hodnoty hash hesla protokolu Kerberos z atributů *supplementalCredentials.*
    * Zkontroluje nastavení konfigurace zabezpečení služby Azure AD Domain Services *SyncNtlmPasswords.*
        * Pokud je toto nastavení zakázáno, vygeneruje náhodnou hash NTLM ntlm s vysokou entropie (odlišnou od hesla uživatele). Tento hash je pak v kombinaci s exacted Kerberos heslo hash z *doplňkuCrendetials* atribut do jedné datové struktury.
        * Pokud je tato možnost povolena, kombinuje hodnotu atributu *unicodePwd* s extrahovanými hodnotami hash hesla kerberos z atributu *supplementalCredentials* do jedné datové struktury.
    * Šifruje jednu datovou strukturu pomocí symetrického klíče AES.
    * Šifruje symetrický klíč AES pomocí veřejného klíče služby Azure AD Domain Services klienta.
1. Azure AD Connect přenáší šifrovaný symetrický klíč AES, šifrovanou datovou strukturu obsahující heslové hashy a inicializační vektor do Azure AD.
1. Azure AD ukládá šifrovaný symetrický klíč AES, šifrovanou datovou strukturu a inicializační vektor pro uživatele.
1. Azure AD tlačí šifrovaný symetrický klíč AES, šifrovanou datovou strukturu a inicializační vektor pomocí mechanismu interní synchronizace přes šifrovanou relaci HTTP do služby Azure AD Domain Services.
1. Služba Azure AD Domain Services načte soukromý klíč pro instanci klienta z trezoru klíčů Azure.
1. Pro každou šifrovanou sadu dat (představující změnu hesla jednoho uživatele) pak služby Azure AD Domain Services provede následující kroky:
    * Používá svůj soukromý klíč k dešifrování symetrického klíče AES.
    * Používá symetrický klíč AES s inicializačním vektorem k dešifrování šifrované datové struktury, která obsahuje zaousávací stavhesla hesla.
    * Zapíše zabezpečení hesla protokolu Kerberos, které obdrží, do řadiče domény služby Azure AD Domain Services. Hodnoty hashe jsou uloženy do atributu *supplementalcredentials* objektu uživatele, který je zašifrován do veřejného klíče řadiče domény služby Azure AD Domain Services.
    * Služba Azure AD Domain Services zapisuje hash hesla NTLM, který přijala, do řadiče domény služby Azure AD Domain Services. Hodnota hash se uloží do atributu *unicodePwd* objektu uživatele, který je zašifrován do veřejného klíče řadiče domény služby Azure AD Domain Services.

## <a name="enable-password-hash-synchronization"></a>Povolení synchronizace hodnoty hash hesel

>[!IMPORTANT]
>Pokud migrujete ze služby AD FS (nebo jiných federačních technologií) do synchronizace hash hesel, důrazně doporučujeme, abyste se řídili podrobným průvodcem nasazením, který byl zveřejněn [zde](https://aka.ms/adfstophsdpdownload).

Při instalaci Služby Azure AD Connect pomocí možnosti **Expresní nastavení** je synchronizace hodnot hash hesel automaticky povolena. Další informace najdete [v tématu Začínáme s Azure AD Connect pomocí expresní nastavení](how-to-connect-install-express.md).

Pokud při instalaci služby Azure AD Connect používáte vlastní nastavení, synchronizace hodnot hash hesel je k dispozici na přihlašovací stránce uživatele. Další informace naleznete [v tématu Vlastní instalace Služby Azure AD Connect](how-to-connect-install-custom.md).

![Povolení synchronizace hodnot hash hesel](./media/how-to-connect-password-hash-synchronization/usersignin2.png)

### <a name="password-hash-synchronization-and-fips"></a>Synchronizace hash hesel a FIPS
Pokud byl váš server uzamčen podle federal information processing standard (FIPS), pak MD5 je zakázán.

**Chcete-li povolit synchronizaci hodnot hash hesel MD5, proveďte následující kroky:**

1. Přejděte na %programfiles%\Azure AD Sync\Bin.
2. Otevřete soubor miiserver.exe.config.
3. Přejděte do uzlu konfigurace/runtime na konci souboru.
4. Přidejte následující uzel:`<enforceFIPSPolicy enabled="false"/>`
5. Uložte provedené změny.

Pro referenci, tento úryvek je to, co by mělo vypadat:

```
    <configuration>
        <runtime>
            <enforceFIPSPolicy enabled="false"/>
        </runtime>
    </configuration>
```

Informace o zabezpečení a FIPS najdete [v tématu Synchronizace hash hash hesla Azure AD, šifrování a dodržování předpisů FIPS](https://blogs.technet.microsoft.com/enterprisemobility/2014/06/28/aad-password-sync-encryption-and-fips-compliance/).

## <a name="troubleshoot-password-hash-synchronization"></a>Poradce při potížích se synchronizací hodnot hash hesel
Pokud máte problémy se synchronizací hodnot hash [hesel, přečtěte si článek Poradce při potížích se synchronizací hodnot hash hesel](tshoot-connect-password-hash-synchronization.md).

## <a name="next-steps"></a>Další kroky
* [Synchronizace azure ad připojení: přizpůsobení možností synchronizace](how-to-connect-sync-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)
* [Získejte podrobný plán nasazení pro migraci z adfs na synchronizaci hash hesel](https://aka.ms/authenticationDeploymentPlan)

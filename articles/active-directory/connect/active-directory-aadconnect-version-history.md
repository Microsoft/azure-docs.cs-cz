---
title: 'Azure AD Connect: Historie verzí | Dokumentace Microsoftu'
description: Tento článek obsahuje seznam všech vydaných verzích služby Azure AD Connect a služby Azure AD Sync
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/31/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: e808d4bf116dcab344308c3dd2aa06c72e0318ba
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39049513"
---
# <a name="azure-ad-connect-version-release-history"></a>Azure AD Connect: Historie vydaných verzí
Tým služby Azure Active Directory (Azure AD) pravidelně aktualizuje s novými funkcemi a funkce služby Azure AD Connect. Ne všechny položky se vztahují na všechny cílové skupiny.


Tento článek je určen, můžete sledovat, které byly vydané verze a pochopit, jaké změny jsou v nejnovější verzi.

V této tabulce je seznam souvisejících tématech:

Téma |  Podrobnosti
--------- | --------- |
Kroky pro upgrade z Azure AD Connect | Různé způsoby [upgrade z předchozí verze na nejnovější verzi](active-directory-aadconnect-upgrade-previous-version.md) verzi služby Azure AD Connect.
Požadovaná oprávnění | Oprávnění potřebná k aktualizaci použít, najdete v části [účtech a oprávněních](./active-directory-aadconnect-accounts-permissions.md#upgrade).

Stáhnout | [Stažení služby Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771).

## <a name="118190"></a>1.1.819.0

### <a name="release-status"></a>Stav verze

5/14/2018: vydala pro automatický upgrade a stažení.

### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení

Nové funkce a vylepšení

- Tato verze obsahuje verzi public preview integrace služby PingFederate ve službě Azure AD Connect. V této verzi zákazníci mohou snadno a spolehlivě nakonfigurovat svoje prostředí Azure Active Directory k využití služby PingFederate jako jejich federačního zprostředkovatele. Další informace o tom, jak použít tuto novou funkci, navštivte prosím náš [online dokumentaci](active-directory-aadconnect-user-signin.md#federation-with-pingfederate). 
- Aktualizovat Azure AD Connect Průvodce Poradce při potížích, kde teď analyzuje Další chyba scénáře, jako je propojená poštovní schránky a dynamické skupiny AD. Další informace o řešení problémů s nástroji [tady](active-directory-aadconnect-troubleshoot-objectsync.md).
- Konfigurace zpětného zápisu zařízení se teď spravuje výhradně v Azure AD Connect průvodce.
- Řešení potíží s nový PowerShell modul volané ADSyncTools.psm1 se přidá, který slouží k řešení problémů s připojením SQL a různé další nástroje. Další informace o modulu ADSyncTools [tady](active-directory-aadconnect-tshoot-sql-connectivity.md). 
- Byla přidána nová dodatečnou úlohu "Nakonfigurovat možnosti zařízení". Úlohu můžete nakonfigurovat následující dvě operace: 
    -   **Připojení k hybridní službě Azure AD**: Pokud má místní prostředí AD nároky na místo a chcete také výhody poskytované službou Azure Active Directory, můžete implementovat hybridních zařízení připojených k Azure AD. Jde o zařízení, která jsou obě, připojené k vaší místní služby Active Directory a Azure Active Directory.
    -   **Zpětný zápis zařízení**: zpětný zápis zařízení se používá k povolení podmíněného přístupu založené na zařízení se službou AD FS (2012 R2 nebo vyšší) chráněný zařízení

   >[!NOTE] 
   > - Možnost povolit zpětný zápis zařízení z přizpůsobit možnosti synchronizace bude zapnutá. 
   > -  V této verzi je zastaralý modul PowerShell nástroje ADPrep.



### <a name="fixed-issues"></a>Opravené problémy 

- Tato verze aktualizuje instalaci systému SQL Server Express k SQL serveru 2012 SP4, které mimo jiné, poskytuje opravy pro několik ohrožení zabezpečení.  Podrobnosti najdete na [tady](https://support.microsoft.com/en-ca/help/4018073/sql-server-2012-service-pack-4-release-information) Další informace o SQL Server 2012 SP4.
- Zpracování pravidla synchronizace: odchozí pravidla synchronizace spojení se žádná podmínka spojení by mělo být zrušit použité, pokud nadřazené pravidlo synchronizace není nadále vhodné
- Několik oprav usnadnění se použily rozhraní Synchronization Service Manager a Editor pravidel synchronizace
- Azure AD Connect průvodce: Chyba při vytváření účtu AD Connector. Pokud Azure AD Connect je v pracovní skupině
- Azure AD Connect průvodce: Na Azure AD přihlašovací stránky zobrazí zaškrtávací políčko ověření vždycky, když je jakákoli Neshoda v AD domén a domén Azure AD ověřeno
- Automatický upgrade prostředí PowerShell opravit a nastavit stav automatické aktualizace v některých případech správně po k pokusu o automatický upgrade.
- Azure AD Connect průvodce: Aktualizovat telemetrie k zachycení dříve chybějící informace
- Azure AD Connect průvodce: Následující změny byly provedeny při použití **změnit přihlášení uživatele** úkolu, chcete-li přepnout ze služby AD FS na předávací ověřování:
    - Předávací ověřování Agent byl nainstalován na serveru služby Azure AD Connect a je povolena funkce předávací ověřování, než jsme převést domény ze Federovaná do služby managed.
    - Uživatelé jsou již převést z Federovaná do spravovaných. Jenom domény budou převedeny.
- Azure AD Connect průvodce: AD FS s více domény Regex není správná po uživatele (UPN) ' speciální znak regulárního výrazu aktualizaci pro podporu speciální znaky
- Azure AD Connect průvodce: Odeberte detekováno falešné "Atribut zdrojového ukotvení konfigurovat" zprávy, když žádné změny 
- Azure AD Connect průvodce: Služba AD FS podporuje pro scénář duální federace
- Azure AD Connect průvodce: Deklarace identity AD FS nejsou aktualizovány pro přidání domény při převodu spravované domény na federovanou
- Azure AD Connect průvodce: Během zjišťování nainstalované balíčky, se nám najít zastaralé Dirsync nebo Azure AD Sync nebo Azure AD Connect souvisejících produktů. Nyní jsme se pokusí odinstalovat zastaralé produkty.
- Azure AD Connect průvodce: Správné chybové zprávy mapování při selhání instalace průchozí ověřovacího agenta služby
- Azure AD Connect průvodce: Odebrání kontejneru "Konfigurace" z filtrování podle organizačních jednotek domény stránky
- Instalace synchronizačního modulu: odeberte nepotřebné starší verze logiku, která někdy se nezdařilo z msi instalace synchronizačního modulu
- Azure AD Connect průvodce: Oprava text nápovědy automaticky otevíraného okna na stránce volitelné funkce synchronizace hodnot Hash hesel
- Synchronizovat modul runtime: oprava scénáři, kde objekt CS má importovaný delete a synchronizační pravidla se pokusí znovu zřídit objektu.
- Synchronizovat modul runtime: nápovědu přidat odkaz na Online připojení Průvodce řešením potíží s do protokolu událostí chybě importu
- Synchronizovat modul runtime: při vytváření výčtu konektory snížení využití paměti Plánovač synchronizace
- Azure AD Connect průvodce: Vyřešit problém řeší vlastní účet synchronizační služby, který nemá žádná oprávnění pro čtení AD
- Azure AD Connect průvodce: Zlepšení protokolování domény a organizační jednotky vybrané možnosti filtrování
- Azure AD Connect průvodce: AD FS přidejte výchozí deklarací na vztah důvěryhodnosti federace vytvořit pro scénář MFA
- Azure AD Connect průvodce: AD FS nasadit WAP: Přidání serveru pomocí nového certifikátu se nezdaří.
- Azure AD Connect průvodce: DSSO výjimka při onPremCredentials nejsou inicializovány pro doménu 
- Přednostně tok distinguishedName atribut AD z objektu aktivního uživatele.
- Oprava kosmetické chyby byly prioritu první synchronizační pravidlo OOB byl nastaven na 99 místo 100



## <a name="117510"></a>1.1.751.0
Stav 4/12/2018: uvolněna pouze ke stažení

>[!NOTE]
>Tato verze je oprava hotfix pro Azure AD Connect

### <a name="azure-ad-connect-sync"></a>Synchronizace služby Azure AD Connect
#### <a name="fixed-issues"></a>Opravené problémy
Opraven problém, byly zjišťování automatické instance Azure pro čas od času došlo k selhání Čína tenantů.  

### <a name="ad-fs-management"></a>Správa služby AD FS
#### <a name="fixed-issues"></a>Opravené problémy

Došlo k potížím s logikou opakování konfigurace, která by mělo za následek ArgumentException s oznámením "položka se stejným klíčem již byla přidána."  To by způsobila selhání všech operací opakování.

## <a name="117500"></a>1.1.750.0
Stav 3 22 2018: vydala pro automatický upgrade a stažení.
>[!NOTE]
>Po dokončení upgradu na tuto novou verzi se automaticky aktivuje úplné synchronizace a úplného importu pro konektor služby Azure AD a úplné synchronizace konektoru služby AD. Protože to může trvat nějakou dobu, v závislosti na velikosti vašeho prostředí Azure AD Connect, ujistěte se, že jste udělali potřebné kroky pro podporu tohoto nebo zdržovat upgradu, dokud jste nalezli pohodlný okamžiku Uděláte to tak.

>[!NOTE]
>"Funkce AutoUpgrade byl nesprávně zakázaná pro některé klienty, kteří později než 1.1.524.0 nasazení sestavení. Aby se zajistilo, že je vaše instance služby Azure AD Connect pro AutoUpgrade i dál nárok, spusťte následující rutinu prostředí PowerShell: "Set ADSyncAutoUpgrade - AutoupGradeState povoleno"


### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Opravené problémy

* Rutina set-ADSyncAutoUpgrade by blokovaly Autoupgrade dříve, pokud automatický upgrade stav je nastavený na pozastaveno. Tato funkce se změnilo tak, že nedochází k blokování AutoUpgrade budoucí sestavení.
* Změnit **přihlášení uživatele** stránce možnost "Synchronizace hesel" do "Synchronizace hodnot Hash hesel".  Azure AD Connect synchronizuje hodnoty hash hesel, a ne hesla, takže ten je v souladu s co se skutečně děje.  Další informace najdete v části [implementace synchronizace hodnot hash hesel pomocí synchronizace Azure AD Connect](active-directory-aadconnectsync-implement-password-hash-synchronization.md)

## <a name="117490"></a>1.1.749.0
Stav: Vyberte zákazníkům obecně dostupné.

>[!NOTE]
>Po dokončení upgradu na tuto novou verzi se automaticky aktivuje úplné synchronizace a úplného importu pro konektor služby Azure AD a úplné synchronizace konektoru služby AD. Protože to může trvat nějakou dobu, v závislosti na velikosti vašeho prostředí Azure AD Connect, ujistěte se prosím, že jste udělali potřebné kroky pro podporu tohoto nebo zdržovat upgradu, dokud jste nalezli pohodlný okamžiku Uděláte to tak.

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Opravené problémy
* Opravte časové okno na úlohy na pozadí pro filtrování oddílů stránku při přechodu na další stránku.

* Je opravená chyba, která způsobila narušení přístupu při ConfigDB vlastní akce.

* Je opravená chyba, provést obnovení při vypršení časového limitu připojení SQL.

* Je opravená chyba, kdy certifikátů se zástupnými znaky SAN selhaly kontrolu požadovaných součástí.

* Je opravená chyba, což způsobí, že miiserver.exe selhání během exportu konektor Azure AD.

* Při spuštění Průvodce Azure AD Connect ke změně konfigurace je opravená chyba protokolována pokus chybných zadání hesla, který byl na řadiči domény.


#### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení

* Přidání nastavení ochrany osobních údajů pro General Data Protection Regulation (GDPR).  Další informace najdete v článku [tady](active-directory-aadconnect-gdpr.md).

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]  

* telemetrie Application - správce můžete přepínat Tato třída dat zapnuto/vypnuto podle

* Azure AD Health dat – Správce musí navštivte portál stavu řídit jejich nastavení stavu.
   Po změně zásad služby se agenti přečte a vynutit.

* Přidání zpětný zápis zařízení akcí konfigurace a indikátor průběhu inicializace stránky.

* Vylepšená Diagnostika obecné sestavu ve formátu HTML a úplná kolekci písmem ZIP / sestavu ve formátu HTML

* Vylepšila se spolehlivost automatický upgrade a přidání další telemetrie k zajištění, že se dá určit stav serveru

* Omezit oprávnění k dispozici k privilegovaným účtům na účet AD Connector.

  * Průvodce pro nové instalace, omezí oprávnění, která privilegované účty na svém účtu MSOL máte po vytvoření účtu služby MSOL.

Změny se postará o následující:
1. Expresní instalace
2. Vlastní instalace pomocí automatické vytvoření účtu
3. Změnit instalační program, takže se nevyžaduje oprávnění správce systému při čisté instalaci služby Azure AD Connect

* Přidat nový nástroj pro řešení potíží se synchronizací pro konkrétní objekt. Je k dispozici v možnost Poradce při potížích s objekt synchronizace Azure AD Connect Průvodce řešení potíží s dodatečnou úlohu. V současné době nástroj kontroluje následující:

  * UserPrincipalName Neshoda mezi objekt synchronizované uživatele a uživatelský účet v Tenantovi Azure AD.
  * Pokud objekt je filtrováno z synchronizace kvůli filtrování domény
  * Pokud objekt je filtrováno z synchronizace, protože organizační jednotka (OU) filtrování

* Přidat nový nástroj pro synchronizaci aktuální hodnoty hash hesla uložená v místním Active Directory pro konkrétní uživatelský účet.

Nástroj nevyžaduje, aby změnu hesla. Je k dispozici v 'Řešení potíží s synchronizaci hodnot Hash hesel' možnost Azure AD Connect Průvodce řešení potíží s dodatečnou úlohu.






## <a name="116540"></a>1.1.654.0
Stav: 12. prosince 2017

>[!NOTE]
>Tato verze je zabezpečení související oprava hotfix pro Azure AD Connect

### <a name="azure-ad-connect"></a>Azure AD Connect
Vylepšení se přidala do služby Azure AD Connect verze 1.1.654.0 (i po) ujistěte se, že změny Doporučená oprávnění je popsáno v části [uzamčení přístupu k účtu služby AD DS](#lock) se automaticky použije, když Azure AD Připojte se vytvoří účet služby AD DS. 

- Při nastavování služby Azure AD Connect, instalace správce můžete zadat existující účet služby AD DS, nebo nechat automaticky vytvořit účet Azure AD Connect. Změny oprávnění provedené se automaticky použijí pro účet služby AD DS, který je vytvořen pomocí služby Azure AD Connect při instalaci. Použijí se existující účet AD DS poskytuje instalace správce.
- Pro zákazníky, kteří upgradovali ze starší verze služby Azure AD Connect k 1.1.654.0 (nebo po) oprávnění změny se nepoužije zpětně existující účty služby AD DS vytvořené před upgradem. Se použije pouze na nové účty služby AD DS vytvořené po upgradu. K tomu dojde, když přidáváte nové doménové struktury AD, které se mají synchronizovat do služby Azure AD.

>[!NOTE]
>Tato verze odebere jenom ohrožení zabezpečení pro nové instalace služby Azure AD Connect, kde účet služby je vytvořena během procesu instalace. Pro existující instalace, nebo v případech, ve kterém můžete zadat účet sami měli byste zajistit, že toto ohrožení zabezpečení neexistuje.

#### <a name="lock"></a> Zamezit přístup k účtu služby AD DS
Uzamčení přístupu k účtu služby AD DS díky implementaci následující změny oprávnění provedené v místní AD:  

*   Zakázat dědičnosti na zadaný objekt
*   Odeberte všechny položky řízení přístupu pro daný objekt, s výjimkou ACE konkrétní sama na sebe. Chcete zachovat beze změny výchozích oprávnění, pokud jde o sama na sebe.
*   Přiřadíte tato konkrétní oprávnění:

Typ     | Název                          | Access               | Platí pro
---------|-------------------------------|----------------------|--------------|
Povolit    | SYSTÉM                        | Úplné řízení         | Tento objekt  |
Povolit    | Enterprise Admins             | Úplné řízení         | Tento objekt  |
Povolit    | Domain Admins                 | Úplné řízení         | Tento objekt  |
Povolit    | Správci                | Úplné řízení         | Tento objekt  |
Povolit    | Enterprise Domain Controllers | Seznam obsahu        | Tento objekt  |
Povolit    | Enterprise Domain Controllers | Číst všechny vlastnosti  | Tento objekt  |
Povolit    | Enterprise Domain Controllers | Oprávnění ke čtení     | Tento objekt  |
Povolit    | Ověření uživatelé           | Seznam obsahu        | Tento objekt  |
Povolit    | Ověření uživatelé           | Číst všechny vlastnosti  | Tento objekt  |
Povolit    | Ověření uživatelé           | Oprávnění ke čtení     | Tento objekt  |

Chcete-li posílit nastavení pro účet služby AD DS, můžete spustit [tento skript Powershellu](https://gallery.technet.microsoft.com/Prepare-Active-Directory-ef20d978). Skript prostředí PowerShell přiřadí oprávnění uvedená výše pro účet služby AD DS.

#### <a name="powershell-script-to-tighten-a-pre-existing-service-account"></a>Skript Powershellu pro už existující účet služby, zkracují

Chcete-li použít tato nastavení pro existující účet služby AD DS pomocí skriptu prostředí PowerShell, (ether, poskytnuté vaší organizací nebo vytvořené předchozí instalace služby Azure AD Connect, stáhněte si prosím skript z výše uvedeným odkazem.

##### <a name="usage"></a>Použití:

```powershell
Set-ADSyncRestrictedPermissions -ObjectDN <$ObjectDN> -Credential <$Credential>
```

Kde 

**$ObjectDN** = účet služby Active Directory, jehož oprávnění musí být zvýšit.

**$Credential** = přihlašovací údaje správce, který má dostatečná oprávnění k omezení oprávnění k účtu $ObjectDN. Tato oprávnění jsou obvykle uloženy správce rozlehlé sítě nebo domény. Aby se zabránilo chybám vyhledávání účtu použijte plně kvalifikovaný název domény účtu správce. Příklad: contoso.com\admin.

>[!NOTE] 
>$credential. Uživatelské jméno musí být ve formátu FQDN\username. Příklad: contoso.com\admin 

##### <a name="example"></a>Příklad:

```powershell
Set-ADSyncRestrictedPermissions -ObjectDN "CN=TestAccount1,CN=Users,DC=bvtadwbackdc,DC=com" -Credential $credential 
```
### <a name="was-this-vulnerability-used-to-gain-unauthorized-access"></a>Bylo toto ohrožení zabezpečení použít k získání neoprávněného přístupu?

Pokud chcete zobrazit, pokud toto ohrožení zabezpečení byla použita prolomit služby Azure AD Connect konfigurace byste měli ověřit poslední heslo resetovat datum účtu služby.  Pokud se časové razítko v neočekávané, o pomoc prostřednictvím protokolu událostí mělo být provedeno pro toto heslo resetovat události,.

Další informace najdete v tématu [Microsoft Security Advisory 4056318](https://technet.microsoft.com/library/security/4056318)

## <a name="116490"></a>1.1.649.0
Stav: Říjen 2017 27

>[!NOTE]
>Toto sestavení není k dispozici zákazníkům prostřednictvím funkce Azure AD Connect automatický Upgrade.

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issue"></a>Oprava potíží
* Opravili jsme potíže s kompatibilitou verze mezi Azure AD Connect a agenta Azure AD Connect Health (pro synchronizaci). Tento problém má vliv na zákazníky, kteří působí Azure AD Connect místní upgrade na verzi 1.1.647.0, ale má v současné době verze 3.0.127.0 stavu agenta. Po upgradu agenta stavu už odesílání dat o službu Azure AD Connect synchronizaci stavu do služby Azure AD Health. S touto opravou během místní upgrade služby Azure AD Connect nainstaluje agenta služby Health verze 3.0.129.0. Agent stavu verze 3.0.129.0 nemá potíže s kompatibilitou s Azure AD Connect verze 1.1.649.0.


## <a name="116470"></a>1.1.647.0
Stav: Říjen 2017 19

> [!IMPORTANT]
> Nastane problém s kompatibilitou mezi Azure AD Connect verze 1.1.647.0 a verze agenta Azure AD Connect Health (pro synchronizaci) 3.0.127.0. Tento problém brání agenta stavu z odesílání dat o stavu týkající se Azure AD Connect synchronizace služby (včetně chyb synchronizace objektů a dat historie spouštění) do služby Azure AD Health. Před nasazením služby Azure AD Connect ručně upgradovat na verzi 1.1.647.0, ověřte, že nainstalovaná aktuální verze agenta Azure AD Connect Health na vašem serveru Azure AD Connect. To lze provést tak, že přejdete do *ovládací panely → Přidat nebo odebrat programy* a vyhledejte aplikaci *Microsoft Agent Azure AD Connect Health pro synchronizaci*. Pokud je jeho verze 3.0.127.0, se doporučuje čekat na další verzi služby Azure AD Connect bude k dispozici před upgradem. Není-li verzi agenta služby Health 3.0.127.0, je v pořádku, pokračujte v manuální, místní upgrade. Všimněte si, že tento problém nemá vliv na swing upgrade nebo zákazníci, kteří jsou provedení nové instalace služby Azure AD Connect.
>
>

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Opravené problémy
* Opravili jsme problém s *změnit přihlášení uživatele* úkol v průvodce službou Azure AD Connect:

  * Tento problém nastane, pokud máte existující nasazení služby Azure AD Connect se synchronizací hesla **povolené**, a pokoušíte se nastavit uživatele přihlásit metody jako *předávací ověřování*. Předtím, než se tato změna uplatní, Průvodce nesprávně ukazuje "*zakázat synchronizaci hesel*" řádku. Synchronizace hesel však zůstane povolena po použití této změny. S touto opravou Průvodce už nebude zobrazovat na řádku.

  * Návrh průvodce nezakáže synchronizace hesel při aktualizaci metodu přihlašování uživatele s využitím *změnit přihlášení uživatele* úloh. Toto je nedošlo k přerušení pro zákazníky, kteří chtějí zachovat synchronizace hesel, i když povolit předávací ověřování nebo federace jako způsob jejich přihlášení primárního uživatele.
  
  * Pokud chcete zakázat po aktualizaci metodu přihlašování uživatele synchronizace hesel, je třeba spustit *přizpůsobení konfigurace synchronizace* úlohy v průvodci. Když přejdete na *volitelné funkce* stránce, zrušte zaškrtnutí políčka *synchronizace hesel* možnost.
  
  * Všimněte si, že stejný problém také v případě pokusu o povolení/zakázání bezproblémového jednotného přihlašování. Konkrétně se synchronizací hesla povolené máte existující nasazení služby Azure AD Connect a metodu přihlašování uživatele je už nakonfigurovaný jako *předávací ověřování*. Použití *změnit přihlášení uživatele* úkolu, pokusíte zaškrtnout/zrušit zaškrtnutí *povolte bezproblémové jednotné přihlašování* možnost při metodu přihlašování uživatele bude nakonfigurována jako "Předávací ověřování". Předtím, než se tato změna uplatní, Průvodce nesprávně ukazuje "*zakázat synchronizaci hesel*" řádku. Synchronizace hesel však zůstane povolena po použití této změny. S touto opravou Průvodce už nebude zobrazovat na řádku.

* Opravili jsme problém s *změnit přihlášení uživatele* úkol v průvodce službou Azure AD Connect:

   * Tento problém nastane, pokud máte existující nasazení služby Azure AD Connect se synchronizací hesla **zakázané**, a pokoušíte se nastavit uživatele přihlásit metody jako *předávací ověřování*. Při použití této změny, Průvodce povolí předávací ověřování a synchronizace hesel. S touto opravou již Průvodce povolí synchronizaci hesel.

  * Synchronizace hesel dřív předpoklad pro povolení předávacího ověřování. Pokud nastavíte uživatele přihlásit metody jako *předávací ověřování*, průvodce by povolit předávací ověřování a synchronizace hesel. Synchronizace hesel se nedávno, se odebral jako předpoklad. Jako součást služby Azure AD Connect verze 1.1.557.0 byla provedena změna na Azure AD Connect není povolení synchronizace hesel při nastavení uživatele přihlásit metody jako *předávací ověřování*. Tato změna se však jedině pro instalace služby Azure AD Connect. S touto opravou stejnou změnu platí také pro *změnit přihlášení uživatele* úloh.
  
  * Všimněte si, že stejný problém také v případě pokusu o povolení/zakázání bezproblémového jednotného přihlašování. Konkrétně se synchronizací hesla zakázané máte existující nasazení služby Azure AD Connect a metodu přihlašování uživatele je už nakonfigurovaný jako *předávací ověřování*. Použití *změnit přihlášení uživatele* úkolu, pokusíte zaškrtnout/zrušit zaškrtnutí *povolte bezproblémové jednotné přihlašování* možnost při metodu přihlašování uživatele bude nakonfigurována jako "Předávací ověřování". Při použití této změny, Průvodce povolí synchronizaci hesel. S touto opravou již Průvodce povolí synchronizaci hesel. 

* Opravili jsme problém, který způsobil selhání upgradu služby Azure AD Connect s chybou "*nelze upgradovat synchronizační služba*". Dále, nedokážou nadále spouštět službu synchronizace s chybou událostí "*služba nemohla spustit, protože verze databáze je novější než verze nainstalovaná binárních souborů*". Tento problém nastane, pokud správce, provádí se upgrade nemá oprávnění správce systému SQL Server, který se používá v Azure AD Connect. S touto opravou Azure AD Connect vyžaduje pouze správci mají oprávnění db_owner databáze ADSync během upgradu.

* Opravili jsme problém, upgradujte Azure AD Connect, která měla vliv na zákazníky, kteří mají povolené [bezproblémové jednotné přihlašování](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). Po dokončení upgradu služby Azure AD Connect bezproblémové jednotné přihlašování se nesprávně zobrazí jako zakázané v průvodce službou Azure AD Connect, i když funkce zůstala povolená a plně funkční. S touto opravou tato funkce se teď zobrazí správně povolené v průvodci.

* Opravili jsme problém, který způsobil Průvodce Azure AD Connect chcete vždy zobrazit "*konfigurovat zdrojové ukotvení*" na příkazový řádek *připravení konfigurovat* stránce, i v případě, že nebyly provedeny žádné změny související s zdrojové ukotvení.

* Když provádíte ruční místní upgrade služby Azure AD Connect, je potřeba zadat přihlašovací údaje globálního správce tenanta Azure AD odpovídající zákazníka. Dříve, může upgrade pokračovat i v případě, že přihlašovací údaje globálního správce patřil do jiné Azure AD tenanta. Při upgradu se zobrazí úspěšné dokončení, některé konfigurace nejsou správně zachované v upgradu. Díky této změně Průvodce zabrání upgradu budete pokračovat, pokud zadané přihlašovací údaje se neshodují s tenantem Azure AD.

* Odebrat redundantní logiku, která zbytečně restartování služby Azure AD Connect Health na začátku upgradu na ruční.


#### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení
* Přidání logiky pro zjednodušení kroky potřebné k nastavení Azure AD Connect pomocí služby Microsoft Germany Cloud. Dříve je nutné provést aktualizaci určité klíče registru na serveru služby Azure AD Connect, aby se správně pracovat s Microsoft Germany Cloud, jak je popsáno v tomto článku. Nyní Azure AD Connect a dokáže automaticky detekovat Pokud váš tenant v Microsoft Germany Cloud vychází přihlašovací údaje globálního správce, které jsou k dispozici během instalace.

### <a name="azure-ad-connect-sync"></a>Synchronizace služby Azure AD Connect
>[!NOTE]
> Poznámka: Synchronizační služby je rozhraní WMI, které vám umožní vyvíjet vlastní vlastní plánovač. Toto rozhraní je nyní zastaralá a bude odebrána z budoucí verze služby Azure AD Connect odeslaná po 30. června 2018. Zákazníci, kteří chtějí přizpůsobit plán synchronizace by měla použít [integrované plánovače (https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-scheduler).

#### <a name="fixed-issues"></a>Opravené problémy
* Když průvodce Azure AD Connect vytvoří účet AD Connector vyžaduje k synchronizaci změn z místní služby Active Directory, nepřiřazuje správně účet potřebná oprávnění k čtení PublicFolder objektů. Tento problém se týká Expresní instalace a vlastní instalace. Tato změna opravuje problém.

* Opravili jsme problém, který způsobil Poradce při potížích stránku průvodce Azure AD Connect nevykreslí správně pro správce spouštění z Windows serveru 2016.

#### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení
* Při řešení potíží s synchronizace hesel pomocí Průvodce Azure AD Connect stránka o řešení problémů, řešení problémů s stránka nyní vrátí stav specifického pro doménu.

* Dříve, pokud chcete povolit synchronizaci hodnot Hash hesel, Azure AD Connect neověřuje, jestli má účet AD Connector. požadovaná oprávnění k synchronizaci hodnot hash hesel z místní služby AD. Nyní Průvodce Azure AD Connect ověří a varovat, pokud účet AD Connector nemá dostatečná oprávnění.

### <a name="ad-fs-management"></a>Správa služby AD FS
#### <a name="fixed-issue"></a>Oprava potíží
* Opravili jsme problém související s používáním [msDS-ConsistencyGuid jako zdrojové ukotvení](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-msds-consistencyguid-as-sourceanchor) funkce. Tento problém má vliv na zákazníky, kteří nakonfigurovali *federace se službou AD FS* jako metodu přihlašování uživatele. Při spuštění *konfigurovat zdrojové ukotvení* úlohy v průvodci, Azure AD Connect se přepne do pomocí * ms-DS-ConsistencyGuid jako zdrojový atribut pro immutableId. V rámci této změny Azure AD Connect se pokusí aktualizovat pravidla deklarace identity ImmutableId ve službě AD FS. Nicméně tento krok se nezdařil, protože Azure AD Connect nejsou přihlašovací údaje správce, které jsou potřeba ke konfiguraci služby AD FS. S touto opravou Azure AD Connect nyní zobrazí výzvu k zadání přihlašovacích údajů správce pro službu AD FS při spuštění *konfigurovat zdrojové ukotvení* úloh.



## <a name="116140"></a>1.1.614.0
Stav:. Září 2017 05

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="known-issues"></a>Známé problémy
* Existuje známý problém, který je příčinou selhání upgradu služby Azure AD Connect s chybou "*nelze upgradovat synchronizační služba*". Dále, nedokážou nadále spouštět službu synchronizace s chybou událostí "*služba nemohla spustit, protože verze databáze je novější než verze nainstalovaná binárních souborů*". Tento problém nastane, pokud správce, provádí se upgrade nemá oprávnění správce systému SQL Server, který se používá v Azure AD Connect. Nejsou dostatečná oprávnění dbo.

* Existuje známý problém s Azure AD Connect Upgrade, který ovlivňuje zákazníky, kteří mají povolené [bezproblémové jednotné přihlašování](active-directory-aadconnect-sso.md). Po upgradu služby Azure AD Connect, tato funkce se zobrazí jako zakázané v průvodci, i v případě, funkce zůstala povolená. Oprava pro tento problém, poskytneme vám v budoucích verzí. Zákazníci, kteří máte obavy o tento problém se zobrazením ručně ho můžou opravit tím, že umožňuje bezproblémové jednotné přihlašování v průvodci.

#### <a name="fixed-issues"></a>Opravené problémy
* Opravili jsme problém, která zabránila Azure AD Connect aktualizuje pravidla deklarací identity v místní služby AD FS při povolování [msDS-ConsistencyGuid jako zdrojové ukotvení](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-msds-consistencyguid-as-sourceanchor) funkce. Pokud se pokusíte k povolení této funkce pro existující nasazení služby Azure AD Connect, který má služba AD FS nakonfigurovaný jako metodu přihlašování dojde k problému. K problému dochází, protože průvodce výzvy k zadání přihlašovacích údajů služby AD FS před pokusem o aktualizovat pravidla deklarace identity ve službě AD FS.
* Opravili jsme problém, který způsobil selhání instalace, pokud Azure AD Connect místní doménové struktuře Active Directory je zakázaný protokol NTLM. Je problém způsobený Průvodce Azure AD Connect, neposkytují úplné přihlašovací údaje při vytváření kontextu zabezpečení potřebné pro ověřování protokolem Kerberos. To způsobí, že průvodce Azure AD Connect, aby pomocí protokolu NTLM a ověřování protokolem Kerberos nezdaří.

### <a name="azure-ad-connect-sync"></a>Synchronizace služby Azure AD Connect
#### <a name="fixed-issues"></a>Opravené problémy
* Opravili jsme problém, kdy nové pravidlo synchronizace nelze vytvořit Pokud se nezadají atribut značky.
* Opravili jsme problém, který způsobil Azure AD Connect pro připojení k místní služby AD pro synchronizaci hesel pomocí protokolu NTLM, i když je k dispozici protokol Kerberos. K tomuto problému dochází, pokud v místním topologie AD má jeden nebo více řadičů domény, které byla obnovena ze zálohy.
* Opravili jsme problém, který způsobil postup úplnou synchronizaci zbytečně vzniknout po upgradu. Obecně platí spuštěna úplná synchronizace kroky vyžádáním po upgradu při změny out-of-box synchronizační pravidla. Tento problém byl z důvodu chyby v logice detekce změn, nesprávně se zjistila při zjištění Výraz pravidla synchronizace s znaky nového řádku. Znaky nového řádku jsou vloženy do Výraz pravidla synchronizace pro lepší čitelnost.
* Opravili jsme problém, který může způsobit, že server Azure AD Connect není správně fungovat po automatický Upgrade. Tento problém se týká servery Azure AD Connect verze 1.1.443.0 (nebo starší). Podrobnosti o problému, najdete v článku [Azure AD Connect, ale nefunguje správně po automatický upgrade](https://support.microsoft.com/help/4038479/azure-ad-connect-is-not-working-correctly-after-an-automatic-upgrade).
* Opravili jsme problém, který může způsobit automatický Upgrade na opakovat každých 5 minut, pokud nedojde k chybám. Oprava automatický Upgrade opakování s exponenciální regresní při výskytu chyby.
* Opravili jsme problém, kde je událost synchronizace hesla 611 nesprávně uvedené v protokolu událostí Windows jako **informační** místo **chyba**. Událost 611 je generována pokaždé, když se synchronizace hesel narazí problém. 
* Opravili jsme problém v Průvodci Azure AD Connect, který umožňuje funkci zpětného zápisu skupina aby byl povolen bez výběru s organizační Jednotkou, vyžaduje se pro zpětný zápis skupin.

#### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení
* Přidat úlohy řešení potíží do Průvodce Azure AD Connect v rámci další úlohy. Zákazníci můžou využít této úlohy můžete řešit problémy spojené s synchronizace hesel a shromažďování diagnostiky Obecné. V budoucnu úlohy řešení potíží prodlouží se mají zahrnout další problémy týkající se synchronizace adresáře.
* Azure AD Connect teď podporuje nový režim instalace volá **použít existující databázi**. Tento režim instalace umožňuje uživatelům instalovat služby Azure AD Connect, která určuje existující databáze ADSync. Další informace o této funkci najdete v článku [použít stávající databázi](active-directory-aadconnect-existing-database.md).
* Pro důkladnější zabezpečení Azure AD Connect teď ve výchozím nastavení používá TLS1.2 pro připojení k Azure AD pro synchronizaci adresářů. Výchozí hodnota dřív protokol TLS 1.0.
* Při spuštění agenta Azure AD Connect heslo synchronizace, pokusí se připojit k dobře známé koncového bodu Azure AD pro synchronizaci hesel. Po úspěšném připojení je přesměrován na koncový bod specifický pro oblast. Agent synchronizace hesla dříve, mezipaměti koncový bod specifický pro oblast, až po restartování. Agenta teď vymaže mezipaměti a opakování pomocí dobře známých koncový bod, pokud zjistí potíže s připojením ke koncovému bodu oblast. Tato změna zajistí, že synchronizace hesel můžete převzetí služeb při selhání na jiný koncový bod specifický pro oblast, když už není k dispozici v mezipaměti koncový bod specifický pro oblast.
* K synchronizaci změn z místní doménové struktury AD se vyžaduje účet služby AD DS. Můžete buď (i) vytvořit služby AD DS sami účet a zadejte své přihlašovací údaje k Azure AD Connect, nebo (ii) zadejte přihlašovací údaje podnikového správce a umožní vytvořit účet služby AD DS za vás Azure AD Connect. (I) dříve, je výchozí možnost v průvodci službou Azure AD Connect. Nyní (ii) je výchozí možnost.

### <a name="azure-ad-connect-health"></a>Azure AD Connect Health

#### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení
* Přidání podpory pro Microsoft Azure Government Cloud a Microsoft Cloud Germany.

### <a name="ad-fs-management"></a>Správa služby AD FS
#### <a name="fixed-issues"></a>Opravené problémy
* Inicializace ADSyncNGCKeysWriteBack rutiny v modulu AD přípravu prostředí powershell byl nesprávně seznamy ACL pro kontejner registrace zařízení a by proto dědit jedině existující oprávnění.  To byla aktualizována tak, aby měl účet synchronizační služby správná oprávnění.

#### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení
* Úkol AAD Connect přihlášení služby AD FS ověřte byla aktualizována tak, aby ověřuje přihlašovací údaje pro Microsoft Online a načtení jenom token ze služby AD FS.
* Když vytváříte novou farmu služby AD FS pomocí AAD Connect, byl přesunut na stránce s žádostí o přihlašovací údaje služby AD FS tak, aby se nyní vyskytuje před uživateli se zobrazí výzva, zajistit servery služby AD FS a WAP.  Díky tomu AAD Connect ke kontrole, že zadaný účet má správná oprávnění.
* Během upgradu AAD Connect jsme už neselže upgrade Pokud vztah důvěryhodnosti služby AD FS AAD se nepodařilo aktualizovat.  Pokud k tomu dojde, uživateli se zobrazí odpovídající zprávu upozornění a pokračovat na resetovat vztah důvěryhodnosti pomocí AAD Connect dodatečnou úlohu.

### <a name="seamless-single-sign-on"></a>Bezproblémové jednotné přihlašování
#### <a name="fixed-issues"></a>Opravené problémy
* Opravili jsme problém, který způsobil Průvodce Azure AD Connect a vrátí chybu, pokud se pokusíte povolit [bezproblémové jednotné přihlašování](active-directory-aadconnect-sso.md). Chybová zpráva *"Konfigurace Microsoft Azure AD Connect ověřování agenta se nezdařila."* Tento problém se týká stávající zákazníci, kteří ručně upgradovali agenty ověřování pro ve verzi preview [předávací ověřování](active-directory-aadconnect-sso.md) podle kroků popsaných v tomto [článku](active-directory-aadconnect-pass-through-authentication-upgrade-preview-authentication-agents.md).


## <a name="115610"></a>1.1.561.0
Stav:. Července 2017 23

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Oprava potíží

* Opravili jsme problém, který způsobil out-of-box synchronizační pravidlo "Mimo AD - ImmutableId uživatele" Odebrat:

  * K tomuto problému dochází při upgradu služby Azure AD Connect, nebo když možnost úloh *aktualizujte konfiguraci synchronizace* v Azure AD Connect se používá Průvodce se aktualizovat konfiguraci synchronizace Azure AD Connect.
  
  * Toto synchronizační pravidlo se vztahuje na zákazníky, kteří mají povolené [msDS-ConsistencyGuid jako zdrojové ukotvení funkce](active-directory-aadconnect-design-concepts.md#using-msds-consistencyguid-as-sourceanchor). Tato funkce byla zavedena v verze 1.1.524.0 a po. Pokud je odebráno synchronizační pravidlo, Azure AD Connect můžete naplnit už v místním AD ms-DS-ConsistencyGuid atributu s hodnotou atribut ObjectGuid. Nezabraňuje noví uživatelé z zřizuje do služby Azure AD.
  
  * Oprava zajistí, že synchronizační pravidlo už se odebere během upgradu nebo během změny konfigurace, za předpokladu, že je povolena funkce. Stávající zákazníci, kteří postižené tímto problémem opravy také zajišťuje, že synchronizační pravidlo je přidána zpět po upgradu na tuto verzi služby Azure AD Connect.

* Opravili jsme problém, který způsobí, že out-of-box pravidel tak, aby mají přednost před hodnotu, která je menší než 100:

  * Obecně přednost před hodnoty 0 - 99 jsou vyhrazené pro vlastní synchronizační pravidla. Během upgradu jsou hodnoty priority pro out-of-box synchronizační pravidla aktualizovány tak, aby vyhovovaly změny pravidel synchronizace. Kvůli tomuto problému může být out-of-box synchronizační pravidla přiřazena hodnota priority, která je menší než 100.
  
  * Oprava zabraňuje problém vyskytovat se během upgradu. Ale neobnoví hodnoty priority pro stávající zákazníky, kteří byly ovlivněny problém. Samostatné opravy, poskytneme vám v budoucnosti usnadňující obnovení.

* Opravili jsme problém, kde [domény a filtrování podle organizačních jednotek obrazovky](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) v Azure AD Connect se zobrazuje průvodce *synchronizovat všechny domény a organizační jednotky* možnost jako vybrané, i když je povolené filtrování podle organizačních jednotek.

*   Opravili jsme problém, který způsobil [obrazovku konfigurace oddílů adresářů](active-directory-aadconnectsync-configure-filtering.md#organizational-unitbased-filtering) v Synchronization Service Manager pro vrácení chyby, pokud *aktualizovat* po kliknutí na tlačítko. Chybová zpráva *"došlo k chybě při aktualizaci domény: nelze přetypovat objekt typu"System.Collections.ArrayList' na typ ' Microsoft.DirectoryServices.MetadirectoryServices.UI.PropertySheetBase.MaPropertyPages.PartitionObject."* Při nové domény AD byl přidán do existující doménové struktury AD a pokoušíte se aktualizace služby Azure AD Connect pomocí tlačítka pro aktualizaci dojde k chybě.

#### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení

* [Funkce automatického upgradu](active-directory-aadconnect-feature-automatic-upgrade.md) i na podporu pro zákazníky s následující konfigurací:
  * Povolíte funkci zpětného zápisu zařízení.
  * Povolili jste funkce zpětný zápis skupin.
  * Instalaci se nepovedlo Expresní nastavení nebo upgradu nástroje DirSync.
  * Máte více než 100 000 objektů v úložišti metaverse.
  * Připojujete se k více než jednu doménovou strukturu. Expresní instalace pouze připojí k jedné doménové struktuře.
  * Účet AD Connector. už není výchozí MSOL_ účet.
  * Serveru je nastavena na byl v pracovní režimu.
  * Povolili jste funkce zpětný zápis uživatelů.
  
  >[!NOTE]
  >Rozšíření oboru funkce automatického upgradu má vliv na zákazníky s využitím Azure AD Connect sestavení 1.1.105.0 a po. Pokud nechcete, aby se váš server Azure AD Connect automaticky upgradovat, musíte spustit následující rutinu na serveru služby Azure AD Connect: `Set-ADSyncAutoUpgrade -AutoUpgradeState disabled`. Další informace o povolení nebo zákaz automatického upgradu, najdete v článku [Azure AD Connect: automatický upgrade](active-directory-aadconnect-feature-automatic-upgrade.md).

## <a name="115580"></a>1.1.558.0
Stav: Nebudou vydány. Změny v tomto sestavení jsou součástí verze 1.1.561.0.

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Oprava potíží

* Opravili jsme problém, který způsobil out-of-box synchronizační pravidlo "Mimo AD - ImmutableId uživatele" odeberou, když dojde k aktualizaci filtrování konfiguraci na základě organizační jednotky. Je třeba použít toto synchronizační pravidlo [msDS-ConsistencyGuid jako zdrojové ukotvení funkce](active-directory-aadconnect-design-concepts.md#using-msds-consistencyguid-as-sourceanchor).

* Opravili jsme problém, kde [domény a filtrování podle organizačních jednotek obrazovky](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) v Azure AD Connect se zobrazuje průvodce *synchronizovat všechny domény a organizační jednotky* možnost jako vybrané, i když je povolené filtrování podle organizačních jednotek.

*   Opravili jsme problém, který způsobil [obrazovku konfigurace oddílů adresářů](active-directory-aadconnectsync-configure-filtering.md#organizational-unitbased-filtering) v Synchronization Service Manager pro vrácení chyby, pokud *aktualizovat* po kliknutí na tlačítko. Chybová zpráva *"došlo k chybě při aktualizaci domény: nelze přetypovat objekt typu"System.Collections.ArrayList' na typ ' Microsoft.DirectoryServices.MetadirectoryServices.UI.PropertySheetBase.MaPropertyPages.PartitionObject."* Při nové domény AD byl přidán do existující doménové struktury AD a pokoušíte se aktualizace služby Azure AD Connect pomocí tlačítka pro aktualizaci dojde k chybě.

#### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení

* [Funkce automatického upgradu](active-directory-aadconnect-feature-automatic-upgrade.md) i na podporu pro zákazníky s následující konfigurací:
  * Povolíte funkci zpětného zápisu zařízení.
  * Povolili jste funkce zpětný zápis skupin.
  * Instalaci se nepovedlo Expresní nastavení nebo upgradu nástroje DirSync.
  * Máte více než 100 000 objektů v úložišti metaverse.
  * Připojujete se k více než jednu doménovou strukturu. Expresní instalace pouze připojí k jedné doménové struktuře.
  * Účet AD Connector. už není výchozí MSOL_ účet.
  * Serveru je nastavena na byl v pracovní režimu.
  * Povolili jste funkce zpětný zápis uživatelů.
  
  >[!NOTE]
  >Rozšíření oboru funkce automatického upgradu má vliv na zákazníky s využitím Azure AD Connect sestavení 1.1.105.0 a po. Pokud nechcete, aby se váš server Azure AD Connect automaticky upgradovat, musíte spustit následující rutinu na serveru služby Azure AD Connect: `Set-ADSyncAutoUpgrade -AutoUpgradeState disabled`. Další informace o povolení nebo zákaz automatického upgradu, najdete v článku [Azure AD Connect: automatický upgrade](active-directory-aadconnect-feature-automatic-upgrade.md).

## <a name="115570"></a>1.1.557.0
Stav: Červenec 2017

>[!NOTE]
>Toto sestavení není k dispozici zákazníkům prostřednictvím funkce Azure AD Connect automatický Upgrade.

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Oprava potíží
* Opravili jsme problém s ADSyncDomainJoinedComputerSync inicializovat rutinu, která způsobila ověřené domény nakonfigurované na existující objekt bodu připojení služby změnit i v případě, že je stále platná doména. Tento problém nastane, pokud váš tenant Azure AD má více než jeden ověřených domén, které lze použít ke konfiguraci spojovacího bodu služby.

#### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení
* Zpětný zápis hesla je teď dostupná ve verzi preview s Microsoft Azure Government cloud a Microsoft Cloud Germany. Další informace o podpoře služby Azure AD Connect pro instance různé služby, najdete v článku [Azure AD Connect: speciální aspekty pro instance](active-directory-aadconnect-instances.md).

* Rutina inicializace ADSyncDomainJoinedComputerSync teď má nový volitelný parametr s názvem AzureADDomain. Tento parametr umožňuje určit, které ověřené domény má být použit pro konfiguraci spojovacího bodu služby.

### <a name="pass-through-authentication"></a>Předávací ověřování

#### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení
* Název agenta požadované pro předávací ověřování se změnil z *Microsoft Azure AD Application Proxy Connector* k *agenta služby Microsoft Azure AD Connect ověřování*.

* Povoluje se předávací ověřování už umožňuje synchronizaci hodnot Hash hesel ve výchozím nastavení.


## <a name="115530"></a>1.1.553.0
Stav: Červen 2017

> [!IMPORTANT]
> Existují schématu a synchronizační pravidlo změny zavedené v tomto sestavení. Služba Azure AD Connect synchronizaci aktivují úplný Import a úplnou synchronizaci kroků po upgradu. Podrobnosti sady změn jsou popsané níže. Chcete-li dočasně odložit úplný Import a úplnou synchronizaci kroků po upgradu, najdete v článku [jak odložit úplnou synchronizaci po upgradu](active-directory-aadconnect-upgrade-previous-version.md#how-to-defer-full-synchronization-after-upgrade).
>
>

### <a name="azure-ad-connect-sync"></a>Synchronizace služby Azure AD Connect

#### <a name="known-issue"></a>Známý problém
* Dochází k nějakému problému, který má vliv na zákazníky, kteří používají [filtrování podle organizační jednotky](active-directory-aadconnectsync-configure-filtering.md#organizational-unitbased-filtering) pomocí synchronizace Azure AD Connect. Když přejdete [stránku domény a filtrování podle organizačních jednotek](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) Průvodce Azure AD Connect, očekává se toto chování:
  * Pokud je povolené filtrování podle organizační jednotky, **synchronizovat vybrané domény a organizační jednotky** je vybraná možnost.
  * V opačném případě **synchronizovat všechny domény a organizační jednotky** je vybraná možnost.

Problém, který nastane je, že **synchronizovat všechny domény a organizační jednotky možnost** je vždycky vybraná při spuštění průvodce.  K tomu dojde i v případě, že filtrování podle organizační jednotky dříve nakonfigurované. Před uložením změny konfigurace službou AAD Connect, ujistěte se, že **synchronizovat vybrané domény a organizační jednotky výběru** a potvrďte, že jsou všechny organizační jednotky, které je třeba synchronizovat znovu povolené. V opačném případě filtrování podle organizačních jednotek se deaktivuje.

#### <a name="fixed-issues"></a>Opravené problémy

* Oprava potíží se zpětným zápisem hesla, která umožňuje resetovat heslo místního správce Azure AD AD privilegovaný uživatelský účet. Tento problém nastane, pokud Azure AD Connect je oprávnění k resetování hesla přes privilegovaného účtu. Problém se zákazníky a vyřešené v této verzi služby Azure AD Connect tím, že správce Azure AD k resetování hesla s libovolnou místní AD privilegovaný uživatelský účet, pokud je vlastník tohoto účtu správce. Další informace najdete v [Security Advisory 4033453](https://technet.microsoft.com/library/security/4033453).

* Opravili jsme problém související s [msDS-ConsistencyGuid jako zdrojové ukotvení](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-msds-consistencyguid-as-sourceanchor) funkce, kam Azure AD Connect není zpětný zápis do místní atribut msDS-ConsistencyGuid AD. Tento problém nastane, pokud existují několika místních doménových struktur AD do Azure AD Connect a *existovat identit uživatelů napříč více adresářů možnost* je vybrána. Když tato konfigurace se použije, výsledná synchronizační pravidla není naplnění atributu sourceAnchorBinary v úložišti Metaverse. Atribut sourceAnchorBinary slouží jako zdrojový atribut pro atribut msDS-ConsistencyGuid. Zpětný zápis na atribut ms-DSConsistencyGuid v důsledku toho nebude fungovat. Pokud chcete problém vyřešit, byly aktualizovány následující pravidla synchronizace k zajištění, že je vždy naplněný atribut sourceAnchorBinary v úložišti Metaverse:
  * V ze služby AD - InetOrgPerson AccountEnabled.xml
  * V ze služby AD - InetOrgPerson Common.xml
  * V ze služby AD - AccountEnabled.xml uživatele
  * V ze služby AD - Common.xml uživatele
  * V ze služby AD - uživatel připojit SOAInAAD.xml

* Dříve i v případě, [msDS-ConsistencyGuid jako zdrojové ukotvení](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-msds-consistencyguid-as-sourceanchor) funkce není povolena, synchronizační pravidlo "Mimo AD – ImmutableId uživatele" je přidána k Azure AD Connect. Tento efekt je neškodný a nezpůsobí zpětný zápis atributu msDS-ConsistencyGuid dojde k. Aby nedocházelo k záměně, logiky přidala k zajištění, že synchronizační pravidlo se přidá pouze pokud je povolena funkce.

* Opravili jsme problém, který způsobil selhání synchronizaci hodnot hash hesel se událost chyby 611. K tomuto problému dochází po jedné nebo více doménu, kterou řadiče byly odebrány z místní služby AD. Na konci každé heslo synchronizační cyklus synchronizačního souboru cookie vydala pomocí místní AD obsahuje ID vyvolání odebraných řadičů domény s USN (Update Sequence Number) hodnotu 0. Správce synchronizace hesel nelze trvale uložit synchronizaci souborů cookie obsahující USN hodnotu 0 a nezdaří a zobrazí se chybová událost 611. Při příštím synchronizačním cyklu Správce synchronizace hesel opětovně používá poslední trvalý synchronizačního souboru cookie, který neobsahuje hodnotu USN 0. To způsobí, že stejné změny hesla Opakovaná synchronizace. S touto opravou Správce synchronizace hesel správně nevyřeší synchronizačního souboru cookie.

* Dříve i v případě, že automatického upgradu byla zakázána pomocí rutiny Set-ADSyncAutoUpgrade, automatický Upgrade proces pokračuje v kontrole pro upgrade pravidelně a spoléhá na stažený instalační program případném dalším sdílení dodržovat postižení. S touto opravou proces automatického upgradu už zkontroluje upgrade pravidelně. Oprava se automaticky použije při spuštění po upgradu instalační program pro tuto verzi služby Azure AD Connect.

#### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení

* Dříve [msDS-ConsistencyGuid jako zdrojové ukotvení](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-msds-consistencyguid-as-sourceanchor) byla k dispozici pro nové nasazení pouze funkce. Teď je k dispozici pro existující nasazení. A konkrétně:
  * Chcete-li získat přístup k funkci, spusťte Průvodce Azure AD Connect a zvolte *aktualizace zdrojové ukotvení* možnost.
  * Tato možnost je jenom viditelné pro stávající nasazení, které používají jako atribut sourceAnchor objectGuid.
  * Při konfiguraci možnosti, Průvodce ověří stav atributu msDS-ConsistencyGuid ve vašem místním Active Directory. Pokud atribut není nakonfigurovaná na libovolný objekt uživatele v adresáři, použije průvodce msDS-ConsistencyGuid jako atribut sourceAnchor. Jestliže je atribut nastaven na jeden nebo více objektů uživatelů v adresáři, dojde k závěru průvodce, atribut se používá jinými aplikacemi a není vhodný jako atribut sourceAnchor a neumožňuje změnu zdrojové ukotvení pokračovat. Pokud jste si jisti, že atribut nepoužívá existující aplikace, budete muset kontaktovat podporu pro informace o tom, jak potlačit chyby.

* Specifické pro **userCertificate** atribut na objekty zařízení Azure AD Connect nyní vyhledá certifikáty hodnoty požadované pro [připojení zařízení připojených k doméně do Azure AD pro Windows 10 prostředí](https://docs.microsoft.com/azure/active-directory/active-directory-azureadjoin-devices-group-policy) a Filtr rest před nesynchronizuje do Azure AD. Pokud chcete povolit toto chování, pravidlo synchronizace out-of-box "Navýšení kapacity na AAD – zařízení připojit SOAInAD" byla aktualizována.

* Azure AD Connect nyní podporuje zpětný zápis Exchange Online **cloudPublicDelegates** atribut místní AD **publicDelegates** atribut. To umožňuje scénáře, ve kterém poštovní schránku Exchange Online lze udělit práva SendOnBehalfTo uživatelům s poštovní schránku v místním systému Exchange. Pro podporu této funkce nové pravidlo synchronizace out-of-box "Mimo AD – zpětný zápis uživatelů Exchange hybridní PublicDelegates" se přidala. Toto synchronizační pravidlo se přidá do služby Azure AD Connect, pouze pokud je povolena funkce hybridní Exchange.

*   Synchronizace Azure AD Connect teď podporuje **altRecipient** atribut ze služby Azure AD. Pro podporu této změně, byly aktualizovány následující pravidla synchronizace out-of-box zahrnout požadovaný atribut toku:
  * V ze služby AD – uživatelů Exchange
  * Na AAD – ExchangeOnline uživatele
  
* **CloudSOAExchMailbox** atribut v úložišti Metaverse označuje, zda má daný uživatel poštovní schránky systému Exchange Online nebo ne. Zahrnout další Exchange Online RecipientDisplayTypes jako taková zařízení a konferenční místnost poštovní schránky byla aktualizována jeho definici. Chcete-li tuto změnu, definice cloudSOAExchMailbox atribut, který se nachází v části pravidla synchronizace out-of-box "V z AAD – uživatel hybridní Exchange", byl aktualizován z:

```
CBool(IIF(IsNullOrEmpty([cloudMSExchRecipientDisplayType]),NULL,BitAnd([cloudMSExchRecipientDisplayType],&amp;HFF) = 0))
```

... pro následující:

```
CBool(
  IIF(IsPresent([cloudMSExchRecipientDisplayType]),(
    IIF([cloudMSExchRecipientDisplayType]=0,True,(
      IIF([cloudMSExchRecipientDisplayType]=2,True,(
        IIF([cloudMSExchRecipientDisplayType]=7,True,(
          IIF([cloudMSExchRecipientDisplayType]=8,True,(
            IIF([cloudMSExchRecipientDisplayType]=10,True,(
              IIF([cloudMSExchRecipientDisplayType]=16,True,(
                IIF([cloudMSExchRecipientDisplayType]=17,True,(
                  IIF([cloudMSExchRecipientDisplayType]=18,True,(
                    IIF([cloudMSExchRecipientDisplayType]=1073741824,True,(
                       IF([cloudMSExchRecipientDisplayType]=1073741840,True,False)))))))))))))))))))),False))

```

* Přidat následující sadu X509Certificate2 kompatibilní s funkcí pro vytváření výrazů pravidla pro zpracování certifikátů hodnoty atributem userCertificate synchronizace:

    ||||
    | --- | --- | --- |
    |CertSubject|CertIssuer|CertKeyAlgorithm|
    |CertSubjectNameDN|CertIssuerOid|CertNameInfo|
    |CertSubjectNameOid|CertIssuerDN|IsCert|
    |CertFriendlyName|certThumbprint|CertExtensionOids|
    |CertFormat|CertNotAfter|CertPublicKeyOid|
    |CertSerialNumber|CertNotBefore|CertPublicKeyParametersOid|
    |CertVersion|CertSignatureAlgorithmOid|Vyberte|
    |CertKeyAlgorithmParams|CertHashString|Kde|
    |||S|

* Umožňuje zákazníkům vytvářet vlastní synchronizační pravidla, které jsou předávány sAMAccountName, domainNetBios a domainFQDN pro objekty skupiny, a také distinguishedName pro uživatelské objekty byly zavedeny následující změny schématu:

  * Schéma MV byly přidány následující atributy:
    * Skupina: název účtu
    * Skupina: domainNetBios
    * Skupina: domainFQDN
    * Osoba: distinguishedName

  * Byly přidány následující atributy do schématu konektor služby Azure AD:
    * Group: OnPremisesSamAccountName
    * Skupina: NetBiosName
    * Group: DnsDomainName
    * Uživatel: OnPremisesDistinguishedName

* Tento skript rutiny ADSyncDomainJoinedComputerSync teď má nový volitelný parametr s názvem AzureEnvironment. Tento parametr slouží k určení jakou oblast odpovídající tenanta Azure Active Directory je hostován v. Platné hodnoty jsou:
  * AzureCloud (výchozí)
  * AzureChinaCloud
  * AzureGermanyCloud
  * USGovernment
 
* Aktualizované Editor pravidel synchronizace pro použití připojení (místo poskytování) jako výchozí hodnota typu odkazu při vytváření pravidla synchronizace.

### <a name="ad-fs-management"></a>Správa služby AD FS

#### <a name="issues-fixed"></a>Problémy opraveny

* Následující adresy URL jsou nové koncové body WS-Federation zavedených v Azure AD a zvýšit odolnost proti chybám před výpadkem ověřování a přidá se do místní služby AD FS odpovídajících stran konfigurace vztahu důvěryhodnosti:
  * https://ests.login.microsoftonline.com/login.srf
  * https://stamp2.login.microsoftonline.com/login.srf
  * https://ccs.login.microsoftonline.com/login.srf
  * https://ccs-sdf.login.microsoftonline.com/login.srf
  
* Opravili jsme problém, která způsobila služba AD FS generují hodnotu nesprávná deklarace identity IssuerID. K problému dochází, pokud existuje více ověřených domén v tenantovi Azure AD a přípona domény atributu userPrincipalName sloužící ke generování deklarace identity IssuerID je minimálně 3 úrovně hloubkové (například johndoe@us.contoso.com). Tento problém vyřeší aktualizací regulární výraz používaný pravidly deklarací identity.

#### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení
* Dříve funkce správy certifikátů služby AD FS poskytuje Azure AD Connect jde použít jenom s farmy služby AD FS spravovat pomocí služby Azure AD Connect. Teď můžete použít funkci pomocí farmy AD FS, které nejsou spravované pomocí služby Azure AD Connect.

## <a name="115240"></a>1.1.524.0
Vydáno: Květen 2017

> [!IMPORTANT]
> Existují schématu a synchronizační pravidlo změny zavedené v tomto sestavení. Služba Azure AD Connect synchronizaci aktivují úplný Import a synchronizaci kroků po upgradu. Podrobnosti sady změn jsou popsané níže.
>
>

**Opravené problémy:**

Synchronizace služby Azure AD Connect

* Opravili jsme problém, který způsobí, že dojde k serveru Azure AD Connect i v případě zákazníků se vypne funkce pomocí rutiny Set-ADSyncAutoUpgrade automatický Upgrade. S touto opravou proces automatický Upgrade na serveru stále kontroluje pro upgrade pravidelně, ale na stažený instalační program respektuje konfiguraci automatického upgradu.
* Při upgradu nástroje DirSync na místě Azure AD Connect vytvoří účet služby Azure AD konektoru služby Azure AD používané pro synchronizaci s Azure AD. Po vytvoření účtu služby Azure AD Connect ověří se pomocí účtu služby Azure AD. V některých případech ověřování selže kvůli přechodným problémům, které způsobí, že místní upgrade nástroje DirSync se nezdaří s chybou *"došlo k chybě provádění úlohy konfigurace AAD Sync: AADSTS50034: pro přihlášení do této aplikace, musí být účet Přidat k adresáři xxx.onmicrosoft.com."* Pokud chcete zlepšit odolnost upgrade nástroje DirSync, Azure AD Connect nyní zopakuje pokus o krok ověřování.
* Došlo k nějakému problému se sestavením 443, který způsobí, že místní upgrade nástroje DirSync na úspěšné, ale nevytvoří profily spuštění, které jsou vyžadované pro synchronizaci adresářů. Opravy logiky je zahrnuta v tomto sestavení služby Azure AD Connect. Po upgradu zákazníků na toto sestavení, Azure AD Connect rozpozná chybějící profily spuštění a vytvoří je.
* Opravili jsme problém, který způsobí, že proces synchronizace hesel nemůže začínat 6900 ID události a chyby *"položka se stejným klíčem již byla přidána"*. K tomuto problému dochází, pokud aktualizujete OU filtrování konfigurace zahrnují AD konfigurační oddíl. Chcete-li vyřešit tento problém, proces synchronizace hesel teď synchronizuje změny hesel z pouze oddíly domény AD. Oddíly doméně jako je například oddílu konfigurace se přeskočí.
* Při Expresní instalace služby Azure AD Connect vytvoří místní účet AD DS se používá konektorem AD ke komunikaci s místní služby AD. Dříve účet se vytvoří s PASSWD_NOTREQD příznak nastaven na atribut řízení uživatelských účtů a náhodné heslo je nastavena na účtu. Nyní Azure AD Connect explicitně odebere příznak PASSWD_NOTREQD po nastavení hesla pro účet.
* Opravili jsme problém způsobující selhání upgradu nástroje DirSync s chybou *"zablokování došlo k chybě v systému sql server pokusu získat zámek aplikace"* po atributu mailNickname nachází v místní schéma služby AD, ale není vázán k Třída objektu uživatele AD.
* Opravili jsme problém, který způsobí, že funkce zpětný zápis zařízení se automaticky deaktivuje při správce aktualizuje konfiguraci synchronizace Azure AD Connect pomocí Průvodce Azure AD Connect. Tento problém je způsoben Průvodce předběžné kontrole pro existující konfigurace zpětného zápisu zařízení v místním AD a kontrola selže. Oprava se pro přeskočení kontroly, pokud zařízení povolený zpětný zápis je již dříve.
* Konfigurace filtrování podle organizačních jednotek, můžete použít buď Průvodce Azure AD Connect nebo Synchronization Service Manager. Dříve Pokud použijete Průvodce Azure AD Connect ke konfiguraci filtrování podle organizačních jednotek, nové organizační jednotky, které vytvořili později jsou uvedené pro synchronizaci adresářů. Pokud nechcete, aby se nové organizační jednotky mají být zahrnuty, je nutné nakonfigurovat filtrování podle organizačních jednotek pomocí Synchronization Service Manager. Teď můžete dosáhnout stejné chování pomocí Průvodce Azure AD Connect.
* Opravili jsme problém, který způsobí, že uložené procedury, které jsou vytvořené v rámci schématu instalaci správce místo v rámci schématu dbo vyžaduje Azure AD Connect.
* Opravili jsme problém, který způsobí, že atribut TrackingId vrácené Azure AD, aby vynechat v AAD Connect protokol událostí serveru. Tento problém nastane, pokud Azure AD Connect přijme zprávu přesměrování z Azure AD a Azure AD Connect se nemůže připojit ke koncovému bodu k dispozici. TrackingId používá techniky podpory k při řešení potíží můžete provádět korelaci s protokoly na straně služby.
* Když Azure AD Connect obdrží chyba LargeObject ze služby Azure AD, Azure AD Connect vygeneruje událost EventID 6941, zpráva *"zřízený objekt je příliš velký. Omezte počet hodnot atributů pro tento objekt."* Ve stejnou dobu, Azure AD Connect také vygeneruje událost zavádějící EventID 6900, zpráva *"Microsoft.Online.Coexistence.ProvisionRetryException: Nelze navázat komunikaci s Windows služby Azure Active Directory."* Pro zachování přehlednosti, Azure AD Connect již negeneruje druhé události, když je obdržena chyba LargeObject.
* Opravili jsme problém, který způsobí, že Synchronization Service Manager přestane reagovat při pokusu o aktualizaci konfigurace pro obecný konektor LDAP.

**Nové funkce a vylepšení:**

Synchronizace služby Azure AD Connect
* Byly implementovány změny pravidel synchronizace – následující změny pravidel synchronizace:
  * Aktualizované výchozí synchronizační pravidlo nastavené na neexportuje atributy **userCertificate** a **userSMIMECertificate** máte víc než 15 hodnot atributů.
  * Atributy AD **employeeID** a **msExchBypassModerationLink** jsou nyní součástí výchozí sadu pravidel synchronizace.
  * Atribut AD **fotografii** byla odebrána z výchozí sadu pravidel synchronizace.
  * Přidání **preferredDataLocation** schéma úložiště Metaverse a schéma konektoru AAD. Zákazníci, kteří chtějí aktualizovat buď atributy ve službě Azure AD můžete implementovat vlastní synchronizační pravidla, Uděláte to tak. 
  * Přidání **userType** schéma úložiště Metaverse a schéma konektoru AAD. Zákazníci, kteří chtějí aktualizovat buď atributy ve službě Azure AD můžete implementovat vlastní synchronizační pravidla, Uděláte to tak.

* Azure AD Connect teď automaticky povolí použití atributu ConsistencyGuid jako atribut zdrojového ukotvení pro místní AD objekty. Další, Azure AD Connect naplní ConsistencyGuid atributu s hodnotou atribut objectGuid, pokud je prázdný. Tato funkce se vztahuje na pouze nové nasazení. Pro další informace o této funkci najdete v části [Azure AD Connect: koncepty návrhu – použití msDS-ConsistencyGuid jako parametru sourceAnchor](active-directory-aadconnect-design-concepts.md#using-msds-consistencyguid-as-sourceanchor).
* Nové řešení potíží s rutinu Invoke-ADSyncDiagnostics se přidala pro usnadnění diagnostiky synchronizaci hodnot Hash hesel v otázkách. Informace o použití rutiny najdete v článku [řešit synchronizaci hodnot hash hesel pomocí synchronizace Azure AD Connect](active-directory-aadconnectsync-troubleshoot-password-hash-synchronization.md).
* Azure AD Connect, teď podporuje synchronizaci veřejné složky s povolenou poštou objekty z místní služby AD do služby Azure AD. Můžete povolit funkci pomocí Průvodce Azure AD Connect v rámci volitelných funkcí. Další informace o této funkci najdete v článku [Office 365 Directory na základě blokování serveru Edge podporu pro místní povolené veřejné složky pošty](https://blogs.technet.microsoft.com/exchange/2017/05/19/office-365-directory-based-edge-blocking-support-for-on-premises-mail-enabled-public-folders).
* Azure AD Connect vyžaduje AD DS účet se má synchronizovat z místní služby AD. Dříve Pokud jste nainstalovali Azure AD Connect s použitím expresní režim, můžete poskytnout přihlašovací údaje podnikového správce účtu a služby Azure AD Connect bude vytvořit účet služby AD DS, vyžaduje. Pro vlastní instalaci a přidání doménových struktur do stávajícího nasazení, ale jsou vyžadovány a místo toho použijte účet služby AD DS. Teď máte také možnost poskytnout přihlašovací údaje podnikového správce účtu při vlastní instalaci a umožní vytvořit účet služby AD DS vyžaduje Azure AD Connect.
* Azure AD Connect podporuje teď SQL AOA. Před instalací Azure AD Connect je nutné povolit SQL AOA. Během instalace Azure AD Connect zjistí, zda zadaná instance SQL je nebo není povolená pro SQL AOA. Pokud je povolené SQL AOA, Azure AD Connect další přijde na to, pokud SQL AOA je nakonfigurovaná pro používání replikace synchronní nebo asynchronní replikace. Při nastavování naslouchacího procesu skupiny dostupnosti, se doporučuje nastavit vlastnost RegisterAllProvidersIP na hodnotu 0. Toto doporučení je vzhledem k tomu, že Azure AD Connect v současnosti využívá nativní klient systému SQL pro připojení k SQL a nativní klient systému SQL nepodporuje používání vlastnosti MultiSubNetFailover.
* Pokud používáte LocalDB jako databázi pro váš server Azure AD Connect a byl dosažen limit velikosti 10 GB, synchronizační služba už spustí. Dříve budete muset provést operaci ShrinkDatabase na LocalDB uvolnit dostatek místa v databázi pro synchronizační službu spustit. A Synchronization Service Manager můžete odstranit historii spuštění uvolnit více místa v databázi. Nyní vám pomůže rutiny Start-ADSyncPurgeRunHistory data historie vyprázdnění spustit z LocalDB na uvolněním místa v databázi. Kromě toho tato rutina podporuje offline režimu (zadáním parametru - offline) který se dá použít při synchronizační služba není spuštěná. Poznámka: Offline režimu jde použít jenom Pokud neběží synchronizační služba a použít databázi LocalDB.
* Ke snížení množství prostor úložiště vyžadovaný, Azure AD Connect nyní komprimuje podrobnosti o chybě synchronizace před jejich uložením do databáze LocalDB nebo SQL. Při upgradu ze starší verze služby Azure AD Connect na tuto verzi služby Azure AD Connect provede jednorázovou komprese existující podrobnosti o chybě synchronizace.
* Dříve po aktualizaci konfiguraci filtrování organizačních jednotek, je nutné ručně spustit úplný import zajistit, že stávající objekty jsou správně zahrnutý/vyloučený z synchronizace adresářů. Nyní, Azure AD Connect automaticky aktivuje úplný import při příští synchronizaci cyklu. Další, úplný import platí jenom pro konektory AD aktualizací ovlivněny. Poznámka: Toto vylepšení se vztahuje na organizační jednotku filtrování aktualizací bylo vytvořeno s použitím Průvodce Azure AD Connect. To neplatí pro organizační jednotku filtrování aktualizací bylo vytvořeno s použitím Synchronization Service Manager.
* Dříve filtrování podle skupin umožňuje uživatelům, skupinám, a kontaktujte pouze objekty. Nyní filtrování podle skupin také podporuje objekty počítače.
* Dříve můžete odstranit data prostoru konektoru bez zakázání Plánovač synchronizace Azure AD Connect. Nyní Synchronization Service Manager blokuje odstranění datového prostoru konektoru, pokud zjistí, zda je povolen plánovač. Upozornění dál, je vrácena informovat zákazníky o ztrátě dat, pokud se odstraní data prostoru konektoru.
* Dříve je nutné zakázat přepis prostředí PowerShell pro Azure AD Connect průvodce správně spustit. Částečně vyřeší tento problém. Přepis prostředí PowerShell můžete povolit, pokud použijete Průvodce Azure AD Connect ke správě konfigurace synchronizace. Pokud ke správě konfigurace služby AD FS používáte Průvodce Azure AD Connect je nutné zakázat určené k transkripci prostředí PowerShell.



## <a name="114860"></a>1.1.486.0
Vydáno: Duben 2017

**Opravené problémy:**
* Byl opraven problém, kam Azure AD Connect nebude úspěšně nainstalovat lokalizované verzi systému Windows Server.

## <a name="114840"></a>1.1.484.0
Vydáno: Duben 2017

**Známé problémy:**

* Tato verze služby Azure AD Connect se úspěšně nainstalovat, pokud jsou splněny všechny následující podmínky:
   1. Provádění obou instalace nástroje DirSync místní upgrade nebo nové služby Azure AD Connect.
   2. Používáte lokalizovanou verzi sady Windows Server, kde název integrované skupiny správce na serveru není "Správce".
   3. Používáte výchozí SQL Server 2012 Express LocalDB službou Azure AD Connect namísto zadávání úplné SQL nainstalované.

**Opravené problémy:**

Synchronizace služby Azure AD Connect
* Opravili jsme problém, kde Plánovač synchronizace přeskočí krok celý synchronizace, pokud jeden nebo více konektorů chybí profil spuštění pro daný krok synchronizace. Například byste přidali ručně konektor bez vytvoření rozdílový Import spuštění profilu pomocí Synchronization Service Manager. Tato oprava zajistí, že Plánovač synchronizace pořád spustit rozdílový Import pro jiné konektory.
* Opravili jsme problém, synchronizační služba kde okamžitě zastaví, zpracování profil spuštění, když se nachází zaznamená problém s jedním z kroků spuštění. Tato oprava zajistí, že synchronizační služba, která spustí krok přeskočí a pokračuje ve zpracování zbytek. Například můžete mít rozdílový Import spustit profil pro váš konektor AD s více kroky spuštění (jeden pro každý místní domény služby AD). Synchronizační služba spustí rozdílový Import z jiných domén AD i v případě, že jeden z nich má problémy se síťovým připojením.
* Opravili jsme problém, který způsobí, že konektor služby Azure AD aktualizace přeskočit během automatického upgradu.
* Opravili jsme problém, který způsobí, že Azure AD Connect k nesprávně určit, zda je server řadičem domény během instalace, které v důsledku způsobí selhání upgradu nástroje DirSync.
* Opravili jsme problém, který způsobí, že místní upgrade nástroje DirSync není vytvoření jakékoli profil spuštění pro konektor služby Azure AD.
* Opravili jsme problém, kdy Synchronization Service Manager uživatelské rozhraní přestane reagovat při pokusu o konfiguraci pro obecný konektor LDAP.

Správa služby AD FS
* Opravili jsme problém, kdy průvodce Azure AD Connect selže, pokud služba AD FS primárního uzlu se přesunul na jiný server.

Jednotného přihlašování
* Oprava potíží v průvodce službou Azure AD Connect, kde přihlašovací obrazovka neumožňuje jednotného funkci povolit, pokud zvolíte synchronizaci hesel jako vaše možnost přihlášení při nové instalaci.

**Nové funkce a vylepšení:**

Synchronizace služby Azure AD Connect
* Azure AD Connect Sync nyní podporuje použití účet virtuální služby, účet spravované služby a skupinový účet spravované služby jako účet služby. To platí pro nové instalace služby Azure AD Connect pouze. Při instalaci Azure AD Connect:
    * Průvodce Azure AD Connect ve výchozím nastavení, vytvoří účet virtuální služby a použije je jako jeho účet služby.
    * Pokud nainstalujete na řadič domény, Azure AD Connect spadne zpět na předchozí chování, kde se vytvoří účet uživatele domény a použije je jako jeho účet služby.
    * Výchozí chování můžete přepsat zadáním jednoho z následujících akcí:
      * Skupinový účet spravované služby
      * Účet spravované služby
      * Účet uživatele domény
      * Místní uživatelský účet
* Dříve při upgradu na nové sestavení Azure AD Connect obsahuje konektory aktualizovat nebo změny v pravidlech synchronizace, Azure AD Connect spustí cyklus úplné synchronizace. Nyní Azure AD Connect selektivně aktivuje kroky úplný Import pouze pro konektory s aktualizací update a úplnou synchronizaci pouze pro konektory se změny pravidel synchronizace.
* Dříve prahová hodnota odstranění exportu platí jenom pro export, které jsou aktivované přes Plánovač synchronizace. Tato funkce je teď rozšířit exporty ručně aktivované zákazník využívající Synchronization Service Manager.
* Ve svém tenantovi Azure AD se konfigurace služby, která určuje, zda funkce Synchronizace hesla je povoleno pro vašeho tenanta, nebo ne. Dříve je snadné pro konfiguraci služby, chcete-li být nesprávně nakonfigurování Azure AD Connect, pokud máte aktivní a pracovní server. Teď by Azure AD Connect se pokusí uchovat konfiguraci služby konzistentní s aktivních pouze server Azure AD Connect.
* Průvodce teď detekuje a vrátí upozornění, pokud Azure AD Connect místní AD nemá Koš služby Active Directory povolena.
* Dříve Export do služby Azure AD vyprší a selže, pokud celková velikost objektů v dávce překročí určitou prahovou hodnotu. Synchronizační služba se nyní znovu pokusí znovu odeslat objekty v samostatné, menší dávky, pokud tento problém nastává.
* Správa synchronizace služby klíč aplikace byla odebrána v nabídce Windows Start. Správa šifrovací klíč bude nadále být podporovány prostřednictvím rozhraní příkazového řádku pomocí miiskmu.exe. Informace o správě šifrovací klíč, najdete v článku [nastavuje Azure AD Connect Sync šifrovací klíč](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-change-serviceacct-pass#abandoning-the-azure-ad-connect-sync-encryption-key).
* Dříve Pokud změníte heslo účtu služby Azure AD Connect sync, synchronizační služba nebude možné start správně opuštěných šifrovacího klíče a znovu inicializovat heslo účtu služby Azure AD Connect sync. Tento proces se teď už nevyžaduje.

Jednotného přihlašování

* Průvodce Azure AD Connect se už nevyžaduje port 9090 otevíraly v síti, při konfiguraci předávací ověřování a Desktop SSO. Vyžaduje se jenom port 443. 

## <a name="114430"></a>1.1.443.0
Vydáno: Březen 2017

**Opravené problémy:**

Synchronizace služby Azure AD Connect
* Opravili jsme chybu, která způsobí, že průvodce Azure AD Connect selhat, pokud zobrazovaný název konektor služby Azure AD neobsahuje počáteční doméně onmicrosoft.com přiřazené k tenantovi Azure AD.
* Opravili jsme chybu, která způsobí, že průvodce Azure AD Connect k selhání při navazování připojení ke službě SQL database, pokud heslo účtu synchronizační služby obsahuje speciální znaky, třeba apostrof, dvojtečku a místo.
* Opravili jsme problém, který způsobuje chybu "dimage má anchor, který se liší od obrázku" na serveru služby Azure AD Connect v pracovním režimu, poté, co jste dočasně vyloučili místní AD objektu synchronizaci a pak ho znovu zahrnuté pro synchronizaci.
* Opravili jsme problém, který způsobuje chybu "nalézt rozlišující název objektu je fiktivní" na serveru služby Azure AD Connect v pracovním režimu, poté, co jste dočasně vyloučili místní AD objektu synchronizaci a pak ho znovu zahrnuté pro synchronizaci.

Správa služby AD FS
* Opravili jsme problém, kdy průvodce Azure AD Connect není aktualizovat konfiguraci služby AD FS a nastavte správné deklarací na vztah důvěryhodnosti předávající strany po dokončení konfigurace alternativního přihlašovacího ID.
* Opravili jsme problém, kdy průvodce Azure AD Connect není schopen správně zpracovat servery služby AD FS, jejíž účty služeb jsou nakonfigurované pomocí formátu userPrincipalName namísto formátu sAMAccountName.

Předávací ověřování
* Opravili jsme chybu, která způsobí, že průvodce Azure AD Connect selhat, pokud je vybrána možnost předat prostřednictvím ověření, ale registrace jeho konektoru selže.
* Opravili jsme problém způsobující Průvodce Azure AD Connect obejít ověření kontrol vybrána, když je povolena funkce jednotného přihlašování – metoda.

Resetování hesla
* Opravili jsme chybu, která může způsobit, že nebude pokoušet o znovu připojit, pokud byl ukončen připojení bránou firewall nebo proxy serveru Azure AAD Connect.

**Nové funkce a vylepšení:**

Synchronizace služby Azure AD Connect
* Rutina Get-ADSyncScheduler nyní vrátí novou logickou vlastnost s názvem SyncCycleInProgress. Pokud vrácená hodnota je true, znamená to, že dochází k zacyklení plánované synchronizace probíhá.
* Cílovou složku pro ukládání instalace služby Azure AD Connect a protokoly instalace se změnila z %localappdata%\AADConnect k %programdata%\AADConnect zlepšení přístupnosti do souborů protokolu.

Správa služby AD FS
* Přidání podpory pro aktualizaci certifikát SSL farmy služby AD FS.
* Přidání podpory pro správu AD FS 2016.
* Teď můžete zadat existující gMSA (skupinový účet spravované služby) během instalace služby AD FS.
* Teď můžete nakonfigurovat SHA-256 jako hashovací algoritmus podpisu pro Azure AD vztah důvěryhodnosti předávající strany.

Resetování hesla
* Zavedení vylepšení umožňující produktu, který v prostředích s přísnější pravidla brány firewall.
* Spolehlivost vylepšené připojení ke službě Azure Service Bus.

## <a name="113800"></a>1.1.380.0
Vydáno: Prosince 2016

**Oprava potíží:**

* Byl opraven problém, kdy pravidlo deklarace identity issuerid pro Active Directory Federation Services (AD FS) chybí v tomto sestavení.

>[!NOTE]
>Toto sestavení není k dispozici zákazníkům prostřednictvím funkce Azure AD Connect automatický Upgrade.

## <a name="113710"></a>1.1.371.0
Vydáno: Prosince 2016

**Známý problém:**

* Pravidlo deklarace identity issuerid pro službu AD FS chybí v tomto sestavení. Pravidlo deklarace identity issuerid je vyžadována, pokud jsou federaci více domén se službou Azure Active Directory (Azure AD). Pokud používáte Azure AD Connect ke správě místního nasazení služby AD FS, upgrade na toto sestavení odebere stávající pravidlo deklarace identity issuerid z konfigurace služby AD FS. Tento problém můžete obejít tak, že přidáte pravidlo deklarace identity issuerid po instalaci nebo upgradu. Pro podrobnosti o přidání issuerid pravidlo deklarace identity, najdete v tomto článku na [podpora více domén pro federaci s Azure AD](active-directory-aadconnect-multiple-domains.md).

**Oprava potíží:**

* Pokud Port 9090 není otevřený pro odchozí připojení, Azure AD Connect instalace nebo upgradu se nezdaří.

>[!NOTE]
>Toto sestavení není k dispozici zákazníkům prostřednictvím funkce Azure AD Connect automatický Upgrade.

## <a name="113700"></a>1.1.370.0
Vydáno: Prosince 2016

**Známé problémy:**

* Pravidlo deklarace identity issuerid pro službu AD FS chybí v tomto sestavení. Pravidlo deklarace identity issuerid je vyžadována, pokud federujete víc domén s Azure AD. Pokud používáte Azure AD Connect ke správě místního nasazení služby AD FS, upgrade na toto sestavení odebere stávající pravidlo deklarace identity issuerid z konfigurace služby AD FS. Tento problém můžete obejít tak, že přidáte pravidlo deklarace identity issuerid po instalaci nebo upgradu. Pro podrobnosti o přidání issuerid pravidlo deklarace identity, najdete v tomto článku na [podpora více domén pro federaci s Azure AD](active-directory-aadconnect-multiple-domains.md).
* Odchozí pro dokončení instalace musíte otevřít port 9090.

**Nové funkce:**

* Předávací ověřování (Preview).

>[!NOTE]
>Toto sestavení není k dispozici zákazníkům prostřednictvím funkce Azure AD Connect automatický Upgrade.

## <a name="113430"></a>1.1.343.0
Vydáno: Listopad 2016

**Známý problém:**

* Pravidlo deklarace identity issuerid pro službu AD FS chybí v tomto sestavení. Pravidlo deklarace identity issuerid je vyžadována, pokud federujete víc domén s Azure AD. Pokud používáte Azure AD Connect ke správě místního nasazení služby AD FS, upgrade na toto sestavení odebere stávající pravidlo deklarace identity issuerid z konfigurace služby AD FS. Tento problém můžete obejít tak, že přidáte pravidlo deklarace identity issuerid po instalaci nebo upgradu. Pro podrobnosti o přidání issuerid pravidlo deklarace identity, najdete v tomto článku na [podpora více domén pro federaci s Azure AD](active-directory-aadconnect-multiple-domains.md).

**Opravené problémy:**

* V některých případech instalaci Azure AD Connect se nezdaří, protože nelze vytvořit s účtem místní služby, jehož heslo splňuje úroveň složitosti určené zásady hesla organizace.
* Opravili jsme problém, kdy spojení nejsou vyhodnocovány až objekt v prostoru konektoru současně na více instancí oboru jednoho připojení pravidlo a stát v oboru pro jiné. To může nastat, pokud mají dvě nebo více pravidel spojení, jejíž podmínky spojení se vzájemně vylučují.
* Opravili jsme problém, kde nejsou zpracovávány pravidla synchronizace příchozích dat (ze služby Azure AD), která neobsahují spojení pravidla, pokud mají hodnoty nižší prioritu než těch, které obsahují pravidla spojení.

**Vylepšení:**

* Přidání podpory pro instalaci Azure AD Connect na Windows serveru 2016 Standard nebo vyšší.
* Přidání podpory pro používání SQL serveru 2016 jako vzdálenou databázi pro služby Azure AD Connect.

## <a name="112810"></a>1.1.281.0
Vydáno: Srpen 2016

**Opravené problémy:**

* Změny synchronizovat intervalu není probíhat až po dokončení další cyklus synchronizace.
* Průvodce Azure AD Connect nepřijímá účet Azure AD, jejichž uživatelské jméno začíná podtržítkem (\_).
* Průvodce Azure AD Connect nepodaří ověřit účet služby Azure AD, pokud heslo účtu obsahuje příliš mnoho speciální znaky. Chybová zpráva "Nepodařilo se ověřit přihlašovací údaje. Neočekávané došlo k chybě." je vrácena.
* Odinstalace pracovní server zakáže synchronizaci hesel v tenantovi Azure AD a způsobí, že synchronizace hesel se nezdaří s aktivní server.
* Synchronizace hesel selže v neobvyklých případech, pokud neexistuje žádné hodnoty hash hesla uložená na uživatele.
* Pokud server Azure AD Connect je povolené pro pracovní režim, není dočasně zakázané zpětný zápis hesla.
* Průvodce Azure AD Connect není uveden synchronizace skutečná hesla a konfigurace zpětného zápisu hesla server je v pracovní režimu. Vždy zobrazuje je jako zakázané.
* Změny konfigurace synchronizace hesel a zpětný zápis hesla nejsou trvalé podle průvodce Azure AD Connect, server je v pracovní režimu.

**Vylepšení:**

* Aktualizace rutiny Start-ADSyncSyncCycle označující, zda je možné úspěšně spustit nový cyklus synchronizace, nebo ne.
* Přidání rutiny Stop-ADSyncSyncCycle ukončit cyklus synchronizace a operace, které zrovna probíhají.
* Aktualizovat rutinu Stop-ADSyncScheduler ukončit cyklus synchronizace a operace, které zrovna probíhají.
* Při konfiguraci [rozšíření adresáře](active-directory-aadconnectsync-feature-directory-extensions.md) v průvodce službou Azure AD Connect, můžete teď vybrat atribut Azure AD typu "Teletex řetězec".

## <a name="111890"></a>1.1.189.0
Vydáno:. Června 2016

**Oprava problémů a vylepšení:**

* Azure AD Connect je nyní nainstalovat na serveru odpovídající standardu FIPS.
  * Synchronizace hesel najdete v části [synchronizace hodnot hash hesel a FIPS](active-directory-aadconnectsync-implement-password-hash-synchronization.md#password-hash-synchronization-and-fips).
* Opravili jsme problém, kdy nelze přeložit název pro rozhraní NetBIOS na plně kvalifikovaný název domény v konektoru služby Active Directory.

## <a name="111800"></a>1.1.180.0
Vydáno:. Května 2016

**Nové funkce:**

* Upozorní a umožňuje ověření domén, pokud se ho nepovedlo provést před spuštěním služby Azure AD Connect.
* Přidání podpory pro [Microsoft Cloud Germany](active-directory-aadconnect-instances.md#microsoft-cloud-germany).
* Přidali jsme podporu pro nejnovější [cloudu Microsoft Azure Government](active-directory-aadconnect-instances.md#microsoft-azure-government-cloud) infrastruktury s požadavky na novou adresu URL.

**Oprava problémů a vylepšení:**

* Přidat do editoru pravidlo synchronizace a usnadňují vyhledání synchronizační pravidla filtrování.
* Vylepšili jsme výkon při odstraňování prostoru konektoru.
* Opravili jsme problém, když na stejný objekt byla odstraněna i přidáno ve stejném běhu (volaná odstranit nebo přidat).
* Zakázané synchronizační pravidlo už znovu povolí zahrnuté objekty a aktualizovat atributy na upgrade nebo adresáře schématu.

## <a name="111300"></a>1.1.130.0
Vydáno:. Dubna 2016

**Nové funkce:**

* Přidali jsme podporu pro více jednohodnotových atributů k [rozšíření adresáře](active-directory-aadconnectsync-feature-directory-extensions.md).
* Přidání podpory pro další změny konfigurace pro [automatický upgrade](active-directory-aadconnect-feature-automatic-upgrade.md) považuje za vhodné k upgradu.
* Přidání některých rutin pro [vlastní plánovač](active-directory-aadconnectsync-feature-scheduler.md#custom-scheduler).

## <a name="111190"></a>1.1.119.0
Vydáno: Březen 2016

**Opravené problémy:**

* Provedené jistotu, že expresní instalaci nelze použít v systému Windows Server 2008 (starší verzi než R2), protože synchronizace hesla se nepodporuje v tomto operačním systému.
* Upgrade z nástroje DirSync s konfigurací vlastního filtru nefunguje podle očekávání.
* Při upgradu na novější verzi a neexistují žádné změny v konfiguraci, nemá být naplánováno úplný import a synchronizace.

## <a name="111100"></a>1.1.110.0
Vydáno: Únor 2016

**Opravené problémy:**

* Upgrade z dřívější verze nebude fungovat, pokud instalaci není ve výchozí složce C:\Program Files.
* Pokud jste nainstalovali a zrušte **spustit proces synchronizace** na konci Průvodce instalací, spustíte Průvodce instalací podruhé neumožní plánovače.
* Plánovač nebude fungovat podle očekávání na serverech, kde se nepoužívá US en formát data a času. Bude také blokovat `Get-ADSyncScheduler` se vraťte správný čas.
* Pokud jste nainstalovali dřívější verze služby Azure AD Connect se službou AD FS jako možnost přihlásit se a upgradu, nelze znovu spustit Průvodce instalací.

## <a name="111050"></a>1.1.105.0
Vydáno: Únor 2016

**Nové funkce:**

* [Automatický upgrade](active-directory-aadconnect-feature-automatic-upgrade.md) funkce pro zákazníky Expresní nastavení.
* Podpora pro globálního správce pomocí ověřování Azure Multi-Factor Authentication a Privileged Identity Management v Průvodci instalací.
  * Je potřeba povolit váš proxy server, která také umožňují přenosy na https://secure.aadcdn.microsoftonline-p.com Pokud používáte ověřování službou Multi-Factor Authentication.
  * Je třeba přidat https://secure.aadcdn.microsoftonline-p.com do seznamu důvěryhodných webů pro Vícefaktorové ověřování a správně fungovat.
* Povolit, změna metodu přihlašování uživatele po počáteční instalaci.
* Povolit [domény a organizační jednotky filtrování](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) v Průvodci instalací. To také umožňuje připojení k doménové struktury, ve kterém jsou k dispozici všechny domény.
* [Plánovač](active-directory-aadconnectsync-feature-scheduler.md) synchronizační modul je součástí.

**Funkce z verze preview přesunuty do všeobecné dostupnosti:**

* [Zpětný zápis zařízení](active-directory-aadconnect-feature-device-writeback.md).
* [Rozšíření adresáře](active-directory-aadconnectsync-feature-directory-extensions.md).

**Nové funkce ve verzi preview:**

* Nové výchozí nastavení synchronizovat cyklu intervalu je 30 minut. Používá tři hodiny pro všechny starší verze. Přidá podporu změnit [Plánovač](active-directory-aadconnectsync-feature-scheduler.md) chování.

**Opravené problémy:**

* Na stránce domén DNS ověřte, zda vždy nerozpozná domény.
* Vyzve k zadání přihlašovacích údajů správce domény při konfiguraci služby AD FS.
* Místní účty AD nejsou rozpoznány pomocí Průvodce instalací, pokud je umístěný v doméně s jiného stromu DNS než kořenové domény.

## <a name="1091310"></a>1.0.9131.0
Vydáno: Prosinec 2015

**Opravené problémy:**

* Synchronizace hesla nemusí fungovat po změně hesla v Active Directory Domain Services (AD DS), ale funguje při nastavování hesla.
* Pokud máte proxy server, může selhat ověřování do služby Azure AD během instalace, nebo pokud se zruší upgrade na stránce konfigurace.
* Aktualizace z předchozí verze služby Azure AD Connect s plnou instanci systému SQL Server selže, pokud si nejste správce systému serveru SQL (SA).
* Aktualizace z předchozí verze služby Azure AD Connect se vzdáleným SQL serverem, zobrazí se chyba "Nelze získat přístup k ADSync SQL databáze".

## <a name="1091250"></a>1.0.9125.0
Vydáno: Listopad 2015

**Nové funkce:**

* Můžete změnit konfiguraci služby AD FS pro vztah důvěryhodnosti Azure AD.
* Můžete aktualizovat schéma služby Active Directory a znovu je obnovovat synchronizační pravidla.
* Pravidlo synchronizace můžete zakázat.
* Můžete definovat "AuthoritativeNull" jako nové literál v synchronizační pravidlo.

**Nové funkce ve verzi preview:**

* [Azure AD Connect Health pro synchronizaci](../connect-health/active-directory-aadconnect-health-sync.md).
* Podpora pro [Azure AD Domain Services](../user-help/active-directory-passwords-update-your-own-password.md) synchronizace hesel.

**Nové Podporované scénáře:**

* Podporuje více organizací v místním systému Exchange. Další informace najdete v tématu [hybridní nasazení s více doménovými strukturami služby Active Directory](https://technet.microsoft.com/library/jj873754.aspx).

**Opravené problémy:**

* Problémy s heslem synchronizace:
  * Jeho synchronizovaným heslem nebudou mít objektu přesunout z mimo obor v oboru. To zahrnuje organizační jednotky a filtrování atributů.
  * Výběr nové organizační jednotky zahrnout synchronizované nevyžaduje úplnou synchronizaci.
  * Pokud je povolena zakázaný uživatel heslo není synchronizovaná.
  * Opakování fronty hesel je nekonečno a předchozí limit 5 000 objektů, které vyřadí z provozu se odebral.
* Nebylo možné se připojit ke službě Active Directory s úrovní funkčnosti doménové struktury Windows serveru 2016.
* Nebylo možné změnit skupinu, která slouží k filtrování skupin po počáteční instalaci.
* Už na serveru služby Azure AD Connect pro každého uživatele to změna hesla se zpětným zápisem hesla povolené vytvoří nový uživatelský profil.
* Není možné používat dlouhé celé číslo hodnoty pravidla synchronizace obory.
* Pokud jsou řadiče domény nedostupný zůstane zakázaná zaškrtávací políčko "zpětný zápis zařízení".

## <a name="1086670"></a>1.0.8667.0
Vydáno: Srpen 2015

**Nové funkce:**

* Průvodce instalací Azure AD Connect je nyní lokalizována pro všechny jazyky Windows serveru.
* Přidání podpory pro účet odemknout při použití Správa hesel služby Azure AD.

**Opravené problémy:**

* Průvodce instalací Azure AD Connect dojde k chybě, pokud instalace spíše než ten, kdo prvním spuštění instalace bude pokračovat jiným uživatelem.
* Pokud se předchozí odinstalace služby Azure AD Connect nepodaří čisté odinstalaci synchronizace Azure AD Connect, není možné znovu nainstalovat.
* Nelze nainstalovat Azure AD Connect s použitím Expresní instalace, pokud uživatel není v kořenové doméně doménové struktury, nebo pokud se používá jiné než anglické jazykové verzi služby Active Directory.
* Pokud nelze přeložit plně kvalifikovaný název domény uživatelského účtu služby Active Directory, se zobrazí zavádějící chybová zpráva "Nepodařilo se zapsat schéma".
* Pokud účet použitý v konektoru služby Active Directory je změněné mimo průvodce, průvodce selže při dalším spuštění.
* Azure AD Connect se někdy nezdaří nainstalovat na řadič domény.
* Nelze povolit nebo zakázat "Pracovní režim", pokud byly přidány atributy rozšíření.
* Zpětný zápis hesla je v některých konfiguracích selže z důvodu nesprávné heslo na konektoru služby Active Directory.
* DirSync nejde upgradovat, pokud se používá rozlišující název (DN) při filtrování atributů.
* Při resetování hesla pomocí nadbytečnému využívání procesoru.

**Funkce odebrané ve verzi preview:**

* Funkce ve verzi preview [zpětný zápis uživatelů](active-directory-aadconnect-feature-preview.md#user-writeback) dočasně byla odebrána na základě zpětné vazby od zákazníků ve verzi preview. Přidá se později po jsme vyřešili zadané zpětné vazby.

## <a name="1086410"></a>1.0.8641.0
Vydáno: Červen 2015

**Počáteční verze služby Azure AD Connect.**

Změněný název ze služby Azure AD Sync na Azure AD Connect.

**Nové funkce:**

* [Expresní nastavení](active-directory-aadconnect-get-started-express.md) instalace
* Můžete [konfigurace služby AD FS](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs)
* Můžete [upgrade z nástroje DirSync](active-directory-aadconnect-dirsync-upgrade-get-started.md)
* [Prevence náhodného odstranění](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)
* Zavedená [pracovní režim](active-directory-aadconnectsync-operations.md#staging-mode)

**Nové funkce ve verzi preview:**

* [Zpětný zápis uživatelů](active-directory-aadconnect-feature-preview.md#user-writeback)
* [Zpětný zápis skupin](active-directory-aadconnect-feature-preview.md#group-writeback)
* [Zpětný zápis zařízení](active-directory-aadconnect-feature-device-writeback.md)
* [Rozšíření adresáře](active-directory-aadconnect-feature-preview.md)

## <a name="104940501"></a>1.0.494.0501
Vydáno: Květen 2015

**Nový požadavek:**

* Synchronizace služby Azure AD teď vyžaduje rozhraní .NET Framework verze 4.5.1 k instalaci.

**Opravené problémy:**

* Zpětný zápis hesla ze služby Azure AD se selháním kvůli chybě připojení k Azure Service Bus.

## <a name="104910413"></a>1.0.491.0413
Vydáno: Duben 2015

**Oprava problémů a vylepšení:**

* Konektor služby Active Directory nezpracovává odstraní správně, pokud je povolený Koš a existuje více domén v doménové struktuře.
* Vylepšili jsme výkon operací importu pro konektor Azure Active Directory.
* Když skupiny překročil limit členství (ve výchozím nastavení, limit nastavený na 50 000 objekty), skupina byla odstraněna v Azure Active Directory. Díky novému chování skupina se neodstraní, je vržena chyba a nové změny členství se nebudou exportovat.
* Nový objekt nejde zřídit, je-li fázované odstranění s využitím stejné rozlišující název už existuje v prostoru konektoru.
* Některé objekty jsou označeny pro synchronizaci během synchronizace delta, i když není žádná změna připravené na objekt.
* Vynucení synchronizace hesel se také odebere seznamu upřednostňovaný řadič domény.
* CSExportAnalyzer má problémy se některé stavy objektů.

**Nové funkce:**

* Spojení se můžete připojit na typ objektu "ANY" v MV.

## <a name="104850222"></a>1.0.485.0222
Vydáno: Únor 2015

**Vylepšení:**

* Import lepší výkon.

**Opravené problémy:**

* Synchronizace hesla respektuje cloudFiltered atribut, který používá filtrování atributů. Filtrované objekty už nejsou v oboru pro synchronizaci hesel.
* Ve výjimečných případech, kde topologie má mnoho řadičů domény synchronizace hesel nefunguje.
* "Zastavena server" při importu z konektoru Azure AD po správy zařízení je povolen v Azure AD a Intune.
* Cizí objekty zabezpečení (FSP) z několika domén ve stejné doménové struktuře připojení způsobí chybu nejednoznačný spojení.

## <a name="104751202"></a>1.0.475.1202
Vydáno: Prosinec 2014

**Nové funkce:**

* Synchronizace hesel pomocí filtrování podle atributů se teď podporuje. Další informace najdete v tématu [synchronizace hesel pomocí filtrování](active-directory-aadconnectsync-configure-filtering.md).
* Atribut msDS-ExternalDirectoryObjectID se zapíšou zpátky do služby Active Directory. Tato funkce přidává podporu pro aplikace Office 365. Přístup k Online a On-Premises poštovní schránky v hybridním nasazení systému Exchange pomocí OAuth2.

**Oprava problémů s upgradem:**

* Novější verzi Pomocníka pro přihlášení je k dispozici na serveru.
* Vlastní instalační cesta se používá k instalaci Azure AD Sync.
* Neplatný vlastní spojení kritérium blokuje upgradu.

**Ostatní opravy:**

* Opravili jsme šablony pro Office Pro Plus.
* Oprava instalace potíže způsobené službou uživatelská jména, které začínají pomlčkou.
* Opravili jsme ztrátě sourceAnchor nastavení, když spustíte Průvodce instalací podruhé.
* Oprava trasování událostí pro Windows pro synchronizaci hesel.

## <a name="104701023"></a>1.0.470.1023
Vydáno: Říjen 2014

**Nové funkce:**

* Synchronizace hesel z několika místní služby Active Directory do služby Azure AD.
* Lokalizované instalace uživatelského rozhraní pro všechny jazyky Windows serveru.

**Upgrade z verze GA AADSync 1.0**

Pokud již máte nainstalované Azure AD Sync, je další krok, který je třeba provést v případě, že jste změnili některý pravidel synchronizace out-of-box. Po upgradu na 1.0.470.1023 uvolněte, synchronizace, které jsou duplicitní pravidla, které jste upravili. Pro každé pravidlo upravené synchronizace postupujte takto:

1.  Vyhledejte pravidlo synchronizace byly upraveny a poznamenejte si změny.
* Odstraníte synchronizační pravidlo.
* Vyhledejte nové pravidlo synchronizace, který je vytvořen pomocí Azure AD Sync a pak znovu použít změny.

**Oprávnění pro účet služby Active Directory**

Účet služby Active Directory musí udělit další oprávnění, aby mohli číst hodnoty hash hesel z Active Directory. Udělení oprávnění jsou s názvem "Replikace změn adresáře" a "Replikuje adresář změní všechny." Obě oprávnění jsou vyžadována bude moct číst hodnoty hash hesel.

## <a name="104190911"></a>1.0.419.0911
Vydáno: Září 2014

**Počáteční verze služby Azure AD Sync.**

## <a name="next-steps"></a>Další postup
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](active-directory-aadconnect.md).

---
title: 'Azure AD Connect: Historie verzí | Microsoft Docs'
description: Tento článek obsahuje seznam všech verzích Azure AD Connect a Azure AD Sync
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
ms.date: 05/07/2018
ms.author: billmath
ms.openlocfilehash: 4d5bd28f6e2831ef7bcecc6e5cb80cb28736ec27
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="azure-ad-connect-version-release-history"></a>Azure AD Connect: Historie verzí
Tým služby Azure Active Directory (Azure AD) pravidelně aktualizuje Azure AD Connect s novými funkcemi a funkce. Ne všechny dodatky platí pro všechny cílové skupiny.


Tento článek je určen, abyste mohli sledovat verze, které byly vydány a zjistit, jestli je potřeba aktualizovat na nejnovější verzi, nebo ne.

Toto je seznam Příbuzná témata:

Téma |  Podrobnosti
--------- | --------- |
Kroky pro upgrade z Azure AD Connect | Různých způsobů [upgrade z předchozí verze na nejnovější](active-directory-aadconnect-upgrade-previous-version.md) verzi Azure AD Connect.
Požadovaná oprávnění | Oprávnění potřebná k použití aktualizace, najdete v části [účty a oprávnění](./active-directory-aadconnect-accounts-permissions.md#upgrade).

Stáhněte si | [Stažení Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771).

## <a name="118190"></a>1.1.819.0

5, 4 nebo 2018: vydání pro automatický upgrade, bude brzy k dispozici ke stažení.



### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení

Nové funkce a vylepšení


- Tato verze zahrnuje integraci PingFederate verzi public preview ve službě Azure AD Connect. V této verzi můžete snadno zákazníky a spolehlivé konfigurace prostředí Azure Active Directory tak, aby využívala PingFederate jako jejich poskytovatel federace. Další informace o tom, jak použít tuto novou funkci, navštivte naše [online dokumentaci](active-directory-aadconnect-user-signin.md#federation-with-pingfederate). 
- Aktualizovali jsme Azure AD Connect Průvodce Poradce při potížích, kde budeme analyzovat teď další chyby scénáře, jako jsou propojená poštovní schránky a dynamických skupin AD. Další informace o odstraňování potíží nástroje [zde](active-directory-aadconnect-troubleshoot-objectsync.md).
- Konfigurace zpětný zápis zařízení je nyní spravují výhradně v rámci Azure AD Connect průvodce.
- Nové prostředí PowerShell modul volané ADSyncTools.psm1 se přidá, kterého chcete-li vyřešit potíže s připojením k SQL a různé další nástroje řešení potíží. Další informace o modulu ADSyncTools [zde](active-directory-aadconnect-tshoot-sql-connectivity.md). 
- Byla přidána nová úloha další "Možnosti konfigurace zařízení". Úlohu můžete nakonfigurovat dvě následující operace: 
    -   **Hybridní Azure AD join**: Pokud vaše prostředí disponuje místní AD nároky a vy chcete také výhody z možnosti poskytované službou Azure Active Directory, můžete implementovat hybridní Azure AD, které jsou připojené k zařízení. Jedná se o zařízení, které jsou obě, připojený k místní službě Active Directory a Azure Active Directory.
    -   **Zpětný zápis zařízení**: zpětný zápis zařízení slouží k povolení podmíněného přístupu podle zařízení do služby AD FS (2012 R2 nebo vyšší) chráněný zařízení

   >[!NOTE] 
   > - Možnost povolit zpětný zápis zařízení z přizpůsobit možnosti synchronizace bude aktivní. 
   > -  V této verzi je zastaralý modul PowerShell nástroje ADPrep.



### <a name="fixed-issues"></a>Opravené problémy 


- Synchronizace zpracování pravidla: odchozí pravidla synchronizace spojení s podmínkou žádné připojení musí být zrušte použité, pokud syncrule nadřazené není nadále vhodné
- Azure AD Connect průvodce: Chyba při vytváření účtu AD Connector. Pokud Azure AD Connect je v pracovní skupině.
- Azure AD Connect průvodce: Na Azure AD přihlašovací stránka zobrazí políčka ověření vždy, když je jakákoli Neshoda v doménami AD a Azure AD ověřeno domén
- Automatický upgrade prostředí PowerShell oprava správně nastavené automatické aktualizace stavu v některých případech po pokusu o automatický upgrade.
- Azure AD Connect průvodce: Aktualizovat telemetrie k zachycení dříve chybějící informace o
- Azure AD Connect průvodce: PTA nainstalovat agenta před převedením domény spravovat
- Azure AD Connect průvodce: Nepřevádějí uživatelé spravovaných (převést pouze doména) pro PTA
- Azure AD Connect průvodce: AD FS více domény výraz Regex není správný když má uživatele (UPN) se aktualizace Regex speciální znak pro podporu speciální znaky
- Azure AD Connect průvodce: Odeberte nesprávné zpráva "Konfigurace zdrojové ukotvení atribut", pokud žádná změna 
- Connect Azure AD: Služby AD FS podpora průvodce Pro scénář duální federace
- Azure AD Connect průvodce: AD FS deklarace identity se neaktualizují pro přidání domény při převodu spravované doméně federovaný
- Azure AD Connect průvodce: Během zjišťování nainstalované balíčky, se nám najít zastaralé Dirsync nebo Azure AD Sync nebo Azure AD Connect související produkty. Nemůžeme se nyní pokusí odinstalovat zastaralé produkty.
- Azure AD Connect průvodce: Správný chyba zpráva mapování při selhání instalace agenta průchozí ověřování
- Azure AD Connect průvodce: Odebrat kontejner "Konfigurace" z filtrování organizační jednotky domény stránky
- Nainstalovat synchronizační modul: odeberte nepotřebné starší verze logiky, která příležitostně se nezdařilo z instalace msi synchronizačního modulu
- Azure AD Connect průvodce: Oprava místní Nápověda text na stránce volitelné funkce synchronizace hodnot Hash hesel
- Synchronizovat modulu runtime: Opravte scénáři, kde má objekt CS importované odstranit a synchronizační pravidla se pokusí znovu zřídit objektu.
- Synchronizovat modulu runtime: nápovědu přidat odkaz na Online připojení k řešení potíží s průvodce do protokolu událostí pro chybu Import
- Synchronizovat modulu runtime: omezení využití paměti plánovače synchronizace při vytváření výčtu konektory
- Azure AD Connect průvodce: Opravte problém řešení vlastní účet služby synchronizace, který má oprávnění pro čtení AD
- Azure AD Connect průvodce: Zlepšení protokolování domény a organizační jednotky filtrování výběr
- Azure AD Connect průvodce: AD FS přidat výchozí deklarací pro vztah důvěryhodnosti federace vytvořit pro scénář vícefaktorového ověřování
- Azure AD Connect průvodce: AD FS nasadit WAP: Přidání serveru nepodaří pomocí nového certifikátu
- Azure AD Connect průvodce: DSSO výjimka při onPremCredentials nejsou inicializovány pro doménu 
- Přednostně toku atributů distinguishedName AD z objektu aktivního uživatele.
- Pevné kosmetické chyb, kde byl nastaven prioritu první pravidlo synchronizace OOB na 99 místo 100



## <a name="117510"></a>1.1.751.0
Stav 4/12/2018: vydané pouze ke stažení

>[!NOTE]
>Toto je oprava hotfix pro Azure AD Connect

### <a name="azure-ad-connect-sync"></a>Synchronizace služby Azure AD Connect
#### <a name="fixed-issues"></a>Opravené problémy
Opravit chyby, kde došlo k zjišťování instance automatické Azure pro klienty Číně příležitostně selhání.  

### <a name="ad-fs-management"></a>Správa služby AD FS
#### <a name="fixed-issues"></a>Opravené problémy

Došlo k potížím s logikou opakování konfigurace, která by způsobilo ArgumentException – s oznámením "položku se stejným klíčem již byla přidána."  To by způsobilo selhání všech operací opakování.

## <a name="117500"></a>1.1.750.0
Stav 3/22/2018: vydané pro automatický upgrade a stahování.
>[!NOTE]
>Po dokončení upgradu na tuto novou verzi, automaticky spustí úplné synchronizace a úplný import pro konektor Azure AD a úplné synchronizace konektoru služby AD. Vzhledem k tomu, že to může trvat delší dobu, v závislosti na velikosti vašeho prostředí Azure AD Connect, ujistěte se, že jste udělali potřebné kroky pro podporu to nebo zdržovat upgrade, dokud jste našli pohodlný chvíli to udělat.

>[!NOTE]
>"AutoUpgrade funkce nesprávně zakázal pro některé klienty, kteří později než 1.1.524.0 nasazenou sestavení. K zajištění, že vaší instanci Azure AD Connect je stále možné AutoUpgrade, spusťte následující rutinu prostředí PowerShell: "Set ADSyncAutoUpgrade - AutoupGradeState povoleno"


### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Opravené problémy

* Rutiny Set-ADSyncAutoUpgrade by dříve blokovat Autoupgrade, pokud automatický upgrade je nastavena do stavu pozastaveno. To je nyní změněno tak, že neblokuje AutoUpgrade budoucí sestavení.
* Změnit **přihlášení uživatele** stránky možnost "Synchronizace hesel" do "Synchronizaci hodnoty Hash hesla".  Azure AD Connect synchronizuje hodnot hash hesel, není hesla, takže to zarovnaná s co dochází ve skutečnosti.  Další informace najdete v části [implementovat synchronizaci hodnoty hash hesla s synchronizace Azure AD Connect](active-directory-aadconnectsync-implement-password-hash-synchronization.md)

## <a name="117490"></a>1.1.749.0
Stav: Vydané vyberte zákazníků

>[!NOTE]
>Po dokončení upgradu na tuto novou verzi, automaticky spustí úplné synchronizace a úplný import pro konektor Azure AD a úplné synchronizace konektoru služby AD. Vzhledem k tomu, že to může trvat delší dobu, v závislosti na velikosti vašeho prostředí Azure AD Connect, zkontrolujte, že jste udělali potřebné kroky pro podporu to nebo zdržovat upgrade, dokud jste našli pohodlný chvíli to udělat.

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Opravené problémy
* Vyřešte časové okno na úlohy na pozadí pro oddíl filtrování stránku při přechodu na další stránku.

* Opravit chyby, která způsobila narušení přístupu během ConfigDB vlastní akci.

* Opravit chyby obnovení z časový limit připojení SQL.

* Opravit chyby, ve kterých selhaly certifikáty se zástupnými znaky SAN kontrolu požadovaných součástí.

* Opravit chyby, což způsobí, že miiserver.exe chyby při exportu konektor služby Azure AD.

* Pevné chyby zaznamenán které pokus nesprávné heslo na řadiči domény při spuštění Průvodce Azure AD Connect a změňte konfiguraci.


#### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení

* Přidání nastavení ochrany osobních údajů pro nařízení ochrany dat obecné (GDPR).  Pro GDPR jsou požadované označíte druhy zákaznická data, která jsou sdílet se společností Microsoft (telemetrie, stavu atd.), mají odkazy na podrobná online dokumentaci a poskytují způsob, jak naše zákazníky změna jejich předvoleb.  Tato změnami přidává tyto funkce:


    - Sdílení dat a oznámení o ochraně osobních údajů na čistou instalaci stránky se smlouvou EULA.
    - Data oznámení na stránce upgradu sdílení a ochrana osobních údajů.
    - Nový další úkol "Nastavení ochrany osobních údajů" kde si uživatel může změnit jeho/jejich předvoleb.

* aplikace telemetrie – správce můžete přepnout Tato třída dat zapnutí nebo vypnutí chtít

* Data na Azure AD Health – správce musí navštívit portál health k řízení jejich nastavení stavu.
   Jakmile změníte zásady služby agenty přečte a vynutit ho.

* Přidané zařízení zpětný zápis akcí konfigurace a indikátor průběhu inicializace stránky

* Obecné diagnostické vylepšuje sestavu ve formátu HTML a shromažďování dat úplné ZIP textu nebo sestavu ve formátu HTML

* Vylepšení spolehlivosti automatický upgrade a přidané další telemetrické zajistit, že se dá určit stav serveru

* Omezit oprávnění k dispozici k privilegovaným účtům na účet AD Connector.

  * Pro nové instalace, bude průvodce omezit oprávnění, která privilegované účty po vytvoření účtu MSOL mít na účet MSOL.

Změny se postará o následující:
1. Expresní instalace
2. Vlastní instalace s automatické vytvoření účtu
3. Změnit instalační program, není třeba oprávnění správce systému na čistou instalaci Azure AD Connect

* Přidat nový nástroj pro řešení potíží se synchronizací pro konkrétní objekt. Je k dispozici v části Možnosti řešení potíží s objekt synchronizace Azure AD Connect Průvodce řešení potíží s další úlohy. V současné době tento nástroj kontroluje následující:

  * UserPrincipalName Neshoda mezi objekt synchronizované uživatele a uživatelský účet v klientovi Azure AD.
  * Pokud objekt je filtrován z synchronizace kvůli filtrování domény
  * Pokud je objekt filtrován z synchronizace z důvodu filtrování organizační jednotky (OU)

* Přidat nový nástroj pro synchronizaci aktuální hodnoty hash hesla uložená ve službě Active Directory místní pro konkrétní uživatelský účet.

Nástroj nevyžaduje, aby změnu hesla. Je k dispozici v části 'Řešení potíží s synchronizaci hodnoty Hash hesla' možnost Azure AD Connect Průvodce řešení potíží s další úlohy.






## <a name="116540"></a>1.1.654.0
Stav: 12. prosince 2017

>[!NOTE]
>Toto je zabezpečení související oprava hotfix pro Azure AD Connect

### <a name="azure-ad-connect"></a>Azure AD Connect
Zlepšení přidala do Azure AD Connect verze 1.1.654.0 (i po) ujistěte se, že se změní popsané v části Doporučená oprávnění [uzamčení přístup k účtu služby AD DS](#lock) budou automaticky použita při Azure AD Připojení vytvoří účet služby AD DS. 

- Při nastavování Azure AD Connect, instalaci správce můžete zadat existující účet služby AD DS, nebo nechat automaticky vytvořit účet Azure AD Connect. Změny oprávnění budou automaticky použita pro účet služby AD DS, který se vytvoří Azure AD Connect během instalace. Nepoužívají se existující účet služby AD DS od instalaci správce.
- Pro zákazníky, kteří upgradovali ze starší verze služby Azure AD Connect na 1.1.654.0 (nebo po) oprávnění změny se nepoužije zpětně do existující účty služby AD DS vytvořené před upgradem. Se použijí jenom k nové účty služby AD DS vytvořené po upgradu. K tomu dochází, když přidáváte nové doménové struktury AD synchronizovány se službou Azure AD.

>[!NOTE]
>Tato verze pouze odebere ohrožení zabezpečení pro nové instalace služby Azure AD Connect, kde se má vytvořit účet služby procesem instalace. Pro existující instalace, nebo v případech, kde můžete vytvořit účet sami měli byste zajistit, že toto ohrožení zabezpečení neexistuje.

#### <a name="lock"></a> Zablokovat přístup k účtu služby AD DS
Uzamčení přístup k účtu služby AD DS implementací následující oprávnění změny v místní AD:  

*   Zakázat dědičnost na zadaný objekt
*   Odeberte všechny položky řízení přístupu pro daný objekt, s výjimkou položky řízení přístupu konkrétním do sebe sama. Chceme zachovat výchozí oprávnění při přechodu do sebe sama.
*   Přiřadíte tato konkrétní oprávnění:

Typ     | Název                          | Access               | Platí pro
---------|-------------------------------|----------------------|--------------|
Povolit    | SYSTÉM                        | Úplné řízení         | Tento objekt  |
Povolit    | Enterprise Admins             | Úplné řízení         | Tento objekt  |
Povolit    | Domain Admins                 | Úplné řízení         | Tento objekt  |
Povolit    | Správci                | Úplné řízení         | Tento objekt  |
Povolit    | Enterprise Domain Controllers | Zobrazovat obsah        | Tento objekt  |
Povolit    | Enterprise Domain Controllers | Číst všechny vlastnosti  | Tento objekt  |
Povolit    | Enterprise Domain Controllers | Oprávnění ke čtení     | Tento objekt  |
Povolit    | Ověření uživatelé           | Zobrazovat obsah        | Tento objekt  |
Povolit    | Ověření uživatelé           | Číst všechny vlastnosti  | Tento objekt  |
Povolit    | Ověření uživatelé           | Oprávnění ke čtení     | Tento objekt  |

Můžete spustit, abyste posílili nastavení pro účet služby AD DS [tento skript prostředí PowerShell](https://gallery.technet.microsoft.com/Prepare-Active-Directory-ef20d978). Skript PowerShell bude přiřadit oprávnění zmíněné k účtu služby AD DS.

#### <a name="powershell-script-to-tighten-a-pre-existing-service-account"></a>Skript prostředí PowerShell, abyste posílili existující účet služby

Chcete-li toto nastavení použít pro existující účet služby AD DS pomocí skriptu prostředí PowerShell, (ether poskytnutá vaší organizací nebo vytvořený předchozí instalace služby Azure AD Connect, stáhněte si prosím skript z výše zadaný odkaz.

##### <a name="usage"></a>Použití:

```powershell
Set-ADSyncRestrictedPermissions -ObjectDN <$ObjectDN> -Credential <$Credential>
```

Kde 

**$ObjectDN** = účet služby Active Directory, jehož oprávnění musí lze zvýšit.

**$Credential** = přihlašovací údaje správce, který má potřebná oprávnění ke omezit oprávnění pro účet $ObjectDN. Toto je obvykle správce rozlehlé sítě nebo domény. Aby se zabránilo selhání vyhledávání účtu použijte plně kvalifikovaný název domény účtu správce. Příklad: contoso.com\admin.

>[!NOTE] 
>$credential. Uživatelské jméno musí být ve formátu FQDN\username. Příklad: contoso.com\admin 

##### <a name="example"></a>Příklad:

```powershell
Set-ADSyncRestrictedPermissions -ObjectDN "CN=TestAccount1,CN=Users,DC=bvtadwbackdc,DC=com" -Credential $credential 
```
### <a name="was-this-vulnerability-used-to-gain-unauthorized-access"></a>Bylo toto ohrožení zabezpečení použít k získání neoprávněného přístupu?

Pokud chcete zobrazit, pokud je toto ohrožení zabezpečení byl použit ohrozit vaši službu Azure AD Connect konfigurace by měl ověřit poslední heslo resetovat datum účtu služby.  Pokud časové razítko v neočekávané, další šetření, prostřednictvím protokolu událostí, mělo být provedeno pro toto heslo resetovat událostí,.

Další informace najdete v tématu [4056318 informační zpravodaj zabezpečení společnosti Microsoft](https://technet.microsoft.com/library/security/4056318)

## <a name="116490"></a>1.1.649.0
Stav: 27 říjen 2017

>[!NOTE]
>Toto sestavení není k dispozici zákazníkům prostřednictvím funkce Azure AD Connect automaticky upgradovat.

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issue"></a>Opravené problém
* Opravit potíže s kompatibilitou verze mezi Azure AD Connect a Agent Azure AD Connect Health (pro synchronizaci). Tento problém ovlivňuje zákazníky, kteří jsou provádění Azure AD Connect místní upgrade na verzi 1.1.647.0, ale verze 3.0.127.0 stavu agenta v současnosti má. Po upgradu agenta stavu už odesílat data o stavu týkající se služby Azure AD Connect synchronizace služby Azure AD Health. Pomocí této opravy je nainstalován Agent stavu verze 3.0.129.0 během Azure AD Connect místní upgrade. Agent stavu verze 3.0.129.0 nemá potíže s kompatibilitou s Azure AD Connect verze 1.1.649.0.


## <a name="116470"></a>1.1.647.0
Stav: 19 říjen 2017

> [!IMPORTANT]
> Nastane problém s kompatibilitou mezi Azure AD Connect 1.1.647.0 a verze agenta Azure AD Connect Health (pro synchronizaci) 3.0.127.0. Tento problém brání odesílá data o stavu týkající se Azure AD Connect synchronizace služby (včetně chyb synchronizace objektů a dat historie spouštění) do služby Azure AD Health Agent stavu. Před nasazením Azure AD Connect ručně upgrade na verzi 1.1.647.0, zkontrolujte, zda že aktuální verzi agenta Azure AD Connect Health nainstalovali na server Azure AD Connect. Můžete to provést tak, že přejdete do *→ Přidat odebrat programy v Ovládacích panelech* a vyhledejte aplikaci *Microsoft Azure agenta AD Connect Health pro synchronizaci*. Pokud je jeho verze 3.0.127.0, se doporučuje počkejte na další verzi Azure AD Connect být k dispozici před provedením upgradu. Není-li verze agenta stavu 3.0.127.0, je v pořádku pokračovat v místě, ruční upgrade. Všimněte si, že tento problém nemá vliv na dráha upgradu nebo zákazníci, kteří jsou provedení nové instalace služby Azure AD Connect.
>
>

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Opravené problémy
* Byl opraven problém s *změnit přihlášení uživatele* úloha v průvodce Azure AD Connect:

  * Tomuto problému dochází, pokud máte existující nasazení Azure AD Connect se synchronizace hesel **povoleno**, a chcete nastavit přihlašovací metodu uživatele jako *předávací ověřování*. Před použitím změn, Průvodce nesprávně ukazuje "*zakázat synchronizaci hesel*" řádku. Synchronizace hesel byla však povolena po použití této změny. S tuto opravu už Průvodce zobrazí výzvu.

  * Návrh průvodce nezakazovat synchronizace hesel při aktualizaci metoda přihlašování uživatele pomocí *změnit přihlášení uživatele* úloh. Toto je chcete zabránit přerušení pro zákazníky, kteří chtějí udržet synchronizace hesel, i když povolit předávací ověřování nebo federování jako metoda jejich přihlašování primárního uživatele.
  
  * Pokud chcete zakázat synchronizaci hesel po aktualizaci metoda přihlašování uživatele, je třeba spustit *přizpůsobení konfigurace synchronizace* úloh v průvodci. Když přejdete *volitelné funkce* stránky, zrušte zaškrtnutí políčka *synchronizace hesel* možnost.
  
  * Všimněte si, že stejný problém také nastat, pokud se pokusíte povolit nebo zakázat bezproblémové jednotné přihlašování. Konkrétně máte stávajícího nasazení Azure AD Connect se synchronizace hesel, které jsou povolené a metoda přihlašování uživatele je už nakonfigurovaný jako *předávací ověřování*. Pomocí *změnit přihlášení uživatele* úloh, pokusíte zaškrtněte nebo zrušte zaškrtnutí políčka *povolit bezproblémové jednotné přihlašování* možnost při současném zachování metoda přihlašování uživatelů nakonfigurovanou jako "Předávací ověřování". Před použitím změn, Průvodce nesprávně ukazuje "*zakázat synchronizaci hesel*" řádku. Synchronizace hesel byla však povolena po použití této změny. S tuto opravu už Průvodce zobrazí výzvu.

* Byl opraven problém s *změnit přihlášení uživatele* úloha v průvodce Azure AD Connect:

   * Tomuto problému dochází, pokud máte existující nasazení Azure AD Connect se synchronizace hesel **zakázáno**, a chcete nastavit přihlašovací metodu uživatele jako *předávací ověřování*. Při použití této změny Průvodce umožňuje předávací ověřování a synchronizace hesel. S tuto opravu už Průvodce umožňuje synchronizaci hesel.

  * Synchronizace hesel byla dřív, nezbytné pro povolení předávací ověřování. Když nastavíte přihlašovací metodu uživatele jako *předávací ověřování*, průvodce by povolit předávací ověřování a synchronizace hesel. Nedávno synchronizace hesel se odebral jako předpoklad. V rámci služby Azure AD Connect verze 1.1.557.0 byla provedena změna na Azure AD Connect není povolení synchronizace hesel při nastavení přihlášení metodu uživatele jako *předávací ověřování*. Ale změny se použijí jenom pro instalace služby Azure AD Connect. Pomocí této opravy stejnou změnu platí také pro *změnit přihlášení uživatele* úloh.
  
  * Všimněte si, že stejný problém také nastat, pokud se pokusíte povolit nebo zakázat bezproblémové jednotné přihlašování. Konkrétně máte existující nasazení Azure AD Connect se synchronizace hesel zakázána a metoda přihlašování uživatele je už nakonfigurovaný jako *předávací ověřování*. Pomocí *změnit přihlášení uživatele* úloh, pokusíte zaškrtněte nebo zrušte zaškrtnutí políčka *povolit bezproblémové jednotné přihlašování* možnost při současném zachování metoda přihlašování uživatelů nakonfigurovanou jako "Předávací ověřování". Při použití této změny Průvodce umožňuje synchronizaci hesel. S tuto opravu už Průvodce umožňuje synchronizaci hesel. 

* Byl opraven problém způsobující selhání upgradu Azure AD Connect s chybou "*upgrade synchronizační služby se nepodařilo*". Navíc synchronizační službu může už začínat Chyba události "*službu se nepodařilo spustit, protože verze databáze je novější než verze binárních souborů nainstalovaná*". K tomuto problému dochází při provádění upgradu správce nemá oprávnění správce k systému SQL server, který se používá Azure AD Connect. S tuto opravu Azure AD Connect vyžaduje pouze správce, aby oprávnění db_owner k databázi ADSync během upgradu.

* Byl opraven problém ovlivňující zákazníci, kteří mají povoleno Azure AD Connect Upgrade [bezproblémové jednotné přihlašování](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). Po upgradu Azure AD Connect bezproblémové jednotné přihlašování se nesprávně zobrazí jako zakázané v průvodce Azure AD Connect, i když tato funkce zůstane povolené a plně funkční. S tuto opravu tato funkce se teď zobrazí správně povolené v průvodci.

* Byl opraven problém způsobující Průvodce Azure AD Connect na vždy zobrazit "*konfigurace zdrojové ukotvení*" výzvy na *připraveni konfigurovat* stránky, i v případě, že nebyly provedeny žádné změny související s zdrojové ukotvení.

* Při provádění ruční místní upgrade služby Azure AD Connect, zákazník je potřeba zadat přihlašovací údaje odpovídající klienta Azure AD globálního správce. Dříve, upgrade může pokračovat i v případě, že zadaná pověření globální správce patří do různých Azure AD klienta. Při upgradu se zobrazí úspěšně dokončil, nejsou správně trvalé některých konfiguracích v upgradu. Díky této změně neumožní Průvodce upgradu pokračovat, pokud zadané přihlašovací údaje se neshodují klienta Azure AD.

* Odebrat redundantní logiky, která zbytečně restartování služby Azure AD Connect Health na začátku ruční upgrade.


#### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení
* Přidané logiku pro zjednodušení kroky potřebné k nastavení plánu Germany cloudu Microsoft Azure AD Connect. Dříve je nutné aktualizovat určité klíče registru na serveru Azure AD Connect mohla správně spolupracovaly s nástrojem Microsoft Cloud Německo, jak je popsáno v tomto článku. Nyní Azure AD Connect můžete automaticky rozpoznat, pokud váš klient je v Německu cloudu Microsoft na základě údajů globálního správce zadané během instalace.

### <a name="azure-ad-connect-sync"></a>Synchronizace služby Azure AD Connect
>[!NOTE]
> Poznámka: Synchronizační služby je rozhraní WMI, které vám umožní vyvíjet vlastní vlastní plánovače. Toto rozhraní je nyní zastaralý a bude odebrána z budoucí verze služby Azure AD Connect dodaný po 30. června 2018. Zákazníci, kteří chtějí přizpůsobit plán synchronizace by měl používat [předdefinované scheduler (https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-scheduler).

#### <a name="fixed-issues"></a>Opravené problémy
* Když průvodce Azure AD Connect vytvoří účet AD konektoru, které jsou potřebné k synchronizaci změn z místní služby Active Directory, nepřiřazuje správně účet potřebná oprávnění k čtení PublicFolder objektů. Tento problém se týká Expresní instalace i instalace vlastní. Tato změna řeší problém.

* Byl opraven problém, který způsobil řešení potíží stránky průvodce Azure AD Connect nezobrazují správně pro správce spouštění ze systému Windows Server 2016.

#### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení
* Při řešení potíží s synchronizace hesel pomocí Průvodce Azure AD Connect, řešení potíží s stránky, řešení potíží stránky nyní vrátí stav specifické pro doménu.

* Dříve, pokud jste se pokusili povolit synchronizaci hodnoty Hash hesla, Azure AD Connect neověřuje, zda má účet AD Connector. požadovaná oprávnění k synchronizaci hodnot hash hesel z místní AD. Průvodce Azure AD Connect bude nyní ověřte a varovat, pokud AD Connector. účet nemá dostatečná oprávnění.

### <a name="ad-fs-management"></a>Správa služby AD FS
#### <a name="fixed-issue"></a>Opravené problém
* Byl opraven problém související s použitím [msDS-ConsistencyGuid jako zdrojové ukotvení](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-msds-consistencyguid-as-sourceanchor) funkce. Tento problém ovlivňuje zákazníky, kteří nakonfigurovali *federační službou AD FS* jako metoda přihlašování uživatelů. Při spuštění *konfigurace zdrojové ukotvení* úloh v průvodci, Azure AD Connect se přepne do pomocí * ms-DS-ConsistencyGuid jako zdrojový atribut pro immutableId. V rámci této změny Azure AD Connect pokusí aktualizovat pravidla deklarace identity pro ImmutableId ve službě AD FS. Však tento krok se nezdařil, protože Azure AD Connect neměl přihlašovací údaje správce, které jsou potřeba ke konfiguraci služby AD FS. S tuto opravu, Azure AD Connect teď výzvu k zadání pověření správce pro AD FS při spuštění *konfigurace zdrojové ukotvení* úloh.



## <a name="116140"></a>1.1.614.0
Stav: 05 září 2017

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="known-issues"></a>Známé problémy
* Je známý problém, který je příčinou selhání upgradu Azure AD Connect s chybou "*upgrade synchronizační služby se nepodařilo*". Navíc synchronizační službu může už začínat Chyba události "*službu se nepodařilo spustit, protože verze databáze je novější než verze binárních souborů nainstalovaná*". K tomuto problému dochází při provádění upgradu správce nemá oprávnění správce k systému SQL server, který se používá Azure AD Connect. Oprávnění dbo nestačí.

* Je známý problém s Azure AD Connect upgradu, ovlivňuje zákazníky, kteří mají povoleno [bezproblémové jednotné přihlašování](active-directory-aadconnect-sso.md). Po upgradu Azure AD Connect funkci se zobrazuje jako zakázané v průvodci, i když funkce zůstala povolená. Oprava pro tento problém bude přidána do budoucích vydání. Zákazníci, kteří chtějí mít přehled o tomto problému zobrazení můžete ručně opravu povolením bezproblémové jednotné přihlašování v průvodci.

#### <a name="fixed-issues"></a>Opravené problémy
* Byl opraven problém, která zabránila aktualizaci pravidel deklarací identity v místní služby AD FS při povolování Azure AD Connect [msDS-ConsistencyGuid jako zdrojové ukotvení](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-msds-consistencyguid-as-sourceanchor) funkce. Tomuto problému dochází, pokud se pokusíte povolit funkci pro existující nasazení Azure AD Connect, který má nakonfigurovaný jako metoda přihlašování do služby AD FS. Tomuto problému dochází, protože průvodce výzvy k zadání přihlašovacích údajů služby AD FS před pokusem o aktualizace pravidel deklarací identity v ADFS.
* Byl opraven problém způsobující selhání instalace, pokud Azure AD Connect na místní doménové struktuře Active Directory je zakázán protokol NTLM. Problém je z důvodu Průvodce Azure AD Connect není poskytnout úplný pověření při vytváření kontextu zabezpečení potřebné pro ověřování pomocí protokolu Kerberos. To způsobí, že došlo k chybě ověřování protokolu Kerberos a Průvodce Azure AD Connect na vrátit k použití protokolu NTLM.

### <a name="azure-ad-connect-sync"></a>Synchronizace služby Azure AD Connect
#### <a name="fixed-issues"></a>Opravené problémy
* Opraven problém, kde nelze nové pravidlo synchronizace vytvořit, pokud není vyplněný atribut značky.
* Byl opraven problém, která způsobila, že Azure AD Connect se připojit k místní službě AD kvůli synchronizaci hesel pomocí protokolu NTLM, i když je k dispozici protokolu Kerberos. K tomuto problému dochází, pokud místní topologie AD má jeden nebo více řadičů domény, která byla obnovena ze zálohy.
* Byl opraven problém způsobující postup úplná synchronizace zbytečně vzniknout po upgradu. Obecně platí spuštěna úplná synchronizace kroky vyžádáním po upgradu pokud existují změny out-of-box synchronizační pravidla. Problém se z důvodu chyby v změny detekční logiku, která nesprávně zjistil změnu při zjištění výraz pravidlo synchronizace s znaky nového řádku. Znaky nového řádku jsou vloženy do Výraz pravidla synchronizace ke zlepšení čitelnosti.
* Opravit problém, který může způsobit, že server Azure AD Connect nebude správně fungovat po automatický Upgrade. Tento problém se týká servery Azure AD Connect s verzí 1.1.443.0 (nebo starší). Podrobnosti o problému najdete v článku [Azure AD Connect není správně po automatický upgrade](https://support.microsoft.com/help/4038479/azure-ad-connect-is-not-working-correctly-after-an-automatic-upgrade).
* Opravit problém, který může způsobit automatický Upgrade na opakovat každých 5 minut, když dojde k chybám. Oprava automatický Upgrade opakování s exponenciální back vypnout když dojde k chybám.
* Opraven problém, kde se heslo synchronizace událostí 611 nesprávně zobrazí v protokolech událostí aplikace systému Windows jako **informační** místo **chyba**. Vždy, když se synchronizace hesel zaznamená problém se vyvolá událost 611. 
* V Průvodci Azure AD Connect, který umožňuje zpětný zápis funkce skupiny bez výběru organizační jednotku pro zpětný zápis skupin vyžaduje povolení byl opraven problém.

#### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení
* Přidat úloha Poradce při potížích do Průvodce Azure AD Connect v části Další úlohy. Zákazníci můžou využít této úlohy můžete vyřešit problémy související s synchronizace hesel a shromažďovat obecné diagnostiky. Poradce při potížích úloh bude v budoucnu, rozšířeno zahrnout další problémy související s synchronizace adresáře.
* Volá se nový režim instalace Azure AD Connect nyní podporuje **použít existující databázi**. Tento režim instalace umožňuje zákazníkům nainstalujte Azure AD Connect, který určuje existující databáze ADSync. Další informace o této funkci najdete v článku [použít existující databázi](active-directory-aadconnect-existing-database.md).
* Pro lepší zabezpečení Azure AD Connect nyní ve výchozím nastavení používá TLS1.2 pro připojení k službě Azure AD pro synchronizaci adresářů. Výchozí hodnota byla dřív, TLS1.0.
* Pokud Azure AD Connect Agent synchronizace hesel spustí, pokusí se připojit k dobře známé koncový bod Azure AD pro synchronizaci hesel. Po úspěšném připojení je přesměrován na koncový bod oblast. Agent synchronizace hesel dříve, mezipaměti oblast koncový bod, dokud se nerestartuje. Agenta teď vymaže mezipaměti a opakování s dobře známé koncový bod, pokud zjistí problém připojení ke koncovému bodu oblast. Tato změna zajišťuje, synchronizace hesel můžete převzetí služeb při selhání na jiný koncový bod oblast, když už není dostupný koncový bod mezipaměti oblast.
* Synchronizaci změn z místní doménové struktuře AD, účet služby AD DS je potřeba. Můžete buď (i) vytvořit služby AD DS sami účet a zadejte své přihlašovací údaje Azure AD Connect nebo (ii) zadejte Správce podnikové přihlašovací údaje a umožnit vytvoření účtu služby AD DS pro vás Azure AD Connect. (I) dříve, je výchozí možnost v průvodce službou Azure AD Connect. Nyní (ii) je výchozí možností.

### <a name="azure-ad-connect-health"></a>Azure AD Connect Health

#### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení
* Byla přidána podpora pro Microsoft Azure Government Cloud a Německo cloudu Microsoftu.

### <a name="ad-fs-management"></a>Správa služby AD FS
#### <a name="fixed-issues"></a>Opravené problémy
* Inicializovat ADSyncNGCKeysWriteBack rutiny v modulu AD přípravný powershell byl nesprávně ACL'ing kontejner registrace zařízení a by proto dědit vlastnosti pouze existující oprávnění.  To bylo aktualizováno, aby měl účet synchronizační služby správná oprávnění.

#### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení
* Úloha AAD Connect přihlášení služby AD FS ověřte byla aktualizována tak, aby tak ověří přihlašovacích údajů oproti Microsoft Online a načítání právě token ze služby AD FS.
* Při nastavování nové farmy služby AD FS pomocí AAD Connect, stránku žádostí o služby AD FS pověření byl přesunut, takže teď nastane dříve, než je uživatel vyzván k poskytování servery služby AD FS a WAP.  To umožňuje AAD Connect zkontrolujte, že zadaný účet má správná oprávnění.
* Během upgradu AAD Connect jsme selže už upgrade vztah důvěryhodnosti služby AD FS AAD selže-li aktualizovat.  Pokud k tomu dojde, uživatel se zobrazí odpovídající zprávu upozornění a by měly pokračovat resetovat důvěryhodnosti prostřednictvím AAD Connect dodatečnou úlohu.

### <a name="seamless-single-sign-on"></a>Bezproblémové jednotné přihlašování
#### <a name="fixed-issues"></a>Opravené problémy
* Byl opraven problém způsobující Průvodce Azure AD Connect a vrátí chybu, pokud se pokusíte povolit [bezproblémové jednotné přihlašování](active-directory-aadconnect-sso.md). Chybová zpráva *"Konfigurace Microsoft Azure AD Connect ověření agenta se nezdařila."* Tento problém má vliv na stávající zákazníky, kteří ručně upgradovali agenty ověřování pro verze preview [předávací ověřování](active-directory-aadconnect-sso.md) podle kroků popsaných v tomto [článku](active-directory-aadconnect-pass-through-authentication-upgrade-preview-authentication-agents.md).


## <a name="115610"></a>1.1.561.0
Stav: 23 července 2017

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Opravené problém

* Byl opraven problém, která způsobila, že pravidlo synchronizace out-of-box "Na více systémů do AD - ImmutableId uživatele" mají být odebrány:

  * Tomuto problému dochází, pokud Azure AD Connect se upgraduje nebo pokud se možnost úloh *aktualizovat konfiguraci synchronizace* v Azure AD Connect se používá průvodce k aktualizaci konfigurace služby synchronizace Azure AD Connect.
  
  * Toto synchronizační pravidlo platí pro zákazníky, kteří mají povoleno [msDS-ConsistencyGuid jako zdrojové ukotvení funkce](active-directory-aadconnect-design-concepts.md#using-msds-consistencyguid-as-sourceanchor). Tato funkce byla zavedená ve verzi 1.1.524.0 a. Pokud je odebráno synchronizační pravidlo, Azure AD Connect můžete už naplnit místní AD ms-DS-ConsistencyGuid atributu s hodnotou atributu ObjectGuid. Nezabrání noví uživatelé z se zřídí do služby Azure AD.
  
  * Opravu zajišťuje synchronizační pravidlo bude odebrána už v během upgradu, nebo při změně konfigurace, tak dlouho, dokud je funkce zapnutá. Pro stávající zákazníky, kteří tento problém týká opravu také zajistí, že je pravidlo synchronizace přidat zpět po upgradu na tuto verzi služby Azure AD Connect.

* Pevné problém způsobující out-of-box synchronizační pravidla do mají přednost před hodnotu, která je menší než 100:

  * Obecně platí přednost před hodnoty 0 - 99 jsou vyhrazené pro vlastní synchronizační pravidla. Během upgradu jsou aktualizovány přednost hodnoty out-of-box synchronizační pravidla pro umístění změny pravidel synchronizace. Kvůli tomuto problému může out-of-box synchronizační pravidla přiřadit prioritu hodnotu, která je menší než 100.
  
  * Oprava zabrání problém během upgradu. Ale neobnoví přednost hodnoty pro stávající zákazníky, kteří byly ovlivněny problém. Samostatnou opravu bude poskytnuta v budoucnu pomoci při obnovení.

* Byl opraven problém kde [domény a filtrování organizační jednotky obrazovky](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) v Azure AD Connect se zobrazuje průvodce *synchronizovat všechny domén a organizačních jednotek* možnost jako vybrané, i když je povolené filtrování založené na organizační jednotku.

*   Opraven problém, která způsobila, že [obrazovky konfigurace oddílů adresářů](active-directory-aadconnectsync-configure-filtering.md#organizational-unitbased-filtering) v Synchronization Service Manager k vrátí chybu, pokud *aktualizovat* po kliknutí na tlačítko. Chybová zpráva *"došlo k chybě při aktualizaci domény: nelze přetypovat typu 'System.Collections.ArrayList' na typ objektu. Microsoft.DirectoryServices.MetadirectoryServices.UI.PropertySheetBase.MaPropertyPages.PartitionObject."* Chyba nastane, když nové domény AD se přidal do existující doménové struktuře AD a se pokoušíte aktualizovat Azure AD Connect s použitím tlačítko Aktualizovat.

#### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení

* [Funkce Automatické aktualizace](active-directory-aadconnect-feature-automatic-upgrade.md) se rozšířila na podporu zákazníky s následující konfigurace:
  * Povolili jste funkci zpětného zápisu zařízení.
  * Povolili jste funkci zpětný zápis skupin.
  * Instalace není Expresní nastavení nebo upgradu nástroje DirSync.
  * Máte více než 100 000 objektů v úložišti metaverse.
  * Se připojujete k více než jedné doménové struktuře. Expresní instalace připojuje pouze k jedné doménové struktuře.
  * AD Connector. účet už není výchozí MSOL_ účet.
  * Server je nastaven v pracovním režimu.
  * Povolili jste funkci zpětný zápis uživatelů.
  
  >[!NOTE]
  >Rozšíření oboru funkce Automatický Upgrade ovlivňuje zákazníky službou Azure AD Connect sestavení 1.1.105.0 a po. Pokud nechcete, aby server Azure AD Connect, abyste mohli automaticky upgradovat, je nutné spustit následující rutinu na serveru Azure AD Connect: `Set-ADSyncAutoUpgrade -AutoUpgradeState disabled`. Další informace o povolení nebo zákaz automatického upgradu, najdete v článku [Azure AD Connect: automatický upgrade](active-directory-aadconnect-feature-automatic-upgrade.md).

## <a name="115580"></a>1.1.558.0
Stav: Neuvolní. Změny v tomto sestavení jsou součástí verze 1.1.561.0.

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Opravené problém

* Byl opraven problém, která způsobila, že pravidlo synchronizace out-of-box "Na více systémů do AD - ImmutableId uživatele" odeberou při aktualizaci konfigurace filtrování založené na organizační jednotku. Je vyžadována pro toto synchronizační pravidlo [msDS-ConsistencyGuid jako zdrojové ukotvení funkce](active-directory-aadconnect-design-concepts.md#using-msds-consistencyguid-as-sourceanchor).

* Byl opraven problém kde [domény a filtrování organizační jednotky obrazovky](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) v Azure AD Connect se zobrazuje průvodce *synchronizovat všechny domén a organizačních jednotek* možnost jako vybrané, i když je povolené filtrování založené na organizační jednotku.

*   Opraven problém, která způsobila, že [obrazovky konfigurace oddílů adresářů](active-directory-aadconnectsync-configure-filtering.md#organizational-unitbased-filtering) v Synchronization Service Manager k vrátí chybu, pokud *aktualizovat* po kliknutí na tlačítko. Chybová zpráva *"došlo k chybě při aktualizaci domény: nelze přetypovat typu 'System.Collections.ArrayList' na typ objektu. Microsoft.DirectoryServices.MetadirectoryServices.UI.PropertySheetBase.MaPropertyPages.PartitionObject."* Chyba nastane, když nové domény AD se přidal do existující doménové struktuře AD a se pokoušíte aktualizovat Azure AD Connect s použitím tlačítko Aktualizovat.

#### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení

* [Funkce Automatické aktualizace](active-directory-aadconnect-feature-automatic-upgrade.md) se rozšířila na podporu zákazníky s následující konfigurace:
  * Povolili jste funkci zpětného zápisu zařízení.
  * Povolili jste funkci zpětný zápis skupin.
  * Instalace není Expresní nastavení nebo upgradu nástroje DirSync.
  * Máte více než 100 000 objektů v úložišti metaverse.
  * Se připojujete k více než jedné doménové struktuře. Expresní instalace připojuje pouze k jedné doménové struktuře.
  * AD Connector. účet už není výchozí MSOL_ účet.
  * Server je nastaven v pracovním režimu.
  * Povolili jste funkci zpětný zápis uživatelů.
  
  >[!NOTE]
  >Rozšíření oboru funkce Automatický Upgrade ovlivňuje zákazníky službou Azure AD Connect sestavení 1.1.105.0 a po. Pokud nechcete, aby server Azure AD Connect, abyste mohli automaticky upgradovat, je nutné spustit následující rutinu na serveru Azure AD Connect: `Set-ADSyncAutoUpgrade -AutoUpgradeState disabled`. Další informace o povolení nebo zákaz automatického upgradu, najdete v článku [Azure AD Connect: automatický upgrade](active-directory-aadconnect-feature-automatic-upgrade.md).

## <a name="115570"></a>1.1.557.0
Stav: 2017 července

>[!NOTE]
>Toto sestavení není k dispozici zákazníkům prostřednictvím funkce Azure AD Connect automaticky upgradovat.

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Opravené problém
* Oprava problému s Initialize-ADSyncDomainJoinedComputerSync rutina, která způsobila, že ověřené domény nakonfigurované na existující objekt bodu připojení služby se musí změnit i v případě, že je stále platná doména. K tomuto problému dochází, když klientovi Azure AD má více než jeden ověřených domén, které lze použít ke konfiguraci spojovacího bodu služby.

#### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení
* Zpětný zápis hesla je nyní k dispozici pro preview s Microsoft Azure Government cloudu a Německo cloudu Microsoftu. Další informace o podpoře Azure AD Connect pro instance jinou službu, najdete v článku [Azure AD Connect: zvláštní upozornění pro instance](active-directory-aadconnect-instances.md).

* Rutina Initialize-ADSyncDomainJoinedComputerSync teď má nový volitelný parametr s názvem AzureADDomain. Tento parametr umožňuje určit, které je ověřit domény, který se má použít pro konfiguraci spojovacího bodu služby.

### <a name="pass-through-authentication"></a>Předávací ověřování

#### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení
* Název agenta požadované pro předávací ověřování byla změněna z hodnoty *Microsoft Azure AD Application Proxy Connector* k *agenta služby Microsoft Azure AD Connect ověřování*.

* Povolení ověřování průchozí už umožňuje synchronizaci hodnoty Hash hesla ve výchozím nastavení.


## <a name="115530"></a>1.1.553.0
Stav: Červen 2017

> [!IMPORTANT]
> Existují schéma a synchronizační pravidlo změny uváděné ve toto sestavení. Služba Azure AD Connect synchronizace aktivují úplný Import a úplnou synchronizaci kroků po upgradu. Podrobnosti o změny jsou popsány níže. Dočasně odložení úplný Import a úplnou synchronizaci kroků po upgradu, naleznete v článku [postup odložení úplnou synchronizaci po upgradu](active-directory-aadconnect-upgrade-previous-version.md#how-to-defer-full-synchronization-after-upgrade).
>
>

### <a name="azure-ad-connect-sync"></a>Synchronizace služby Azure AD Connect

#### <a name="known-issue"></a>Známý problém
* Existuje problém, který ovlivňuje zákazníky, kteří používají [filtrování založené na organizační jednotku](active-directory-aadconnectsync-configure-filtering.md#organizational-unitbased-filtering) s synchronizace Azure AD Connect. Když přejdete [stránka domény a filtrování organizační jednotky](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) Průvodce Azure AD Connect, očekává se následující chování:
  * Pokud je povolené filtrování založené na organizační jednotku, **synchronizovat vybraných domén a organizačních jednotek** je vybraná možnost.
  * Jinak **synchronizovat všechny domén a organizačních jednotek** je vybraná možnost.

Problém, který nastane je, že **synchronizovat všechny domény a organizační jednotky možnost** je vždycky vybraná při spuštění průvodce.  K tomu dojde i v případě, že na základě organizační jednotky filtrování dříve nakonfigurované. Před uložením změny konfigurace AAD Connect, zajistěte, aby **synchronizovat vybraných domén a organizačních jednotek možnost** a potvrďte, že jsou všechny organizační jednotky, které je třeba synchronizovat znovu povolena. Jinak založené na organizační jednotku filtrování bude zakázáno.

#### <a name="fixed-issues"></a>Opravené problémy

* Byl opraven problém s zpětný zápis hesla, která umožňuje resetovat heslo místního správce Azure AD AD privilegovaný účet uživatele. Tento problém nastane, když Azure AD Connect je uděleno oprávnění k resetování hesla přes privilegovaného účtu. Problém je určeno v této verzi služby Azure AD Connect není povolením Správce Azure AD k resetování hesla libovolný místní AD privilegovaný účet uživatele, pokud je vlastník tohoto účtu správce. Další informace najdete v části [4033453 informační zpravodaj zabezpečení](https://technet.microsoft.com/library/security/4033453).

* Byl opraven problém související s [msDS-ConsistencyGuid jako zdrojové ukotvení](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-msds-consistencyguid-as-sourceanchor) funkce, kde Azure AD Connect nemá zpětný zápis do místní atribut msDS-ConsistencyGuid AD. Tento problém nastane, když je více místní doménových struktur služby AD, které jsou přidány do Azure AD Connect a *identit uživatelů existovat napříč více adresářů možnost* je vybrána. Pokud je použita taková konfigurace, výsledná synchronizační pravidla není vyplnit atribut sourceAnchorBinary v úložišti Metaverse. Atribut sourceAnchorBinary slouží jako zdrojový atribut pro atribut msDS-ConsistencyGuid. Zpětný zápis do atribut ms-DSConsistencyGuid v důsledku toho nebude fungovat. Pokud chcete vyřešit problém, byly aktualizovány následující pravidla synchronizace zajistit, že se vždy naplní atribut sourceAnchorBinary v úložišti Metaverse:
  * V ze služby Active Directory - InetOrgPerson AccountEnabled.xml
  * V ze služby Active Directory - InetOrgPerson Common.xml
  * V ze služby Active Directory - AccountEnabled.xml uživatele
  * V ze služby Active Directory - Common.xml uživatele
  * V ze služby Active Directory - uživatele připojit SOAInAAD.xml

* Dříve i když [msDS-ConsistencyGuid jako zdrojové ukotvení](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-msds-consistencyguid-as-sourceanchor) funkce není povolena, synchronizační pravidlo "Na více systémů do AD – uživatel ImmutableId" přidána k Azure AD Connect. Účinek je neškodné a nezpůsobí zpětný zápis atributu msDS-ConsistencyGuid proběhnout. Pokud chcete předejít nejasnostem, přidala se logika zajistit, že je pravidlo synchronizace je přidat jenom Pokud je povolena funkce.

* Byl opraven problém způsobující synchronizaci hodnoty hash hesla k selhání s chybová událost 611. K tomuto problému dochází po jeden nebo více domény, které byly odebrány z řadiče místní AD. Na konci každé heslo synchronizační cyklus, synchronizačního souboru cookie vydala pomocí místní AD obsahuje ID vyvolání řadičů domény odebrané s USN (Update Sequence Number) na hodnotu 0. Správce synchronizace hesel nelze trvale uložit synchronizace souboru cookie obsahující hodnoty USN na hodnotu 0 a nezdaří a zobrazí se chybová událost 611. Při příštím synchronizačním cyklu Správce synchronizace hesel opětovně používá poslední synchronizace trvalého souboru cookie, který neobsahuje USN hodnotu 0. To způsobí, že stejné změny hesla třeba znovu synchronizovat. Pomocí této opravy Správce synchronizace hesel správně trvá synchronizačního souboru cookie.

* Dříve i v případě automatický Upgrade byl zakázán pomocí rutiny Set-ADSyncAutoUpgrade, automatický Upgrade proces bude kontrolovat pro upgrade pravidelně a spoléhá na přijmout postižení stažený instalační program. Pomocí této opravy proces automatický Upgrade už zkontroluje upgrade pravidelně. Potíže se automaticky použije při upgradu instalační program pro tuto verzi Azure AD Connect se spustí jednou.

#### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení

* Dříve [msDS-ConsistencyGuid jako zdrojové ukotvení](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-msds-consistencyguid-as-sourceanchor) funkce byla k dispozici pouze pro nová nasazení. Nyní je k dispozici pro existující nasazení. A konkrétně:
  * Pro přístup k funkci, spusťte Průvodce službou Azure AD Connect a vyberte *aktualizace zdrojové ukotvení* možnost.
  * Tato možnost je jenom viditelné pro existující nasazení, které používají objectGuid jako atribut sourceAnchor.
  * Při konfiguraci možností, Průvodce ověří stav atribut msDS-ConsistencyGuid ve vaší místní službě Active Directory. Pokud atribut není nakonfigurováno uživatelských objektů v adresáři, použije průvodce msDS-ConsistencyGuid jako atribut sourceAnchor. Pokud atribut je nakonfigurovaný na jeden nebo více objektů uživatele v adresáři, průvodce se ukončí atribut se používá jiné aplikace a není vhodný jako atribut sourceAnchor a nepovoluje zdrojové ukotvení změna, která má pokračovat. Pokud jste si jisti, že atribut není používán existující aplikace, budete muset obraťte se na podporu pro informace o tom, jak potlačit chyby.

* Specifické pro **userCertificate** atribut na objekty zařízení, Azure AD Connect teď vypadá pro certifikáty hodnoty požadované pro [připojení k službě Azure AD pro prostředí Windows 10 připojená k doméně](https://docs.microsoft.com/azure/active-directory/active-directory-azureadjoin-devices-group-policy) a filtry se rest před synchronizací do služby Azure AD. Chcete-li toto chování, pravidlo synchronizace out-of-box "Se do AAD – zařízení připojit SOAInAD" byla aktualizována.

* Azure AD Connect nyní podporuje zpětný zápis Exchange Online **cloudPublicDelegates** atribut místní AD **publicDelegates** atribut. To umožňuje scénář, kde poštovní schránku systému Exchange Online lze udělit práva SendOnBehalfTo uživatelům s místní poštovní schránka systému Exchange. Pro podporu této funkce nové pravidlo synchronizace out-of-box "Na více systémů do AD – zpětný zápis uživatelů Exchange hybridní PublicDelegates" byla přidána. Toto synchronizační pravidlo se přidá do Azure AD Connect, pouze pokud je povolena funkce hybridní Exchange.

*   Synchronizace Azure AD Connect nyní podporuje **altRecipient** atributů z Azure AD. Pro podporu tuto změnu, byly aktualizovány následující pravidla synchronizace out-of-box zahrnout toku povinný atribut:
  * V ze služby Active Directory – Exchange uživatele
  * Out aad – ExchangeOnline uživatele
  
* **CloudSOAExchMailbox** atribut v úložišti Metaverse označuje, zda daný uživatel má poštovní schránka systému Exchange Online nebo ne. Zahrnout další RecipientDisplayTypes Exchange Online jako taková zařízení a konferenční místnosti poštovní schránky se aktualizovalo jeho definice. Chcete-li tuto změnu, definice cloudSOAExchMailbox atribut, který se nachází v části pravidla synchronizace out-of-box "V z AAD – uživatel Exchange hybridní", aktualizovaný z:

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

* Byla přidána následující sadu X509Certificate2 kompatibilní s funkcí pro vytváření výrazů pravidla pro zpracování certifikát hodnot v atributu userCertificate synchronizace:

    ||||
    | --- | --- | --- |
    |CertSubject|CertIssuer|CertKeyAlgorithm|
    |CertSubjectNameDN|CertIssuerOid|CertNameInfo|
    |CertSubjectNameOid|CertIssuerDN|IsCert|
    |CertFriendlyName|CertThumbprint|CertExtensionOids|
    |CertFormat|CertNotAfter|CertPublicKeyOid|
    |CertSerialNumber|CertNotBefore|CertPublicKeyParametersOid|
    |CertVersion|CertSignatureAlgorithmOid|Vyberte|
    |CertKeyAlgorithmParams|CertHashString|Kde|
    |||S|

* Umožňuje zákazníkům vytvářet vlastní synchronizační pravidla, které jsou předávány sAMAccountName, domainNetBios a domainFQDN pro objekty skupiny, a také distinguishedName pro uživatelské objekty byly zavedeny následující změny schématu:

  * MV schématu byly přidány následující atributy:
    * Skupiny: název účtu
    * Skupiny: domainNetBios
    * Skupiny: domainFQDN
    * Uživatel: distinguishedName

  * Byly přidány následující atributy do schématu konektoru služby Azure AD:
    * Group: OnPremisesSamAccountName
    * Skupiny: název pro rozhraní NetBIOS
    * Group: DnsDomainName
    * Uživatel: OnPremisesDistinguishedName

* Skript rutiny ADSyncDomainJoinedComputerSync má nyní nový volitelný parametr s názvem AzureEnvironment. Parametr slouží k určení, které oblasti odpovídající klienta Azure Active Directory je hostován v. Platné hodnoty patří:
  * AzureCloud (výchozí)
  * AzureChinaCloud
  * AzureGermanyCloud
  * USGovernment
 
* Aktualizovaných Rule Editor synchronizace používat připojit (namísto zřídit) jako výchozí hodnota typu odkaz během vytvoření pravidla synchronizace.

### <a name="ad-fs-management"></a>Správa služby AD FS

#### <a name="issues-fixed"></a>Chyby

* Nové koncové body služby WS-Federation zavedené službou Azure AD pro zlepšení odolnost proti výpadku ověřování jsou následující adresy URL a přidá se k místnímu konfigurace služby AD FS odpovídajících stran důvěryhodnosti:
  * https://ests.login.microsoftonline.com/login.srf
  * https://stamp2.login.microsoftonline.com/login.srf
  * https://ccs.login.microsoftonline.com/login.srf
  * https://ccs-sdf.login.microsoftonline.com/login.srf
  
* Byl opraven problém, která způsobila, že na generování hodnoty nesprávné deklarace identity pro IssuerID služby AD FS. Tomuto problému dochází, pokud existuje více ověřených domén v klientovi Azure AD a přípona domény atributu userPrincipalName sloužící ke generování deklarací identity IssuerID je minimálně 3 úrovně hloubkové (například johndoe@us.contoso.com). Problém se vyřeší aktualizací regulární výraz používaný pravidla deklarace identity.

#### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení
* Dříve funkce správy certifikátů služby AD FS poskytuje Azure AD Connect se použít jenom s farmy služby AD FS, které jsou spravované přes Azure AD Connect. Teď můžete použít funkci s farmy služby AD FS, které nejsou spravované přes Azure AD Connect.

## <a name="115240"></a>1.1.524.0
Vydáno: 2017 může

> [!IMPORTANT]
> Existují schéma a synchronizační pravidlo změny uváděné ve toto sestavení. Služba Azure AD Connect synchronizace aktivují úplný Import a úplnou synchronizaci kroků po upgradu. Podrobnosti o změny jsou popsány níže.
>
>

**Opravené problémy:**

Synchronizace služby Azure AD Connect

* Opravit problém, který způsobuje automatický Upgrade i v případě, že zákazník má zakázanou funkci pomocí rutiny Set-ADSyncAutoUpgrade proběhnout na server Azure AD Connect. Pomocí této opravy automatický Upgrade proces na serveru stále kontroluje pro upgrade pravidelně, ale stažený instalační program ctí konfigurace automatického upgradu.
* Během upgradu nástroje DirSync na místě vytvoří Azure AD Connect účtu služby Azure AD má být používána konektoru služby Azure AD pro synchronizaci se službou Azure AD. Po vytvoření účtu se pomocí účtu služby Azure AD ověřuje Azure AD Connect. V některých případech ověřování selže kvůli přechodným potížím, které způsobí, že DirSync místního upgradu k selhání s chybou *"došlo k chybě provádění úlohy konfigurace AAD Sync: AADSTS50034: pro přihlášení do této aplikace, musí být účet Přidat k adresáři xxx.onmicrosoft.com."* Pokud chcete zlepšit odolnost upgradu nástroje DirSync, Azure AD Connect nyní pokusí krok ověřování.
* Byl problém se sestavením 443, který způsobuje místní upgrade nástroje DirSync na úspěšné, ale nejsou vytvořeny profilů spuštění, které jsou požadované pro synchronizaci adresářů. Opravy logiku je součástí tohoto sestavení Azure AD Connect. Když zákazník upgraduje na tento build, Azure AD Connect zjistí chybí profilů spuštění a vytvoří je.
* Byl opraven problém, který způsobuje, že proces synchronizace hesel nepodaří spustit s 6900 ID události a chyby *"položku se stejným klíčem již byla přidána"*. K tomuto problému dochází v případě, že aktualizujete organizační jednotky, filtrování konfigurace zahrnují oddílu konfigurace AD. Chcete-li tento problém vyřešit, proces synchronizace hesel nyní synchronizuje změny hesel z pouze oddíly domény AD. Oddíly mimo doménu jako konfigurační oddíl se přeskočí.
* Během instalace Express, vytvoří Azure AD Connect místní účet služby AD DS pro použití konektorem AD ke komunikaci s místní AD. Dříve je účet vytvořený s příznakem PASSWD_NOTREQD nastaveným na atribut řízení uživatelských účtů a náhodné heslo je nastaven na účtu. Nyní Azure AD Connect explicitně odebere příznak PASSWD_NOTREQD po nastavení hesla pro účet.
* Pevné problém způsobující selhání upgradu nástroje DirSync s chybou *"došlo k vzájemnému zablokování v systému sql server které pokouší získat zámek aplikace"* když najde atribut mailNickname v místní schéma služby AD, ale není vázaný na třídu objektu uživatele AD.
* Opravit problém, který způsobuje funkce zpětný zápis zařízení automaticky zakázána při správce je aktualizaci konfigurace služby Azure AD Connect sync pomocí Průvodce Azure AD Connect. To je způsobeno Průvodce provádění předběžné kontroly pro existující konfigurace zpětný zápis zařízení v místní službě AD a kontrola selže. Je tak, aby přeskočil kontrolu, pokud zpětný zápis zařízení je již povolen dříve.
* Konfigurace filtrování organizační jednotky, můžete použít buď Průvodce službou Azure AD Connect nebo Synchronization Service Manager. Dříve, pokud používáte Průvodce službou Azure AD Connect konfigurace filtrování organizační jednotky, nové organizační jednotky vytvořit později jsou zahrnuté pro synchronizaci adresářů. Pokud nechcete, aby nové organizační jednotky mají být zahrnuty, musíte nakonfigurovat filtrování organizační jednotky pomocí Synchronization Service Manager. Teď můžete dosáhnout stejné chování pomocí Průvodce Azure AD Connect.
* Opravit problém, který způsobuje uložené procedury požadované přes Azure AD Connect, které mají být vytvořeny v rámci schématu instalaci správce, místo v rámci schématu dbo.
* Opravit problém, který způsobuje atribut TrackingId vrácený Azure AD vynechává v AAD Connect protokol událostí serveru. Tomuto problému dochází, pokud Azure AD Connect přijme zprávu o přesměrování z Azure AD a Azure AD Connect se nemůže připojit k zadaný koncový bod. TrackingId slouží pracovníkem technické podpory ke korelaci s protokoly na straně služby při řešení potíží.
* Pokud Azure AD Connect obdrží LargeObject chyba z Azure AD, Azure AD Connect vygeneruje událost s ID události 6941 a zprávou *"zřízený objekt je příliš velký. Omezte počet hodnot atributů pro tento objekt."* Ve stejnou dobu, Azure AD Connect rovněž vygeneruje zavádějící událost s ID události 6900 a zpráva *"Microsoft.Online.Coexistence.ProvisionRetryException: Nelze navázat komunikaci s Windows služby Azure Active Directory."* Pro zachování přehlednosti, Azure AD Connect již negeneruje pozdější událost v případě LargeObject chybě.
* Opravit problém, který způsobuje Synchronization Service Manager přestane odpovídat při pokusu o aktualizaci konfigurace pro generický konektor LDAP.

**Nové funkce nebo vylepšení:**

Synchronizace služby Azure AD Connect
* Byly implementovány změny pravidlo synchronizace – následující změny pravidlo synchronizace:
  * Aktualizované výchozí synchronizační pravidlo nastavené není export atributy **userCertificate** a **userSMIMECertificate** Pokud máte více než 15 hodnoty atributů.
  * Atributy AD **employeeID** a **msExchBypassModerationLink** jsou teď součástí je sada pravidel pro výchozí synchronizace.
  * Atribut AD **fotografií** byl odebrán z výchozí sada pravidel synchronizace.
  * Přidat **preferredDataLocation** schéma úložiště Metaverse a schématu na konektor AAD. Zákazníci, kteří chtějí aktualizovat buď atributy ve službě Azure AD můžete implementovat vlastní synchronizační pravidla Uděláte to tak. 
  * Přidat **userType** schéma úložiště Metaverse a schématu na konektor AAD. Zákazníci, kteří chtějí aktualizovat buď atributy ve službě Azure AD můžete implementovat vlastní synchronizační pravidla Uděláte to tak.

* Azure AD Connect nyní automaticky povolí použití atributu ConsistencyGuid jako atribut zdrojové ukotvení pro místní objekty služby AD. Další, Azure AD Connect naplní atribut ConsistencyGuid s hodnotou atributu objectGuid, pokud je prázdná. Tato funkce se vztahuje na pouze nové nasazení. Další informace o této funkci, naleznete v části [Azure AD Connect: Principy – pomocí msDS-ConsistencyGuid jako sourceAnchor návrh](active-directory-aadconnect-design-concepts.md#using-msds-consistencyguid-as-sourceanchor).
* Nové řešení potíží s rutinu Invoke-ADSyncDiagnostics přidala pro usnadnění diagnostiky synchronizaci hodnoty Hash hesla problémy související s. Informace o použití rutiny, najdete v článku [řešení potíží s synchronizaci hodnoty hash hesla s Azure AD Connect sync](active-directory-aadconnectsync-troubleshoot-password-hash-synchronization.md).
* Azure AD Connect teď podporuje synchronizace Mail-Enabled veřejné složky objekty z místní služby AD do Azure AD. Můžete povolit funkci pomocí Průvodce Azure AD Connect v části volitelné funkce. Další informace o této funkci, naleznete v článku [Office 365 adresáře na základě Edge blokování podporu pro místní e-mailu povolen veřejných složek](https://blogs.technet.microsoft.com/exchange/2017/05/19/office-365-directory-based-edge-blocking-support-for-on-premises-mail-enabled-public-folders).
* Azure AD Connect vyžaduje AD DS účet k synchronizaci z místní AD. Dříve Pokud jste nainstalovali Azure AD Connect pomocí expresního režimu, je možné zadat, že účet služby AD DS vyžaduje by vytvořit přihlašovací údaje účtu správce podniku a Azure AD Connect. Ale pro vlastní instalaci a přidání doménových struktur do stávajícího nasazení, museli místo toho zadat účet služby AD DS. Nyní máte také možnost během vlastní instalaci zadejte přihlašovací údaje účtu správce podniku a umožní vytvořit účet služby AD DS vyžaduje Azure AD Connect.
* Azure AD Connect teď podporuje SQL AOA. Před instalací Azure AD Connect je nutné povolit SQL AOA. Při instalaci Azure AD Connect zjistí, zda zadaná instance SQL je povoleno pro SQL AOA, nebo ne. Pokud je povoleno SQL AOA, Azure AD Connect další hodnoty, pokud SQL AOA je konfigurovaná pro použití replikace synchronní nebo asynchronní replikaci. Při nastavování naslouchacího procesu skupiny dostupnosti, se doporučuje nastavit vlastnost RegisterAllProvidersIP na 0. Je to proto, že Azure AD Connect aktuálně používá SQL Native Client k připojení k SQL a nativní klient SQL nepodporuje použití vlastnosti MultiSubNetFailover.
* Pokud používáte LocalDB jako databáze pro server Azure AD Connect a byl dosažen limit velikosti 10 GB, spustí se už synchronizační služby. Dříve budete muset provést operaci ShrinkDatabase na instanci LocalDB uvolnění dostatek místa databáze pro synchronizační službu spustit. Po kterém Synchronization Service Manager můžete použít k odstranění historie spouštění získat více místa na DB. Teď můžete rutiny Start-ADSyncPurgeRunHistory data o historii vyprázdnění spustit z LocalDB uvolnění místa DB. Další, tato rutina podporuje offline režim (zadáním parametru - offline) který může být použit, pokud není spuštěna služba synchronizace. Poznámka: Offline režimu lze pouze použijí, pokud není spuštěna služba synchronizace a je databáze používá LocalDB.
* Ke snížení množství prostoru úložiště vyžaduje, Azure AD Connect teď komprimaci podrobnosti o chybě synchronizačního před jejich ukládání do databáze LocalDB/SQL. Při upgradu ze starší verze služby Azure AD Connect na tuto verzi Azure AD Connect provede jednorázovou komprese existující podrobnosti o chybě synchronizace.
* Dříve po aktualizaci konfigurací filtrování organizační jednotky, je nutné ručně spustit úplný import zajistit, že stávající objekty jsou správně zahrnutý/vyloučený ze synchronizace adresářů. Nyní, Azure AD Connect automaticky aktivuje úplný import při příští synchronizaci cyklus. Další, úplný import se použije pouze na AD konektory vliv na aktualizace. Poznámka: Tato zlepšování se vztahuje na organizační jednotku filtrování aktualizace provedené pomocí Průvodce Azure AD Connect. Není použitelný do organizační jednotky filtrování aktualizací, které jsou vytvořené pomocí Synchronization Service Manager.
* Dříve filtrování podle skupiny podporuje uživatelé, skupiny a obraťte se na pouze objekty. Teď na základě skupiny filtrování podporuje také objekty počítače.
* Dříve můžete odstranit data prostoru konektoru bez zakázání plánovače synchronizace Azure AD Connect. Nyní Synchronization Service Manager blokuje odstranění dat prostoru konektoru, pokud zjistí, zda je povoleno plánovač. Upozornění další, je vrácena zákazníky informovat o potenciální ztrátě dat, pokud dojde k odstranění dat prostoru konektoru.
* Dříve je nutné zakázat přepis prostředí PowerShell pro Průvodce Azure AD Connect správné fungování. Tento problém je částečně vyřešený. Přepis prostředí PowerShell můžete povolit, pokud používáte ke správě konfigurace sync Průvodce Azure AD Connect. Pokud používáte ke správě konfigurace služby AD FS používáte Průvodce Azure AD Connect je nutné zakázat přepis prostředí PowerShell.



## <a name="114860"></a>1.1.486.0
Vydáno: Dubna 2017

**Opravené problémy:**
* Opravit problém, kde Azure AD Connect nelze úspěšně nainstalovat lokalizované verzi systému Windows Server.

## <a name="114840"></a>1.1.484.0
Vydáno: Dubna 2017

**Známé problémy:**

* Tato verze služby Azure AD Connect se úspěšně nainstalovat, pokud jsou splněny všechny následující podmínky:
   1. Provádíte buď DirSync místní upgrade nebo novou instalaci služby Azure AD Connect.
   2. Používáte lokalizované verzi systému Windows Server, kde název předdefinované skupiny Administrators na serveru není "Administrators".
   3. Používáte výchozí SQL Server 2012 Express LocalDB nainstalovaná službou Azure AD Connect místo vlastní úplné SQL.

**Opravené problémy:**

Synchronizace služby Azure AD Connect
* Opravit problém, kde plánovače synchronizace přeskočí krok celý sync, pokud jeden nebo více konektorů chybí profil spuštění pro tento krok synchronizace. Například jste ručně přidali konektor bez vytvoření rozdílový Import spuštění profilu pro něj pomocí Synchronization Service Manager. Tato oprava zajistí, že plánovače synchronizace nadále spustit rozdílový Import pro ostatní konektory.
* Byl opraven problém synchronizační službu kde okamžitě ukončí, zpracování profil spuštění, když je zaznamená problém s jedním z kroků spuštění. Tato oprava zajišťuje, že služba synchronizace přeskočí, který krok spustit a pokračuje ve zpracování s ostatními. Například máte rozdílový Import spuštění profilu pro AD connector s více kroků spuštění (jeden pro každou místní AD domény). Synchronizační služba spustí rozdílový Import s ostatními doménami AD i v případě, že jeden z nich má problémy se síťovým připojením.
* Opravit problém, který způsobí, že Azure AD Connector. aktualizace lze vynechat během automatický Upgrade.
* Byl opraven problém, který způsobuje, že Azure AD Connect nesprávně určit, zda je server řadič domény během instalace, které v zapnout způsobí selhání upgradu nástroje DirSync.
* Opravit problém, který způsobuje místní upgrade nástroje DirSync nemusíte vytvářet žádné profil spuštění pro konektor služby Azure AD.
* Opravit problém, kde uživatelské rozhraní Synchronization Service Manager přestane reagovat při pokusu o konfiguraci obecné konektor LDAP.

Správa služby AD FS
* Opravit problém, kde se Průvodce službou Azure AD Connect nezdaří, pokud primární uzel služby AD FS se přesunul na jiný server.

Plochy jednotného přihlašování
* Opraven problém Průvodce službou Azure AD Connect, kde na přihlašovací obrazovce neumožňuje povolit funkci jednotného přihlašování k ploše, pokud vyberete jako svoji možnost přihlášení během instalace nové synchronizace hesel.

**Nové funkce nebo vylepšení:**

Synchronizace služby Azure AD Connect
* Azure AD Connect Sync teď podporuje použití virtuální účet služby, účet spravované služby a skupinový účet spravované služby jako svůj účet služby. To platí pro nové instalace služby Azure AD Connect jenom. Při instalaci Azure AD Connect:
    * Ve výchozím nastavení Průvodce Azure AD Connect se vytvoří virtuální účet služby a použije jako svůj účet služby.
    * Pokud instalujete na řadiči domény, Azure AD Connect spadne zpět na předchozí chování, kde se vytvoří účet uživatele domény a místo toho použije jako svůj účet služby.
    * Výchozí chování můžete přepsat zadáním jednoho z následujících akcí:
      * Skupinu účet spravované služby
      * Účet spravované služby
      * Účet uživatele domény
      * Místní uživatelský účet
* Dříve Pokud upgradujete na nové sestavení Azure AD Connect obsahující konektory aktualizovat nebo změny pravidel synchronizace, Azure AD Connect se aktivuje cyklus úplné synchronizace. Nyní Azure AD Connect selektivně aktivuje úplný Import kroky pouze pro konektory s aktualizací a úplnou synchronizaci pouze pro konektory s změny synchronizační pravidlo.
* Dříve prahovou hodnotu odstranění exportu platí jenom pro export, které se spouštějí pomocí plánovače synchronizace. Tato funkce je nyní, rozšířen o exportuje ručně aktivovány zákazníka pomocí Synchronization Service Manager.
* V klientovi Azure AD je konfigurace služby, které označuje, zda je povolena funkce Synchronizace hesel pro vašeho klienta nebo ne. Dříve je snadné se nesprávně nakonfigurována přes Azure AD Connect Pokud máte aktivní a na testovacím serveru v konfiguraci služby. Teď by Azure AD Connect se pokusí uchovat konfiguraci služby konzistentní s vaší aktivní pouze server Azure AD Connect.
* Průvodce nyní zjistí a vrátí upozornění, pokud Azure AD Connect místní AD nemá Koš služby AD povolena.
* Dříve exportovat do Azure AD časový limit a selže, pokud celková velikost objektů v dávce překročí určité prahovou hodnotu. Synchronizační služby se nyní znovu pokusí znovu odeslal objekty v dávkách samostatný, menší, pokud je došlo k problému.
* Správa synchronizace služby klíč aplikace byla odebrána z nabídky Start v systému Windows. Správa šifrovací klíč bude i dál podporovaná prostřednictvím rozhraní příkazového řádku pomocí miiskmu.exe. Informace o správě šifrovací klíč, najdete v článku [zrušení šifrovací klíč Azure AD Connect Sync](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-change-serviceacct-pass#abandoning-the-azure-ad-connect-sync-encryption-key).
* Dříve Pokud změníte heslo účtu služby synchronizace Azure AD Connect, synchronizační služby nebude možné spustit správně opuštění šifrovací klíč a heslo účtu služby Azure AD Connect sync znovu inicializován. Nyní to se už nevyžaduje.

Plochy jednotného přihlašování

* Průvodce Azure AD Connect již nevyžaduje port 9090 otevřít v síti při konfiguraci předávací ověřování a jednotné přihlašování plochy. Jenom port 443 je povinný. 

## <a name="114430"></a>1.1.443.0
Vydáno: 2017 března

**Opravené problémy:**

Synchronizace služby Azure AD Connect
* Byl opraven problém, což způsobí, že průvodce Azure AD Connect nezdaří, pokud zobrazovaný název konektor služby Azure AD neobsahuje počáteční doméně onmicrosoft.com přiřazené klientovi Azure AD.
* Byl opraven problém, což způsobí, že průvodce Azure AD Connect k selhání při navazování připojení k databázi SQL, pokud heslo účtu synchronizační služby obsahuje speciální znaky, třeba apostrof, dvojtečkou a místa.
* Byl opraven problém, který způsobuje chybu "dimage má element anchor, který se liší od obrázku" na serveru Azure AD Connect v pracovním režimu, po které jste vyloučili dočasně místní AD objekt ze synchronizace a poté ji znovu zahrnuté pro synchronizaci.
* Byl opraven problém, který způsobuje chybu "nalézt rozlišující název objektu je fiktivní" na serveru Azure AD Connect v pracovním režimu, po které jste vyloučili dočasně místní AD objekt ze synchronizace a poté ji znovu zahrnuté pro synchronizaci.

Správa služby AD FS
* Byl opraven problém kde aktualizovat konfiguraci služby AD FS a nastavit správné deklarací na vztah důvěryhodnosti předávající strany po dokončení konfigurace alternativního přihlašovacího ID průvodce Azure AD Connect není.
* Opraven problém, kde nelze správně zpracovat serverů služby AD FS, účty služby nakonfigurované formátu userPrincipalName místo sAMAccountName formátu Průvodce Azure AD Connect.

Předávací ověřování
* Byl opraven problém, což způsobí, že průvodce Azure AD Connect nezdaří, pokud je vybrána možnost předat prostřednictvím ověření, ale registrace jeho konektoru selže.
* Byl opraven problém, který spustí Průvodce Azure AD Connect k ověření obejít kontroly v metoda přihlašování vybrána, když je povolena funkce jednotného přihlašování k ploše.

Resetování hesla
* Opravit problém, který může způsobit, že není se pokusí znovu připojit, pokud připojení byl ukončen bránou firewall nebo proxy serveru Azure AAD Connect.

**Nové funkce nebo vylepšení:**

Synchronizace služby Azure AD Connect
* Rutina Get-ADSyncScheduler nyní vrátí novou vlastnost typu Boolean s názvem SyncCycleInProgress. Pokud je vrácená hodnota true, znamená to, že je cyklus plánované synchronizace v průběhu.
* Cílovou složku pro uložení instalace služby Azure AD Connect a protokoly instalace se přesunul z %localappdata%\AADConnect do %programdata%\AADConnect ke zlepšení dostupnosti souborů protokolu.

Správa služby AD FS
* Byla přidána podpora pro aktualizaci certifikát SSL služby AD FS farmy.
* Byla přidána podpora pro správu AD FS 2016.
* Nyní můžete určit existující gMSA (skupinový účet spravované služby) během instalace služby AD FS.
* Teď můžete konfigurovat SHA-256 jako algoritmus hash podpisu vztahu důvěryhodnosti předávající strany služby Azure AD.

Resetování hesla
* Přináší vylepšení umožňuje produktu, funkce v prostředí s přísnější pravidla brány firewall.
* Spolehlivost vylepšené připojení k Azure Service Bus.

## <a name="113800"></a>1.1.380.0
Vydáno: 2016 prosinec

**Opravené problému:**

* Opravit problém, kde issuerid pravidla deklarace identity pro Active Directory Federation Services (AD FS) chybí v něm.

>[!NOTE]
>Toto sestavení není k dispozici zákazníkům prostřednictvím funkce Azure AD Connect automaticky upgradovat.

## <a name="113710"></a>1.1.371.0
Vydáno: 2016 prosinec

**Známý problém:**

* Pravidlo deklarace identity issuerid pro službu AD FS chybí v tomto sestavení. Pravidlo deklarace identity issuerid je vyžadován, pokud jsou federaci několika domén v Azure Active Directory (Azure AD). Pokud používáte Azure AD Connect spravovat místní nasazení služby AD FS, upgrade na tento build odebere stávající pravidlo deklarace identity issuerid z konfigurace služby AD FS. Problém můžete vyřešit přidáním pravidla deklarace identity issuerid po dokončení instalace nebo upgradu. Pro informace o přidání issuerid pravidel deklarace identity, naleznete v tomto článku v [podpora více domén pro federaci s Azure AD](active-directory-aadconnect-multiple-domains.md).

**Opravené problému:**

* Pokud není Port 9090 otevřený pro odchozí připojení, Azure AD Connect instalaci nebo upgrade selže.

>[!NOTE]
>Toto sestavení není k dispozici zákazníkům prostřednictvím funkce Azure AD Connect automaticky upgradovat.

## <a name="113700"></a>1.1.370.0
Vydáno: 2016 prosinec

**Známé problémy:**

* Pravidlo deklarace identity issuerid pro službu AD FS chybí v tomto sestavení. Pravidlo deklarace identity issuerid je vyžadován, pokud jsou federaci několika domén s Azure AD. Pokud používáte Azure AD Connect spravovat místní nasazení služby AD FS, upgrade na tento build odebere stávající pravidlo deklarace identity issuerid z konfigurace služby AD FS. Tento problém můžete obejít tak, že přidáte pravidlo deklarace identity issuerid po instalaci nebo upgradu. Pro informace o přidávání issuerid pravidel deklarace identity, naleznete v tomto článku v [podpora více domén pro federaci s Azure AD](active-directory-aadconnect-multiple-domains.md).
* Odchozí do dokončení instalace musíte otevřít port 9090.

**Nové funkce:**

* Předávací ověřování (Preview).

>[!NOTE]
>Toto sestavení není k dispozici zákazníkům prostřednictvím funkce Azure AD Connect automaticky upgradovat.

## <a name="113430"></a>1.1.343.0
Vydáno: Listopadu 2016

**Známý problém:**

* Pravidlo deklarace identity issuerid pro službu AD FS chybí v tomto sestavení. Pravidlo deklarace identity issuerid je vyžadován, pokud jsou federaci několika domén s Azure AD. Pokud používáte Azure AD Connect spravovat místní nasazení služby AD FS, upgrade na tento build odebere stávající pravidlo deklarace identity issuerid z konfigurace služby AD FS. Tento problém můžete obejít tak, že přidáte pravidlo deklarace identity issuerid po instalaci nebo upgradu. Pro informace o přidávání issuerid pravidel deklarace identity, naleznete v tomto článku v [podpora více domén pro federaci s Azure AD](active-directory-aadconnect-multiple-domains.md).

**Opravené problémy:**

* V některých případech instalace služby Azure AD Connect se nezdaří, protože se nepodařilo vytvořit účet místní služby, jehož heslo splňuje úroveň složitosti určeného organizace zásady hesel.
* Opraven problém, kde spojení nejsou vyhodnocovány když objekt v prostoru konektoru současně je mimo rozsah pro jedno připojení k pravidlo a stát v oboru pro jinou. To může nastat, když máte dva nebo více připojení pravidla, jejichž podmínky spojení se vzájemně vylučují.
* Opraven problém, kde nejsou pravidla synchronizace příchozích dat (ze služby Azure AD), která neobsahují pravidla spojení, zpracovávají, pokud mají nižší prioritu hodnoty než ty, které používají pravidla spojení.

**Vylepšení:**

* Byla přidána podpora pro instalaci Azure AD Connect na Windows Server 2016 Standard nebo vyšší.
* Byla přidána podpora pro použití SQL serveru 2016 jako Vzdálená databáze pro Azure AD Connect.

## <a name="112810"></a>1.1.281.0
Vydáno: Srpna 2016

**Opravené problémy:**

* Změny synchronizovat interval není provést až po dokončení příštího cyklu synchronizace.
* Průvodce Azure AD Connect nepřijímá účet Azure AD, jejichž uživatelské jméno začíná podtržítkem (\_).
* Průvodce Azure AD Connect nepodaří ověřit účet Azure AD, pokud heslo účtu obsahuje příliš mnoho speciální znaky. Chybová zpráva "nelze ověřit pověření. Došlo k neočekávané chybě." je vrácen.
* Odinstalace pracovní server zakáže synchronizace hesel v klientovi Azure AD a způsobí, že synchronizace hesel do selhat s aktivní server.
* Synchronizace hesel selže v neobvyklých případech po žádné hodnoty hash hesla, který je uložený na uživatele.
* Když pracovní režim povolen server Azure AD Connect, není dočasně zakázáno zpětný zápis hesla.
* Průvodce Azure AD Connect nezobrazuje vlastní heslo synchronizace a konfigurace zpětný zápis hesla, pokud je server v pracovním režimu. Vždy zobrazuje je jako zakázané.
* Změny konfigurace synchronizace hesel a zpětný zápis hesla nejsou trvalé pomocí Průvodce Azure AD Connect, pokud je server v pracovním režimu.

**Vylepšení:**

* Aktualizovat rutiny Start-ADSyncSyncCycle k označení, zda je možné úspěšně spustit nový cyklus synchronizace nebo ne.
* Přidat rutinu Stop-ADSyncSyncCycle ukončit synchronizačním cyklu a operace, které jsou aktuálně probíhá.
* Aktualizovat rutinu Stop-ADSyncScheduler ukončit synchronizačním cyklu a operace, které jsou aktuálně probíhá.
* Při konfiguraci [rozšíření adresáře](active-directory-aadconnectsync-feature-directory-extensions.md) v průvodce Azure AD Connect, můžete nyní vybrat atribut typu "Teletex řetězec" Azure AD.

## <a name="111890"></a>1.1.189.0
Vydáno: Červen 2016

**Opravené problémy a vylepšení:**

* Azure AD Connect je nyní možné nainstalovat na serveru kompatibilní se standardem FIPS.
  * Synchronizace hesel, najdete v části [synchronizace hodnot hash hesel a FIPS](active-directory-aadconnectsync-implement-password-hash-synchronization.md#password-hash-synchronization-and-fips).
* Opravit problém, kde nebylo možné přeložit název pro rozhraní NetBIOS do plně kvalifikovaný název domény v konektoru služby Active Directory.

## <a name="111800"></a>1.1.180.0
Vydáno: Května 2016

**Nové funkce:**

* Varuje a umožňuje ověření domén, pokud nebylo ji provést před spuštěním Azure AD Connect.
* Přidání podpory pro [Microsoft Cloud Německo](active-directory-aadconnect-instances.md#microsoft-cloud-germany).
* Přidání podpory pro nejnovější [cloudu Microsoft Azure Government](active-directory-aadconnect-instances.md#microsoft-azure-government-cloud) infrastruktury pomocí nové požadavky na adresu URL.

**Opravené problémy a vylepšení:**

* Přidat k editoru pravidlo synchronizace snadno najít synchronizační pravidla filtrování.
* Lepší výkon při odstraňování prostoru konektoru.
* Opraven problém, když na stejný objekt byla odstraněna i přidat ve stejném spustit (volané odstranit nebo přidat).
* Zakázané synchronizační pravidlo už znovu povolí zahrnovala objekty a atributy na upgrade nebo adresáři schématu aktualizujte.

## <a name="111300"></a>1.1.130.0
Vydáno:. Dubna 2016

**Nové funkce:**

* Přidaná podpora pro více hodnot atributů na [rozšíření adresáře](active-directory-aadconnectsync-feature-directory-extensions.md).
* Přidání podpory pro další změny konfigurace pro [automatický upgrade](active-directory-aadconnect-feature-automatic-upgrade.md) považuje za vhodné k upgradu.
* Přidat některé rutiny pro [vlastní plánovače](active-directory-aadconnectsync-feature-scheduler.md#custom-scheduler).

## <a name="111190"></a>1.1.119.0
Vydáno: Března 2016

**Opravené problémy:**

* Provedené se, že expresní instalace nelze použít v systému Windows Server 2008 (pre-R2), protože synchronizace hesla není podporována v tomto operačním systému.
* Upgrade z nástroje DirSync s konfigurací vlastního filtru nefunguje podle očekávání.
* Při upgradu na novější verze a neexistují žádné změny konfigurace, nemá být naplánováno úplné import nebo synchronizaci.

## <a name="111100"></a>1.1.110.0
Vydáno: Leden 2016

**Opravené problémy:**

* Upgrade ze starších verzích nefunguje, pokud není instalaci do výchozí složky C:\Program Files.
* Pokud nainstalujete a vymazat **spustit proces synchronizace** na konci Průvodce instalací, spustíte Průvodce instalací podruhé neumožní plánovače.
* Plánovač nebude fungovat podle očekávání na serverech, kde formát data a času US en nepoužívají. Bude také blokovat `Get-ADSyncScheduler` vrátit správného.
* Pokud jste nainstalovali dřívější verze služby Azure AD Connect se službou AD FS jako možnost přihlášení a upgradu, nebudete moci spustit Průvodce instalací znovu.

## <a name="111050"></a>1.1.105.0
Vydáno: Leden 2016

**Nové funkce:**

* [Automatický upgrade](active-directory-aadconnect-feature-automatic-upgrade.md) funkce pro zákazníky Expresní nastavení.
* Podpora pro globální správce pomocí ověřování Azure Multi-Factor Authentication a Privileged Identity Management v Průvodci instalací.
  * Je potřeba povolit proxy také povolit provoz https://secure.aadcdn.microsoftonline-p.com Pokud používáte službu Multi-Factor Authentication.
  * Je nutné přidat https://secure.aadcdn.microsoftonline-p.com do seznamu důvěryhodných serverů pro službu Multi-Factor Authentication správně fungovat.
* Povolit, změna metoda přihlašování uživatele po počáteční instalaci.
* Povolit [domény a organizační jednotky filtrování](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) v Průvodci instalací. To také umožňuje připojení k doménovými strukturami, kde jsou k dispozici všechny domény.
* [Scheduler](active-directory-aadconnectsync-feature-scheduler.md) synchronizační modul je součástí.

**Funkce z verze preview povýšen na GA:**

* [Zpětný zápis zařízení](active-directory-aadconnect-feature-device-writeback.md).
* [Rozšíření adresáře](active-directory-aadconnectsync-feature-directory-extensions.md).

**Nové funkce verze preview:**

* Nový výchozí synchronizovat cyklus interval je 30 minut. Použít na všechny starší verze po třech hodinách. Přidává podporu změnit [Plánovač](active-directory-aadconnectsync-feature-scheduler.md) chování.

**Opravené problémy:**

* Stránka domén DNS ověřte, zda vždy nerozpoznal domén.
* Výzvy k zadání pověření správce domény při konfiguraci služby AD FS.
* Místní účty AD nejsou rozpoznány pomocí Průvodce instalací, pokud se nachází v doméně s jiného stromu DNS než kořenové domény.

## <a name="1091310"></a>1.0.9131.0
Vydáno: Prosince 2015

**Opravené problémy:**

* Synchronizace hesla nemusí fungovat při změně hesla ve službě Active Directory Domain Services (AD DS), ale funguje, když jste nastavili heslo.
* Až budete mít proxy server, může selhat ověřování do služby Azure AD během instalace, nebo pokud je na stránce konfigurace zrušit upgrade.
* Aktualizace z předchozí verze služby Azure AD Connect s plná instance systému SQL Server selže, pokud si nejste správcem systému SQL Server (SA).
* Aktualizace z předchozí verze služby Azure AD Connect s vzdálený SQL Server, zobrazí se chyba "Nelze pro přístup k ADSync SQL databáze".

## <a name="1091250"></a>1.0.9125.0
Vydáno: Listopad 2015

**Nové funkce:**

* Můžete změnit konfiguraci služby AD FS pro vztah důvěryhodnosti služby Azure AD.
* Můžete aktualizovat schéma služby Active Directory a znovu vygenerovat synchronizačního pravidla.
* Můžete zakázat synchronizační pravidlo.
* Můžete definovat "AuthoritativeNull" jako nový literál v synchronizační pravidlo.

**Nové funkce verze preview:**

* [Azure AD Connect Health pro synchronizaci](../connect-health/active-directory-aadconnect-health-sync.md).
* Podpora pro [Azure AD Domain Services](../active-directory-passwords-update-your-own-password.md) synchronizace hesel.

**Nový podporovaný scénář:**

* Podporuje několik organizací místního Exchange. Další informace najdete v tématu [hybridní nasazení s více doménovými strukturami služby Active Directory](https://technet.microsoft.com/library/jj873754.aspx).

**Opravené problémy:**

* Problémy s synchronizací hesla:
  * Objekt přesunout z na více systémů oboru v oboru nebude mít jeho heslo synchronizovány. To zahrnuje organizační jednotky a filtrování atributů.
  * Výběr nové organizační jednotky zahrnout synchronizované nevyžaduje úplnou synchronizaci.
  * Pokud je povoleno zakázaný uživatel heslo není synchronizovaná.
  * Fronty opakování heslo je nekonečno a předchozí maximálně 5 000 objektů vyřadit byla odebrána.
* Nebylo možné se připojit ke službě Active Directory s úrovní funkčnosti doménové struktury Windows Server 2016.
* Nepodařilo se změnit skupinu, která se použije pro filtrování skupiny po počáteční instalaci.
* Už vytvoří nový uživatelský profil na server Azure AD Connect pro každého uživatele provádění změnu hesla s povolen zpětný zápis hesla.
* Nepodařilo se použít dlouhé celé číslo hodnoty synchronizované pravidla oborů.
* Zaškrtněte políčko "zpětný zápis zařízení" zakázáno, pokud jsou řadiče domény nedostupný.

## <a name="1086670"></a>1.0.8667.0
Vydáno: Srpen 2015

**Nové funkce:**

* Průvodce instalací služby Azure AD Connect je nyní lokalizované pro všechny jazyky Windows serveru.
* Přidaná podpora pro účet odemknout při použití správou hesel Azure AD.

**Opravené problémy:**

* Průvodce instalací služby Azure AD Connect dojde k chybě, pokud instalace spíše než osobě, která prvním spuštění instalace bude pokračovat jiným uživatelem.
* Pokud předchozí odinstalace služby Azure AD Connect nepodaří řádně odinstalovat synchronizace Azure AD Connect, není možné znovu nainstalovat.
* Nelze nainstalovat Azure AD Connect s použitím Expresní instalace, pokud uživatel není v kořenové doméně doménové struktury nebo pokud se používá jinou než anglickou verzi služby Active Directory.
* Pokud nelze přeložit plně kvalifikovaný název domény uživatelského účtu Active Directory, zobrazí se zavádějící chybová zpráva "Failed potvrzení schématu".
* Pokud účet použitý na konektor služby Active Directory se změnila mimo průvodce, průvodce selže při dalším spuštění.
* Azure AD Connect se někdy nepodaří nainstalovat na řadič domény.
* Nelze povolit nebo zakázat "Pracovní režim", pokud nebyly přidané atributy rozšíření.
* Zpětný zápis hesla se v některých konfiguracích nezdaří z důvodu nesprávné heslo na konektor služby Active Directory.
* DirSync nejde upgradovat, je-li rozlišující název (DN) se používá v filtrování atributů.
* Při použití resetování hesla nadměrnému využití procesoru.

**Funkce odebrané preview:**

* Funkce preview [zpětný zápis uživatelů](active-directory-aadconnect-feature-preview.md#user-writeback) dočasně odebral podle zpětnou vazbu od našich zákazníků preview. Přidá se později po jsme vyřešili zadaný zpětnou vazbu.

## <a name="1086410"></a>1.0.8641.0
Vydáno: Červen 2015

**Počáteční verze služby Azure AD Connect.**

Změněné název ze služby Azure AD Sync Azure AD Connect.

**Nové funkce:**

* [Expresní nastavení](active-directory-aadconnect-get-started-express.md) instalace
* Můžete [konfigurace služby AD FS](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs)
* Můžete [upgrade z nástroje DirSync](active-directory-aadconnect-dirsync-upgrade-get-started.md)
* [Prevence náhodného odstranění](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)
* Zavedly [pracovní režim](active-directory-aadconnectsync-operations.md#staging-mode)

**Nové funkce verze preview:**

* [Zpětný zápis uživatelů.](active-directory-aadconnect-feature-preview.md#user-writeback)
* [Zpětný zápis skupin](active-directory-aadconnect-feature-preview.md#group-writeback)
* [Zpětný zápis zařízení.](active-directory-aadconnect-feature-device-writeback.md)
* [Rozšíření adresáře](active-directory-aadconnect-feature-preview.md)

## <a name="104940501"></a>1.0.494.0501
Vydáno: Květen 2015

**Nový požadavek:**

* Azure AD Sync nyní vyžaduje rozhraní .NET Framework verze 4.5.1 k instalaci.

**Opravené problémy:**

* Zpětný zápis hesla z Azure AD se nezdařilo s chybou připojení k Azure Service Bus.

## <a name="104910413"></a>1.0.491.0413
Vydáno: Duben 2015

**Opravené problémy a vylepšení:**

* Konektor služby Active Directory nezpracovává odstranění správně, pokud je povolená koše a je více domén v doménové struktuře.
* Vylepšili jsme výkon operace importu pro konektor Azure Active Directory.
* Když skupina překročila limit členství (ve výchozím nastavení, limit je nastaven na 50 000 objektů), skupina byla odstraněna v Azure Active Directory. Novým chováním skupina se neodstraní, vygeneruje se chyba a nové změny členství neexportují.
* Nový objekt nelze zřídit, pokud dvoufázové instalace odstranit pomocí stejné rozlišující název se již nachází v prostoru konektoru.
* Některé objekty jsou označeny pro synchronizaci během synchronizace delta, i když se nezměnila, připraví se na objekt.
* Vynucení synchronizace hesla také odebere seznamu upřednostňovaných řadiče domény.
* CSExportAnalyzer došlo k problémům se některé stavy objektů.

**Nové funkce:**

* Spojení se můžete připojit typ objektu "ANY" v více hodnot.

## <a name="104850222"></a>1.0.485.0222
Vydáno: Únor 2015

**Vylepšení:**

* Import lepší výkon.

**Opravené problémy:**

* Synchronizace hesla ctí cloudFiltered atribut, který používá filtrování atributů. Vyfiltrovaných objektů již nejsou v oboru pro synchronizaci hesel.
* Ve výjimečných případech, kde topologii měl mnoho řadičů domény synchronizaci hesel nefunguje.
* Bylo povoleno "Zastavena server" při importu z konektoru nástroje Azure AD po správu zařízení v Azure AD nebo Intune.
* Připojení cizí objekty zabezpečení (FSP) z několika domén ve stejné doménové struktuře způsobí chybu nejednoznačný spojení.

## <a name="104751202"></a>1.0.475.1202
Vydáno: Z prosince 2014

**Nové funkce:**

* Synchronizace hesel pomocí filtrování podle atributů se teď podporuje. Další informace najdete v tématu [synchronizace hesel s filtrování](active-directory-aadconnectsync-configure-filtering.md).
* Atribut msDS-ExternalDirectoryObjectID se nezapisují zpět do služby Active Directory. Tato funkce přidává podporu pro aplikace Office 365. Přístup k poštovním schránkám Online a místně v hybridním nasazení Exchange používá OAuth2.

**Opravené upgradu problémy:**

* Novější verzi Pomocníka pro přihlášení je k dispozici na serveru.
* Vlastní cesta použité při instalaci Azure AD Sync.
* Neplatný vlastní spojení kritérium blokuje upgradu.

**Ostatní opravy:**

* Opravě šablony pro Office Pro Plus.
* Problémy s pevnou instalace způsobené uživatelská jména, která začínat pomlčkou.
* Pevné ztrátě sourceAnchor nastavení, když spustíte Průvodce instalací znovu.
* Opravené trasování událostí pro Windows pro synchronizaci hesel.

## <a name="104701023"></a>1.0.470.1023
Vydáno: Říjen 2014

**Nové funkce:**

* Synchronizace hesel z více místní služby Active Directory do Azure AD.
* Lokalizovaného instalačního uživatelského rozhraní pro všechny jazyky Windows serveru.

**Upgrade z GA nebude služba AADSync 1.0**

Pokud už máte Azure AD Sync nainstalovat, je další krok, který je třeba provést v případě, že jste změnili žádné z pravidel synchronizace out-of-box. Po upgradu na 1.0.470.1023 vydání, synchronizace, které jsou duplikované pravidla, které byly upraveny. Pro každé pravidlo upravené synchronizace postupujte takto:

1.  Vyhledejte pravidlo synchronizace byly upraveny a poznamenejte si změny.
* Odstraníte pravidlo synchronizace.
* Vyhledejte nové pravidlo synchronizace, který je vytvořen pomocí Azure AD Sync a potom znovu použijte změny.

**Oprávnění pro účet služby Active Directory**

Účet služby Active Directory musí mít udělen další oprávnění, aby mohli číst hodnot hash hesel ze služby Active Directory. Udělení oprávnění se s názvem "Replikovat změny adresáře" a "Replikujícím Directory změní všechny." Obě oprávnění musí být možné číst hodnoty hash hesla.

## <a name="104190911"></a>1.0.419.0911
Vydáno: Září 2014

**Počáteční verzi Azure AD Sync.**

## <a name="next-steps"></a>Další postup
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](active-directory-aadconnect.md).
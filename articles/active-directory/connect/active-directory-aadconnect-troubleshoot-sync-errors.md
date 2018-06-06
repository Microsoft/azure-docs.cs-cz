---
title: 'Azure AD Connect: Řešení potíží s chybami při synchronizaci | Microsoft Docs'
description: Vysvětluje, jak k řešení chyb zjištěných při synchronizaci se službou Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: 2209d5ce-0a64-447b-be3a-6f06d47995f8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: c38187221e7cd4e3244199e713f41be0005eb024
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "34801877"
---
# <a name="troubleshooting-errors-during-synchronization"></a>Řešení potíží s chybami při synchronizaci
Při synchronizaci dat identity z Windows Server Active Directory (AD DS) do Azure Active Directory (Azure AD), může dojít k chybám. Tento článek obsahuje přehled různých typů chyb synchronizace, některé možných scénářů, které způsobují tyto chyby a opravte potenciální způsoby. Tento článek obsahuje běžné typy chyb a nemusí zahrnovat všechny možné chyby.

 Tento článek předpokládá, že čtečka je obeznámeni s základní [návrh koncepty Azure AD a Azure AD Connect](active-directory-aadconnect-design-concepts.md).

Pomocí nejnovější verze služby Azure AD Connect \(srpna 2016 nebo vyšší\), sestavy chyb synchronizace je k dispozici v [portálu Azure](https://aka.ms/aadconnecthealth) v rámci služby Azure AD Connect Health pro synchronizaci.

Od 1. září 2016 [Azure Active Directory duplicitní atribut odolnosti](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md) funkce budou povolené ve výchozím nastavení pro všechny *nové* Azure Active Directory klientů. Tato funkce budou automaticky povolené pro existující klienty v nadcházejících měsících.

Azure AD Connect provádí tři typy operací z adresáře udržuje synchronizované: Import, synchronizaci a exportu. Chyby může provádět všechny operace. Tento článek zaměřuje především na chyby během exportu do služby Azure AD.

## <a name="errors-during-export-to-azure-ad"></a>Chyby při exportu do služby Azure AD
Následující část popisuje různé typy chyb synchronizace, které se můžou vyskytnout během exportu do služby Azure AD pomocí konektoru služby Azure AD. Tento konektor lze identifikovat podle formát názvu se "contoso. *onmicrosoft.com*".
Vyskytly chyby při exportu do služby Azure AD, která operaci \(přidání, aktualizace, odstranění atd.\) pokus o Azure AD Connect \(synchronizační modul\) v Azure Active Directory se nezdařilo.

![Přehled chyby exportu](./media/active-directory-aadconnect-troubleshoot-sync-errors/Export_Errors_Overview_01.png)

## <a name="data-mismatch-errors"></a>Chyby neshoda typů dat.
### <a name="invalidsoftmatch"></a>InvalidSoftMatch
#### <a name="description"></a>Popis
* Pokud Azure AD Connect \(synchronizační modul\) dá pokyn, Azure Active Directory můžete přidat nebo aktualizovat objekty, Azure AD odpovídá příchozí použití objektu **sourceAnchor** atribut **immutableId** atributů objektů ve službě Azure AD. Toto porovnání se nazývá **pevný odpovídat**.
* Pokud Azure AD **nebyly nalezeny** libovolný objekt, který odpovídá **immutableId** atribut s **sourceAnchor** atribut příchozí objektu, než nový objekt, je spadne zpět na atributy ProxyAddresses a UserPrincipalName použít k vyhledání shody. Toto porovnání se nazývá **logicky odpovídat**. Logicky odpovídat slouží tak, aby odpovídaly objekty již existuje ve službě Azure AD (která pocházejí ve službě Azure AD) se nové objekty se přidat/aktualizovat během synchronizace, které představují stejné entit (uživatelů, skupin) místně.
* **InvalidSoftMatch** chyba nastane, když pevný shody nebyly nalezeny žádné odpovídající objekt **a** logicky match najde odpovídající objekt, ale tento objekt má hodnotu různých *immutableId* než příchozí objekt *SourceAnchor*, které naznačují, že byl objekt odpovídající synchronizován s jiným objektem z na místní služby Active Directory.

Jinými slovy, pro logicky shodu pro práci, objekt, který má být konfigurace soft-najde shoda s by neměl mít žádnou hodnotu pro *immutableId*. Pokud žádný objekt s *immutableId* sady s hodnotou je selhání pevného match, ale které splňují kritéria soft-match, operace by způsobilo chybu InvalidSoftMatch synchronizace.

Azure schéma služby Active Directory neumožňuje dvou nebo více objektů do mají stejnou hodnotu z následujících atributů. \(Nejedná se o vyčerpávající seznam.\)

* ProxyAddresses
* UserPrincipalName
* onPremisesSecurityIdentifier
* ObjectId

> [!NOTE]
> [Azure AD atribut duplicitní atribut odolnosti](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md) je také se nasazuje funkce jako výchozí chování služby Azure Active Directory.  Tím se sníží počet chyb synchronizace pohledu Azure AD Connect (stejně jako ostatní klienty synchronizace) tím, že Azure AD pružnější způsobem zpracovává duplicitní ProxyAddresses UserPrincipalName atributy a přítomný v v prostředí místní AD. Tato funkce není opravte chyby duplicita. Proto data pořád je potřeba opravit. Ale umožňuje zřizování nových objektů, které jsou jinak blokovaná se zřídí kvůli duplicitní hodnoty ve službě Azure AD. Tím se také sníží počet chyb synchronizace vrácen do klienta synchronizace.
> Pokud je tato funkce je povoleno pro vašeho klienta, neuvidíte chyby synchronizace InvalidSoftMatch viděli při zřizování nových objektů.
>
>

#### <a name="example-scenarios-for-invalidsoftmatch"></a>Příklady scénářů pro InvalidSoftMatch
1. Existují dva nebo více objektů se stejnou hodnotou atributu ProxyAddresses v místní službě Active Directory. Pouze jedna je získávání zřídí ve službě Azure AD.
2. Dva nebo více objektů se stejnou hodnotu pro atribut userPrincipalName v místní službě Active Directory existuje. Pouze jedna je získávání zřídí ve službě Azure AD.
3. Objekt je přidaná do na místní služby Active Directory se stejnou hodnotou atributu ProxyAddresses jako existující objekt ve službě Azure Active Directory. Typ objektu, přidat místní není získávání zřízený v Azure Active Directory.
4. Objekt je přidaná do místní služby Active Directory se stejnou hodnotou atributu userPrincipalName jako účet ve službě Azure Active Directory. Objekt není získávání zřízený v Azure Active Directory.
5. Synchronizovaná účet byl přesunut z doménové struktury A doménové struktuře B. Azure AD Connect (synchronizační stroj) používal atribut ObjectGUID k výpočtu SourceAnchor. Po přesunu doménové struktury se liší hodnotu SourceAnchor. Nový objekt (z doménové struktury B) selhává k synchronizaci s existující objekt ve službě Azure AD.
6. Synchronizovaná objekt získali omylem odstranit z místní služby Active Directory a nový objekt, který byl vytvořen ve službě Active Directory pro stejnou entitu (například uživatele) bez odstranění účtu v Azure Active Directory. Nový účet se nepodaří synchronizovat s existující objekt služby Azure AD.
7. Azure AD Connect byla odinstalování a opětovném nainstalování. Při přeinstalaci jste vybrali jiný atribut jako SourceAnchor. Všechny objekty, které měl dříve synchronizovány zastavena, synchronizuje s chybou InvalidSoftMatch.

#### <a name="example-case"></a>Příklad případ:
1. **Bob Smith** je synchronizoval uživatele v Azure Active Directory z v místní službě Active Directory *contoso.com*
2. Bob Smith **UserPrincipalName** je nastaven jako **bobs@contoso.com**.
3. **"abcdefghijklmnopqrstuv =="** je **SourceAnchor** vypočítána Azure AD Connect s použitím Bob Smith **objectGUID** z místní služby Active Directory, která je  **immutableId** Bob Smith v Azure Active Directory.
4. Bob má také následující hodnoty **proxyAddresses** atribut:
   * SMTP: bobs@contoso.com
   * SMTP: bob.smith@contoso.com
   * **SMTP: bob@contoso.com**
5. Nový uživatel **Bob Taylora**, se přidá do místních služby Active Directory.
6. Bob Taylora **UserPrincipalName** je nastaven jako **bobt@contoso.com**.
7. **"abcdefghijkl0123456789 ==" "** je **sourceAnchor** vypočítána Azure AD Connect s použitím Bob Taylora **objectGUID** z na místní služby Active Directory. Bob Taylora objekt má nejsou synchronizované s Azure Active Directory.
8. Bob Taylora má následující hodnoty pro atribut proxyAddresses
   * SMTP: bobt@contoso.com
   * SMTP: bob.taylor@contoso.com
   * **SMTP: bob@contoso.com**
9. Během synchronizace Azure AD Connect rozpozná přidání Roberta Taylora v místní službě Active Directory a požádejte Azure AD, aby byly stejné změny.
10. Azure AD se nejprve provést pevný shodu. To znamená, bude vyhledávat, pokud se rovná libovolného objektu s immutableId "abcdefghijkl0123456789 ==". Pevné shoda se nezdaří, žádný další objekt ve službě Azure AD bude mít tento immutableId.
11. Azure AD se potom pokusí konfigurace soft-match Bob Taylora. To znamená bude hledání, pokud je jakýkoli objekt s proxyAddresses rovna tří hodnot, včetně smtp: bob@contoso.com
12. Azure AD se najít objekt Bob Smith konfigurace soft-match kritériím. Ale tento objekt má hodnotu immutableId = "abcdefghijklmnopqrstuv ==". Určuje, která tento objekt byl synchronizované z jiného objektu z místní služby Active Directory. Proto Azure AD nejde konfigurace soft-match tyto objekty a má za následek **InvalidSoftMatch** došlo k chybě synchronizace.

#### <a name="how-to-fix-invalidsoftmatch-error"></a>Jak opravit chyby InvalidSoftMatch
Nejčastější příčinou chyby InvalidSoftMatch je dva objekty s jinou SourceAnchor \(immutableId\) mají stejnou hodnotu pro ProxyAddresses nebo UserPrincipalName atributy, které se používají během procesu konfigurace soft-match na Azure AD. Chcete-li opravit neplatný logicky shody

1. Identifikujte duplicitní proxyAddresses, userPrincipalName nebo jinou hodnotu atributu, která je příčinou chyby. Také zjistit, jaké dva \(nebo více\) objekty jsou součástí konflikt. Sestavy generované [Azure AD Connect Health pro synchronizaci](https://aka.ms/aadchsyncerrors) můžete identifikovat dva objekty.
2. Určete, který objekt by měly být nadále mají duplicitní hodnotu a který objekt neměli.
3. Odeberte duplicitní hodnotu z objektu, který by měl není tuto hodnotu. V adresáři, kde se objekt pochází z by měla provést změnu. V některých případech budete pravděpodobně muset odstranit jeden z objektů v konfliktu.
4. Pokud jste provedli změnu v místních AD, nechat Azure AD Connect synchronizovat změny.

Sestavy chyb synchronizace v rámci Azure AD Connect Health pro synchronizaci se aktualizují každých 30 minut a zahrnují chyb z poslední pokus o synchronizaci.

> [!NOTE]
> ImmutableId, podle definice neměli měnit v průběhu životnosti objektu. Pokud Azure AD Connect nebyl nakonfigurován pomocí některého z následujících scénářů v paměti ze seznamu výše, může dojít v situaci, kde Azure AD Connect vypočítá jinou hodnotu SourceAnchor pro AD objekt, který reprezentuje stejné entity (stejné uživatele/skupiny nebo kontaktu atd), která má existující objekt Azure AD, který chcete používat.
>
>

#### <a name="related-articles"></a>Související články
* [Neplatná nebo duplicitní atributy zabránit synchronizaci adresářů v Office 365](https://support.microsoft.com/en-us/kb/2647098)

### <a name="objecttypemismatch"></a>ObjectTypeMismatch
#### <a name="description"></a>Popis
Pokud Azure AD se pokusí o soft porovnání dva objekty, je možné, že dva objekty různé "typ objektu" (například uživatelem, skupinou, obraťte se na atd.) mají stejné hodnoty pro atributy používané k provedení logicky shody. Jako duplikace těchto atributů není povolena ve službě Azure AD, operaci může způsobit chyby synchronizace "ObjectTypeMismatch".

#### <a name="example-scenarios-for-objecttypemismatch-error"></a>Ukázkové scénáře ObjectTypeMismatch došlo k chybě
* V Office 365 se vytvoří skupinu zabezpečení e-mailu povolen. Správce přidá nového uživatele nebo kontaktujte v místní AD (který není synchronizován do služby Azure AD ještě) se stejnou hodnotou atributu ProxyAddresses jako u skupiny Office 365.

#### <a name="example-case"></a>Příklad případu
1. Správce vytvoří novou skupinu zabezpečení e-mailu povolen v Office 365 pro oddělení daň a poskytuje e-mailovou adresu jako tax@contoso.com. Tato skupina je přiřazena hodnota atributu ProxyAddresses **smtp: tax@contoso.com**
2. Nový uživatel připojí Contoso.com a účet se vytvoří pro uživatele na místní s proxyAddress jako **smtp: tax@contoso.com**
3. Pokud Azure AD Connect se budou synchronizovat nový uživatelský účet, získají se chyba "ObjectTypeMismatch".

#### <a name="how-to-fix-objecttypemismatch-error"></a>Jak opravit chyby ObjectTypeMismatch
Nejčastější příčinou chyby ObjectTypeMismatch je, že dva objekty jiného typu (uživatelem, skupinou, obraťte se na atd.) mají stejnou hodnotu pro atribut ProxyAddresses. Chcete-li opravit ObjectTypeMismatch:

1. Identifikovat duplicitní proxyAddresses (nebo jiný atribut) hodnotu, která je příčinou chyby. Také zjistit, jaké dva \(nebo více\) objekty jsou součástí konflikt. Sestavy generované [Azure AD Connect Health pro synchronizaci](https://aka.ms/aadchsyncerrors) můžete identifikovat dva objekty.
2. Určete, který objekt by měly být nadále mají duplicitní hodnotu a který objekt neměli.
3. Odeberte duplicitní hodnotu z objektu, který by měl není tuto hodnotu. Všimněte si, že by měl provedení změny v adresáři, kde se objekt pochází z. V některých případech budete pravděpodobně muset odstranit jeden z objektů v konfliktu.
4. Pokud jste provedli změnu v místních AD, nechat Azure AD Connect synchronizovat změny. Synchronizace chybách v rámci Azure AD Connect Health pro synchronizaci aktualizuje každých 30 minut a patří i chyby z poslední pokus o synchronizaci.

## <a name="duplicate-attributes"></a>Duplicitní atributy
### <a name="attributevaluemustbeunique"></a>AttributeValueMustBeUnique
#### <a name="description"></a>Popis
Azure schéma služby Active Directory neumožňuje dvou nebo více objektů do mají stejnou hodnotu z následujících atributů. To je, že každý objekt ve službě Azure AD musí mít jedinečnou hodnotu z těchto atributů v dané instanci.

* ProxyAddresses
* UserPrincipalName

Pokud Azure AD Connect se pokusí přidat nový objekt nebo aktualizovat existující objekt s hodnotou pro výše uvedené atributy, která je již přiřazen k jinému objektu v Azure Active Directory, výsledkem operace Chyba synchronizace "AttributeValueMustBeUnique".

#### <a name="possible-scenarios"></a>Možných scénářů:
1. Duplicitní hodnota je přiřazena k již synchronizovaného objektu, který je v konfliktu s jiným objektem synchronizovaná.

#### <a name="example-case"></a>Příklad případ:
1. **Bob Smith** je synchronizoval uživatele v Azure Active Directory z na místní služby Active Directory contoso.com
2. Bob Smith **UserPrincipalName** místní je nastaven jako **bobs@contoso.com**.
3. Bob má také následující hodnoty **proxyAddresses** atribut:
   * SMTP: bobs@contoso.com
   * SMTP: bob.smith@contoso.com
   * **SMTP: bob@contoso.com**
4. Nový uživatel **Bob Taylora**, se přidá do místních služby Active Directory.
5. Bob Taylora **UserPrincipalName** je nastaven jako **bobt@contoso.com**.
6. **Bob Taylora** má následující hodnoty pro **ProxyAddresses** atribut i. SMTP: bobt@contoso.com ii. SMTP: bob.taylor@contoso.com
7. Bob Taylora objektu je úspěšně synchronizovat s Azure AD.
8. Správce se rozhodli aktualizovat Bob Taylora **ProxyAddresses** atribut s následující hodnotou: i. **SMTP: bob@contoso.com**
9. Azure AD bude pokus o aktualizaci Bob Taylora objektu ve službě Azure AD s hodnotu uvedenou výše, ale tato operace se nezdaří jako že ProxyAddresses hodnota je již přiřazen k Bob Smith, výsledkem je chyba "AttributeValueMustBeUnique".

#### <a name="how-to-fix-attributevaluemustbeunique-error"></a>Jak opravit chyby AttributeValueMustBeUnique
Nejčastější příčinou chyby AttributeValueMustBeUnique je dva objekty s jinou SourceAnchor \(immutableId\) mají stejnou hodnotu pro atributy ProxyAddresses nebo UserPrincipalName. Chcete-li opravit chyby AttributeValueMustBeUnique

1. Identifikujte duplicitní proxyAddresses, userPrincipalName nebo jinou hodnotu atributu, která je příčinou chyby. Také zjistit, jaké dva \(nebo více\) objekty jsou součástí konflikt. Sestavy generované [Azure AD Connect Health pro synchronizaci](https://aka.ms/aadchsyncerrors) můžete identifikovat dva objekty.
2. Určete, který objekt by měly být nadále mají duplicitní hodnotu a který objekt neměli.
3. Odeberte duplicitní hodnotu z objektu, který by měl není tuto hodnotu. Všimněte si, že by měl provedení změny v adresáři, kde se objekt pochází z. V některých případech budete pravděpodobně muset odstranit jeden z objektů v konfliktu.
4. Pokud jste provedli změnu v místních AD, nechat synchronizovat změny pro chybu na získat pevný Azure AD Connect.

#### <a name="related-articles"></a>Související články
-[Neplatná nebo duplicitní atributy zabránit synchronizaci adresářů v Office 365](https://support.microsoft.com/en-us/kb/2647098)

## <a name="data-validation-failures"></a>Ověřování dat
### <a name="identitydatavalidationfailed"></a>IdentityDataValidationFailed
#### <a name="description"></a>Popis
Azure Active Directory vynucuje různé omezení samotná data předtím, než tato data k zápisu do adresáře. Tato omezení jsou zajistit, aby koncoví uživatelé získávají nejlepší možný prostředí při použití aplikace, které závisí na tato data.

#### <a name="scenarios"></a>Scénáře
a. Hodnota atributu UserPrincipalName obsahuje neplatné znaky.
b. Atribut UserPrincipalName nedodrží požadovaný formát.

#### <a name="how-to-fix-identitydatavalidationfailed-error"></a>Jak opravit chyby IdentityDataValidationFailed
a. Ujistěte se, že má atribut userPrincipalName podporovaná znaky a požadovaný formát.

#### <a name="related-articles"></a>Související články
* [Příprava na zřízení uživatelů prostřednictvím synchronizaci adresářů, aby Office 365](https://support.office.com/en-us/article/Prepare-to-provision-users-through-directory-synchronization-to-Office-365-01920974-9e6f-4331-a370-13aea4e82b3e)

### <a name="federateddomainchangeerror"></a>FederatedDomainChangeError
#### <a name="description"></a>Popis
Výsledkem tento případ **"FederatedDomainChangeError"** při příponu UserPrincipalName uživatele z jedné federované domény na jiné federované domény došlo k chybě synchronizace.

#### <a name="scenarios"></a>Scénáře
Pro synchronizované uživatele příponou UserPrincipalName byl změněn z jedné federované domény na jiné federované domény místně. Například *UserPrincipalName = bob@contoso.com*  byl změněn na *UserPrincipalName = bob@fabrikam.com* .

#### <a name="example"></a>Příklad:
1. Bob Smith, účtu pro Contoso.com, získá přidat jako nový uživatel ve službě Active Directory s UserPrincipalName bob@contoso.com
2. Bob přesune do různých dělení contoso.com názvem Fabrikam.com a jeho UserPrincipalName se změní na bob@fabrikam.com
3. Domény contoso.com a fabrikam.com jsou federovaných domén v Azure Active Directory.
4. UserPrincipalName Boba neaktualizuje a výsledkem chyba synchronizace "FederatedDomainChangeError".

#### <a name="how-to-fix"></a>Informace o vyřešení
Pokud přípona UserPrincipalName uživatele byla aktualizována z bob @**contoso.com** Bob**fabrikam.com**, kde obě **contoso.com** a **fabrikam.com** jsou **federované domény**, postupujte podle těchto kroků opravte chybu synchronizace

1. Aktualizovat UserPrincipalName uživatele ve službě Azure AD z bob@contoso.com k bob@contoso.onmicrosoft.com. Následující příkaz prostředí PowerShell můžete pomocí modulu Azure AD PowerShell: `Set-MsolUserPrincipalName -UserPrincipalName bob@contoso.com -NewUserPrincipalName bob@contoso.onmicrosoft.com`
2. Povolit příštím synchronizačním cyklu k pokusu o synchronizaci. Tento čas synchronizace bude úspěšné a bude aktualizovat UserPrincipalName Roberta do bob@fabrikam.com podle očekávání.

#### <a name="related-articles"></a>Související články
* [Změny nejsou synchronizovány pomocí nástroje Azure Active Directory Sync po změně UPN uživatelský účet používat jiné federované domény](https://support.microsoft.com/en-us/help/2669550/changes-aren-t-synced-by-the-azure-active-directory-sync-tool-after-you-change-the-upn-of-a-user-account-to-use-a-different-federated-domain)

## <a name="largeobject"></a>LargeObject
### <a name="description"></a>Popis
Pokud atribut překračuje maximální povolenou velikost, maximální délku nebo limit počtu nastavit podle schématu služby Azure Active Directory, výsledkem operace synchronizace **LargeObject** nebo **ExceededAllowedLength** došlo k chybě synchronizace. Obvykle k této chybě dojde u následujících atributů

* userCertificate
* userSMIMECertificate
* thumbnailPhoto
* proxyAddresses

### <a name="possible-scenarios"></a>Možné scénáře
1. Atribut userCertificate Boba ukládá přiřazené Bob moc velký počet certifikátů. To může zahrnovat certifikáty starší, jejichž platnost vypršela. Pevný limit je 15 certifikáty. Další informace o tom, jak zpracovávat chyby LargeObject atributem userCertificate, naleznete v článku [LargeObject zpracování chyb vzniklých při userCertificate atribut](active-directory-aadconnectsync-largeobjecterror-usercertificate.md).
2. Atribut userSMIMECertificate Boba ukládá přiřazené Bob moc velký počet certifikátů. To může zahrnovat certifikáty starší, jejichž platnost vypršela. Pevný limit je 15 certifikáty.
3. Boba thumbnailPhoto nastavit ve službě Active Directory je možné synchronizovat ve službě Azure AD příliš velký.
4. Při automatické naplňování ProxyAddresses atributu ve službě Active Directory objekt má příliš mnoho ProxyAddresses přiřazen.

### <a name="how-to-fix"></a>Informace o vyřešení
1. Zkontrolujte, zda atribut, který způsobil chybu v rámci povolené omezení.

## <a name="related-links"></a>Související odkazy
* [Vyhledat objekty služby Active Directory v Centru správy služby Active Directory](https://technet.microsoft.com/library/dd560661.aspx)
* [Postup dotazování Azure Active Directory pro objekt, který používá Azure Active Directory PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx)

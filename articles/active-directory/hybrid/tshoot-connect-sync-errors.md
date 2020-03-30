---
title: 'Azure AD Connect: Poradce při potížích s chybami během synchronizace | Dokumenty společnosti Microsoft'
description: Vysvětluje, jak řešit chyby, ke kterým došlo během synchronizace s Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 2209d5ce-0a64-447b-be3a-6f06d47995f8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/29/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 745ddcc95bb91e61478307265aec1ac8a7ebba54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609192"
---
# <a name="troubleshooting-errors-during-synchronization"></a>Poradce při potížích s chybami během synchronizace
K chybám může dojít při synchronizaci dat identity ze služby Windows Server Active Directory (AD DS) do služby Azure Active Directory (Azure AD). Tento článek obsahuje přehled různých typů chyb synchronizace, některé z možných scénářů, které způsobují tyto chyby a potenciální způsoby, jak opravit chyby. Tento článek obsahuje běžné typy chyb a nemusí zahrnovat všechny možné chyby.

 Tento článek předpokládá, že čtenář je obeznámen s základní [koncepty návrhu Azure AD a Azure AD Connect](plan-connect-design-concepts.md).

S nejnovější verzí Azure AD Connect \(srpna 2016 nebo vyšší\), sestava chyb synchronizace je k dispozici na webu Azure [Portal](https://aka.ms/aadconnecthealth) jako součást Azure AD Connect health pro synchronizaci.

září 2016 bude funkce [odolnosti atributů duplicitního atributu služby Azure Active Directory](how-to-connect-syncservice-duplicate-attribute-resiliency.md) ve výchozím nastavení povolena pro všechny *nové* klienty Služby Azure Active Directory. Tato funkce bude automaticky povolena pro stávající klienty v nadcházejících měsících.

Azure AD Connect provádí tři typy operací z adresářů, které udržuje v synchronizaci: Import, synchronizace a Export. Chyby mohou probíhat ve všech operacích. Tento článek se zaměřuje hlavně na chyby během exportu do Azure AD.

## <a name="errors-during-export-to-azure-ad"></a>Chyby během exportu do Azure AD
Následující část popisuje různé typy chyb synchronizace, ke kterým může dojít během operace exportu do služby Azure AD pomocí konektoru Azure AD. Tento konektor lze identifikovat podle formátu názvu je "contoso. *onmicrosoft.com*".
Chyby během exportu do azure \(ad označují,\) že operace přidat, \(aktualizovat,\) odstranit atd pokus o Azure AD Connect Sync Engine ve službě Azure Active Directory se nezdařilo.

![Přehled chyb exportu](./media/tshoot-connect-sync-errors/Export_Errors_Overview_01.png)

## <a name="data-mismatch-errors"></a>Chyby neshody dat
### <a name="invalidsoftmatch"></a>InvalidSoftMatch
#### <a name="description"></a>Popis
* Když synchronizační \(modul\) Azure AD Connect instruuje Azure Active Directory k přidání nebo aktualizaci objektů, Azure AD odpovídá příchozíobjekt pomocí **sourceAnchor** atribut **neměnnéId** atribut objektů ve službě Azure AD. Tato shoda se nazývá **tvrdá shoda**.
* Když Azure AD **nenajde** žádný objekt, který odpovídá **atributu immutableId** s **sourceAnchor** atribut příchozí objekt, před zřízením nový objekt, přejde zpět k použití ProxyAddresses a UserPrincipalName atributy najít shodu. Tato shoda se nazývá **měkká shoda**. Měkká shoda je navržentak, aby odpovídala objekty, které jsou již k dispozici ve službě Azure AD (které jsou zdrojem ve službě Azure AD) s nové objekty, které se přidávají nebo aktualizují během synchronizace, které představují stejnou entitu (uživatelé, skupiny) v místním prostředí.
* K chybě **InvalidSoftMatch** dochází, když tvrdá shoda nenajde žádný odpovídající objekt **a** měkká shoda najde odpovídající objekt, ale tento objekt má jinou hodnotu *imemtableId* než příchozí objekt *SourceAnchor*, což naznačuje, že odpovídající objekt byl synchronizován s jiným objektem z místní služby Active Directory.

Jinými slovy, aby měkké zápas do práce, objekt, který má být soft-matched s by neměl mít žádnou hodnotu pro *immutableId*. Pokud jakýkoli objekt s *imemtableId* set s hodnotou se nedaří pevný zápas, ale splňující kritéria měkké shody, operace by mít za následek chybu synchronizace InvalidSoftMatch.

Schéma služby Azure Active Directory neumožňuje dva nebo více objektů mít stejnou hodnotu z následujících atributů. \(Toto není vyčerpávající seznam.\)

* ProxyAddresses
* UserPrincipalName
* onPremisesSecurityIdentifier
* ObjectId

> [!NOTE]
> [Funkce odolnosti atributů atributu Azure AD](how-to-connect-syncservice-duplicate-attribute-resiliency.md) se také zavádí jako výchozí chování služby Azure Active Directory.  Tím se sníží počet chyb synchronizace, které vidí Azure AD Connect (stejně jako ostatní klienti synchronizace) tím, že Azure AD odolnější ve způsobu, jakým zpracovává duplicitní ProxyAddresses a UserPrincipalName atributy přítomné v místních prostředích Služby AD. Tato funkce neopravuje chyby duplikace. Takže data musí být ještě opravena. Ale umožňuje zřizování nových objektů, které jsou jinak blokovány zřídit z důvodu duplicitní hodnoty ve službě Azure AD. Tím se také sníží počet chyb synchronizace vrácených klientovi synchronizace.
> Pokud je tato funkce povolena pro vašeho tenanta, neuvidíte chyby synchronizace InvalidSoftMatch, které se zobrazují při zřizování nových objektů.
>
>

#### <a name="example-scenarios-for-invalidsoftmatch"></a>Příklad y pro InvalidSoftMatch
1. Dva nebo více objektů se stejnou hodnotou pro atribut ProxyAddresses existují v místním adresáři Active Directory. Ve službě Azure AD se zřaží jenom jeden.
2. Dva nebo více objektů se stejnou hodnotou pro atribut userPrincipalName existuje v místním adresáři Active Directory. Ve službě Azure AD se zřaží jenom jeden.
3. Objekt byl přidán do místní služby Active Directory se stejnou hodnotou atributu ProxyAddresses jako existující objekt ve službě Azure Active Directory. Objekt přidaný místně se nezřává ve službě Azure Active Directory.
4. Objekt byl přidán v místním adresáři Active Directory se stejnou hodnotou atributu userPrincipalName jako u účtu ve službě Azure Active Directory. Objekt není stále zřízené ve službě Azure Active Directory.
5. Synchronizovaný účet byl přesunut z doménové struktury A do doménové struktury B. Azure AD Connect (synchronizační modul) používal atribut ObjectGUID k výpočtu SourceAnchor. Po přesunutí doménové struktury je hodnota SourceAnchor odlišná. Nový objekt (z doménové struktury B) se nedaří synchronizovat s existující objekt ve službě Azure AD.
6. Synchronizovaný objekt byl omylem odstraněn z místní služby Active Directory a nový objekt byl vytvořen ve službě Active Directory pro stejnou entitu (například uživatele) bez odstranění účtu ve službě Azure Active Directory. Nový účet se nepodaří synchronizovat s existující maty Azure AD.
7. Azure AD Connect byl odinstalován a přeinstalovat. Během přeinstalace byl vybrán jiný atribut jako SourceAnchor. Všechny objekty, které byly dříve synchronizovány, přestaly synchronizovat s chybou InvalidSoftMatch.

#### <a name="example-case"></a>Příklad případu:
1. **Bob Smith** je synchronizovaný uživatel ve službě Azure Active Directory z místní služby Active Directory *contoso.com*
2. **Jméno UserPrincipalName** Boba Nováka je nastaveno jako **bobs\@contoso.com**.
3. **"abcdefghijklmnopqrstuv=="** je **SourceAnchor** vypočítaný službou Azure AD Connect pomocí **objektu Guid** Boba Nováka z místní služby Active Directory, což je **neměnné Id pro Boba Nováka** ve službě Azure Active Directory.
4. Bob má také následující hodnoty pro atribut **proxyAddresses:**
   * Smtp:bobs@contoso.com
   * Smtp:bob.smith@contoso.com
   * **smtp:\@bob contoso.com**
5. Do místní služby Active Directory je přidán nový **uživatel, Bob Taylor**.
6. Bob Taylor **UserPrincipalName** je nastaven jako **\@bobt contoso.com**.
7. **"abcdefghijkl0123456789==""** je **sourceAnchor** vypočítaný službou Azure AD Connect pomocí **objektu Guid** Boba Taylora z místní služby Active Directory. Objekt Boba Taylora se ještě nesynchronizoval se službou Azure Active Directory.
8. Bob Taylor má následující hodnoty pro atribut proxyAddresses
   * Smtp:bobt@contoso.com
   * Smtp:bob.taylor@contoso.com
   * **smtp:\@bob contoso.com**
9. Během synchronizace Azure AD Connect rozpozná přidání Bob Taylor v místním active directory a požádejte Azure AD provést stejnou změnu.
10. Azure AD nejprve provede tvrdou shodu. To znamená, že bude hledat, pokud existuje nějaký objekt s immutableId rovná "abcdefghijkl0123456789==". Pevný zápas se nezdaří jako žádný jiný objekt ve službě Azure AD bude mít, že imemtableId.
11. Azure AD se pak pokusí soft-match Bob Taylor. To znamená, že bude hledat, pokud existuje nějaký objekt s proxyAddresses rovná tři hodnoty, včetně smtp:bob@contoso.com
12. Azure AD nalezne objekt Boba Nováka tak, aby odpovídal kritériím měkké shody. Ale tento objekt má hodnotu immutableId = "abcdefghijklmnopqrstuv==". což znamená, že tento objekt byl synchronizován z jiného objektu z místní služby Active Directory. Proto Azure AD nemůže soft-match tyto objekty a výsledky v chybu synchronizace **InvalidSoftMatch.**

#### <a name="how-to-fix-invalidsoftmatch-error"></a>Jak opravit chybu InvalidSoftMatch
Nejběžnější důvod pro Chybu InvalidSoftMatch je dva objekty s různými \(\) SourceAnchor immutableId mají stejnou hodnotu pro ProxyAddresses nebo UserPrincipalName atributy, které se používají během procesu měkké shody na Azure AD. Chcete-li opravit neplatnou měkkou shodu

1. Identifikujte duplicitní proxyAdresy, userPrincipalName nebo jinou hodnotu atributu, která chybu způsobuje. Také určit, \(které\) dva nebo více objektů jsou zapojeny do konfliktu. Sestava generovaná [službou Azure AD Connect Health pro synchronizaci](https://aka.ms/aadchsyncerrors) vám může pomoci identifikovat dva objekty.
2. Určete, který objekt by měl mít duplicitní hodnotu a který objekt by neměl.
3. Odeberte duplicitní hodnotu z objektu, který by neměl mít tuto hodnotu. Měli byste provést změnu v adresáři, ze kterého je objekt pochází. V některých případech může být nutné odstranit jeden z objektů v konfliktu.
4. Pokud jste provedli změnu v místní službě AD, nechte Azure AD Connect synchronizovat změnu.

Zprávy o chybách synchronizace v rámci Azure AD Connect Health pro synchronizaci se aktualizují každých 30 minut a zahrnují chyby z nejnovějšího pokusu o synchronizaci.

> [!NOTE]
> ImmutableId, podle definice by se neměla měnit v životnostobjektu. Pokud Azure AD Connect nebyl nakonfigurován s některými scénáři v mysli z výše uvedeného seznamu, můžete skončit v situaci, kdy Azure AD Connect vypočítá jinou hodnotu SourceAnchor pro objekt AD, který představuje stejnou entitu (stejný uživatel/skupina/kontakt atd.), který má existující objekt Azure AD, který chcete pokračovat v používání.
>
>

#### <a name="related-articles"></a>Související články
* [Duplicitní nebo neplatné atributy brání synchronizaci adresářů v Office 365](https://support.microsoft.com/kb/2647098)

### <a name="objecttypemismatch"></a>Neshoda objektu ObjectTypeMismatch
#### <a name="description"></a>Popis
Když Se pokusí Azure AD měkké shody dva objekty, je možné, že dva objekty různého "typu objektu" (například uživatel, skupina, kontakt atd.) mají stejné hodnoty pro atributy použité k provedení měkké shody. Jako duplikace těchto atributů není povoleno ve službě Azure AD, operace může mít za následek chybu synchronizace "ObjectTypeMismatch".

#### <a name="example-scenarios-for-objecttypemismatch-error"></a>Příklad scénáře pro chybu ObjectTypeMismatch
* V Office 365 se vytvoří skupina zabezpečení s povolenou poštou. Správce přidá nového uživatele nebo kontakt v místním službě AD (to ještě není synchronizováno s Azure AD) se stejnou hodnotou pro atribut ProxyAddresses jako ve skupině Office 365.

#### <a name="example-case"></a>Příklad případu
1. Správce vytvoří novou skupinu zabezpečení s podporou pošty v Office 365 pro daňové oddělení a zadává e-mailovou adresu jako tax@contoso.com. Této skupině je přiřazena hodnota atributu ProxyAddresses **smtp:\@tax contoso.com**
2. Nový uživatel se připojí Contoso.com a účet je vytvořen pro uživatele v místním prostředí s proxyAddress jako **smtp:\@daňové contoso.com**
3. Když Azure AD Connect synchronizuje nový uživatelský účet, zobrazí se chyba "ObjectTypeMismatch".

#### <a name="how-to-fix-objecttypemismatch-error"></a>Jak opravit chybu ObjectTypeMismatch
Nejběžnější důvod pro ObjectTypeMismatch chyba je dva objekty různého typu (Uživatel, Skupina, Kontakt atd.) mají stejnou hodnotu pro ProxyAddresses atribut. Chcete-li opravit ObjectTypeMismatch:

1. Identifikujte duplicitní hodnotu proxyAddresses (nebo jiný atribut), která je příčinou chyby. Také určit, \(které\) dva nebo více objektů jsou zapojeny do konfliktu. Sestava generovaná [službou Azure AD Connect Health pro synchronizaci](https://aka.ms/aadchsyncerrors) vám může pomoci identifikovat dva objekty.
2. Určete, který objekt by měl mít duplicitní hodnotu a který objekt by neměl.
3. Odeberte duplicitní hodnotu z objektu, který by neměl mít tuto hodnotu. Všimněte si, že byste měli provést změnu v adresáři, odkud je objekt pochází. V některých případech může být nutné odstranit jeden z objektů v konfliktu.
4. Pokud jste provedli změnu v místní službě AD, nechte Azure AD Connect synchronizovat změnu. Zpráva o chybách synchronizace v rámci Azure AD Connect Health pro synchronizaci se aktualizuje každých 30 minut a zahrnuje chyby z nejnovějšího pokusu o synchronizaci.

## <a name="duplicate-attributes"></a>Duplicitní atributy
### <a name="attributevaluemustbeunique"></a>AttributeValueMustBeUnique
#### <a name="description"></a>Popis
Schéma služby Azure Active Directory neumožňuje dva nebo více objektů mít stejnou hodnotu z následujících atributů. To je každý objekt ve službě Azure AD je vynuceno mít jedinečnou hodnotu těchto atributů v dané instanci.

* ProxyAddresses
* UserPrincipalName

Pokud Azure AD Connect pokusí přidat nový objekt nebo aktualizovat existující objekt s hodnotou pro výše uvedené atributy, které je již přiřazeny k jinému objektu ve službě Azure Active Directory, operace výsledky chyby synchronizace "AttributeValueMustBeUnique".

#### <a name="possible-scenarios"></a>Možné scénáře:
1. Duplicitní hodnota je přiřazena již synchronizovaným objektům, který je v konfliktu s jiným synchronizovaným objektem.

#### <a name="example-case"></a>Příklad případu:
1. **Bob Smith** je synchronizovaný uživatel ve službě Azure Active Directory z místní služby Active Directory contoso.com
2. Uživatelské jméno Uživatele Hlavní ho boba **Nováka** je nastaveno jako **\@bobs contoso.com**.
3. Bob má také následující hodnoty pro atribut **proxyAddresses:**
   * Smtp:bobs@contoso.com
   * Smtp:bob.smith@contoso.com
   * **smtp:\@bob contoso.com**
4. Do místní služby Active Directory je přidán nový **uživatel, Bob Taylor**.
5. Bob Taylor **UserPrincipalName** je nastaven jako **\@bobt contoso.com**.
6. **Bob Taylor** má následující hodnoty pro **Atribut ProxyAddresses** i. smtp: bobt@contoso.com ii. Smtp:bob.taylor@contoso.com
7. Objekt Boba Taylora je úspěšně synchronizován se službou Azure AD.
8. Admin se rozhodl aktualizovat Atribut **ProxyAddresses** Boba Taylora s následující hodnotou: i. **smtp:\@bob contoso.com**
9. Azure AD se pokusí aktualizovat objekt Boba Taylora ve službě Azure AD s výše uvedenou hodnotou, ale tato operace se nezdaří, protože hodnota ProxyAddresses je již přiřazena Bobovi Smithovi, což vede k chybě "AttributeValueMustBeUnique".

#### <a name="how-to-fix-attributevaluemustbeunique-error"></a>Jak opravit chybu AttributeValueMustMustNutUnique
Nejběžnější důvod pro AttributeValueMustMustUnique chyba je dva objekty s různými \(\) SourceAnchor immutableId mají stejnou hodnotu pro ProxyAddresses nebo UserPrincipalName atributy. Chcete-li opravit chybu AttributeValueMustMustMustUnique

1. Identifikujte duplicitní proxyAdresy, userPrincipalName nebo jinou hodnotu atributu, která chybu způsobuje. Také určit, \(které\) dva nebo více objektů jsou zapojeny do konfliktu. Sestava generovaná [službou Azure AD Connect Health pro synchronizaci](https://aka.ms/aadchsyncerrors) vám může pomoci identifikovat dva objekty.
2. Určete, který objekt by měl mít duplicitní hodnotu a který objekt by neměl.
3. Odeberte duplicitní hodnotu z objektu, který by neměl mít tuto hodnotu. Všimněte si, že byste měli provést změnu v adresáři, odkud je objekt pochází. V některých případech může být nutné odstranit jeden z objektů v konfliktu.
4. Pokud jste provedli změnu v místní službě AD, nechte Azure AD Connect synchronizovat změnu, aby se chyba opravila.

#### <a name="related-articles"></a>Související články
-[Duplicitní nebo neplatné atributy brání synchronizaci adresářů v Office 365](https://support.microsoft.com/kb/2647098)

## <a name="data-validation-failures"></a>Selhání ověření dat
### <a name="identitydatavalidationfailed"></a>Ověření identityDatase nezdařilo.
#### <a name="description"></a>Popis
Služba Azure Active Directory vynucuje různá omezení na samotná data, než povolí zápis dat do adresáře. Tato omezení mají zajistit, aby koncoví uživatelé získali nejlepší možné možnosti při používání aplikací, které jsou závislé na těchto datech.

#### <a name="scenarios"></a>Scénáře
a. Hodnota atributu UserPrincipalName má neplatné/nepodporované znaky.
b. Atribut UserPrincipalName neodpovídá požadovanému formátu.

#### <a name="how-to-fix-identitydatavalidationfailed-error"></a>Jak opravit chybu IdentityDataValidationFailed
a. Ujistěte se, že atribut userPrincipalName má podporované znaky a požadovaný formát.

#### <a name="related-articles"></a>Související články
* [Příprava na zřízení uživatelů prostřednictvím synchronizace adresářů s Office 365](https://support.office.com/article/Prepare-to-provision-users-through-directory-synchronization-to-Office-365-01920974-9e6f-4331-a370-13aea4e82b3e)

### <a name="federateddomainchangeerror"></a>FederatedDomainChangeError
#### <a name="description"></a>Popis
Výsledkem tohoto případu je chyba synchronizace **FederatedDomainChangeError,** když se přípona userprincipalname uživatele změní z jedné federované domény na jinou federovně.

#### <a name="scenarios"></a>Scénáře
Pro synchronizované uživatele byla přípona UserPrincipalName změněna z jedné federované domény na jinou federovovně. Například *UserPrincipalName =\@bob contoso.com* byl změněn na *UserPrincipalName =\@bob fabrikam.com*.

#### <a name="example"></a>Příklad
1. Bob Smith, účet pro Contoso.com, bude přidán jako nový uživatel ve službě Active Directory pomocí userprincipalnamebob@contoso.com
2. Bob se přesune do jiné divize Contoso.com nazývanou Fabrikam.com a jejich UserPrincipalName se změní nabob@fabrikam.com
3. contoso.com i fabrikam.com domény jsou federované domény se službou Azure Active Directory.
4. Bobje userPrincipalName nezíská aktualizována a výsledky v "FederatedDomainChangeError" chyba synchronizace.

#### <a name="how-to-fix"></a>Jak problém vyřešit
Pokud byla přípona UserPrincipalName uživatele aktualizována z\@bob@**contoso.com** na bob**fabrikam.com**, kde jsou **federované domény** **contoso.com** i **fabrikam.com** , opravte chybu synchronizace pomocí těchto kroků.

1. Aktualizujte userprincipalname uživatele ve službě bob@contoso.com Azure bob@contoso.onmicrosoft.comAD z na . S modulem Azure AD PowerShell můžete použít následující příkaz PowerShellu:`Set-MsolUserPrincipalName -UserPrincipalName bob@contoso.com -NewUserPrincipalName bob@contoso.onmicrosoft.com`
2. Povolte další cyklus synchronizace k pokusu o synchronizaci. Tentokrát synchronizace bude úspěšná a bude aktualizovat UserPrincipalName Bob podle bob@fabrikam.com očekávání.

#### <a name="related-articles"></a>Související články
* [Po změně hlavního názvu uživatele uživatelského účtu na jinou federovosadu nejsou nástroje Azure Active Directory Sync synchronizovány se změnami.](https://support.microsoft.com/help/2669550/changes-aren-t-synced-by-the-azure-active-directory-sync-tool-after-you-change-the-upn-of-a-user-account-to-use-a-different-federated-domain)

## <a name="largeobject"></a>LargeObject
### <a name="description"></a>Popis
Pokud atribut překročí povolený limit velikosti, omezení délky nebo počet limit nastavený schématem služby Azure Active Directory, výsledkem operace synchronizace dojde k chybě synchronizace **LargeObject** nebo **ExceededAllowedLength.** K této chybě obvykle dochází u následujících atributů.

* userCertificate
* userSMIMECertificate
* thumbnailPhoto
* proxyAddresses

### <a name="possible-scenarios"></a>Možné scénáře
1. Atribut userCertificate uživatele bobu ukládá příliš mnoho certifikátů přiřazených Bobovi. Ty mohou zahrnovat starší certifikáty s ukončenou platností. Pevný limit je 15 certifikátů. Další informace o tom, jak zpracovat chyby LargeObject s atributem userCertificate, naleznete v článku [Zpracování chyb LargeObject způsobených atributem userCertificate](tshoot-connect-largeobjecterror-usercertificate.md).
2. Atribut userSMIMECertificate uživatele Boba ukládá příliš mnoho certifikátů přiřazených Bobovi. Ty mohou zahrnovat starší certifikáty s ukončenou platností. Pevný limit je 15 certifikátů.
3. Bobova sada miniatur Photo ve službě Active Directory je příliš velká na synchronizaci ve službě Azure AD.
4. Během automatického plnění atributu ProxyAddresses ve službě Active Directory má objekt přiřazeno příliš mnoho proxyadres.

### <a name="how-to-fix"></a>Jak problém vyřešit
1. Ujistěte se, že atribut způsobující chybu je v rámci povoleného omezení.

## <a name="existing-admin-role-conflict"></a>Konflikt existující role správce

### <a name="description"></a>Popis
**Konflikt existující role správce** nastane u objektu uživatele během synchronizace, pokud tento objekt uživatele má:

- oprávnění správce a
- stejné UserPrincipalName jako existující objekt Azure AD

Azure AD Connect není povoleno měkké shody objektu uživatele z místní služby AD s objektem uživatele ve službě Azure AD, který má přiřazenou roli pro správu.  Další informace naleznete [v tématu Základní připisplnění UserPrincipalName služby Azure AD](plan-connect-userprincipalname.md)

![Stávající správce](media/tshoot-connect-sync-errors/existingadmin.png)


### <a name="how-to-fix"></a>Jak problém vyřešit
Chcete-li tento problém vyřešit, postupujte takto:

1. Odeberte účet Azure AD (vlastník) ze všech rolí správce. 
2. **Pevný Odstranit** objekt v karanténě v cloudu. 
3. Další cyklus synchronizace se postará o měkké párování místního uživatele s cloudovým účtem (protože uživatel cloudu už není globální ga). 
4. Obnovte členství v rolích vlastníka. 

>[!NOTE]
>Roli pro správu můžete znovu přiřadit existujícímu objektu uživatele po dokončení měkké shody mezi objektem místního uživatele a objektem uživatele Azure AD.

## <a name="related-links"></a>Související odkazy
* [Vyhledání objektů služby Active Directory v Centru správy služby Active Directory](https://technet.microsoft.com/library/dd560661.aspx)
* [Jak se dotazovat služby Azure Active Directory na objekt pomocí prostředí Azure Active Directory PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx)

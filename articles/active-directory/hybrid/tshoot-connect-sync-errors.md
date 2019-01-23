---
title: 'Azure AD Connect: Řešení chyb při synchronizaci | Dokumentace Microsoftu'
description: Vysvětluje, jak řešit chyby zjištěné během synchronizace se službou Azure AD Connect.
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
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 34a719c8fb62a2b993320d1bd9f97f9d47abf494
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54463303"
---
# <a name="troubleshooting-errors-during-synchronization"></a>Řešení chyb při synchronizaci
Při synchronizaci dat identity z Windows Server Active Directory (AD DS) do Azure Active Directory (Azure AD), může dojít k chybám. Tento článek obsahuje přehled různých typů chyb synchronizace, některé z možných scénářů, které způsobují tyto chyby a potenciální způsoby, jak opravit chyby. Tento článek obsahuje běžné typy chyb a nemusí zahrnovat všechny možné chyby.

 Tento článek předpokládá čtecí modul je obeznámen se základní [návrh konceptech služby Azure AD a Azure AD Connect](plan-connect-design-concepts.md).

S nejnovější verzí služby Azure AD Connect \(. srpna 2016 nebo novějším\), Sestava chyb synchronizace je k dispozici v [webu Azure portal](https://aka.ms/aadconnecthealth) jako součást služby Azure AD Connect Health pro synchronizaci.

Od 1. září 2016 [Azure Active Directory duplicitní atribut odolnost proti chybám](how-to-connect-syncservice-duplicate-attribute-resiliency.md) funkce bude povolena ve výchozím nastavení pro všechny *nové* Tenantů Azure Active Directory. Tato funkce bude automaticky povolená u stávajících tenantů v nadcházejících měsících.

Azure AD Connect provede tři typy operací z adresáře, které provádí synchronizaci: Import, synchronizace a exportu. Chyby může proběhnout ve všech operacích. Tento článek zaměřuje především na chyby při exportu do služby Azure AD.

## <a name="errors-during-export-to-azure-ad"></a>Chyby při exportu do služby Azure AD
Následující část popisuje různé druhy chyb synchronizace, které se můžou objevit během operace exportu do služby Azure AD pomocí konektoru služby Azure AD. Tento konektor lze identifikovat podle názvu formát je "contoso. *onmicrosoft.com*".
Chyby při exportu do služby Azure AD označuje, že operace \(přidání, aktualizace, odstranění atd.\) pokus o Azure AD Connect \(synchronizační modul\) v Azure Active Directory se nepovedl.

![Přehled chyby exportu](./media/tshoot-connect-sync-errors/Export_Errors_Overview_01.png)

## <a name="data-mismatch-errors"></a>Data chyby
### <a name="invalidsoftmatch"></a>InvalidSoftMatch
#### <a name="description"></a>Popis
* Pokud Azure AD Connect \(synchronizační modul\) instruuje Azure Active Directory a přidat nebo aktualizovat objekty, Azure AD odpovídá na příchozí pomocí objektu **sourceAnchor** atribut **immutableId**  atribut objektů ve službě Azure AD. Tuto shodu je volána **pevné odpovídat**.
* Při Azure AD **nenajde** libovolný objekt, který odpovídá **immutableId** atributem **sourceAnchor** atribut příchozího objektu před zřizováním nový objekt, vrátí se zpět k použití atributů ProxyAddresses ani atribut UserPrincipalName pro vyhledání shody. Tuto shodu je volána **Obnovitelně odpovídat**. Obnovitelně odpovídat je navržená tak, aby odpovídaly objekty již existuje ve službě Azure AD (zdroje ve službě Azure AD) se nové objekty je přidán nebo aktualizován v průběhu synchronizace, které představují stejnou entitu (uživatele, skupiny) v místním prostředí.
* **InvalidSoftMatch** chybě dochází, když pevný shoda nenajde žádné odpovídající objekt **a** obnovitelně shoda najde odpovídající objekt, ale tento objekt má jinou hodnotu *immutableId* než příchozí objekt *SourceAnchor*, navrhněte, odpovídající objekt byl synchronizován s jiným objektem z na místní služby Active Directory.

Jinými slovy, obnovitelně shoda pro práci, objekt, který má být obnovitelné porovnány s by neměl mít libovolnou hodnotu pro *immutableId*. Pokud některé objekty s *immutableId* sady s hodnotou je selhání pevného match ale splňující kritéria konfigurace soft-match, operace by za následek chybu InvalidSoftMatch synchronizace.

Schéma služby Active Directory Azure nepovoluje dva nebo více objektů mají stejnou hodnotu z následujících atributů. \(Nejedná se o vyčerpávající seznam.\)

* ProxyAddresses
* UserPrincipalName
* onPremisesSecurityIdentifier
* ObjectId

> [!NOTE]
> [Azure AD atribut duplicitní atribut odolnost](how-to-connect-syncservice-duplicate-attribute-resiliency.md) funkce se zavádí také jako výchozí chování služby Azure Active Directory.  Tím se sníží počet chyb synchronizace vidět Azure AD Connect (stejně jako ostatní klienty synchronizace) tím, že Azure AD odolnější ve způsobu, jakým zpracovává duplicitní ProxyAddresses UserPrincipalName atributy a k dispozici v prostředích místní AD. Tuto funkci nelze vyřešit chyby duplicitních. Proto data stále je potřeba opravit. Ale umožňuje zřizovat nové objekty, které jsou jinak blokovaná zřizuje z důvodu duplicitních hodnot ve službě Azure AD. Tím se sníží také číslo vrácen do klienta synchronizace chyb synchronizace.
> Pokud tato funkce je povolená pro vašeho Tenanta, nezobrazí se chyby synchronizace InvalidSoftMatch během zřizování nových objektů.
>
>

#### <a name="example-scenarios-for-invalidsoftmatch"></a>Ukázkové scénáře pro InvalidSoftMatch
1. Existují dva nebo více objektů se stejnou hodnotou atributu ProxyAddresses v místní službě Active Directory. Pouze jeden získávání zřídí ve službě Azure AD.
2. Dva nebo více objektů se stejnou hodnotou pro atribut userPrincipalName v místní službě Active Directory existuje. Pouze jeden získávání zřídí ve službě Azure AD.
3. Objekt byl přidán do místní služby Active Directory se stejnou hodnotou atributu ProxyAddresses jako existující objekt ve službě Azure Active Directory. Ve službě Azure Active Directory není získávání zřízený objekt přidán v místním prostředí.
4. Objekt byla přidána do místní služby Active Directory se stejnou hodnotou atributu userPrincipalName jako účet ve službě Azure Active Directory. Objekt není získávání zřízený v Azure Active Directory.
5. Synchronizované účet se přesunul z doménové struktury A doménová struktura služby serveru B. služby Azure AD Connect (synchronizační stroj) používala atribut ObjectGUID vypočítat SourceAnchor. Po přesunutí doménové struktury hodnotu SourceAnchor se liší. Nový objekt (z doménové struktury B) se nezdařilo se provést synchronizaci s existující objekt ve službě Azure AD.
6. Objekt synchronizace je teď omylem odstranili z místní služby Active Directory a nový objekt byl vytvořen ve službě Active Directory pro stejnou entitu (například uživatel) bez odstranění účtu ve službě Azure Active Directory. Nový účet se nepovedlo synchronizovat s existující objekt služby Azure AD.
7. Azure AD Connect byla odinstalování a opětovném nainstalování. Při přeinstalaci jiný atribut byl zvolen jako SourceAnchor. Všechny objekty, které bylo dříve synchronizovaná zastavila synchronizaci s chybou InvalidSoftMatch.

#### <a name="example-case"></a>Příklad případů:
1. **Bob Smith** je synchronizované uživatele v Azure Active Directory z na místní služby Active Directory této *contoso.com*
2. Bob Smith **UserPrincipalName** je nastaven jako **bobs@contoso.com**.
3. **"abcdefghijklmnopqrstuv =="** je **SourceAnchor** vypočte tak Azure AD Connect s použitím Bob Smith **objectGUID** z místní služby Active Directory, která je  **immutableId** Bob Smith v Azure Active Directory.
4. Bob také obsahuje následující hodnoty **proxyAddresses** atribut:
   * smtp: bobs@contoso.com
   * smtp: bob.smith@contoso.com
   * **smtp: bob@contoso.com**
5. Nový uživatel **Bob Taylora**, se přidá do místní služby Active Directory.
6. Bob Taylora **UserPrincipalName** je nastaven jako **bobt@contoso.com**.
7. **"abcdefghijkl0123456789 ==" "** je **sourceAnchor** vypočte tak Azure AD Connect s použitím Bob Taylora **objectGUID** z na místní služby Active Directory. Bob Taylora objektu má není synchronizované do Azure Active Directory ještě.
8. Bob Taylora má následující hodnoty atributu proxyAddresses
   * smtp: bobt@contoso.com
   * smtp: bob.taylor@contoso.com
   * **smtp: bob@contoso.com**
9. Během synchronizace Azure AD Connect rozpozná přidání Roberta Taylora v místní službě Active Directory a pokládat službu Azure AD, chcete-li provést stejnou změnu.
10. Azure AD se nejprve provést pevné shoda. To znamená, budou vyhledány Pokud libovolný objekt s immutableId se rovná "abcdefghijkl0123456789 ==". Pevné shoda se nezdaří, protože žádný jiný objekt ve službě Azure AD bude mít tento immutableId.
11. Azure AD se pak pokusí konfigurace soft-match Bob Taylora. To znamená bude hledat, pokud je jakýkoli objekt s proxyAddresses rovna tří hodnot, včetně smtp: bob@contoso.com
12. Azure AD se najít objekt Bob Smith kritériím konfigurace soft-match. Ale tento objekt má hodnotu z immutableId = "abcdefghijklmnopqrstuv ==". Označuje, který tento objekt byl synchronizovat z jiného objektu z místní služby Active Directory. Díky tomu se Azure AD nelze konfigurace soft-match těchto objektů a výsledky v **InvalidSoftMatch** došlo k chybě synchronizace.

#### <a name="how-to-fix-invalidsoftmatch-error"></a>K vyřešení chyby InvalidSoftMatch
Dva objekty se různé SourceAnchor je nejčastější příčinou chyby InvalidSoftMatch \(immutableId\) mají stejnou hodnotu pro ProxyAddresses a/nebo UserPrincipalName atributy, které se používají během konfigurace soft-match zpracování v Azure AD. Pokud chcete problém neplatný Obnovitelně shody

1. Identifikujte duplicitní proxyAddresses, userPrincipalName nebo jiných hodnotu atributu, který je příčinou chyby. Také zjistit, jaké dvě \(nejmíň\) objektů jsou zahrnuty v konfliktu. Sestavy vygenerované [Azure AD Connect Health pro synchronizaci](https://aka.ms/aadchsyncerrors) vám mohou pomoci zjistit tyto dva objekty.
2. Určete který z objektů by měl mít i nadále duplicitní hodnota a který z objektů by neměla.
3. Odeberte duplicitní hodnotu z objektu, u kterých by měla tuto hodnotu. V adresáři, ve kterém se objekt pochází z by měl provést změnu. V některých případech budete muset odstranit jeden z objektů v konfliktu.
4. Pokud jste provedli změny v místní AD, dejte Azure AD Connect synchronizuje změny.

Zprávy o chybách synchronizace v rámci Azure AD Connect Health pro synchronizaci se aktualizují každých 30 minut a zahrnout chyby z poslední pokus o synchronizaci.

> [!NOTE]
> ImmutableId, definicí, by neměly měnit v průběhu životnosti objektu. Pokud Azure AD Connect nebyla nakonfigurovaná u některých scénářů v úvahu z výše uvedeného seznamu, můžou být nakonec v situaci, kde Azure AD Connect vypočítá jinou hodnotu SourceAnchor objektu AD, který představuje stejnou entitu (stejnou skupinu uživatelů / / kontaktů atd.), který má existující objekt AD Azure, kterou chcete dál používat.
>
>

#### <a name="related-articles"></a>Související články
* [Duplicitní nebo neplatný atributů, které brání synchronizace adresářů v Office 365](https://support.microsoft.com/kb/2647098)

### <a name="objecttypemismatch"></a>ObjectTypeMismatch
#### <a name="description"></a>Popis
Pokud Azure AD se pokusí obnovitelné porovnat dva objekty, je možné, že dva objekty různé "typ objektu" (například uživatele, skupiny, kontaktujte atd.) mají stejné hodnoty pro atributy používané k provedení obnovitelně shoda. Duplikace tyto atributy není povolená ve službě Azure AD, operace může způsobit chyby synchronizace "ObjectTypeMismatch".

#### <a name="example-scenarios-for-objecttypemismatch-error"></a>Ukázkové scénáře pro ObjectTypeMismatch chybu
* Vytvoření skupiny zabezpečení pošty v Office 365. Správce přidá nový uživatel nebo kontakt v v místní instalaci AD (který se nesynchronizuje do Azure AD ještě) se stejnou hodnotou atributu ProxyAddresses jako příslušná skupina Office 365.

#### <a name="example-case"></a>Příklad případ
1. Správce vytvoří novou skupinu zabezpečení pošty v Office 365 pro oddělení daně a poskytuje e-mailovou adresu jako tax@contoso.com. Tato skupina je přiřazena hodnota atributu ProxyAddresses **smtp: tax@contoso.com**
2. Nový uživatel připojí Contoso.com a vytvoření účtu pro uživatele v místním prostředí pomocí proxyAddress jako **smtp: tax@contoso.com**
3. Pokud Azure AD Connect synchronizuje nový uživatelský účet, se zobrazí chybová zpráva "ObjectTypeMismatch".

#### <a name="how-to-fix-objecttypemismatch-error"></a>K vyřešení chyby ObjectTypeMismatch
Nejběžnějším důvodem ObjectTypeMismatch chyby je, že dva objekty jiného typu (uživatele, skupiny, kontaktujte atd.) mají stejnou hodnotu atributu ProxyAddresses. Pokud chcete problém ObjectTypeMismatch:

1. Identifikujte duplicitní proxyAddresses (nebo jiného atributu) hodnotu, která je příčinou chyby. Také zjistit, jaké dvě \(nejmíň\) objektů jsou zahrnuty v konfliktu. Sestavy vygenerované [Azure AD Connect Health pro synchronizaci](https://aka.ms/aadchsyncerrors) vám mohou pomoci zjistit tyto dva objekty.
2. Určete který z objektů by měl mít i nadále duplicitní hodnota a který z objektů by neměla.
3. Odeberte duplicitní hodnotu z objektu, u kterých by měla tuto hodnotu. Všimněte si, že byste změnu provést v adresáři, ve kterém se objekt pochází z. V některých případech budete muset odstranit jeden z objektů v konfliktu.
4. Pokud jste provedli změny v místní AD, dejte Azure AD Connect synchronizuje změny. Sestava chyb synchronizace v rámci Azure AD Connect Health pro synchronizaci se aktualizuje každých 30 minut a patří i chyby z poslední pokus o synchronizaci.

## <a name="duplicate-attributes"></a>Duplicitní atributy
### <a name="attributevaluemustbeunique"></a>AttributeValueMustBeUnique
#### <a name="description"></a>Popis
Schéma služby Active Directory Azure nepovoluje dva nebo více objektů mají stejnou hodnotu z následujících atributů. To je, že každý objekt v Azure AD musí mít jedinečnou hodnotu z těchto atributů v dané instanci.

* ProxyAddresses
* UserPrincipalName

Pokud Azure AD Connect se pokusí o přidání nového objektu nebo aktualizovat existující objekt s hodnotou, která je už přiřazený k jinému objektu v Azure Active Directory pro výše uvedené atributy, operace vyústí v chybu synchronizace "AttributeValueMustBeUnique".

#### <a name="possible-scenarios"></a>Možné scénáře:
1. Duplicitní hodnota přiřazena již synchronizovaného objektu, který je v konfliktu s jiným objektem synchronizované.

#### <a name="example-case"></a>Příklad případů:
1. **Bob Smith** je synchronizované uživatele v Azure Active Directory z na místní služby Active Directory contoso.com
2. Bob Smith **UserPrincipalName** v místním prostředí je nastaven jako **bobs@contoso.com**.
3. Bob také obsahuje následující hodnoty **proxyAddresses** atribut:
   * smtp: bobs@contoso.com
   * smtp: bob.smith@contoso.com
   * **smtp: bob@contoso.com**
4. Nový uživatel **Bob Taylora**, se přidá do místní služby Active Directory.
5. Bob Taylora **UserPrincipalName** je nastaven jako **bobt@contoso.com**.
6. **Bob Taylora** má následující hodnoty **ProxyAddresses** atribut i. smtp: bobt@contoso.com ii. smtp: bob.taylor@contoso.com
7. Objekt Taylora Bob úspěšně synchronizována s Azure AD.
8. Správce rozhodl aktualizaci Bob Taylora **ProxyAddresses** atribut s následující hodnotou: i. **smtp: bob@contoso.com**
9. Azure AD se pokus o aktualizaci Bob Taylora objektu ve službě Azure AD s vyšší hodnotou, ale tato operace se nezdaří jako, že hodnota ProxyAddresses je už přiřazený k Bob Smith, výsledkem chyba "AttributeValueMustBeUnique".

#### <a name="how-to-fix-attributevaluemustbeunique-error"></a>K vyřešení chyby AttributeValueMustBeUnique
Dva objekty se různé SourceAnchor je nejčastější příčinou chyby AttributeValueMustBeUnique \(immutableId\) mají stejnou hodnotu pro atributy ProxyAddresses a/nebo UserPrincipalName. Pokud chcete problém AttributeValueMustBeUnique chyba

1. Identifikujte duplicitní proxyAddresses, userPrincipalName nebo jiných hodnotu atributu, který je příčinou chyby. Také zjistit, jaké dvě \(nejmíň\) objektů jsou zahrnuty v konfliktu. Sestavy vygenerované [Azure AD Connect Health pro synchronizaci](https://aka.ms/aadchsyncerrors) vám mohou pomoci zjistit tyto dva objekty.
2. Určete který z objektů by měl mít i nadále duplicitní hodnota a který z objektů by neměla.
3. Odeberte duplicitní hodnotu z objektu, u kterých by měla tuto hodnotu. Všimněte si, že byste změnu provést v adresáři, ve kterém se objekt pochází z. V některých případech budete muset odstranit jeden z objektů v konfliktu.
4. Pokud jste provedli změny v místní AD, dejte Azure AD Connect synchronizuje změny k chybě pro opravu.

#### <a name="related-articles"></a>Související články
-[Duplicitní nebo neplatný atributů, které brání synchronizace adresářů v Office 365](https://support.microsoft.com/kb/2647098)

## <a name="data-validation-failures"></a>Selhání ověřování dat.
### <a name="identitydatavalidationfailed"></a>IdentityDataValidationFailed
#### <a name="description"></a>Popis
Azure Active Directory vynucuje různá omezení na vlastní data předtím, než tato data k zápisu do adresáře. Tato omezení jsou Ujistěte se, že koncoví uživatelé získávají nejlepší možné zkušenosti při používání aplikací, které na těchto datech závisí.

#### <a name="scenarios"></a>Scénáře
a. Hodnota atributu UserPrincipalName obsahuje neplatné znaky.
b. Atribut UserPrincipalName požadovaný formát nedodržuje.

#### <a name="how-to-fix-identitydatavalidationfailed-error"></a>K vyřešení chyby IdentityDataValidationFailed
a. Ujistěte se, že se znaky a požadovaný formát podporuje atribut userPrincipalName.

#### <a name="related-articles"></a>Související články
* [Příprava ke zřízení uživatelů prostřednictvím synchronizace adresářů Office 365](https://support.office.com/article/Prepare-to-provision-users-through-directory-synchronization-to-Office-365-01920974-9e6f-4331-a370-13aea4e82b3e)

### <a name="federateddomainchangeerror"></a>FederatedDomainChangeError
#### <a name="description"></a>Popis
Tento případ vede **"FederatedDomainChangeError"** došlo k chybě synchronizace, pokud přípona UserPrincipalName uživatele se změní z jedné federované domény na jinou federovanou doménu.

#### <a name="scenarios"></a>Scénáře
Synchronizované uživatele byl změněn přípona UserPrincipalName z jedné federované domény na jinou federovanou doménu v místním prostředí. Například *UserPrincipalName = bob@contoso.com*  byl změněn na *UserPrincipalName = bob@fabrikam.com* .

#### <a name="example"></a>Příklad:
1. Bob Smith, účet pro Contoso.com, získá přidat jako nový uživatel ve službě Active Directory s UserPrincipalName bob@contoso.com
2. Bob se přesune do jiné divize Contoso.com, Fabrikam.com volá a jeho hodnota UserPrincipalName se změní na bob@fabrikam.com
3. Federované domény Azure Active Directory jsou domény contoso.com a fabrikam.com.
4. UserPrincipalName Boba neaktualizuje a má za následek chybu při synchronizaci "FederatedDomainChangeError".

#### <a name="how-to-fix"></a>K vyřešení
Pokud přípona UserPrincipalName uživatele byl aktualizován z bob @**contoso.com** Bob**fabrikam.com**, kde obě **contoso.com** a **fabrikam.com** jsou **federovaných domén**, proveďte následující postup k vyřešení chyby synchronizace

1. Aktualizovat atribut UserPrincipalName daného uživatele ve službě Azure AD z bob@contoso.com k bob@contoso.onmicrosoft.com. Pomocí modulu Azure AD PowerShell můžete použít následující příkaz Powershellu: `Set-MsolUserPrincipalName -UserPrincipalName bob@contoso.com -NewUserPrincipalName bob@contoso.onmicrosoft.com`
2. Povolte další cyklus synchronizace pokusu o synchronizaci. Tato doba synchronizace bude úspěšné a bude se aktualizovat atribut UserPrincipalName Bob k bob@fabrikam.com podle očekávání.

#### <a name="related-articles"></a>Související články
* [Změny se synchronizují pomocí nástroje Azure Active Directory Sync po změně UPN uživatelský účet použít jinou federovanou doménu](https://support.microsoft.com/help/2669550/changes-aren-t-synced-by-the-azure-active-directory-sync-tool-after-you-change-the-upn-of-a-user-account-to-use-a-different-federated-domain)

## <a name="largeobject"></a>LargeObject
### <a name="description"></a>Popis
Pokud atribut překračuje maximální povolenou velikost, maximální délku nebo limit počtu určené schéma služby Azure Active Directory, operace synchronizace vede **LargeObject** nebo **ExceededAllowedLength**došlo k chybě synchronizace. Obvykle k této chybě dochází pro následující atributy

* userCertificate
* userSMIMECertificate
* thumbnailPhoto
* proxyAddresses

### <a name="possible-scenarios"></a>Možné scénáře
1. Atributem userCertificate Boba ukládá moc velký počet certifikátů přiřazené Bob. Ty mohou zahrnovat certifikáty starší, jejichž platnost vypršela. Pevný limit je 15 certifikáty. Další informace o tom, jak zpracování chyb LargeObject s atributem userCertificate najdete v článku [chyb LargeObject způsobených atributem userCertificate](tshoot-connect-largeobjecterror-usercertificate.md).
2. Atribut userSMIMECertificate Boba ukládá moc velký počet certifikátů přiřazené Bob. Ty mohou zahrnovat certifikáty starší, jejichž platnost vypršela. Pevný limit je 15 certifikáty.
3. Bob thumbnailphoto nastavuje nastavit ve službě Active Directory je příliš velký, aby se ve službě Azure AD synchronizovat.
4. Během automatického naplnění atributu ProxyAddresses v Active Directory objekt má příliš mnoho ProxyAddresses přiřazené.

### <a name="how-to-fix"></a>K vyřešení
1. Ujistěte se, že je atribut, který způsobil chybu v rámci omezení na povolené.

## <a name="existing-admin-role-conflict"></a>Konflikt existujících rolí správce

### <a name="description"></a>Popis
**Existující konflikt Role správce** dojde u objektu uživatele během synchronizace po tento objekt uživatele:

- oprávnění správce a
- stejné UserPrincipalName jako existující objekt služby Azure AD

Azure AD Connect není povoleno obnovitelně shoda objekt uživatele z místní AD pomocí objektu uživatele ve službě Azure AD, který má přiřazenou roli pro správu.  Další informace najdete v části [naplnění Azure AD UserPrincipalName](plan-connect-userprincipalname.md)

![Stávající správce](media/tshoot-connect-sync-errors/existingadmin.png)


### <a name="how-to-fix"></a>K vyřešení
K vyřešení tohoto problému, proveďte jednu z následujících akcí:


- Zadejte hodnotu, která se neshoduje s uživateli s právy ve službě Azure AD – které se vytvoří nový uživatel ve službě Azure AD s odpovídající UserPrincipalName UserPrincipalName
- Odeberte roli správce od uživatele s rolí správce v Azure AD, která povolí měkké shody mezi místní objekt uživatele a existující objekt uživatele Azure AD.

>[!NOTE]
>Správní roli můžete znovu přiřadit na existující objekt uživatele, po dokončení obnovitelně shody mezi místní objekt uživatele a objekt uživatele Azure AD.

## <a name="related-links"></a>Související odkazy
* [Hledání objektů Active Directory v Centru správy služby Active Directory](https://technet.microsoft.com/library/dd560661.aspx)
* [Postup dotazování služby Azure Active Directory pro objekt pomocí Azure Active Directory PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx)

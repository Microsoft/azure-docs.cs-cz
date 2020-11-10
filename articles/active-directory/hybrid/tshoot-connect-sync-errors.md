---
title: 'Azure AD Connect: řešení chyb při synchronizaci | Microsoft Docs'
description: Vysvětluje, jak řešit chyby zjištěné při synchronizaci s Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 2209d5ce-0a64-447b-be3a-6f06d47995f8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/29/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a668fa9bf0ef4fd3b5451ff4c815b676fe237e51
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2020
ms.locfileid: "94410619"
---
# <a name="troubleshooting-errors-during-synchronization"></a>Řešení chyb při synchronizaci
Pokud jsou data identity synchronizovaná ze služby Windows Server Active Directory (služba AD DS) do Azure Active Directory (Azure AD), může dojít k chybám. Tento článek poskytuje přehled různých typů chyb synchronizace, některé z možných scénářů, které způsobují tyto chyby a potenciální způsoby, jak chyby opravit. Tento článek zahrnuje běžné typy chyb a nemusí pokrývat všechny možné chyby.

 Tento článek předpokládá, že čtenář je obeznámen se základními [Koncepty návrhu Azure AD a Azure AD Connect](plan-connect-design-concepts.md).

V případě nejnovější verze Azure AD Connect \( srpna 2016 nebo vyšší \) je zpráva o chybách synchronizace k dispozici v [Azure Portal](https://aka.ms/aadconnecthealth) jako součást Azure AD Connect Health pro synchronizaci.

Od 1. září Azure Active Directory 2016 bude funkce [odolnosti atributů s duplicitními atributy](how-to-connect-syncservice-duplicate-attribute-resiliency.md) ve výchozím nastavení povolená pro všechny *nové* klienty Azure Active Directory. Tato funkce bude automaticky povolena pro existující klienty v nadcházejících měsících.

Azure AD Connect provádí tři typy operací z adresářů, které uchovává synchronizace: import, synchronizace a export. V rámci všech operací může dojít k chybám. Tento článek se zaměřuje hlavně na chyby při exportu do Azure AD.

## <a name="errors-during-export-to-azure-ad"></a>Chyby při exportu do služby Azure AD
Následující část popisuje různé typy chyb synchronizace, ke kterým může dojít během operace exportu do služby Azure AD pomocí konektoru služby Azure AD. Tento konektor můžete identifikovat pomocí formátu názvu "contoso". *onmicrosoft.com* ".
Chyby při exportu do služby Azure AD znamenají, že operace \( Přidání, aktualizace, odstranění atd. \) \( Azure Active Directory Azure AD Connect synchronizace \) neproběhla úspěšně.

![Přehled chyb exportu](./media/tshoot-connect-sync-errors/Export_Errors_Overview_01.png)

## <a name="data-mismatch-errors"></a>Chyby neshody dat
### <a name="invalidsoftmatch"></a>InvalidSoftMatch
#### <a name="description"></a>Popis
* Když Azure AD Connect \( synchronizačního modulu \) instruuje Azure Active Directory, aby přidal nebo aktualizoval objekty, služba Azure AD odpovídá příchozímu objektu pomocí atributu **SourceAnchor** pro atribut **immutableId** objektů v Azure AD. Tato shoda se nazývá **těžká shoda**.
* Když Azure AD **nenajde** žádný objekt, který by odpovídal atributu **immutableId** s atributem **sourceAnchor** příchozího objektu, před zřízením nového objektu se vrátí k použití atributů proxyAddresses a userPrincipalName k nalezení shody. Tato shoda se nazývá **měkká shoda**. Měkké porovnávání je navrženo tak, aby odpovídalo objektům, které již existují ve službě Azure AD (které jsou ve službě Azure AD), s novými přidanými a aktualizovanými objekty během synchronizace, které představují stejnou entitu (uživatele, skupiny) místně.
* K chybě **InvalidSoftMatch** dojde v případě, že se při neshodě nenalezne žádný odpovídající objekt **a** měkké porovnávání najde odpovídající objekt, ale tento objekt má jinou hodnotu *immutableId* než *SourceAnchor* příchozích objektů, což naznačuje, že odpovídající objekt byl synchronizován s jiným objektem z místní služby Active Directory.

Jinými slovy, aby bylo Podmíněné porovnávání fungovat, objekt, který má být nevyhovující, by neměl mít pro *immutableId* žádnou hodnotu. Pokud některý z objektů s *immutableId* sadou s hodnotou neselže, ale splňuje kritéria pro tichou shodu, výsledkem operace bude chyba synchronizace InvalidSoftMatch.

Azure Active Directory schéma neumožňuje, aby dva nebo více objektů mělo stejnou hodnotu následujících atributů. \(Nejedná se o vyčerpávající seznam.\)

* ProxyAddresses
* UserPrincipalName
* onPremisesSecurityIdentifier
* ObjectId

> [!NOTE]
> Jako výchozí chování Azure Active Directory se také zavádí funkce [odolnosti duplicitních atributů atributů Azure AD](how-to-connect-syncservice-duplicate-attribute-resiliency.md) .  Tím se sníží počet chyb synchronizace, ke kterým dochází Azure AD Connect (stejně jako ostatní synchronizační klienti) tím, že Azure AD zpracovává duplicitní atributy ProxyAddresses a UserPrincipalName přítomné v místních prostředích služby AD. Tato funkce neopraví chyby duplikace. Proto musí být tato data stále opravena. Umožňuje ale zřídit nové objekty, které se jinak zablokují z důvodu duplicitních hodnot ve službě Azure AD. Tím se také sníží počet chyb synchronizace vrácených klientovi synchronizace.
> Pokud je tato funkce pro vašeho tenanta povolená, neuvidíte chyby synchronizace InvalidSoftMatch při zřizování nových objektů.
>
>

#### <a name="example-scenarios-for-invalidsoftmatch"></a>Příklady scénářů pro InvalidSoftMatch
1. V místní službě Active Directory existují dva nebo více objektů se stejnou hodnotou atributu ProxyAddresses. V Azure AD se zřídí jenom jedna z nich.
2. Dva nebo více objektů se stejnou hodnotou pro atribut userPrincipalName existuje v místní službě Active Directory. V Azure AD se zřídí jenom jedna z nich.
3. Objekt byl přidán do místní služby Active Directory se stejnou hodnotou atributu ProxyAddresses jako stávající objekt v Azure Active Directory. Objekt přidaný místně není v Azure Active Directory zřízen.
4. Do místní služby Active Directory byl přidán objekt se stejnou hodnotou atributu userPrincipalName jako u účtu v Azure Active Directory. Objekt není zřízen v Azure Active Directory.
5. Synchronizovaný účet se přesunul z doménové struktury A na doménovou strukturu B. Azure AD Connect (synchronizační modul) použil k výpočtu SourceAnchor atribut ObjectGUID. Po přesunutí doménové struktury se hodnota SourceAnchor liší. U nového objektu (z doménové struktury B) se nedaří synchronizovat s existujícím objektem ve službě Azure AD.
6. Synchronizovaný objekt se omylem odstranil z místní služby Active Directory a ve službě Active Directory se vytvořil nový objekt pro stejnou entitu (třeba uživatel), aniž by bylo potřeba účet odstranit v Azure Active Directory. Novému účtu se nepovedlo synchronizovat s existujícím objektem Azure AD.
7. Azure AD Connect odinstalovat a znovu nainstalovat. Během přeinstalace byl jako SourceAnchor vybrán jiný atribut. Všechny objekty, které se dřív synchronizovaly, zastavily synchronizaci s InvalidSoftMatch chybou.

#### <a name="example-case"></a>Příklad případu:
1. **Bob Smith** je synchronizovaný uživatel v Azure Active Directory z místní služby Active Directory of *contoso.com*
2. Hodnota **userPrincipalName** Jan Kovář je nastavená jako **honzuv \@ contoso.com**.
3. **"abcdefghijklmnopqrstuv = ="** je **SourceAnchor** vypočtený Azure AD Connect pomocí objektu **objectGUID** Jan Novák z místní služby Active Directory, což je **immutableId** pro Bob Smith v Azure Active Directory.
4. Bob má také následující hodnoty pro atribut **proxyAddresses** :
   * SMTP bobs@contoso.com
   * SMTP bob.smith@contoso.com
   * **SMTP: Bob \@ contoso.com**
5. Nový uživatel, **Bob Taylor** , se přidá do místní služby Active Directory.
6. Atribut **userPrincipalName** Taylor je nastaven jako **bobt \@ contoso.com**.
7. **"abcdefghijkl0123456789 = =" "** je **sourceAnchor** vypočtený pomocí Azure AD Connect pomocí objektu **objectGUID** Taylor z místní služby Active Directory. Objekt Bob Taylor se ještě nesynchronizoval na Azure Active Directory.
8. Bob Taylor má následující hodnoty pro atribut proxyAddresses
   * SMTP bobt@contoso.com
   * SMTP bob.taylor@contoso.com
   * **SMTP: Bob \@ contoso.com**
9. Během synchronizace Azure AD Connect rozpozná přidání Boba Taylor v místní službě Active Directory a požádejte Azure AD, aby provedl stejnou změnu.
10. Služba Azure AD nejprve provede závažnou shodu. To znamená, že bude hledat v případě, že objekt s immutableId se rovná "abcdefghijkl0123456789 = =". Neshoda se nezdaří, protože ve službě Azure AD nebude mít žádný jiný objekt immutableId.
11. Služba Azure AD se pak pokusí o neshodu Boba Taylor. To znamená, že bude hledat v případě, že je nějaký objekt s proxyAddresses, který se rovná třem hodnotám, včetně protokolu SMTP: bob@contoso.com
12. Služba Azure AD nalezne objekt Bob Smith, aby odpovídal kritériím pro tichou shodu. Ale tento objekt má hodnotu immutableId = "abcdefghijklmnopqrstuv = =". což znamená, že tento objekt byl synchronizován z jiného objektu z místní služby Active Directory. Proto služba Azure AD nemůže tyto objekty podmíněně vyhodnotit a výsledkem je chyba synchronizace **InvalidSoftMatch** .

#### <a name="how-to-fix-invalidsoftmatch-error"></a>Jak opravit chybu InvalidSoftMatch
Nejběžnějším důvodem pro chybu InvalidSoftMatch je, že dva objekty s různou SourceAnchor \( immutableId \) mají stejnou hodnotu pro atributy proxyAddresses a/nebo userPrincipalName, které se používají během procesu tichého porovnávání v Azure AD. Aby bylo možné opravit neplatnou neplatnou chybu

1. Identifikujte duplicitní proxyAddresses, userPrincipalName nebo jiné hodnoty atributu, které způsobují chybu. Také určete, které dva \( nebo více \) objektů jsou součástí konfliktu. Sestava vygenerovaná [Azure AD Connect Health pro synchronizaci](./how-to-connect-health-sync.md) vám může pomáhat identifikovat dva objekty.
2. Určete, který objekt by měl i nadále mít duplicitní hodnotu, a objekt by neměl být.
3. Odebere duplicitní hodnotu z objektu, který by neměl mít tuto hodnotu. Měli byste provést změnu v adresáři, ze kterého je objekt zdroj. V některých případech může být nutné odstranit jeden z objektů, které jsou v konfliktu.
4. Pokud jste provedli změnu v místní službě AD, nechte tuto změnu Azure AD Connect synchronizovat.

Zprávy o chybách synchronizace v rámci Azure AD Connect Health pro synchronizaci se každých 30 minut aktualizují a obsahují chyby z posledního pokusu o synchronizaci.

> [!NOTE]
> ImmutableId, podle definice, by se nemělo měnit během životnosti objektu. Pokud se Azure AD Connect nenakonfiguroval s některými scénáři z výše uvedeného seznamu, můžete skončit v situaci, kdy Azure AD Connect vypočítá jinou hodnotu SourceAnchor pro objekt AD, který představuje stejnou entitu (stejný uživatel, skupina/kontakt atd.) s existujícím objektem Azure AD, který chcete dál používat.
>
>

#### <a name="related-articles"></a>Související články
* [Duplicitní a neplatné atributy zabraňují synchronizaci adresářů v Microsoft 365](https://support.microsoft.com/kb/2647098)

### <a name="objecttypemismatch"></a>ObjectTypeMismatch
#### <a name="description"></a>Popis
Když se služba Azure AD pokusí dočasná shoda dvou objektů, je možné, že dva objekty různého typu objektu (například uživatel, skupina, kontakt atd.) mají stejné hodnoty pro atributy používané k provedení měkké shody. Vzhledem k tomu, že duplikace těchto atributů není ve službě Azure AD povolená, může operace způsobit chybu synchronizace "ObjectTypeMismatch".

#### <a name="example-scenarios-for-objecttypemismatch-error"></a>Příklady scénářů pro chybu ObjectTypeMismatch
* V Microsoft 365 se vytvoří skupina zabezpečení s povoleným e-mailem. Správce přidá nového uživatele nebo kontakt do místní služby AD (který ještě není synchronizovaný do Azure AD) se stejnou hodnotou atributu ProxyAddresses jako skupina Microsoft 365.

#### <a name="example-case"></a>Příklad případu
1. Správce vytvoří novou skupinu zabezpečení s povoleným e-mailem v Microsoft 365 pro daňové oddělení a poskytne e-mailovou adresu jako tax@contoso.com . Této skupině se přiřadí hodnota atributu ProxyAddresses pro **SMTP: daňový \@ contoso.com.**
2. Novému uživateli se připojí Contoso.com a účet se vytvoří pro uživatele místně s proxyAddress jako **SMTP: tax \@ contoso.com**
3. Když Azure AD Connect provede synchronizaci nového uživatelského účtu, zobrazí se chyba "ObjectTypeMismatch".

#### <a name="how-to-fix-objecttypemismatch-error"></a>Jak opravit chybu ObjectTypeMismatch
Nejběžnějším důvodem pro chybu ObjectTypeMismatch jsou dva objekty různých typů (uživatel, skupina, kontakt atd.), které mají stejnou hodnotu pro atribut ProxyAddresses. Chcete-li opravit ObjectTypeMismatch:

1. Identifikujte duplicitní hodnotu proxyAddresses (nebo jiný atribut), která způsobuje chybu. Také určete, které dva \( nebo více \) objektů jsou součástí konfliktu. Sestava vygenerovaná [Azure AD Connect Health pro synchronizaci](./how-to-connect-health-sync.md) vám může pomáhat identifikovat dva objekty.
2. Určete, který objekt by měl i nadále mít duplicitní hodnotu, a objekt by neměl být.
3. Odebere duplicitní hodnotu z objektu, který by neměl mít tuto hodnotu. Všimněte si, že byste měli provést změnu v adresáři, ze kterého je objekt zdroj. V některých případech může být nutné odstranit jeden z objektů, které jsou v konfliktu.
4. Pokud jste provedli změnu v místní službě AD, nechte tuto změnu Azure AD Connect synchronizovat. Zpráva o chybách synchronizace v rámci Azure AD Connect Health pro synchronizaci se každých 30 minut aktualizuje a obsahuje chyby z posledního pokusu o synchronizaci.

## <a name="duplicate-attributes"></a>Duplicitní atributy
### <a name="attributevaluemustbeunique"></a>AttributeValueMustBeUnique
#### <a name="description"></a>Popis
Azure Active Directory schéma neumožňuje, aby dva nebo více objektů mělo stejnou hodnotu následujících atributů. To znamená, že každý objekt ve službě Azure AD má v dané instanci jedinečnou hodnotu těchto atributů.

* ProxyAddresses
* UserPrincipalName

Pokud se Azure AD Connect pokusí přidat nový objekt nebo aktualizovat existující objekt s hodnotou pro výše uvedené atributy, které jsou již přiřazeny k jinému objektu v Azure Active Directory, výsledkem operace je chyba synchronizace "AttributeValueMustBeUnique".

#### <a name="possible-scenarios"></a>Možné scénáře:
1. K již synchronizovanému objektu je přiřazena duplicitní hodnota, která je v konfliktu s jiným synchronizovaným objektem.

#### <a name="example-case"></a>Příklad případu:
1. **Bob Smith** je synchronizovaný uživatel v Azure Active Directory z místní služby Active Directory of contoso.com
2. Hodnota **userPrincipalName** Smith v místním prostředí je nastavená jako **honzuv \@ contoso.com**.
3. Bob má také následující hodnoty pro atribut **proxyAddresses** :
   * SMTP bobs@contoso.com
   * SMTP bob.smith@contoso.com
   * **SMTP: Bob \@ contoso.com**
4. Nový uživatel, **Bob Taylor** , se přidá do místní služby Active Directory.
5. Atribut **userPrincipalName** Taylor je nastaven jako **bobt \@ contoso.com**.
6. **Bob Taylor** má následující hodnoty pro atribut **proxyAddresses** i. SMTP: bobt@contoso.com II. SMTP bob.taylor@contoso.com
7. Objekt Bob Taylor se úspěšně synchronizuje se službou Azure AD.
8. Správce rozhodl aktualizovat atribut **proxyAddresses** Boba Taylor o následující hodnotu: i. **SMTP: Bob \@ contoso.com**
9. Služba Azure AD se pokusí aktualizovat objekt Bob Taylor ve službě Azure AD s výše uvedenou hodnotou, ale tato operace selže, protože ProxyAddresses hodnota je již přiřazena k Bobovi Smith, což způsobí chybu "AttributeValueMustBeUnique".

#### <a name="how-to-fix-attributevaluemustbeunique-error"></a>Jak opravit chybu AttributeValueMustBeUnique
Nejběžnějším důvodem pro chybu AttributeValueMustBeUnique je, že dva objekty s různou SourceAnchor \( immutableId \) mají stejnou hodnotu pro atributy proxyAddresses a/nebo userPrincipalName. Aby bylo možné opravit chybu AttributeValueMustBeUnique

1. Identifikujte duplicitní proxyAddresses, userPrincipalName nebo jinou hodnotu atributu, která způsobuje chybu. Také určete, které dva \( nebo více \) objektů jsou součástí konfliktu. Sestava vygenerovaná [Azure AD Connect Health pro synchronizaci](./how-to-connect-health-sync.md) vám může pomáhat identifikovat dva objekty.
2. Určete, který objekt by měl i nadále mít duplicitní hodnotu, a objekt by neměl být.
3. Odebere duplicitní hodnotu z objektu, který by neměl mít tuto hodnotu. Všimněte si, že byste měli provést změnu v adresáři, ze kterého je objekt zdroj. V některých případech může být nutné odstranit jeden z objektů, které jsou v konfliktu.
4. Pokud jste provedli změnu v místní službě AD, nechte Azure AD Connect synchronizovat změnu chyby, aby se opravila.

#### <a name="related-articles"></a>Související články
-[Duplicitní a neplatné atributy zabraňují synchronizaci adresářů v Microsoft 365](https://support.microsoft.com/kb/2647098)

## <a name="data-validation-failures"></a>Selhání ověřování dat
### <a name="identitydatavalidationfailed"></a>IdentityDataValidationFailed
#### <a name="description"></a>Popis
Azure Active Directory před tím, než povolí zápis těchto dat do adresáře, vynutila různá omezení nad samotnými daty. Tato omezení slouží k zajištění toho, aby koncoví uživatelé získali nejlepší možné prostředí při používání aplikací, které jsou na těchto datech závislé.

#### <a name="scenarios"></a>Scénáře
a. Hodnota atributu UserPrincipalName obsahuje neplatné/nepodporované znaky.
b. Atribut UserPrincipalName nedodržuje požadovaný formát.

#### <a name="how-to-fix-identitydatavalidationfailed-error"></a>Jak opravit chybu IdentityDataValidationFailed
a. Ujistěte se, že atribut userPrincipalName má podporované znaky a požadovaný formát.

#### <a name="related-articles"></a>Související články
* [Příprava na zřízení uživatelů prostřednictvím synchronizace adresářů na Microsoft 365](https://support.office.com/article/Prepare-to-provision-users-through-directory-synchronization-to-Office-365-01920974-9e6f-4331-a370-13aea4e82b3e)

### <a name="federateddomainchangeerror"></a>FederatedDomainChangeError
#### <a name="description"></a>Popis
V takovém případě dojde k chybě synchronizace **"FederatedDomainChangeError"** , pokud je přípona názvu userPrincipalName uživatele změněna z jedné federované domény na jinou federované domény.

#### <a name="scenarios"></a>Scénáře
Pro synchronizovaného uživatele se přípona UserPrincipalName změnila z jedné federované domény na jinou federované domény místně. Například *userPrincipalName = jan \@ contoso.com* byl změněn na *userPrincipalName = Bob \@ fabrikam.com*.

#### <a name="example"></a>Příklad
1. Bob Smith, účet pro Contoso.com, se přidá jako nový uživatel ve službě Active Directory s použitím UserPrincipalName. bob@contoso.com
2. Bob se přesune na jiné rozdělení Contoso.com s názvem Fabrikam.com a jejich UserPrincipalName se změní na bob@fabrikam.com
3. Contoso.com i fabrikam.com domény jsou federované domény s Azure Active Directory.
4. Hodnota userPrincipalName elementu Bob se neaktualizuje a výsledkem je chyba synchronizace "FederatedDomainChangeError".

#### <a name="how-to-fix"></a>Jak problém vyřešit
Pokud byla přípona UserPrincipalName uživatele aktualizována z bob@ **contoso.com** na Bob \@ **fabrikam.com** , kde **contoso.com** i **fabrikam.com** jsou **federované domény** , opravte chybu synchronizace pomocí těchto kroků.

1. Aktualizujte v Azure AD atribut UserPrincipalName uživatele z bob@contoso.com na bob@contoso.onmicrosoft.com . Pomocí modulu Azure AD PowerShell můžete použít následující příkaz prostředí PowerShell: `Set-MsolUserPrincipalName -UserPrincipalName bob@contoso.com -NewUserPrincipalName bob@contoso.onmicrosoft.com`
2. Umožněte dalšímu synchronizačnímu cyklu pokus o synchronizaci. Tato doba synchronizace bude úspěšná a bude aktualizovat UserPrincipalName aplikace Bob na bob@fabrikam.com podle očekávání.

#### <a name="related-articles"></a>Související články
* [Po změně hlavního názvu uživatele (UPN) uživatelského účtu na používání jiné federované domény nejsou změny synchronizované nástrojem Azure Active Directory Sync.](https://support.microsoft.com/help/2669550/changes-aren-t-synced-by-the-azure-active-directory-sync-tool-after-you-change-the-upn-of-a-user-account-to-use-a-different-federated-domain)

## <a name="largeobject"></a>LargeObject
### <a name="description"></a>Popis
Pokud atribut překročí povolený limit velikosti, omezení délky nebo omezení počtu, který je nastaven Azure Active Directory schématem, výsledkem operace synchronizace je chyba synchronizace **LargeObject** nebo **ExceededAllowedLength** . K této chybě obvykle dochází pro následující atributy.

* userCertificate
* userSMIMECertificate
* thumbnailPhoto
* proxyAddresses

### <a name="possible-scenarios"></a>Možné scénáře
1. Atribut userCertificate Boba ukládá příliš mnoho certifikátů přiřazených Bobovi. Mezi ně můžou patřit starší certifikáty s vypršenou platností. Pevný limit je 15 certifikátů. Další informace o tom, jak zpracovávat chyby LargeObject pomocí atributu userCertificate, najdete v článku [zpracování LargeObject chyb způsobených atributem userCertificate](tshoot-connect-largeobjecterror-usercertificate.md).
2. Atribut userSMIMECertificate Boba ukládá příliš mnoho certifikátů přiřazených Bobovi. Mezi ně můžou patřit starší certifikáty s vypršenou platností. Pevný limit je 15 certifikátů.
3. ThumbnailPhoto sada Boba ve službě Active Directory je moc velká pro synchronizaci ve službě Azure AD.
4. Během automatického naplnění atributu ProxyAddresses ve službě Active Directory má objekt příliš mnoho přiřazených ProxyAddresses.

### <a name="how-to-fix"></a>Jak problém vyřešit
1. Zajistěte, aby byl atribut způsobil chybu v rámci povoleného omezení.

## <a name="existing-admin-role-conflict"></a>Konflikt stávající role správce

### <a name="description"></a>Popis
**Ke konfliktu role správce** dojde v uživatelském objektu během synchronizace, pokud má tento objekt uživatele:

- oprávnění správce a
- stejná hodnota UserPrincipalName jako stávající objekt Azure AD

Azure AD Connect nemá povolenou možnost Soft odpovídat objektu uživatele z místní služby AD s uživatelským objektem ve službě Azure AD, který má přiřazenou roli správce.  Další informace najdete v tématu [populace Azure AD userPrincipalName](plan-connect-userprincipalname.md)

![Stávající správce](media/tshoot-connect-sync-errors/existingadmin.png)


### <a name="how-to-fix"></a>Jak problém vyřešit
Chcete-li vyřešit tento problém, postupujte takto:

1. Odeberte účet Azure AD (Owner) ze všech rolí správce. 
2. **Pevným odstraněním** objektu v karanténě v cloudu. 
3. Další cyklus synchronizace se postará o tiché porovnání místního uživatele s cloudovým účtem (vzhledem k tomu, že uživatel cloudu už není globálním GAm). 
4. Obnovte členství v rolích pro vlastníka. 

>[!NOTE]
>Roli pro správu můžete přiřadit existujícímu uživatelskému objektu znovu, až se dočasná shoda mezi místním uživatelským objektem a objektem uživatele Azure AD dokončí.

## <a name="related-links"></a>Související odkazy
* [Vyhledat objekty služby Active Directory v Centrum správy služby Active Directory](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd560661(v=ws.10))
* [Postup dotazování Azure Active Directory pro objekt pomocí Azure Active Directory PowerShellu](/previous-versions/azure/jj151815(v=azure.100))
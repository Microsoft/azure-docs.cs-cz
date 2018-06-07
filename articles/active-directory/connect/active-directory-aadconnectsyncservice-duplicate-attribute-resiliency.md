---
title: Odolnost identity synchronizace a duplicitní atribut | Microsoft Docs
description: Nové chování o práci s objekty s konflikty UPN nebo ProxyAddress během synchronizace adresářů přes Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 537a92b7-7a84-4c89-88b0-9bce0eacd931
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.component: hybrid
ms.author: markvi
ms.openlocfilehash: cfed9d32e919cc3c1b7b9c2a6ea5ddb31f2a8fb9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34593204"
---
# <a name="identity-synchronization-and-duplicate-attribute-resiliency"></a>Synchronizace identit a odolnost duplicitních atributů
Duplicitní atribut odolnosti je funkce v Azure Active Directory, který bude eliminovat třecí způsobené **UserPrincipalName** a **ProxyAddress** konfliktu při spuštění jeden společnosti Microsoft Nástroje pro synchronizaci.

Tyto dva atributy jsou obecně musí být jedinečná napříč všemi **uživatele**, **skupiny**, nebo **kontaktujte** objekty v daném klienta Azure Active Directory.

> [!NOTE]
> Pouze uživatelé, může mít názvy UPN.
> 
> 

Nové chování, které tato funkce umožňuje je v části cloudu kanálu synchronizace, a proto je klient lhostejné a relevantní pro synchronizaci produktů společnosti Microsoft včetně Azure AD Connect, DirSync a MIM + konektor. Obecný pojem "sync client" je v tomto dokumentu používá k reprezentování některého z těchto produktů.

## <a name="current-behavior"></a>Aktuální chování
Pokud dojde pokusu o zřídit nový objekt s hodnotou UPN nebo ProxyAddress, která porušuje toto omezení jedinečnosti, Azure Active Directory blokuje tento objekt z vytváří. Podobně pokud jedinečný UPN nebo ProxyAddress je aktualizován objekt, že se aktualizace nezdaří. Pokus o zřizování nebo aktualizace se pokus o synchronizaci klient při každém cyklu export a nadále fungovat, dokud nebude bude konflikt vyřešen. Při každém pokusu o se vygeneruje chyba sestav e-mailu a zaznamenána chyba klientem synchronizace.

## <a name="behavior-with-duplicate-attribute-resiliency"></a>Chování při odolnosti duplicitní atribut
Místo zcela neúspěšné zřizovat nebo aktualizovat objekt s duplicitní atribut, Azure Active Directory "umístí do karantény" duplicitní atribut, který by způsobila porušení omezení jedinečnosti. Pokud tento atribut je požadován pro zřizování, jako jsou UserPrincipalName, službu přiřadí hodnotu zástupného symbolu. Formát dočasné hodnoty  
"***<OriginalPrefix>+ < 4DigitNumber > @<InitialTenantDomain>. onmicrosoft.com***".  
Pokud atribut není potřeba, jako například **ProxyAddress**, Azure Active Directory jednoduše umístí do karantény atribut konfliktu a pokračuje v objektu vytvoření nebo aktualizace.

Při umístění do karantény atribut, informace o konflikt odesílána ve stejné chyba sestav e-mailu, použít v původním chování. Ale tyto informace se zobrazí pouze v zprávy o chybách jednou, když se stane karanténu, ho nebude dále být přihlášení budoucí e-mailů. Navíc vzhledem k tomu exportu pro tento objekt byl úspěšný, klienta synchronizace neprotokoluje chybu a neopakuje vytvořením / aktualizace operace při následné synchronizaci cykly.

Pro podporu toto chování se přidal do třídy objektu uživatele, skupiny a obraťte se na nový atribut:  
**DirSyncProvisioningErrors**

To je více hodnotami atributu, který se používá k ukládání konfliktní atributy, které by způsobila porušení omezení jedinečnosti se přidaly normálně. Časovač úlohy na pozadí byla povolena v Azure Active Directory, který spouští každou hodinu a hledat duplicitní atribut konflikty, které byly vyřešeny a automaticky odebere dotyčném atributy z karantény.

### <a name="enabling-duplicate-attribute-resiliency"></a>Povolíte odolnost duplicitní atribut
Duplicitní atribut odolnosti bude nové výchozí chování napříč všech klientech služby Azure Active Directory. Na bude ve výchozím nastavení pro všechny klienty, kteří povolena synchronizace poprvé 22. srpna 2016 nebo novější. Tuto funkci povolit v dávkách bude mít klienty, kteří povolena synchronizace před toto datum. Zavádění tohoto řešení bude zahájena v září 2016 a odešlou se e-mailové oznámení na každého klienta technické oznámení kontaktu s konkrétní datum, kdy bude tato funkce povolena.

> [!NOTE]
> Jakmile je zapnutý duplicitní atribut odolnosti nelze zakázat.

Pokud chcete zkontrolovat, pokud je funkce pro vašeho klienta, můžete tak učinit stažení nejnovější verze modulu Azure Active Directory PowerShell a spuštěním:

`Get-MsolDirSyncFeatures -Feature DuplicateUPNResiliency`

`Get-MsolDirSyncFeatures -Feature DuplicateProxyAddressResiliency`

> [!NOTE]
> Rutiny Set-MsolDirSyncFeature už můžete proaktivně zapnout funkci duplicitní atribut odolnosti předtím, než je zapnutý pro vašeho klienta. Abyste mohli otestovat funkci, musíte vytvořit nového klienta Azure Active Directory.

## <a name="identifying-objects-with-dirsyncprovisioningerrors"></a>Identifikaci objektů s DirSyncProvisioningErrors
K identifikaci objektů, které mají tyto chyby kvůli konfliktům duplicitní vlastnost, Azure Active Directory PowerShell a portálu pro správu Office 365 aktuálně dvěma způsoby. Existují plánujete rozšířit do další portálu závislosti vytváření sestav v budoucnu.

### <a name="azure-active-directory-powershell"></a>Azure Active Directory PowerShell
Pro rutiny prostředí PowerShell v tomto tématu platí následující:

* Všechny tyto rutiny jsou velká a malá písmena.
* **– ErrorCategory PropertyConflict** musí být zahrnut. Aktuálně nejsou žádné jiné typy **ErrorCategory**, ale to může v budoucnu rozšířit.

Nejprve Začínáme spuštěním **Connect-MsolService** a zadání přihlašovacích údajů správce klienta.

Potom pomocí následující rutiny a operátory zobrazíte chyby různými způsoby:

1. [Zobrazit všechny](#see-all)
2. [Podle typu vlastnosti](#by-property-type)
3. [Podle konfliktní hodnoty](#by-conflicting-value)
4. [Pomocí vyhledávání řetězec](#using-a-string-search)
5. [Seřadit](#sorted)
6. [V omezené množství nebo všechny](#in-a-limited-quantity-or-all)

#### <a name="see-all"></a>Zobrazit všechno
Po připojení Pokud chcete zobrazit obecné seznam atributů zřizování chyby v klientovi spusťte:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict`

To vytvoří výsledek takto:  
 ![Get-MsolDirSyncProvisioningError](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/1.png "Get-MsolDirSyncProvisioningError")  

#### <a name="by-property-type"></a>Podle typu vlastnosti
Chcete-li zobrazit chyby podle typu vlastnosti, přidejte **- PropertyName** příznak s **UserPrincipalName** nebo **ProxyAddresses** argument:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName UserPrincipalName`

Nebo

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName ProxyAddresses`

#### <a name="by-conflicting-value"></a>Podle konfliktní hodnoty
Pokud chcete zobrazit chyby týkající se určité vlastnosti přidat **- PropertyValue** příznak (**- PropertyName** musí použít také při přidávání tento příznak):

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyValue User@domain.com -PropertyName UserPrincipalName`

#### <a name="using-a-string-search"></a>Pomocí vyhledávání řetězec
Uděláte to k využívání široký řetězec hledání **- SearchString** příznak. Je možné používat nezávisle ze všech výše uvedených příznaky, s výjimkou produktů **- ErrorCategory PropertyConflict**, který bude vždy požadován:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -SearchString User`

#### <a name="in-a-limited-quantity-or-all"></a>V omezené množství nebo všechny
1. **Shluku <Int>**  lze použít k omezení dotazu na konkrétní počet hodnot.
2. **Všechny** lze použít k zajištění jsou načteny všechny výsledky v případě, že existuje velký počet chyb.

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -MaxResults 5`

## <a name="office-365-admin-portal"></a>Portál správy Office 365
Chyby synchronizace adresáře můžete zobrazit v Centru správy Office 365. Na portálu Office 365 je sestava zobrazena pouze **uživatele** objekty, které mají tyto chyby. Nezobrazuje informace o konflikty mezi **skupiny** a **kontakty**.

![Aktivní uživatelé](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/1234.png "aktivní uživatelé")

Pokyny o tom, jak zobrazit chyby synchronizace adresáře v Centru pro správu Office 365 najdete v tématu [identifikovat chyby synchronizace adresáře v Office 365](https://support.office.com/en-us/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067).

### <a name="identity-synchronization-error-report"></a>Sestava chyby synchronizace identit
Pokud se objekt se konflikt duplicitní atribut zpracovává toto nové chování oznámení je součástí standardní e-mailovou zprávu o chybách synchronizace identit, které je odesláno oznámení technické kontaktovat u klienta. Však dojde důležité změně toto chování. V minulosti informace o konflikt duplicitní atribut by být součástí každých dalších chybách dokud konflikt se vyřešil. S toto nové chování oznámení chyba pro danou konflikt zobrazovat jenom jednou – v době, kdy konfliktní atribut v karanténě.

Tady je příklad vzhled e-mailové oznámení pro konflikt ProxyAddress:  
    ![Aktivní uživatelé](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/6.png "aktivní uživatelé")  

## <a name="resolving-conflicts"></a>Řešení konfliktů
Řešení potíží s svoji strategii a řešení pro tyto chyby by neměl liší od způsobu duplicitní atribut chyby byly zpracovány v minulosti. Jediným rozdílem je, že úlohy časovače přesune prostřednictvím klienta na straně služby automaticky přidat atribut dotyčném k objektu správné, jakmile bude konflikt vyřešen.

V následujícím článku popisuje různé strategie pro odstraňování potíží a řešení: [duplicitní nebo neplatné atributy zabránit synchronizaci adresářů v Office 365](https://support.microsoft.com/kb/2647098).

## <a name="known-issues"></a>Známé problémy
Žádná z těchto známých problémů způsobí snížení dat výpadku nebo služby. Několik z nich jsou estetické, ostatní způsobit standard "*před odolnosti*" duplicitní atribut chyby vyvolání místo umístění do karantény atribut konfliktu a jiné způsobí, že některé chyby tak, aby vyžadovala velmi Ruční oprava up.

**Základní chování:**

1. Objektů s určitým atributem konfigurace dál dostávat export chyby oproti duplicitní atributy, které jsou v karanténě.  
   Příklad:
   
    a. Po vytvoření nového uživatele ve službě AD s název UPN **Joe@contoso.com** a ProxyAddress **smtp:Joe@contoso.com**
   
    b. Vlastnosti tohoto objektu v konfliktu s existující skupiny, kde je ProxyAddress **SMTP:Joe@contoso.com**.
   
    c. Při exportu **ProxyAddress konflikt** místo nutnosti konflikt atributy v karanténě, je vržena chyba. Operace je opakována při každém cyklu následná synchronizace, jako by byl před povolením funkce odolnost proti chybám.
2. Pokud se stejnou adresou SMTP se vytvoří místní dvě skupiny, jeden server selže zřídit na první pokus o s standardní duplicitní **ProxyAddress** chyba. Ale duplicitní hodnota je správně umístit do karantény, při příštím synchronizačním cyklu.

**Sestavy portálu Office**:

1. Podrobná chybová zpráva pro dva objekty v sadě konflikt UPN je stejný. To znamená, že budou obě neměla jejich UPN změnit / do karantény, když ve skutečnosti jenom jeden z nich měl žádná data změnit.
2. Podrobná chybová zpráva pro konflikt UPN zobrazuje nesprávné displayName pro uživatele, kteří byli jejich UPN, změnit nebo v karanténě. Příklad:
   
    a. **Uživatel A** synchronizuje si první s **UPN = User@contoso.com** .
   
    b. **Uživatel B** proveden pokus o synchronizovat až další s **UPN = User@contoso.com** .
   
    c. **Uživatele B** (UPN) se změní na **User1234@contoso.onmicrosoft.com** a **User@contoso.com** se přidá do **DirSyncProvisioningErrors**.
   
    d. Chybová zpráva pro **uživatel B** by měl označuje, že **uživatel A** již **User@contoso.com** podle názvu UPN, ale zobrazuje **uživatel B** vlastní displayName.

**Sestava chyby synchronizace identit**:

Odkaz pro *postup, chcete-li vyřešit tento problém* je nesprávný:  
    ![Aktivní uživatelé](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/6.png "aktivní uživatelé")  

By měla odkazovat na [ https://aka.ms/duplicateattributeresiliency ](https://aka.ms/duplicateattributeresiliency).

## <a name="see-also"></a>Další informace najdete v tématech
* [Synchronizace služby Azure AD Connect](active-directory-aadconnectsync-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](active-directory-aadconnect.md)
* [Identifikovat chyby synchronizace adresáře v Office 365](https://support.office.com/en-us/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067)


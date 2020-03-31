---
title: Synchronizace identit a odolnost proti duplicitnímu atributu | Dokumenty společnosti Microsoft
description: Nové chování, jak zpracovat objekty s konflikty UPN nebo ProxyAddress během synchronizace adresářů pomocí Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 537a92b7-7a84-4c89-88b0-9bce0eacd931
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/15/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5585f0cd04dca4145f0322db9d625e35372b24b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78298339"
---
# <a name="identity-synchronization-and-duplicate-attribute-resiliency"></a>Synchronizace identit a odolnost duplicitních atributů
Odolnost proti chybám duplicitního atributu je funkce ve službě Azure Active Directory, která eliminuje tření způsobené konflikty **UserPrincipalName** a SMTP **ProxyAddress** při spuštění jednoho z synchronizačních nástrojů společnosti Microsoft.

Tyto dva atributy jsou obecně nutné být jedinečné pro všechny objekty **uživatele**, **skupiny**nebo **kontaktu** v daném tenantovi služby Azure Active Directory.

> [!NOTE]
> Upn mohou mít pouze uživatelé.
> 
> 

Nové chování, které tato funkce umožňuje, je v cloudové části kanálu synchronizace, proto je nezávislá na klientovi a je relevantní pro všechny synchronizační produkty Microsoftu, včetně Azure AD Connect, DirSync a MIM + Connector. Obecný termín "klient synchronizace" se v tomto dokumentu používá k reprezentaci některého z těchto produktů.

## <a name="current-behavior"></a>Aktuální chování
Pokud dojde k pokusu o zřízení nového objektu s hodnotou UPN nebo ProxyAddress, která porušuje toto omezení jedinečnosti, služba Azure Active Directory zablokuje vytvoření tohoto objektu. Podobně pokud je objekt aktualizován nejedinečným hlavní název zabezpečení nebo proxyadresa, aktualizace se nezdaří. Pokus o zřizování nebo aktualizace je opakován klientem synchronizace při každém cyklu exportu a nadále selhává, dokud není konflikt vyřešen. Při každém pokusu je generován e-mail zprávy o chybách a klient synchronizace zaznamená chybu.

## <a name="behavior-with-duplicate-attribute-resiliency"></a>Chování s odolností proti chybám duplicitního atributu
Místo toho, aby zcela nedaří zřídit nebo aktualizovat objekt s duplicitním atributem, služba Azure Active Directory "umístí do karantény" duplicitní atribut, který by porušil omezení jedinečnosti. Pokud tento atribut je vyžadován pro zřizování, jako je UserPrincipalName, služba přiřadí zástupnou hodnotu. Formát těchto dočasných hodnot je  
OriginalPrefix>_**+\<4DigitNumber \@ \<>InitialTenantDomain>.onmicrosoft.com . \<**_

Proces odolnosti proti chybám atributů zpracovává pouze hodnoty UPN a SMTP **ProxyAddress.**

Pokud atribut není vyžadován, například **proxyadresa**, služba Azure Active Directory jednoduše umístí atribut konfliktu do karantény a pokračuje v vytváření nebo aktualizaci objektu.

Při umístění atributu do karantény jsou informace o konfliktu odeslány ve stejném e-mailu zprávy o chybě, který byl použit ve starém chování. Tyto informace se však zobrazí pouze v sestavě chyb jednou, když dojde k karanténě, nebude nadále zaznamenávána v budoucích e-mailech. Vzhledem k tomu, že export pro tento objekt proběhl úspěšně, klient synchronizace nezaprotokoluje chybu a nezopakují operaci vytvoření nebo aktualizace při následujících cyklech synchronizace.

Pro podporu tohoto chování byl přidán nový atribut do tříd objektů User, Group a Contact:  
**DirSyncProvisioningErrors**

Toto je vícehodnotový atribut, který se používá k ukládání konfliktních atributů, které by porušovaly omezení jedinečnosti, pokud by byly přidány normálně. Ve službě Azure Active Directory byla povolena úloha časovače na pozadí, která se spouští každou hodinu, aby hledala vyřešené konflikty duplicitních atributů a automaticky odebere dané atributy z karantény.

### <a name="enabling-duplicate-attribute-resiliency"></a>Povolení odolnosti proti chybám duplicitního atributu
Odolnost proti chybám duplicitních atributů bude nové výchozí chování ve všech tenantech služby Azure Active Directory. Ve výchozím nastavení bude zapnutý pro všechny klienty, kteří poprvé povolili synchronizaci 22. Klienti, kteří povolili synchronizaci před tímto datem, budou mít tuto funkci povolenou v dávkách. Toto zavedení začne v září 2016 a e-mailové oznámení bude odesláno na každého klienta technické oznámení kontakt s konkrétním datem, kdy bude funkce povolena.

> [!NOTE]
> Jakmile je zapnuta odolnost proti chybám duplicitního atributu, nelze ji zakázat.

Chcete-li zkontrolovat, zda je tato funkce povolena pro vašeho tenanta, můžete tak učinit stažením nejnovější verze modulu Azure Active Directory PowerShell a spuštěním:

`Get-MsolDirSyncFeatures -Feature DuplicateUPNResiliency`

`Get-MsolDirSyncFeatures -Feature DuplicateProxyAddressResiliency`

> [!NOTE]
> Rutina Set-MsolDirSyncFeature již nelze použít k aktivnímu povolení funkce odolnosti proti chybám duplicitního atributu před zapnutím pro vašeho tenanta. Abyste mohli tuto funkci otestovat, budete muset vytvořit nového klienta služby Azure Active Directory.

## <a name="identifying-objects-with-dirsyncprovisioningerrors"></a>Identifikace objektů s DirSyncProvisioningErrors
V současné době existují dvě metody k identifikaci objektů, které mají tyto chyby z důvodu duplicitní konflikty vlastností, Azure Active Directory PowerShell a [Microsoft 365 admin center](https://admin.microsoft.com). Existují plány rozšířit na další portál na základě vykazování v budoucnu.

### <a name="azure-active-directory-powershell"></a>Azure Active Directory PowerShell
Pro rutiny prostředí PowerShell v tomto tématu platí následující:

* Všechny následující rutiny jsou malá a velká písmena.
* **–ErrorCategory PropertyConflict** musí být vždy zahrnuty. V současné době neexistují žádné jiné typy **ErrorCategory**, ale to může být rozšířena v budoucnu.

Nejprve můžete začít spuštěním **služby Connect-MsolService** a zadáním přihlašovacích údajů pro správce klienta.

Potom pomocí následujících rutin a operátorů zobrazit chyby různými způsoby:

1. [Zobrazit vše](#see-all)
2. [Podle typu vlastnosti](#by-property-type)
3. [Podle konfliktní hodnoty](#by-conflicting-value)
4. [Použití hledání řetězců](#using-a-string-search)
5. Seřazeny
6. [V omezeném množství nebo](#in-a-limited-quantity-or-all)

#### <a name="see-all"></a>Zobrazit vše
Po připojení zobrazíte obecný seznam chyb zřizování atributů v spuštění klienta:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict`

Výsledkem je následující:  
 ![Get-MsolDirSyncProvisioningError](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/1.png "Get-MsolDirSyncProvisioningError")  

#### <a name="by-property-type"></a>Podle typu vlastnosti
Chcete-li zobrazit chyby podle typu vlastnosti, přidejte příznak **-PropertyName** s argumentem **UserPrincipalName** nebo **ProxyAddresses:**

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName UserPrincipalName`

Nebo

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName ProxyAddresses`

#### <a name="by-conflicting-value"></a>Kolizující hodnotou
Chcete-li zobrazit chyby týkající se určité vlastnosti, přidejte příznak **-PropertyValue** (**-PropertyName** musí být také použit při přidávání tohoto příznaku):

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyValue User@domain.com -PropertyName UserPrincipalName`

#### <a name="using-a-string-search"></a>Použití hledání v řetězci
Chcete-li provést hledání v širokém řetězci, použijte příznak **-SearchString.** To lze použít nezávisle na všech výše uvedených příznaků, s výjimkou **-ErrorCategory PropertyConflict**, který je vždy vyžadován:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -SearchString User`

#### <a name="in-a-limited-quantity-or-all"></a>V omezeném množství nebo
1. **MaxResults \<Int>** lze omezit dotaz na určitý počet hodnot.
2. **Všechny** lze zajistit všechny výsledky jsou načteny v případě, že existuje velký počet chyb.

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -MaxResults 5`

## <a name="microsoft-365-admin-center"></a>Centrum pro správu Microsoftu 365
Chyby synchronizace adresářů můžete zobrazit v Centru pro správu Microsoftu 365. Sestava v Centru pro správu Microsoft 365 zobrazuje pouze **uživatelské** objekty, které mají tyto chyby. Nezobrazuje informace o konfliktech mezi **skupinami** a **kontakty**.

![Aktivní uživatelé](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/1234.png "Aktivní uživatelé")

Pokyny k zobrazení chyb synchronizace adresářů v Centru pro správu Microsoftu 365 najdete [v tématu Identifikace chyb synchronizace adresářů v Office 365](https://support.office.com/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067).

### <a name="identity-synchronization-error-report"></a>Zpráva o chybách synchronizace identity
Pokud je objekt s duplicitním atributem zpracován s tímto novým chováním, je oznámení zahrnuto do e-mailu zprávy o chybě standardní synchronizace identity, který je odeslán kontaktu technického oznámení pro klienta. V tomto chování však dochází k důležité změně. V minulosti by informace o konfliktu duplicitních atributů byly zahrnuty do každé následné zprávy o chybě, dokud nebyl konflikt vyřešen. S tímto novým chováním se oznámení o chybě pro daný konflikt zobrazí pouze jednou v době, kdy je konfliktní atribut umístěn do karantény.

Zde je příklad toho, co e-mailové oznámení vypadá pro proxyaddress konfliktu:  
    ![Aktivní uživatelé](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/6.png "Aktivní uživatelé")  

## <a name="resolving-conflicts"></a>Řešení konfliktů
Řešení potíží strategie a řešení taktiky pro tyto chyby by se neměly lišit od způsobu, jakým byly zpracovány chyby duplicitní atribut v minulosti. Jediným rozdílem je, že úloha časovače se protloukne klientem na straně služby a automaticky přidá daný atribut do správného objektu po vyřešení konfliktu.

Následující článek popisuje různé strategie řešení potíží a řešení: [Duplicitní nebo neplatné atributy zabraňují synchronizaci adresářů v Office 365](https://support.microsoft.com/kb/2647098).

## <a name="known-issues"></a>Známé problémy
Žádný z těchto známých problémů nezpůsobí ztrátu dat nebo snížení stavu služby. Některé z nich jsou estetické, jiné způsobit standardní "*pre-odolnost*" duplicitní atribut chyby, které mají být vyvolány namísto umístění do karantény konfliktu atribut, a další způsobí, že některé chyby vyžadují další ruční oprava.

**Základní chování:**

1. Objekty s konfigurací určitých atributů nadále přijímat chyby exportu na rozdíl od duplicitní atributy jsou v karanténě.  
   Například:
   
    a. Nový uživatel je vytvořen ve službě AD s UPN **Joe\@contoso.com** a ProxyAddress **\@smtp:Joe contoso.com**
   
    b. Vlastnosti tohoto objektu jsou v konfliktu s existující skupinou, kde proxyadresa je **SMTP:Joe\@contoso.com**.
   
    c. Při exportu je vyvolána chyba **konfliktu proxyadresa** namísto karantény atributů konfliktu. Operace je opakována při každém následujícím cyklu synchronizace, stejně jako by tomu bylo před povolením funkce odolnosti proti chybám.
2. Pokud jsou dvě skupiny vytvořeny místně se stejnou adresou SMTP, jedna se nepodaří zřídit při prvním pokusu se standardní duplicitní **chybou ProxyAddress.** Duplicitní hodnota je však správně umístěna do karantény při dalším cyklu synchronizace.

**Sestava portálu Office**:

1. Podrobná chybová zpráva pro dva objekty v sadě konfliktů UPN je stejná. To znamená, že oba měli své UPN změnil / v karanténě, když ve skutečnosti jen jeden z nich měl nějaké údaje změnily.
2. Podrobná chybová zpráva pro konflikt hlavního názvu uživatele zobrazuje nesprávný název zobrazení pro uživatele, kterému byla změněna nebo umístěna do karantény. Například:
   
    a. **Uživatel A** se nejprve synchronizuje s **upn\@= uživatel contoso.com**.
   
    b. **Uživatel B** se pokouší o synchronizaci s **UPN\@= uživatel contoso.com**.
   
    c. **Uživatel B** Upn se změní na **User1234\@contoso.onmicrosoft.com** a **uživatel\@contoso.com** je přidán do **DirSyncProvisioningErrors**.
   
    d. Chybová zpráva pro **uživatele B** by měla znamenat, že **uživatel A** již má uživatele **\@contoso.com** jako hlavní název uživatele, ale zobrazí vlastní displayName uživatele **B.**

**Zpráva o chybách synchronizace identity**:

Odkaz *na kroky, jak tento problém vyřešit,* je nesprávný:  
    ![Aktivní uživatelé](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/6.png "Aktivní uživatelé")  

Měl by [https://aka.ms/duplicateattributeresiliency](https://aka.ms/duplicateattributeresiliency)poukázat na .

## <a name="see-also"></a>Viz také
* [Synchronizace Azure AD Connect](how-to-connect-sync-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)
* [Identifikace chyb synchronizace adresářů v Office 365](https://support.office.com/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067)


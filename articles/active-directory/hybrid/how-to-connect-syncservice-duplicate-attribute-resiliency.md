---
title: Synchronizace identity a duplicitní odolnost atributů | Microsoft Docs
description: Nové chování při zpracování objektů pomocí hlavního názvu uživatele (UPN) nebo ProxyAddress koliduje během synchronizace adresářů pomocí Azure AD Connect.
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
ms.topic: how-to
ms.date: 01/15/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e09dd6a127bd04ae698cb6cad2ffd7f35e3b51c3
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2020
ms.locfileid: "94413424"
---
# <a name="identity-synchronization-and-duplicate-attribute-resiliency"></a>Synchronizace identit a odolnost duplicitních atributů
Odolnost duplicitních atributů je funkce v Azure Active Directory, která eliminuje tření způsobené ProxyAddress **a konflikty** protokolu **ProxyAddress** SMTP při spuštění některého z nástrojů pro synchronizaci od společnosti Microsoft.

Tyto dva atributy se obecně vyžadují jako jedinečné v rámci všech objektů **uživatele** , **skupiny** nebo **kontaktu** v daném klientovi Azure Active Directory.

> [!NOTE]
> UPN můžou mít jenom uživatelé.
> 
> 

Nové chování, které tato funkce umožňuje, je v cloudové části synchronizačního kanálu, proto je nezávislá klienta a relevantní pro libovolný produkt pro synchronizaci Microsoftu, včetně Azure AD Connect, DirSync a konektoru MIM +. V tomto dokumentu se používá obecný pojem "synchronizace klienta", který představuje některý z těchto produktů.

## <a name="current-behavior"></a>Aktuální chování
Pokud dojde k pokusu o zřízení nového objektu s hodnotou hlavního názvu uživatele (UPN) nebo ProxyAddress, která porušuje toto omezení jedinečnosti, Azure Active Directory blokuje vytvoření objektu. Podobně, pokud je objekt aktualizován pomocí nejedinečného hlavního názvu uživatele (UPN) nebo ProxyAddress, aktualizace se nezdařila. Pokus o zřízení nebo aktualizace se zopakuje u synchronizačního klienta na každém cyklu exportu a pokračuje v selhání, dokud konflikt nebude vyřešen. Při každém pokusu se vygeneruje e-mail s oznámením o chybách a synchronizační klient se zaprotokoluje chyba.

## <a name="behavior-with-duplicate-attribute-resiliency"></a>Chování s odolností duplicitních atributů
Místo úplného selhání při zřizování nebo aktualizaci objektu s duplicitním atributem Azure Active Directory "karanténa" duplicitní atribut, který by narušil omezení jedinečnosti. Pokud je tento atribut vyžadován pro zřizování, jako je UserPrincipalName, služba přiřadí zástupnou hodnotu. Formát těchto dočasných hodnot je  
_**\<OriginalPrefix> + \<4DigitNumber> \@ \<InitialTenantDomain> . onmicrosoft.com**_.

Proces odolnosti atributů zpracovává pouze hodnoty hlavního názvu uživatele (UPN) a SMTP **ProxyAddress** .

Pokud atribut není vyžadován, jako je například  **ProxyAddress** , Azure Active Directory jednoduše umístí do karantény atribut konflikt a pokračuje v vytvoření nebo aktualizaci objektu.

Po umístění atributu do karantény se informace o konfliktu odesílají do stejného e-mailu s hlášením o chybách, který se používá ve starém chování. Tyto informace se však v hlášení o chybách zobrazí pouze jednou, když dojde k umístění karantény, není nadále přihlášena k budoucím e-mailům. Vzhledem k tomu, že export pro tento objekt byl úspěšný, synchronizační klient neprotokoluje chybu a při dalších synchronizačních cyklech neopakuje operaci vytvoření/aktualizace.

Pro podporu tohoto chování byl do třídy objektu uživatel, skupina a kontakt přidán nový atribut:  
**DirSyncProvisioningErrors**

Jedná se o vícehodnotový atribut, který se používá k uložení konfliktních atributů, které by porušily omezení jedinečnosti, pokud by se měly přidat normálně. Úloha časovače na pozadí byla povolena v Azure Active Directory, která se spouští každou hodinu pro hledání duplicitních konfliktů atributů, které byly vyřešeny, a automaticky odstraňuje atributy z karantény.

### <a name="enabling-duplicate-attribute-resiliency"></a>Povoluje se odolnost duplicitních atributů.
Odolnost duplicitních atributů bude novým výchozím chováním ve všech Azure Active Directorych klientech. Ve výchozím nastavení bude pro všechny klienty s povolenou synchronizací poprvé 22 2016 nebo novější. Klienti, kteří mají povolenou synchronizaci před tímto datem, budou mít povolenou funkci v dávkách. Toto zavedení začne v září 2016 a pošle se e-mailové oznámení každému klientovi s technickým oznámením s konkrétním datem, kdy bude funkce povolená.

> [!NOTE]
> Když je zapnutá odolnost duplicitního atributu, nejde zakázat.

Pokud chcete zjistit, jestli je funkce pro vašeho tenanta povolená, můžete to udělat stažením nejnovější verze modulu Azure Active Directory PowerShellu a spuštěním:

`Get-MsolDirSyncFeatures -Feature DuplicateUPNResiliency`

`Get-MsolDirSyncFeatures -Feature DuplicateProxyAddressResiliency`

> [!NOTE]
> Nemůžete už používat rutinu Set-MsolDirSyncFeature k proaktivnímu povolení funkce odolnosti atributu před tím, než je pro vašeho tenanta zapnutá. Aby bylo možné funkci otestovat, budete muset vytvořit nového tenanta Azure Active Directory.

## <a name="identifying-objects-with-dirsyncprovisioningerrors"></a>Identifikace objektů s DirSyncProvisioningErrors
V současné době existují dvě metody identifikace objektů, které mají tyto chyby kvůli konfliktům duplicitních vlastností, Azure Active Directory PowerShellu a [centru pro správu Microsoft 365](https://admin.microsoft.com). Existují plány pro další vytváření sestav na základě portálu v budoucnu.

### <a name="azure-active-directory-powershell"></a>Azure Active Directory PowerShell
Pro rutiny PowerShellu v tomto tématu platí následující:

* U všech následujících rutin se rozlišují velká a malá písmena.
* **– ErrorCategory PropertyConflict** musí být zahrnuto vždy. V tuto chvíli neexistují žádné jiné typy **ErrorCategory** , ale to může být v budoucnu prodloužené.

Nejdřív Začněte spuštěním **Connect-MsolService** a zadáním přihlašovacích údajů pro správce tenanta.

Pak použijte následující rutiny a operátory k zobrazení chyb různými způsoby:

1. [Zobrazit vše](#see-all)
2. [Podle typu vlastnosti](#by-property-type)
3. [Konfliktní hodnota](#by-conflicting-value)
4. [Použití vyhledávání řetězců](#using-a-string-search)
5. Sorted
6. [V omezeném množství nebo všech](#in-a-limited-quantity-or-all)

#### <a name="see-all"></a>Zobrazit vše
Po připojení zobrazíte obecný seznam chyb při zřizování atributů ve spuštění tenanta:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict`

Výsledkem je například následující:  
 ![Get-MsolDirSyncProvisioningError](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/1.png "Get-MsolDirSyncProvisioningError")  

#### <a name="by-property-type"></a>Podle typu vlastnosti
Chcete-li zobrazit chyby podle typu vlastnosti, přidejte příznak **-PropertyName** s argumentem **userPrincipalName** nebo **proxyAddresses** :

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName UserPrincipalName`

Nebo

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName ProxyAddresses`

#### <a name="by-conflicting-value"></a>Konfliktní hodnota
Pokud chcete zobrazit chyby související s konkrétní vlastností, přidejte příznak **-PropertyValue** ( **-PropertyName** se musí použít zároveň při přidávání tohoto příznaku):

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyValue User@domain.com -PropertyName UserPrincipalName`

#### <a name="using-a-string-search"></a>Použití vyhledávání řetězců
K provedení širokého vyhledávání řetězců použijte příznak **-SearchString** . Dá se použít nezávisle na všech výše uvedených příznakech, s výjimkou **-ErrorCategory PropertyConflict** , který je vždycky povinný:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -SearchString User`

#### <a name="in-a-limited-quantity-or-all"></a>V omezeném množství nebo všech
1. **MaxResults \<Int>** dá se použít k omezení dotazu na určitý počet hodnot.
2. **Vše** lze použít k zajištění toho, aby všechny výsledky byly načteny v případě, že existuje velký počet chyb.

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -MaxResults 5`

## <a name="microsoft-365-admin-center"></a>Centrum pro správu služby Microsoft 365
Chyby synchronizace adresářů můžete zobrazit v centru pro správu Microsoft 365. Sestava v centru pro správu Microsoft 365 zobrazuje pouze **uživatelské** objekty, které obsahují tyto chyby. Nezobrazuje informace o konfliktech mezi **skupinami** a **Kontakty**.

![Snímek obrazovky, který zobrazuje chyby synchronizace adresářů v centru pro správu Microsoft 365.](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/1234.png "Aktivní uživatelé")

Pokyny k zobrazení chyb synchronizace adresářů v centru pro správu Microsoft 365 najdete v tématu [Identifikace chyb synchronizace adresářů v Microsoft 365](https://support.office.com/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067).

### <a name="identity-synchronization-error-report"></a>Zpráva o chybě Synchronizace identity
Když se při tomto novém chování zpracuje objekt s duplicitním atributem, do e-mailové zprávy o chybě synchronizace identity, který se pošle na kontaktní zprávu s technickým oznámením, se pošle oznámení. V tomto chování však existuje důležitá změna. V minulosti byly informace o konfliktu duplicitních atributů zahrnuty do každé následné zprávy o chybách, dokud konflikt nebude vyřešen. S tímto novým chováním se oznámení o chybách daného konfliktu zobrazí jenom jednou – v okamžiku, kdy je konfliktní atribut v karanténě.

Tady je příklad, jak e-mailové oznámení vypadá jako u ProxyAddress konfliktu:  
    ![Snímek obrazovky, který zobrazuje příklad e-mailového oznámení pro ProxyAddress konflikt.](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/6.png "Aktivní uživatelé")  

## <a name="resolving-conflicts"></a>Řešení konfliktů
Strategie řešení potíží a řešení taktiku pro tyto chyby by se neměly lišit od způsobu zpracování duplicitních chyb atributů v minulosti. Jediným rozdílem je, že úloha časovače se v rámci tenanta na straně služby po vyřešení konfliktu automaticky přidá k příslušnému objektu.

Následující článek popisuje různé strategie řešení potíží a řešení: [duplicitní nebo neplatné atributy brání synchronizaci adresářů v sadě Office 365](https://support.microsoft.com/kb/2647098).

## <a name="known-issues"></a>Známé problémy
Žádné z těchto známých problémů nezpůsobí ztrátu dat nebo snížení úrovně služeb. Některé z nich jsou estetické, jiné způsobují standardní " *odolnost proti* chybám duplicitních atributů namísto umístění do karantény atributu konflikt a další způsobí, že některé chyby budou vyžadovat další ruční opravy.

**Základní chování:**

1. Objekty s konkrétními konfiguracemi atributů nadále obdrží chyby exportu, a to na rozdíl od duplicitních atributů, které jsou v karanténě.  
   Zde je příklad:
   
    a. Ve službě AD se vytvoří nový uživatel s hlavním názvem uživatele (UPN) **jana \@ contoso.com** a ProxyAddress **SMTP: Jan \@ contoso.com**
   
    b. Vlastnosti tohoto objektu jsou v konfliktu s existující skupinou, kde ProxyAddress je **SMTP: jan \@ contoso.com**.
   
    c. Při exportu je vyvolána chyba **konfliktu ProxyAddress** namísto výskytu atributů konfliktu v karanténě. Operace se u každého následného synchronizačního cyklu opakuje, protože by to bylo předtím, než byla povolená funkce odolnosti proti chybám.
2. Pokud se dvě skupiny vytvoří místně se stejnou adresou SMTP, jedna se nepovede zřídit při prvním pokusu se standardní duplicitní chybou **ProxyAddress** . Duplicitní hodnota je ale v rámci dalšího synchronizačního cyklu správně umístěná do karantény.

**Sestava portálu Office** :

1. Podrobná chybová zpráva pro dva objekty v sadě konfliktů hlavního názvu uživatele (UPN) je stejná. To značí, že se změnil hlavní název uživatele (UPN) i v karanténě, pokud ve skutečnosti pouze jeden z nich změnil data.
2. Podrobná chybová zpráva pro konflikt hlavního názvu uživatele (UPN) zobrazuje špatný parametr DisplayName pro uživatele, který měl své hlavní název uživatele změněn nebo v karanténě. Zde je příklad:
   
    a. **Uživatel A** nejprve synchronizuje s hlavním názvem uživatele **(UPN) = User \@ contoso.com**.
   
    b. **Uživatel B** se pokusil o synchronizaci s hlavním názvem uživatele **(UPN) = User \@ contoso.com**.
   
    c. **Uživatel B** Hlavní název uživatele (UPN) se změní na **User1234 \@ contoso.onmicrosoft.com** a **Uživatel \@ contoso.com** se přidá do **DirSyncProvisioningErrors**.
   
    d. Chybová zpráva pro **uživatele B** by měla indikovat, **že uživatel A** už **má \@ uživatel contoso.com** jako hlavní název uživatele (UPN), ale zobrazuje vlastní zobrazovaný název **uživatele b** .

**Zpráva o chybě synchronizace identity** :

Odkaz na *postup, jak tento problém vyřešit* , je nesprávný:  
    ![Aktivní uživatelé](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/6.png "Aktivní uživatelé")  

Měl by odkazovat na [https://aka.ms/duplicateattributeresiliency]() .

## <a name="see-also"></a>Viz také
* [Azure AD Connect synchronizace](how-to-connect-sync-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)
* [Identifikace chyb synchronizace adresářů v Microsoft 365](https://support.office.com/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067)
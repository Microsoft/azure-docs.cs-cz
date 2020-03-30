---
title: Zřizování aplikací pomocí filtrů oborů | Dokumenty společnosti Microsoft
description: Zjistěte, jak pomocí filtrů oborů zabránit tomu, aby se objekty v aplikacích, které podporují automatické zřizování uživatelů, zřizovaly, pokud objekt nesplňuje vaše obchodní požadavky.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: mimart
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2c667409f2abb9f1cf89ae3b34f08e0f9eec067e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79138531"
---
# <a name="attribute-based-application-provisioning-with-scoping-filters"></a>Zřizování aplikací založené na atributech pomocí filtrů oborů
Cílem tohoto článku je vysvětlit, jak používat filtry oborů k definování pravidel založených na atributech, která určují, kteří uživatelé jsou zřízeni do aplikace.

## <a name="scoping-filter-use-cases"></a>Obory filtr použití případy

Filtr oborů umožňuje službě zřizování Azure Active Directory (Azure AD) zahrnout nebo vyloučit všechny uživatele, kteří mají atribut, který odpovídá určité hodnotě. Například při zřizování uživatelů z Azure AD do aplikace SaaS používané prodejní tým, můžete určit, že pouze uživatelé s atributem "Oddělení" "Prodej" by měl být v oboru pro zřizování.

Oborové filtry lze použít odlišně v závislosti na typu zřizovacího konektoru:

* **Odchozí zřizování z Azure AD do aplikací SaaS**. Když Azure AD je zdrojový systém, [přiřazení uživatele a skupiny](../manage-apps/assign-user-or-group-access-portal.md) jsou nejběžnější metodou pro určení, kteří uživatelé jsou v oboru pro zřizování. Tato přiřazení se také používají pro povolení jednotného přihlášení a poskytují jedinou metodu pro správu přístupu a zřizování. Filtry oborů lze volitelně použít kromě přiřazení nebo místo nich k filtrování uživatelů na základě hodnot atributů.

    >[!TIP]
    > Zřizování na základě přiřazení pro podnikovou aplikaci můžete zakázat změnou nastavení v nabídce [Obor](../app-provisioning/user-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) v nastavení zřizování, aby **bylo možné synchronizovat všechny uživatele a skupiny**. Použití této možnosti a filtry oborů založené na atributech nabízejí vyšší výkon než při použití přiřazení založených na skupinách.  

* **Příchozí zřizování z aplikací HCM do Azure AD a Active Directory**. Pokud je zdrojový systém [aplikace HCM, jako je Workday,](../saas-apps/workday-tutorial.md) jsou filtry oborů primární metodou pro určení, kteří uživatelé by měli být zřízeni z aplikace HCM do služby Active Directory nebo Azure AD.

Ve výchozím nastavení konektory zřizování Azure AD nemají nakonfigurované žádné filtry oborů založené na atributech. 

## <a name="scoping-filter-construction"></a>Konstrukce filtru vymezení rozsahu

Filtr oboru se skládá z jedné nebo více *klauzulí*. Klauzule určují, kteří uživatelé mohou projít filtrem oborů vyhodnocením atributů jednotlivých uživatelů. Můžete mít například jednu klauzuli, která vyžaduje, aby se atribut "State" uživatele rovnal "New York", takže do aplikace jsou zřízeni pouze uživatelé v New Yorku. 

Jedna klauzule definuje jednu podmínku pro hodnotu jednoho atributu. Pokud více klauzulí jsou vytvořeny v jednom oboru filtru, jsou vyhodnoceny společně pomocí logiky "AND". To znamená, že všechny klauzule musí vyhodnotit na "true", aby uživatel zřídit.

Nakonec lze vytvořit více filtrů oborů pro jednu aplikaci. Pokud je k dispozici více filtrů oborů, jsou vyhodnoceny společně pomocí logiky "OR". To znamená, že pokud všechny klauzule v některém z nakonfigurovaných filtrů oborů vyhodnotit na "true", uživatel je zřízena.

Každý uživatel nebo skupina zpracovaná službou zřizování Azure AD je vždy vyhodnocena jednotlivě proti každému filtru oborů.

Jako příklad zvažte následující filtr oborů:

![Filtr oboru](./media/define-conditional-rules-for-provisioning-user-accounts/scoping-filter.PNG) 

Podle tohoto filtru oborů musí uživatelé splňovat následující kritéria, která mají být zřízena:

* Musí být v New Yorku.
* Musí pracovat ve strojovně.
* ID zaměstnance společnosti musí být mezi 1 000 000 a 2 000 000.
* Jejich pracovní pozice nesmí být null nebo prázdná.

## <a name="create-scoping-filters"></a>Vytvoření filtrů oborů
Obory filtry jsou nakonfigurované jako součást mapování atributů pro každý konektor zřizování uživatelů Azure AD. Následující postup předpokládá, že jste již nastavili automatické zřizování pro [jednu z podporovaných aplikací](../saas-apps/tutorial-list.md) a přidáváte do něj filtr oborů.

### <a name="create-a-scoping-filter"></a>Vytvoření filtru oboru
1. Na [webu Azure Portal](https://portal.azure.com)přejděte do části **Azure Active Directory** > **Enterprise Applications** > **All applications** .

2. Vyberte aplikaci, pro kterou jste nakonfigurovali automatické zřizování: například "ServiceNow".

3. Vyberte kartu **Zřizování.**

4. V části Mapování vyberte mapování, pro které chcete nakonfigurovat filtr oborů: například "Synchronizovat uživatele **služby** Azure Active Directory do služby".

5. Vyberte nabídku **Obor zdrojového objektu.**

6. Vyberte **Přidat filtr oboru**.

7. Definujte klauzuli výběrem názvu zdrojového **atributu**, **operátoru**a **hodnoty atributu,** proti které se má srovnat. Podporovány jsou následující operátory:

   a. **Rovná se**. Klauzule vrátí "true", pokud vyhodnocený atribut přesně odpovídá hodnotě vstupního řetězce (malá a velká písmena).

   b. **NENÍ ROVNO**. Klauzule vrátí "true", pokud vyhodnocený atribut neodpovídá hodnotě vstupního řetězce (malá a velká písmena).

   c. **JE PRAVDA**. Klauzule vrátí hodnotu "true", pokud vyhodnocený atribut obsahuje logickou hodnotu true.

   d. **JE FALSE**. Klauzule vrátí hodnotu "true", pokud vyhodnocený atribut obsahuje logickou hodnotu false.

   e. **JE NULL**. Klauzule vrátí hodnotu "true", pokud je vyhodnocený atribut prázdný.

   f. **NENÍ NULL**. Klauzule vrátí "true", pokud vyhodnocený atribut není prázdný.

   g. **REGEX ZÁPAS**. Klauzule vrátí hodnotu "true", pokud vyhodnocený atribut odpovídá vzoru regulárního výrazu. Například: ([1-9][0-9]) odpovídá libovolnému číslu mezi 10 a 99.

   h. **NE REGULÁRNÍ SHODA**. Klauzule vrátí hodnotu "true", pokud vyhodnocený atribut neodpovídá vzoru regulárního výrazu.
   
   i. **Greater_Than.** Klauzule vrátí hodnotu "true", pokud je vyhodnocený atribut větší než hodnota. Hodnota zadaná ve filtru oborů musí být celé číslo a atribut emitovaného uživatele musí být celé číslo [0,1,2,...]. 
   
   j. **Greater_Than_OR_EQUALS.** Klauzule vrátí hodnotu "true", pokud je vyhodnocený atribut větší nebo roven hodnotě. Hodnota zadaná ve filtru oborů musí být celé číslo a atribut emitovaného uživatele musí být celé číslo [0,1,2,...]. 
   
   k. **Zahrnuje.** Klauzule vrátí hodnotu "true", pokud vyhodnocený atribut obsahuje hodnotu řetězce (rozlišující malá a velká písmena), jak je popsáno [zde](https://docs.microsoft.com/dotnet/api/system.string.contains?view=netframework-4.8). 


>[!IMPORTANT] 
> - Filtr IsMemberOf není aktuálně podporován.
> - Equals a NOT EQUALS nejsou podporovány pro atributy s více hodnotami.

9. Volitelně opakujte kroky 7-8 přidat další klauzule oborů.

10. V **posunutí názvu filtru**přidejte název filtru oborů.

11. Vyberte **OK**.

12. Na obrazovce **Filtry rozsahu** znovu vyberte **OK.** Volitelně opakujte kroky 6-11 a přidejte další filtr oboru.

13. Na obrazovce **Mapování atributů** vyberte **Uložit.** 

>[!IMPORTANT] 
> Uložení nového filtru oborů spustí novou úplnou synchronizaci pro aplikaci, kde jsou všichni uživatelé ve zdrojovém systému znovu vyhodnoceni proti novému filtru oborů. Pokud uživatel v aplikaci byl dříve v oboru pro zřizování, ale spadá mimo rozsah, jejich účet je zakázáno nebo deprovisioned v aplikaci. Chcete-li toto výchozí chování přepsat, přečtěte si [odkaz na možnost Přeskočit odstranění uživatelských účtů, které jsou mimo rozsah](../app-provisioning/skip-out-of-scope-deletions.md).


## <a name="common-scoping-filters"></a>Běžné filtry oborů
| Atribut cíle| Operátor | Hodnota | Popis|
|----|----|----|----|
|userPrincipalName (Hlavní název uživatele)|SHODA REGULÁRNÍHO VÝRAZU|.\*@domain.com |Všichni uživatelé s userPrincipal, který má doménu @domain.com bude v oboru pro zřizování|
|userPrincipalName (Hlavní název uživatele)|NENÍ REGEX SHODA|.\*@domain.com|Všichni uživatelé s userPrincipal, který má doménu @domain.com bude mimo rozsah pro zřizování|
|Oddělení|Rovná|Prodejní|Všichni uživatelé z obchodního oddělení jsou v oboru pro zřizování|
|workerID|SHODA REGULÁRNÍHO VÝRAZU|(1[0-9][0-9][0-9][0-9][0-9][0-9])| Všichni zaměstnanci s id pracovníka mezi 1000000 a 2000000 jsou v oboru pro zřizování.|

## <a name="related-articles"></a>Související články
* [Automatizace zřizování uživatelů a deprovisioning u aplikací SaaS](../app-provisioning/user-provisioning.md)
* [Přizpůsobení mapování atributů pro zřizování uživatelů](../app-provisioning/customize-application-attributes.md)
* [Zápis výrazů pro mapování atributů](functions-for-customizing-application-data.md)
* [Oznámení o zřizování účtů](../app-provisioning/user-provisioning.md)
* [Použití SCIM k povolení automatického zřizování uživatelů a skupin z Azure Active Directory do aplikací](../app-provisioning/use-scim-to-provision-users-and-groups.md)
* [Seznam výukových programů o tom, jak integrovat aplikace SaaS](../saas-apps/tutorial-list.md)


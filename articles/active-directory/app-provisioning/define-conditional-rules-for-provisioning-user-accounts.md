---
title: Zřizování aplikací s filtry oborů | Microsoft Docs
description: Naučte se používat filtry oborů, abyste zabránili tomu, aby objekty v aplikacích, které podporují automatizované zřizování uživatelů, nebyly zřízené, pokud objekt nesplňuje vaše podnikové požadavky.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 06/08/2020
ms.author: kenwith
ms.openlocfilehash: 7fff456b7ad6e980fc3c9bda36cfcab02e2ed863
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99255827"
---
# <a name="attribute-based-application-provisioning-with-scoping-filters"></a>Zřizování aplikací na základě atributů s filtry oborů
Cílem tohoto článku je vysvětlit, jak používat filtry oborů k definování pravidel založených na atributech, která určují, kteří uživatelé se zřídí do aplikace.

## <a name="scoping-filter-use-cases"></a>Případy použití filtru oboru

Filtr oboru umožňuje službě zřizování služby Azure Active Directory (Azure AD) zahrnout nebo vyloučit všechny uživatele, kteří mají atribut, který odpovídá konkrétní hodnotě. Když například zřídíte uživatele ze služby Azure AD do aplikace SaaS, kterou používá prodejní tým, můžete určit, že pro zřizování by měl být v oboru pouze uživatelé s atributem "oddělení".

Filtry oborů se dají použít různě v závislosti na typu konektoru pro zřizování:

* **Odchozí zřizování z Azure AD do aplikací SaaS**. Když je služba Azure AD zdrojem zdrojového systému, jsou [přiřazení uživatelů a skupin](../manage-apps/assign-user-or-group-access-portal.md) Nejběžnější metodou určení toho, kteří uživatelé jsou v oboru pro zřizování. Tato přiřazení se taky používají k povolení jednotného přihlašování a k poskytnutí jediné metody správy přístupu a zřizování. Filtry oborů lze použít k filtrování uživatelů na základě hodnot atributů, a to i v případě, že jsou také přiřazení, nebo místo nich.

    >[!TIP]
    > Zřizování můžete zakázat na základě přiřazení pro podnikovou aplikaci změnou nastavení v nabídce [obor](../app-provisioning/user-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) v části Nastavení zřizování na **synchronizovat všechny uživatele a skupiny**. 

* **Příchozí zřizování z aplikací HCM do služby Azure AD a služby Active Directory**. Když je [aplikace HCM, jako](../saas-apps/workday-tutorial.md) je například Workday, zdrojovým systémem, jsou filtry oborů primární metodou určení toho, kteří uživatelé by se měli zřídit z aplikace HCM do služby Active Directory nebo Azure AD.

Ve výchozím nastavení nemají konektory zřizování služby Azure AD nakonfigurované žádné filtry oborů založené na atributech. 

## <a name="scoping-filter-construction"></a>Vytváření filtru oborů

Filtr oboru se skládá z jedné nebo více *klauzulí*. Klauzule určují, kteří uživatelé mají povolený průchod filtrem oboru vyhodnocením atributů každého uživatele. Například můžete mít jednu klauzuli, která vyžaduje, aby se atribut "State" uživatele rovnal "New York", takže se do aplikace zřídí pouze noví uživatelé Praha. 

Jedna klauzule definuje jednu podmínku pro jednu hodnotu atributu. Pokud je v jednom oboru filtru vytvořeno více klauzulí, jsou vyhodnocovány společně pomocí logiky "a". To znamená, že všechny klauzule se musí vyhodnotit na "true", aby bylo možné uživatele zřídit.

Nakonec lze pro jednu aplikaci vytvořit více filtrů oborů. Pokud je přítomno více filtrů oborů, jsou vyhodnocovány společně pomocí logiky "nebo". To znamená, že pokud jsou všechny klauzule v některém z nakonfigurovaných filtrů oborů vyhodnoceny na hodnotu "true", bude uživatel zřízen.

Každý uživatel nebo skupina, které zpracovala služba zřizování služby Azure AD, se vždycky vyhodnocují jednotlivě na základě každého filtru oborů.

Zvažte například následující filtr oboru:

![Filtr oboru](./media/define-conditional-rules-for-provisioning-user-accounts/scoping-filter.PNG) 

Podle tohoto filtru oboru musí uživatelé splnit následující kritéria, která se mají zřídit:

* Musí být v New Yorku.
* Musí fungovat v technickém oddělení.
* ID zaměstnance společnosti musí být mezi 1 000 000 a 2 000 000.
* Jejich název úlohy nesmí být null ani prázdný.

## <a name="create-scoping-filters"></a>Vytvoření filtrů oborů
Filtry oborů se konfigurují jako součást mapování atributů pro jednotlivé konektory zřizování uživatelů Azure AD. Následující postup předpokládá, že jste již nastavili Automatické zřizování pro [jednu z podporovaných aplikací](../saas-apps/tutorial-list.md) a přidáte do ní filtr oborů.

### <a name="create-a-scoping-filter"></a>Vytvoření filtru oboru
1. V [Azure Portal](https://portal.azure.com)přejdete do části **Azure Active Directory**  >  **podnikové aplikace**  >  **všechny aplikace** .

2. Vyberte aplikaci, pro kterou jste nakonfigurovali Automatické zřizování: například "ServiceNow".

3. Vyberte kartu **Zřizování**.

4. V části **mapování** vyberte mapování, pro které chcete nakonfigurovat filtr oboru: například "synchronizovat Azure Active Directory uživatelů do ServiceNow".

5. Vyberte nabídku **obor zdrojového objektu** .

6. Vyberte **Přidat filtr oborů**.

7. Definujte klauzuli tak, že vyberete **název zdrojového atributu**, **operátor** a **hodnotu atributu** pro porovnání. Podporovány jsou následující operátory:

   a. **Je rovno**. Klauzule vrátí "true", pokud vyhodnocený atribut odpovídá hodnotě vstupního řetězce (rozlišuje velká a malá písmena).

   b. **není rovno**. Klauzule vrátí "true", pokud se vyhodnocený atribut neshoduje s hodnotou vstupního řetězce (rozlišuje velká a malá písmena).

   c. **má hodnotu true**. Klauzule vrátí "true", pokud vyhodnocený atribut obsahuje logickou hodnotu true.

   d. **je false**. Klauzule vrátí "true", pokud vyhodnocený atribut obsahuje logickou hodnotu false.

   e. **má hodnotu null**. Klauzule vrátí "true", pokud je vyhodnocený atribut prázdný.

   f. není **null**. Klauzule vrátí hodnotu true, pokud vyhodnocený atribut není prázdný.

   například **porovnávání regulárních** znaků. Klauzule vrátí "true", pokud vyhodnocený atribut odpovídá vzoru regulárního výrazu. Například: ([1-9] [0-9]) odpovídá libovolnému číslu mezi 10 a 99.

   h. **NEshoda s regulárním výrazem** Klauzule vrátí "true", pokud se vyhodnocený atribut neshoduje se vzorem regulárního výrazu.
   
   i. **Greater_Than.** Klauzule vrátí "true", pokud je vyhodnocený atribut větší než hodnota. Hodnota zadaná ve filtru oboru musí být celé číslo a atribut na tomto uživateli musí být celé číslo [0, 1, 2,...]. 
   
   j. **Greater_Than_OR_EQUALS.** Klauzule vrátí "true", pokud je vyhodnocený atribut větší nebo roven hodnotě. Hodnota zadaná ve filtru oboru musí být celé číslo a atribut na tomto uživateli musí být celé číslo [0, 1, 2,...]. 
   
   k. **Zahrnující.** Klauzule vrátí "true", pokud vyhodnocený atribut obsahuje hodnotu řetězce (rozlišuje velká a malá písmena), jak je popsáno [zde](/dotnet/api/system.string.contains). 


>[!IMPORTANT] 
> - Filtr $ MemberOf není aktuálně podporován.
> - EQUALs a NOT EQUALs nejsou podporovány pro vícehodnotové atributy.

9. Volitelně můžete opakováním kroků 7-8 přidat další klauzule rozsahu.

10. V části **název filtru oboru** přidejte název filtru oborů.

11. Vyberte **OK**.

12. Na obrazovce **filtry oboru** vyberte znovu **OK** . Volitelně můžete opakováním kroků 6-11 přidat další filtr oborů.

13. Na obrazovce **mapování atributů** vyberte **Uložit** . 

>[!IMPORTANT] 
> Uložení nového filtru oboru aktivuje novou úplnou synchronizaci pro aplikaci, kde všichni uživatelé ve zdrojovém systému jsou znovu vyhodnocováni proti novému filtru oborů. Pokud byl uživatel v aplikaci dříve v oboru pro zřizování, ale nespadají do oboru, je jeho účet v aplikaci zakázán nebo zrušen. Pokud chcete toto výchozí chování přepsat, přečtěte si téma [přeskočení odstranění u uživatelských účtů, které se nacházejí mimo rozsah](../app-provisioning/skip-out-of-scope-deletions.md).


## <a name="common-scoping-filters"></a>Běžné filtry oboru
| Cílový atribut| Operátor | Hodnota | Popis|
|----|----|----|----|
|userPrincipalName (Hlavní název uživatele)|POROVNÁVÁNÍ REGULÁRNÍCH HODNOT|.\*@domain.com |Všichni uživatelé s userPrincipal, kteří mají doménu, @domain.com budou v oboru pro zřizování.|
|userPrincipalName (Hlavní název uživatele)|NESHODA S REGULÁRNÍM VÝRAZEM|.\*@domain.com|Všichni uživatelé s userPrincipal, kteří mají doménu, @domain.com budou mimo rozsah zřizování.|
|Oddělení|ROVNÁ|SalesTable|Všichni uživatelé z prodejního oddělení jsou v oboru pro zřizování.|
|workerID|POROVNÁVÁNÍ REGULÁRNÍCH HODNOT|(1 [0-9] [0-9] [0-9] [0-9] [0-9] [0-9])| Všichni zaměstnanci s workerIDs mezi 1000000 a 2000000 jsou v oboru pro zřizování.|

## <a name="related-articles"></a>Související články
* [Automatizace zřizování a rušení uživatelů pro aplikace SaaS](../app-provisioning/user-provisioning.md)
* [Přizpůsobení mapování atributů pro zřizování uživatelů](../app-provisioning/customize-application-attributes.md)
* [Zápis výrazů pro mapování atributů](functions-for-customizing-application-data.md)
* [Oznámení zřizování účtů](../app-provisioning/user-provisioning.md)
* [Pomocí SCIM můžete povolit Automatické zřizování uživatelů a skupin od Azure Active Directory k aplikacím.](../app-provisioning/use-scim-to-provision-users-and-groups.md)
* [Seznam kurzů, jak integrovat aplikace SaaS](../saas-apps/tutorial-list.md)

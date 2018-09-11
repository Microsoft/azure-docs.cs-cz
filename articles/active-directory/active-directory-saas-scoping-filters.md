---
title: Zřizování aplikací s filtry oborů | Dokumentace Microsoftu
description: Další informace o použití filtrů oborů objekty v aplikacích, které podporují zřizování automatizované uživatelů z zřizuje, pokud objekt nevyhovují vašim podnikovým požadavkům.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/09/2018
ms.author: barbkess
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1da742f54a9a415c6078a6185a9605db700dffd5
ms.sourcegitcommit: 465ae78cc22eeafb5dfafe4da4b8b2138daf5082
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2018
ms.locfileid: "44324950"
---
# <a name="attribute-based-application-provisioning-with-scoping-filters"></a>Zřizování aplikací na základě atributů s filtry oborů
Cílem tohoto článku je vysvětlují, jak používat filtry oborů k definování pravidel založených na atributech, které určují, které uživatelé jsou přiřazeni k aplikaci.

## <a name="scoping-filter-use-cases"></a>Případy použití filtrů oborů

Filtr oborů umožňuje Azure Active Directory (Azure AD) služby zřizování pro zahrnutí nebo vyloučení všechny uživatele, kteří mají atribut, který se shoduje s konkrétní hodnotou. Například při zřizování uživatelů z Azure AD k aplikaci SaaS, používá prodejní tým, určíte, že jenom uživatelé s atributem "Oddělení", "Prodeje" by měla být v oboru pro zřizování.

Filtry oborů lze různě v závislosti na typu zřizování konektoru:

* **Odchozí zřizování ze služby Azure AD k aplikacím SaaS**. Pokud Azure AD je systém zdroj [přiřazení uživatelů a skupin](manage-apps/assign-user-or-group-access-portal.md) jsou nejčastější metodou pro určení, kteří uživatelé jsou v oboru pro zřizování. Tato přiřazení také se používají pro povolení jednotného přihlašování a poskytnout jedinou metodu ke správě přístupu a zřizování. Filtry oborů je možné volitelně kromě přiřazení nebo místo nich k filtrování uživatelů podle hodnot atributů.

    >[!TIP]
    > Můžete zakázat zřizování na základě přiřazení pro podniková aplikace změnou nastavení [oboru](active-directory-saas-app-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) nabídky v části Nastavení zřizování, které se **synchronizovat všechny uživatele a skupiny**. Pomocí této možnosti plus založených na atributech filtry oborů nabízí vyšší výkon než pomocí přiřazení na základě skupin.  

* **Příchozí zřizování z HCM aplikací do služby Azure AD a služby Active Directory**. Když [HCM aplikaci, jako je například Workday](saas-apps/workday-tutorial.md) je zdrojový systém filtry oborů jsou primární metodou pro určení, kteří by mělo proběhnout zřízení ze systémů HCM aplikaci do služby Active Directory nebo Azure AD.

Ve výchozím nastavení konektory pro zřizování Azure AD není nutné žádné založených na atributech filtry oborů nakonfigurované. 

## <a name="scoping-filter-construction"></a>Vytváření oborů filtru

Filtr oborů se skládá z jednoho nebo více *klauzule*. Klauzule určit, kteří uživatelé jsou povoleno předávání filtr oborů vyhodnocením atributy každého uživatele. Například můžete mít jednu klauzuli, která vyžaduje, že atribut uživatele "Stavu" rovno "New York", tak jenom uživatelé v New Yorku, které jsou zřízené do aplikace. 

Jedna klauzule definuje jednu podmínku pro jeden atribut hodnotu. Pokud více klauzulí jsou vytvořené v jednom oboru filtru, se vyhodnocují dohromady pomocí logiky "A". To znamená, že všechny klauzule se musí vyhodnotit na "true" v pořadí pro uživatele, které se mají zřídit.

Nakonec více filtrů oborů lze vytvořit pro jednu aplikaci. Pokud je zadáno více filtrů oborů jsou k dispozici, se vyhodnocují dohromady pomocí logiky "Nebo". To znamená, že pokud mají všechny klauzule v některém z nakonfigurovaných filtry oborů "true", uživatel je zřízený.

Každý uživatel nebo skupina zpracovává zřizovací služba Azure AD se vždy vyhodnocují zvlášť pro každý filtr oborů.

Jako příklad vezměte v úvahu následující filtr oborů:

![Filtr oborů](./media/active-directory-saas-scoping-filters/scoping-filter.PNG) 

Podle tohoto oboru filtru uživatelé musí splňovat následující kritéria, které se mají zřídit:

* Musí být v New Yorku.
* Musí pracovat v oddělení inženýrství.
* ID zaměstnance společnosti musí být mezi 1 000 000 a 2 000 000.
* Jejich pracovní pozice nesmí být null ani prázdný.

## <a name="create-scoping-filters"></a>Vytvoření filtrů oborů
Filtry oborů se nakonfigurují jako součást mapování atributů pro každého uživatele Azure AD zřizování konektoru. Následující postup předpokládá, že jste již nastavili automatické zřizování pro [jednu z podporovaných aplikací](saas-apps/tutorial-list.md) a do ní přidat filtr oborů.

### <a name="create-a-scoping-filter"></a>Vytvořit filtr oborů
1. V [webu Azure portal](https://portal.azure.com), přejděte **Azure Active Directory** > **podnikové aplikace** > **všechnyaplikace** oddílu.

2. Vyberte aplikaci, pro který jste nakonfigurovali automatické zřizování: například "ServiceNow".

3. Vyberte **zřizování** kartu.

4. V **mapování** vyberte, kterou chcete konfigurovat filtr oborů pro mapování: například "synchronizace Azure Active Directory uživatelům k ServiceNow".

5. Vyberte **zdrojového objektu oboru** nabídky.

6. Vyberte **přidat filtr oborů**.

7. Definování klauzule tak, že vyberete zdroj **název atributu**, **operátor**a **hodnota atributu** k porovnání. Jsou podporovány následující operátory:

   a. **SE ROVNÁ**. Klauzule vrací hodnotu "true" Pokud vyhodnocený atribut přesně odpovídá vstupní řetězcovou hodnotu (rozlišuje velikost písmen).

   b. **NENÍ ROVNO**. Klauzule vrací hodnotu "true", pokud atribut Vyhodnocená neodpovídá vstupní řetězcovou hodnotu (rozlišuje velikost písmen).

   c. **MÁ HODNOTU TRUE**. Klauzule vrací hodnotu "true", pokud vyhodnocený atribut neobsahuje logickou hodnotu true.

   d. **MÁ HODNOTU FALSE**. Klauzule vrací hodnotu "true", pokud vyhodnocený atribut neobsahuje logickou hodnotu false.

   e. **MÁ HODNOTU NULL**. Klauzule vrací hodnotu "true", pokud vyhodnocený atributu je prázdný.

   f. **NENÍ ROVNO HODNOTĚ NULL**. Klauzule vrací hodnotu "true", pokud vyhodnocený atribut není prázdný.

   g. **REGULÁRNÍHO VÝRAZU SHODY**. Klauzule vrací hodnotu "true", pokud atribut Vyhodnocená odpovídá vzoru regulárního výrazu. Například: ([1-9][0-9]) odpovídá jakémukoliv číslu mezi 10 a 99.

   h. **NEODPOVÍDÁ REGULÁRNÍMU**. Klauzule vrací hodnotu "true", pokud atribut Vyhodnocená neodpovídá vzoru regulárního výrazu.

8. Vyberte **novou klauzuli oboru přidat**.

9. Opakováním kroků 7. a 8 přidat více klauzulí oborů.

10. V **název filtru oborů**, přidejte název oboru filtru.

11. Vyberte **OK**.

12. Vyberte **OK** znovu na **filtry oborů** obrazovky. Volitelně opakujte kroky 6 – 11 přidat jiný filtr oborů.

13. Vyberte **Uložit** na **mapování atributů** obrazovky. 

>[!IMPORTANT] 
> Ukládání nových oborů filtru triggerů nové úplnou synchronizaci pro aplikaci, ve kterém všichni uživatelé ve zdrojovém systému vyhodnocují znovu nový filtr oborů. Pokud uživatel v aplikaci byl dříve v oboru pro zřizování, ale spadá mimo rozsah, jejich účet je zakázán nebo zrušení zřízení v aplikaci.


## <a name="related-articles"></a>Související články
* [Rejstřík článků o správě aplikací ve službě Azure Active Directory](active-directory-apps-index.md)
* [Automatizace zřizování a jeho rušení pro aplikace SaaS](active-directory-saas-app-provisioning.md)
* [Přizpůsobení mapování atributů pro zřizování uživatelů](active-directory-saas-customizing-attribute-mappings.md)
* [Zápis výrazů pro mapování atributů](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [Oznámení o zřizování účtů](active-directory-saas-app-provisioning.md)
* [Povolit automatické zřizování uživatelů a skupin ze služby Azure Active Directory do aplikací pomocí SCIM](manage-apps/use-scim-to-provision-users-and-groups.md)
* [Seznam kurzů o integraci aplikací SaaS](saas-apps/tutorial-list.md)


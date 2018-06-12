---
title: Zřizování aplikací s filtry oborů | Microsoft Docs
description: Další informace o použití oboru filtrů objekty v aplikacích, které podporují zřizování automatizované uživatelů z se zřídí, pokud objekt nemá splňují vaše podnikové požadavky.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: bcfbda74-e4d4-4859-83bc-06b104df3918
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2017
ms.author: markvi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d6f4f257d380d6521774afd23dbeaf6a94711c6d
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35293071"
---
# <a name="attribute-based-application-provisioning-with-scoping-filters"></a>Zřizování aplikace na základě atributů s filtry oborů
Cílem tohoto článku je vysvětlují, jak definovat pravidla na základě atributů, které určují, kteří uživatelé jsou zřízené do aplikace pomocí oboru filtrů.

## <a name="scoping-filter-use-cases"></a>Obor případy použití filtru

Oboru filtr umožňuje Azure Active Directory (Azure AD) zřizování služby zahrnout nebo vyloučit všechny uživatele, kteří mají atribut, který odpovídá konkrétní hodnotu. Například při zřizování uživatelů z Azure AD k aplikaci SaaS používané prodejního týmu, je můžete zadat, pouze uživatelé s atributem "Oddělení" "Prodeje" by měla být v oboru pro zřizování.

Filtry oborů lze použít odlišně v závislosti na typu zřizování konektor:

* **Odchozí zřizování z Azure AD pro aplikace SaaS**. Pokud Azure AD je zdrojovém systému [přiřazení uživatelů a skupin](manage-apps/assign-user-or-group-access-portal.md) jsou nejběžnější metodou pro určení uživatelů, kteří jsou v oboru pro zřizování. Tato přiřazení také se používají pro povolení jednotného přihlašování a zadat jednu metodu pro správu přístupu a zřizování. Filtry oborů lze volitelně můžete kromě přiřazení nebo místo nich, k filtrování uživatelů podle hodnot atributů.

    >[!TIP]
    > Můžete zakázat zřizování na základě přiřazení pro podniková aplikace změnou nastavení v [oboru](active-directory-saas-app-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) nabídky v části Možnosti zřizování **synchronizaci všech uživatelů a skupin**. Pomocí této možnosti plus založená na atributu oboru filtrů nabízí vyšší výkon než pomocí přiřazení na základě skupiny.  

* **Příchozí zřizování z HCM aplikací do Azure AD a služby Active Directory**. Když [HCM aplikaci, například Workday](active-directory-saas-workday-tutorial.md) je systém zdrojového oboru filtry jsou primární metodou pro určení uživatelů, kteří by měl být zřízen z HCM aplikaci do služby Active Directory nebo Azure AD.

Ve výchozím nastavení Azure AD zřizování konektory nemají žádné založená na atributu oboru filtry nakonfigurované. 

## <a name="scoping-filter-construction"></a>Vytváření oboru filtru

Oboru filtru se skládá z jednoho nebo více *klauzule*. Klauzule určit, kteří uživatelé mohou předávat oboru filtru vyhodnocením atributy každého uživatele. Například můžete mít jednu klauzuli, která vyžaduje, aby atribut uživatele "Stavu" rovná "Praha", proto jsou pouze uživatelé v New Yorku zřízené do aplikace. 

Jedna klauzule definuje jednu podmínku pro hodnotu jeden atribut. Pokud více klauzulí jsou vytvořené v jednom oboru filtru, že se vyhodnotí společně pomocí logiky "A". To znamená, že všechny klauzule musí vyhodnotit na hodnotu "true" v pořadí pro uživatele, které se má zřídit.

Nakonec můžete vytvořit více oboru filtrů pro jednu aplikaci. Pokud jsou v něm více oboru filtrů, že jste vyhodnocovány společně pomocí logiky "Nebo". To znamená, že pokud všechny klauzule v žádném nakonfigurované oboru filtrů vyhodnotit na hodnotu "PRAVDA", uživatel je zřízený.

Jednotlivé uživatele nebo skupiny, zpracovaných zřizování službou Azure AD je vždy vyhodnocena jednotlivě u každého oboru filtru.

Jako příklad zvažte následující oboru filtru:

![Filtr vytváření oboru](./media/active-directory-saas-scoping-filters/scoping-filter.PNG) 

Podle tohoto oboru filtru uživatelé musí splňovat následující kritéria zřídit:

* Musí být v New Yorku.
* Musí pracovat v inženýrství oddělení.
* ID zaměstnance společnosti musí být mezi 1 000 000 a 2 000 000.
* Jejich název úlohy nesmí být null nebo prázdný.

## <a name="create-scoping-filters"></a>Vytvoření oboru filtrů
Oboru filtry jsou nakonfigurovány jako součást mapování atributů pro každého uživatele Azure AD zřizování konektor. Následující postup předpokládá, že jste již nastavili automatické zřizování pro [jednu z podporovaných aplikací](active-directory-saas-tutorial-list.md) a jsou k němu přidáním oboru filtru.

### <a name="create-a-scoping-filter"></a>Vytvoření oboru filtru
1. V [portál Azure](https://portal.azure.com), přejděte na **Azure Active Directory** > **podnikové aplikace, které** > **všechny aplikace** části.

2. Vyberte aplikaci, pro který jste nakonfigurovali automatické zřizování: například "ServiceNow".

3. Vyberte **zřizování** kartě.

4. V **mapování** vyberte, kterou chcete konfigurovat oboru filtr pro mapování: například "synchronizace Azure Active Directory uživatelů k ServiceNow".

5. Vyberte **zdroje obor objektu** nabídky.

6. Vyberte **přidat oboru filtru**.

7. Definování klauzuli výběrem zdroj **název atributu**, **operátor**a **hodnota atributu** k porovnání. Podporovány jsou následující operátory:

   a. **SE ROVNÁ**. Klauzule vrací "true" Pokud atribut vyhodnotí přesně odpovídá vstupní řetězcovou hodnotu (malá a velká písmena).

   b. **NENÍ ROVNO**. Klauzule vrací "PRAVDA", pokud atribut vyhodnotí neodpovídá vstupní řetězcovou hodnotu (malá a velká písmena).

   c. **PLATÍ**. Klauzule vrací "PRAVDA", pokud atribut vyhodnotí obsahuje logickou hodnotu true.

   d. **NEPRAVDA**. Klauzule vrací "PRAVDA", pokud atribut vyhodnotí obsahuje logickou hodnotu false.

   e. **MÁ HODNOTU NULL**. Klauzule vrací "PRAVDA", pokud vyhodnotí atributu je prázdný.

   f. **NENÍ NULL**. Klauzule vrací "PRAVDA", pokud atribut vyhodnotí není prázdný.

   g. **SHODA REGEX**. Klauzule vrací "PRAVDA", pokud atribut vyhodnotí odpovídá regulárnímu výrazu. Například: ([1-9][0-9]) odpovídá jakémukoliv číslu 10 až 99.

   h. **NEODPOVÍDÁ REGEX**. Klauzule vrací "PRAVDA", pokud atribut vyhodnotí neodpovídá regulárnímu výrazu.

8. Vyberte **přidat nového oboru klauzule**.

9. Opakováním kroků 7-8, chcete-li přidat další oboru klauzule.

10. V **rozsahu název filtru**, přidejte název vašeho oboru filtru.

11. Vyberte **OK**.

12. Vyberte **OK** znovu na **filtry rozsahu** obrazovky. Opakováním kroků 6-11 přidání jiného oboru filtru.

13. Vyberte **Uložit** na **mapování atributů** obrazovky. 

>[!IMPORTANT] 
> Ukládání novou aktivační události oboru filtru nové úplnou synchronizaci pro aplikaci, kde jsou všechny uživatele ve zdrojovém systému porovnán znovu s novou oboru filtru. Pokud uživatel v aplikaci byl dříve v oboru pro zřizování, ale spadá mimo rozsah, svůj účet je zakázán nebo zrušit v aplikaci.


## <a name="related-articles"></a>Související články
* [Rejstřík článků o správě aplikací ve službě Azure Active Directory](active-directory-apps-index.md)
* [Automatizovat uživatele zajišťování a rušení zajištění pro aplikace SaaS](active-directory-saas-app-provisioning.md)
* [Přizpůsobení mapování atributů pro zřizování uživatelů](active-directory-saas-customizing-attribute-mappings.md)
* [Zápis výrazů pro mapování atributů](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [Účet zřizování oznámení](active-directory-saas-account-provisioning-notifications.md)
* [Pomocí SCIM zapněte automatické zřizování uživatelů a skupin ze služby Azure Active Directory k aplikacím](manage-apps/use-scim-to-provision-users-and-groups.md)
* [Seznam kurzů k integraci aplikací SaaS](active-directory-saas-tutorial-list.md)


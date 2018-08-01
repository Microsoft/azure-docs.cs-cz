---
title: Správa přístupu k aplikacím pomocí služby Azure AD | Dokumentace Microsoftu
description: Popisuje, jak Azure Active Directory umožňuje organizacím zadejte aplikace, ke kterým má každý uživatel přístup.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2017
ms.author: barbkess
ms.openlocfilehash: d39df136c6c81e380ccbe7fa2c050d906dc4c182
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2018
ms.locfileid: "39367041"
---
# <a name="managing-access-to-apps"></a>Správa přístupu k aplikacím
Probíhající access managementu, využití hodnocení a vytváření sestav i nadále být náročné z těchto po integraci aplikace do systému identity vaší organizace. V mnoha případech třeba provádět průběžnou aktivní roli při správě přístupu k vašim aplikacím správci IT nebo helpdesk. Přiřazení v některých případech se provádí pomocí obecné nebo oddělení IT tým. Často se má rozhodnutí přiřazení delegovat na obchodní rozhodovací pravomocí vyžadující schválení před IT díky přiřazení.  Další organizace investovat do integrace s existující automatizované identit a přístupu systémem správy, jako je řízení přístupu na základě Role (RBAC) nebo řízení přístupu na základě atributů (ABAC). Integrace a vývoje pravidla jsou obvykle specializovaných a drahých. Monitorování a vytváření sestav o buď způsob správy je svůj vlastní samostatný, nákladné a komplexní investice.

## <a name="how-does-azure-active-directory-help"></a>Jak pomáhá Azure Active Directory?
 Azure AD podporuje správu přístupu k rozsáhlé nakonfigurovaných aplikací umožňujících organizacím snadno dosáhnout zásady správná přístupová práva od automatické, založených na atributech přiřazení (scénáře ABAC neboli RBAC) prostřednictvím delegování a včetně Správa správců. S Azure AD můžete snadno dosáhnout komplexní zásady, kombinování více správy modelů pro jednu aplikaci a znovu správu pravidla i použít napříč aplikacemi s využitím stejné cílové skupiny.

* [Přidat nové nebo existující aplikace](configure-single-sign-on-portal.md)

 Přiřazení aplikací Azure AD se zaměřuje na dva režimy primární přiřazení:

* **Jednotlivé přiřazení** správce IT s oprávněními globálního správce adresáře můžete vybrat jednotlivé uživatelské účty a udělovat oprávnění pro přístup k aplikaci.
* **Přiřazení na základě skupin (placené pouze Azure AD)** správce IT s oprávněními globálního správce adresáře můžete přiřadit skupiny k aplikaci. Přístup k určitým uživatelům je určeno, jestli jsou členové skupiny při pokusu o přístup k aplikaci. Jinými slovy správce může efektivně vytvořit pravidlo přiřazení s oznámením "všechny aktuální člen přiřazená skupina má přístup k aplikaci". Použití této možnosti přiřazení správců mohou těžit z některé z možností správy skupiny Azure AD, včetně [dynamické skupiny podle atributů](../fundamentals/active-directory-groups-create-azure-portal.md), externí systémové skupiny (například v místním Active Directory nebo pracovní den) nebo skupiny řízená správcem nebo spravovanými na samoobslužných služeb. Jednu skupinu lze snadno přiřadit k více aplikacemi, a ujistěte se, že můžete sdílet aplikace s přiřazení spřažení pravidla přiřazení zjednodušit celkové správy. Všimněte si, že vnořené členství ve skupinách na základě skupin přiřazování k aplikacím v tuto chvíli nepodporuje.

Pomocí těchto režimech dvě přiřazení, Správce může dosáhnout jakékoli způsob správy žádoucí přiřazení.

S Azure AD používání a vytváření sestav přiřazení je plně integrovaná, umožňuje správcům snadno sestav o stavu přiřazení, chyby přiřazení a dokonce i využití.

## <a name="complex-application-assignment-with-azure-ad"></a>Přiřazení komplexních aplikací s Azure AD
Vezměte v úvahu aplikace, jako je Salesforce. V mnoha organizacích Salesforce primárně využívá marketingové a prodejní organizace. Často členové týmu, marketingu vysoce privilegovaný přístup k Salesforce, zatímco členové prodejního týmu mít omezený přístup. V mnoha případech široké naplnění informačních pracovníků vynutit omezil přístup k aplikaci. Výjimky pro tato pravidla zpracovávaný. Často je vyhrazeny vedení týmů marketingové a prodejní udělit přístup uživatelům nebo změnit jejich role nezávisle na tato obecná pravidla.

S Azure AD může být předem konfigurované pro jednotné přihlašování (SSO) a automatického zřizování aplikací, jako je Salesforce. Jakmile je aplikace nakonfigurována, Správce může převzít jednorázová akce vytvoření a přidání příslušných skupin. V tomto příkladu může správce provést následující přiřazení:

* [Dynamické skupiny](../fundamentals/active-directory-groups-create-azure-portal.md) můžete definovat tak, aby automaticky představují všechny členy marketingové a prodejní týmy, které jsou použity atributy jako jsou oddělení nebo role:
  
  * Všichni členové skupiny marketing by byli přiřazeni k roli "marketing" v Salesforce.
  * Všichni členové prodejní tým, které skupiny by byli přiřazeni k roli "prodeje" v Salesforce. Další vylepšení použít víc skupin, které představují regionálních prodejních týmů na přiřazené pro různé role Salesforce.
* Pokud chcete povolit mechanismu výjimek, nemohl být vytvořen samoobslužné skupiny pro každou roli. Můžete například vytvořit skupinu, "Salesforce marketingové výjimka" jako samoobslužné skupiny. Je možné přiřadit skupiny k roli marketingové Salesforce a marketingový tým vedení možné vlastníky. Členové marketingový tým vedení může přidat nebo odebrat uživatele, nastavit zásady, spojení, nebo dokonce Schvalte nebo zamítněte jednotlivým uživatelům žádosti o připojení. Tento mechanismus je podporované prostřednictvím informace pracovního procesu vhodné prostředí, které nevyžaduje specializované školení pro vlastníky nebo členy.

V takovém případě všechny přiřazené uživatele by být automaticky přiřazeni k Salesforce, jako jsou přidány do jiné skupiny, které se aktualizují přiřazení role v Salesforce. Uživatelé by mohli vyhledat a získat přístup k Salesforce přes přístupový panel aplikací Microsoftu, klienti Office web, nebo dokonce i v případě, že přejdete na jejich organizace přihlašovací stránka Salesforce. Správci by mohli snadno zobrazit využití a přiřazení stavu pomocí vytváření sestav Azure AD.

Správci můžou využívat [podmíněný přístup Azure AD](../active-directory-conditional-access-azure-portal.md) nastavovat zásady pro konkrétní role. Tyto zásady můžete zahrnout, zda je povolen přístup mimo podniková prostředí a získat přístup v různých případech i ověřování službou Multi-Factor Authentication nebo zařízení požadavky.

## <a name="next-steps"></a>Další postup

* [Rejstřík článků o správě aplikací ve službě Azure Active Directory](../active-directory-apps-index.md)
* [Ochrana aplikací s podmíněným přístupem](../active-directory-conditional-access-azure-portal.md)
* [Správa/SSAA samoobslužné skupiny](../users-groups-roles/groups-self-service-management.md)

---
title: Správa přístupu k aplikacím pomocí služby Azure AD | Microsoft Docs
description: Popisuje, jak Azure Active Directory umožňuje organizacím umožňuje určit aplikace, ke kterým má každý uživatel přístup.
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
ms.topic: article
ms.date: 05/16/2017
ms.author: barbkess
ms.openlocfilehash: 019bdecd315f6da91fef8e7ed5d088e328253ef6
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35303356"
---
# <a name="managing-access-to-apps"></a>Správa přístupu k aplikacím
Probíhající přístup správu, využití hodnocení a vytváření sestav nadále výzvu po integraci aplikace do systému identity vaší organizace. V řadě případů muset provést probíhající aktivní roli při správě přístupu k aplikacím správci IT nebo technickou podporu. V některých případech přiřazení provádí obecné nebo oddělení IT tým. Často se má rozhodnutí o přiřazení delegovat na pracovník s rozhodovací pravomocí, které vyžadují jejich schválení před IT díky přiřazení.  Jiných organizací investovat do integraci se stávajícím automatizované přístupu a identit a správy systému, jako je řízení přístupu na základě Role (RBAC) nebo na základě atributů řízení přístupu (ABAC). Integrace i vývoj pravidla jsou obvykle specializované a nákladné. Monitorování nebo generování sestav na buď způsob správy je vlastní samostatný, nákladná a komplexní investice.

## <a name="how-does-azure-active-directory-help"></a>Jak funguje Azure Active Directory?
 Azure AD podporuje správu rozsáhlé přístup nakonfigurovaných aplikací, což umožňuje organizacím snadno dosáhnout správná přístupová práva zásady od automatická, na základě atributů přiřazení (ABAC nebo RBAC scénáře) prostřednictvím delegování a včetně Správa správců. S Azure AD můžete snadno dosáhnout komplexní zásady kombinování více správu modelů pro jednu aplikaci a i znovu pravidla můžete použít správu napříč aplikacemi pomocí stejné cílové skupiny.

* [Přidat nové nebo existující aplikace](configure-single-sign-on-portal.md)

 Přiřazení aplikace Azure AD se zaměřuje na dva režimy primární přiřazení:

* **Jednotlivé přiřazení** správce IT s oprávněními globálního správce adresáře můžete vybrat jednotlivé uživatelské účty a jim udělit přístup k aplikaci.
* **Přiřazení na základě skupiny (placené pouze Azure AD)** správce IT s oprávněními globálního správce adresáře můžete přiřadit skupinu do aplikace. Zda jsou členové skupiny při pokusu o přístup k aplikaci je určena přístupu konkrétním uživatelům. Jinými slovy správce můžete efektivně vytvořit pravidlo přiřazení s oznámením "žádné aktuálního člena skupiny přiřazené má přístup k aplikaci". Použití této možnosti přiřazení správci mohou těžit z výhod všechny možnosti správy skupin Azure AD, včetně [na základě atributů dynamických skupin](../active-directory-groups-create-azure-portal.md), externí systém skupiny (například místní služby Active Directory nebo Workday) nebo skupiny řízená správcem nebo řízená na samoobslužných služeb. Jednu skupinu lze snadno přiřadit k více aplikací, zajištění, že aplikace s přiřazení spřažení sdílet pravidla přiřazení snižuje složitost celkové správy. Upozorňujeme, že vnořené skupiny členství ve skupinách nejsou podporovány pro přiřazení na základě skupiny k aplikacím v tuto chvíli.

Pomocí těchto režimech dvě přiřazení, správci můžete dosáhnout žádné způsob správy žádoucí přiřazení.

S Azure AD použití a vytváření sestav přiřazení jsou plně integrované, že správcům umožňuje snadno sestav o stavu přiřazení, přiřazení chyby a i využití.

## <a name="complex-application-assignment-with-azure-ad"></a>Přiřazení složitých aplikací s Azure AD
Zvažte aplikaci, například služby Salesforce. V mnoha organizacích Salesforce slouží především marketing a prodejní organizace. Často členové týmu pro marketing vysoce privilegovaný přístup do služby Salesforce, zatímco členové prodejního týmu mít omezený přístup. V mnoha případech mají široký naplnění informačních pracovníků vynutit omezený přístup k aplikaci. Výjimky pro tato pravidla zkomplikovat záleží. Často je vyhrazeny vedení týmy marketing nebo prodejní udělit přístup uživatelům nebo změnit jejich rolí nezávisle na tato obecná pravidla.

S Azure AD může být aplikace, jako je Salesforce předem nakonfigurovaná pro jednotné přihlašování (SSO) a automatické zřizování. Jakmile je aplikace konfigurována, může trvat správce jednorázová akce můžete vytvořit a přiřadit příslušných skupin. V tomto příkladu může správce provést následující přiřazení:

* [Dynamické skupiny](../active-directory-groups-create-azure-portal.md) lze definovat za účelem automaticky představují všichni členové pomocí atributů, například role nebo oddělení prodeje a marketingu týmy:
  
  * Všichni členové skupiny marketing by být přiřazeny k roli "marketing" v Salesforce
  * Všichni členové prodejního týmu, který by být přiřazeny skupiny k roli "prodeje" v Salesforce. Další upřesnění může používat více skupin, které představují regionální prodejní týmy, které jsou přiřazené pro různé role služby Salesforce.
* Pokud chcete povolit mechanismus výjimek, by bylo možné vytvořit skupinu samoobslužné služby pro každou roli. Například "Salesforce marketing výjimky" skupiny lze vytvořit jako skupina samoobslužné služby. Skupinu lze přiřadit k roli marketing Salesforce a marketing vedení, můžete provést za vlastníky. Členové vedení marketing může přidat nebo odebrat uživatele, nastavit zásady spojení, nebo i schválit nebo zamítnout jednotlivé uživatele žádosti o připojení. Tato možnost je podporována prostřednictvím prostředí příslušné informace pracovního procesu, které nevyžaduje specializované školení pro vlastníky nebo členy.

V takovém případě všechny přiřazené uživatele by automaticky zřídí do služby Salesforce, když jsou přidávány do různých skupin, které by v Salesforce aktualizovat jejich přiřazení role. Uživatelé budou moct zjistit a přístup k Salesforce prostřednictvím přístupového panelu aplikací společnosti Microsoft, webovými klienty Office, nebo i tak, že přejdete na jejich organizace přihlašovací stránku služby Salesforce. Správci by mohli snadno zobrazit stav využití a přiřazení pomocí vytvářením sestav Azure AD.

Můžete použít správce [podmíněného přístupu Azure AD](../active-directory-conditional-access-azure-portal.md) nastavit zásady přístupu pro konkrétní role. Tyto zásady můžete zahrnují mimo zajistit přístup v různých případech i ověřování Multi-Factor Authentication nebo zařízení požadavky na podnikovém prostředí a je povolen přístup.

## <a name="next-steps"></a>Další postup

* [Rejstřík článků o správě aplikací ve službě Azure Active Directory](../active-directory-apps-index.md)
* [Ochrana aplikací pomocí podmíněného přístupu](../active-directory-conditional-access-azure-portal.md)
* [Samoobslužné služby skupiny správy nebo SSAA](../active-directory-accessmanagement-self-service-group-management.md)

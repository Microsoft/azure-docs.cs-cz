---
title: Správa přístupu k aplikacím pomocí Azure AD
description: Popisuje, jak Azure Active Directory umožňuje organizacím určit aplikace, ke kterým mají jednotliví uživatelé přístup.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/16/2017
ms.author: kenwith
ms.openlocfilehash: 5afc6aa8f52011eba6d7cfdfaa09b0ab995183e8
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2021
ms.locfileid: "99257419"
---
# <a name="managing-access-to-apps"></a>Správa přístupu k aplikacím

Průběžná správa přístupu, vyhodnocení využití a vytváření sestav se po integraci aplikace do systému identit vaší organizace pořád stane výzvou. V mnoha případech musí správci IT nebo helpdesku vzít v úvahu probíhající aktivní roli při správě přístupu k vašim aplikacím. V některých případech je přiřazení prováděno generálním týmem týmu nebo oddělení IT. Rozhodnutí o přiřazení je často určené k tomu, aby mohl být delegovaný vedoucím podniku pro rozhodování, který vyžaduje jejich schválení předtím, než provede přiřazení.  Jiné organizace investovaly do integrace s existujícím automatizovaným systémem pro správu identit a přístupu, jako je Role-Based Access Control (RBAC) nebo Attribute-Based Access Control (ABAC). Vývoj pro integraci i pravidla je typicky specializovaný a nákladný. Monitorování nebo vytváření sestav z obou přístupů je svou vlastní samostatnou, nákladnou a složitou investici.

## <a name="how-does-azure-active-directory-help"></a>Jak Azure Active Directory nápovědě?

Azure AD podporuje rozsáhlou správu přístupu pro nakonfigurované aplikace, což organizacím umožňuje snadno dosáhnout správných zásad přístupu v rozsahu od automatického přiřazení založeného na atributech (scénáře ABAC nebo RBAC) prostřednictvím delegování a správy správců. Pomocí Azure AD můžete snadno dosáhnout složitých zásad, kombinování více modelů správy pro jednu aplikaci a můžete dokonce znovu použít pravidla správy napříč aplikacemi se stejnými cílovými skupinami.

Pomocí služby Azure AD je vytváření sestav využití a přiřazení plně integrované a umožňuje správcům snadno vytvářet sestavy o stavu přiřazení, chybách přiřazení a dokonce o využití.

### <a name="assigning-users-and-groups-to-an-app"></a>Přiřazení uživatelů a skupin k aplikaci

Přiřazení aplikace Azure AD se zaměřuje na dva primární režimy přiřazení:

* **Individuální přiřazení** Správce IT, který má oprávnění globálního správce adresáře, může vybrat jednotlivé uživatelské účty a udělit jim přístup k aplikaci.

* **Přiřazení na základě skupin (vyžaduje Azure AD Premium P1 nebo P2)** Správce IT, který má oprávnění globálního správce adresáře, může přiřadit skupinu k aplikaci. Přístup ke konkrétním uživatelům se určuje podle toho, jestli jsou členy skupiny v době, kdy se pokusí o přístup k aplikaci. Jinými slovy může správce efektivně vytvořit pravidlo přiřazení s informacemi o tom, že aktuální člen přiřazené skupiny má přístup k aplikaci. Pomocí této možnosti přiřazení můžou správci těžit z jakékoli možnosti správy skupin Azure AD, včetně [dynamických skupin založených na atributech](../fundamentals/active-directory-groups-create-azure-portal.md), skupin externích systémů (například místní služby Active Directory nebo Workday) nebo skupin spravovaných správcem nebo samoobslužné služby. Jednu skupinu je možné snadno přiřadit k více aplikacím. tím se zajistí, že aplikace s spřažením přiřazení můžou sdílet pravidla přiřazení a snížit tak celkovou složitost správy. Všimněte si, že v tuto chvíli není v tomto okamžiku pro přiřazení na základě skupin podporována vnořená členství ve skupině.

Pomocí těchto dvou režimů přiřazení můžou správci dosáhnout jakéhokoli žádoucího přístupu pro správu přiřazení.

### <a name="requiring-user-assignment-for-an-app"></a>Vyžadování přiřazení uživatele pro aplikaci

U určitých typů aplikací máte možnost [vyžadovat, aby byli uživatelé přiřazeni k aplikaci](assign-user-or-group-access-portal.md#configure-an-application-to-require-user-assignment). Tím se zabráníte všem uživatelům, kteří se přihlásí, s výjimkou uživatelů, které explicitně přiřadíte k aplikaci. Tuto možnost podporují následující typy aplikací:

* Aplikace nakonfigurované pro federované jednotné přihlašování (SSO) s ověřováním založeném na SAML
* Aplikace proxy aplikací, které používají předběžné ověření Azure Active Directory
* Aplikace založené na platformě aplikace Azure AD, které používají ověřování OAuth 2,0/OpenID Connect po tom, co uživatel nebo správce souhlasí s touto aplikací. Některé podnikové aplikace poskytují další kontrolu nad tím, kdo se může přihlásit.

Když není přiřazení uživatele *vyžadováno*, nepřiřazeným uživatelům se aplikace v jejich aplikacích nezobrazuje, ale můžou se i nadále přihlašovat k samotné aplikaci (označuje se také jako přihlašování iniciované v rámci SP) nebo můžou použít **adresu URL přístupu uživatele** na stránce **vlastností** aplikace (označuje se také jako přihlášení iniciované IDP).

U některých aplikací není ve vlastnostech aplikace možnost vyžadovat přiřazení uživatele k dispozici. V těchto případech můžete pomocí PowerShellu nastavit vlastnost appRoleAssignmentRequired instančního objektu.

### <a name="determining-the-user-experience-for-accessing-apps"></a>Určení uživatelského prostředí pro přístup k aplikacím

Azure AD poskytuje [několik přizpůsobitelných způsobů, jak nasadit aplikace](end-user-experiences.md) pro koncové uživatele ve vaší organizaci:

* Moje aplikace v Azure AD
* Microsoft 365 spouštěč aplikace
* Přímé přihlašování k federovaným aplikacím (služba-PR)
* Přímé odkazy na federované nebo existující aplikace či aplikace založené na hesle

Můžete určit, jestli se uživatelé přiřazení k podnikové aplikaci uvidí v okně moje aplikace a Microsoft 365 spouštěče aplikací.

## <a name="example-complex-application-assignment-with-azure-ad"></a>Příklad: komplexní přiřazení aplikace s Azure AD
Vezměte v úvahu aplikaci, jako je Salesforce. V mnoha organizacích se Salesforce primárně používá pro marketingové a prodejní týmy. Členové marketingového týmu často mají vysoce privilegovaný přístup k Salesforce, zatímco členové prodejního týmu mají omezený přístup. V mnoha případech má hlavní populace informačních pracovníků omezený přístup k aplikaci. Výjimky z těchto pravidel komplikuje věci. Je často výhradním přístupem k marketingovým nebo prodejním týmům, aby uživatelům udělili přístup nebo měnili své role nezávisle na těchto obecných pravidlech.

S Azure AD je možné předem nakonfigurovat aplikace, jako je Salesforce, jednotné přihlašování (SSO) a automatizované zřizování. Po nakonfigurování aplikace může správce provést jednorázovou akci, která vytvoří a přiřadí příslušné skupiny. V tomto příkladu může správce spustit následující přiřazení:

* [Dynamické skupiny](../fundamentals/active-directory-groups-create-azure-portal.md) se dají definovat tak, aby automaticky představovaly všechny členy marketingových a prodejních týmů pomocí atributů, jako je oddělení nebo role:
  
  * Do role marketing v Salesforce by se přiřadili všichni členové marketingových skupin.
  * Všechny členy skupin prodejního týmu by se přiřadily k roli Sales v Salesforce. Další upřesnění může použít několik skupin, které reprezentují regionální prodejní týmy přiřazené k různým rolím Salesforce.

* Pro povolení mechanismu výjimek může být pro každou roli vytvořená samoobslužná skupina. Například skupina "výjimka marketingu pro službu Salesforce" může být vytvořena jako samoobslužná skupina. Skupinu je možné přiřadit k marketingovým rolím Salesforce a tým vedoucí oddělení marketingu může být vlastníkem. Členové týmu marketingového vedení můžou přidat nebo odebrat uživatele, nastavit zásady připojení nebo dokonce schvalovat nebo odmítat žádosti jednotlivých uživatelů o připojení. Tento mechanismus se podporuje prostřednictvím vhodného prostředí pro informační pracovníky, které nevyžaduje specializované školení pro vlastníky nebo členy.

V tomto případě se všichni přiřazení uživatelé automaticky zřídí do Salesforce, protože se přidají do různých skupin. jejich přiřazení role by se v Salesforce aktualizovala. Uživatelé budou moci zjišťovat a přistupovat k Salesforce prostřednictvím mých aplikací, webových klientů Office nebo dokonce přechodem na přihlašovací stránku organizace Salesforce. Správci by mohli snadno zobrazit využití a stav přiřazení pomocí generování sestav Azure AD.

Správci můžou využít [podmíněný přístup Azure AD](../conditional-access/concept-conditional-access-users-groups.md) k nastavení zásad přístupu pro konkrétní role. Tyto zásady můžou zahrnovat, jestli je povolený přístup mimo podnikové prostředí, a dokonce i Multi-Factor Authentication nebo požadavky na zařízení pro zajištění přístupu v různých případech.

## <a name="access-to-microsoft-applications"></a>Přístup k aplikacím Microsoftu

Aplikace Microsoftu (jako Exchange, SharePoint, Yammer atd.) se přiřazují a spravují jinak než aplikace třetích stran SaaS nebo jiné aplikace, které integrujete se službou Azure AD pro jednotné přihlašování.

Existují tři hlavní způsoby, jak může uživatel získat přístup k aplikaci publikované v Microsoftu.

- Pro aplikace v Microsoft 365 nebo jiné placené sady se uživatelům udělí přístup prostřednictvím **přiřazení licence** buď přímo ke svému uživatelskému účtu, nebo prostřednictvím skupiny pomocí možnosti přiřazení licencí na základě skupin.
- Pro aplikace, které společnost Microsoft nebo třetí strana zveřejňuje volně, aby je mohli používat, můžou být uživatelům udělený přístup prostřednictvím [souhlasu uživatele](configure-user-consent.md). To znamená, že se přihlásí k aplikaci pomocí pracovního nebo školního účtu Azure AD a umožní, aby měl přístup k některé omezené sadě dat na svém účtu.
- Pro aplikace, které společnost Microsoft nebo třetí strana zveřejňuje volně pro použití kýmkoli, můžou být uživatelům udělen i přístup prostřednictvím [souhlasu správce](manage-consent-requests.md). To znamená, že správce zjistil, že aplikace může být používána všemi uživateli v organizaci, aby se přihlásila k aplikaci pomocí účtu globálního správce a udělila přístup všem v organizaci.

Některé aplikace tyto metody kombinují. Například některé aplikace od společnosti Microsoft jsou součástí předplatného Microsoft 365, ale stále vyžadují souhlas.

Uživatelé mají přístup k Microsoft 365 aplikacím prostřednictvím svých portálů Office 365. Můžete také zobrazit nebo skrýt aplikace Microsoft 365 v okně moje aplikace s [přepínačem viditelnosti sady Office 365](hide-application-from-user-portal.md) v **uživatelském nastavení** adresáře. 

Stejně jako u podnikových aplikací můžete [přiřadit uživatele](assign-user-or-group-access-portal.md) k určitým aplikacím microsoftu prostřednictvím Azure Portal nebo, pokud možnost portálu není dostupná, pomocí PowerShellu.

## <a name="next-steps"></a>Další kroky
* [Ochrana aplikací pomocí podmíněného přístupu](../conditional-access/concept-conditional-access-cloud-apps.md)
* [Samoobslužná správa skupin/SSAA](../enterprise-users/groups-self-service-management.md)

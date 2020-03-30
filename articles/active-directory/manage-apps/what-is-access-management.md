---
title: Správa přístupu k aplikacím pomocí Azure AD | Dokumenty společnosti Microsoft
description: Popisuje, jak služba Azure Active Directory umožňuje organizacím určit aplikace, ke kterým má každý uživatel přístup.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 489b15423add03d69070bc32057af97396a85309
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409062"
---
# <a name="managing-access-to-apps"></a>Správa přístupu k aplikacím

Průběžná správa přístupu, vyhodnocení využití a vytváření sestav jsou i nadále výzvou i poté, co je aplikace integrována do systému identit vaší organizace. V mnoha případech musí správci IT nebo helpdesk převzít trvalou aktivní roli při správě přístupu k vašim aplikacím. Někdy je přiřazení prováděno obecným nebo divizním IT týmem. Rozhodnutí o postoupení má být často delegováno na osoby s rozhodovací pravomocí, která vyžaduje jejich schválení před tím, než it provede přiřazení.  Jiné organizace investují do integrace s existujícím automatizovaným systémem správy identit a přístupu, jako je řízení přístupu na základě rolí (RBAC) nebo řízení přístupu na základě atributů (ABAC). Jak integrace, tak rozvoj pravidel bývají specializované a nákladné. Monitorování nebo vykazování obou přístupů řízení je jeho vlastní samostatné, nákladné a složité investice.

## <a name="how-does-azure-active-directory-help"></a>Jak služba Azure Active Directory pomáhá?

Azure AD podporuje rozsáhlou správu přístupu pro nakonfigurované aplikace, což organizacím umožňuje snadno dosáhnout správných zásad přístupu od automatického přiřazení založeného na atributech (scénáře ABAC nebo RBAC) prostřednictvím delegování a včetně správce. S Azure AD můžete snadno dosáhnout složitých zásad, které kombinují více modelů správy pro jednu aplikaci a dokonce můžete znovu použít pravidla správy napříč aplikacemi se stejnými cílovými skupinami.

Díky Azure AD je vykazování využití a přiřazení plně integrované, což správcům umožňuje snadno podávat zprávy o stavu přiřazení, chybách přiřazení a dokonce i využití.

### <a name="assigning-users-and-groups-to-an-app"></a>Přiřazení uživatelů a skupin k aplikaci

Přiřazení aplikace Azure AD se zaměřuje na dva primární režimy přiřazení:

* **Individuální zadání** Správce IT s oprávněními globálního správce adresáře může vybrat jednotlivé uživatelské účty a udělit jim přístup k aplikaci.

* **Přiřazení založené na skupině (vyžaduje Azure AD Premium P1 nebo P2)** Správce IT s oprávněními globálního správce adresáře může aplikaci přiřadit skupinu. Přístup konkrétních uživatelů je určen tím, zda jsou členy skupiny v době, kdy se pokusí o přístup k aplikaci. Jinými slovy, správce může efektivně vytvořit pravidlo přiřazení, které uvádí, že "každý aktuální člen přiřazené skupiny má přístup k aplikaci". Pomocí této možnosti přiřazení mohou správci využívat některou z možností správy skupin Azure AD, včetně [dynamických skupin založených na atributech](../fundamentals/active-directory-groups-create-azure-portal.md), externích skupin systému (například místní služby Active Directory nebo Workday) nebo skupin spravovaných správcem nebo samoobslužných spravovaných. Jednu skupinu lze snadno přiřadit k více aplikacím a zajistit, aby aplikace se spřažením přiřazení mohly sdílet pravidla přiřazení, což snižuje celkovou složitost správy. Všimněte si, že vnořené členství ve skupinách nejsou podporovány pro přiřazení na základě skupiny k aplikacím v tomto okamžiku.

Pomocí těchto dvou režimů přiřazení mohou správci dosáhnout jakéhokoli žádoucího přístupu správy přiřazení.

### <a name="requiring-user-assignment-for-an-app"></a>Vyžadování přiřazení uživatele pro aplikaci

U určitých typů aplikací máte možnost [vyžadovat, aby uživatelé byli přiřazeni k aplikaci](assign-user-or-group-access-portal.md#configure-an-application-to-require-user-assignment). Tímzabráníte všem uživatelům v přihlášení s výjimkou uživatelů, které explicitně přiřadíte k aplikaci. Tuto možnost podporují následující typy aplikací:

* Aplikace nakonfigurované pro federované jednotné přihlašování (SSO) s ověřováním na základě SAML
* Aplikace proxy aplikací, které používají předběžné ověřování služby Azure Active Directory
* Aplikace postavené na platformě aplikace Azure AD, které používají Ověřování OAuth 2.0 / OpenID Connect poté, co uživatel nebo správce s tímto uživatelem souhlasil. Některé podnikové aplikace nabízejí další kontrolu nad tím, kdo se může přihlásit.

Pokud přiřazení uživatele *není vyžadováno*, nepřiřazení uživatelé neuvidí aplikaci na přístupovém panelu Moje aplikace, ale stále se mohou přihlásit k samotné aplikaci (označované také jako přihlašování iniciované sp) nebo mohou použít **adresu URL uživatelského přístupu** na stránce **Vlastnosti** aplikace (označované také jako přihlášení iniciované idicí protokolu IDP).

U některých aplikací není ve vlastnostech aplikace k dispozici možnost vyžadovat přiřazení uživatele. V těchto případech můžete pomocí prostředí PowerShell nastavit vlastnost appRoleAssignmentRequired na instančním objektu.

### <a name="determining-the-user-experience-for-accessing-apps"></a>Určení uživatelského prostředí pro přístup k aplikacím

Azure AD poskytuje [několik přizpůsobitelných způsobů nasazení aplikací](end-user-experiences.md) koncovým uživatelům ve vaší organizaci:

* Přístupový panel Azure AD My Apps
* Spouštěč aplikací Office 365
* Přímé přihlašování k federovaným aplikacím (service-pr)
* Přímé odkazy na federované nebo existující aplikace či aplikace založené na hesle

Můžete určit, jestli ji uživatelé přiřazení k podnikové aplikaci uvidí na přístupovém panelu a ve spouštěči aplikací Office 365.

## <a name="example-complex-application-assignment-with-azure-ad"></a>Příklad: Komplexní přiřazení aplikací s Azure AD
Zvažte aplikaci, jako je Salesforce. V mnoha organizacích salesforce používají především marketingové a prodejní týmy. Členové marketingového týmu mají často vysoce privilegovaný přístup k salesforce, zatímco členové prodejního týmu mají omezený přístup. V mnoha případech má široká populace informačních pracovníků omezený přístup k aplikaci. Výjimky z těchto pravidel věci komplikují. Často je výsadou marketingových nebo prodejních vedoucích týmů udělit uživateli přístup nebo změnit jejich role nezávisle na těchto obecných pravidlech.

S Azure AD, aplikace jako Salesforce můžete předem nakonfigurovat pro jednotné přihlašování (SSO) a automatizované zřizování. Po konfiguraci aplikace může správce provést jednorázovou akci k vytvoření a přiřazení příslušných skupin. V tomto příkladu může správce provést následující přiřazení:

* [Dynamické skupiny](../fundamentals/active-directory-groups-create-azure-portal.md) lze definovat tak, aby automaticky reprezentovaly všechny členy marketingových a prodejních týmů pomocí atributů, jako je oddělení nebo role:
  
  * Všichni členové marketingových skupin by byli přiřazeni k roli "marketing" v Salesforce
  * Všichni členové skupin prodejního týmu budou přiřazeni k roli "prodej" v salesforce. Další upřesnění by mohlo použít více skupin, které představují regionální prodejní týmy přiřazené k různým rolím Salesforce.

* Chcete-li povolit mechanismus výjimky, samoobslužné skupiny mohou být vytvořeny pro každou roli. Například skupinu "Prodejní síla marketingové výjimky" lze vytvořit jako samoobslužnou skupinu. Skupinu lze přiřadit k marketingové roli Salesforce a tým vedoucích marketingu může být vlastníkem. Členové týmu vedoucích marketingu mohou přidávat nebo odebírat uživatele, nastavit zásady připojení nebo dokonce schválit nebo zamítnout žádosti jednotlivých uživatelů o připojení. Tento mechanismus je podporován prostřednictvím informačnípracovník odpovídající zkušenosti, které nevyžaduje specializované školení pro vlastníky nebo členy.

V takovém případě by se všichni přiřazení uživatelé automaticky zřažili do služby Salesforce, protože jsou přidáni do různých skupin, jejich přiřazení role by bylo aktualizováno v Salesforce. Uživatelé by mohli zjistit a získat přístup k Salesforce prostřednictvím panelu pro přístup k aplikacím společnosti Microsoft, webových klientů sady Office nebo dokonce přechodem na přihlašovací stránku salesforce organizace. Správci by mohli snadno zobrazit stav využití a přiřazení pomocí sestav Azure AD.

Správci můžou používat [podmíněný přístup Azure AD](../active-directory-conditional-access-azure-portal.md) k nastavení zásad přístupu pro konkrétní role. Tyto zásady mohou zahrnovat, zda je přístup povolen mimo podnikové prostředí a dokonce i vícefaktorové ověřování nebo požadavky na zařízení k dosažení přístupu v různých případech.

## <a name="access-to-microsoft-applications"></a>Přístup k aplikacím společnosti Microsoft

Microsoft Aplikace (jako Office 365 Exchange, SharePoint, Yammer, atd.) jsou přiřazeny a spravovány trochu jinak než aplikace SaaS třetích stran nebo jiné aplikace, které integrujete s Azure AD pro jednotné přihlašování.

Existují tři hlavní způsoby, které uživatel může získat přístup k aplikaci publikované společností Microsoft.

- U aplikací v Office 365 nebo jiných placených sadách je uživatelům udělen přístup prostřednictvím **přiřazení licence** buď přímo k jejich uživatelskému účtu, nebo prostřednictvím skupiny využívající možnosti přiřazení licencí založených na skupině.
- U aplikací, které společnost Microsoft nebo třetí strana publikuje volně pro každého, kdo může používat, může být uživatelům udělen přístup prostřednictvím [souhlasu uživatele](configure-user-consent.md). To znamená, že se přihlásí k aplikaci pomocí svého účtu Azure AD Work nebo School a umožní jí přístup k některé omezené sadě dat na svém účtu.
- U aplikací, které společnost Microsoft nebo třetí strana publikuje volně pro každého, kdo může používat, může být uživatelům udělen přístup také na základě [souhlasu správce](manage-consent-requests.md). To znamená, že správce určil, že aplikace může být použita všemi v organizaci, takže se k aplikaci přihlásí pomocí účtu globálního správce a udělí přístup všem uživatelům v organizaci.

Některé aplikace kombinují tyto metody. Například některé aplikace Microsoftu jsou součástí předplatného Office 365, ale stále vyžadují souhlas.

Uživatelé mají přístup k aplikacím Office 365 prostřednictvím svých portálů Office 365. Aplikace Office 365 můžete taky zobrazit nebo skrýt na přístupovém panelu Moje aplikace s [přepínačem Viditelnost Office 365](hide-application-from-user-portal.md) v **nastavení uživatele**adresáře . 

Stejně jako u podnikových aplikací můžete [přiřadit uživatele](assign-user-or-group-access-portal.md) k určitým aplikacím Microsoftu prostřednictvím portálu Azure nebo, pokud možnost portálu není dostupná, pomocí PowerShellu.

## <a name="next-steps"></a>Další kroky
* [Ochrana aplikací pomocí podmíněného přístupu](../active-directory-conditional-access-azure-portal.md)
* [Samoobslužné řízení skupin/SSAA](../users-groups-roles/groups-self-service-management.md)

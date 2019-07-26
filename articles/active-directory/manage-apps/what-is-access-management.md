---
title: Správa přístupu k aplikacím pomocí Azure AD | Microsoft Docs
description: Popisuje, jak Azure Active Directory umožňuje organizacím určit aplikace, ke kterým mají jednotliví uživatelé přístup.
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
ms.openlocfilehash: 99c7947b6469f64f2ea05b2290305710db1ee796
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68477116"
---
# <a name="managing-access-to-apps"></a>Správa přístupu k aplikacím
Průběžná správa přístupu, vyhodnocení využití a vytváření sestav se po integraci aplikace do systému identit vaší organizace pořád stane výzvou. V mnoha případech musí správci IT nebo helpdesku vzít v úvahu probíhající aktivní roli při správě přístupu k vašim aplikacím. V některých případech je přiřazení prováděno generálním týmem týmu nebo oddělení IT. Rozhodnutí o přiřazení je často určené k tomu, aby mohl být delegovaný vedoucím podniku pro rozhodování, který vyžaduje jejich schválení předtím, než provede přiřazení.  Jiné organizace investovaly do integrace s existujícím automatizovaným systémem pro správu identit a přístupu, jako je Access Control na základě rolí (RBAC) nebo Access Control na základě atributů (ABAC). Vývoj pro integraci i pravidla je typicky specializovaný a nákladný. Monitorování nebo vytváření sestav z obou přístupů je svou vlastní samostatnou, nákladnou a složitou investici.

## <a name="how-does-azure-active-directory-help"></a>Jak Azure Active Directory nápovědě?
 Azure AD podporuje rozsáhlou správu přístupu pro nakonfigurované aplikace, což organizacím umožňuje snadno dosáhnout správných zásad přístupu v rozsahu od automatického přiřazení založeného na atributech (scénáře ABAC nebo RBAC) prostřednictvím delegování a včetně Správa správců. Pomocí Azure AD můžete snadno dosáhnout složitých zásad, kombinování více modelů správy pro jednu aplikaci a můžete dokonce znovu použít pravidla správy napříč aplikacemi se stejnými cílovými skupinami.

* [Přidávání nových nebo existujících aplikací](add-gallery-app.md)

  Přiřazení aplikace Azure AD se zaměřuje na dva primární režimy přiřazení:

* **Individuální přiřazení** Správce IT, který má oprávnění globálního správce adresáře, může vybrat jednotlivé uživatelské účty a udělit jim přístup k aplikaci.
* **Přiřazení na základě skupin (jenom placená služba Azure AD)** Správce IT, který má oprávnění globálního správce adresáře, může přiřadit skupinu k aplikaci. Přístup ke konkrétním uživatelům se určuje podle toho, jestli jsou členy skupiny v době, kdy se pokusí o přístup k aplikaci. Jinými slovy může správce efektivně vytvořit pravidlo přiřazení s informacemi o tom, že aktuální člen přiřazené skupiny má přístup k aplikaci. Pomocí této možnosti přiřazení můžou správci těžit z jakékoli možnosti správy skupin Azure AD, včetně [dynamických skupin založených na atributech](../fundamentals/active-directory-groups-create-azure-portal.md), skupin externích systémů (například místní služby Active Directory nebo Workday). Skupiny spravované správcem nebo samoobslužné služby. Jednu skupinu je možné snadno přiřadit k více aplikacím. tím se zajistí, že aplikace s spřažením přiřazení můžou sdílet pravidla přiřazení a snížit tak celkovou složitost správy. Všimněte si, že v tuto chvíli není v tomto okamžiku pro přiřazení na základě skupin podporována vnořená členství ve skupině.

Pomocí těchto dvou režimů přiřazení můžou správci dosáhnout jakéhokoli žádoucího přístupu pro správu přiřazení.

Pomocí služby Azure AD je vytváření sestav využití a přiřazení plně integrované a umožňuje správcům snadno vytvářet sestavy o stavu přiřazení, chybách přiřazení a dokonce o využití.

## <a name="complex-application-assignment-with-azure-ad"></a>Komplexní přiřazení aplikace pomocí Azure AD
Vezměte v úvahu aplikaci, jako je Salesforce. V mnoha organizacích se Salesforce primárně používá pro marketingové a prodejní týmy. Členové marketingového týmu často mají vysoce privilegovaný přístup k Salesforce, zatímco členové prodejního týmu mají omezený přístup. V mnoha případech má hlavní populace informačních pracovníků omezený přístup k aplikaci. Výjimky z těchto pravidel komplikuje věci. Je často výhradním přístupem k marketingovým nebo prodejním týmům, aby uživatelům udělili přístup nebo měnili své role nezávisle na těchto obecných pravidlech.

S Azure AD je možné předem nakonfigurovat aplikace, jako je Salesforce, jednotné přihlašování (SSO) a automatizované zřizování. Po nakonfigurování aplikace může správce provést jednorázovou akci, která vytvoří a přiřadí příslušné skupiny. V tomto příkladu může správce spustit následující přiřazení:

* [Dynamické skupiny](../fundamentals/active-directory-groups-create-azure-portal.md) se dají definovat tak, aby automaticky představovaly všechny členy marketingových a prodejních týmů pomocí atributů, jako je oddělení nebo role:
  
  * Do role marketing v Salesforce by se přiřadili všichni členové marketingových skupin.
  * Všechny členy skupin prodejního týmu by se přiřadily k roli Sales v Salesforce. Další upřesnění může použít několik skupin, které reprezentují regionální prodejní týmy přiřazené k různým rolím Salesforce.
* Pro povolení mechanismu výjimek může být pro každou roli vytvořená samoobslužná skupina. Například skupina "výjimka marketingu pro službu Salesforce" může být vytvořena jako samoobslužná skupina. Skupinu je možné přiřadit k marketingovým rolím Salesforce a tým vedoucí oddělení marketingu může být vlastníkem. Členové týmu marketingového vedení můžou přidat nebo odebrat uživatele, nastavit zásady připojení nebo dokonce schvalovat nebo odmítat žádosti jednotlivých uživatelů o připojení. Tento mechanismus se podporuje prostřednictvím vhodného prostředí pro informační pracovníky, které nevyžaduje specializované školení pro vlastníky nebo členy.

V tomto případě se všichni přiřazení uživatelé automaticky zřídí do Salesforce, protože se přidají do různých skupin. jejich přiřazení role by se v Salesforce aktualizovala. Uživatelé by mohli zjišťovat a přistupovat k Salesforce prostřednictvím panelu pro přístup k aplikacím Microsoftu, webových klientů Office, nebo dokonce přechodem na přihlašovací stránku organizace Salesforce. Správci by mohli snadno zobrazit využití a stav přiřazení pomocí generování sestav Azure AD.

Správci můžou využít [podmíněný přístup Azure AD](../active-directory-conditional-access-azure-portal.md) k nastavení zásad přístupu pro konkrétní role. Tyto zásady můžou zahrnovat, jestli je povolený přístup mimo podnikové prostředí, a dokonce i službu Multi-Factor Authentication nebo požadavky na zařízení, abyste mohli dosáhnout přístupu v různých případech.

## <a name="next-steps"></a>Další postup
* [Ochrana aplikací pomocí podmíněného přístupu](../active-directory-conditional-access-azure-portal.md)
* [Samoobslužná správa skupin/SSAA](../users-groups-roles/groups-self-service-management.md)

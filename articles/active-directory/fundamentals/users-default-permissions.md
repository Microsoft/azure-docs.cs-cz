---
title: Výchozí uživatelská oprávnění – Azure Active Directory | Microsoft Docs
description: Přečtěte si o různých uživatelských oprávněních, která jsou k dispozici v Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: ajburnle
ms.reviewer: vincesm
ms.custom: it-pro, seodec18, contperf-fy21q1
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb01a3e0fc5bc11a4d3de62b16aafb7dd308e34a
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2021
ms.locfileid: "98724267"
---
# <a name="what-are-the-default-user-permissions-in-azure-active-directory"></a>Jaká jsou výchozí uživatelská oprávnění v Azure Active Directory?
V Azure Active Directory (Azure AD) mají všichni uživatelé udělenou sadu výchozích oprávnění. Přístup uživatele se skládá z typu uživatel, [přiřazení jejich rolí](active-directory-users-assign-role-azure-portal.md)a jejich vlastnictví jednotlivých objektů. Tento článek popisuje tato výchozí oprávnění a obsahuje porovnání výchozích nastavení člena a uživatele typu host. Výchozí uživatelská oprávnění se dají změnit jenom v nastavení uživatele v Azure AD.

## <a name="member-and-guest-users"></a>Členové a uživatelé typu host
Sada výchozích oprávnění přijatá závisí na tom, jestli je uživatel nativním členem tenanta (členský uživatel), nebo jestli je uživatel převzatý z jiného adresáře jako host spolupráce B2B (uživatel typu Host). Další informace o přidávání uživatelů typu Host najdete v tématu [co je spolupráce Azure AD B2B](../external-identities/what-is-b2b.md) .
* Členové můžou registrovat aplikace, spravovat vlastní profilovou fotku a číslo mobilního telefonu, změnit vlastní heslo a zvát hosty B2B. Kromě toho můžou uživatelé číst všechny informace v adresáři (s několika výjimkami). 
* Uživatelé typu Host mají omezená oprávnění k adresáři. Můžou spravovat svůj vlastní profil, měnit vlastní heslo a načítat některé informace o dalších uživatelích, skupinách a aplikacích, ale nemůžou číst informace o všech adresářích. Uživatel typu Host například nemůže vytvořit výčet seznamu všech uživatelů, skupin a dalších objektů adresáře. Hosty je možné přidat do rolí správce, které jim udělí úplná oprávnění ke čtení a zápisu obsažená v dané roli. Hosté můžou pozvat i další hosty.

## <a name="compare-member-and-guest-default-permissions"></a>Porovnání výchozích oprávnění pro členy a hosty

**Plošný** | **Oprávnění člena** | **Výchozí oprávnění uživatele typu Host** | **Omezená uživatelská oprávnění typu Host (Preview)**
------------ | --------- | ---------- | ----------
Uživatelé a kontakty | <ul><li>Výčet seznamu všech uživatelů a kontaktů<li>Čtení všech veřejných vlastností uživatelů a kontaktů</li><li>Zvaní hostů<li>Změna vlastního hesla<li>Správa vlastního čísla mobilního telefonu<li>Správa vlastní fotky<li>Zneplatnění vlastních obnovovacích tokenů</li></ul> | <ul><li>Čtení vlastních vlastností<li>Čtení zobrazovaného jména, e-mailu, přihlašovací jméno, fotografie, hlavní název uživatele a vlastnosti typu uživatele u dalších uživatelů a kontaktů<li>Změna vlastního hesla<li>Vyhledat jiného uživatele podle ObjectId (Pokud je povoleno)<li>Informace o Správci čtení a přímých sestavách jiných uživatelů</li></ul> | <ul><li>Čtení vlastních vlastností<li>Změna vlastního hesla</li></ul>
Skupiny | <ul><li>Vytváření skupin zabezpečení<li>Vytvoření skupin Microsoft 365<li>Výčet seznamu všech skupin<li>Čtení všech vlastností skupin<li>Čtení neskrytých členství ve skupinách<li>Číst skryté Microsoft 365 členství ve skupině pro připojenou skupinu<li>Správa vlastností, vlastnictví a členství ve skupinách, které uživatel vlastní<li>Přidávání hostů do vlastněných skupin<li>Správa nastavení dynamického členství<li>Odstranění vlastněných skupin<li>Obnovení vlastněných Microsoft 365 skupin</li></ul> | <ul><li>Číst vlastnosti neskrytých skupin, včetně členství a vlastnictví (i nepřipojené skupiny)<li>Číst skryté Microsoft 365 členství ve skupinách pro připojené skupiny<li>Vyhledat skupiny podle zobrazovaného jména nebo ObjectId (Pokud je povoleno)</li></ul> | <ul><li>ID objektu pro Spojené skupiny<li>Čtení členství a vlastnictví spojených skupin v některých aplikacích Microsoft 365 (Pokud je povolené)</li></ul>
Aplikace | <ul><li>Registrace (vytvoření) nové aplikace<li>Zobrazení výčtu všech aplikací<li>Čtení vlastností zaregistrovaných a podnikových aplikací<li>Správa vlastností aplikací, jejich přiřazení a přihlašovacích údajů u vlastněných aplikací<li>Vytvoření nebo odstranění hesla aplikace pro uživatele<li>Odstranění vlastněných aplikací<li>Obnovení vlastněných aplikací</li></ul> | <ul><li>Čtení vlastností zaregistrovaných a podnikových aplikací</li></ul> | <ul><li>Čtení vlastností zaregistrovaných a podnikových aplikací
Zařízení</li></ul> | <ul><li>Zobrazení výčtu všech zařízení<li>Čtení všech vlastností zařízení<li>Správa všech vlastností vlastněných zařízení</li></ul> | Žádná oprávnění | Žádná oprávnění
Adresář | <ul><li>Čtení všech informací o společnosti<li>Čtení všech domén<li>Čtení všech partnerských kontraktů</li></ul> | <ul><li>Přečíst zobrazované jméno společnosti<li>Čtení všech domén</li></ul> | <ul><li>Přečíst zobrazované jméno společnosti<li>Čtení všech domén</li></ul>
Role a obory | <ul><li>Čtení všech rolí pro správu a členství v nich<li>Čtení všech vlastností a členství jednotek pro správu</li></ul> | Žádná oprávnění | Žádná oprávnění
Předplatná | <ul><li>Čtení všech předplatných<li>Povolení člena plánu služby</li></ul> | Žádná oprávnění | Žádná oprávnění
Zásady | <ul><li>Čtení všech vlastností zásad<li>Správa všech vlastností vlastněných zásad</li></ul> | Žádná oprávnění | Žádná oprávnění

## <a name="restrict-member-users-default-permissions"></a>Omezení výchozích oprávnění uživatelů pro členy 

Výchozí oprávnění pro členské uživatele lze omezit následujícími způsoby:

Oprávnění | Vysvětlení nastavení
---------- | ------------
Uživatelé můžou zaregistrovat aplikaci. | Nastavením této možnosti na možnost Ne znemožníte uživatelům vytvářet registrace aplikací. Tuto možnost je pak možné udělit konkrétním jednotlivcům, když je přidáte do role vývojář aplikace.
Umožňuje uživatelům připojit pracovní nebo školní účet pomocí LinkedInu. | Nastavením této možnosti na ne znemožníte uživatelům připojit svůj pracovní nebo školní účet ke svému účtu LinkedIn. Další informace najdete v tématu [připojení k účtu LinkedIn – sdílení dat a jejich souhlas](../enterprise-users/linkedin-user-consent.md).
Možnost vytvářet skupiny zabezpečení | Nastavení této možnosti na hodnotu Ne zabrání uživatelům vytvářet skupiny zabezpečení. Globální správci a správci uživatelů stále můžou vytvářet skupiny zabezpečení. Informace o postupu najdete v tématu [Rutiny Azure Active Directory pro konfiguraci nastavení skupiny](../enterprise-users/groups-settings-cmdlets.md).
Možnost vytvářet Microsoft 365 skupiny | Nastavením této možnosti na možnost Ne znemožníte uživatelům vytvářet Microsoft 365 skupiny. Nastavení této možnosti na některé umožňuje vybrat sadu uživatelů pro vytváření Microsoft 365ch skupin. Globální správci a správci uživatelů budou stále moci vytvářet Microsoft 365 skupiny. Informace o postupu najdete v tématu [Rutiny Azure Active Directory pro konfiguraci nastavení skupiny](../enterprise-users/groups-settings-cmdlets.md).
Omezení přístupu k portálu pro správu Azure AD | Nastavením této možnosti na Ne umožníte, aby uživatelé bez oprávnění správce mohli číst a spravovat prostředky Azure AD pomocí portálu pro správu Azure AD. Hodnota Ano omezí přístup všech uživatelů bez oprávnění správce k žádným datům Azure AD na portálu pro správu.<p>**Poznámka**: Toto nastavení neomezuje přístup k datům Azure AD pomocí PowerShellu nebo jiných klientů, jako je například Visual Studio. Pokud nastavíte Ano, udělíte konkrétnímu uživateli bez oprávnění správce možnost používat portál pro správu Azure AD, přiřazovat jakékoli administrativní role, jako je například role čtenáři adresáře.<p>Tato role umožňuje čtení základních informací o adresáři, které členské uživatelé mají ve výchozím nastavení (hosté a instanční objekty nedělají).
Možnost číst ostatní uživatele | Toto nastavení je k dispozici pouze v PowerShellu. Nastavením tohoto příznaku $false znemožníte čtení informací o uživatelích z adresáře všem jiným uživatelům než správcům. Tento příznak nebrání čtení informací o uživateli v jiných službách Microsoftu, jako je Exchange Online. Toto nastavení je určeno pro zvláštní okolnosti a nastavení tohoto příznaku na $false se nedoporučuje.

## <a name="restrict-guest-users-default-permissions"></a>Omezení výchozích oprávnění uživatelů typu Host

Výchozí oprávnění pro uživatele typu Host můžou být omezená následujícími způsoby:

>[!NOTE]
>Nastavení omezení přístupu uživatele hostů nahrazená **oprávnění uživatelé typu Host jsou omezená** . Pokyny k použití této funkce najdete v tématu [omezení oprávnění přístupu hosta (Preview) v Azure Active Directory](../enterprise-users/users-restrict-guest-permissions.md).

Oprávnění | Vysvětlení nastavení
---------- | ------------
Omezení přístupu uživatele hosta (Preview) | Když nastavíte tuto možnost na **uživatele typu Host, budou mít stejný přístup jako členové** ve výchozím nastavení všichni členové oprávnění uživatelů typu Host.<p>Nastavení této možnosti na **uživatelský přístup typu Host je omezené na vlastnosti a členství ve vlastních objektech adresáře** omezuje přístup hosta pouze na vlastní profil uživatele ve výchozím nastavení. Přístup jiným uživatelům již není povolen ani při hledání podle hlavního názvu uživatele, ObjectId nebo zobrazovaného názvu. Přístup k informacím o skupinách, včetně členství ve skupinách, už není povolený.<p>**Poznámka**: Toto nastavení nebrání přístupu k připojeným skupinám v některých službách Microsoft 365, jako je Microsoft Teams. Další informace najdete v tématu věnovaném [přístupu Microsoft Teams Host](/MicrosoftTeams/guest-access) .<p>Uživatelé typu Host můžou být stále přidáni do rolí správce bez ohledu na toto nastavení oprávnění.
Hosté můžou posílat pozvánky | Nastavení této možnosti na hodnotu Ano umožní hostům pozvat další hosty. Další informace najdete v tématu [delegované pozvánky pro spolupráci B2B](../external-identities/delegate-invitations.md#configure-b2b-external-collaboration-settings) .
Členové můžou posílat pozvánky | Když tuto možnost nastavíte na Ano, umožníte uživatelům, kteří nejsou správci vašeho adresáře, pozvat hosty. Další informace najdete v tématu [delegované pozvánky pro spolupráci B2B](../external-identities/delegate-invitations.md#configure-b2b-external-collaboration-settings) .
Správci a uživatelé v roli odesílatele pozvánky hostů můžou posílat pozvánky | Nastavení této možnosti na Ano umožní správcům a uživatelům v roli "pozvání hosta" pozvat hosty. Pokud je nastaveno na Ano, uživatelé v roli pozvání hosta budou moci pozvat hosty, bez ohledu na to, co můžou členové pozvat. Další informace najdete v tématu [delegované pozvánky pro spolupráci B2B](../external-identities/delegate-invitations.md#assign-the-guest-inviter-role-to-a-user) .

## <a name="object-ownership"></a>Vlastnictví objektů

### <a name="application-registration-owner-permissions"></a>Oprávnění vlastníka registrace aplikace
Když uživatel zaregistruje aplikaci, automaticky se přidá jako vlastník této aplikace. Jako vlastník může spravovat metadata aplikace, například její název a oprávnění, která aplikace vyžaduje. Může spravovat také konfiguraci aplikace specifickou pro tenanta, jako je konfigurace jednotného přihlašování a přiřazení uživatelů. Vlastník může také přidat nebo odebrat další vlastníky. Na rozdíl od globálních správců můžou vlastníci spravovat pouze aplikace, které vlastní.

### <a name="enterprise-application-owner-permissions"></a>Oprávnění vlastníka podnikové aplikace
Když uživatel přidá novou podnikovou aplikaci, automaticky se přidá jako vlastník. Jako vlastník můžou spravovat konfiguraci aplikace specifickou pro tenanta, jako je například konfigurace jednotného přihlašování, zřizování a přiřazování uživatelů. Vlastník může také přidat nebo odebrat další vlastníky. Na rozdíl od globálních správců můžou vlastníci spravovat jenom aplikace, které vlastní.

### <a name="group-owner-permissions"></a>Oprávnění vlastníka skupiny
Když uživatel vytvoří skupinu, automaticky se přidá jako vlastník této skupiny. Jako vlastník mohou spravovat vlastnosti skupiny, jako je název, a také spravovat členství ve skupině. Vlastník může také přidat nebo odebrat další vlastníky. Na rozdíl od globálních správců a správců uživatelů můžou vlastníci spravovat jenom skupiny, které vlastní. Pokud chcete přiřadit vlastníka skupiny, přečtěte si téma [Správa vlastníků skupiny](active-directory-accessmanagement-managing-group-owners.md).

### <a name="ownership-permissions"></a>Oprávnění vlastnictví
Následující tabulky popisují specifická oprávnění v Azure Active Directory členské uživatele mají nad vlastními objekty. Uživatel má tato oprávnění pouze pro objekty, které vlastní.

#### <a name="owned-application-registrations"></a>Vlastní registrace aplikací
Uživatelé mohou provádět následující akce při registraci vlastní aplikace.

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Directory/Applications/publikum/Update | Aktualizuje vlastnost Applications. cílová skupina v Azure Active Directory. |
| Microsoft. Directory/Applications/Authentication/Update | Aktualizuje vlastnost Applications. Authentication v Azure Active Directory. |
| Microsoft. Directory/Applications/Basic/Update | Aktualizuje základní vlastnosti pro aplikace v Azure Active Directory. |
| Microsoft. Directory/aplikace/přihlašovací údaje/aktualizace | Aktualizuje vlastnost Applications. credentialss v Azure Active Directory. |
| Microsoft. Directory/aplikace/odstranit | Odstraní aplikace v Azure Active Directory. |
| Microsoft. Directory/aplikace/vlastníci/aktualizace | Aktualizuje vlastnost Applications. Owners v Azure Active Directory. |
| Microsoft. Directory/aplikace/oprávnění/aktualizace | Aktualizuje vlastnost Applications. Permissions v Azure Active Directory. |
| Microsoft. Directory/aplikace/zásady/aktualizace | Aktualizuje vlastnost Applications. policies v Azure Active Directory. |
| Microsoft. Directory/aplikace/obnovení | Obnovte aplikace v Azure Active Directory. |

#### <a name="owned-enterprise-applications"></a>Vlastněné podnikové aplikace
Uživatelé můžou na vlastněných podnikových aplikacích provádět následující akce. Podniková aplikace se skládá z instančního objektu, jedné nebo více zásad použití a někdy aplikace ve stejném tenantovi jako instanční objekt.

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Directory/auditLogs/allProperties/Read | Načte všechny vlastnosti (včetně privilegovaných vlastností) na auditLogs v Azure Active Directory. |
| Microsoft. Directory/policies/Basic/Update | Aktualizuje základní vlastnosti zásad v Azure Active Directory. |
| Microsoft. Directory/policies/DELETE | Odstraňte zásady v Azure Active Directory. |
| Microsoft. Directory/policies/Owners/Update | V Azure Active Directory vlastnost Update policies. Owners. |
| Microsoft. Directory/servicePrincipals/appRoleAssignedTo/Update | Aktualizuje vlastnost servicePrincipals. appRoleAssignedTo v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/appRoleAssignments/Update | Umožňuje aktualizovat vlastnost Users. appRoleAssignments v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/publikum/Update | Aktualizuje vlastnost servicePrincipals. cílová skupina v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/Authentication/Update | Aktualizuje vlastnost servicePrincipals. Authentication v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/Basic/Update | Aktualizuje základní vlastnosti v servicePrincipals v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/přihlašovací údaje/aktualizace | Aktualizuje vlastnost servicePrincipals. credentialss v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/DELETE | Odstraní servicePrincipals v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/Owners/Update | Aktualizuje vlastnost servicePrincipals. Owners v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/oprávnění/aktualizace | Aktualizujte vlastnost servicePrincipals. Permissions v Azure Active Directory. |
| Microsoft. Directory/servicePrincipals/policies/Update | Aktualizujte vlastnost servicePrincipals. policies v Azure Active Directory. |
| Microsoft. Directory/signInReports/allProperties/Read | Načte všechny vlastnosti (včetně privilegovaných vlastností) na signInReports v Azure Active Directory. |

#### <a name="owned-devices"></a>Vlastněná zařízení
Uživatelé můžou na vlastněných zařízeních provádět následující akce.

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Directory/Devices/bitLockerRecoveryKeys/Read | Číst vlastnost Devices. bitLockerRecoveryKeys v Azure Active Directory. |
| Microsoft. Directory/Devices/Disable | Zakáže zařízení v Azure Active Directory. |

#### <a name="owned-groups"></a>Vlastněné skupiny
Uživatelé mohou provádět následující akce u vlastněných skupin.

| **Akce** | **Popis** |
| --- | --- |
| Microsoft. Directory/Groups/appRoleAssignments/Update | Aktualizuje vlastnost groups. appRoleAssignments v Azure Active Directory. |
| Microsoft. Directory/Groups/Basic/Update | Aktualizuje základní vlastnosti pro skupiny v Azure Active Directory. |
| Microsoft. Directory/Groups/DELETE | Odstraní skupiny v Azure Active Directory. |
| Microsoft. Directory/Groups/dynamicMembershipRule/Update | Aktualizuje vlastnost groups. dynamicMembershipRule v Azure Active Directory. |
| Microsoft. Directory/Groups/Members/Update | Aktualizuje vlastnost groups. Members v Azure Active Directory. |
| Microsoft. Directory/Groups/Owners/Update | Aktualizuje vlastnost groups. Owners v Azure Active Directory. |
| Microsoft. Directory/skupiny/obnovení | Obnovte skupiny v Azure Active Directory. |
| Microsoft. Directory/Groups/Settings/Update | Aktualizuje vlastnost groups. Settings v Azure Active Directory. |

## <a name="next-steps"></a>Další kroky

* Další informace o nastavení omezení přístupu uživatele hostů najdete [v tématu omezení oprávnění přístupu hosta (Preview) v Azure Active Directory](../enterprise-users/users-restrict-guest-permissions.md).
* Další informace o tom, jak přiřadit role správce Azure AD, najdete v tématu [přiřazení uživatele k rolím Správce v Azure Active Directory](active-directory-users-assign-role-azure-portal.md)
* Další informace o tom, jak se přístup k prostředkům řídí ve službě Microsoft Azure, najdete v části [Principy přístupu k prostředkům ve službě Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Další informace o vztahu Azure Active Directory k předplatnému Azure najdete v tématu [Jak je předplatné Azure propojeno se službou Azure Active Directory](active-directory-how-subscriptions-associated-directory.md).
* [Správa uživatelů](add-users-azure-active-directory.md)
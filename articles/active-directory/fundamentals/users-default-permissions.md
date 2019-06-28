---
title: Výchozí uživatelská oprávnění – Azure Active Directory | Dokumentace Microsoftu
description: Další informace o různých uživatelských oprávnění k dispozici v Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 02/16/2019
ms.author: lizross
ms.reviewer: vincesm
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 206e501860691cccc0578a0df4eec2b161b99b4c
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341370"
---
# <a name="what-are-the-default-user-permissions-in-azure-active-directory"></a>Co jsou výchozí oprávnění uživatelů ve službě Azure Active Directory?
V Azure Active Directory (Azure AD) mají všichni uživatelé udělenou sadu výchozích oprávnění. Přístup uživatele se skládá z typ uživatele, jejich [přiřazení rolí](active-directory-users-assign-role-azure-portal.md)a jejich vlastnictví jednotlivé objekty. Tento článek popisuje tato výchozí oprávnění a obsahuje porovnání výchozích nastavení člena a uživatele typu host. Výchozí oprávnění uživatelů lze změnit pouze v nastavení uživatele ve službě Azure AD.

## <a name="member-and-guest-users"></a>Členové a uživatelé typu host
Sada výchozích oprávnění přijata závisí na, jestli se jedná o nativní člena tenanta (uživatel členem) nebo pokud uživatel se přepne do režimu z jiného adresáře v roli hosta spolupráce B2B (uživatel typu Host). Zobrazit [co je spolupráce B2B ve službě Azure AD?](../b2b/what-is-b2b.md) pro další informace o přidání uživatele typu Host.
* Členové můžou registrovat aplikace, spravovat vlastní profilovou fotku a číslo mobilního telefonu, změnit vlastní heslo a zvát hosty B2B. Kromě toho můžou uživatelé číst všechny informace v adresáři (s několika výjimkami). 
* Uživatelé typu Host mají omezený oprávnění pro adresář. Uživatelé typu host například nemůžou procházet informace z tenanta nad rámec informací o vlastním profilu. Uživatel typu host však může načíst informace o jiném uživateli tím, že zadá hlavní název uživatele (UPN) nebo identifikátor objectID. Uživatel typu Host může číst vlastnosti skupin, do kterých patří, včetně členství ve skupině, bez ohledu na to **oprávnění uživatelů typu Host jsou omezená** nastavení. Host nejde zobrazit informace o dalších objektů pro tenanta.

Výchozí oprávnění pro hosty jsou ve výchozím nastavení omezující. Hosty je možné přidat do rolí správce, které jim udělí úplná oprávnění ke čtení a zápisu obsažená v dané roli. K dispozici je ještě jedno omezení – možnost hostů zvát jiné hosty. Nastavením možnosti **Hosté můžou posílat pozvánky** na hodnotu **Ne** zabráníte hostům zvát jiné hosty. Informace o postupu najdete v tématu [Delegování pozvánek pro spolupráci B2B](../b2b/delegate-invitations.md). Pokud chcete uživatelům typu host udělit stejná oprávnění, jako mají členové ve výchozím nastavení, nastavte možnost **Oprávnění uživatelů typu host jsou omezená** na hodnotu **Ne**. Toto nastavení standardně udělí uživatelům typu host všechna uživatelská oprávnění členů a také povolí přidání hostů do rolí pro správu.

## <a name="compare-member-and-guest-default-permissions"></a>Porovnání výchozích oprávnění pro členy a hosty

**Oblast** | **Uživatelská oprávnění člena** | **Uživatelská oprávnění hosta**
------------ | --------- | ----------
Uživatelé a kontakty | Čtení všech veřejných vlastností uživatelů a kontaktů<br>Zvaní hostů<br>Změna vlastního hesla<br>Správa vlastního čísla mobilního telefonu<br>Správa vlastní fotky<br>Zneplatnění vlastních obnovovacích tokenů | Čtení vlastních vlastností<br>Přečtěte si zobrazované jméno, e-mailu, přihlaste se název, fotografie, hlavní název uživatele a typ vlastnosti uživatele z jiných uživatelů a kontaktů<br>Změna vlastního hesla
Skupiny | Vytváření skupin zabezpečení<br>Vytváření skupin Office 365<br>Čtení všech vlastností skupin<br>Čtení neskrytých členství ve skupinách<br>Čtení skrytých členství ve skupinách Office 365 u připojené skupiny<br>Spravovat vlastnosti, vlastnictví a členství ve skupinách, které uživatel vlastní<br>Přidávání hostů do vlastněných skupin<br>Správa nastavení dynamického členství<br>Odstranění vlastněných skupin<br>Obnovení vlastněných skupin Office 365 | Čtení všech vlastností skupin<br>Čtení neskrytých členství ve skupinách<br>Čtení skrytých členství ve skupinách Office 365 u připojených skupin<br>Správa vlastněných skupin<br>Přidávání hostů do vlastněných skupin (pokud je to povoleno)<br>Odstranění vlastněných skupin<br>Obnovení vlastněných skupin Office 365<br>Umožňuje číst vlastnosti skupin, do kterých patří, včetně členství.
Aplikace | Registrace (vytvoření) nové aplikace<br>Čtení vlastností zaregistrovaných a podnikových aplikací<br>Správa vlastností aplikací, jejich přiřazení a přihlašovacích údajů u vlastněných aplikací<br>Vytvoření nebo odstranění hesla aplikace pro uživatele<br>Odstranění vlastněných aplikací<br>Obnovení vlastněných aplikací | Čtení vlastností zaregistrovaných a podnikových aplikací<br>Správa vlastností aplikací, jejich přiřazení a přihlašovacích údajů u vlastněných aplikací<br>Odstranění vlastněných aplikací<br>Obnovení vlastněných aplikací
Zařízení | Čtení všech vlastností zařízení<br>Správa všech vlastností vlastněných zařízení<br> | Žádná oprávnění<br>Odstranění vlastněných zařízení<br>
Adresář | Čtení všech informací o společnosti<br>Čtení všech domén<br>Čtení všech partnerských kontraktů | Čtení zobrazovaného názvu a ověřených domén
Role a obory | Čtení všech rolí pro správu a členství v nich<br>Čtení všech vlastností a členství jednotek pro správu | Žádná oprávnění 
Předplatná | Čtení všech předplatných<br>Povolení člena plánu služby | Žádná oprávnění
Zásady | Čtení všech vlastností zásad<br>Správa všech vlastností vlastněných zásad | Žádná oprávnění

## <a name="to-restrict-the-default-permissions-for-member-users"></a>Omezení výchozích oprávnění pro členy

Výchozí oprávnění pro členy je možné omezit následujícími způsoby.

Oprávnění | Vysvětlení nastavení
---------- | ------------
Uživatelé můžou registrovat aplikace | Nastavení této možnosti na Ne zabraňuje uživatelům vytvářet registrace aplikací. Možnost lze udělit pak zpátky na určité jednotlivce jejich přidáním do role pro vývojáře aplikací.
Umožnit uživatelům propojit pracovní nebo školní účet s Linkedinem | Nastavení této možnosti na Ne zabraňuje uživatelům v připojení svého pracovního nebo školního účtu se svým účtem LinkedIn. Další informace najdete v tématu [účet LinkedIn připojení sdílení dat a vyjádření souhlasu](https://docs.microsoft.com/azure/active-directory/users-groups-roles/linkedin-user-consent).
Možnost vytvářet skupiny zabezpečení | Nastavení této možnosti na hodnotu Ne zabrání uživatelům vytvářet skupiny zabezpečení. Globální správci a Správci uživatelů stále můžou vytvářet skupiny zabezpečení. Informace o postupu najdete v tématu [Rutiny Azure Active Directory pro konfiguraci nastavení skupiny](../users-groups-roles/groups-settings-cmdlets.md).
Možnost vytvářet skupiny Office 365 | Nastavení této možnosti na hodnotu Ne zabrání uživatelům vytvářet skupiny Office 365. Nastavení této možnosti na hodnotu Někteří umožní vytvářet skupiny Office 365 vybrané skupině uživatelů. Globální správci a Správci uživatelů bude stále moci vytvářet skupiny Office 365. Informace o postupu najdete v tématu [Rutiny Azure Active Directory pro konfiguraci nastavení skupiny](../users-groups-roles/groups-settings-cmdlets.md).
Omezení přístupu k portálu pro správu Azure AD | Nastavení této možnosti na hodnotu Ano zabraňuje uživatelům v přístupu Azure Active Directory s využitím webu Azure portal pouze.
Možnost číst ostatní uživatele | Toto nastavení je k dispozici pouze v PowerShellu. Nastavení tohoto příznaku na $false brání všechny bez oprávnění správce ve čtení informací o uživateli z adresáře. Tento příznak nezabraňuje čtení informací o uživateli v dalším službám Microsoftu, jako je Exchange Online. Toto nastavení je určená pro zvláštní okolnosti, a na $false nastavení tohoto příznaku se nedoporučuje.

## <a name="object-ownership"></a>Vlastnictví objektů

### <a name="application-registration-owner-permissions"></a>Oprávnění vlastníka registrace aplikace
Když uživatel zaregistruje aplikaci, automaticky se přidá jako vlastník této aplikace. Jako vlastník může spravovat metadata aplikace, například její název a oprávnění, která aplikace vyžaduje. Může spravovat také konfiguraci aplikace specifickou pro tenanta, jako je konfigurace jednotného přihlašování a přiřazení uživatelů. Vlastník může také přidat nebo odebrat další vlastníky. Na rozdíl od globálních správců můžou vlastníci spravovat pouze aplikace, které vlastní.

### <a name="enterprise-application-owner-permissions"></a>Oprávnění vlastníka aplikace organizace
Když uživatel přidá nové podnikovou aplikaci, jsou automaticky přidány jako vlastníka. Jako vlastníka můžou spravovat aplikace, jako je jednotné přihlašování konfiguraci, zřizování a přiřazení uživatelských konfigurace specifickým pro tenanta. Vlastník může také přidat nebo odebrat další vlastníky. Na rozdíl od globální správci vlastníci můžou spravovat jenom aplikace, které vlastní.

### <a name="group-owner-permissions"></a>Oprávnění vlastníka skupiny
Když uživatel vytvoří skupinu, automaticky se přidá jako vlastník této skupiny. Jako vlastníka, mohou spravovat vlastnosti skupiny, jako je například název, jakož i Správa členství ve skupinách. Vlastník může také přidat nebo odebrat další vlastníky. Na rozdíl od globální správci a Správci uživatelů pouze vlastníci můžou spravovat skupiny, které vlastní. Pokud chcete přiřadit vlastníka skupiny, přečtěte si téma [Správa vlastníků skupiny](active-directory-accessmanagement-managing-group-owners.md).

### <a name="ownership-permissions"></a>Vlastnictví oprávnění
Následující tabulky popisují konkrétní oprávnění v Azure Active Directory člen uživatelé mají nad vlastnictví objektů. Uživatel má tato oprávnění pouze u objektů, které vlastní.

#### <a name="owned-application-registrations"></a>Registrace vlastněné aplikací
Uživatelé mohou provádět následující akce na registrace vlastněné aplikací.

| **Akce** | **Popis** |
| --- | --- |
| microsoft.directory/applications/audience/update | Umožňuje aktualizujte vlastnost applications.audience ve službě Azure Active Directory. |
| microsoft.directory/applications/authentication/update | Umožňuje aktualizujte vlastnost applications.authentication ve službě Azure Active Directory. |
| microsoft.directory/applications/basic/update | Aktualizace základní vlastnosti na aplikace v Azure Active Directory. |
| microsoft.directory/applications/credentials/update | Umožňuje aktualizujte vlastnost applications.credentials ve službě Azure Active Directory. |
| microsoft.directory/applications/delete | Umožňuje odstraňte applications v Azure Active Directory. |
| microsoft.directory/applications/owners/update | Umožňuje aktualizujte vlastnost applications.owners v Azure Active Directory. |
| microsoft.directory/applications/permissions/update | Umožňuje aktualizujte vlastnost applications.permissions ve službě Azure Active Directory. |
| microsoft.directory/applications/policies/update | Umožňuje aktualizujte vlastnost applications.policies ve službě Azure Active Directory. |
| microsoft.directory/applications/restore | Umožňuje obnovte applications v Azure Active Directory. |

#### <a name="owned-enterprise-applications"></a>Vlastní podnikové aplikace
Uživatelé mohou provádět následující akce na vlastní podnikové aplikace. Podniková aplikace je tvořené instanční objekt služby, jeden nebo více zásad aplikací a někdy objekt aplikace ve stejném tenantovi jako instanční objekt služby.

| **Akce** | **Popis** |
| --- | --- |
| microsoft.directory/auditLogs/allProperties/read | Číst všechny vlastnosti (včetně privileged vlastností) na mají ve službě Azure Active Directory. |
| microsoft.directory/policies/basic/update | Aktualizace základní vlastnosti v policies v Azure Active Directory. |
| microsoft.directory/policies/delete | Umožňuje odstraňte policies v Azure Active Directory. |
| microsoft.directory/policies/owners/update | Umožňuje aktualizujte vlastnost policies.owners v Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Umožňuje aktualizujte vlastnost servicePrincipals.appRoleAssignedTo v Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignments/update | Umožňuje aktualizujte vlastnost users.appRoleAssignments v Azure Active Directory. |
| microsoft.directory/servicePrincipals/audience/update | Umožňuje aktualizujte vlastnost servicePrincipals.audience ve službě Azure Active Directory. |
| microsoft.directory/servicePrincipals/authentication/update | Umožňuje aktualizujte vlastnost servicePrincipals.authentication ve službě Azure Active Directory. |
| microsoft.directory/servicePrincipals/basic/update | Aktualizace základní vlastnosti na servicePrincipals v Azure Active Directory. |
| microsoft.directory/servicePrincipals/credentials/update | Umožňuje aktualizujte vlastnost servicePrincipals.credentials ve službě Azure Active Directory. |
| microsoft.directory/servicePrincipals/delete | Umožňuje odstraňte servicePrincipals v Azure Active Directory. |
| microsoft.directory/servicePrincipals/owners/update | Umožňuje aktualizujte vlastnost servicePrincipals.owners v Azure Active Directory. |
| microsoft.directory/servicePrincipals/permissions/update | Umožňuje aktualizujte vlastnost servicePrincipals.permissions ve službě Azure Active Directory. |
| microsoft.directory/servicePrincipals/policies/update | Umožňuje aktualizujte vlastnost servicePrincipals.policies ve službě Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Číst všechny vlastnosti (včetně privileged vlastností) na signInReports ve službě Azure Active Directory. |

#### <a name="owned-devices"></a>Zařízení ve vlastnictví firmy
Uživatelé mohou provádět následující akce v zařízení ve vlastnictví firmy.

| **Akce** | **Popis** |
| --- | --- |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | Umožňuje číst vlastnost devices.bitLockerRecoveryKeys ve službě Azure Active Directory. |
| microsoft.directory/devices/disable | Zakážete devices v Azure Active Directory. |

#### <a name="owned-groups"></a>Vlastní skupiny
Uživatelé mohou provádět následující akce pro vlastnictví skupiny.

| **Akce** | **Popis** |
| --- | --- |
| microsoft.directory/groups/appRoleAssignments/update | Umožňuje aktualizujte vlastnost groups.appRoleAssignments v Azure Active Directory. |
| microsoft.directory/groups/basic/update | Aktualizace základní vlastnosti skupin v Azure Active Directory. |
| microsoft.directory/groups/delete | Umožňuje odstraňte groups v Azure Active Directory. |
| microsoft.directory/groups/dynamicMembershipRule/update | Umožňuje aktualizujte vlastnost groups.dynamicMembershipRule v Azure Active Directory. |
| microsoft.directory/groups/members/update | Umožňuje aktualizujte vlastnost groups.members v Azure Active Directory. |
| microsoft.directory/groups/owners/update | Umožňuje aktualizujte vlastnost groups.owners v Azure Active Directory. |
| microsoft.directory/groups/restore | Umožňuje obnovte groups v Azure Active Directory. |
| microsoft.directory/groups/settings/update | Umožňuje aktualizujte vlastnost groups.settings v Azure Active Directory. |

## <a name="next-steps"></a>Další postup

* Další informace o tom, jak přiřadit role správce Azure AD najdete v tématu [uživatele přiřadit do rolí správce ve službě Azure Active Directory](active-directory-users-assign-role-azure-portal.md)
* Další informace o tom, jak se přístup k prostředkům řídí ve službě Microsoft Azure, najdete v části [Principy přístupu k prostředkům ve službě Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Další informace o vztahu Azure Active Directory k předplatnému Azure najdete v tématu [Jak je předplatné Azure propojeno se službou Azure Active Directory](active-directory-how-subscriptions-associated-directory.md).
* [Správa uživatelů](add-users-azure-active-directory.md)

---
title: Role správce odkazovat ve službě Azure Active Directory | Dokumentace Microsoftu
description: Roli správce můžete přidat uživatele, přiřazovat role správců, resetovat hesla uživatelů, spravovat uživatelské licence nebo spravovat domény.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 09/19/2018
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: 04deb1168c8c5c0977d0f20c9307ce10d2d12d35
ms.sourcegitcommit: 06724c499837ba342c81f4d349ec0ce4f2dfd6d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46466111"
---
# <a name="assigning-administrator-roles-in-azure-active-directory"></a>Přiřazení rolí správce v Azure Active Directory

Pomocí Azure Active Directory (Azure AD), můžete určit samostatné správcům slouží různým funkcím. Správci můžou určené na portálu Azure AD k provádění úlohy, jako je přidání nebo změna uživatelů, přiřazení správních rolí, resetovat hesla uživatelů, Správa uživatelských licencí a správa názvů domén.

## <a name="details-about-the-global-administrator-role"></a>Podrobnosti o roli Globální správce

Globální správce má přístup ke všem funkcím pro správu. Ve výchozím nastavení je osoba, která uživatel zaregistruje do služby pro předplatné Azure přiřadit roli globálního správce adresáře. Další role správců můžou přiřazovat jenom globální správci.

## <a name="assign-or-remove-administrator-roles"></a>Přiřazení nebo odebrání rolí správce

Zjistěte, jak přiřadit správní role pro uživatele v Azure Active Directory, najdete v článku [uživatele přiřadit do rolí správce ve službě Azure Active Directory](../fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="available-roles"></a>Dostupné role

K dispozici jsou následující role správce:

* **[Správce aplikace](#application-administrator)**: uživatelé v této roli mohou vytvářet a spravovat všechny aspekty podnikové aplikace, registrace aplikací a nastavení proxy aplikací. Tato role také umožňuje udělovat souhlas delegovaná oprávnění a oprávnění aplikací bez Microsoft Graph a Azure AD Graph. Členové této role nebudou přidány jako vlastníky, při vytváření aplikace v kterémkoli nebo podnikové aplikace.

* **[Vývojář aplikace](#application-developer)**: uživatelé s touto rolí může vytvářet registrace aplikací při "Uživatelé můžou registrovat aplikace" nastavená na Ne. Tato role také umožňuje členům vyjádřili souhlas na vlastním jménem při "Uživatelé můžou udělit souhlas s aplikací, které přistupují k firemním datům jejich jménem" nastavená na Ne. Členové této role jsou přidány jako vlastníky, při vytváření aplikace v kterémkoli nebo podnikové aplikace.

* **[Správce fakturace](#billing-administrator)**: může dělat nákupy, spravovat předplatná, spravovat lístky podpory a sledovat stav služeb.

* **[Správce cloudové aplikace](#cloud-application-administrator)**: uživatelé v této roli mají stejná oprávnění jako role správce aplikace s výjimkou schopnost spravovat proxy aplikací. Tato role uděluje možnost vytvářet a spravovat všechny aspekty podnikové aplikace a registrace aplikací. Tato role také umožňuje udělovat souhlas delegovaná oprávnění a oprávnění aplikací bez Microsoft Graph a Azure AD Graph. Členové této role nebudou přidány jako vlastníky, při vytváření aplikace v kterémkoli nebo podnikové aplikace.

* **[Správce cloudových zařízení](#cloud-device-administrator)**: uživatelé v této roli můžou povolit, zakázat a odstraňovat zařízení ve službě Azure AD a čtení klíče nástroje BitLocker systému Windows 10 (pokud existuje) na webu Azure Portal. Role nejsou udělena oprávnění ke správě jiných vlastností v zařízení.

* **[Správce dodržování předpisů](#compliance-administrator)**: uživatelé s touto rolí mají oprávnění pro správu v Office 365 zabezpečení a dodržování předpisů System Center a centra pro správu Exchange. Další informace na [role správců Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **[Správce podmíněného přístupu](#conditional-access-administrator)**: uživatelé s touto rolí se budou moct spravovat nastavení podmíněného přístupu Azure Active Directory.
  > [!NOTE]
  > Pokud chcete nasadit zásady podmíněného přístupu Exchange ActiveSync v Azure, musí uživatel také být globálním správcem.
  
* **[Správci zařízení](#device-administrators)**: Tato role je k dispozici pro přiřazení pouze jako další místní správce v [nastavení zařízení](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/). Uživatelé s touto rolí se na všech zařízeních s Windows 10, která jsou připojená k Azure Active Directory, stávají správci místních počítačů. Nemají možnost spravovat objekty zařízení v Azure Active Directory. 

* **[Uživatelé Čtoucí z adresáře](#directory-readers)**: Toto je starší verze role, která má být přiřazena k aplikacím, které nepodporují [souhlas Framework](../develop/quickstart-v1-integrate-apps-with-azure-ad.md). Neměla být přiřazena k žádným uživatelům.

* **[Účty pro synchronizaci adresářů](#directory-synchronization-accounts)**: nepoužívejte. Tato role se automaticky přiřadí ke službě Azure AD Connect a není určena nebo nepodporuje pro jiné použití.

* **[Uživatelé zapisující do adresáře](#directory-writers)**: Toto je starší verze role, která má být přiřazena k aplikacím, které nepodporují [souhlas Framework](../develop/quickstart-v1-integrate-apps-with-azure-ad.md). Neměla být přiřazena k žádným uživatelům.

* **[Správce služby Dynamics 365 nebo správce služby CRM](#dynamics-365-service-administrator)**: uživatelé s touto rolí mají globální oprávnění ve službě Microsoft Dynamics 365 Online, když služba používá, a možnost spravovat lístky podpory a monitorování stavu služby. Další informace na [použít roli Správce služby ke správě svého tenanta](https://docs.microsoft.com/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant).

* **[Správce služby Exchange](#exchange-service-administrator)**: uživatelé s touto rolí mají globální oprávnění ve službě Microsoft Exchange Online, pokud služba není k dispozici. Další informace na [role správců Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **[Globální správce / správce společnosti](#company-administrator)**: uživatelé s touto rolí mají přístup ke všem funkcím pro správu v Azure Active Directory, jakož i služeb, které používají identity Azure Active Directory, jako je Exchange Online SharePoint Online a Online Skype pro firmy. Osoba, která se zaregistruje k tenantovi Azure Active Directory se stane globálním správcem. Další role správců můžou přiřazovat jenom globální správci. Ve vaší společnosti může být více než jednoho globálního správce. Globální správci můžou resetovat heslo kteréhokoliv uživatele a všech ostatních správců.

  > [!NOTE]
  > V rozhraní Microsoft Graph API, Azure AD Graph API a Azure AD PowerShell tato role nazývá "Správce společnosti". Je "Globální správce" v [webu Azure portal](https://portal.azure.com).
  >
  >

* **[Odesílatel pozvánky hostů](#guest-inviter)**: uživatelé v této roli můžou spravovat pozvánky uživatelů typu Host Azure Active Directory s B2B při **členové můžou posílat pozvánky** uživatelské nastavení je Ne. Další informace o spolupráci B2B v [o aplikaci Azure AD B2B collaboration](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Nezahrnuje žádné jiné oprávnění.

* **[Správce Information Protection](#information-protection-administrator)**: uživatelé s touto rolí mají všechna oprávnění ve službě Azure Information Protection. Tato role umožňuje konfigurace popisků pro zásady Azure Information Protection, spravovat šablony ochrany a zapnutí ochrany. Tato role neuděluje žádná oprávnění v Centrum Identity Protection, Privileged Identity Management, monitorování Office 365 Service Health, nebo Office 365 Centru zabezpečení a dodržování předpisů.

* **[Správce služby Intune](#intune-service-administrator)**: uživatelé s touto rolí mají globální oprávnění v Microsoft Intune Online, pokud služba není k dispozici. Kromě toho tato role obsahuje možnost spravovat uživatele a zařízení, aby bylo možné přiřazovat zásady, jakož i vytvářet a spravovat skupiny. Další informace na [řízení správy na základě rolí (RBAC) v Microsoft Intune](https://docs.microsoft.com/intune/role-based-access-control)

* **[Správce licencí](#license-administrator)**: v této roli uživatele můžete přidat, odebrat a aktualizovat přiřazení licencí pro uživatele, skupiny (použití skupinové licence) a spravovat místo využívání na uživatele. Role nejsou udělena možnost nákupu nebo spravovat předplatná, vytvořit nebo spravovat skupiny, nebo vytvořit nebo spravovat uživatele nad rámec místo využívání.

* **[Čtenář Centra zpráv](#message-center-reader)**: uživatelé v této roli můžete sledovat oznámení a poradenství stavu aktualizace v [Centru zpráv Office 365](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093) pro svoji organizaci na nakonfigurované služby, jako je například Exchange, Intune a Microsoft Teams. Centrum zpráv čtečky přijímat týdenního přehledu e-mailu příspěvků, aktualizace a můžete sdílet zpráva center příspěvky ve službách Office 365. Ve službě Azure AD Uživatelé s touto rolí budete mít pouze oprávnění jen pro čtení na služby Azure AD, jako jsou uživatelé a skupiny. 

* **[Partnerská podpora úrovně 1](#partner-tier1-support)**: nepoužívejte. Tato role se už nepoužívá a bude odebrána z Azure AD v budoucnu. Tato role je určen pro malý počet těmito partnery společnosti Microsoft a není určena pro obecné použití.

* **[Partnerská podpora úrovně 2](#partner-tier2-support)**: nepoužívejte. Tato role se už nepoužívá a bude odebrána z Azure AD v budoucnu. Tato role je určen pro malý počet těmito partnery společnosti Microsoft a není určena pro obecné použití.

* **[Heslo správce / správce technické podpory](#helpdesk-administrator)**: uživatelé s touto rolí můžou měnit hesla, zneplatnit obnovovací tokeny, spravovat žádosti o služby a monitorovat stav služby. Správců technické podpory můžou měnit hesla a zneplatnit tokeny obnovení pouze pro uživatele a jiným správcům technické podpory. Zrušení platnosti tokenu obnovení vynutí uživatel znovu přihlásil.

  > [!NOTE]
  > V rozhraní Microsoft Graph API, Azure AD Graph API a Azure AD PowerShell tato role nazývá "Správce technické podpory". Je "Heslo správce" v [webu Azure portal](https://portal.azure.com/).
  >
  >
  
* **[Správce služby Power BI](#power-bi-service-administrator)**: uživatelé s touto rolí mají globální oprávnění v Microsoft Power BI, pokud služba používá, a možnost spravovat lístky podpory a monitorovat stav služby. Další informace na [Principy role správce Power BI](https://docs.microsoft.com/power-bi/service-admin-role).

* **[Privilegované Role správce](#privileged-role-administrator)**: uživatelé s touto rolí můžou Spravovat přiřazení rolí v Azure Active Directory, i v rámci Azure AD Privileged Identity Management. Kromě toho tato role umožňuje spravovat všechny aspekty služby Privileged Identity Management.

* **[Čtenář sestav](#reports-reader)**: uživatelé s touto rolí můžou zobrazit využití sestav, data a sestavy řídicího panelu v Centru pro správu Office 365 a kontext přijetí pack ve službě Power BI. Kromě toho role poskytuje přístup k přihlašování sestavy a aktivity ve službě Azure AD a dat vrácených Microsoft Graph API pro vytváření sestav. Uživatel přiřazený k roli Čtenář sestav můžete přistupovat pouze relevantní využití a metrik přijetí. Nemají žádná oprávnění správce ke konfiguraci nastavení nebo přístupu centra pro správu konkrétní produktu jako je Exchange. 

* **[Správce zabezpečení](#security-administrator)**: uživatelé s touto rolí mají všechna oprávnění jen pro čtení v roli Čtenář zabezpečení, plus umožňuje spravovat konfiguraci služeb souvisejících se zabezpečením: Azure Active Directory Identity Protection, Azure Information Protection a Office 365 Centru zabezpečení a dodržování předpisů. Další informace o oprávněních Office 365 je k dispozici na [oprávnění v centru dodržování předpisů a zabezpečení Office 365](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).
  
  | V | Můžete provést |
  | --- | --- |
  | Centrum Identity Protection |<ul><li>Všechna oprávnění role čtenáře zabezpečení.<li>Kromě toho možnost provádět všechny operace IPC s výjimkou resetování hesla. |
  | Privileged Identity Management |<ul><li>Všechna oprávnění role čtenáře zabezpečení.<li>**Nelze** spravovat členství v rolích Azure AD nebo nastavení. |
  | <p>Monitorování stavu služby Office 365</p><p>Centrum zabezpečení a dodržování předpisů Office 365 |<ul><li>Všechna oprávnění role čtenáře zabezpečení.<li>Můžete nakonfigurovat všechna nastavení ve funkci rozšířené ochrany před internetovými útoky (ochrany proti malwaru a virů, škodlivý konfigurace adresy URL, adresa URL trasování atd.). |
  
* **[Čtenář zabezpečení](#security-reader)**: uživatelé s touto rolí mají globální přístup jen pro čtení, včetně všech informací v Azure Active Directory, Identity Protection, Privileged Identity Management, jakož i oprávnění ke čtení služby Azure Active Directory sestavy přihlášení a protokoly auditu. Role navíc uděluje oprávnění jen pro čtení v centru dodržování předpisů a zabezpečení Office 365. Další informace o oprávněních Office 365 je k dispozici na [oprávnění v centru dodržování předpisů a zabezpečení Office 365](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

  | V | Můžete provést |
  | --- | --- |
  | Centrum Identity Protection |Číst všechny zprávy o zabezpečení a informace o nastavení zabezpečení funkcí<ul><li>Antispamových<li>Šifrování<li>Prevence ztráty dat<li>Anti-malware<li>Pokročilou ochranu před hrozbami<li>Anti phisingová<li>Mailflow pravidla |
  | Privileged Identity Management |<p>Má přístup jen pro čtení ke všem informacím prezentované v Azure AD PIM: zásady a sestav pro přiřazení role Azure AD, bezpečnostní kontroly a v budoucnu přístup pro čtení k zásad dat a sestav pro scénáře kromě přiřazení role Azure AD.<p>**Nelze** registrace pro Azure AD PIM nebo dělat žádné změny. PIM na portálu nebo pomocí Powershellu někdo v této roli můžete aktivovat další role (například globální správce nebo správce privilegovaných rolí), pokud uživatel je kandidátem pro ně. |
  | <p>Monitorování stavu služby Office 365</p><p>Centrum zabezpečení a dodržování předpisů Office 365</p> |<ul><li>Čtení a Správa výstrah<li>Zásady zabezpečení pro čtení<li>Přečtěte si analýzy hrozeb, zjišťování cloudových aplikací a karantény v hledání a prošetřit<li>Čtení všech sestav |

* **[Správce podpory služeb](#service-support-administrator)**: uživatelé s touto rolí můžete otevřít žádosti o podporu u Microsoftu pro služby Azure a Office 365 a zobrazení řídicího panelu služby a message center webu Azure portal a portálu pro správu Office 365. Další informace na [role správců Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **[Správce služeb Sharepointu](#sharepoint-service-administrator)**: uživatelé s touto rolí mají globální oprávnění v Microsoft SharePoint Online, pokud služba používá, a možnost spravovat lístky podpory a monitorovat stav služby. Další informace na [role správců Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **[Skype pro firmy nebo správce služeb Lyncu](#lync-service-administrator)**: uživatelé s touto rolí mají globální oprávnění v rámci Microsoft Skype pro firmy, pokud služba používá, stejně jako správu Skype konkrétní atributy uživatele v Azure Active Adresář. Kromě toho tato role uděluje možnost spravovat lístky podpory a monitorovat stav služby a k přístupu do týmů a Skype pro firmy centra pro správu. Účet musí mít také licenci pro týmy, nebo se nedal spustit rutiny prostředí PowerShell týmy. Další informace na [o Skype pro firmy roli správce](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5) a týmy, které informace o licencích na [Skype pro firmy a Microsoft Teams doplněk licencování](https://docs.microsoft.com/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing)

  > [!NOTE]
  > V rozhraní Microsoft Graph API, Azure AD Graph API a Azure AD PowerShell tato role nazývá "Správce služeb Lyncu". Je "Skype pro firmy Správce služby" v [webu Azure portal](https://portal.azure.com/).
  >
  >

* **[Týmy, které správce komunikaci](#teams-communications-administrator)**: uživatelé v této roli mohou spravovat aspekty související s Hl & telefonního subsystému zatížení Microsoft Teams. To zahrnuje nástroje pro správu pro přiřazení telefonní čísla, zásady pro hlasové hovory a schůzky a úplný přístup k nástrojů analytics volání.

* **[Týmy, které pracovník podpory komunikace](#teams-communications-support-engineer)**: uživatelé v této roli můžete řešit problémy s komunikací v Microsoft Teams a Skype pro volání nástroje pro odstraňování potíží v Microsoft Teams a Skype pro firmy pomocí uživatele Centrum pro správu firmy. Uživatelé v této roli můžete zobrazit informace o záznamu úplné volání pro všechny účastníky zahrnuté.

* **[Týmy, které odborné komunikace](#teams-communications-support-specialist)**: uživatelé v této roli můžete řešit problémy s komunikací v Microsoft Teams a Skype pro volání nástroje pro odstraňování potíží v Microsoft Teams a Skype pro firmy pomocí uživatele Centrum pro správu firmy. Uživatelé v této roli můžete zobrazit podrobnosti o uživateli pouze ve volání pro konkrétního uživatele, že se že mají vyhledávat.

* **[Týmy, které správce služeb](#teams-service-administrator)**: uživatelé s touto rolí může spravovat všechny aspekty sady funkcí Microsoft Teams přes Microsoft Teams a Skype pro firmy centra pro správu a odpovídajících modulů prostředí PowerShell. To zahrnuje mimo jiné oblasti, všem nástrojům pro správu související s telefonního subsystému, zasílání zpráv, setkání a samotné týmy. Tato role také uděluje možnost Spravovat skupiny Office 365.

* **[Správce uživatelských účtů](#user-account-administrator)**: uživatelé s touto rolí můžete vytvářet a spravovat všechny aspekty uživatelů a skupin. Kromě toho tato role obsahuje možnost spravovat lístky podpory a monitorovat stav služby. Platí určitá omezení. Tato role například neumožňuje odstranit globálního správce. Správci uživatelského účtu můžete změnit heslo a zneplatnit obnovovací tokeny pro uživatele, správců technické podpory a jiných správců uživatelských účtů. Zrušení platnosti tokenu obnovení vynutí uživatel znovu přihlásil.

| Můžete provést | Nelze provést. |
| --- | --- |
| <p>Zobrazit firemní a uživatelská informace</p><p>Spravovat lístky podpory Office</p><p>Změna hesla pro uživatele, správců technické podpory a jiných správců uživatelských účtů</p><p>Vytvořit a spravovat zobrazení uživatelů</p><p>Vytvořit, upravit a odstraňovat uživatele a skupiny a spravovat uživatelské licence s omezeními. Uživatel nemůže odstranit globálního správce nebo vytvářet jiné správce.</p> |<p>Provádění operací nákupu a fakturace pro produkty Office</p><p>Spravovat domény</p><p>Správa informací společnosti</p><p>Delegovat role správců jiným uživatelům</p><p>Používat synchronizaci adresářů</p><p>Povolení nebo zakázání služby Multi-Factor authentication</p><p>Zobrazení protokolů auditu</p> |

## <a name="deprecated-roles"></a>Nepoužívané role

Tyto role se nesmí používat. Že byl zastaralý a z Azure AD v budoucnu odeberou.

* Správce ad hoc licencí
* Připojení zařízení
* Správci zařízení
* Uživatelé zařízení
* Tvůrce uživatelů ověřovaných na základě e-mailu
* Správce poštovních schránek
* Připojení pracovních zařízení

## <a name="detailed-azure-active-directory-permissions"></a>Podrobné oprávnění služby Azure Active Directory
Následující tabulky popisují konkrétní oprávnění v Azure Active Directory na každou roli. Některé role, jako je například globální správce, může mít další oprávnění v outide služby Microsoft Azure Active Directory.


### <a name="adhoc-license-administrator"></a>Správce ad hoc licencí
Může vytvářet a spravovat všechny aspekty registrací aplikací a podnikových aplikací.

  > [!NOTE]
  > Tato role dědí z role ke čtení adresáře další oprávnění.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| Microsoft.aad.Directory/Domains/default/Read | Přečtěte si základní vlastnosti Domains v Azure Active Directory. |
| microsoft.aad.directory/groups/appRoleAssignments/read | Umožňuje číst vlastnost groups.appRoleAssignments v Azure Active Directory. |
| Microsoft.aad.Directory/groups/default/Read | Přečtěte si základní vlastnosti skupin v Azure Active Directory. |
| microsoft.aad.directory/groups/memberOf/read | Umožňuje číst vlastnost groups.memberOf v Azure Active Directory. |
| Microsoft.aad.Directory/groups/Members/Read | Umožňuje číst vlastnost groups.members v Azure Active Directory. |
| Microsoft.aad.Directory/groups/Owners/Read | Umožňuje číst vlastnost groups.owners v Azure Active Directory. |
| Microsoft.aad.Directory/groups/Settings/Read | Umožňuje číst vlastnost groups.settings v Azure Active Directory. |
| microsoft.aad.directory/oAuth2PermissionGrants/default/read | Přečtěte si základní vlastnosti v oAuth2PermissionGrants v Azure Active Directory. |
| microsoft.aad.directory/oAuth2PermissionGrants/update | Aktualizujte oAuth2PermissionGrants v Azure Active Directory. |
| Microsoft.aad.Directory/Organization/default/Read | Přečtěte si základní vlastnosti v organizaci v Azure Active Directory. |
| microsoft.aad.directory/organization/trustedCAsForPasswordlessAuth/read | Umožňuje číst vlastnost organization.trustedCAsForPasswordlessAuth ve službě Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Správa licencí pro uživatele v Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/read | Umožňuje číst vlastnost users.appRoleAssignments v Azure Active Directory. |
| Microsoft.aad.Directory/Users/Default/Read | Přečtěte si základní vlastnosti pro uživatele v Azure Active Directory. |
| microsoft.aad.directory/users/directReports/read | Umožňuje číst vlastnost users.directReports v Azure Active Directory. |
| microsoft.aad.directory/users/invitedBy/read | Umožňuje číst vlastnost users.invitedBy v Azure Active Directory. |
| microsoft.aad.directory/users/invitedUsers/read | Umožňuje číst vlastnost users.invitedUsers v Azure Active Directory. |
| Microsoft.aad.Directory/Users/Manager/Read | Umožňuje číst vlastnost users.manager v Azure Active Directory. |
| microsoft.aad.directory/users/memberOf/read | Umožňuje číst vlastnost users.memberOf v Azure Active Directory. |
| microsoft.aad.directory/users/oAuth2PermissionGrants/default/read | Umožňuje číst vlastnost users.oAuth2PermissionGrants v Azure Active Directory. |
| microsoft.aad.directory/users/ownedDevices/read | Umožňuje číst vlastnost users.ownedDevices v Azure Active Directory. |
| microsoft.aad.directory/users/ownedObjects/read | Umožňuje číst vlastnost users.ownedObjects v Azure Active Directory. |
| microsoft.aad.directory/users/registeredDevices/read | Umožňuje číst vlastnost users.registeredDevices v Azure Active Directory. |

### <a name="application-administrator"></a>Správce aplikace
Může vytvářet a spravovat všechny aspekty registrací aplikací a podnikových aplikací.

  > [!NOTE]
  > Tato role dědí z role ke čtení adresáře další oprávnění.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| Microsoft.aad.Directory/Applications/audience/Update | Umožňuje aktualizujte vlastnost applications.audience ve službě Azure Active Directory. |
| Microsoft.aad.Directory/Applications/Authentication/Update | Umožňuje aktualizujte vlastnost applications.authentication ve službě Azure Active Directory. |
| Microsoft.aad.Directory/Applications/default/Update | Aktualizace základní vlastnosti na aplikace v Azure Active Directory. |
| Microsoft.aad.Directory/Applications/Create | Vytváření aplikací v Azure Active Directory. |
| Microsoft.aad.Directory/Applications/credentials/Update | Umožňuje aktualizujte vlastnost applications.credentials ve službě Azure Active Directory. |
| Microsoft.aad.Directory/Applications/DELETE | Umožňuje odstraňte applications v Azure Active Directory. |
| Microsoft.aad.Directory/Applications/Owners/Update | Umožňuje aktualizujte vlastnost applications.owners v Azure Active Directory. |
| Microsoft.aad.Directory/Applications/Permissions/Update | Umožňuje aktualizujte vlastnost applications.permissions ve službě Azure Active Directory. |
| Microsoft.aad.Directory/Applications/Policies/Update | Umožňuje aktualizujte vlastnost applications.policies ve službě Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/create | Umožňuje vytvořte appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/read | Přečtěte si appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/update | Aktualizujte appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/delete | Umožňuje odstraňte appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/default/read | Umožňuje číst vlastnost policies.applicationConfiguration ve službě Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/default/update | Umožňuje aktualizujte vlastnost policies.applicationConfiguration ve službě Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/create | Vytvoření zásad v Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/delete | Umožňuje odstraňte policies v Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/read | Umožňuje číst vlastnost policies.applicationConfiguration ve službě Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/update | Umožňuje aktualizujte vlastnost policies.applicationConfiguration ve službě Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/policyAppliedTo/read | Umožňuje číst vlastnost policies.applicationConfiguration ve službě Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/default/update | Aktualizace základní vlastnosti na servicePrincipals v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/create | Umožňuje vytvořte servicePrincipals v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/delete | Umožňuje odstraňte servicePrincipals v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Umožňuje aktualizujte vlastnost servicePrincipals.appRoleAssignedTo v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Umožňuje aktualizujte vlastnost servicePrincipals.appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/update | Umožňuje aktualizujte vlastnost servicePrincipals.owners v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Umožňuje aktualizujte vlastnost servicePrincipals.policies ve službě Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Správa licencí pro uživatele v Azure Active Directory. |
| microsoft.aad.reports/allEntities/read | Umožňuje číst sestavy Azure AD. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat služby Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvořit a spravovat lístky podpory Azure. |
| Microsoft.Office365.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat stav služby Office 365. |
| Microsoft.Office365.supportTickets/allEntities/allTasks | Umožňuje vytvářet a spravovat lístky podpory Office 365. |

### <a name="application-developer"></a>Vývojář aplikace
Může vytvářet registrace aplikací nezávisle na uživatelé můžou registrovat aplikace nastavení.

  > [!NOTE]
  > Tato role dědí z role ke čtení adresáře další oprávnění.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/applications/createAsOwner | Vytváření aplikací v Azure Active Directory. Autor se přidá jako první vlastník a vytvořený objekt počítat do kvóty 250 vytvořených objektů autora. |
| microsoft.aad.directory/appRoleAssignments/createAsOwner | Umožňuje vytvořte appRoleAssignments v Azure Active Directory. Autor se přidá jako první vlastník a vytvořený objekt počítat do kvóty 250 vytvořených objektů autora. |
| microsoft.aad.directory/oAuth2PermissionGrants/createAsOwner | Umožňuje vytvořte oAuth2PermissionGrants v Azure Active Directory. Autor se přidá jako první vlastník a vytvořený objekt počítat do kvóty 250 vytvořených objektů autora. |
| microsoft.aad.directory/servicePrincipals/createAsOwner | Umožňuje vytvořte servicePrincipals v Azure Active Directory. Autor se přidá jako první vlastník a vytvořený objekt počítat do kvóty 250 vytvořených objektů autora. |

### <a name="billing-administrator"></a>Správce fakturace
Může provádět běžné úkoly související s fakturací, třeba aktualizovat platební údaje.

  > [!NOTE]
  > Tato role dědí z role ke čtení adresáře další oprávnění.
  >
  >

  > [!NOTE]
  > Tato role má oprávnění addditonal mimo službu Azure Active Directory. Zobrazit popis role výše pro další informace.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| Microsoft.aad.Directory/Organization/default/Update | Aktualizujte základní vlastnosti v organizaci v Azure Active Directory. |
| microsoft.aad.directory/organization/trustedCAsForPasswordlessAuth/update | Umožňuje aktualizujte vlastnost organization.trustedCAsForPasswordlessAuth ve službě Azure Active Directory. |
| microsoft.azure.accessService/allEntities/allTasks | Spravujte všechny aspekty služby Azure Access. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat služby Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvořit a spravovat lístky podpory Azure. |
| microsoft.commerce.billing/allEntities/allTasks | Umožňuje spravovat všechny aspekty fakturace Office 365. |
| Microsoft.Office365.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat stav služby Office 365. |
| Microsoft.Office365.supportTickets/allEntities/allTasks | Umožňuje vytvářet a spravovat lístky podpory Office 365. |

### <a name="cloud-application-administrator"></a>Správce cloudové aplikace
Může vytvářet a spravovat všechny aspekty registrací aplikací a podnikových aplikací, kromě Proxy aplikací.

  > [!NOTE]
  > Tato role dědí z role ke čtení adresáře další oprávnění.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| Microsoft.aad.Directory/Applications/audience/Update | Umožňuje aktualizujte vlastnost applications.audience ve službě Azure Active Directory. |
| Microsoft.aad.Directory/Applications/Authentication/Update | Umožňuje aktualizujte vlastnost applications.authentication ve službě Azure Active Directory. |
| Microsoft.aad.Directory/Applications/default/Update | Aktualizace základní vlastnosti na aplikace v Azure Active Directory. |
| Microsoft.aad.Directory/Applications/Create | Vytváření aplikací v Azure Active Directory. |
| Microsoft.aad.Directory/Applications/credentials/Update | Umožňuje aktualizujte vlastnost applications.credentials ve službě Azure Active Directory. |
| Microsoft.aad.Directory/Applications/DELETE | Umožňuje odstraňte applications v Azure Active Directory. |
| Microsoft.aad.Directory/Applications/Owners/Update | Umožňuje aktualizujte vlastnost applications.owners v Azure Active Directory. |
| Microsoft.aad.Directory/Applications/Permissions/Update | Umožňuje aktualizujte vlastnost applications.permissions ve službě Azure Active Directory. |
| Microsoft.aad.Directory/Applications/Policies/Update | Umožňuje aktualizujte vlastnost applications.policies ve službě Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/create | Umožňuje vytvořte appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/update | Aktualizujte appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/delete | Umožňuje odstraňte appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/create | Vytvoření zásad v Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/default/read | Umožňuje číst vlastnost policies.applicationConfiguration ve službě Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/default/update | Umožňuje aktualizujte vlastnost policies.applicationConfiguration ve službě Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/delete | Umožňuje odstraňte policies v Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/read | Umožňuje číst vlastnost policies.applicationConfiguration ve službě Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/update | Umožňuje aktualizujte vlastnost policies.applicationConfiguration ve službě Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/policyAppliedTo/read | Umožňuje číst vlastnost policies.applicationConfiguration ve službě Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Umožňuje aktualizujte vlastnost servicePrincipals.appRoleAssignedTo v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Umožňuje aktualizujte vlastnost servicePrincipals.appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/default/update | Aktualizace základní vlastnosti na servicePrincipals v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/create | Umožňuje vytvořte servicePrincipals v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/delete | Umožňuje odstraňte servicePrincipals v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/update | Umožňuje aktualizujte vlastnost servicePrincipals.owners v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Umožňuje aktualizujte vlastnost servicePrincipals.policies ve službě Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Správa licencí pro uživatele v Azure Active Directory. |
| microsoft.aad.reports/allEntities/read | Umožňuje číst sestavy Azure AD. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat služby Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvořit a spravovat lístky podpory Azure. |
| Microsoft.Office365.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat stav služby Office 365. |
| Microsoft.Office365.supportTickets/allEntities/allTasks | Umožňuje vytvářet a spravovat lístky podpory Office 365. |

### <a name="cloud-device-administrator"></a>Správce cloudových zařízení
Úplný přístup ke správě zařízení v Azure AD

  > [!NOTE]
  > Tato role dědí z role ke čtení adresáře další oprávnění.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| Microsoft.aad.Directory/Devices/DELETE | Umožňuje odstraňte devices v Azure Active Directory. |
| Microsoft.aad.Directory/Devices/disable | Zakážete devices v Azure Active Directory. |
| Microsoft.aad.Directory/Devices/enable | Povolte zařízení v Azure Active Directory. |
| microsoft.aad.reports/allEntities/read | Umožňuje číst sestavy Azure AD. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat služby Azure Service Health. |
| Microsoft.Office365.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat stav služby Office 365. |

### <a name="company-administrator"></a>Správce společnosti
Může spravovat všechny aspekty služeb Azure AD a Microsoft, které používají identity Azure AD.

  > [!NOTE]
  > Tato role dědí další oprávnění z role.
  >
  >

  > [!NOTE]
  > Tato role má oprávnění addditonal mimo službu Azure Active Directory. Zobrazit popis role výše pro další informace.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/administrativeUnits/allProperties/allTasks | Vytvářet a odstraňovat administrativeUnits a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/applications/allProperties/allTasks | Vytvářet a odstraňovat applications a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/allProperties/allTasks | Vytvářet a odstraňovat appRoleAssignments a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/contacts/allProperties/allTasks | Vytvářet a odstraňovat contacts a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/contracts/allProperties/allTasks | Vytvoření a odstraňovat contracts a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/devices/allProperties/allTasks | Vytvořit a odstraňovat devices a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/allProperties/allTasks | Vytvářet a odstraňovat directoryRoles a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/directoryRoleTemplates/allProperties/allTasks | Vytvářet a odstraňovat directoryRoleTemplates a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/domains/allProperties/allTasks | Vytvářet a odstraňovat domains a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/groups/allProperties/allTasks | Vytvářet a odstraňovat groups a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/groupSettings/allProperties/allTasks | Vytvářet a odstraňovat groupSettings a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/groupSettingTemplates/allProperties/allTasks | Vytvářet a odstraňovat groupSettingTemplates a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/loginTenantBranding/allProperties/allTasks | Vytvářet a odstraňovat loginTenantBranding a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/oAuth2PermissionGrants/allProperties/allTasks | Vytvářet a odstraňovat oAuth2PermissionGrants a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/organization/allProperties/allTasks | Vytvářet a odstraňovat organizace a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/policies/allProperties/allTasks | Vytvářet a odstraňovat policies a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/roleAssignments/allProperties/allTasks | Vytvářet a odstraňovat roleAssignments a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/roleDefinitions/allProperties/allTasks | Vytvářet a odstraňovat roleDefinitions a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/scopedRoleMemberships/allProperties/allTasks | Vytvářet a odstraňovat scopedRoleMemberships a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/serviceAction/activateService | Můžete provádět akce Activateservice služby v Azure Active Directory |
| microsoft.aad.directory/serviceAction/disableDirectoryFeature | Můžete provádět akce Disabledirectoryfeature služby v Azure Active Directory |
| microsoft.aad.directory/serviceAction/enableDirectoryFeature | Můžete provádět akce Enabledirectoryfeature služby v Azure Active Directory |
| microsoft.aad.directory/serviceAction/getAvailableExtentionProperties | Můžete provádět akce Getavailableextentionproperties služby v Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/allProperties/allTasks | Vytvářet a odstraňovat servicePrincipals a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/subscribedSkus/allProperties/allTasks | Vytvářet a odstraňovat subscribedSkus a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/users/allProperties/allTasks | Vytvoření a odstraňovat users a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directorySync/allEntities/allTasks | Umožňuje provádět všechny akce ve službě Azure AD Connect. |
| microsoft.aad.identityProtection/allEntities/allTasks | Vytvářet a odstraňovat všechny prostředky a číst a aktualizovat standardní vlastnosti v microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Číst všechny prostředky v microsoft.aad.privilegedIdentityManagement. |
| microsoft.aad.reports/allEntities/allTasks | Umožňuje číst a konfigurovat sestavy Azure AD. |
| microsoft.azure.accessService/allEntities/allTasks | Spravujte všechny aspekty služby Azure Access. |
| microsoft.azure.informationProtection/allEntities/allTasks | Spravujte všechny aspekty služby Azure Information Protection. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat služby Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvořit a spravovat lístky podpory Azure. |
| microsoft.commerce.billing/allEntities/allTasks | Umožňuje spravovat všechny aspekty fakturace Office 365. |
| microsoft.intune/allEntities/allTasks | Umožňuje spravovat všechny aspekty Intune. |
| Microsoft.Office365.complianceManager/allEntities/allTasks | Spravovat všechny aspekty správce dodržování předpisů Office 365 |
| Microsoft.Office365.Exchange/allEntities/allTasks | Umožňuje spravovat všechny aspekty Exchange Online. |
| Microsoft.Office365.lockbox/allEntities/allTasks | Spravovat všechny aspekty Lockboxu zákazníků Office 365 |
| microsoft.powerApps.powerBI/allEntities/allTasks | Umožňuje spravovat všechny aspekty Power BI. |
| Microsoft.Office365.protectionCenter/allEntities/allTasks | Spravujte všechny aspekty Centra ochrany Office 365. |
| Microsoft.Office365.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat stav služby Office 365. |
| Microsoft.Office365.SharePoint/allEntities/allTasks | Vytvářet a odstraňovat všechny prostředky a číst a aktualizovat standardní vlastnosti v microsoft.office365.sharepoint. |
| Microsoft.Office365.skypeForBusiness/allEntities/allTasks | Spravovat všechny aspekty skypu for Business Online. |
| Microsoft.Office365.supportTickets/allEntities/allTasks | Umožňuje vytvářet a spravovat lístky podpory Office 365. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Umožňuje spravovat všechny aspekty Dynamics 365. |

### <a name="compliance-administrator"></a>Správce dodržování předpisů
Může číst a spravovat konfiguraci dodržování předpisů a sestav v Azure AD a Office 365.

  > [!NOTE]
  > Tato role dědí z role ke čtení adresáře další oprávnění.
  >
  >

  > [!NOTE]
  > Tato role má oprávnění addditonal mimo službu Azure Active Directory. Zobrazit popis role výše pro další informace.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Spravujte všechny aspekty služby Azure Access. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat služby Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvořit a spravovat lístky podpory Azure. |
| Microsoft.Office365.complianceManager/allEntities/allTasks | Spravovat všechny aspekty správce dodržování předpisů Office 365 |
| Microsoft.Office365.Exchange/allEntities/allTasks | Umožňuje spravovat všechny aspekty Exchange Online. |
| Microsoft.Office365.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat stav služby Office 365. |
| Microsoft.Office365.SharePoint/allEntities/allTasks | Vytvářet a odstraňovat všechny prostředky a číst a aktualizovat standardní vlastnosti v microsoft.office365.sharepoint. |
| Microsoft.Office365.skypeForBusiness/allEntities/allTasks | Spravovat všechny aspekty skypu for Business Online. |
| Microsoft.Office365.supportTickets/allEntities/allTasks | Umožňuje vytvářet a spravovat lístky podpory Office 365. |

### <a name="conditional-access-administrator"></a>Správce podmíněného přístupu
Může spravovat funkce podmíněného přístupu.

  > [!NOTE]
  > Tato role dědí z role ke čtení adresáře další oprávnění.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/policies/conditionalAccess/default/read | Umožňuje číst vlastnost policies.conditionalAccess ve službě Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/default/update | Umožňuje aktualizujte vlastnost policies.conditionalAccess ve službě Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/create | Vytvoření zásad v Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/delete | Umožňuje odstraňte policies v Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/owners/read | Umožňuje číst vlastnost policies.conditionalAccess ve službě Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/owners/update | Umožňuje aktualizujte vlastnost policies.conditionalAccess ve službě Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/policiesAppliedTo/read | Umožňuje číst vlastnost policies.conditionalAccess ve službě Azure Active Directory. |

### <a name="crm-service-administrator"></a>Správce služby CRM
Může spravovat všechny aspekty produktu Dynamics 365.

  > [!NOTE]
  > Tato role dědí z role ke čtení adresáře další oprávnění.
  >
  >

  > [!NOTE]
  > Tato role má oprávnění addditonal mimo službu Azure Active Directory. Zobrazit popis role výše pro další informace.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Spravujte všechny aspekty služby Azure Access. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat služby Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvořit a spravovat lístky podpory Azure. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Umožňuje spravovat všechny aspekty Dynamics 365. |
| Microsoft.Office365.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat stav služby Office 365. |
| Microsoft.Office365.supportTickets/allEntities/allTasks | Umožňuje vytvářet a spravovat lístky podpory Office 365. |

### <a name="customer-lockbox-access-approver"></a>Schvalovatel přístupu ke Customer LockBoxu
Může schvalovat žádosti podpory Microsoftu o přístup k datům organizace zákazníka.

  > [!NOTE]
  > Tato role dědí z role ke čtení adresáře další oprávnění.
  >
  >

  > [!NOTE]
  > Tato role má oprávnění addditonal mimo službu Azure Active Directory. Zobrazit popis role výše pro další informace.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Spravujte všechny aspekty služby Azure Access. |
| Microsoft.Office365.lockbox/allEntities/allTasks | Spravovat všechny aspekty Lockboxu zákazníků Office 365 |

### <a name="device-administrators"></a>Správci zařízení
Členové této role se přidají do skupiny místních správců na Azure zařízení připojených k doméně AD.

  > [!NOTE]
  > Tato role dědí z role ke čtení adresáře další oprávnění.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/groupSettings/default/read | Přečtěte si základní vlastnosti na groupSettings ve službě Azure Active Directory. |
| microsoft.aad.directory/groupSettingTemplates/default/read | Přečtěte si základní vlastnosti na groupSettingTemplates ve službě Azure Active Directory. |

### <a name="device-managers"></a>Správci zařízení
Může schvalovat žádosti podpory Microsoftu o přístup k datům organizace zákazníka.

  > [!NOTE]
  > Tato role dědí z role ke čtení adresáře další oprávnění.
  >
  >

  > [!NOTE]
  > Tato role má oprávnění addditonal mimo službu Azure Active Directory. Zobrazit popis role výše pro další informace.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| Microsoft.aad.Directory/Devices/default/Read | Přečtěte si základní vlastnosti v devices v Azure Active Directory. |
| Microsoft.aad.Directory/Devices/default/Update | Aktualizace základní vlastnosti v devices v Azure Active Directory. |
| microsoft.aad.directory/devices/memberOf/read | Umožňuje číst vlastnost devices.memberOf v Azure Active Directory. |
| microsoft.aad.directory/devices/registeredOwners/read | Umožňuje číst vlastnost devices.registeredOwners v Azure Active Directory. |
| microsoft.aad.directory/devices/registeredOwners/update | Umožňuje aktualizujte vlastnost devices.registeredOwners v Azure Active Directory. |
| microsoft.aad.directory/devices/registeredUsers/read | Umožňuje číst vlastnost devices.registeredUsers v Azure Active Directory. |
| microsoft.aad.directory/devices/registeredUsers/update | Umožňuje aktualizujte vlastnost devices.registeredUsers v Azure Active Directory. |

### <a name="directory-readers"></a>Uživatelé s oprávněním ke čtení adresářů
Může číst informace o základní adresář. Pro udělení přístupu k aplikacím

  > [!NOTE]
  > Tato role dědí další oprávnění z role.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/administrativeUnits/default/read | Přečtěte si základní vlastnosti administrativeunits v Azure Active Directory. |
| microsoft.aad.directory/administrativeUnits/members/read | Umožňuje číst vlastnost administrativeUnits.members v Azure Active Directory. |
Azure Active Directory. |
| Microsoft.aad.Directory/Applications/default/Read | Přečtěte si základní vlastnosti na aplikace v Azure Active Directory. |
| Microsoft.aad.Directory/Applications/Owners/Read | Umožňuje číst vlastnost applications.owners v Azure Active Directory. |
| Microsoft.aad.Directory/Contacts/default/Read | Přečtěte si základní vlastnosti v contacts v Azure Active Directory. |
| microsoft.aad.directory/contacts/memberOf/read | Umožňuje číst vlastnost contacts.memberOf v Azure Active Directory. |
| Microsoft.aad.Directory/Contracts/default/Read | Přečtěte si základní vlastnosti o smlouvách ve službě Azure Active Directory. |
| Microsoft.aad.Directory/Devices/default/Read | Přečtěte si základní vlastnosti v devices v Azure Active Directory. |
| microsoft.aad.directory/devices/memberOf/read | Umožňuje číst vlastnost devices.memberOf v Azure Active Directory. |
| microsoft.aad.directory/devices/registeredOwners/read | Umožňuje číst vlastnost devices.registeredOwners v Azure Active Directory. |
| microsoft.aad.directory/devices/registeredUsers/read | Umožňuje číst vlastnost devices.registeredUsers v Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/default/read | Přečtěte si základní vlastnosti na directoryRoles ve službě Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/eligibleMembers/read | Umožňuje číst vlastnost directoryRoles.eligibleMembers v Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/members/read | Umožňuje číst vlastnost directoryRoles.members v Azure Active Directory. |
| Microsoft.aad.Directory/Domains/default/Read | Přečtěte si základní vlastnosti Domains v Azure Active Directory. |
| microsoft.aad.directory/groups/appRoleAssignments/read | Umožňuje číst vlastnost groups.appRoleAssignments v Azure Active Directory. |
| Microsoft.aad.Directory/groups/default/Read | Přečtěte si základní vlastnosti skupin v Azure Active Directory. |
| microsoft.aad.directory/groups/memberOf/read | Umožňuje číst vlastnost groups.memberOf v Azure Active Directory. |
| Microsoft.aad.Directory/groups/Members/Read | Umožňuje číst vlastnost groups.members v Azure Active Directory. |
| Microsoft.aad.Directory/groups/Owners/Read | Umožňuje číst vlastnost groups.owners v Azure Active Directory. |
| Microsoft.aad.Directory/groups/Settings/Read | Umožňuje číst vlastnost groups.settings v Azure Active Directory. |
| microsoft.aad.directory/groupSettings/default/read | Přečtěte si základní vlastnosti na groupSettings ve službě Azure Active Directory. |
| microsoft.aad.directory/groupSettingTemplates/default/read | Přečtěte si základní vlastnosti na groupSettingTemplates ve službě Azure Active Directory. |
| microsoft.aad.directory/oAuth2PermissionGrants/default/read | Přečtěte si základní vlastnosti v oAuth2PermissionGrants v Azure Active Directory. |
| Microsoft.aad.Directory/Organization/default/Read | Přečtěte si základní vlastnosti v organizaci v Azure Active Directory. |
| microsoft.aad.directory/organization/trustedCAsForPasswordlessAuth/read | Umožňuje číst vlastnost organization.trustedCAsForPasswordlessAuth ve službě Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/read | Umožňuje číst vlastnost servicePrincipals.appRoleAssignedTo v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/read | Umožňuje číst vlastnost servicePrincipals.appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/default/read | Přečtěte si základní vlastnosti na servicePrincipals v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/memberOf/read | Umožňuje číst vlastnost servicePrincipals.memberOf v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/default/read | Umožňuje číst vlastnost servicePrincipals.oAuth2PermissionGrants v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/ownedObjects/read | Umožňuje číst vlastnost servicePrincipals.ownedObjects v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/read | Umožňuje číst vlastnost servicePrincipals.owners v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/read | Umožňuje číst vlastnost servicePrincipals.policies ve službě Azure Active Directory. |
| microsoft.aad.directory/subscribedSkus/default/read | Přečtěte si základní vlastnosti na subscribedSkus ve službě Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/read | Umožňuje číst vlastnost users.appRoleAssignments v Azure Active Directory. |
| Microsoft.aad.Directory/Users/Default/Read | Přečtěte si základní vlastnosti pro uživatele v Azure Active Directory. |
| microsoft.aad.directory/users/directReports/read | Umožňuje číst vlastnost users.directReports v Azure Active Directory. |
| microsoft.aad.directory/users/invitedBy/read | Umožňuje číst vlastnost users.invitedBy v Azure Active Directory. |
| microsoft.aad.directory/users/invitedUsers/read | Umožňuje číst vlastnost users.invitedUsers v Azure Active Directory. |
| Microsoft.aad.Directory/Users/Manager/Read | Umožňuje číst vlastnost users.manager v Azure Active Directory. |
| microsoft.aad.directory/users/memberOf/read | Umožňuje číst vlastnost users.memberOf v Azure Active Directory. |
| microsoft.aad.directory/users/oAuth2PermissionGrants/default/read | Umožňuje číst vlastnost users.oAuth2PermissionGrants v Azure Active Directory. |
| microsoft.aad.directory/users/ownedDevices/read | Umožňuje číst vlastnost users.ownedDevices v Azure Active Directory. |
| microsoft.aad.directory/users/ownedObjects/read | Umožňuje číst vlastnost users.ownedObjects v Azure Active Directory. |
| microsoft.aad.directory/users/registeredDevices/read | Umožňuje číst vlastnost users.registeredDevices v Azure Active Directory. |

### <a name="directory-synchronization-accounts"></a>Účty pro synchronizaci adresáře
Pouze používá služba Azure AD Connect.

  > [!NOTE]
  > Tato role dědí další oprávnění z role.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/organization/dirSync/update | Umožňuje aktualizujte vlastnost organization.dirSync ve službě Azure Active Directory. |
| Microsoft.aad.Directory/Policies/Create | Vytvoření zásad v Azure Active Directory. |
| Microsoft.aad.Directory/Policies/DELETE | Umožňuje odstraňte policies v Azure Active Directory. |
| Microsoft.aad.Directory/Policies/default/Read | Přečtěte si základní vlastnosti v policies v Azure Active Directory. |
| Microsoft.aad.Directory/Policies/default/Update | Aktualizace základní vlastnosti v policies v Azure Active Directory. |
| Microsoft.aad.Directory/Policies/Owners/Read | Umožňuje číst vlastnost policies.owners v Azure Active Directory. |
| Microsoft.aad.Directory/Policies/Owners/Update | Umožňuje aktualizujte vlastnost policies.owners v Azure Active Directory. |
| microsoft.aad.directory/policies/policiesAppliedTo/read | Umožňuje číst vlastnost policies.policiesAppliedTo ve službě Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/read | Umožňuje číst vlastnost servicePrincipals.appRoleAssignedTo v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Umožňuje aktualizujte vlastnost servicePrincipals.appRoleAssignedTo v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/read | Umožňuje číst vlastnost servicePrincipals.appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Umožňuje aktualizujte vlastnost servicePrincipals.appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/default/read | Přečtěte si základní vlastnosti na servicePrincipals v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/default/update | Aktualizace základní vlastnosti na servicePrincipals v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/create | Umožňuje vytvořte servicePrincipals v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/memberOf/read | Umožňuje číst vlastnost servicePrincipals.memberOf v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/default/read | Umožňuje číst vlastnost servicePrincipals.oAuth2PermissionGrants v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/read | Umožňuje číst vlastnost servicePrincipals.owners v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/update | Umožňuje aktualizujte vlastnost servicePrincipals.owners v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/ownedObjects/read | Umožňuje číst vlastnost servicePrincipals.ownedObjects v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/read | Umožňuje číst vlastnost servicePrincipals.policies ve službě Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Umožňuje aktualizujte vlastnost servicePrincipals.policies ve službě Azure Active Directory. |
| microsoft.aad.directorySync/allEntities/allTasks | Umožňuje provádět všechny akce ve službě Azure AD Connect. |

### <a name="directory-writers"></a>Uživatelé s oprávněním k zápisu do adresářů
Může číst a zapisovat informace základní adresář. Pro udělení přístupu k aplikacím

  > [!NOTE]
  > Tato role dědí z role ke čtení adresáře další oprávnění.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| Microsoft.aad.Directory/groups/Create | Vytváření skupin v Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Vytváření skupin v Azure Active Directory. Autor se přidá jako první vlastník a vytvořený objekt počítat do kvóty 250 vytvořených objektů autora. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Umožňuje aktualizujte vlastnost groups.appRoleAssignments v Azure Active Directory. |
| Microsoft.aad.Directory/groups/default/Update | Aktualizace základní vlastnosti skupin v Azure Active Directory. |
| Microsoft.aad.Directory/groups/Members/Update | Umožňuje aktualizujte vlastnost groups.members v Azure Active Directory. |
| Microsoft.aad.Directory/groups/Owners/Update | Umožňuje aktualizujte vlastnost groups.owners v Azure Active Directory. |
| Microsoft.aad.Directory/groups/Settings/Update | Umožňuje aktualizujte vlastnost groups.settings v Azure Active Directory. |
| microsoft.aad.directory/groupSettings/default/update | Aktualizace základní vlastnosti na groupSettings ve službě Azure Active Directory. |
| microsoft.aad.directory/groupSettings/create | Vytvoření groupSettings ve službě Azure Active Directory. |
| microsoft.aad.directory/groupSettings/delete | Odstraňte groupSettings ve službě Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Umožňuje aktualizujte vlastnost users.appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Správa licencí pro uživatele v Azure Active Directory. |
| Microsoft.aad.Directory/Users/Default/Update | Aktualizace základní vlastnosti pro uživatele v Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Umožňuje zneplatnit všechny obnovovací tokeny uživatele v Azure Active Directory. |
| Microsoft.aad.Directory/Users/Manager/Update | Umožňuje aktualizujte vlastnost users.manager v Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Umožňuje aktualizujte vlastnost users.userPrincipalName ve službě Azure Active Directory. |

### <a name="exchange-service-administrator"></a>Správce služby Exchange
Může spravovat všechny aspekty produktu Exchange.

  > [!NOTE]
  > Tato role dědí z role ke čtení adresáře další oprávnění.
  >
  >

  > [!NOTE]
  > Tato role má oprávnění addditonal mimo službu Azure Active Directory. Zobrazit popis role výše pro další informace.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Spravujte všechny aspekty služby Azure Access. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat služby Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvořit a spravovat lístky podpory Azure. |
| Microsoft.Office365.Exchange/allEntities/allTasks | Umožňuje spravovat všechny aspekty Exchange Online. |
| Microsoft.Office365.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat stav služby Office 365. |
| Microsoft.Office365.supportTickets/allEntities/allTasks | Umožňuje vytvářet a spravovat lístky podpory Office 365. |

### <a name="guest"></a>Host
Výchozí role pro uživatele typu Host. Může číst informace o adresáři omezená sada.

  > [!NOTE]
  > Tato role dědí další oprávnění z role.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| Microsoft.aad.Directory/Applications/default/Read | Přečtěte si základní vlastnosti na aplikace v Azure Active Directory. |
| Microsoft.aad.Directory/Applications/Owners/Read | Umožňuje číst vlastnost applications.owners v Azure Active Directory. |
| Microsoft.aad.Directory/Domains/default/Read | Přečtěte si základní vlastnosti Domains v Azure Active Directory. |
| microsoft.aad.directory/groups/appRoleAssignments/read | Umožňuje číst vlastnost groups.appRoleAssignments v Azure Active Directory. |
| Microsoft.aad.Directory/groups/default/Read | Přečtěte si základní vlastnosti skupin v Azure Active Directory. |
| microsoft.aad.directory/groups/memberOf/read | Umožňuje číst vlastnost groups.memberOf v Azure Active Directory. |
| Microsoft.aad.Directory/groups/Members/Read | Umožňuje číst vlastnost groups.members v Azure Active Directory. |
| Microsoft.aad.Directory/groups/Owners/Read | Umožňuje číst vlastnost groups.owners v Azure Active Directory. |
| Microsoft.aad.Directory/groups/Settings/Read | Umožňuje číst vlastnost groups.settings v Azure Active Directory. |
| microsoft.aad.directory/organization/basicProfile/read | Umožňuje číst základní informace o profilu organizace v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/read | Umožňuje číst vlastnost servicePrincipals.appRoleAssignedTo v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/read | Umožňuje číst vlastnost servicePrincipals.appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/default/read | Přečtěte si základní vlastnosti na servicePrincipals v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/memberOf/read | Umožňuje číst vlastnost servicePrincipals.memberOf v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/members/read | Umožňuje číst vlastnost servicePrincipals.members ve službě Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/default/read | Umožňuje číst vlastnost servicePrincipals.oAuth2PermissionGrants v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/read | Umožňuje číst vlastnost servicePrincipals.owners v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/ownedObjects/read | Umožňuje číst vlastnost servicePrincipals.ownedObjects v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/read | Umožňuje číst vlastnost servicePrincipals.policies ve službě Azure Active Directory. |
| microsoft.aad.directory/users/basicProfile/read | Umožňuje číst vlastnost users.basicProfile v Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/read | Umožňuje číst vlastnost users.appRoleAssignments v Azure Active Directory. |
| Microsoft.aad.Directory/Users/Default/Read | Přečtěte si základní vlastnosti pro uživatele v Azure Active Directory. |
| microsoft.aad.directory/users/directReports/read | Umožňuje číst vlastnost users.directReports v Azure Active Directory. |
| microsoft.aad.directory/users/eligibleMemberOf/read | Umožňuje číst vlastnost users.eligibleMemberOf ve službě Azure Active Directory. |
| microsoft.aad.directory/users/invitedBy/read | Umožňuje číst vlastnost users.invitedBy v Azure Active Directory. |
| microsoft.aad.directory/users/invitedUsers/read | Umožňuje číst vlastnost users.invitedUsers v Azure Active Directory. |
| Microsoft.aad.Directory/Users/Manager/Read | Umožňuje číst vlastnost users.manager v Azure Active Directory. |
| microsoft.aad.directory/users/memberOf/read | Umožňuje číst vlastnost users.memberOf v Azure Active Directory. |
| microsoft.aad.directory/users/oAuth2PermissionGrants/default/read | Umožňuje číst vlastnost users.oAuth2PermissionGrants v Azure Active Directory. |
| microsoft.aad.directory/users/ownedDevices/read | Umožňuje číst vlastnost users.ownedDevices v Azure Active Directory. |
| microsoft.aad.directory/users/ownedObjects/read | Umožňuje číst vlastnost users.ownedObjects v Azure Active Directory. |
| Microsoft.aad.Directory/Users/Password/Update | Aktualizace hesla pro všechny uživatele ve službě Azure Active Directory. Najdete v online dokumentaci pro více podrobností. |
| microsoft.aad.directory/users/pendingMemberOf/read | Umožňuje číst vlastnost users.pendingMemberOf ve službě Azure Active Directory. |
| microsoft.aad.directory/users/registeredDevices/read | Umožňuje číst vlastnost users.registeredDevices v Azure Active Directory. |
| microsoft.aad.directory/users/scopedAdministratorOf/read | Umožňuje číst vlastnost users.scopedAdministratorOf ve službě Azure Active Directory. |

### <a name="guest-inviter"></a>Odesílatel pozvánky hostů
Můžete pozvání hosta nezávisle na členy zvát hosty nastavení.

  > [!NOTE]
  > Tato role dědí další oprávnění z role.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/users/appRoleAssignments/read | Umožňuje číst vlastnost users.appRoleAssignments v Azure Active Directory. |
| Microsoft.aad.Directory/Users/Default/Read | Přečtěte si základní vlastnosti pro uživatele v Azure Active Directory. |
| microsoft.aad.directory/users/directReports/read | Umožňuje číst vlastnost users.directReports v Azure Active Directory. |
| microsoft.aad.directory/users/invitedBy/read | Umožňuje číst vlastnost users.invitedBy v Azure Active Directory. |
| microsoft.aad.directory/users/inviteGuest | Umožňuje zvát hosty do Azure Active Directory. |
| microsoft.aad.directory/users/invitedUsers/read | Umožňuje číst vlastnost users.invitedUsers v Azure Active Directory. |
| Microsoft.aad.Directory/Users/Manager/Read | Umožňuje číst vlastnost users.manager v Azure Active Directory. |
| microsoft.aad.directory/users/memberOf/read | Umožňuje číst vlastnost users.memberOf v Azure Active Directory. |
| microsoft.aad.directory/users/oAuth2PermissionGrants/default/read | Umožňuje číst vlastnost users.oAuth2PermissionGrants v Azure Active Directory. |
| microsoft.aad.directory/users/ownedDevices/read | Umožňuje číst vlastnost users.ownedDevices v Azure Active Directory. |
| microsoft.aad.directory/users/ownedObjects/read | Umožňuje číst vlastnost users.ownedObjects v Azure Active Directory. |
| microsoft.aad.directory/users/registeredDevices/read | Umožňuje číst vlastnost users.registeredDevices v Azure Active Directory. |

### <a name="helpdesk-administrator"></a>Správce technické podpory
Může resetovat hesla uživatelů, kteří nejsou správci, a správců technické podpory.

  > [!NOTE]
  > Tato role dědí z role ke čtení adresáře další oprávnění.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Umožňuje zneplatnit všechny obnovovací tokeny uživatele v Azure Active Directory. |
| Microsoft.aad.Directory/Users/Password/Update | Aktualizace hesla pro všechny uživatele ve službě Azure Active Directory. Najdete v online dokumentaci pro více podrobností. |
| microsoft.azure.accessService/allEntities/allTasks | Spravujte všechny aspekty služby Azure Access. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat služby Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvořit a spravovat lístky podpory Azure. |
| Microsoft.Office365.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat stav služby Office 365. |
| Microsoft.Office365.supportTickets/allEntities/allTasks | Umožňuje vytvářet a spravovat lístky podpory Office 365. |

### <a name="information-protection-administrator"></a>Správce Information Protection
Může spravovat všechny aspekty produktu Azure Information Protection.

  > [!NOTE]
  > Tato role dědí z role ke čtení adresáře další oprávnění.
  >
  >

  > [!NOTE]
  > Tato role má oprávnění addditonal mimo službu Azure Active Directory. Zobrazit popis role výše pro další informace.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.azure.informationProtection/allEntities/allTasks | Spravujte všechny aspekty služby Azure Information Protection. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat služby Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvořit a spravovat lístky podpory Azure. |
| Microsoft.Office365.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat stav služby Office 365. |
| Microsoft.Office365.supportTickets/allEntities/allTasks | Umožňuje vytvářet a spravovat lístky podpory Office 365. |

### <a name="intune-service-administrator"></a>Správce služby Intune
Může spravovat všechny aspekty produktu Intune.

  > [!NOTE]
  > Tato role dědí z role ke čtení adresáře další oprávnění.
  >
  >

  > [!NOTE]
  > Tato role má oprávnění addditonal mimo službu Azure Active Directory. Zobrazit popis role výše pro další informace.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| Microsoft.aad.Directory/Contacts/default/Update | Aktualizace základní vlastnosti v contacts v Azure Active Directory. |
| Microsoft.aad.Directory/Contacts/Create | Umožňuje vytvořte contacts v Azure Active Directory. |
| Microsoft.aad.Directory/Contacts/DELETE | Odstraňte contacts v Azure Active Directory. |
| Microsoft.aad.Directory/Devices/default/Update | Aktualizace základní vlastnosti v devices v Azure Active Directory. |
| Microsoft.aad.Directory/Devices/Create | Umožňuje vytvořte devices v Azure Active Directory. |
| Microsoft.aad.Directory/Devices/DELETE | Umožňuje odstraňte devices v Azure Active Directory. |
| microsoft.aad.directory/devices/registeredOwners/update | Umožňuje aktualizujte vlastnost devices.registeredOwners v Azure Active Directory. |
| microsoft.aad.directory/devices/registeredUsers/update | Umožňuje aktualizujte vlastnost devices.registeredUsers v Azure Active Directory. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Umožňuje aktualizujte vlastnost groups.appRoleAssignments v Azure Active Directory. |
| Microsoft.aad.Directory/groups/default/Update | Aktualizace základní vlastnosti skupin v Azure Active Directory. |
| Microsoft.aad.Directory/groups/Create | Vytváření skupin v Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Vytváření skupin v Azure Active Directory. Autor se přidá jako první vlastník a vytvořený objekt počítat do kvóty 250 vytvořených objektů autora. |
| Microsoft.aad.Directory/groups/DELETE | Umožňuje odstraňte groups v Azure Active Directory. |
| microsoft.aad.directory/groups/hiddenMembers/read | Umožňuje číst vlastnost groups.hiddenMembers v Azure Active Directory. |
| Microsoft.aad.Directory/groups/Members/Update | Umožňuje aktualizujte vlastnost groups.members v Azure Active Directory. |
| Microsoft.aad.Directory/groups/Owners/Update | Umožňuje aktualizujte vlastnost groups.owners v Azure Active Directory. |
| Microsoft.aad.Directory/groups/Restore | Umožňuje obnovte groups v Azure Active Directory. |
| Microsoft.aad.Directory/groups/Settings/Update | Umožňuje aktualizujte vlastnost groups.settings v Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Umožňuje aktualizujte vlastnost users.appRoleAssignments v Azure Active Directory. |
| Microsoft.aad.Directory/Users/Default/Update | Aktualizace základní vlastnosti pro uživatele v Azure Active Directory. |
| Microsoft.aad.Directory/Users/Manager/Update | Umožňuje aktualizujte vlastnost users.manager v Azure Active Directory. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvořit a spravovat lístky podpory Azure. |
| microsoft.intune/allEntities/allTasks | Umožňuje spravovat všechny aspekty Intune. |
| Microsoft.Office365.supportTickets/allEntities/allTasks | Umožňuje vytvářet a spravovat lístky podpory Office 365. |

### <a name="license-administrator"></a>Správce licencí
Můžete spravovat licence na produkty na uživatele a skupiny.

  > [!NOTE]
  > Tato role dědí z role ke čtení adresáře další oprávnění.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/users/assignLicense | Správa licencí pro uživatele v Azure Active Directory. |
| microsoft.aad.directory/users/usageLocation/update | Umožňuje aktualizujte vlastnost users.usageLocation v Azure Active Directory. |
| microsoft.azure.accessService/allEntities/allTasks | Spravujte všechny aspekty služby Azure Access. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat služby Azure Service Health. |
| Microsoft.Office365.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat stav služby Office 365. |

### <a name="lync-service-administrator"></a>Správce služeb Lyncu
Může spravovat všechny aspekty produktu Skype pro firmy.

  > [!NOTE]
  > Tato role dědí z role ke čtení adresáře další oprávnění.
  >
  >

  > [!NOTE]
  > Tato role má oprávnění addditonal mimo službu Azure Active Directory. Zobrazit popis role výše pro další informace.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Spravujte všechny aspekty služby Azure Access. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat služby Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvořit a spravovat lístky podpory Azure. |
| Microsoft.Office365.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat stav služby Office 365. |
| Microsoft.Office365.skypeForBusiness/allEntities/allTasks | Spravovat všechny aspekty skypu for Business Online. |
| Microsoft.Office365.supportTickets/allEntities/allTasks | Umožňuje vytvářet a spravovat lístky podpory Office 365. |

### <a name="message-center-reader"></a>Čtenář Centra zpráv
Může číst zprávy a aktualizace pro svou organizaci jen v Centru zpráv Office 365. 

  > [!NOTE]
  > Tato role dědí z role ke čtení adresáře další oprávnění.
  >
  >

  > [!NOTE]
  > Tato role má oprávnění addditonal mimo službu Azure Active Directory. Zobrazit popis role výše pro další informace.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.accessmessagecenter/allEntities/allTasks | Umožňuje vytvářet a odstraňovat všechny prostředky a číst a aktualizovat standardní vlastnosti v Centru zpráv. |
| microsoft.azure.accessService/allEntities/allTasks | Spravujte všechny aspekty služby Azure Access. |

### <a name="partner-tier1-support"></a>Podpora partnerů úrovně 1
Nepoužívejte – nejsou určené pro obecné použití.

  > [!NOTE]
  > Tato role dědí z role ke čtení adresáře další oprávnění.
  >
  >

  > [!NOTE]
  > Tato role má oprávnění addditonal mimo službu Azure Active Directory. Zobrazit popis role výše pro další informace.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| Microsoft.aad.Directory/Contacts/default/Update | Aktualizace základní vlastnosti v contacts v Azure Active Directory. |
| Microsoft.aad.Directory/Contacts/Create | Umožňuje vytvořte contacts v Azure Active Directory. |
| Microsoft.aad.Directory/Contacts/DELETE | Odstraňte contacts v Azure Active Directory. |
| Microsoft.aad.Directory/groups/Create | Vytváření skupin v Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Vytváření skupin v Azure Active Directory. Autor se přidá jako první vlastník a vytvořený objekt počítat do kvóty 250 vytvořených objektů autora. |
| Microsoft.aad.Directory/groups/Members/Update | Umožňuje aktualizujte vlastnost groups.members v Azure Active Directory. |
| Microsoft.aad.Directory/groups/Owners/Update | Umožňuje aktualizujte vlastnost groups.owners v Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Umožňuje aktualizujte vlastnost users.appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Správa licencí pro uživatele v Azure Active Directory. |
| Microsoft.aad.Directory/Users/Default/Update | Aktualizace základní vlastnosti pro uživatele v Azure Active Directory. |
| Microsoft.aad.Directory/Users/DELETE | Umožňuje odstraňte users v Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Umožňuje zneplatnit všechny obnovovací tokeny uživatele v Azure Active Directory. |
| Microsoft.aad.Directory/Users/Manager/Update | Umožňuje aktualizujte vlastnost users.manager v Azure Active Directory. |
| Microsoft.aad.Directory/Users/Password/Update | Aktualizace hesla pro všechny uživatele ve službě Azure Active Directory. Najdete v online dokumentaci pro více podrobností. |
| Microsoft.aad.Directory/Users/Restore | Umožňuje obnovovat odstraněné uživatele v Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Umožňuje aktualizujte vlastnost users.userPrincipalName ve službě Azure Active Directory. |
| microsoft.azure.accessService/allEntities/allTasks | Spravujte všechny aspekty služby Azure Access. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat služby Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvořit a spravovat lístky podpory Azure. |
| Microsoft.Office365.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat stav služby Office 365. |
| Microsoft.Office365.supportTickets/allEntities/allTasks | Umožňuje vytvářet a spravovat lístky podpory Office 365. |

### <a name="partner-tier2-support"></a>Podpora partnerů úrovně 2
Nepoužívejte – nejsou určené pro obecné použití.

  > [!NOTE]
  > Tato role dědí z role ke čtení adresáře další oprávnění.
  >
  >

  > [!NOTE]
  > Tato role má oprávnění addditonal mimo službu Azure Active Directory. Zobrazit popis role výše pro další informace.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| Microsoft.aad.Directory/Contacts/default/Update | Aktualizace základní vlastnosti v contacts v Azure Active Directory. |
| Microsoft.aad.Directory/Contacts/Create | Umožňuje vytvořte contacts v Azure Active Directory. |
| Microsoft.aad.Directory/Contacts/DELETE | Odstraňte contacts v Azure Active Directory. |
| microsoft.aad.directory/domains/allTasks | Vytvářet a odstraňovat domains a číst a aktualizovat standardní vlastnosti v Azure Active Directory. |
| Microsoft.aad.Directory/groups/Create | Vytváření skupin v Azure Active Directory. |
| Microsoft.aad.Directory/groups/DELETE | Umožňuje odstraňte groups v Azure Active Directory. |
| Microsoft.aad.Directory/groups/Members/Update | Umožňuje aktualizujte vlastnost groups.members v Azure Active Directory. |
| Microsoft.aad.Directory/groups/Restore | Umožňuje obnovte groups v Azure Active Directory. |
| Microsoft.aad.Directory/Organization/default/Update | Aktualizujte základní vlastnosti v organizaci v Azure Active Directory. |
| microsoft.aad.directory/organization/trustedCAsForPasswordlessAuth/update | Umožňuje aktualizujte vlastnost organization.trustedCAsForPasswordlessAuth ve službě Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Umožňuje aktualizujte vlastnost users.appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Správa licencí pro uživatele v Azure Active Directory. |
| Microsoft.aad.Directory/Users/Default/Update | Aktualizace základní vlastnosti pro uživatele v Azure Active Directory. |
| Microsoft.aad.Directory/Users/DELETE | Umožňuje odstraňte users v Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Umožňuje zneplatnit všechny obnovovací tokeny uživatele v Azure Active Directory. |
| Microsoft.aad.Directory/Users/Manager/Update | Umožňuje aktualizujte vlastnost users.manager v Azure Active Directory. |
| Microsoft.aad.Directory/Users/Password/Update | Aktualizace hesla pro všechny uživatele ve službě Azure Active Directory. Najdete v online dokumentaci pro více podrobností. |
| Microsoft.aad.Directory/Users/Restore | Umožňuje obnovovat odstraněné uživatele v Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Umožňuje aktualizujte vlastnost users.userPrincipalName ve službě Azure Active Directory. |
| microsoft.azure.accessService/allEntities/allTasks | Spravujte všechny aspekty služby Azure Access. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat služby Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvořit a spravovat lístky podpory Azure. |
| Microsoft.Office365.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat stav služby Office 365. |
| Microsoft.Office365.supportTickets/allEntities/allTasks | Umožňuje vytvářet a spravovat lístky podpory Office 365. |

### <a name="power-bi-service-administrator"></a>Správce služeb Power BI
Může spravovat všechny aspekty produktu Power BI.

  > [!NOTE]
  > Tato role dědí z role ke čtení adresáře další oprávnění.
  >
  >

  > [!NOTE]
  > Tato role má oprávnění addditonal mimo službu Azure Active Directory. Zobrazit popis role výše pro další informace.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Spravujte všechny aspekty služby Azure Access. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat služby Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvořit a spravovat lístky podpory Azure. |
| microsoft.powerApps.powerBI/allEntities/allTasks | Umožňuje spravovat všechny aspekty Power BI. |
| Microsoft.Office365.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat stav služby Office 365. |
| Microsoft.Office365.supportTickets/allEntities/allTasks | Umožňuje vytvářet a spravovat lístky podpory Office 365. |

### <a name="privileged-role-administrator"></a>Správce privilegované role
Můžou Spravovat přiřazení rolí ve službě Azure AD

  > [!NOTE]
  > Tato role dědí z role ke čtení adresáře další oprávnění.
  >
  >

  > [!NOTE]
  > Tato role má oprávnění addditonal mimo službu Azure Active Directory. Zobrazit popis role výše pro další informace.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/directoryRoles/update | Aktualizujte directoryRoles ve službě Azure Active Directory. |
| microsoft.aad.privilegedIdentityManagement/allEntities/allTasks | Vytvářet a odstraňovat všechny prostředky a číst a aktualizovat standardní vlastnosti v microsoft.aad.privilegedIdentityManagement. |

### <a name="reports-reader"></a>Čtenář sestav
Může číst sestavy o přihlašování a auditech.

  > [!NOTE]
  > Tato role dědí z role ke čtení adresáře další oprávnění.
  >
  >

  > [!NOTE]
  > Tato role má oprávnění addditonal mimo službu Azure Active Directory. Zobrazit popis role výše pro další informace.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.reports/allEntities/read | Umožňuje číst sestavy Azure AD. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat služby Azure Service Health. |
| Microsoft.Office365.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat stav služby Office 365. |
| Microsoft.Office365.usageReports/allEntities/Read | Umožňuje číst sestavy využití Office 365. |

### <a name="security-administrator"></a>Správce zabezpečení
Může číst informace o zabezpečení a sestavy

  > [!NOTE]
  > Tato role dědí z role ke čtení adresáře další oprávnění.
  >
  >

  > [!NOTE]
  > Tato role má oprávnění addditonal mimo službu Azure Active Directory. Zobrazit popis role výše pro další informace.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| Microsoft.aad.Directory/Applications/Policies/Update | Umožňuje aktualizujte vlastnost applications.policies ve službě Azure Active Directory. |
| Microsoft.aad.Directory/Policies/default/Update | Aktualizace základní vlastnosti v policies v Azure Active Directory. |
| Microsoft.aad.Directory/Policies/Create | Vytvoření zásad v Azure Active Directory. |
| Microsoft.aad.Directory/Policies/DELETE | Umožňuje odstraňte policies v Azure Active Directory. |
| Microsoft.aad.Directory/Policies/Owners/Update | Umožňuje aktualizujte vlastnost policies.owners v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Umožňuje aktualizujte vlastnost servicePrincipals.policies ve službě Azure Active Directory. |
| microsoft.aad.identityProtection/allEntities/read | Číst všechny prostředky v microsoft.aad.identityProtection. |
| microsoft.aad.identityProtection/allEntities/update | Aktualizujte všechny prostředky v microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Číst všechny prostředky v microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.accessService/allEntities/allTasks | Spravujte všechny aspekty služby Azure Access. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat služby Azure Service Health. |
| Microsoft.Office365.protectionCenter/allEntities/Read | Umožňuje číst všechny aspekty Centra ochrany Office 365. |
| Microsoft.Office365.protectionCenter/allEntities/Update | Aktualizujte všechny prostředky v microsoft.office365.protectionCenter. |
| Microsoft.Office365.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat stav služby Office 365. |

### <a name="security-reader"></a>Čtecí zařízení pro zabezpečení
Může číst bezpečnostní údaje a sestavy v Azure AD a Office 365.

  > [!NOTE]
  > Tato role dědí z role ke čtení adresáře další oprávnění.
  >
  >

  > [!NOTE]
  > Tato role má oprávnění addditonal mimo službu Azure Active Directory. Zobrazit popis role výše pro další informace.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.identityProtection/allEntities/read | Číst všechny prostředky v microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Číst všechny prostředky v microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.accessService/allEntities/allTasks | Spravujte všechny aspekty služby Azure Access. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat služby Azure Service Health. |
| Microsoft.Office365.protectionCenter/allEntities/Read | Umožňuje číst všechny aspekty Centra ochrany Office 365. |
| Microsoft.Office365.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat stav služby Office 365. |

### <a name="service-support-administrator"></a>Správce podpory služeb
Může číst informace o stavu služby a spravovat lístky podpory.

  > [!NOTE]
  > Tato role dědí z role ke čtení adresáře další oprávnění.
  >
  >

  > [!NOTE]
  > Tato role má oprávnění addditonal mimo službu Azure Active Directory. Zobrazit popis role výše pro další informace.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Spravujte všechny aspekty služby Azure Access. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat služby Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvořit a spravovat lístky podpory Azure. |
| Microsoft.Office365.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat stav služby Office 365. |
| Microsoft.Office365.supportTickets/allEntities/allTasks | Umožňuje vytvářet a spravovat lístky podpory Office 365. |

### <a name="sharepoint-service-administrator"></a>Správce služby SharePoint
Může spravovat všechny aspekty služby SharePoint.

  > [!NOTE]
  > Tato role dědí z role ke čtení adresáře další oprávnění.
  >
  >

  > [!NOTE]
  > Tato role má oprávnění addditonal mimo službu Azure Active Directory. Zobrazit popis role výše pro další informace.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Spravujte všechny aspekty služby Azure Access. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat služby Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvořit a spravovat lístky podpory Azure. |
| Microsoft.Office365.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat stav služby Office 365. |
| Microsoft.Office365.SharePoint/allEntities/allTasks | Vytvářet a odstraňovat všechny prostředky a číst a aktualizovat standardní vlastnosti v microsoft.office365.sharepoint. |
| Microsoft.Office365.supportTickets/allEntities/allTasks | Umožňuje vytvářet a spravovat lístky podpory Office 365. |

### <a name="teams-communications-administrator"></a>Týmy komunikace správce
Můžete spravovat volání a schůzky funkcí ve službě Microsoft Teams.

  > [!NOTE]
  > Tato role dědí z role ke čtení adresáře další oprávnění.
  >
  >

  > [!NOTE]
  > Tato role má oprávnění addditonal mimo službu Azure Active Directory. Zobrazit popis role výše pro další informace.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| Microsoft.aad.Directory/Policies/Basic/Read | Přečtěte si základní vlastnosti v policies v Azure Active Directory. |
| microsoft.azure.accessService/allEntities/allTasks | Spravujte všechny aspekty služby Azure Access. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat služby Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvořit a spravovat lístky podpory Azure. |
| Microsoft.Office365.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat stav služby Office 365. |
| Microsoft.Office365.supportTickets/allEntities/allTasks | Umožňuje vytvářet a spravovat lístky podpory Office 365. |
| Microsoft.Office365.usageReports/allEntities/Read | Umožňuje číst sestavy využití Office 365. |

### <a name="teams-communications-support-engineer"></a>Pracovník podpory komunikaci týmů
Můžete řešit problémy komunikace v rámci týmů pomocí pokročilé nástroje.

  > [!NOTE]
  > Tato role dědí z role ke čtení adresáře další oprávnění.
  >
  >

  > [!NOTE]
  > Tato role má oprávnění addditonal mimo službu Azure Active Directory. Zobrazit popis role výše pro další informace.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| Microsoft.aad.Directory/Policies/Basic/Read | Přečtěte si základní vlastnosti v policies v Azure Active Directory. |
| microsoft.azure.accessService/allEntities/allTasks | Spravujte všechny aspekty služby Azure Access. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat služby Azure Service Health. |
| Microsoft.Office365.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat stav služby Office 365. |

### <a name="teams-communications-support-specialist"></a>Týmy komunikace odborné
Můžete řešit problémy komunikace v rámci týmy, které používají základní nástroje.

  > [!NOTE]
  > Tato role dědí z role ke čtení adresáře další oprávnění.
  >
  >

  > [!NOTE]
  > Tato role má oprávnění addditonal mimo službu Azure Active Directory. Zobrazit popis role výše pro další informace.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| Microsoft.aad.Directory/Policies/Basic/Read | Přečtěte si základní vlastnosti v policies v Azure Active Directory. |
| microsoft.azure.accessService/allEntities/allTasks | Spravujte všechny aspekty služby Azure Access. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat služby Azure Service Health. |
| Microsoft.Office365.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat stav služby Office 365. |

### <a name="teams-service-administrator"></a>Správce služby pro týmy
Může spravovat službu Microsoft Teams.

  > [!NOTE]
  > Tato role dědí z role ke čtení adresáře další oprávnění.
  >
  >

  > [!NOTE]
  > Tato role má oprávnění addditonal mimo službu Azure Active Directory. Zobrazit popis role výše pro další informace.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/groups/hiddenMembers/read | Umožňuje číst vlastnost groups.hiddenMembers v Azure Active Directory. |
| Microsoft.aad.Directory/Policies/Basic/Read | Přečtěte si základní vlastnosti v policies v Azure Active Directory. |
| microsoft.azure.accessService/allEntities/allTasks | Spravujte všechny aspekty služby Azure Access. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat služby Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvořit a spravovat lístky podpory Azure. |
| Microsoft.Office365.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat stav služby Office 365. |
| Microsoft.Office365.supportTickets/allEntities/allTasks | Umožňuje vytvářet a spravovat lístky podpory Office 365. |
| Microsoft.Office365.usageReports/allEntities/Read | Umožňuje číst sestavy využití Office 365. |

### <a name="user-account-administrator"></a>Správce uživatelských účtů
Může spravovat všechny aspekty uživatelů a skupin

  > [!NOTE]
  > Tato role dědí z role ke čtení adresáře další oprávnění.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/appRoleAssignments/create | Umožňuje vytvořte appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/delete | Umožňuje odstraňte appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/update | Aktualizujte appRoleAssignments v Azure Active Directory. |
| Microsoft.aad.Directory/Contacts/default/Update | Aktualizace základní vlastnosti v contacts v Azure Active Directory. |
| Microsoft.aad.Directory/Contacts/Create | Umožňuje vytvořte contacts v Azure Active Directory. |
| Microsoft.aad.Directory/Contacts/DELETE | Odstraňte contacts v Azure Active Directory. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Umožňuje aktualizujte vlastnost groups.appRoleAssignments v Azure Active Directory. |
| Microsoft.aad.Directory/groups/default/Update | Aktualizace základní vlastnosti skupin v Azure Active Directory. |
| Microsoft.aad.Directory/groups/Create | Vytváření skupin v Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Vytváření skupin v Azure Active Directory. Autor se přidá jako první vlastník a vytvořený objekt počítat do kvóty 250 vytvořených objektů autora. |
| Microsoft.aad.Directory/groups/DELETE | Umožňuje odstraňte groups v Azure Active Directory. |
| microsoft.aad.directory/groups/hiddenMembers/read | Umožňuje číst vlastnost groups.hiddenMembers v Azure Active Directory. |
| Microsoft.aad.Directory/groups/Members/Update | Umožňuje aktualizujte vlastnost groups.members v Azure Active Directory. |
| Microsoft.aad.Directory/groups/Owners/Update | Umožňuje aktualizujte vlastnost groups.owners v Azure Active Directory. |
| Microsoft.aad.Directory/groups/Restore | Umožňuje obnovte groups v Azure Active Directory. |
| Microsoft.aad.Directory/groups/Settings/Update | Umožňuje aktualizujte vlastnost groups.settings v Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Umožňuje aktualizujte vlastnost users.appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Správa licencí pro uživatele v Azure Active Directory. |
| Microsoft.aad.Directory/Users/Default/Update | Aktualizace základní vlastnosti pro uživatele v Azure Active Directory. |
| Microsoft.aad.Directory/Users/Create | Vytvořte uživatele v Azure Active Directory. |
| Microsoft.aad.Directory/Users/DELETE | Umožňuje odstraňte users v Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Umožňuje zneplatnit všechny obnovovací tokeny uživatele v Azure Active Directory. |
| Microsoft.aad.Directory/Users/Manager/Update | Umožňuje aktualizujte vlastnost users.manager v Azure Active Directory. |
| Microsoft.aad.Directory/Users/Password/Update | Aktualizace hesla pro všechny uživatele ve službě Azure Active Directory. Najdete v online dokumentaci pro více podrobností. |
| Microsoft.aad.Directory/Users/Restore | Umožňuje obnovovat odstraněné uživatele v Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Umožňuje aktualizujte vlastnost users.userPrincipalName ve službě Azure Active Directory. |
| microsoft.azure.accessService/allEntities/allTasks | Spravujte všechny aspekty služby Azure Access. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat služby Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvořit a spravovat lístky podpory Azure. |
| Microsoft.Office365.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat stav služby Office 365. |
| Microsoft.Office365.supportTickets/allEntities/allTasks | Umožňuje vytvářet a spravovat lístky podpory Office 365. |

### <a name="user"></a>Uživatel
Výchozí role pro uživatele člena. Můžete všechny čtení a zápis omezenou sadu informace o adresáři.

  > [!NOTE]
  > Tato role dědí z role ke čtení adresáře další oprávnění.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/applications/createAsOwner | Vytváření aplikací v Azure Active Directory. Autor se přidá jako první vlastník a vytvořený objekt počítat do kvóty 250 vytvořených objektů autora. |
| Microsoft.aad.Directory/groups/default/Read | Přečtěte si základní vlastnosti skupin v Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Vytváření skupin v Azure Active Directory. Autor se přidá jako první vlastník a vytvořený objekt počítat do kvóty 250 vytvořených objektů autora. |
| microsoft.aad.directory/oAuth2PermissionGrants/create | Umožňuje vytvořte oAuth2PermissionGrants v Azure Active Directory. |
| microsoft.aad.directory/oAuth2PermissionGrants/delete | Umožňuje odstraňte oAuth2PermissionGrants v Azure Active Directory. |
| microsoft.aad.directory/oAuth2PermissionGrants/update | Aktualizujte oAuth2PermissionGrants v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/createAsOwner | Umožňuje vytvořte servicePrincipals v Azure Active Directory. Autor se přidá jako první vlastník a vytvořený objekt počítat do kvóty 250 vytvořených objektů autora. |
| microsoft.aad.directory/users/activateServicePlan | Activateserviceplan uživatelů v Azure Active Directory. |
| microsoft.aad.directory/users/inviteGuest | Umožňuje zvát hosty do Azure Active Directory. |
| Microsoft.aad.Directory/Applications/default/Update | Aktualizace základní vlastnosti na aplikace v Azure Active Directory. |
| Microsoft.aad.Directory/Applications/DELETE | Umožňuje odstraňte applications v Azure Active Directory. |
| Microsoft.aad.Directory/Applications/Owners/Update | Umožňuje aktualizujte vlastnost applications.owners v Azure Active Directory. |
| Microsoft.aad.Directory/Applications/Permissions/Update | Umožňuje aktualizujte vlastnost applications.permissions ve službě Azure Active Directory. |
| Microsoft.aad.Directory/Applications/Policies/Update | Umožňuje aktualizujte vlastnost applications.policies ve službě Azure Active Directory. |
| Microsoft.aad.Directory/Applications/Restore | Umožňuje obnovte applications v Azure Active Directory. |
| Microsoft.aad.Directory/Devices/disable | Zakážete devices v Azure Active Directory. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Umožňuje aktualizujte vlastnost groups.appRoleAssignments v Azure Active Directory. |
| Microsoft.aad.Directory/groups/default/Update | Aktualizace základní vlastnosti skupin v Azure Active Directory. |
| Microsoft.aad.Directory/groups/DELETE | Umožňuje odstraňte groups v Azure Active Directory. |
| microsoft.aad.directory/groups/dynamicMembershipRule/update | Umožňuje aktualizujte vlastnost groups.dynamicMembershipRule v Azure Active Directory. |
| Microsoft.aad.Directory/groups/Members/Update | Umožňuje aktualizujte vlastnost groups.members v Azure Active Directory. |
| Microsoft.aad.Directory/groups/Owners/Update | Umožňuje aktualizujte vlastnost groups.owners v Azure Active Directory. |
| Microsoft.aad.Directory/groups/Restore | Umožňuje obnovte groups v Azure Active Directory. |
| Microsoft.aad.Directory/groups/Settings/Update | Umožňuje aktualizujte vlastnost groups.settings v Azure Active Directory. |
| Microsoft.aad.Directory/Policies/default/Update | Aktualizace základní vlastnosti v policies v Azure Active Directory. |
| Microsoft.aad.Directory/Policies/DELETE | Umožňuje odstraňte policies v Azure Active Directory. |
| Microsoft.aad.Directory/Policies/Owners/Update | Umožňuje aktualizujte vlastnost policies.owners v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Umožňuje aktualizujte vlastnost servicePrincipals.appRoleAssignedTo v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Umožňuje aktualizujte vlastnost servicePrincipals.appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/default/update | Aktualizace základní vlastnosti na servicePrincipals v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/delete | Umožňuje odstraňte servicePrincipals v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/update | Umožňuje aktualizujte vlastnost servicePrincipals.owners v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Umožňuje aktualizujte vlastnost servicePrincipals.policies ve službě Azure Active Directory. |
| microsoft.aad.directory/users/changePassword | Změna hesla pro všechny uživatele ve službě Azure Active Directory. Najdete v online dokumentaci pro více podrobností. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Umožňuje zneplatnit všechny obnovovací tokeny uživatele v Azure Active Directory. |
| microsoft.aad.directory/users/basicProfile/update | Umožňuje aktualizujte vlastnost users.basicProfile v Azure Active Directory. |
| Microsoft.aad.Directory/Users/Mobile/Update | Umožňuje aktualizujte vlastnost users.mobile ve službě Azure Active Directory. |
| microsoft.aad.directory/users/searchableDeviceKey/update | Umožňuje aktualizujte vlastnost users.searchableDeviceKey ve službě Azure Active Directory. |


## <a name="next-steps"></a>Další postup

* Další informace o tom, jak přiřadit uživatele jako správce předplatného Azure, najdete v článku [správě přístupu pomocí RBAC a webu Azure portal](../../role-based-access-control/role-assignments-portal.md)
* Další informace o tom, jak se přístup k prostředkům řídí ve službě Microsoft Azure, najdete v části [Principy přístupu k prostředkům ve službě Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Další informace o vztahu Azure Active Directory k předplatnému Azure najdete v tématu [Jak je předplatné Azure propojeno se službou Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md).

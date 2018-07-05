---
title: Přiřazení rolí správce v Azure Active Directory | Dokumentace Microsoftu
description: Roli správce můžete přidat uživatele, přiřazovat role správců, resetovat hesla uživatelů, spravovat uživatelské licence nebo spravovat domény. Uživatel, který má přiřazenou roli správce má stejná oprávnění ve všech cloudových služeb, u kterých vaše organizace předplatila.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 06/07/2018
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: 34b56c7435e2995f806828dce34f3d6bf425ca75
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37449795"
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

* **[Správce dodržování předpisů](#compliance-administrator)**: uživatelé s touto rolí mají oprávnění pro správu v Office 365 zabezpečení a dodržování předpisů System Center a centra pro správu Exchange. Další informace na [role správců Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **[Správce podmíněného přístupu](#conditional-access-administrator)**: uživatelé s touto rolí se budou moct spravovat nastavení podmíněného přístupu Azure Active Directory.
  > [!NOTE]
  > Nasadit zásady podmíněného přístupu Exchange ActiveSync v Azure, uživatel musí také být globální správce.
  
* **[Správce služby Dynamics 365 nebo správce služby CRM](#crm-service-administrator)**: uživatelé s touto rolí mají globální oprávnění v Microsoft CRM Online, pokud služba používá, a možnost spravovat lístky podpory a monitorování Stav služby. Další informace na [použít roli Správce služby ke správě svého tenanta](https://docs.microsoft.com/en-us/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant).

* **[Správci zařízení](#device-administrators)**: uživatelé s touto rolí stávají místními správci počítačů na všech zařízeních s Windows 10, která jsou připojená ke službě Azure Active Directory. Nemají možnost spravovat objektů zařízení ve službě Azure Active Directory.

* **[Uživatelé Čtoucí z adresáře](#directory-readers)**: Toto je starší verze role, která má být přiřazena k aplikacím, které nepodporují [souhlas Framework](../develop/active-directory-integrating-applications.md). Neměla být přiřazena k žádným uživatelům.

* **[Účty pro synchronizaci adresářů](#directory-synchronization-accounts)**: nepoužívejte. Tato role se automaticky přiřadí ke službě Azure AD Connect a není určena nebo nepodporuje pro jiné použití.

* **[Uživatelé zapisující do adresáře](#directory-writers)**: Toto je starší verze role, která má být přiřazena k aplikacím, které nepodporují [souhlas Framework](../develop/active-directory-integrating-applications.md). Neměla být přiřazena k žádným uživatelům.

* **[Správce služby Exchange](#exchange-service-administrator)**: uživatelé s touto rolí mají globální oprávnění ve službě Microsoft Exchange Online, pokud služba není k dispozici. Další informace na [role správců Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **[Globální správce / správce společnosti](#company-administrator)**: uživatelé s touto rolí mají přístup ke všem funkcím pro správu v Azure Active Directory, jakož i služeb, které používají identity Azure Active Directory, jako je Exchange Online SharePoint Online a Online Skype pro firmy. Osoba, která se zaregistruje k tenantovi Azure Active Directory se stane globálním správcem. Další role správců můžou přiřazovat jenom globální správci. Ve vaší společnosti může být více než jednoho globálního správce. Globální správci můžou resetovat heslo kteréhokoliv uživatele a všech ostatních správců.

  > [!NOTE]
  > V rozhraní Microsoft Graph API, Azure AD Graph API a Azure AD PowerShell tato role nazývá "Správce společnosti". Je "Globální správce" v [webu Azure portal](https://portal.azure.com).
  >
  >

* **[Odesílatel pozvánky hostů](#guest-inviter)**: uživatelé v této roli můžou spravovat pozvánky uživatelů typu Host Azure Active Directory s B2B při **členové můžou posílat pozvánky** uživatelské nastavení je Ne. Další informace o spolupráci B2B v [o aplikaci Azure AD B2B collaboration](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Nezahrnuje žádné jiné oprávnění.

* **[Správce Information Protection](#information-protection-administrator)**: uživatelé s touto rolí mají všechna oprávnění ve službě Azure Information Protection. Tato role umožňuje konfigurace popisků pro zásady Azure Information Protection, spravovat šablony ochrany a zapnutí ochrany. Tato role neuděluje žádná oprávnění v Centrum Identity Protection, Privileged Identity Management, monitorování Office 365 Service Health, nebo Office 365 Centru zabezpečení a dodržování předpisů.

* **[Správce služby Intune](#intune-service-administrator)**: uživatelé s touto rolí mají globální oprávnění v Microsoft Intune Online, pokud služba není k dispozici. Kromě toho tato role obsahuje možnost spravovat uživatele a zařízení, aby bylo možné přiřazovat zásady, jakož i vytvářet a spravovat skupiny. Další informace na [řízení správy na základě rolí (RBAC) v Microsoft Intune](https://docs.microsoft.com/en-us/intune/role-based-access-control)

* **[Čtenář Centra zpráv](#message-center-reader)**: uživatelé v této roli můžete sledovat oznámení a poradenství stavu aktualizace v [Centru zpráv Office 365](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093) pro svoji organizaci na nakonfigurované služby, jako je například Exchange, Intune a Microsoft Teams. Centrum zpráv čtečky přijímat týdenního přehledu e-mailu příspěvků, aktualizace a můžete sdílet zpráva center příspěvky ve službách Office 365. Ve službě Azure AD Uživatelé s touto rolí budete mít pouze oprávnění jen pro čtení na služby Azure AD, jako jsou uživatelé a skupiny. 

* **[Partnerská podpora úrovně 1](#partner-tier1-support)**: nepoužívejte. Tato role se už nepoužívá a bude odebrána z Azure AD v budoucnu. Tato role je určen pro malý počet těmito partnery společnosti Microsoft a není určena pro obecné použití.

* **[Partnerská podpora úrovně 2](#partner-tier2-support)**: nepoužívejte. Tato role se už nepoužívá a bude odebrána z Azure AD v budoucnu. Tato role je určen pro malý počet těmito partnery společnosti Microsoft a není určena pro obecné použití.

* **[Heslo správce / správce technické podpory](#helpdesk-administrator)**: uživatelé s touto rolí může měnit hesla, spravovat žádosti o služby a monitorovat stav služby. Správců technické podpory můžou měnit hesla jen uživatelům a jiným správcům technické podpory. 

  > [!NOTE]
  > V rozhraní Microsoft Graph API, Azure AD Graph API a Azure AD PowerShell tato role nazývá "Správce technické podpory". Je "Heslo správce" v [webu Azure portal](https://portal.azure.com/).
  >
  >
  
* **[Správce služby Power BI](#power-bi-service-administrator)**: uživatelé s touto rolí mají globální oprávnění v Microsoft Power BI, pokud služba používá, a možnost spravovat lístky podpory a monitorovat stav služby. Další informace na [Principy role správce Power BI](https://docs.microsoft.com/en-us/power-bi/service-admin-role).

* **[Privilegované Role správce](#privileged-role-administrator)**: uživatelé s touto rolí můžou Spravovat přiřazení rolí v Azure Active Directory, i v rámci Azure AD Privileged Identity Management. Kromě toho tato role umožňuje spravovat všechny aspekty služby Privileged Identity Management.

* **[Čtenář sestav](#reports-reader)**: uživatelé s touto rolí můžou zobrazit využití sestav, data a sestavy řídicího panelu v Centru pro správu Office 365 a kontext přijetí pack ve službě Power BI. Kromě toho role poskytuje přístup k přihlašování sestavy a aktivity ve službě Azure AD a dat vrácených Microsoft Graph API pro vytváření sestav. Uživatel přiřazený k roli Čtenář sestav můžete přistupovat pouze relevantní využití a metrik přijetí. Nemají žádná oprávnění správce ke konfiguraci nastavení nebo přístupu centra pro správu konkrétní produktu jako je Exchange. 

* **[Správce zabezpečení](#security-administrator)**: uživatelé s touto rolí mají všechna oprávnění jen pro čtení v roli Čtenář zabezpečení, plus umožňuje spravovat konfiguraci služeb souvisejících se zabezpečením: Azure Active Directory Identity Protection, Azure Information Protection, Privileged Identity Management a Office 365 zabezpečení a dodržování předpisů. Další informace o oprávněních Office 365 je k dispozici na [oprávnění v centru dodržování předpisů a zabezpečení Office 365](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).
  
  | V | Můžete provést |
  | --- | --- |
  | Centrum Identity Protection |<ul><li>Všechna oprávnění role čtenáře zabezpečení.<li>Kromě toho možnost provádět všechny operace IPC s výjimkou resetování hesla. |
  | Privileged Identity Management |<ul><li>Všechna oprávnění role čtenáře zabezpečení.<li>**Nelze** spravovat členství v rolích Azure AD nebo nastavení. |
  | <p>Monitorování stavu služby Office 365</p><p>Office 365 zabezpečení a dodržování předpisů |<ul><li>Všechna oprávnění role čtenáře zabezpečení.<li>Můžete nakonfigurovat všechna nastavení ve funkci rozšířené ochrany před internetovými útoky (ochrany proti malwaru a virů, škodlivý konfigurace adresy URL, adresa URL trasování atd.). |
  
* **[Čtenář zabezpečení](#security-reader)**: uživatelé s touto rolí mají globální přístup jen pro čtení, včetně všech informací v Azure Active Directory, Identity Protection, Privileged Identity Management, jakož i oprávnění ke čtení služby Azure Active Directory sestavy přihlášení a protokoly auditu. Role navíc uděluje oprávnění jen pro čtení v centru dodržování předpisů a zabezpečení Office 365. Další informace o oprávněních Office 365 je k dispozici na [oprávnění v centru dodržování předpisů a zabezpečení Office 365](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

  | V | Můžete provést |
  | --- | --- |
  | Centrum Identity Protection |Číst všechny zprávy o zabezpečení a informace o nastavení zabezpečení funkcí<ul><li>Antispamových<li>Šifrování<li>Prevence ztráty dat<li>Anti-malware<li>Pokročilou ochranu před hrozbami<li>Anti phisingová<li>Mailflow pravidla |
  | Privileged Identity Management |<p>Má přístup jen pro čtení ke všem informacím prezentované v Azure AD PIM: zásady a sestav pro přiřazení role Azure AD, bezpečnostní kontroly a v budoucnu přístup pro čtení k zásad dat a sestav pro scénáře kromě přiřazení role Azure AD.<p>**Nelze** registrace pro Azure AD PIM nebo dělat žádné změny. PIM na portálu nebo pomocí Powershellu někdo v této roli můžete aktivovat další role (například globální správce nebo správce privilegovaných rolí), pokud uživatel je kandidátem pro ně. |
  | <p>Monitorování stavu služby Office 365</p><p>Office 365 zabezpečení a dodržování předpisů</p> |<ul><li>Čtení a Správa výstrah<li>Zásady zabezpečení pro čtení<li>Přečtěte si analýzy hrozeb, zjišťování cloudových aplikací a karantény v hledání a prošetřit<li>Čtení všech sestav |

* **[Správce podpory služeb](#service-support-administrator)**: uživatelé s touto rolí můžete otevřít žádosti o podporu u Microsoftu pro služby Azure a Office 365 a zobrazení řídicího panelu služby a message center webu Azure portal a portálu pro správu Office 365. Další informace na [role správců Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **[Správce služeb Sharepointu](#sharepoint-service-administrator)**: uživatelé s touto rolí mají globální oprávnění v Microsoft SharePoint Online, pokud služba používá, a možnost spravovat lístky podpory a monitorovat stav služby. Další informace na [role správců Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **[Skype pro firmy nebo správce služeb Lyncu](#lync-service-administrator)**: uživatelé s touto rolí mají globální oprávnění v rámci Microsoft Skype pro firmy, pokud služba používá, stejně jako správu Skype konkrétní atributy uživatele v Azure Active Adresář. Kromě toho tato role uděluje možnost spravovat lístky podpory a monitorovat stav služby. Další informace na [o Skype pro firmy roli správce](https://support.office.com/en-us/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5).

  > [!NOTE]
  > V rozhraní Microsoft Graph API, Azure AD Graph API a Azure AD PowerShell tato role nazývá "Správce služeb Lyncu". Je "Skype pro firmy Správce služby" v [webu Azure portal](https://portal.azure.com/).
  >
  >

* **[Správce uživatelských účtů](#user-account-administrator)**: uživatelé s touto rolí můžete vytvářet a spravovat všechny aspekty uživatelů a skupin. Kromě toho tato role obsahuje možnost spravovat lístky podpory a monitorovat stav služby. Platí určitá omezení. Tato role například neumožňuje odstranit globálního správce. Správci účtu uživatele můžou měnit hesla jiných správců uživatelských účtů, uživatelů a správců technické podpory.

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


### <a name="to-add-a-colleague-as-a-global-administrator"></a>Chcete-li přidat kolegy jako globální správce

1. Přihlaste se k [centrum pro správu Azure Active Directory](https://aad.portal.azure.com) pomocí účtu, který je globálním správcem nebo správcem privilegovaných rolí pro adresář tenanta.

   ![Otevřete Centrum pro správu azure AD](./media/directory-assign-admin-roles/active-directory-admin-center.png)

2. Vyberte **uživatelé**.

3. Vyhledejte uživatele, který chcete určit jako globální správce a otevřete okno pro tohoto uživatele.

4. V okně uživatele vyberte **role adresáře**.
 
5. V okně role adresáře vyberte **globálního správce** role a uložte.

## <a name="detailed-azure-active-directory-permissions"></a>Podrobné oprávnění služby Azure Active Directory
Následující tabulky popisují konkrétní oprávnění v Azure Active Directory na každou roli. Některé role, jako je například globální správce, může mít další oprávnění v outide služby Microsoft Azure Active Directory.


### <a name="application-administrator"></a>Správce aplikace
Může vytvářet a spravovat všechny aspekty registrací aplikací a podnikových aplikací.

  > [!NOTE]
  > Další oprávnění dědí tato role [role uživatele](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/Application/Create | Umožňuje vytvářet Applications v Azure Active Directory. |
| microsoft.aad.directory/Application/Delete | Umožňuje odstraňovat Applications v Azure Active Directory. |
| microsoft.aad.directory/Application/Update | Umožňuje aktualizovat standardní vlastnosti v Applications v Azure Active Directory. |
| microsoft.aad.directory/Application/Update/DefaultPolicy | Umožňuje aktualizovat vlastnost Applications.DefaultPolicy v Azure Active Directory. |
| microsoft.aad.directory/Application/Update/Owners | Umožňuje aktualizovat vlastnost Applications.Owners v Azure Active Directory. |
| microsoft.aad.directory/AppRoleAssignment/Create | Umožňuje vytvářet AppRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/AppRoleAssignment/Delete | Umožňuje odstraňovat AppRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/AppRoleAssignment/Update | Umožňuje aktualizovat standardní vlastnosti v AppRoleAssigments v Azure Active Directory. |
| microsoft.aad.directory/Policy/Create | Umožňuje vytvářet Policies v Azure Active Directory. |
| microsoft.aad.directory/Policy/Delete | Umožňuje odstraňovat Policies v Azure Active Directory. |
| microsoft.aad.directory/Policy/Update | Umožňuje aktualizovat standardní vlastnosti v Policies v Azure Active Directory. |
| microsoft.aad.directory/Policy/Update/Owners | Umožňuje aktualizovat vlastnost Policies.Owners v Azure Active Directory. |
| microsoft.aad.directory/ServiceAction/ConsentOnBehalfOfAllNoDirectory | Umožňuje udělit souhlas jménem všech uživatelů ke všem prostředkům kromě Azure Active Directory (Azure AD Graph a Microsoft Graph) |
| microsoft.aad.directory/ServicePrincipal/Create | Umožňuje vytvářet ServicePrincipals v Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Delete | Umožňuje odstraňovat ServicePrincipals v Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update | Umožňuje aktualizovat standardní vlastnosti v ServicePrincipals v Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignedTo | Umožňuje aktualizovat vlastnost ServicePrincipals.AppRoleAssignedTo v Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignments | Umožňuje aktualizovat vlastnost ServicePrincipals.AppRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/DefaultPolicy | Umožňuje aktualizovat vlastnost ServicePrincipals.DefaultPolicy v Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/Owners | Umožňuje aktualizovat vlastnost ServicePrincipals.Owners v Azure Active Directory. |
| microsoft.aad.directory/User/AssignLicense | Umožňuje spravovat licence uživatelů v Azure Active Directory. |
| microsoft.aad.reports/AllEntities/Read | Umožňuje číst sestavy Azure AD. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Umožňuje číst a konfigurovat stav služby Office 365. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Umožňuje vytvářet a spravovat lístky podpory Office 365. |

### <a name="application-developer"></a>Vývojář aplikace
Může vytvářet registrace aplikací nezávisle **uživatelé můžou registrovat aplikace** nastavení.

  > [!NOTE]
  > Další oprávnění dědí tato role [role uživatele](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/Application/CreateAsOwner | Umožňuje vytvářet Applications v Azure Active Directory. Autor se přidá jako první vlastník a vytvořený objekt počítat do kvóty 250 vytvořených objektů autora. |
| microsoft.aad.directory/AppRoleAssignment/CreateAsOwner | Umožňuje vytvářet AppRoleAssignments v Azure Active Directory. Autor se přidá jako první vlastník a vytvořený objekt počítat do kvóty 250 vytvořených objektů autora. |
| microsoft.aad.directory/OAuth2PermissionGrant/CreateAsOwner | Umožňuje vytvářet OAuth2PermissionGrants v Azure Active Directory. Autor se přidá jako první vlastník a vytvořený objekt počítat do kvóty 250 vytvořených objektů autora. |
| microsoft.aad.directory/ServicePrincipal/CreateAsOwner | Umožňuje vytvářet ServicePrincipals v Azure Active Directory. Autor se přidá jako první vlastník a vytvořený objekt počítat do kvóty 250 vytvořených objektů autora. |

### <a name="billing-administrator"></a>Správce fakturace
Může provádět běžné úkoly související s fakturací, třeba aktualizovat platební údaje.

  > [!NOTE]
  > Další oprávnění dědí tato role [role uživatele](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > Tato role má dodatečná oprávnění mimo službu Azure Active Directory. Zobrazit popis role výše pro další informace.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Umožňuje číst vlastnost Organizations.TrustedCAsForPasswordlessAuth v Azure Active Directory. |
| microsoft.aad.directory/Organization/Update | Umožňuje aktualizovat standardní vlastnosti v Organizations v Azure Active Directory. |
| microsoft.aad.directory/Organization/Update/TrustedCAsForPasswordlessAuth | Umožňuje aktualizovat vlastnost Organizations.TrustedCAsForPasswordlessAuth v Azure Active Directory. |
| microsoft.aad.accessservice/AllEntities/AllActions | Umožňuje vytvářet a odstraňovat všechny prostředky a číst a aktualizovat standardní vlastnosti v Azure Access Control. |
| microsoft.aad.billing/AllEntities/AllActions | Umožňuje spravovat všechny aspekty fakturace Office 365. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Umožňuje číst a konfigurovat stav služby Office 365. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Umožňuje vytvářet a spravovat lístky podpory Office 365. |

### <a name="cloud-application-administrator"></a>Správce cloudové aplikace
Může vytvářet a spravovat všechny aspekty registrací aplikací a podnikových aplikací, kromě Proxy aplikací.

  > [!NOTE]
  > Další oprávnění dědí tato role [role uživatele](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/Application/Create | Umožňuje vytvářet Applications v Azure Active Directory. |
| microsoft.aad.directory/Application/Delete | Umožňuje odstraňovat Applications v Azure Active Directory. |
| microsoft.aad.directory/Application/Update | Umožňuje aktualizovat standardní vlastnosti v Applications v Azure Active Directory. |
| microsoft.aad.directory/Application/Update/DefaultPolicy | Umožňuje aktualizovat vlastnost Applications.DefaultPolicy v Azure Active Directory. |
| microsoft.aad.directory/Application/Update/Owners | Umožňuje aktualizovat vlastnost Applications.Owners v Azure Active Directory. |
| microsoft.aad.directory/AppRoleAssignment/Create | Umožňuje vytvářet AppRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/AppRoleAssignment/Delete | Umožňuje odstraňovat AppRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/AppRoleAssignment/Update | Umožňuje aktualizovat standardní vlastnosti v AppRoleAssigments v Azure Active Directory. |
| microsoft.aad.directory/Policy/Create | Umožňuje vytvářet Policies v Azure Active Directory. |
| microsoft.aad.directory/Policy/Delete | Umožňuje odstraňovat Policies v Azure Active Directory. |
| microsoft.aad.directory/Policy/Update | Umožňuje aktualizovat standardní vlastnosti v Policies v Azure Active Directory. |
| microsoft.aad.directory/Policy/Update/Owners | Umožňuje aktualizovat vlastnost Policies.Owners v Azure Active Directory. |
| microsoft.aad.directory/ServiceAction/ConsentOnBehalfOfAllNoDirectory | Umožňuje udělit souhlas jménem všech uživatelů ke všem prostředkům kromě Azure Active Directory (Azure AD Graph a Microsoft Graph) |
| microsoft.aad.directory/ServicePrincipal/Create | Umožňuje vytvářet ServicePrincipals v Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Delete | Umožňuje odstraňovat ServicePrincipals v Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update | Umožňuje aktualizovat standardní vlastnosti v ServicePrincipals v Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignedTo | Umožňuje aktualizovat vlastnost ServicePrincipals.AppRoleAssignedTo v Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignments | Umožňuje aktualizovat vlastnost ServicePrincipals.AppRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/DefaultPolicy | Umožňuje aktualizovat vlastnost ServicePrincipals.DefaultPolicy v Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/Owners | Umožňuje aktualizovat vlastnost ServicePrincipals.Owners v Azure Active Directory. |
| microsoft.aad.directory/User/AssignLicense | Umožňuje spravovat licence uživatelů v Azure Active Directory. |
| microsoft.aad.reports/AllEntities/Read | Umožňuje číst sestavy Azure AD. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Umožňuje číst a konfigurovat stav služby Office 365. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Umožňuje vytvářet a spravovat lístky podpory Office 365. |

### <a name="company-administrator"></a>Správce společnosti
Může spravovat všechny aspekty služeb Azure AD a Microsoft, které používají identity Azure AD. V rozhraní Microsoft Graph API, Azure AD Graph API a Azure AD PowerShell tato role nazývá "Správce společnosti". Je "Globální správce" v [webu Azure portal](https://portal.azure.com).

  > [!NOTE]
  > Další oprávnění dědí tato role [role uživatele](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Tato role má dodatečná oprávnění mimo službu Azure Active Directory. Zobrazit popis role výše pro další informace.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/AdministrativeUnit/AllActions/AllProperties | Umožňuje vytvářet a odstraňovat AdministrativeUnits a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/Application/AllActions/AllProperties | Umožňuje vytvářet a odstraňovat Applications a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/AppRoleAssignment/AllActions/AllProperties | Umožňuje vytvářet a odstraňovat AppRoleAssignments a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/CollaborationSpace/AllActions/AllProperties | Umožňuje vytvářet a odstraňovat CollaborationSpaces a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/Contact/AllActions/AllProperties | Umožňuje vytvářet a odstraňovat Contacts a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/Device/AllActions/AllProperties | Umožňuje vytvářet a odstraňovat Devices a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/DirectoryRole/AllActions/AllProperties | Umožňuje vytvářet a odstraňovat DirectoryRoles a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/DirectoryRoleTemplate/AllActions/AllProperties | Umožňuje vytvářet a odstraňovat DirectoryRoleTemplates a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/DirectorySetting/AllActions/AllProperties | Umožňuje vytvářet a odstraňovat DirectorySettings a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/DirectorySettingTemplate/AllActions/AllProperties | Umožňuje vytvářet a odstraňovat DirectorySettingTemplates a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/Domain/AllActions/AllProperties | Umožňuje vytvářet a odstraňovat Domains a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/Group/AllActions/AllProperties | Umožňuje vytvářet a odstraňovat Groups a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/LoginTenantBranding/AllActions/AllProperties | Umožňuje vytvářet a odstraňovat LoginTenantBrandings a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/OAuth2PermissionGrant/AllActions/AllProperties | Umožňuje vytvářet a odstraňovat OAuth2PermissionGrants a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/Policy/AllActions/AllProperties | Umožňuje vytvářet a odstraňovat Policies a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/ServiceAction/ConsentOnBehalfOfAllWithDirectory | Umožňuje udělit souhlas jménem všech uživatelů ke všem prostředkům včetně Azure Active Directory (Azure AD Graph a Microsoft Graph) |
| microsoft.aad.directory/ServicePrincipal/AllActions/AllProperties | Umožňuje vytvářet a odstraňovat ServicePrincipals a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/Organization/AllActions/AllProperties | Umožňuje vytvářet a odstraňovat Organizations a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/User/AllActions/AllProperties | Umožňuje vytvářet a odstraňovat Users a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.aadconnect/AllEntities/AllActions | Umožňuje vytvářet a odstraňovat všechny prostředky a číst a aktualizovat standardní vlastnosti v microsoft.aad.aadconnect. |
| microsoft.aad.accessservice/AllEntities/AllActions | Umožňuje vytvářet a odstraňovat všechny prostředky a číst a aktualizovat standardní vlastnosti v Azure Access Control. |
| microsoft.aad.billing/AllEntities/AllActions | Umožňuje spravovat všechny aspekty fakturace Office 365. |
| microsoft.aad.compliance/AllEntities/AllActions | Umožňuje vytvářet a odstraňovat všechny prostředky a číst a aktualizovat standardní vlastnosti v Centru dodržování předpisů. |
| microsoft.aad.directorysync/AllEntities/AllActions | Umožňuje provádět všechny akce ve službě Azure AD Connect. |
| microsoft.aad.lockbox/AllEntities/AllActions | Umožňuje spravovat všechny aspekty služby Lockbox. |
| microsoft.aad.privilegedrolemanagement/AllEntities/AllActions | Umožňuje spravovat všechny aspekty služby Privileged Role Management. |
| microsoft.aad.reports/AllEntities/AllActions | Umožňuje číst a konfigurovat sestavy Azure AD. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Umožňuje číst a konfigurovat stav služby Office 365. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Umožňuje vytvářet a spravovat lístky podpory Office 365. |
| microsoft.crm/AllEntities/AllActions | Umožňuje spravovat všechny aspekty Dynamics 365. |
| microsoft.exchange/AllEntities/AllActions | Umožňuje spravovat všechny aspekty Exchange Online. |
| microsoft.aad.informationprotection/AllEntities/AllActions | Umožňuje spravovat všechny aspekty služby Information Protection. |
| microsoft.intune/AllEntities/AllActions | Umožňuje spravovat všechny aspekty Intune. |
| microsoft.powerbi/AllEntities/AllActions | Umožňuje spravovat všechny aspekty Power BI. |
| microsoft.protectioncenter/AllEntities/AllActions | Umožňuje spravovat Centrum ochrany Office 365. |
| microsoft.sharepoint/AllEntities/AllActions | Umožňuje spravovat SharePoint Online. |
| microsoft.skypeforbusiness/AllEntities/AllActions | Umožňuje spravovat Online Skype pro firmy. |

### <a name="compliance-administrator"></a>Správce dodržování předpisů
Může číst a spravovat konfiguraci dodržování předpisů a sestav v Azure AD a Office 365.

  > [!NOTE]
  > Další oprávnění dědí tato role [role uživatele](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Tato role má dodatečná oprávnění mimo službu Azure Active Directory. Zobrazit popis role výše pro další informace.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | Umožňuje vytvářet a odstraňovat všechny prostředky a číst a aktualizovat standardní vlastnosti v Azure Access Control. |
| microsoft.aad.compliance/AllEntities/AllActions | Umožňuje vytvářet a odstraňovat všechny prostředky a číst a aktualizovat standardní vlastnosti v Centru dodržování předpisů. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Umožňuje číst a konfigurovat stav služby Office 365. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Umožňuje vytvářet a spravovat lístky podpory Office 365. |
| microsoft.exchange/Compliance/AllActions | Umožňuje spravovat dodržování předpisů v Exchangi Online. |
| microsoft.sharepoint/Compliance/AllActions | Umožňuje spravovat dodržování předpisů v SharePointu Online. |
| microsoft.skypeforbusiness/Compliance/AllActions | Umožňuje spravovat dodržování předpisů v Online Skypu pro firmy. |

### <a name="conditional-access-administrator"></a>Správce podmíněného přístupu
Může spravovat funkce podmíněného přístupu.

  > [!NOTE]
  > Další oprávnění dědí tato role [role uživatele](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/ConditionalAccessPolicy/Create | Umožňuje vytvářet ConditionalAccessPolicys v Azure Active Directory. |
| microsoft.aad.directory/ConditionalAccessPolicy/Delete | Umožňuje odstraňovat ConditionalAccessPolicys v Azure Active Directory. |
| microsoft.aad.directory/ConditionalAccessPolicy/Read | Umožňuje číst standardní vlastnosti ConditionalAccessPolicys v Azure Active Directory. |
| microsoft.aad.directory/ConditionalAccessPolicy/Read/Owners | Umožňuje číst vlastnost ConditionalAccessPolicys.Owners v Azure Active Directory. |
| microsoft.aad.directory/ConditionalAccessPolicy/Read/PolicyAppliedTo | Umožňuje číst vlastnost ConditionalAccessPolicys.PolicyAppliedTo v Azure Active Directory. |
| microsoft.aad.directory/ConditionalAccessPolicy/Update | Umožňuje aktualizovat standardní vlastnosti v ConditionalAccessPolicys v Azure Active Directory. |
| microsoft.aad.directory/ConditionalAccessPolicy/Update/Owners | Umožňuje aktualizovat vlastnost ConditionalAccessPolicys.Owners v Azure Active Directory. |

### <a name="crm-service-administrator"></a>Správce služby CRM
Může spravovat všechny aspekty produktu Dynamics 365.

  > [!NOTE]
  > Další oprávnění dědí tato role [role uživatele](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Tato role má dodatečná oprávnění mimo službu Azure Active Directory. Zobrazit popis role výše pro další informace.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Umožňuje číst vlastnost Organizations.TrustedCAsForPasswordlessAuth v Azure Active Directory. |
| microsoft.aad.accessservice/AllEntities/AllActions | Umožňuje vytvářet a odstraňovat všechny prostředky a číst a aktualizovat standardní vlastnosti v Azure Access Control. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Umožňuje číst a konfigurovat stav služby Office 365. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Umožňuje vytvářet a spravovat lístky podpory Office 365. |
| microsoft.crm/AllEntities/AllActions | Umožňuje spravovat všechny aspekty Dynamics 365. |

### <a name="device-administrators"></a>Správci zařízení
Členové této role se přidají do skupiny místních správců na Azure zařízení připojených k doméně AD.

  > [!NOTE]
  > Další oprávnění dědí tato role [role uživatele](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

| **Akce** | **Popis** |
| --- | --- |

### <a name="directory-reader"></a>Čtečka adresáře
Může číst informace o základní adresář. Pro udělení přístupu k aplikacím

  > [!NOTE]
  > Další oprávnění dědí tato role [role uživatele](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/AdministrativeUnit/Read | Umožňuje číst standardní vlastnosti AdministrativeUnits v Azure Active Directory. |
| microsoft.aad.directory/AdministrativeUnit/Read/Members | Umožňuje číst vlastnost AdministrativeUnits.Members v Azure Active Directory. |
| microsoft.aad.directory/Application/Read | Umožňuje číst standardní vlastnosti v Applications v Azure Active Directory. |
| microsoft.aad.directory/Application/Read/Owners | Umožňuje číst vlastnost Applications.Owners v Azure Active Directory. |
| microsoft.aad.directory/CollaborationSpace/Read | Umožňuje číst standardní vlastnosti CollaborationSpaces v Azure Active Directory. |
| microsoft.aad.directory/CollaborationSpace/Read/Owners | Umožňuje číst vlastnost CollaborationSpaces.Owners v Azure Active Directory. |
| microsoft.aad.directory/Contact/Read | Umožňuje číst standardní vlastnosti Contacts v Azure Active Directory. |
| microsoft.aad.directory/Contact/Read/MemberOf | Umožňuje číst vlastnost Contacts.MemberOf v Azure Active Directory. |
| microsoft.aad.directory/Device/Read | Umožňuje číst standardní vlastnosti v Devices v Azure Active Directory. |
| microsoft.aad.directory/Device/Read/MemberOf | Umožňuje číst vlastnost Devices.MemberOf v Azure Active Directory. |
| microsoft.aad.directory/Device/Read/RegisteredOwners | Umožňuje číst vlastnost Devices.RegisteredOwners v Azure Active Directory. |
| microsoft.aad.directory/Device/Read/RegisteredUsers | Umožňuje číst vlastnost Devices.RegisteredUsers v Azure Active Directory. |
| microsoft.aad.directory/DirectoryRole/Read | Umožňuje číst standardní vlastnosti DirectoryRoles v Azure Active Directory. |
| microsoft.aad.directory/DirectoryRole/Read/EligibleMembers | Umožňuje číst vlastnost DirectoryRoles.EligibleMembers v Azure Active Directory. |
| microsoft.aad.directory/DirectoryRole/Read/Members | Umožňuje číst vlastnost DirectoryRoles.Members v Azure Active Directory. |
| microsoft.aad.directory/DirectorySetting/Read | Umožňuje číst standardní vlastnosti DirectorySettings v Azure Active Directory. |
| microsoft.aad.directory/DirectorySettingTemplate/Read | Umožňuje číst standardní vlastnosti v DirectorySettingTemplates v Azure Active Directory. |
| microsoft.aad.directory/Domain/Read | Umožňuje číst standardní vlastnosti Domains v Azure Active Directory. |
| microsoft.aad.directory/Group/Read | Umožňuje číst standardní vlastnosti Groups v Azure Active Directory. |
| microsoft.aad.directory/Group/Read/AppRoleAssignments | Umožňuje číst vlastnost Groups.AppRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/Group/Read/MemberOf | Umožňuje číst vlastnost Groups.MemberOf v Azure Active Directory. |
| microsoft.aad.directory/Group/Read/Members | Umožňuje číst vlastnost Groups.Members v Azure Active Directory. |
| microsoft.aad.directory/Group/Read/Owners | Umožňuje číst vlastnost Groups.Owners v Azure Active Directory. |
| microsoft.aad.directory/Group/Read/Settings | Umožňuje číst vlastnost Groups.Settings v Azure Active Directory. |
| microsoft.aad.directory/OAuth2PermissionGrant/Read | Umožňuje číst standardní vlastnosti OAuth2PermissionGrants v Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read | Umožňuje číst standardní vlastnosti v ServicePrincipals v Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/AppRoleAssignedTo | Umožňuje číst vlastnost ServicePrincipals.AppRoleAssignedTo v Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/AppRoleAssignments | Umožňuje číst vlastnost ServicePrincipals.AppRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/DefaultPolicy | Umožňuje číst vlastnost ServicePrincipals.DefaultPolicy v Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/MemberOf | Umožňuje číst vlastnost ServicePrincipals.MemberOf v Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/OAuth2PermissionGrants | Umožňuje číst vlastnost ServicePrincipals.OAuth2PermissionGrants v Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/Owners | Umožňuje číst vlastnost ServicePrincipals.Owners v Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/OwnedObjects | Umožňuje číst vlastnost ServicePrincipals.OwnedObjects v Azure Active Directory. |
| microsoft.aad.directory/Organization/Read | Umožňuje číst standardní vlastnosti v Organizations v Azure Active Directory. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Umožňuje číst vlastnost Organizations.TrustedCAsForPasswordlessAuth v Azure Active Directory. |
| microsoft.aad.directory/User/Read | Umožňuje číst standardní vlastnosti Users v Azure Active Directory. |
| microsoft.aad.directory/User/Read/AppRoleAssignments | Umožňuje číst vlastnost Users.AppRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/User/Read/DirectReports | Umožňuje číst vlastnost Users.DirectReports v Azure Active Directory. |
| microsoft.aad.directory/User/Read/InvitedBy | Umožňuje číst vlastnost Users.InvitedBy v Azure Active Directory. |
| microsoft.aad.directory/User/Read/InvitedUsers | Umožňuje číst vlastnost Users.InvitedUsers v Azure Active Directory. |
| microsoft.aad.directory/User/Read/Manager | Umožňuje číst vlastnost Users.Manager v Azure Active Directory. |
| microsoft.aad.directory/User/Read/MemberOf | Umožňuje číst vlastnost Users.MemberOf v Azure Active Directory. |
| microsoft.aad.directory/User/Read/OAuth2PermissionGrants | Umožňuje číst vlastnost Users.OAuth2PermissionGrants v Azure Active Directory. |
| microsoft.aad.directory/User/Read/OwnedDevices | Umožňuje číst vlastnost Users.OwnedDevices v Azure Active Directory. |
| microsoft.aad.directory/User/Read/OwnedObjects | Umožňuje číst vlastnost Users.OwnedObjects v Azure Active Directory. |
| microsoft.aad.directory/User/Read/RegisteredDevices | Umožňuje číst vlastnost Users.RegisteredDevices v Azure Active Directory. |

### <a name="directory-synchronization-accounts"></a>Účty pro synchronizaci adresáře
Pouze používá služba Azure AD Connect.

  > [!NOTE]
  > Další oprávnění dědí tato role [role uživatele](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/Policy/Create | Umožňuje vytvářet Policies v Azure Active Directory. |
| microsoft.aad.directory/Policy/Delete | Umožňuje odstraňovat Policies v Azure Active Directory. |
| microsoft.aad.directory/Policy/Read | Umožňuje číst standardní vlastnosti v Policies v Azure Active Directory. |
| microsoft.aad.directory/Policy/Read/Owners | Umožňuje číst vlastnost Policies.Owners v Azure Active Directory. |
| microsoft.aad.directory/Policy/Read/PolicyAppliedTo | Umožňuje číst vlastnost Policies.PolicyAppliedTo v Azure Active Directory. |
| microsoft.aad.directory/Policy/Update | Umožňuje aktualizovat standardní vlastnosti v Policies v Azure Active Directory. |
| microsoft.aad.directory/Policy/Update/Owners | Umožňuje aktualizovat vlastnost Policies.Owners v Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Create | Umožňuje vytvářet ServicePrincipals v Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read | Umožňuje číst standardní vlastnosti v ServicePrincipals v Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/AppRoleAssignedTo | Umožňuje číst vlastnost ServicePrincipals.AppRoleAssignedTo v Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/AppRoleAssignments | Umožňuje číst vlastnost ServicePrincipals.AppRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/DefaultPolicy | Umožňuje číst vlastnost ServicePrincipals.DefaultPolicy v Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/MemberOf | Umožňuje číst vlastnost ServicePrincipals.MemberOf v Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/OAuth2PermissionGrants | Umožňuje číst vlastnost ServicePrincipals.OAuth2PermissionGrants v Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/Owners | Umožňuje číst vlastnost ServicePrincipals.Owners v Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/OwnedObjects | Umožňuje číst vlastnost ServicePrincipals.OwnedObjects v Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update | Umožňuje aktualizovat standardní vlastnosti v ServicePrincipals v Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignedTo | Umožňuje aktualizovat vlastnost ServicePrincipals.AppRoleAssignedTo v Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignments | Umožňuje aktualizovat vlastnost ServicePrincipals.AppRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/DefaultPolicy | Umožňuje aktualizovat vlastnost ServicePrincipals.DefaultPolicy v Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/Owners | Umožňuje aktualizovat vlastnost ServicePrincipals.Owners v Azure Active Directory. |
| microsoft.aad.directory/Organization/Update/DirSync | Umožňuje aktualizovat vlastnost Organizations.DirSync v Azure Active Directory. |
| microsoft.aad.directorysync/AllEntities/AllActions | Umožňuje provádět všechny akce ve službě Azure AD Connect. |

### <a name="directory-writer"></a>Zapisovač adresáře
Může číst a zapisovat informace základní adresář. Pro udělení přístupu k aplikacím

  > [!NOTE]
  > Další oprávnění dědí tato role [role uživatele](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/DirectorySetting/Create | Umožňuje vytvářet DirectorySettings v Azure Active Directory. |
| microsoft.aad.directory/DirectorySetting/Delete | Umožňuje odstraňovat DirectorySettings v Azure Active Directory. |
| microsoft.aad.directory/DirectorySetting/Update | Umožňuje aktualizovat standardní vlastnosti v DirectorySettingTemplates v Azure Active Directory. |
| microsoft.aad.directory/Group/Create | Umožňuje vytvářet Groups v Azure Active Directory. |
| microsoft.aad.directory/Group/CreateAsOwner | Umožňuje vytvářet Groups v Azure Active Directory. Autor se přidá jako první vlastník a vytvořený objekt počítat do kvóty 250 vytvořených objektů autora. |
| microsoft.aad.directory/Group/Read | Umožňuje číst standardní vlastnosti Groups v Azure Active Directory. |
| microsoft.aad.directory/Group/Update | Umožňuje aktualizovat standardní vlastnosti v Groups v Azure Active Directory. |
| microsoft.aad.directory/Group/Update/AppRoleAssignments | Umožňuje aktualizovat vlastnost Groups.AppRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Members | Umožňuje aktualizovat vlastnost Groups.Members v Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Owners | Umožňuje aktualizovat vlastnost Groups.Owners v Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Settings | Umožňuje aktualizovat vlastnost Groups.Settings v Azure Active Directory. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Umožňuje číst vlastnost Organizations.TrustedCAsForPasswordlessAuth v Azure Active Directory. |
| microsoft.aad.directory/User/AssignLicense | Umožňuje spravovat licence uživatelů v Azure Active Directory. |
| microsoft.aad.directory/User/InvalidateAllRefreshTokens | Umožňuje zneplatnit všechny obnovovací tokeny uživatele v Azure Active Directory. |
| microsoft.aad.directory/User/Update | Umožňuje aktualizovat standardní vlastnosti v Users v Azure Active Directory. |
| microsoft.aad.directory/User/Update/AppRoleAssignments | Umožňuje aktualizovat vlastnost Users.AppRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/User/Update/Manager | Umožňuje aktualizovat vlastnost Users.Manager v Azure Active Directory. |

### <a name="exchange-service-administrator"></a>Správce služby Exchange
Může spravovat všechny aspekty produktu Exchange.

  > [!NOTE]
  > Další oprávnění dědí tato role [role uživatele](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Tato role má dodatečná oprávnění mimo službu Azure Active Directory. Zobrazit popis role výše pro další informace.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | Umožňuje vytvářet a odstraňovat všechny prostředky a číst a aktualizovat standardní vlastnosti v Azure Access Control. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Umožňuje vytvářet a spravovat lístky podpory Office 365. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Umožňuje číst a konfigurovat stav služby Office 365. |
| microsoft.exchange/AllEntities/AllActions | Umožňuje spravovat všechny aspekty Exchange Online. |

### <a name="guest-inviter"></a>Odesílatel pozvánky hostů
Můžete pozvat uživatele typu Host nezávisle **členové můžou zvát hosty** nastavení.

  > [!NOTE]
  > Tato role dědí další oprávnění v roli hosta.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/User/InviteGuest | Umožňuje zvát hosty do Azure Active Directory. |
| microsoft.aad.directory/User/Read | Umožňuje číst standardní vlastnosti Users v Azure Active Directory. |
| microsoft.aad.directory/User/Read/AppRoleAssignments | Umožňuje číst vlastnost Users.AppRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/User/Read/DirectReports | Umožňuje číst vlastnost Users.DirectReports v Azure Active Directory. |
| microsoft.aad.directory/User/Read/InvitedBy | Umožňuje číst vlastnost Users.InvitedBy v Azure Active Directory. |
| microsoft.aad.directory/User/Read/InvitedUsers | Umožňuje číst vlastnost Users.InvitedUsers v Azure Active Directory. |
| microsoft.aad.directory/User/Read/Manager | Umožňuje číst vlastnost Users.Manager v Azure Active Directory. |
| microsoft.aad.directory/User/Read/MemberOf | Umožňuje číst vlastnost Users.MemberOf v Azure Active Directory. |
| microsoft.aad.directory/User/Read/OAuth2PermissionGrants | Umožňuje číst vlastnost Users.OAuth2PermissionGrants v Azure Active Directory. |
| microsoft.aad.directory/User/Read/OwnedDevices | Umožňuje číst vlastnost Users.OwnedDevices v Azure Active Directory. |
| microsoft.aad.directory/User/Read/OwnedObjects | Umožňuje číst vlastnost Users.OwnedObjects v Azure Active Directory. |
| microsoft.aad.directory/User/Read/RegisteredDevices | Umožňuje číst vlastnost Users.RegisteredDevices v Azure Active Directory. |

### <a name="helpdesk-administrator"></a>Správce technické podpory
Může resetovat hesla uživatelů, kteří nejsou správci, a správců technické podpory.

  > [!NOTE]
  > Další oprávnění dědí tato role [role uživatele](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Umožňuje číst vlastnost Organizations.TrustedCAsForPasswordlessAuth v Azure Active Directory. |
| microsoft.aad.directory/User/InvalidateAllRefreshTokens | Umožňuje zneplatnit všechny obnovovací tokeny uživatele v Azure Active Directory. |
| microsoft.aad.directory/User/Update/PasswordHelpdeskScope | Aktualizace hesel omezených správců a jiní správci technické podpory v Azure Active Directory. Najdete v online dokumentaci pro více podrobností. |
| microsoft.aad.accessservice/AllEntities/AllActions | Umožňuje vytvářet a odstraňovat všechny prostředky a číst a aktualizovat standardní vlastnosti v Azure Access Control. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Umožňuje vytvářet a spravovat lístky podpory Office 365. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Umožňuje číst a konfigurovat stav služby Office 365. |

### <a name="information-protection-administrator"></a>Správce Information Protection
Může spravovat všechny aspekty produktu Azure Information Protection.

  > [!NOTE]
  > Další oprávnění dědí tato role [role uživatele](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Tato role má dodatečná oprávnění mimo službu Azure Active Directory. Zobrazit popis role výše pro další informace.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/Group/Read | Umožňuje číst standardní vlastnosti Groups v Azure Active Directory. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Umožňuje číst vlastnost Organizations.TrustedCAsForPasswordlessAuth v Azure Active Directory. |
| microsoft.aad.informationprotection/AllEntities/AllActions | Umožňuje spravovat všechny aspekty služby Information Protection. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Umožňuje číst a konfigurovat stav služby Office 365. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Umožňuje vytvářet a spravovat lístky podpory Office 365. |

### <a name="intune-service-administrator"></a>Správce služby Intune
Může spravovat všechny aspekty produktu Intune.

  > [!NOTE]
  > Další oprávnění dědí tato role [role uživatele](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Tato role má dodatečná oprávnění mimo službu Azure Active Directory. Zobrazit popis role výše pro další informace.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/Contact/Create | Umožňuje vytvářet Contacts v Azure Active Directory. |
| microsoft.aad.directory/Contact/Delete | Umožňuje odstraňovat Contacts v Azure Active Directory. |
| microsoft.aad.directory/Contact/Update | Umožňuje aktualizovat standardní vlastnosti v Contacts v Azure Active Directory. |
| microsoft.aad.directory/Device/Create | Umožňuje vytvářet Devices v Azure Active Directory. |
| microsoft.aad.directory/Device/Delete | Umožňuje odstraňovat Devices v Azure Active Directory. |
| microsoft.aad.directory/Device/Update | Umožňuje aktualizovat standardní vlastnosti v Devices v Azure Active Directory. |
| microsoft.aad.directory/Device/Update/RegisteredOwners | Umožňuje aktualizovat vlastnost Devices.RegisteredOwners v Azure Active Directory. |
| microsoft.aad.directory/Device/Update/RegisteredUsers | Umožňuje aktualizovat vlastnost Devices.RegisteredUsers v Azure Active Directory. |
| microsoft.aad.directory/Group/Create | Umožňuje vytvářet Groups v Azure Active Directory. |
| microsoft.aad.directory/Group/CreateAsOwner | Umožňuje vytvářet Groups v Azure Active Directory. Autor se přidá jako první vlastník a vytvořený objekt počítat do kvóty 250 vytvořených objektů autora. |
| microsoft.aad.directory/Group/Delete | Umožňuje odstraňovat Groups v Azure Active Directory. |
| microsoft.aad.directory/Group/Read | Umožňuje číst standardní vlastnosti Groups v Azure Active Directory. |
| microsoft.aad.directory/Group/Read/HiddenMembers | Umožňuje číst vlastnost Groups.HiddenMembers v Azure Active Directory. |
| microsoft.aad.directory/Group/Restore | Umožňuje obnovovat Groups v Azure Active Directory. |
| microsoft.aad.directory/Group/Update | Umožňuje aktualizovat standardní vlastnosti v Groups v Azure Active Directory. |
| microsoft.aad.directory/Group/Update/AppRoleAssignments | Umožňuje aktualizovat vlastnost Groups.AppRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Members | Umožňuje aktualizovat vlastnost Groups.Members v Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Owners | Umožňuje aktualizovat vlastnost Groups.Owners v Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Settings | Umožňuje aktualizovat vlastnost Groups.Settings v Azure Active Directory. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Umožňuje číst vlastnost Organizations.TrustedCAsForPasswordlessAuth v Azure Active Directory. |
| microsoft.aad.directory/User/Update | Umožňuje aktualizovat standardní vlastnosti v Users v Azure Active Directory. |
| microsoft.aad.directory/User/Update/AppRoleAssignments | Umožňuje aktualizovat vlastnost Users.AppRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/User/Update/Manager | Umožňuje aktualizovat vlastnost Users.Manager v Azure Active Directory. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Umožňuje vytvářet a spravovat lístky podpory Office 365. |
| microsoft.intune/AllEntities/AllActions | Umožňuje spravovat všechny aspekty Intune. |

### <a name="lync-service-administrator"></a>Správce služeb Lyncu
Může spravovat všechny aspekty produktu Skype pro firmy.

  > [!NOTE]
  > Další oprávnění dědí tato role [role uživatele](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Tato role má dodatečná oprávnění mimo službu Azure Active Directory. Zobrazit popis role výše pro další informace.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | Umožňuje vytvářet a odstraňovat všechny prostředky a číst a aktualizovat standardní vlastnosti v Azure Access Control. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Umožňuje číst a konfigurovat stav služby Office 365. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Umožňuje vytvářet a spravovat lístky podpory Office 365. |
| microsoft.skypeforbusiness/AllEntities/AllActions | Umožňuje spravovat Online Skype pro firmy. |

### <a name="message-center-reader"></a>Čtenář Centra zpráv
Může číst zprávy a aktualizace pro svou organizaci jen v Centru zpráv Office 365. 

  > [!NOTE]
  > Tato role dědí z role ke čtení adresáře další oprávnění.
  >
  >

  > [!NOTE]
  > Tato role má dodatečná oprávnění mimo službu Azure Active Directory. Zobrazit popis role výše pro další informace.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/Group/Read | Umožňuje číst standardní vlastnosti Groups v Azure Active Directory. |
| microsoft.aad.accessmessagecenter/AllEntities/AllActions | Umožňuje vytvářet a odstraňovat všechny prostředky a číst a aktualizovat standardní vlastnosti v Centru zpráv. |
| microsoft.aad.accessservice/AllEntities/AllActions | Umožňuje vytvářet a odstraňovat všechny prostředky a číst a aktualizovat standardní vlastnosti v Azure Access Control. |

### <a name="partner-tier1-support"></a>Podpora partnerů úrovně 1
Nepoužívejte – nejsou určené pro obecné použití.

  > [!NOTE]
  > Další oprávnění dědí tato role [role uživatele](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Tato role má dodatečná oprávnění mimo službu Azure Active Directory. Zobrazit popis role výše pro další informace.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | Umožňuje vytvářet a odstraňovat všechny prostředky a číst a aktualizovat standardní vlastnosti v Azure Access Control. |
| microsoft.aad.directory/Contact/Create | Umožňuje vytvářet Contacts v Azure Active Directory. |
| microsoft.aad.directory/Contact/Delete | Umožňuje odstraňovat Contacts v Azure Active Directory. |
| microsoft.aad.directory/Contact/Update | Umožňuje aktualizovat standardní vlastnosti v Contacts v Azure Active Directory. |
| microsoft.aad.directory/Group/Create | Umožňuje vytvářet Groups v Azure Active Directory. |
| microsoft.aad.directory/Group/CreateAsOwner | Umožňuje vytvářet Groups v Azure Active Directory. Autor se přidá jako první vlastník a vytvořený objekt počítat do kvóty 250 vytvořených objektů autora. |
| microsoft.aad.directory/Group/Read | Umožňuje číst standardní vlastnosti Groups v Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Members | Umožňuje aktualizovat vlastnost Groups.Members v Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Owners | Umožňuje aktualizovat vlastnost Groups.Owners v Azure Active Directory. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Umožňuje číst vlastnost Organizations.TrustedCAsForPasswordlessAuth v Azure Active Directory. |
| microsoft.aad.directory/User/AssignLicense | Umožňuje spravovat licence uživatelů v Azure Active Directory. |
| microsoft.aad.directory/User/Delete | Umožňuje odstraňovat Users v Azure Active Directory. |
| microsoft.aad.directory/User/InvalidateAllRefreshTokens | Umožňuje zneplatnit všechny obnovovací tokeny uživatele v Azure Active Directory. |
| microsoft.aad.directory/User/Restore | Umožňuje obnovovat odstraněné uživatele v Azure Active Directory. |
| microsoft.aad.directory/User/Update | Umožňuje aktualizovat standardní vlastnosti v Users v Azure Active Directory. |
| microsoft.aad.directory/User/Update/AppRoleAssignments | Umožňuje aktualizovat vlastnost Users.AppRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/User/Update/Manager | Umožňuje aktualizovat vlastnost Users.Manager v Azure Active Directory. |
| microsoft.aad.directory/User/Update/PasswordUserScope | Aktualizace hesla pro bez oprávnění správce v Azure Active Directory. Najdete v online dokumentaci pro více podrobností. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Umožňuje číst a konfigurovat stav služby Office 365. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Umožňuje vytvářet a spravovat lístky podpory Office 365. |

### <a name="partner-tier2-support"></a>Podpora partnerů úrovně 2
Nepoužívejte – nejsou určené pro obecné použití.

  > [!NOTE]
  > Další oprávnění dědí tato role [role uživatele](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Tato role má dodatečná oprávnění mimo službu Azure Active Directory. Zobrazit popis role výše pro další informace.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | Umožňuje vytvářet a odstraňovat všechny prostředky a číst a aktualizovat standardní vlastnosti v Azure Access Control. |
| microsoft.aad.directory/Contact/Create | Umožňuje vytvářet Contacts v Azure Active Directory. |
| microsoft.aad.directory/Contact/Delete | Umožňuje odstraňovat Contacts v Azure Active Directory. |
| microsoft.aad.directory/Contact/Update | Umožňuje aktualizovat standardní vlastnosti v Contacts v Azure Active Directory. |
| microsoft.aad.directory/Domain/AllActions | Umožňuje vytvářet a odstraňovat Domains a číst a aktualizovat standardní vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/Group/Create | Umožňuje vytvářet Groups v Azure Active Directory. |
| microsoft.aad.directory/Group/Delete | Umožňuje odstraňovat Groups v Azure Active Directory. |
| microsoft.aad.directory/Group/Read | Umožňuje číst standardní vlastnosti Groups v Azure Active Directory. |
| microsoft.aad.directory/Group/Restore | Umožňuje obnovovat Groups v Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Members | Umožňuje aktualizovat vlastnost Groups.Members v Azure Active Directory. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Umožňuje číst vlastnost Organizations.TrustedCAsForPasswordlessAuth v Azure Active Directory. |
| microsoft.aad.directory/Organization/Update | Umožňuje aktualizovat standardní vlastnosti v Organizations v Azure Active Directory. |
| microsoft.aad.directory/Organization/Update/TrustedCAsForPasswordlessAuth | Umožňuje aktualizovat vlastnost Organizations.TrustedCAsForPasswordlessAuth v Azure Active Directory. |
| microsoft.aad.directory/User/AssignLicense | Umožňuje spravovat licence uživatelů v Azure Active Directory. |
| microsoft.aad.directory/User/Delete | Umožňuje odstraňovat Users v Azure Active Directory. |
| microsoft.aad.directory/User/InvalidateAllRefreshTokens | Umožňuje zneplatnit všechny obnovovací tokeny uživatele v Azure Active Directory. |
| microsoft.aad.directory/User/Restore | Umožňuje obnovovat odstraněné uživatele v Azure Active Directory. |
| microsoft.aad.directory/User/Update | Umožňuje aktualizovat standardní vlastnosti v Users v Azure Active Directory. |
| microsoft.aad.directory/User/Update/AppRoleAssignments | Umožňuje aktualizovat vlastnost Users.AppRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/User/Update/Manager | Umožňuje aktualizovat vlastnost Users.Manager v Azure Active Directory. |
| microsoft.aad.directory/User/Update/Password | Aktualizace hesla pro všechny uživatele ve službě Azure Active Directory. Najdete v online dokumentaci pro více podrobností. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Umožňuje číst a konfigurovat stav služby Office 365. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Umožňuje vytvářet a spravovat lístky podpory Office 365. |

### <a name="power-bi-service-administrator"></a>Správce služeb Power BI
Může spravovat všechny aspekty produktu Power BI.

  > [!NOTE]
  > Další oprávnění dědí tato role [role uživatele](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Tato role má dodatečná oprávnění mimo službu Azure Active Directory. Zobrazit popis role výše pro další informace.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Umožňuje číst vlastnost Organizations.TrustedCAsForPasswordlessAuth v Azure Active Directory. |
| microsoft.aad.accessservice/AllEntities/AllActions | Umožňuje vytvářet a odstraňovat všechny prostředky a číst a aktualizovat standardní vlastnosti v Azure Access Control. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Umožňuje číst a konfigurovat stav služby Office 365. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Umožňuje vytvářet a spravovat lístky podpory Office 365. |
| microsoft.powerbi/AllEntities/AllActions | Umožňuje spravovat všechny aspekty Power BI. |

### <a name="privileged-role-administrator"></a>Správce privilegované role
Můžou Spravovat přiřazení rolí ve službě Azure AD

  > [!NOTE]
  > Další oprávnění dědí tato role [role uživatele](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Tato role má dodatečná oprávnění mimo službu Azure Active Directory. Zobrazit popis role výše pro další informace.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/DirectoryRole/Update | Umožňuje aktualizovat standardní vlastnosti v DirectoryRoles v Azure Active Directory. |
| microsoft.aad.privilegedrolemanagement/AllEntities/AllActions | Umožňuje spravovat všechny aspekty služby Privileged Role Management. |

### <a name="security-administrator"></a>Správce zabezpečení
Může číst informace o zabezpečení a sestavy

  > [!NOTE]
  > Další oprávnění dědí tato role [role uživatele](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Tato role má dodatečná oprávnění mimo službu Azure Active Directory. Zobrazit popis role výše pro další informace.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/Application/Update/DefaultPolicy | Umožňuje aktualizovat vlastnost Applications.DefaultPolicy v Azure Active Directory. |
| microsoft.aad.directory/Policy/Create | Umožňuje vytvářet Policies v Azure Active Directory. |
| microsoft.aad.directory/Policy/Delete | Umožňuje odstraňovat Policies v Azure Active Directory. |
| microsoft.aad.directory/Policy/Update | Umožňuje aktualizovat standardní vlastnosti v Policies v Azure Active Directory. |
| microsoft.aad.directory/Policy/Update/Owners | Umožňuje aktualizovat vlastnost Policies.Owners v Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/DefaultPolicy | Umožňuje aktualizovat vlastnost ServicePrincipals.DefaultPolicy v Azure Active Directory. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Umožňuje číst vlastnost Organizations.TrustedCAsForPasswordlessAuth v Azure Active Directory. |
| microsoft.aad.accessservice/AllEntities/AllActions | Umožňuje vytvářet a odstraňovat všechny prostředky a číst a aktualizovat standardní vlastnosti v Azure Access Control. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Umožňuje číst a konfigurovat stav služby Office 365. |
| microsoft.aad.privilegedrolemanagement/AllEntities/Read | Umožňuje číst všechny aspekty služby Privileged Identity Management. |
| microsoft.protectioncenter/AllEntities/Read | Umožňuje číst všechny aspekty Centra ochrany Office 365. |
| microsoft.protectioncenter/AllEntities/Update | Umožňuje spravovat Centrum ochrany Office 365. |

### <a name="reports-reader"></a>Čtenář sestav
Může číst sestavy o přihlašování a auditech.

  > [!NOTE]
  > Tato role dědí z role ke čtení adresáře další oprávnění.
  >
  >

  > [!NOTE]
  > Tato role má dodatečná oprávnění mimo službu Azure Active Directory. Zobrazit popis role výše pro další informace.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.reports/AllEntities/Read | Umožňuje číst sestavy Azure AD. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Umožňuje číst a konfigurovat stav služby Office 365. |
| Microsoft.Office365.usagereports/AllEntities/Read | Umožňuje číst sestavy využití Office 365. |

### <a name="security-reader"></a>Čtecí zařízení pro zabezpečení
Může číst informace o zabezpečení a sestavy v Azure AD a Office 365.

  > [!NOTE]
  > Tato role dědí z role ke čtení adresáře další oprávnění.
  >
  >

  > [!NOTE]
  > Tato role má dodatečná oprávnění mimo službu Azure Active Directory. Zobrazit popis role výše pro další informace.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Umožňuje číst vlastnost Organizations.TrustedCAsForPasswordlessAuth v Azure Active Directory. |
| microsoft.aad.accessservice/AllEntities/AllActions | Umožňuje vytvářet a odstraňovat všechny prostředky a číst a aktualizovat standardní vlastnosti v Azure Access Control. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Umožňuje číst a konfigurovat stav služby Office 365. |
| microsoft.privilegedidentitymanagement/AllEntities/Read | Umožňuje číst všechny aspekty služby Privileged Identity Management. |
| microsoft.protectioncenter/AllEntities/Read | Umožňuje číst všechny aspekty Centra ochrany Office 365. |

### <a name="service-support-administrator"></a>Správce podpory služeb
Může číst informace o stavu služby a spravovat lístky podpory.

  > [!NOTE]
  > Další oprávnění dědí tato role [role uživatele](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Tato role má dodatečná oprávnění mimo službu Azure Active Directory. Zobrazit popis role výše pro další informace.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Umožňuje číst vlastnost Organizations.TrustedCAsForPasswordlessAuth v Azure Active Directory. |
| microsoft.aad.accessservice/AllEntities/AllActions | Umožňuje vytvářet a odstraňovat všechny prostředky a číst a aktualizovat standardní vlastnosti v Azure Access Control. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Umožňuje vytvářet a spravovat lístky podpory Office 365. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Umožňuje číst a konfigurovat stav služby Office 365. |

### <a name="sharepoint-service-administrator"></a>Správce služby SharePoint
Může spravovat všechny aspekty služby SharePoint.

  > [!NOTE]
  > Další oprávnění dědí tato role [role uživatele](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Tato role má dodatečná oprávnění mimo službu Azure Active Directory. Zobrazit popis role výše pro další informace.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | Umožňuje vytvářet a odstraňovat všechny prostředky a číst a aktualizovat standardní vlastnosti v Azure Access Control. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Umožňuje číst a konfigurovat stav služby Office 365. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Umožňuje vytvářet a spravovat lístky podpory Office 365. |
| microsoft.sharepoint/AllEntities/AllActions | Umožňuje spravovat SharePoint Online. |

### <a name="user-account-administrator"></a>Správce uživatelských účtů
Může spravovat všechny aspekty uživatelů a skupin

  > [!NOTE]
  > Další oprávnění dědí tato role [role uživatele](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/AppRoleAssignment/Create | Umožňuje vytvářet AppRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/AppRoleAssignment/Delete | Umožňuje odstraňovat AppRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/AppRoleAssignment/Update | Umožňuje aktualizovat standardní vlastnosti v AppRoleAssigments v Azure Active Directory. |
| microsoft.aad.directory/Contact/Create | Umožňuje vytvářet Contacts v Azure Active Directory. |
| microsoft.aad.directory/Contact/Delete | Umožňuje odstraňovat Contacts v Azure Active Directory. |
| microsoft.aad.directory/Contact/Update | Umožňuje aktualizovat standardní vlastnosti v Contacts v Azure Active Directory. |
| microsoft.aad.directory/Group/Create | Umožňuje vytvářet Groups v Azure Active Directory. |
| microsoft.aad.directory/Group/CreateAsOwner | Umožňuje vytvářet Groups v Azure Active Directory. Autor se přidá jako první vlastník a vytvořený objekt počítat do kvóty 250 vytvořených objektů autora. |
| microsoft.aad.directory/Group/Delete | Umožňuje odstraňovat Groups v Azure Active Directory. |
| microsoft.aad.directory/Group/Read | Umožňuje číst standardní vlastnosti Groups v Azure Active Directory. |
| microsoft.aad.directory/Group/Read/HiddenMembers | Umožňuje číst vlastnost Groups.HiddenMembers v Azure Active Directory. |
| microsoft.aad.directory/Group/Restore | Umožňuje obnovovat Groups v Azure Active Directory. |
| microsoft.aad.directory/Group/Update | Umožňuje aktualizovat standardní vlastnosti v Groups v Azure Active Directory. |
| microsoft.aad.directory/Group/Update/AppRoleAssignments | Umožňuje aktualizovat vlastnost Groups.AppRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Members | Umožňuje aktualizovat vlastnost Groups.Members v Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Owners | Umožňuje aktualizovat vlastnost Groups.Owners v Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Settings | Umožňuje aktualizovat vlastnost Groups.Settings v Azure Active Directory. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Umožňuje číst vlastnost Organizations.TrustedCAsForPasswordlessAuth v Azure Active Directory. |
| microsoft.aad.directory/User/AssignLicense | Umožňuje spravovat licence uživatelů v Azure Active Directory. |
| microsoft.aad.directory/User/Create | Umožňuje vytvářet Users v Azure Active Directory. |
| microsoft.aad.directory/User/Delete | Umožňuje odstraňovat Users v Azure Active Directory. |
| microsoft.aad.directory/User/InvalidateAllRefreshTokens | Umožňuje zneplatnit všechny obnovovací tokeny uživatele v Azure Active Directory. |
| microsoft.aad.directory/User/Restore | Umožňuje obnovovat odstraněné uživatele v Azure Active Directory. |
| microsoft.aad.directory/User/Update | Umožňuje aktualizovat standardní vlastnosti v Users v Azure Active Directory. |
| microsoft.aad.directory/User/Update/AppRoleAssignments | Umožňuje aktualizovat vlastnost Users.AppRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/User/Update/Manager | Umožňuje aktualizovat vlastnost Users.Manager v Azure Active Directory. |
| microsoft.aad.directory/User/Update/PasswordUserAcctAdminScope | Aktualizace hesla omezených správců, správců helpdesku a správcům další uživatelský účet ve službě Azure Active Directory. Najdete v online dokumentaci pro více podrobností. |
| microsoft.aad.accessservice/AllEntities/AllActions | Umožňuje vytvářet a odstraňovat všechny prostředky a číst a aktualizovat standardní vlastnosti v Azure Access Control. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Umožňuje vytvářet a spravovat lístky podpory Office 365. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Umožňuje číst a konfigurovat stav služby Office 365. |

## <a name="next-steps"></a>Další postup

* Další informace o tom, jak změnit správce pro předplatné služby Azure naleznete v tématu [Postup přidání nebo změna role správce služby Azure](../../billing/billing-add-change-azure-subscription-administrator.md)
* Další informace o tom, jak se přístup k prostředkům řídí ve službě Microsoft Azure, najdete v části [Principy přístupu k prostředkům ve službě Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Další informace o vztahu Azure Active Directory k předplatnému Azure najdete v tématu [Jak je předplatné Azure propojeno se službou Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md).
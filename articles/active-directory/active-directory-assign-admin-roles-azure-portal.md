---
title: Přiřazení rolí správce v Azure Active Directory | Microsoft Docs
description: Roli správce můžete přidat uživatele, přiřadit role pro správu, resetovat uživatelská hesla, spravovat uživatelské licence nebo spravovat domény. Uživatel, který je přiřazen roli správce má stejné oprávnění mezi všechny cloudové služby, na které má vaše organizace předplatné.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 03/15/2018
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: fc7a67c644894606b09ed3ad594ff9f87b29f307
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="assigning-administrator-roles-in-azure-active-directory"></a>Přiřazení rolí správce v Azure Active Directory

Pomocí služby Azure Active Directory (Azure AD), můžete určit samostatné správcům poskytovat různé funkce. Správci mají přístup k různým funkcím v portálu Azure a v závislosti na jejich role, můžete vytvořit nebo upravit uživatele, přiřadit role správců jiným uživatelům, resetovat uživatelská hesla, spravovat uživatelské licence a správě domén, mimo jiné. Uživatel, který je přiřazen roli správce bude mít stejné oprávnění ve všech cloudových služeb, na které má vaše organizace předplatné, bez ohledu na to, jestli přiřadíte roli na portálu Office 365 nebo portálu Azure nebo pomocí modulu Azure AD pro Prostředí Windows PowerShell.

## <a name="details-about-the-global-administrator-role"></a>Podrobnosti o roli globálního správce
Globální správce má přístup ke všem funkcím pro správu. Ve výchozím nastavení osoba, která se zaregistruje pro předplatné služby Azure má přiřazenou roli globálního správce pro adresář. Jenom globální správci můžete přiřadit další role správců.

## <a name="assign-or-remove-administrator-roles"></a>Přiřazení nebo odebrání role správce
Naučte se přiřazovat správní role pro uživatele v Azure Active Directory, najdete v tématu [přiřazení uživatele k rolí správce ve službě Azure Active Directory](active-directory-users-assign-role-azure-portal.md).

## <a name="available-roles"></a>Dostupné role
Následující role správce jsou k dispozici:

* **Správce fakturace**: může dělat nákupy, spravovat předplatná, spravovat lístky žádostí o podporu a sledovat stav služeb.

* **Správce dodržování předpisů**: uživatelé s touto rolí mají oprávnění pro správu v rámci v Office 365 zabezpečení & centru dodržování předpisů a Centrum pro správu Exchange. Další informace "[o rolích správce Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)."

* **Správce podmíněného přístupu**: uživatelé s touto rolí mají možnost spravovat nastavení podmíněného přístupu k Azure Active Directory.
  > [!NOTE]
  > Nasadit zásady podmíněného přístupu Exchange ActiveSync v Azure, uživatel musí být také globálního správce.
  
* **Správce služeb CRM**: uživatelé s touto rolí mají globální oprávnění v rámci aplikace Microsoft CRM Online, když se služba nachází, a také možnost spravovat lístky žádostí o podporu a sledovat stav služeb. Další informace v [role Správce služeb Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **Správci zařízení**: uživatelé s touto rolí stane správci místního počítače na všech zařízeních Windows 10, které jsou připojeny k Azure Active Directory. Nemají možnost spravovat objekty zařízení v Azure Active Directory.

* **Directory čtečky**: to je starší verze role, která má být přiřazena k aplikacím, které nepodporují [souhlas Framework](active-directory-integrating-applications.md). Neměla být přiřazena pro všechny uživatele.

* **Účty synchronizace adresáře**: nepoužívejte. Tato role se automaticky přiřadí k službě Azure AD Connect a není určena nebo podporována pro jiné použití.

* **Directory zapisovače**: to je starší verze role, která má být přiřazena k aplikacím, které nepodporují [souhlas Framework](active-directory-integrating-applications.md). Neměla být přiřazena pro všechny uživatele.

* **Správce služby Exchange**: uživatelé s touto rolí mají globální oprávnění v rámci Microsoft Exchange Online, když se služba nachází. Další informace v [role Správce služeb Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **Globální správce nebo správce společnosti**: uživatelé s touto rolí mají přístup ke všem funkcím pro správu v Azure Active Directory, stejně jako služby, které vytvořit federaci s Azure Active Directory, jako je Exchange Online, SharePoint Online, a Online Skype pro firmy. Osoba, která se zaregistruje klienta Azure Active Directory se změní na globálního správce. Jenom globální správci můžete přiřadit další role správců. Ve vaší společnosti může být více než jeden globální správce. Globální správci můžete resetovat heslo pro všechny uživatele a všechny ostatní správce.

  > [!NOTE]
  > Microsoft Graph API, Azure AD Graph API a Azure AD PowerShell je tato role označený "Správce společnosti". Je "Globální správce" v [portál Azure](https://portal.azure.com).
  >
  >

* **Pozvání hosta odeslal**: uživatelům v této roli můžete spravovat pozvánek uživatele Azure Active Directory s B2B hosta uživatelské nastavení "Členy můžete pozvat" je nastavena na Ne. Další informace o spolupráci B2B v [spolupráce o B2B Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Neobsahuje žádné další oprávnění.

* **Správce informačních ochrany**: uživatelé s touto rolí mají uživatelská práva pouze ve službě Azure Information Protection. Jejich nejsou udělena práva uživatele v Centru pro ochranu Identity, Privileged Identity Management, stavu služby Office 365 monitorování, nebo zabezpečení Office 365 a centru dodržování předpisů. Můžete nakonfigurovat popisky zásad Azure Information Protection, správě ochrany šablon a aktivujte ochranu.

* **Správce služby Intune**: uživatelé s touto rolí mají globální oprávnění v rámci Microsoft Intune Online, když se služba nachází. Kromě toho tato role zahrnuje schopnost spravovat uživatele a zařízení, aby bylo možné přidružit zásady, a také vytvářet a spravovat skupiny.

* **Poštovní schránky správce**: Tato role se používá pouze v rámci Exchange Online e-mailová podpora pro RIM Blackberry zařízení. Pokud vaše organizace nepoužívá Exchange Online e-mailu na zařízeních RIM Blackberry, nepoužívejte tuto roli.

* **Partner podpora vrstvy 1**: nepoužívejte. Tato role se už nepoužívá a bude odebrána z Azure AD v budoucnu. Tato role je určená pro malý počet prodej partnery společnosti Microsoft a není určena pro obecné použití.

* **Partner podpory vrstvy 2**: nepoužívejte. Tato role se už nepoužívá a bude odebrána z Azure AD v budoucnu. Tato role je určená pro malý počet prodej partnery společnosti Microsoft a není určena pro obecné použití.

* **Heslo správce nebo správce technické podpory**: uživatelé s touto rolí můžete změnit hesla, spravovat žádosti o služby a sledovat stav služeb. Správci technické podpory, můžete změnit hesla jenom uživatelům a jiným správcům technickou podporu. 

  > [!NOTE]
  > V Microsoft Graph API, Azure AD Graph API a Azure AD PowerShell tato role je označeno "Helpdesk Administrator". Je "Heslo správce" v [portál Azure](https://portal.azure.com/).
  >
  >
  
* **Správce služby Power BI**: uživatelé s touto rolí mají globální oprávnění v rámci Microsoft Power BI, když se služba nachází, a také možnost spravovat lístky žádostí o podporu a sledovat stav služeb. Další informace v [role Správce služeb Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d?ui=en-US&rs=en-001&ad=US).

* **Privilegované Role správce**: uživatelé s touto rolí můžete spravovat přiřazení rolí v Azure Active Directory, a také v rámci Azure AD Privileged Identity Management. Kromě toho tato role umožňuje správu všech aspektů Privileged Identity Management.

* **Sestavy čtečky**: uživatelé k této roli mohou zobrazit využití generování sestav dat a řídicí panel sestavy v Centru pro správu Office 365 a kontext přijetí pack v PowerBI. Kromě toho role poskytuje přístup k přihlašování sestavy a aktivity ve službě Azure AD a vykazování dat vrácených Microsoft Graph API. Uživatel přiřazený k roli čtečky sestavy můžete přístup pouze relevantní využití a přijetí metriky. Nemají žádné oprávnění správce pro konfiguraci nastavení nebo přístupu, kterou centra pro správu konkrétní produkt jako je Exchange. 

* **Správce zabezpečení**: uživatelé s touto rolí mají všechna oprávnění jen pro čtení čtečky role zabezpečení a možnosti správy konfigurace pro služby související se zabezpečením: Azure Active Directory Identity Protection, Azure Information Protection, Správa privilegovaných identit a zabezpečení Office 365 a centru dodržování předpisů. Další informace o oprávněních Office 365 je k dispozici na [oprávnění v Office 365 zabezpečení a dodržování předpisů Center](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

* **Zabezpečení čtečky**: uživatelé s touto rolí mají globální přístup jen pro čtení, včetně všech informací v Azure Active Directory, ochrany identit, Privileged Identity Management, jakož i možnost číst Azure Active Directory přihlášení sestavy a protokoly auditu. Role uděluje také oprávnění jen pro čtení v centru dodržování předpisů a zabezpečení Office 365. Další informace o oprávněních Office 365 je k dispozici na [oprávnění v Office 365 zabezpečení a dodržování předpisů Center](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

* **Správce podpory služeb**: uživatelé s touto rolí můžete otevřít žádosti o podporu se společností Microsoft pro služby Azure a Office 365 a zobrazení řídicího panelu služby a zpráva center na portálu Azure a portálu pro správu Office 365. Další informace v [role Správce služeb Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **Správce služby SharePoint**: uživatelé s touto rolí mají globální oprávnění v rámci služby Microsoft SharePoint Online, když se služba nachází, a také možnost spravovat lístky žádostí o podporu a sledovat stav služeb. Další informace v [role Správce služeb Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **Skype pro firmy nebo správce služby Lync**: uživatelé s touto rolí mají globální oprávnění v rámci Microsoft Skype pro firmy, když se služba nachází, a také spravovat uživatelské atributy specifické pro Skype ve službě Azure Active Directory. Kromě toho tato role poskytuje možnost spravovat lístky žádostí o podporu a sledovat stav služeb. Další informace v [role Správce služeb Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

  > [!NOTE]
  > Microsoft Graph API, Azure AD Graph API a Azure AD PowerShell je tato role označený "Správce služby Lync". Je "Skype pro firmy Správce služby" v [portál Azure](https://portal.azure.com/).
  >
  >

* **Správce účtu uživatele**: uživatelé k této roli mohou vytvářet a spravovat všechny aspekty uživatelů a skupin. Kromě toho tato role zahrnuje schopnost spravovat lístky žádostí o podporu a monitorování služby stavu. Platí určitá omezení. Například tato role neumožňuje odstraňování globálního správce. Správci účtu uživatele, můžete změnit hesla pro uživatele, správce technické podpory a jiné uživatelský účet správce.

## <a name="administrator-permissions"></a>Oprávnění správce

### <a name="billing-administrator"></a>Správce fakturace

| Můžete provést | Nelze provést |
| --- | --- |
|<p>Zobrazení informací o společnosti a uživatele</p><p>Spravovat lístky žádostí o podporu Office</p><p>Provádět operace fakturace a nákupu produktů Office</p> |<p>Resetovat uživatelská hesla</p><p>Vytvoření a Správa zobrazení uživatele</p><p>Vytvářet, upravovat a odstraňovat uživatele a skupiny a spravovat uživatelské licence</p><p>Spravovat domény</p><p>Spravovat informace o společnosti</p><p>Delegovat role správců jiným uživatelům</p><p>Používat synchronizaci adresářů</p><p>Zobrazení protokolů auditování</p>|

### <a name="conditional-access-administrator"></a>Správce podmíněného přístupu

| Můžete provést | Nelze provést |
| --- | --- |
|<p>Zobrazení informací o společnosti a uživatele</p><p>Spravovat nastavení podmíněného přístupu</p> |<p>Resetovat uživatelská hesla</p><p>Vytvoření a Správa zobrazení uživatele</p><p>Vytvářet, upravovat a odstraňovat uživatele a skupiny a spravovat uživatelské licence</p><p>Spravovat domény</p><p>Spravovat informace o společnosti</p><p>Delegovat role správců jiným uživatelům</p><p>Používat synchronizaci adresářů</p><p>Zobrazení protokolů auditování</p>|

### <a name="global-administrator"></a>Globální správce
| Můžete provést | Nelze provést |
| --- | --- |
|<p>Zobrazení informací o společnosti a uživatele</p><p>Spravovat lístky žádostí o podporu Office</p><p>Provádět operace fakturace a nákupu produktů Office</p><p>Resetovat uživatelská hesla</p><p>Resetovat hesla jiných správce</p> <p>Vytvoření a Správa zobrazení uživatele</p><p>Vytvářet, upravovat a odstraňovat uživatele a skupiny a spravovat uživatelské licence</p><p>Spravovat domény</p><p>Spravovat informace o společnosti</p><p>Delegovat role správců jiným uživatelům</p><p>Používat synchronizaci adresářů</p><p>Povolení nebo zakázání služby Multi-Factor authentication</p><p>Zobrazení protokolů auditování</p> |neuvedeno |

### <a name="password-administrator--helpdesk-administrator"></a>Heslo správce nebo správce technické podpory
| Můžete provést | Nelze provést |
| --- | --- |
| <p>Zobrazení informací o společnosti a uživatele</p><p>Spravovat lístky žádostí o podporu Office</p><p>Změna hesla pro uživatele a další technickou podporu správci</p>|<p>Provádět operace fakturace a nákupu produktů Office</p><p>Vytvoření a Správa zobrazení uživatele</p><p>Vytvářet, upravovat a odstraňovat uživatele a skupiny a spravovat uživatelské licence</p><p>Spravovat domény</p><p>Spravovat informace o společnosti</p><p>Delegovat role správců jiným uživatelům</p><p>Používat synchronizaci adresářů</p><p>Zobrazení sestav</p>|

### <a name="information-protection-administrator"></a>Správce informačních ochrany
V | Můžete provést
-------- | ---------
Azure Information Protection | <li>Konfigurace nastavení a popisky v zásadách globální a oboru<li>Konfigurovat a spravovat šablony ochrany<li>Aktivace nebo deaktivace ochrany--
 
### <a name="reports-reader"></a>Čtečka sestavy 
Můžete provést | Nelze provést
------ | ----------
Zobrazení Azure AD přihlášení sestavy a protokoly auditu<br>Zobrazení informací o společnosti a uživatele<br>Přístup k Office 365 použití řídicího panelu | Vytvoření a Správa zobrazení uživatele<br>Vytvářet, upravovat a odstraňovat uživatele a skupiny a spravovat uživatelské licence<br>Delegovat role správců jiným uživatelům<br>Spravovat informace o společnosti

### <a name="security-reader"></a>Čtecí zařízení pro zabezpečení
| V | Můžete provést |
| --- | --- |
| Centrum Identity Protection |Číst všechny sestavy zabezpečení a informace o nastavení pro funkce zabezpečení<ul><li>Proti spamu<li>Šifrování<li>Zabránění ztrátě dat<li>Anti-malware<li>Pokročilé threat protection<li>Ochrana proti podvodným<li>Mailflow pravidla |
| Privileged Identity Management |<p>Má přístup jen pro čtení ke všem informacím prezentované v Azure AD PIM: zásady a sestav pro Azure AD přiřazení rolí zabezpečení zkontroluje a v budoucnu přístup pro čtení k zásad datům a sestavám pro scénáře kromě přiřazení role Azure AD.<p>**Nelze** registrace pro Azure AD PIM nebo proveďte změny. PIM na portálu nebo pomocí prostředí PowerShell někdo v této roli můžete aktivovat další role (například globální správce nebo správce privilegovaných rolí), pokud je uživatel kandidátem pro ně. |
| <p>Monitorování stavu služby Office 365</p><p>Centru dodržování předpisů a zabezpečení Office 365</p> |<ul><li>Číst a spravovat výstrahy<li>Zásady zabezpečení pro čtení<li>Číst analýzou hrozeb, Cloud App Discovery a karantény v hledání a prošetřit<li>Číst všechny sestavy |

### <a name="security-administrator"></a>Správce zabezpečení
| V | Můžete provést |
| --- | --- |
| Centrum Identity Protection |<ul><li>Všechna oprávnění role zabezpečení čtečky.<li>Kromě toho možnost provádět všechny operace IPC s výjimkou resetování hesla. |
| Privileged Identity Management |<ul><li>Všechna oprávnění role zabezpečení čtečky.<li>**Nelze** spravovat členství v rolích Azure AD nebo nastavení. |
| <p>Monitorování stavu služby Office 365</p><p>Centru dodržování předpisů a zabezpečení Office 365 |<ul><li>Všechna oprávnění role zabezpečení čtečky.<li>Můžete nakonfigurovat všechna nastavení ve funkci Advanced Threat Protection (ochrany proti malwaru a virů, škodlivý konfigurace adresy URL, adresa URL trasování atd.). |

### <a name="service-administrator"></a>Správce služeb
| Můžete provést | Nelze provést |
| --- | --- |
| <p>Zobrazení informací o společnosti a uživatele</p><p>Spravovat lístky žádostí o podporu Office</p> |<p>Resetovat uživatelská hesla</p><p>Provádět operace fakturace a nákupu produktů Office</p><p>Vytvoření a Správa zobrazení uživatele</p><p>Vytvářet, upravovat a odstraňovat uživatele a skupiny a spravovat uživatelské licence</p><p>Spravovat domény</p><p>Spravovat informace o společnosti</p><p>Delegovat role správců jiným uživatelům</p><p>Používat synchronizaci adresářů</p><p>Zobrazení protokolů auditování</p> |

### <a name="user-account-administrator"></a>Správce účtu uživatele
| Můžete provést | Nelze provést |
| --- | --- |
| <p>Zobrazení informací o společnosti a uživatele</p><p>Spravovat lístky žádostí o podporu Office</p><p>Změna hesla pro uživatele, správce technické podpory a jiné uživatelský účet správce</p><p>Vytvoření a Správa zobrazení uživatele</p><p>Vytvořit, upravit a odstraňovat uživatele a skupiny a spravovat uživatelské licence, ale s omezením. Potvrdí nelze odstraňovat globální správce ani vytvářet jiné správce.</p> |<p>Provádět operace fakturace a nákupu produktů Office</p><p>Spravovat domény</p><p>Spravovat informace o společnosti</p><p>Delegovat role správců jiným uživatelům</p><p>Používat synchronizaci adresářů</p><p>Povolení nebo zakázání služby Multi-Factor authentication</p><p>Zobrazení protokolů auditování</p> |

### <a name="to-add-a-colleague-as-a-global-administrator"></a>Chcete-li přidat kolegu o jako globální správce

1. Přihlaste se k [Centrum správy služby Azure Active Directory](https://aad.portal.azure.com) pomocí účtu, který je globálním správcem adresáře klienta.

   ![Otevření centra pro správu azure AD](./media/active-directory-assign-admin-roles-azure-portal/active-directory-admin-center.png)

2. Vyberte **uživatelů a skupin &gt; všichni uživatelé**

3. Vyhledejte uživatele, které chcete označit jako globální správce a otevřete okno pro daného uživatele.

4. V okně uživatele vyberte **Directory role**.
 
5. V okně role adresáře vyberte **globálního správce** role a uložte.

## <a name="deprecated-roles"></a>Nepoužívané role

Následující role není vhodné používat. Budou se nepoužívá a bude odebrána z Azure AD v budoucnu.

* Správce ad hoc licencí
* Tvůrce uživatelů ověřovaných na základě e-mailu
* Připojení zařízení k
* Správci zařízení
* Uživatelé zařízení
* Připojení pracovních zařízení

## <a name="next-steps"></a>Další postup

* Další informace o tom, jak změnit správce pro předplatné služby Azure naleznete v tématu [Postup přidání nebo změna role správce služby Azure](../billing-add-change-azure-subscription-administrator.md)
* Další informace o tom, jak se přístup k prostředkům řídí ve službě Microsoft Azure, najdete v části [Principy přístupu k prostředkům ve službě Azure](../role-based-access-control/rbac-and-directory-admin-roles.md)
* Další informace o tom, jak Azure Active Directory má vztah k předplatnému Azure, najdete v části [asociování předplatných Azure se službou Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
* [Správa uživatelů](active-directory-create-users.md)
* [Správa hesel](active-directory-manage-passwords.md)
* [Správa skupin](active-directory-manage-groups.md)

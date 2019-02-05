---
title: Popisy role správce a oprávnění – Azure Active Directory | Dokumentace Microsoftu
description: Roli správce můžete přidat uživatele, přiřazovat role správců, resetovat hesla uživatelů, spravovat uživatelské licence nebo spravovat domény.
services: active-directory
author: curtand
manager: mtillman
search.appverid: MET150
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: 6fc85bd96294650eb2bbf9495642851ade7c7868
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55731508"
---
# <a name="administrator-role-permissions-in-azure-active-directory"></a>Oprávnění role správce v Azure Active Directory

Pomocí Azure Active Directory (Azure AD), můžete určit samostatné správcům slouží různým funkcím. Správci můžou určené na portálu Azure AD k provádění úlohy, jako je přidání nebo změna uživatelů, přiřazení správních rolí, resetovat hesla uživatelů, Správa uživatelských licencí a správa názvů domén.

Globální správce má přístup ke všem funkcím pro správu. Ve výchozím nastavení je osoba, která uživatel zaregistruje do služby pro předplatné Azure přiřadit roli globálního správce adresáře. Jenom globální správci a správci privilegovaných rolí můžete delegovat role správců.

## <a name="assign-or-remove-administrator-roles"></a>Přiřazení nebo odebrání rolí správce

Zjistěte, jak přiřadit správní role pro uživatele v Azure Active Directory, najdete v článku [zobrazení a přiřazení rolí správce ve službě Azure Active Directory](directory-manage-roles-portal.md).

## <a name="available-roles"></a>Dostupné role

K dispozici jsou následující role správce:

* **[Správce aplikace](#application-administrator)**: Uživatelé v této roli mohou vytvářet a spravovat všechny aspekty podnikové aplikace, registrace aplikací a nastavení proxy aplikací. Tato role také umožňuje udělovat souhlas delegovaná oprávnění a oprávnění aplikací bez Microsoft Graph a Azure AD Graph. Členové této role nebudou přidány jako vlastníky, při vytváření aplikace v kterémkoli nebo podnikové aplikace.

  <b>Důležité</b>: Tato role uděluje možnost spravovat přihlašovací údaje aplikací. Uživatelé s touto rolí můžete přidat přihlašovací údaje k aplikaci a používat tyto přihlašovací údaje k zosobnění aplikace identity. Pokud aplikace byl identitě udělen přístup do služby Azure Active Directory, jako je například schopnost vytvořit nebo aktualizovat uživatele nebo jiné objekty, pak uživatel přiřazený k této roli může provádět tyto akce při zosobňování aplikace. Tato schopnost zosobnit identitu aplikace může být zvýšení oprávnění prostřednictvím co může uživatel provést prostřednictvím svá přiřazení rolí ve službě Azure AD. Je důležité pochopit, že přiřazení uživatele k roli správce aplikace dává jim možnost zosobnit identitu aplikace.

* **[Vývojář aplikace](#application-developer)**: Uživatelé s touto rolí může vytvářet registrace aplikací při "Uživatelé můžou registrovat aplikace" nastavená na Ne. Tato role také umožňuje členům vyjádřili souhlas na vlastním jménem při "Uživatelé můžou udělit souhlas s aplikací, které přistupují k firemním datům jejich jménem" nastavená na Ne. Členové této role jsou přidány jako vlastníky, při vytváření aplikace v kterémkoli nebo podnikové aplikace.

* **[Správce ověřování](#authentication-administrator)**: Uživatelé s touto rolí můžete zobrazit aktuální informace o metodě ověřování a nastavit nebo resetovat přihlašovací údaje bez hesla. Správci ověřování můžete vynutit uživatelům přeregistrovat na existující bez hesla k přihlášení (třeba vícefaktorové ověřování, FIDO) dokumentů a odvolání přístupu "zapamatovat MFA na zařízení", s výzvou pro vícefaktorové ověřování při dalším přihlášení jiných uživatelů, kteří nejsou správci, nebo členy z pouze následující role:
  * Správce ověření
  * Uživatelé s oprávněním ke čtení adresářů
  * Odesílatel pozvánky hostů
  * Čtenář Centra zpráv
  * Čtenář sestav
  
  <b>Důležité</b>: Uživatelé s touto rolí může změnit přihlašovací údaje pro uživatele, kteří můžou mít přístup k citlivým nebo soukromých informací nebo kritické konfigurace uvnitř i mimo Azure Active Directory. Změna přihlašovacích údajů uživatele, může to znamenat schopnost předpokládat identit a oprávnění daného uživatele. Příklad:
  * Registrace aplikace a podniková aplikace vlastníky, kteří můžou spravovat aplikace, které vlastní přihlašovací údaje. Tyto aplikace mohou mít Privilegovaná oprávnění ve službě Azure AD a jinam nikoli udělit správcům ověřování. Pomocí této cesty, Správce ověřování může být schopen převzít identitu vlastníka aplikace a pak dále převzít identitu privilegovaného aplikace stačí aktualizovat přihlašovací údaje pro aplikaci.
  * Vlastníci předplatného Azure, kteří můžou mít přístup k citlivým nebo soukromých informací nebo kritické konfigurace v Azure.
  * Vlastníci skupiny zabezpečení a skupiny Office 365, kteří můžou spravovat členství ve skupině. Těmto skupinám udělit přístup k citlivým nebo soukromých informací nebo kritické konfigurace ve službě Azure AD a jinde.
  * Správci v dalších službách mimo Azure AD, jako jsou systémy Exchange Online, Office zabezpečení a dodržování předpisů System Center a lidské zdroje.
  * Všichni uživatelé, jako jsou vedoucí pracovníci, právním poradcem a lidské zdroje zaměstnanců, kteří můžou mít přístup k citlivým nebo soukromých informací.

* **[Správce fakturace](#billing-administrator)**: Nakupuje, spravuje předplatná, spravuje lístky podpory a monitoruje stav služby.

* **[Správce cloudové aplikace](#cloud-application-administrator)**: Uživatelé v této roli mají stejná oprávnění jako role správce aplikace s výjimkou schopnost spravovat proxy aplikací. Tato role uděluje možnost vytvářet a spravovat všechny aspekty podnikové aplikace a registrace aplikací. Tato role také umožňuje udělovat souhlas delegovaná oprávnění a oprávnění aplikací bez Microsoft Graph a Azure AD Graph. Členové této role nebudou přidány jako vlastníky, při vytváření aplikace v kterémkoli nebo podnikové aplikace.

  <b>Důležité</b>: Tato role uděluje možnost spravovat přihlašovací údaje aplikací. Uživatelé s touto rolí můžete přidat přihlašovací údaje k aplikaci a používat tyto přihlašovací údaje k zosobnění aplikace identity. Pokud aplikace byl identitě udělen přístup do služby Azure Active Directory, jako je například schopnost vytvořit nebo aktualizovat uživatele nebo jiné objekty, pak uživatel přiřazený k této roli může provádět tyto akce při zosobňování aplikace. Tato schopnost zosobnit identitu aplikace může být zvýšení oprávnění prostřednictvím co může uživatel provést prostřednictvím svá přiřazení rolí ve službě Azure AD. Je důležité pochopit, že přiřazení uživatele k roli správce cloudové aplikace dává jim možnost zosobnit identitu aplikace.

* **[Správce cloudových zařízení](#cloud-device-administrator)**: Uživatelé v této roli můžou povolit, zakázat a odstraňovat zařízení ve službě Azure AD a čtení klíče nástroje BitLocker systému Windows 10 (pokud existuje) na webu Azure Portal. Role nejsou udělena oprávnění ke správě jiných vlastností v zařízení.

* **[Správce dodržování předpisů](#compliance-administrator)**: Uživatelé s touto rolí mají oprávnění ke správě funkce související s dodržování předpisů v centru dodržování předpisů Microsoft 365, centra pro správu služeb Microsoft 365, Azure a zabezpečení Office 365 a centru dodržování předpisů. Uživatelé mohou také spravovat všechny funkce v rámci centra pro správu Exchange a týmy a Skype pro firmy centra pro správu a vytvářet lístky podpory pro Azure a Microsoft 365. Další informace jsou k dispozici v [role správců Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

  V | Můžete provést
  ----- | ----------
  [Centrum pro dodržování předpisů Microsoft 365](https://protection.microsoft.com) | Chránit a spravovat data vaší organizace v rámci služeb Microsoft 365<br>Správa výstrah dodržování předpisů
  [Správce dodržování předpisů](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Sledování, přiřaďte a ověřte aktivit dodržování legislativních předpisů vaší organizace
  [Office 365 zabezpečení a dodržování předpisů](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Správa zásad správného řízení dat<br>Proveďte šetření právní informace a data<br>Spravovat žádost o subjektu dat
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | Zobrazit všechna data auditu Intune
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Má oprávnění jen pro čtení a může spravovat upozornění<br>Můžete vytvořit a upravit zásady souborů a povolit akce zásad správného řízení souborů<br> Můžete zobrazit všechny předdefinované sestavy v správy dat

<!--* **[Compliance Data Administrator](#compliance-data-administrator)**: Users with this role have permissions to protect and track data in the Microsoft 365 compliance center, Microsoft 365 admin center, and Azure. Users can also manage all features within the Exchange admin center, Compliance Manager, and Teams & Skype for Business admin center and create support tickets for Azure and Microsoft 365.

  In | Can do
  ----- | ----------
  [Microsoft 365 compliance center](https://protection.microsoft.com) | Monitor compliance-related policies across Microsoft 365 services<br>Manage compliance alerts
  [Compliance Manager](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Track, assign, and verify your organization's regulatory compliance activities
  [Office 365 Security & Compliance Center](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Manage data governance<br>Perform legal and data investigation<br>Manage Data Subject Request
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | View all Intune audit data
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Has read-only permissions and can manage alerts<br>Can create and modify file policies and allow file governance actions<br> Can view all the built-in reports under Data Management
-->
* **[Správce podmíněného přístupu](#conditional-access-administrator)**: Uživatelé s touto rolí mají možnost spravovat nastavení podmíněného přístupu Azure Active Directory.
  > [!NOTE]
  > Pokud chcete nasadit zásady podmíněného přístupu Exchange ActiveSync v Azure, musí uživatel také být globálním správcem.
  
* **[Správci zařízení](#device-administrators)**: Tato role je k dispozici pro přiřazení pouze jako další místní správce v [nastavení zařízení](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/). Uživatelé s touto rolí se na všech zařízeních s Windows 10, která jsou připojená k Azure Active Directory, stávají správci místních počítačů. Nemají možnost spravovat objekty zařízení v Azure Active Directory. 

* **[Uživatelé Čtoucí z adresáře](#directory-readers)**: Toto je starší verze role, která má být přiřazena k aplikacím, které nepodporují [souhlas Framework](../develop/quickstart-v1-integrate-apps-with-azure-ad.md). Neměla být přiřazena k žádným uživatelům.

* **[Účty pro synchronizaci adresářů](#directory-synchronization-accounts)**: Nepoužívejte. Tato role se automaticky přiřadí ke službě Azure AD Connect a není určena nebo nepodporuje pro jiné použití.

* **[Uživatelé zapisující do adresáře](#directory-writers)**: Toto je starší verze role, která má být přiřazena k aplikacím, které nepodporují [souhlas Framework](../develop/quickstart-v1-integrate-apps-with-azure-ad.md). Neměla být přiřazena k žádným uživatelům.

* **[Správce Dynamics 365 nebo správce CRM](#crm-service-administrator)**: Uživatelé s touto rolí mají globální oprávnění ve službě Microsoft Dynamics 365 Online, když služba používá, a možnost spravovat lístky podpory a monitorovat stav služby. Další informace na [použít roli Správce služby ke správě svého tenanta](https://docs.microsoft.com/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant).
  > [!NOTE] 
  > V rozhraní Microsoft Graph API, Azure AD Graph API a Azure AD PowerShell tato role nazývá "Správce služby Dynamics 365". Je "Dynamics 365 správce" v [webu Azure portal](https://portal.azure.com).

* **[Správce Exchange](#exchange-service-administrator)**: Uživatelé s touto rolí mají globální oprávnění ve službě Microsoft Exchange Online, pokud se tato služba používá. a také možnost vytvářet a spravovat všechny skupiny Office 365 spravovat lístky podpory a monitorovat stav služby. Další informace na [role správců Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).
  > [!NOTE]
  > V rozhraní Microsoft Graph API, Azure AD Graph API a Azure AD PowerShell tato role je označena jako "Správce služby Exchange". Je "Správce systému Exchange" v [webu Azure portal](https://portal.azure.com).

* **[Globální správce / správce společnosti](#company-administrator)**: Uživatelé s touto rolí mají přístup ke všem funkcím pro správu v Azure Active Directory, jakož i služeb, které používají identity Azure Active Directory jako Centrum zabezpečení Microsoft 365, Microsoft 365 centru dodržování předpisů, Exchange Online, SharePoint Online, a Online Skype pro firmy. Osoba, která se zaregistruje k tenantovi Azure Active Directory se stane globálním správcem. Další role správců můžou přiřazovat jenom globální správci. Ve vaší společnosti může být více než jednoho globálního správce. Globální správci můžou resetovat heslo kteréhokoliv uživatele a všech ostatních správců.

  > [!NOTE]
  > V rozhraní Microsoft Graph API, Azure AD Graph API a Azure AD PowerShell tato role nazývá "Správce společnosti". Je "Globální správce" v [webu Azure portal](https://portal.azure.com).
  >
  >

* **[Odesílatel pozvánky hostů](#guest-inviter)**: Uživatelé v této roli můžou spravovat pozvánky uživatelů typu Host Azure Active Directory s B2B při **členové můžou posílat pozvánky** uživatelské nastavení je Ne. Další informace o spolupráci B2B v [o aplikaci Azure AD B2B collaboration](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Nezahrnuje žádné jiné oprávnění.

* **[Správce Information Protection](#information-protection-administrator)**: Uživatelé s touto rolí mají všechna oprávnění ve službě Azure Information Protection. Tato role umožňuje konfigurace popisků pro zásady Azure Information Protection, spravovat šablony ochrany a zapnutí ochrany. Tato role neuděluje žádná oprávnění v Centrum Identity Protection, Privileged Identity Management, monitorování Office 365 Service Health, nebo Office 365 Centru zabezpečení a dodržování předpisů.

* **[Správce Intune](#intune-service-administrator)**: Uživatelé s touto rolí mají globální oprávnění v Microsoft Intune Online, pokud služba není k dispozici. Kromě toho tato role obsahuje možnost spravovat uživatele a zařízení, aby bylo možné přiřazovat zásady, jakož i vytvářet a spravovat skupiny. Další informace na [řízení správy na základě rolí (RBAC) v Microsoft Intune](https://docs.microsoft.com/intune/role-based-access-control)
  > [!NOTE]
  > V rozhraní Microsoft Graph API, Azure AD Graph API a Azure AD PowerShell tato role je označena jako "Správce služby Intune". Je "Správce Intune" v [webu Azure portal](https://portal.azure.com).

* **[Správce licencí](#license-administrator)**: Uživatelé v této roli můžou přidávat, odebrat a aktualizaci licencí přiřazení na uživatele, skupiny (použití skupinové licence) a spravovat místo využívání na uživatele. Role nejsou udělena možnost nákupu nebo spravovat předplatná, vytvořit nebo spravovat skupiny, nebo vytvořit nebo spravovat uživatele nad rámec místo využívání. Tato role nemá přístup k zobrazení, vytvořit nebo spravovat lístky podpory.

* **[Čtenář Centra zpráv](#message-center-reader)**: Uživatelé v této roli můžete sledovat oznámení a poradenství stavu aktualizace v [Centru zpráv Office 365](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093) pro svoji organizaci na nakonfigurované služby, jako je například Exchange, Intune a Microsoft Teams. Centrum zpráv čtečky přijímat týdenního přehledu e-mailu příspěvků, aktualizace a můžete sdílet zpráva center příspěvky ve službách Office 365. Ve službě Azure AD Uživatelé s touto rolí budete mít pouze oprávnění jen pro čtení na služby Azure AD, jako jsou uživatelé a skupiny. Tato role nemá přístup k zobrazení, vytvořit nebo spravovat lístky podpory.

* **[Partnerská podpora úrovně 1](#partner-tier1-support)**: Nepoužívejte. Tato role se už nepoužívá a bude odebrána z Azure AD v budoucnu. Tato role je určen pro malý počet těmito partnery společnosti Microsoft a není určena pro obecné použití.

* **[Partnerská podpora úrovně 2](#partner-tier2-support)**: Nepoužívejte. Tato role se už nepoužívá a bude odebrána z Azure AD v budoucnu. Tato role je určen pro malý počet těmito partnery společnosti Microsoft a není určena pro obecné použití.

* **[Heslo správce / správce technické podpory](#helpdesk-administrator)**: Uživatelé s touto rolí můžou měnit hesla, zneplatnit obnovovací tokeny, spravovat žádosti o služby a monitorovat stav služby. Zrušení platnosti tokenu obnovení vynutí uživatel znovu přihlásil. Správců technické podpory může resetovat hesla a zneplatnit obnovovací tokeny jiných uživatelů, kteří nejsou správci, nebo členy z následujících rolí:
  * Uživatelé s oprávněním ke čtení adresářů
  * Odesílatel pozvánky hostů
  * Správce technické podpory
  * Čtenář Centra zpráv
  * Čtenář sestav
  
  <b>Důležité</b>: Uživatelé s touto rolí můžou měnit hesla pro uživatele, kteří můžou mít přístup k citlivým nebo soukromých informací nebo kritické konfigurace uvnitř i mimo Azure Active Directory. Změna hesla pro uživatele, může to znamenat schopnost předpokládat identit a oprávnění daného uživatele. Příklad:
  * Registrace aplikace a podniková aplikace vlastníky, kteří můžou spravovat aplikace, které vlastní přihlašovací údaje. Tyto aplikace mohou mít Privilegovaná oprávnění ve službě Azure AD a jinam nikoli udělit správců technické podpory. Pomocí této cesty, správce technické podpory může být schopen převzít identitu vlastníka aplikace a pak dále převzít identitu aplikace privileged stačí aktualizovat přihlašovací údaje pro aplikaci.
  * Vlastníci předplatného Azure, kteří můžou mít přístup k citlivým nebo soukromých informací nebo kritické konfigurace v Azure.
  * Vlastníci skupiny zabezpečení a skupiny Office 365, kteří můžou spravovat členství ve skupině. Těmto skupinám udělit přístup k citlivým nebo soukromých informací nebo kritické konfigurace ve službě Azure AD a jinde.
  * Správci v dalších službách mimo Azure AD, jako jsou systémy Exchange Online, Office zabezpečení a dodržování předpisů System Center a lidské zdroje.
  * Všichni uživatelé, jako jsou vedoucí pracovníci, právním poradcem a lidské zdroje zaměstnanců, kteří můžou mít přístup k citlivým nebo soukromých informací.

  > [!NOTE]
  > V rozhraní Microsoft Graph API, Azure AD Graph API a Azure AD PowerShell tato role nazývá "Správce technické podpory". Je "Heslo správce" v [webu Azure portal](https://portal.azure.com/).
  >
  
* **[Správce služby Power BI](#power-bi-service-administrator)**: Uživatelé s touto rolí mají globální oprávnění v Microsoft Power BI, pokud se služba používá, a možnost spravovat lístky podpory a monitorovat stav služby. Další informace na [Principy role správce Power BI](https://docs.microsoft.com/power-bi/service-admin-role).
  > [!NOTE]
  > V rozhraní Microsoft Graph API, Azure AD Graph API a Azure AD PowerShell tato role je označena jako "Správce služby Power BI". Je "správce Power BI" v [webu Azure portal](https://portal.azure.com).

* **[Privilegované Role správce](#privileged-role-administrator)**: Uživatelé s touto rolí můžou Spravovat přiřazení rolí v Azure Active Directory, i v rámci Azure AD Privileged Identity Management. Kromě toho tato role umožňuje spravovat všechny aspekty služby Privileged Identity Management.

  <b>Důležité</b>: Tato role uděluje možnost spravovat členství ve všech rolích Azure AD, včetně rolí globálního správce. Tato role neobsahuje další privilegovaných schopnosti ve službě Azure AD, jako je vytváření nebo aktualizaci uživatelů. Však přiřazena k této roli uživatele můžete udělit sami, nebo jinými další oprávnění přiřazením dalších rolí.

* **[Čtenář sestav](#reports-reader)**: Uživatelé s touto rolí můžou zobrazit vytváření sestav dat využití a sestavy řídicího panelu v Centru pro správu Office 365 a kontext přijetí aktualizací Service pack v Power BI. Kromě toho role poskytuje přístup k přihlášení sestavy a aktivity ve službě Azure AD a dat vrácených Microsoft Graph API pro vytváření sestav. Uživatel přiřazený k roli Čtenář sestav můžete přistupovat pouze relevantní využití a metrik přijetí. Nemají žádná oprávnění správce ke konfiguraci nastavení nebo přístupu centra pro správu určitého produktu jako je Exchange. Tato role nemá přístup k zobrazení, vytvořit nebo spravovat lístky podpory.

* **[Správce zabezpečení](#security-administrator)**: Uživatelé s touto rolí mají oprávnění ke správě funkce související se zabezpečením v Centru zabezpečení Microsoft 365, Azure Active Directory Identity Protection, Azure Information Protection a zabezpečení Office 365 a centru dodržování předpisů. Další informace o oprávněních Office 365 je k dispozici na [oprávnění v centru dodržování předpisů a zabezpečení Office 365](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).
  
  V | Můžete provést
  --- | ---
  [Centrum zabezpečení Microsoft 365](https://protection.microsoft.com) | Monitorování zásad souvisejících se zabezpečením ve službách Microsoft 365<br>Správa ohrožení zabezpečení a upozornění<br>Zobrazení sestav
  Centrum Identity Protection | Všechna oprávnění role Čtenář zabezpečení<br>Kromě toho možnost provádět všechny operace Centrum Identity Protection kromě resetování hesel
  [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Všechna oprávnění role Čtenář zabezpečení<br>**Nelze** spravovat členství v rolích Azure AD nebo nastavení
  [Office 365 zabezpečení a dodržování předpisů](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Správa zásad zabezpečení<br>Zobrazení, prozkoumání a reakce na hrozby zabezpečení<br>Zobrazení sestav
  Rozšířená ochrana před internetovými útoky Azure | Monitorování a reakce na bezpečnostní podezřelé aktivity
  Windows Defender ATP a EDR | Přiřazení rolí<br>Spravovat skupiny počítačů<br>Konfigurace koncového bodu zjišťování hrozeb a automatizovanou nápravu<br>Zobrazení, prozkoumání a reakce na výstrahy
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | Zobrazení uživatele, zařízení, registraci, konfiguraci a informace o aplikaci<br>Nelze provádět změny v Intune
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Přidat správce, přidejte zásady a nastavení, nahrávat protokoly a provádět akce zásad správného řízení
  [Azure Security Center](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | Můžete zobrazit zásady zabezpečení, zobrazení stavu zabezpečení, upravit zásady zabezpečení, zobrazení výstrah a doporučení, Zavřít upozornění a doporučení
  [Stav služby Office 365](https://docs.microsoft.com/office365/enterprise/view-service-health) | Zobrazení stavu služeb Office 365

<!--* **[Security operator](#security-operator)**: Users with this role can manage alerts and have global read-only access on security-related feature, including all information in Microsoft 365 security center, Azure Active Directory, Identity Protection, Privileged Identity Management, as well as the ability to read Azure Active Directory sign-in reports and audit logs, and in Office 365 Security & Compliance Center.

  In | Can do
  --- | ---
  [Microsoft 365 security center](https://protection.microsoft.com) | All permissions of the Security Reader role<br>View, investigate, and respond to security threats alerts
  Identity Protection Center | All permissions of the Security Reader role<br>Additionally, the ability to perform all Identity Protection Center operations except for resetting passwords
  [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | All permissions of the Security Reader role
  [Office 365 Security & Compliance Center](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | All permissions of the Security Reader role<br>View, investigate, and respond to security alerts
  Windows Defender ATP and EDR | All permissions of the Security Reader role<br>View, investigate, and respond to security alerts
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | All permissions of the Security Reader role
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | All permissions of the Security Reader role
  [Office 365 service health](https://docs.microsoft.com/office365/enterprise/view-service-health) | View the health of Office 365 services
-->
* **[Čtenář zabezpečení](#security-reader)**: Uživatelé s touto rolí mají globální přístup jen pro čtení na funkce související se zabezpečením, včetně všech informací v Microsoftu 365 security center, Azure Active Directory, Identity Protection, Privileged Identity Management, jakož i oprávnění ke čtení Azure Active Adresář přihlášení sestavy a protokoly auditu a v centru dodržování předpisů a zabezpečení Office 365. Další informace o oprávněních Office 365 je k dispozici na [oprávnění v centru dodržování předpisů a zabezpečení Office 365](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

  V | Můžete provést
  --- | ---
  [Centrum zabezpečení Microsoft 365](https://protection.microsoft.com) | Zobrazit zásady zabezpečení ve službách Microsoft 365<br>Výstrahy a zobrazení bezpečnostních hrozeb<br>Zobrazení sestav
  Centrum Identity Protection | Číst všechny zprávy o zabezpečení a informace o nastavení zabezpečení funkcí<br><ul><li>Anti-spam<li>Šifrování<li>Prevence ztráty dat<li>Anti-malware<li>Rozšířená ochrana před internetovými útoky<li>Anti phisingová<li>Mailflow pravidla
  [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Má přístup jen pro čtení ke všem informacím prezentované v Azure AD PIM: Zásady a sestav pro přiřazení role Azure AD, bezpečnostní kontroly a v budoucnu přístup pro čtení k zásad dat a sestav pro scénáře kromě přiřazení role Azure AD.<br>**Nelze** registrace pro Azure AD PIM nebo dělat žádné změny. PIM portálu nebo pomocí Powershellu někdo v této roli můžete aktivovat další role (například globální správce nebo správce privilegovaných rolí), pokud je uživatel oprávněné pro ně.
  [Office 365 zabezpečení a dodržování předpisů](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Zobrazení zásad zabezpečení<br>Zobrazení a prošetřování bezpečnostní hrozby<br>Zobrazení sestav
  Windows Defender ATP a EDR | Zobrazení a prošetřování výstrah
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | Zobrazení uživatele, zařízení, registraci, konfiguraci a informace o aplikaci. Nelze provádět změny v Intune.
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Má oprávnění jen pro čtení a může spravovat upozornění
  [Azure Security Center](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | Můžete zobrazit doporučení a výstrahy, zásady zabezpečení, zobrazit stavy zabezpečení, ale nemůže provádět změny zobrazení
  [Stav služby Office 365](https://docs.microsoft.com/office365/enterprise/view-service-health) | Zobrazení stavu služeb Office 365

* **[Správce podpory služeb](#service-support-administrator)**: Uživatelé s touto rolí můžou žádat Microsoft o podporu pro služby Azure a Office 365 a zobrazovat řídicí panel služby a centrum zpráv na portálu Azure Portal a portálu pro správu Office 365. Další informace na [role správců Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).
  > [!NOTE]
  > V rozhraní Microsoft Graph API, Azure AD Graph API a Azure AD PowerShell tato role je označena jako "Správce podpory služeb." Je "Správce služby" v [webu Azure portal](https://portal.azure.com), na portálu pro správu Office 365 a na portálu Intune.

* **[Správce Sharepointu](#sharepoint-service-administrator)**: Uživatelé s touto rolí mají globální oprávnění v Microsoft SharePoint Online, pokud služba používá, a možnost vytvářet a spravovat všechny skupiny Office 365, spravovat lístky podpory a monitorovat stav služby. Další informace na [role správců Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).
  > [!NOTE]
  > V rozhraní Microsoft Graph API, Azure AD Graph API a Azure AD PowerShell tato role je označena jako "Správce služeb Sharepointu." Je "Správce služby SharePoint" v [webu Azure portal](https://portal.azure.com).

* **[Skype pro firmy / Správce služby Lync](#lync-service-administrator)**: Uživatelé s touto rolí mají globální oprávnění v rámci Microsoft Skype pro firmy, pokud služba používá, stejně jako správu Skype konkrétní atributy uživatele v Azure Active Directory. Kromě toho tato role uděluje možnost spravovat lístky podpory a monitorovat stav služby a k přístupu do týmů a Skype pro firmy centra pro správu. Účet musí mít také licenci pro týmy, nebo se nedal spustit rutiny prostředí PowerShell týmy. Další informace na [o Skype pro firmy roli správce](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5) a týmy, které informace o licencích na [Skype pro firmy a Microsoft Teams doplněk licencování](https://docs.microsoft.com/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing)

  > [!NOTE]
  > V rozhraní Microsoft Graph API, Azure AD Graph API a Azure AD PowerShell tato role nazývá "Správce služeb Lyncu". Je "Skype pro firmy správce" v [webu Azure portal](https://portal.azure.com/).

* **[Týmy, které správce](#teams-service-administrator)**: Uživatelé s touto rolí může spravovat všechny aspekty sady funkcí Microsoft Teams přes Microsoft Teams a Skype pro firmy centra pro správu a odpovídajících modulů prostředí PowerShell. To zahrnuje mimo jiné oblasti, všem nástrojům pro správu související s telefonního subsystému, zasílání zpráv, setkání a samotné týmy. Tato role navíc uděluje možnost vytvářet a spravovat všechny skupiny Office 365, spravovat lístky podpory a monitorovat stav služby.
  > [!NOTE]
  > V rozhraní Microsoft Graph API, Azure AD Graph API a Azure AD PowerShell tato role je identifikován jako "týmy služby správce". Je "Týmy správce" v [webu Azure portal](https://portal.azure.com).

* **[Týmy, které správce komunikaci](#teams-communications-administrator)**: Uživatelé v této roli mohou spravovat aspekty související s Hl & telefonního subsystému zatížení Microsoft Teams. To zahrnuje nástroje pro správu pro přiřazení telefonní čísla, zásady pro hlasové hovory a schůzky a úplný přístup k nástrojů analytics volání.

* **[Týmy, které pracovník podpory komunikace](#teams-communications-support-engineer)**: Uživatelé v této roli můžete řešit problémy s komunikací v Microsoft Teams a Skype pro firmy pomocí volání uživatele řešení potíží s nástroji v Microsoft Teams a Skype pro firmy centra pro správu. Uživatelé v této roli můžete zobrazit informace o záznamu úplné volání pro všechny účastníky zahrnuté. Tato role nemá přístup k zobrazení, vytvořit nebo spravovat lístky podpory.

* **[Týmy, které odborné komunikace](#teams-communications-support-specialist)**: Uživatelé v této roli můžete řešit problémy s komunikací v Microsoft Teams a Skype pro firmy pomocí volání uživatele řešení potíží s nástroji v Microsoft Teams a Skype pro firmy centra pro správu. Uživatelé v této roli můžete zobrazit podrobnosti o uživateli pouze ve volání pro konkrétního uživatele, že se že mají vyhledávat. Tato role nemá přístup k zobrazení, vytvořit nebo spravovat lístky podpory.

* **[Správce uživatelských účtů](#user-account-administrator)**: Uživatelé s touto rolí můžou vytvářet uživatele a spravovat všechny aspekty uživatelů s určitými omezeními (viz níže). Uživatelé s touto rolí kromě toho můžete vytvořit a spravovat všechny skupiny. Tato role zahrnuje také možnost vytvářet a spravovat zobrazení uživatelů, spravovat lístky podpory a monitorovat stav služby.

  | | |
  | --- | --- |
  |Obecné oprávnění|<p>Vytvoření uživatelů a skupin</p><p>Vytvořit a spravovat zobrazení uživatelů</p><p>Spravovat lístky podpory Office|
  |<p>Pro všechny uživatele včetně všechny správce</p>|<p>Správa licencí</p><p>Spravovat všechny vlastnosti uživatele s výjimkou hlavní název uživatele</p>
  |Pouze na uživatele, kteří bez oprávnění správce, nebo v některém z následujících omezené role správce:<ul><li>Uživatelé s oprávněním ke čtení adresářů<li>Odesílatel pozvánky hostů<li>Správce technické podpory<li>Čtenář Centra zpráv<li>Čtenář sestav<li>Správce uživatelských účtů|<p>Odstranění a obnovení</p><p>Zakázání a povolení</p><p>Zneplatnit obnovovacích tokenů</p><p>Spravovat všechny vlastnosti uživatele včetně hlavní název uživatele</p><p>Resetování hesla</p><p>Aktualizovat klíče zařízení (FIDO)</p>
  
  <b>Důležité</b>: Uživatelé s touto rolí můžou měnit hesla pro uživatele, kteří můžou mít přístup k citlivým nebo soukromých informací nebo kritické konfigurace uvnitř i mimo Azure Active Directory. Změna hesla pro uživatele, může to znamenat schopnost předpokládat identit a oprávnění daného uživatele. Příklad:
  * Registrace aplikace a podniková aplikace vlastníky, kteří můžou spravovat aplikace, které vlastní přihlašovací údaje. Tyto aplikace mohou mít Privilegovaná oprávnění ve službě Azure AD a jinde nebyla udělena pro uživatele správce. Pomocí této cesty, Správce uživatelů může být schopen převzít identitu vlastníka aplikace a pak dále převzít identitu privilegovaného aplikace stačí aktualizovat přihlašovací údaje pro aplikaci.
  * Vlastníci předplatného Azure, kteří můžou mít přístup k citlivým nebo soukromých informací nebo kritické konfigurace v Azure.
  * Vlastníci skupiny zabezpečení a skupiny Office 365, kteří můžou spravovat členství ve skupině. Těmto skupinám udělit přístup k citlivým nebo soukromých informací nebo kritické konfigurace ve službě Azure AD a jinde.
  * Správci v dalších službách mimo Azure AD, jako jsou systémy Exchange Online, Office zabezpečení a dodržování předpisů System Center a lidské zdroje.
  * Všichni uživatelé, jako jsou vedoucí pracovníci, právním poradcem a lidské zdroje zaměstnanců, kteří můžou mít přístup k citlivým nebo soukromých informací.

## <a name="role-permissions"></a>Oprávnění role
Následující tabulky popisují konkrétní oprávnění v Azure Active Directory na každou roli. Některé role může mít další oprávnění ve službě Microsoft mimo službu Azure Active Directory.

### <a name="application-administrator"></a>Správce aplikace
Může vytvářet a spravovat všechny aspekty registrací aplikací a podnikových aplikací.

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/applications/audience/update | Umožňuje aktualizovat vlastnost applications.audience v Azure Active Directory. |
| microsoft.aad.directory/applications/authentication/update | Umožňuje aktualizovat vlastnost applications.authentication v Azure Active Directory. |
| microsoft.aad.directory/applications/basic/update | Umožňuje aktualizovat základní vlastnosti applications v Azure Active Directory. |
| microsoft.aad.directory/applications/create | Umožňuje vytvářet applications v Azure Active Directory. |
| microsoft.aad.directory/applications/credentials/update | Umožňuje aktualizovat vlastnost applications.credentials v Azure Active Directory. |
| microsoft.aad.directory/applications/delete | Umožňuje odstraňovat applications v Azure Active Directory. |
| microsoft.aad.directory/applications/owners/update | Umožňuje aktualizovat vlastnost applications.owners v Azure Active Directory. |
| microsoft.aad.directory/applications/permissions/update | Umožňuje aktualizovat vlastnost applications.permissions v Azure Active Directory. |
| microsoft.aad.directory/applications/policies/update | Umožňuje aktualizovat vlastnost applications.policies v Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/create | Umožňuje vytvářet appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/read | Umožňuje číst appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/update | Umožňuje aktualizovat appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/delete | Umožňuje odstraňovat appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/auditLogs/allProperties/read | Umožňuje číst všechny vlastnosti (včetně privilegovaných) v auditLogs v Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/read | Umožňuje číst vlastnost policies.applicationConfiguration v Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/update | Umožňuje aktualizovat vlastnost policies.applicationConfiguration v Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/create | Umožňuje vytvářet policies v Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/delete | Umožňuje odstraňovat policies v Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/read | Umožňuje číst vlastnost policies.applicationConfiguration v Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/update | Umožňuje aktualizovat vlastnost policies.applicationConfiguration v Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/policyAppliedTo/read | Umožňuje číst vlastnost policies.applicationConfiguration v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Umožňuje aktualizovat vlastnost servicePrincipals.appRoleAssignedTo v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Umožňuje aktualizovat vlastnost servicePrincipals.appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/audience/update | Umožňuje aktualizovat vlastnost servicePrincipals.audience v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/authentication/update | Umožňuje aktualizovat vlastnost servicePrincipals.authentication v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/update | Umožňuje aktualizovat základní vlastnosti v servicePrincipals v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/create | Umožňuje vytvářet servicePrincipals v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/credentials/update | Umožňuje aktualizovat vlastnost servicePrincipals.credentials v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/delete | Umožňuje odstraňovat servicePrincipals v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/update | Umožňuje aktualizovat vlastnost servicePrincipals.owners v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/permissions/update | Umožňuje aktualizovat vlastnost servicePrincipals.permissions v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Umožňuje aktualizovat vlastnost servicePrincipals.policies v Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Umožňuje číst všechny vlastnosti (včetně privilegovaných) v signInReports v Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat službu Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Umožňuje vytvářet a spravovat lístky podpory Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat stav služby Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Umožňuje vytvářet a spravovat lístky podpory Office 365. |

### <a name="application-developer"></a>Vývojář aplikace
Může vytvářet registrace aplikací nezávisle "uživatelé můžou registrovat aplikace' nastavení.

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/applications/createAsOwner | Umožňuje vytvářet applications v Azure Active Directory. Autor se přidá jako první vlastník a vytvořený objekt počítat do kvóty 250 vytvořených objektů autora. |
| microsoft.aad.directory/appRoleAssignments/createAsOwner | Umožňuje vytvářet appRoleAssignments v Azure Active Directory. Autor se přidá jako první vlastník a vytvořený objekt počítat do kvóty 250 vytvořených objektů autora. |
| microsoft.aad.directory/oAuth2PermissionGrants/createAsOwner | Umožňuje vytvářet oAuth2PermissionGrants v Azure Active Directory. Autor se přidá jako první vlastník a vytvořený objekt počítat do kvóty 250 vytvořených objektů autora. |
| microsoft.aad.directory/servicePrincipals/createAsOwner | Umožňuje vytvářet servicePrincipals v Azure Active Directory. Autor se přidá jako první vlastník a vytvořený objekt počítat do kvóty 250 vytvořených objektů autora. |

### <a name="authentication-administrator"></a>Správce ověření
Povoleno zobrazení, nastavení a obnovit informace metody ověřování pro všechny uživatele bez oprávnění správce.

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Umožňuje zneplatnit všechny obnovovací tokeny uživatele v Azure Active Directory. |
| microsoft.aad.directory/users/strongAuthentication/update | Umožňuje aktualizovat vlastnosti silného ověření, třeba přihlašovací údaje MFA. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat službu Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Umožňuje vytvářet a spravovat lístky podpory Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Umožňuje číst základní vlastnosti ve všech prostředcích v microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat stav služby Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Umožňuje vytvářet a spravovat lístky podpory Office 365. |

### <a name="billing-administrator"></a>Správce fakturace
Může provádět běžné úkoly související s fakturací, třeba aktualizovat platební údaje.

  > [!NOTE]
  > Tato role má oprávnění další mimo službu Azure Active Directory. Další informace najdete v tématu role popisu výše.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/organization/basic/update | Umožňuje aktualizovat základní vlastnosti organization v Azure Active Directory. |
| microsoft.aad.directory/organization/trustedCAsForPasswordlessAuth/update | Umožňuje aktualizovat vlastnost organization.trustedCAsForPasswordlessAuth v Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat službu Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Umožňuje vytvářet a spravovat lístky podpory Azure. |
| microsoft.commerce.billing/allEntities/allTasks | Umožňuje spravovat všechny aspekty fakturace Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Umožňuje číst základní vlastnosti ve všech prostředcích v microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat stav služby Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Umožňuje vytvářet a spravovat lístky podpory Office 365. |

### <a name="desktop-analytics-administrator"></a>Správce Desktop Analytics
Můžete získat přístup a správa desktopovým nástrojům pro správu a službám, včetně Intune.

  > [!NOTE]
  > Tato role má oprávnění další mimo službu Azure Active Directory. Další informace najdete v tématu role popisu výše.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat službu Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Umožňuje vytvářet a spravovat lístky podpory Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Umožňuje číst základní vlastnosti ve všech prostředcích v microsoft.office365.webPortal. |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | Umožňuje spravovat všechny aspekty Desktop Analytics. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat stav služby Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Umožňuje vytvářet a spravovat lístky podpory Office 365. |

### <a name="cloud-application-administrator"></a>Správce cloudové aplikace
Může vytvářet a spravovat všechny aspekty registrací aplikací a podnikových aplikací, kromě Proxy aplikací.

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/applications/audience/update | Umožňuje aktualizovat vlastnost applications.audience v Azure Active Directory. |
| microsoft.aad.directory/applications/authentication/update | Umožňuje aktualizovat vlastnost applications.authentication v Azure Active Directory. |
| microsoft.aad.directory/applications/basic/update | Umožňuje aktualizovat základní vlastnosti applications v Azure Active Directory. |
| microsoft.aad.directory/applications/create | Umožňuje vytvářet applications v Azure Active Directory. |
| microsoft.aad.directory/applications/credentials/update | Umožňuje aktualizovat vlastnost applications.credentials v Azure Active Directory. |
| microsoft.aad.directory/applications/delete | Umožňuje odstraňovat applications v Azure Active Directory. |
| microsoft.aad.directory/applications/owners/update | Umožňuje aktualizovat vlastnost applications.owners v Azure Active Directory. |
| microsoft.aad.directory/applications/permissions/update | Umožňuje aktualizovat vlastnost applications.permissions v Azure Active Directory. |
| microsoft.aad.directory/applications/policies/update | Umožňuje aktualizovat vlastnost applications.policies v Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/create | Umožňuje vytvářet appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/update | Umožňuje aktualizovat appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/delete | Umožňuje odstraňovat appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/auditLogs/allProperties/read | Umožňuje číst všechny vlastnosti (včetně privilegovaných) v auditLogs v Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/create | Umožňuje vytvářet policies v Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/read | Umožňuje číst vlastnost policies.applicationConfiguration v Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/update | Umožňuje aktualizovat vlastnost policies.applicationConfiguration v Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/delete | Umožňuje odstraňovat policies v Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/read | Umožňuje číst vlastnost policies.applicationConfiguration v Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/update | Umožňuje aktualizovat vlastnost policies.applicationConfiguration v Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/policyAppliedTo/read | Umožňuje číst vlastnost policies.applicationConfiguration v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Umožňuje aktualizovat vlastnost servicePrincipals.appRoleAssignedTo v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Umožňuje aktualizovat vlastnost servicePrincipals.appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/audience/update | Umožňuje aktualizovat vlastnost servicePrincipals.audience v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/authentication/update | Umožňuje aktualizovat vlastnost servicePrincipals.authentication v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/update | Umožňuje aktualizovat základní vlastnosti v servicePrincipals v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/create | Umožňuje vytvářet servicePrincipals v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/credentials/update | Umožňuje aktualizovat vlastnost servicePrincipals.credentials v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/delete | Umožňuje odstraňovat servicePrincipals v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/update | Umožňuje aktualizovat vlastnost servicePrincipals.owners v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/permissions/update | Umožňuje aktualizovat vlastnost servicePrincipals.permissions v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Umožňuje aktualizovat vlastnost servicePrincipals.policies v Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Umožňuje číst všechny vlastnosti (včetně privilegovaných) v signInReports v Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat službu Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Umožňuje vytvářet a spravovat lístky podpory Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat stav služby Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Umožňuje vytvářet a spravovat lístky podpory Office 365. |

### <a name="cloud-device-administrator"></a>Správce cloudových zařízení
Úplný přístup ke správě zařízení v Azure AD

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/auditLogs/allProperties/read | Umožňuje číst všechny vlastnosti (včetně privilegovaných) v auditLogs v Azure Active Directory. |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Umožňuje číst vlastnost devices.bitLockerRecoveryKeys v Azure Active Directory. |
| microsoft.aad.directory/devices/delete | Umožňuje odstraňovat devices v Azure Active Directory. |
| microsoft.aad.directory/devices/disable | Umožňuje zakázat devices v Azure Active Directory. |
| microsoft.aad.directory/devices/enable | Umožňuje povolovat devices v Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Umožňuje číst všechny vlastnosti (včetně privilegovaných) v signInReports v Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat službu Azure Service Health. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat stav služby Office 365. |

### <a name="company-administrator"></a>Správce společnosti
Může spravovat všechny aspekty služeb Azure AD a Microsoft, které používají identity Azure AD.

  > [!NOTE]
  > Tato role má oprávnění další mimo službu Azure Active Directory. Další informace najdete v tématu role popisu výše.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | Umožňuje vytvářet a odstraňovat všechny prostředky a číst a aktualizovat standardní vlastnosti v microsoft.aad.cloudAppSecurity. |
| microsoft.aad.directory/administrativeUnits/allProperties/allTasks | Umožňuje vytvářet a odstraňovat administrativeUnits a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/applications/allProperties/allTasks | Umožňuje vytvářet a odstraňovat applications a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/allProperties/allTasks | Umožňuje vytvářet a odstraňovat appRoleAssignments a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/auditLogs/allProperties/read | Umožňuje číst všechny vlastnosti (včetně privilegovaných) v auditLogs v Azure Active Directory. |
| microsoft.aad.directory/contacts/allProperties/allTasks | Umožňuje vytvářet a odstraňovat contacts a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/contracts/allProperties/allTasks | Umožňuje vytvářet a odstraňovat contracts a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/devices/allProperties/allTasks | Umožňuje vytvářet a odstraňovat devices a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/allProperties/allTasks | Umožňuje vytvářet a odstraňovat directoryRoles a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/directoryRoleTemplates/allProperties/allTasks | Umožňuje vytvářet a odstraňovat directoryRoleTemplates a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/domains/allProperties/allTasks | Umožňuje vytvářet a odstraňovat domains a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/groups/allProperties/allTasks | Umožňuje vytvářet a odstraňovat groups a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/groupSettings/allProperties/allTasks | Umožňuje vytvářet a odstraňovat groupSettings a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/groupSettingTemplates/allProperties/allTasks | Umožňuje vytvářet a odstraňovat groupSettingTemplates a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/loginTenantBranding/allProperties/allTasks | Umožňuje vytvářet a odstraňovat loginTenantBranding a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/oAuth2PermissionGrants/allProperties/allTasks | Umožňuje vytvářet a odstraňovat oAuth2PermissionGrants a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/organization/allProperties/allTasks | Umožňuje vytvářet a odstraňovat organization a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/policies/allProperties/allTasks | Umožňuje vytvářet a odstraňovat policies a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/roleAssignments/allProperties/allTasks | Umožňuje vytvářet a odstraňovat roleAssignments a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/roleDefinitions/allProperties/allTasks | Umožňuje vytvářet a odstraňovat roleDefinitions a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/scopedRoleMemberships/allProperties/allTasks | Umožňuje vytvářet a odstraňovat scopedRoleMemberships a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/serviceAction/activateService | Umožňuje provádět akci služby Activeservice v Azure Active Directory. |
| microsoft.aad.directory/serviceAction/disableDirectoryFeature | Umožňuje provádět akci služby Disabledirectoryfeature v Azure Active Directory. |
| microsoft.aad.directory/serviceAction/enableDirectoryFeature | Umožňuje provádět akci služby Enabledirectoryfeature v Azure Active Directory. |
| microsoft.aad.directory/serviceAction/getAvailableExtentionProperties | Umožňuje provádět akci služby Getavailableextentionproperties v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/allProperties/allTasks | Umožňuje vytvářet a odstraňovat servicePrincipals a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Umožňuje číst všechny vlastnosti (včetně privilegovaných) v signInReports v Azure Active Directory. |
| microsoft.aad.directory/subscribedSkus/allProperties/allTasks | Umožňuje vytvářet a odstraňovat subscribedSkus a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/users/allProperties/allTasks | Umožňuje vytvářet a odstraňovat users a číst a aktualizovat všechny vlastnosti v Azure Active Directory. |
| microsoft.aad.directorySync/allEntities/allTasks | Umožňuje provádět všechny akce ve službě Azure AD Connect. |
| microsoft.aad.identityProtection/allEntities/allTasks | Umožňuje vytvářet a odstraňovat všechny prostředky a číst a aktualizovat standardní vlastnosti v microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Umožňuje přečíst všechny prostředky v microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.advancedThreatProtection/allEntities/read | Umožňuje číst všechny prostředky v microsoft.azure.advancedThreatProtection. |
| microsoft.azure.informationProtection/allEntities/allTasks | Umožňuje spravovat všechny aspekty služby Azure Information Protection. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat službu Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Umožňuje vytvářet a spravovat lístky podpory Azure. |
| microsoft.commerce.billing/allEntities/allTasks | Umožňuje spravovat všechny aspekty fakturace Office 365. |
| microsoft.intune/allEntities/allTasks | Umožňuje spravovat všechny aspekty Intune. |
| microsoft.office365.webPortal/allEntities/basic/read | Umožňuje číst základní vlastnosti ve všech prostředcích v microsoft.office365.webPortal. |
| microsoft.office365.complianceManager/allEntities/allTasks | Umožňuje spravovat všechny aspekty Správce dodržování předpisů Office 365. |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | Umožňuje spravovat všechny aspekty Desktop Analytics. |
| microsoft.office365.exchange/allEntities/allTasks | Umožňuje spravovat všechny aspekty Exchange Online. |
| microsoft.office365.lockbox/allEntities/allTasks | Umožňuje spravovat všechny aspekty funkce Office 365 Customer Lockbox. |
| microsoft.office365.messageCenter/messages/read | Umožňuje číst messages v microsoft.office365.messageCenter. |
| microsoft.office365.messageCenter/securityMessages/read | Umožňuje číst securityMessages v microsoft.office365.messageCenter. |
| microsoft.powerApps.powerBI/allEntities/allTasks | Umožňuje spravovat všechny aspekty Power BI. |
| microsoft.office365.protectionCenter/allEntities/allTasks | Umožňuje spravovat všechny aspekty Centra ochrany Office 365. |
| microsoft.office365.securityComplianceCenter/allEntities/allTasks | Umožňuje vytvářet a odstraňovat všechny prostředky a číst a aktualizovat standardní vlastnosti v microsoft.office365.securityComplianceCenter. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat stav služby Office 365. |
| microsoft.office365.sharepoint/allEntities/allTasks | Umožňuje vytvářet a odstraňovat všechny prostředky a číst a aktualizovat standardní vlastnosti v microsoft.office365.sharepoint. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Umožňuje spravovat všechny aspekty Online Skypu pro firmy. |
| microsoft.office365.supportTickets/allEntities/allTasks | Umožňuje vytvářet a spravovat lístky podpory Office 365. |
| microsoft.office365.usageReports/allEntities/read | Umožňuje číst sestavy využití Office 365. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Umožňuje spravovat všechny aspekty Dynamics 365. |
| microsoft.windows.defenderAdvancedThreatProtection/allEntities/read | Umožňuje číst všechny prostředky v microsoft.windows.defenderAdvancedThreatProtection. |

### <a name="compliance-administrator"></a>Správce dodržování předpisů
Může číst a spravovat konfiguraci dodržování předpisů a sestav v Azure AD a Office 365.

  > [!NOTE]
  > Tato role má oprávnění další mimo službu Azure Active Directory. Další informace najdete v tématu role popisu výše.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat službu Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Umožňuje vytvářet a spravovat lístky podpory Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Umožňuje číst základní vlastnosti ve všech prostředcích v microsoft.office365.webPortal. |
| microsoft.office365.complianceManager/allEntities/allTasks | Umožňuje spravovat všechny aspekty Správce dodržování předpisů Office 365. |
| microsoft.office365.exchange/allEntities/allTasks | Umožňuje spravovat všechny aspekty Exchange Online. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat stav služby Office 365. |
| microsoft.office365.sharepoint/allEntities/allTasks | Umožňuje vytvářet a odstraňovat všechny prostředky a číst a aktualizovat standardní vlastnosti v microsoft.office365.sharepoint. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Umožňuje spravovat všechny aspekty Online Skypu pro firmy. |
| microsoft.office365.supportTickets/allEntities/allTasks | Umožňuje vytvářet a spravovat lístky podpory Office 365. |

### <a name="conditional-access-administrator"></a>Správce podmíněného přístupu
Může spravovat funkce podmíněného přístupu.

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/policies/conditionalAccess/basic/read | Umožňuje číst vlastnost policies.conditionalAccess v Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/basic/update | Umožňuje aktualizovat vlastnost policies.conditionalAccess v Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/create | Umožňuje vytvářet policies v Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/delete | Umožňuje odstraňovat policies v Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/owners/read | Umožňuje číst vlastnost policies.conditionalAccess v Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/owners/update | Umožňuje aktualizovat vlastnost policies.conditionalAccess v Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/policiesAppliedTo/read | Umožňuje číst vlastnost policies.conditionalAccess v Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/tenantDefault/update | Umožňuje aktualizovat vlastnost policies.conditionalAccess v Azure Active Directory. |

### <a name="crm-service-administrator"></a>Správce služby CRM
Může spravovat všechny aspekty produktu Dynamics 365.

  > [!NOTE]
  > Tato role má oprávnění další mimo službu Azure Active Directory. Další informace najdete v tématu role popisu výše.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat službu Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Umožňuje vytvářet a spravovat lístky podpory Azure. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Umožňuje spravovat všechny aspekty Dynamics 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Umožňuje číst základní vlastnosti ve všech prostředcích v microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat stav služby Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Umožňuje vytvářet a spravovat lístky podpory Office 365. |

### <a name="customer-lockbox-access-approver"></a>Schvalovatel přístupu ke Customer LockBoxu
Může schvalovat žádosti podpory Microsoftu o přístup k datům organizace zákazníka. Tato role nemá přístup k zobrazení, vytvořit nebo spravovat lístky podpory.

  > [!NOTE]
  > Tato role má oprávnění další mimo službu Azure Active Directory. Další informace najdete v tématu role popisu výše.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | Umožňuje číst základní vlastnosti ve všech prostředcích v microsoft.office365.webPortal. |
| microsoft.office365.lockbox/allEntities/allTasks | Umožňuje spravovat všechny aspekty funkce Office 365 Customer Lockbox. |

### <a name="device-administrators"></a>Správci zařízení
Členové této role se přidají do skupiny místních správců na Azure zařízení připojených k doméně AD.

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/groupSettings/basic/read | Umožňuje číst základní vlastnosti groupSettings v Azure Active Directory. |
| microsoft.aad.directory/groupSettingTemplates/basic/read | Umožňuje číst základní vlastnosti groupSettingTemplates v Azure Active Directory. |

### <a name="directory-readers"></a>Uživatelé s oprávněním ke čtení adresářů
Může číst informace o základní adresář. Pro udělení přístupu k aplikacím, není určený pro uživatele.

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/administrativeUnits/basic/read | Umožňuje číst základní vlastnosti administrativeUnits v Azure Active Directory. |
| microsoft.aad.directory/administrativeUnits/members/read | Umožňuje číst vlastnost administrativeUnits.members v Azure Active Directory. |
| microsoft.aad.directory/applications/basic/read | Umožňuje číst základní vlastnosti applications v Azure Active Directory. |
| microsoft.aad.directory/applications/owners/read | Umožňuje číst vlastnost applications.owners v Azure Active Directory. |
| microsoft.aad.directory/applications/policies/read | Umožňuje číst vlastnost applications.policies v Azure Active Directory. |
| microsoft.aad.directory/contacts/basic/read | Umožňuje číst základní vlastnosti contacts v Azure Active Directory. |
| microsoft.aad.directory/contacts/memberOf/read | Umožňuje číst vlastnost contacts.memberOf v Azure Active Directory. |
| microsoft.aad.directory/contracts/basic/read | Umožňuje číst základní vlastnosti contracts v Azure Active Directory. |
| microsoft.aad.directory/devices/basic/read | Umožňuje číst základní vlastnosti devices v Azure Active Directory. |
| microsoft.aad.directory/devices/memberOf/read | Umožňuje číst vlastnost devices.memberOf v Azure Active Directory. |
| microsoft.aad.directory/devices/registeredOwners/read | Umožňuje číst vlastnost devices.registeredOwners v Azure Active Directory. |
| microsoft.aad.directory/devices/registeredUsers/read | Umožňuje číst vlastnost devices.registeredUsers v Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/basic/read | Umožňuje číst základní vlastnosti directoryRoles v Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/eligibleMembers/read | Umožňuje číst vlastnost directoryRoles.eligibleMembers v Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/members/read | Umožňuje číst vlastnost directoryRoles.members v Azure Active Directory. |
| microsoft.aad.directory/domains/basic/read | Umožňuje číst základní vlastnosti domains v Azure Active Directory. |
| microsoft.aad.directory/groups/appRoleAssignments/read | Umožňuje číst vlastnost groups.appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/groups/basic/read | Umožňuje číst základní vlastnosti groups v Azure Active Directory. |
| microsoft.aad.directory/groups/memberOf/read | Umožňuje číst vlastnost groups.memberOf v Azure Active Directory. |
| microsoft.aad.directory/groups/members/read | Umožňuje číst vlastnost groups.members v Azure Active Directory. |
| microsoft.aad.directory/groups/owners/read | Umožňuje číst vlastnost groups.owners v Azure Active Directory. |
| microsoft.aad.directory/groups/settings/read | Umožňuje číst vlastnost groups.settings v Azure Active Directory. |
| microsoft.aad.directory/groupSettings/basic/read | Umožňuje číst základní vlastnosti groupSettings v Azure Active Directory. |
| microsoft.aad.directory/groupSettingTemplates/basic/read | Umožňuje číst základní vlastnosti groupSettingTemplates v Azure Active Directory. |
| microsoft.aad.directory/oAuth2PermissionGrants/basic/read | Umožňuje číst základní vlastnosti oAuth2PermissionGrants v Azure Active Directory. |
| microsoft.aad.directory/organization/basic/read | Umožňuje číst základní vlastnosti organization v Azure Active Directory. |
| microsoft.aad.directory/organization/trustedCAsForPasswordlessAuth/read | Umožňuje číst vlastnost organization.trustedCAsForPasswordlessAuth v Azure Active Directory. |
| microsoft.aad.directory/roleAssignments/basic/read | Umožňuje číst základní vlastnosti roleAssignments v Azure Active Directory. |
| microsoft.aad.directory/roleDefinitions/basic/read | Umožňuje číst základní vlastnosti roleDefinitions v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/read | Umožňuje číst vlastnost servicePrincipals.appRoleAssignedTo v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/read | Umožňuje číst vlastnost servicePrincipals.appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/read | Umožňuje číst základní vlastnosti v servicePrincipals v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/memberOf/read | Umožňuje číst vlastnost servicePrincipals.memberOf v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | Umožňuje číst vlastnost servicePrincipals.oAuth2PermissionGrants v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/ownedObjects/read | Umožňuje číst vlastnost servicePrincipals.ownedObjects v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/read | Umožňuje číst vlastnost servicePrincipals.owners v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/read | Umožňuje číst vlastnost servicePrincipals.policies v Azure Active Directory. |
| microsoft.aad.directory/subscribedSkus/basic/read | Umožňuje číst základní vlastnosti subscribedSkus v Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/read | Umožňuje číst vlastnost users.appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/users/basic/read | Umožňuje číst základní vlastnosti users v Azure Active Directory. |
| microsoft.aad.directory/users/directReports/read | Umožňuje číst vlastnost users.directReports v Azure Active Directory. |
| microsoft.aad.directory/users/manager/read | Umožňuje číst vlastnost users.manager v Azure Active Directory. |
| microsoft.aad.directory/users/memberOf/read | Umožňuje číst vlastnost users.memberOf v Azure Active Directory. |
| microsoft.aad.directory/users/oAuth2PermissionGrants/basic/read | Umožňuje číst vlastnost users.oAuth2PermissionGrants v Azure Active Directory. |
| microsoft.aad.directory/users/ownedDevices/read | Umožňuje číst vlastnost users.ownedDevices v Azure Active Directory. |
| microsoft.aad.directory/users/ownedObjects/read | Umožňuje číst vlastnost users.ownedObjects v Azure Active Directory. |
| microsoft.aad.directory/users/registeredDevices/read | Umožňuje číst vlastnost users.registeredDevices v Azure Active Directory. |

### <a name="directory-synchronization-accounts"></a>Účty pro synchronizaci adresáře
Pouze používá služba Azure AD Connect.

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/organization/dirSync/update | Umožňuje aktualizovat vlastnost organization.dirSync v Azure Active Directory. |
| microsoft.aad.directory/policies/create | Umožňuje vytvářet policies v Azure Active Directory. |
| microsoft.aad.directory/policies/delete | Umožňuje odstraňovat policies v Azure Active Directory. |
| microsoft.aad.directory/policies/basic/read | Umožňuje číst základní vlastnosti policies v Azure Active Directory. |
| microsoft.aad.directory/policies/basic/update | Umožňuje aktualizovat základní vlastnosti policies v Azure Active Directory. |
| microsoft.aad.directory/policies/owners/read | Umožňuje číst vlastnost policies.owners v Azure Active Directory. |
| microsoft.aad.directory/policies/owners/update | Umožňuje aktualizovat vlastnost policies.owners v Azure Active Directory. |
| microsoft.aad.directory/policies/policiesAppliedTo/read | Umožňuje číst vlastnost policies.policiesAppliedTo v Azure Active Directory. |
| microsoft.aad.directory/policies/tenantDefault/update | Umožňuje aktualizovat vlastnost policies.tenantDefault v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/read | Umožňuje číst vlastnost servicePrincipals.appRoleAssignedTo v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Umožňuje aktualizovat vlastnost servicePrincipals.appRoleAssignedTo v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/read | Umožňuje číst vlastnost servicePrincipals.appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Umožňuje aktualizovat vlastnost servicePrincipals.appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/audience/update | Umožňuje aktualizovat vlastnost servicePrincipals.audience v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/authentication/update | Umožňuje aktualizovat vlastnost servicePrincipals.authentication v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/read | Umožňuje číst základní vlastnosti v servicePrincipals v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/update | Umožňuje aktualizovat základní vlastnosti v servicePrincipals v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/create | Umožňuje vytvářet servicePrincipals v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/credentials/update | Umožňuje aktualizovat vlastnost servicePrincipals.credentials v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/memberOf/read | Umožňuje číst vlastnost servicePrincipals.memberOf v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | Umožňuje číst vlastnost servicePrincipals.oAuth2PermissionGrants v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/read | Umožňuje číst vlastnost servicePrincipals.owners v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/update | Umožňuje aktualizovat vlastnost servicePrincipals.owners v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/ownedObjects/read | Umožňuje číst vlastnost servicePrincipals.ownedObjects v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/permissions/update | Umožňuje aktualizovat vlastnost servicePrincipals.permissions v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/read | Umožňuje číst vlastnost servicePrincipals.policies v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Umožňuje aktualizovat vlastnost servicePrincipals.policies v Azure Active Directory. |
| microsoft.aad.directorySync/allEntities/allTasks | Umožňuje provádět všechny akce ve službě Azure AD Connect. |

### <a name="directory-writers"></a>Zapisovače do adresáře
Může číst a zapisovat informace základní adresář. Pro udělení přístupu k aplikacím, není určený pro uživatele.

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/groups/create | Umožňuje vytvářet groups v Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Umožňuje vytvářet groups v Azure Active Directory. Autor se přidá jako první vlastník a vytvořený objekt počítat do kvóty 250 vytvořených objektů autora. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Umožňuje aktualizovat vlastnost groups.appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/groups/basic/update | Umožňuje aktualizovat základní vlastnosti groups v Azure Active Directory. |
| microsoft.aad.directory/groups/members/update | Umožňuje aktualizovat vlastnost groups.members v Azure Active Directory. |
| microsoft.aad.directory/groups/owners/update | Umožňuje aktualizovat vlastnost groups.owners v Azure Active Directory. |
| microsoft.aad.directory/groups/settings/update | Umožňuje aktualizovat vlastnost groups.settings v Azure Active Directory. |
| microsoft.aad.directory/groupSettings/basic/update | Umožňuje aktualizovat základní vlastnosti groupSettings v Azure Active Directory. |
| microsoft.aad.directory/groupSettings/create | Umožňuje vytvářet groupSettings v Azure Active Directory. |
| microsoft.aad.directory/groupSettings/delete | Umožňuje odstraňovat groupSettings v Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Umožňuje aktualizovat vlastnost users.appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Umožňuje spravovat licence uživatelů v Azure Active Directory. |
| microsoft.aad.directory/users/basic/update | Umožňuje aktualizovat základní vlastnosti users v Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Umožňuje zneplatnit všechny obnovovací tokeny uživatele v Azure Active Directory. |
| microsoft.aad.directory/users/manager/update | Umožňuje aktualizovat vlastnost users.manager v Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Umožňuje aktualizovat vlastnost users.userPrincipalName v Azure Active Directory. |

### <a name="exchange-service-administrator"></a>Správce služby Exchange
Může spravovat všechny aspekty produktu Exchange.

  > [!NOTE]
  > Tato role má oprávnění další mimo službu Azure Active Directory. Další informace najdete v tématu role popisu výše.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/groups/unified/appRoleAssignments/update | Umožňuje aktualizovat vlastnost groups.unified v Azure Active Directory. |
| microsoft.aad.directory/groups/unified/basic/update | Umožňuje aktualizovat základní vlastnosti Skupin Office 365. |
| microsoft.aad.directory/groups/unified/create | Umožňuje vytvořit Skupiny Office 365. |
| microsoft.aad.directory/groups/unified/delete | Umožňuje odstranit Skupiny Office 365. |
| microsoft.aad.directory/groups/unified/members/update | Umožňuje aktualizovat členství ve Skupinách Office 365. |
| microsoft.aad.directory/groups/unified/owners/update | Umožňuje aktualizovat vlastnictví Skupin Office 365. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat službu Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Umožňuje vytvářet a spravovat lístky podpory Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Umožňuje číst základní vlastnosti ve všech prostředcích v microsoft.office365.webPortal. |
| microsoft.office365.exchange/allEntities/allTasks | Umožňuje spravovat všechny aspekty Exchange Online. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat stav služby Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Umožňuje vytvářet a spravovat lístky podpory Office 365. |

### <a name="guest-inviter"></a>Odesílatel pozvánky hostů
Může zvát uživatele typu host bez ohledu na nastavení, jestli členové můžou zvát hosty.

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/users/appRoleAssignments/read | Umožňuje číst vlastnost users.appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/users/basic/read | Umožňuje číst základní vlastnosti users v Azure Active Directory. |
| microsoft.aad.directory/users/directReports/read | Umožňuje číst vlastnost users.directReports v Azure Active Directory. |
| microsoft.aad.directory/users/inviteGuest | Umožňuje zvát hosty do Azure Active Directory. |
| microsoft.aad.directory/users/manager/read | Umožňuje číst vlastnost users.manager v Azure Active Directory. |
| microsoft.aad.directory/users/memberOf/read | Umožňuje číst vlastnost users.memberOf v Azure Active Directory. |
| microsoft.aad.directory/users/oAuth2PermissionGrants/basic/read | Umožňuje číst vlastnost users.oAuth2PermissionGrants v Azure Active Directory. |
| microsoft.aad.directory/users/ownedDevices/read | Umožňuje číst vlastnost users.ownedDevices v Azure Active Directory. |
| microsoft.aad.directory/users/ownedObjects/read | Umožňuje číst vlastnost users.ownedObjects v Azure Active Directory. |
| microsoft.aad.directory/users/registeredDevices/read | Umožňuje číst vlastnost users.registeredDevices v Azure Active Directory. |

### <a name="helpdesk-administrator"></a>Správce technické podpory
Může resetovat hesla uživatelů, kteří nejsou správci, a správců technické podpory.

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Umožňuje číst vlastnost devices.bitLockerRecoveryKeys v Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Umožňuje zneplatnit všechny obnovovací tokeny uživatele v Azure Active Directory. |
| microsoft.aad.directory/users/password/update | Aktualizace hesla pro všechny uživatele ve službě Azure Active Directory. Najdete v online dokumentaci pro více podrobností. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat službu Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Umožňuje vytvářet a spravovat lístky podpory Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Umožňuje číst základní vlastnosti ve všech prostředcích v microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat stav služby Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Umožňuje vytvářet a spravovat lístky podpory Office 365. |

### <a name="information-protection-administrator"></a>Správce Information Protection
Může spravovat všechny aspekty produktu Azure Information Protection.

  > [!NOTE]
  > Tato role má oprávnění další mimo službu Azure Active Directory. Další informace najdete v tématu role popisu výše.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.azure.informationProtection/allEntities/allTasks | Umožňuje spravovat všechny aspekty služby Azure Information Protection. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat službu Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Umožňuje vytvářet a spravovat lístky podpory Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat stav služby Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Umožňuje vytvářet a spravovat lístky podpory Office 365. |

### <a name="intune-service-administrator"></a>Správce služby Intune
Může spravovat všechny aspekty produktu Intune.

  > [!NOTE]
  > Tato role má oprávnění další mimo službu Azure Active Directory. Další informace najdete v tématu role popisu výše.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/contacts/basic/update | Umožňuje aktualizovat základní vlastnosti contacts v Azure Active Directory. |
| microsoft.aad.directory/contacts/create | Umožňuje vytvářet contacts v Azure Active Directory. |
| microsoft.aad.directory/contacts/delete | Umožňuje odstraňovat contacts v Azure Active Directory. |
| microsoft.aad.directory/devices/basic/update | Umožňuje aktualizovat základní vlastnosti devices v Azure Active Directory. |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Umožňuje číst vlastnost devices.bitLockerRecoveryKeys v Azure Active Directory. |
| microsoft.aad.directory/devices/create | Umožňuje vytvářet devices v Azure Active Directory. |
| microsoft.aad.directory/devices/delete | Umožňuje odstraňovat devices v Azure Active Directory. |
| microsoft.aad.directory/devices/registeredOwners/update | Umožňuje aktualizovat vlastnost devices.registeredOwners v Azure Active Directory. |
| microsoft.aad.directory/devices/registeredUsers/update | Umožňuje aktualizovat vlastnost devices.registeredUsers v Azure Active Directory. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Umožňuje aktualizovat vlastnost groups.appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/groups/basic/update | Umožňuje aktualizovat základní vlastnosti groups v Azure Active Directory. |
| microsoft.aad.directory/groups/create | Umožňuje vytvářet groups v Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Umožňuje vytvářet groups v Azure Active Directory. Autor se přidá jako první vlastník a vytvořený objekt počítat do kvóty 250 vytvořených objektů autora. |
| microsoft.aad.directory/groups/delete | Umožňuje odstraňovat groups v Azure Active Directory. |
| microsoft.aad.directory/groups/hiddenMembers/read | Umožňuje číst vlastnost groups.hiddenMembers v Azure Active Directory. |
| microsoft.aad.directory/groups/members/update | Umožňuje aktualizovat vlastnost groups.members v Azure Active Directory. |
| microsoft.aad.directory/groups/owners/update | Umožňuje aktualizovat vlastnost groups.owners v Azure Active Directory. |
| microsoft.aad.directory/groups/restore | Umožňuje obnovovat groups v Azure Active Directory. |
| microsoft.aad.directory/groups/settings/update | Umožňuje aktualizovat vlastnost groups.settings v Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Umožňuje aktualizovat vlastnost users.appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/users/basic/update | Umožňuje aktualizovat základní vlastnosti users v Azure Active Directory. |
| microsoft.aad.directory/users/manager/update | Umožňuje aktualizovat vlastnost users.manager v Azure Active Directory. |
| microsoft.azure.supportTickets/allEntities/allTasks | Umožňuje vytvářet a spravovat lístky podpory Azure. |
| microsoft.intune/allEntities/allTasks | Umožňuje spravovat všechny aspekty Intune. |
| microsoft.office365.supportTickets/allEntities/allTasks | Umožňuje vytvářet a spravovat lístky podpory Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Umožňuje číst základní vlastnosti ve všech prostředcích v microsoft.office365.webPortal. |

### <a name="license-administrator"></a>Správce licencí
Můžete spravovat licence na produkty na uživatele a skupiny.

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/users/assignLicense | Umožňuje spravovat licence uživatelů v Azure Active Directory. |
| microsoft.aad.directory/users/usageLocation/update | Umožňuje aktualizovat vlastnost users.usageLocation v Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat službu Azure Service Health. |
| microsoft.office365.webPortal/allEntities/basic/read | Umožňuje číst základní vlastnosti ve všech prostředcích v microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat stav služby Office 365. |

### <a name="lync-service-administrator"></a>Správce služeb Lyncu
Může spravovat všechny aspekty produktu Skype pro firmy.

  > [!NOTE]
  > Tato role má oprávnění další mimo službu Azure Active Directory. Další informace najdete v tématu role popisu výše.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat službu Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Umožňuje vytvářet a spravovat lístky podpory Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Umožňuje číst základní vlastnosti ve všech prostředcích v microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat stav služby Office 365. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Umožňuje spravovat všechny aspekty Online Skypu pro firmy. |
| microsoft.office365.supportTickets/allEntities/allTasks | Umožňuje vytvářet a spravovat lístky podpory Office 365. |

### <a name="message-center-reader"></a>Čtenář Centra zpráv
Může číst zprávy a aktualizace pro svou organizaci jen v Centru zpráv Office 365. Tato role nemá přístup k zobrazení, vytvořit nebo spravovat lístky podpory.

  > [!NOTE]
  > Tato role má oprávnění další mimo službu Azure Active Directory. Další informace najdete v tématu role popisu výše.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | Umožňuje číst základní vlastnosti ve všech prostředcích v microsoft.office365.webPortal. |
| microsoft.office365.messageCenter/messages/read | Umožňuje číst messages v microsoft.office365.messageCenter. |

### <a name="partner-tier1-support"></a>Podpora partnerů úrovně 1
Nepoužívejte – nejsou určené pro obecné použití.

  > [!NOTE]
  > Tato role má oprávnění další mimo službu Azure Active Directory. Další informace najdete v tématu role popisu výše.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/contacts/basic/update | Umožňuje aktualizovat základní vlastnosti contacts v Azure Active Directory. |
| microsoft.aad.directory/contacts/create | Umožňuje vytvářet contacts v Azure Active Directory. |
| microsoft.aad.directory/contacts/delete | Umožňuje odstraňovat contacts v Azure Active Directory. |
| microsoft.aad.directory/groups/create | Umožňuje vytvářet groups v Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Umožňuje vytvářet groups v Azure Active Directory. Autor se přidá jako první vlastník a vytvořený objekt počítat do kvóty 250 vytvořených objektů autora. |
| microsoft.aad.directory/groups/members/update | Umožňuje aktualizovat vlastnost groups.members v Azure Active Directory. |
| microsoft.aad.directory/groups/owners/update | Umožňuje aktualizovat vlastnost groups.owners v Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Umožňuje aktualizovat vlastnost users.appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Umožňuje spravovat licence uživatelů v Azure Active Directory. |
| microsoft.aad.directory/users/basic/update | Umožňuje aktualizovat základní vlastnosti users v Azure Active Directory. |
| microsoft.aad.directory/users/delete | Umožňuje odstraňovat users v Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Umožňuje zneplatnit všechny obnovovací tokeny uživatele v Azure Active Directory. |
| microsoft.aad.directory/users/manager/update | Umožňuje aktualizovat vlastnost users.manager v Azure Active Directory. |
| microsoft.aad.directory/users/password/update | Aktualizace hesla pro všechny uživatele ve službě Azure Active Directory. Najdete v online dokumentaci pro více podrobností. |
| microsoft.aad.directory/users/restore | Umožňuje obnovovat odstraněné uživatele v Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Umožňuje aktualizovat vlastnost users.userPrincipalName v Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat službu Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Umožňuje vytvářet a spravovat lístky podpory Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Umožňuje číst základní vlastnosti ve všech prostředcích v microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat stav služby Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Umožňuje vytvářet a spravovat lístky podpory Office 365. |

### <a name="partner-tier2-support"></a>Podpora partnerů úrovně 2
Nepoužívejte – nejsou určené pro obecné použití.

  > [!NOTE]
  > Tato role má oprávnění další mimo službu Azure Active Directory. Další informace najdete v tématu role popisu výše.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/contacts/basic/update | Umožňuje aktualizovat základní vlastnosti contacts v Azure Active Directory. |
| microsoft.aad.directory/contacts/create | Umožňuje vytvářet contacts v Azure Active Directory. |
| microsoft.aad.directory/contacts/delete | Umožňuje odstraňovat contacts v Azure Active Directory. |
| microsoft.aad.directory/domains/allTasks | Umožňuje vytvářet a odstraňovat domains a číst a aktualizovat standardní vlastnosti v Azure Active Directory. |
| microsoft.aad.directory/groups/create | Umožňuje vytvářet groups v Azure Active Directory. |
| microsoft.aad.directory/groups/delete | Umožňuje odstraňovat groups v Azure Active Directory. |
| microsoft.aad.directory/groups/members/update | Umožňuje aktualizovat vlastnost groups.members v Azure Active Directory. |
| microsoft.aad.directory/groups/restore | Umožňuje obnovovat groups v Azure Active Directory. |
| microsoft.aad.directory/organization/basic/update | Umožňuje aktualizovat základní vlastnosti organization v Azure Active Directory. |
| microsoft.aad.directory/organization/trustedCAsForPasswordlessAuth/update | Umožňuje aktualizovat vlastnost organization.trustedCAsForPasswordlessAuth v Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Umožňuje aktualizovat vlastnost users.appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Umožňuje spravovat licence uživatelů v Azure Active Directory. |
| microsoft.aad.directory/users/basic/update | Umožňuje aktualizovat základní vlastnosti users v Azure Active Directory. |
| microsoft.aad.directory/users/delete | Umožňuje odstraňovat users v Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Umožňuje zneplatnit všechny obnovovací tokeny uživatele v Azure Active Directory. |
| microsoft.aad.directory/users/manager/update | Umožňuje aktualizovat vlastnost users.manager v Azure Active Directory. |
| microsoft.aad.directory/users/password/update | Aktualizace hesla pro všechny uživatele ve službě Azure Active Directory. Najdete v online dokumentaci pro více podrobností. |
| microsoft.aad.directory/users/restore | Umožňuje obnovovat odstraněné uživatele v Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Umožňuje aktualizovat vlastnost users.userPrincipalName v Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat službu Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Umožňuje vytvářet a spravovat lístky podpory Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Umožňuje číst základní vlastnosti ve všech prostředcích v microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat stav služby Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Umožňuje vytvářet a spravovat lístky podpory Office 365. |

### <a name="power-bi-service-administrator"></a>Správce služeb Power BI
Může spravovat všechny aspekty produktu Power BI.

  > [!NOTE]
  > Tato role má oprávnění další mimo službu Azure Active Directory. Další informace najdete v tématu role popisu výše.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat službu Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Umožňuje vytvářet a spravovat lístky podpory Azure. |
| microsoft.powerApps.powerBI/allEntities/allTasks | Umožňuje spravovat všechny aspekty Power BI. |
| microsoft.office365.webPortal/allEntities/basic/read | Umožňuje číst základní vlastnosti ve všech prostředcích v microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat stav služby Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Umožňuje vytvářet a spravovat lístky podpory Office 365. |

### <a name="privileged-role-administrator"></a>Správce privilegované role
Můžou Spravovat přiřazení rolí ve službě Azure AD a všechny aspekty služby Privileged Identity Management.

  > [!NOTE]
  > Tato role má oprávnění další mimo službu Azure Active Directory. Další informace najdete v tématu role popisu výše.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/directoryRoles/update | Umožňuje aktualizovat directoryRoles v Azure Active Directory. |
| microsoft.aad.privilegedIdentityManagement/allEntities/allTasks | Umožňuje vytvářet a odstraňovat všechny prostředky a číst a aktualizovat standardní vlastnosti v microsoft.aad.privilegedIdentityManagement. |

### <a name="reports-reader"></a>Čtenář sestav
Může číst sestavy o přihlašování a auditech.

  > [!NOTE]
  > Tato role má oprávnění další mimo službu Azure Active Directory. Další informace najdete v tématu role popisu výše.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/auditLogs/allProperties/read | Umožňuje číst všechny vlastnosti (včetně privilegovaných) v auditLogs v Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Umožňuje číst všechny vlastnosti (včetně privilegovaných) v signInReports v Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat službu Azure Service Health. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat stav služby Office 365. |
| microsoft.office365.usageReports/allEntities/read | Umožňuje číst sestavy využití Office 365. |

### <a name="security-administrator"></a>Správce zabezpečení
Může číst informace o zabezpečení a sestavy a spravovat konfigurace ve službě Azure AD a Office 365.

  > [!NOTE]
  > Tato role má oprávnění další mimo službu Azure Active Directory. Další informace najdete v tématu role popisu výše.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/applications/policies/update | Umožňuje aktualizovat vlastnost applications.policies v Azure Active Directory. |
| microsoft.aad.directory/auditLogs/allProperties/read | Umožňuje číst všechny vlastnosti (včetně privilegovaných) v auditLogs v Azure Active Directory. |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Umožňuje číst vlastnost devices.bitLockerRecoveryKeys v Azure Active Directory. |
| microsoft.aad.directory/policies/basic/update | Umožňuje aktualizovat základní vlastnosti policies v Azure Active Directory. |
| microsoft.aad.directory/policies/create | Umožňuje vytvářet policies v Azure Active Directory. |
| microsoft.aad.directory/policies/delete | Umožňuje odstraňovat policies v Azure Active Directory. |
| microsoft.aad.directory/policies/owners/update | Umožňuje aktualizovat vlastnost policies.owners v Azure Active Directory. |
| microsoft.aad.directory/policies/tenantDefault/update | Umožňuje aktualizovat vlastnost policies.tenantDefault v Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Umožňuje aktualizovat vlastnost servicePrincipals.policies v Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Umožňuje číst všechny vlastnosti (včetně privilegovaných) v signInReports v Azure Active Directory. |
| microsoft.aad.identityProtection/allEntities/read | Umožňuje přečíst všechny prostředky v microsoft.aad.identityProtection. |
| microsoft.aad.identityProtection/allEntities/update | Umožňuje aktualizovat všechny prostředky v microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Umožňuje přečíst všechny prostředky v microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat službu Azure Service Health. |
| microsoft.office365.webPortal/allEntities/basic/read | Umožňuje číst základní vlastnosti ve všech prostředcích v microsoft.office365.webPortal. |
| microsoft.office365.protectionCenter/allEntities/read | Umožňuje číst všechny aspekty Centra ochrany Office 365. |
| microsoft.office365.protectionCenter/allEntities/update | Umožňuje aktualizovat všechny prostředky v microsoft.office365.protectionCenter. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat stav služby Office 365. |

### <a name="security-reader"></a>Čtecí zařízení pro zabezpečení
Může číst bezpečnostní údaje a sestavy v Azure AD a Office 365.

  > [!NOTE]
  > Tato role má oprávnění další mimo službu Azure Active Directory. Další informace najdete v tématu role popisu výše.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/auditLogs/allProperties/read | Umožňuje číst všechny vlastnosti (včetně privilegovaných) v auditLogs v Azure Active Directory. |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Umožňuje číst vlastnost devices.bitLockerRecoveryKeys v Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Umožňuje číst všechny vlastnosti (včetně privilegovaných) v signInReports v Azure Active Directory. |
| microsoft.aad.identityProtection/allEntities/read | Umožňuje přečíst všechny prostředky v microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Umožňuje přečíst všechny prostředky v microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat službu Azure Service Health. |
| microsoft.office365.webPortal/allEntities/basic/read | Umožňuje číst základní vlastnosti ve všech prostředcích v microsoft.office365.webPortal. |
| microsoft.office365.protectionCenter/allEntities/read | Umožňuje číst všechny aspekty Centra ochrany Office 365. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat stav služby Office 365. |

### <a name="service-support-administrator"></a>Správce podpory služeb
Může číst informace o stavu služby a spravovat lístky podpory.

  > [!NOTE]
  > Tato role má oprávnění další mimo službu Azure Active Directory. Další informace najdete v tématu role popisu výše.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat službu Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Umožňuje vytvářet a spravovat lístky podpory Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Umožňuje číst základní vlastnosti ve všech prostředcích v microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat stav služby Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Umožňuje vytvářet a spravovat lístky podpory Office 365. |

### <a name="sharepoint-service-administrator"></a>Správce služeb SharePointu
Může spravovat všechny aspekty služby SharePoint.

  > [!NOTE]
  > Tato role má oprávnění další mimo službu Azure Active Directory. Další informace najdete v tématu role popisu výše.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/groups/unified/appRoleAssignments/update | Umožňuje aktualizovat vlastnost groups.unified v Azure Active Directory. |
| microsoft.aad.directory/groups/unified/basic/update | Umožňuje aktualizovat základní vlastnosti Skupin Office 365. |
| microsoft.aad.directory/groups/unified/create | Umožňuje vytvořit Skupiny Office 365. |
| microsoft.aad.directory/groups/unified/delete | Umožňuje odstranit Skupiny Office 365. |
| microsoft.aad.directory/groups/unified/members/update | Umožňuje aktualizovat členství ve Skupinách Office 365. |
| microsoft.aad.directory/groups/unified/owners/update | Umožňuje aktualizovat vlastnictví Skupin Office 365. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat službu Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Umožňuje vytvářet a spravovat lístky podpory Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Umožňuje číst základní vlastnosti ve všech prostředcích v microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat stav služby Office 365. |
| microsoft.office365.sharepoint/allEntities/allTasks | Umožňuje vytvářet a odstraňovat všechny prostředky a číst a aktualizovat standardní vlastnosti v microsoft.office365.sharepoint. |
| microsoft.office365.supportTickets/allEntities/allTasks | Umožňuje vytvářet a spravovat lístky podpory Office 365. |

### <a name="teams-communications-administrator"></a>Správce komunikace ve službě Teams
Může spravovat funkce volání a schůzek ve službě Microsoft Teams.

  > [!NOTE]
  > Tato role má oprávnění další mimo službu Azure Active Directory. Další informace najdete v tématu role popisu výše.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat službu Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Umožňuje vytvářet a spravovat lístky podpory Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Umožňuje číst základní vlastnosti ve všech prostředcích v microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat stav služby Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Umožňuje vytvářet a spravovat lístky podpory Office 365. |
| microsoft.office365.usageReports/allEntities/read | Umožňuje číst sestavy využití Office 365. |

### <a name="teams-communications-support-engineer"></a>Odborný technik podpory komunikace ve službě Teams
Může řešit potíže s komunikací ve službě Teams pomocí rozšířených nástrojů.

  > [!NOTE]
  > Tato role má oprávnění další mimo službu Azure Active Directory. Další informace najdete v tématu role popisu výše.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat službu Azure Service Health. |
| microsoft.office365.webPortal/allEntities/basic/read | Umožňuje číst základní vlastnosti ve všech prostředcích v microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat stav služby Office 365. |

### <a name="teams-communications-support-specialist"></a>Specialista technické podpory komunikace ve službě Teams
Může řešit potíže s komunikací ve službě Teams pomocí základních nástrojů.

  > [!NOTE]
  > Tato role má oprávnění další mimo službu Azure Active Directory. Další informace najdete v tématu role popisu výše.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat službu Azure Service Health. |
| microsoft.office365.webPortal/allEntities/basic/read | Umožňuje číst základní vlastnosti ve všech prostředcích v microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat stav služby Office 365. |

### <a name="teams-service-administrator"></a>Správce služby Teams
Může spravovat službu Microsoft Teams. 

  > [!NOTE]
  > Tato role má oprávnění další mimo službu Azure Active Directory. Další informace najdete v tématu role popisu výše.
  >
  >

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/groups/hiddenMembers/read | Umožňuje číst vlastnost groups.hiddenMembers v Azure Active Directory. |
| microsoft.aad.directory/groups/unified/appRoleAssignments/update | Umožňuje aktualizovat vlastnost groups.unified v Azure Active Directory. |
| microsoft.aad.directory/groups/unified/basic/update | Umožňuje aktualizovat základní vlastnosti Skupin Office 365. |
| microsoft.aad.directory/groups/unified/create | Umožňuje vytvořit Skupiny Office 365. |
| microsoft.aad.directory/groups/unified/delete | Umožňuje odstranit Skupiny Office 365. |
| microsoft.aad.directory/groups/unified/members/update | Umožňuje aktualizovat členství ve Skupinách Office 365. |
| microsoft.aad.directory/groups/unified/owners/update | Umožňuje aktualizovat vlastnictví Skupin Office 365. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat službu Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Umožňuje vytvářet a spravovat lístky podpory Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Umožňuje číst základní vlastnosti ve všech prostředcích v microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat stav služby Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Umožňuje vytvářet a spravovat lístky podpory Office 365. |
| microsoft.office365.usageReports/allEntities/read | Umožňuje číst sestavy využití Office 365. |

### <a name="user-account-administrator"></a>Správce uživatelských účtů
Může spravovat všechny aspekty uživatelů a skupin, včetně resetování hesel omezených správců.

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.directory/appRoleAssignments/create | Umožňuje vytvářet appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/delete | Umožňuje odstraňovat appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/update | Umožňuje aktualizovat appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/contacts/basic/update | Umožňuje aktualizovat základní vlastnosti contacts v Azure Active Directory. |
| microsoft.aad.directory/contacts/create | Umožňuje vytvářet contacts v Azure Active Directory. |
| microsoft.aad.directory/contacts/delete | Umožňuje odstraňovat contacts v Azure Active Directory. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Umožňuje aktualizovat vlastnost groups.appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/groups/basic/update | Umožňuje aktualizovat základní vlastnosti groups v Azure Active Directory. |
| microsoft.aad.directory/groups/create | Umožňuje vytvářet groups v Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Umožňuje vytvářet groups v Azure Active Directory. Autor se přidá jako první vlastník a vytvořený objekt počítat do kvóty 250 vytvořených objektů autora. |
| microsoft.aad.directory/groups/delete | Umožňuje odstraňovat groups v Azure Active Directory. |
| microsoft.aad.directory/groups/hiddenMembers/read | Umožňuje číst vlastnost groups.hiddenMembers v Azure Active Directory. |
| microsoft.aad.directory/groups/members/update | Umožňuje aktualizovat vlastnost groups.members v Azure Active Directory. |
| microsoft.aad.directory/groups/owners/update | Umožňuje aktualizovat vlastnost groups.owners v Azure Active Directory. |
| microsoft.aad.directory/groups/restore | Umožňuje obnovovat groups v Azure Active Directory. |
| microsoft.aad.directory/groups/settings/update | Umožňuje aktualizovat vlastnost groups.settings v Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Umožňuje aktualizovat vlastnost users.appRoleAssignments v Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Umožňuje spravovat licence uživatelů v Azure Active Directory. |
| microsoft.aad.directory/users/basic/update | Umožňuje aktualizovat základní vlastnosti users v Azure Active Directory. |
| microsoft.aad.directory/users/create | Umožňuje vytvářet users v Azure Active Directory. |
| microsoft.aad.directory/users/delete | Umožňuje odstraňovat users v Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Umožňuje zneplatnit všechny obnovovací tokeny uživatele v Azure Active Directory. |
| microsoft.aad.directory/users/manager/update | Umožňuje aktualizovat vlastnost users.manager v Azure Active Directory. |
| microsoft.aad.directory/users/password/update | Aktualizace hesla pro všechny uživatele ve službě Azure Active Directory. Najdete v online dokumentaci pro více podrobností. |
| microsoft.aad.directory/users/restore | Umožňuje obnovovat odstraněné uživatele v Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Umožňuje aktualizovat vlastnost users.userPrincipalName v Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat službu Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Umožňuje vytvářet a spravovat lístky podpory Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Umožňuje číst základní vlastnosti ve všech prostředcích v microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Umožňuje číst a konfigurovat stav služby Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Umožňuje vytvářet a spravovat lístky podpory Office 365. |

## <a name="deprecated-roles"></a>Nepoužívané role

Tyto role se nesmí používat. Tyto jsou zastaralé a bude odebrána z Azure AD v budoucnu.

* Správce ad hoc licencí
* Připojení zařízení
* Správci zařízení
* Uživatelé zařízení
* Tvůrce uživatele ověřený e-mailem
* Správce poštovní schránky
* Připojení pracovních zařízení

## <a name="next-steps"></a>Další postup

* Další informace o tom, jak přiřadit uživatele jako správce předplatného Azure, najdete v článku [správě přístupu pomocí RBAC a webu Azure portal](../../role-based-access-control/role-assignments-portal.md)
* Další informace o tom, jak se přístup k prostředkům řídí ve službě Microsoft Azure, najdete v části [Principy přístupu k prostředkům ve službě Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Další informace o vztahu Azure Active Directory k předplatnému Azure najdete v tématu [Jak je předplatné Azure propojeno se službou Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md).

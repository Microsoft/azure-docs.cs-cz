---
title: Sestavy aktivit auditu na portálu Azure Active Directory | Dokumentace Microsoftu
description: Seznámení se sestavami aktivit auditu na portálu Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 09/17/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5aa8f675e3fd36fbebfecf42db0f02b0f0f00115
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2020
ms.locfileid: "92319735"
---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal"></a>Sestavy aktivit auditu na portálu Azure Active Directory 

Pomocí sestav Azure Active Directory (Azure AD) můžete získat informace, které potřebujete k určení toho, jak vaše prostředí dělá.



Architektura vytváření sestav se skládá z následujících součástí:

- **Aktivita** 
    - **Přihlášení** – [Sestava přihlášení](concept-sign-ins.md) poskytuje informace o použití spravovaných aplikací a aktivitách přihlašování uživatelů.
    - **Protokoly auditu** – Zajišťuje sledovatelnost prostřednictvím protokolů všech změn provedených různými funkcemi v rámci Azure AD. Mezi příklady protokolů auditu patří změny provedené u všech prostředků v rámci Azure AD, jako je přidávání nebo odebírání uživatelů, aplikací, skupin, rolí a zásad.
    - **Zřizování protokolů**  -  [Protokoly zřizování](./concept-provisioning-logs.md) umožňují zákazníkům monitorovat aktivity prostřednictvím služby zřizování, například vytvoření skupiny v ServiceNow nebo uživatele naimportovaného z Workday. 
- **Zabezpečení** 
    - **Riziková přihlášení** – [rizikové přihlášení](../identity-protection/overview-identity-protection.md) je indikátorem pokusu o přihlášení, který mohl provést někdo, kdo není legitimním vlastníkem uživatelského účtu. 
    - **Uživatelé označení příznakem rizika** – [rizikové uživatel](../identity-protection/overview-identity-protection.md) je indikátorem uživatelského účtu, který mohl být ohrožen.

Tento článek vám poskytne přehled o sestavě auditu.
 
## <a name="who-can-access-the-data"></a>Kdo má přístup k datům?

* Uživatelé v roli **Správce zabezpečení**, **Čtenář zabezpečení**, **čtečka sestav** , **globální čtenář** nebo **globální správce**

## <a name="audit-logs"></a>Protokoly auditu

Protokoly auditu Azure AD poskytují záznamy systémových aktivit pro dodržování předpisů. Chcete-li získat přístup k sestavě auditu, vyberte v části **monitorování** v **Azure Active Directory**možnost **protokoly auditu** . 



Protokol auditu má výchozí zobrazení seznamu, které obsahuje následující položky:

- datum a čas výskytu
- služba, která protokoluje výskyt
- kategorie a název aktivity (*co*) 
- stav aktivity (úspěch nebo neúspěch)
- cíl
- iniciátor/aktér aktivity (kdo)

![Protokoly auditování](./media/concept-audit-logs/listview.png "Protokoly auditu")

Zobrazení seznamu můžete upravit kliknutím na **Sloupce** na panelu nástrojů.

![Sloupce auditu](./media/concept-audit-logs/columns.png "Sloupce auditu")

To umožňuje zobrazit další pole, nebo odebrat pole, která jsou už zobrazená.

![Odebrat pole](./media/concept-audit-logs/columnselect.png "Odebrat pole")

Chcete-li získat podrobnější informace, vyberte položku v zobrazení seznamu.

![vybrat položku](./media/concept-audit-logs/details.png "Vybrat položku")


## <a name="filtering-audit-logs"></a>Filtrování protokolů auditu

Data auditu můžete filtrovat v následujících polích:

- Služba
- Kategorie
- Aktivita
- Stav
- Cíl
- Spustil(a) (činitel)
- Rozsah dat

![Filter – objekt](./media/concept-audit-logs/filter.png "Filter – objekt")

Filtr **služby** umožňuje vybrat z rozevíracího seznamu následující služby:

- Vše
- UX pro správu AAD
- Kontroly přístupu
- Zřizování účtů
- Proxy aplikací
- Metody ověřování
- B2C
- Podmíněný přístup
- Základní adresář
- Správa nároků
- Hybridní ověřování
- Identity Protection
- Pozvaní uživatelé
- Služba MIM
- MyApps
- PIM
- Samoobslužná správa skupin
- Samoobslužná správa hesel
- Podmínky použití

Filtr **kategorie** umožňuje vybrat jeden z následujících filtrů:

- Vše
- AdministrativeUnit
- ApplicationManagement
- Authentication
- Autorizace
- Kontakt
- Zařízení
- DeviceConfiguration
- DirectoryManagement
- EntitlementManagement
- GroupManagement
- KerberosDomain
- Správa
- Popisek
- Ostatní
- PermissionGrantPolicy
- Zásada
- ResourceManagement
- RoleManagement
- UserManagement

Filtr **aktivity** je založen na typu kategorie a prostředku aktivity, kterou provedete. Můžete vybrat konkrétní aktivitu, kterou chcete zobrazit, nebo zvolit všechny. 

Seznam všech aktivit auditu můžete získat pomocí Graph API: `https://graph.windows.net/<tenantdomain>/activities/auditActivityTypesV2?api-version=beta`

Filtr **stavu** vám umožňuje filtrovat na základě stavu operace auditu. Stav může být jedna z následujících:

- Vše
- Success
- Selhání

**Cílový** filtr vám umožní vyhledat konkrétní cíl podle názvu nebo hlavního názvu uživatele (UPN). Název cíle a hlavní název uživatele (UPN) rozlišují velká a malá písmena. 

Filtr **iniciovaná pomocí** filtru umožňuje definovat, s jakým jménem a názvem objektu actor začíná. Název a hlavní název uživatele (UPN) rozlišují malá a velká písmena.

Filtr **rozsahu data** umožňuje definovat časový rámec pro vracená data.  
Možné hodnoty:

- 7 dní
- 24 hodin
- Vlastní

Když vyberete vlastní časový rámec, můžete nakonfigurovat počáteční a koncový čas.

Můžete si také stáhnout filtrovaná data, až 250 000 záznamů, a to tak, že vyberete tlačítko **Stáhnout** . Protokoly si můžete stáhnout buď ve formátu CSV, nebo ve formátu JSON. Počet záznamů, které si můžete stáhnout, je omezený o [Azure Active Directory zásady uchovávání sestav](reference-reports-data-retention.md).

![Stahování souborů](./media/concept-audit-logs/download.png "Stahování souborů")

## <a name="audit-logs-shortcuts"></a>Zástupci pro protokoly auditu

Kromě **Azure Active Directory** poskytuje web Azure Portal dva další vstupní body k datům auditu:

- Uživatelé a skupiny
- Podnikové aplikace

### <a name="users-and-groups-audit-logs"></a>Protokoly auditu uživatelů a skupin

S použitím sestav auditu orientovaných na uživatele a skupiny můžete najít odpovědi na otázky tohoto typu:

- Jaké typy aktualizací byly pro uživatele aplikovány?

- Kolik uživatelů bylo změněno?

- Kolik hesel bylo změněno?

- Co provedl správce v adresáři?

- Které skupiny byly přidány?

- Došlo u některých skupin ke změnám členství?

- Došlo ke změnám vlastníků skupiny?

- Jaké licence byly přiřazeny skupině nebo uživateli?

Chcete-li zkontrolovat pouze auditovaná data, která se vztahují k uživatelům, můžete filtrované zobrazení najít v části **protokoly auditu** v části **monitorování** na kartě **Uživatelé** . Tento vstupní bod má **UserManagement** jako předvybranou kategorii.

![Uživatel](./media/concept-audit-logs/users.png "Uživatel")

Pokud chcete zkontrolovat jenom auditovaná data, která souvisí se skupinami, najdete filtrované zobrazení v části **protokoly auditu** v části **monitorování** na kartě **skupiny** . Tento vstupní bod má **GroupManagement** jako předvybranou kategorii.

![Filtrovat skupiny](./media/concept-audit-logs/groups.png "Filtrovat skupiny")

### <a name="enterprise-applications-audit-logs"></a>Protokoly auditu podnikových aplikací

S použitím sestav auditu orientovaných na aplikace můžete najít odpovědi na otázky tohoto typu:

* Jaké aplikace byly přidány nebo aktualizovány?
* Které aplikace byly odebrány?
* Změnil se objekt služby pro aplikaci?
* Změnily se názvy aplikací?
* Kdo udělil souhlas pro aplikaci?

Pokud chcete zkontrolovat data auditu související s vašimi aplikacemi, najdete filtrované zobrazení v části **protokoly auditu** v části **aktivita** v okně **podnikové aplikace** . Tento vstupní bod má jako **Typ aplikace**předvybranou možnost **podnikové aplikace** .

![Podnikové aplikace](./media/concept-audit-logs/enterpriseapplications.png "Podnikové aplikace")

## <a name="microsoft-365-activity-logs"></a>Protokoly aktivit Microsoft 365

Protokoly aktivit Microsoft 365 můžete zobrazit v centru pro [správu Microsoft 365](/office365/admin/admin-overview/about-the-admin-center). I když Microsoft 365 aktivity a protokoly aktivit služby Azure AD sdílejí spoustu prostředků adresáře, zobrazí se úplné zobrazení protokolů aktivit Microsoft 365 jenom v centru pro správu Microsoft 365. 

Přístup k protokolům aktivit Microsoft 365 lze také programově pomocí [rozhraní API pro správu sady Office 365](/office/office-365-management-api/office-365-management-apis-overview).

## <a name="next-steps"></a>Další kroky

- [Referenční informace k aktivitám auditování Azure AD](reference-audit-activities.md)
- [Referenční informace o uchovávání sestav Azure AD](reference-reports-data-retention.md)
- [Reference latence protokolu Azure AD](reference-reports-latencies.md)
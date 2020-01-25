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
ms.date: 07/17/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 49b49949c1765c3cb1598d728e21479c65037930
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76714492"
---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal"></a>Sestavy aktivit auditu na portálu Azure Active Directory 

Pomocí sestav Azure Active Directory (Azure AD) můžete získat informace, které potřebujete k určení toho, jak vaše prostředí dělá.

Architektura vytváření sestav se skládá z následujících součástí:

- **Aktivita** 
    - **Přihlášení** – [Sestava přihlášení](concept-sign-ins.md) poskytuje informace o použití spravovaných aplikací a aktivitách přihlašování uživatelů.
    - **Protokoly auditu** – Zajišťuje sledovatelnost prostřednictvím protokolů všech změn provedených různými funkcemi v rámci Azure AD. Mezi příklady protokolů auditu patří změny provedené u všech prostředků v rámci Azure AD, jako je přidávání nebo odebírání uživatelů, aplikací, skupin, rolí a zásad.
- **Zabezpečení** 
    - **Riziková přihlášení** – [rizikové přihlášení](concept-risky-sign-ins.md) je indikátorem pokusu o přihlášení, který mohl provést někdo, kdo není legitimním vlastníkem uživatelského účtu. 
    - **Uživatelé označení příznakem rizika** – [rizikové uživatel](concept-user-at-risk.md) je indikátorem uživatelského účtu, který mohl být ohrožen.

Tento článek vám poskytne přehled o sestavě auditu.
 
## <a name="who-can-access-the-data"></a>Kdo má přístup k datům?

* Uživatelé v rolích **Správce zabezpečení**, **Čtenář zabezpečení**, **Čtenář sestav** nebo **globální role správce**

## <a name="audit-logs"></a>Protokoly auditu

Protokoly auditu Azure AD poskytují záznamy systémových aktivit pro dodržování předpisů. Chcete-li získat přístup k sestavě auditu, vyberte v části **aktivita** v **Azure Active Directory**možnost **protokoly auditu** . Všimněte si, že protokoly auditu můžou mít latenci až hodinu, takže může trvat dlouhou dobu, než se na portálu po dokončení úkolu zobrazí data o aktivitě auditu.



Protokol auditu má výchozí zobrazení seznamu, které obsahuje následující položky:

- datum a čas výskytu
- služba, která protokoluje výskyt
- kategorie a název aktivity (*co*) 
- stav aktivity (úspěch nebo neúspěch)
- cíl
- Iniciátor/actor (kdo) aktivity

![Protokoly auditu](./media/concept-audit-logs/listview.png "Protokoly auditu")

Zobrazení seznamu můžete upravit kliknutím na **Sloupce** na panelu nástrojů.

![Protokoly auditu](./media/concept-audit-logs/columns.png "Protokoly auditu")

To umožňuje zobrazit další pole, nebo odebrat pole, která jsou už zobrazená.

![Protokoly auditu](./media/concept-audit-logs/columnselect.png "Protokoly auditu")

Chcete-li získat podrobnější informace, vyberte položku v zobrazení seznamu.

![Protokoly auditu](./media/concept-audit-logs/details.png "Protokoly auditu")


## <a name="filtering-audit-logs"></a>Filtrování protokolů auditu

Data auditu můžete filtrovat v následujících polích:

- Služba
- Kategorie
- Aktivita
- Stav
- Výběr cílového umístění
- Spustil(a) (činitel)
- Rozsah dat

![Protokoly auditu](./media/concept-audit-logs/filter.png "Protokoly auditu")

Filtr **služby** umožňuje vybrat z rozevíracího seznamu následující služby:

- Všechno
- Kontroly přístupu
- Zřizování účtů 
- Jednotné přihlašování aplikace
- Metody ověřování
- B2C
- Podmíněný přístup
- Základní adresář
- Správa nároků
- Ochrany identit
- Pozvaní uživatelé
- PIM
- Samoobslužná správa skupin
- Samoobslužná správa hesel
- Podmínky použití

Filtr **kategorie** umožňuje vybrat jeden z následujících filtrů:

- Všechno
- AdministrativeUnit
- ApplicationManagement
- Ověření
- Autorizace
- Kontaktovat
- Zařízení
- DeviceConfiguration
- DirectoryManagement
- EntitlementManagement
- GroupManagement
- Jiné
- Zásady
- ResourceManagement
- RoleManagement
- UserManagement

Filtr **aktivity** je založen na typu kategorie a prostředku aktivity, kterou provedete. Můžete vybrat konkrétní aktivitu, kterou chcete zobrazit, nebo zvolit všechny. 

Seznam všech aktivit auditu můžete získat pomocí Graph API: `https://graph.windows.net/<tenantdomain>/activities/auditActivityTypesV2?api-version=beta`

Filtr **stavu** vám umožňuje filtrovat na základě stavu operace auditu. Stav může být jedna z následujících:

- Všechno
- Úspěch
- Selhání

**Cílový** filtr vám umožní vyhledat konkrétní cíl podle názvu nebo hlavního názvu uživatele (UPN). Název cíle a hlavní název uživatele (UPN) rozlišují velká a malá písmena. 

Filtr **iniciovaná pomocí** filtru umožňuje definovat jméno objektu actor nebo univerzální hlavní název uživatele (UPN). Název a hlavní název uživatele (UPN) rozlišují malá a velká písmena.

Filtr **rozsahu data** umožňuje definovat časový rámec pro vracená data.  
Možné hodnoty:

- 1 měsíc
- 7 dní
- 24 hodin
- Vlastní

Když vyberete vlastní časový rámec, můžete nakonfigurovat počáteční a koncový čas.

Můžete si také stáhnout filtrovaná data, až 250 000 záznamů, a to tak, že vyberete tlačítko **Stáhnout** . Protokoly si můžete stáhnout buď ve formátu CSV, nebo ve formátu JSON. Počet záznamů, které si můžete stáhnout, je omezený o [Azure Active Directory zásady uchovávání sestav](reference-reports-data-retention.md).

![Protokoly auditu](./media/concept-audit-logs/download.png "Protokoly auditu")

## <a name="audit-logs-shortcuts"></a>Zástupci pro protokoly auditu

Kromě **Azure Active Directory** poskytuje web Azure Portal dva další vstupní body k datům auditu:

- Uživatelé a skupiny
- Podnikové aplikace

### <a name="users-and-groups-audit-logs"></a>Protokoly auditu uživatelů a skupin

S použitím sestav auditu orientovaných na uživatele a skupiny můžete najít odpovědi na otázky tohoto typu:

- Jaké typy aktualizací uživatelé použili?

- Kolik uživatelů bylo změněno?

- Kolik hesel bylo změněno?

- Co provedl správce v adresáři?

- Které skupiny byly přidány?

- Došlo u některých skupin ke změnám členství?

- Došlo ke změnám vlastníků skupiny?

- Jaké licence byly přiřazeny skupině nebo uživateli?

Pokud chcete zkontrolovat jenom auditovaná data, která se vztahují k uživatelům, můžete filtrované zobrazení najít v části **protokoly auditu** v části **aktivita** na kartě **Uživatelé** . Tento vstupní bod má **UserManagement** jako předvybranou kategorii.

![Protokoly auditu](./media/concept-audit-logs/users.png "Protokoly auditu")

Pokud chcete zkontrolovat jenom auditovaná data, která souvisí se skupinami, najdete filtrované zobrazení v části **protokoly auditu** v části **aktivita** na kartě **skupiny** . Tento vstupní bod má **GroupManagement** jako předvybranou kategorii.

![Protokoly auditu](./media/concept-audit-logs/groups.png "Protokoly auditu")

### <a name="enterprise-applications-audit-logs"></a>Protokoly auditu podnikových aplikací

S použitím sestav auditu orientovaných na aplikace můžete najít odpovědi na otázky tohoto typu:

* Jaké aplikace byly přidány nebo aktualizovány?
* Které aplikace byly odebrány?
* Změnil se objekt služby pro aplikaci?
* Změnily se názvy aplikací?
* Kdo udělil souhlas pro aplikaci?

Pokud chcete zkontrolovat data auditu související s vašimi aplikacemi, najdete filtrované zobrazení v části **protokoly auditu** v části **aktivita** v okně **podnikové aplikace** . Tento vstupní bod má jako **Typ aplikace**předvybranou možnost **podnikové aplikace** .

![Protokoly auditu](./media/concept-audit-logs/enterpriseapplications.png "Protokoly auditu")

## <a name="office-365-activity-logs"></a>Protokoly aktivit Office 365

Protokoly aktivit Office 365 můžete zobrazit v centru pro [správu Microsoft 365](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center). I když aktivity Office 365 a protokoly aktivit služby Azure AD sdílejí spoustu prostředků adresáře, zobrazí se úplné zobrazení protokolů aktivit Office 365 jenom v centru pro správu Microsoft 365. 

Přístup k protokolům aktivit Office 365 můžete také programově pomocí [rozhraní API pro správu sady office 365](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview).

## <a name="next-steps"></a>Další kroky

- [Referenční informace o aktivitě auditu Azure AD](reference-audit-activities.md)
- [Referenční informace o uchovávání sestav Azure AD](reference-reports-data-retention.md)
- [Reference latence protokolu Azure AD](reference-reports-latencies.md)

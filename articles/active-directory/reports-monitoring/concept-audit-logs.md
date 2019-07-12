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
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 59f716ccd7b2e02c9064f13cf1ffd6e8180858c9
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827916"
---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal"></a>Sestavy aktivit auditu na portálu Azure Active Directory 

Pomocí sestav Azure Active Directory (Azure AD) můžete získat informace, které potřebujete ke zjištění stavu vašeho prostředí.

Architektura generování sestav se skládá z následujících součástí:

- **Aktivita** 
    - **Přihlášení** – [sestavy přihlášení](concept-sign-ins.md) poskytuje informace o využití spravovaných aplikací a uživatel aktivit přihlašování.
    - **Protokoly auditu** – Zajišťuje sledovatelnost prostřednictvím protokolů všech změn provedených různými funkcemi v rámci Azure AD. Mezi příklady protokolů auditu patří změny jakýchkoli prostředků v rámci Azure AD, jako jsou přidávání nebo odebírání uživatelů, aplikace, skupiny, role a zásady.
- **Zabezpečení** 
    - **Riziková přihlášení** – [rizikových přihlášení](concept-risky-sign-ins.md) je indikátorem pokusu přihlásit, který mohl provést někdo, kdo není legitimním vlastníkem uživatelského účtu. 
    - **Uživatelé označení příznakem rizika** – [rizikový uživatel](concept-user-at-risk.md) je indikátorem uživatelského účtu, který mohl být ohrožený.

Tento článek obsahuje přehled sestavy auditu.
 
## <a name="who-can-access-the-data"></a>Kdo má přístup k datům?

* Uživatelé v **správce zabezpečení**, **Čtenář zabezpečení**, **čtenáře sestav** nebo **globálního správce** role
* Kromě toho všichni uživatelé (bez oprávnění správce) mohou zobrazit své vlastní aktivity auditu

## <a name="audit-logs"></a>Protokoly auditu

Auditování Azure AD protokoly obsahují záznamy aktivit systému kvůli dodržování předpisů. Pro přístup k sestavě auditu, vyberte **protokoly auditu** v **aktivity** část **Azure Active Directory**. Všimněte si, že protokoly auditu mohou latenci až hodinu, tak může trvat dlouho to pro data aktivit auditu se zobrazí na portálu po dokončení úlohy.

![Protokoly auditu](./media/concept-audit-logs/61.png "Protokoly auditu")

Protokol auditu má výchozí zobrazení seznamu, které obsahuje následující položky:

- datum a čas výskytu
- Služba, která způsobila výskytu
- kategorie a název aktivity (*co*) 
- Stav aktivity (úspěch nebo neúspěch)
- cíl
- Iniciátor / objektu actor (kdo) aktivity

![Protokoly auditu](./media/concept-audit-logs/listview.png "Protokoly auditu")

Zobrazení seznamu můžete upravit kliknutím na **Sloupce** na panelu nástrojů.

![Protokoly auditu](./media/concept-audit-logs/columns.png "Protokoly auditu")

To umožňuje zobrazit další pole, nebo odebrat pole, která jsou už zobrazená.

![Protokoly auditu](./media/concept-audit-logs/columnselect.png "Protokoly auditu")

Vyberte položku v zobrazení seznamu zobrazíte podrobnější informace.

![Protokoly auditu](./media/concept-audit-logs/details.png "Protokoly auditu")


## <a name="filtering-audit-logs"></a>Filtrování protokolů auditu

Můžete filtrovat data auditu pro následující pole:

- Služba
- Kategorie
- Aktivita
- Stav
- Target
- Spustil(a) (činitel)
- Rozsah dat

![Protokoly auditu](./media/concept-audit-logs/filter.png "Protokoly auditu")

**Služby** filtr umožňuje vyberte z rozevíracího seznamu z následujících služeb:

- Vše
- Kontroly přístupu
- Zřizování účtů 
- Aplikace SSO
- Metody ověřování
- B2C
- Podmíněný přístup
- Základní adresář
- Správa nároků
- Identity Protection
- Pozvaní uživatelé
- PIM
- Samoobslužná správa skupin
- Správa samoobslužných služeb Passord
- Podmínky použití

**Kategorie** filtr umožňuje vybrat jednu z následujících filtrů:

- Vše
- AdministrativeUnit
- ApplicationManagement
- Ověřování
- Authorization
- Kontakt
- Zařízení
- DeviceConfiguration
- DirectoryManagement
- EntitlementManagement
- GroupManagement
- Ostatní
- Zásada
- ResourceManagement
- RoleManagement
- UserManagement

**Aktivity** filtr podle kategorií a aktivit prostředků typ výběru provedete. Můžete vybrat konkrétní aktivitu, kterou chcete zobrazit, nebo zvolit všechny. 

Seznam všech aktivit auditu můžete získat pomocí Graph API https://graph.windows.net/ $tenantdomain/activities/auditActivityTypes?api-version=beta, kde $tenantdomain = název domény. Také se můžete podívat na článek o [událostech sestavy auditování](reference-audit-activities.md).

**Stav** filtrování umožňuje filtrovat na základě stavu operace auditu. Stav může být jeden z následujících akcí:

- Vše
- Úspěch
- Selhání

**Cílové** filtr umožňuje hledat podle názvu nebo hlavní název uživatele (UPN) pro konkrétní cíl. Název cíle a hlavní název uživatele jsou malá a velká písmena. 

**Iniciovaných** filtr umožňuje definovat jméno prvek "actor" nebo univerzální hlavní název (UPN). Název a hlavní název uživatele jsou malá a velká písmena.

**Rozsah kalendářních dat** filtr umožňuje definovat časový rámec pro vracená data.  
Možné hodnoty jsou:

- 1 měsíc
- 7 dní
- 24 hodin
- Vlastní

Když vyberete vlastní časový rámec, můžete nakonfigurovat počáteční a koncový čas.

Můžete také stáhnout tak, že vyberete filtrovaná data, až 250 000 záznamů, **Stáhnout** tlačítko. Můžete stáhnout protokoly ve formátu CSV nebo JSON. Počet záznamů, které si můžete stáhnout, které je omezená [zásady uchování sestav Azure Active Directory](reference-reports-data-retention.md).

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

Pokud chcete jenom zkontrolovat data auditování týkající se uživatelů, najdete filtrované zobrazení v sekci **protokoly auditu** v **aktivity** část **uživatelé** kartu. Tento vstupní bod má **entit správy uživatelů** jako Zkontrolujte předem vybrané kategorie.

![Protokoly auditu](./media/concept-audit-logs/users.png "Protokoly auditu")

Pokud chcete jenom zkontrolovat data auditování týkající se skupiny, najdete filtrované zobrazení v sekci **protokoly auditu** v **aktivity** část **skupiny** kartu. Tento vstupní bod má **GroupManagement** jako Zkontrolujte předem vybrané kategorie.

![Protokoly auditu](./media/concept-audit-logs/groups.png "Protokoly auditu")

### <a name="enterprise-applications-audit-logs"></a>Protokoly auditu podnikových aplikací

S použitím sestav auditu orientovaných na aplikace můžete najít odpovědi na otázky tohoto typu:

* Které aplikace byly přidány nebo aktualizovány?
* Které aplikace byly odebrány?
* Změnil se instanční objekt pro aplikaci?
* Změnily se názvy aplikací?
* Kdo udělil souhlas pro aplikaci?

Pokud chcete zkontrolovat data auditování týkající se aplikací, najdete filtrované zobrazení v sekci **protokoly auditu** v **aktivity** část **podnikové aplikace** okno. Tento vstupní bod má **podnikové aplikace** Zkontrolujte předem vybrané jako **typ aplikace**.

![Protokoly auditu](./media/concept-audit-logs/enterpriseapplications.png "Protokoly auditu")

## <a name="office-365-activity-logs"></a>Protokoly aktivit Office 365

Můžete zobrazit protokoly aktivit Office 365 z [centra pro správu služeb Microsoft 365](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center). I když aktivita Office 365 a Azure AD aktivity protokoly sdílejí velké množství prostředků adresáře pouze centru pro správu služeb Microsoft 365 poskytuje úplný přehled protokolů aktivit Office 365. 

Programově pomocí protokolů aktivit Office 365 se dá dostat taky [rozhraní API pro správu Office 365](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview).

## <a name="next-steps"></a>Další postup

- [Referenční informace aktivit auditu Azure AD](reference-audit-activities.md)
- [Odkaz na uchování sestavy Azure AD](reference-reports-data-retention.md)
- [Odkazovat na latenci protokolu Azure AD](reference-reports-latencies.md)

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
ms.openlocfilehash: ad399fc24b2cdfbdc51e7feccba2c05786216b19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253231"
---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal"></a>Sestavy aktivit auditu na portálu Azure Active Directory 

Pomocí sestav Azure Active Directory (Azure AD) můžete získat informace, které potřebujete k určení, jak se vaše prostředí daří.

Architektura vykazování se skládá z následujících součástí:

- **Činnosti** 
    - **Přihlášení** – sestava [přihlášení](concept-sign-ins.md) obsahuje informace o využití spravovaných aplikací a aktivity přihlášení uživatelů.
    - **Protokoly auditu** – Zajišťuje sledovatelnost prostřednictvím protokolů všech změn provedených různými funkcemi v rámci Azure AD. Příklady protokolů auditu zahrnují změny provedené ve všech prostředcích v rámci Azure AD, jako je přidání nebo odebrání uživatelů, aplikací, skupin, rolí a zásad.
- **Zabezpečení** 
    - **Riskantní přihlášení** – riskantní [přihlášení](concept-risky-sign-ins.md) je indikátorem pro pokus o přihlášení, který mohl provést někdo, kdo není legitimním vlastníkem uživatelského účtu. 
    - **Uživatelé označeni jako riziko** – [rizikový uživatel](concept-user-at-risk.md) je indikátorem uživatelského účtu, který mohl být ohrožen.

Tento článek poskytuje přehled sestavy auditu.
 
## <a name="who-can-access-the-data"></a>Kdo má přístup k datům?

* Uživatelé v **rolích Správce zabezpečení**, **Čtečka zabezpečení**, **Čtečka sestav** , **Globální čtečka** nebo **Globální správce**

## <a name="audit-logs"></a>Protokoly auditu

Protokoly auditu Azure AD poskytují záznamy o systémových aktivitách pro dodržování předpisů. Chcete-li získat přístup k sestavě auditu, vyberte **protokoly auditu** v části **Monitorování** služby **Azure Active Directory**. Všimněte si, že protokoly auditu může mít latenci až hodinu, takže může trvat tak dlouho, než se data auditní aktivity zobrazí na portálu po dokončení úkolu.



Protokol auditu má výchozí zobrazení seznamu, které obsahuje následující položky:

- datum a čas výskytu
- služba, která výskyt zaznamenala
- kategorie a název aktivity (*co*) 
- stav aktivity (úspěch nebo neúspěch)
- cíl
- iniciátor/aktér aktivity (kdo)

![Protokoly auditu](./media/concept-audit-logs/listview.png "Protokoly auditu")

Zobrazení seznamu můžete upravit kliknutím na **Sloupce** na panelu nástrojů.

![Protokoly auditu](./media/concept-audit-logs/columns.png "Protokoly auditu")

To umožňuje zobrazit další pole, nebo odebrat pole, která jsou už zobrazená.

![Protokoly auditu](./media/concept-audit-logs/columnselect.png "Protokoly auditu")

Vyberte položku v zobrazení seznamu, abyste získali podrobnější informace.

![Protokoly auditu](./media/concept-audit-logs/details.png "Protokoly auditu")


## <a name="filtering-audit-logs"></a>Filtrování protokolů auditu

Data auditu můžete filtrovat v následujících polích:

- Služba
- Kategorie
- Aktivita
- Status
- Cíl
- Spustil(a) (činitel)
- Rozsah dat

![Protokoly auditu](./media/concept-audit-logs/filter.png "Protokoly auditu")

Filtr **Služba** umožňuje vybrat z rozevíracího seznamu následujících služeb:

- Všechny
- AAD Management UX
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

Filtr **Kategorie** umožňuje vybrat jeden z následujících filtrů:

- Všechny
- AdministrativeUnit
- Správa aplikací
- Ověřování
- Autorizace
- Kontakt
- Zařízení
- DeviceConfiguration
- Správa adresářů
- Správa nároků
- Správa skupiny
- KerberosDomain
- Správa klíčů
- Popisek
- Ostatní
- Zásady udělování oprávnění
- Zásada
- Správa zdrojů
- Správa rolí
- Správa uživatelů

Filtr **Aktivita** je založen na výběru kategorie a typu zdroje aktivity, který provedete. Můžete vybrat konkrétní aktivitu, kterou chcete zobrazit, nebo zvolit všechny. 

Seznam všech aktivit auditování můžete získat pomocí rozhraní GRAPH API:`https://graph.windows.net/<tenantdomain>/activities/auditActivityTypesV2?api-version=beta`

Filtr **Stav** umožňuje filtrovat na základě stavu operace auditu. Stav může být jeden z následujících:

- Všechny
- Úspěch
- Selhání

Cílový **Target** filtr umožňuje vyhledat konkrétní cíl podle počátečního jména nebo hlavního jména uživatele (UPN). Cílový název a hlavní název rozlišují malá a velká písmena. 

Filtr **Iniciováno** umožňuje definovat, čím začíná název objektu actor nebo univerzální hlavní název (UPN). Název a hlavní název jsou rozlišována malá a velká písmena.

Filtr **Rozsah dat** umožňuje definovat časový rámec vrácených dat.  
Možné hodnoty:

- 7 dní
- 24 hodin
- Vlastní

Když vyberete vlastní časový rámec, můžete nakonfigurovat počáteční a koncový čas.

Výběrem tlačítka **Stáhnout** můžete také stáhnout filtrovaná data s až 250 000 záznamy. Protokoly si můžete stáhnout ve formátu CSV nebo JSON. Počet záznamů, které si můžete stáhnout, je omezen [zásadami uchovávání zpráv služby Azure Active Directory](reference-reports-data-retention.md).

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

Pokud chcete zkontrolovat pouze auditování dat, která se vztahují k uživatelům, můžete najít filtrované zobrazení v části **Protokoly auditu** v části **Monitorování** na kartě **Uživatelé.** Tento vstupní bod má **UserManagement** jako předem vybranou kategorii.

![Protokoly auditu](./media/concept-audit-logs/users.png "Protokoly auditu")

Pokud chcete zkontrolovat pouze data auditování, která se vztahují ke skupinám, můžete najít filtrované zobrazení v části **Protokoly auditu** v části **Monitorování** na kartě **Skupiny.** Tento vstupní bod má **GroupManagement** jako předem vybranou kategorii.

![Protokoly auditu](./media/concept-audit-logs/groups.png "Protokoly auditu")

### <a name="enterprise-applications-audit-logs"></a>Protokoly auditu podnikových aplikací

S použitím sestav auditu orientovaných na aplikace můžete najít odpovědi na otázky tohoto typu:

* Jaké aplikace byly přidány nebo aktualizovány?
* Jaké aplikace byly odebrány?
* Změnil se instanční objekt aplikace?
* Změnily se názvy aplikací?
* Kdo udělil souhlas pro aplikaci?

Pokud chcete zkontrolovat data auditu související s vašimi aplikacemi, můžete najít filtrované zobrazení v **protokolech auditování** v části **Aktivita** v okně **Podnikové aplikace.** Tento vstupní bod má **podnikové aplikace** předvolené jako **typ aplikace**.

![Protokoly auditu](./media/concept-audit-logs/enterpriseapplications.png "Protokoly auditu")

## <a name="office-365-activity-logs"></a>Protokoly aktivit Office 365

Protokoly aktivit Office 365 si můžete prohlédnout v [Centru pro správu Microsoftu 365](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center). I když aktivity Office 365 a protokoly aktivit Azure AD sdílejí velké množství prostředků adresáře, pouze Centrum pro správu Microsoftu 365 poskytuje úplné zobrazení protokolů aktivit Office 365. 

K protokolům aktivit Office 365 můžete také přistupovat programově pomocí [api pro správu Office 365](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview).

## <a name="next-steps"></a>Další kroky

- [Referenční informace k aktivitám auditování Azure AD](reference-audit-activities.md)
- [Odkaz na uchovávání sestav Azure AD](reference-reports-data-retention.md)
- [Odkaz na latence protokolu Azure AD](reference-reports-latencies.md)

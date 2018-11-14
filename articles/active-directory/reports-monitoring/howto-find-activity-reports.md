---
title: Vyhledávání sestav aktivit uživatelů Azure Active Directory na webu Azure portal | Dokumentace Microsoftu
description: Zjistěte, ve kterém jsou sestavy aktivit uživatelů Azure Active Directory na webu Azure Portal.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: fab94088d1d54012a955b0663b078d03b13d6299
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2018
ms.locfileid: "51624908"
---
# <a name="find-activity-reports-in-the-azure-portal"></a>Vyhledávání sestav aktivit na webu Azure Portal

V tomto článku se dozvíte, jak najít sestavy aktivit uživatelů Azure Active Directory (Azure AD) na webu Azure Portal.

## <a name="audit-logs-report"></a>Sestava protokolů auditu

Sestava protokolů auditu kombinuje několik sestavy na základě aplikace aktivit do jediné zobrazení pro vytváření sestav založené na kontextu. Pro přístup k sestavě protokolů auditu:

1. Přejděte na [Azure Portal](https://portal.azure.com).
2. V pravém horním rohu vyberte svůj adresář a potom vyberte **Azure Active Directory** okno v levém navigačním podokně.
3. Vyberte **protokoly auditu** z **aktivity** část okno Azure Active Directory. 

    ![Protokoly auditu](./media/howto-find-activity-reports/482.png "Protokoly auditu")

Sestava protokolů auditu konsoliduje následující sestavy:

* Sestava auditování
* Aktivity resetování hesla
* Registrace aktivita resetování hesla
* Aktivita samoobslužných skupin
* Změny názvu skupiny Office 365
* Aktivita zřizování účtů
* Stav hesla při přechodu myší
* Chyby zřizování účtů

### <a name="filtering-on-audit-logs"></a>Filtrování protokolů auditování

Rozšířené filtrování sestavy auditu můžete použít pro přístup k určité kategorie dat auditu tak, že ho zadáte **kategorie aktivit** filtru. Například, chcete-li zobrazit všechny aktivity související se samoobslužné resetování hesla, vyberte **Samoobslužná správa hesel** kategorie. 

    ![Category options on the Filter Audit Logs page](./media/howto-find-activity-reports/06.png "Category options on the Filter Audit Logs page")

Kategorie aktivit patří:

- Základní adresář
- Samoobslužná správa hesel
- Samoobslužná správa skupin
- Zřizování účtů


## <a name="sign-ins-report"></a>Sestava přihlášení 

**Přihlášení** zobrazení obsahuje všechny uživatele přihlášení, stejně jako **využití aplikace** sestavy. Také můžete zobrazit informace o použití aplikace v **spravovat** část **podnikové aplikace** Přehled.

    ![Enterprise applications](./media/howto-find-activity-reports/484.png "Enterprise applications")

Pro přístup k sestavě přihlášení:

1. Přejděte na [Azure Portal](https://portal.azure.com).
2. V pravém horním rohu vyberte svůj adresář a potom vyberte **Azure Active Directory** okno v levém navigačním podokně.
3. Vyberte **přehledu přihlášení** z **aktivity** část okno Azure Active Directory. 

    ![Zobrazení přihlášení](./media/howto-find-activity-reports/483.png "zobrazení přihlášení")


### <a name="filtering-on-application-name"></a>Filtrování podle názvu aplikace

Sestava přihlašování slouží k zobrazení podrobností o využití aplikace pomocí filtrování podle uživatelské jméno nebo název aplikace.

![Stránka filtru událostí přihlášení](./media/howto-find-activity-reports/07.png "události přihlášení filtr stránky")

## <a name="security-reports"></a>Sestavy zabezpečení

### <a name="anomalous-activity-reports"></a>Sestavy neobvyklých aktivit

Anomální aktivity sestavy poskytují informace o souvisejících se zabezpečením rizikových událostí, které Azure AD můžete zjišťovat a vytvoření sestavy.

Následující tabulka seznamy Azure AD anomální aktivity zabezpečení zprávy a odpovídající typy rizikových událostí na webu Azure Portal. Další informace najdete v tématu věnovaném [rizikovým událostem služby Azure Active Directory](concept-risk-events.md).  


| Sestava anomální aktivity služby Azure AD |  Typ události rizika identity protection|
| :--- | :--- |
| Uživatelé s uniklými přihlašovacími údaji | Uniklé přihlašovací údaje |
| Nestandardní přihlašovací aktivita | Nemožná cesta do netypických míst |
| Přihlášení z možných nakažených zařízení | Přihlášení z nakažených zařízení|
| Přihlášení z neznámých zdrojů | Přihlášení z anonymních IP adres |
| Přihlášení z IP adres s podezřelou aktivitou | Přihlášení z IP adres s podezřelou aktivitou |
| - | Přihlášení z neznámých míst |

Sestavy zabezpečení anomální aktivity služby Azure AD nejsou zahrnuty jako rizikových událostí na webu Azure Portal:

* Přihlášení po několika neúspěších
* Přihlášení z více geografických poloh


### <a name="detected-risk-events"></a>Zjištěné rizikové události

Můžete přístup k sestavám o zjištěné rizikové události v **zabezpečení** část **Azure Active Directory** okna portálu [webu Azure portal](https://portal.azure.com). Zjištěné rizikové události jsou sledovány v následující sestavy:   

- [Ohrožení uživatelé](concept-user-at-risk.md)
- [Riziková přihlášení](concept-risky-sign-ins.md)

    ![Sestavy zabezpečení](./media/howto-find-activity-reports/04.png "zprávy o zabezpečení")

## <a name="next-steps"></a>Další postup

* [Přehled protokolů auditování](concept-audit-logs.md)
* [Přehled přihlášení](concept-sign-ins.md)
* [Přehled rizikových událostí](concept-risk-events.md)

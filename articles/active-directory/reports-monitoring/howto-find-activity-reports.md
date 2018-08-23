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
ms.date: 12/06/2017
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 182537d6f07b624f2395f591681ed4596579bde0
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2018
ms.locfileid: "42060067"
---
# <a name="find-activity-reports-in-the-azure-portal"></a>Vyhledávání sestav aktivit na webu Azure Portal

V tomto článku probereme možnosti vyhledávání sestav aktivit uživatelů Azure Active Directory na webu Azure Portal.

## <a name="activity-and-integrated-app-reports"></a>Aktivity a integrované aplikace sestav

Na základě kontextu vytváření sestav na webu Azure Portal, existující sestavy jsou sloučeny do jednoho zobrazení. Jediné, základní rozhraní API poskytuje data do zobrazení.

V tomto zobrazení **Azure Active Directory** okně v části **aktivity**vyberte **protokoly auditu**.

![Protokoly auditu](./media/howto-find-activity-reports/482.png "Protokoly auditu")

V tomto zobrazení jsou konsolidovány následující sestavy:

* Sestava auditování
* Aktivity resetování hesla
* Registrace aktivita resetování hesla
* Aktivita samoobslužných skupin
* Změny názvu skupiny Office 365
* Aktivita zřizování účtů
* Stav hesla při přechodu myší
* Chyby zřizování účtů


Sestava využití aplikace byla vylepšena a je součástí **přihlášení** zobrazení. V tomto zobrazení **Azure Active Directory** okně v části **aktivity**vyberte **přihlášení**.

![Zobrazení přihlášení](./media/howto-find-activity-reports/483.png "zobrazení přihlášení")

**Přihlášení** zobrazení zahrnuje všechna přihlášení uživatele. Tyto informace můžete získat informace o využití aplikace. Také můžete zobrazit informace o použití aplikace v **podnikové aplikace** přehled v **SPRAVOVAT** oddílu.

![Podnikové aplikace](./media/howto-find-activity-reports/484.png "podnikových aplikací")

## <a name="access-a-specific-report"></a>Přístup ke konkrétní sestavy

I když na webu Azure portal nabízí jedno zobrazení, můžete také můžete podívat na konkrétní sestavy.

### <a name="audit-logs"></a>Protokoly auditu

V reakci na zpětnou vazbu zákazníků na webu Azure Portal můžete rozšířené filtrování pro přístup k datům, které chcete. Je jeden filtr, můžete použít *kategorie aktivit*, která uvádí různé typy aktivit protokolů ve službě Azure AD. Chcete-li zúžit výsledky co hledáte, můžete vybrat kategorii.

Například pokud se zajímáte pouze aktivit související se resetování hesla pomocí samoobslužné služby, můžete **samoobslužné správy hesel** kategorie. Kategorie, které se zobrazí jsou založeny na prostředek, který se k práci používáte.  

![Kategorie možnosti na stránce protokoly auditu filtr](./media/howto-find-activity-reports/06.png "kategorie možnosti na stránce protokoly auditu filtru")

Kategorie aktivit patří:

- Základní adresář
- Samoobslužná správa hesel
- Samoobslužná správa skupin
- Zřizování účtů

### <a name="application-usage"></a>Využití aplikace

Chcete-li zobrazit podrobnosti o použití aplikace pro všechny aplikace nebo pro jednu aplikaci, v části **aktivity**vyberte **přihlášení**. Chcete-li zúžit výsledky, můžete filtrovat podle uživatelské jméno nebo název aplikace.

![Stránka filtru událostí přihlášení](./media/howto-find-activity-reports/07.png "události přihlášení filtr stránky")

### <a name="security-reports"></a>Sestavy zabezpečení

#### <a name="azure-ad-anomalous-activity-reports"></a>Sestavy neobvyklých aktivit služby Azure AD

Sestavy zabezpečení Azure AD anomální aktivity konsolidované poskytnout centralizovaný pohled na jeden. Toto zobrazení uvádí všechny události související se zabezpečením riziko, že Azure AD můžete zjišťovat a vytvoření sestavy.

Následující tabulka seznamy Azure AD anomální aktivity zabezpečení zprávy a odpovídající typy rizikových událostí na webu Azure Portal.

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

Další informace najdete v tématu věnovaném [rizikovým událostem služby Azure Active Directory](concept-risk-events.md).  


#### <a name="detected-risk-events"></a>Zjištěné rizikové události

Na webu Azure Portal je k dispozici sestavy o zjištěné rizikové události na **Azure Active Directory** okně v části **zabezpečení**. Zjištěné rizikové události jsou sledovány v následující sestavy:   

- Ohrožení uživatelé
- Riziková přihlášení

![Sestavy zabezpečení](./media/howto-find-activity-reports/04.png "zprávy o zabezpečení")

Další informace o sestavách zabezpečení naleznete v tématu:

- [Sestava ohrožených zabezpečení na portálu Azure Active Directory uživatelů](concept-user-at-risk.md)
- [Sestavy rizikových přihlášení na portálu Azure Active Directory](concept-risky-sign-ins.md)


Chcete-li zobrazit **využití aplikace** sestavy na **Azure Active Directory** okně v části **SPRAVOVAT**vyberte **podnikové aplikace**, a pak vyberte **přihlášení**.


![Sestava podnikových aplikací přihlášení](./media/howto-find-activity-reports/199.png)

## <a name="next-steps"></a>Další postup

Přehled generování sestav najdete v tématu [Generování sestav v Azure Active Directory](overview-reports.md).

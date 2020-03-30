---
title: Vyhledání přehledů aktivit uživatelů na webu Azure Portal | Dokumenty společnosti Microsoft
description: Zjistěte, kde jsou sestavy aktivit uživatelů služby Azure Active Directory na webu Azure Portal.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: d356f8c10ae3770d9f4ade100ab0496ee58d772f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74008216"
---
# <a name="find-activity-reports-in-the-azure-portal"></a>Vyhledání sestav aktivit na webu Azure Portal

V tomto článku se dozvíte, jak najít sestavy aktivit uživatelů Služby Azure Active Directory (Azure AD) na webu Azure Portal.

## <a name="audit-logs-report"></a>Sestava protokolů auditu

Sestava protokolů auditu kombinuje několik sestav týkajících se aktivit aplikací do jednoho zobrazení pro kontextové vykazování. Přístup k sestavě protokolů auditu:

1. Přejděte na [Azure Portal](https://portal.azure.com).
2. Vyberte svůj adresář v pravém horním rohu a v levém navigačním podokně vyberte okno **Služby Azure Active Directory.**
3. V yberte **Protokoly auditování** z části **Aktivita** v okně Služby Azure Active Directory. 

    ![Protokoly auditu](./media/howto-find-activity-reports/482.png "Protokoly auditu")

Sestava protokolů auditu konsoliduje následující sestavy:

* Sestava auditování
* Aktivity resetování hesla
* Aktivita registrace resetování hesla
* Aktivita samoobslužných skupin
* Změny názvu skupiny Office365
* Aktivita zřizování účtu
* Stav přechodu hesla
* Chyby zřizování účtů

### <a name="filtering-on-audit-logs"></a>Filtrování protokolů auditu

Rozšířené filtrování v sestavě auditu můžete použít pro přístup k určité kategorii dat auditu zadáním ve filtru **Kategorie.** Chcete-li například zobrazit všechny aktivity související s uživateli, vyberte kategorii **UserManagement.** 

Kategorie zahrnují:

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
- Ostatní
- Zásada
- Správa zdrojů
- Správa rolí
- Správa uživatelů

Můžete také filtrovat na konkrétní služby pomocí **služby** rozbalovací filtr. Chcete-li například získat všechny události auditu související se samoobslužnou správou hesel, vyberte filtr **Samoobslužná správa hesel.**

Služby zahrnují:

- Všechny
- Kontroly přístupu
- Zřizování účtů 
- Přihlašuje se aplikace
- Metody ověřování
- B2C
- Podmíněný přístup
- Základní adresář
- Správa nároků
- Identity Protection
- Pozvaní uživatelé
- PIM
- Samoobslužná správa skupin
- Samoobslužná správa hesel
- Podmínky použití

## <a name="sign-ins-report"></a>Sestava přihlášení 

Zobrazení **Přihlášení** zahrnuje všechna přihlášení uživatelů a také sestavu **Využití aplikace.** Informace o využití aplikací můžete také zobrazit v části **Spravovat** v přehledu **podnikových aplikací.**

Přístup k sestavě přihlášení:

1. Přejděte na [Azure Portal](https://portal.azure.com).
2. Vyberte svůj adresář v pravém horním rohu a v levém navigačním podokně vyberte okno **Služby Azure Active Directory.**
3. V části **Aktivita** v okně Služby Azure Active Directory vyberte **Přihlášení.** 

    ![Zobrazení přihlášení](./media/howto-find-activity-reports/483.png "Zobrazení přihlášení")


### <a name="filtering-on-application-name"></a>Filtrování názvu aplikace

Pomocí sestavy přihlášení můžete zobrazit podrobnosti o využití aplikace filtrováním uživatelského jména nebo názvu aplikace.

![Stránka Události přihlášení filtru](./media/howto-find-activity-reports/07.png "Stránka Události přihlášení filtru")

## <a name="security-reports"></a>Sestavy zabezpečení

### <a name="anomalous-activity-reports"></a>Sestavy anomálních aktivit

Sestavy anomálních aktivit poskytují informace o zjišťování rizik souvisejících se zabezpečením, které azure ad můžete zjistit a sestavy na.

V následující tabulce jsou uvedeny sestavy zabezpečení anomální aktivity Azure AD a odpovídající typy detekce rizik na webu Azure Portal. Další informace naleznete v [tématu Azure Active Directory risk detections](concept-risk-events.md).  


| Sestava anomálních aktivit azure ad |  Typ detekce rizika ochrany identity|
| :--- | :--- |
| Uživatelé s uniklými přihlašovacími údaji | Uniklá pověření |
| Nestandardní přihlašovací aktivita | Nemožná cesta do netypických míst |
| Přihlášení z možných nakažených zařízení | Přihlášení z nakažených zařízení|
| Přihlášení z neznámých zdrojů | Přihlášení z anonymních IP adres |
| Přihlášení z IP adres s podezřelou aktivitou | Přihlášení z IP adres s podezřelou aktivitou |
| - | Přihlášení z neznámých míst |

Následující sestavy monitorování anomální aktivity Azure AD nejsou zahrnuty jako detekce rizik na webu Azure Portal:

* Přihlášení po několika neúspěších
* Přihlášení z více geografických poloh


### <a name="detected-risk-detections"></a>Zjištěné detekce rizik

K sestavám zjištěných detekcí rizik se dostanete v části **Zabezpečení** v okně **Azure Active Directory** na webu Azure [Portal](https://portal.azure.com). Zjištěné detekce rizik jsou sledovány v následujících sestavách:   

- [Ohrožení uživatelé](concept-user-at-risk.md)
- [Riziková přihlášení](concept-risky-sign-ins.md)

    ![Sestavy zabezpečení](./media/howto-find-activity-reports/04.png "Sestavy zabezpečení")

## <a name="troubleshoot-issues-with-activity-reports"></a>Poradce při potížích se sestavami aktivit

### <a name="missing-data-in-the-downloaded-activity-logs"></a>Chybějící data ve stažených protokolech aktivit

#### <a name="symptoms"></a>Příznaky 

Ve stažených protokolech aktivity (auditu nebo přihlášení) se nezobrazují žádné záznamy pro zvolený čas. Proč? 

 ![Vytváření sestav](./media/troubleshoot-missing-data-download/01.png)
 
#### <a name="cause"></a>Příčina

Když si stáhnete protokoly aktivit na webu Azure Portal, omezíme škálování na 250000 záznamů seřazených podle nejnovějších záznamů. 

#### <a name="resolution"></a>Řešení

Můžete využít [rozhraní API pro vytváření sestav Azure AD](concept-reporting-api.md), abyste načetli až milion záznamů v libovolném časovém okamžiku.

### <a name="missing-audit-data-for-recent-actions-in-the-azure-portal"></a>Chybějící data auditu pro nedávné akce na webu Azure Portal

#### <a name="symptoms"></a>Příznaky

Provedl jsem nějaké akce na webu Azure Portal a očekával jsem pro tyto akce zobrazení protokolu auditu v okně `Activity logs > Audit Logs`, ale nemůžu je najít.

 ![Vytváření sestav](./media/troubleshoot-missing-audit-data/01.png)
 
#### <a name="cause"></a>Příčina

Akce se v protokolech aktivit nezobrazí okamžitě. V následující tabulce jsou uvedené naše latence u protokolů aktivit. 

| Sestava | &nbsp; | Latence (P95) | Latence (P99) |
|--------|--------|---------------|---------------|
| Audit adresáře | &nbsp; | 2 minuty | 5 minut |
| Aktivita přihlášení | &nbsp; | 2 minuty | 5 minut | 

#### <a name="resolution"></a>Řešení

Počkejte 15 minut až dvě hodiny a pak se podívejte, jestli se akce v protokolu zobrazily. Pokud se protokoly ani po dvou hodinách nezobrazí, [vytvořte lístek podpory](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) a my se na to podíváme.

### <a name="missing-logs-for-recent-user-sign-ins-in-the-azure-ad-sign-ins-activity-log"></a>Chybějící protokoly pro poslední přihlášení uživatelů v protokolu aktivit přihlášení Služby Azure AD

#### <a name="symptoms"></a>Příznaky

Nedávno jsem se přihlásil/a k webu Azure Portal a očekával/a jsem, že se pro tyto akce zobrazí protokoly přihlášení v okně `Activity logs > Sign-ins`, ale nemůžu je najít.

 ![Vytváření sestav](./media/troubleshoot-missing-audit-data/02.png)
 
#### <a name="cause"></a>Příčina

Akce se v protokolech aktivit nezobrazí okamžitě. V následující tabulce jsou uvedené naše latence u protokolů aktivit. 

| Sestava | &nbsp; | Latence (P95) | Latence (P99) |
|--------|--------|---------------|---------------|
| Audit adresáře | &nbsp; | 2 minuty | 5 minut |
| Aktivita přihlášení | &nbsp; | 2 minuty | 5 minut | 

#### <a name="resolution"></a>Řešení

Počkejte 15 minut až dvě hodiny a pak se podívejte, jestli se akce v protokolu zobrazily. Pokud se protokoly ani po dvou hodinách nezobrazí, [vytvořte lístek podpory](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) a my se na to podíváme.

### <a name="i-cant-view-more-than-30-days-of-report-data-in-the-azure-portal"></a>Na webu Azure Portal nemůžu zobrazit data sestav za více než 30 dnů.

#### <a name="symptoms"></a>Příznaky

Na webu Azure Portal nemůžu zobrazit data přihlášení a auditu za více než 30 dnů. Proč? 

 ![Vytváření sestav](./media/troubleshoot-missing-audit-data/03.png)

#### <a name="cause"></a>Příčina

V závislosti na vaší licenci akce služby Azure Active Directory ukládají sestavy aktivit na tyto počty dní:

| Sestava           | &nbsp; |  Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| ---              | ----   |  ---           | ---                 | ---                 |
| Audit adresáře  | &nbsp; |   7 dní     | 30 dní             | 30 dní             |
| Přihlašovací aktivita | &nbsp; | Není k dispozici. K vlastním přihlášením máte přístup po dobu 7 dnů v okně profilu uživatele. | 30 dní | 30 dní             |

Další informace najdete v tématu [Zásady uchovávání sestav Azure Active Directory](reference-reports-data-retention.md).  

#### <a name="resolution"></a>Řešení

Pokud chcete data uchovávat déle než 30 dnů, máte dvě možnosti. Pomocí [rozhraní API pro generování sestav v Azure AD](concept-reporting-api.md) můžete data načíst prostřednictvím kódu programu a uložit je do databáze. Případně můžete protokoly auditu integrovat do systému SIEM třetí strany, jako je Splunk nebo Sumo Logic.

## <a name="next-steps"></a>Další kroky

* [Přehled protokolů auditu](concept-audit-logs.md)
* [Přehled přihlášení](concept-sign-ins.md)
* [Přehled rizikových událostí](concept-risk-events.md)

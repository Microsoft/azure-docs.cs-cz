---
title: Hledání sestav aktivit uživatelů v Azure Portal | Microsoft Docs
description: Zjistěte, kde jsou sestavy aktivity Azure Active Directory uživatele v Azure Portal.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42afa073da9197c12e4cbd316d311a7699d9a95f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96013017"
---
# <a name="find-activity-reports-in-the-azure-portal"></a>Vyhledání sestav aktivit na webu Azure Portal

V tomto článku se dozvíte, jak v Azure Portal najít sestavy aktivit uživatelů Azure Active Directory (Azure AD).

## <a name="audit-logs-report"></a>Sestava protokolů auditu

Sestava protokoly auditu kombinuje několik sestav kolem aplikačních aktivit do jediného zobrazení pro vytváření sestav na základě kontextu. Přístup k sestavě protokolů auditu:

1. Přejděte na [Azure Portal](https://portal.azure.com).
2. V pravém horním rohu vyberte svůj adresář a pak v levém navigačním podokně vyberte okno **Azure Active Directory** .
3. V části **aktivita** v okně Azure Active Directory vyberte **protokoly auditu** . 

    ![Protokoly auditování](./media/howto-find-activity-reports/482.png "Protokoly auditu")

Sestava protokoly auditu slučuje následující sestavy:

* Sestava auditování
* Aktivity resetování hesla
* Aktivita registrace resetování hesla
* Aktivita samoobslužných skupin
* Změny názvu skupiny Office 365
* Aktivita zřizování účtů
* Stav přecházení hesla
* Chyby zřizování účtů

### <a name="filtering-on-audit-logs"></a>Filtrování protokolů auditu

Pomocí pokročilého filtrování v sestavě audit můžete získat přístup ke konkrétní kategorii dat auditu zadáním do filtru **kategorií** . Chcete-li například zobrazit všechny aktivity související s uživateli, vyberte kategorii **UserManagement** . 

Mezi kategorie patří:

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
- Jiné
- Zásady
- ResourceManagement
- RoleManagement
- UserManagement

Pomocí filtru rozevíracího seznamu **služby** můžete také filtrovat konkrétní službu. Pokud například chcete získat všechny události auditu, které souvisejí se správou hesel samoobslužných služeb, vyberte filtr **samoobslužné správy hesel** .

Služby zahrnují:

- Vše
- Kontroly přístupu
- Zřizování účtů 
- Jednotné přihlašování aplikace
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

Zobrazení **přihlášení** zahrnuje všechna přihlášení uživatelů a sestavu **využití aplikace** . Informace o použití aplikace můžete zobrazit také v části **Správa** v tématu Přehled **podnikových aplikací** .

Přístup k sestavě přihlášení:

1. Přejděte na [Azure Portal](https://portal.azure.com).
2. V pravém horním rohu vyberte svůj adresář a pak v levém navigačním podokně vyberte okno **Azure Active Directory** .
3. V okně Azure Active Directory v části **aktivita** vyberte **nenašla** . 

    ![Zobrazení přihlášení](./media/howto-find-activity-reports/483.png "Zobrazení přihlášení")


### <a name="filtering-on-application-name"></a>Filtrování názvu aplikace

Pomocí sestavy přihlášení můžete zobrazit podrobnosti o využití aplikace, a to filtrováním podle uživatelského jména nebo názvu aplikace.

![Stránka Sign-In události filtru](./media/howto-find-activity-reports/07.png "Stránka Sign-In události filtru")

## <a name="security-reports"></a>Sestavy zabezpečení

### <a name="anomalous-activity-reports"></a>Sestavy aktivit neobvyklé

Sestavy aktivit neobvyklé poskytují informace o detekcích rizik souvisejících se zabezpečením, které může služba Azure AD detekovat a sestavovat.

V následující tabulce jsou uvedené sestavy zabezpečení aktivity Azure AD neobvyklé a odpovídající typy detekce rizik v Azure Portal. Další informace najdete v tématu [Azure Active Directory detekci rizik](../identity-protection/overview-identity-protection.md).  


| Sestava aktivity Azure AD neobvyklé |  Typ detekce rizika ochrany identity|
| :--- | :--- |
| Uživatelé s uniklými přihlašovacími údaji | Uniklé přihlašovací údaje |
| Nestandardní přihlašovací aktivita | Nemožná cesta do netypických míst |
| Přihlášení z možných nakažených zařízení | Přihlášení z nakažených zařízení|
| Přihlášení z neznámých zdrojů | Přihlášení z anonymních IP adres |
| Přihlášení z IP adres s podezřelou aktivitou | Přihlášení z IP adres s podezřelou aktivitou |
| - | Přihlášení z neznámých míst |

Následující sestavy zabezpečení aktivity Azure AD neobvyklé nejsou zahrnuté do zjišťování rizik v Azure Portal:

* Přihlášení po několika neúspěších
* Přihlášení z více geografických poloh


### <a name="detected-risk-detections"></a>Zjištěné detekce rizik

K sestavám zjištěných detekcí rizik můžete získat přístup v části **zabezpečení** okna **Azure Active Directory** v [Azure Portal](https://portal.azure.com). Zjištěná zjištění rizik jsou sledována v následujících sestavách:   

- [Ohrožení uživatelé](../identity-protection/overview-identity-protection.md)
- [Riziková přihlášení](../identity-protection/overview-identity-protection.md)

    ![Sestavy zabezpečení](./media/howto-find-activity-reports/04.png "Sestavy zabezpečení")

## <a name="troubleshoot-issues-with-activity-reports"></a>Řešení potíží se sestavami aktivit

### <a name="missing-data-in-the-downloaded-activity-logs"></a>Chybějící data ve stažených protokolech aktivit

#### <a name="symptoms"></a>Příznaky 

Ve stažených protokolech aktivity (auditu nebo přihlášení) se nezobrazují žádné záznamy pro zvolený čas. Proč? 

 ![Snímek obrazovky se zobrazí v sestavě aktivity tlačítko Stáhnout.](./media/troubleshoot-missing-data-download/01.png)
 
#### <a name="cause"></a>Příčina

Když si stáhnete protokoly aktivit v Azure Portal, omezíme měřítko na 250000 záznamů seřazených podle nejnovějšího prvního. 

#### <a name="resolution"></a>Řešení

Můžete využít [rozhraní API pro vytváření sestav Azure AD](concept-reporting-api.md), abyste načetli až milion záznamů v libovolném časovém okamžiku.

### <a name="missing-audit-data-for-recent-actions-in-the-azure-portal"></a>Chybějící data auditu pro nedávné akce v Azure Portal

#### <a name="symptoms"></a>Příznaky

Provedl jsem nějaké akce na webu Azure Portal a očekával jsem pro tyto akce zobrazení protokolu auditu v okně `Activity logs > Audit Logs`, ale nemůžu je najít.

 ![Snímek obrazovky se zobrazí v sestavě aktivity.](./media/troubleshoot-missing-audit-data/01.png)
 
#### <a name="cause"></a>Příčina

Akce se v protokolech aktivit nezobrazí okamžitě. V následující tabulce jsou uvedené naše latence u protokolů aktivit. 

| Sestava | Latence (P95) | Latence (P99) |
|--------|---------------|---------------|
| Audit adresáře | 2 minuty | 5 minut |
| Aktivita přihlášení | 2 minuty | 5 minut |

#### <a name="resolution"></a>Řešení

Počkejte 15 minut až dvě hodiny a pak se podívejte, jestli se akce v protokolu zobrazily. Pokud se protokoly ani po dvou hodinách nezobrazí, [vytvořte lístek podpory](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) a my se na to podíváme.

### <a name="missing-logs-for-recent-user-sign-ins-in-the-azure-ad-sign-ins-activity-log"></a>Chybějící protokoly pro přihlášení k poslednímu uživateli v protokolu aktivit přihlášení k Azure AD

#### <a name="symptoms"></a>Příznaky

Nedávno jsem se přihlásil/a k webu Azure Portal a očekával/a jsem, že se pro tyto akce zobrazí protokoly přihlášení v okně `Activity logs > Sign-ins`, ale nemůžu je najít.

 ![Snímek obrazovky ukazuje přihlášení pro Azure Active Directory.](./media/troubleshoot-missing-audit-data/02.png)
 
#### <a name="cause"></a>Příčina

Akce se v protokolech aktivit nezobrazí okamžitě. V následující tabulce jsou uvedené naše latence u protokolů aktivit. 

| Sestava | Latence (P95) | Latence (P99) |
|--------|---------------|---------------|
| Audit adresáře | 2 minuty | 5 minut |
| Aktivita přihlášení | 2 minuty | 5 minut |

#### <a name="resolution"></a>Řešení

Počkejte 15 minut až dvě hodiny a pak se podívejte, jestli se akce v protokolu zobrazily. Pokud se protokoly ani po dvou hodinách nezobrazí, [vytvořte lístek podpory](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) a my se na to podíváme.

### <a name="i-cant-view-more-than-30-days-of-report-data-in-the-azure-portal"></a>Na webu Azure Portal nemůžu zobrazit data sestav za více než 30 dnů.

#### <a name="symptoms"></a>Příznaky

Na webu Azure Portal nemůžu zobrazit data přihlášení a auditu za více než 30 dnů. Proč? 

 ![Snímek obrazovky se zobrazí v nabídce datum.](./media/troubleshoot-missing-audit-data/03.png)

#### <a name="cause"></a>Příčina

V závislosti na vaší licenci akce služby Azure Active Directory ukládají sestavy aktivit na tyto počty dní:

| Sestava           | Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| ---              | ---           | ---                 | ---
| Audit adresáře  | 7 dní        | 30 dní             | 30 dní             |
| Přihlašovací aktivita | Není k dispozici. K vlastním přihlášením máte přístup po dobu 7 dnů v okně profilu uživatele. | 30 dní | 30 dní             |

Další informace najdete v tématu [Zásady uchovávání sestav Azure Active Directory](reference-reports-data-retention.md).  

#### <a name="resolution"></a>Řešení

Pokud chcete data uchovávat déle než 30 dnů, máte dvě možnosti. Pomocí [rozhraní API pro generování sestav v Azure AD](concept-reporting-api.md) můžete data načíst prostřednictvím kódu programu a uložit je do databáze. Případně můžete protokoly auditu integrovat do systému SIEM třetí strany, jako je Splunk nebo Sumo Logic.

## <a name="next-steps"></a>Další kroky

* [Přehled protokolů auditu](concept-audit-logs.md)
* [Přehled přihlášení](concept-sign-ins.md)
* [Přehled rizikových událostí](../identity-protection/overview-identity-protection.md)
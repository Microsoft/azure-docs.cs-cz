---
title: Vyhledávání sestav aktivit uživatelů Azure Active Directory na webu Azure portal | Dokumentace Microsoftu
description: Zjistěte, ve kterém jsou sestavy aktivit uživatelů Azure Active Directory na webu Azure Portal.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 9c7560dc6c3c5237a056a9468d3ab4f440e8e6dc
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55190781"
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

## <a name="troubleshoot-issues-with-activity-reports"></a>Řešení potíží se sestavami aktivit

### <a name="missing-data-in-the-downloaded-activity-logs"></a>Chybějící data v protokolech stažené aktivity

#### <a name="symptoms"></a>Příznaky 

Ve stažených protokolech aktivity (auditu nebo přihlášení) se nezobrazují žádné záznamy pro zvolený čas. Proč? 

 ![Vytváření sestav](./media/troubleshoot-missing-data-download/01.png)
 
#### <a name="cause"></a>Příčina

Když si stáhnete protokoly aktivity na webu Azure Portal, Omezujeme rozsah na 5000 záznamů řazených od nejnovější je první. 

#### <a name="resolution"></a>Řešení

Můžete využít [rozhraní API pro vytváření sestav Azure AD](concept-reporting-api.md), abyste načetli až milion záznamů v libovolném časovém okamžiku. Náš doporučený postup je [spuštění skriptu na základě plánu](tutorial-signin-logs-download-script.md) , která volá rozhraní API pro generování sestav k načtení záznamy přírůstkově v časovém intervalu času (například denně nebo týdně). 

### <a name="missing-audit-data-for-recent-actions-in-the-azure-portal"></a>Chybějící data auditu pro poslední akce na webu Azure Portal

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

### <a name="missing-logs-for-recent-user-sign-ins-in-the-azure-ad-sign-ins-activity-log"></a>Protokolovat chybějící protokoly pro poslední přihlášení uživatelů v rámci aktivity přihlášení Azure AD

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

## <a name="next-steps"></a>Další postup

* [Přehled protokolů auditování](concept-audit-logs.md)
* [Přehled přihlášení](concept-sign-ins.md)
* [Přehled rizikových událostí](concept-risk-events.md)

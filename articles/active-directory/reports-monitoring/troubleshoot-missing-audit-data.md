---
title: Řešení potíží s chybějícími daty v protokolech aktivit Azure Active Directory | Microsoft Docs
description: Nabízí řešení pro chybějící data v protokolech aktivit Azure Active Directory.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: 7cbe4337-bb77-4ee0-b254-3e368be06db7
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/15/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 1ceb903d5586dbcc824e3ad9de56e609f162e888
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55174478"
---
# <a name="troubleshoot-missing-data-in-the-azure-active-directory-activity-logs"></a>Řešení potíží: Chybějící data v protokolech aktivit Azure Active Directory 

## <a name="i-cant-find-audit-logs-for-recent-actions-in-the-azure-portal"></a>Nemůžu na webu Azure Portal najít protokoly aktivit pro nedávné akce

### <a name="symptoms"></a>Příznaky

Provedl jsem nějaké akce na webu Azure Portal a očekával jsem pro tyto akce zobrazení protokolu auditu v okně `Activity logs > Audit Logs`, ale nemůžu je najít.

 ![Vytváření sestav](./media/troubleshoot-missing-audit-data/01.png)
 
### <a name="cause"></a>Příčina

Akce se v protokolech aktivit nezobrazí okamžitě. V následující tabulce jsou uvedené naše latence u protokolů aktivit. 

| Sestava | &nbsp; | Latence (P95) | Latence (P99) |
|--------|--------|---------------|---------------|
| Audit adresáře | &nbsp; | 2 minuty | 5 minut |
| Aktivita přihlášení | &nbsp; | 2 minuty | 5 minut | 

### <a name="resolution"></a>Řešení

Počkejte 15 minut až dvě hodiny a pak se podívejte, jestli se akce v protokolu zobrazily. Pokud se protokoly ani po dvou hodinách nezobrazí, [vytvořte lístek podpory](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) a my se na to podíváme.

## <a name="i-cant-find-recent-user-sign-ins-in-the-azure-active-directory-sign-ins-activity-log"></a>V protokolu aktivit přihlášení služby Azure Active Directory nemůžu najít nedávná přihlášení uživatelů

### <a name="symptoms"></a>Příznaky

Nedávno jsem se přihlásil/a k webu Azure Portal a očekával/a jsem, že se pro tyto akce zobrazí protokoly přihlášení v okně `Activity logs > Sign-ins`, ale nemůžu je najít.

 ![Vytváření sestav](./media/troubleshoot-missing-audit-data/02.png)
 
### <a name="cause"></a>Příčina

Akce se v protokolech aktivit nezobrazí okamžitě. V následující tabulce jsou uvedené naše latence u protokolů aktivit. 

| Sestava | &nbsp; | Latence (P95) | Latence (P99) |
|--------|--------|---------------|---------------|
| Audit adresáře | &nbsp; | 2 minuty | 5 minut |
| Aktivita přihlášení | &nbsp; | 2 minuty | 5 minut | 

### <a name="resolution"></a>Řešení

Počkejte 15 minut až dvě hodiny a pak se podívejte, jestli se akce v protokolu zobrazily. Pokud se protokoly ani po dvou hodinách nezobrazí, [vytvořte lístek podpory](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) a my se na to podíváme.

## <a name="i-cant-view-more-than-30-days-of-report-data-in-the-azure-portal"></a>Na webu Azure Portal nemůžu zobrazit data sestav za více než 30 dnů.

### <a name="symptoms"></a>Příznaky

Na webu Azure Portal nemůžu zobrazit data přihlášení a auditu za více než 30 dnů. Proč? 

 ![Vytváření sestav](./media/troubleshoot-missing-audit-data/03.png)

### <a name="cause"></a>Příčina

V závislosti na vaší licenci akce služby Azure Active Directory ukládají sestavy aktivit na tyto počty dní:

| Sestava           | &nbsp; |  Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| ---              | ----   |  ---           | ---                 | ---                 |
| Audit adresáře  | &nbsp; |   7 dní     | 30 dní             | 30 dní             |
| Přihlašovací aktivita | &nbsp; | Není k dispozici. K vlastním přihlášením máte přístup po dobu 7 dnů v okně profilu uživatele. | 30 dní | 30 dní             |

Další informace najdete v tématu [Zásady uchovávání sestav Azure Active Directory](reference-reports-data-retention.md).  

### <a name="resolution"></a>Řešení

Pokud chcete data uchovávat déle než 30 dnů, máte dvě možnosti. Pomocí [rozhraní API pro generování sestav v Azure AD](concept-reporting-api.md) můžete data načíst prostřednictvím kódu programu a uložit je do databáze. Případně můžete protokoly auditu integrovat do systému SIEM třetí strany, jako je Splunk nebo Sumo Logic.

## <a name="next-steps"></a>Další postup

* [Uchovávání sestav v Azure AD](reference-reports-data-retention.md)
* [Latence generování sestav v Azure Active Directory](reference-reports-latencies.md)
* [Nejčastější dotazy ke generování sestav v Azure Active Directory](reports-faq.md)


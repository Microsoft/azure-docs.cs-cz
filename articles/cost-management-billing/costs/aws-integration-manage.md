---
title: Spravujte AWS náklady a využití v Azure Cost Management
description: Tento článek vám pomůže pochopit, jak používat analýzy a rozpočty nákladů v Cost Management ke správě nákladů a využití AWS.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: ormaoz
ms.custom: ''
ms.openlocfilehash: a7520d7b3bd46aa9151c68598574f9cbec84d6fc
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "75988614"
---
# <a name="manage-aws-costs-and-usage-in-azure"></a>Správa AWSch nákladů a využití v Azure

Jakmile nastavíte a nakonfigurujete integraci AWSch nákladů a sestav využití pro Azure Cost Management, budete připraveni začít spravovat náklady na AWS a jejich využití. Tento článek vám pomůže pochopit, jak používat analýzy a rozpočty nákladů v Cost Management ke správě nákladů a využití AWS.

Pokud jste konfiguraci integrace ještě nenakonfigurovali, přečtěte si téma [nastavení a konfigurace integrace sestavy využití AWS](aws-integration-set-up-configure.md).

_Než začnete_: Pokud nejste obeznámeni s analýzou nákladů, přečtěte si téma [prozkoumání a analýza nákladů pomocí rychlého startu analýzy nákladů](quick-acm-cost-analysis.md) . Pokud si nejste zvyklí rozpočty v Azure, přečtěte si kurz [Vytvoření a Správa rozpočtů Azure](tutorial-acm-create-budgets.md) .

## <a name="view-aws-costs-in-cost-analysis"></a>Zobrazení nákladů na AWS v analýze nákladů

Náklady na AWS jsou k dispozici při analýze nákladů v následujících oborech:

- Propojené účty AWS ve skupině pro správu
- Náklady na propojený účet AWS
- Náklady na konsolidovanou AWS účet

V dalších oddílech je popsáno, jak použít rozsahy, abyste viděli data o nákladech a využití pro každé z nich.

### <a name="view-aws-linked-accounts-under-a-management-group"></a>Zobrazení propojených účtů AWS ve skupině pro správu

Zobrazení nákladů pomocí oboru skupiny pro správu je jediným způsobem, jak zobrazit agregované náklady pocházející z různých předplatných a propojených účtů. Použití skupiny pro správu poskytuje zobrazení pro více cloudů.

V části analýza nákladů otevřete výběr oboru a vyberte skupinu pro správu, která obsahuje propojené účty AWS. Tady je příklad obrázku v Azure Portal:

![Příklad zobrazení oboru pro výběr](./media/aws-integration-manage/select-scope01.png)



Tady je příklad znázorňující náklady skupiny pro správu v tématu Analýza nákladů seskupené podle poskytovatele (Azure a AWS).

![Příklad ukazující náklady na Azure a AWS za čtvrtletí při analýze nákladů](./media/aws-integration-manage/cost-analysis-aws-azure.png)

### <a name="view-aws-linked-account-costs"></a>Zobrazení nákladů na propojený účet AWS

Pokud chcete zobrazit náklady na účet AWS Link, otevřete výběr oboru a vyberte propojený účet AWS. Všimněte si, že propojené účty jsou přidružené ke skupině pro správu, jak jsou definované v konektoru AWS.

Tady je příklad, který ukazuje výběr oboru propojeného účtu AWS.

![Příklad zobrazení oboru pro výběr](./media/aws-integration-manage/select-scope02.png)



### <a name="view-aws-consolidated-account-costs"></a>Zobrazit náklady na konsolidovanou AWS účet

Pokud chcete zobrazit AWS konsolidované náklady na účet, otevřete výběr oboru a vyberte konsolidovaný účet AWS. Tady je příklad, který ukazuje výběr oboru konsolidovaného účtu AWS.

![Příklad zobrazení oboru pro výběr](./media/aws-integration-manage/select-scope03.png)



Tento obor poskytuje agregované zobrazení všech propojených účtů AWS přidružených k konsolidovanému účtu AWS. Tady je příklad, který ukazuje náklady na konsolidovaný účet AWS seskupený podle názvu služby.

![Příklad znázorňující konsolidované náklady AWS při analýze nákladů](./media/aws-integration-manage/cost-analysis-aws-consolidated.png)

### <a name="dimensions-available-for-filtering-and-grouping"></a>Dostupné dimenze pro filtrování a seskupení

Následující tabulka popisuje dimenze, které jsou k dispozici pro seskupení a filtrování podle v části analýza nákladů.

| Dimenze | Amazon – stejné záhlaví | Obory | Komentáře |
| --- | --- | --- | --- |
| Zóna dostupnosti | lineitem/AvailabilityZone | Všechno |   |
| Umístění | produkt/oblast | Všechno |   |
| Měření |   | Všechno |   |
| Kategorie měřiče | lineItem/ProductCode | Všechno |   |
| Podkategorie měřiče | LineItem/položku UsageType | Všechno |   |
| Operace | lineItem/operace | Všechno |   |
| Prostředek | lineItem/ResourceId | Všechno |   |
| Typ prostředku | produkt/instanceType | Všechno | Pokud má produkt/instanceType hodnotu null, použije se lineItem/položku UsageType. |
| ResourceGuid | Nevztahuje se | Všechno | Identifikátor GUID měřiče Azure |
| Název služby | produkt/NázevVýrobku | Všechno | Pokud je produkt/NázevVýrobku null, použije se lineItem/ProductCode. |
| Úroveň služby |   |   |   |
| ID předplatného | lineItem/UsageAccountId | Konsolidovaný účet a skupina pro správu |   |
| Název předplatného | Nevztahuje se | Konsolidovaný účet a skupina pro správu | Názvy účtů se shromažďují pomocí rozhraní AWS organizace API. |
| Tag | resourceTags/\* | Všechno | _Uživatel:_ předpona je odebrána z uživatelsky definovaných značek, aby povolovala značky křížového cloudu. Předpona _AWS:_ je ponechána beze změny. |
| ID fakturačního účtu | bill/PayerAccountId | Skupina pro správu |   |
| Název fakturačního účtu | Nevztahuje se | Skupina pro správu | Názvy účtů se shromažďují pomocí rozhraní AWS organizace API. |
| Poskytovatel | Nevztahuje se | Skupina pro správu | Buď AWS nebo Azure. |

## <a name="set-budgets-on-aws-scopes"></a>Nastavení rozpočtů v oborech AWS

Používejte rozpočty k proaktivní správě nákladů a k zajištění zodpovědnosti ve vaší organizaci. Rozpočty se nastavují na konsolidovaném účtu AWS a v oborech propojených účtů AWS. Tady je příklad rozpočtů pro konsolidovaný účet AWS, který je zobrazený v Cost Management:

![Příklad znázorňující rozpočty pro konsolidovanou AWS účet](./media/aws-integration-manage/budgets-aws-consolidated-account01.png)

## <a name="aws-data-collection-process"></a>Proces shromažďování dat AWS

Po nastavování konektoru AWS se spustí procesy shromažďování a zjišťování dat. Shromažďování všech dat o využití může trvat několik hodin. Doba trvání závisí na:

- Čas potřebný ke zpracování souborů stejné v intervalu AWS S3.
- Čas potřebný k vytvoření konsolidovaného účtu AWS a rozsahů propojených účtů AWS.
- Čas a četnost AWS zapisují soubory sestav cost a Usage v intervalu S3.

## <a name="aws-integration-pricing"></a>Ceny AWS Integration

Každý konektor AWS získá 90 volných zkušebních dnů. Během Public Preview se neúčtují žádné poplatky.

Ceník je 1% vašich AWS měsíčních nákladů. Každý měsíc se vám bude účtovat podle fakturovaných nákladů za předchozí měsíc.

Za přístup k rozhraním API AWS se můžou účtovat další poplatky.

## <a name="aws-integration-limitations"></a>Omezení integrace AWS

- Cost Management nepodporuje sestavy nákladů, které obsahují více typů měn. Pokud vyberete obor, který má více měn, zobrazí se chybová zpráva.
- Cloudové konektory nepodporují AWS GovCloud (US), AWS gov nebo AWS Čína.
- Cost Management se zobrazují jenom _náklady na používání_ AWS. Daň, podpora, refundace, rezervované instance, kredity nebo jakékoli jiné typy poplatků se ještě nepodporují.

## <a name="troubleshooting-aws-integration"></a>Řešení potíží s integrací AWS

Při řešení běžných problémů použijte následující informace pro řešení potíží.

### <a name="no-permission-to-aws-linked-accounts"></a>Žádné oprávnění k propojeným účtům AWS

**Kód chyby:** _Neautorizováno_

Existují dva způsoby, jak získat oprávnění pro přístup k nákladům na AWS propojené účty:

- Získejte přístup ke skupině pro správu, která má propojené účty AWS.
- Dejte někomu oprávnění k propojenému účtu AWS.

Ve výchozím nastavení je autor konektoru AWS vlastníkem všech objektů, které konektor vytvořil. Včetně, konsolidovaného účtu AWS a propojeného účtu AWS.

Aby bylo možné ověřit nastavení konektoru, budete potřebovat alespoň roli přispěvatel, čtenář nemůže ověřit nastavení konektoru.

### <a name="collection-failed-with-assumerole"></a>Kolekce selhala s AssumeRole

**Kód chyby:** _FailedToAssumeRole_

Tato chyba znamená, že Cost Management nemůže volat rozhraní AssumeRole API pro AWS. K tomuto problému může dojít z důvodu problému s definicí role. Ověřte, že jsou splněné následující podmínky:

- Externí ID je stejné jako u definice role a definice konektoru.
- Typ role je nastavený na **jiný účet AWS, který patří vám nebo třetí straně.**
- Výběr možnosti **vyžadovat MFA** je zrušený.
- Účet důvěryhodného AWS v roli AWS je _432263259397_.

### <a name="collection-failed-with-access-denied---cur-report-definitions"></a>Shromažďování se nepovedlo kvůli odepření přístupu – stejné definice sestav

**Kód chyby:** _AccessDeniedReportDefinitions_

Tato chyba znamená, že Cost Management nemůže zobrazit definice sestav náklady a využití. Toto oprávnění slouží k ověření, že je měna definována podle očekávání Azure Cost Management. Přečtěte si téma [Vytvoření sestavy nákladů a využití v AWS](aws-integration-set-up-configure.md#create-a-cost-and-usage-report-in-aws).

### <a name="collection-failed-with-access-denied---list-reports"></a>Shromažďování se nepovedlo kvůli odepření přístupu – Seznam sestav

**Kód chyby:** _AccessDeniedListReports_

Tato chyba znamená, že Cost Management není schopen vypsat objekt v bloku S3, kde je umístěn stejný. Zásady AWS IAM vyžadují oprávnění k tomuto kontejneru a objektům v kontejneru. Viz [vytvoření role a zásady v AWS](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws).

### <a name="collection-failed-with-access-denied---download-report"></a>Shromažďování se nepovedlo se zakázaným přístupem – stáhnout sestavu

**Kód chyby:** _AccessDeniedDownloadReport_

Tato chyba znamená, že Cost Management nemá přístup k souborům, které jsou uložené v kontejneru Amazon S3, a stáhne je. Ujistěte se, že se zásada AWS JSON připojená k roli podobá příkladu, který je uvedený v dolní části tématu [vytvoření role a zásady v části AWS](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws) .

### <a name="collection-failed-since-we-did-not-find-the-cost-and-usage-report"></a>Shromažďování se nepovedlo, protože se nám nepovedlo najít sestavu náklady a využití.

**Kód chyby:** _FailedToFindReport_

Tato chyba znamená, že Cost Management nemůže najít sestavu nákladů a využití, která byla definována v konektoru. Ujistěte se, že se neodstraní a že se zásada AWS JSON připojená k roli podobá příkladu, který je uvedený v dolní části tématu [vytvoření role a zásady v části AWS](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws) .

### <a name="unable-to-create-or-verify-connector-due-to-cost-and-usage-report-definitions-mismatch"></a>Nelze vytvořit nebo ověřit konektor z důvodu neshody definic a nákladů na využití.

**Kód chyby:** _ReportIsNotValid_

Tato chyba se týká definice AWS nákladů a sestavy o využití. pro tuto sestavu vyžadujeme konkrétní nastavení. Přečtěte si požadavky v tématu [Vytvoření sestavy nákladů a využití v AWS](aws-integration-set-up-configure.md#create-a-cost-and-usage-report-in-aws) .

## <a name="next-steps"></a>Další kroky

- Pokud jste prostředí Azure ještě nenakonfigurovali pomocí skupin pro správu, přečtěte si téma [počáteční nastavení skupin pro správu](../../governance/management-groups/overview.md#initial-setup-of-management-groups).

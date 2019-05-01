---
title: Spravovat náklady na AWS a využití ve službě Azure Cost Management
description: Tento článek vám pomůže pochopit způsob použití analýzy nákladů a rozpočty ve službě Cost Management spravovat náklady na AWS a využití.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: cost-management
manager: ormaoz
ms.custom: ''
ms.openlocfilehash: 007b6c409dde248a4dde7a15fd16b543add234bc
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870309"
---
# <a name="manage-aws-costs-and-usage-in-azure"></a>Spravovat náklady na AWS a jeho použití v Azure

Poté, co jste nastavili a nakonfigurovali AWS nákladů a využití integrace sestavy pro službu Azure Cost Management, jste připravení začít spravovat náklady na AWS a využití. Tento článek vám pomůže pochopit způsob použití analýzy nákladů a rozpočty ve službě Cost Management spravovat náklady na AWS a využití.

Pokud jste ještě nenakonfigurovali integraci, naleznete v tématu [nastavení a konfiguraci integrace sestavy využití AWS](aws-integration-set-up-configure.md).

_Než začnete_: Pokud nejste obeznámeni s analýzy nákladů, přečtěte si článek [zkoumat a analyzovat náklady pomocí Cost analysis](quick-acm-cost-analysis.md) rychlý start. A pokud jste obeznámeni s rozpočty v Azure, najdete v článku [vytvořit a spravovat Azure rozpočty](tutorial-acm-create-budgets.md) kurzu.

## <a name="view-aws-costs-in-cost-analysis"></a>Náklady na AWS zobrazení v analýzy nákladů

Náklady na AWS jsou k dispozici v analýzu nákladů na následující obory:

- Účty AWS propojené v rámci skupiny pro správu
- Náklady na AWS propojený účet
- Náklady na účtu AWS konsolidováno

Další části popisují způsob použití rozsahů, tak, aby se zobrazí data o využití a nákladů pro každé z nich.

### <a name="view-aws-linked-accounts-under-a-management-group"></a>Zobrazit AWS, propojené účty v rámci skupiny pro správu

Zobrazení nákladů s využitím oboru skupiny pro správu je jediný způsob, jak zobrazit souhrnné náklady, které pocházejí z různých předplatných a propojené účty. Pomocí skupiny pro správu poskytuje přehled cloudu.

V analýzy nákladů otevřete výběr oboru a vyberte skupiny pro správu, který obsahuje vaše účty propojené AWS. Tady je příklad image na webu Azure Portal:

![Příklad zobrazení vyberte rozsah](./media/aws-integration-manage/select-scope01.png)



Tady je příklad zobrazující náklady na správu skupiny v analýzy nákladů, seskupené podle poskytovatele (Azure a AWS).

![Příklad zobrazující Azure a AWS náklady na analýzu nákladů na čtvrtletí](./media/aws-integration-manage/cost-analysis-aws-azure.png)

### <a name="view-aws-linked-account-costs"></a>Zobrazit AWS propojený účet náklady

Pokud chcete zobrazit náklady na AWS propojení účtu, otevřete výběr oboru a vyberte AWS propojené účtu. Všimněte si, že propojené účty přidružené ke skupině pro správu, jak jsou definovány v konektoru AWS.

Tady je příklad, že ukazuje výběr AWS propojený účet oboru.

![Příklad zobrazení vyberte rozsah](./media/aws-integration-manage/select-scope02.png)



### <a name="view-aws-consolidated-account-costs"></a>Náklady na účet konsolidované zobrazení AWS

Chcete-li zobrazit AWS konsolidovat konsolidovaný účet náklady, otevřete výběr oboru a vyberte AWS účtu. Tady je příklad, že ukazuje výběr AWS konsolidovat rozsahu účtu.

![Příklad zobrazení vyberte rozsah](./media/aws-integration-manage/select-scope03.png)



Tento obor obsahuje že souhrnný náhled na AWS všechny propojené účty přidružené k účtu AWS konsolidovat. Tady je příklad ukazující, že náklady AWS konsolidovat účtu, seskupené podle názvu služby.

![Příklad zobrazující AWS konsolidovat náklady pro analýzy nákladů](./media/aws-integration-manage/cost-analysis-aws-consolidated.png)

### <a name="dimensions-available-for-filtering-and-grouping"></a>Dimenze k dispozici pro filtrování a seskupování

Následující tabulka popisuje dimenze, které jsou k dispozici pro skupinu a filtrovat podle analýzy nákladů.

| Dimenze | MĚNA Amazon záhlaví | Obory | Komentáře |
| --- | --- | --- | --- |
| Zóna dostupnosti | lineitem/AvailabilityZone | Vše |   |
| Location | produkt/oblast | Vše |   |
| Měření |   | Vše |   |
| Kategorie měřiče | lineItem/ProductCode | Vše |   |
| Podkategorie měřiče | lineitem/UsageType | Vše |   |
| Operace | lineItem/operace | Vše |   |
| Prostředek | lineItem/ResourceId | Vše |   |
| Typ prostředku | product/instanceType | Vše | Pokud produkt/instanceType má hodnotu null, použije se lineItem/UsageType. |
| ResourceGuid | neuvedeno | Vše | Měřičů Azure identifikátor GUID. |
| Název služby | produkt/ProductName | Vše | Pokud produkt/ProductName má hodnotu null, použije se lineItem/kód ProductCode souboru. |
| Úroveň služeb |   |   |   |
| ID předplatného | lineItem/UsageAccountId | Konsolidované skupiny účet a správu |   |
| Název předplatného | neuvedeno | Konsolidované skupiny účet a správu | Názvy účtů se shromažďují pomocí rozhraní API pro organizaci AWS. |
| Značka | resourceTags/\* | Vše | _Uživatele:_ předpona je odebrán z uživatelské značky povolit značky cloudu. _Aws:_ předponu zůstává nedotčeno. |
| ID fakturačního účtu | bill/PayerAccountId | Skupina pro správu |   |
| Název fakturačního účtu | neuvedeno | Skupina pro správu | Názvy účtů se shromažďují pomocí rozhraní API pro organizaci AWS. |
| Poskytovatel | neuvedeno | Skupina pro správu | AWS a Azure. |

## <a name="set-budgets-on-aws-scopes"></a>Sada rozpočty na AWS obory

Můžete aktivně spravovat náklady a jednotky zodpovědností ve vaší organizaci rozpočty. Rozpočty jsou nastavené v účtu AWS konsolidovat a AWS obory propojený účet. Tady je příklad rozpočtů účtu AWS konsolidovat zobrazí ve službě Cost Management:

![Příklad zobrazující rozpočty pro AWS konsolidovat účtu](./media/aws-integration-manage/budgets-aws-consolidated-account01.png)

## <a name="aws-data-collection-process"></a>Proces shromažďování dat AWS

Po nastavení konektoru AWS, spusťte shromažďování dat a procesů zjišťování. Může trvat několik hodin ke shromažďování všech dat využití. Doba závisí na:

- Čas potřebný ke zpracování stejné soubory, které jsou v intervalu AWS S3.
- Čas potřebný k vytvoření účtu AWS konsolidovat a obory AWS propojený účet.
- Čas a četnost AWS psaní náklady a sestava využití souborů v intervalu S3

## <a name="aws-integration-pricing"></a>Ceny za integrace AWS

Každý konektor AWS získá 90denní bezplatná zkušební verze. Ve verzi Public Preview se neúčtuje.

Ceníková cena je 1 % měsíční náklady na AWS. Každý měsíc, bude se vám účtovat na základě vašich invoiced nákladů z předchozího měsíce.

Přístup k rozhraní API AWS může způsobit dodatečné náklady.

## <a name="aws-integration-limitations"></a>Omezení integrace AWS

- Náklady na správu nepodporuje sestavy nákladů, které obsahují více typů měny. Chybová zpráva se zobrazí, pokud vyberete obor, který má více měn.
- Konektory cloud nepodporují GovCloud AWS (USA), AWS Gov nebo AWS China.
- Služba Cost Management zobrazuje AWS _náklady na využití_ pouze. Daň, podpora, finanční prostředky, rezervované instance, kredity nebo u jiných typů poplatek se zatím nepodporují.

## <a name="troubleshooting-aws-integration"></a>Řešení potíží s integrací AWS

Použijte následující informace o odstraňování potíží vyřešit běžné problémy.

### <a name="no-permission-to-aws-linked-accounts"></a>Nemáte oprávnění ke AWS propojené účty

Existují dva způsoby, jak získat oprávnění pro přístup k náklady na AWS, propojené účty:

- Získáte přístup do skupiny pro správu, který má AWS propojené účty.
- Si nechejte udělit oprávnění k účtu AWS propojený.

Ve výchozím nastavení autora konektoru AWS je vlastníkem všechny objekty, které tento konektor vytvořen. Včetně AWS konsolidovaný účet a AWS propojený účet.

### <a name="collection-failed-with-assumerole"></a>Kolekce se nezdařilo s AssumeRole

Tato chyba znamená, že nelze volat rozhraní API AssumeRole AWS Cost Management. Tomuto problému může dojít z důvodu problému s definice role. Ověřte, že jsou splněny následující podmínky:

- Externí ID je stejný jako v definici role a definice konektoru.
- Typ role je nastaven na **AWS jiný účet patřící vás nebo 3. stran.**
- **Vyžadovat vícefaktorové ověřování** volba je zrušeno.
- Je důvěryhodného účtu AWS v roli AWS _432263259397_.

### <a name="collection-failed-with-access-denied"></a>Collection se nezdařil se přístup odepřen

Tato chybová zpráva znamená, že Cost Management nejde získat přístup ke stejné soubory uložené v intervalu Amazon S3. Ujistěte se, že AWS JSON zásady připojené k roli vypadá podobně jako v příkladu se zobrazí v dolní části [vytvoření role a zásady v AWS](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws) oddílu.

### <a name="connector-error-with-failedtofindreport"></a>Chyba konektoru s FailedToFindReport

Tato chyba znamená, že náklady na správu nelze najít sestavu nákladů a využití, která byla definována v konektoru. Ujistěte se, že se neodstraní a AWS JSON zásady připojené k roli vypadá podobně jako v příkladu se zobrazí v dolní části [vytvoření role a zásady v AWS](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws) oddílu.

## <a name="next-steps"></a>Další postup

- Pokud už jste nenakonfigurovali prostředí Azure pomocí skupin pro správu, přečtěte si téma [počáteční nastavení skupin pro správu](../governance/management-groups/index.md#initial-setup-of-management-groups).

---
title: Scénáře automatizace správy nákladů a fakturací Azure | Dokumentace Microsoftu
description: Zjistěte, jak se běžné fakturace a náklady správy, které scénáře jsou mapovány na různé rozhraní API.
services: billing
documentationcenter: ''
author: Erikre
manager: dougeby
editor: ''
tags: billing
ms.assetid: 204b15b2-6667-4b6c-8ea4-f32c06f287fd
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 6/13/2018
ms.author: erikre
ms.openlocfilehash: c5445eb5e936fe73d51a41936b052f6495b1bcc6
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39634242"
---
# <a name="billing-and-cost-management-automation-scenarios"></a>Scénáře automatizace správy fakturace a nákladů

Časté scénáře pro místo správy fakturace a náklady jsou uvedené níže a namapované na různých rozhraní API, který lze použít v těchto scénářích. Přehled rozhraní API dostupná a funkce, které nabízejí najdete pod scénář pro mapování rozhraní API. 

## <a name="common-scenarios"></a>Obvyklé scénáře

Můžete použít fakturace a Správa nákladů rozhraní API v různých scénářů odpovědí nákladů a využití související s dotazy.  Níže je uvedený přehled běžných scénářů.

- **Vyrovnání faktury** -nebyla Microsoft účtovat úroveň?  Co je vyúčtování a můžete I vypočítat sám?

- **Poplatky za různé** – teď, když mi vědět, kolik se mi účtuje, kdo v organizaci musí platit?

- **Optimalizace nákladů** -vím, kolik se mi jsme se naúčtuje, ale, jak lze získat další mimo peníze trávím na Azure?

- **Sledování nákladů** – chci zobrazit, kolik jsem výdaje a pomocí Azure v čase. Co jsou trendy? Jak můžou můžu provádět lepší?

- **Výdaje za Azure za daný měsíc** – jaká je moje aktuální měsíc je výdaje na data? Je potřeba provést nějaké úpravy v Moje výdaje a/nebo využití Azure? Pokud v měsíci teď můžu využívání Azure na maximum?

- **Nastavení výstrah** – chci nastavení spotřeby prostředků nebo peněžní na základě výstrahy.

## <a name="scenario-to-api-mappings"></a>Scénáře pro mapování rozhraní API

|         Rozhraní API nebo scénáře        | Vyrovnání faktury    | Různé poplatky za    | Optimalizace nákladů    | Sledování nákladů    | Polovině měsíce výdajů    | Výstrahy    |
|:---------------------------:|:-------------------------:|:----------------:|:--------------------:|:----------------:|:------------------:|:---------:|
| Rozpočty                     |                           |                  |           X          |                  |                    |     X     |
| Tržiště                |             X             |         X        |           X          |         X        |          X         |     X     |
| Ceník                 |             X             |         X        |           X          |         X        |          X         |           |
| Doporučení pro rezervaci |                           |                  |           X          |                  |                    |           |
| Podrobnosti rezervace         |                           |                  |           X          |         X        |                    |           |
| Souhrny rezervace       |                           |                  |           X          |         X        |                    |           |
| Podrobnosti o využití               |             X             |         X        |           X          |         X        |          X         |     X     |
| Fakturační období             |             X             |         X        |           X          |         X        |                    |           |
| Faktury                    |             X             |         X        |           X          |         X        |                    |           |
| RateCard                    |             X             |                  |           X          |         X        |          X         |           |
| Nehodnocené využití               |             X             |                  |           X          |                  |          X         |           |

> [!NOTE]
> Scénář, který má následující mapování rozhraní API nezahrnují Consumption API Enterprise. Kde je to možné, využijte prosím obecné Consumption API k vyřešení net nové vývojové scénáře v budoucnu.

## <a name="api-summaries"></a>Souhrny rozhraní API

### <a name="consumption"></a>Využití
(*Web Direct + podnikoví zákazníci pro všechna rozhraní API s výjimkou těchto názvem out níže*)

-   **Rozpočty** (*podnikoví zákazníci pouze*): použijte [rozpočty API](https://docs.microsoft.com/rest/api/consumption/budgets) vytvořit rozpočty nákladech nebo využití pro prostředky, skupiny prostředků nebo měřiče.  Po vytvoření rozpočty výstrahy lze nastavit upozornění při překročení prahové hodnoty definované uživatelem. Akce můžete nakonfigurovat také nastat, když se dosáhne rozpočet.
-   **Tržiště**: použijte [API poplatky webu Marketplace](https://docs.microsoft.com/rest/api/consumption/marketplaces) zobrazíte data o využití a poplatků pro všechny prostředky Marketplace (Azure nabídky 3. stran). Tato data je možné přidat vyšší náklady napříč všechny prostředky Marketplace nebo prozkoumat náklady nebo použití na konkrétní prostředky.
-   **Ceník** (*podnikoví zákazníci pouze*): podnikoví zákazníci mohou pomocí [cena list API](https://docs.microsoft.com/rest/api/consumption/pricesheet) načíst jejich vlastních cen pro všechny měřiče. Podniky můžou tato data použít v kombinaci s informace o využití využití. Podrobnosti a tržišť s řešeními náklady na výpočty pomocí data o využití a marketplace. 
-   **Doporučení pro rezervaci**: použití [rozhraní API pro doporučování rezervace](https://docs.microsoft.com/rest/api/consumption/reservationrecommendations) zobrazíte doporučení k nákupu rezervovaných instancí virtuálních počítačů. Doporučení jsou navrženy pro toto řešení umožňuje zákazníkům analyzovat očekávaných úspor a nákup částky.
-   **Podrobnosti o rezervaci**: použijte [API podrobnosti o rezervaci](https://docs.microsoft.com/rest/api/consumption/reservationsdetails) zobrazit informace na dříve zakoupená rezervace virtuálních počítačů, například k jak velkému využití byla rezervována a kolik se ve skutečnosti používá. Zobrazí se data na detaily na úrovni jednotlivých virtuálních počítačů.
-   **Rezervace souhrny**: použijte [rezervace souhrny API](https://docs.microsoft.com/rest/api/consumption/reservationssummaries) zobrazíte souhrnné informace na dříve zakoupená rezervace virtuálních počítačů, například k jak velkému využití byla rezervována a kolik se ve skutečnosti používá v agregační funkci. 
-   **Podrobnosti o použití**: použijte [podrobnosti o použití rozhraní API](https://docs.microsoft.com/rest/api/consumption/usagedetails) zobrazíte využití a náklady na všechny Azure 1 stran prostředky. Informace jsou ve formě záznamů podrobnosti o využití, které jsou aktuálně, protože ho jednou za měřiče za den. Informace slouží k přidání s náklady přes všechny prostředky nebo prozkoumat náklady nebo použití na konkrétní prostředky.
-   **RateCard**: Web Direct zákazníci můžou využít [RateCard API](https://msdn.microsoft.com/library/azure/mt219005.aspx) zobrazíte míry měřiče. Vrácené informace ten pak může použít k výpočtu ručně očekávané vyúčtování s informacemi o využití prostředků. 
-   **Bez použití hodnocení**: můžete použít [nehodnocené rozhraní API využití](https://msdn.microsoft.com/library/azure/mt219003.aspx) získat informace o používání nezpracovaných před všechny měření/účtování provádí Azure.

### <a name="billing"></a>Fakturace
-   **Fakturační období**: použití [rozhraní API pro fakturaci období](https://docs.microsoft.com/rest/api/billing/billingperiods) fakturačního období pro analýzu, spolu s faktury určit ID pro dané období. Faktury ID lze použít s níže uvedené rozhraní API faktury. 
-   **Faktury**: použijte [faktury API](https://docs.microsoft.com/rest/api/billing/invoices) získat adresu URL pro stažení faktury pro daného fakturačního období ve formátu PDF.

### <a name="enterprise-consumption"></a>Podnikové využití
*(Všechna rozhraní API Enterprise jenom)*

-   **Souhrn Zůstatek**: použití [souhrn rozhraní API Zůstatek](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) získat měsíční souhrnné informace o zůstatcích, nové nákupy, poplatků za služby Azure Marketplace, úpravy a poplatky za Nadlimitní využití. Můžete získat tyto informace pro aktuální fakturační období nebo období v minulosti. Podniky můžou tato data použít k porovnání s ručně počítané souhrnné náklady. Toto rozhraní API neposkytuje informace specifické pro prostředek nebo agregovaná zobrazení nákladů.
-   **Podrobnosti o použití**: použijte [podrobnosti o použití rozhraní API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail) zobrazíte stran 1. Podrobné informace využití Azure pro aktuální měsíc, konkrétní fakturačního období nebo vlastní datum období. Podniky tato data můžete použít k výpočtu ručně fakturu na základě frekvence a spotřebě a můžete také použít informace o oddělení/organizaci k dispozici pro atribut náklady napříč organizací. Data poskytuje specifické podle prostředků zobrazení využití a náklady.
-   **Poplatek za úložiště Marketplace**: použijte [API Store poplatky webu Marketplace](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) zobrazíte podrobné informace využití Azure pro aktuální měsíc, konkrétní fakturačního období nebo vlastní datum období 3. stran. Podniky tato data můžete použít k výpočtu ručně fakturu na základě frekvence a spotřebě a můžete také použít informace o oddělení/organizaci k dispozici pro atribut náklady napříč organizací. Poplatek za úložiště Marketplace API poskytuje specifické podle prostředků zobrazení využití a náklady.
-   **Ceník**: [cena list API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) poskytuje příslušný míru pro každého měřiče pro danou registrace a fakturační období. Tyto informace míry je možné v kombinaci s informace o využití využití. Podrobnosti a tržišť s řešeními pro ruční výpočet očekávané vyúčtování.
-   **Fakturační období**: použijte [rozhraní API pro fakturaci období](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) zobrazíte seznam fakturačních obdobích, která spolu s vlastností odkazující na trasy rozhraní API pro čtyři sady Enterprise API data, která se vztahují na daném fakturačním období - BalanceSummary, UsageDetails poplatků za Marketplace a ceník.
-   **Doporučení Azure rezervace**: [rezervované Instance rozhraní Recommendations API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation) vypadá na 7 dní zákazníka, 30 dnů nebo 60 dnů od používání virtuálního počítače a nabízejí doporučení, jednou a sdílené nákupu. Rezervované instance, které rozhraní API umožňuje zákazníkům, kteří k analýze očekává úspory nákladů a doporučené objemy nákupu.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="what-is-the-difference-between-the-enterprise-reporting-apis-and-the-consumption-apis-when-should-i-use-each"></a>Jaký je rozdíl mezi využití rozhraní API a rozhraní API Enterprise Reporting? Při každé mám použít?
Tato rozhraní API mají podobnou sadu funkcí a můžou odpovídat stejné široké škály dotazy v oboru správy fakturace a nákladů. Každé rozhraní API, zaměřuje na různé skupiny uživatelů: 

- **API Enterprise Reporting**: Tato rozhraní API jsou dostupná pro zákazníky, kteří si zaregistrovali smlouvu Enterprise s Microsoftem, která jim uděluje přístup k vyjednávaný peněžní závazky a získání vlastních cen. Rozhraní API vyžadují klíč použít, který můžete získat prostřednictvím [Enterprise Portal](https://ea.azure.com). Popis těchto rozhraní API, naleznete v tématu [přehled generování sestav rozhraní API pro podnikové zákazníky](billing-enterprise-api.md).

- **Využití rozhraní API**: Tato rozhraní API jsou dostupná pro všechny zákazníky s několika výjimkami. Další informace najdete v tématu [přehled rozhraní API konektoru Azure consumption](billing-consumption-api-overview.md) a [reference k rozhraní Azure Consumption API](https://docs.microsoft.com/rest/api/consumption/). Zadané rozhraní API jsou doporučené řešení pro nejnovější vývojové scénáře. 

### <a name="what-is-the-difference-between-the-usage-details-api-and-the-usage-api"></a>Jaký je rozdíl mezi rozhraním API podrobnosti o využití a využití rozhraní API?
Tato rozhraní API nabízejí fundamentálně odlišný způsob dat:

- **Podrobnosti o použití**: [podrobnosti o použití rozhraní API](https://docs.microsoft.com/rest/api/consumption/usagedetails) Azure poskytuje informace o využití a náklady na jednu instanci měřiče. Data k dispozici již předat prostřednictvím systému měření náklady na Azure a měl náklady použit společně s další možné změny:

    - Změny pro použití předplacené peněžní závazky
    - Změny, aby se zohlednily využití nesrovnalosti zjištěny nástrojem Azure

- **Využití**: [využití rozhraní API](https://msdn.microsoft.com/library/Mt219003.aspx) poskytuje informace o použití nezpracovaných Azure před odesláním prostřednictvím Azure je nákladů měřicího systému. Tato data pravděpodobně jakákoli korelace se velikostí využití a/nebo poplatek, který zobrazuje po Azure účtovat měřicího systému.

### <a name="what-is-the-difference-between-the-invoice-api-and-the-usage-details-api"></a>Jaký je rozdíl mezi faktury rozhraní API a rozhraní API podrobnosti o využití?
Tato rozhraní API poskytují různé zobrazení stejná data. [Faktury API](https://docs.microsoft.com/rest/api/billing/invoices) je Web Direct jenom pro zákazníky a poskytuje shrnutí měsíční faktuře v závislosti na agregované poplatky za každý typ měření. [Podrobnosti o použití rozhraní API](https://docs.microsoft.com/rest/api/consumption/usagedetails) poskytuje podrobné zobrazení záznamů využití a náklady za každý den a je možné zákazníkům Web Direct i Enterprise.

### <a name="what-is-the-difference-between-the-price-sheet-api-and-the-ratecard-api"></a>Jaký je rozdíl mezi rozhraní API tabulky ceny a RateCard API?
Tato rozhraní API poskytují podobné sady dat, ale mají různé skupiny uživatelů. Následující informace.

- Ceny rozhraní API tabulky: [cena list API](https://docs.microsoft.com/rest/api/consumption/pricesheet) poskytuje vlastní ceny, který má se vyjedná pro zákazníky s Enterprise.
- RateCard API: [RateCard API](https://msdn.microsoft.com/library/mt219005.aspx) poskytuje veřejné ceny, které se vztahuje na Web Direct zákazníky.

## <a name="next-steps"></a>Další kroky

- Informace o použití rozhraní API pro Azure prostřednictvím kódu programu získat přehled o využívání služeb Azure najdete v tématu [přehled rozhraní API Azure Consumption](billing-consumption-api-overview.md) a [přehled rozhraní API pro fakturaci Azure](billing-usage-rate-card-overview.md).
- Porovnat faktuře se podrobný soubor denní využití a sestav služby cost management na webu Azure Portal, najdete v článku [vysvětlení vašeho vyúčtování služeb Microsoft Azure](billing-understand-your-bill.md)
- Pokud máte další otázky, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pro rychlé vyřešení problému.

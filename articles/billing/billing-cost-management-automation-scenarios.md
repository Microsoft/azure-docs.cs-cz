---
title: Scénáře automatizace pro správu nákladů a fakturací Azure | Dokumentace Microsoftu
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
ms.openlocfilehash: a20ec22bee17bd73df24a6a0653e458241f90cfc
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55746434"
---
# <a name="automation-scenarios-for-billing-and-cost-management"></a>Scénáře automatizace pro správu nákladů a fakturace

Tento článek uvádí běžné scénáře pro správu nákladů a fakturací Azure. Mapuje se na rozhraní API, která můžete použít tyto scénáře. V rámci každé mapování scénář API najdete přehled rozhraní API a funkce, které nabízejí.

## <a name="common-scenarios"></a>Obvyklé scénáře

Můžete použít fakturace a rozhraní API pro správu v několika situacích odpovědi na otázky související náklady a využití související náklady. Tady je přehled běžných scénářů:

- **Vyrovnání faktury**: Microsoft účtovat mi úroveň?  Co je vyúčtování a můžete I vypočítat sám?

- **Mezi poplatky**: Teď, když mi vědět, kolik se mi účtuje, kdo v organizaci musí platit?

- **Optimalizace nákladů**: Vím, kolik se mi jsme se naúčtuje. Jak můžu získat mnohem víc z peníze, které trávím na Azure?

- **Sledování nákladů**: Chci zobrazit, kolik jsem výdaje a pomocí Azure v čase. Co jsou trendy? Co můžu udělat líp?

- **Azure výdaje za daný měsíc**: Kolik je moje aktuální měsíc k datu útraty? Je nutné provést změny v Moje výdaje a/nebo využití Azure? Pokud v měsíci teď můžu využívání Azure na maximum?

- **Výstrahy**: Jak nastavím založené na prostředcích spotřeby nebo peněžní výstrahy založené na?

## <a name="scenario-to-api-mapping"></a>Mapování scénář k rozhraní API

|         Rozhraní API        | Vyrovnání faktury    | Mezi náklady    | Optimalizace nákladů    | Sledování nákladů    | Midmonth útraty    | Výstrahy    |
|:---------------------------:|:-------------------------:|:----------------:|:--------------------:|:----------------:|:------------------:|:---------:|
| Rozpočty                     |                           |                  |           X          |                  |                    |     X     |
| Poplatky za marketplace                |             X             |         X        |           X          |         X        |          X         |     X     |
| Ceník                 |             X             |         X        |           X          |         X        |          X         |           |
| Doporučení pro rezervace |                           |                  |           X          |                  |                    |           |
| Podrobnosti rezervace         |                           |                  |           X          |         X        |                    |           |
| Přehledy rezervace       |                           |                  |           X          |         X        |                    |           |
| Podrobnosti využití               |             X             |         X        |           X          |         X        |          X         |     X     |
| Fakturační období             |             X             |         X        |           X          |         X        |                    |           |
| Faktury                    |             X             |         X        |           X          |         X        |                    |           |
| RateCard                    |             X             |                  |           X          |         X        |          X         |           |
| Nehodnocené využití               |             X             |                  |           X          |                  |          X         |           |

> [!NOTE]
> Mapování scénář API neobsahuje Consumption API Enterprise. Tam, kde je to možné, použijte obecné rozhraní API využití pro nové vývojové scénáře.

## <a name="api-summaries"></a>Souhrny rozhraní API

### <a name="consumption"></a>Využití
Všechny následující rozhraní API, pokud není uvedeno jinak můžete použít web Direct a podnikovými zákazníky:

-   [Rozhraní API rozpočty](https://docs.microsoft.com/rest/api/consumption/budgets) (*podnikoví zákazníci pouze*): Vytvoření rozpočty nákladech nebo využití pro prostředky, skupiny prostředků nebo měřiče. Pokud jste vytvořili rozpočty, můžete nakonfigurovat upozornění byli informováni vždy, když jste překročili definované prahové hodnoty. Můžete také nakonfigurovat akce dojít, když jste dosáhli rozpočet.

-   [Rozhraní API poplatky webu Marketplace](https://docs.microsoft.com/rest/api/consumption/marketplaces): Získáte data o využití a poplatků pro všechny prostředky Azure Marketplace (partnerských nabídek Azure). Tato data můžete použít pro přidání vyšší náklady napříč všechny prostředky Marketplace nebo prozkoumat náklady nebo použití na konkrétní prostředky.

-   [Rozhraní API tabulky Cena](https://docs.microsoft.com/rest/api/consumption/pricesheet) (*podnikoví zákazníci pouze*): Získání vlastních cen pro všechny měřiče. Podniky můžete tato data použít v kombinaci s informacemi o využití webu marketplace a podrobnosti o použití k výpočtu nákladů s využitím data o využití a marketplace. 

-   [Rozhraní API pro doporučování rezervace](https://docs.microsoft.com/rest/api/consumption/reservationrecommendations): Získejte doporučení k nákupu rezervovaných instancí virtuálních počítačů. Doporučení vám usnadní analýzu úspory očekávané náklady a částky nákupu. Další informace najdete v tématu [rozhraní API pro automatizaci Azure rezervace](billing-reservation-apis.md).

-   [Podrobnosti rezervace API](https://docs.microsoft.com/rest/api/consumption/reservationsdetails): Zobrazit informace o dříve zakoupená rezervace virtuálních počítačů, jako je například k jak velkému využití je vyhrazený a kolik se používá. Zobrazí se data na detaily na úrovni jednotlivých virtuálních počítačů. Další informace najdete v tématu [rozhraní API pro automatizaci Azure rezervace](billing-reservation-apis.md).

-   [Rezervace souhrny API](https://docs.microsoft.com/rest/api/consumption/reservationssummaries): Zobrazit souhrnné informace o rezervace virtuálních počítačů, které vaší organizaci zakoupíte jako k jak velkému využití je vyhrazený a kolik se používá v agregaci. Další informace najdete v tématu [rozhraní API pro automatizaci Azure rezervace](billing-reservation-apis.md).

-   [Rozhraní API s podrobnostmi o využití](https://docs.microsoft.com/rest/api/consumption/usagedetails): Získejte informace o využití a poplatků pro všechny prostředky Azure od Microsoftu. Informace jsou ve formě záznamů podrobnosti o využití, které jsou aktuálně, protože ho jednou za měřiče za den. Tyto informace slouží k přidání s náklady přes všechny prostředky nebo prozkoumat náklady nebo použití na konkrétní prostředky.

-   [RateCard API](https://msdn.microsoft.com/library/azure/mt219005.aspx): Získáte měřiče sazby, pokud jste zákazník Web Direct. Pak můžete vrácené informace s informacemi o využití prostředků pro ruční výpočet očekávané vyúčtování. 

-   [Využití rozhraní API bez hodnocení](https://msdn.microsoft.com/library/azure/mt219003.aspx): Získáte informace o použití nezpracovaných před všechny měření/účtování Azure.

### <a name="billing"></a>Fakturace
-   [Fakturační období API](https://docs.microsoft.com/rest/api/billing/billingperiods): Určení fakturačního období pro analýzu, spolu s identifikátory faktury za toto období. Faktury ID můžete použít s rozhraním API faktury.

-   [Zasílání faktur API](https://docs.microsoft.com/rest/api/billing/invoices): Získáte adresu URL pro stažení faktury pro fakturační období ve formátu PDF.

### <a name="enterprise-consumption"></a>Podnikové využití
Následující rozhraní API jsou k dispozici pouze pro podniky:

-   [Souhrn rozhraní API vyvážit](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-balance-summary): Získejte měsíční souhrnné informace o zůstatcích, nové nákupy, poplatků za služby Azure Marketplace, úpravy a poplatky za Nadlimitní využití. Můžete získat tyto informace pro aktuální fakturační období nebo období v minulosti. Podniky můžou tato data použít k porovnání s ručně počítané souhrnné náklady. Toto rozhraní API neposkytuje informace specifické pro prostředek nebo agregovaná zobrazení nákladů.

-   [Rozhraní API s podrobnostmi o využití](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail): Získání informací o využití Azure (Microsoft nabídek) pro aktuální měsíc, konkrétní fakturačního období nebo vlastní datum období. Podniky můžou tato data použít k ruční výpočet účtuje na základě frekvence a využití. Podniky můžete také použít informace o oddělení/organizaci atribut náklady napříč organizací. Data poskytuje specifické podle prostředků zobrazení využití a náklady.

-   [Poplatky webu Marketplace Store API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge): Získání informací o využití Azure (partnerských nabídek) pro aktuální měsíc, konkrétní fakturačního období nebo vlastní datum období. Podniky můžou tato data použít k ruční výpočet účtuje na základě frekvence a využití. Podniky můžete také použít informace o oddělení/organizaci atribut náklady napříč organizací. Toto rozhraní API poskytuje specifické podle prostředků zobrazení využití a náklady.

-   [Ceny rozhraní API tabulky](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-pricesheet): Získáte příslušné rychlost pro každého měřiče pro danou registrace a fakturačního období. Tento kurz informace v kombinaci s podrobnostmi o využití a informace o využití webu marketplace slouží k ruční výpočet očekávané vyúčtování.

-   [Fakturační období API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods): Získání seznamu fakturačních obdobích, která. Rozhraní API také poskytuje vlastnost, která odkazuje na trasu rozhraní API pro čtyři sady Enterprise API data, která se týkají fakturační období: BalanceSummary UsageDetails, poplatky webu Marketplace a ceník.

-   [Rezervované Instance rozhraní Recommendations API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation): Podívejte se na 7 dní, 30 dnů nebo 60 dní využití virtuálních počítačů a doporučení jednou a možnostech zakoupení sdílené. Toto rozhraní API můžete použít k analýze úspory očekávané náklady a doporučené objemy nákupu. Další informace najdete v tématu [rozhraní API pro automatizaci Azure rezervace](billing-reservation-apis.md).

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="whats-the-difference-between-the-enterprise-reporting-apis-and-the-consumption-apis-when-should-i-use-each"></a>Jaký je rozdíl mezi využití rozhraní API a rozhraní API Enterprise Reporting? Při každé mám použít?
Tato rozhraní API mají podobnou sadu funkcí a můžou odpovídat stejné široké škály dotazy v oboru správy fakturace a nákladů. Ale cílí na různé skupiny uživatelů: 

- Enterprise Reporting API jsou dostupná pro zákazníky, které podepsaly smlouvu Enterprise s Microsoftem, která jim uděluje přístup k vyjednávaný peněžní závazky a získání vlastních cen. Rozhraní API vyžadují klíč, který můžete získat [Enterprise Portal](https://ea.azure.com). Popis těchto rozhraní API najdete v tématu [přehled generování sestav rozhraní API pro podnikové zákazníky](billing-enterprise-api.md).

- Využití rozhraní API jsou dostupná pro všechny zákazníky s několika výjimkami. Další informace najdete v tématu [přehled rozhraní API konektoru Azure consumption](billing-consumption-api-overview.md) a [reference k rozhraní Azure Consumption API](https://docs.microsoft.com/rest/api/consumption/). Doporučujeme, abyste poskytnutých rozhraní API jako řešení pro nejnovější vývojových scénářů. 

### <a name="whats-the-difference-between-the-usage-details-api-and-the-usage-api"></a>Jaký je rozdíl mezi rozhraním API podrobnosti o využití a využití rozhraní API?
Tato rozhraní API nabízejí fundamentálně odlišný způsob dat:

- [Podrobnosti o použití rozhraní API](https://docs.microsoft.com/rest/api/consumption/usagedetails) Azure poskytuje informace o využití a náklady na jednu instanci měřiče. Poskytnutá data již předává náklady na měření systému v Azure a měl nákladů použijí, společně s další možné změny:

   - Změny pro použití předplacené peněžní závazky
   - Změny, aby se zohlednily využití nesrovnalosti zjištěny nástrojem Azure

- [Využití rozhraní API](https://msdn.microsoft.com/library/Mt219003.aspx) informacemi nezpracovaná využití Azure před odesláním náklady měření systému v Azure. Tato data možná není jakákoli korelace se využití nebo proplacení velikostí, který zobrazuje po Azure účtovat měřicího systému.

### <a name="whats-the-difference-between-the-invoice-api-and-the-usage-details-api"></a>Jaký je rozdíl mezi faktury rozhraní API a rozhraní API podrobnosti o využití?
Tato rozhraní API poskytují různé zobrazení stejná data:

- [Faktury API](https://docs.microsoft.com/rest/api/billing/invoices) je Web Direct jenom pro zákazníky. Poskytuje souhrn měsíční faktuře v závislosti na agregované poplatky za každý typ měření. 

- [Podrobnosti o použití rozhraní API](https://docs.microsoft.com/rest/api/consumption/usagedetails) poskytuje podrobné zobrazení záznamů využití a náklady za každý den. Zákazníky s Enterprise nebo Web Direct můžete ho použít.

### <a name="whats-the-difference-between-the-price-sheet-api-and-the-ratecard-api"></a>Jaký je rozdíl mezi rozhraní API tabulky ceny a RateCard API?
Tato rozhraní API nabízejí podobné sad dat ale mají různé skupiny uživatelů:

- [Cena list API](https://docs.microsoft.com/rest/api/consumption/pricesheet) poskytuje vlastní ceny, který nebyl vyjednán pro zákazníky s Enterprise.

- [RateCard API](https://msdn.microsoft.com/library/mt219005.aspx) poskytuje veřejná ceny, které se vztahuje na Web Direct zákazníky.

## <a name="next-steps"></a>Další postup

- Informace o použití rozhraní API pro Azure prostřednictvím kódu programu získat přehled o využívání služeb Azure najdete v tématu [přehled rozhraní Azure Consumption API](billing-consumption-api-overview.md) a [přehled rozhraní API Azure pro fakturaci](billing-usage-rate-card-overview.md).

- Porovnat faktuře se podrobný soubor denní využití a sestav služby cost management na webu Azure Portal, najdete v článku [vysvětlení vašeho vyúčtování služeb Microsoft Azure](billing-understand-your-bill.md).

- Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

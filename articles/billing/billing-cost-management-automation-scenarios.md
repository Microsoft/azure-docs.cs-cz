---
title: Scénáře automatizace pro Azure pro fakturaci a správu nákladů | Microsoft Docs
description: Přečtěte si, jak jsou běžné možnosti fakturace a správy nákladů namapovány na různá rozhraní API.
services: billing
documentationcenter: ''
author: bandersmsft
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
ms.author: banders
ms.openlocfilehash: 3d06df9b9a90f26b39afc17b8fcd02c85da567a1
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443257"
---
# <a name="automation-scenarios-for-billing-and-cost-management"></a>Scénáře automatizace pro fakturaci a správu nákladů

Tento článek obsahuje seznam běžných scénářů pro fakturační a nákladovou správu Azure. Tyto scénáře namapuje na rozhraní API, které můžete použít. V každém mapování scénář – rozhraní API můžete najít souhrn rozhraní API a funkce, které nabízejí.

## <a name="common-scenarios"></a>Obvyklé scénáře

Pomocí rozhraní API pro fakturaci a správu nákladů můžete v několika scénářích odpovědět na otázky související s náklady a využití. Tady je přehled běžných scénářů:

- **Odsouhlasení faktury**: Účtuje se od Microsoftu správné množství?  Co je moje faktura a můžu ji vypočítat?

- **Křížové poplatky**: Teď, když víte, kolik jsem se vám účtuje, kdo v mojí organizaci potřebuje platit?

- **Optimalizace nákladů**: Nevím, kolik se vám účtuje. Jak můžu využít peníze, které jsem stráví na Azure?

- **Sledování nákladů**: Chci zjistit, kolik tráví a co využívá Azure v čase. Co jsou trendy? Jak můžu lépe?

- **Výdaje za Azure v průběhu měsíce**: Kolik je můj aktuální měsíc útraty? Musím dělat změny ve své útratě nebo využití Azure? Když v měsíci spotřebováváte Azure nejvíc?

- **Výstrahy**: Jak můžu nastavit spotřebu na základě prostředků nebo finanční výstrahy?

## <a name="scenario-to-api-mapping"></a>Mapování scénářů na rozhraní API

|         rozhraní API        | Odsouhlasení faktury    | Křížové poplatky    | Optimalizace nákladů    | Sledování nákladů    | Midmonth útraty    | Upozornění    |
|:---------------------------:|:-------------------------:|:----------------:|:--------------------:|:----------------:|:------------------:|:---------:|
| Rozpočty                     |                           |                  |           X          |                  |                    |     X     |
| Poplatky za marketplace                |             X             |         X        |           X          |         X        |          X         |     X     |
| Ceník                 |             X             |         X        |           X          |         X        |          X         |           |
| Doporučení pro rezervace |                           |                  |           X          |                  |                    |           |
| Podrobnosti rezervace         |                           |                  |           X          |         X        |                    |           |
| Přehledy rezervace       |                           |                  |           X          |         X        |                    |           |
| Využití: podrobnosti               |             X             |         X        |           X          |         X        |          X         |     X     |
| Fakturační období             |             X             |         X        |           X          |         X        |                    |           |
| Faktury                    |             X             |         X        |           X          |         X        |                    |           |
| RateCard                    |             X             |                  |           X          |         X        |          X         |           |
| Nehodnocené využití               |             X             |                  |           X          |                  |          X         |           |

> [!NOTE]
> Mapování scénářů na rozhraní API neobsahuje rozhraní API pro podnikovou spotřebu. Pokud je to možné, použijte rozhraní API pro obecné použití pro nové vývojové scénáře.

## <a name="api-summaries"></a>Souhrny rozhraní API

### <a name="consumption"></a>Využití
Zákazníci využívající web Direct a Enterprise můžou používat všechna následující rozhraní API, s výjimkou případů, kdy je uvedeno níže:

-   [Rozhraní API](https://docs.microsoft.com/rest/api/consumption/budgets) pro rozpočty (*Jenom Podnikoví zákazníci*): Můžete vytvářet rozpočty nákladů nebo využití pro prostředky, skupiny prostředků nebo měřiče účtování. Po vytvoření rozpočtů můžete nakonfigurovat výstrahy, které vás upozorní, pokud jste překročili definované prahové hodnoty rozpočtu. Můžete také nakonfigurovat akce, které se mají provést, pokud jste dosáhli rozpočtových částek.

-   [API pro poplatky na Marketplace](https://docs.microsoft.com/rest/api/consumption/marketplaces): Získejte data o využití a data o využití všech Azure Marketplacech prostředků (nabídky partnerů Azure). Tato data můžete použít k přidání nákladů na všechny prostředky Marketplace nebo k prošetření nákladů na používání konkrétních prostředků.

-   [Rozhraní API ceníku](https://docs.microsoft.com/rest/api/consumption/pricesheet) (*Jenom Podnikoví zákazníci*): Získejte vlastní ceny pro všechny měřiče. Podniky můžou tato data využít v kombinaci s podrobnostmi o využití a informacemi o využití Marketplace k výpočtu nákladů pomocí dat využití a Marketplace. 

-   [Rozhraní API doporučení rezervací](https://docs.microsoft.com/rest/api/consumption/reservationrecommendations): Získejte doporučení k nákupu rezervovaných instancí virtuálních počítačů. Doporučení vám pomůžou analyzovat očekávané úspory nákladů a koupit částky. Další informace najdete v tématu [rozhraní API pro automatizaci rezervací Azure](billing-reservation-apis.md).

-   [Rozhraní API podrobností rezervací](https://docs.microsoft.com/rest/api/consumption/reservationsdetails): Podívejte se na informace o dříve zakoupených rezervacích virtuálních počítačů, například o tom, kolik je spotřeba rezervovaných a kolik je využito. Můžete zobrazit data podle podrobností na úrovni virtuálního počítače. Další informace najdete v tématu [rozhraní API pro automatizaci rezervací Azure](billing-reservation-apis.md).

-   [Rozhraní API pro Shrnutí rezervací](https://docs.microsoft.com/rest/api/consumption/reservationssummaries): Prohlédněte si agregované informace o rezervacích virtuálních počítačů, které vaše organizace zakoupila, například o tom, kolik je spotřeba rezervovaných a kolik se v agregaci používá. Další informace najdete v tématu [rozhraní API pro automatizaci rezervací Azure](billing-reservation-apis.md).

-   [Rozhraní API pro podrobnosti o využití](https://docs.microsoft.com/rest/api/consumption/usagedetails): Získejte informace o poplatkůch a využití všech prostředků Azure od Microsoftu. Informace jsou ve formě záznamů s podrobnostmi o využití, které jsou aktuálně vydávány jednou za měřič za den. Tyto informace můžete využít k sestavování nákladů napříč všemi prostředky nebo k prošetření nákladů na používání konkrétních prostředků.

-   [Rozhraní RateCard API](/previous-versions/azure/reference/mt219005(v=azure.100)): Získejte sazby za měřič, pokud jste zákazník web Direct. Pak můžete použít vrácené informace s informacemi o využití prostředků k ručnímu výpočtu očekávaného vyúčtování. 

-   [Rozhraní API nehodnoceného využití](/previous-versions/azure/reference/mt219003(v=azure.100)): Získejte informace o nezpracovaných použitích předtím, než Azure provede monitorování a účtování.

### <a name="billing"></a>Fakturace
-   [Rozhraní API pro fakturační období](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods): Určete fakturační období, které se má analyzovat, spolu s ID faktury pro dané období. Můžete použít ID faktury s rozhraním API pro fakturaci.

-   [API](https://docs.microsoft.com/rest/api/billing/2018-11-01-preview/invoices)pro fakturaci: Získá adresu URL pro stažení faktury pro fakturační období ve formátu PDF.

### <a name="enterprise-consumption"></a>Podniková spotřeba
Následující rozhraní API jsou jenom pro firmy:

-   [Souhrnné rozhraní API pro vyrovnávání zatížení](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-balance-summary): Získejte Měsíční souhrn informací o zůstatcích, nových nákupech, Azure Marketplace poplatky za služby, úpravy a poplatky za nadlimitní využití. Tyto informace můžete získat pro aktuální fakturační období nebo libovolné období v minulosti. Podniky můžou tato data použít k porovnání s ručně vypočítanými souhrnnými poplatky. Toto rozhraní API neposkytuje informace specifické pro prostředky ani agregované zobrazení nákladů.

-   [Rozhraní API pro podrobnosti o využití](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail): Získejte informace o využití Azure (z nabídek Microsoftu) pro aktuální měsíc, konkrétní fakturační období nebo vlastní časové období. Podniky můžou tato data použít k ručnímu výpočtu účtů na základě sazby a spotřeby. Podniky mohou také informace o oddělení a organizaci používat k atributům nákladů napříč organizacemi. Data poskytují zobrazení a náklady specifické pro prostředky.

-   [API pro poplatky za Store na webu Marketplace](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge): Získejte informace o využití Azure (nabídky partnerů) pro aktuální měsíc, konkrétní fakturační období nebo vlastní časové období. Podniky můžou tato data použít k ručnímu výpočtu účtů na základě sazby a spotřeby. Podniky mohou také informace o oddělení a organizaci používat k atributům nákladů napříč organizacemi. Toto rozhraní API poskytuje zobrazení a náklady specifické pro konkrétní prostředek.

-   [Rozhraní API ceníku](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-pricesheet): Získá platnou sazbu pro každý měřič pro danou registraci a fakturační období. Tyto informace o sazbách můžete použít v kombinaci s podrobnostmi o využití a informací o využití Marketplace k ručnímu výpočtu očekávaného vyúčtování.

-   [Rozhraní API pro fakturační období](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods): Získá seznam fakturačních období. Rozhraní API také poskytuje vlastnost, která odkazuje na trasu rozhraní API pro čtyři sady dat podnikového rozhraní API, které se týkají fakturačního období: BalanceSummary, UsageDetails, poplatky na tržišti a PriceSheet.

-   [Rozhraní API pro doporučení rezervovaných instancí](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation): Podívejte se na 7 dní, 30 dnů nebo 60 dní využití virtuálních počítačů a získejte doporučení pro jednotlivá a sdílená nákupu. Toto rozhraní API můžete použít k analýze očekávaných úspor nákladů a doporučených nákupních částek. Další informace najdete v tématu [rozhraní API pro automatizaci rezervací Azure](billing-reservation-apis.md).

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="whats-the-difference-between-the-enterprise-reporting-apis-and-the-consumption-apis-when-should-i-use-each"></a>Jaký je rozdíl mezi rozhraními API pro vytváření podnikových sestav a spotřebou rozhraní API? Kdy mám použít?
Tato rozhraní API mají podobnou sadu funkcí a mohou odpovídat stejné široké sadě otázek v prostoru fakturace a Správa nákladů. Ale cílí na různé cílové skupiny: 

- Rozhraní API pro vytváření podnikových sestav jsou dostupná zákazníkům, kteří si podepsali smlouva Enterprise Microsoftu, kteří jim udělí přístup k vyjednání s peněžními závazky a s vlastními cenami. Rozhraní API vyžadují klíč, který můžete získat z [Enterprise Portal](https://ea.azure.com). Popis těchto rozhraní API najdete v tématu [Přehled rozhraní API pro vytváření sestav pro podnikové zákazníky](billing-enterprise-api.md).

- Rozhraní API pro vydanou spotřebu jsou k dispozici všem zákazníkům s několika výjimkami. Další informace najdete v tématu [Přehled rozhraní API pro využití Azure](billing-consumption-api-overview.md) a [referenční informace k rozhraní API pro Azure spotřeber](https://docs.microsoft.com/rest/api/consumption/). Jako řešení pro nejnovější vývojové scénáře doporučujeme zadaná rozhraní API. 

### <a name="whats-the-difference-between-the-usage-details-api-and-the-usage-api"></a>Jaký je rozdíl mezi rozhraním API s podrobnostmi o využití a rozhraním API využití?
Tato rozhraní API poskytují zásadní odlišná data:

- [Rozhraní API s podrobnostmi o využití](https://docs.microsoft.com/rest/api/consumption/usagedetails) poskytuje informace o využití Azure a nákladech na instanci měřiče. Poskytnutá data už prošla prostřednictvím systému měření nákladů v Azure a měly na ni použité náklady společně s dalšími možnými změnami:

   - Změny v účtu pro použití předplaceného peněžního závazku
   - Změny účtu pro rozdíly ve využití zjištěné Azure

- [Rozhraní API pro využití](/previous-versions/azure/reference/mt219003(v=azure.100)) poskytuje hrubé informace o využití Azure ještě před tím, než se doplní systémem měření nákladů v Azure. Tato data nemusí mít žádnou korelaci s množstvím využití nebo poplatků, které se zobrazuje po systému služby měření poplatků za Azure.

### <a name="whats-the-difference-between-the-invoice-api-and-the-usage-details-api"></a>Jaký je rozdíl mezi rozhraním API pro fakturaci a rozhraním API s podrobnostmi o využití?
Tato rozhraní API poskytují různé zobrazení stejných dat:

- [Rozhraní API](https://docs.microsoft.com/rest/api/billing/2018-11-01-preview/invoices) pro fakturaci je jenom pro zákazníky používající web Direct. Poskytuje Měsíční souhrn vaší faktury na základě celkových poplatků za jednotlivé typy měřičů. 

- [Rozhraní API s podrobnostmi o využití](https://docs.microsoft.com/rest/api/consumption/usagedetails) poskytuje podrobné zobrazení záznamů o využití a nákladech za každý den. Zákazníci používající Enterprise i web Direct můžou použít.

### <a name="whats-the-difference-between-the-price-sheet-api-and-the-ratecard-api"></a>Jaký je rozdíl mezi rozhraním API ceníku a rozhraním API RateCard?
Tato rozhraní API poskytují podobné sady dat, ale mají různé cílové skupiny:

- [Rozhraní API ceníku](https://docs.microsoft.com/rest/api/consumption/pricesheet) poskytuje vlastní ceny vyjednané pro zákazníka v podniku.

- [Rozhraní RateCard API](/previous-versions/azure/reference/mt219005(v=azure.100)) poskytuje veřejné ceny, které se vztahují na zákazníky využívající web Direct.

## <a name="next-steps"></a>Další postup

- Informace o použití rozhraní API Azure k programovému získání přehledu o využití Azure najdete v tématu [Přehled rozhraní API Azure Usage API](billing-consumption-api-overview.md) a [Přehled fakturačního rozhraní Azure](billing-usage-rate-card-overview.md).

- Informace o porovnání vaší faktury s detailním souborem denního využití a sestavami cost management v Azure Portal najdete v tématu [informace o vyúčtování Microsoft Azure](billing-understand-your-bill.md).

- Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

---
title: Scénáře automatizace pro fakturaci a správu nákladů Azure | Microsoft Docs
description: Zjistěte, jak se na různá rozhraní API mapují běžné scénáře fakturace a správy nákladů.
services: billing
documentationcenter: ''
author: bandersmsft
manager: dougeby
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: c9de7d5f7661e4083d3a2f5b53368616d0e6655a
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "75992813"
---
# <a name="automation-scenarios-for-billing-and-cost-management"></a>Scénáře automatizace pro fakturaci a správu nákladů

Tento článek obsahuje běžné scénáře fakturace a správy nákladů v Azure. Tyto scénáře mapuje na rozhraní API, která můžete použít. U každého mapování scénáře na rozhraní API najdete přehled rozhraní API a funkcí, které nabízejí.

## <a name="common-scenarios"></a>Obvyklé scénáře

Pomocí rozhraní API pro fakturaci a správu nákladů si můžete v několika různých scénářích odpovědět na otázky související s náklady a využitím. Tady je přehled běžných scénářů:

- **Odsouhlasení faktury**: od Microsoftu se účtují správné množství?  Jaká je výše mé faktury a můžu ji vypočítat vlastními silami?

- **Křížové náklady**: teď jsem vědět, kolik jsem se vám účtuje, kdo v mojí organizaci potřebuje platit?

- **Optimalizace nákladů**: Nevím, kolik jsem se vám účtuje. Jak můžu peníze, které utrácím za Azure, využít efektivněji?

- **Sledování nákladů**: Chci zjistit, kolik stojí a Azure využívá v průběhu času. Jaké jsou trendy? Co můžu dělat lépe?

- Náklady na **Azure v průběhu měsíce**: kolik je můj aktuální měsíc útrata? Musím ve svých nákladech nebo využití Azure dělat nějaké změny? V kterém období měsíce využívám Azure nejvíc?

- **Výstrahy**: Jak můžu nastavit spotřebu na základě prostředků nebo finanční výstrahy?

## <a name="scenario-to-api-mapping"></a>Mapování scénářů na rozhraní API

|         API        | Sesouhlasení faktur    | Přiřazení poplatků    | Optimalizace nákladů    | Sledování nákladů    | Útrata uprostřed měsíce    | Výstrahy    |
|:---------------------------:|:-------------------------:|:----------------:|:--------------------:|:----------------:|:------------------:|:---------:|
| Rozpočty                     |                           |                  |           ×          |                  |                    |     ×     |
| Poplatky za marketplace                |             ×             |         ×        |           ×          |         ×        |          ×         |     ×     |
| Ceník                 |             ×             |         ×        |           ×          |         ×        |          ×         |           |
| Doporučení pro rezervace |                           |                  |           ×          |                  |                    |           |
| Podrobnosti rezervace         |                           |                  |           ×          |         ×        |                    |           |
| Přehledy rezervace       |                           |                  |           ×          |         ×        |                    |           |
| Podrobnosti využití               |             ×             |         ×        |           ×          |         ×        |          ×         |     ×     |
| Fakturační období             |             ×             |         ×        |           ×          |         ×        |                    |           |
| Faktury                    |             ×             |         ×        |           ×          |         ×        |                    |           |
| RateCard                    |             ×             |                  |           ×          |         ×        |          ×         |           |
| Nehodnocené využití               |             ×             |                  |           ×          |                  |          ×         |           |

> [!NOTE]
> Mapování scénářů na rozhraní API nezahrnuje rozhraní API Enterprise Consumption. Pokud je to možné, pro nové scénáře vývoje používejte všeobecná rozhraní API Consumption.

## <a name="api-summaries"></a>Shrnutí rozhraní API

### <a name="consumption"></a>Využití
Zákazníci programu Web Direct a Enterprise můžou používat všechna následující rozhraní API, u kterých není uvedeno jinak:

-   [Rozhraní API pro rozpočty](https://docs.microsoft.com/rest/api/consumption/budgets) (*pouze Podnikoví zákazníci*): Vytvořte náklady nebo rozpočty využití pro prostředky, skupiny prostředků nebo měřiče účtování. Až vytvoříte rozpočty, můžete nakonfigurovat výstrahy, které vás upozorní na překročení nastavených rozpočtových prahů. Můžete také nakonfigurovat akce, které se mají provést, když dosáhnete částek nastavených v rozpočtu.

-   [API pro poplatky na webu Marketplace](https://docs.microsoft.com/rest/api/consumption/marketplaces): Získejte data o nákladech a využití všech Azure Marketplacech prostředků (nabídky partnerů Azure). Tato data můžete využít k přiřazování nákladů napříč všemi prostředky z Marketplace nebo ke zkoumání nákladů/využití u konkrétních prostředků.

-   [API ceníku](https://docs.microsoft.com/rest/api/consumption/pricesheet) (*jenom Podnikoví zákazníci*): Získejte vlastní ceny pro všechny měřiče. Podniky můžou tato data v kombinaci s informacemi o využití a informacemi o využití Marketplace použít k výpočtu nákladů na základě dat o využití a Marketplace.

-   [Rozhraní API pro rezervaci rezervací](https://docs.microsoft.com/rest/api/consumption/reservationrecommendations): Získejte doporučení k nákupu rezervovaných instancí virtuálních počítačů. Doporučení pomáhají analyzovat očekávané úspory nákladů a kupní ceny. Další informace najdete v tématu [Rozhraní API pro automatizaci rezervací Azure](../reservations/reservation-apis.md).

-   [Rozhraní API s podrobnostmi o rezervacích](https://docs.microsoft.com/rest/api/consumption/reservationsdetails): Přečtěte si informace o dříve zakoupených rezervacích virtuálních počítačů, například o tom, kolik je spotřeba rezervovaných a kolik se používá Data můžete zobrazit na úrovni konkrétních virtuálních počítačů. Další informace najdete v tématu [Rozhraní API pro automatizaci rezervací Azure](../reservations/reservation-apis.md).

-   [Shrnutí rezervací – rozhraní API](https://docs.microsoft.com/rest/api/consumption/reservationssummaries): viz agregované informace o rezervacích virtuálních počítačů, které vaše organizace zakoupila, jako kolik je spotřeba rezervovaná a kolik se v agregaci používá. Další informace najdete v tématu [Rozhraní API pro automatizaci rezervací Azure](../reservations/reservation-apis.md).

-   [Rozhraní API s podrobnostmi o využití](https://docs.microsoft.com/rest/api/consumption/usagedetails): Získejte informace o nabití a využití všech prostředků Azure od Microsoftu. Informace mají podobu podrobných záznamů o využití, které se v současné době vystavují jednou denně pro každý měřič. Tyto informace můžete využít k přiřazování nákladů napříč všemi prostředky nebo ke zkoumání nákladů/využití u konkrétních prostředků.

-   [RATECARD API](/previous-versions/azure/reference/mt219005(v=azure.100)): Získejte sazby za měřiče, pokud jste zákazník web Direct. Vrácené informace pak můžete v kombinaci s informacemi o využití prostředků použít k ručnímu výpočtu očekávané výše faktury.

-   Neměřené [rozhraní API pro využití](/previous-versions/azure/reference/mt219003(v=azure.100)): Získejte informace o nezpracovaných použitích, než Azure provede jakékoli měření a účtování.

### <a name="billing"></a>Vyúčtování
-   [Fakturační období – rozhraní API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods): Určete fakturační období, které chcete analyzovat, spolu s ID faktury pro dané období. ID faktur můžete použít i v rozhraní API Faktury.

-   [API](/rest/api/billing/2019-10-01-preview/invoices)pro fakturaci: Získejte adresu URL pro stažení faktury pro fakturační období ve formátu PDF.

### <a name="enterprise-consumption"></a>Podniková spotřeba
Následující rozhraní API jsou určená jenom pro program Enterprise:

-   [Souhrnné rozhraní API pro vyrovnávání zatížení](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-balance-summary): Získejte Měsíční souhrn informací o zůstatcích, nových nákupech, Azure Marketplace poplatky za služby, úpravy a poplatky za nadlimitní využití. Tyto informace se můžou týkat aktuálního fakturačního období nebo libovolného minulého období. Podniky můžou tato data využít k porovnání s ručně vypočítanými souhrnnými poplatky. Toto rozhraní API neposkytuje informace o konkrétních prostředcích ani agregované zobrazení nákladů.

-   [Rozhraní API s podrobnostmi o využití](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail): Získejte informace o využití Azure (z nabídek Microsoftu) pro aktuální měsíc, konkrétní fakturační období nebo vlastní časové období. Podniky můžou tato data využít k ručnímu výpočtu výše faktury na základě sazby a spotřeby. Podniky taky můžou rozdělit náklady mezi různé organizace na základě informací o odděleních/organizacích. Tato data poskytují přehled o využití/nákladech na úrovni jednotlivých prostředků.

-   [API pro poplatky za Store na webu Marketplace](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge): Získejte informace o využití Azure (nabídky partnerů) pro aktuální měsíc, konkrétní fakturační období nebo vlastní časové období. Podniky můžou tato data využít k ručnímu výpočtu výše faktury na základě sazby a spotřeby. Podniky taky můžou rozdělit náklady mezi různé organizace na základě informací o odděleních/organizacích. Toto rozhraní API poskytuje přehled o využití/nákladech na úrovni jednotlivých prostředků.

-   [Rozhraní API ceníku](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-pricesheet): Získá platnou sazbu pro každý měřič pro danou registraci a fakturační období. Tyto informace o sazbách můžete v kombinaci s podrobnostmi o využití a informacích o využití Marketplace použít k ručnímu výpočtu očekávané výše faktury.

-   [Rozhraní API pro fakturační období](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods): Získá seznam fakturačních období. Rozhraní API také poskytuje vlastnost, která odkazuje na trasu rozhraní API pro čtyři sady dat podnikového rozhraní API, které se týkají fakturačního období: BalanceSummary, UsageDetails, poplatky za tržišti a PriceSheet.

-   [Rozhraní API pro rezervované instance](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation): Podívejte se na 7 dní, 30 dnů nebo 60 dnů využití virtuálního počítače a získejte doporučení pro jednotlivá a sdílená rozhodnutí. Pomocí tohoto rozhraní API můžete analyzovat očekávané úspory nákladů a doporučené kupní náklady. Další informace najdete v tématu [Rozhraní API pro automatizaci rezervací Azure](../reservations/reservation-apis.md).

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="whats-the-difference-between-the-enterprise-reporting-apis-and-the-consumption-apis-when-should-i-use-each"></a>Jaký je rozdíl mezi rozhraními Enterprise Reporting API a rozhraními Consumption API? Kdy mám které použít?
Tato rozhraní API mají podobnou sadu funkcí a můžou odpovědět na stejné obecné otázky týkající se fakturace a správy nákladů. Mají ale různé cílové skupiny:

- Rozhraní Enterprise Reporting API jsou dostupná zákazníkům, kteří s Microsoftem uzavřeli smlouvu Enterprise, která jim zaručuje přístup ke sjednaným peněžním závazkům a vlastním cenám. Tato rozhraní API vyžadují klíč, který můžete získat na webu [Enterprise Portal](https://ea.azure.com). Popis těchto rozhraní API najdete v tématu [Přehled rozhraní API pro vytváření sestav pro podnikové zákazníky](enterprise-api.md).

- Rozhraní Consumption API jsou s několika výjimkami dostupná všem zákazníkům. Další informace najdete v tématech [Přehled rozhraní API služby Azure Consumption](consumption-api-overview.md) a [Referenční informace o rozhraní Azure Consumption API](https://docs.microsoft.com/rest/api/consumption/). Poskytnutá API doporučujeme jako řešení pro nejnovější scénáře vývoje.

### <a name="whats-the-difference-between-the-usage-details-api-and-the-usage-api"></a>Jaký je rozdíl mezi rozhraním API Podrobnosti využití a rozhraním API Využití?
Tato rozhraní API poskytují zásadně odlišná data:

- [Rozhraní API Podrobnosti využití](https://docs.microsoft.com/rest/api/consumption/usagedetails) poskytuje informace o nákladech na Azure a využití Azure na instanci měřiče. Poskytnutá data už prošla systémem měření nákladů v Azure a jsou v nich použité náklady společně s jinými možnými změnami:

   - Změny účtu týkající se použití předplacených peněžních závazků
   - Změny účtu týkající se nesrovnalostí ve využití zjištěných Azure

- Rozhraní [API Využití](/previous-versions/azure/reference/mt219003(v=azure.100)) poskytuje nezpracované informace o využití Azure, které ještě neprošly systémem měření nákladů v Azure. Tato data nemusejí nijak souviset s využitím nebo naúčtovanou částkou, které vyjdou po průchodu systémem měření nákladů v Azure.

### <a name="whats-the-difference-between-the-invoice-api-and-the-usage-details-api"></a>Jaký je rozdíl mezi rozhraním API Faktura a rozhraním API Podrobnosti využití?
Tato rozhraní API poskytují různé zobrazení stejných dat:

- [Rozhraní API Faktura](/rest/api/billing/2019-10-01-preview/invoices) je určené jenom zákazníkům programu Web Direct. Poskytuje měsíční souhrn vaší faktury na základě souhrnných poplatků za jednotlivé typy měřičů.

- [Rozhraní API Podrobnosti využití](https://docs.microsoft.com/rest/api/consumption/usagedetails) poskytuje podrobné zobrazení záznamů o využití/nákladech za každý den. Můžou je využívat zákazníci programu Enterprise i Web Direct.

### <a name="whats-the-difference-between-the-price-sheet-api-and-the-ratecard-api"></a>Jaký je rozdíl mezi rozhraním API Ceník a rozhraním API RateCard?
Tato rozhraní API poskytují podobné sady dat, ale jsou určené jiným cílovým skupinám:

- [Rozhraní API Ceník](https://docs.microsoft.com/rest/api/consumption/pricesheet) poskytuje vlastní ceny vyjednané se zákazníkem programu Enterprise.

- [Rozhraní API RateCard](/previous-versions/azure/reference/mt219005(v=azure.100)) poskytuje veřejné ceny, které platí pro zákazníky programu Web Direct.

## <a name="next-steps"></a>Další kroky

- Informace o tom, jak prostřednictvím rozhraní API Azure získat pomocí kódu programu přehled o využití Azure, najdete v tématech [Přehled rozhraní API služby Azure Consumption](consumption-api-overview.md) a [Přehled rozhraní API služby Azure Billing](usage-rate-card-overview.md).

- Pokud chcete porovnat svou fakturu se souborem s podrobným denním využitím a sestavami správy nákladů na webu Azure Portal, projděte si článek [Vysvětlení vašeho vyúčtování služeb Microsoft Azure](../understand/review-individual-bill.md).

- Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

---
title: Scénáře automatizace pro správu nákladů a fakturaci Azure
description: Zjistěte, jak se na různá rozhraní API mapují běžné scénáře fakturace a správy nákladů.
author: bandersmsft
ms.reviewer: adwise
tags: billing
ms.service: cost-management-billing
ms.subservice: common
ms.topic: reference
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: 8cef36cf26231758eb839379d0c020484cbe09fb
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132597"
---
# <a name="automation-scenarios-for-billing-and-cost-management"></a>Scénáře automatizace pro fakturaci a správu nákladů

Tento článek obsahuje běžné scénáře fakturace a správy nákladů v Azure. Tyto scénáře mapuje na rozhraní API, která můžete použít. U každého mapování scénáře na rozhraní API najdete přehled rozhraní API a funkcí, které nabízejí.

## <a name="common-scenarios"></a>Obvyklé scénáře

Pomocí rozhraní API pro fakturaci a správu nákladů si můžete v několika různých scénářích odpovědět na otázky související s náklady a využitím. Tady je přehled běžných scénářů:

- **Sesouhlasení faktur:** Naúčtoval mi Microsoft správnou částku?  Jaká je výše mé faktury a můžu ji vypočítat vlastními silami?

- **Přiřazení poplatků:** Když teď znám výši své faktury, kdo v mé organizaci ji má zaplatit?

- **Optimalizace nákladů:** Znám výši fakturované částky. Jak můžu peníze, které utrácím za Azure, využít efektivněji?

- **Sledování nákladů:** Chci zjistit, kolik utrácím a jak moc v průběhu času využívám Azure. Jaké jsou trendy? Co můžu dělat lépe?

- **Výdaje za Azure v průběhu měsíce:** Kolik dělají mé měsíční výdaje k dnešnímu datu? Musím ve svých nákladech nebo využití Azure dělat nějaké změny? V kterém období měsíce využívám Azure nejvíc?

- **Výstrahy:** Jak nastavím spotřebu na základě prostředků nebo finanční výstrahy?

## <a name="scenario-to-api-mapping"></a>Mapování scénářů na rozhraní API

|         Rozhraní API        | Sesouhlasení faktur    | Přiřazení poplatků    | Optimalizace nákladů    | Sledování nákladů    | Útrata uprostřed měsíce    | Výstrahy    |
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

-   [Rozhraní API Rozpočty](/rest/api/consumption/budgets) (*jenom zákazníci programu Enterprise*): Můžete vytvářet rozpočty nákladů nebo využití pro prostředky, skupiny prostředků nebo měřiče účtování. Až vytvoříte rozpočty, můžete nakonfigurovat výstrahy, které vás upozorní na překročení nastavených rozpočtových prahů. Můžete také nakonfigurovat akce, které se mají provést, když dosáhnete částek nastavených v rozpočtu.

-   [Rozhraní API Poplatky za Marketplace:](/rest/api/consumption/marketplaces) Mějte přehled o poplatcích a využití pro všechny prostředky Azure Marketplace (nabídky partnerů Azure). Tato data můžete využít k přiřazování nákladů napříč všemi prostředky z Marketplace nebo ke zkoumání nákladů/využití u konkrétních prostředků.

-   [Rozhraní API Ceník](/rest/api/consumption/pricesheet) (*jenom zákazníci programu Enterprise*): Získejte vlastní ceny u všech měřičů. Podniky můžou tato data v kombinaci s informacemi o využití a informacemi o využití Marketplace použít k výpočtu nákladů na základě dat o využití a Marketplace.

-   [Rozhraní API Doporučení pro rezervace:](/rest/api/consumption/reservationrecommendations) Získejte doporučení pro nákup rezervovaných instancí virtuálních počítačů. Doporučení pomáhají analyzovat očekávané úspory nákladů a kupní ceny. Další informace najdete v tématu [Rozhraní API pro automatizaci rezervací Azure](../reservations/reservation-apis.md).

-   [Rozhraní API Podrobnosti rezervace:](/rest/api/consumption/reservationsdetails) Podívejte se na informace o dříve zakoupených rezervacích virtuálních počítačů, třeba o tom, jaká spotřeba je rezervovaná a jaká se opravdu využije. Data můžete zobrazit na úrovni konkrétních virtuálních počítačů. Další informace najdete v tématu [Rozhraní API pro automatizaci rezervací Azure](../reservations/reservation-apis.md).

-   [Rozhraní API Přehledy rezervace:](/rest/api/consumption/reservationssummaries) Prohlédněte si agregované informace o rezervacích virtuálních počítačů, které vaše organizace zakoupila, třeba o tom, jaká spotřeba je rezervovaná a jaká se opravdu souhrnně využije. Další informace najdete v tématu [Rozhraní API pro automatizaci rezervací Azure](../reservations/reservation-apis.md).

-   [Rozhraní API Podrobnosti využití:](/rest/api/consumption/usagedetails) Získejte informace o nákladech na všechny prostředky Azure od Microsoftu a o jejich využití. Informace mají podobu podrobných záznamů o využití, které se v současné době vystavují jednou denně pro každý měřič. Tyto informace můžete využít k přiřazování nákladů napříč všemi prostředky nebo ke zkoumání nákladů/využití u konkrétních prostředků.

-   [Rozhraní API RateCard:](/previous-versions/azure/reference/mt219005(v=azure.100)) Pokud jste zákazník programu Web Direct, můžete si zobrazit sazby měřičů. Vrácené informace pak můžete v kombinaci s informacemi o využití prostředků použít k ručnímu výpočtu očekávané výše faktury.

-   [Rozhraní API Nehodnocené využití:](/previous-versions/azure/reference/mt219003(v=azure.100)) Získejte nezpracované informace o využití ještě před tím, než Azure provede měření a vyúčtování.

### <a name="billing"></a>Fakturace
-   [Rozhraní API Fakturační období:](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) Určete si fakturační období, které chcete analyzovat, a ID faktur za dané období. ID faktur můžete použít i v rozhraní API Faktury.

-   [Rozhraní API Faktury:](/rest/api/billing/2019-10-01-preview/invoices) Získejte adresu URL pro stažení faktury za určité fakturační období ve formátu PDF.

### <a name="enterprise-consumption"></a>Podniková spotřeba
Následující rozhraní API jsou určená jenom pro program Enterprise:

-   [Rozhraní API Shrnutí zůstatku:](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) Získejte měsíční přehled informací o zůstatcích, nových nákupech, poplatcích za službu Azure Marketplace, úpravách a poplatcích za nadlimitní využití. Tyto informace se můžou týkat aktuálního fakturačního období nebo libovolného minulého období. Podniky můžou tato data využít k porovnání s ručně vypočítanými souhrnnými poplatky. Toto rozhraní API neposkytuje informace o konkrétních prostředcích ani agregované zobrazení nákladů.

-   [Rozhraní API Podrobnosti využití:](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail) Získejte informace o využití Azure (z nabídky Microsoftu) za aktuální měsíc, určité fakturační období nebo vlastní časové období. Podniky můžou tato data využít k ručnímu výpočtu výše faktury na základě sazby a spotřeby. Podniky taky můžou rozdělit náklady mezi různé organizace na základě informací o odděleních/organizacích. Tato data poskytují přehled o využití/nákladech na úrovni jednotlivých prostředků.

-   [Rozhraní API Poplatky za obchod Marketplace:](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) Získejte informace o využití Azure (z nabídky partnerů) za aktuální měsíc, určité fakturační období nebo vlastní časové období. Podniky můžou tato data využít k ručnímu výpočtu výše faktury na základě sazby a spotřeby. Podniky taky můžou rozdělit náklady mezi různé organizace na základě informací o odděleních/organizacích. Toto rozhraní API poskytuje přehled o využití/nákladech na úrovni jednotlivých prostředků.

-   [Rozhraní API Ceník:](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) Zjistěte platnou sazbu každého měřiče pro danou registraci a fakturační období. Tyto informace o sazbách můžete v kombinaci s podrobnostmi o využití a informacích o využití Marketplace použít k ručnímu výpočtu očekávané výše faktury.

-   [Rozhraní API Fakturační období:](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) Zobrazte si seznam fakturačních období. Toto rozhraní API taky poskytuje vlastnost, která odkazuje na trasu rozhraní API ke čtyřem sadám dat rozhraní Enterprise API, které se týkají fakturačního období: Shrnutí zůstatku, Podrobnosti využití, Poplatky za Marketplace a Ceník.

-   [Rozhraní API Doporučení rezervovaných instancí:](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation) Prohlédněte si využití virtuálních počítačů za 7, 30 nebo 60 dní a projděte si doporučení jednorázového nebo sdíleného nákupu. Pomocí tohoto rozhraní API můžete analyzovat očekávané úspory nákladů a doporučené kupní náklady. Další informace najdete v tématu [Rozhraní API pro automatizaci rezervací Azure](../reservations/reservation-apis.md).

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="whats-the-difference-between-the-enterprise-reporting-apis-and-the-consumption-apis-when-should-i-use-each"></a>Jaký je rozdíl mezi rozhraními Enterprise Reporting API a rozhraními Consumption API? Kdy mám které použít?
Tato rozhraní API mají podobnou sadu funkcí a můžou odpovědět na stejné obecné otázky týkající se fakturace a správy nákladů. Mají ale různé cílové skupiny:

- Rozhraní Enterprise Reporting API jsou dostupná zákazníkům, kteří s Microsoftem uzavřeli smlouvu Enterprise, která jim zaručuje přístup ke sjednaným peněžním závazkům a vlastním cenám. Tato rozhraní API vyžadují klíč, který můžete získat na webu [Enterprise Portal](https://ea.azure.com). Popis těchto rozhraní API najdete v tématu [Přehled rozhraní API pro vytváření sestav pro podnikové zákazníky](enterprise-api.md).

- Rozhraní Consumption API jsou s několika výjimkami dostupná všem zákazníkům. Další informace najdete v tématech [Přehled rozhraní API služby Azure Consumption](consumption-api-overview.md) a [Referenční informace o rozhraní Azure Consumption API](/rest/api/consumption/). Poskytnutá API doporučujeme jako řešení pro nejnovější scénáře vývoje.

### <a name="whats-the-difference-between-the-usage-details-api-and-the-usage-api"></a>Jaký je rozdíl mezi rozhraním API Podrobnosti využití a rozhraním API Využití?
Tato rozhraní API poskytují zásadně odlišná data:

- [Rozhraní API Podrobnosti využití](/rest/api/consumption/usagedetails) poskytuje informace o nákladech na Azure a využití Azure na instanci měřiče. Poskytnutá data už prošla systémem měření nákladů v Azure a jsou v nich použité náklady společně s jinými možnými změnami:

   - Změny účtu týkající se použití předplacených peněžních závazků
   - Změny účtu týkající se nesrovnalostí ve využití zjištěných Azure

- Rozhraní [API Využití](/previous-versions/azure/reference/mt219003(v=azure.100)) poskytuje nezpracované informace o využití Azure, které ještě neprošly systémem měření nákladů v Azure. Tato data nemusejí nijak souviset s využitím nebo naúčtovanou částkou, které vyjdou po průchodu systémem měření nákladů v Azure.

### <a name="whats-the-difference-between-the-invoice-api-and-the-usage-details-api"></a>Jaký je rozdíl mezi rozhraním API Faktura a rozhraním API Podrobnosti využití?
Tato rozhraní API poskytují různé zobrazení stejných dat:

- [Rozhraní API Faktura](/rest/api/billing/2019-10-01-preview/invoices) je určené jenom zákazníkům programu Web Direct. Poskytuje měsíční souhrn vaší faktury na základě souhrnných poplatků za jednotlivé typy měřičů.

- [Rozhraní API Podrobnosti využití](/rest/api/consumption/usagedetails) poskytuje podrobné zobrazení záznamů o využití/nákladech za každý den. Můžou je využívat zákazníci programu Enterprise i Web Direct.

### <a name="whats-the-difference-between-the-price-sheet-api-and-the-ratecard-api"></a>Jaký je rozdíl mezi rozhraním API Ceník a rozhraním API RateCard?
Tato rozhraní API poskytují podobné sady dat, ale jsou určené jiným cílovým skupinám:

- [Rozhraní API Ceník](/rest/api/consumption/pricesheet) poskytuje vlastní ceny vyjednané se zákazníkem programu Enterprise.

- [Rozhraní API RateCard](/previous-versions/azure/reference/mt219005(v=azure.100)) poskytuje veřejné ceny, které platí pro zákazníky programu Web Direct.

## <a name="next-steps"></a>Další kroky

- Informace o tom, jak prostřednictvím rozhraní API Azure získat pomocí kódu programu přehled o využití Azure, najdete v tématech [Přehled rozhraní API služby Azure Consumption](consumption-api-overview.md) a [Přehled rozhraní API služby Azure Billing](usage-rate-card-overview.md).

- Pokud chcete porovnat svou fakturu se souborem s podrobným denním využitím a sestavami správy nákladů na webu Azure Portal, projděte si článek [Vysvětlení vašeho vyúčtování služeb Microsoft Azure](../understand/review-individual-bill.md).

- Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).
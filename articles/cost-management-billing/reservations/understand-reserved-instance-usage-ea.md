---
title: Princip využití rezervací Azure u smluv Enterprise
description: Zjistěte, jak číst údaje o využití, abyste měli představu o tom, jak se na vaši prováděcí smlouvu Enterprise uplatňují rezervace Azure.
author: bandersmsft
ms.reviewer: yashar
tags: billing
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: banders
ms.openlocfilehash: 874d5cb022a38b172bb37009bd86b5e6988f3204
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96545600"
---
# <a name="get-enterprise-agreement-reservation-costs-and-usage"></a>Získání nákladů na rezervace a jejich využití u smlouvy Enterprise

Náklady na rezervace a údaje o využití jsou zákazníkům se smlouvou Enterprise dostupné na webu Azure Portal a přes rozhraní REST API. Tento článek vám pomůže:

- Získat údaje o nákupech rezervací
- Zjistit, které předplatné, skupina prostředků nebo prostředek rezervaci využil
- Udělat vratku za využití rezervace
- Vypočítat úspory vyplývající z rezervace
- Získat údaje o nedostatečném využití rezervací
- Amortizovat náklady na rezervace

Poplatky Marketplace jsou konsolidovány v údajích o využití. Poplatky za využití první stranou, využití Marketplace a nákupy můžete zobrazit z jednoho zdroje dat.

## <a name="reservation-charges-in-azure-usage-data"></a>Poplatky za rezervace v údajích o využití Azure

Data jsou rozdělena do dvou samostatných datových sad: _Skutečné náklady_ a _Amortizované náklady_. Tyto dvě datové sady se liší takto:

**Skutečné náklady** – poskytují data pro uvedení do souladu s vaším měsíčním vyúčtováním. Tato data obsahují náklady na nákup rezervací a podrobnosti k žádostem o rezervace. Z těchto dat můžete zjistit, které předplatné, skupina prostředků nebo prostředek obdržely v konkrétní den slevu za rezervaci. Platná cena (EffectivePrice) za využití, které obdrží slevu za rezervaci, je nulová.

**Amortizované náklady** – tato datová sada je podobná datové sadě Skutečné náklady až na to, že Platná cena za využití, které obdrží slevu za rezervaci, představuje poměrné náklady rezervace (místo toho, aby byla nulová). To vám umožní zjistit finanční hodnotu spotřeby rezervace podle předplatného, skupiny prostředků nebo prostředku a pomůže s interní vratkou za využití rezervace. Tato datová sada obsahuje také čas nevyužité rezervace. Tato datová sada neobsahuje záznamy o nákupu rezervací.

Porovnání těchto dvou datových sad:

| Data | Datová sada Skutečné náklady | Datová sada Amortizované náklady |
| --- | --- | --- |
| Nákupy rezervací | K dispozici v tomto zobrazení.<br><br>  Tato data získáte tak, že použijete filtr ChargeType = &quot;Purchase&quot;. <br><br> Pomocí ReservationID nebo ReservationName zjistíte, ke které rezervaci se poplatek vztahuje.  | Nelze použít v tomto zobrazení. <br><br> Náklady na nákup nejsou v amortizovaných údajích uvedeny. |
| EffectivePrice (Platná cena) | Pro využití, které obdrží slevu za rezervaci, je tato hodnota nulová. | Tato hodnota představuje poměrné hodinové náklady na rezervaci za využití, které obdrželo slevu za rezervaci. |
| Nevyužitá rezervace (udává počet hodin, po které rezervace nebyla během dne využita, a peněžní hodnotu tohoto plýtvání) | Nelze použít v tomto zobrazení. | K dispozici v tomto zobrazení.<br><br> Tato data získáte tak, že použijete filtr ChargeType = &quot;UnusedReservation&quot;.<br><br>  Pomocí ReservationID nebo ReservationName zjistíte, která rezervace byla využita nedostatečně. Tato hodnota udává, kolik rezervace bylo v daném dni vyplýtváno.  |
| UnitPrice (jednotková cena, cena za prostředek z vašeho ceníku) | K dispozici. | K dispozici. |

Jiné informace dostupné v údajích o využití Azure byly změněny:

- Informace Product a Meter – Azure nenahrazuje původně spotřebovaný měřič údaji ReservationId a ReservationName jako dříve.
- ReservationId and ReservationName – jedná se o vlastní pole v těchto údajích. Dříve byla dostupná jen v sekci AdditionalInfo.
- ProductOrderId – ID objednávky rezervace, přidáno jako vlastní pole.
- ProductOrderName – název produktu zakoupené rezervace.
- Term – 12 nebo 36 měsíců.
- RINormalizationRatio – k dispozici v sekci AdditionalInfo. Jedná se o poměr, kdy je rezervace vztažena k záznamu o využití. Pokud je u rezervace povolena flexibilní velikost instance, může platit pro jiné velikosti. Tato hodnota udává poměr, kdy byla rezervace vztažena na záznam o využití.

[Zobrazit definici polí](/rest/api/consumption/usagedetails/list#definitions)

## <a name="get-azure-consumption-and-reservation-usage-data-using-api"></a>Získání dat o spotřebě a využití rezervací Azure pomocí rozhraní API

Tato data můžete získat přes rozhraní API nebo stáhnout z webu Azure Portal.

Nová data získáte voláním [rozhraní API podrobností využití](/rest/api/consumption/usagedetails/list). Podrobnosti o terminologii najdete v článku s [pojmy souvisejícími s využitím](../understand/understand-usage.md). Volajícím by měl být podnikový správce smlouvy Enterprise, který používá [portál EA](https://ea.azure.com). Tato data mohou získat také podnikoví správci s právy jen pro čtení.

Upozorňujeme, že tato data nejsou dostupná v [rozhraních API pro vytváření sestav pro podnikové zákazníky – podrobnosti o využití](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail).

Tady je příklad volání rozhraní API Podrobnosti využití:

```
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{enrollmentId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodId}/providers/Microsoft.Consumption/usagedetails?metric={metric}&amp;api-version=2019-05-01&amp;$filter={filter}
```

Další informace o {enrollmentId} a {billingPeriodId}, najdete v článku [Podrobnosti využití – rozhraní API seznamu](/rest/api/consumption/usagedetails/list).

Informace o metrice a filtru v následující tabulce vám pomohou vyřešit běžné problémy s rezervacemi.

| **Typ dat rozhraní API** | Akce volání rozhraní API |
| --- | --- |
| **Všechny poplatky (využití a nákupy)** | Nahraďte {metric} za ActualCost |
| **Využití, které obdrželo slevu za rezervaci** | Nahraďte {metric} za ActualCost<br><br>Nahraďte {filter} za: properties/reservationId%20ne%20 |
| **Využití, které neobdrželo slevu za rezervaci** | Nahraďte {metric} za ActualCost<br><br>Nahraďte {filter} za: properties/reservationId%20eq%20 |
| **Amortizované poplatky (využití a nákupy)** | Nahraďte {metric} za AmortizedCost |
| **Sestava nevyužitých rezervací** | Nahraďte {metric} za AmortizedCost<br><br>Nahraďte {filter} za: properties/ChargeType%20eq%20'UnusedReservation' |
| **Nákupy rezervací** | Nahraďte {metric} za ActualCost<br><br>Nahraďte {filter} za: properties/ChargeType%20eq%20'Purchase'  |
| **Refundace** | Nahraďte {metric} za ActualCost<br><br>Nahraďte {filter} za: properties/ChargeType%20eq%20'Refund' |

## <a name="download-the-usage-csv-file-with-new-data"></a>Stažení souboru CSV využití s novými daty

Pokud jste správcem EA, můžete si z webu Azure Portal stáhnout soubor CSV, který obsahuje nová data o využití. Tato data nejsou k dispozici na portálu EA (ea.azure.com), pokud chcete zobrazit nová data, musíte stáhnout soubor využití z webu Azure Portal (portal.azure.com).

Na webu Azure Portal přejděte na [Správa nákladů a fakturace](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/BillingAccounts).

1. Vyberte fakturační účet.
2. Klikněte na **Využití a poplatky**.
3. Klikněte na **Stáhnout**.  
![Příklad, který ukazuje, kde na webu Azure Portal stáhnout soubor CSV s daty o využití](./media/understand-reserved-instance-usage-ea/portal-download-csv.png)
4. V okně **Stáhnout využití a poplatky** v oblasti **Podrobnosti využití verze 2** vyberte **Všechny poplatky (využití a nákupy)** a klikněte na Stáhnout. Totéž opakujte pro **Amortizované poplatky (využití a nákupy)** .

Stažené soubory CSV obsahují skutečné náklady a amortizované náklady.

## <a name="common-cost-and-usage-tasks"></a>Běžné úlohy související s náklady a využitím

V následujících částech jsou běžné úlohy, které většina lidí používá k zobrazení svých nákladů na rezervaci a údajů o využití.

### <a name="get-reservation-purchase-costs"></a>Získání nákladů na nákup rezervací

Náklady na nákup rezervací jsou dostupné v datech Skutečné náklady. Použijte filtr _ChargeType = Purchase_. Pomocí údaje ProductOrderID zjistíte, na kterou objednávku rezervace se nákup vztahuje.

### <a name="get-underutilized-reservation-quantity-and-costs"></a>Získání množství a nákladů na nevyužité rezervace

Opatřete si data Amortizované náklady a použijte filtr _ChargeType_ _= UnusedReservation_. Získáte denní množství a náklady na nevyužité rezervace. Pomocí polí _ReservationId_ a _ProductOrderId_ můžete v datech filtrovat rezervaci nebo objednávku rezervace. Pokud byla rezervace 100% využita, je v záznamu množství 0.

### <a name="amortize-reservation-costs"></a>Amortizace nákladů na rezervace

Opatřete si data Amortizované náklady a pomocí pole _ProductOrderID_ vyfiltrujte objednávku rezervace. Získáte denní amortizované náklady na rezervaci.

### <a name="chargeback-for-a-reservation"></a>Vratka za rezervaci

Za využití rezervace můžete udělat vratku jiné organizaci podle předplatného, skupin prostředků nebo značek. Data Amortizované náklady udávají peněžní hodnotu využití rezervace u následujících typů dat:

- Prostředky (například virtuální počítač)
- Skupina prostředků
- Značky
- Předplatné

### <a name="get-the-blended-rate-for-chargeback"></a>Získání smíšené sazby pro vratku

Pro určení smíšené sazby potřebujete získat amortizované náklady a agregovat celkové náklady. Pro virtuální počítače můžete použít buď údaje MeterName nebo ServiceType ze sekce AdditionalInfo dat JSON. Smíšenou sazbu získáte tak, že celkové náklady vydělíte množstvím.

### <a name="audit-optimum-reservation-use-for-instance-size-flexibility"></a>Audit optimálního využití rezervace pro flexibilní velikost instance

Vynásobte množství údajem _RINormalizationRatio_ ze sekce AdditionalInfo. Výsledky udávají, kolik hodin využití rezervace bylo vztaženo na záznam o využití.

### <a name="determine-reservation-savings"></a>Určení úspor vyplývajících z rezervací

Opatřete si data Amortizované náklady a vyfiltrujte v datech rezervovanou instanci. Potom:

1. Získejte odhadované náklady na průběžné platby. Vynásobením hodnot _UnitPrice_ a _Quantity_ získáte odhadované náklady na průběžné platby, kdyby na využití nebyla uplatněna sleva za rezervaci.
2. Získejte náklady na rezervaci. Sečtením hodnot _Cost_ získáte peněžní hodnotu, kterou jste zaplatili za rezervovanou instanci. Zahrnuje využité a nevyužité náklady na rezervaci.
3. Odečtením nákladů na rezervaci od odhadovaných nákladů na průběžné platby získáte odhadované úspory.

Nezapomínejte, že v případě nedostatečně využité rezervace je potřeba vzít v úvahu také položku _UnusedReservation_ pro _ChargeType_. Pokud máte plně využitou rezervaci, získáváte maximální možnou úsporu. Libovolná hodnota _UnusedReservation_ vaše úspory snižuje.

## <a name="reservation-purchases-and-amortization-in-cost-analysis"></a>Nákupy a amortizace rezervací v analýze nákladů

Náklady na rezervaci jsou dostupné v [analýze nákladů](https://aka.ms/costanalysis). V analýze nákladů se standardně zobrazují **Skutečné náklady**, což jsou náklady, které budou uvedeny ve vašem vyúčtování. Pokud chcete zobrazit nákupy rezervací rozepsané a přiřazené k prostředkům, které tuto výhodu využily, přepněte na **Amortizované náklady**:

![Příklad, který ukazuje, kde se v analýze nákladů vybírají amortizované náklady](./media/understand-reserved-instance-usage-ea/portal-cost-analysis-amortized-view.png)

Při seskupení podle typu poplatku zobrazíte rozpis využití, nákupů a refundací; při seskupení podle rezervace zobrazíte rozpis rezervací a nákladů na vyžádání. Mějte na paměti, že jedinými náklady na rezervaci, které uvidíte při pohledu na skutečné náklady, jsou nákupy; při pohledu na amortizované náklady budou ale náklady přiděleny k jednotlivým prostředkům, které tuto výhodu využily. Při pohledu na amortizované náklady uvidíte také nový poplatek typu **UnusedReservation**.

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky

Další informace o rezervacích Azure najdete v následujících článcích:

- [Co jsou rezervace Azure?](save-compute-costs-reservations.md)
- [Předplacení virtuálních počítačů se službou Azure Reserved VM Instances](../../virtual-machines/prepay-reserved-vm-instances.md)
- [Předplacení výpočetních prostředků SQL Database se záložní kapacitou služby Azure SQL Database](../../azure-sql/database/reserved-capacity-overview.md)
- [Správa rezervací Azure](manage-reserved-vm-instance.md)
- [Vysvětlení způsobu uplatnění slevy za rezervaci](../manage/understand-vm-reservation-charges.md)
- [Vysvětlení využití rezervací u předplatného s průběžnými platbami](understand-reserved-instance-usage.md)
- [Náklady na software pro Windows nezahrnuté v rezervacích](reserved-instance-windows-software-costs.md)
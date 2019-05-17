---
title: Vysvětlení využití Azure rezervace pro smlouvy Enterprise
description: Zjistěte, jak číst využití, abyste pochopili, jak se má rezervace Azure u prováděcí smlouvy Enterprise použít.
author: bandersmsft
manager: yashar
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/07/2019
ms.author: banders
ms.openlocfilehash: 09242eaa6058229226062801f5f71f2bf4c7a9e8
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2019
ms.locfileid: "65789393"
---
# <a name="get-enterprise-agreement-reservation-costs-and-usage"></a>Získat smlouvu Enterprise rezervace nákladů a využití

Data o využití a náklady na rezervaci jsou dostupné pro zákazníky se smlouvou Enterprise webu Azure portal a rozhraní REST API. Tento článek vám pomůže:

- Získání dat nákup rezervace
- Získání rezervace snížení využití dat
- Umořit náklady na rezervaci
- Vrácení peněz pro rezervaci využití
- Vypočítat úspory rezervace

Poplatky za Marketplace konsolidované v datech využití. Poplatky za využití první strany, využití webu marketplace a nákupy z jednoho zdroje dat zobrazení.

## <a name="reservation-charges-in-azure-usage-data"></a>Poplatky za rezervaci v datech využití Azure

Data je rozdělena na dvě samostatné datové sady: _Skutečné náklady_ a _Amortizovaných nákladů_. Jak se liší tyto dvě datové sady:

**Skutečné náklady** – poskytuje data sjednotit se svoje měsíční náklady. Tato data obsahují náklady na nákup rezervace. Nemá žádný EffectivePrice za využití, která se zobrazila sleva za rezervaci.

**Amortizovaných nákladů** -prostředků EffectiveCost, který získá sleva za rezervaci se poměrná část náklady na rezervovanou instanci. Datová sada obsahuje také náklady na nevyužité rezervace. Součet rezervace nákladů a nevyužité rezervace poskytuje denní amortizované náklady na rezervaci.

Porovnání dvou datových sad:

| Data | Skutečné náklady na datovou sadu | Amortizované náklady na datová sada |
| --- | --- | --- |
| Nákup rezervace | K dispozici v tomto zobrazení.<br>  Chcete-li získat tento filtr dat na ChargeType = &quot;nákupní&quot;. <br> Odkazovat ReservationID nebo ReservationName vědět, jaké rezervace platí se za.  | Neplatí pro toto zobrazení. <br> Náklady na nákup nejsou k dispozici v amortizovaném data. |
| effectivePrice | Hodnota je nula využití, získá sleva za rezervaci. | Hodnota se účtuje pomocí poměrného přepočítání na hodiny náklady na rezervaci pro využití, které má sleva za rezervaci. |
| Nevyužité rezervace (poskytuje počet hodin, po které rezervace nebyl použit za den a peněžní hodnoty odpadu) | Není k dispozici v tomto zobrazení. | K dispozici v tomto zobrazení.<br> K získání těchto dat, filtrování ChargeType = &quot;UnusedReservation&quot;.<br>  Odkazovat ReservationID nebo ReservationName vědět, které rezervace se historického. To je, jak velká část rezervace byla zbytečná v den.  |
| UnitPrice (cena prostředku z nového ceníku) | K dispozici | K dispozici |

Další informace, které jsou k dispozici v datech využití Azure se změnil:

- Produktu a informace o měření - Azure nenahrazuje původně spotřebované měřiče ReservationId a ReservationName, stejně jako dříve.
- ReservationId a ReservationName – jsou vlastní pole v datech. Dříve použít k dispozici pouze v části AdditionalInfo.
- ProductOrderId - ID objednávky rezervace přidán jako vlastní pole.
- ProductOrderName - název produktu je zakoupená rezervace.
- Termín - 12 měsíců nebo 36 měsíců.
- RINormalizationRatio – k dispozici v části AdditionalInfo. Toto je poměr, ve kterém se má rezervace použít na záznam využití. Pokud velikost flexibilita instancí je zapnutá pro vaší rezervace, můžete použít jiné formáty. Hodnota zobrazuje poměr, rezervace byla použita k záznamu využití.

## <a name="get-azure-consumption-and-reservation-usage-data-using-api"></a>Získat využití a rezervace využití dat Azure pomocí rozhraní API

Můžete načíst data pomocí rozhraní API nebo si ho stáhnout z webu Azure portal.

Volání [podrobnosti o použití rozhraní API](/rest/api/consumption/usagedetails/list) s verzí API &quot;2019-04-01-preview&quot; získat nová data. Podrobnosti o terminologii najdete v tématu [podmínkami použití](billing-understand-your-usage.md). Volající by měl být správce rozlehlé sítě s využitím smlouvy enterprise [portál EA](https://ea.azure.com). Enterprise Administrators jen pro čtení můžete také získat data.

Data nejsou k dispozici v [Reporting rozhraní API pro podnikové zákazníky – podrobnosti o použití](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail).

Tady je příklad volání rozhraní API:

```
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{enrollmentId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodId}/providers/Microsoft.Consumption/usagedetails?metric={metric}&amp;api-version=2019-04-01-preview&amp;$filter={filter}
```

Další informace o {enrollmentId} a {billingPeriodId}, najdete v článku [podrobnosti o použití – seznam](https://docs.microsoft.com/rest/api/consumption/usagedetails/list) článku rozhraní API.

Informace v následující tabulce o metriky a filtr můžou pomoct vyřešit běžné problémy rezervace.

| **Typ dat rozhraní API** | Akce volání rozhraní API |
| --- | --- |
| **Všechny poplatky (využití a nákupů)** | Nahraďte ActualCost {metrika} |
| **Využití, které je teď sleva za rezervaci** | Nahraďte ActualCost {metrika}<br>Nahraďte {filter}: properties/reservationId%20ne%20 |
| **Využití, které nezískaly sleva za rezervaci** | Nahraďte ActualCost {metrika}<br>Nahraďte {filter}: properties/reservationId%20eq%20 |
| **Amortizované náklady (využití a nákupů)** | Nahraďte AmortizedCost {metrika} |
| **Nevyužité rezervace sestavy** | Nahraďte AmortizedCost {metrika}<br>Nahraďte {filter}: properties/ChargeType%20eq%20'UnusedReservation. |
| **Nákup rezervace** | Nahraďte ActualCostReplace {filter} {metrika} s: properties/ChargeType%20eq%20'Purchase.  |
| **Vrácení peněz** | Nahraďte ActualCost {metrika}<br>Nahraďte {filter}: properties/ChargeType%20eq%20'Refund. |

## <a name="download-the-usage-csv-file-with-new-data"></a>Stáhněte si soubor CSV využití s novými daty

Pokud si nejste správce EA, můžete stáhnout soubor CSV, který obsahuje nová data o využití z webu Azure portal. Tato data není k dispozici [portál EA](https://ea.azure.com).

Na webu Azure Portal, přejděte na [Správa nákladů a fakturace](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/BillingAccounts).

1. Vyberte fakturační účet.
2. Klikněte na tlačítko **a poplatky za využití**.
3. Klikněte na tlačítko **Stáhnout**.  
![Příklad znázorňující, kde chcete stáhnout soubor dat využití sdíleného svazku clusteru na portálu Azure portal](./media/billing-understand-reserved-instance-usage-ea/portal-download-csv.png)
4. V **stáhnout využití a poplatky za** v části **využití podrobnosti verze 2** vyberte **všechny poplatky (využití a nákupy)** a potom klikněte na stáhnout. Opakujte pro **Amortizovaných nákladů (využití a nákupy)**.

Soubory CSV, kterou stáhnete obsahovat skutečné náklady a amortizované náklady.

## <a name="common-cost-and-usage-tasks"></a>Běžné úlohy nákladů a využití

V následujících částech jsou běžné úlohy, které většinu lidí slouží k zobrazení jejich data o využití a náklady na rezervaci.

### <a name="get-reservation-purchase-costs"></a>Získat náklady na nákup rezervace

Náklady na nákup rezervace jsou dostupné ve skutečných nákladech. Filtrovat _ChargeType = nákupní_. Odkazovat na ProductOrderID k určení pořadí rezervace nákup.

### <a name="get-underutilized-reservation-quantity-and-costs"></a>Získat množství rezervací nedostatečně využité a nákladů

Získání dat Amortizovaných nákladů a filtrovat _ChargeType_ _= UnusedReservation_. Získáte denní množství nevyužité rezervace a náklady. Můžete filtrovat data pro rezervaci nebo pomocí objednávky rezervace _ReservationId_ a _ProductOrderId_ pole, v uvedeném pořadí. Pokud rezervaci se využívá 100 %, záznam obsahuje množství 0.

### <a name="amortize-reservation-costs"></a>Umořit náklady na rezervaci

Získání dat Amortizovaných nákladů a filtrování pro používání objednávky rezervace _ProductOrderID_ získat denní amortizované náklady pro rezervaci.

### <a name="chargeback-for-a-reservation"></a>Vrácení peněz pro rezervaci.

Můžete použít vrácení peněz rezervace pro jiné organizace podle předplatného, skupiny prostředků nebo značky. Amortizované náklady dat poskytuje peněžní hodnoty rezervaci využití na následující typy dat:

- Prostředky (například virtuální počítač)
- Skupina prostředků
- Tags
- Předplatné

### <a name="get-the-blended-rate-for-chargeback"></a>Získání kombinaci rychlost pro vrácení peněz

K určení kombinaci rychlost, získat amortizované náklady na data a agregovat celkové náklady. Pro virtuální počítače můžete použít MeterName nebo ServiceType informace z dat AdditionalInfo JSON. Rozdělte náklady podle množství použít k získání kombinaci rychlost.

### <a name="audit-optimum-reservation-use-for-instance-size-flexibility"></a>Audit optimální rezervace použijte například velikost flexibilitu

Více množství se _RINormalizationRatio_, z AdditionalInfo. Výsledky ukazují, kolik hodin užívání rezervace byla použita k záznamu využití.

### <a name="determine-reservation-savings"></a>Určení úspory rezervace

Získání dat Amortized náklady a filtrovat data pro rezervované instance. Potom:

1. Získejte odhadované náklady s průběžnými platbami. Vynásobit _UnitPrice_ hodnotu _množství_ hodnoty získat odhadovaný platili s průběžnými platbami, pokud se sleva za rezervaci se nepoužil, přes použití.
2. Získejte náklady na rezervaci. Součet _náklady_ hodnoty k získání peněžní hodnoty za rezervovanou instanci. Zahrnuje náklady na použité a nevyužité rezervace.
3. Odečte náklady na rezervaci z odhadované platili s průběžnými platbami, chcete-li získat Odhadované úspory.

## <a name="reservation-purchases-and-amortization-in-azure-cost-analysis"></a>Nákup rezervace a amortizace analýzy nákladů na Azure

Náklady na rezervovanou instanci je k dispozici v [režim náhledu analýzy nákladů na Azure](https://preview.portal.azure.com/?feature.canmodifystamps=true&amp;microsoft_azure_costmanagement=stage2&amp;Microsoft_Azure_CostManagement_arm_canary=true&amp;Microsoft_Azure_CostManagement_apiversion=2019-04-01-preview&amp;Microsoft_Azure_CostManagement_amortizedCost=true#blade/Microsoft_Azure_CostManagement/Menu/costanalysis). Ve výchozím nastavení zobrazení dat nákladů je pro skutečné náklady. Můžete přepnout na amortizované náklady. Tady je příklad.

![Příklad znázorňující, kde můžete vybrat amortizované náklady v analýzy nákladů](./media/billing-understand-reserved-instance-usage-ea/portal-cost-analysis-amortized-view.png)

Použijte filtry zobrazíte poplatky podle typu rezervace nebo poplatku. Seskupit podle název rezervace zobrazíte náklady porušena rezervace.

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další postup

Další informace o rezervacích Azure, najdete v následujících článcích:

- [Co jsou Azure rezervace?](billing-save-compute-costs-reservations.md)
- [Předplacení virtuálních počítačů se službou Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Předplacení výpočetních prostředků SQL Database se záložní kapacitou služby Azure SQL Database](../sql-database/sql-database-reserved-capacity.md)
- [Správa rezervací Azure](billing-manage-reserved-vm-instance.md)
- [Vysvětlení, jak se sleva za rezervaci použije](billing-understand-vm-reservation-charges.md)
- [Vysvětlení využití rezervace pro vaše předplatné s průběžnými platbami](billing-understand-reserved-instance-usage.md)
- [Náklady na software Windows, které nejsou součástí rezervace](billing-reserved-instance-windows-software-costs.md)

---
title: Zpětné vyúčtování na rezervace Azure
description: Přečtěte si, jak zobrazovat náklady na rezervace Azure pro zpětné vyúčtování.
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 03/10/2021
ms.author: banders
ms.openlocfilehash: 4059318e6b8052f3b0221c87e8a357cfc8679e44
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532754"
---
# <a name="charge-back-azure-reservation-costs"></a>Zpětné vyúčtování na rezervace Azure

Čtenáři fakturace pro smlouvu Enterprise a Smlouvu se zákazníkem Microsoftu si mohou projít údaje o amortizovaných nákladech na rezervace. Tyto údaje o nákladech mohou využít ke zpětnému vyúčtování finanční hodnoty předplatného, skupiny prostředků, prostředku nebo značky pro svoje partnery. V rámci údajů s využitím amortizace jsou platnou cenou náklady na rezervace s poměrným přepočítáním po hodinách. Náklady jsou celkové náklady na využití rezervací prostředku v daném dni.

Uživatelé s individuálním předplatným mohou údaje o amortizovaných nákladech zjistit ze souboru využití. Když prostředek získá slevu za rezervaci, sekce *AdditionalInfo* souboru využití obsahuje podrobnosti o příslušné rezervaci. Další informace najdete v tématu [Stažení informací o využití z webu Azure Portal](../understand/download-azure-daily-usage.md#download-usage-from-the-azure-portal-csv).

## <a name="see-reservation-usage-data-for-show-back-and-charge-back"></a>Viz data o využití rezervace pro zobrazení zpět a vrácení zpět.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
2. Přejděte do části **Cost Management a fakturace**. 
3. Vybrat **analýzu nákladů** z levého navigačního panelu 
4. V části **Skutečné náklady** vyberte metriku **Amortizované náklady**.
5. Pokud chcete zjistit, které prostředky využily konkrétní rezervace, použijte filtr pro **rezervace** a potom vyberte příslušné rezervace.
6. **Úroveň podrobností** nastavte na **Měsíční** nebo **Denní**.
7. Nastavte typ grafu na **Tabulkový**.
8. Možnost **Seskupit podle** nastavte na **Prostředek**.

[![Příklad ukazující náklady na prostředky, které se dají použít pro zpětné vyúčtování](./media/charge-back-usage/amortized-reservation-costs.png)](./media/charge-back-usage/amortized-reservation-costs.png#lightbox)

Tady je video, které ukazuje, jak zobrazit náklady na používání rezervací na úrovni předplatného, skupiny prostředků a prostředků v Azure Portal.

 > [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4sQOw] 

## <a name="get-the-data-for-show-back-and-charge-back"></a>Získat data pro vrácení zpět a vrácení zpět
1. Přihlaste se na [Azure Portal](https://portal.azure.com).
2. Přejděte do části **Cost Management a fakturace**. 
3. Vybrat **Export** z levé navigační části 
4. Klikněte na tlačítko **Přidat** .
5. Vyberte náklady na amortizaci jako tlačítko metriky a nastavte svůj export.

EffectivePrice pro použití, který vrací zlevněnou slevu, je poměrná cena rezervace (místo hodnoty nula). To vám umožní zjistit finanční hodnotu spotřeby rezervace podle předplatného, skupiny prostředků nebo prostředku a pomůže s interní vratkou za využití rezervace. Tato datová sada obsahuje také čas nevyužité rezervace. 

## <a name="get-azure-consumption-and-reservation-usage-data-using-api"></a>Získání dat o spotřebě a využití rezervací Azure pomocí rozhraní API

Tato data můžete získat přes rozhraní API nebo stáhnout z webu Azure Portal.

Nová data získáte voláním [rozhraní API podrobností využití](/rest/api/consumption/usagedetails/list). Podrobnosti o terminologii najdete v článku s [pojmy souvisejícími s využitím](../understand/understand-usage.md).

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

Pokud jste správce služby EA, můžete si stáhnout soubor CSV, který obsahuje nová data o využití z Azure Portal. Tato data nejsou k dispozici na portálu EA (ea.azure.com), pokud chcete zobrazit nová data, musíte stáhnout soubor využití z webu Azure Portal (portal.azure.com).

Na webu Azure Portal přejděte na [Správa nákladů a fakturace](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/BillingAccounts).

1. Vyberte fakturační účet.
2. Klikněte na **Využití a poplatky**.
3. Klikněte na **Stáhnout**.  
![Příklad, který ukazuje, kde na webu Azure Portal stáhnout soubor CSV s daty o využití](./media/understand-reserved-instance-usage-ea/portal-download-csv.png)
4. V **podrobnostech o využití** vyberte **data s využitím amortizace**.

Stažené soubory CSV obsahují skutečné náklady a amortizované náklady.

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Další kroky
- Další informace o Azure Reservations dat o využití najdete v následujících článcích:
  - [smlouva Enterprise a ceny a využití rezervace zákaznických smluv Microsoftu](understand-reserved-instance-usage-ea.md)
 

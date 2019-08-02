---
title: Vysvětlení použití rezervací Azure pro smlouvy Enterprise
description: Naučte se, jak si přečíst své využití a pochopit, jak se používá rezervace Azure pro vaši podnikovou registraci.
author: bandersmsft
manager: yashar
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 507ad62a917120689bee3f1e293e23c9ab8b0f66
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68598092"
---
# <a name="get-enterprise-agreement-reservation-costs-and-usage"></a>Získat smlouva Enterprise náklady a využití rezervace

Náklady na rezervaci a data o využití jsou k dispozici pro smlouva Enterprise zákazníky v rozhraních API Azure Portal a REST. Tento článek vám pomůže:

- Získat data nákupu rezervací
- Zjistěte, které předplatné, skupina prostředků nebo prostředek použila rezervaci.
- Vrácení peněz pro využití rezervace
- Vypočítat úspory rezervací
- Získat údaje o rezervacích v rámci využití
- Náklady na amortizaci na amortizaci

Poplatky za Marketplace jsou konsolidovány v datech o využití. Můžete si zobrazit poplatky za využití první strany, využití Marketplace a nákupy z jednoho zdroje dat.

## <a name="reservation-charges-in-azure-usage-data"></a>Poplatky za rezervace v datech využití Azure

Data jsou rozdělena do dvou samostatných datových sad: _Skutečné náklady_ a _náklady_na amortizaci. Jak se tyto dvě datové sady liší:

**Skutečné náklady** – poskytuje data, která se mají sjednotit s vaším měsíčním vyúčtováním. Tato data obsahují podrobnosti o nákupu rezervací a o rezervacích aplikace. Tato data vám pomohou zjistit, které předplatné nebo skupinu prostředků nebo prostředek přijal slevu za konkrétní den. EffectivePrice pro použití, který přijímá slevu rezervace, je nula.

**Náklady** na amortizaci – tato datová sada je podobná skutečné datové sadě nákladů, s výjimkou toho, že EffectivePrice pro použití, který se slevou rezervace, je poměrná cena rezervace (místo toho, aby byla nulová). To vám pomůže zjistit finanční hodnotu rezervované spotřeby pomocí předplatného, skupiny prostředků nebo prostředku a vám může pomoci vám vrátit se k využití rezervace interně. Datová sada má také nepoužité hodiny rezervace. Datová sada neobsahuje záznamy o nákupu rezervací. 

Porovnání dvou datových sad:

| Data | Sada skutečných dat nákladů | Sada dat pro amortizaci nákladů |
| --- | --- | --- |
| Nákupy rezervací | K dispozici v tomto zobrazení.<br><br>  Pro získání tohoto filtru dat v ChargeType = &quot;purchase&quot;. <br><br> Chcete-li zjistit, na které rezervaci se účtuje poplatek, použijte ReservationID nebo reservation.  | Nelze použít pro toto zobrazení. <br><br> Náklady na nákup nejsou k dispozici v datech v amortizaci. |
| EffectivePrice | Hodnota je nula pro použití, která vrací slevu za rezervaci. | Hodnota je sazba za hodinu průběžných nákladů na rezervaci pro použití, která má slevu za rezervaci. |
| Nevyužitá rezervace (poskytuje počet hodin, po které se rezervace nepoužila za den a peněžní hodnota odpadu) | V tomto zobrazení nelze použít. | K dispozici v tomto zobrazení.<br><br> Chcete-li získat tato data, vyfiltrujte&quot;ChargeType = &quot;UnusedReservation.<br><br>  Pokud chcete zjistit, která rezervace byla nevyužita, přečtěte si ReservationID nebo reservation. To je počet nevyužitých rezervací za den.  |
| JednotkováCena (cena za prostředek ze ceníku) | K dispozici | K dispozici |

Další informace, které jsou dostupné v datech o využití Azure, se změnily:

- Informace o produktu a měřiči – Azure nenahrazuje původně spotřebovaný měřič s ReservationId a rezervací, jako by byl dřív.
- ReservationId a reservation – jedná se o vlastní pole v datech. Dřív se použila k dispozici pouze v rámci AdditionalInfo.
- ProductOrderId – ID objednávky rezervace, přidané jako své vlastní pole.
- ProductOrderName – název produktu zakoupené rezervace.
- Období – 12 měsíců nebo 36 měsíců.
- RINormalizationRatio – k dispozici v rámci AdditionalInfo. Toto je poměr, ve kterém se rezervace aplikuje na záznam o využití. Pokud je pro vaši rezervaci povolená flexibilita velikosti instance, pak se může vztahovat na jiné velikosti. Hodnota zobrazuje poměr použití rezervace pro záznam o využití.

## <a name="get-azure-consumption-and-reservation-usage-data-using-api"></a>Získání dat využití a rezervace Azure pomocí rozhraní API

Data můžete získat pomocí rozhraní API nebo si je stáhnout z Azure Portal.

Zavoláte [rozhraní API s podrobnostmi o využití](/rest/api/consumption/usagedetails/list) pomocí rozhraní&quot; API verze &quot;2019-04-01-Preview a získáte nová data. Podrobnosti o terminologii najdete v tématu [použití podmínek](billing-understand-your-usage.md). Volající by měl být podnikovým správcem pro smlouvu Enterprise, který používá [portál EA](https://ea.azure.com). K získání dat můžou získat taky Správci firemních jen pro čtení.

Data nejsou k dispozici v [rozhraní API pro vytváření sestav pro podnikové zákazníky – podrobnosti o využití](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail).

Tady je příklad volání rozhraní API:

```
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{enrollmentId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodId}/providers/Microsoft.Consumption/usagedetails?metric={metric}&amp;api-version=2019-04-01-preview&amp;$filter={filter}
```

Další informace o {Enrollmentid má} a {billingPeriodId} najdete v článku věnovaném podrobnostem o [využití – seznam](https://docs.microsoft.com/rest/api/consumption/usagedetails/list) rozhraní API.

Informace v následující tabulce o metrikě a filtru můžou pomáhat vyřešit běžné problémy s rezervacemi.

| **Typ dat rozhraní API** | Akce volání rozhraní API |
| --- | --- |
| **Všechny poplatky (využití a nákupy)** | Nahradit {metric} pomocí ActualCost |
| **Využití s slevou rezervace** | Nahradit {metric} pomocí ActualCost<br><br>Nahradit {Filter} pomocí: Properties/reservationId% 20ne% 20 |
| **Použití, které nezískalo slevu za rezervaci** | Nahradit {metric} pomocí ActualCost<br><br>Nahradit {Filter} pomocí: Properties/reservationId% 20eq% 20 |
| **Poplatky za amortizaci (využití a nákupy)** | Nahradit {metric} pomocí AmortizedCost |
| **Nepoužitá sestava rezervace** | Nahradit {metric} pomocí AmortizedCost<br><br>Nahradit {Filter} pomocí: Properties/ChargeType% 20eq% 20 ' UnusedReservation ' |
| **Nákupy rezervací** | Nahradit {metric} pomocí ActualCost<br><br>Nahradit {Filter} pomocí: Properties/ChargeType% 20eq% 20 ' purchase '  |
| **Náhrad** | Nahradit {metric} pomocí ActualCost<br><br>Nahradit {Filter} pomocí: Properties/ChargeType% 20eq% 20 refundace |

## <a name="download-the-usage-csv-file-with-new-data"></a>Stáhnout soubor CSV využití s novými daty

Pokud jste správce služby EA, můžete si stáhnout soubor CSV, který obsahuje nová data o využití z Azure Portal. Tato data nejsou k dispozici na [portálu EA](https://ea.azure.com).

V Azure Portal přejděte na [cost management + fakturace](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/BillingAccounts).

1. Vyberte fakturační účet.
2. Klikněte na **využití + poplatky**.
3. Klikněte na tlačítko **Stáhnout**.  
![Příklad ukazující, kde stáhnout soubor dat o využití sdíleného svazku clusteru v Azure Portal](./media/billing-understand-reserved-instance-usage-ea/portal-download-csv.png)
4. V části **Stáhnout využití + poplatky** v části **Podrobnosti o využití verze 2** vyberte **všechny poplatky (využití a nákupy)** a pak klikněte na stáhnout. Zopakujte pro **poplatky za amortizaci (využití a nákupy)** .

Soubory CSV, které stáhnete, obsahují skutečné náklady a náklady na amortizaci.

## <a name="common-cost-and-usage-tasks"></a>Běžné náklady a úlohy využití

V následujících částech jsou běžné úkoly, které většina lidí používá k zobrazení svých nákladů na rezervaci a dat o využití.

### <a name="get-reservation-purchase-costs"></a>Získat nákupní náklady na rezervaci

Náklady na nákup rezervací jsou k dispozici ve skutečných nákladových datech. Filtr pro _ChargeType = nákup_. Informace o tom, pro kterou objednávku rezervace se má koupit, najdete v tématu ProductOrderID.

### <a name="get-underutilized-reservation-quantity-and-costs"></a>Získat nevyužité množství a náklady na rezervaci

Získejte data o amortizaci a filtr pro _ChargeType_ _= UnusedReservation_. Získáte denní nevyužité množství rezervace a náklady. Můžete filtrovat data pro rezervaci nebo objednávku rezervace pomocí polí _ReservationId_ a _ProductOrderId_ . Pokud byla využívána rezervace 100%, má záznam množství 0.

### <a name="amortize-reservation-costs"></a>Náklady na amortizaci na amortizaci

Získejte data o amortizaci a filtr pro objednávku rezervace pomocí _ProductOrderID_ a Získejte denní náklady na vynaloženou cenu za rezervaci.

### <a name="chargeback-for-a-reservation"></a>Vrácení peněz pro rezervaci

Můžete vrácení peněz použití rezervace pro jiné organizace podle předplatného, skupin prostředků nebo značek. Data z amortizace poskytují peněžní hodnotu využití rezervace v následujících datových typech:

- Prostředky (například virtuální počítač)
- Resource group
- Tags
- Subscription

### <a name="get-the-blended-rate-for-chargeback"></a>Získat smíšenou sazbu pro vrácení peněz

Chcete-li zjistit poměrnou sazbu, Získejte data s náklady na amortizaci a celkové náklady. Pro virtuální počítače můžete použít buď informace o měřiči nebo ServiceType z AdditionalInfo dat JSON. Vydělte celkové náklady podle množství, které se používá k získání poměru v Blendu.

### <a name="audit-optimum-reservation-use-for-instance-size-flexibility"></a>Auditovat optimální využití rezervace pro flexibilitu velikosti instance

Vícenásobné množství s _RINormalizationRatio_, od AdditionalInfo. Výsledky ukazují, kolik hodin bylo použito pro záznam o využití rezervace.

### <a name="determine-reservation-savings"></a>Určení úspory rezervací

Získejte data o amortizaci a filtrujte data pro rezervovanou instanci. Stisknutím

1. Získejte odhadované náklady na průběžné platby. Vynásobte hodnotu _JednotkováCena_ hodnotami _množství_ , abyste získali odhadované náklady na průběžné platby, pokud se na použití nepoužila sleva rezervace.
2. Získejte náklady na rezervaci. Sečtěte hodnoty _nákladů_ a získejte peněžní hodnotu toho, co jste zaplatili za rezervovanou instanci. Zahrnuje využité a nevyužité náklady na rezervaci.
3. Odečíst náklady na rezervaci z odhadovaných nákladů na průběžné platby, abyste získali Odhadované úspory.

## <a name="reservation-purchases-and-amortization-in-cost-analysis"></a>Nákupy a amortizace rezervací při analýze nákladů

Náklady na rezervaci jsou dostupné při [analýze nákladů](https://aka.ms/costanalysis). Ve výchozím nastavení vám analýza nákladů zobrazuje **skutečné náklady**, což znamená, jak budou náklady zobrazeny na faktuře. Pokud chcete zobrazit nákupy rezervací rozdělené dolů a spojit se s prostředky, které tuto výhodu využily, přepněte na **náklady**na amortizaci:

![Příklad ukazující, kde vybrat náklady na amortizaci v analýze nákladů](./media/billing-understand-reserved-instance-usage-ea/portal-cost-analysis-amortized-view.png)

Typ seskupit podle poplatků pro zobrazení přerušení použití, nákupu a refundace; nebo rezervací pro rozpis rezervací a nákladů na vyžádání. Mějte na paměti, že jedinými náklady na rezervaci, které se zobrazí při nákupu, se účtují skutečné náklady, ale náklady budou přiděleny individuálním prostředkům, které benfit při prohledání podle amortizace. Při hledání na proložené náklady se vám zobrazí také nový typ **UnusedReservation** poplatků.

## <a name="need-help-contact-us"></a>Potřebujete pomoct? Kontaktujte nás.

Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další postup

Další informace o Azure Reservations najdete v následujících článcích:

- [Co jsou Azure Reservations?](billing-save-compute-costs-reservations.md)
- [Předplacení virtuálních počítačů se službou Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Předplacení výpočetních prostředků SQL Database se záložní kapacitou služby Azure SQL Database](../sql-database/sql-database-reserved-capacity.md)
- [Správa rezervací Azure](billing-manage-reserved-vm-instance.md)
- [Vysvětlení způsobu použití slevy rezervace](billing-understand-vm-reservation-charges.md)
- [Vysvětlení využití rezervací pro předplatné s průběžnými platbami](billing-understand-reserved-instance-usage.md)
- [Náklady na software systému Windows, které nejsou součástí rezervací](billing-reserved-instance-windows-software-costs.md)

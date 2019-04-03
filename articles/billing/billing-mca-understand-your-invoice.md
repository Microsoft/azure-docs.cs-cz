---
title: Vysvětlení faktury smlouvy zákazníka Microsoftu | Dokumentace Microsoftu
description: Zjistěte, jak číst a vysvětlení vašeho vyúčtování služeb MCA
services: ''
documentationcenter: ''
author: jureid
manager: jureid
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: ee6317f61f95b19effd64308b88f53c027582b63
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2019
ms.locfileid: "58883005"
---
# <a name="understand-terms-on-your-microsoft-customer-agreement-invoice"></a>Vysvětlení podmínek na faktuře smlouvy zákazníka se společností Microsoft

Tento článek se týká fakturační účet Microsoft zákaznické smlouvy. [Zaškrtněte, pokud máte přístup k zákaznické smlouvy Microsoft](#check-access-to-a-microsoft-customer-agreement).

Vaše faktura poskytuje souhrn nákladů a pokyny pro platbu. Je k dispozici ke stažení v Portable Document Format (PDF) z [webu Azure portal](https://portal.azure.com/) nebo je lze odeslat e-mailem. Další informace najdete v tématu [zobrazit a stáhnout fakturu Microsoft Azure](billing-download-azure-invoice.md).

<!-- ## When am I billed?

You are invoiced on a monthly basis. You can find out which day of the month you receive invoices by checking *invoice date* under billing profile properties in the [Azure portal](https://portal.azure.com/). Charges that occur between the end of the billing period and the invoice date are included in the next month's invoice, since they are in the next billing period. The billing period start and end dates for each invoice are listed in the invoice PDF above **Billing Summary**. -->

## <a name="invoice-terms-and-descriptions"></a>Podmínky fakturační a popisy

V následujících částech jsou důležité termíny, které se zobrazují na faktuře a popisy pro každé období.

### <a name="invoice-summary"></a>Souhrn faktury

**Souhrnu faktury** je nahoře na stránce první a s informacemi o fakturační profil a jak platit.

![Oddíl Souhrn faktury](./media/billing-understand-your-invoice-mca/invoicesummary.png)

| Označení | Popis |
| --- | --- |
| Kupující |Adresa vaše právní entitu, v účtování vlastnosti účtu|
| Příjemce faktury |Fakturační adresa fakturační profil příjem faktury, v účtování vlastnosti profilu|
| Fakturační profil |Název fakturačního profilu příjem faktury |
| P.O. číslo |Číslo volitelné nákupní objednávky, přiřadil ke sledování |
| Číslo faktury |Jedinečné, vygeneroval Microsoft faktury číslo používané pro účely sledování |
| Datum faktury |Datum faktury se vygeneruje, obvykle 5 až 12 dnů po konci fakturační cyklus. Můžete zkontrolovat vaše datum faktury v fakturační vlastnosti profilu.|
| Platební podmínky |Jak platíte za vyúčtování od Microsoftu. *30 dnů* znamená, že platíte do 30 dní od data vystavení faktury. |

### <a name="billing-summary"></a>Souhrn fakturace

**Fakturace Souhrn** patřičně poplatky proti fakturační profil od předchozího fakturačního období, jakékoli kredity, které byly použity, daň a celkovou velikost.

![Oddíl souhrn fakturace](./media/billing-understand-your-invoice-mca/billingsummary.png)

| Označení | Popis |
| --- | --- |
| Poplatky|Celkový počet Microsoft poplatky za tento fakturační profil od poslední fakturačního období |
| Závěrečné titulky |Kredity, které jste dostali od vrátí |
| Kredity Azure použít | Kredity Azure, která se automaticky použijí Azure účtuje každého fakturačního období |
| Mezisoučet |Dlužná částka před zdaněním |
| Daň |Typ a velikost daň, která platíte, v závislosti na zemi fakturace profilu. Pokud nemusíte zaplatit daň, neuvidíte daně na vaší faktuře. |
| Odhadované celkové úspory |Odhadované celkové množství, které jste si uložili z efektivní slevy. Pokud je k dispozici, efektivní zvýhodněné sazby jsou uvedeny pod položky řádku nákupní podrobnosti faktury z oddílu. |

### <a name="invoice-sections"></a>Oddíly faktury

U každé části faktury za fakturační profil zobrazí se vám poplatky, výše kreditů Azure použít, daň a Celková dlužná částka.

`Total = Charges - Azure Credit + Tax`

### <a name="details-by-invoice-section"></a>Podrobnosti faktury z oddílu

Podrobnosti zobrazení nákladů na každý oddíl faktury rozdělené podle pořadí produktu. V rámci každé objednávky produktů náklady rozdělené podle typu služby. Denní náklady pro své produkty a služby najdete webu Azure portal a využití Azure a poplatky za sdíleného svazku clusteru. Další informace najdete tady [vysvětlení poplatků za na vaší faktuře zákaznické smlouvy Microsoft](billing-mca-understand-your-bill.md).

Celková dlužná částka pro každou řadu service se vypočte tak, že *kredity Azure* z *kredity/poplatky* a přidání *daně*:

<!-- `Total = Charges/Credits - Azure Credit + Tax` -->

![Podrobnosti faktury z oddílu](./media/billing-understand-your-invoice-mca/invoicesectiondetails.png)

| Označení |Popis |
| --- | --- |
| Jednotková cena | Efektivní Jednotková cena služby (v cenách měny), která se používá pro míru využití. Toto je jedinečné pro produkt, řady služeb, měření a nabídky. |
| Množství | Množství zakoupených nebo spotřebované během fakturačního období |
| Poplatky za/kredity | NET výše poplatků po použití kredity/vrácení peněz |
| Azure Credit | Množství použít na poplatky za/kredity kredity Azure|
| Sazba daně | Daň za procenta podle země |
| Částka daně | Částka daně použít k nákupu podle sazba daně |
| Celkem | Celková dlužná částka za nákup |

### <a name="how-to-pay"></a>Způsoby platby

V dolní části faktury jsou pokyny, jak platit vyúčtování. Můžete platit kontrolou při přenosu, nebo online. Pokud platíte online, můžete použít kreditní nebo debetní kartou nebo kredity Azure, pokud je k dispozici.

### <a name="publisher-information"></a>Informace o vydavateli

Pokud máte ve vašem vyúčtování služeb třetích stran, název a adresu každému zdroji je uveden v dolní části faktury.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrola přístupu na základě smlouvy Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás

Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další postup

- [Principy poplatků za fakturační profil faktury](billing-mca-understand-your-bill.md)
- [Jak získat Azure billing invoice a denní data o využití](billing-download-azure-invoice-daily-usage-date.md)
- [Prohlédněte si ceny Azure vaší organizace](billing-ea-pricing.md)
- [Zobrazit daňové doklady pro váš fakturační profil](billing-mca-download-tax-document.md)

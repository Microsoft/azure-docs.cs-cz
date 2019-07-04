---
title: Vysvětlení faktury vaše smlouvy zákazníka Microsoftu v Azure
description: Zjistěte, jak číst a vysvětlení vašeho vyúčtování služeb Microsoft smlouvy zákazníka v Azure
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: fed658d3f672d6116d7c2b0f3e2e9ad989dd67c6
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490641"
---
# <a name="terms-in-your-microsoft-customer-agreement-invoice"></a>Faktura podmínek ve vaší smlouvě zákazníků společnosti Microsoft

Tento článek se týká fakturace účtu Azure pro zákaznické smlouvy Microsoft. [Zaškrtněte, pokud máte přístup k zákaznické smlouvy Microsoft](#check-access-to-a-microsoft-customer-agreement).

Vaše faktura poskytuje souhrn nákladů a pokyny pro platbu. Je k dispozici ke stažení v Portable Document Format (PDF) z [webu Azure portal](https://portal.azure.com/) nebo je lze odeslat e-mailem. Další informace najdete v tématu [zobrazit a stáhnout fakturu Microsoft Azure](billing-download-azure-invoice.md).

## <a name="billing-period"></a>Fakturační období

Se fakturují měsíčně. Můžete zjistit, který den v měsíci dostávat faktury kontrolou *datum faktury* pod vlastností profilu ve fakturaci [webu Azure portal](https://portal.azure.com/). Poplatky, ke kterým dochází mezi konci fakturačního období a datum faktury jsou součástí příští měsíc fakturu, protože jsou do dalšího fakturačního období. Fakturační období počáteční a koncové datum pro každý faktury jsou uvedeny v faktury PDF výše **fakturační Souhrn**.

## <a name="invoice-terms-and-descriptions"></a>Podmínky fakturační a popisy

V následujících částech Seznam důležité termíny, které najdete v článku na vaší faktuře a popisují ke každému termínu.

### <a name="invoice-summary"></a>Souhrn faktury

**Souhrnu faktury** je v horní části na první stránce a s informacemi o fakturační profil a jak platit.

![Oddíl Souhrn faktury](./media/billing-understand-your-invoice-mca/invoicesummary.png)

| Termín | Popis |
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

| Termín | Popis |
| --- | --- |
| Poplatky za|Celkový počet Microsoft poplatky za tento fakturační profil od poslední fakturačního období |
| Závěrečné titulky |Kredity, které jste dostali od vrátí |
| Kredity Azure použít | Kredity Azure, která se automaticky použijí Azure účtuje každého fakturačního období |
| Mezisoučet |Dlužná částka před zdaněním |
| Daně |Typ a velikost daň, která platíte, v závislosti na zemi nebo oblast, váš fakturační profil. Pokud nemusíte zaplatit daň, neuvidíte daně na vaší faktuře. |
| Odhadované celkové úspory |Odhadované celkové množství, které jste si uložili z efektivní slevy. Pokud je k dispozici, efektivní zvýhodněné sazby jsou uvedeny pod položky řádku nákupní podrobnosti faktury z oddílu. |

### <a name="invoice-sections"></a>Části faktury

U každé části faktury za fakturační profil zobrazí se vám poplatky, výše kreditů Azure použít, daň a Celková dlužná částka.

`Total = Charges - Azure Credit + Tax`

### <a name="details-by-invoice-section"></a>Podrobnosti faktury z oddílu

Podrobnosti zobrazení nákladů na každý oddíl faktury rozdělené podle pořadí produktu. V rámci každé objednávky produktů náklady rozdělené podle typu služby. Denní náklady pro své produkty a služby najdete webu Azure portal a využití Azure a poplatky za sdíleného svazku clusteru. Další informace najdete tady [vysvětlení poplatků za na vaší faktuře zákaznické smlouvy Microsoft](billing-mca-understand-your-bill.md).

Celková dlužná částka pro každou řadu service se vypočte tak, že *kredity Azure* z *kredity/poplatky* a přidání *daně*:


![Podrobnosti faktury z oddílu](./media/billing-understand-your-invoice-mca/invoicesectiondetails.png)

| Termín |Popis |
| --- | --- |
| Cena za jednotku | Efektivní Jednotková cena služby (v cenách měny), která se používá pro míru využití. Toto je jedinečné pro produkt, řady služeb, měření a nabídky. |
| Množství | Množství zakoupených nebo spotřebované během fakturačního období |
| Poplatky za/kredity | NET výše poplatků po použití kredity/vrácení peněz |
| Azure Credit | Množství použít na poplatky za/kredity kredity Azure|
| Sazba daně | Daň za procenta podle země/oblasti |
| Částka daně | Částka daně použít k nákupu podle sazba daně |
| Celkem | Celková dlužná částka za nákup |

### <a name="how-to-pay"></a>Způsoby platby

V dolní části faktury jsou pokyny, jak platit vyúčtování. Můžete platit kontrolou při přenosu, nebo online. Pokud platíte online, můžete použít kreditní nebo debetní kartou nebo kredity Azure, pokud je k dispozici.

### <a name="publisher-information"></a>Informace o vydavateli

Pokud máte ve vašem vyúčtování služeb třetích stran, název a adresu každému zdroji je uveden v dolní části faktury.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrola přístupu na základě smlouvy Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další postup

- [Principy poplatků za fakturační profil faktury](billing-mca-understand-your-bill.md)
- [Jak získat data o denním využití a fakturu za Azure](billing-download-azure-invoice-daily-usage-date.md)
- [Prohlédněte si ceny Azure vaší organizace](billing-ea-pricing.md)
- [Zobrazit daňové doklady pro váš fakturační profil](billing-mca-download-tax-document.md)

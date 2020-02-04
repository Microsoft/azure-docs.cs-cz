---
title: Vysvětlení faktury za smlouvu s partnerem Microsoftu v Azure
description: Zjistěte, jak číst fakturu za smlouvu s partnerem Microsoftu v Azure a jak jí rozumět.
author: bandersmsft
manager: jureid
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/22/2019
ms.author: banders
ms.openlocfilehash: 7555fc490804a847e61a46f194e77c62f0a985d9
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2020
ms.locfileid: "75989745"
---
# <a name="terms-in-your-microsoft-partner-agreement-invoice"></a>Výrazy na faktuře za smlouvu s partnerem Microsoftu

Tento článek se týká fakturačního účtu Azure pro smlouvu s partnerem Microsoftu. [Ověřte si, jestli máte přístup ke smlouvě s partnerem Microsoftu](#check-access-to-a-microsoft-partner-agreement).

Na faktuře najdete souhrn poplatků a pokyny k platbě. Můžete si ji stáhnout ve formátu PDF (Portable Document Format) z webu [Azure Portal](https://portal.azure.com/) nebo si ji nechat zaslat e-mailem. Další informace najdete v tématu [Zobrazení a stažení faktury za Microsoft Azure](download-azure-invoice.md).

V následujících částech najdete důležité výrazy, které uvidíte na své faktuře, a jejich popisy.

## <a name="billing-period"></a>Fakturační období

Fakturace probíhá každý měsíc. Poplatky, které se naúčtují mezi koncem fakturačního období a datem faktury, se zahrnou do faktury za další měsíc, protože spadají do dalšího fakturačního období. Počáteční a koncové datum fakturačního období pro každou fakturu najdete v souboru PDF faktury nad položkou **Souhrn fakturace**.

## <a name="invoice-summary"></a>Souhrn faktury

**Souhrn faktury** se nachází v horní části první stránky a obsahuje informace o vašem fakturačním profilu a o tom, jakým způsobem platíte.

<!-- add screenshot -->

| Označení | Popis |
| --- | --- |
| Kupující |Adresa vaší právnické osoby zadaná ve vlastnostech fakturačního účtu|
| Příjemce faktury |Fakturační adresa fakturačního profilu, který dostane fakturu, zadaná ve vlastnostech fakturačního profilu|
| Fakturační profil |Název fakturačního profilu, který dostane fakturu |
| P.O. číslo |Volitelné číslo nákupní objednávky, které máte přiřazené pro účely sledování |
| Číslo faktury |Jedinečné číslo faktury vygenerované Microsoftem, které se používá pro účely sledování |
| Datum faktury |Datum vygenerování faktury, většinou 5 až 12 dní po konci fakturačního období. Datum faktury můžete zkontrolovat ve vlastnostech fakturačního profilu|
| Platební podmínky |Způsob, jakým platíte faktury od Microsoftu. *Čistých 60 dní* znamená, že platíte do 60 dnů od vystavení faktury. |

## <a name="billing-summary"></a>Souhrn fakturace

**Souhrn fakturace** obsahuje poplatky pro daný fakturační profil od předchozího fakturačního období, uplatněné kredity, daně a celkovou dlužnou částku.

<!-- add screenshot -->

| Označení | Popis |
| --- | --- |
| Poplatky|Celková výše poplatků Microsoftu pro tento fakturační profil od posledního fakturačního období |
| Kredity |Kredity, které jste dostali za vratky |
| Uplatněné kredity Azure | Kredity Azure, které se v každém fakturačním období automaticky uplatňují na poplatky za Azure |
| Mezisoučet |Splatná částka před zdaněním |
| Daň |Typ a částka daně, kterou máte zaplatit, v závislosti na zemi/oblasti fakturačního profilu. Pokud žádnou daň platit nemusíte, na vaší faktuře se nezobrazí |
| Odhadované celkové úspory |Odhad celkové částky, kterou jste ušetřili díky platným slevám. Případné platné slevové sazby najdete v řádkových položkách nákupu v oddílu Podrobnosti podle faktury |


## <a name="billing-details-by-product"></a>Podrobnosti o fakturaci podle konkrétního produktu

Část **Podrobnosti o fakturaci podle konkrétního produktu** uvádí celkové poplatky za jednotlivé produkty přidružené k tomuto fakturačnímu profilu. Denní rozpis poplatků za jednotlivé služby můžete zobrazit v souboru CSV s informacemi o využití a poplatcích za Azure. Informace o párování souboru PDF faktury se souborem CSV s informacemi o využití a poplatcích za Azure najdete v tématu [Vysvětlení informací na faktuře za smlouvu s partnerem Microsoftu](review-partner-agreement-bill.md).

## <a name="how-to-pay"></a>Postup platby

Ve spodní části faktury najdete pokyny pro úhradu. Do 60 dnů od vystavení faktury můžete zaplatit šekem nebo převodem.

## <a name="publisher-information"></a>Informace o vydavateli

Pokud máte na faktuře služby třetích stran, zobrazí se ve spodní části faktury název a adresa každého vydavatele.

## <a name="check-access-to-a-microsoft-partner-agreement"></a>Kontrola přístupu ke smlouvě s partnerem Microsoftu
[!INCLUDE [billing-check-mpa](../../../includes/billing-check-mpa.md)]

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky

- [Vysvětlení poplatků na faktuře pro váš fakturační profil](review-customer-agreement-bill.md)
- [Jak získat data o denním využití a fakturu za Azure](../manage/download-azure-invoice-daily-usage-date.md)
- [Zobrazení cen Azure pro vaši organizaci](../manage/ea-pricing.md)
- [Zobrazení daňových dokumentů pro váš fakturační profil](mca-download-tax-document.md)

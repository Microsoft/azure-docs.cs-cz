---
title: Pochopení faktury zákaznického souhlasu Microsoftu v Azure
description: Přečtěte si, jak číst a pochopit vaše smlouvy o zákaznících Microsoftu v Azure.
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/22/2019
ms.author: banders
ms.openlocfilehash: a5f77120c1d4e8a6721f3bc207132bee19a7772f
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/22/2019
ms.locfileid: "68383541"
---
# <a name="terms-in-your-microsoft-customer-agreement-invoice"></a>Výrazy na faktuře se zákaznickou smlouvou Microsoftu

Tento článek se týká fakturačního účtu Azure pro zákaznickou smlouvu Microsoftu. [Ověřte, jestli máte přístup k smlouvě o zákaznících Microsoftu](#check-access-to-a-microsoft-customer-agreement).

Vaše faktura poskytuje souhrn poplatků a pokynů k platbám. Je k dispozici ke stažení ve formátu Portable Document Format (. PDF) z [Azure Portal](https://portal.azure.com/) nebo je lze odeslat e-mailem. Další informace najdete v tématu [zobrazení a stažení faktury Microsoft Azure](billing-download-azure-invoice.md).

## <a name="billing-period"></a>Fakturační období

Fakturuje se vám každý měsíc. To, který den v měsíci obdržíte, zjistíte tak, že zkontrolujete *Datum faktury* ve vlastnostech fakturačního profilu ve [Azure Portal](https://portal.azure.com/). Poplatky, které se vyskytují mezi koncem fakturačního období a datem faktury, jsou zahrnuté do faktury za příští měsíc, protože se nacházejí v dalším fakturačním období. Počáteční a koncové datum fakturačního období pro každou fakturu najdete v souhrnu fakturace v dokumentu faktury.

## <a name="invoice-terms-and-descriptions"></a>Výrazy a popisy faktury

V následujících oddílech jsou uvedeny důležité termíny, které vidíte na faktuře, a uveďte popisy jednotlivých termínů.

### <a name="invoice-summary"></a>Souhrn faktury

**Souhrn faktury** je v horní části první stránky a zobrazuje informace o vašem fakturačním profilu a způsobu, jakým platíte.

![Část Souhrn faktury](./media/billing-understand-your-invoice-mca/invoicesummary.png)

| Termín | Popis |
| --- | --- |
| Kupující |Adresa vaší právní entity, která se našla ve vlastnostech fakturačního účtu|
| Příjemce faktury |Fakturační adresa fakturačního profilu, který obdrží fakturu, se našla ve vlastnostech fakturačního profilu.|
| Fakturační profil |Název fakturačního profilu, který obdrží fakturu |
| P.O. číslo |Volitelné číslo nákupní objednávky přiřazené vámi pro sledování |
| Číslo faktury |Jedinečné číslo faktury vygenerované společností Microsoft, které se používá pro účely sledování |
| Datum faktury |Datum, kdy se faktura vygeneruje, obvykle pět až 12 dní po skončení fakturačního cyklu. Datum faktury můžete kontrolovat ve vlastnostech fakturačního profilu.|
| Platební podmínka |Jak platíte za váš účet Microsoft. *Čisté 30 dnů* znamená, že platíte do 30 dnů od data faktury. |

### <a name="billing-summary"></a>Souhrn fakturace

**Souhrn fakturace** zobrazuje poplatky za fakturační profil od předchozího fakturačního období, všech kreditů, které byly uplatněny, daní a celkové dlužné částky.

![Část Souhrn fakturace](./media/billing-understand-your-invoice-mca/billingsummary.png)

| Termín | Popis |
| --- | --- |
| Poplatky|Celkový počet poplatků za Microsoft za tento Fakturační profil od posledního fakturačního období |
| Závěrečné titulky |Kredity, které jste dostali z vrácených |
| Použité kredity Azure | Kredity Azure, které se automaticky vztahují na Azure, se účtují za každé fakturační období. |
| Mezisoučet |Splatná částka před zdaněním |
| Daň |Typ a množství daně, které platíte, v závislosti na zemi nebo oblasti vašeho fakturačního profilu. Pokud nemusíte platit daň, neuvidíte daň na faktuře. |
| Odhadované celkové úspory |Odhadovaná celková velikost, kterou jste uložili z efektivní slevy. V případě potřeby jsou platné zlevněné sazby uvedené pod sekcí řádky nákupu v části Podrobnosti podle faktury. |

### <a name="invoice-sections"></a>Oddíly faktury

U každé faktury v rámci vašeho fakturačního profilu se vám budou účtovat poplatky, množství využitých kreditů Azure, daň a celková dlužná částka.

`Total = Charges - Azure Credit + Tax`

### <a name="details-by-invoice-section"></a>Část Podrobnosti podle faktury

V části Podrobnosti se zobrazuje náklady na jednotlivé faktury rozdělené podle objednávky produktu. V rámci každé objednávky produktu se náklady rozdělují podle typu služby. Denní poplatky za vaše produkty a služby najdete v části Azure Portal a využití Azure a v CSV. Další informace najdete v tématu [vysvětlení poplatků na faktuře pro smlouvy o zákaznících Microsoftu](billing-mca-understand-your-bill.md).

Celková částka splatná pro každou rodinu služeb se počítá odečtením *kreditů Azure* od kreditů a *poplatků* a připočtením *daně*:


![Část Podrobnosti podle faktury](./media/billing-understand-your-invoice-mca/invoicesectiondetails.png)

| Termín |Popis |
| --- | --- |
| Jednotková cena | Efektivní Jednotková cena služby (v cenové měně), která se používá k sazbě využití. Toto je jedinečné pro produkt, rodinu služeb, měřič a nabídku. |
| Množství | Zakoupené nebo spotřebované množství během fakturačního období |
| Poplatky/kredity | Čisté množství poplatků po použití kreditů nebo refundací |
| Azure Credit | Množství kreditů Azure, které se vztahují na poplatky nebo kredity|
| Daňová sazba | Daňové sazby v závislosti na zemi nebo oblasti |
| Daňová částka | Množství daně použité k nákupu na základě daňové sazby |
| Celkem | Celková částka splatná za nákup |

### <a name="how-to-pay"></a>Jak platíte

V dolní části faktury jsou k dispozici pokyny pro platbu faktury. Můžete platit kontrolou, telegraficky nebo online. Pokud platíte online, můžete použít platební kartu nebo kredity Azure, pokud je to možné.

### <a name="publisher-information"></a>Informace o vydavateli

Pokud máte ve vyúčtování služby třetích stran, zobrazí se v dolní části faktury název a adresa každého vydavatele.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Podívejte se na přístup k zákaznickým smlouvám Microsoftu
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Potřebujete pomoct? Kontaktujte nás.

Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další postup

- [Pochopte poplatky na faktuře vašeho fakturačního profilu.](billing-mca-understand-your-bill.md)
- [Jak získat data o denním využití a fakturu za Azure](billing-download-azure-invoice-daily-usage-date.md)
- [Zobrazení cen za Azure ve vaší organizaci](billing-ea-pricing.md)
- [Zobrazení daňových dokumentů pro fakturační profil](billing-mca-download-tax-document.md)

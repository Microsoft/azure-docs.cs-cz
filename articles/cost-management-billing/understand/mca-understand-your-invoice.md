---
title: Vysvětlení faktury za smlouvu se zákazníkem Microsoftu v Azure
description: Zjistěte, jak číst fakturu za smlouvu se zákazníkem Microsoftu v Azure a jak jí rozumět.
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 04/08/2021
ms.author: banders
ms.openlocfilehash: ff53131f3078b33b7e7d853c1fca891b0b86d792
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484590"
---
# <a name="terms-in-your-microsoft-customer-agreement-invoice"></a>Výrazy na faktuře za smlouvu se zákazníkem Microsoftu

Tento článek se týká fakturačního účtu Azure za smlouvu se zákazníkem Microsoftu. [Ověřte si, jestli máte přístup ke smlouvě se zákazníkem Microsoftu](#check-access-to-a-microsoft-customer-agreement).

Na faktuře najdete souhrn poplatků a pokyny k platbě. Můžete si ji stáhnout ve formátu PDF (Portable Document Format) z webu [Azure Portal](https://portal.azure.com/) nebo si ji nechat zaslat e-mailem. Další informace najdete v tématu [Zobrazení a stažení faktury za Microsoft Azure](download-azure-invoice.md).

Podívejte se na video o [fakturaci zákaznické smlouvy Microsoftu](https://www.youtube.com/watch?v=e2LGZZ7GubA) , kde se dozvíte o vaší faktuře a o tom, jak s nimi analyzovat poplatky.

>[!VIDEO https://www.youtube.com/embed/e2LGZZ7GubA]

## <a name="billing-period"></a>Fakturační období

Fakturace probíhá každý měsíc. To, který den v měsíci dostáváte faktury, zjistíte podle *data faktury* ve vlastnostech fakturačního profilu na webu [Azure Portal](https://portal.azure.com/). Poplatky, které se naúčtují mezi koncem fakturačního období a datem faktury, se zahrnou do faktury za další měsíc, protože spadají do dalšího fakturačního období. Počáteční a koncové datum fakturačního období pro každou fakturu najdete v souboru PDF faktury nad položkou **Souhrn fakturace**.

Pokud migrujete z smlouvy EA na zákaznickou smlouvu Microsoftu, budete mít i nadále dostávat faktury za váš EA, dokud neproběhne datum migrace. Nová faktura pro vaši smlouvu o zákaznících Microsoftu se vygeneruje pátý den v měsíci po dokončení migrace. První faktura zobrazuje částečný poplatek od data migrace. Pozdější faktury se generují každý měsíc a zobrazují se všechny poplatky za každý měsíc.

### <a name="changes-for-pay-as-you-go-subscriptions"></a>Změny předplatných s průběžnými platbami

Když se předplatné převede, přenese nebo zruší, bude poslední vygenerovaná faktura obsahovat poplatky za předchozí fakturační cyklus a nový nedokončený fakturační cyklus.

Například:

Předpokládejme, že váš fakturační cyklus předplatného s průběžnými platbami je od dne 8 do 7. den v měsíci. Předplatné se od 16. listopadu přeneslo na zákaznickou smlouvu Microsoftu. Poslední faktura za průběžné platby zahrnuje poplatky za 8. října 2020 do 7. listopadu 2020. Obsahuje taky poplatky za nový částečný fakturační cyklus smlouvy o zákaznících Microsoftu od 8. listopadu 2020 do 16. listopadu 2020. Tady je ukázkový obrázek.

:::image type="content" source="./media/mca-understand-your-invoice/last-invoice-billing-cycle.png" alt-text="Příklad obrázku faktury znázorňující poslední fakturační cyklus." lightbox="./media/mca-understand-your-invoice/last-invoice-billing-cycle.png" :::

## <a name="invoice-terms-and-descriptions"></a>Výrazy na faktuře a jejich popis

V následujících částech najdete důležité výrazy, které uvidíte na své faktuře, a jejich popisy.

### <a name="invoice-summary"></a>Souhrn faktury

**Souhrn faktury** se nachází v horní části první stránky a obsahuje informace o vašem fakturačním profilu a o tom, jakým způsobem platíte.

![Oddíl Souhrn faktury](./media/mca-understand-your-invoice/invoicesummary.png)

| Období | Popis |
| --- | --- |
| Kupující |Adresa vaší právnické osoby zadaná ve vlastnostech fakturačního účtu|
| Příjemce faktury |Fakturační adresa fakturačního profilu, který dostane fakturu, zadaná ve vlastnostech fakturačního profilu|
| Fakturační profil |Název fakturačního profilu, který dostane fakturu |
| P.O. číslo |Volitelné číslo nákupní objednávky, které máte přiřazené pro účely sledování |
| Číslo faktury |Jedinečné číslo faktury vygenerované Microsoftem, které se používá pro účely sledování |
| Datum faktury |Datum vygenerování faktury, většinou 5 až 12 dní po konci fakturačního období. Datum faktury můžete zkontrolovat ve vlastnostech fakturačního profilu|
| Platební podmínky |Způsob, jakým platíte faktury od Microsoftu. *Čistých 30 dní* znamená, že platíte do 30 dnů od data faktury |

### <a name="billing-summary"></a>Souhrn fakturace

**Souhrn fakturace** obsahuje poplatky pro daný fakturační profil od předchozího fakturačního období, uplatněné kredity, daně a celkovou dlužnou částku.

![Oddíl Souhrn fakturace](./media/mca-understand-your-invoice/billingsummary.png)

| Období | Popis |
| --- | --- |
| Poplatky|Celková výše poplatků Microsoftu pro tento fakturační profil od posledního fakturačního období |
| Kredity |Kredity, které jste dostali za vratky |
| Uplatněné kredity Azure | Kredity Azure, které se v každém fakturačním období automaticky uplatňují na poplatky za Azure |
| Mezisoučet |Splatná částka před zdaněním |
| Daň |Typ a částka daně, kterou máte zaplatit, v závislosti na zemi/oblasti fakturačního profilu. Pokud žádnou daň platit nemusíte, na vaší faktuře se nezobrazí |
| Odhadované celkové úspory |Odhad celkové částky, kterou jste ušetřili díky platným slevám. Případné platné slevové sazby najdete v řádkových položkách nákupu v oddílu Podrobnosti podle faktury |

### <a name="invoice-sections"></a>Oddíly faktury

Na každé faktuře v rámci vašeho fakturačního uvidíte poplatky, výši uplatněných kreditů Azure, daň a celkovou dlužnou částku.

`Total = Charges - Azure Credit + Tax`

### <a name="details-by-invoice-section"></a>Oddíl Podrobnosti podle faktury

Tady se zobrazují náklady z každého oddílu faktury rozdělené podle objednávek produktu. V rámci každé objednávky produktu jsou náklady rozdělené podle typu služby. Denní poplatky za produkty a služby najdete na webu Azure Portal a v souboru CSV s informacemi o využití Azure a poplatcích. Další informace najdete v tématu [Vysvětlení poplatků na faktuře za smlouvu se zákazníkem Microsoftu](review-customer-agreement-bill.md).

Celková dlužná částka za každou řadu služeb se vypočítá odečtením *kreditů Azure* od hodnoty *Kredity/poplatky* a přičtením hodnoty *Daň*:


![Oddíl Podrobnosti podle faktury](./media/mca-understand-your-invoice/invoicesectiondetails.png)

| Období |Popis |
| --- | --- |
| Jednotková cena | Platná jednotková cena služby (v ceníkové měně), která se používá k nacenění využívání. Liší se v závislosti na produktu, řadě služeb, měřiči a nabídce |
| Množství | Množství zakoupené nebo spotřebované během fakturačního období |
| Poplatky/kredity | Čistá částka poplatků po uplatnění kreditů/refundací |
| Kredit Azure | Výše kreditů Azure uplatněná na hodnotu Poplatky/kredity|
| Daňová sazba | Daňové sazby v závislosti na zemi/oblasti |
| Částka daně | Částka daně přičtená k nákupu na základě daňové sazby |
| Celkem | Celková částka splatná za nákup |

### <a name="how-to-pay"></a>Postup platby

Ve spodní části faktury najdete pokyny pro úhradu. Můžete platit šekem, bezhotovostním převodem nebo online. Pokud platíte online, můžete použít platební kartu nebo kredity Azure, pokud je to možné.

### <a name="publisher-information"></a>Informace o vydavateli

Pokud máte na faktuře služby třetích stran, zobrazí se ve spodní části faktury název a adresa každého vydavatele.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrola přístupu k zákaznické smlouvě Microsoftu
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky

- [Vysvětlení poplatků na faktuře pro váš fakturační profil](review-customer-agreement-bill.md)
- [Jak získat data o denním využití a fakturu za Azure](../manage/download-azure-invoice-daily-usage-date.md)
- [Zobrazení cen Azure pro vaši organizaci](../manage/ea-pricing.md)
- [Zobrazení daňových dokumentů pro váš fakturační profil](mca-download-tax-document.md)

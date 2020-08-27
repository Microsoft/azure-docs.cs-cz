---
title: Vysvětlení faktury za Azure
description: Zjistěte, jak číst informace o využití a vyúčtování předplatného Azure a jak je chápat
author: bandersmsft
ms.reviewer: jureid
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: b7b115f909c57470bfbdab9cc44e6a258f85b8f1
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2020
ms.locfileid: "88685914"
---
# <a name="understand-terms-on-your-microsoft-azure-invoice"></a>Vysvětlení výrazů na faktuře za Microsoft Azure

Na faktuře najdete souhrn poplatků a pokyny k platbě. Můžete si ji stáhnout ve formátu PDF (Portable Document Format) z webu [Azure Portal](https://portal.azure.com/) nebo si ji nechat zaslat e-mailem. Další informace najdete v tématu, které vysvětluje, [jak získat fakturu a informace o denním využívání Azure](../manage/download-azure-invoice-daily-usage-date.md).

Pár věcí, které stojí za zmínku:

-   Pokud používáte bezplatnou zkušební verzi předplatného, můžete podrobné informace o využití zobrazit na webu Azure Portal, ale nemáte fakturu.

-   Na aktuální faktuře se může zobrazit využití za dobu až 24 hodin z konce předchozího fakturačního období.

-   Poplatky uvedené na výpisu vyúčtování pro mezinárodní zákazníky slouží pouze jako odhad. Směnné kurzy jednotlivých bank se můžou lišit.

>[!VIDEO https://www.youtube.com/embed/jWG1lyJe3Mg]

## <a name="detailed-terms-and-descriptions-of-your-invoice"></a>Podrobnosti o výrazech a popis faktury
V následujících částech najdete důležité výrazy, které uvidíte na své faktuře, a jejich popisy.

### <a name="account-information"></a>Informace o účtu

Oddíl Informace o účtu najdete v horní části první stránky faktury. Obsahuje informace o vašem profilu a předplatném.

![Oddíl faktury Informace o účtu](./media/understand-invoice/account-information-section.png)

| Období | Popis |
| --- | --- |
| Č. objednávky zákazníka |Volitelné číslo nákupní objednávky, které máte přiřazené pro účely sledování |
| Faktura č. |Jedinečné číslo faktury vygenerované Microsoftem, které se používá pro účely sledování |
| Fakturační cyklus |Rozsah dat, který tato faktura pokrývá |
| Datum faktury |Datum vygenerování faktury, většinou den po konci fakturačního období |
| Způsob platby |Typ platby používaný u účtu (faktura nebo kreditní karta) |
| Příjemce faktury |Fakturační adresa uvedená pro daný účet |
| Nabídka předplatného („průběžné platby“) |Typ zakoupené nabídky předplatného (Průběžné platby, BizSpark Plus, Azure Pass atd.). Další informace najdete v tématu věnovaném [typům nabídek Azure](https://azure.microsoft.com/support/legal/offer-details/). |
| E-mail vlastníka účtu | E-mailová adresa účtu, pod kterou je účet Microsoft Azure registrovaný. <br /><br />Pokud chcete e-mailovou adresu změnit, přečtěte si téma věnované tomu, [jak změnit informace v profilu účtu Azure, jako je kontaktní e-mail, adresa a telefonní číslo](../manage/change-azure-account-profile.md). |

### <a name="understand-the-invoice-summary"></a>Vysvětlení souhrnu faktury
V oddílu faktury **Souhrn faktury** najdete celkové částky transakcí od posledního fakturačního období a vaše aktuální poplatky za využívání.

![Oddíl Souhrn faktury](./media/understand-invoice/invoice-summary-section.png)

Název předplatného („Produkční úložiště“) je název předplatného pro tuto fakturu.

#### <a name="understand-the-previous-charges"></a>Vysvětlení předchozích poplatků
Oddíl faktury s předchozím zůstatkem, platbami a nevyrovnaným zůstatkem obsahuje souhrn transakcí od posledního fakturačního období.

| Období | Popis |
| --- | --- |
| Předchozí zůstatek |Celková dlužná částka od posledního fakturačního období |
| Platby |Celkové platby a kredity uplatněné na poslední fakturační období |
| Nevyrovnaný zůstatek (z předchozího fakturačního cyklu) |Veškeré kredity nebo zbývající zůstatek na vašem účtu od posledního fakturačního období |

#### <a name="understand-the-current-charges"></a>Vysvětlení aktuálních poplatků
Oddíl faktury s aktuálními poplatky obsahuje podrobnosti o měsíčních poplatcích za aktuální fakturační období.

| Období | Popis |
| --- | --- |
| Poplatky za používání |Poplatky za využívání jsou celkové měsíční poplatky za využívání předplatného pro aktuální fakturační období|
| Slevy |Slevy za služby uplatněné na aktuální fakturační období|
| Úpravy |Různé kredity jsou různé kredity (bezplatné využití, kredity atd.) nebo neuhrazené poplatky uplatněné na aktuální fakturační období.<br/><br/>Pokud máte například nabídku Visual Studio Enterprise with MSDN, uvidíte u této položky měsíční kredit. Pokud zrušíte předplatné, uvidíte veškeré poplatky za měsíční využívání překračující kredit, který dostáváte k nabídce předplatného. Poplatky se počítají od začátku aktuálního fakturačního období do data zrušení předplatného. |

#### <a name="sold-to-and-payment-instructions"></a>Kupující a pokyny pro platbu

Následující tabulka obsahuje popis polí Kupující a Pokyny pro platbu na druhé stránce faktury.

| Období |Popis |
| --- | --- |
| Kupující |Adresa profilu na účtu <br/><br/>Pokud potřebujete adresu změnit, přečtěte si téma věnované tomu, [jak změnit informace v profilu účtu Azure, jako je kontaktní e-mail, adresa a telefonní číslo](../manage/change-azure-account-profile.md).|
| Pokyny pro platbu |Pokyny pro platbu v závislosti na způsobu platby (například pomocí platební karty nebo na fakturu) |

#### <a name="usage-charges"></a>Poplatky za používání

Oddíl faktury Poplatky za používání obsahuje informace o poplatcích na úrovni jednotlivých měřičů.

![Oddíl Poplatky za používání](./media/understand-invoice/usage-charges-section.png)

Následující tabulka popisuje záhlaví sloupců s poplatky za používání uvedená na faktuře.

| Období |Popis |
| --- | --- |
| Název |Udává službu nejvyšší úrovně, které se toto využití týká |
| Typ |Definuje typ služby Azure, který může mít vliv na sazbu |
| Prostředek |Určuje měrnou jednotku spotřebovávaného měřiče |
| Oblast |Určuje polohu datového centra u některých služeb, jejichž ceny vycházejí z umístění datového centra |
| Spotřebované |Množství spotřebované na daném měřiči za příslušné fakturační období |
| Zahrnuje |Množství na daném měřiči, které je v aktuálním fakturačním období zahrnuté bezplatně |
| Fakturovatelné |Udává rozdíl mezi spotřebovaným množstvím a zahrnutým množstvím. Toto množství vám budeme fakturovat. U nabídek s průběžnými platbami, které nezahrnují žádné množství, bude tento součet stejný jako spotřebované množství |
| Sazba |Sazba, která se vám účtuje za jednu fakturovatelnou jednotku |
| Hodnota |Udává výsledek součinu sloupců Nadlimitní množství a Sazba. Pokud spotřebované množství nepřekročí zahrnuté množství, v tomto sloupci nezobrazí žádný poplatek |
| Mezisoučet |Součet všech poplatků před zdaněním za toto fakturační období |
| Celkový součet |Součet všech poplatků po zdanění za toto fakturační období |

## <a name="how-do-i-make-sure-that-the-charges-in-my-invoice-are-correct"></a>Jak mám zkontrolovat, jestli jsou poplatky na faktuře správné?
Pokud na faktuře najdete poplatek, o kterém byste se rádi dozvěděli víc, přečtěte si téma [Vysvětlení faktury za Microsoft Azure](review-individual-bill.md).

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

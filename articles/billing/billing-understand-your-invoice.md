---
title: Vysvětlení faktuře Azure | Dokumentace Microsoftu
description: Zjistěte, jak číst informace o využití a vyúčtování předplatného Azure a jak je chápat
services: ''
documentationcenter: ''
author: tonguyen10
manager: tonguyen
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: cwatson
ms.openlocfilehash: 4bfaaa51e39a7db297626a8009306eef9e3efa80
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47393777"
---
# <a name="understand-terms-on-your-microsoft-azure-invoice"></a>Vysvětlení podmínek na faktuře Microsoft Azure

Faktury poskytuje přehled poplatky a poskytuje pokyny pro platbu. Je k dispozici ke stažení v Portable Document Format (PDF) z [webu Azure portal](https://portal.azure.com/) nebo je lze odeslat e-mailem. Další informace najdete v tématu [jak získat Azure billing invoice a denní data o využití](billing-download-azure-invoice-daily-usage-date.md).

Mějte na paměti několik věcí:

-   Pokud používáte bezplatné předplatné zkušební verze, vaše podrobné informace o použití můžete získat z webu Azure portal, ale nemáte k dispozici fakturu.

-   Až 24 hodin využití na konci předchozího fakturačního období může zobrazit na aktuální faktuře.

-   Poplatky uvedené na výpisy pro mezinárodní zákazníky, kteří jsou odhadu pouze pro účely. Bank mohou mít různé náklady pro míry úspěšnosti.

>[!VIDEO https://www.youtube.com/embed/jWG1lyJe3Mg]

## <a name="detailed-terms-and-descriptions-of-your-invoice"></a>Podrobné podmínky a popisy faktuře
V následujících částech jsou důležité termíny, které se zobrazují na faktuře a popisy pro každé období.

### <a name="account-information"></a>Informace o účtu

Části informace o účtu faktury se nahoře na první stránce a zobrazuje informace o profilu a předplatné.

![Informace o účtu část faktury](./media/billing-understand-your-invoice/1.png)

| Označení | Popis |
| --- | --- |
| Číslo objednávky zákazníka. |Číslo volitelné nákupní objednávky, přiřadil ke sledování |
| Faktura č. |Jedinečné, číslo faktury vygenerované Microsoft používá pro účely sledování |
| Fakturační cyklus |Rozsah dat, která zahrnuje toto faktury |
| Datum faktury |Datum faktury vygenerovaný, obvykle za den za koncem fakturační cyklus |
| Způsob platby |Typ platby používaný u účtu (faktura nebo kreditní karta) |
| Příjemce faktury |Fakturační adresa, která je uvedená pro účet |
| Předplatné nabízejí ("průběžné platby") |Typ nabídky předplatného, který byl zakoupen (průběžné platby, BizSpark Plus, Azure Pass atd.). Další informace najdete v tématu [typy nabídek Azure](https://azure.microsoft.com/support/legal/offer-details/). |
| E-mail vlastníka účtu | E-mailová adresa účtu, pod kterou je účet Microsoft Azure registrovaný. <br /><br />Chcete-li změnit e-mailovou adresu, [jak ke změně informací profilu účtu Azure, jako je například kontaktní e-mailovou adresu a telefonní číslo](billing-how-to-change-azure-account-profile.md). |

### <a name="understand-the-invoice-summary"></a>Vysvětlení souhrnu faktury
**Souhrnu faktury** části faktury jsou uvedeny částky celková transakce od vaší poslední fakturačního období a vaše aktální poplatky za používání.

![Oddíl Souhrn faktury](./media/billing-understand-your-invoice/2.png)

Název předplatného ("produkční úložiště") je název předplatného pro tuto fakturu.

#### <a name="understand-the-previous-charges"></a>Vysvětlení předchozí poplatků za
Předchozí zůstatek, platbami a nevyrovnaným zůstatkem části faktury shrnuje transakce od vaší poslední fakturačního období.

| Označení | Popis |
| --- | --- |
| Předchozí zůstatek |Celková dlužná částka z poslední fakturačního období |
| Platby |Celkové platby a kredity u vaší poslední fakturačního období |
| Nevyrovnaný zůstatek (z předchozího fakturačního cyklu) |Všechny zbývající zůstatek na účtu od vaší poslední fakturačního období nebo kredity |

#### <a name="understand-the-current-charges"></a>Vysvětlení aktuálních poplatků
Aktuální poplatky části faktury zobrazuje podrobnosti o měsíčních poplatcích pro aktuální fakturační období.

| Označení | Popis |
| --- | --- |
| Poplatky za používání |Poplatky za využívání jsou celkové měsíční poplatky za odběr pro aktuální fakturační období|
| Slevy |U aktuálního fakturačního období slevy|
| Úpravy |Různé kredity (bezplatné využití, kredity atd.) nebo nezaplacené poplatky na aktuální fakturační období.<br/><br/>Například pokud máte Visual Studio Enterprise v rámci nabídky MSDN, uvidíte měsíční kredit. Pokud svoje předplatné zrušíte, zobrazí se veškeré měsíční poplatky využití přesahující měsíční kredit, který získáte v rámci vaší nabídky předplatného. Poplatky se vám účtovat na začátku aktuálního fakturačního období až do data zrušení odběru. |

#### <a name="sold-to-and-payment-instructions"></a>Kupující a pokyny pro platbu

Následující tabulka popisuje prodávané na a pokyny pro platbu na druhé stránce faktuře.

| Označení |Popis |
| --- | --- |
| Kupující |Adresa profilu, který je v účtu. <br/><br/>Pokud potřebujete změnit adresu, přečtěte si [jak ke změně informací profilu účtu Azure, jako je například kontaktní e-mailovou adresu a telefonní číslo](billing-how-to-change-azure-account-profile.md).|
| Pokyny pro platbu |Návod, jak platit v závislosti na způsobu platby (například jako autor kredit ve výši karty nebo na fakturu). |

#### <a name="usage-charges"></a>Poplatky za využívání

V části poplatky za využívání, faktury zobrazí úrovni informace měření v poplatky.

![Části poplatky za využívání](./media/billing-understand-your-invoice/3.png)

Následující tabulka popisuje záhlaví sloupců poplatky za využití uvedené na vaší faktuře.

| Označení |Popis |
| --- | --- |
| Název |Identifikuje službu nejvyšší úrovně za využití |
| Typ |Definuje typ služeb Azure, který může mít vliv na sazbu |
| Prostředek |Určuje měrnou jednotku pro měření spotřebovávanou |
| Oblast |Určuje polohu datacentra pro určité služby, které se počítají na základě umístění datového centra |
| Spotřebované |Množství měřiče použít během fakturačního období |
| Zahrnuje |Množství měřiče, které je zdarma v aktuálním fakturačním období |
| Fakturovatelné |Ukazuje rozdíl mezi spotřebované množství a zahrnuté množství. Tuto částku vám budeme fakturovat. U nabídek s průběžnými platbami se nabídka nezahrnuje žádné množství je tento součet stejný jako spotřebované množství |
| Sazba |Sazba se účtuje za fakturovatelnou jednotku |
| Hodnota |Zobrazuje výsledek součinu sloupce Nadlimitní množství sloupcem sazba. Pokud spotřebované množství nepřekročí zahrnuté množství, neplatí žádné poplatky v tomto sloupci. |
| Dílčí součet |Součet všech vaše náklady před zdaněním za tento fakturační období |
| Celkový součet |Součet všech poplatků po zdanění za tento fakturační období |

## <a name="how-do-i-make-sure-that-the-charges-in-my-invoice-are-correct"></a>Jak můžu se ujistit, že jsou správné poplatky na mé faktuře?
Pokud na vaší faktuře, kterou byste uvítali další podrobnosti se účtuje poplatek, přečtěte si téma [vysvětlení vašeho vyúčtování služeb Microsoft Azure.](billing-understand-your-bill.md)

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Obraťte se na podporu.
Pokud stále potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pro rychlé vyřešení problému.

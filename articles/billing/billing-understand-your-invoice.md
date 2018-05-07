---
title: Pochopení faktury Azure
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
ms.author: tonguyen
ms.openlocfilehash: 38126e4539719ba56e6e5eac5e860cea9b49d446
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2018
---
# <a name="understand-terms-on-your-microsoft-azure-invoice"></a>Pochopit podmínky na vaší faktuře Microsoft Azure

Faktury poskytuje souhrn vaše poplatky a poskytuje pokyny pro platbu. Je k dispozici ke stažení ve Portable Document Format (PDF) z [portál Azure](https://portal.azure.com/) nebo lze odeslat e-mailem. Další informace najdete v tématu [jak získat vaše Azure fakturace faktury a denní data o využití](billing-download-azure-invoice-daily-usage-date.md).

Všimněte si, několik akcí:

-   Pokud používáte bezplatné předplatné zkušební verze, můžete získat vaše podrobné informace o použití portálu Azure, ale nemáte faktury.

-   Až 24 hodin využití na konci fakturačního období předchozí může zobrazí v aktuální faktury.

-   Poplatky uvedené v příkazech fakturace pro mezinárodní zákazníci jsou odhad pouze pro účely. Bankami může mít různé náklady na převod sazby.

>[!VIDEO https://www.youtube.com/embed/jWG1lyJe3Mg]

## <a name="detailed-terms-and-descriptions-of-your-invoice"></a>Podrobné podmínky a popisy faktury
Následující části uvádějí důležité termíny, které se zobrazí na vaší faktuře a popisy ke každému termínu.

### <a name="account-information"></a>Informace o účtu

Části informace o účtu faktury je nahoře na první stránce a zobrazí se informace o váš profil a předplatné.

![Části informace o účtu faktury](./media/billing-understand-your-invoice/1.png)

| Označení | Popis |
| --- | --- |
| SP číslo. |Číslo objednávky volitelné nákupu přiřadila pro sledování |
| Faktura č. |Jedinečné, Microsoft generované faktury číslo používané pro účely sledování |
| Fakturační cyklus |Rozsah dat, které pokrývá tato faktury |
| Datum faktury |Datum, faktury byla vygenerována, obvykle denně po skončení cyklu fakturace |
| Způsob platby |Typ platby používaný u účtu (faktura nebo kreditní karta) |
| Příjemce faktury |Fakturační adresu, která je uvedena pro účet |
| Předplatné nabízejí (dále jen "průběžné platby") |Typ nabídky odběr, který byl zakoupen (průběžné platby, BizSpark Plus, Azure Pass, atd.). Další informace najdete v tématu [nabídka Azure typy](https://azure.microsoft.com/support/legal/offer-details/). |
| E-mail vlastníka účtu | E-mailová adresa účtu, pod kterou je účet Microsoft Azure registrovaný. <br /><br />Chcete-li změnit e-mailovou adresu, [jak změnit informace o profilu účtu Azure, jako je například kontaktní e-mailová adresa a telefonní číslo](billing-how-to-change-azure-account-profile.md). |

### <a name="understand-the-invoice-summary"></a>Vysvětlení souhrnu faktury
**Faktury Souhrn** části faktury je uveden seznam objemy celkový počet transakcí od vaší poslední fakturační období a aktuální poplatky za používání.

![Souhrn části faktury](./media/billing-understand-your-invoice/2.png)

Název odběru ("produkční úložiště") je název odběru pro tuto fakturu.

#### <a name="understand-the-previous-charges"></a>Pochopení předchozí poplatky
Předchozí zůstatek, platby a zůstatek části faktury shrnuje transakce od vaší poslední fakturačního období.

| Označení | Popis |
| --- | --- |
| Předchozí zůstatek |Celkový počet částka z důvodu z vaší poslední fakturační období. |
| Platby |Celkový počet platby a kredity u vaší poslední fakturační období |
| Nevyrovnaný zůstatek (z předchozího fakturačního cyklu) |Všechny kredity nebo je zůstatek ve vašem účtu od vaší poslední fakturační období |

#### <a name="understand-the-current-charges"></a>Vysvětlení aktuálních poplatků
V části aktuální poplatky faktury obsahuje podrobnosti o měsíční poplatky pro aktuální fakturační období.

| Označení | Popis |
| --- | --- |
| Poplatky za používání |Poplatky za používání jsou celkové měsíční náklady na předplatné pro aktuální fakturační období|
| Slevy |Použít pro vaše aktuální fakturační období slevy služby|
| Úpravy |Různé kredity (volné využití, kredity atd.) nebo nezaplacených poplatků použít pro vaše aktuální fakturačního období.<br/><br/>Například pokud máte Visual Studio Enterprise s nabídkou MSDN, uvidíte měsíční kredit. Pokud zrušíte předplatné, zobrazí se všechny měsíční poplatky za používání, které překročí měsíční kredit, kterou můžete získat nabídnout předplatné. Poplatků zpoplatněná na začátku vaše aktuální fakturační období až datum zrušení předplatného. |

#### <a name="sold-to-and-payment-instructions"></a>Prodávané a pokyny pro platbu

Následující tabulka popisuje prodané k a pokyny pro platbu zobrazený na stránce druhé části faktury.

| Označení |Popis |
| --- | --- |
| Kupující |Profil adresu, která je v účtu. <br/><br/>Pokud potřebujete změnit adresu, přečtěte si [jak změnit informace o profilu účtu Azure, jako je například kontaktní e-mailová adresa a telefonní číslo](billing-how-to-change-azure-account-profile.md).|
| Pokyny pro platbu |Pokyny, jak platit v závislosti na způsobu platby (například jako pomocí úvěrového karty nebo faktury). |

#### <a name="usage-charges"></a>Poplatky za využívání

V části poplatky za využití faktury zobrazí měření úrovně informace o vaše poplatky.

![Oddíl poplatky za využití](./media/billing-understand-your-invoice/3.png)

Následující tabulka popisuje záhlaví sloupců poplatky za využití zobrazený na faktury.

| Označení |Popis |
| --- | --- |
| Název |Identifikuje nejvyšší úrovně služby využití |
| Typ |Definuje typ služby Azure, který může mít vliv na rychlost |
| Prostředek |Identifikuje pro měření spotřebovávanou měrné jednotky |
| Oblast |Určuje umístění datového centra pro určité služby, které jsou za cenu na základě umístění datového centra |
| Spotřebované |Množství měření používá během fakturačního období |
| Zahrnuje |Množství monitorování, která je zahrnutá ve vaší aktuální fakturační období bez poplatku |
| Fakturovatelné |Ukazuje rozdíl mezi množství spotřebované a množství zahrnuty. Fakturuje se toto množství. Průběžné platby nabídky se žádné zahrnuté s nabídkou množství Tato celková hodnota je stejná jako objemu spotřebovaného |
| Sazba |Rychlost, kterou jste se vám účtovat na jednotku fakturovatelného času |
| Hodnota |Zobrazuje výsledek násobení sloupci Nadlimitní množství podle sloupce míry. Pokud množství spotřebované není překročit množství zahrnuty, je bezplatná v tomto sloupci. |
| Dílčí celkem |Součet všech vaše poplatky za předběžné daň pro toto fakturační období |
| Celkový součet |Součet všech poplatků po zdanění pro toto fakturační období |

## <a name="how-do-i-make-sure-that-the-charges-in-my-invoice-are-correct"></a>Jak se ujistěte se, zda jsou informace správné poplatky v fakturou?
Pokud je na vaší faktuře, která chcete podrobnosti na zpoplatněny, najdete v části [porozumět vaší faktuře pro Microsoft Azure.](billing-understand-your-bill.md)

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Obraťte se na podporu.
Pokud stále potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) získat rychle vyřešit problém.

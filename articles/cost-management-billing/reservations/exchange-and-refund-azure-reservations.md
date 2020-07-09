---
title: Samoobslužné výměny a vrácení peněz za rezervace Azure
description: Přečtěte si, jak postupovat při vrácení peněz za rezervace Azure nebo při jejich výměně.
author: yashesvi
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 07/01/2020
ms.author: banders
ms.openlocfilehash: 4a25bb13bfa5b2e4f13ddf437bcd9577f9602aed
ms.sourcegitcommit: 374d1533ea2f2d9d3f8b6e6a8e65c6a5cd4aea47
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2020
ms.locfileid: "85807685"
---
# <a name="self-service-exchanges-and-refunds-for-azure-reservations"></a>Samoobslužné výměny a vrácení peněz za rezervace Azure

Rezervace Azure vám dávají flexibilitu pro zvládání stále se vyvíjejících potřeb. Rezervaci můžete vyměnit za jinou rezervaci stejného typu. To znamená, že rezervace virtuálního počítače se dá vyměnit za nákup rezervace pro libovolnou oblast nebo velikost virtuálního počítače. Podobně se dá rezervace databáze SQL PaaS vyměnit za nákup rezervace databáze SQL PaaS libovolného typu a v libovolné oblasti. Rezervace se dají také refundovat, ale celkový objem zrušených rezervací za průběžné období 12 měsíců nesmí překročit 50 000 USD. Refundovat se nedá rezervovaná kapacita Azure Databricks, rezervace Řešení Azure VMware od CloudSimple, rezervace Azure Red Hat Open Shiftu, plány pro Red Hat a plány pro SUSE Linux.

Možnost samoobslužné výměny nebo zrušení není k dispozici pro zákazníky s předplatným US Government se smlouvou Enterprise. Podporují se i ostatní typy předplatného US Government, včetně předplatného s průběžnými platbami a CSP (Cloud Solution Provider).

Pokud chcete vyměnit stávající rezervaci nebo za ni vrátit peníze, musíte mít přístup vlastníka k příslušné objednávce rezervace. Můžete [přidat nebo změnit uživatelů, kteří mohou spravovat rezervaci](https://docs.microsoft.com/azure/cost-management-billing/reservations/manage-reserved-vm-instance#add-or-change-users-who-can-manage-a-reservation).

> [!NOTE]
> Microsoft v současnosti u refundací rezervací neúčtuje poplatky za předčasné ukončení. Možná budeme účtovat poplatky za refundace provedené v budoucnu. Momentálně ale nemáme pro aktivaci tohoto poplatku stanovený žádný pevný termín.

## <a name="how-to-exchange-or-refund-an-existing-reservation"></a>Postup při výměně nebo refundaci existující rezervace

K výměně rezervace můžete využít [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

1. Vyberte rezervace, za které chcete vrátit peníze, a vyberte **Vyměnit**.  
    [![Příklad znázorňující rezervace k vrácení](./media/exchange-and-refund-azure-reservations/exchange-refund-return.png)](./media/exchange-and-refund-azure-reservations/exchange-refund-return.png#lightbox)
1. Vyberte produkt virtuálního počítače, který chcete koupit, a zadejte množství. Ujistěte se, že celková hodnota nového nákupu je vyšší než celková částka k vrácení. [Před nákupem určete správnou velikost](../../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy).  
    [![Příklad znázorňující produkt virtuálního počítače, který se má koupit při výměně](./media/exchange-and-refund-azure-reservations/exchange-refund-select-purchase.png)](./media/exchange-and-refund-azure-reservations/exchange-refund-select-purchase.png#lightbox)
1. Zkontrolujte a dokončete transakci.  
    [![Příklad znázorňující produkt virtuálního počítače kupovaný při výměně během dokončování vrácení](./media/exchange-and-refund-azure-reservations/exchange-refund-confirm-exchange.png)](./media/exchange-and-refund-azure-reservations/exchange-refund-confirm-exchange.png#lightbox)

Pokud chcete vrátit peníze za rezervaci, přejděte do části **Podrobnosti o rezervaci** a vyberte **Refundace**.

## <a name="exchange-non-premium-storage-for-premium-storage"></a>Výměna služby jiného typu než Premium Storage za službu Premium Storage

Rezervaci zakoupenou pro virtuální počítač, jehož velikost nepodporuje službu Premium Storage, můžete vyměnit za odpovídající virtuální počítač, jehož velikost tuto službu podporuje. Můžete například vyměnit _F1_ za _F1s_. Provedete to tak, že přejdete do části Podrobnosti o rezervaci a vyberete **Vyměnit**. Při výměně nedojde k obnově období rezervované instance, ani k vytvoření nové transakce. 

## <a name="how-transactions-are-processed"></a>Jak se zpracovávají transakce

Nejprve Microsoft zruší stávající rezervaci a vrátí za ni přepočítanou částku. Pokud zvolíte výměnu, zpracuje se nový nákup. Microsoft provede vrácení peněz pomocí jednoho ze způsobů uvedených níže na základě typu vašeho účtu a způsobu platby:

### <a name="enterprise-agreement-customers"></a>Zákazníci se smlouvou Enterprise

Částka se přidá do peněžního závazku pro výměny a refundace, a to v případě, že původní nákup byl takto proveden. Pokud už není období peněžního závazku využívajícího zakoupenou rezervaci aktivní, kredit se přidá do vašeho aktuálního období peněžního závazku při smlouvě Enterprise. Kredit je platný po dobu 90 dnů od data refundace. Po uplynutí 90 dnů platnost nevyužitého kreditu končí.

Pokud byl původní nákup provedený jako nadlimitní, původní faktura, na kterou byla rezervace zakoupena, a všechny pozdější faktury se znovu otevřou a upraví. Microsoft vydá pro refundace dobropis.

### <a name="pay-as-you-go-invoice-payments-and-csp-program"></a>Průběžné platby za faktury a program CSP

Faktura z původního nákupu rezervace se zruší a vytvoří se nová faktura pro refundaci. V případě výměny se v nové faktuře zobrazuje vrácená položka i nový nákup. Vrácená částka se upraví podle nákupu. Pokud jste zvolili vrácení peněz za rezervaci, přepočítaná částka zůstává na straně Microsoftu a upraví se při dalším nákupu rezervace. Pokud jste si rezervaci koupili s tarify průběžných plateb a později jste přešli na CSP, můžete rezervaci vrátit a znovu zakoupit bez penalizace.

### <a name="pay-as-you-go-credit-card-customers"></a>Zákazníci s platební kartou a průběžnými platbami

Původní faktura se zruší a následně se vytvoří nová faktura. Částka se vrátí na platební kartu, kterou jste použili při původním nákupu. Pokud jste kartu změnili, [obraťte se na podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="cancel-exchange-and-refund-policies"></a>Zásady pro zrušení, výměny a vrácení peněz

Azure využívá při zrušení, výměně nebo refundaci následující zásady.

**Zásady pro výměny**

- Můžete vrátit více stávajících rezervací za účelem nákupu jedné nové rezervace stejného typu. Rezervace jednoho typu není možné měnit za jiný typ. Pro nákup rezervace SQL tak například nemůžete vrátit rezervaci virtuálního počítače. Současně s výměnou můžete měnit vlastnosti rezervace, jako je rodina, verze, SKU, oblast, množství a období.
- Výměny mohou provádět jen vlastníci rezervací. [Přečtěte si, jak přidávat nebo měnit uživatele, kteří mohou spravovat rezervaci](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).
- Výměna se zpracuje jako refundace a opětovný nákup (pro zrušení a nový nákup rezervace se vytváří odlišné transakce). Za rezervace, které jste dali na protiúčet, se vrátí přepočítaná částka za rezervaci. Za nový nákup se účtuje celá částka. Přepočítaná částka za rezervaci představuje zbývající hodnotu vrácené rezervace přepočítanou podle počtu dní.
- Rezervace můžete vyměnit nebo refundovat i tehdy, pokud smlouvě Enterprise použité při nákupu rezervace vypršela platnost, a obnovila se jako nová smlouva.
- Celkový závazek životnosti nové rezervace by měl být stejný nebo větší než zbývající závazek vracené rezervace. Příklad: Pro rezervaci na tři roky, která stojí 100 USD měsíčně a vymění se po 18. platbě, by celkový závazek životnosti nové rezervace měl být 1 800 USD nebo víc (placeno měsíčně nebo předem).
- Pro novou rezervaci zakoupenou v rámci výměny platí nové období, které začíná okamžikem výměny.
- Na výměny se nevztahují žádné pokuty ani roční limity.

**Zásady pro refundace**

- Za předčasné ukončení momentálně neúčtujeme žádný poplatek, ale v budoucnu se v případě zrušení bude za předčasné ukončení pravděpodobně účtovat poplatek 12 %.
- Celková výše zrušeného závazku za průběžné období 12 měsíců nesmí překročit 50 000 USD. Příklad: Pro rezervaci na tři roky, která stojí 100 USD měsíčně a vymění se po 18. platbě, se zrušený závazek rovná 1 800 USD. Váš nový dostupný limit po provedení této refundace bude 48 200 USD. Za 365 dnů od provedení této refundace se limit 48 200 USD zvýší o 1 800 USD a znovu budete mít k dispozici 50 000 USD. Z tohoto fondu se odčítají všechny ostatní rezervace a platí pro ně stejná doplňovací logika.
- Výše vrácených peněz vychází z vaší kupní ceny nebo aktuální ceny rezervace podle toho, která hodnota je nižší.
- Refundace můžou provádět pouze vlastníci objednávek rezervací. [Přečtěte si, jak přidávat nebo měnit uživatele, kteří mohou spravovat rezervaci](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Další kroky

- Informace o tom, jak spravovat rezervace, najdete v tématu [Správa rezervací Azure](manage-reserved-vm-instance.md).
- Další informace o rezervacích Azure najdete v následujících článcích:
    - [Co jsou rezervace Azure?](save-compute-costs-reservations.md)
    - [Správa rezervací v Azure](manage-reserved-vm-instance.md)
    - [Vysvětlení způsobu uplatnění slevy za rezervaci](../manage/understand-vm-reservation-charges.md)
    - [Vysvětlení využití rezervací u předplatného s průběžnými platbami](understand-reserved-instance-usage.md)
    - [Vysvětlení využití rezervací u smlouvy Enterprise](understand-reserved-instance-usage-ea.md)
    - [Náklady na software pro Windows nezahrnuté v rezervacích](reserved-instance-windows-software-costs.md)
    - [Rezervace Azure v programu CSP](/partner-center/azure-reservations)

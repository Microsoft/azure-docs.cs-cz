---
title: Samoobslužné výměny a vrácení peněz za rezervace Azure
description: Přečtěte si, jak postupovat při vrácení peněz za rezervace Azure nebo při jejich výměně.
author: yashesvi
manager: yashesvi
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/21/2020
ms.author: banders
ms.openlocfilehash: ea545919436201524a1c77b27e9b187f3b1c3b64
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2020
ms.locfileid: "76314041"
---
# <a name="self-service-exchanges-and-refunds-for-azure-reservations"></a>Samoobslužné výměny a vrácení peněz za rezervace Azure

Rezervace Azure vám dávají flexibilitu pro zvládání stále se vyvíjejících potřeb. Rezervaci si můžete vyměnit za jinou rezervaci stejného typu. Za rezervaci si také můžete nechat vrátit peníze, pokud už ji nepotřebujete, a to až do 50 000 USD za rok. Maximální limit refundace se vztahuje na všechny rezervace v oboru vaší smlouvy s Microsoftem.

Možnost samoobslužné výměny nebo zrušení není k dispozici pro zákazníky s předplatným US Government se smlouvou Enterprise. Podporují se i ostatní typy předplatného US Government, včetně předplatného s průběžnými platbami a předplatného CSP.

Pokud chcete vyměnit stávající rezervaci nebo za ni vrátit peníze, musíte mít přístup vlastníka k příslušné objednávce rezervace.

## <a name="exchange-an-existing-reserved-instance"></a>Výměna stávající rezervované instance

Na webu [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) si můžete vyměnit rezervaci ve třech rychlých krocích.

1. Vyberte rezervace, za které chcete vrátit peníze, a klikněte na **Vyměnit**.  
    ![Příklad znázorňující rezervace k vrácení](./media/exchange-and-refund-azure-reservations/exchange-refund-return.png)
2. Vyberte produkt virtuálního počítače, který chcete koupit, a zadejte množství. Ujistěte se, že celková hodnota nového nákupu je vyšší než celková částka k vrácení. [Před nákupem určete správnou velikost](../../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy).  
    ![Příklad znázorňující produkt virtuálního počítače, který se má koupit při výměně](./media/exchange-and-refund-azure-reservations/exchange-refund-select-purchase.png)
3. Zkontrolujte a dokončete transakci.  
    ![Příklad znázorňující produkt virtuálního počítače kupovaný při výměně během dokončování vrácení](./media/exchange-and-refund-azure-reservations/exchange-refund-confirm-exchange.png)

Pokud chcete vrátit peníze za rezervaci, přejděte do části **Podrobnosti o rezervaci** a klikněte na**Refundace**.

## <a name="how-transactions-are-processed"></a>Jak se zpracovávají transakce

Nejprve Microsoft zruší stávající rezervaci a vrátí za ni přepočítanou částku. Pokud zvolíte výměnu, zpracuje se nový nákup. Microsoft provede vrácení peněz pomocí jednoho ze způsobů uvedených níže na základě typu vašeho účtu a způsobu platby:

### <a name="enterprise-agreement-customers"></a>Zákazníci se smlouvou Enterprise

Částka se přidá do peněžního závazku pro výměny a refundace, a to v případě, že původní nákup byl takto proveden. Nadlimitní faktury vytvořené po původním nákupu se znovu otevřou a přepočítají, aby se zajistilo, že se peněžní závazek použije. Pokud už není období peněžního závazku využívajícího zakoupenou rezervaci aktivní, kredit se přidá do vašeho aktuálního období peněžního závazku při smlouvě Enterprise. Kredit je platný po dobu 90 dnů od data refundace. Po uplynutí 90 dnů platnost nevyužitého kreditu končí.

Pokud byl původní nákup nadlimitní, Microsoft vydá prodejní dobropis.

### <a name="pay-as-you-go-invoice-payments-and-csp-program"></a>Průběžné platby za faktury a program CSP

Faktura z původního nákupu rezervace se zruší a vytvoří se nová faktura pro refundaci. V případě výměny se v nové faktuře zobrazuje vrácená položka i nový nákup. Vrácená částka se upraví podle nákupu. Pokud jste zvolili vrácení peněz za rezervaci, přepočítaná částka zůstává na straně Microsoftu a upraví se při dalším nákupu rezervace.

### <a name="pay-as-you-go-credit-card-customers"></a>Zákazníci s platební kartou a průběžnými platbami

Původní faktura se zruší a následně se vytvoří nová faktura. Částka se vrátí na platební kartu, kterou jste použili při původním nákupu. Pokud jste kartu změnili, [obraťte se na podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="cancel-exchange-and-refund-policies"></a>Zásady pro zrušení, výměny a vrácení peněz

Azure využívá při zrušení, výměně nebo refundaci následující zásady.

**Zásady pro výměny**

- Můžete vrátit více stávajících rezervací za účelem nákupu nové rezervace stejného typu. Rezervace jednoho typu není možné měnit za jiný typ. Pro nákup rezervace SQL tak například nemůžete vrátit rezervaci virtuálního počítače.
- Výměny mohou provádět jen vlastníci rezervací. [Přečtěte si, jak přidávat nebo měnit uživatele, kteří mohou spravovat rezervaci](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).
- Výměna se zpracuje jako refundace a opětovný nákup (pro zrušení a nový nákup se vytváří odlišné transakce). Za rezervace, které jste dali na protiúčet, se vrátí přepočítaná částka za rezervaci. Za nový nákup se účtuje celá částka. Přepočítaná částka za rezervaci představuje zbývající hodnotu vrácené rezervace přepočítanou podle počtu dní.
- Rezervace můžete vyměnit nebo refundovat i tehdy, pokud smlouvě Enterprise použité při nákupu rezervace vypršela platnost, a obnovila se jako nová smlouva.
- Současně s výměnou můžete měnit jakékoli vlastnosti rezervace, jako je velikost, oblast, množství a období.
- Celková hodnota nového nákupu by měla být stejná jako vrácená částka, nebo vyšší.
- Pro novou rezervaci zakoupenou v rámci výměny platí nové období, které začíná okamžikem výměny.
- Na výměny se nevztahují žádné pokuty ani roční limity.

**Zásady pro refundace**
- Pokud svou rezervaci zrušíte, může se vám účtovat poplatek za předčasné ukončení ve výši 12 %.
- Výše vrácených peněz představuje zbývající poměrný zůstatek minus poplatek za předčasné ukončení ve výši 12 %. Zrušení provede tak, že na webu Azure Portal přejdete na danou rezervaci a vyberete možnost **Refundace**.
- Celková vrácená částka nesmí překročit 50 000 UDS za rok.
- Výše vrácených peněz vychází z vaší kupní ceny nebo aktuální ceny rezervace podle toho, která hodnota je nižší.
- Refundace mohou provádět jen vlastníci rezervací. [Přečtěte si, jak přidávat nebo měnit uživatele, kteří mohou spravovat rezervaci](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).
- Microsoft si vyhrazuje právo požadovat za jakékoli vrácení rezervace 12% poplatek. Ten se sice v současnosti ještě neúčtuje, bude tomu tak ale v budoucnu.

## <a name="exchange-non-premium-storage-for-premium-storage"></a>Výměna služby jiného typu než Premium Storage za službu Premium Storage

Rezervaci zakoupenou pro virtuální počítač, jehož velikost nepodporuje službu Premium Storage, můžete vyměnit za odpovídající virtuální počítač, jehož velikost tuto službu podporuje. Můžete například vyměnit _F1_ za _F1s_. Provedete to tak, že přejdete do části Podrobnosti o rezervaci a kliknete na **Vyměnit**. Při výměně nedojde k obnově období rezervované instance, ani k vytvoření nové transakce.

## <a name="need-help-contact-us"></a>Potřebujete pomoct? Kontaktujte nás.

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
    - [Rezervace Azure v programu Partner Center Cloud Solution Provider (CSP)](/partner-center/azure-reservations)

---
title: Samoobslužné výměny a refundace pro Azure Reservations
description: Přečtěte si, jak můžete Azure Reservations Exchange nebo refundace.
author: yashesvi
manager: yashesvi
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/05/2019
ms.author: banders
ms.openlocfilehash: 828bc3784a118a81adc4391b1bf222c00ee2025a
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2019
ms.locfileid: "69543018"
---
# <a name="self-service-exchanges-and-refunds-for-azure-reservations"></a>Samoobslužné výměny a refundace pro Azure Reservations

Azure Reservations poskytují flexibilitu, která vám umožní splnit vaše požadavky na vývoj. Rezervaci si můžete vyměnit za jinou rezervaci stejného typu. Za rezervaci si také můžete nechat vrátit peníze, pokud už ji nepotřebujete, a to až do 50 000 USD za rok.

Možnost samoobslužné výměny nebo zrušení není k dispozici pro zákazníky s předplatným US Government se smlouvou Enterprise. Podporovány jsou i další typy předplatného pro státní správu USA včetně průběžných plateb a CSP.

Abyste mohli vyměnit nebo vrátit existující rezervaci, musíte mít přístup vlastníka k objednávce rezervace.

## <a name="exchange-an-existing-reserved-instance"></a>Výměna stávající rezervované instance

Rezervaci můžete vyměnit pomocí tří rychlých kroků v [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

1. Vyberte rezervace, u kterých chcete refundaci, a klikněte na tlačítko **Exchange**.  
    ![Příklad obrázku znázorňujícího vracené rezervace](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-return.png)
2. Vyberte produkt virtuálního počítače, který chcete koupit, a zadejte množství. Ujistěte se, že je nový nákup celkem větší než celková hodnota. [Před nákupem určíte správnou velikost](../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy).  
    ![Příklad obrázku znázorňujícího produkt pro virtuální počítače, který se má koupit pomocí Exchange](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-select-purchase.png)
3. Zkontrolujte a dokončete transakci.  
    ![Příklad obrázku znázorňujícího produkt pro virtuální počítače, který se má koupit v systému Exchange a dokončení návratu](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-confirm-exchange.png)

Pokud chcete rezervaci vrátit, přejděte na **podrobnosti rezervace** a kliknětena refundace.

## <a name="how-transactions-are-processed"></a>Jak se zpracovávají transakce

Nejprve společnost Microsoft zruší stávající rezervaci a refundace pro tuto rezervaci za hodnocenou částku. Pokud je v systému Exchange, bude zpracován nový nákup. Microsoft zpracovává náhrady pomocí jedné z následujících metod v závislosti na typu účtu a způsobu platby:

### <a name="enterprise-agreement-customers"></a>Zákazníci se smlouvou Enterprise

Peníze se přidávají do peněžního závazku pro výměny a refundace, pokud byl původní nákup vytvořen pomocí jednoho. Jakékoli faktury za nadlimitní využití od původního nákupu se znovu otevřou a účtují se, aby se zajistilo použití peněžního závazku. Pokud se podmínky peněžního závazku, které používají rezervaci, už neaktivní, přidají se k vašemu aktuálnímu období peněžního závazku smlouvy Enterprise kredit.

Pokud byl původní nákup proveden jako nadlimitní využití, společnost Microsoft vydá dobropis.

### <a name="pay-as-you-go-invoice-payments-and-csp-program"></a>Platby faktury s průběžnými platbami a program CSP

Původní nákupní faktura pro rezervaci se zruší a pak se pro refundaci vytvoří nová faktura. Pro výměny se na nové faktuře zobrazí refundace a nové nákupy. Částka náhrady se upraví oproti nákupu. Pokud jste rezervaci překoupili jenom, zůstane poměrná částka u Microsoftu a ta se upraví oproti budoucímu nákupu rezervací.

### <a name="pay-as-you-go-credit-card-customers"></a>Zákazníci s průběžnými platbami podle aktuálního využití

Původní faktura se zruší a vytvoří se nová faktura. Peníze se vracejí na platební kartu, která se použila pro původní nákup. Pokud jste kartu změnili, obraťte se na [podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="cancel-exchange-and-refund-policies"></a>Zásady zrušení, výměny a refundace

Azure má následující zásady pro zrušení, výměny a refundace.

**Zásady Exchange**

- Můžete vrátit více existujících rezervací pro zakoupení nové rezervace stejného typu. Nemůžete vyměnit rezervace jednoho typu pro jiný. Například nemůžete vrátit rezervaci virtuálního počítače a koupit rezervaci SQL.
- Výměnu můžou zpracovat jenom vlastníci rezervace. [Naučte se, jak přidat nebo změnit uživatele, kteří můžou spravovat rezervaci](billing-manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).
- Výměna je zpracována jako refundace a nového nákupu – pro zrušení a nové nákupy se vytvoří různé transakce. Částka průběžné rezervace je pro rezervace, které jste provedete, refinancovány. Za nový nákup se vám budeme účtovat. Částka poměrné rezervace je denní poměrná zůstatková hodnota vracené rezervace.
- Rezervace můžete vyměňovat nebo vrátit, i když platnost smlouvy Enterprise, která se používá k nákupu rezervace, vypršela a byla obnovena jako nová smlouva.
- Můžete změnit jakoukoli vlastnost rezervace, jako je velikost, oblast, množství a termín, pomocí výměny.
- Hodnota nového nákupu by měla být větší nebo rovna vrácené hodnotě.
- Nová rezervace zakoupená jako součást systému Exchange má nový termín od okamžiku výměny.
- Pro výměny není k dispozici žádná pokuta ani roční omezení.

**Zásady refundace**
- Pokud rezervaci zrušíte, může se jednat o 12% poplatek za předčasné ukončení.
- Refundace, kterou obdržíte za zrušení, je zbývající poměr pro nominální částku minus 12% poplatek za předčasné ukončení. Pokud ho chcete zrušit, pokračujte na rezervaci v Azure Portal a vyberte **refundace**.
- Celková částka náhrady nesmí překročit $50 000 USD v průběhu 12 měsíců.
- Náhrady se počítají na základě nejnižší ceny nákupní ceny nebo aktuální ceny za rezervaci.
- Refundaci mohou zpracovat pouze vlastníci rezervace. [Naučte se, jak přidat nebo změnit uživatele, kteří můžou spravovat rezervaci](billing-manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).
- Společnost Microsoft si vyhrazuje právo účtovat pokutu 12% pro jakékoli vrácení. Pokuta se momentálně neúčtuje, ale bude se účtovat později.

## <a name="exchange-non-premium-storage-for-premium-storage"></a>Výměna nepremium Storage pro Storage úrovně Premium

Můžete si vyměnit rezervaci zakoupenou pro velikost virtuálního počítače, která nepodporuje Premium Storage na odpovídající velikost virtuálního počítače. Například _F1_ pro _f1s úrovně_. Pokud chcete Exchange udělat, přejděte na podrobnosti o rezervacích a klikněte na **Exchange**. Výměna neresetuje termín rezervované instance nebo vytvoří novou transakci.

## <a name="need-help-contact-us"></a>Potřebujete pomoct? Kontaktujte nás.

Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Další kroky

- Informace o tom, jak spravovat rezervaci, najdete v tématu [správa Azure reservations](billing-manage-reserved-vm-instance.md).
- Další informace o Azure Reservations najdete v následujících článcích:
    - [Co jsou Azure Reservations?](billing-save-compute-costs-reservations.md)
    - [Správa rezervací v Azure](billing-manage-reserved-vm-instance.md)
    - [Vysvětlení způsobu použití slevy rezervace](billing-understand-vm-reservation-charges.md)
    - [Vysvětlení využití rezervací pro předplatné s průběžnými platbami](billing-understand-reserved-instance-usage.md)
    - [Vysvětlení použití rezervací pro podnikovou registraci](billing-understand-reserved-instance-usage-ea.md)
    - [Náklady na software systému Windows, které nejsou součástí rezervací](billing-reserved-instance-windows-software-costs.md)
    - [Azure Reservations v programu partner Center pro Cloud Solution Provider (CSP)](/partner-center/azure-reservations)

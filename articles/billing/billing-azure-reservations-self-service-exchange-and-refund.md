---
title: Samoobslužná služba výměny a vrácení peněz rezervací Azure | Dokumentace Microsoftu
description: Zjistěte, jak mohou vyměňovat nebo náhrady Azure rezervace.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/5/2019
ms.author: banders
ms.openlocfilehash: aa1a218fbf0bc7eacac65b50e4ee1f86791e2b3b
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2019
ms.locfileid: "59281977"
---
# <a name="self-service-exchanges-and-refunds-for-azure-reservations"></a>Samoobslužná služba výměny a finanční prostředky Azure rezervací

Rezervace Azure poskytují flexibilitu, která vám pomohou splnit potřeby vyvíjející. Můžete si mohou vyměňovat rezervace pro jiné rezervace stejného typu. Rezervace, až 50 000 USD za rok, můžete také vrátit, pokud ho už nepotřebují.

Schopnosti samoobslužné služby exchange a zrušení není k dispozici pro US Government zákazníci se smlouvou Enterprise. Jsou podporovány dalších typů předplatného pro státní správu USA, včetně průběžných plateb a CSP.

## <a name="exchange-an-existing-reserved-instance"></a>Výměna existující rezervované instance

Vyměňujete vaší rezervace se ve třech rychlých krocích [webu Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

1. Vyberte rezervace, které chcete vrátit a klikněte na tlačítko **Exchange**.  
    ![Obrázek příkladu rezervace vrátit](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-return.png)
2. Vyberte produkt virtuálního počítače, který chcete zakoupit a zadejte množství. Ujistěte se, že je nová nákupní celkem více než návratový celkem. [Určení správné velikosti před nákupem](../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy).  
    ![Obrázek příkladu virtuálních počítačů produktu nákupu se systému exchange](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-select-purchase.png)
3. Zkontrolujte a dokončete transakci.  
    ![Obrázek příkladu virtuálních počítačů produktu nákupu se serveru exchange, dokončení vrácení](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-confirm-exchange.png)

Náhrada rezervaci, přejděte na **podrobnosti o rezervaci** a klikněte na tlačítko **refundaci**.

## <a name="how-return-and-exchange-transactions-are-processed"></a>Jakým způsobem se vrátit a zpracování transakcí exchange

Nejdřív Microsoft zruší stávající rezervaci a náhrad poměrně velikost pro tuto rezervaci. Pokud dojde systému exchange, zpracování nového nákupu. Společnost Microsoft zpracovává finanční prostředky pomocí jedné z následujících metod, v závislosti na typu vašeho účtu a platby:

### <a name="enterprise-agreement-customers"></a>Zákazníci se smlouvou Enterprise

Peníze se přidá do peněžních závazků pro výměnu a náhrad Pokud původnímu nákupu bylo provedeno pomocí jedné. Žádné Nadlimitní faktury od původního nákupu se znovu otevřít a rerated zajistit peněžního závazku se používá. Pokud peněžní závazek termín pomocí zakoupenou rezervaci již není aktivní, kredit se přidá do aktuálního období peněžní závazky smlouvy enterprise.

Pokud původnímu nákupu bylo provedeno jako Nadlimitní využití, společnost Microsoft vydává dobropis.

### <a name="pay-as-you-go-invoice-payment-customers-and-cloud-solution-provider-program"></a>Zákazníci s průběžnými platbami faktury platby a programu Cloud solution provider

Původní fakturu nákupu rezervace se zruší a vytvoří novou fakturu náhradu. Pro výměny informací zobrazuje nový faktury náhrady a nový nákup. Částka se upraví na nákup. Pokud pouze refundovat rezervaci, zůstane poměrné částky se společností Microsoft a dojde k přenastavení proti nákup budoucí rezervace.

### <a name="pay-as-you-go-credit-card-customers"></a>Zákazníci s průběžnými platbami platební karty

Původní faktury se zruší a vytvoří novou fakturu. Z vaší platební karty, která byla použita k původnímu nákupu bude vrácena peníze. Pokud jste změnili vaší karty [obraťte se na podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="exchange-policies"></a>Zásady Exchange

- Může vrátit více existující rezervace zakoupit novou rezervaci stejného typu. Nelze vyměňovat rezervace jednoho typu. pro další. Například nelze vrátit rezervaci virtuálních počítačů, která si koupit rezervaci SQL.
- Výměna může zpracovat pouze vlastníci rezervace. [Zjistěte, jak přidat nebo změnit uživatelům, kteří můžou spravovat rezervaci](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance#add-or-change-users-who-can-manage-a-reservation).
- Výměna zpracovávány jako repo a reklamace – jiné transakce jsou vytvořeny pro zrušení a nový nákup. Velikost poměrná část rezervace bude vrácena rezervací této ojetých vám. Za plně nového nákupu. Poměrná část rezervace je poměrná část plyne ze zbytkových hodnotu denní rezervace se vrací.
- Můžete vyměnit nebo náhrady rezervace to i v případě smlouvy enterprise použít k nákupu rezervace vypršela platnost a byl obnoven jako nové smlouvy.
- Můžete změnit jakékoli vlastnosti rezervace, jako je například velikost, oblast, množství a délku smlouvy s výměnou.
- Nové nákupní celkem má roven nebo větší než velikost vrácené.
- Nová rezervace zakoupit jako součást systému exchange má nový termín od doby, exchange.
- Neexistuje žádné snížení nebo roční limity pro výměnu.

## <a name="refund-policies"></a>Zásady refundace

- Vaše celkové refundaci množství může mít maximálně 50 000 USD v okně posledních 12 měsíců.
- Pouze vlastníci rezervace může zpracovat refundaci. [Zjistěte, jak přidat nebo změnit uživatelům, kteří můžou spravovat rezervaci](billing-manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).
- Společnost Microsoft si vyhrazuje právo účtovat penalizace 12 % pro všechny vrátí, i když není v sankce aktuálně neúčtují žádné poplatky.

## <a name="exchange-a-non-premium-storage-vm-reservation-for-a-premium-storage-reservation"></a>Výměna jiné úrovně než premium storage rezervaci virtuálních počítačů pro rezervaci úložiště úrovně premium

Můžete si mohou vyměňovat rezervace zakoupit pro velikost virtuálního počítače, který nepodporuje odpovídající velikost virtuálního počítače, který nemá službu storage úrovně premium. Například _F1_ pro _F1s_. Chcete-li serveru exchange, přejděte na podrobnosti o rezervaci a klikněte na tlačítko **Exchange**. Exchange není resetování období rezervované instance nebo vytvořit novou transakci.

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Další postup

- Další informace o správě rezervaci, najdete v článku [spravovat Azure rezervace](billing-manage-reserved-vm-instance.md).
- Další informace o rezervacích Azure, najdete v následujících článcích:
    - [Co jsou rezervace Azure?](billing-save-compute-costs-reservations.md)
    - [Správa rezervací v Azure](billing-manage-reserved-vm-instance.md)
    - [Vysvětlení, jak se sleva za rezervaci použije](billing-understand-vm-reservation-charges.md)
    - [Vysvětlení využití rezervace pro vaše předplatné s průběžnými platbami](billing-understand-reserved-instance-usage.md)
    - [Vysvětlení využití rezervaci u prováděcí smlouvy Enterprise](billing-understand-reserved-instance-usage-ea.md)
    - [Náklady na software Windows, které nejsou součástí rezervace](billing-reserved-instance-windows-software-costs.md)
    - [Rezervace Azure v programu Partnerské centrum Cloud Solution Provider (CSP)](/partner-center/azure-reservations)

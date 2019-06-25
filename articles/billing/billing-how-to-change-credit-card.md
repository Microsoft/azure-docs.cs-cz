---
title: Změna platební karty pro Azure | Dokumentace Microsoftu
description: Popisuje postup při změně platební karty použité k úhradě předplatného Azure
services: ''
documentationcenter: ''
author: genlin
manager: jureid
editor: ''
tags: billing
ms.assetid: 15252ced-1841-4a66-ae79-2e58af1d3370
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 3/13/2019
ms.author: banders
ms.openlocfilehash: b910cb3061b1451ea80b9843e2aa4047a784548f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60918615"
---
# <a name="add-update-or-remove-a-credit-or-debit-card-for-azure"></a>Přidání, aktualizace nebo odebrání kreditní nebo debetní kartou pro Azure

Na webu Azure Portal můžete přidat novou platební kartu, aktualizovat stávající platební kartu nebo odstranit platební karty, které nepoužíváte. Musí být [správce účtu](billing-subscription-transfer.md#whoisaa) k provedení těchto změn.

<!-- If your Angola, Belize, Algeria, Vietnam, or Virgin Islands. -->

**Chcete přepnout na platbu na fakturu?** Zobrazit [platbu na fakturu za odběr služeb Azure](billing-how-to-pay-by-invoice.md).

<a id="addcard"></a>

## <a name="add-a-new-credit-or-debit-card-to-an-azure-subscription"></a>Přidat novou kreditní nebo debetní kartu k předplatnému Azure

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) jako správce účtu.
1. Hledat na **Cost Management a fakturace**.

    ![Snímek obrazovky zobrazující vyhledávání](./media/billing-how-to-change-credit-card/search.png)

1. Vyberte předplatné, které chcete přidat kreditní nebo debetní kartu k.
1. Vyberte **Způsoby platby**.

    ![Snímek obrazovky s možností metody spravovat vybrali.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. V levém horním rohu vyberte + a přidejte kartu. Na pravé straně se zobrazí formulář platební karty.
1. Zadejte podrobnosti o kreditní nebo debetní kartě.

    ![Snímek obrazovky zobrazující přidání nové karty.](./media/billing-how-to-change-credit-card/sub-add-new-x.png)

1. Chcete-li tato karta vaše aktivní způsob platby, zaškrtněte políčko vedle položky **nastavit aktivní způsob platby metodu** nad formuláře. Tato karta se stane aktivním platebním nástrojem pro všechna předplatná využívající stejnou kartu jako vybrané předplatné.

1. Vyberte **Další**.

Pokud dojde k chybě po přidat platební kartu, najdete v článku [odmítl platební karty v Azure, zaregistrujte](billing-credit-card-fails-during-azure-sign-up.md).

## <a name="update-existing-credit-or-debit-card"></a>Aktualizovat existující kreditní nebo debetní kartu

Pokud vaší platební karty získá obnoveno a počet zůstala stejná, aktualizujte existující platební karty podrobnosti jako datum vypršení platnosti. Pokud změny číslo platební karty protože karty dojde ke ztrátě nebo vám ho někdo ukradne, nebo vypršela jeho platnost, postupujte podle pokynů [přidat platební kartu jako způsob platby](#addcard) oddílu. Není nutné aktualizovat CVV.

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) jako správce účtu.
1. Hledat na **Cost Management a fakturace**.

    ![Snímek obrazovky zobrazující vyhledávání](./media/billing-how-to-change-credit-card/search.png)

1. Vyberte **Způsoby platby**.

    ![Snímek obrazovky s možností metody spravovat vybrali.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Klikněte na kreditní nebo debetní kartu, kterou chcete upravit. Na pravé straně se zobrazí formulář platební karty.

    ![Snímek obrazovky zobrazující kreditní nebo debetní kartu.](./media/billing-how-to-change-credit-card/edit-card-x.png)

1. Aktualizujte podrobnosti o kreditní nebo debetní kartě.
1. Vyberte **Uložit**.

## <a name="use-a-different-credit-card-for-the-azure-subscription"></a>Použití jiné platební karty pro předplatné Azure

Pokud máte více než jednoho z vašich předplatných stejné aktivní způsob platby, změníte aktivní způsob platby na některý z těchto předplatných rovněž aktualizuje aktivní způsob platby na ostatní.

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) jako správce účtu.
1. Hledat na **Cost Management a fakturace**.

    ![Snímek obrazovky zobrazující vyhledávání](./media/billing-how-to-change-credit-card/search.png)

1. Vyberte předplatné, které chcete přidat kreditní nebo debetní kartu k.
1. Vyberte **Způsoby platby**.

    ![Snímek obrazovky s možností metody spravovat vybrali.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Zaškrtněte políčko vedle karty, které byste rádi Ujistěte se, aktivní způsob platby.
1. Klikněte na tlačítko **nastavit aktivní**.
    ![Snímek obrazovky zobrazující kreditní nebo debetní kartou vybrali a nastavit aktivní.](./media/billing-how-to-change-credit-card/sub-change-active-x.png)

## <a name="remove-a-credit-or-debit-card-from-the-account"></a>Odebrat kreditní nebo debetní kartu z účtu

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) jako správce účtu.
1. Vyberte **Správa nákladů a fakturace** na levé straně stránky.

    ![Snímek obrazovky zobrazující vyhledávání](./media/billing-how-to-change-credit-card/search.png)

1. V části **fakturace**vyberte **způsoby platby**.

    ![Snímek obrazovky s možností metody spravovat vybrali.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Zaškrtněte políčko vedle karty, kterou chcete odebrat.
1. Klikněte na tlačítko **odstranit**.

Pokud vaší platební karty je aktivní způsob platby pro některý z vašich předplatných Microsoft, nelze odebrat z účtu Azure. Změnit aktivní způsob platby všech předplatných přidružených k této kreditní nebo debetní karta a zkuste to znovu
<!-- # Add, update, or remove a credit or debit card for Azure

In the Account Center, you can add a new credit card, update an existing credit card, or delete a credit card that you don't use. You must be an [Account Administrator](billing-subscription-transfer.md#whoisaa) to make these changes.

**Want to switch to pay by invoice?** See [Pay for Azure subscriptions by invoice](billing-how-to-pay-by-invoice.md).
 
<a id="addcard"></a>

## Add a new credit or debit card

1. Sign in to the [Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select a subscription.
1. On the right side of the page, select **Manage payment methods**.

    ![Screenshot that shows Manage payment methods option selected.](./media/billing-how-to-change-credit-card/changesub_new.png)
1. Select “+” to add a card.

    ![Screenshot that shows the edit option next to the payment method.](./media/billing-how-to-change-credit-card/editcard_new.png)
1. Enter credit or debit card details.
1. Select **Save**. 

If you get an error after you add the credit card, see [Credit card declined at Azure sign-up](billing-credit-card-fails-during-azure-sign-up.md).

## Update existing credit or debit card

If your credit card gets renewed and the number remains the same, update the existing credit card details like the expiration date. If your credit card number changes because the card is lost, stolen, or expired, follow the steps in the [Add a credit card as a payment method](#addcard) section. You don't need to update the CVV.

1. Sign in to the [Azure Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select the subscription that's linked to the card.
1. Select **Manage payment methods**.
1. Select **Edit** next to the card you want to update.
1. Update the credit or debit card details.
1. Select **Save**.

## Use a different credit card for the Azure subscription

1. Sign in to the [Azure Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select the subscription that's linked to the card.
1. On the right side of the page, select **Manage payment methods**.
1. Click **Use Instead** next to the card that you want to use. This also updates any other subscriptions currently associated with this card. 

## Remove a credit or debit card from the account

1. Sign in to the [Azure Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select the subscription that's linked to the card.
3. On the right side of the page, select **Manage payment methods**.
4. Click **Delete** for the credit card that you want to delete.

If your credit card is associated with other active Microsoft subscriptions, you can't remove it from your Azure account. Remove the credit card from all active subscriptions that you have with Microsoft and try again. -->
## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="my-subscription-is-disabled-why-cant-i-remove-my-credit-card-now"></a>Moje předplatné je zakázané. Proč nelze odebrat platební karty nyní?

Poté, co vaše předplatné je zakázané nebo bylo zrušeno, čekáme 90 dní, než vaše předplatné se odstraňuje natrvalo. V případě, že chcete předplatné znovu aktivovat uchováváme během doby uchování vaším způsobem platby na soubor. Potom se trvale odstraní odběr.

Pokud je potřeba odebrat kreditní nebo debetní karta předtím, než skončí se uchovávají 90 dnů, [znovu aktivovat vaše předplatné](billing-subscription-become-disable.md). Pokud nelze aktivovat, [požádejte podporu Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="why-do-i-keep-getting-your-login-session-has-expired-please-click-here-to-log-back-in"></a>Proč zobrazuje "vypršela platnost vaší relace přihlášení. Klikněte prosím sem a znovu přihlásit,"?

Pokud i v případě, že jste už přihlášení bude dále zobrazovat tato chybová zpráva a zpět v, zkuste to znovu s Chcete relaci privátního procházení.

### <a name="how-do-i-use-a-different-card-for-each-subscription-i-have"></a>Jak použít jinou kartu pro každé předplatné, které budu mít?

Bohužel Pokud předplatné už používají stejné karty, není možné oddělit je, aby používaly různé karty. Ale když se zaregistrujete k novému předplatnému, můžete použít nový způsob platby pro dané předplatné.

### <a name="how-do-i-make-payments"></a>Jak mohu učinit platby?

Pokud jste nastavili kreditní nebo debetní kartu jako způsob platby, účtujeme automaticky vaší karty po každého fakturačního období. Není nutné nic dělat.

Pokud jste [platíte fakturami](billing-how-to-pay-by-invoice.md), odeslat vaše platba do umístění uvedených v dolní části faktury.

### <a name="how-do-i-make-a-one-time-payment"></a>Jak mohu učinit jednorázovou platbu?

Bohužel Azure v současné době nepodporuje platby jednorázové pro kreditní nebo debetní karty. 

### <a name="how-do-i-change-the-tax-id"></a>Jak změním ID daň?

Chcete-li přidat nebo aktualizovat daňové identifikační číslo, navštivte [ **profilu** v centru účtů Azure](https://account.azure.com/Profile)a pak vyberte **daňové záznam**. Toto DIČ slouží k výpočtu slevy na dani a zobrazí se na vaší faktuře.

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

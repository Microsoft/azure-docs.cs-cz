---
title: Změna platební karty pro Azure
description: Popisuje, jak změnit platební kartu, která se používá k placení předplatného Azure.
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: banders
ms.openlocfilehash: 7719ae83525883a6d3f014dbb99877b7319f2ccd
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/22/2019
ms.locfileid: "68383658"
---
# <a name="add-update-or-remove-a-credit-card-for-azure"></a>Přidání, aktualizace nebo odebrání platební karty pro Azure

V Azure Portal můžete přidat novou platební kartu, aktualizovat existující platební kartu nebo odstranit platební kartu, kterou nepoužíváte. Chcete-li provést tyto změny, musíte být [správcem účtu](billing-subscription-transfer.md#whoisaa) .

Pokud máte smlouvu o [zákaznících Microsoftu](#check-access-to-a-microsoft-customer-agreement), vaše způsoby platby se přidruží k profilům fakturace. Naučte se, jak [změnit výchozí způsob platby pro fakturační profil](#change-payment-method-for-a-billing-profile). Způsob platby může aktualizovat jenom uživatel, který se zaregistroval do Azure.

**Chcete přejít na platbu fakturou (kontrolu/přenos přes přenos)?** Podívejte se [na platby za předplatné Azure podle faktury](billing-how-to-pay-by-invoice.md).

<a id="addcard"></a>

## <a name="add-a-new-credit-card-to-an-azure-subscription"></a>Přidání nové platební karty do předplatného Azure

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako správce účtu.
1. Vyhledejte **cost management a fakturaci**.

    ![Snímek obrazovky, který zobrazuje hledání](./media/billing-how-to-change-credit-card/search.png)

1. Vyberte předplatné, do kterého chcete přidat platební kartu.
1. Vyberte **Způsoby platby**.

    ![Snímek obrazovky, který zobrazuje vybranou možnost spravovat způsoby platby.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. V levém horním rohu vyberte + a přidejte kartu. Na pravé straně se zobrazí formulář platební karty.
1. Zadejte podrobnosti o kreditní kartě.

    ![Snímek obrazovky, který ukazuje přidání nové karty](./media/billing-how-to-change-credit-card/sub-add-new-x.png)

1. Pokud chcete tuto kartu aktivovat jako aktivní způsob platby, zaškrtněte políčko u **této aktivní metody platby** nad formulářem. Tato karta se stane aktivním platebním nástrojem pro všechna předplatná využívající stejnou kartu jako vybrané předplatné.

1. Vyberte **Další**.

Pokud po přidání platební karty dojde k chybě, přečtěte si část [platební karta odmítla při registraci v Azure](billing-credit-card-fails-during-azure-sign-up.md).

## <a name="update-existing-credit-card"></a>Aktualizovat existující platební kartu

Pokud se vaše platební karta obnoví a číslo zůstane stejné, aktualizujte si existující údaje o kreditní kartě, jako je datum vypršení platnosti. Pokud se vaše číslo platební karty změní, protože došlo ke ztrátě, odcizení nebo vypršení platnosti karty, postupujte podle kroků uvedených v části [přidání platební karty jako způsobu platby](#addcard) . Nemusíte aktualizovat CVV.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako správce účtu.
1. Vyhledejte **cost management a fakturaci**.

    ![Snímek obrazovky, který zobrazuje hledání](./media/billing-how-to-change-credit-card/search.png)

1. Vyberte **Způsoby platby**.

    ![Snímek obrazovky, který zobrazuje vybranou možnost spravovat způsoby platby.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Klikněte na platební kartu, kterou chcete upravit. Na pravé straně se zobrazí formulář platební karty.

    ![Snímek obrazovky zobrazující vybranou kreditní kartu](./media/billing-how-to-change-credit-card/edit-card-x.png)

1. Aktualizujte podrobnosti platební karty.
1. Vyberte **Uložit**.

## <a name="use-a-different-credit-card"></a>Použít jinou platební kartu

Pokud má více než jedno předplatné stejný aktivní způsob platby, pak Změna aktivního způsobu platby na kterékoli z těchto předplatných také aktualizuje aktivní způsob platby na jiné.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako správce účtu.
1. Vyhledejte **cost management a fakturaci**.

    ![Snímek obrazovky, který zobrazuje hledání](./media/billing-how-to-change-credit-card/search.png)

1. Vyberte předplatné, do kterého chcete přidat platební kartu.
1. Vyberte **Způsoby platby**.

    ![Snímek obrazovky, který zobrazuje vybranou možnost spravovat způsoby platby.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Zaškrtněte políčko vedle karty, kterou chcete nastavit jako aktivní způsob platby.
1. Klikněte na **nastavit aktivní**.
    ![Snímek obrazovky, který zobrazuje vybranou platební kartu a nastavit jako aktivní](./media/billing-how-to-change-credit-card/sub-change-active-x.png)

## <a name="remove-a-credit-card-from-the-account"></a>Odebrání platební karty z účtu

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako správce účtu.
1. Na levé straně stránky vyberte **cost management + fakturace** .

    ![Snímek obrazovky, který zobrazuje hledání](./media/billing-how-to-change-credit-card/search.png)

1. V části **fakturace**vyberte **způsoby platby**.

    ![Snímek obrazovky, který zobrazuje vybranou možnost spravovat způsoby platby.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Zaškrtněte políčko vedle karty, kterou chcete odebrat.
1. Klikněte na tlačítko **odstranit**.

Pokud je vaše platební karta aktivním způsobem platby pro některé z vašich předplatných Microsoftu, nemůžete ji odebrat z účtu Azure. Změňte aktivní způsob platby pro všechna předplatná propojená s touto kreditní kartou a zkuste to znovu.
<!-- # Add, update, or remove a credit card for Azure

In the Account Center, you can add a new credit card, update an existing credit card, or delete a credit card that you don't use. You must be an [Account Administrator](billing-subscription-transfer.md#whoisaa) to make these changes.

**Want to switch to pay by invoice?** See [Pay for Azure subscriptions by invoice](billing-how-to-pay-by-invoice.md).

<a id="addcard"></a>

## Add a new credit card

1. Sign in to the [Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select a subscription.
1. On the right side of the page, select **Manage payment methods**.

    ![Screenshot that shows Manage payment methods option selected.](./media/billing-how-to-change-credit-card/changesub_new.png)
1. Select “+” to add a card.

    ![Screenshot that shows the edit option next to the payment method.](./media/billing-how-to-change-credit-card/editcard_new.png)
1. Enter credit card details.
1. Select **Save**.

If you get an error after you add the credit card, see [Credit card declined at Azure sign-up](billing-credit-card-fails-during-azure-sign-up.md).

## Update existing credit card

If your credit card gets renewed and the number remains the same, update the existing credit card details like the expiration date. If your credit card number changes because the card is lost, stolen, or expired, follow the steps in the [Add a credit card as a payment method](#addcard) section. You don't need to update the CVV.

1. Sign in to the [Azure Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select the subscription that's linked to the card.
1. Select **Manage payment methods**.
1. Select **Edit** next to the card you want to update.
1. Update the credit card details.
1. Select **Save**.

## Use a different credit card for the Azure subscription

1. Sign in to the [Azure Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select the subscription that's linked to the card.
1. On the right side of the page, select **Manage payment methods**.
1. Click **Use Instead** next to the card that you want to use. This also updates any other subscriptions currently associated with this card.

## Remove a credit card from the account

1. Sign in to the [Azure Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select the subscription that's linked to the card.
3. On the right side of the page, select **Manage payment methods**.
4. Click **Delete** for the credit card that you want to delete.

If your credit card is associated with other active Microsoft subscriptions, you can't remove it from your Azure account. Remove the credit card from all active subscriptions that you have with Microsoft and try again. -->

## <a name="change-payment-method-for-a-billing-profile"></a>Změnit způsob platby pro fakturační profil

Pokud chcete změnit způsob platby pro fakturační profil, musíte být osoba, která se zaregistrovala v Azure.

Pokud chcete přepnout výchozí způsob platby na kontrolu/přenos přenosu, Naučte se, jak přepínat [Fakturační profil a ověřit/přenést přenos](billing-how-to-pay-by-invoice.md).

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Hledat na **Cost Management a fakturace**.
1. V nabídce na levé straně klikněte na **profily fakturace**.

    ![snímek obrazovky, který zobrazuje profil fakturace v nabídce](./media/billing-how-to-change-credit-card/billing-profile.png)

1. Vyberte fakturační profil.
1. V nabídce na levé straně vyberte **způsoby platby**.

   ![Snímek obrazovky, který zobrazuje způsoby platby v nabídce](./media/billing-how-to-change-credit-card/billing-profile-payment-methods.png)

1. Nad výchozí způsob platby klikněte na **změnit**.

    ![Snímek obrazovky, který zobrazuje tlačítko pro změnu](./media/billing-how-to-change-credit-card/customer-led-switch-credit-card.png)

1. Vyberte existující kartu nebo přidejte novou.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy
V následujících částech najdete odpovědi na nejčastější dotazy týkající se změny informací o kreditních kartách.

### <a name="my-subscription-is-disabled-why-cant-i-remove-my-credit-card-now"></a>Moje předplatné je zakázané. Proč teď nejde platební karta odebrat?

Po zakázání nebo zrušení předplatného počkáme 90 dní, než se vaše předplatné trvale odstraní. V případě, že chcete předplatné znovu aktivovat, budeme v průběhu doby uchování v souboru uchovávat způsob platby. Pak se předplatné trvale odstraní.

Pokud potřebujete platební kartu odebrat před uplynutím 90 dní, [znovu aktivujte své předplatné](billing-subscription-become-disable.md). Pokud se nemůžete znovu aktivovat, obraťte se na [podporu Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="why-do-i-keep-getting-your-login-session-has-expired-please-click-here-to-log-back-in"></a>Proč se pořád zobrazuje "vaše přihlašovací relace vypršela. Pokud se chcete znovu přihlásit, klikněte prosím sem.

Pokud se tato chybová zpráva zobrazuje i v případě, že jste se už odhlásili a zpátky, zkuste to znovu s privátní relací procházení.

### <a name="how-do-i-use-a-different-card-for-each-subscription-i-have"></a>Návody použít pro každé předplatné jinou kartu?

Pokud však vaše předplatná již používají stejnou kartu, není možné je oddělit, aby používaly různé karty. Když se ale přihlásíte k novému předplatnému, můžete pro toto předplatné použít nový způsob platby.

### <a name="how-do-i-make-payments"></a>Návody udělat platby?

Pokud nastavíte platební kartu jako způsob platby, po každém fakturačním období vám karta automaticky účtujeme. Nemusíte nic dělat.

Pokud [platíte podle faktury](billing-how-to-pay-by-invoice.md), pošlete platbu do umístění uvedeného v dolní části faktury.

### <a name="how-do-i-change-the-tax-id"></a>Návody změnit daňové ID?

Pokud chcete přidat nebo aktualizovat daňové ID, aktualizujte svůj profil v [centrum účtů Azure](https://account.azure.com/Profile)a pak vyberte **daňový záznam**. Toto DIČ slouží k výpočtu slevy na dani a zobrazí se na vaší faktuře.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Podívejte se na přístup k zákaznickým smlouvám Microsoftu
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Potřebujete pomoct? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další postup
- Přečtěte si o [rezervacích Azure](billing-save-compute-costs-reservations.md) , abyste zjistili, jestli vám můžou ušetřit peníze.

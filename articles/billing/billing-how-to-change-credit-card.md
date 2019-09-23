---
title: Změna platební karty pro Azure
description: Popisuje, jak změnit platební kartu, kterou používáte k úhradě předplatného Azure.
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
ms.openlocfilehash: 05b1fe3d061e735322e6eb6c65e60f63d8adb4d6
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2019
ms.locfileid: "70933870"
---
# <a name="add-update-or-remove-a-credit-card-for-azure"></a>Přidání, aktualizace nebo odebrání platební karty v Azure

Tento dokument je určený pro zákazníky, kteří si zaregistrovali Azure online pomocí platební karty.

Na webu Azure Portal můžete přidat novou platební kartu, aktualizovat stávající platební kartu nebo odstranit platební kartu, kterou nepoužíváte. Pokud chcete provést tyto změny, musíte být [Správce účtu](billing-subscription-transfer.md#whoisaa).

Pokud máte [smlouvu se zákazníkem Microsoftu](#check-access-to-a-microsoft-customer-agreement), jsou způsoby platby přidružené k fakturačním profilům. Zjistěte, jak [změnit výchozí způsob platby pro určitý fakturační profil](#change-payment-method-for-a-billing-profile). Způsob platby může aktualizovat pouze uživatel, který se zaregistrovat v Azure.

**Chcete přejít na platbu fakturou (šekem / bezhotovostním převodem)?** Přečtěte si téma [Úhrada předplatných Azure pomocí faktury](billing-how-to-pay-by-invoice.md).

<a id="addcard"></a>

## <a name="add-a-new-credit-card-to-an-azure-subscription"></a>Přidání nové platební karty k předplatnému Azure

1. Přihlaste se na web [Azure Portal](https://portal.azure.com) jako správce účtu.
1. Vyhledejte položku **Správa nákladů a fakturace**.

    ![Snímek obrazovky znázorňující hledání](./media/billing-how-to-change-credit-card/search.png)

1. Vyberte předplatné, ke kterému chcete přidat platební kartu.
1. Vyberte **Způsoby platby**.

    ![Snímek obrazovky s vybranou možností Správa způsobů platby.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. V levém horním rohu vyberte + a přidejte kartu. Na pravé straně se zobrazí formulář platební karty.
1. Zadejte údaje platební karty.

    ![Snímek obrazovky znázorňující přidávání nové karty](./media/billing-how-to-change-credit-card/sub-add-new-x.png)

1. Pokud chcete tuto kartu nastavit jako aktivní způsob platby, zaškrtněte nad formulářem políčko vedle možnosti **Nastavit tento způsob platby jako aktivní**. Tato karta se stane aktivním platebním nástrojem pro všechna předplatná využívající stejnou kartu jako vybrané předplatné.

1. Vyberte **Další**.

Pokud po přidání platební karty dojde k chybě, přečtěte si téma věnované [odmítnutí platební karty při registraci do Azure](billing-credit-card-fails-during-azure-sign-up.md).

## <a name="update-existing-credit-card"></a>Aktualizace stávající platební karty

Pokud dojde k obnovení vaší platební karty a její číslo zůstane stejné, můžete aktualizovat údaje stávající platební karty, jako je datum vypršení platnosti. Pokud se číslo vaší karty změní, třeba z důvodu ztráty, krádeže nebo vypršení platnosti karty, postupujte podle návodu v části [Přidání platební karty jako způsobu platby](#addcard). Kód CVV není nutné aktualizovat.

1. Přihlaste se na web [Azure Portal](https://portal.azure.com) jako správce účtu.
1. Vyhledejte položku **Správa nákladů a fakturace**.

    ![Snímek obrazovky znázorňující hledání](./media/billing-how-to-change-credit-card/search.png)

1. Vyberte **Způsoby platby**.

    ![Snímek obrazovky s vybranou možností Správa způsobů platby.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Klikněte na platební kartu, kterou chcete upravit. Na pravé straně se zobrazí formulář platební karty.

    ![Snímek obrazovky znázorňující vybranou platební kartu](./media/billing-how-to-change-credit-card/edit-card-x.png)

1. Aktualizujte údaje platební karty.
1. Vyberte **Uložit**.

## <a name="use-a-different-credit-card"></a>Použití jiné platební karty

Pokud má více vašich předplatných stejný aktivní způsob platby, změnou aktivního způsobu platby u kteréhokoli z těchto předplatných zároveň aktualizujete aktivní způsob platby u ostatních předplatných.

1. Přihlaste se na web [Azure Portal](https://portal.azure.com) jako správce účtu.
1. Vyhledejte položku **Správa nákladů a fakturace**.

    ![Snímek obrazovky znázorňující hledání](./media/billing-how-to-change-credit-card/search.png)

1. Vyberte předplatné, ke kterému chcete přidat platební kartu.
1. Vyberte **Způsoby platby**.

    ![Snímek obrazovky s vybranou možností Správa způsobů platby.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Zaškrtněte políčko vedle karty, kterou chcete nastavit jako aktivní způsob platby.
1. Klikněte na **Nastavit jako aktivní**.
    ![Snímek obrazovky znázorňující vybranou platební kartu a její nastavení jako aktivní](./media/billing-how-to-change-credit-card/sub-change-active-x.png)

## <a name="remove-a-credit-card-from-the-account"></a>Odebrání platební karty z účtu

1. Přihlaste se na web [Azure Portal](https://portal.azure.com) jako správce účtu.
1. Na levé straně stránky vyberte **Správa nákladů a fakturace**.

    ![Snímek obrazovky znázorňující hledání](./media/billing-how-to-change-credit-card/search.png)

1. V části **Fakturace** vyberte **Způsoby platby**.

    ![Snímek obrazovky s vybranou možností Správa způsobů platby.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Zaškrtněte políčko vedle karty, kterou chcete odebrat.
1. Klikněte na **Odstranit**.

Pokud vaše platební karta představuje aktivní způsob platby pro některé z vašich předplatných Microsoftu, nemůžete ji z účtu Azure odebrat. Změňte aktivní způsob platby u všech předplatných spojených s touto platební kartou a zkuste to znovu.
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

## <a name="change-payment-method-for-a-billing-profile"></a>Změna způsobu platby pro fakturační profil

Způsob platby pro určitý fakturační profil může změnit jenom osoba, která provedla registraci v Azure.

Pokud chcete změnit výchozí způsob platby na šek / bezhotovostní převod, přečtěte si, jak [změnit nastavení fakturačního profilu na šek / bezhotovostní převod](billing-how-to-pay-by-invoice.md).

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Vyhledejte **Cost Management a fakturace**.
1. V nabídce na levé straně klikněte na **Fakturační profily**.

    ![Snímek obrazovky s možností Fakturační profily v nabídce](./media/billing-how-to-change-credit-card/billing-profile.png)

1. Zvolte fakturační profil.
1. V nabídce na levé straně vyberte **Způsoby platby**.

   ![Snímek obrazovky s možností Způsoby platby v nabídce](./media/billing-how-to-change-credit-card/billing-profile-payment-methods.png)

1. Nad výchozím způsobem platby klikněte na **Změnit**.

    ![Snímek obrazovky s tlačítkem Změnit](./media/billing-how-to-change-credit-card/customer-led-switch-credit-card.png)

1. Vyberte stávající kartu nebo přidejte novou.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy
V následujících částech najdete odpovědi na nejčastější dotazy týkající se změny údajů platebních karet.

### <a name="my-subscription-is-disabled-why-cant-i-remove-my-credit-card-now"></a>Moje předplatné je zablokované. Proč teď nemůžu odebrat platební kartu?

Po zablokování nebo zrušení vašeho předplatného čekáme 90 dní a pak vaše předplatné trvale odstraníme. Během této doby uchovávání způsob platby archivujeme pro případ, že byste se rozhodli předplatné znovu aktivovat. Po této době předplatné trvale odstraníme.

Pokud potřebujete platební kartu odebrat před uplynutím 90denní doby uchovávání, [znovu aktivujte své předplatné](billing-subscription-become-disable.md). Pokud se vám nedaří předplatné znovu aktivovat, [obraťte se na podporu Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="why-do-i-keep-getting-your-login-session-has-expired-please-click-here-to-log-back-in"></a>Proč se mi pořád zobrazuje zpráva „Vypršela platnost relace přihlášení. Pokud se chcete znovu přihlásit, klikněte sem“?

Pokud se vám pořád zobrazuje tato chybová zpráva, i když se odhlásíte a znovu přihlásíte, zkuste to znovu, tentokrát pomocí relace anonymního prohlížení.

### <a name="how-do-i-use-a-different-card-for-each-subscription-i-have"></a>Jak nastavím pro každé ze svých předplatných jinou kartu?

Pokud už vaše předplatná používají stejnou kartu, bohužel je nemůžete rozdělit, aby každé používalo jinou kartu. Když si ale zaregistrujete nové předplatné, můžete si pro toto předplatné zvolit nový způsob platby.

### <a name="how-do-i-make-payments"></a>Jak se provádějí platby?

Pokud jako způsob platby nastavíte platební kartu, po uplynutí každého fakturačního období vám z karty automaticky strhneme příslušné poplatky. Vy nemusíte dělat nic.

Pokud [platíte pomocí faktury](billing-how-to-pay-by-invoice.md), zašlete svoji platbu na adresu uvedenou ve spodní části faktury.

### <a name="how-do-i-change-the-tax-id"></a>Jak změním DIČ?

Pokud chcete přidat nebo aktualizovat DIČ, aktualizujte svůj profil v [Centru účtů Azure](https://account.azure.com/Profile) a vyberte **Daňové záznamy**. Toto DIČ slouží k výpočtu slevy na dani a zobrazí se na vaší faktuře.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Ověření přístupu ke smlouvě se zákazníkem Microsoftu
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky
- Přečtěte si o [rezervacích Azure](billing-save-compute-costs-reservations.md) a zjistěte, jestli vám můžou ušetřit peníze.

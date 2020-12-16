---
title: Úlohy správce účtu na webu Azure Portal
description: Popis postupu při provádění platebních operací na webu Azure Portal
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 10/26/2020
ms.author: banders
ms.custom: contperf-fy21q2
ms.openlocfilehash: bd46e7b2f0713da67842def47dfeadc837027d8f
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2020
ms.locfileid: "97027964"
---
# <a name="account-administrator-tasks-in-the-azure-portal"></a>Úlohy správce účtu na webu Azure Portal

Tento článek vysvětluje, jak na webu Azure Portal provádět následující úlohy:
- Správa způsobů platby předplatného
- Odebrání limitu útraty pro předplatné
- Přidání kreditů k předplatnému Azure v rámci licenčního programu Open License

Abyste mohli provádět tyto úlohy, musíte být správcem účtu.

## <a name="navigate-to-your-subscriptions-payment-methods"></a>Přechod na způsoby platby předplatného

1. Přihlaste se na web Azure Portal jako správce účtu.

1. Vyhledejte položku **Správa nákladů a fakturace**.

    ![Snímek obrazovky znázorňující hledání položky Cost Management a fakturace ](./media/account-admin-tasks/search-bar.png)

1. V seznamu **Moje předplatná** vyberte předplatné, ke kterému chcete přidat platební kartu.

   ![Snímek obrazovky se stránkou Cost Management + Billing, kde můžete vybrat předplatné](./media/account-admin-tasks/cost-management-billing-overview-x.png)

   > [!NOTE]
   > Pokud tady nevidíte některá svá předplatná, je možné, že jste v nějakém okamžiku změnili adresář předplatného. U těchto předplatných musíte přepnout na původní adresář (adresář, ve které jste se původně zaregistrovali). Potom opakujte krok 2.

1. Vyberte **Způsoby platby**.

    ![Snímek obrazovky se stránkou Způsoby platby, kde můžete přidat způsob platby](./media/account-admin-tasks/subscription-payment-methods-blade.png)

Tady můžete přidat novou platební kartu, změnit aktivní způsob platby, upravit údaje platebních karet a odstranit platební karty.

### <a name="change-active-payment-method"></a>Změna aktivního způsobu platby

Aktivní způsob platby můžete změnit přidáním nové platební karty nebo zvolením již uložené platební karty. Změna aktivního způsobu platby na novou platební kartu:

1. V levém horním rohu vyberte + a přidejte platební kartu.

    ![Snímek obrazovky se symbolem plus](./media/account-admin-tasks/subscription-payment-methods-plus.png)

1. Do formuláře vpravo zadejte údaje platební karty.

    ![Snímek obrazovky s formulářem pro přidání platební karty.](./media/account-admin-tasks/subscription-add-payment-method-x.png)

1. Pokud chcete tuto kartu nastavit jako aktivní způsob platby, zaškrtněte nad formulářem políčko vedle možnosti **Nastavit tento způsob platby jako aktivní**. Tato karta se stane aktivním platebním nástrojem pro všechna předplatná využívající stejnou kartu jako vybrané předplatné.

    ![Snímek obrazovky se zaškrtávacím políčkem pro nastavení karty na aktivní způsob platby](./media/account-admin-tasks/subscription-make-active-payment-method-x.png)

1. Vyberte **Další**.

Změna aktivního způsobu platby na již uloženou platební kartu:

1. Zaškrtněte políčko vedle karty, kterou chcete nastavit jako aktivní způsob platby.

    ![Snímek obrazovky se zaškrtnutým políčkem vedle platební karty](./media/account-admin-tasks/subscription-checked-payment-method-x.png)

1. Na panelu příkazů klikněte na **Nastavit jako aktivní**.

    ![Snímek obrazovky s tlačítkem Nastavit jako aktivní](./media/account-admin-tasks/subscription-checked-payment-method-set-active.png)

### <a name="edit-credit-card-details"></a>Zadání údajů platební karty

Pokud chcete upravit údaje platební karty, jako je datum vypršení platnosti nebo adresa, klikněte na platební kartu, kterou chcete upravit. Na pravé straně se zobrazí formulář platební karty.

![Snímek obrazovky s vybranou platební kartou](./media/account-admin-tasks/subscription-edit-payment-method-x.png)

Aktualizujte údaje platební karty a klikněte na **Uložit**.

### <a name="remove-a-credit-card-from-the-account"></a>Odebrání platební karty z účtu

1. Zaškrtněte políčko vedle karty, kterou chcete odstranit.

    ![Snímek obrazovky se zaškrtnutým políčkem vedle platební karty](./media/account-admin-tasks/subscription-checked-payment-method-x.png)

1. Na panelu příkazů klikněte na **Odstranit**.

    ![Snímek obrazovky s tlačítkem Odstranit](./media/account-admin-tasks/subscription-checked-payment-method-delete.png)

Pokud vaše platební karta představuje aktivní způsob platby pro některé z vašich předplatných Microsoftu, nemůžete ji z účtu Azure odebrat. Změňte aktivní způsob platby u všech předplatných spojených s touto platební kartou a zkuste to znovu.

### <a name="switch-to-invoice-payment"></a>Přepnutí na platby pomocí faktur

Pokud máte nárok na platby pomocí faktur (šekem nebo převodem), můžete předplatné přepnout na platby pomocí faktur (šekem nebo převodem) na webu Azure Portal.

1. Na panelu příkazů vyberte **Platba pomocí faktury**.

    ![Snímek obrazovky se stránkou Způsoby platby a vybranou možností Platba pomocí faktury](./media/account-admin-tasks/subscription-payment-methods-pay-by-invoice.png)

1. Zadejte pro způsob platby pomocí faktur adresu.
1. Klikněte na **Další**.

Pokud chcete získat schválení pro platby pomocí faktur, přečtěte si, [jak platit pomocí faktur](pay-by-invoice.md).

### <a name="edit-invoice-payment-address"></a>Úprava adresy pro platby pomocí faktur

Pokud chcete upravit adresu pro platby pomocí faktur, v seznamu způsobů platby pro vaše předplatné klikněte na **Faktura**. Na pravé straně se otevře formulář adresy.

## <a name="remove-spending-limit"></a>Odebrání limitu útraty

Limit útraty v Azure zabraňuje překročení částky kreditu. Limit útraty můžete odebrat kdykoli za předpokladu, že je k vašemu předplatnému Azure přidružen platný způsob platby. U typů předplatného s kreditem na několik měsíců, jako jsou předplatná sady Visual Studio Enterprise nebo Visual Studio Professional, můžete limit útraty znovu povolit na začátku dalšího fakturačního období.

Limit útraty není dostupný u předplatných s plány závazků nebo s průběžnými platbami.

1. Přihlaste se na web Azure Portal jako správce účtu.
1. Vyhledejte položku **Správa nákladů a fakturace**.

    ![Snímek obrazovky znázorňující hledání položky Cost Management a fakturace ](./media/account-admin-tasks/search-bar.png)

1. V seznamu **Moje předplatná** vyberte vaše předplatné sady Visual Studio Enterprise.

   ![Snímek obrazovky s oblastí Moje předplatná, kde můžete vybrat své předplatné sady Visual Studio Enterprise](./media/account-admin-tasks/cost-management-overview-msdn-x.png)

    > [!NOTE]
    > Pokud tady nevidíte některá svá předplatná sady Visual Studio, je možné, že jste v nějakém okamžiku změnili adresář předplatného. U těchto předplatných musíte přepnout na původní adresář (adresář, ve které jste se původně zaregistrovali). Potom opakujte krok 2.

1. V přehledu předplatného odeberte limit útraty kliknutím na oranžový banner.

    ![Snímek obrazovky s bannerem pro odebrání limitu útraty](./media/account-admin-tasks/msdn-remove-spending-limit-banner-x.png)

1. Zvolte, jestli chcete limit útraty odebrat trvale, nebo pouze pro aktuální fakturační období.

   ![Snímek obrazovky s oknem Odebrat limit útraty](./media/account-admin-tasks/remove-spending-limit-blade-x.png)

1. Klikněte na **Vyberte způsob platby** a zvolte způsob platby pro vaše předplatné. Tento způsob platby se nastaví jako aktivní způsob platby pro vaše předplatné.

1. Klikněte na **Finish** (Dokončit).

## <a name="add-credits-to-azure-in-open-subscription"></a>Přidání kreditů k předplatnému Azure v rámci licenčního programu Open License

Pokud máte předplatné Azure v rámci licenčního programu Open License, můžete k předplatnému přidat kredity na webu Azure Portal uplatněním kódu Product Key nebo nákupem kreditů pomocí platební karty.

1. Přihlaste se na web Azure Portal jako správce účtu.
1. Vyhledejte položku **Správa nákladů a fakturace**.

    ![Snímek obrazovky znázorňující hledání položky Cost Management a fakturace ](./media/account-admin-tasks/search-bar.png)

1. V seznamu **Moje předplatná** vyberte vaše předplatné Azure v rámci licenčního programu Open License.

    ![Snímek obrazovky s oblastí Moje předplatná, kde můžete vybrat své předplatné Azure v rámci licenčního programu Open License](./media/account-admin-tasks/cost-management-overview-aio-x.png)

   > [!NOTE]
   > Pokud tady nevidíte své předplatné, je možné, že jste v nějakém okamžiku změnili jeho adresář. Musíte přepnout adresář předplatného na původní adresář (adresář, ve kterém jste se původně zaregistrovali). Potom opakujte krok 2.

1. Vyberte **Historie kreditu**.

    ![Snímek obrazovky s historií kreditu](./media/account-admin-tasks/aio-credit-history-blade.png)

1. V levém horním rohu vyberte + a přidejte další kredity.

    ![Snímek obrazovky s tlačítkem Přidat kredity](./media/account-admin-tasks/aio-credit-history-plus.png)

1. V rozevírací nabídce vyberte způsob platby. Můžete přidat kód Product Key nebo zakoupit kredity pomocí platební karty.

    ![Snímek obrazovky s rozevírací nabídkou pro výběr způsobu platby v okně Přidat kredity](./media/account-admin-tasks/add-credits-select-payment-method.png)

1. Pokud jste zvolili kód Product Key:
    - Zadejte kód Product Key.
    - Klikněte na **Ověřit**.

1. Pokud jste zvolili platební kartu:
    - Klikněte na **Vyberte způsob platby** a přidejte platební kartu nebo vyberte existující.
    - Zadejte množství kreditů, které chcete přidat.

1. Klikněte na **Použít**.

## <a name="troubleshooting"></a>Řešení potíží
Virtuální ani předplacené karty nepodporujeme. Pokud při přidávání nebo aktualizaci platné platební karty dochází k chybám, zkuste otevřít prohlížeč v privátním režimu.

## <a name="next-steps"></a>Další kroky
- Přečtěte si další informace o [analýze neočekávaných poplatků](../understand/analyze-unexpected-charges.md).

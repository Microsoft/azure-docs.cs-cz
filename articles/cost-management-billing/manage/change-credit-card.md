---
title: Změna platební karty pro Azure
description: Popisuje, jak změnit platební kartu, kterou používáte k úhradě předplatného Azure.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: c7622c4916b186a39aad9dafb54a1e2404458794
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/09/2020
ms.locfileid: "89569352"
---
# <a name="add-update-or-remove-a-credit-card-for-azure"></a>Přidání, aktualizace nebo odebrání platební karty v Azure

Tento dokument je určený pro zákazníky, kteří si zaregistrovali Azure online pomocí platební karty.

Na webu Azure Portal můžete změnit výchozí způsob platby na novou platební kartu, aktualizovat podrobnosti o platebních kartách a odstranit kreditní karty, které nepoužíváte. Pokud chcete provést tyto změny, musíte být [Správce účtu](billing-subscription-transfer.md#whoisaa).

Podporované způsoby platby pro Microsoft Azure jsou platební karty a šek nebo bezhotovostní převod. Pokud chcete získat schválení pro platbu šekem nebo bezhotovostním převodem, projděte si téma [Úhrada předplatných Azure pomocí faktury](pay-by-invoice.md).

Pokud máte smlouvu se zákazníkem Microsoftu, jsou způsoby platby přidružené k fakturačním profilům. Projděte si, jak [zkontrolovat přístup ke smlouvě se zákazníkem Microsoftu](#check-the-type-of-your-account). Pokud máte MCA, přejděte ke [správě platebních karet pro smlouvu se zákazníkem Microsoftu](#manage-credit-cards-for-a-microsoft-customer-agreement).

<a id="addcard"></a>

## <a name="manage-credit-cards-for-an-azure-subscription"></a>Správa platebních karet pro předplatné Azure

Následující části se vztahují na zákazníky, kteří mají fakturační účet programu MOSP (Microsoft Online Services Program). Zjistěte, jak [zkontrolovat typ vašeho fakturačního účtu](#check-the-type-of-your-account). Pokud je váš fakturační účet typu MOSP (Microsoft Online Services Program), jsou způsoby platby přidružené k jednotlivým předplatným Azure. Pokud po přidání platební karty dojde k chybě, přečtěte si téma věnované [odmítnutí platební karty při registraci do Azure](../../billing/billing-credit-card-fails-during-azure-sign-up.md).

### <a name="change-credit-card-for-a-subscription-by-adding-a-new-credit-card"></a>Změna platební karty pro předplatné (přidání nové platební karty)

Jako výchozí platební kartu pro předplatné Azure můžete zadat novou platební kartu, nebo kartu, kterou jste si už uložili na webu Azure Portal. Abyste mohli provést změnu platební karty, musíte být správce účtu. Pokud má více vašich předplatných stejný aktivní způsob platby, změnou aktivního způsobu platby u kteréhokoli z těchto předplatných zároveň aktualizujete aktivní způsob platby u ostatních předplatných.


Výchozí platební kartu předplatného můžete změnit na novou pomocí následujících kroků:

1. Přihlaste se na web [Azure Portal](https://portal.azure.com) jako správce účtu.
1. Vyhledejte položku **Správa nákladů a fakturace**.

    ![Snímek obrazovky znázorňující hledání](./media/change-credit-card/search.png)

1. Vyberte předplatné, ke kterému chcete přidat platební kartu.
1. Vyberte **Způsoby platby**.

    ![Snímek obrazovky s vybranou možností Správa způsobů platby.](./media/change-credit-card/payment-methods-blade-x.png)

1. V levém horním rohu vyberte + a přidejte kartu. Na pravé straně se zobrazí formulář platební karty.
1. Zadejte údaje platební karty.

    ![Snímek obrazovky znázorňující přidávání nové karty](./media/change-credit-card/sub-add-new-x.png)

1. Pokud chcete tuto kartu nastavit jako aktivní způsob platby, zaškrtněte nad formulářem políčko vedle možnosti **Nastavit tento způsob platby jako aktivní**. Tato karta se stane aktivním platebním nástrojem pro všechna předplatná využívající stejnou kartu jako vybrané předplatné.

1. Vyberte **Další**.

### <a name="change-credit-card-for-a-subscription-to-a-previously-saved-credit-card"></a>Změna platební karty pro předplatné (použití už uložené platební karty)

Výchozí platební kartu vašeho předplatného můžete také změnit na kartu, kterou jste si už ve vašem účtu uložili, a to pomocí následujících kroků:

1. Přihlaste se na web [Azure Portal](https://portal.azure.com) jako správce účtu.
1. Vyhledejte položku **Správa nákladů a fakturace**.

    ![Snímek obrazovky znázorňující hledání](./media/change-credit-card/search.png)

1. Vyberte předplatné, ke kterému chcete přidat platební kartu.
1. Vyberte **Způsoby platby**.

    ![Snímek obrazovky s vybranou možností Správa způsobů platby.](./media/change-credit-card/payment-methods-blade-x.png)

1. Zaškrtněte políčko vedle karty, kterou chcete nastavit jako aktivní způsob platby.
1. Klikněte na **Nastavit jako aktivní**.
    ![Snímek obrazovky znázorňující vybranou platební kartu a její nastavení jako aktivní](./media/change-credit-card/sub-change-active-x.png)

### <a name="edit-credit-card-details"></a>Zadání údajů platební karty

Pokud dojde k obnovení vaší platební karty a její číslo zůstane stejné, můžete aktualizovat údaje stávající platební karty, jako je datum vypršení platnosti. Pokud se číslo vaší karty změní, třeba z důvodu ztráty, krádeže nebo vypršení platnosti karty, postupujte podle návodu v části [Přidání platební karty jako způsobu platby](#addcard). Kód CVV není nutné aktualizovat.

1. Přihlaste se na web [Azure Portal](https://portal.azure.com) jako správce účtu.
1. Vyhledejte položku **Správa nákladů a fakturace**.

    ![Snímek obrazovky znázorňující hledání](./media/change-credit-card/search.png)

1. Vyberte **Způsoby platby**.

    ![Snímek obrazovky s vybranou možností Správa způsobů platby.](./media/change-credit-card/payment-methods-blade-x.png)

1. Klikněte na platební kartu, kterou chcete upravit. Na pravé straně se zobrazí formulář platební karty.

    ![Snímek obrazovky znázorňující vybranou platební kartu](./media/change-credit-card/edit-card-x.png)

1. Aktualizujte údaje platební karty.
1. Vyberte **Uložit**.

### <a name="delete-a-credit-card-from-the-account"></a>Odstranění platební karty z účtu

1. Přihlaste se na web [Azure Portal](https://portal.azure.com) jako správce účtu.
1. Na levé straně stránky vyberte **Správa nákladů a fakturace**.

    ![Snímek obrazovky znázorňující hledání](./media/change-credit-card/search.png)

1. V části **Fakturace** vyberte **Způsoby platby**.

    ![Snímek obrazovky s vybranou možností Správa způsobů platby.](./media/change-credit-card/payment-methods-blade-x.png)

1. Zaškrtněte políčko vedle karty, kterou chcete odebrat.
1. Klikněte na **Odstranit**.

Pokud vaše platební karta představuje aktivní způsob platby pro některé z vašich předplatných Microsoftu, nemůžete ji z účtu Azure odebrat. Změňte aktivní způsob platby u všech předplatných spojených s touto platební kartou a zkuste to znovu.

## <a name="manage-credit-cards-for-a-microsoft-customer-agreement"></a>Správa platebních karet pro smlouvu se zákazníkem Microsoftu

Následující části se vztahují na zákazníky, kteří mají smlouvu se zákazníkem Microsoftu a zaregistrovali si Azure online pomocí platební karty. [Další informace o postupu při kontrole, jestli máte smlouvu se zákazníkem Microsoftu](#check-the-type-of-your-account).

### <a name="change-default-credit-card"></a>Změna výchozí platební karty

Pokud máte smlouvu se zákazníkem Microsoftu, je vaše platební karta přidružená k fakturačnímu profilu. Způsob platby pro určitý fakturační profil může změnit jenom osoba, která provedla registraci v Azure a vytvořila fakturační účet.

Pokud chcete jako výchozí způsob platby pro váš fakturační profil nastavit šek nebo bezhotovostní převod, projděte si téma [Úhrada předplatných Azure pomocí faktury](pay-by-invoice.md).

Pokud chcete změnit platební kartu, postupujte podle následujících kroků:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Vyhledejte **Cost Management a fakturace**.
1. V nabídce na levé straně klikněte na **Fakturační profily**.
1. Vyberte fakturační profil.
1. V nabídce na levé straně vyberte **Způsoby platby**.

   ![Snímek obrazovky s možností Způsoby platby v nabídce](./media/change-credit-card/payment-methods-tab-mca.png)

1. V části **Výchozí způsob platby** klikněte na **Změnit**.

    ![Snímek obrazovky s tlačítkem Změnit](./media/change-credit-card/change-payment-method-mca.png)

1. V novém okně na pravé straně buď vyberte existující kartu z rozevíracího seznamu, nebo přidejte novou kliknutím na modrý odkaz Přidat nový způsob platby.

### <a name="edit-or-delete-a-credit-card"></a>Úprava nebo odstranění platební karty

Upravovat podrobnosti o platební kartě (například aktualizovat datum ukončení platnosti) a odstraňovat platební karty můžete z vašeho účtu na webu Azure Portal. Platební kartu můžete odstranit jenom v případě, že není přidružená k žádnému fakturačnímu profilu ani předplatnému Azure. Pokud je přidružená k zablokovanému předplatnému Azure, musíte počkat, než se toto předplatné odstraní (30 až 90 dní po zrušení). Teprve potom je možné kartu odstranit.

Pokud chcete upravit nebo odstranit platební kartu, postupujte podle následujících kroků:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Vyhledejte **Cost Management a fakturace**.
1. V nabídce na levé straně klikněte na **Fakturační profily**.
1. Vyberte fakturační profil.
1. V nabídce na levé straně vyberte **Způsoby platby**.

   ![Snímek obrazovky s možností Způsoby platby v nabídce](./media/change-credit-card/payment-methods-tab-mca.png)

1. V části **Vaše platební karty** vyhledejte platební kartu, kterou chcete upravit nebo odstranit.
1. Vyberte tři tečky (`...`) na konci příslušného řádku.

    ![Snímek obrazovky ukazující tři tečky](./media/change-credit-card/edit-delete-credit-card-mca.png)

1. Pokud chcete upravovat podrobnosti platební karty, v místní nabídce vyberte **Upravit**.
1. Pokud chcete platební karty odstranit, v místní nabídce vyberte **Odstranit**.

## <a name="troubleshooting"></a>Řešení potíží

Virtuální ani předplacené karty nepodporujeme. Pokud při přidávání nebo aktualizaci platné platební karty dochází k chybám, zkuste otevřít prohlížeč v privátním režimu.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

V následujících částech najdete odpovědi na nejčastější dotazy týkající se změny údajů platebních karet.

### <a name="my-subscription-is-disabled-why-cant-i-remove-my-credit-card-now"></a>Moje předplatné je zablokované. Proč teď nemůžu odebrat platební kartu?

Po zablokování nebo zrušení vašeho předplatného čekáme 90 dní a pak vaše předplatné trvale odstraníme. Během této doby uchovávání způsob platby archivujeme pro případ, že byste se rozhodli předplatné znovu aktivovat. Po této době předplatné trvale odstraníme.

Pokud potřebujete platební kartu odebrat před uplynutím 90denní doby uchovávání, [znovu aktivujte své předplatné](subscription-disabled.md). Pokud se vám nedaří předplatné znovu aktivovat, [obraťte se na podporu Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="why-do-i-keep-getting-your-login-session-has-expired-please-click-here-to-log-back-in"></a>Proč se mi pořád zobrazuje zpráva „Vypršela platnost relace přihlášení. Pokud se chcete znovu přihlásit, klikněte sem“?

Pokud se vám pořád zobrazuje tato chybová zpráva, i když se odhlásíte a znovu přihlásíte, zkuste to znovu, tentokrát pomocí relace anonymního prohlížení.

### <a name="how-do-i-use-a-different-card-for-each-subscription-i-have"></a>Jak nastavím pro každé ze svých předplatných jinou kartu?

Pokud už vaše předplatná používají stejnou kartu, bohužel je nemůžete rozdělit, aby každé používalo jinou kartu. Když si ale zaregistrujete nové předplatné, můžete si pro toto předplatné zvolit nový způsob platby.

### <a name="how-do-i-make-payments"></a>Jak se provádějí platby?

Pokud jako způsob platby nastavíte platební kartu, po uplynutí každého fakturačního období vám z karty automaticky strhneme příslušné poplatky. Vy nemusíte dělat nic.

Pokud [platíte pomocí faktury](pay-by-invoice.md), zašlete svoji platbu na adresu uvedenou ve spodní části faktury.

### <a name="how-do-i-change-the-tax-id"></a>Jak změním DIČ?

Pokud chcete přidat nebo aktualizovat DIČ, aktualizujte svůj profil na webu [Azure Portal](https://portal.azure.com) a vyberte **Daňové záznamy**. Toto DIČ slouží k výpočtu slevy na dani a zobrazí se na vaší faktuře.

## <a name="check-the-type-of-your-account"></a>Ověření typu účtu

[!INCLUDE [billing-check-mca](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [rezervacích Azure](../reservations/save-compute-costs-reservations.md) a zjistěte, jestli vám můžou ušetřit peníze.

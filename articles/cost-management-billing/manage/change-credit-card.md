---
title: Změna platební karty pro Azure
description: Popisuje, jak změnit platební kartu, kterou používáte k úhradě předplatného Azure.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: banders
ms.openlocfilehash: f773c964d900c45a51eac433c3616a6e4b8978ae
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2020
ms.locfileid: "92131135"
---
# <a name="add-or-update-a-credit-card-for-azure"></a>Přidání nebo aktualizace platební karty pro Azure

Tento dokument je určený pro zákazníky, kteří si zaregistrovali Azure online pomocí platební karty.

Na webu Azure Portal můžete změnit výchozí způsob platby na novou platební kartu a aktualizovat podrobnosti o platebních kartách. Pokud chcete provést tyto změny, musíte být [Správce účtu](../understand/subscription-transfer.md#whoisaa).

Pokud chcete platební kartu odstranit, přečtěte si téma věnované [odstranění způsobu platby pro fakturaci v Azure](delete-azure-payment-method.md).

Podporované způsoby platby pro Microsoft Azure jsou platební karty a šek nebo bezhotovostní převod. Pokud chcete získat schválení pro platbu šekem nebo bezhotovostním převodem, projděte si téma [Úhrada předplatných Azure pomocí faktury](pay-by-invoice.md).

V případě smlouvy se zákazníkem Microsoftu (MCA) jsou způsoby platby přidružené k fakturačním profilům. Projděte si, jak [zkontrolovat přístup ke smlouvě se zákazníkem Microsoftu](#check-the-type-of-your-account). Pokud máte MCA, přejděte ke [správě platebních karet pro smlouvu se zákazníkem Microsoftu](#manage-credit-cards-for-a-microsoft-customer-agreement).

<a id="addcard"></a>

## <a name="manage-credit-cards-for-an-azure-subscription"></a>Správa platebních karet pro předplatné Azure

Následující části se vztahují na zákazníky, kteří mají fakturační účet programu MOSP (Microsoft Online Services Program). Zjistěte, jak [zkontrolovat typ vašeho fakturačního účtu](#check-the-type-of-your-account). Pokud je váš fakturační účet typu MOSP (Microsoft Online Services Program), jsou způsoby platby přidružené k jednotlivým předplatným Azure. Pokud po přidání platební karty dojde k chybě, přečtěte si téma věnované [odmítnutí platební karty při registraci do Azure](./troubleshoot-declined-card.md).

### <a name="change-credit-card-for-a-subscription-by-adding-a-new-credit-card"></a>Změna platební karty pro předplatné (přidání nové platební karty)

Jako výchozí platební kartu pro předplatné Azure můžete zadat novou platební kartu, nebo kartu, kterou jste si už uložili na webu Azure Portal. Abyste mohli provést změnu platební karty, musíte být správce účtu. Pokud má více předplatných stejný aktivní způsob platby, změnou aktivního způsobu platby u kteréhokoli z těchto předplatných zároveň aktualizujete aktivní způsob platby u ostatních předplatných.

Výchozí platební kartu předplatného můžete změnit na novou pomocí následujících kroků:

1. Přihlaste se na web [Azure Portal](https://portal.azure.com) jako správce účtu.
1. Vyhledejte položku **Správa nákladů a fakturace**.  
    ![Snímek obrazovky znázorňující hledání](./media/change-credit-card/search.png)
1. Vyberte předplatné, ke kterému chcete přidat platební kartu.
1. Vyberte **Způsoby platby**.  
    ![Snímek obrazovky s vybranou možností Spravovat způsoby platby](./media/change-credit-card/payment-methods-blade-x.png)
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
    ![Snímek obrazovky s vybranou možností Spravovat způsoby platby](./media/change-credit-card/payment-methods-blade-x.png)
1. Zaškrtněte políčko vedle karty, kterou chcete nastavit jako aktivní způsob platby.
1. Vyberte **Nastavit jako aktivní**.
    ![Snímek obrazovky znázorňující vybranou platební kartu a její nastavení jako aktivní](./media/change-credit-card/sub-change-active-x.png)

### <a name="edit-credit-card-details"></a>Zadání údajů platební karty

Pokud dojde k obnovení vaší platební karty a její číslo zůstane stejné, můžete aktualizovat údaje stávající platební karty, jako je datum vypršení platnosti. Pokud se číslo vaší karty změní, třeba z důvodu ztráty, krádeže nebo vypršení platnosti karty, postupujte podle návodu v části [Přidání platební karty jako způsobu platby](#addcard). Kód CVV není nutné aktualizovat.

1. Přihlaste se na web [Azure Portal](https://portal.azure.com) jako správce účtu.
1. Vyhledejte položku **Správa nákladů a fakturace**.
    ![Snímek obrazovky znázorňující hledání](./media/change-credit-card/search.png)
1. Vyberte **Způsoby platby**.
    ![Snímek obrazovky s vybranou možností Spravovat způsoby platby](./media/change-credit-card/payment-methods-blade-x.png)
1. Vyberte platební kartu, kterou chcete upravit. Na pravé straně se zobrazí formulář platební karty.
    ![Snímek obrazovky s vybranou platební kartou](./media/change-credit-card/edit-card-x.png)
1. Aktualizujte údaje platební karty.
1. Vyberte **Uložit**.

## <a name="manage-credit-cards-for-a-microsoft-customer-agreement"></a>Správa platebních karet pro smlouvu se zákazníkem Microsoftu

Následující části se vztahují na zákazníky, kteří mají smlouvu se zákazníkem Microsoftu a zaregistrovali si Azure online pomocí platební karty. [Další informace o postupu při kontrole, jestli máte smlouvu se zákazníkem Microsoftu](#check-the-type-of-your-account).

### <a name="change-default-credit-card"></a>Změna výchozí platební karty

Pokud máte smlouvu se zákazníkem Microsoftu, je vaše platební karta přidružená k fakturačnímu profilu. Způsob platby pro určitý fakturační profil může změnit jenom osoba, která provedla registraci v Azure a vytvořila fakturační účet.

Pokud chcete jako výchozí způsob platby pro váš fakturační profil nastavit šek nebo bezhotovostní převod, projděte si téma [Úhrada předplatných Azure pomocí faktury](pay-by-invoice.md).

Pokud chcete změnit platební kartu, postupujte podle následujících kroků:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Vyhledejte **Cost Management a fakturace**.
1. V nabídce nalevo vyberte **Fakturační profily**.
1. Vyberte fakturační profil.
1. V nabídce na levé straně vyberte **Způsoby platby**.  
   ![Snímek obrazovky s možností Způsoby platby v nabídce](./media/change-credit-card/payment-methods-tab-mca.png)
1. V části **Výchozí způsob platby** vyberte **Nahradit**.  
    :::image type="content" source="./media/change-credit-card/change-payment-method-mca.png" alt-text="Snímek obrazovky s možností nahrazení" :::
1. V nové oblasti napravo buď vyberte existující kartu z rozevíracího seznamu, nebo přidejte novou výběrem modrého odkazu **Přidat nový způsob platby**.

### <a name="edit-a-credit-card"></a>Úprava platební karty

Podrobnosti o platební kartě (například aktualizace data vypršení platnosti) můžete upravit na portálu Azure Portal. 

Pokud chcete upravit platební kartu, postupujte podle následujících kroků:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Vyhledejte **Cost Management a fakturace**.
1. V nabídce nalevo vyberte **Fakturační profily**.
1. Vyberte fakturační profil.
1. V nabídce na levé straně vyberte **Způsoby platby**.  
   ![Snímek obrazovky s možností Způsoby platby v nabídce](./media/change-credit-card/payment-methods-tab-mca.png)
1. V části **Vaše platební karty** vyhledejte platební kartu, kterou chcete upravit.
1. Vyberte tři tečky (`...`) na konci příslušného řádku.  
    :::image type="content" source="./media/change-credit-card/edit-delete-credit-card-mca.png" alt-text="Snímek obrazovky ukazující tři tečky" :::
1. Pokud chcete upravovat podrobnosti platební karty, v místní nabídce vyberte **Upravit**.

## <a name="troubleshooting"></a>Řešení potíží

Azure nepodporuje virtuální ani předplacené karty. Pokud při přidávání nebo aktualizaci platné platební karty dochází k chybám, zkuste otevřít prohlížeč v privátním režimu.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

V následujících částech najdete odpovědi na nejčastější dotazy týkající se změny údajů platebních karet.

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
- Pokud chcete platební kartu odstranit, přečtěte si téma věnované [odstranění způsobu platby pro fakturaci v Azure](delete-azure-payment-method.md).
---
title: Úhrada předplatných Azure pomocí faktury
description: Tento článek popisuje, jak zaplatit předplatná Azure pomocí faktury.
author: bandersmsft
manager: jureid
tags: billing
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/23/2019
ms.author: banders
ms.openlocfilehash: a17ffab72163768a59da182204bb207003f5f774
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74223865"
---
# <a name="pay-for-your-azure-subscription-by-invoice"></a>Úhrada předplatných Azure pomocí faktury

Pokud přepnete na platby pomocí faktur, znamená to, že budete hradit faktury prostřednictvím šeku nebo bezhotovostního převodu do 30 dnů od jejich vystavení. Pokud chcete získat nárok na placení předplatného Azure pomocí faktur, odešlete žádost na podporu Azure. Po schválení vaší žádosti můžete přepnout na platby pomocí faktur (šekem nebo převodem) na webu[Azure Portal](https://portal.azure.com).

> [!IMPORTANT]
> * Platby pomocí faktur (šekem nebo bezhotovostním převodem) jsou dostupné jen pro podnikové účty.
> * Před přepnutím na tento způsob plateb je potřeba uhradit všechny nezaplacené poplatky.
> * Platby pomocí faktur nejsou v současnosti podporované globální službou Azure v Číně.

## <a name="request-to-pay-by-invoice"></a>Podání žádosti o platby pomocí faktur

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/). Vyberte **Nápověda a podpora** > **Nová žádost o podporu**.

    ![Odkaz na nápovědu a podporu](./media/billing-how-to-pay-by-invoice/help-and-support.png)

2. Jako **Typ problému** vyberte **Fakturace**. *Typ problému* představuje kategorii žádosti o podporu. Vyberte předplatné, které chcete hradit pomocí faktur, vyberte plán podpory a pak zvolte **Další**.

3. Jako **Typ problému** vyberte **Payment** (Platba). *Typ problému* představuje podkategorii žádosti o podporu.

4. Jako **Podtyp problému** vyberte **Switch to Pay by Invoice** (Přepnout na platby pomocí faktur).

5. Do pole **Podrobnosti** zadejte následující informace a potom vyberte **Další**.

         New or existing customer:
         If existing, current payment method:
         Order ID (requesting for invoice option):
         Account Admins Live ID (or Org ID) (should be company domain):
         Commerce Account ID:
         Company Name (as registered under VAT or Government Website):
         Company Address (as registered under VAT or Government Website):
         Company Website:
         Country:
         TAX ID/ VAT ID:
         Company Established on (Year):
         Any prior business with Microsoft:
         Contact Name:
         Contact Phone:
         Contact Email:
         Justification on why you prefer Invoice option over credit card:

        For cores increase, provide the following additional information:

         (Old quota) Existing Cores:
         (New quota) Requested cores:
         Specific region & series of Subscription:

    - Položky **Company name** (Název společnosti) a **Company address** (Adresa společnosti) by se měly shodovat s informacemi, které jste zadali v účtu Azure. Pokud chcete informace zobrazit nebo aktualizovat, přečtěte si téma [Změna informací o profilu účtu Azure](billing-how-to-change-azure-account-profile.md).
    - Před schválením kreditního limitu je potřeba přidat na webu Azure Portal kontaktní údaje pro fakturaci. Mělo by se jednat o údaje oddělení závazků nebo financí vaší společnosti. Pokud chcete kontaktní údaje pro fakturaci aktualizovat, přejděte do [centra účtů Azure](https://account.azure.com/Profile).

6. Zkontrolujte své kontaktní informace a upřednostňovaný způsob kontaktování a pak klikněte na **Vytvořit**.

Pokud bude nutné provést ověření kredibility vzhledem k potřebnému množství kreditů, pošleme vám žádost o ověření kredibility.

## <a name="switch-to-invoice-pay-checkwire-transfer"></a>Přepnutí na platby pomocí faktur (šekem nebo převodem)

Po schválení plateb můžete na platby pomocí faktur (šekem nebo převodem) přepnout na webu Azure Portal.

Pokud máte účet programu Microsoft Online Services, můžete své předplatné Azure přepnout na platby šekem nebo převodem. Pokud máte smlouvu se zákazníkem Microsoftu, můžete na platby šekem nebo převodem přepnout svůj fakturační profil. [Zjistěte, jak zkontrolovat typ vašeho účtu](#check-access-to-a-microsoft-customer-agreement).

### <a name="switch-azure-subscription-to-checkwire-transfer"></a>Přepnutí předplatného Azure na platby šekem nebo převodem

Pokud chcete přepnout předplatné Azure na platby pomocí faktur (šekem nebo převodem), postupujte podle kroků níže. **Po přepnutí na platby pomocí faktur není možné přepnout zpět na platby pomocí platební karty**.

1. Přihlaste se na web [Azure Portal](https://portal.azure.com) jako správce účtu.
1. Vyhledejte položku **Správa nákladů a fakturace**.

    ![Snímek obrazovky znázorňující hledání](./media/billing-how-to-pay-by-invoice/search.png)

1. Vyberte předplatné, které chcete přepnout na platby pomocí faktur.
1. Vyberte **Způsoby platby**.
1. Na panelu příkazů klikněte na tlačítko **Zaplatit fakturou**.

    ![Snímek obrazovky s tlačítkem Platby pomocí faktur](./media/billing-how-to-pay-by-invoice/pay-by-invoice.png)

### <a name="switch-billing-profile-to-checkwire-transfer"></a>Přepnutí fakturačního profilu na platby šekem nebo převodem

Pokud chcete přepnout fakturační profil na platby šekem nebo převodem, postupujte podle kroků níže. Výchozí způsob platby pro určitý fakturační profil může změnit jen osoba, která provedla registraci v Azure.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Vyhledejte **Cost Management a fakturace**.
1. V nabídce na levé straně klikněte na **Fakturační profily**.

    ![Snímek obrazovky s možností Fakturační profily v nabídce](./media/billing-how-to-pay-by-invoice/billing-profile.png)

1. Zvolte fakturační profil.
1. V nabídce na levé straně vyberte **Způsoby platby**.

   ![Snímek obrazovky s možností Způsoby platby v nabídce](./media/billing-how-to-pay-by-invoice/billing-profile-payment-methods.png)

1. Klikněte na modrý banner, který oznamuje, že máte nárok na platby šekem nebo převodem.

    ![Snímek obrazovky s modrým bannerem pro přepnutí na platby šekem nebo převodem](./media/billing-how-to-pay-by-invoice/customer-led-switch-to-invoice.png)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrola přístupu ke smlouvě se zákazníkem Microsoftu
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky

- V případě potřeby aktualizujte své kontaktní údaje pro fakturaci v [centru účtů Azure](https://account.azure.com/Profile).

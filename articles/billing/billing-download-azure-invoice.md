---
title: Zobrazit a stáhnout fakturu Microsoft Azure
description: Popisuje, jak zobrazit a stáhnout Microsoft Azure fakturu.
keywords: faktura fakturace, stažení faktury, faktura Azure, využití Azure
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: banders
ms.openlocfilehash: 4dc5f88f7a5994e5bcd50d71c86bf1ba35b10734
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2019
ms.locfileid: "68321769"
---
# <a name="view-and-download-your-microsoft-azure-invoice"></a>Zobrazit a stáhnout fakturu Microsoft Azure

U většiny předplatných si můžete stáhnout fakturu z [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) nebo ji nechat odeslat e-mailem. Pokud jste zákazníkem Azure s smlouva Enterpriseem (zákazníkem EA), nemůžete stáhnout faktury vaší organizace. Faktury se odesílají na sebe, která je nastavená tak, aby přijímala faktury pro registraci.

Pouze některé role mají oprávnění k zobrazení faktur. Například správce účtu nebo správce podniku. Další informace o získání přístupu k fakturačním informacím najdete v tématu [Správa přístupu k fakturaci Azure pomocí rolí](billing-manage-access.md).

Pokud máte zákaznickou [smlouvu Microsoftu](#check-your-access-to-a-microsoft-customer-agreement), musíte mít k dispozici jednu z následujících rolí, abyste mohli získat své faktury:

- Vlastník fakturačního profilu
- Přispěvatel
- Čtenář
- Správce faktur

Další informace o fakturačních rolích pro smlouvy o zákaznících Microsoftu najdete v tématu [role a úlohy fakturačního profilu](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="noinvoice"></a>Proč nemůžete získat fakturu

Faktura se nemusí zobrazovat z několika důvodů:

- Od vytvoření vašeho předplatného Azure uplynulo méně než 30 dnů.

- Azure se účtuje na konci období faktury. Takže faktura ještě nemusí být vygenerována. Počkejte na konec fakturačního období.

- Nemáte oprávnění k zobrazení faktur. Pokud máte smlouvu o zákaznících Microsoftu, musíte být vlastníkem fakturačního profilu, přispěvatelem, čtenářem nebo správcem faktur. U jiných předplatných se nemusí zobrazovat staré faktury, pokud nejste správcem účtu. Další informace o získání přístupu k fakturačním údajům najdete v článku o [správě přístupu k fakturaci Azure pomocí rolí](billing-manage-access.md).

- Pokud máte k dispozici bezplatnou zkušební verzi nebo měsíční kredit v rámci vašeho předplatného, dostanete fakturu jenom tehdy, když překročíte částku měsíčního kreditu. Pokud máte smlouvu o zákaznících Microsoftu, vždycky obdržíte fakturu.

## <a name="download-your-azure-invoices-pdf"></a>Stáhnout faktury Azure (. PDF)

U většiny předplatných si můžete stáhnout svou fakturu z Azure Portal. Pokud máte smlouvu o zákaznících Microsoftu, přečtěte si téma [stažení faktur pro smlouvu o zákaznících Microsoftu](#download-invoices-for-a-microsoft-customer-agreement).

### <a name="download-invoices-for-an-individual-subscription"></a>Stažení faktur pro jednotlivé předplatné

1. Vyberte své předplatné na [stránce Předplatná](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) v Azure Portal jako [uživatel s přístupem k fakturám](billing-manage-access.md).

2. Vyberte **Faktury**.

    ![Snímek obrazovky zobrazující možnost použití & fakturace](./media/billing-download-azure-invoice-daily-usage-date/billingandusage.png)

3. Kliknutím na **stáhnout fakturu** zobrazíte kopii vaší faktury ve formátu PDF. Pokud **není k dispozici**, přečtěte si téma Proč se mi nezobrazuje [Faktura za poslední fakturační období?](#noinvoice)

    ![Snímek obrazovky zobrazující fakturační období, možnost stažení a celkové poplatky za každé fakturační období](./media/billing-download-azure-invoice-daily-usage-date/billing4.png)

4. Můžete si také prohlédnout denní využití kliknutím na fakturační období.

Další informace o vaší faktuře najdete v tématu informace o [vyúčtování Microsoft Azure](billing-understand-your-bill.md). Nápovědu ke správě nákladů najdete v tématu [Ochrana před neočekávanými náklady pomocí fakturace a správy nákladů Azure](billing-getting-started.md).

### <a name="download-invoices-for-a-microsoft-customer-agreement"></a>Stažení faktur pro smlouvu o zákaznících Microsoftu

Faktury se generují pro každý [Fakturační profil](billing-mca-overview.md#billing-profiles) v rámci smlouvy o zákaznících Microsoftu. Faktury z Azure Portal lze stáhnout pouze v případě, že jste vlastníkem fakturačního profilu, přispěvatele, čtenář nebo správce faktury.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Vyhledejte *cost management a fakturaci*.
1. Vyberte fakturační profil. V závislosti na vašem přístupu možná budete muset nejdřív vybrat fakturační účet.
1. Vyberte **Faktury**.
1. V mřížce faktury vyhledejte řádek faktury, kterou chcete stáhnout.
1. Klikněte na tlačítko se`...`třemi tečkami () na konci řádku.
    ![Snímek obrazovky zobrazující tři tečky na konci řádku](./media/billing-download-azure-invoice/billingprofile-invoicegrid.png)
1. V místní nabídce pro stažení vyberte možnost **Faktura**.

    ![Snímek obrazovky, který zobrazuje kontextovou nabídku](./media/billing-download-azure-invoice/contextmenu.png)

Pokud nevidíte fakturu za poslední fakturační období, přečtěte si téma Proč se mi nezobrazuje [Faktura za poslední fakturační období?](#noinvoice)

## <a name="get-your-invoice-in-email-pdf"></a>Získat fakturu v e-mailu (. PDF)

Můžete se rozhodnout a nakonfigurovat další příjemce pro příjem faktury Azure v e-mailu. Tato funkce nemusí být k dispozici pro určitá předplatná, jako jsou nabídky podpory, smlouvy Enterprise nebo Systém Azure v rámci licenčního programu Open. Pokud máte smlouvu o zákaznících Microsoftu, přečtěte si další část a [Získejte faktury fakturačního profilu v e-mailu](#get-your-subscriptions-invoices-in-email).

### <a name="get-your-subscriptions-invoices-in-email"></a>Získat faktury předplatného v e-mailu

1. Na [stránce Předplatná](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)vyberte své předplatné. Zaregistrujte se u každého předplatného, které vlastníte. Klikněte na **faktury** a **odešlete e-mailovou fakturu**.

    ![Snímek obrazovky zobrazující tok výslovných přihlášení](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)

2. Klikněte na možnost **vyjádřit** se a přijměte podmínky.

    ![Snímek obrazovky, který zobrazuje krok pro průběh výslovných přihlášení. 2](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)

3. Po přijetí smlouvy můžete nakonfigurovat další příjemce. Po odebrání příjemce se e-mailová adresa už neukládá. Pokud si to rozmyslíte, musíte si je přečíst.

    ![Snímek obrazovky, na kterém se zobrazuje tok výslovných přihlášení krok 3](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)

Pokud neobdržíte e-mail po následujících krocích, ujistěte se, že je vaše e-mailová adresa správná v [preferencích komunikace na vašem profilu](https://account.windowsazure.com/profile).

### <a name="opt-out-of-getting-your-subscriptions-invoices-in-email"></a>Odhlášení od získání faktury předplatného v e-mailu

Pokud se chcete odhlásit od získání faktury e-mailem, postupujte podle předchozích kroků a klikněte na **výslovný souhlas z e-mailových faktur**. Tato možnost odebere všechny e-mailové adresy, pro které je nastavené zasílání faktur e-mailem. Pokud se znovu rozhodnete, můžete příjemce znovu nakonfigurovat.

 ![Snímek obrazovky zobrazující tok odhlášení](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep4.PNG)

### <a name="get-your-microsoft-customer-agreement-invoices-in-email"></a>Stažení faktury zákaznických smluv Microsoftu v e-mailu

Pokud máte smlouvu o zákaznících Microsoftu, můžete se přihlásit k možnosti získat svou fakturu v e-mailu. Všichni vlastníci fakturačního profilu, přispěvatelé, čtenáři a správci faktur obdrží fakturu e-mailem. Čtenáři nemůžou aktualizovat předvolby e-mailové faktury.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Vyhledejte **cost management a fakturaci**.
1. Vyberte fakturační profil. V závislosti na vašem přístupu možná budete muset nejdřív vybrat fakturační účet.
1. V části **Nastavení**vyberte **vlastnosti**.
1. V části **e-mailová faktura**vyberte možnost **aktualizovat emailovou fakturu předvolby**.

    ![Snímek obrazovky, který zobrazuje vlastnosti e-mailové faktury](./media/billing-download-azure-invoice/billingprofile-email.png)

1. Vyberte možnost přihlásit **se**.
1. Klikněte na tlačítko **aktualizace**.

### <a name="opt-out-of-getting-your-microsoft-customer-agreement-invoices-in-email"></a>Výslovný souhlas se získáním faktury od zákazníků Microsoftu v e-mailu

Pokud se chcete odhlásit od získání faktury e-mailem, postupujte podle předchozích kroků **a klikněte na**odhlásit. U všech vlastníků, přispěvatelů, čtenářů a správců faktur se na základě e-mailu neúčtují také faktury. Pokud jste čtenář, nemůžete změnit předvolby e-mailové faktury.



## <a name="check-your-access-to-a-microsoft-customer-agreement"></a>Podívejte se na váš přístup k zákaznickým smlouvám Microsoftu
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Potřebujete pomoct? Kontaktujte nás.

Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další postup

Další informace o vaší faktuře a poplatkůch najdete tady:

- [Zobrazení a stažení Microsoft Azure používání a poplatků](billing-download-azure-daily-usage.md)
- [Vysvětlení informací na faktuře za Microsoft Azure](billing-understand-your-bill.md)
- [Porozumění podmínkám na faktuře Azure](billing-understand-your-invoice.md)
- [Vysvětlení podmínek Microsoft Azure podrobného využití](billing-understand-your-usage.md)
- [Zobrazení cen za Azure ve vaší organizaci](billing-ea-pricing.md)

Pokud máte zákaznickou smlouvu Microsoftu, přečtěte si:

- [Pochopení poplatků za fakturační profil](billing-mca-understand-your-bill.md)
- [Porozumění podmínkám na faktuře pro fakturační profil](billing-mca-understand-your-invoice.md)
- [Seznamte se s využitím a poplatkovým souborem Azure pro fakturační profil](billing-mca-understand-your-usage.md)
- [Zobrazení a stažení daňových dokumentů pro fakturační profil](billing-mca-download-tax-document.md)
- [Zobrazení cen za Azure ve vaší organizaci](billing-ea-pricing.md)

---
title: Zobrazit a stáhnout fakturu Microsoft Azure | Dokumentace Microsoftu
description: Popisuje, jak zobrazit a stáhnout fakturu Microsoft Azure
keywords: fakturu, stažení faktury, azure faktury, využití azure
services: billing
documentationcenter: ''
author: genlin
manager: jureid
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: f71fe9b02765e0fc8fd5f3b7abbd54c87b08132f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60617919"
---
# <a name="view-and-download-your-microsoft-azure-invoice"></a>Zobrazit a stáhnout fakturu Microsoft Azure

Pro většinu předplatných si můžete stáhnout fakturu z [webu Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) nebo odeslat e-mailu. Pokud jste zákazník Azure se smlouvou Enterprise (EA zákazníků), nemůže stahovat faktury vaší organizace. Kdo je nastaven na přijímání faktur pro registraci se posílají faktury.

Pouze určité role mají oprávnění k zobrazení faktur, jako je účet správce nebo správce podnikové sítě. Další informace o získání přístupu k fakturačním údajům najdete v článku o [správě přístupu k fakturaci Azure pomocí rolí](billing-manage-access.md).

Pokud máte [smlouvy zákazníka se společností Microsoft](#check-your-access-to-a-microsoft-customer-agreement), musí být fakturační profil vlastník, Přispěvatel, čtenář, nebo fakturovat zástupce, který vám vašich fakturách se projeví. Další informace o rolích fakturace pro smlouvy Microsoft zákazníka najdete v tématu [fakturace profilu role a úlohy](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="download-your-azure-invoices-pdf"></a>Stažení faktury Azure (PDF)

Pro většinu předplatných si můžete stáhnout z webu Azure portal faktuře. Pokud máte smlouvu Microsoft zákazníka, naleznete v tématu ke stažení faktury pro fakturační profil.

### <a name="download-invoices-for-an-individual-subscription"></a>Stažení faktury pro samostatného předplatného

1. Vyberte své předplatné z [stránce předplatná](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) na webu Azure Portal jako [uživatele s přístupem na fakturách](billing-manage-access.md).

2. Vyberte **Faktury**.

    ![Snímek obrazovky zobrazující možnost fakturace a využití](./media/billing-download-azure-invoice-daily-usage-date/billingandusage.png)

3. Klikněte na tlačítko **stáhnout fakturu** zobrazíte kopii faktury ve formátu PDF. Při stavu **není k dispozici**, naleznete v tématu [Proč nevidím faktury pro poslední fakturačního období?](#noinvoice)

    ![Snímek obrazovky s fakturační období, možnost stažení a celkové náklady pro každého fakturačního období](./media/billing-download-azure-invoice-daily-usage-date/billing4.png)

4. Denní využití můžete zobrazit také kliknutím fakturačního období.

Další informace o vaší faktuře najdete v tématu [vysvětlení vašeho vyúčtování služeb Microsoft Azure](billing-understand-your-bill.md). Pomoci se správou nákladů, naleznete v tématu [jak zabránit neočekávaným nákladům se správou nákladů a fakturací Azure](billing-getting-started.md).

### <a name="download-invoices-for-a-microsoft-customer-agreement"></a>Stažení faktury zákaznické smlouvy Microsoft

Faktury se generují pro každou [fakturační profil](billing-mca-overview.md#understand-billing-profiles) smlouvy zákazníka Microsoftu. Musí být fakturační profil vlastník, Přispěvatel, čtenář, nebo fakturovat správce stahovat faktury z portálu Azure portal.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Vyhledejte **Cost Management a fakturace**.
1. Vyberte fakturační profil. V závislosti na přístup potřebujete nejprve vybrat fakturační účet.
1. Vyberte **Faktury**.
1. V mřížce faktury najde řádek faktury, kterou chcete stáhnout.
1. Klikněte na symbol tří teček (`...`) na konci řádku.
    ![Snímek obrazovky zobrazující na tři tečky na konci řádku](./media/billing-download-azure-invoice/billingprofile-invoicegrid.png)
1. V místní nabídce stahování vyberte **faktury**.

    ![Snímek obrazovky zobrazující kontextové nabídky](./media/billing-download-azure-invoice/contextmenu.png)

Pokud nevidíte fakturu za poslední fakturační období, přečtěte si téma [Proč nevidím faktury pro poslední fakturačního období?](#noinvoice)

## <a name="get-your-invoice-in-email-pdf"></a>Získání faktury v e-mailu (PDF)

Můžete vyjádřit výslovný souhlas a nakonfigurovat další příjemce pro příjem Azure faktury e-mailem. Tato funkce nemusí být k dispozici pro určitá předplatná, jako jsou nabídky podpory, smlouvy Enterprise nebo Azure v programu Open. Pokud máte smlouvu Microsoft Customer, přečtěte si téma Get fakturační profil zasílání faktur e-mailem.

### <a name="get-your-subscriptions-invoices-in-email"></a>Zasílání faktur vašeho předplatného na e-mail

1. Vyberte své předplatné z [stránce předplatná](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Vyjádřit výslovný souhlas pro každé předplatné, které vlastníte. Klikněte na tlačítko **faktury** pak **e-mailu mé faktury**.

    ![Snímek obrazovky zobrazující tok vyjádření souhlasu](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)

2. Klikněte na tlačítko **vyjádřit výslovný souhlas** a přijměte podmínky.

    ![Snímek obrazovky zobrazující tok vyjádření souhlasu se krok 2](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)
 
3. Jakmile jste přijali smlouvu, můžete nakonfigurovat další příjemce. Při odebrání příjemce e-mailová adresa je již uloženy. Pokud změníte své rozhodnutí, budete muset znovu přidat.

    ![Snímek obrazovky zobrazující tok vyjádření souhlasu se krok 3](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)

Pokud e-mail neobdržíte po provedení kroků, ujistěte se, že je správný v e-mailovou adresu [předvolby komunikace ve vašem profilu](https://account.windowsazure.com/profile).

### <a name="opt-out-of-getting-your-subscriptions-invoices-in-email"></a>Vyjádřit výslovný nesouhlas získání faktury vaše předplatné v e-mailu

Získání faktury e-mailem podle pokynů výše a kliknutím na tlačítko můžete zrušit **vyjádřit výslovný nesouhlas faktury e-mailem**. Tato možnost odebere všechny e-mailové adresy, pro které je nastavené zasílání faktur e-mailem. Pokud se připojíte zpět můžete překonfigurovat příjemce.

 ![Snímek obrazovky zobrazující tok výslovného nesouhlasu s](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep4.PNG)

### <a name="get-your-microsoft-customer-agreement-invoices-in-email"></a>Získání faktury smlouvy zákazníka se společností Microsoft v e-mailu

Pokud máte smlouvu Microsoft zákazníka, můžete přejít k získání faktury e-mailem. Všechny fakturační profil vlastníci, přispěvatelé, čtenáři a faktury Správci získají faktury e-mailem. Čtenáři nelze aktualizovat předvolby faktury e-mailu.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Vyhledejte **Cost Management a fakturace**.
1. Vyberte fakturační profil. V závislosti na přístup potřebujete nejprve vybrat fakturační účet.
1. V části **nastavení**vyberte **vlastnosti**.
1. V části **fakturu e-mailem**vyberte **předvolby faktury e-mailu aktualizace**.

    ![Snímek obrazovky, který ukazuje vlastnosti faktury e-mailů](./media/billing-download-azure-invoice/billingprofile-email.png)

1. Vyberte **vyjádřit výslovný souhlas**.
1. Klikněte na tlačítko **aktualizace**.

### <a name="opt-out-of-getting-your-microsoft-customer-agreement-invoices-in-email"></a>Vyjádřit výslovný nesouhlas získání vašich fakturách se projeví smlouvy zákazníka se společností Microsoft v e-mailu

Získání faktury e-mailem podle pokynů výše a kliknutím na tlačítko můžete zrušit **Odhlásit se totiž**. Vlastníci, přispěvatelé, čtenáři a faktury správci účast získání faktury e-mailem, příliš. Pokud jste čtečku, nelze změnit předvolby faktury e-mailu.

### <a name="noinvoice"></a> Proč nevidím faktury pro poslední fakturačního období?

Faktura se nemusí zobrazovat z několika důvodů:

- Od vytvoření vašeho předplatného Azure uplynulo méně než 30 dnů.

- Faktura se zatím nevygenerovala. Počkejte na konec fakturačního období.

- Nemáte oprávnění k zobrazení faktur. Pokud máte smlouvu Microsoft zákazníků, musí být fakturační profil vlastník, Přispěvatel, čtenář, nebo správce fakturace. Pro další předplatná nemusíte to vidět staré faktury Pokud si nejste správce účtu. Další informace o získání přístupu k fakturačním údajům najdete v článku o [správě přístupu k fakturaci Azure pomocí rolí](billing-manage-access.md).

- Pokud už máte bezplatnou zkušební verzi nebo měsíční částku kreditu s vaším předplatným, které byste neměli překročit, nezískáte faktury, pokud máte smlouvu Microsoft zákazníka.

## <a name="check-your-access-to-a-microsoft-customer-agreement"></a>Zkontrolujte svůj přístup na základě smlouvy Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás

Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další postup

Další informace týkající se faktury a náklady, naleznete v tématu:

- [Zobrazovat a stahovat využití Microsoft Azure a poplatky za](billing-download-azure-daily-usage.md)
- [Vysvětlení informací na faktuře za Microsoft Azure](billing-understand-your-bill.md)
- [Vysvětlení podmínek na faktuře Azure](billing-understand-your-invoice.md)
- [Vysvětlení podmínek na Microsoft Azure podrobné využití](billing-understand-your-usage.md)
- [Prohlédněte si ceny Azure vaší organizace](billing-ea-pricing.md)

Pokud máte smlouvu Microsoft zákazníka, naleznete v tématu:

- [Principy poplatků za na fakturu za fakturační profil](billing-mca-understand-your-bill.md)
- [Vysvětlení podmínek na faktuře pro váš fakturační profil](billing-mca-understand-your-invoice.md)
- [Principy Azure file využití a poplatků pro váš fakturační profil](billing-mca-understand-your-usage.md)
- [Zobrazovat a stahovat daňové doklady pro váš fakturační profil](billing-mca-download-tax-document.md)
- [Prohlédněte si ceny Azure vaší organizace](billing-ea-pricing.md)

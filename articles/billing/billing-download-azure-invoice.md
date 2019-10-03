---
title: Zobrazit a stáhnout fakturu Microsoft Azure
description: Vysvětluje, jak zobrazit a stáhnout fakturu za Microsoft Azure.
keywords: billing invoice, invoice download, azure invoice, azure usage
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 862ec629504da4d8ee7eadd8f9b925984d96614c
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71718897"
---
# <a name="view-and-download-your-microsoft-azure-invoice"></a>Zobrazit a stáhnout fakturu Microsoft Azure

U většiny předplatných si můžete fakturu stáhnout z webu [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) nebo si ji nechat poslat e-mailem. Pokud jste zákazníkem Azure se smlouvou Enterprise (zákazníkem EA), nemůžete stahovat faktury vaší organizace. Faktury se odesílají osobě, která je nastavená jako příjemce faktur pro danou smlouvu.

K zobrazení faktur mají oprávnění pouze určité role. Například správce účtu nebo podnikový správce. Další informace o získání přístupu k fakturačním údajům najdete v tématu [Správa přístupu k fakturaci Azure pomocí rolí](billing-manage-access.md).

Pokud máte [smlouvu se zákazníkem Microsoftu](#check-your-access-to-a-microsoft-customer-agreement) a chcete získat faktury, musíte mít jednu z následujících rolí:

- Vlastník fakturačního profilu
- Přispěvatel
- Čtenář
- Správce faktur

Další informace o fakturačních rolích u smluv se zákazníkem Microsoftu najdete v tématu [Role a úlohy související s fakturačním profilem](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="noinvoice"></a>Proč nemůžete získat fakturu

Faktura se nemusí zobrazovat z několika důvodů:

- Od vytvoření vašeho předplatného Azure uplynulo méně než 30 dnů.

- Azure se fakturuje na konci fakturačního období. Proto se faktura možná ještě nevygenerovala. Počkejte na konec fakturačního období.

- Nemáte oprávnění k zobrazení faktur. Pokud máte smlouvu se zákazníkem Microsoftu, musíte být vlastníkem, přispěvatelem, čtenářem nebo správcem faktur daného fakturačního profilu. U jiných předplatných se může stát, že pokud nejste správcem účtu, nemusí se vám zobrazovat staré faktury. Další informace o získání přístupu k fakturačním údajům najdete v článku o [správě přístupu k fakturaci Azure pomocí rolí](billing-manage-access.md).

- Pokud máte bezplatnou zkušební verzi předplatného nebo předplatné s měsíčním kreditem, faktura se vám vystaví pouze v případě, že překročíte měsíční kredit. Pokud máte smlouvu se zákazníkem Microsoftu, faktura se vám vystaví vždy.

## <a name="download-your-azure-invoices-pdf"></a>Stahování faktur za Azure (PDF)

U většiny předplatných si fakturu můžete stáhnout z webu Azure Portal. Pokud máte smlouvu se zákazníkem Microsoftu, přečtěte si téma [Stahování faktur pro smlouvu se zákazníkem Microsoftu](#download-invoices-for-a-microsoft-customer-agreement).

### <a name="download-invoices-for-an-individual-subscription"></a>Stahování faktur pro jedno předplatné

1. Vyberte své předplatné na stránce [Předplatná](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) na webu Azure Portal jako [uživatel s přístupem k fakturám](billing-manage-access.md).

2. Vyberte **Faktury**.

    ![Snímek obrazovky znázorňující možnost Využití a fakturace](./media/billing-download-azure-invoice-daily-usage-date/billingandusage.png)

3. Pokud chcete zobrazit kopii faktury v PDF, klikněte na **Stáhnout fakturu**. Pokud se zobrazí **Není k dispozici**, přečtěte si téma [Proč se mi nezobrazuje faktura za poslední fakturační období?](#noinvoice)

    ![Snímek obrazovky ukazující fakturační období, možnost stažení a celkové poplatky za každé fakturační období](./media/billing-download-azure-invoice-daily-usage-date/billing4.png)

4. Denní využití můžete zobrazit také kliknutím na fakturační období.

Další informace o své faktuře najdete v tématu [Vysvětlení informací na faktuře za Microsoft Azure](billing-understand-your-bill.md). Pokud potřebujete pomoc se správou nákladů, přečtěte si, [jak zabránit neočekávaným nákladům v rámci fakturace Azure a jak používat správu nákladů](billing-getting-started.md).

### <a name="download-invoices-for-a-microsoft-customer-agreement"></a>Stahování faktur pro smlouvu se zákazníkem Microsoftu

Faktury se generují pro každý [fakturační profil](billing-mca-overview.md#billing-profiles) ve smlouvě se zákazníkem Microsoftu. Pokud chcete stahovat faktury z webu Azure Portal, musíte být vlastníkem, přispěvatelem, čtenářem nebo správcem faktur daného fakturačního profilu.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Vyhledejte *Cost Management a fakturace*.
1. Vyberte fakturační profil. V závislosti na úrovni vašeho přístupu možná budete muset nejprve vybrat fakturační účet.
1. Vyberte **Faktury**.
1. V tabulce faktur najděte řádek s fakturou, kterou chcete stáhnout.
1. Klikněte na tři tečky (`...`) na konci řádku.
    ![Snímek obrazovky ukazující tři tečky na konci řádku](./media/billing-download-azure-invoice/billingprofile-invoicegrid.png)
1. V místní nabídce pro stažení vyberte možnost **Faktura**.

    ![Snímek obrazovky s místní nabídkou](./media/billing-download-azure-invoice/contextmenu.png)

Pokud se nezobrazí faktura za poslední fakturační období, přečtěte si téma [Proč se mi nezobrazuje faktura za poslední fakturační období?](#noinvoice)

## <a name="get-your-invoice-in-email-pdf"></a>Zaslání faktury e-mailem (PDF)

Můžete se přihlásit k zasílání a nakonfigurovat další příjemce, kteří mají dostat vaši fakturu za Azure e-mailem. Tato funkce nemusí být u některých předplatných dostupná, například u nabídek podpory, smluv Enterprise nebo Azure v rámci licenčního programu Open. Pokud máte smlouvu se zákazníkem Microsoftu, přečtěte si následující část [Zasílání faktur pro fakturační profil e-mailem](#get-your-subscriptions-invoices-in-email).

### <a name="get-your-subscriptions-invoices-in-email"></a>Zasílání faktur pro předplatné e-mailem

1. Na stránce [Předplatná](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) vyberte své předplatné. Pro každé předplatné, které vlastníte, se budete muset přihlásit k zasílání. Klikněte na **Faktury** a pak na **Zaslat fakturu e-mailem**.

    ![Snímek obrazovky, který ukazuje průběh přihlášení k zasílání](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)

2. Klikněte na *****Přihlásit** a přijměte podmínky.

    ![Snímek obrazovky, který ukazuje průběh přihlášení k zasílání, krok 2](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)

3. Po přijetí smlouvy můžete nakonfigurovat další příjemce. Když určitého příjemce odeberete, jeho e-mailová adresa se odstraní. Pokud si tento krok později rozmyslíte, je potřeba příjemce znovu přidat.

    ![Snímek obrazovky, který ukazuje průběh přihlášení k zasílání, krok 3](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)

Pokud vám po provedení těchto kroků nepřijde žádný e-mail, ujistěte se, že je v [předvolbách komunikace ve vašem profilu](https://account.windowsazure.com/profile) uvedená správná e-mailová adresa.

### <a name="opt-out-of-getting-your-subscriptions-invoices-in-email"></a>Odhlášení ze zasílání faktur pro předplatné e-mailem

Pokud se chcete odhlásit ze zasílání faktur e-mailem, proveďte výše uvedený postup a klikněte na **Odhlásit ze zasílání faktur e-mailem**. Tato možnost odebere všechny e-mailové adresy, pro které je nastavené zasílání faktur e-mailem. Pokud se k zasílání znovu přihlásíte, můžete znovu nakonfigurovat příjemce.

 ![Snímek obrazovky, který ukazuje průběh odhlášení ze zasílání](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep4.PNG)

### <a name="get-your-microsoft-customer-agreement-invoices-in-email"></a>Zasílání faktur pro smlouvu se zákazníkem Microsoftu e-mailem

Pokud máte smlouvu se zákazníkem Microsoftu, můžete se přihlásit k zasílání faktur e-mailem. Faktura se odešle e-mailem všem vlastníkům, přispěvatelům, čtenářům a správcům faktur daného fakturačního profilu. Čtenáři nemůžou aktualizovat předvolby zasílání faktur e-mailem.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Vyhledejte **Cost Management a fakturace**.
1. Vyberte fakturační profil. V závislosti na úrovni vašeho přístupu možná budete muset nejprve vybrat fakturační účet.
1. V části **Nastavení** vyberte **Vlastnosti**.
1. V části **Poslat fakturu e-mailem** vyberte **Aktualizovat předvolbu odesílání faktury e-mailem**.

    ![Snímek obrazovky ukazující vlastnosti zasílání faktur e-mailem](./media/billing-download-azure-invoice/billingprofile-email.png)

1. Vyberte *****Přihlásit**.
1. Klikněte na **Aktualizovat**.

### <a name="opt-out-of-getting-your-microsoft-customer-agreement-invoices-in-email"></a>Odhlášení ze zasílání faktur pro smlouvu se zákazníkem Microsoftu e-mailem

Pokud se chcete odhlásit ze zasílání faktur e-mailem, proveďte výše uvedený postup a klikněte na **Odhlásit**. Ze zasílání faktur e-mailem se odhlásí i všichni vlastníci, přispěvatelé, čtenáři a správci faktur. Pokud jste čtenář, nemůžete měnit předvolby zasílání faktur e-mailem.



## <a name="check-your-access-to-a-microsoft-customer-agreement"></a>Kontrola přístupu ke smlouvě se zákazníkem Microsoftu
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky

Další informace o vaší faktuře a poplatcích najdete tady:

- [Zobrazení a stažení informací o využití a poplatcích za Microsoft Azure](billing-download-azure-daily-usage.md)
- [Vysvětlení informací na faktuře za Microsoft Azure](billing-understand-your-bill.md)
- [Vysvětlení výrazů na faktuře Azure](billing-understand-your-invoice.md)
- [Vysvětlení výrazů v podrobných informacích o využití Microsoft Azure](billing-understand-your-usage.md)
- [Zobrazení cen Azure pro vaši organizaci](billing-ea-pricing.md)

Pokud máte smlouvu se zákazníkem Microsoftu, projděte si tato témata:

- [Vysvětlení poplatků na faktuře pro váš fakturační profil](billing-mca-understand-your-bill.md)
- [Vysvětlení výrazů na faktuře pro váš fakturační profil](billing-mca-understand-your-invoice.md)
- [Vysvětlení souboru s informacemi o využití a poplatcích za Azure pro váš fakturační profil](billing-mca-understand-your-usage.md)
- [Zobrazení a stažení daňových dokumentů pro váš fakturační profil](billing-mca-download-tax-document.md)
- [Zobrazení cen Azure pro vaši organizaci](billing-ea-pricing.md)

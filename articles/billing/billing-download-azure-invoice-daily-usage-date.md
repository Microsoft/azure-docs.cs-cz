---
title: Stažení faktury za Azure a dat o denním využití | Microsoft Docs
description: Vysvětluje, jak stáhnout nebo zobrazit fakturu za Azure a data o denním využití.
keywords: billing invoice,invoice download,azure invoice,azure usage
services: billing
documentationcenter: ''
author: genlin
manager: adpick
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: 16b2eaef74a7aa0e3e28bfcbb6dbd9da568db6cf
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "67491405"
---
# <a name="download-or-view-your-azure-billing-invoice-and-daily-usage-data"></a>Stažení nebo zobrazení faktury za Azure a dat o denním využití

U většiny předplatných si můžete fakturu stáhnout z webu [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) nebo si ji nechat poslat e-mailem. Pokud jste zákazníkem Azure se smlouvou Enterprise (zákazníkem EA), nemůžete stahovat faktury vaší organizace. Faktury se odesílají osobě, která je nastavená jako příjemce faktur pro danou smlouvu.

Pokud jste zákazníkem se smlouvou Enterprise nebo máte [smlouvu se zákazníkem Microsoftu](#check-access-to-a-microsoft-customer-agreement), můžete si z webu [Azure Portal](https://portal.azure.com/) stáhnout údaje o využití. V případě jiných předplatných si můžete údaje o využití stáhnout v [Centru účtů Azure](https://account.azure.com/Subscriptions).

K získání faktury a informací o využití mají oprávnění jenom některé role, třeba správce účtu nebo podnikový správce. Další informace o získání přístupu k fakturačním údajům najdete v článku o [správě přístupu k fakturaci Azure pomocí rolí](billing-manage-access.md).

Pokud máte smlouvu se zákazníkem Microsoftu a chcete zobrazit informace o fakturaci a využití, musíte být vlastníkem, přispěvatelem, čtenářem nebo správcem faktur daného fakturačního profilu. Další informace o fakturačních rolích u smluv se zákazníkem Microsoftu najdete v tématu [Role a úlohy související s fakturačním profilem](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="download-your-azure-invoices-pdf"></a>Stahování faktur za Azure (PDF)

U většiny předplatných si můžete fakturu stáhnout z webu Azure Portal. Pokud máte smlouvu se zákazníkem Microsoftu, projděte si téma Stahování faktur pro fakturační profil.

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

1. Vyhledejte položku **Správa nákladů a fakturace**.
2. Zvolte fakturační profil.
3. Vyberte **Faktury**.
4. V tabulce faktur najděte řádek s fakturou, kterou chcete stáhnout.
5. Klikněte na tři tečky (`...`) na konci řádku.
6. V místní nabídce pro stažení vyberte možnost **Faktura**.

Pokud nevidíte fakturu za poslední fakturační období, přečtěte si **Další informace**. <!-- Fix this -->
### <a name="noinvoice"></a> Proč se mi nezobrazuje faktura za poslední fakturační období?

Faktura se nemusí zobrazovat z několika důvodů:

- Od vytvoření vašeho předplatného Azure uplynulo méně než 30 dnů.

- Faktura se zatím nevygenerovala. Počkejte na konec fakturačního období.

- Nemáte oprávnění k zobrazení faktur. Pokud máte smlouvu se zákazníkem Microsoftu, musíte být vlastníkem, přispěvatelem, čtenářem nebo správcem faktur daného fakturačního profilu. U jiných předplatných se může stát, že pokud nejste správcem účtu, nemusí se vám zobrazovat staré faktury. Další informace o získání přístupu k fakturačním údajům najdete v článku o [správě přístupu k fakturaci Azure pomocí rolí](billing-manage-access.md).

- Pokud máte bezplatnou zkušební verzi nebo máte v rámci svého předplatného měsíční kredit, který jste ještě nevyčerpali, a zároveň nemáte smlouvu se zákazníkem Microsoftu, nedostanete žádnou fakturu.

## <a name="get-your-invoice-in-email-pdf"></a>Zaslání faktury e-mailem (PDF)

Můžete se přihlásit k zasílání a nakonfigurovat další příjemce, kteří mají dostat vaši fakturu za Azure e-mailem. Tato funkce nemusí být u některých předplatných dostupná, například u nabídek podpory, smluv Enterprise nebo Azure v rámci licenčního programu Open. Pokud máte smlouvu se zákazníkem Microsoftu, přečtěte si téma Zasílání faktur pro fakturační profil e-mailem.

### <a name="get-your-subscriptions-invoices-in-email"></a>Zasílání faktur pro předplatné e-mailem

1. Na stránce [Předplatná](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) vyberte své předplatné. Pro každé předplatné, které vlastníte, se budete muset přihlásit k zasílání. Klikněte na **Faktury** a pak na **Zaslat fakturu e-mailem**.

    ![Snímek obrazovky, který ukazuje průběh přihlášení k zasílání](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)

2. Klikněte na *****Přihlásit** a přijměte podmínky.

    ![Snímek obrazovky, který ukazuje průběh přihlášení k zasílání, krok 2](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)

3. Po přijetí smlouvy můžete nakonfigurovat další příjemce. Když určitého příjemce odeberete, jeho e-mailová adresa se odstraní. Pokud si tento krok později rozmyslíte, je potřeba příjemce znovu přidat.

    ![Snímek obrazovky, který ukazuje průběh přihlášení k zasílání, krok 3](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)

Pokud vám po provedení těchto kroků nepřijde žádný e-mail, ujistěte se, že je v [předvolbách komunikace ve vašem profilu](https://account.windowsazure.com/profile) uvedená správná e-mailová adresa.

### <a name="opt-out-of-getting-your-subscriptions-invoices-in-email"></a>Odhlášení ze zasílání faktur pro předplatné e-mailem

Pokud chcete vyjádřit výslovný nesouhlas s příjmem faktur e-mailem, proveďte výše uvedený postup a klikněte na **Odhlásit ze zasílání faktur e-mailem**. Tato možnost odebere všechny e-mailové adresy, pro které je nastavené zasílání faktur e-mailem. Pokud se k zasílání znovu přihlásíte, můžete znovu nakonfigurovat příjemce.

 ![Snímek obrazovky, který ukazuje průběh odhlášení ze zasílání](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep4.PNG)

### <a name="get-your-microsoft-customer-agreement-invoices-in-email"></a>Zasílání faktur pro smlouvu se zákazníkem Microsoftu e-mailem

Pokud máte smlouvu se zákazníkem Microsoftu, můžete se přihlásit k zasílání a nechat si posílat faktury e-mailem. Faktura se odešle e-mailem všem vlastníkům, přispěvatelům, čtenářům a správcům faktur daného fakturačního profilu. Čtenáři nemůžou aktualizovat předvolby zasílání faktur e-mailem.

1. Vyhledejte položku **Správa nákladů a fakturace**.
1. Zvolte fakturační profil.
1. V části **Nastavení** vyberte **Vlastnosti**.
1. V části **Poslat fakturu e-mailem** vyberte **Aktualizovat předvolbu odesílání faktury e-mailem**.
1. Zvolte *****Přihlásit**.
1. Klikněte na **Aktualizovat**.

### <a name="opt-out-of-getting-your-billing-profile-invoices-in-email"></a>Odhlášení ze zasílání faktur pro fakturační profil e-mailem

Pokud chcete vyjádřit výslovný nesouhlas s příjmem faktur e-mailem, proveďte výše uvedený postup a klikněte na **Odhlásit**. Ze zasílání faktur e-mailem budou odhlášeni i všichni vlastníci, přispěvatelé, čtenáři a správci faktur. Pokud jste čtenář, nemůžete měnit předvolby zasílání faktury e-mailem.

## <a name="download-usage"></a>Stažení dat o využití

 U většiny předplatných najděte soubor s informacemi o denním využití v [Centru účtů Azure](https://account.azure.com/Subscriptions). Pokud jste zákazníkem se smlouvou Enterprise nebo máte smlouvu se zákazníkem Microsoftu, můžete si stáhnout údaje o využití z webu [Azure Portal](https://portal.azure.com/). <!-- TO DO: update PayG experience to Ibiza once it ships-->

### <a name="download-usage-from-the-account-center-csv"></a>Stažení informací o využití z Centra účtů (CSV)

1. Přihlaste se do [Centra účtů Azure](https://account.windowsazure.com/subscriptions) jako správce účtu.

2. Vyberte předplatné, pro které chcete fakturu a informace o využití.

3. Zvolte **Historie fakturace**.

    ![Snímek obrazovky s možností Historie fakturace](./media/billing-download-azure-invoice-daily-usage-date/Billinghisotry.png)

4. Zobrazí se výpisy za posledních šest fakturačních období i za aktuální, ještě nevyfakturované období.

    ![Snímek obrazovky ukazující fakturační období, možnosti stažení faktury a informací o denním využití a celkové poplatky za každé fakturační období](./media/billing-download-azure-invoice-daily-usage-date/billingSum.png)

5. Pokud chcete zobrazit odhadované poplatky k datu generování odhadu, vyberte **Zobrazit aktuální výpis**. Tyto informace se aktualizují jenom jednou denně, takže nemusí obsahovat celkové využití. Vaše měsíční faktura se může od tohoto odhadu lišit.

    ![Snímek obrazovky ukazující možnost Zobrazit aktuální výpis](./media/billing-download-azure-invoice-daily-usage-date/billingSum2.png)

    ![Snímek obrazovky ukazující odhad aktuálních poplatků](./media/billing-download-azure-invoice-daily-usage-date/billingSum3.png)

6. Pokud chcete stáhnout data o denním využití jako soubor CSV, vyberte **Stáhnout informace o využití**. Pokud se zobrazí dvě dostupné verze, stáhněte si verzi 2.

    ![Snímek obrazovky ukazující možnost stažení informací o využití](./media/billing-download-azure-invoice-daily-usage-date/DLusage.png)

K Centru účtů Azure má přístup pouze správce účtu. Ostatní správci fakturace, například vlastník, můžou získat informace o využití pomocí [rozhraní API pro fakturaci](billing-usage-rate-card-overview.md).

Další informace o denním využití najdete v tématu [Vysvětlení informací na faktuře za Microsoft Azure](billing-understand-your-bill.md). Pokud potřebujete pomoc se správou nákladů, přečtěte si, [jak zabránit neočekávaným nákladům v rámci fakturace Azure a jak používat správu nákladů](billing-getting-started.md).

### <a name="download-usage-for-ea-customers"></a>Stažení informací o využití pro zákazníky se smlouvou Enterprise

Pokud si chcete zobrazit a stáhnout data o využití jako zákazník se smlouvou Enterprise, musíte být podnikový správce, vlastník účtu nebo správce oddělení s povolenou zásadou zobrazení poplatků.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Vyhledejte položku *Správa nákladů a fakturace*.

    ![Snímek obrazovky znázorňující hledání na webu Azure Portal](./media/billing-download-azure-invoice-daily-usage-date/portal-cm-billing-search.png)

1. Zvolte **Využití a poplatky**.
1. U měsíce, který chcete zobrazit, vyberte **Stáhnout**.

### <a name="download-usage-for-your-microsoft-customer-agreement"></a>Stažení informací o využití pro smlouvu se zákazníkem Microsoftu

Pokud chcete zobrazit a stáhnout informace o využití pro určitý fakturační profil, musíte být vlastníkem, přispěvatelem, čtenářem nebo správcem faktur daného fakturačního profilu.

#### <a name="download-usage-for-billed-charges"></a>Stažení informací o využití u účtovaných poplatků

1. Vyhledejte položku **Správa nákladů a fakturace**.
2. Zvolte fakturační profil.
3. Vyberte **Faktury**.
4. V tabulce faktur najděte řádek s fakturou odpovídající informacím o využití, které chcete stáhnout.
5. Klikněte na tři tečky (`...`) na konci řádku.
6. V místní nabídce pro stažení vyberte **Využití a poplatky Azure**.

#### <a name="download-usage-for-open-charges"></a>Stažení informací o využití u otevřených poplatků

Můžete si taky stáhnout informace o využití od začátku měsíce za aktuální fakturační období, tedy poplatky, které ještě nejsou vyfakturované.

1. Vyhledejte položku **Správa nákladů a fakturace**.
2. Zvolte fakturační profil.
3. V okně **Přehled** klikněte na **Stáhnout využití a poplatky Azure**.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Ověření přístupu ke smlouvě se zákazníkem Microsoftu
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky

Další informace o vaší faktuře a poplatcích najdete tady:

- [Vysvětlení informací na faktuře za Microsoft Azure](billing-understand-your-bill.md)
- [Vysvětlení podmínek na faktuře Azure](billing-understand-your-invoice.md)
- [Vysvětlení podmínek podrobných informací o vašem využití Microsoft Azure](billing-understand-your-usage.md)
- [Zobrazení cen Azure pro vaši organizaci](billing-ea-pricing.md)

Pokud máte smlouvu se zákazníkem Microsoftu, projděte si tato témata:

- [Vysvětlení poplatků na faktuře pro váš fakturační profil](billing-mca-understand-your-bill.md)
- [Vysvětlení podmínek faktury pro váš fakturační profil](billing-mca-understand-your-invoice.md)
- [Vysvětlení souboru s informacemi o využití a poplatcích za Azure pro váš fakturační profil](billing-mca-understand-your-usage.md)
- [Zobrazení a stažení daňových dokumentů pro váš fakturační profil](billing-mca-download-tax-document.md)
- [Zobrazení cen Azure pro vaši organizaci](billing-ea-pricing.md)

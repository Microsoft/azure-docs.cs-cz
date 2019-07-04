---
title: Stáhnout Azure billing invoice a denní data o využití | Dokumentace Microsoftu
description: Popisuje, jak stáhnout nebo zobrazení Azure fakturační faktury a denní data o využití.
keywords: fakturu, stažení faktury, azure faktury, využití azure
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
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491405"
---
# <a name="download-or-view-your-azure-billing-invoice-and-daily-usage-data"></a>Stažení nebo zobrazení Azure fakturační faktury a dat o denním využití

Pro většinu předplatných si můžete stáhnout fakturu z [webu Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) nebo odeslat e-mailu. Pokud jste zákazník Azure se smlouvou Enterprise (EA zákazníků), nemůže stahovat faktury vaší organizace. Kdo je nastaven na přijímání faktur pro registraci se posílají faktury.

Pokud jste zákazník EA nebo máte [smlouvy zákazníka se společností Microsoft](#check-access-to-a-microsoft-customer-agreement), si můžete stáhnout využití [webu Azure portal](https://portal.azure.com/). Pro jiné předplatné, přejděte [centra účtů Azure](https://account.azure.com/Subscriptions) stáhnout využití.

Pouze některé role mají oprávnění získat fakturační údaje faktury a využití, jako je účet správce nebo správce podnikové sítě. Další informace o získání přístupu k fakturačním údajům najdete v článku o [správě přístupu k fakturaci Azure pomocí rolí](billing-manage-access.md).

Pokud máte smlouvu Microsoft zákazníků, musí být fakturační profil vlastník, Přispěvatel, čtenář, nebo fakturovat správce, chcete-li zobrazit informace o fakturaci a využití. Další informace o rolích fakturace pro smlouvy Microsoft zákazníka najdete v tématu [fakturace profilu role a úlohy](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

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

Faktury se generují pro každou [fakturační profil](billing-mca-overview.md#billing-profiles) smlouvy zákazníka Microsoftu. Musí být fakturační profil vlastník, Přispěvatel, čtenář, nebo fakturovat správce stahovat faktury z portálu Azure portal.

1. Vyhledejte **Cost Management a fakturace**.
2. Vyberte fakturační profil.
3. Vyberte **Faktury**.
4. V mřížce faktury najde řádek faktury, kterou chcete stáhnout.
5. Klikněte na symbol tří teček (`...`) na konci řádku.
6. V místní nabídce stahování vyberte **faktury**.

Pokud nevidíte fakturu za poslední fakturační období, přečtěte si téma **Další informace o**. <!-- Fix this -->
### <a name="noinvoice"></a> Proč nevidím faktury pro poslední fakturačního období?

Faktura se nemusí zobrazovat z několika důvodů:

- Od vytvoření vašeho předplatného Azure uplynulo méně než 30 dnů.

- Faktura se zatím nevygenerovala. Počkejte na konec fakturačního období.

- Nemáte oprávnění k zobrazení faktur. Pokud máte smlouvu Microsoft zákazníků, musí být fakturační profil vlastník, Přispěvatel, čtenář, nebo správce fakturace. Pro další předplatná nemusíte to vidět staré faktury Pokud si nejste správce účtu. Další informace o získání přístupu k fakturačním údajům najdete v článku o [správě přístupu k fakturaci Azure pomocí rolí](billing-manage-access.md).

- Pokud už máte bezplatnou zkušební verzi nebo měsíční částku kreditu s vaším předplatným, které byste neměli překročit, nezískáte faktury, pokud máte smlouvu Microsoft zákazníka.

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

1. Vyhledejte **Cost Management a fakturace**.
1. Vyberte fakturační profil.
1. V části **nastavení**vyberte **vlastnosti**.
1. V části **fakturu e-mailem**vyberte **předvolby faktury e-mailu aktualizace**.
1. Vyberte **vyjádřit výslovný souhlas**.
1. Klikněte na tlačítko **aktualizace**.

### <a name="opt-out-of-getting-your-billing-profile-invoices-in-email"></a>Vyjádřit výslovný nesouhlas získání vaší fakturační profil faktury v e-mailu

Získání faktury e-mailem podle pokynů výše a kliknutím na tlačítko můžete zrušit **Odhlásit se totiž**. Vlastníci, přispěvatelé, čtenáři a faktury správci účast získání faktury e-mailem, příliš. Pokud jste čtečku, nelze změnit předvolby faktury e-mailu.

## <a name="download-usage"></a>Stažení dat o využití

 Většina předplatných, najít souboru denního využití v [centra účtů Azure](https://account.azure.com/Subscriptions). Pokud jste zákazník EA nebo máte zákaznické smlouvy Microsoft, můžete stáhnout využití [webu Azure portal](https://portal.azure.com/). <!-- TO DO: update PayG experience to Ibiza once it ships-->

### <a name="download-usage-from-the-account-center-csv"></a>Stáhnout využití z centra účtů (CSV)

1. Přihlaste se [centra účtů Azure](https://account.windowsazure.com/subscriptions) jako správce účtu.

2. Vyberte předplatné, pro které chcete informace o faktury a využití.

3. Vyberte **HISTORIE FAKTURACE**.

    ![Snímek obrazovky zobrazující možnost historie fakturace](./media/billing-download-azure-invoice-daily-usage-date/Billinghisotry.png)

4. Zobrazí se vaše příkazy pro posledních šest fakturačních období a aktuální nefakturované období.

    ![Snímek obrazovky s fakturační období, možnosti pro stažení faktury a denního využití a celkové náklady pro každého fakturačního období](./media/billing-download-azure-invoice-daily-usage-date/billingSum.png)

5. Pokud chcete zobrazit odhadované poplatky k datu generování odhadu, vyberte **Zobrazit aktuální výpis**. Tyto informace se aktualizují každý den a nemusí obsahovat celkové využití. Měsíční faktuře může od odhadu lišit.

    ![Snímek obrazovky zobrazující možnost zobrazit aktuální výpis](./media/billing-download-azure-invoice-daily-usage-date/billingSum2.png)

    ![Snímek obrazovky zobrazující odhad aktuální poplatky](./media/billing-download-azure-invoice-daily-usage-date/billingSum3.png)

6. Pokud chcete stáhnout data o denním využití jako soubor CSV, vyberte **Stáhnout informace o využití**. Pokud se zobrazí dvě dostupné verze, stáhněte si verzi 2.

    ![Snímek obrazovky zobrazující možnost stažení informací o využití](./media/billing-download-azure-invoice-daily-usage-date/DLusage.png)

Pouze správce účtu může přístup k centru účtů Azure. Jiní správci fakturace, jako je například vlastníka, můžete získat pomocí informace o využití [rozhraní API pro fakturaci](billing-usage-rate-card-overview.md).

Další informace o denním využití najdete v tématu [Vysvětlení informací na faktuře za Microsoft Azure](billing-understand-your-bill.md). Pomoci se správou nákladů, naleznete v tématu [jak zabránit neočekávaným nákladům se správou nákladů a fakturací Azure](billing-getting-started.md).

### <a name="download-usage-for-ea-customers"></a>Stáhnout využití pro zákazníky se smlouvou EA

Zobrazovat a stahovat data o využití jako zákazník EA, musíte být správce podnikové sítě, vlastníka účtu, nebo správce oddělení, když je zobrazení poplatky za povolenou zásadou.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Vyhledejte *Cost Management a fakturace*.

    ![Snímek obrazovky zobrazující Azure search na portálu](./media/billing-download-azure-invoice-daily-usage-date/portal-cm-billing-search.png)

1. Vyberte **a poplatky za využití**.
1. Za měsíc, kterou chcete stáhnout, vyberte **Stáhnout**.

### <a name="download-usage-for-your-microsoft-customer-agreement"></a>Stáhnout využití pro smlouvy Microsoft zákazníka

Zobrazovat a stahovat data o využití pro fakturační profil, musí být fakturační profil vlastník, Přispěvatel, čtenář, nebo správce fakturace.

#### <a name="download-usage-for-billed-charges"></a>Stáhnout využití fakturuje za

1. Vyhledejte **Cost Management a fakturace**.
2. Vyberte fakturační profil.
3. Vyberte **Faktury**.
4. V mřížce faktury nalezen řádek odpovídající využití, které chcete stáhnout fakturu.
5. Klikněte na symbol tří teček (`...`) na konci řádku.
6. V místní nabídce stahování vyberte **využití Azure a poplatky za**.

#### <a name="download-usage-for-open-charges"></a>Stáhnout využití za otevřené

Můžete také stáhnout využití za měsíc k datu v aktuálním fakturačním období, to znamená, že ještě nebyly byla účtuje poplatky.

1. Vyhledejte **Cost Management a fakturace**.
2. Vyberte fakturační profil.
3. V **přehled** okna, klikněte na tlačítko **Azure stáhnout využití a poplatků**.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrola přístupu na základě smlouvy Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás

Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další postup

Další informace týkající se faktury a náklady, naleznete v tématu:

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

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
ms.date: 11/16/2018
ms.author: cwatson
ms.openlocfilehash: 80721fc82a54c62c982298cb8eabb999caaf1dfb
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/29/2018
ms.locfileid: "52583104"
---
# <a name="download-or-view-your-azure-billing-invoice-and-daily-usage-data"></a>Stažení nebo zobrazení Azure fakturační faktury a dat o denním využití

Pro většinu předplatných si můžete stáhnout fakturu z [webu Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) nebo odeslat e-mailu. Pokud jste zákazník Azure se smlouvou Enterprise (EA zákazníků), nemůže stahovat faktury vaší organizace. Kdo je nastaven na přijímání faktur pro registraci se posílají faktury.

Pokud chcete stáhnout využití jako zákazník EA, je k dispozici v [webu Azure portal](https://portal.azure.com/) > **Správa nákladů a fakturace** > **a poplatky za využití**. Pro jiné předplatné, přejděte [centra účtů Azure](https://account.azure.com/Subscriptions).

Pouze některé role mají oprávnění získat fakturační údaje faktury a využití, jako je účet správce nebo správce podnikové sítě. Další informace o získání přístupu k fakturačním údajům najdete v článku o [správě přístupu k fakturaci Azure pomocí rolí](billing-manage-access.md).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="download-or-view-your-invoice"></a>Stažení nebo zobrazení vaší faktuře

 Pokud jste zákazník EA, nemůže stahovat faktury vaší organizace. Kdo je nastaven na přijímání faktur pro registraci se posílají faktury. U jiných předplatných můžete získat faktury e-mailem nebo si ho stáhnout z webu Azure portal.

### <a name="get-your-invoice-in-email-pdf"></a>Získání faktury v e-mailu (PDF)
Můžete vyjádřit výslovný souhlas a nakonfigurovat další příjemce pro příjem Azure faktury e-mailem. Tato funkce nemusí být k dispozici pro určitá předplatná, jako jsou nabídky podpory, smlouvy Enterprise nebo Azure v programu Open.

1. Vyberte své předplatné z [stránce předplatná](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Vyjádřit výslovný souhlas pro každé předplatné, které vlastníte. Klikněte na tlačítko **faktury** pak **e-mailu mé faktury**. 

    ![Snímek obrazovky zobrazující tok vyjádření souhlasu](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)
    
2. Klikněte na tlačítko **vyjádřit výslovný souhlas** a přijměte podmínky.

    ![Snímek obrazovky zobrazující tok vyjádření souhlasu se krok 2](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)
 
3. Jakmile jste přijali smlouvu, můžete nakonfigurovat další příjemce. Při odebrání příjemce e-mailová adresa je již uloženy. Pokud změníte své rozhodnutí, budete muset znovu přidat.

    ![Snímek obrazovky zobrazující tok vyjádření souhlasu se krok 3](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)
    
Pokud e-mail neobdržíte po provedení kroků, ujistěte se, že je správný v e-mailovou adresu [předvolby komunikace ve vašem profilu](https://account.windowsazure.com/profile).

### <a name="opt-out-from-getting-your-invoice-in-email"></a>Odhlásit se totiž od získání faktury v e-mailu
Pokud nechcete, aby se získat faktury e-mailem, klikněte na tlačítko **vyjádřit výslovný nesouhlas faktury e-mailem**. Tato možnost odebere všechny e-mailové adresy nastaveny na přijímání faktury e-mailem. Pokud se rozhodnete zpátky, budete muset překonfigurovat příjemce.

 ![Snímek obrazovky zobrazující tok výslovného nesouhlasu s](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep4.PNG)

### <a name="download-invoice-from-azure-portal-pdf"></a>Stažení faktury z webu Azure portal (PDF)

1. Vyberte své předplatné z [stránce předplatná](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) na webu Azure portal jako [uživatele s přístupem na fakturách](billing-manage-access.md).

2. Vyberte **faktury**. 

    ![Snímek obrazovky zobrazující možnost fakturace a využití](./media/billing-download-azure-invoice-daily-usage-date/billingandusage.png) 

3. Klikněte na tlačítko **stáhnout fakturu** zobrazíte kopii faktury ve formátu PDF. Při stavu **není k dispozici**, naleznete v tématu [Proč nevidím faktury pro poslední fakturačního období?](#noinvoice)

    ![Snímek obrazovky s fakturační období, možnost stažení a celkové náklady pro každého fakturačního období](./media/billing-download-azure-invoice-daily-usage-date/billing4.png)

4. Denní využití můžete zobrazit také kliknutím fakturačního období. 

Další informace o vaší faktuře najdete v tématu [vysvětlení vašeho vyúčtování služeb Microsoft Azure](billing-understand-your-bill.md). Vám pomůžou mít náklady pod kontrolou [jak zabránit neočekávaným nákladům se správou nákladů a fakturací Azure](billing-getting-started.md).

### <a name="noinvoice"></a> Proč nevidím faktury pro poslední fakturačního období?

Faktura se nemusí zobrazovat z několika důvodů:

- U svého předplatného máte měsíční kredit, jehož výši jste nepřekročili, nebo máte bezplatnou zkušební verzi. Faktura se generuje pouze v případě, že dlužíte peníze.

- Od vytvoření vašeho předplatného Azure uplynulo méně než 30 dnů.

- Faktur se zatím nevygenerovala. Počkejte na konec fakturačního období.

- Pokud nejste správce účtu, možná nemáte přístup ke starším fakturám.

## <a name="download-usage"></a>Stáhnout využití

 Většina předplatných, najít souboru denního využití v [centra účtů Azure](https://account.azure.com/Subscriptions). Pokud chcete stáhnout využití jako zákazník EA, je k dispozici v [webu Azure portal](https://portal.azure.com/) > **Správa nákladů a fakturace** > **a poplatky za využití**. 

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

Další informace o denním využití najdete v tématu [Vysvětlení informací na faktuře za Microsoft Azure](billing-understand-your-bill.md). Vám pomůžou mít náklady pod kontrolou [jak zabránit neočekávaným nákladům se správou nákladů a fakturací Azure](billing-getting-started.md).

### <a name="download-usage-for-ea-customers"></a>Stáhnout využití pro zákazníky se smlouvou EA

Zobrazovat a stahovat data o využití jako zákazník EA, musíte být správce podnikové sítě, nebo účtu vlastníka nebo správce oddělení s povolenou zásadou poplatky zobrazení.

1. Přihlaste se k webu [Azure Portal]( http://portal.azure.com).
1. Hledat na **Cost Management a fakturace**.

    ![Snímek obrazovky zobrazující Azure search na portálu](./media/billing-download-azure-invoice-daily-usage-date/portal-cm-billing-search.png)

1. Vyberte **a poplatky za využití**.
1. Za měsíc, kterou chcete stáhnout, vyberte **Stáhnout**.

## <a name="need-help-contact-us"></a>Potřebujete pomoct? Kontaktujte nás.

Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

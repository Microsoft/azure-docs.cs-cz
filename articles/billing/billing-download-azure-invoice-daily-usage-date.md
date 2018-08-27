---
title: Stáhnout Azure billing invoice a denní data o využití | Dokumentace Microsoftu
description: Popisuje, jak stáhnout nebo zobrazení Azure fakturační faktury a denní data o využití.
keywords: fakturu, stažení faktury, azure faktury, využití azure
services: billing
documentationcenter: ''
author: genlin
manager: tonguyen
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: e2985882b2e72b6e2f23945aa54c7a282137ce4e
ms.sourcegitcommit: ebb460ed4f1331feb56052ea84509c2d5e9bd65c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2018
ms.locfileid: "42918978"
---
# <a name="download-or-view-your-azure-billing-invoice-and-daily-usage-data"></a>Stažení nebo zobrazení Azure fakturační faktury a dat o denním využití
Můžete si stáhnout fakturu z [webu Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) nebo odeslat e-mailu. Chcete-li stáhnout denního využití, přejděte na [centra účtů Azure](https://account.azure.com/Subscriptions). Pouze určité role mají oprávnění k získání fakturu a informace o využití, jako je správce účtu. Další informace o získání přístupu k fakturačním údajům najdete v článku o [správě přístupu k fakturaci Azure pomocí rolí](billing-manage-access.md).

Tento článek se nevztahuje na zákazníky Enterprise Agreement (EA). Pokud jste zákazník EA, vašich fakturách se projeví se odesílají přímo pro správce registrace.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

> [!div class="nextstepaction"]
> [Pomozte nám vylepšit Azure fakturační dokumentace](https://go.microsoft.com/fwlink/p/?linkid=2010091)

## <a name="get-your-invoice-in-email-pdf"></a>Získání faktury v e-mailu (PDF)
Můžete vyjádřit výslovný souhlas a nakonfigurovat další příjemce pro příjem Azure faktury e-mailem. Tato funkce nemusí být k dispozici pro určitá předplatná, jako jsou nabídky podpory, smlouvy Enterprise nebo Azure v programu Open.

1. Vyberte své předplatné z [stránce předplatná](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Vyjádřit výslovný souhlas pro každé předplatné, které vlastníte. Klikněte na tlačítko **faktury** pak **e-mailu mé faktury**. 

    ![Snímek obrazovky zobrazující tok vyjádření souhlasu](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)
    
2. Klikněte na tlačítko **vyjádřit výslovný souhlas** a přijměte podmínky.

    ![Snímek obrazovky zobrazující tok vyjádření souhlasu se krok 2](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)
 
3. Jakmile jste přijali smlouvu, můžete nakonfigurovat další příjemce. Při odebrání příjemce e-mailová adresa je již uloženy. Pokud změníte své rozhodnutí, budete muset znovu přidat.

    ![Snímek obrazovky zobrazující tok vyjádření souhlasu se krok 3](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)
    
Pokud e-mail neobdržíte po provedení kroků, ujistěte se, že je správný v e-mailovou adresu [předvolby komunikace ve vašem profilu](https://account.windowsazure.com/profile).

### <a name="opt-out-from-getting-your-invoice-in-email"></a>Odhlásit se totiž od získání faktury v e-mailu
Pokud nechcete, aby se získat faktury e-mailem, klikněte na tlačítko nesouhlas faktury e-mailem. Tato operace odebere všechny e-mailové adresy nastaveny na přijímání faktury e-mailem. Pokud se rozhodnete znovu v budete muset překonfigurovat tak příjemce.

 ![Snímek obrazovky zobrazující tok výslovného nesouhlasu s](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep4.PNG)

## <a name="download-invoice-from-azure-portal-pdf"></a>Stažení faktury z webu Azure portal (PDF)

1. Vyberte své předplatné z [stránce předplatná](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) na webu Azure portal jako [uživatele s přístupem na fakturách](billing-manage-access.md).

2. Vyberte **faktury**. 

    ![Snímek obrazovky zobrazující možnost fakturace a využití](./media/billing-download-azure-invoice-daily-usage-date/billingandusage.png) 

3. Klikněte na tlačítko **stáhnout fakturu** zobrazíte kopii faktury ve formátu PDF. Při stavu **není k dispozici**, naleznete v tématu [Proč nevidím faktury pro poslední fakturačního období?](#noinvoice)

    ![Snímek obrazovky s fakturační období, možnost stažení a celkové náklady pro každého fakturačního období](./media/billing-download-azure-invoice-daily-usage-date/billing4.png)

4. Denní využití můžete zobrazit také kliknutím fakturačního období. 

Další informace o vaší faktuře najdete v tématu [vysvětlení vašeho vyúčtování služeb Microsoft Azure](billing-understand-your-bill.md). Vám pomůžou mít náklady pod kontrolou [jak zabránit neočekávaným nákladům se správou nákladů a fakturací Azure](billing-getting-started.md).

## <a name="download-usage-from-the-account-center-csv"></a>Stáhnout využití z centra účtů (CSV)

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

## <a name="noinvoice"></a> Proč nevidím faktury pro poslední fakturačního období?

Možná existuje několik důvodů, proč nevidíte faktury:

- Máte předplatné, které byste neměli překročit měsíční částku kreditu nebo máte bezplatnou zkušební verzi. Faktury se vygeneruje pouze tehdy, když dlužíte peníze.

- Je menší než 30 dnů ode dne, k jehož odběru přihlášeni do Azure.

- Zatím není vygenerovaný faktury. Počkejte, až do konce fakturačního období.

- Pokud si nejste správce účtu, nemusí být k dispozici, starší faktury.

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Obraťte se na podporu.
Pokud máte další otázky, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pro rychlé vyřešení problému.


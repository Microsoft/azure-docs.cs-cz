---
title: Stažení Azure fakturace faktury a denní data o využití | Microsoft Docs
description: Popisuje postup stažení nebo zobrazení Azure fakturace faktury a denní data o využití.
keywords: fakturace faktury, stažení faktury, faktury azure, azure využití
services: ''
documentationcenter: ''
author: genlin
manager: tonguyen
editor: ''
tags: billing
ms.assetid: 6d568d1d-3bd6-4348-97d0-1098b5fe0661
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e57e3898c5e843dcc130746a10e78b0681303670
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="download-or-view-your-azure-billing-invoice-and-daily-usage-data"></a>Stažení nebo zobrazení Azure fakturace faktury a dat o denním využití
Vaše faktura z si můžete stáhnout [portál Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) nebo jej odeslat e-mailem. Chcete-li stáhnout denní využití, přejděte na [centra účtů Azure](https://account.azure.com/Subscriptions). Pouze některé role nemáte oprávnění k získání fakturace faktury a informace o využití, jako je správce účtu. Další informace o získání přístupu k fakturační informace najdete v tématu [spravovat přístup k Azure fakturace použití rolí](billing-manage-access.md).

Tento článek se nevztahuje na zákazníky Enterprise Agreement (EA). Pokud jste již EA zákazníka, vaše faktury jsou odesílány přímo správce registrace.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="get-your-invoice-in-email-pdf"></a>Získat faktury v e-mailu (PDF)
Můžete vyjádřit výslovný souhlas a nakonfigurovat další příjemce pro příjem vaší Azure faktury e-mailem. Tato funkce nemusí být dostupné pro určité odběry například nabízí podporu, smlouvy Enterprise nebo Azure v otevřené.

1. Vyberte své předplatné z [předplatné](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Výslovný souhlas pro každé předplatné, které vlastníte. Klikněte na tlačítko **faktury** pak **e-mailu fakturou**. 

    ![Snímek obrazovky, který zobrazuje tok přihlášení](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)
    
2. Klikněte na tlačítko **vyjádřit výslovný souhlas** podmínky a přijměte je.

    ![Snímek obrazovky, který zobrazuje tok přihlášení](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)
 
3. Jakmile jste přijatá smlouvu, můžete nakonfigurovat další příjemce.

    ![Snímek obrazovky, který zobrazuje tok přihlášení](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)
    
Pokud neobdržíte e-mail po provedení kroků, zkontrolujte, zda je správný v e-mailovou adresu [předvolby komunikace na váš profil](https://account.windowsazure.com/profile).

## <a name="download-invoice-from-azure-portal-pdf"></a>Stahovat faktury z portálu Azure (PDF)

1. Vyberte své předplatné z [předplatné](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) na portálu Azure jako [uživatele s přístupem k faktury](billing-manage-access.md).

2. Vyberte **faktury**. 

    ![Snímek obrazovky zobrazující možnosti využití & fakturace](./media/billing-download-azure-invoice-daily-usage-date/billingandusage.png) 

3. Klikněte na tlačítko **stažení faktury** zobrazíte kopii faktury PDF. Pokud se říká **není k dispozici**, najdete v části [proč pro poslední fakturační období nevidíte faktury?](#noinvoice)

    ![Snímek obrazovky, který zobrazuje fakturační období, možnost stažení a celkové náklady pro každé fakturační období](./media/billing-download-azure-invoice-daily-usage-date/billing4.png)

4. Denní využití můžete také zobrazit kliknutím fakturačního období. 

Další informace o vaší faktuře najdete v tématu [porozumět vaší faktuře pro Microsoft Azure](billing-understand-your-bill.md). Správa nákladů pomoc najdete v tématu [zabránit neočekávané náklady s Azure fakturace a náklady na správu](billing-getting-started.md).

## <a name="download-usage-from-the-account-center-csv"></a>Stáhnout využití z centra účtů (CSV)

1. Přihlaste se k [centra účtů Azure](https://account.windowsazure.com/subscriptions) jako správce účtu.

2. Vyberte předplatné, pro které chcete informace o faktury a využití.

3. Vyberte **HISTORIE FAKTURACE**. 

    ![Snímek obrazovky, který ukazuje možnost historie fakturace](./media/billing-download-azure-invoice-daily-usage-date/Billinghisotry.png)

4. Zobrazí se vaše příkazy pro posledních šest fakturační období a aktuální nefakturovaný období. 

    ![Snímek obrazovky zobrazující fakturační období, možnosti a stahovat faktury a denní využití a celkové náklady pro každé fakturační období](./media/billing-download-azure-invoice-daily-usage-date/billingSum.png)

5. Vyberte **zobrazení aktuální příkaz** zobrazíte odhad vaše poplatky v době odhad byl vygenerován. Tyto informace se pouze denně aktualizují a nemusí obsahovat všechny využití. Měsíční faktury může lišit od tento odhad.

    ![Snímek obrazovky, který ukazuje možnost zobrazení aktuální příkaz](./media/billing-download-azure-invoice-daily-usage-date/billingSum2.png)

    ![Snímek obrazovky zobrazující odhad aktuální poplatky](./media/billing-download-azure-invoice-daily-usage-date/billingSum3.png)

6. Vyberte **stáhnout využití** stáhnout denní data o využití do souboru CSV. Pokud se zobrazují dvě verze, které jsou k dispozici, stáhněte si verze 2.

    ![Snímek obrazovky, který ukazuje možnost stáhnout využití](./media/billing-download-azure-invoice-daily-usage-date/DLusage.png)

Pouze správce účtu mají přístup k centru účtů Azure. Jiní správci fakturace, jako je vlastníkem, můžete získat pomocí informace o využití [fakturace rozhraní API](billing-usage-rate-card-overview.md).

Další informace o denní využití najdete v tématu [porozumět vaší faktuře pro Microsoft Azure](billing-understand-your-bill.md). Správa nákladů pomoc najdete v tématu [zabránit neočekávané náklady s Azure fakturace a náklady na správu](billing-getting-started.md).

## <a name="noinvoice"></a> Proč nevidím fakturu za poslední fakturační období?

Je možné, že nevidíte faktury z několika důvodů:

- Máte ve vašem předplatném, které nebylo delší než měsíční částku Dal nebo máte bezplatnou zkušební verzi. Faktury se vygeneruje pouze tehdy, když dlužíte peníze.

- Je menší než 30 dnů ode dne, které odebíráte Azure.

- Faktury ještě nevygenerovala. Počkejte, až do konce fakturačního období.

- Pokud si nejste účet správce, nemusí být k dispozici starší faktury.

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Obraťte se na podporu.
Pokud máte další otázky, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) získat rychle vyřešit problém.


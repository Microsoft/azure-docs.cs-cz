---
title: Tutorial - Create and manage exported data from Azure Cost Management
description: This article shows you how you can create and manage exported Azure Cost Management data so that you can use it in external systems.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/12/2019
ms.topic: tutorial
ms.service: cost-management-billing
manager: dougeby
ms.custom: seodec18
ms.openlocfilehash: a462b3d165a596673049abbbb8b5b8d346f5fc9d
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229827"
---
# <a name="tutorial-create-and-manage-exported-data"></a>Kurz: Vytvoření a správa exportovaných dat

Pokud jste si prošli kurz Analýza nákladů, pak jste obeznámeni s ručním stahováním dat ze služby Cost Management. However, you can create a recurring task that automatically exports your Cost Management data to Azure storage on a daily, weekly, or monthly basis. Exportovaná data jsou ve formátu CSV a obsahují všechny informace, které služba Cost Management shromažďuje. Exportovaná data v úložišti Azure pak můžete používat s externími systémy a kombinovat je se svými vlastními daty. Exportovaná data můžete používat v externím systému, jako je třeba řídicí panel nebo jiný finanční systém.

Watch the [How to schedule exports to storage with Azure Cost Management](https://www.youtube.com/watch?v=rWa_xI1aRzo) video about creating a scheduled export of your Azure cost data to Azure Storage.

Příklady v tomto kurzu vás provedou exportem dat služby Cost Management a následným ověřením, že se daná data úspěšně exportovala.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření každodenního exportu
> * Ověřit shromáždění dat

## <a name="prerequisites"></a>Předpoklady
Data export is available for a variety of Azure account types, including [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) customers. To view the full list of supported account types, see [Understand Cost Management data](understand-cost-mgt-data.md). The following Azure permissions, or scopes, are supported per subscription for data export by user and group. For more information about scopes, see [Understand and work with scopes](understand-work-scopes.md).

- Vlastník – může vytvářet, upravovat a odstraňovat naplánované exporty u předplatných.
- Přispěvatel – může vytvářet, upravovat a odstraňovat svoje vlastní naplánované exporty. Může měnit názvy naplánovaných exportů, které vytvořili jiní uživatelé.
- Čtenář – může naplánovat exporty, ke kterým má oprávnění.

Pro účty služby Azure Storage:
- Ke změně nakonfigurovaného účtu úložiště se vyžaduje oprávnění k zápisu, a to bez ohledu na oprávnění k exportu.
- Příslušný účet úložiště Azure musí být nakonfigurovaný jako úložiště souborů nebo objektů blob.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure
Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com/).

## <a name="create-a-daily-export"></a>Vytvoření každodenního exportu

To create or view a data export or to schedule an export, open the desired scope in the Azure portal and select **Cost analysis** in the menu. For example, navigate to **Subscriptions**, select a subscription from the list, and then select **Cost analysis** in the menu. At the top of the Cost analysis page, click **Export** and then choose an export option. For example, click **Schedule export**.  

> [!NOTE]
> - Besides subscriptions, you can create exports on resource groups, accounts, departments, and enrollments. For more information about scopes, see [Understand and work with scopes](understand-work-scopes.md).
>- When you’re signed in as a partner at the billing account scope or on a customer’s tenant, you can export data to an Azure Storage account that’s linked to your partner storage account. However, you must have an active subscription in your CSP tenant.
>


Click **Add**, type a name for the export, and then select the **Daily export of month-to-date costs** option. Klikněte na **Další**.

![New export example showing export type](./media/tutorial-export-acm-data/basics_exports.png)

Specify the subscription for your Azure storage account, then select your storage account.  Specify the storage container and the directory path that you'd like the export file to go to.  Klikněte na **Další**.

![New export example showing storage account details](./media/tutorial-export-acm-data/storage_exports.png)

Review your export details and click **Create**.

Nový export se zobrazí v seznamu exportů. By default, new exports are enabled. Pokud chcete zakázat nebo odstranit naplánovaný export, klikněte na libovolnou položku v seznamu a pak klikněte na **Zakázat** nebo **Odstranit**.

Na začátku může trvat jednu až dvě hodiny, než se export spustí. Může však trvat až čtyři hodiny, než se v exportovaných souborech zobrazí data.

### <a name="export-schedule"></a>Export schedule

Scheduled exports are affected by the time and day of week of when you initially create the export. When you create a scheduled export, the export runs at the same time of day for each subsequent export occurrence. For example, you create a daily export at 1:00 PM. The next export runs at 1:00 PM the following day. The current time affects all other export types in the same manner—they always run at the same time of day as when you initially created the export. In a different example, you create a weekly export at 4:00 PM on Monday. The next report runs at 4:00 PM the following Monday. *Exported data is available within four hours of run time.*

Each export creates a new file, so older exports are not overwritten.

There are three types of export options:

**Daily export of month-to-date costs** – The initial export runs immediately. Subsequent exports run the next day at the same time as the initial export. The latest data is aggregated from previous daily exports.

**Weekly export of costs for the last 7 days** – The initial export runs immediately. Subsequent exports run on the day of the week and at the same time as the initial export. Costs are for the last seven days.

**Custom** – Allows you to schedule weekly and monthly exports with week-to-date and month-to-date options. *The initial export will run immediately.*

If you have a Pay-As-You-Go, MSDN, or Visual Studio subscription, your invoice billing period might not align to the calendar month. For those types of subscriptions and resource groups, you can create an export that's aligned to your invoice period or to calendar months. To create an export aligned to your invoice month, navigate to **Custom**, then select **Billing-period-to-date**.  To create an export aligned to the calendar month, select **Month-to-date**.
>
>

![New export - Basics tab showing a custom weekly week-to-date selection](./media/tutorial-export-acm-data/tutorial-export-schedule-weekly-week-to-date.png)

## <a name="verify-that-data-is-collected"></a>Ověřit shromáždění dat

Pomocí Průzkumníka služby Azure Storage můžete snadno ověřit, že se data služby Cost Management shromažďují, a zobrazit exportovaný soubor CSV.

V seznamu exportu klikněte na název účtu úložiště. Na stránce účtu úložiště klikněte na Otevřít v Průzkumníkovi. Pokud se zobrazí potvrzovací okno, klikněte na **Ano** a otevřete soubor v Průzkumníkovi služby Azure Storage.

![Storage account page showing example information and link to Open in Explorer](./media/tutorial-export-acm-data/storage-account-page.png)

V Průzkumníkovi služby Storage přejděte ke kontejneru, který chcete otevřít, a vyberte složku odpovídající aktuálnímu měsíci. Zobrazí se seznam souborů CSV. Vyberte jeden soubor a pak klikněte na **Otevřít**.

![Example information shown in Storage Explorer](./media/tutorial-export-acm-data/storage-explorer.png)

Soubor se otevře v programu nebo aplikaci, která je nastavená k otevírání souborů s příponou CSV. Tady je příklad v Excelu.

![Example exported CSV data shown in Excel](./media/tutorial-export-acm-data/example-export-data.png)


## <a name="access-exported-data-from-other-systems"></a>Přístup k exportovaným datům z jiných systémů

Jedním z důvodů pro export dat služby Cost Management je umožnění přístupu k těmto datům z externích systémů. Možná používáte systém řídicích panelů nebo jiný finanční systém. Takové systémy se dost liší, takže ukázka s příkladem by nemusela být užitečná.  S přístupem k datům z vašich aplikací však můžete začít podle tématu [Úvod do Azure Storage](../storage/common/storage-introduction.md).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření každodenního exportu
> * Ověřit shromáždění dat

Přejděte k dalšímu kurzu, který se zabývá optimalizací a zvyšováním efektivity pomocí identifikace nečinných a nedostatečně využitých prostředků.

> [!div class="nextstepaction"]
> [Kontrola optimalizačních doporučení a reakce na ně](tutorial-acm-opt-recommendations.md)

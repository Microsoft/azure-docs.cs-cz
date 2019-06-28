---
title: Vysvětlení Azure poplatků za externí služby | Dokumentace Microsoftu
description: Další informace o fakturaci externích služeb, dřív označované jako Marketplace, poplatky v Azure.
services: ''
documentationcenter: ''
author: jureid
manager: jureid
editor: ''
tags: billing
ms.assetid: 5e0e2a3c-d111-4054-8508-0c111c1b749b
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 90753c6046425b60fda04fa99b2952e706d9c0e5
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2019
ms.locfileid: "67311969"
---
# <a name="understand-your-azure-external-services-charges"></a>Vysvětlení poplatků za externí služby Azure
Externí služby se publikují dodavatelé softwaru třetích stran na Azure Marketplace. Například SendGrid je externí služby, které si můžete koupit v Azure, ale nebyl vydán microsoftem. Některé produkty Microsoft příliš prodávají prostřednictvím Azure marketplace.

## <a name="how-external-services-are-billed"></a>Jak se účtují externí služby

- Pokud máte [smlouvy zákazníka se společností Microsoft](#check-access-to-a-microsoft-customer-agreement), služby třetích stran se účtují pomocí rest služby Azure.
- Pokud nemáte smlouvu zákazníků společnosti Microsoft, externí služby se účtují odděleně od služeb Azure.
- Každá externí služba má jiný model fakturace. Některé služby se účtují způsobem s průběžnými platbami ostatní mít pevnou měsíční poplatky.
- Měsíční bezplatný kredit nelze použít pro externí služby. Pokud používáte předplatné Azure, které zahrnuje [bezplatné kredity](https://azure.microsoft.com/pricing/spending-limits/), pravidla nelze použít na poplatky za z externích služeb. Když si zřídíte nový externí službě nebo prostředku, upozornění se zobrazí:

    ![Marketplace zakoupit upozornění](./media/billing-understand-your-azure-marketplace-charges/credit-warning.png)

<!-- ## View external service spending and history in the Azure portal
You can view a list of the external services that are on each subscription within the [Azure portal](https://portal.azure.com/): 

1. Sign in to the [Azure portal](https://portal.azure.com/) as the account administrator.
2. In the Hub menu, select **Subscriptions**.
   
    ![Select Subscriptions in the Hub menu](./media/billing-understand-your-azure-marketplace-charges/sub-button.png) 
3. In the **Subscriptions** blade, select the subscription that you want to view, and then select **External services**.
   
    ![Select a subscription in the billing blade](./media/billing-understand-your-azure-marketplace-charges/select-sub-external-services.png)
4. You should see each of your external service orders, the publisher name, service tier you bought, name you gave the resource, and the current order status. To see past bills, select an external service.
   
    ![Select an external service](./media/billing-understand-your-azure-marketplace-charges/external-service-blade2.png)
5. From here, you can view past bill amounts including the tax breakdown.
   
    ![View external services billing history](./media/billing-understand-your-azure-marketplace-charges/billing-overview-blade.png) -->

## <a name="view-and-download-azure-marketplace-invoices"></a>Zobrazovat a stahovat faktury Azure marketplace

Pokud máte [smlouvy zákazníka se společností Microsoft](#check-access-to-a-microsoft-customer-agreement), poplatky třetích stran jsou na stejné faktury jako poplatky za Azure. Zjistěte, jak [zobrazovat a stahovat faktuře Azure](billing-download-azure-invoice.md) na webu Azure Portal podívat poplatky třetích stran.

Pokud nemáte smlouvu zákazníků společnosti Microsoft, máte samostatné faktury za třetí strany. Můžete zobrazit a stahovat faktury vašeho webu Azure Marketplace na webu Azure Portal pomocí následujících kroků:

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Hledat na **Cost Management a fakturace**.
1. V nabídce vlevo vyberte **faktury**.
1. Klikněte na **Azure Marketplace a rezervace** kartu.  ![Obrázek karty Azure marketplace a rezervace](./media/billing-understand-your-azure-marketplace-charges/invoice-tabs.png)
1. V předplatném rozevíracího seznamu vyberte předplatné, který obsahuje externí služby, které chcete zobrazit faktury.

## <a name="view-external-service-spending-for-enterprise-agreement-ea-customers"></a>Zobrazení externí služby útraty pro zákazníky, kteří Enterprise Agreement (EA)

Zákazníci programu EA si můžou zobrazit útraty externí služby a stáhnout sestav na portálu EA. Zobrazit [Azure Marketplace pro zákazníky se smlouvou EA](https://ea.azure.com/helpdocs/azureMarketplace) začít.

## <a name="manage-payment-methods-for-external-service-orders"></a>Spravovat způsoby platby za objednávky externí služby

Při nákupu externí služby, zvolte předplatné Azure pro prostředek. Platby z vybraného předplatného Azure se změní způsob platby pro externí služby. Pokud chcete změnit způsob platby pro externí služby, musíte [změnit způsob platby předplatného Azure](billing-how-to-change-credit-card.md) vázané na tuto službu externí. Můžete zjistit, jaké předplatné objednávku externí služby se váže na pomocí následujících kroků:

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Klikněte na **všechny prostředky** v levé navigační nabídce.
     ![snímek obrazovky se všechny položky nabídky prostředky](./media/billing-understand-your-azure-marketplace-charges/all-resources.png)
1. Hledání pro externí služby.
1. Vyhledejte název předplatného v **předplatné** sloupce.
    ![snímek obrazovky název předplatného pro prostředek](./media/billing-understand-your-azure-marketplace-charges/sub-selected.png)
1. Klikněte na název předplatného a [aktualizovat aktivní způsob platby](billing-how-to-change-credit-card.md).
 
<!-- Update your payment methods for external service orders from the [Account Center](https://account.windowsazure.com/).

> [!NOTE]
> If you purchased your subscription with a Work or School account, [contact support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) to make changes to your payment method.

1. Sign in to the [Account Center](https://account.windowsazure.com/) and [navigate to the **marketplace** tab](https://account.windowsazure.com/Store)
   
    ![Select marketplace in the account center](./media/billing-understand-your-azure-marketplace-charges/select-marketplace.png)
2. Select the external service you want to manage
   
    ![Select the external service you want to manage](./media/billing-understand-your-azure-marketplace-charges/select-ext-service.png)
3. Click **Change payment method** on the right side of the page. This link brings you to a different portal to manage your payment method.
   
    ![Order summary](./media/billing-understand-your-azure-marketplace-charges/change-payment.PNG)
4. Click **Edit info** and follow instructions to update your payment information.
   
    ![Select edit info](./media/billing-understand-your-azure-marketplace-charges/edit-info.png) -->

## <a name="cancel-an-external-service-order"></a>Zrušit objednávku externí služby
Pokud chcete zrušit objednávku externí služby, odstranění prostředku v [webu Azure portal](https://portal.azure.com).

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Klikněte na **všechny prostředky** v levé navigační nabídce.
    ![Snímek obrazovky se všechny položky nabídky prostředky](./media/billing-understand-your-azure-marketplace-charges/all-resources.png)
1. Hledání pro externí služby.
1. Zaškrtněte políčko vedle prostředků, kterou chcete odstranit.
1. Vyberte **odstranit** na panelu příkazů.
    ![Snímek obrazovky tlačítko Odstranit](./media/billing-understand-your-azure-marketplace-charges/delete-button.png)
1. Typ *"Yes"* v okně potvrzení.
    ![Odstranit prostředek](./media/billing-understand-your-azure-marketplace-charges/delete-resource.PNG)
1. Klikněte na tlačítko **odstranit**.



## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrola přístupu na základě smlouvy Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).


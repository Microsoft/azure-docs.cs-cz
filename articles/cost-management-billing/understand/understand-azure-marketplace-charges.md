---
title: Vysvětlení poplatků za externí služby v Azure | Microsoft Docs
description: Přečtěte si o informace účtování poplatků za externí služby v Azure, dříve označované jako poplatky za Marketplace.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ce385de408d4b0a8cf1531f762a126a6b9b2c126
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2020
ms.locfileid: "77199071"
---
# <a name="understand-your-azure-external-services-charges"></a>Vysvětlení poplatků za externí služby v Azure
Externí služby zveřejňují externí dodavatelé softwaru na webu Azure Marketplace. Takovou externí službou, kterou si můžete zakoupit v Azure, přestože ji nevydal Microsoft, je například SendGrid. Prostřednictvím Azure Marketplace se prodávají i některé produkty Microsoftu.

## <a name="how-external-services-are-billed"></a>Způsob účtování externích služeb

- Pokud máte smlouvu se zákazníkem Microsoftu (MCA) nebo smlouvu s partnerem Microsoftu (MPA), služby třetích stran se vám fakturují spolu s ostatními službami Azure. Pokud chcete zjistit, jestli máte přístup ke smlouvě MCA nebo MPA, [zkontrolujte typ svého fakturačního účtu](#check-billing-account-type).
- Pokud smlouvu MCA ani MPA nemáte, externí služby se vám fakturují odděleně od ostatních služeb Azure.
- Každá externí služba má jiný model fakturace. Některé služby se účtují pomocí průběžných plateb, za jiné platíte pevné měsíční poplatky.
- Pro externí služby nemůžete využívat měsíční bezplatné kredity. Pokud používáte předplatné Azure, které zahrnuje [bezplatné kredity](https://azure.microsoft.com/pricing/spending-limits/), nedají se uplatnit na poplatky za externí služby. Při zřizování nové externí služby nebo prostředku se vám zobrazí upozornění:

    ![Upozornění související s nákupy na Azure Marketplace](./media/understand-azure-marketplace-charges/credit-warning.png)

<!-- ## View external service spending and history in the Azure portal
You can view a list of the external services that are on each subscription within the [Azure portal](https://portal.azure.com/):

1. Sign in to the [Azure portal](https://portal.azure.com/) as the account administrator.
2. In the Hub menu, select **Subscriptions**.

    ![Select Subscriptions in the Hub menu](./media/understand-azure-marketplace-charges/sub-button.png)
3. In the **Subscriptions** blade, select the subscription that you want to view, and then select **External services**.

    ![Select a subscription in the billing blade](./media/understand-azure-marketplace-charges/select-sub-external-services.png)
4. You should see each of your external service orders, the publisher name, service tier you bought, name you gave the resource, and the current order status. To see past bills, select an external service.

    ![Select an external service](./media/understand-azure-marketplace-charges/external-service-blade2.png)
5. From here, you can view past bill amounts including the tax breakdown.

    ![View external services billing history](./media/understand-azure-marketplace-charges/billing-overview-blade.png) -->

## <a name="view-and-download-invoices-for-external-services"></a>Zobrazení a stažení faktur za externí služby

Pokud máte smlouvu se zákazníkem Microsoftu (MCA) nebo smlouvu s partnerem Microsoftu (MPA), služby třetích stran se vám fakturují spolu s ostatními službami Azure. Pokud chcete zjistit, jestli máte přístup ke smlouvě MCA nebo MPA, [zkontrolujte typ svého fakturačního účtu](#check-billing-account-type). Pokud k nim máte přístup a chcete zobrazit poplatky za služby třetích stran, přečtěte si téma [Zobrazení a stažení faktur na webu Azure Portal](download-azure-invoice.md).

Pokud nemáte smlouvu MCA ani MPA, poplatky za služby třetích stran najdete na samostatné faktuře. Faktury za Azure Marketplace si můžete na webu Azure Portal zobrazit a stáhnout pomocí následujícího postupu:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Vyhledejte **Cost Management a fakturace**.
1. V nabídce vlevo vyberte **Faktury**.
1. Klikněte na kartu **Azure Marketplace a rezervace**.  ![Obrázek karty Azure Marketplace a rezervace](./media/understand-azure-marketplace-charges/invoice-tabs.png)
1. V rozevíracím seznamu předplatných vyberte předplatné obsahující externí služby, pro které chcete zobrazit faktury.

## <a name="external-spending-for-ea-customers"></a>Externí výdaje u zákazníků se smlouvou Enterprise

Zákazníci se smlouvou Enterprise si můžou zobrazit a stáhnout sestavy externích výdajů na webu Enterprise Portal. Základní informace najdete v tématu [Azure Marketplace pro zákazníky se smlouvou Enterprise](https://ea.azure.com/helpdocs/azureMarketplace).

## <a name="manage-payment-for-external-services"></a>Správa plateb za externí služby

Při nákupu externí služby si zvolíte předplatné Azure pro daný prostředek. Způsob platby za vybrané předplatné Azure se použije i jako způsob platby za externí službu. Pokud chcete způsob platby za externí službu změnit, je potřeba [změnit způsob platby předplatného Azure](../manage/change-credit-card.md) svázaného s danou externí službou. To, se kterým předplatným je objednávka externí služby svázaná, zjistíte pomocí následujícího postupu:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. V navigační nabídce vlevo klikněte na **Všechny prostředky**.
     ![Snímek obrazovky s položkou nabídky Všechny prostředky](./media/understand-azure-marketplace-charges/all-resources.png)
1. Vyhledejte externí službu.
1. Ve sloupci **Předplatné** vyhledejte název předplatného.
    ![Snímek s názvem předplatného pro určitý prostředek](./media/understand-azure-marketplace-charges/sub-selected.png)
1. Klikněte na název předplatného a [aktualizujte aktivní způsob platby](../manage/change-credit-card.md).

<!-- Update your payment methods for external service orders from the [Account Center](https://account.windowsazure.com/).

> [!NOTE]
> If you purchased your subscription with a Work or School account, [contact support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) to make changes to your payment method.

1. Sign in to the [Account Center](https://account.windowsazure.com/) and [navigate to the **marketplace** tab](https://account.windowsazure.com/Store)

    ![Select marketplace in the account center](./media/understand-azure-marketplace-charges/select-marketplace.png)
2. Select the external service you want to manage

    ![Select the external service you want to manage](./media/understand-azure-marketplace-charges/select-ext-service.png)
3. Click **Change payment method** on the right side of the page. This link brings you to a different portal to manage your payment method.

    ![Order summary](./media/understand-azure-marketplace-charges/change-payment.PNG)
4. Click **Edit info** and follow instructions to update your payment information.

    ![Select edit info](./media/understand-azure-marketplace-charges/edit-info.png) -->

## <a name="cancel-an-external-service-order"></a>Zrušení objednávky externí služby
Pokud chcete objednávku externí služby zrušit, na webu [Azure Portal](https://portal.azure.com) odstraňte příslušný prostředek.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. V navigační nabídce vlevo klikněte na **Všechny prostředky**.
    ![Snímek obrazovky s položkou nabídky Všechny prostředky](./media/understand-azure-marketplace-charges/all-resources.png)
1. Vyhledejte externí službu.
1. Zaškrtněte políčko vedle prostředku, který chcete odstranit.
1. Na panelu příkazů vyberte **Odstranit**.
    ![Snímek obrazovky s tlačítkem Odstranit](./media/understand-azure-marketplace-charges/delete-button.png)
1. V potvrzovacím okně zadejte *Ano*.
    ![Odstranění prostředku](./media/understand-azure-marketplace-charges/delete-resource.PNG)
1. Klikněte na **Odstranit**.

## <a name="check-billing-account-type"></a>Kontrola typu fakturačního účtu
[!INCLUDE [billing-check-account-type](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky
- [Začínáme s analýzou nákladů](../costs/quick-acm-cost-analysis.md)

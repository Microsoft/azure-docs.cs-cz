---
title: Principy poplatků za na faktuře smlouvy zákazníka Microsoftu – Azure
description: Zjistěte, jak číst a vysvětlení poplatků za na vaší faktuře.
author: jureid
manager: jureid
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: ee250589133abb1944ff17e39dc650cbae4279c6
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490680"
---
# <a name="understand-charges-on-your-microsoft-customer-agreement-invoice"></a>Principy poplatků za na vaší faktuře smlouvy zákazníka se společností Microsoft

Rozumíte poplatky na faktuře díky analýze jednotlivých transakcí. Ve fakturační účet zákaznické smlouvy Microsoft generováno faktury každý měsíc pro každou fakturační profil. Faktury zahrnuje všechny poplatky za předchozí měsíc. Na webu Azure Portal můžete zobrazit vašich fakturách se projeví. Další informace najdete v tématu [stahovat faktury zákaznické smlouvy Microsoft](billing-download-azure-invoice-daily-usage-date.md#download-invoices-for-a-microsoft-customer-agreement).

Tento článek se týká fakturační účet Microsoft zákaznické smlouvy. [Zaškrtněte, pokud máte přístup k zákaznické smlouvy Microsoft](#check-access-to-a-microsoft-customer-agreement).

## <a name="view-transactions-for-an-invoice-in-the-azure-portal"></a>Zobrazit transakce pro fakturu na webu Azure Portal

1. Přihlaste se k webu [Azure Portal](https://www.azure.com).

2. Vyhledejte **Cost Management a fakturace**.

    ![Snímek obrazovky zobrazující Azure portal vyhledejte Správa nákladů a fakturace](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Vyberte **všechny transakce** na levé straně stránky. V závislosti na přístup, budete muset vybrat fakturační účet, fakturační profil nebo oddíl faktury a pak vyberte **všechny transakce**.

4. Všechny transakce stránky zobrazí následující informace:

    ![Snímek obrazovky zobrazující seznam se fakturuje transakcí](./media/billing-understand-your-bill-mca/mca-billed-transactions-list.png)

    |Sloupec  |Definice  |
    |---------|---------|
    |Datum     | Datum transakce  |
    |ID faktury     | Identifikátor faktury, na kterém je teď účtovat transakce. Pokud odešlete žádost o podporu sdílení ID s podporou Azure ohledně urychlení jejich zpracování vaší žádosti o podporu |
    |Typ transakce     |  Typ transakce jako poplatků za nákup, zrušit a používání  |
    |Produktová řada     | Kategorie produktů, jako jsou výpočetní prostředky pro virtuální počítače nebo databáze pro službu Azure SQL database|
    |Sku produktu     | Jedinečný kód identifikující instanci produktu |
    |Částka     |  Částka transakce      |
    |Část faktury     | Transakce se zobrazí v této části billing invoice profilu |
    |Fakturační profil     | Transakce se zobrazí na faktuře fakturační profil |

5. Vyhledejte ID faktury transakce faktury filtrovat.

### <a name="view-transactions-by-invoice-sections"></a>Zobrazit transakce pomocí faktury

Části faktury usnadňují uspořádání ceny za fakturační profil faktury. Další informace najdete v tématu [vysvětlení části faktury](billing-mca-overview.md#invoice-sections). Při vygenerování faktury poplatky za všechny části v fakturační profil se zobrazí na faktuře.

Následující obrázek ukazuje poplatky za část faktury účetní oddělení na faktuře vzorku.

![Příklad obrázek znázorňující podrobnosti faktury části informace](./media/billing-understand-your-bill-mca/invoicesection-details.png)

Když jste identifikovali poplatky za oddíl faktury, můžete zobrazit transakce na webu Azure Portal k pochopení poplatků.

1. Přejděte na stránku všechny transakce na webu Azure Portal zobrazit transakce pro faktury. Další informace najdete v tématu [zobrazit transakce pro fakturu na webu Azure Portal](#view-transactions-for-an-invoice-in-the-azure-portal).

2. Filtrovat podle názvu části faktury Chcete-li zobrazit transakce.

## <a name="review-pending-charges-to-estimate-your-next-invoice"></a>Kontrola čekající poplatky pro odhad další faktury

Ve fakturační účet zákaznické smlouvy Microsoft poplatky jsou odhadované a považuje za probíhající, dokud se fakturují. Můžete zobrazit čekající poplatky webu Azure Portal pro odhad další faktury. Čekající jsou odhadované poplatky a že jsou bez daně. Skutečné náklady na další faktuře se liší od čekající poplatky.

### <a name="view-summary-of-pending-charges"></a>Zobrazení souhrnu probíhající poplatky

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Vyhledejte **Cost Management a fakturace**.

   ![Snímek obrazovky zobrazující Azure portal vyhledejte Správa nákladů a fakturace](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Vyberte fakturační profil. V závislosti na přístup budete muset vybrat fakturační účet. Fakturační účet, vyberte **fakturace profily** vyberte fakturační profil.

4. Vyberte **Souhrn** kartu z horní části obrazovky.

5. Poplatky za část zobrazení za měsíc k datu a poplatky v posledním měsíci.

   ![Snímek obrazovky zobrazující Azure portal vyhledejte Správa nákladů a fakturace](./media/billing-understand-your-bill-mca/mca-billing-profile-summary.png)

Poplatky za měsíc k datu se čeká na náklady pro aktuální měsíc a se účtují při vygenerování faktury za měsíc. Pokud není fakturu za poslední měsíc i nadále vygenerována, pak poplatky v posledním měsíci také čekající na vyřízení a zobrazí se na další faktuře.

### <a name="view-pending-transactions"></a>Zobrazit čekající transakce

Při určování čekající poplatky za rozumíte náklady díky analýze, které poskytly se účtovat poplatky za jednotlivé transakce. V tomto okamžiku čekající využití poplatky nejsou zobrazeny na stránce všechny transakce. Použití čekající poplatků můžou podívat na stránce předplatná Azure. Další informace najdete v tématu [zobrazení čekajících poplatky za využívání](#view-pending-usage-charges)

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Vyhledejte **Cost Management a fakturace**.

   ![Snímek obrazovky zobrazující Azure portal vyhledejte Správa nákladů a fakturace](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Vyberte fakturační profil. V závislosti na přístup budete muset vybrat fakturační účet. Fakturační účet, vyberte **fakturace profily** vyberte fakturační profil.

4. Vyberte **všechny transakce** na levé straně stránky.

5. Vyhledejte *čekající*. Použití **Timespan** filtr na zobrazení čekajících náklady pro aktuální nebo poslední měsíc.

   ![Snímek obrazovky zobrazující seznam čekající transakce](./media/billing-understand-your-bill-mca/mca-pending-transactions-list.png)

### <a name="view-pending-usage-charges"></a>Zobrazit poplatky za využívání čekající na vyřízení

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Vyhledejte *Cost Management a fakturace*.

   ![Snímek obrazovky zobrazující Azure portal vyhledejte Správa nákladů a fakturace](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Vyberte fakturační profil. V závislosti na přístup budete muset vybrat fakturační účet. Fakturační účet, vyberte **fakturace profily** vyberte fakturační profil.

4. Vyberte **Všechna předplatná** jeden levé části stránky.

5. Na stránce předplatná Azure zobrazí aktuální a poplatky v posledním měsíci pro každé předplatné v fakturační profil. Poplatky za měsíc k datu se čeká na náklady pro aktuální měsíc a se účtují při vygenerování faktury za měsíc. Pokud není fakturu za poslední měsíc i nadále vygenerována, pak poplatky v posledním měsíci také čekající na vyřízení.

    ![Snímek obrazovky zobrazující seznam předplatných Azure pro fakturační profil](./media/billing-understand-your-bill-mca/mca-billing-profile-subscriptions-list.png)

## <a name="analyze-your-azure-usage-charges"></a>Analyzujte své poplatky za využití Azure

Použijte k analýze poplatky podle využití Azure souboru CSV využití a poplatků. Soubor pro fakturu nebo si můžete stáhnout čekající poplatky. Další informace najdete v tématu [získat Azure billing invoice a denní data o využití](billing-download-azure-invoice-daily-usage-date.md).

### <a name="view-detailed-usage-by-invoice-section"></a>Zobrazit podrobné informace o využití z oddílu faktury

Můžete filtrovat Azure file využití a poplatků sloučit poplatky za využívání pro oddíly faktury.

Následující kroky vás provedou sjednocování poplatky za výpočetní kapacitu pro části faktury účetní oddělení:

![Příklad obrázek znázorňující podrobnosti faktury části informace](./media/billing-understand-your-bill-mca/invoicesection-details.png)

 | Faktury PDF | Využití Azure a poplatky za sdíleného svazku clusteru |
 | --- | --- |
 |Účetní oddělení |invoiceSectionName |
 |Poplatky za využívání - plánu Microsoft Azure |productOrderName |
 |Compute |serviceFamily |

1. Filtr **invoiceSectionName** sloupec v souboru CSV do **účetní oddělení**.
2. Filtr **productOrderName** sloupec v souboru CSV do **plánu Microsoft Azure**.
3. Filtr **serviceFamily** sloupec v souboru CSV do **Microsoft.Compute**.

![Snímek obrazovky, který zobrazuje využití a poplatky za soubor filtrované podle části faktury](./media/billing-understand-your-bill-mca/billing-usage-file-filtered-by-invoice-section.png)


### <a name="view-detailed-usage-by-subscription"></a>Zobrazit podrobné využití podle předplatného

Můžete filtrovat Azure využití a poplatků soubor CSV ke sjednocení poplatků za využití pro vaše předplatná. Chcete-li zobrazit všechna předplatná v profilu fakturace, najdete v článku [zobrazení čekajících poplatky za využívání](#view-pending-usage-charges).

Při určování poplatky za předplatné použijte k analýze se účtovat poplatky za využití a poplatky za Azure souboru CSV.

Následující obrázek ukazuje seznam předplatných na webu Azure Portal.

![Snímek obrazovky zobrazující seznam předplatných Azure pro fakturační profil](./media/billing-understand-your-bill-mca/mca-billing-profile-subscriptions-list-highlighted.png)

Filtr **subscriptionName** sloupec v Azure souboru CSV využití a náklady na **WA_Subscription** Chcete-li zobrazit podrobné informace o použití poplatky WA_Subscription.

![Snímek obrazovky, který zobrazuje využití a poplatky za soubor filtrovat podle předplatného](./media/billing-understand-your-bill-mca/billing-usage-file-filtered-by-subscription.png)

## <a name="pay-your-bill"></a>Platit vyúčtování

Pokyny, jak platit váš účet se zobrazí v dolní části faktury. [Zjistěte, jak platit](billing-mca-understand-your-invoice.md#how-to-pay).

Pokud jste už jste platili vyúčtování, můžete zkontrolovat stav platby na fakturách stránce na webu Azure Portal.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrola přístupu na základě smlouvy Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další postup

Další informace týkající se faktury a podrobné informace o použití najdete v tématu:

- [Jak získat data o denním využití a fakturu za Azure](billing-download-azure-invoice-daily-usage-date.md)
- [Vysvětlení podmínek na faktuře smlouvy zákazníka se společností Microsoft](billing-mca-understand-your-invoice.md)
- [Vysvětlení podmínek vašeho použití smlouvy zákazníka se společností Microsoft sdíleného svazku clusteru](billing-mca-understand-your-usage.md)

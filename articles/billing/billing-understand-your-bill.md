---
title: Porozumět vaší faktuře pro Azure
description: Zjistěte, jak číst a porozumět využívání a fakturovaná částka u předplatného Azure
services: ''
documentationcenter: ''
author: tonguyen10
manager: tonguyen
editor: ''
tags: billing
ms.assetid: 32eea268-161c-4b93-8774-bc435d78a8c9
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: tonguyen
ms.openlocfilehash: ec4fd970c6b552d87e006f9e0ab55d3495c9b0c8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="understand-your-bill-for-microsoft-azure"></a>Vysvětlení vašeho vyúčtování služeb Microsoft Azure
Chcete-li vysvětlení faktury Azure, porovnejte faktury s podrobné soubor denní využití a správu sestav náklady na portálu Azure.

>[!NOTE]
>Tento článek se nevztahuje na zákazníky Enterprise Agreement (EA). Pokud jste již zákazníka EA [faktury dokumentaci můžete najít na podnikovém portálu.](https://ea.azure.com/helpdocs/understandingYourInvoice)  

Pokud chcete získat PDF části faktury a kopii podrobné stahování sdíleného svazku clusteru souboru denní využití, najdete v části [získat vaše Azure fakturace faktury a denní data o využití](billing-download-azure-invoice-daily-usage-date.md). 

Podrobné podmínky a popisy faktury a podrobný soubor denní využití najdete v tématu [pochopit podmínky v Microsoft Azure faktury](billing-understand-your-invoice.md) a [Rady pro pochopení podmínky v Microsoft Azure podrobné využití](billing-understand-your-usage.md). 

Podrobnosti sestavy náklady na správu najdete v tématu [Správa nákladů na portálu Azure](https://docs.microsoft.com/azure/billing/billing-getting-started).

## <a name="charges"></a>Jak se ujistěte se, zda jsou informace správné poplatky v fakturou?

>[!VIDEO https://www.youtube.com/embed/3YegFD769Pk]

Pokud je na vaší faktuře, které chcete podrobnosti na zpoplatněny, existuje několik možností.

### <a name="option-1-review-your-invoice-and-compare-the-usage-and-costs-with-the-detailed-usage-csv-file"></a>Možnost 1: Zkontrolujte faktury a porovnání využití a náklady s podrobné informace o použití souboru CSV

Podrobné informace o použití souboru CSV zobrazuje vaše poplatky podle denní využití a fakturační období. Chcete-li získat podrobné informace o použití souboru CSV, přečtěte si téma [získat vaše Azure fakturace faktury a denní data o využití](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date).

Vaše poplatky za používání se zobrazí na úrovni měření. Následující termíny mají stejný význam v faktury a podrobné informace o použití souboru. Například fakturačního cyklu na faktuře je ekvivalentní fakturační období uvedené v souboru podrobné informace o použití.

 | Faktura (PDF) | Podrobné informace o použití (CSV)|
 | --- | --- |
|Fakturační cyklus | Fakturační období |
 |Název |Kategorie měření |
 |Typ |Podkategorie měření |
 |Prostředek |Název měření |
 |Oblast |Oblast měření |
 |Spotřebované |Spotřebované množství |
 |Zahrnuje |Zahrnuté množství |
 |Fakturovatelné |Překročené množství |

**Poplatky za používání** části faktury má celkovou hodnotu pro každé monitorování, která se spotřebovala během fakturačního období. Například následující snímek obrazovky ukazuje použití zdarma pro službu Azure Scheduler.

![Poplatky za používání faktury](./media/billing-understand-your-bill/1.png)

**Příkaz** části vaší podrobné informace o použití sdíleného svazku clusteru se zobrazí stejné poplatků. Obě *Potřebováno* velikost a *hodnotu* odpovídat faktury.

![Poplatky za používání sdíleného svazku clusteru](./media/billing-understand-your-bill/2.png)

Chcete-li zobrazit rozpis tento poplatek za každý den, přejděte na **denní využití** části sdílený svazek clusteru. Filtrovat "Scheduler" v části *měření kategorie* a zobrazí se které dny byl použit měřidla a kolik se spotřebovala. *Prostředků* a *skupiny prostředků* informace jsou také uvedeny pro porovnání. *Potřebováno* hodnoty by měla dohromady informace zobrazené na faktuře.

![Denní využití části ve sdíleném svazku clusteru](./media/billing-understand-your-bill/3.png)

Chcete-li získat náklady za den, vynásobte *Potřebováno* částky s *míra* z hodnoty **příkaz** části.

Další informace o faktuře najdete v tématu [pochopit Azure faktury](billing-understand-your-invoice.md).

Další informace o všech sloupcích v sdílený svazek clusteru najdete v tématu [porozumět Azure podrobné používání](billing-understand-your-invoice.md).

### <a name="option-2-review-your-invoice-and-compare-with-the-usage-and-costs-in-the-azure-portal"></a>Možnost 2: Přečtěte si faktury a porovnání s využití a náklady na portálu Azure

Portál Azure také můžete ověřit vaše poplatky. Portál Azure poskytuje náklady na správu grafy pro rychlý přehled o vaší využití a poplatky na faktury.

Pokud chcete pokračovat s z výše uvedených příkladech, přejděte [předplatné](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade), vyberte předplatné a potom zvolte **analýza nákladů**. Odtud můžete zadat časové rozpětí a najdete v části poplatků využití služby Azure Scheduler.

![Zobrazení analýza nákladů na portálu Azure](./media/billing-understand-your-bill/4.png)

Chcete-li zobrazit denní rozpis nákladů v **náklady historie**, klikněte na řádek.

![Zobrazení historie náklady na portálu Azure](./media/billing-understand-your-bill/5.png)

Další informace najdete v tématu [zabránit neočekávané náklady s Azure fakturace a náklady na správu](billing-getting-started.md#costs).

## <a name="external"></a>Co externí poplatky za služby?
Externích služeb (také označované jako Azure Marketplace objednávky) jsou k dispozici nezávislé služby dodavatelé a se účtují samostatně. Poplatků nezobrazovat Azure faktury. Další informace najdete v tématu [pochopit Azure externí poplatky za](billing-understand-your-azure-marketplace-charges.md).

## <a name="payment"></a>Jak lze vytvořit platba?

Pokud jste nastavili kreditní nebo debetní karty jako váš způsob platby, je platba je účtován automaticky do 10 dnů po skončení fakturačního období. Na příkazu platební karty, by vyslovení položky řádku **MSFT Azure**.

Pokud jste [platíte podle fakturace](billing-how-to-pay-by-invoice.md), odeslání vaší platební do umístění uvedených v dolní části faktury. Další pomoc [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="how-do-i-check-the-status-of-a-payment-made-by-credit-card"></a>Jak zkontrolovat stav platba platební karty?

[Vytvořit lístek podpory](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) požádat o stav platby. 

## <a name="tips-for-cost-management"></a>Tipy pro náklady na správu
- Odhad nákladů pomocí [cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/) a [celkové náklady na vlastnictví kalkulačky](https://aka.ms/azure-tco-calculator)a získat [podrobné informace o cenách pro každou službu](https://azure.microsoft.com/pricing/).
- [Nastavit výstrahy fakturace](billing-set-up-alerts.md).
- [Zkontrolujte využití a náklady na portálu Azure pravidelně](billing-getting-started.md#costs).

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Obraťte se na podporu.

Pokud stále potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) získat rychle vyřešit problém.

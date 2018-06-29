---
title: Porozumět vaší faktuře pro Azure | Microsoft Docs
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
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/14/2018
ms.author: tonguyen
ms.openlocfilehash: 689ea9e0d029bb65bc579fc914c6ed3073b4a96b
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063823"
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

## <a name="are-there-different-azure-customer-types-how-do-i-know-what-customer-type-i-am"></a>Existují různé Azure zákazníků typy? Jak zjistím, jaké zákazníka typ jsem?
Existují různé typy Azure zákazníků. Abyste lépe pochopili ceny a faktury, najdete v následujících popisech typ odběratele.

- **Enterprise**: podnikoví zákazníci zaregistrovali smlouvu Enterprise Agreement s Azure a ujistěte se, vyjednané peněžních závazků a získat přístup k vlastní ceny prostředků Azure.
- **Web přímo**: webové přímého zákazníkům nezaregistrovali jakékoli vlastní smlouvy, s Azure. Těchto zákazníků prostřednictvím azure.com registraci k Azure a přijímat veřejné přístupných ceny pro všechny prostředky Azure.
- **Cloud poskytovatele služeb**: poskytovatele cloudových služeb jsou obvykle společností, které pronajaté podle koncoví zákazníka k sestavení řešení v Azure.

## <a name="why-dont-i-see-the-cost-the-resource-i-have-created-in-my-bill"></a>Proč nevidím náklady na prostředek, který vytvořil jsem v mé faktury?
Azure nepodporuje není faktury přímo podle prostředků náklady. Fakturace se provádí na základě vypnout jeden nebo více měřidla, které se používají ke sledování využití prostředků v celé jeho životnosti. Tato měřidla jsou poté použít k výpočtu kusovníku. Další informace o Azure měření níže v tématu.

## <a name="how-does-azure-charge-metering-work"></a>Jak Azure účtují měření pracovní?
Když začne pracovat jeden prostředků Azure, jako je například virtuální počítač, bude mít také vytvořit jeden nebo více instancí měření. Tato měřidla se používají ke sledování využití prostředku v čase. Každý měření vysílá záznamů využití, které jsou pak používány Azure v našem náklady systému měření k výpočtu kusovníku. 

Například jeden virtuální počítač vytvořený v Azure může mít následující měřidla vytvořili ke sledování jeho využití:

- Výpočetní čas
- Hodiny IP adresy
- Přenos dat
- Odchozí přenosy dat
- Spravovaných disků na úrovni Standard
- Operace spravovaných disků na úrovni Standard
- Standardní v/v – Disk
- Standardní bloku vstupně-výstupní operace čtení objektů Blob
- Standardní bloku vstupně-výstupní operace zápisu objektů Blob
- Odstranění objektů Blob standardní vstupně-výstupní – blok

Po vytvoření virtuálního počítače zahájíte každé z nich výše měřidla generování záznamů využití. Toto použití se potom použije v Azure měření systému, společně s cena monitorování k určení, kolik zákazníka je účtován.

> [!Note]
> Výše uvedený příklad měřidla může být pouze podmnožinu měřidla vytvořit virtuální počítač, který je vytvořen.

## <a name="what-is-the-difference-between-azure-1st-party-charges-and-azure-marketplace-charges"></a>Jaký je rozdíl mezi Azure 1 strany poplatky a poplatky Azure Marketplace?
Azure 1 strany poplatky jsou pro prostředky, které jsou přímo vyvinutých, které nabízí Azure. Azure Marketplace poplatky jsou pro prostředky, které byly vytvořeny dodavatelé softwaru třetích stran, které jsou k dispozici pro použití prostřednictvím Azure marketplace. Například Brána Firewall Barracuda je prostředek Azure marketplace nabízí třetí strany. Všechny poplatky za bránu firewall a jeho odpovídající měřidla zobrazí jako marketplace poplatky. 

## <a name="tips-for-cost-management"></a>Tipy pro náklady na správu
- Odhad nákladů pomocí [cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/) a [celkové náklady na vlastnictví kalkulačky](https://aka.ms/azure-tco-calculator)a získat [podrobné informace o cenách pro každou službu](https://azure.microsoft.com/pricing/).
- [Nastavit výstrahy fakturace](billing-set-up-alerts.md).
- [Zkontrolujte využití a náklady na portálu Azure pravidelně](billing-getting-started.md#costs).

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Obraťte se na podporu.

Pokud stále potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) získat rychle vyřešit problém.

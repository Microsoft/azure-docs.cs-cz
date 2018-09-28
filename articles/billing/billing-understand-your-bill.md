---
title: Vysvětlení vašeho vyúčtování služeb Azure | Dokumentace Microsoftu
description: Zjistěte, jak číst a využití a vyúčtování předplatného Azure
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
ms.author: cwatson
ms.openlocfilehash: be2e774bc4220d5d93f555263fa9f342f449f8b2
ms.sourcegitcommit: 42405ab963df3101ee2a9b26e54240ffa689f140
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/28/2018
ms.locfileid: "47423438"
---
# <a name="understand-your-bill-for-microsoft-azure"></a>Vysvětlení vašeho vyúčtování služeb Microsoft Azure
Chcete-li vysvětlení vašeho vyúčtování služeb Azure, porovnejte faktuře se podrobný soubor denní využití a sestav služby cost management na webu Azure Portal.

>[!NOTE]
>Tento článek se nevztahuje na zákazníky Enterprise Agreement (EA). Pokud jste zákazník EA [faktury dokumentaci můžete najít na portálu Enterprise.](https://ea.azure.com/helpdocs/understandingYourInvoice)  

Chcete-li získat PDF faktury a zkopírujte váš podrobné denní využití soubor CSV ke stažení, přečtěte si téma [získat Azure billing invoice a denní data o využití](billing-download-azure-invoice-daily-usage-date.md). 

Podrobné podmínky a popis faktury a podrobný soubor denní využití najdete v tématu [vysvětlení podmínek na faktuře Microsoft Azure](billing-understand-your-invoice.md) a [vysvětlení podmínky v Microsoft Azure podrobné využití](billing-understand-your-usage.md). 

Podrobnosti o sestav služby cost management, najdete v části [Správa nákladů na webu Azure portal](https://docs.microsoft.com/azure/billing/billing-getting-started).

> [!div class="nextstepaction"]
> [Pomozte vylepšit dokumentaci k fakturaci Azure](https://go.microsoft.com/fwlink/p/?linkid=2010091)

## <a name="charges"></a>Jak můžu se ujistit, že jsou správné poplatky na mé faktuře?

>[!VIDEO https://www.youtube.com/embed/3YegFD769Pk]

Pokud je na vaší faktuře, které chcete podrobnosti na poplatek, máte několik možností.

### <a name="option-1-review-your-invoice-and-compare-the-usage-and-costs-with-the-detailed-usage-csv-file"></a>Možnost 1: Kontrola faktury a porovnat využití a nákladů s podrobnými informacemi o využití souboru CSV

Soubor CSV podrobné informace o využití zobrazuje poplatky podle fakturačního období a denní využívání. Chcete-li získat podrobné informace o použití souboru CSV, naleznete v tématu [získat Azure billing invoice a denní data o využití](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date).

Své poplatky za využití se zobrazují na úrovni měřiče. Následující terminologií mají stejný význam v faktury a podrobné informace o použití souboru. Například je ekvivalentní fakturační období, zobrazeny v podrobné informace o použití souboru fakturačního cyklu na faktuře.

 | Faktury (PDF) | Podrobné informace o využití (CSV)|
 | --- | --- |
|Fakturační cyklus | Fakturační období |
 |Název |Kategorie měření |
 |Typ |Podkategorie měřiče |
 |Prostředek |Název měření |
 |Oblast |Oblast měření |
 |Spotřebované |Spotřebované množství |
 |Zahrnuje |Zahrnuté množství |
 |Fakturovatelné |Překročené množství |

**Poplatky za využívání** části faktury má celkovou hodnotu pro každého měřiče, které se během fakturačního období. Například následující snímek obrazovky ukazuje využití poplatek za službu Azure Scheduler.

![Poplatky za využívání faktury](./media/billing-understand-your-bill/1.png)

**Příkaz** části Podrobné využití sdíleného svazku clusteru se zobrazí stejné poplatek. Oba *spotřebovaná* velikost a *hodnotu* odpovídat faktury.

![Poplatky za používání sdíleného svazku clusteru](./media/billing-understand-your-bill/2.png)

Chcete-li zobrazit rozpis tento poplatek za každý den, přejděte **denního využití** část sdílený svazek clusteru. Filtrovat "Plánovač" v části *kategorie měřiče* a uvidíte, které dny se použil měřič a kolik spotřebovával. *Prostředků* a *skupiny prostředků* informace jsou také uvedeny pro porovnání. *Spotřebovaná* hodnot by měl nasčítá a co se zobrazí na faktuře.

![Části denní využívání ve sdíleném svazku clusteru](./media/billing-understand-your-bill/3.png)

K získání nákladů za den, vynásobte *spotřebovaná* částky se *míra* hodnotu **příkaz** části.

Další informace o faktury, naleznete v tématu [pochopit faktuře Azure](billing-understand-your-invoice.md).

Další informace o jednotlivých sloupců ve sdíleném svazku clusteru, naleznete v tématu [pochopit využívání služeb Azure podrobné](billing-understand-your-invoice.md).

### <a name="option-2-review-your-invoice-and-compare-with-the-usage-and-costs-in-the-azure-portal"></a>Možnost 2: Kontrola faktury a porovnejte s využití a nákladů na webu Azure Portal

Na webu Azure portal také můžete ověřit poplatky. Na webu Azure portal poskytuje správu grafy nákladů a získejte rychlý přehled o využití a náklady na vaší faktuře.

Chcete-li pokračovat v příkladu výše, navštivte [stránce předplatná](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade), vyberte své předplatné a pak zvolte **analýza nákladů**. Odtud můžete zadat časový rozsah a zobrazit využití poplatky za službu Azure Scheduler.

![Zobrazení analýza nákladů na webu Azure portal](./media/billing-understand-your-bill/4.png)

Chcete-li zobrazit rozpis nákladů denní v **historii nákladů**, klikněte na řádek.

![Zobrazení historie nákladů na webu Azure portal](./media/billing-understand-your-bill/5.png)

Další informace najdete v tématu [jak zabránit neočekávaným nákladům se správou nákladů a fakturací Azure](billing-getting-started.md#costs).

## <a name="external"></a>A co poplatků za externí služby?
Externí služby (také označované jako Azure Marketplace objednávky) jsou k dispozici služba nezávislá dodavatelé a služby se účtují zvlášť. Poplatky se nezobrazují na faktuře Azure. Další informace najdete v tématu [vysvětlení Azure poplatků za externí služby](billing-understand-your-azure-marketplace-charges.md).

## <a name="payment"></a>Jak mohu učinit platby?

Pokud jste nastavili kreditní nebo debetní kartu jako způsob platby, způsob platby se účtuje automaticky do 10 dnů po fakturačního období skončí. Na váš výpis z platební karty, položky řádku fungujeme **MSFT Azure**.

Pokud jste [platit ve fakturaci](billing-how-to-pay-by-invoice.md), odeslat vaše platba do umístění uvedených v dolní části faktury. Další nápovědu [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="how-do-i-check-the-status-of-a-payment-made-by-credit-card"></a>Jak můžu zkontrolovat stav Platba pomocí platební karty?

[Vytvoření lístku podpory](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) požádat o stav vaše platba. 

## <a name="are-there-different-azure-customer-types-how-do-i-know-what-customer-type-i-am"></a>Existují typy různých zákazníků Azure? Jak zjistím, jaké zákazníka jsem?
Existují různé typy zákazníků Azure. Pro lepší pochopení cen a faktury, najdete v následujících popisech typ zákazníka.

- **Enterprise**: podnikoví zákazníci podepsaly smlouvu Enterprise s Azure a ujistěte se, vyjednávaný peněžní závazky a získat přístup k získání vlastních cen pro prostředky Azure.
- **Web Direct**: Web přímí zákazníci se nezaregistrovali jste žádné vlastní smlouvu s Azure. Tito zákazníci si zaregistrovali službu Azure prostřednictvím webu azure.com, získáte veřejný internetový ceny platné pro všechny prostředky Azure.
- **Poskytovatel cloudových služeb**: poskytovatelů cloudových služeb jsou obvykle společností, které byly najaté podle koncový zákazník vytvářet řešení nad Azure.

## <a name="why-dont-i-see-the-cost-the-resource-i-have-created-in-my-bill"></a>Proč nevidím náklady na prostředku, kterou vytvořím na mém vyúčtování?
Azure fakturuje se u není vyúčtování přímo na prostředek nákladů. Fakturace se provádí na základě vypnout jeden nebo více měřiče, které se používají ke sledování využití prostředku v průběhu svého životního cyklu. Těchto měřičů se použije k výpočtu faktury. Další informace o Azure měření níže.

## <a name="how-does-azure-charge-metering-work"></a>Jak se v Azure účtují měření práce?
Po zprovoznění jeden prostředek Azure, jako je například virtuální počítač, bude mít vytvořených také jeden nebo více instancí měřiče. Tato měření se používají ke sledování využití prostředku v čase. Vysílá jednotlivých měření využití záznamů, které se pak používají Azure v našich náklady systému měření k výpočtu faktury. 

Například jeden virtuální počítač vytvořený v Azure může mít následující měřiče vytvořil pro sledování jeho používání:

- Výpočetní čas
- Hodiny IP adresy
- Příchozí přenos dat
- Odchozí přenosy dat
- Spravovaných disků úrovně Standard
- Operace spravovaných disků úrovně Standard
- Standardní vstupně-výstupní operace disku
- Standardním bloku vstupně-výstupních operací čtení objektů Blob
- Standardním bloku vstupně-výstupních operací zápisu objektů Blob
- Standardní v/v úrovně bloku odstranění objektů Blob

Po vytvoření virtuálního počítače, každý z nich měřičů výše začne generování záznamů využití. Toto použití se potom použije v Azure společně s cena měřiče měřicího systému k určení, kolik zákazníků se účtuje.

> [!Note]
> Příklad měřiče uvedené výše může být pouze podmnožinu měřiče vytvořili virtuální počítač, který je vytvořen.

## <a name="what-is-the-difference-between-azure-1st-party-charges-and-azure-marketplace-charges"></a>Jaký je rozdíl mezi Azure 1. poplatky za stran a webu Azure Marketplace?
Azure 1 stran poplatky jsou určené pro prostředky, které jsou vyvíjeny a které Azure nabízí přímo. Poplatky za Azure Marketplace jsou určené pro prostředky, které byly vytvořeny dodavateli softwaru třetích stran, které jsou k dispozici prostřednictvím Azure marketplace. Například Brána Firewall Barracuda je prostředek Azure marketplace nabízí třetí stranou. Všechny poplatky za bránu firewall a jeho odpovídajícího měření se zobrazí jako poplatků za marketplace. 

## <a name="tips-for-cost-management"></a>Tipy pro správu nákladů
- Odhad nákladů s využitím [cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/) a [celkové náklady na vlastnictví kalkulačky](https://aka.ms/azure-tco-calculator), dostanete [podrobné informace o cenách pro každou službu](https://azure.microsoft.com/pricing/).
- [Kontrola využití a nákladů pravidelně na webu Azure portal](billing-getting-started.md#costs).

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Obraťte se na podporu.

Pokud stále potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pro rychlé vyřešení problému.

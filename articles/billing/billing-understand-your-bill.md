---
title: Vysvětlení vašeho vyúčtování služeb Azure | Dokumentace Microsoftu
description: Zjistěte, jak číst a využití a vyúčtování předplatného Azure
services: ''
documentationcenter: ''
author: tonguyen10
manager: alherz
editor: ''
tags: billing
ms.assetid: 32eea268-161c-4b93-8774-bc435d78a8c9
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2019
ms.author: banders
ms.openlocfilehash: fdf346348be11a9f592fa3eff40e2f56e94ba656
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2019
ms.locfileid: "54904314"
---
# <a name="understand-your-bill-for-microsoft-azure"></a>Vysvětlení vašeho vyúčtování služeb Microsoft Azure
Chcete-li vysvětlení vašeho vyúčtování služeb Azure, porovnejte faktuře se podrobný soubor denní využití a sestav služby cost management na webu Azure Portal.

Tento článek se nevztahuje na zákazníky Azure se smlouvou Enterprise (EA zákazníci). Pokud jste zákazník EA, přečtěte si téma [vysvětlení vašeho vyúčtování služeb Azure zákazníky se smlouvou Enterprise](billing-understand-your-bill-ea.md).  

Vysvětlení toho, jak funguje fakturace v programu Azure Cloud Solution Provider (CSP pro Azure), včetně fakturační cyklus, ceny a využití, viz [fakturační základní informace o Azure CSP](/azure/cloud-solution-provider/billing/azure-csp-billing-overview/).

## <a name="charges"></a>Zkontrolujte své poplatky za

>[!VIDEO https://www.youtube.com/embed/3YegFD769Pk]

Pokud je na vaší faktuře, který chcete získat další informace o poplatek, můžete porovnat využití a nákladů souborem využití nebo na webu Azure portal.

### <a name="option-1-compare-usage-and-costs-with-usage-file"></a>Option 1: Porovnání využití a náklady na využití souboru

Soubor CSV podrobné informace o využití zobrazuje poplatky podle fakturačního období a denní využívání. Pokud chcete získat soubor, naleznete v tématu [získat Azure billing invoice a denní data o využití](billing-download-azure-invoice-daily-usage-date.md).

Své poplatky za využití se zobrazují na úrovni měřiče. Následující terminologií mají stejný význam v faktury a podrobné informace o použití souboru. Například fakturačního cyklu na faktuře je stejný jako fakturační období, zobrazeny v souboru podrobné informace o využití.

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

Chcete-li zobrazit rozpis denní tento poplatek, přejděte **denního využití** část sdílený svazek clusteru. Filtrovat podle "Plánovač" *kategorie měřiče*. Uvidíte, které dny se použil měřič a kolik spotřebovával. *Prostředků* a *skupiny prostředků* informace jsou také uvedeny pro porovnání. *Spotřebovaná* hodnot by měl nasčítá a co se zobrazí na faktuře.

![Části denní využívání ve sdíleném svazku clusteru](./media/billing-understand-your-bill/3.png)

K získání nákladů za den, vynásobte *spotřebovaná* částky se *míra* hodnotu **příkaz** části.

Další informace naleznete v tématu:

- [Principy Azure faktury](billing-understand-your-invoice.md)
- [Vysvětlení podrobných využívání služeb Azure](billing-understand-your-invoice.md)

### <a name="option-2-compare-the-usage-and-costs-with-the-azure-portal"></a>Option 2: Porovnání využití a náklady na webu Azure portal

Na webu Azure portal také můžete ověřit poplatky. Získat rychlý přehled fakturovaných využití a poplatků, zobrazte grafy náklady na správu.

1. Na webu Azure Portal, přejděte na [předplatná](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Vyberte své předplatné > **analýza nákladů**. 
1. Filtrovat podle **Timespan**.
1. Chcete-li pokračovat v předchozím příkladu, uvidíte využití poplatek za službu Azure Scheduler.

   ![Zobrazení analýza nákladů na webu Azure portal](./media/billing-understand-your-bill/4.png)

1. Vyberte tento řádek zobrazíte denní rozpis nákladů.

   ![Zobrazení historie nákladů na webu Azure portal](./media/billing-understand-your-bill/5.png)

Další informace najdete v tématu [jak zabránit neočekávaným nákladům se správou nákladů a fakturací Azure](billing-getting-started.md#costs).

## <a name="external"></a>Externí služby se účtují zvlášť

Externí služby nebo poplatků za marketplace, jsou určené pro prostředky, které byly vytvořeny od dodavatelů softwaru třetích stran. Tyto prostředky jsou k dispozici pro použití na webu Azure Marketplace. Například Brána Firewall Barracuda je prostředek Azure marketplace, které nabízí třetí strany. Všechny poplatky za bránu firewall a jeho odpovídajícího měření jeví jako poplatků za externí služby.

Poplatků za externí služby se účtují zvlášť. Poplatky se nezobrazují na faktuře Azure. Další informace najdete v tématu [vysvětlení Azure poplatků za externí služby](billing-understand-your-azure-marketplace-charges.md).

## <a name="resources-billed-by-usage-meters"></a>Účtuje se podle měřiče využití prostředků

Azure nemá přímo faktura za založená na prostředek nákladů. Poplatky za prostředek se počítají pomocí jednoho nebo více měřiče. Tato měření se používají ke sledování využití prostředku v průběhu svého životního cyklu. Těchto měřičů se použije k výpočtu faktury.

Například při vytváření jeden prostředek Azure, jako jsou virtuální počítače má jednu nebo víc instancí měřiče vytvořili. Tato měření se používají ke sledování využití prostředku v čase. Vysílá jednotlivých měření využití záznamy, které Azure používá k výpočtu faktury.

Například jeden virtuální počítač (VM) vytvořené v Azure může mít následující měřiče vytvořil pro sledování jeho používání:

- Výpočetní čas
- Hodiny IP adresy
- Přenos příchozích dat
- Odchozí přenosy dat
- Spravovaných disků úrovně Standard
- Operace spravovaných disků úrovně Standard
- Standardní vstupně-výstupní operace disku
- Standardním bloku vstupně-výstupních operací čtení objektů Blob
- Standardním bloku vstupně-výstupních operací zápisu objektů Blob
- Standardní v/v úrovně bloku odstranění objektů Blob

Při vytvoření virtuálního počítače, každý z nich těchto měřičů začíná generování záznamů využití. Toto využití a měřiče cen je sledována v Azure, monitorování míry využívání systému.

## <a name="payment"></a>Platit vyúčtování

Pokud jste nastavili kreditní nebo debetní kartu jako způsob platby, způsob platby se účtuje automaticky do 10 dnů po fakturačního období skončí. Na váš výpis z platební karty, položky řádku fungujeme **MSFT Azure**.

Chcete-li změnit kreditní nebo debetní karty, kterou se účtuje [přidat, aktualizujte nebo odeberte kreditní nebo debetní kartou pro Azure](billing-how-to-change-credit-card.md).

Pokud jste [platit fakturou](billing-how-to-pay-by-invoice.md), odeslat vaše platba do umístění uvedených v dolní části faktury.

Chcete-li zkontrolovat stav vaší platby [vytvořit lístek podpory](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).


## <a name="tips-for-cost-management"></a>Tipy pro správu nákladů

- Odhad nákladů s využitím:
  - [Cenovou kalkulačku Azure](https://azure.microsoft.com/pricing/calculator/)
  - [Celkové náklady na vlastnictví kalkulačky](https://aka.ms/azure-tco-calculator)
  - [Podrobné informace o cenách pro každou službu](https://azure.microsoft.com/pricing/)
- [Kontrola využití a nákladů pravidelně na webu Azure portal](billing-getting-started.md#costs).

## <a name="learn-more"></a>Další informace

- [Získat Azure fakturační faktury a dat o denním využití](billing-download-azure-invoice-daily-usage-date.md)
- [Vysvětlení podmínek na faktuře Microsoft Azure](billing-understand-your-invoice.md)
- [Vysvětlení podmínek na Microsoft Azure podrobné využití](billing-understand-your-usage.md)
- [Správa nákladů na Azure portal](https://docs.microsoft.com/azure/billing/billing-getting-started)
- [Jak zabránit neočekávaným nákladům se správou nákladů a fakturací Azure](billing-getting-started.md#costs)

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

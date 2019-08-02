---
title: Vysvětlení faktury za Azure
description: Přečtěte si, jak číst a porozumět využití a vyúčtování předplatného Azure.
author: bandersmsft
manager: dougeby
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/28/2019
ms.author: banders
ms.openlocfilehash: 51143644a62a77a61c4540d9f2ad3dce401c496b
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68610136"
---
# <a name="understand-your-microsoft-azure-bill"></a>Pochopení Microsoft Azure faktury
Abyste porozuměli službě Azure Invoice, porovnáte svou fakturu s použitím podrobného souboru denního využití a sestav služby cost management v Azure Portal.

Tento článek se nevztahuje na tyto zákazníky:
- Zákazníci Azure se smlouva Enterprise (zákazníci se smlouvou EA). Pokud jste zákazníkem se smlouvou EA, přečtěte si téma [vysvětlení vaší faktury pro zákazníky Azure pomocí smlouva Enterprise](billing-understand-your-bill-ea.md).
- Zákazníci Azure se smlouvou o [zákaznících Microsoftu](#check-access-to-a-microsoft-customer-agreement). Pokud máte smlouvu o zákaznících Microsoftu, přečtěte si téma [vysvětlení poplatků za Azure na faktuře se zákaznickou smlouvou Microsoftu](billing-mca-understand-your-bill.md).

Vysvětlení způsobu, jakým funguje fakturace v programu Azure Cloud Solution Provider (CSP), včetně fakturačního cyklu, cen a využití, najdete v tématu [Přehled fakturace CSP Azure](/azure/cloud-solution-provider/billing/azure-csp-billing-overview/).

## <a name="charges"></a>Kontrola poplatků

>[!VIDEO https://www.youtube.com/embed/3YegFD769Pk]

Pokud je na faktuře účtováno více informací, můžete porovnat využití a náklady pomocí souboru použití nebo Azure Portal.

### <a name="option-1-compare-usage-and-costs-with-usage-file"></a>Možnost 1: Porovnání využití a nákladů pomocí souboru využití

Podrobný soubor CSV s využitím zobrazuje vaše poplatky podle fakturačního období a denního využití. Pokud si chcete stáhnout nebo zobrazit soubor, přečtěte si téma [získání fakturačních a denních údajů o využití Azure](billing-download-azure-invoice-daily-usage-date.md).

Poplatky za využití se zobrazí na úrovni měřiče. Následující výrazy znamenají stejnou věc jak na faktuře, tak i v podrobném souboru využití. Například fakturační cyklus na faktuře je stejný jako fakturační období zobrazené v podrobném souboru použití.

 | Faktura (PDF) | Podrobné použití (CSV)|
 | --- | --- |
|Fakturační cyklus | Fakturační období |
 |Name |Kategorie měřiče |
 |type |Podkategorie měřiče |
 |Resource |Název měřiče |
 |Oblast |Oblast měřiče |
 |Využité |Využité množství |
 |Zahrnuje |Zahrnuté množství |
 |Fakturovatelné |Překročené množství |

Část s **poplatky za využití** ve vaší faktuře zobrazuje celkovou hodnotu pro každý měřič spotřebovaný během fakturačního období. Například následující obrázek ukazuje poplatek za použití pro službu Azure Scheduler.

![Poplatky za využití faktury](./media/billing-understand-your-bill/1.png)

V části **Statement** (sdílený svazek clusteru) se zobrazuje stejný poplatek. *Spotřebovaný* objem i *hodnota* odpovídají faktuře.

![Poplatky za použití sdíleného svazku clusteru](./media/billing-understand-your-bill/2.png)

Pokud se chcete podívat na denní rozpis poplatků, přejděte do části **denní využití** v souboru CSV. Filtr pro *Scheduler* v *kategorii měřiče* Vidíte, ve kterých dnech se měřič použil a kolik byl spotřebovaný. Pro porovnání se zobrazí také informace o *prostředku* a *skupině prostředků* . *Spotřebované* hodnoty by měly být přidány do a spárovány s tím, co se na faktuře zobrazuje.

![Část denního využití ve sdíleném svazku clusteru](./media/billing-understand-your-bill/3.png)

Pokud chcete získat náklady za den, vynásobte *spotřebované* množství hodnotou pomocí hodnoty *sazba* z oddílu **příkazu** .

Další informace naleznete v tématu:

- [Pochopení faktury Azure](billing-understand-your-invoice.md)
- [Podrobné informace o využití Azure](billing-understand-your-invoice.md)

### <a name="option-2-compare-the-usage-and-costs-in-the-azure-portal"></a>Možnost 2: Porovnání využití a nákladů v Azure Portal

Azure Portal vám taky může pomáhat ověřit poplatky. Pokud chcete získat rychlý přehled o fakturovaném využití a nákladech, Prohlédněte si grafy služby cost management.

1. V Azure Portal přejít na předplatná [](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Vyberte své předplatné > **analýzy nákladů**.
1. Filtrovat podle **TimeSpan**
1. Pokud chcete v předchozím příkladu pokračovat, uvidíte poplatek za použití pro službu Azure Scheduler.

   ![Zobrazení analýzy nákladů v Azure Portal](./media/billing-understand-your-bill/4.png)

1. Vyberte řádek, který zobrazí náklady, abyste viděli denní rozpis nákladů.

   ![Zobrazení historie nákladů v Azure Portal](./media/billing-understand-your-bill/5.png)

Další informace najdete v tématu [Ochrana před neočekávanými náklady pomocí fakturace a správy nákladů Azure](billing-getting-started.md#costs).

## <a name="external"></a>Externí služby se účtují samostatně.

Za prostředky, které vytvořili dodavatelé softwaru třetích stran, se účtují i externí služby nebo poplatky na webu Marketplace. Tyto prostředky jsou k dispozici pro použití z Azure Marketplace. Například brána Barracuda firewall je Azure Marketplace prostředek nabízený třetí stranou. Všechny poplatky za bránu firewall a odpovídající měřiče se zobrazí jako poplatky za externí služby.

Poplatky za externí služby se účtují zvlášť. Poplatky se na faktuře na Azure nezobrazují. Další informace najdete v tématu [vysvětlení poplatků za externí služby Azure](billing-understand-your-azure-marketplace-charges.md).

## <a name="resources-billed-by-usage-meters"></a>Prostředky účtované měřiči využití

Azure se nefakturuje přímo na základě nákladů na prostředky. Poplatky za prostředek se vypočtou pomocí jednoho nebo více měřičů. Měřiče se používají ke sledování využití prostředků během své životnosti. Tyto měřiče se pak použijí k výpočtu faktury.

Například při vytváření jednoho prostředku Azure, jako je třeba virtuální počítač, má vytvořenou jednu nebo více instancí měřiče. Měřiče se používají ke sledování využití prostředků v průběhu času. Každý měřič vygeneruje záznamy využití, které Azure používá k výpočtu faktury.

Například jeden virtuální počítač vytvořený v Azure může mít vytvořené následující měřiče, které sledují jeho využití:

- Výpočetní hodiny
- Hodiny IP adresy
- Přenos příchozích dat
- Odchozí přenosy dat
- Spravovaný disk úrovně Standard
- Operace spravovaných disků úrovně Standard
- Standardní v/v – disk
- Standardní v/v – čtení objektů blob bloku
- Standardní v/v – zápis objektů blob bloku
- Standardní v/v – odstranění objektu blob bloku

Po vytvoření virtuálního počítače začne každý měřič vysílat záznamy o využití. Toto využití a cena měřiče se sleduje v systému měření v Azure.

## <a name="payment"></a>Plaťte si vyúčtování

Pokud nastavíte kartu platebních karet jako způsob platby, platba se účtuje automaticky do 10 dnů od skončení fakturačního období. V příkazu platební karty by položka řádku znamenala službu **MSFT Azure**.

Pokud chcete změnit účtovanou platební kartu, přečtěte si téma [Přidání, aktualizace nebo odebrání platební karty pro Azure](billing-how-to-change-credit-card.md).

Pokud [platíte podle faktury](billing-how-to-pay-by-invoice.md), pošlete platbu do umístění uvedeného v dolní části faktury.

Pokud chcete zjistit stav platby, [vytvořte lístek podpory](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).


## <a name="tips-for-cost-management"></a>Tipy pro cost management

- Odhadované náklady pomocí:
  - [Cenová Kalkulačka Azure](https://azure.microsoft.com/pricing/calculator/)
  - [Kalkulačka celkových nákladů na vlastnictví](https://aka.ms/azure-tco-calculator)
  - [Podrobné informace o cenách pro každou službu](https://azure.microsoft.com/pricing/)
- [Pravidelná kontrola využití a nákladů v Azure Portal](billing-getting-started.md#costs)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Podívejte se na přístup k zákaznickým smlouvám Microsoftu
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Potřebujete pomoct? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="learn-more"></a>Víc se uč

- [Získání fakturačních a denních dat o využití Azure](billing-download-azure-invoice-daily-usage-date.md)
- [Porozumění podmínkám Microsoft Azure faktury](billing-understand-your-invoice.md)
- [Vysvětlení podmínek Microsoft Azure podrobného využití](billing-understand-your-usage.md)
- [Správa nákladů na Azure Portal](https://docs.microsoft.com/azure/billing/billing-getting-started)
- [Zabránění neočekávaným nákladům s využitím fakturace a správy nákladů na Azure](billing-getting-started.md#costs)

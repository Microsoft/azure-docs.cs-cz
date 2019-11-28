---
title: Vysvětlení faktury za Azure
description: Zjistěte, jak číst a chápat informace o využití a fakturu za vaše předplatné Azure.
author: bandersmsft
manager: dougeby
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 9486d56a723bb311c05ab7aa776060dfa9561aae
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74223034"
---
# <a name="understand-your-microsoft-azure-bill"></a>Vysvětlení faktury za Microsoft Azure
Pokud chcete porozumět faktuře za Azure, porovnejte svou fakturu se souborem s podrobným denním využitím a sestavami řízení nákladů na webu Azure Portal.

Tento článek se nevztahuje na následující zákazníky:
- Zákazníci Azure se smlouvou Enterprise (zákazníci EA). Pokud jste zákazníkem EA, přečtěte si článek [Vysvětlení informací na faktuře pro zákazníky Azure se smlouvou Enterprise](billing-understand-your-bill-ea.md).
- Zákazníci Azure se [zákaznickou smlouvou Microsoftu](#check-access-to-a-microsoft-customer-agreement). Pokud máte zákaznickou smlouvu Microsoftu, přečtěte si článek[Vysvětlení poplatků za Azure na faktuře zákaznické smlouvy Microsoftu](billing-mca-understand-your-bill.md).

Vysvětlení principů fakturace v programu Azure CSP (Cloud Solution Provider), včetně informací o fakturačním období, cenách a využití, najdete v článku [Přehled fakturace Azure CSP](/azure/cloud-solution-provider/billing/azure-csp-billing-overview/).

## <a name="charges"></a>Kontrola poplatků

>[!VIDEO https://www.youtube.com/embed/3YegFD769Pk]

Pokud máte na faktuře poplatek, ke kterému potřebujete zjistit více informací, můžete porovnat využití a náklady se souborem využití nebo webem Azure Portal.

### <a name="option-1-compare-usage-and-costs-with-usage-file"></a>Možnost 1: Porovnání využití a nákladů se souborem využití

Podrobný soubor CSV s využitím zobrazuje vaše poplatky podle fakturačního období a denního využití. Stažení nebo zobrazení tohoto souboru popisuje článek o [získání faktury za Azure a dat o denním využití](billing-download-azure-invoice-daily-usage-date.md).

Poplatky za využití se zobrazují na úrovni měřiče. Následující výrazy znamenají totéž na faktuře i v souboru s podrobným využitím. Například fakturační cyklus na faktuře je totéž jako fakturační období uvedené v souboru s podrobným využitím.

 | Faktura (PDF) | Podrobné využití (CSV)|
 | --- | --- |
|Fakturační cyklus | Fakturační období |
 |Název |Kategorie měření |
 |Typ |Podkategorie měřiče |
 |Prostředek |Název měření |
 |Oblast |Oblast měření |
 |Spotřebované |Spotřebované množství |
 |Zahrnuje |Zahrnuté množství |
 |Fakturovatelné |Překročené množství |

V sekci **Poplatky za využití** faktury se zobrazuje celková hodnota za každý měřič spotřebovaná během fakturačního období. Následující obrázek například ukazuje poplatek za využití služby Azure Scheduler.

![Poplatky za využití na faktuře](./media/billing-understand-your-bill/1.png)

Stejný poplatek se zobrazuje v sekci **Výpis** souboru CSV s podrobným využitím. Částka *Spotřebované* i *Hodnota* se shodují s fakturou.

![Poplatky za využití v souboru CSV](./media/billing-understand-your-bill/2.png)

Denní rozpis poplatků zobrazíte tak, že přejdete do sekce **Denní využití** souboru CSV. Ve sloupci *Kategorie měřiče* vyfiltrujte *Scheduler*. Uvidíte, které dny se tento měřič používal a kolik bylo spotřebováno. Pro porovnání se rovněž zobrazují údaje *Prostředek* a *Skupina prostředků*. Hodnoty *Spotřebované* by měly být sečtené a odpovídat tomu, co je uvedené na faktuře.

![Sekce s denním využitím v souboru CSV](./media/billing-understand-your-bill/3.png)

Pokud chcete získat náklady za den, vynásobte částky *Spotřebované* hodnotou *Sazba* ze sekce **Výpis**.

Další informace naleznete v tématu:

- [Vysvětlení faktury za Azure](billing-understand-your-invoice.md)
- [Vysvětlení podrobného využití Azure](billing-understand-your-invoice.md)

### <a name="option-2-compare-the-usage-and-costs-in-the-azure-portal"></a>Možnost 2: Porovnání využití a nákladů na webu Azure Portal

S ověřením poplatků vám může pomoci také Azure Portal. Pokud chcete získat rychlý přehled o fakturovaném využití a poplatcích, prohlédněte si grafy řízení nákladů.

1. Na webu Azure Portal přejděte na [Předplatná](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Vyberte své předplatné > **Analýza nákladů**.
1. Nastavte filtr **Časový rozsah**.
1. Pokud budeme pokračovat předchozím příkladem, uvidíte poplatek za využití služby Azure Scheduler.

   ![Zobrazení analýzy nákladů na webu Azure Portal](./media/billing-understand-your-bill/4.png)

1. Výběrem řádku, na kterém je tento poplatek, zobrazíte denní rozpis nákladů.

   ![Zobrazení historie nákladů na webu Azure Portal](./media/billing-understand-your-bill/5.png)

Další informace najdete v článku [Jak zabránit neočekávaným nákladům pomocí fakturace a řízení nákladů Azure](billing-getting-started.md#costs).

## <a name="external"></a>Externí služby fakturované samostatně

Poplatky za externí služby nebo Marketplace se účtují za prostředky, které byly vytvořeny dodavateli softwaru třetích stran. Tyto prostředky jsou dostupné z Azure Marketplace. Například Barracuda Firewall je prostředek z Azure Marketplace nabízený třetí stranou. Všechny poplatky za tuto bránu firewall a její odpovídající měřiče se zobrazují jako poplatky za externí služby.

Poplatky za externí služby se fakturují zvlášť. Tyto poplatky nejsou uvedené na faktuře za Azure. Další informace najdete v článku [Vysvětlení poplatků za externí služby Azure](billing-understand-your-azure-marketplace-charges.md).

## <a name="resources-billed-by-usage-meters"></a>Prostředky fakturované měřiči využití

Azure se nefakturuje přímo na základě nákladů na určitý prostředek. Poplatky za prostředek se počítají pomocí jednoho nebo více měřičů. Měřiče slouží ke sledování využití prostředků během doby jejich života. Tyto měřiče se pak použijí k výpočtu faktury.

Když například vytvoříte určitý prostředek Azure, například virtuální počítač, vytvoří se u něj jedna nebo více instancí měřiče. Měřiče slouží ke sledování využití prostředku v průběhu času. Každý měřič generuje záznamy využití, které Azure používá k výpočtu faktury.

Například u jednoho virtuálního počítače vytvořeného v Azure se mohou vytvořit následující měřiče sledující jeho využití:

- Výpočetní hodiny
- Hodiny IP adresy
- Přenos příchozích dat
- Přenos odchozích dat
- Spravovaný disk úrovně Standard
- Operace spravovaných disků úrovně Standard
- Standardní V/V – disk
- Standardní V/V – čtení objektů blob bloku
- Standardní V/V – zápis objektů blob bloku
- Standardní V/V – odstranění objektů blob bloku

Po vytvoření virtuálního počítače začne každý měřič generovat záznamy o využití. Toto využití a cena z měřiče se sledují v měřicím systému Azure.

## <a name="payment"></a>Úhrada faktury

Pokud jako způsob platby nastavíte platební kartu, strhne se platba automaticky do 10 dnů od skončení fakturačního období. Ve výpisu platební karty by na řádkové položce bylo uvedeno **MSFT Azure**.

Pokud chcete změnit platební kartu, ze které se mají strhávat poplatky, přečtěte si článek [Přidání, aktualizace nebo odebrání platební karty v Azure](billing-how-to-change-credit-card.md).

Pokud [platíte pomocí faktury](billing-how-to-pay-by-invoice.md), zašlete svoji platbu na adresu uvedenou ve spodní části faktury.

Pokud chcete zjistit stav platby, [vytvořte lístek podpory](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).


## <a name="tips-for-cost-management"></a>Tipy pro řízení nákladů

- K odhadu nákladů vám poslouží:
  - [Cenová kalkulačka Azure](https://azure.microsoft.com/pricing/calculator/)
  - [Kalkulačka celkových nákladů na vlastnictví](https://aka.ms/azure-tco-calculator)
  - [Podrobné informace o cenách jednotlivých služeb](https://azure.microsoft.com/pricing/)
- [Pravidelná kontrola využití a nákladů na webu Azure Portal](billing-getting-started.md#costs)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrola přístupu k zákaznické smlouvě Microsoftu
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="learn-more"></a>Další informace

- [Získání faktury za Azure a dat o denním využití](billing-download-azure-invoice-daily-usage-date.md)
- [Vysvětlení výrazů na faktuře za Microsoft Azure](billing-understand-your-invoice.md)
- [Vysvětlení výrazů v podrobných informacích o využití Microsoft Azure](billing-understand-your-usage.md)
- [Řízení nákladů na webu Azure Portal](https://docs.microsoft.com/azure/billing/billing-getting-started)
- [Jak zabránit neočekávaným nákladům v rámci fakturace Azure a správy nákladů](billing-getting-started.md#costs)

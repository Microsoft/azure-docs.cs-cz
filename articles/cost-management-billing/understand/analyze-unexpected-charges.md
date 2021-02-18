---
title: Analýza neočekávaných poplatků za Azure
description: Zjistěte, jak analyzovat neočekávané poplatky za vaše předplatné Azure.
author: bandersmsft
ms.reviewer: adwise
ms.service: cost-management-billing
ms.subservice: cost-management
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: banders
ms.custom: contperf-fy21q1
ms.openlocfilehash: 6d5198f47b6089730ff345eb12ab522fc5ce58a1
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100575386"
---
# <a name="analyze-unexpected-charges"></a>Analýza neočekávaných poplatků

Infrastruktura cloudových prostředků, kterou jste pro vaši organizaci vytvořili, je pravděpodobně velmi složitá. Řada typů prostředků Azure může mít různé typy poplatků. Prostředky Azure můžou vlastnit různé týmy ve vaší organizaci a ty můžou mít pro různé prostředky různé typy modelu fakturace. Pokud chcete lépe porozumět poplatkům, zahajte analýzu s využitím jedné nebo několika strategií v následujících částech.

## <a name="review-invoice-for-resource-responsible-for-charge"></a>Kontrola prostředků zodpovědných za poplatky na faktuře

Způsob, jakým nakupujete služby Azure, vám pomůže určit, jakou metodologii a jaké nástroje máte k dispozici pro identifikaci prostředků přidružených k poplatkům. Pokud chcete zjistit, která metodologie se vás týká, nejprve [určete typ vaší nabídky Azure](../costs/understand-cost-mgt-data.md#determine-your-offer-type). Pak identifikujte kategorii zákazníka v seznamu [podporovaných nabídek Azure](../costs/understand-cost-mgt-data.md#supported-microsoft-azure-offers).

Následující články obsahují podrobný postup s vysvětlením, jak zkontrolovat fakturu na základě typu zákazníka. Jednotlivé články obsahují pokyny ke stažení souboru CSV s podrobnostmi o využití a nákladech za dané fakturační období.

- [Proces kontroly faktury za průběžné platby](review-individual-bill.md#charges)
- [Proces kontroly faktury za smlouvu Enterprise](review-enterprise-agreement-bill.md)
- [Proces kontroly faktury za smlouvu se zákazníkem Microsoftu](review-customer-agreement-bill.md#analyze-your-azure-usage-charges)
- [Proces kontroly faktury za smlouvu s partnerem Microsoftu](review-partner-agreement-bill.md#analyze-your-azure-usage-charges)

Faktura za Azure obsahuje agregované poplatky za měsíc podle jednotlivých _měřičů_. Měřiče slouží ke sledování využití prostředků v průběhu času a používají se k výpočtu faktury. Když vytvoříte určitý prostředek Azure, například virtuální počítač, vytvoří se pro něj jedna nebo více instancí měřiče.

Soubor CSV s informacemi o využití můžete filtrovat podle _názvu měřiče_, který se zobrazuje na faktuře, kterou chcete analyzovat, a zobrazit tak všechny řádkové položky vztahující se k danému měřiči. _ID instance_ u řádkové položky odpovídá skutečnému prostředku Azure, který je za poplatek zodpovědný.

Jakmile daný prostředek identifikujete, můžete pomocí analýzy nákladů ve službě Azure Cost Management pokračovat v analýze nákladů souvisejících s tímto prostředkem. Další informace o používání analýzy nákladů najdete v tématu [Začínáme s analýzou nákladů](../costs/quick-acm-cost-analysis.md).

## <a name="review-invoiced-charges-in-cost-analysis"></a>Kontrola fakturovaných poplatků při analýze nákladů

Pokud si chcete prohlédnout podrobnosti o vaší faktuře na webu Azure Portal, přejděte k analýze nákladů pro rozsah přidružený k faktuře, kterou analyzujete. Vyberte **Podrobnosti o faktuře**. V podrobnostech o faktuře jsou uvedené stejné poplatky jako na faktuře.

[![Příklad znázorňující výběr možnosti Podrobnosti o faktuře](./media/analyze-unexpected-charges/invoice-details.png)](./media/analyze-unexpected-charges/invoice-details.png#lightbox)

Při prohlížení podrobností o faktuře můžete určit službu, která má neočekávané náklady, a zjistit, které prostředky jsou přímo přidružené k prostředku v analýze nákladů. Pokud například chcete analyzovat poplatky za službu Virtual Machines, přejděte k zobrazení **Kumulované náklady**. Potom nastavte úroveň podrobností na **denní**, vyfiltrujte poplatky **Název služby: Virtual Machines** a seskupte je podle **prostředku**.

[![Příklad znázorňující kumulované náklady pro virtuální počítače](./media/analyze-unexpected-charges/virtual-machines.png)](./media/analyze-unexpected-charges/virtual-machines.png#lightbox)

## <a name="identify-spikes-in-cost-over-time"></a>Identifikace špiček v nákladech v průběhu času

V některých případech se může stát, že nevíte, jaké nedávné náklady způsobily změny účtovaných poplatků. Pokud chcete pochopit, co se změnilo, můžete pomocí analýzy nákladů [zobrazit denní nebo měsíční rozpis nákladů v průběhu času](../costs/cost-analysis-common-uses.md#view-costs-per-day-or-by-month). Po vytvoření příslušného zobrazení můžete poplatky seskupit podle **služby** nebo **prostředku** a identifikovat změny. Pokud chcete získat lepší vizualizaci dat, můžete také změnit zobrazení na **spojnicový** graf.

![Příklad ukazující náklady v průběhu času v analýze nákladů](./media/analyze-unexpected-charges/costs-over-time.png)

## <a name="determine-resource-pricing-and-billing-model"></a>Určení cen a modelu fakturace prostředků

Za jeden prostředek se můžou účtovat poplatky napříč několika produkty a službami Azure. Další informace o cenách jednotlivých služeb Azure najdete na stránce s [cenami Azure podle konkrétního produktu](https://azure.microsoft.com/pricing/#product-pricing). Například u jednoho virtuálního počítače vytvořeného v Azure se můžou vytvořit následující měřiče sledující jeho využití. Pro každý z nich můžou platit jiné ceny.

- Výpočetní hodiny
- Hodiny IP adresy
- Příchozí přenosy
- Odchozí přenosy
- Spravovaný disk úrovně Standard
- Operace spravovaného disku úrovně Standard
- Vstupy/výstupy disku úrovně Standard
- Čtení vstupních/výstupních objektů blob bloku – úroveň Standard
- Zápis vstupních/výstupních objektů blob bloku – úroveň Standard
- Standardní V/V – odstranění objektů blob bloku

Po vytvoření virtuálního počítače začne každý měřič generovat záznamy o využití. Využití a cena z měřiče se sledují v měřicím systému Azure. Na měřiče, které byly použity k výpočtu faktury, se můžete podívat v souboru CSV s využitím.

## <a name="find-people-responsible-for-the-resource-and-engage"></a>Vyhledání lidí zodpovědných za konkrétní prostředky a jejich zapojení

Týmy zodpovědné za konkrétní prostředky často ví o změnách, které se s prostředky prováděly. Jejich zapojení vám může pomoct zjistit, proč se určité poplatky účtovaly. Vlastnící tým například mohl nedávno vytvořit prostředek, aktualizovat jeho skladovou položku (a tím změnit sazbu za prostředek) nebo zvýšit zatížení prostředku kvůli změnám kódu. Pokračujte a přečtěte si následující části, ve kterých najdete informace o dalších technikách umožňujících určit, kdo je vlastníkem prostředku.

### <a name="analyze-the-audit-logs-for-the-resource"></a>Analýza protokolů auditu pro prostředek

Pokud máte oprávnění k zobrazení prostředku, měli byste mít přístup k jeho protokolům auditu. Projděte si protokoly a zjistěte, který uživatel byl zodpovědný za poslední změny prostředku. Další informace najdete v tématu [Zobrazení a načtení událostí protokolu aktivit Azure](../../azure-monitor/essentials/activity-log.md#view-the-activity-log).

### <a name="analyze-user-permissions-to-the-resources-parent-scope"></a>Analýza uživatelských oprávnění k nadřazenému oboru prostředku

Lidé, kteří mají k předplatnému nebo skupině prostředků přístup pro zápis, obvykle mají informace o vytvořených prostředcích. Měli by být schopni vysvětlit účel daného prostředku nebo vás odkázat na osobu, která ho zná. Pokud chcete zjistit, kteří lidé mají oprávnění k určitému rozsahu předplatného, přečtěte si téma věnované [kontrole přístupu uživatelů k prostředkům Azure](../../role-based-access-control/check-access.md). Podobný postup můžete použít i u skupin prostředků.

## <a name="get-help-to-identify-charges"></a>Získání nápovědy k identifikaci poplatků

Pokud jste použili výše uvedené strategie, ale stále nechápete, proč se vám účtovaly některé poplatky, nebo pokud potřebujete pomoc s jinými problémy s fakturací, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky

- Zjistěte, jak [optimalizovat investice do cloudu s využitím služby Azure Cost Management](../costs/cost-mgt-best-practices.md).
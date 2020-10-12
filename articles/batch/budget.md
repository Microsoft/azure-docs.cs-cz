---
title: Analýza nákladů a rozpočet
description: Naučte se, jak získat nákladovou analýzu a nastavit rozpočet pro základní výpočetní prostředky a softwarové licence používané ke spouštění úloh služby Batch.
ms.topic: how-to
ms.date: 07/19/2019
ms.openlocfilehash: 50ca1ecfd0a973ff39dabfcb62175ce820d0a0d6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88654238"
---
# <a name="cost-analysis-and-budgets-for-azure-batch"></a>Analýza nákladů a rozpočty pro Azure Batch

Neúčtují se žádné poplatky za Azure Batch sebe, jenom základní výpočetní prostředky a softwarové licence používané ke spouštění dávkových úloh. Na vysoké úrovni se náklady účtují z virtuálních počítačů ve fondu, z virtuálního počítače nebo jakýchkoli vstupních nebo výstupních dat uložených v cloudu. Pojďme se podívat na některé klíčové komponenty služby Batch, které vám pomůžou pochopit, kde náklady pocházejí z, jak nastavit rozpočet pro fond nebo účet, a některé techniky, díky kterým se úlohy služby Batch budou efektivněji zefektivnit.

## <a name="batch-resources"></a>Prostředky Batch

Virtuální počítače jsou nejvýznamnějším prostředkem používaným pro dávkové zpracování. Náklady na použití virtuálních počítačů pro dávku se vypočítávají na základě typu, množství a doby trvání použití. Možnosti fakturace virtuálních počítačů zahrnují průběžné [platby](https://azure.microsoft.com/offers/ms-azr-0003p/) nebo [rezervace](../cost-management-billing/reservations/save-compute-costs-reservations.md) (platí předem). Obě možnosti platby mají různé výhody v závislosti na výpočetním zatížení a oba platební modely budou mít na vyúčtování vliv odlišně.

Když se aplikace nasazují na uzly služby Batch pomocí [balíčků aplikací](batch-application-packages.md), budou se vám účtovat Azure Storage prostředky, které využívají balíčky aplikací. Účtuje se vám taky úložiště všech vstupních nebo výstupních souborů, jako jsou soubory prostředků a další data protokolu. Obecně platí, že cena dat úložiště přidružených ke službě Batch je mnohem nižší než náklady na výpočetní prostředky. Každý virtuální počítač ve fondu, který se vytvořil pomocí **VirtualMachineConfiguration** , má přidružený disk s operačním systémem, který používá disky Azure spravované v Azure. Disky spravované službou Azure mají další náklady a další úrovně výkonu na disku mají i různé náklady.

Fondy služby Batch využívají síťové prostředky. Konkrétně pro **VirtualMachineConfiguration** fondy se používají standardní nástroje pro vyrovnávání zatížení, které vyžadují statické IP adresy. Nástroje pro vyrovnávání zatížení, které služba Batch používá, jsou viditelné pro účty **předplatného uživatele** , ale nejsou viditelné pro účty **služby Batch** . Služby Vyrovnávání zatížení úrovně Standard účtují poplatky za všechna data předaná do a z virtuálních počítačů fondu Batch; Vyberte rozhraní API služby Batch, která načítají data z uzlů fondu (například získat soubor s úlohami nebo uzly), balíčky aplikací úkolů, prostředky/výstupní soubory a image kontejneru, se účtují poplatky.

### <a name="additional-services"></a>Další služby

Služby, které nezahrnují virtuální počítače a úložiště, můžou brát v úvahu náklady na účet Batch.

Mezi další služby běžně používané ve službě Batch můžou patřit:

- Application Insights
- Data Factory
- Azure Monitor
- Virtual Network
- Virtuální počítače s grafickými aplikacemi

V závislosti na tom, které služby s řešením Batch používáte, vám mohou být účtovány další poplatky. Pokud chcete zjistit náklady na každou další službu, podívejte se do [cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/) .

## <a name="cost-analysis-and-budget-for-a-pool"></a>Analýza nákladů a rozpočet pro fond

Prostřednictvím Azure Portal můžete vytvořit rozpočty a oznámení o výdajích pro fondy služby Batch nebo účet Batch. Rozpočty a upozornění jsou užitečné pro informování zúčastněných stran o rizicích spojených s riziky. Je možné, že dojde ke zpoždění upozornění na útratu a k mírnému překročení rozpočtu. V tomto příkladu zobrazíme analýzu nákladů na jednotlivé fondy služby Batch.

1. V Azure Portal v levém navigačním panelu vyberte **cost management + fakturace** .
1. Vyberte své předplatné z oddílu **Moje předplatné** .
1. V části **cost management** v levém navigačním panelu se podívejte na **Analýza nákladů** , která zobrazí toto zobrazení:
1. Vyberte **Přidat filtr**. V prvním rozevíracím seznamu vyberte **prostředek** ![ Vybrat filtr prostředků.](./media/batch-budget/resource-filter.png)
1. V druhém rozevíracím seznamu vyberte fond Batch. Při výběru fondu bude analýza nákladů vypadat podobně jako u následující analýzy.
    ![Analýza nákladů fondu](./media/batch-budget/pool-cost-analysis.png)

Výsledná analýza nákladů zobrazuje náklady na fond a také prostředky, které přispívají k těmto nákladům. V tomto příkladu jsou virtuální počítače, které se používají ve fondu, nejnákladným prostředkem.

Pokud chcete vytvořit rozpočet pro fond, vyberte **rozpočet: žádné**a pak vyberte **vytvořit novou rozpočtovou >**. Teď použijte okno ke konfiguraci rozpočtu specificky pro váš fond.

Další informace o konfiguraci rozpočtu najdete v tématu [Vytvoření a Správa rozpočtů Azure](../cost-management-billing/costs/tutorial-acm-create-budgets.md).

> [!NOTE]
> Azure Batch je postavená na technologii Azure Cloud Services a platformě Azure Virtual Machines. Když zvolíte **Cloud Services konfiguraci**, bude se vám účtovat na základě Cloud Services cenové struktury. Když zvolíte **konfiguraci virtuálního počítače**, bude se vám účtovat na základě Virtual Machines cenové struktury. V příkladu na této stránce se používá **Konfigurace virtuálního počítače**.

## <a name="minimize-cost"></a>Minimalizace nákladů

Používání několika virtuálních počítačů a služeb Azure po delší dobu může být nákladné. Naštěstí jsou dostupné služby, které pomáhají snižovat vaše útraty, a také strategie pro maximalizaci efektivity vašich úloh.

### <a name="low-priority-virtual-machines"></a>Virtuální počítače s nízkou prioritou

Virtuální počítače s nízkou prioritou snižují náklady na úlohy služby Batch díky využívání přebytečných výpočetních kapacit v Azure. Když ve fondech zadáte virtuální počítače s nízkou prioritou, dávkou použije tento přebytek ke spuštění vašich úloh. Za použití virtuálních počítačů s nízkou prioritou místo vyhrazených virtuálních počítačů dochází k výraznému snížení nákladů.

Přečtěte si další informace o nastavení virtuálních počítačů s nízkou prioritou pro vaše zatížení při [použití virtuálních počítačů s nízkou prioritou ve službě Batch](batch-low-pri-vms.md).

### <a name="virtual-machine-os-disk-type"></a>Typ disku s operačním systémem virtuálního počítače

Existuje několik [typů disků s operačním systémem virtuálních počítačů](../virtual-machines/disks-types.md). Většina řady virtuálních počítačů má velikosti podporující úložiště úrovně Premium i Standard. Když je pro fond vybraná velikost virtuálního počítače, Batch nakonfiguruje disky s operačním systémem SSD úrovně Premium. Když je vybraná velikost virtuálního počítače bez s, použije se levnější pevný disk typu HDD. Například disky s operačním systémem SSD úrovně Premium se používají pro `Standard_D2s_v3` a standardní disky s operačním systémem HDD se používají pro `Standard_D2_v3` .

SSD úrovně Premium disky s operačním systémem jsou dražší, ale mají vyšší výkon a virtuální počítače s disky Premium můžou u virtuálních počítačů s disky s pevným pevným operačním systémem trochu rychlejší. Při použití dávky se disk s operačním systémem často nepoužívá mnohem jako aplikace a soubory úloh se nacházejí na dočasném disku SSD. V mnoha případech tedy není nutné platit zvýšené náklady na jednotku SSD úrovně Premium, která je zřízena při určení velikosti virtuálního počítače.

### <a name="reserved-virtual-machine-instances"></a>Rezervované instance virtuálních počítačů

Pokud máte v úmyslu používat Batch po dlouhou dobu, můžete ušetřit náklady na virtuální počítače pomocí [Azure reservations](../cost-management-billing/reservations/save-compute-costs-reservations.md) pro vaše úlohy. Míra rezervace je výrazně nižší než sazba průběžných plateb. Instance virtuálních počítačů, které se používají bez rezervace, se účtují podle tarifu průběžných plateb. Pokud si zakoupíte rezervaci, použije se sleva rezervace a už se vám nebudou účtovat poplatky za průběžné platby.

### <a name="automatic-scaling"></a>Automatické škálování

[Automatické škálování](batch-automatic-scaling.md) dynamicky škáluje počet virtuálních počítačů ve fondu Batch na základě požadavků aktuální úlohy. Díky škálování fondu na základě doby životnosti úlohy zajišťuje automatické škálování, že se virtuální počítače škálují a používají pouze v případě, že je k dispozici úloha. Když se úloha dokončí nebo neexistují žádné úlohy, virtuální počítače se automaticky škálují na nižší kapacitu, aby se ukládaly výpočetní prostředky. Škálování umožňuje snížit celkové náklady na řešení Batch pomocí pouze těch potřebných prostředků.

Další informace o automatickém škálování najdete v tématu [Automatické škálování výpočetních uzlů ve fondu Azure Batch](batch-automatic-scaling.md).

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [rozhraních API a nástrojích služby Batch](batch-apis-tools.md) dostupných pro vytváření a monitorování řešení Batch.  

- Seznamte [se s virtuálními počítači s nízkou prioritou a službou Batch](batch-low-pri-vms.md).

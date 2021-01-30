---
title: Získání analýzy nákladů a nastavení rozpočtů pro Azure Batch
description: Naučte se, jak získat nákladovou analýzu, nastavit rozpočet a snížit náklady na základní výpočetní prostředky a softwarové licence používané ke spouštění úloh služby Batch.
ms.topic: how-to
ms.date: 01/29/2021
ms.openlocfilehash: d1fc2d15a7037e56a8056efa67d2017badb77ffd
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2021
ms.locfileid: "99091323"
---
# <a name="get-cost-analysis-and-set-budgets-for-azure-batch"></a>Získání analýzy nákladů a nastavení rozpočtů pro Azure Batch

Toto téma vám pomůže pochopit náklady, které mohou být spojeny s Azure Batch, jak nastavit rozpočet pro fond nebo účet služby Batch, a způsoby, jak snížit náklady na úlohy služby Batch.

## <a name="understand-costs-associated-with-batch-resources"></a>Pochopení nákladů spojených s prostředky Batch

Neexistují žádné náklady na použití samotného Azure Batch, i když se vám můžou účtovat základní výpočetní prostředky a softwarové licence používané ke spouštění dávkových úloh. Náklady se můžou vydávat z virtuálních počítačů ve fondu, z virtuálního počítače nebo jakýchkoliv vstupních nebo výstupních dat uložených v cloudu.

### <a name="virtual-machines"></a>Virtuální počítače

Virtuální počítače jsou nejvýznamnějším prostředkem používaným pro dávkové zpracování. Náklady na použití virtuálních počítačů pro dávku se vypočítávají na základě typu, množství a doby trvání použití. Možnosti fakturace virtuálních počítačů zahrnují průběžné [platby](https://azure.microsoft.com/offers/ms-azr-0003p/) nebo [rezervace](../cost-management-billing/reservations/save-compute-costs-reservations.md) (platí předem). Obě možnosti platby mají různé výhody v závislosti na výpočetní zátěži a budou mít na vyúčtování vliv odlišně.

Každý virtuální počítač ve fondu, který se vytvořil s [konfigurací virtuálního počítače](nodes-and-pools.md#virtual-machine-configuration) , má přidružený disk s operačním systémem, který používá disky spravované Azure. Disky spravované službou Azure mají další náklady a další úrovně výkonu na disku mají i různé náklady.

### <a name="storage"></a>Storage

Když se aplikace nasazují na uzly služby Batch pomocí [balíčků aplikací](batch-application-packages.md), budou se vám účtovat Azure Storage prostředky, které využívají balíčky aplikací. Účtuje se také úložiště všech vstupních nebo výstupních souborů, jako jsou soubory prostředků a další data protokolu.

Obecně platí, že cena dat úložiště přidružených ke službě Batch je mnohem nižší než náklady na výpočetní prostředky.

### <a name="networking-resources"></a>Síťové prostředky

Fondy služby Batch využívají síťové prostředky, z nichž některé mají přidružené náklady. Zejména pro fondy konfigurací virtuálních počítačů se používají standardní nástroje pro vyrovnávání zatížení, které vyžadují statické IP adresy. Nástroje pro vyrovnávání zatížení, které služba Batch používá, se zobrazí pro [účty](accounts.md#batch-accounts) nakonfigurované v režimu předplatného uživatele, ale ne v režimu služby Batch.

Služby Vyrovnávání zatížení úrovně Standard účtují poplatky za všechna data předaná do a z virtuálních počítačů fondu Batch. Vyberte rozhraní API služby Batch, která načítají data z uzlů fondu (například získat soubor úlohy nebo uzlu), balíčky aplikací pro úlohy, prostředky a výstupní soubory a image kontejnerů. budou se vám účtovat taky poplatky.

### <a name="additional-services"></a>Další služby

V závislosti na tom, které služby s řešením Batch používáte, vám mohou být účtovány další poplatky. Pokud chcete zjistit náklady na každou další službu, podívejte se do [cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/) . Služby běžně používané ve službě Batch, které mohou mít přidružené náklady, zahrnují:

- Application Insights
- Data Factory
- Azure Monitor
- Virtual Network
- Virtuální počítače s grafickými aplikacemi

## <a name="view-cost-analysis-and-create-a-budget-for-a-pool"></a>Zobrazit analýzu nákladů a vytvořit rozpočet pro fond

V Azure Portal můžete vytvořit rozpočty a oznámení o výdajích pro fondy služby Batch nebo účty Batch. Rozpočty a upozornění jsou užitečné pro upozorňování zúčastněných stran na rizika překračovatcích, i když je možné, že se v upozorněních na útratu vyskytnou zpoždění a mírně překročit rozpočet.

> [!NOTE]
> Fond v tomto příkladě používá **konfiguraci virtuálního počítače**, která se doporučuje pro většinu fondů a má za následek, že se účtují poplatky na základě Virtual Machines cenové struktury. Fondy, které používají **Cloud Services konfiguraci** , se účtují na základě Cloud Services cenové struktury.

### <a name="view-cost-analysis-for-a-batch-pool"></a>Zobrazit analýzu nákladů pro fond Batch

1. Do Azure Portal zadejte nebo vyberte **cost management + fakturace** .
1. V části **obory fakturace** vyberte své předplatné.
1. V části **Správa nákladů** vyberte **Analýza nákladů**.
1. Vyberte **Přidat filtr**. V prvním rozevíracím seznamu vyberte **prostředek** .
1. V druhém rozevíracím seznamu vyberte fond Batch. Po výběru fondu se vám zobrazí analýza nákladů pro fond, podobně jako v příkladu, který se tady zobrazuje.
    ![Snímek obrazovky znázorňující analýzu nákladů fondu ve Azure Portal.](./media/batch-budget/pool-cost-analysis.png)

Výsledná analýza nákladů zobrazuje náklady na fond a také prostředky, které přispívají k těmto nákladům. V tomto příkladu jsou virtuální počítače, které se používají ve fondu, nejnákladným prostředkem.

### <a name="create-a-budget-for-a-batch-pool"></a>Vytvoření rozpočtu pro fond služby Batch

1. Na stránce **Analýza nákladů** vyberte možnost **rozpočet: žádné**.
1. Vyberte **vytvořit novou rozpočtovou >**.
1. Pomocí výsledného okna můžete nakonfigurovat rozpočet specificky pro váš fond. Další informace najdete v tématu [kurz: vytváření a Správa rozpočtů Azure](../cost-management-billing/costs/tutorial-acm-create-budgets.md).

## <a name="minimize-costs-associated-with-azure-batch"></a>Minimalizace nákladů spojených s Azure Batch

V závislosti na vašem scénáři možná budete chtít snížit náklady co nejvíce. Zvažte použití jedné nebo několika z těchto strategií k maximalizaci efektivity úloh a omezení potenciálních nákladů.

### <a name="use-low-priority-virtual-machines"></a>Použití virtuálních počítačů s nízkou prioritou

[Virtuální počítače s nízkou prioritou](batch-low-pri-vms.md) snižují náklady na úlohy služby Batch díky využívání přebytečných výpočetních kapacit v Azure. Když ve fondech zadáte virtuální počítače s nízkou prioritou, dávkou použije tento přebytek ke spuštění vašich úloh. Pokud používáte virtuální počítače s nízkou prioritou místo vyhrazených virtuálních počítačů, může dojít k výraznému snížení nákladů.

### <a name="select-a-standard-virtual-machine-os-disk-type"></a>Vyberte standardní typ disku s operačním systémem virtuálního počítače.

Azure nabízí několik [typů disků s operačním systémem virtuálních počítačů](../virtual-machines/disks-types.md). Většina řady virtuálních počítačů má velikosti podporující úložiště úrovně Premium i Standard. Když je pro fond vybraná velikost virtuálního počítače, Batch nakonfiguruje disky s operačním systémem SSD úrovně Premium. Když je vybraná velikost virtuálního počítače bez s, použije se levnější pevný disk typu HDD. Například disky s operačním systémem SSD úrovně Premium se používají pro `Standard_D2s_v3` a standardní disky s operačním systémem HDD se používají pro `Standard_D2_v3` .

SSD úrovně Premium disky s operačním systémem jsou dražší, ale mají vyšší výkon. Virtuální počítače s disky Premium můžou u virtuálních počítačů s disky na pevných discích s PEVNÝm operačním systémem trochu rychlejší. Při použití dávky se disk s operačním systémem často nepoužívá, protože aplikace a soubory úloh se nacházejí na dočasném disku SSD virtuálního počítače. Z tohoto důvodu můžete často vybrat velikost virtuálního počítače bez s, abyste se vyhnuli zvýšení nákladů na jednotku SSD úrovně Premium, která se zřídí při určení velikosti virtuálního počítače.

### <a name="purchase-reservations-for-virtual-machine-instances"></a>Nákup rezervací pro instance virtuálních počítačů

Pokud máte v úmyslu používat Batch po dlouhou dobu, můžete snížit náklady na virtuální počítače pomocí [Azure reservations](../cost-management-billing/reservations/save-compute-costs-reservations.md) pro vaše úlohy. Míra rezervace je výrazně nižší než sazba průběžných plateb. Instance virtuálních počítačů, které se používají bez rezervace, se účtují podle tarifu průběžných plateb. Při nákupu rezervace se použije sleva rezervace.

### <a name="use-automatic-scaling"></a>Použít automatické škálování

[Automatické škálování](batch-automatic-scaling.md) dynamicky škáluje počet virtuálních počítačů ve fondu Batch na základě požadavků aktuální úlohy. Díky automatickému škálování fondu na základě doby životnosti úlohy zajistíte horizontální navýšení kapacity virtuálních počítačů a jejich použití pouze v případě, že je úloha k provedení. Po dokončení úlohy nebo v případě, že nejsou k dispozici žádné úlohy, se virtuální počítače automaticky škálují, aby se ukládaly výpočetní prostředky. Škálování umožňuje snížit celkové náklady na řešení Batch pomocí pouze těch potřebných prostředků.

## <a name="next-steps"></a>Další kroky

- Další informace o [Azure cost management a fakturaci](../cost-management-billing/cost-management-billing-overview.md)
- Přečtěte si o [použití virtuálních počítačů s nízkou prioritou se službou Batch](batch-low-pri-vms.md).

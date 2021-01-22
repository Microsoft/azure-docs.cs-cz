---
title: Analýza nákladů a rozpočty
description: Naučte se, jak získat nákladovou analýzu, nastavit rozpočet a snížit náklady na základní výpočetní prostředky a softwarové licence používané ke spouštění úloh služby Batch.
ms.topic: how-to
ms.date: 01/21/2021
ms.openlocfilehash: 1a950f23b7ecee11dd7da5414b7eed9e87277850
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2021
ms.locfileid: "98679305"
---
# <a name="cost-analysis-and-budgets-for-azure-batch"></a>Analýza nákladů a rozpočty pro Azure Batch

Neexistují žádné náklady na použití samotného Azure Batch, i když se vám můžou účtovat základní výpočetní prostředky a softwarové licence používané ke spouštění dávkových úloh. Náklady se můžou vydávat z virtuálních počítačů ve fondu, z virtuálního počítače nebo jakýchkoliv vstupních nebo výstupních dat uložených v cloudu. Toto téma vám pomůže pochopit, kde náklady pocházejí z, jak nastavit rozpočet pro fond nebo účet služby Batch, a způsoby, jak snížit náklady na úlohy služby Batch.

## <a name="batch-resources"></a>Prostředky Batch

Virtuální počítače jsou nejvýznamnějším prostředkem používaným pro dávkové zpracování. Náklady na použití virtuálních počítačů pro dávku se vypočítávají na základě typu, množství a doby trvání použití. Možnosti fakturace virtuálních počítačů zahrnují průběžné [platby](https://azure.microsoft.com/offers/ms-azr-0003p/) nebo [rezervace](../cost-management-billing/reservations/save-compute-costs-reservations.md) (platí předem). Obě možnosti platby mají různé výhody v závislosti na výpočetní zátěži a budou mít na vyúčtování vliv odlišně.

Když se aplikace nasazují na uzly služby Batch pomocí [balíčků aplikací](batch-application-packages.md), budou se vám účtovat Azure Storage prostředky, které využívají balíčky aplikací. Účtuje se vám taky úložiště všech vstupních nebo výstupních souborů, jako jsou soubory prostředků a další data protokolu. Obecně platí, že cena dat úložiště přidružených ke službě Batch je mnohem nižší než náklady na výpočetní prostředky. Každý virtuální počítač ve fondu, který se vytvořil s [konfigurací virtuálního počítače](nodes-and-pools.md#virtual-machine-configuration) , má přidružený disk s operačním systémem, který používá disky spravované Azure. Disky spravované službou Azure mají další náklady a další úrovně výkonu na disku mají i různé náklady.

Fondy služby Batch využívají síťové prostředky. Zejména pro fondy **VirtualMachineConfiguration** se používají standardní nástroje pro vyrovnávání zatížení, které vyžadují statické IP adresy. Nástroje pro vyrovnávání zatížení, které služba Batch používá, jsou viditelné pro účty **předplatného uživatele** , ale nejsou viditelné pro účty **služby Batch** . Služby Vyrovnávání zatížení úrovně Standard účtují poplatky za všechna data předaná do a z virtuálních počítačů fondu Batch; Vyberte rozhraní API služby Batch, která načítají data z uzlů fondu (například získat soubor s úlohami nebo uzly), balíčky aplikací úkolů, prostředky/výstupní soubory a image kontejneru, se účtují poplatky.

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

V Azure Portal můžete vytvořit rozpočty a oznámení o výdajích pro fondy služby Batch nebo účty Batch. Rozpočty a upozornění jsou užitečné pro upozorňování zúčastněných stran na rizika překračovatcích, i když je možné, že se v upozorněních na útratu vyskytnou zpoždění a mírně překročit rozpočet.

V tomto příkladu zobrazíme analýzu nákladů na jednotlivé fondy služby Batch.

1. Do Azure Portal zadejte nebo vyberte **cost management + fakturace** .
1. V části **obory fakturace** vyberte své předplatné.
1. V části **Správa nákladů** vyberte **Analýza nákladů**.
1. Vyberte **Přidat filtr**. V prvním rozevíracím seznamu vyberte **prostředek** .
1. V druhém rozevíracím seznamu vyberte fond Batch. Po výběru fondu se vám zobrazí analýza nákladů pro fond, podobně jako v příkladu, který se tady zobrazuje.
    ![Snímek obrazovky znázorňující analýzu nákladů fondu ve Azure Portal.](./media/batch-budget/pool-cost-analysis.png)

Výsledná analýza nákladů zobrazuje náklady na fond a také prostředky, které přispívají k těmto nákladům. V tomto příkladu jsou virtuální počítače, které se používají ve fondu, nejnákladným prostředkem.

Pokud chcete vytvořit rozpočet pro fond, vyberte **rozpočet: žádné** a pak vyberte **vytvořit novou rozpočtovou >**. Teď použijte okno ke [konfiguraci rozpočtu](../cost-management-billing/costs/tutorial-acm-create-budgets.md) specificky pro váš fond.

> [!NOTE]
> Azure Batch je postavená na technologii Azure Cloud Services a platformě Azure Virtual Machines. Když zvolíte **Cloud Services konfiguraci**, bude se vám účtovat na základě Cloud Services cenové struktury. Když zvolíte **konfiguraci virtuálního počítače**, bude se vám účtovat na základě Virtual Machines cenové struktury. Příklad na této stránce používá **konfiguraci virtuálního počítače**, která se doporučuje pro většinu fondů služby Batch.

## <a name="minimize-cost"></a>Minimalizace nákladů

Používání několika virtuálních počítačů a služeb Azure po delší dobu může být nákladné. Pomocí těchto strategií můžete maximalizovat efektivitu úloh a snížit náklady.

### <a name="low-priority-virtual-machines"></a>Virtuální počítače s nízkou prioritou

[Virtuální počítače s nízkou prioritou](batch-low-pri-vms.md) snižují náklady na úlohy služby Batch díky využívání přebytečných výpočetních kapacit v Azure. Když ve fondech zadáte virtuální počítače s nízkou prioritou, dávkou použije tento přebytek ke spuštění vašich úloh. Pokud používáte virtuální počítače s nízkou prioritou místo vyhrazených virtuálních počítačů, může dojít k výraznému snížení nákladů.

### <a name="virtual-machine-os-disk-type"></a>Typ disku s operačním systémem virtuálního počítače

Azure nabízí několik [typů disků s operačním systémem virtuálních počítačů](../virtual-machines/disks-types.md). Většina řady virtuálních počítačů má velikosti podporující úložiště úrovně Premium i Standard. Když je pro fond vybraná velikost virtuálního počítače, Batch nakonfiguruje disky s operačním systémem SSD úrovně Premium. Když je vybraná velikost virtuálního počítače bez s, použije se levnější pevný disk typu HDD. Například disky s operačním systémem SSD úrovně Premium se používají pro `Standard_D2s_v3` a standardní disky s operačním systémem HDD se používají pro `Standard_D2_v3` .

SSD úrovně Premium disky s operačním systémem jsou dražší, ale mají vyšší výkon. Virtuální počítače s disky Premium můžou u virtuálních počítačů s disky na pevných discích s PEVNÝm operačním systémem trochu rychlejší. Při použití dávky se disk s operačním systémem často nepoužívá, protože aplikace a soubory úloh se nacházejí na dočasném disku SSD virtuálního počítače. Z tohoto důvodu můžete často vybrat velikost virtuálního počítače bez s, abyste se vyhnuli zvýšení nákladů na jednotku SSD úrovně Premium, která se zřídí při určení velikosti virtuálního počítače.

### <a name="reserved-virtual-machine-instances"></a>Rezervované instance virtuálních počítačů

Pokud máte v úmyslu používat Batch po dlouhou dobu, můžete snížit náklady na virtuální počítače pomocí [Azure reservations](../cost-management-billing/reservations/save-compute-costs-reservations.md) pro vaše úlohy. Míra rezervace je výrazně nižší než sazba průběžných plateb. Instance virtuálních počítačů, které se používají bez rezervace, se účtují podle tarifu průběžných plateb. Při nákupu rezervace se použije sleva rezervace.

### <a name="automatic-scaling"></a>Automatické škálování

[Automatické škálování](batch-automatic-scaling.md) dynamicky škáluje počet virtuálních počítačů ve fondu Batch na základě požadavků aktuální úlohy. Díky automatickému škálování fondu na základě doby životnosti úlohy zajistíte horizontální navýšení kapacity virtuálních počítačů a jejich použití pouze v případě, že je úloha k provedení. Po dokončení úlohy nebo v případě, že nejsou k dispozici žádné úlohy, se virtuální počítače automaticky škálují, aby se ukládaly výpočetní prostředky. Škálování umožňuje snížit celkové náklady na řešení Batch pomocí pouze těch potřebných prostředků.

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [rozhraních API a nástrojích služby Batch](batch-apis-tools.md) dostupných pro vytváření a monitorování řešení Batch.  
- Přečtěte si o [použití virtuálních počítačů s nízkou prioritou se službou Batch](batch-low-pri-vms.md).

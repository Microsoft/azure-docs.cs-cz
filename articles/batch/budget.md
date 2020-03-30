---
title: Analýza nákladů a rozpočet – Azure Batch
description: Zjistěte, jak získat analýzu nákladů a nastavit rozpočet pro základní výpočetní prostředky a softwarové licence používané ke spuštění dávkových úloh.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 07/19/2019
ms.author: labrenne
ms.openlocfilehash: 819b5e16f4730e9a1998234288e181772f7c1996
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022711"
---
# <a name="cost-analysis-and-budgets-for-azure-batch"></a>Analýza nákladů a rozpočty pro Azure Batch

Za azure batch se neplatí, jenom základní výpočetní prostředky a softwarové licence používané ke spuštění dávkových úloh. Na vysoké úrovni vznikají náklady z virtuálních počítačů (VM) ve fondu, přenos dat z virtuálního počítače nebo jakýchkoli vstupních nebo výstupních dat uložených v cloudu. Podívejme se na některé klíčové součásti batch, abychom pochopili, odkud pocházejí náklady, jak nastavit rozpočet pro fond nebo účet a některé techniky pro zvýšení efektivity úloh batch.

## <a name="batch-resources"></a>Dávkové prostředky

Virtuální počítače jsou nejvýznamnější prostředek používaný pro dávkové zpracování. Náklady na použití virtuálních počítačů pro dávku se vypočítají na základě typu, množství a doby použití. Možnosti fakturace virtuálního účtu zahrnují [průběžně platby](https://azure.microsoft.com/offers/ms-azr-0003p/) nebo [rezervace](../cost-management-billing/reservations/save-compute-costs-reservations.md) (platba předem). Obě možnosti platby mají různé výhody v závislosti na výpočetním zatížení a oba modely plateb ovlivní váš účet jinak.

Když se aplikace nasazují do dávkových uzlů (VM) pomocí [balíčků aplikací](batch-application-packages.md), budou se vám účtovat prostředky azure storage, které vaše balíčky aplikací spotřebovávají. Účtuje se vám také ukládání všech vstupních nebo výstupních souborů, jako jsou například soubory prostředků a další data protokolu. Obecně platí, že náklady na data úložiště spojená s Batch je mnohem nižší než náklady na výpočetní prostředky. Každý virtuální počítač ve fondu vytvořeném pomocí **VirtualMachineConfiguration** má přidružený disk operačního systému, který používá disky spravované Azure. Disky spravované Azure mají další náklady a další úrovně výkonu disku mají také různé náklady.

Fondy dávek používají síťové prostředky. Zejména pro **fondy VirtualMachineConfiguration** se používají standardní nástroje pro vyrovnávání zatížení, které vyžadují statické adresy IP. Nástroj pro vyrovnávání zatížení používané batch jsou viditelné pro účty **předplatného uživatele,** ale nejsou viditelné pro účty **dávkové služby.** Standardní mašle zatížení vznikají poplatky za všechna data předávaná do a z virtuálních her fondu dávek; vyberte dávková api, která načítají data z uzlů fondu (například získat soubor úloh/uzlu), balíčky aplikací úloh, soubory prostředků a výstup y a image kontejnerů budou účtovány poplatky.

### <a name="additional-services"></a>Další služby

Služby, které nezahrnuje virtuální počítače a úložiště, mohou zohlednit náklady na váš účet Batch.

Mezi další služby běžně používané s batch mohou patřit:

- Application Insights
- Data Factory
- Azure Monitor
- Virtual Network
- Virtuální aplikace s grafickými aplikacemi

V závislosti na tom, které služby používáte s řešením Batch, vám mohou být účtovány další poplatky. Informace o nákladech na každou další službu naleznete v [cenové kalkulačce.](https://azure.microsoft.com/pricing/calculator/)

## <a name="cost-analysis-and-budget-for-a-pool"></a>Analýza nákladů a rozpočet fondu

Prostřednictvím portálu Azure můžete vytvářet rozpočty a upozornění na výdaje pro váš fondy dávek nebo dávkový účet. Rozpočty a výstrahy jsou užitečné pro informování zúčastněných stran o všech rizicích nadměrných výdajů. Je možné, že dojde ke zpoždění v upozorněních na výdaje a mírně překročí rozpočet. V tomto příkladu zobrazíme analýzu nákladů jednotlivých fondu dávek.

1. Na portálu Azure vyberte na levém navigačním panelu **položku Správa nákladů + Fakturace.**
1. V části **Moje odběry vyberte předplatné.**
1. Přejděte na **analýzu nákladů** v části **Řízení nákladů** na levém panelu nav, která zobrazí zobrazení, jako je toto:
1. Vyberte **Přidat filtr**. V prvním rozevíracím seznamku vyberte **Možnost Zdroj** ![Vyberte filtr zdroje.](./media/batch-budget/resource-filter.png)
1. V druhém rozevíracím seznamu vyberte fond dávek. Když je fond vybrán, analýza nákladů bude vypadat podobně jako následující analýza.
    ![Analýza nákladů fondu](./media/batch-budget/pool-cost-analysis.png)

Výsledná analýza nákladů ukazuje náklady fondu a zdroje, které přispívají k této ceně. V tomto příkladu jsou virtuální mise používané ve fondu nejnákladnějším zdrojem.

Chcete-li vytvořit rozpočet pro fond, vyberte **Rozpočet: žádný**a pak vyberte **Vytvořit nový rozpočet >**. Nyní použijte okno ke konfiguraci rozpočtu speciálně pro váš fond.

Další informace o konfiguraci rozpočtu najdete v tématu [Vytváření a správa rozpočtů Azure](../cost-management-billing/costs/tutorial-acm-create-budgets.md).

> [!NOTE]
> Azure Batch je integrovaný na Azure Cloud Services a technologii Virtuálních počítačů Azure. Když zvolíte **konfiguraci cloudových služeb**, bude vám účtovánpoplatek na základě cenové struktury cloudových služeb. Když zvolíte **konfiguraci virtuálního počítače**, bude se vám účtovat na základě cenové struktury Virtuální počítače. Příklad na této stránce používá **konfiguraci virtuálního počítače**.

## <a name="minimize-cost"></a>Minimalizace nákladů

Použití několika virtuálních počítačů a služeb Azure po delší dobu může být nákladné. Naštěstí jsou k dispozici služby, které vám pomohou snížit výdaje, stejně jako strategie pro maximalizaci efektivity vaší pracovní zátěže.

### <a name="low-priority-virtual-machines"></a>Virtuální počítače s nízkou prioritou

Virtuální počítače s nízkou prioritou snižují náklady na úlohy Batch využitím nadbytečné výpočetní kapacity v Azure. Když zadáte virtuální počítače s nízkou prioritou ve fondech, Batch používá tento přebytek ke spuštění úlohy. Existuje značná úspora nákladů pomocí virtuálních aplikací s nízkou prioritou namísto vyhrazených virtuálních aplikací.

Další informace o tom, jak nastavit virtuální počítače s nízkou prioritou pro vaše úlohy na [použití virtuálních počítačů s nízkou prioritou s dávkovou .](batch-low-pri-vms.md)

### <a name="virtual-machine-os-disk-type"></a>Typ disku operačního systému virtuálního počítače

Existuje více [typů disků operačního systému v systému VM](../virtual-machines/windows/disks-types.md). Většina řad virtuálních počítače má velikosti, které podporují prémiové i standardní úložiště. Když je pro fond vybrána velikost virtuálního počítače , batch konfiguruje disky s prémiovým ssd os. Když je vybrána velikost virtuálního počítače "non-s", použije se levnější standardní typ disku HDD. Například pro disky s prémiovým ssd os se používají `Standard_D2s_v3` a `Standard_D2_v3`pro ně se používají standardní disky s pevným diskem .

Disky s os s s ssd s prémiovým snímkem jsou dražší, ale mají vyšší výkon a virtuální počítače s prémiovými disky můžou začít o něco rychleji než virtuální počítače se standardními disky s pevným diskem. S Batch, disk operačního systému se často nepoužívá moc jako aplikace a soubory úloh jsou umístěny na disku dočasné SSD virtuálních počítačů. Proto v mnoha případech není nutné platit zvýšené náklady na premium SSD, který je zřízen, když je zadána velikost virtuálního počítače "s".

### <a name="reserved-virtual-machine-instances"></a>Rezervované instance virtuálních strojů

Pokud máte v úmyslu používat Batch po dlouhou dobu, můžete ušetřit na nákladech na virtuální počítače pomocí [rezervace Azure](../cost-management-billing/reservations/save-compute-costs-reservations.md) pro vaše úlohy. Rezervační sazba je podstatně nižší než průběžná platba. Instance virtuálních strojů používané bez rezervace se účtují podle sazby průběžných plateb. Pokud si zakoupíte rezervaci, bude vám účtována rezervační sleva a již vám nebudou účtovány poplatky za průběžnou platbu.

### <a name="automatic-scaling"></a>Automatické škálování

[Automatické škálování](batch-automatic-scaling.md) dynamicky škáluje počet virtuálních počítačů ve fondu dávek na základě požadavků aktuální úlohy. Škálováním fondu na základě životnosti úlohy zajišťuje automatické škálování, že virtuální počítač i virtuální chody se navíjejí a používá pouze v případě, že je úloha k provedení. Po dokončení úlohy nebo neexistují žádné úlohy, virtuální počítače jsou automaticky škálovat dolů k uložení výpočetních prostředků. Škálování umožňuje snížit celkové náklady na řešení Batch pomocí pouze prostředků, které potřebujete.

Další informace o automatickéškálování najdete [v tématu automatické škálování výpočetních uzlů ve fondu Azure Batch](batch-automatic-scaling.md).

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [dávkových rozhraních API a nástrojích, které](batch-apis-tools.md) jsou k dispozici pro vytváření a monitorování řešení Batch.  

- Informace o [virtuálních aplikacích](batch-low-pri-vms.md)s nízkou prioritou s dávkou .

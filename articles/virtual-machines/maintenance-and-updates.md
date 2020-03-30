---
title: Údržba a aktualizace
description: Přehled údržby a aktualizací pro virtuální počítače spuštěné v Azure.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/18/2019
ms.author: shants
ms.openlocfilehash: eaf7616b3bd69828829342b4dca9247c009d3475
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250228"
---
# <a name="maintenance-for-virtual-machines-in-azure"></a>Údržba virtuálních počítačů v Azure

Azure pravidelně aktualizuje svou platformu, aby zlepšil spolehlivost, výkon a zabezpečení hostitelské infrastruktury pro virtuální počítače. Účel těchto aktualizací sahá od oprav softwarových komponent v hostitelském prostředí až po upgrady síťových komponent nebo vyřazování hardwaru z provozu. 

Aktualizace zřídka ovlivňují hostované virtuální chody. Když aktualizace mají vliv, Azure zvolí nejméně ovlivňující metodu pro aktualizace:

- Pokud aktualizace nevyžaduje restartování, virtuální počítač se pozastavuje, zatímco je hostitel aktualizován, nebo virtuální počítač je živě migrován na již aktualizovaný hostitel. 
- Pokud údržba vyžaduje restartování, budete upozorněni na plánovanou údržbu. Azure také poskytuje časové okno, ve kterém můžete spustit údržbu sami, v době, která pracuje pro vás. Okno samoúdržby je obvykle 30 dní, pokud není údržba naléhavá. Azure investuje do technologií, aby snížil počet případů, kdy plánovaná údržba platformy vyžaduje restartování virtuálních počítačí. Pokyny ke správě plánované údržby najdete v tématu Zpracování oznámení o plánované údržbě pomocí azure [cli](maintenance-notifications-cli.md), [PowerShell](maintenance-notifications-powershell.md) nebo [portál](maintenance-notifications-portal.md).

Tato stránka popisuje, jak Azure provádí oba typy údržby. Další informace o neplánovaných událostech (výpadky) najdete [v tématu Správa dostupnosti virtuálních počítačových sítí pro Windows](./windows/manage-availability.md) nebo odpovídající článek pro [Linux](./linux/manage-availability.md).

V rámci virtuálního počítače můžete dostávat oznámení o nadcházející údržbě [pomocí naplánovaných událostí pro Windows](./windows/scheduled-events.md) nebo pro [Linux](./linux/scheduled-events.md).



## <a name="maintenance-that-doesnt-require-a-reboot"></a>Údržba, která nevyžaduje restartování

Většina aktualizací platformy nemá vliv na virtuální chod zákazníků. Když není možné bez dopadu aktualizace, Azure zvolí mechanismus aktualizace, který je nejméně působivý pro virtuální počítače zákazníků. 

Většina údržby s nenulovým dopadem pozastaví virtuální počítače na méně než 10 sekund. V některých případech Azure používá mechanismy údržby zachování paměti. Tyto mechanismy pozastavit virtuální počítače po dobu až 30 sekund a zachovat paměť v paměti RAM. Virtuální počítače je pak obnovena a jeho hodiny se automaticky synchronizuje. 

Údržba pro zachování paměti funguje pro více než 90 procent virtuálních počítačů Azure. Nefunguje to pro řady G, M, N a H. Azure stále více používá technologie migrace za provozu a vylepšuje mechanismy údržby zachování paměti, aby se zkrátila doba pozastavení.  

Tyto operace údržby, které nevyžadují restartování, jsou použity současně jednu doménu selhání. Zastaví se, pokud obdrží nějaké varovné zdravotní signály. 

Tyto typy aktualizací mohou ovlivnit některé aplikace. Když je virtuální počítače živě migrovány do jiného hostitele, některé citlivé úlohy může zobrazit mírné snížení výkonu v několika minutách, které vedou k pozastavení virtuálního počítače. Pokud se chcete připravit na údržbu virtuálních počítačích a snížit dopad během údržby Azure, zkuste pro takové aplikace [použít naplánované události pro Windows](./windows/scheduled-events.md) nebo [Linux.](./linux/scheduled-events.md) 

K dispozici je také funkce, řízení údržby, ve verzi Public Preview, která může pomoci spravovat údržbu, která nevyžaduje restartování. Musíte používat azure [dedicated hosts](./linux/dedicated-hosts.md) nebo izolovaný virtuální [počítač](../security/fundamentals/isolation-choices.md). Řízení údržby vám dává možnost přeskočit aktualizace platformy a použít aktualizace podle vašeho výběru času v rámci 35denního rolovacího okna. Další informace najdete [v tématu Řízení aktualizací pomocí řízení údržby a Azure CLI](maintenance-control-cli.md).


### <a name="live-migration"></a>Migrace za provozu

Migrace za provozu je operace, která nevyžaduje restartování a která zachová paměť pro virtuální počítač. To způsobí pauzu nebo zmrazení, obvykle trvající ne více než 5 sekund. S výjimkou řady G, M, N a H jsou všechny virtuální zařízení infrastruktury jako služby (IaaS) způsobilé pro migraci za provozu. Způsobilé virtuální počítače představují více než 90 procent virtuálních počítačích IaaS, které jsou nasazené do vozového parku Azure. 

Platforma Azure spustí migraci za provozu v následujících scénářích:
- Plánovaná údržba
- Selhání hardwaru
- Optimalizace přidělení

Některé scénáře plánované údržby používají migraci za provozu a pomocí naplánovaných událostí můžete předem zjistit, kdy budou zahájeny operace migrace za provozu.

Migrace za provozu se dá použít také k přesunutí virtuálních počítačů, když algoritmy Azure Machine Learning předpovídají hrozící selhání hardwaru nebo když chcete optimalizovat přidělení virtuálních počítačů. Další informace o prediktivním modelování, které detekuje instance zhoršeného hardwaru, najdete [v tématu Zlepšení odolnosti virtuálního počítače Azure pomocí prediktivního strojového učení a migrace za provozu](https://azure.microsoft.com/blog/improving-azure-virtual-machine-resiliency-with-predictive-ml-and-live-migration/?WT.mc_id=thomasmaurer-blog-thmaure). Oznámení o migraci za provozu se zobrazují na portálu Azure v protokolech monitorování a stavu služby a také v naplánovaných událostech, pokud používáte tyto služby.

## <a name="maintenance-that-requires-a-reboot"></a>Údržba, která vyžaduje restartování

Ve výjimečných případech, kdy virtuální počítače je třeba restartovat pro plánovanou údržbu, budete upozorněni předem. Plánovaná údržba má dvě fáze: samoobslužnou fázi a plánovanou fázi údržby.

Během *samoobslužné fáze*, která obvykle trvá čtyři týdny, spustíte údržbu na virtuálních počítačích. Jako součást samoobslužné služby můžete dotazovat každý virtuální počítač zobrazíte jeho stav a výsledek poslední žádosti o údržbu.

Když spustíte samoobslužnou údržbu, váš virtuální počítač se znovu nasadí do již aktualizovaného uzlu. Vzhledem k tomu, že se virtuální počítač restartuje, dočasný disk se ztratí a dynamické IP adresy přidružené k rozhraní virtuální sítě jsou aktualizovány.

Pokud během samoobslužné údržby dojde k chybě, operace se zastaví, virtuální počítač se neaktualizuje a získáte možnost opakovat samoobslužnou údržbu. 

Po ukončení samoobslužné fáze začne *fáze plánované údržby.* Během této fáze můžete stále dotaz na fázi údržby, ale nelze spustit údržbu sami.

Další informace o správě údržby, která vyžaduje restartování, najdete **v tématu Zpracování oznámení plánované údržby** pomocí Azure [CLI](maintenance-notifications-cli.md), [PowerShell](maintenance-notifications-powershell.md) nebo [portál](maintenance-notifications-portal.md). 

### <a name="availability-considerations-during-scheduled-maintenance"></a>Důležité informace o dostupnosti během plánované údržby 

Pokud se rozhodnete počkat až do fáze plánované údržby, je několik věcí, které byste měli zvážit k zachování nejvyšší dostupnosti virtuálních počítačů. 

#### <a name="paired-regions"></a>Spárované oblasti

Každá oblast Azure je spárována s jinou oblastí ve stejné geografické blízkosti. Společně tvoří regionální pár. Během fáze plánované údržby Azure aktualizuje pouze virtuální počítače v jedné oblasti dvojice oblastí. Například při aktualizaci virtuálního počítače v severní centrální USA, Azure neaktualizuje žádný virtuální počítač v jižní centrální USA ve stejnou dobu. V ostatních oblastech, jako je Severní Evropa, však může údržba probíhat ve stejnou dobu jako v oblasti USA – východ. Pochopení toho, jak dvojice oblastí fungují, vám může pomoct lépe distribuovat virtuální počítače napříč oblastmi. Další informace najdete v tématu [páry oblastí Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

#### <a name="availability-sets-and-scale-sets"></a>Skupiny dostupnosti a škálovací sady

Při nasazování úlohy na virtuálních počítačích Azure můžete vytvořit virtuální počítače v rámci *sady dostupnosti,* abyste vaší aplikaci zajistili vysokou dostupnost. Pomocí sad dostupnosti můžete zajistit, že během výpadku nebo údržby události, které vyžadují restartování, alespoň jeden virtuální počítač je k dispozici.

V rámci skupiny dostupnosti jsou jednotlivé virtuální servery rozloženy až do 20 aktualizačních domén. Během plánované údržby je v daném okamžiku aktualizována pouze jedna aktualizační doména. Aktualizační domény nemusí být nutně aktualizovány postupně. 

*Škálovací sady* virtuálních strojů jsou výpočetní prostředek Azure, který můžete použít k nasazení a správě sady identických virtuálních počítačů jako jednoho prostředku. Škálovací sada se automaticky nasadí napříč ud, jako jsou virtuální aplikace v sadě dostupnosti. Stejně jako u sad dostupnosti, při použití škálovacísady pouze jeden UD je aktualizován v daném okamžiku během plánované údržby.

Další informace o nastavení virtuálních počítačů pro vysokou dostupnost najdete [v tématu Správa dostupnosti virtuálních počítačových sítí pro Windows](./windows/manage-availability.md) nebo odpovídající článek pro [Linux](./linux/manage-availability.md).

#### <a name="availability-zones"></a>Zóny dostupnosti

Zóny dostupnosti jsou jedinečná fyzická umístění v rámci oblasti Azure. Každou zónu tvoří jedno nebo několik datacenter vybavených nezávislým napájením, chlazením a sítí. Aby byla zajištěna odolnost proti chybám, jsou ve všech povolených oblastech minimálně tři samostatné zóny. 

Zóna dostupnosti je kombinací domény selhání a aktualizační domény. Pokud vytvoříte tři nebo více virtuálních počítačů ve třech zónách v oblasti Azure, vaše virtuální počítače se efektivně distribuují mezi tři domény selhání a tři aktualizační domény. Platforma Azure rozpoznává tuto distribuci mezi aktualizačními doménami, aby se ujistila, že virtuální počítače v různých zónách nejsou aktualizovány současně.

Každá aktualizace infrastruktury zavádí zónu po zóně v rámci jedné oblasti. Ale můžete mít nasazení probíhá v zóně 1 a různé nasazení probíhá v zóně 2, ve stejnou dobu. Nasazení nejsou všechny serializované. Ale jedno nasazení zavádí pouze jednu zónu najednou, aby se snížilo riziko.

## <a name="next-steps"></a>Další kroky 

Ke správě plánované údržby můžete použít [Azure CLI](maintenance-notifications-cli.md), [Azure PowerShell](maintenance-notifications-powershell.md) nebo [portál.](maintenance-notifications-portal.md) 
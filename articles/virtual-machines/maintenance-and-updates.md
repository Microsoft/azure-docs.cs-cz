---
title: Údržba a aktualizace
description: Přehled údržby a aktualizací pro virtuální počítače běžící v Azure.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 05/22/2020
ms.author: shants
ms.openlocfilehash: 51df72e31acaadc83f4c094b99fa938377e5f023
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "102499998"
---
# <a name="maintenance-for-virtual-machines-in-azure"></a>Údržba virtuálních počítačů v Azure

Azure pravidelně aktualizuje svou platformu, aby vylepšila spolehlivost, výkon a zabezpečení hostitelské infrastruktury pro virtuální počítače. Účel těchto aktualizací sahá od oprav softwarových komponent v hostitelském prostředí až po upgrady síťových komponent nebo vyřazování hardwaru z provozu. 

Aktualizace mají zřídka vliv na hostované virtuální počítače. V případě, že se na aktualizace projeví změny, Azure zvolí pro aktualizace nejméně ovlivněné metody:

- Pokud aktualizace nevyžaduje restart, je virtuální počítač pozastaven během aktualizace hostitele nebo je virtuální počítač v provozu, který se migruje na již aktualizovaného hostitele. 
- Pokud údržba vyžaduje restart, budete informováni o plánované údržbě. Azure také poskytuje časové okno, ve kterém můžete zahájit údržbu, a to v čase, který vám funguje. Okno samoobslužné údržby je obvykle 35 dní, pokud je údržba naléhavá. Azure slouží k tomu, aby se snížil počet případů, ve kterých údržba plánované platformy vyžaduje restartování virtuálních počítačů. Pokyny ke správě plánované údržby najdete v tématu zpracování oznámení o plánované údržbě pomocí Azure [CLI](maintenance-notifications-cli.md), [PowerShellu](maintenance-notifications-powershell.md) nebo [portálu](maintenance-notifications-portal.md).

Tato stránka popisuje, jak Azure provádí oba typy údržby. Další informace o neplánovaných událostech (výpadky) najdete v tématu [Správa dostupnosti virtuálních počítačů pro systém Windows](./availability.md) nebo odpovídající článek pro [Linux](./availability.md).

V rámci virtuálního počítače můžete dostávat oznámení o nadcházející údržbě [pomocí Scheduled Events pro Windows](./windows/scheduled-events.md) nebo [Linux](./linux/scheduled-events.md).



## <a name="maintenance-that-doesnt-require-a-reboot"></a>Údržba, která nevyžaduje restartování

Většina aktualizací platforem nemá vliv na virtuální počítače zákazníka. Pokud není k dispozici aktualizace bez dopadu, Azure zvolí mechanismus aktualizace, který je nejméně ovlivněný pro virtuální počítače zákazníka. 

Většina nenulového dopadu údržby pozastaví virtuální počítač za méně než 10 sekund. V některých případech Azure používá mechanismy údržby pro zachování paměti. Tyto mechanismy pozastaví virtuální počítač po dobu až 30 sekund a zachovají paměť v paměti RAM. Virtuální počítač se pak obnoví a automaticky se synchronizuje jeho hodiny. 

Údržba inobsluhující paměť funguje na více než 90% virtuálních počítačů Azure. Nefunguje pro řady G, M, N a H. Azure stále používá technologie pro migraci za provozu a vylepšuje mechanismy údržby pro zachování paměti, aby se snížila doba trvání pozastavení.  

Tyto operace údržby, které nevyžadují restart, se v jednu chvíli používají v jedné doméně selhání. Zastaví se, pokud obdrží jakékoli varovné signály stavu z nástrojů pro monitorování platformy. 

Tyto typy aktualizací mohou ovlivnit některé aplikace. Když se virtuální počítač migruje za provozu na jiného hostitele, můžou se u některých citlivých úloh Zobrazit mírné snížení výkonu během několika minut, které zavedou až do pozastavení virtuálního počítače. Pokud chcete připravit údržbu virtuálních počítačů a snížit dopad při údržbě Azure, zkuste pro tyto aplikace [použít Scheduled Events pro Windows](./windows/scheduled-events.md) nebo [Linux](./linux/scheduled-events.md) . 

Pro lepší kontrolu nad všemi aktivitami údržby, včetně nulových a znovu spuštěných aktualizací, můžete použít funkci řízení údržby. Musíte použít buď [vyhrazené hostitele Azure](./dedicated-hosts.md) , nebo [izolovaný virtuální počítač](../security/fundamentals/isolation-choices.md). Řízení údržby vám umožní přeskočit všechny aktualizace platforem a použít aktualizace podle vašeho výběru času během 35ho okna. Další informace najdete v tématu [řízení aktualizací pomocí řízení údržby a Azure CLI](maintenance-control.md).


### <a name="live-migration"></a>Migrace za provozu

Migrace za provozu je operace, která nevyžaduje restartování a zachovává paměť pro virtuální počítač. Způsobuje pozastavení nebo zablokování, obvykle trvalá déle než 5 sekund. S výjimkou pro řady G, M, N a H jsou všechny virtuální počítače IaaS (infrastruktura jako služba) vhodné k migraci za provozu. Opravňující virtuální počítače představují více než 90% IaaS virtuálních počítačů, které jsou nasazeny do loďstva Azure. 

Platforma Azure spouští migraci za provozu v následujících scénářích:
- Plánovaná údržba
- Selhání hardwaru
- Optimalizace přidělení

Některé scénáře plánované údržby využívají migraci za provozu a při spuštění operací migrace za provozu můžete použít Scheduled Events k objednání předem.

Migraci za provozu je taky možné použít k přesunu virtuálních počítačů, když Azure Machine Learning algoritmy předpovídá blížící se selhání hardwaru nebo když chcete optimalizovat přidělení virtuálních počítačů. Další informace o prediktivním modelování, které detekuje instance sníženého hardwaru, najdete v tématu [vylepšení odolnosti virtuálních počítačů Azure pomocí prediktivního strojového učení a migrace za provozu](https://azure.microsoft.com/blog/improving-azure-virtual-machine-resiliency-with-predictive-ml-and-live-migration/?WT.mc_id=thomasmaurer-blog-thmaure). Oznámení živé migrace se zobrazí v Azure Portal v protokolech monitorování a Service Health a také v Scheduled Events, pokud používáte tyto služby.

## <a name="maintenance-that-requires-a-reboot"></a>Údržba, která vyžaduje restart

Ve výjimečném případě, kdy je potřeba restartovat virtuální počítače kvůli plánované údržbě, se vám bude informovat předem. Plánovaná údržba má dvě fáze: fáze samoobslužných služeb a plánovaná údržba.

Během *fáze samoobslužné služby*, která obvykle trvá čtyři týdny, spustíte údržbu virtuálních počítačů. V rámci samoobslužné služby se můžete dotazovat na každý virtuální počítač a zobrazit jeho stav a výsledek vaší poslední žádosti o údržbu.

Když spustíte samoobslužnou údržbu, váš virtuální počítač se znovu nasadí do již aktualizovaného uzlu. Vzhledem k tomu, že se virtuální počítač restartuje, dojde ke ztrátě dočasného disku a aktualizuje se dynamická IP adresa přidružená k rozhraní virtuální sítě.

Pokud při samoobslužné údržbě dojde k chybě, operace se zastaví, virtuální počítač se neaktualizuje a získáte možnost opakovat si samoobslužnou údržbu. 

Po skončení samoobslužné fáze se spustí *plánovaná fáze údržby* . Během této fáze se můžete stále dotazovat na fázi údržby, ale nemůžete zahájit údržbu sami.

Další informace o správě údržby, která vyžaduje restart, najdete v tématu **zpracování oznámení o plánované údržbě** pomocí Azure [CLI](maintenance-notifications-cli.md), [PowerShellu](maintenance-notifications-powershell.md) nebo [portálu](maintenance-notifications-portal.md). 

### <a name="availability-considerations-during-scheduled-maintenance"></a>Předpoklady dostupnosti při plánované údržbě 

Pokud se rozhodnete počkat do plánované fáze údržby, měli byste zvážit několik věcí, které byste měli vzít v úvahu při zachování nejvyšší dostupnosti virtuálních počítačů. 

#### <a name="paired-regions"></a>Spárované oblasti

Každá oblast Azure je spárovaná s jinou oblastí v rámci stejného geografického okolí. Dohromady vytvářejí pár oblastí. V průběhu plánované fáze údržby Azure aktualizuje jenom virtuální počítače v jedné oblasti páru oblastí. Když například aktualizujete virtuální počítač v Střed USA – sever, Azure neaktualizuje žádný virtuální počítač ve Střed USA – jih ve stejnou dobu. V ostatních oblastech, jako je Severní Evropa, však může údržba probíhat ve stejnou dobu jako v oblasti USA – východ. Porozumět způsobu, jakým dvojice oblastí fungují, vám pomůžou lépe distribuovat vaše virtuální počítače do různých oblastí. Další informace najdete v tématu [dvojice oblastí Azure](../best-practices-availability-paired-regions.md).

#### <a name="availability-sets-and-scale-sets"></a>Sady dostupnosti a sady škálování

Při nasazování úlohy na virtuální počítače Azure můžete vytvořit virtuální počítače v rámci *skupiny dostupnosti* , aby vaše aplikace poskytovala vysokou dostupnost. Pomocí skupin dostupnosti můžete zajistit, aby během výpadku nebo údržby, které vyžadují restartování, byl k dispozici aspoň jeden virtuální počítač.

V rámci skupiny dostupnosti jsou jednotlivé virtuální počítače rozloženy až do 20 aktualizačních domén. Během plánované údržby se v daném okamžiku aktualizuje jenom jedna aktualizační doména. Aktualizace domén se nemusí nutně aktualizovat sekvenčně. 

Služby Virtual Machine *Scale Sets* jsou výpočetním prostředkem Azure, který můžete použít k nasazení a správě sady identických virtuálních počítačů jako jednoho prostředku. Sada škálování se automaticky nasadí přes UDs, jako jsou virtuální počítače ve skupině dostupnosti. Stejně jako u skupin dostupnosti se při použití sad škálování aktualizuje jenom jeden UD v daném okamžiku během plánované údržby.

Další informace o nastavení virtuálních počítačů pro zajištění vysoké dostupnosti najdete v tématu [Správa dostupnosti virtuálních počítačů pro Windows](./availability.md) nebo odpovídající článek pro [Linux](./availability.md).

#### <a name="availability-zones"></a>Zóny dostupnosti

Zóny dostupnosti jsou jedinečná fyzická umístění v rámci oblasti Azure. Každou zónu tvoří jedno nebo několik datacenter vybavených nezávislým napájením, chlazením a sítí. Aby se zajistila odolnost, existuje minimálně tři samostatné zóny ve všech povolených oblastech. 

Zóna dostupnosti je kombinací domény selhání a aktualizační domény. Pokud vytvoříte tři nebo víc virtuálních počítačů ve třech zónách v oblasti Azure, budou vaše virtuální počítače efektivně distribuované mezi třemi doménami selhání a třemi aktualizačními doménami. Platforma Azure tuto distribuci rozpoznává mezi aktualizačními doménami, aby se zajistilo, že se virtuální počítače v různých zónách neaktualizují současně.

Každá aktualizace infrastruktury shrnuje zónu podle zóny v rámci jedné oblasti. Ale můžete mít nasazení v systému v Zóna 1 a jiné nasazení v Zóna 2, ve stejnou dobu. Nasazení nejsou serializovaná. Jediné nasazení ale najednou shrnuje jenom jednu zónu, aby se snížila rizika.

## <a name="next-steps"></a>Další kroky 

Ke správě plánované údržby můžete použít [Azure CLI](maintenance-notifications-cli.md), [Azure PowerShell](maintenance-notifications-powershell.md) nebo [portál](maintenance-notifications-portal.md) .
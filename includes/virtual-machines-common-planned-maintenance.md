---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: include
ms.date: 8/22/2019
ms.author: shants
ms.custom: include file
ms.openlocfilehash: b2a7bbef2c421281780c0191fa32381468899bbf
ms.sourcegitcommit: 3f78a6ffee0b83788d554959db7efc5d00130376
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/26/2019
ms.locfileid: "70020323"
---
Azure pravidelně aktualizuje svou platformu, aby vylepšila spolehlivost, výkon a zabezpečení hostitelské infrastruktury pro virtuální počítače. Tyto aktualizace jsou v rozsahu od oprav softwarových součástí v hostitelském prostředí pro upgrade síťových součástí nebo vyřazení hardwaru z provozu. 

Aktualizace mají zřídka vliv na hostované virtuální počítače. V případě, že se na aktualizace projeví změny, Azure zvolí pro aktualizace nejméně ovlivněné metody:

- Pokud aktualizace nevyžaduje restart, je virtuální počítač pozastaven během aktualizace hostitele nebo je virtuální počítač v provozu, který se migruje na již aktualizovaného hostitele.

- Pokud údržba vyžaduje restart, budete informováni o plánované údržbě. Azure také poskytuje časové okno, ve kterém můžete zahájit údržbu, a to v čase, který vám funguje. Okno samoobslužné údržby je obvykle 35 dní, pokud je údržba naléhavá. Azure slouží k tomu, aby se snížil počet případů, ve kterých údržba plánované platformy vyžaduje restartování virtuálních počítačů. 

Tato stránka popisuje, jak Azure provádí oba typy údržby. Další informace o neplánovaných událostech (výpadky) najdete v tématu [Správa dostupnosti virtuálních počítačů pro systém Windows](../articles/virtual-machines/windows/manage-availability.md) nebo odpovídající článek pro [Linux](../articles/virtual-machines/linux/manage-availability.md).

V rámci virtuálního počítače můžete dostávat oznámení o nadcházející údržbě [pomocí Scheduled Events pro Windows](../articles/virtual-machines/windows/scheduled-events.md) nebo [Linux](../articles/virtual-machines/linux/scheduled-events.md).

Pokyny ke správě plánované údržby najdete v tématu [zpracování oznámení o plánované údržbě pro Linux](../articles/virtual-machines/linux/maintenance-notifications.md) nebo odpovídající článek pro [Windows](../articles/virtual-machines/windows/maintenance-notifications.md).

## <a name="maintenance-that-doesnt-require-a-reboot"></a>Údržba, která nevyžaduje restartování

Jak je uvedeno výše, většina aktualizací platforem nemá vliv na virtuální počítače zákazníka. Pokud není k dispozici aktualizace bez dopadu, Azure zvolí mechanismus aktualizace, který je nejméně ovlivněný pro virtuální počítače zákazníka. 

Většina nenulového dopadu údržby pozastaví virtuální počítač za méně než 10 sekund. V některých případech Azure používá mechanismy údržby pro zachování paměti. Tyto mechanismy pozastaví virtuální počítač po dobu až 30 sekund a zachovají paměť v paměti RAM. Virtuální počítač se pak obnoví a automaticky se synchronizuje jeho hodiny. 

Údržba inobsluhující paměť funguje na více než 90% virtuálních počítačů Azure. Nefunguje pro řady G, M, N a H. Azure stále používá technologie pro migraci za provozu a vylepšuje mechanismy údržby pro zachování paměti, aby se snížila doba trvání pozastavení.  

Tyto operace údržby, které nevyžadují restart, se v jednu chvíli používají v jedné doméně selhání. Zastaví se, pokud obdrží nějaké varovné signály stavu. 

Tyto typy aktualizací mohou ovlivnit některé aplikace. Když se virtuální počítač migruje za provozu na jiného hostitele, můžou se u některých citlivých úloh Zobrazit mírné snížení výkonu během několika minut, které zavedou až do pozastavení virtuálního počítače. Pokud chcete připravit údržbu virtuálních počítačů a snížit dopad při údržbě Azure, zkuste pro tyto aplikace [použít Scheduled Events pro Windows](../articles/virtual-machines/windows/scheduled-events.md) nebo [Linux](../articles/virtual-machines/linux/scheduled-events.md) . Azure taky poskytuje plnou kontrolu nad takovou nenulovou údržbou platforem na [vyhrazených hostitelích Azure](../articles/virtual-machines/windows/dedicated-hosts.md) a [izolovaných virtuálních počítačích](../articles/security/fundamentals/isolation-choices.md). Funkce řízení údržby je ve verzi Preview a můžete požádat o přístup odesláním [registračního formuláře](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR6lJf7DwiQxNmz51ksQvxV9UNUM3UllWUjBMTFZQUFhHUDI0VTBPQlJFNS4u). Poskytuje možnost přeskočit nenulové aktualizace platformy a použít aktualizace jako dávku v čase podle vašeho výběru během 35ho okna.

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

Další informace o správě údržby, která vyžaduje restart, najdete v tématu [zpracování oznámení o plánované údržbě pro Linux](../articles/virtual-machines/linux/maintenance-notifications.md) nebo odpovídající článek pro [Windows](../articles/virtual-machines/windows/maintenance-notifications.md). 

### <a name="availability-considerations-during-scheduled-maintenance"></a>Předpoklady dostupnosti při plánované údržbě 

Pokud se rozhodnete počkat do plánované fáze údržby, měli byste zvážit několik věcí, které byste měli vzít v úvahu při zachování nejvyšší dostupnosti virtuálních počítačů. 

#### <a name="paired-regions"></a>Spárované oblasti

Každá oblast Azure je spárovaná s jinou oblastí v rámci stejného geografického okolí. Dohromady vytvářejí pár oblastí. V průběhu plánované fáze údržby Azure aktualizuje jenom virtuální počítače v jedné oblasti páru oblastí. Když například aktualizujete virtuální počítač v Střed USA – sever, Azure neaktualizuje žádný virtuální počítač ve Střed USA – jih ve stejnou dobu. V ostatních oblastech, jako je Severní Evropa, však může údržba probíhat ve stejnou dobu jako v oblasti Východní USA. Porozumět způsobu, jakým dvojice oblastí fungují, vám pomůžou lépe distribuovat vaše virtuální počítače do různých oblastí. Další informace najdete v tématu [dvojice oblastí Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

#### <a name="availability-sets-and-scale-sets"></a>Sady dostupnosti a sady škálování

Při nasazování úlohy na virtuální počítače Azure můžete vytvořit virtuální počítače v rámci *skupiny dostupnosti* , aby vaše aplikace poskytovala vysokou dostupnost. Pomocí skupin dostupnosti můžete zajistit, aby během výpadku nebo údržby, které vyžadují restartování, byl k dispozici aspoň jeden virtuální počítač.

V rámci skupiny dostupnosti jsou jednotlivé virtuální počítače rozloženy až do 20 aktualizačních domén (UDs). Během plánované údržby se v daném okamžiku aktualizuje jenom jeden UD. UDs se nemusí nutně aktualizovat sekvenčně. 

Služby Virtual Machine *Scale Sets* jsou výpočetním prostředkem Azure, který můžete použít k nasazení a správě sady identických virtuálních počítačů jako jednoho prostředku. Sada škálování se automaticky nasadí přes UDs, jako jsou virtuální počítače ve skupině dostupnosti. Stejně jako u skupin dostupnosti se při použití sad škálování aktualizuje jenom jeden UD v daném okamžiku během plánované údržby.

Další informace o nastavení virtuálních počítačů pro zajištění vysoké dostupnosti najdete v tématu [Správa dostupnosti virtuálních počítačů pro Windows](../articles/virtual-machines/windows/manage-availability.md) nebo odpovídající článek pro [Linux](../articles/virtual-machines/linux/manage-availability.md).

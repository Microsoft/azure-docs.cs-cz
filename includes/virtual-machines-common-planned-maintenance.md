---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: include
ms.date: 4/30/2019
ms.author: shants
ms.custom: include file
ms.openlocfilehash: c2931fa410cf92755a5df5b7129dcf93de900930
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67174987"
---
Azure pravidelně aktualizuje svou platformu zlepšit spolehlivost, výkon a zabezpečení hostitelské infrastruktury pro virtuální počítače. Účelem těchto aktualizací se od použití dílčích oprav softwarové komponenty v hostitelském prostředí pro upgrade síťové součásti nebo vyřazení z provozu hardwaru. 

Aktualizace ovlivní jen zřídka hostované virtuální počítače. Při aktualizace nemají vliv, Azure vybere metodu minimální dopad aktualizací:

- Pokud tato aktualizace nevyžaduje restartování, virtuální počítač se pozastaví při aktualizaci hostitele nebo virtuální počítač je už aktualizovaného hostitele migrovat za provozu.

- Pokud Údržba vyžaduje restartování, zobrazí se oznámení plánované údržby. Azure poskytuje také časový interval, ve které můžete spustit údržbu sami, v čase, která vám vyhovuje. Samoobslužné údržby je obvykle 30 dnů, pokud je urgentní údržbu. Azure je investic do technologií a snížit počet případů, kdy platforma plánované údržby vyžaduje virtuální počítače až po restartování. 

Tato stránka popisuje, jak Azure provede oba typy údržby. Další informace o neplánované události (výpadky) najdete v tématu [Správa dostupnosti virtuálních počítačů pro Windows](../articles/virtual-machines/windows/manage-availability.md) nebo odpovídající článku [Linux](../articles/virtual-machines/linux/manage-availability.md).

V rámci virtuálního počítače, můžete získat upozornění o nadcházející údržby podle [naplánované událostí pro Windows pomocí](../articles/virtual-machines/windows/scheduled-events.md) nebo [Linux](../articles/virtual-machines/linux/scheduled-events.md).

Pokyny týkající se správy plánované údržby najdete v tématu [zpracování oznámení plánované údržby pro Linux](../articles/virtual-machines/linux/maintenance-notifications.md) nebo odpovídající článku [Windows](../articles/virtual-machines/windows/maintenance-notifications.md).

## <a name="maintenance-that-doesnt-require-a-reboot"></a>Údržby, které nevyžaduje restartování

Jak bylo uvedeno dříve, většina aktualizace platformy nemají vliv na virtuální počítače zákazníků. Při aktualizaci bez dopadů není možné, Azure vybere aktualizace mechanismus, který je nejméně impactful k virtuálním počítačům zákazníků. 

Většina údržby nenulovou dopad pozastaví virtuální počítač menší než 10 sekund. V některých případech Azure využívá mechanismy údržby zachovávající paměť. Tyto mechanismy pozastavení virtuálního počítače po dobu až 30 sekund a zachovat paměť RAM. Virtuální počítač se pak obnoví a hodiny se automaticky synchronizuje. 

Údržba pro zachování paměti funguje pro více než 90 procent virtuálních počítačů Azure. Nebude fungovat pro řadu G, M, N a H. Azure stále používá technologie migrace za provozu a zlepšuje zachovávající paměť údržby mechanismy ke snížení doby trvání pozastavení.  

Tyto operace údržby, které nevyžadují restartování jsou použité jedné doméně selhání najednou. Vyhodnocování se zastaví, pokud se zobrazí všechny signály stavu upozornění. 

Tyto typy aktualizací může mít vliv na některé aplikace. Když je virtuální počítač do jiného hostitele migrovat za provozu, některé citlivými úlohami může zobrazovat mírné snížení výkonu za několik minut, vedoucí k pozastavení virtuálního počítače. Chcete-li připravit Údržba virtuálního počítače a omezit dopad během údržby Azure, zkuste [naplánované událostí pro Windows pomocí](../articles/virtual-machines/windows/scheduled-events.md) nebo [Linux](../articles/virtual-machines/linux/scheduled-events.md) pro takové aplikace. Azure funguje na funkce pro řízení Údržba těchto citlivých aplikací. 

### <a name="live-migration"></a>Migrace za provozu

Migrace za provozu je operace, která nevyžaduje restartování a které zachovává paměť pro virtuální počítač. Dojde k pozastavení nebo zablokování, obvykle trvá více než 5 sekund. S výjimkou G řady M, N a H, veškerá infrastruktura jako služba (IaaS) VMs, jsou vhodné pro migraci za provozu. Oprávněné virtuálních počítačů představují víc než 90 procent virtuálních počítačů IaaS, které jsou nasazené na Azure fleet. 

Platforma Azure začne migrace za provozu v následujících scénářích:
- Plánovaná údržba
- Selhání hardwaru
- Optimalizace přidělování

Některé scénáře plánované údržby pomocí migrace za provozu a naplánovaných událostí můžete použít předem vědět, když za provozu se spustí operace migrace.

Migrace za provozu je také možné přesunout virtuální počítače, pokud algoritmů Azure Machine Learning předvídat nastávající selhání hardwaru nebo pokud chcete optimalizovat přidělení virtuálního počítače. Další informace o prediktivní modelování, který zjistí instance útoku degradovaný hardwaru najdete v tématu [odolnost zlepšení virtuálních počítačů Azure s prediktivní strojového učení a migrace za provozu](https://azure.microsoft.com/blog/improving-azure-virtual-machine-resiliency-with-predictive-ml-and-live-migration/?WT.mc_id=thomasmaurer-blog-thmaure). Migrace za provozu oznámení se zobrazují na webu Azure Portal v monitorování a stavy služeb protokoly stejně jako v naplánovaných událostí, pokud tyto služby používat.

## <a name="maintenance-that-requires-a-reboot"></a>Údržby, které vyžaduje restart

Ve výjimečných případech, kdy je potřeba restartovat kvůli plánované údržbě virtuálních počítačů budete upozorněni na předem. Plánovaná údržba má dvě fáze: samoobslužných služeb fáze a fáze plánované údržby.

Během *samoobslužné fáze*, což obvykle trvá čtyři týdny, spustit údržbu na virtuálních počítačích. Jako součást samoobslužné služby se můžete dotazovat jednotlivých virtuálních počítačích, chcete-li zobrazit svůj stav a výsledek poslední žádosti údržby.

Když spustíte samoobslužné údržby, váš virtuální počítač se znovu nasadil už aktualizovaný uzel. Vzhledem k tomu, že restartování virtuálního počítače, dočasný disk je ztraceny a dynamické IP adresy přidružené k rozhraní virtuální sítě se aktualizují.

Pokud dojde k chybě během samoobslužné údržby, zastaví operace virtuálního počítače není aktualizován a budete mít možnost to chcete zkusit znovu samoobslužné údržby. 

Po skončení fáze samoobslužných služeb, *fáze plánované údržby* začíná. Během této fáze se pořád můžou dotazovat pro fáze údržby, ale nelze spustit údržbu, sami.

Další informace o správě údržby, které vyžaduje restart, naleznete v tématu [zpracování oznámení plánované údržby pro Linux](../articles/virtual-machines/linux/maintenance-notifications.md) nebo odpovídající článku [Windows](../articles/virtual-machines/windows/maintenance-notifications.md). 

### <a name="availability-considerations-during-scheduled-maintenance"></a>Důležité informace o dostupnosti během plánované údržby 

Pokud se rozhodnete počkat do fáze plánované údržby, existuje několik věcí, které byste měli zvážit nejvyšší dostupnost vašich virtuálních počítačů. 

#### <a name="paired-regions"></a>Spárované oblasti

Každá oblast Azure je spárovaná s jinou oblastí v rámci stejné zeměpisné okolí. Společně získávají páru oblastí. Azure během fáze plánované údržby aktualizuje pouze virtuální počítače v jedné oblasti z páru oblastí. Například při aktualizaci virtuálního počítače v střed USA – sever nebude Azure aktualizovat všech virtuálních počítačů v střed USA – jih ve stejnou dobu. V ostatních oblastech, jako je Severní Evropa, však může údržba probíhat ve stejnou dobu jako v oblasti Východní USA. Principy fungování párování oblastí vám mohou pomoci při lepší distribuci vašich virtuálních počítačů napříč oblastmi. Další informace najdete v tématu [párování oblastí Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

#### <a name="availability-sets-and-scale-sets"></a>Skupiny dostupnosti a škálovací sady

Při nasazení úlohy na virtuálních počítačích Azure, můžete vytvořit virtuální počítače v rámci *dostupnosti* pro zajištění vysoké dostupnosti pro vaši aplikaci. Použití skupiny dostupnosti, můžete zajistit, že během výpadku nebo údržby událostí, které vyžadují restartování, je k dispozici alespoň jeden virtuální počítač.

V rámci skupiny dostupnosti jednotlivé virtuální počítače jsou rozloženy až 20 aktualizační doména (ud). Během plánované údržby se aktualizuje jenom jeden UD v daném okamžiku. Aktualizačními doménami již nejsou nutně postupně aktualizovat. 

Virtuální počítač *škálovacích sad* jsou tedy výpočetní prostředek, který můžete použít k nasazení a správě sady identických virtuálních počítačů jako jeden prostředek Azure. Škálovací sady se automaticky nasadí napříč aktualizačními doménami, jako jsou virtuální počítače ve skupině dostupnosti. Jako se skupinami dostupnosti při použití škálovacích sad, pouze jeden UD se aktualizuje v daném okamžiku během plánované údržby.

Další informace o nastavení vašich virtuálních počítačů pro zajištění vysoké dostupnosti najdete v tématu [spravovat dostupnost vašich virtuálních počítačů pro Windows](../articles/virtual-machines/windows/manage-availability.md) nebo odpovídající článku [Linux](../articles/virtual-machines/linux/manage-availability.md).

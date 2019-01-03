---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: include
ms.date: 12/14/2018
ms.author: shants
ms.custom: include file
ms.openlocfilehash: 34723a6ee37e54ea2d81e6d1143672e3ccb30d1e
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/27/2018
ms.locfileid: "53805716"
---
Azure pravidelně aktualizuje platformy zvýšit spolehlivost, výkon a zabezpečení hostitelské infrastruktury pro virtuální počítače. Tyto aktualizace v rozsahu od opravy softwarové komponenty v hostitelském prostředí upgrade síťových součástí, na vyřazení z provozu hardwaru. Většina těchto aktualizací mít žádný vliv na hostované virtuální počítače. Existují však případech, kdy aktualizace nemají vliv a Azure vybere metodu minimální dopad aktualizací:

- Pokud není rebootful aktualizace je možné, že virtuální počítač je pozastavený, zatímco se aktualizuje hostitele nebo je v provozu migrovat do už aktualizovaného hostitele.

- Údržba vyžaduje restartování, dostanete oznámení o při plánované údržby. Azure vám také poskytne časové okno, kde můžete začít údržbu sami v čase, která vám vyhovuje. Azure je investic do technologií ke snížení případech, pokud mají virtuální počítače až po restartování pro platformu plánované údržby. 

Tato stránka popisuje, jak Azure provede oba typy údržby. Další informace o neplánované události (výpadky), naleznete v části Správa dostupnosti virtuálních počítačů pro [Windows](../articles/virtual-machines/windows/manage-availability.md) nebo [Linux](../articles/virtual-machines/linux/manage-availability.md).

Můžete získat v rámci virtuálního počítače upozornění o nadcházející údržbu pomocí naplánovaných událostí pro [Windows](../articles/virtual-machines/windows/scheduled-events.md) nebo [Linux](../articles/virtual-machines/linux/scheduled-events.md).

"Postupy:" informace o správě plánované údržby, najdete v části "Zpracování oznámení plánované údržby" pro [Linux](../articles/virtual-machines/linux/maintenance-notifications.md) nebo [Windows](../articles/virtual-machines/windows/maintenance-notifications.md).

## <a name="memory-preserving-maintenance"></a>Údržba pro zachování paměti

Pro většinu aktualizací bez rebootful je menší než 10 sekund pozastavení virtuálního počítače. V některých případech paměti Údržba pro zachování se používají mechanismus, který pozastaví virtuální počítač po dobu až 30 sekund a zachovává přitom paměť RAM. Virtuální počítač se pak obnoví a hodiny virtuálního počítače se automaticky synchronizuje. Azure je stále pomocí migrace za provozu technologie a zlepšení paměti zachování údržby mechanismus zkrátit dobu trvání pozastavení.

Tyto operace údržby bez rebootful jsou použité doména podle domény selhání a průběh je zastaven, pokud jsou přijímány všechny signály stavu upozornění. 

Některé aplikace může mít vliv tyto typy aktualizací. V případě virtuálního počítače za provozu migrovat na jiného hostitele, všimnout některých citlivých úloh mírné snížení výkonu za několik minut, vedoucí k pozastavení virtuálního počítače. Tato aplikace může přinést pomocí naplánovaných událostí pro [Windows](../articles/virtual-machines/windows/scheduled-events.md) nebo [Linux](../articles/virtual-machines/linux/scheduled-events.md) můžete připravit Údržba virtuálního počítače a mít žádný vliv při údržbě Azure. Azure také pracuje na funkce pro řízení Údržba těchto názvových citlivých aplikací. 


## <a name="maintenance-requiring-a-reboot"></a>Údržby, které vyžadují restartování

Ve výjimečných případech, když virtuální počítače je třeba restartovat kvůli plánované údržbě, budete upozorněni předem. Plánovaná údržba má dvě fáze: okno samoobslužných služeb a plánované údržby.

**Samoobslužné služby okno** umožňuje zahájit údržbu na virtuálních počítačích. Během této doby se můžete dotazovat každý virtuální počítač, který chcete zobrazit jejich stav a podívat se na výsledek poslední žádosti údržby.

Když spustíte samoobslužné údržby, váš virtuální počítač se znovu nasadil už aktualizovaný uzel. Vzhledem k tomu, že restartování virtuálního počítače, dočasný disk je ztraceny a dynamické IP adresy přidružené k rozhraní virtuální sítě se aktualizují.

Pokud dojde k chybě během procesu spuštění samoobslužné údržby, operace se zastavila, není aktualizován virtuálního počítače a budete mít možnost to chcete zkusit znovu samoobslužné údržby. 

Po uplynutí okno samoobslužných služeb, **plánované časové období údržby** začíná. Během tohoto časového intervalu pro údržbu se pořád můžou dotazovat ale nemůže spustit údržbu, sami.

Informace o správě údržby, které vyžadují restartování, najdete v části "Zpracování oznámení plánované údržby" pro [Linux](../articles/virtual-machines/linux/maintenance-notifications.md) nebo [Windows](../articles/virtual-machines/windows/maintenance-notifications.md). 

### <a name="availability-considerations-during-scheduled-maintenance"></a>Důležité informace o dostupnosti během plánované údržby 

Pokud se rozhodnete Počkejte, dokud plánované časové období údržby, existuje několik věcí, které je potřeba zvážit udržována nejvyšší dostupnost vašich virtuálních počítačů. 

#### <a name="paired-regions"></a>Spárované oblasti

Každá oblast Azure je spárovaná s jinou oblastí ve stejné zeměpisné oblasti a současně využívají páru oblastí. Ve fázi plánované údržby Azure aktualizuje pouze virtuální počítače v jedné oblasti z páru oblastí. Například při aktualizaci virtuálního počítače v střed USA – sever, Azure se neaktualizuje všech virtuálních počítačů v střed USA – jih ve stejnou dobu. V ostatních oblastech, jako je Severní Evropa, však může údržba probíhat ve stejnou dobu jako v oblasti Východní USA. Principy fungování párování oblastí vám mohou pomoci při lepší distribuci vašich virtuálních počítačů napříč oblastmi. Další informace najdete v tématu [párování oblastí Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

#### <a name="availability-sets-and-scale-sets"></a>Skupiny dostupnosti a škálovací sady

Při nasazení úlohy na virtuálních počítačích Azure, můžete vytvořit virtuální počítače ve skupině dostupnosti pro zajištění vysoké dostupnosti pro vaši aplikaci. Tím se zajistí, že během výpadku nebo události rebootful údržby je k dispozici alespoň jeden virtuální počítač.

V rámci skupiny dostupnosti jednotlivé virtuální počítače jsou rozloženy až 20 aktualizační doména (ud). Během plánované údržby se aktualizuje jenom jednu aktualizační doménu v daném okamžiku. Pořadí aktualizačních domén aktualizuje tomu tak není nutně postupně. 

Škálovací sady virtuálních počítačů jsou výpočetním prostředkem Azure, která umožňuje nasadit a spravovat sadu identických virtuálních počítačů jako jeden prostředek. Škálovací sady se automaticky nasadí napříč doménami aktualizace, jako jsou virtuální počítače ve skupině dostupnosti. Stejně jako se skupinami dostupnosti se škálovacími sadami pouze jedné aktualizační doméně se aktualizuje v daném okamžiku během plánované údržby.

Další informace o konfiguraci virtuálních počítačů pro zajištění vysoké dostupnosti najdete v tématu Správa dostupnosti virtuálních počítačů pro [Windows](../articles/virtual-machines/windows/manage-availability.md) nebo [Linux](../articles/virtual-machines/linux/manage-availability.md).

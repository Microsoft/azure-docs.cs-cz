---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: include
ms.date: 07/05/2018
ms.author: shants
ms.custom: include file
ms.openlocfilehash: f203e056df00fb1a9b1e7e43930955040dfce4aa
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39030033"
---
Azure pravidelně provádí aktualizace za účelem zlepšení spolehlivosti, výkonu a zabezpečení hostitelské infrastruktury pro virtuální počítače. Rozsah těchto aktualizací je od oprav softwarových komponent v hostitelském prostředí (např. operační systém, hypervisor a různí agenti nasazení v hostiteli) přes upgrade síťových komponent až po vyřazení hardwaru z provozu. Většina těchto aktualizací nemá jejich provedení žádný vliv na hostované virtuální počítače. Existují však případy, kdy aktualizace mít vliv:

- Pokud aktualizace bez restartování počítače je možné, využívá Azure Údržba pro zachování paměti se pozastavit virtuální počítač při aktualizaci hostitele nebo virtuální počítač je úplně přesunout do už aktualizovaného hostitele.

- Údržba vyžaduje restartování, dostanete oznámení o při plánované údržby. V těchto případech budete také mít k dispozici časový interval, kde můžete začít údržbu sami v čase, která vám vyhovuje.

Tato stránka popisuje, jak Microsoft Azure provede oba typy údržby. Další informace o neplánované události (výpadky), naleznete v části Správa dostupnosti virtuálních počítačů pro [Windows](../articles/virtual-machines/windows/manage-availability.md) nebo [Linux](../articles/virtual-machines/linux/manage-availability.md).

Aplikace běžící na virtuálním počítači může shromažďovat informace o budoucích aktualizacích pomocí služby Azure Metadata pro [Windows](../articles/virtual-machines/windows/instance-metadata-service.md) nebo [Linux](../articles/virtual-machines/linux/instance-metadata-service.md).

"Postupy:" informace o správě plánované údržby, najdete v části "Zpracování oznámení plánované údržby" pro [Linux](../articles/virtual-machines/linux/maintenance-notifications.md) nebo [Windows](../articles/virtual-machines/windows/maintenance-notifications.md).

## <a name="memory-preserving-maintenance"></a>Údržba pro zachování paměti

Při aktualizace nevyžadují úplného restartování, údržby mechanismy zachováním paměti umožňují omezit dopad na virtuální počítač. Virtuální počítač je pozastavený po dobu až 30 sekund, paměť se zachová v paměti RAM, zatímco hostitelské prostředí platí potřebné aktualizace a opravy nebo přesune virtuální počítač už aktualizovaného hostitele. Virtuální počítač se pak obnoví a hodiny virtuálního počítače se automaticky synchronizuje. 

Tyto operace údržby bez rebootful jsou použité doména podle domény selhání a průběh je zastaven, pokud jsou přijímány všechny signály stavu upozornění.

Některé aplikace může mít vliv tyto typy aktualizací. Aplikace, které provádějí v reálném čase, jako je streamování médií nebo překódování nebo vysokou propustnost sítě scénáře, pro zpracování událostí možná není označený tolerovat 30 sekundové pauzy. <!-- sooooo, what should they do? --> V případě, že virtuální počítač se přesouvá do jiného hostitele, všimnout některých citlivých úloh mírné snížení výkonu za několik minut, vedoucí k pozastavení virtuálního počítače. 


## <a name="maintenance-requiring-a-reboot"></a>Údržby, které vyžadují restartování

Pokud virtuální počítače, musí se restartuje kvůli plánované údržbě, budete upozorněni předem. Plánovaná údržba má dvě fáze: okno samoobslužných služeb a plánované údržby.

**Samoobslužné služby okno** umožňuje zahájení údržby na virtuálních počítačích. Během této doby se můžete dotazovat každý virtuální počítač, který chcete zobrazit jejich stav a podívat se na výsledek poslední žádosti údržby.

Když začnete samoobslužné údržby, váš virtuální počítač se přesune do uzlu, který již byl aktualizován a pak ji zpět zapne. Vzhledem k tomu, že restartování virtuálního počítače, dočasný disk je ztraceny a dynamické IP adresy přidružené k rozhraní virtuální sítě se aktualizují.

Pokud dojde k chybě během procesu spuštění samoobslužné údržby, operace se zastavila, není aktualizován virtuálního počítače a budete mít možnost to chcete zkusit znovu samoobslužné údržby. 

Po uplynutí okno samoobslužných služeb, **plánované časové období údržby** začíná. Během tohoto časového intervalu můžete stále dotaz pro období údržby, ale nadále již nebudou schopni spustit údržbu sami.

Informace o správě údržby, které vyžadují restartování, najdete v části "Zpracování oznámení plánované údržby" pro [Linux](../articles/virtual-machines/linux/maintenance-notifications.md) nebo [Windows](../articles/virtual-machines/windows/maintenance-notifications.md). 

### <a name="availability-considerations-during-scheduled-maintenance"></a>Důležité informace o dostupnosti během plánované údržby 

Pokud se rozhodnete Počkejte, dokud plánované časové období údržby, existuje několik věcí, které je potřeba zvážit udržována nejvyšší dostupnost vašich virtuálních počítačů. 

#### <a name="paired-regions"></a>Spárované oblasti

Každá oblast Azure je spárovaná s jinou oblastí na stejném území, společně využívají páru oblastí. Během plánované údržby Azure aktualizuje pouze virtuální počítače v jedné oblasti z páru oblastí. Například při aktualizaci virtuálních počítačů v oblasti Střed USA – sever nebude Azure zároveň aktualizovat žádné virtuální počítače v oblasti Střed USA – jih. V ostatních oblastech, jako je Severní Evropa, však může údržba probíhat ve stejnou dobu jako v oblasti Východní USA. Principy fungování párování oblastí vám mohou pomoci při lepší distribuci vašich virtuálních počítačů napříč oblastmi. Další informace najdete v tématu [párování oblastí Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

#### <a name="availability-sets-and-scale-sets"></a>Skupiny dostupnosti a škálovací sady

Při nasazení úlohy na virtuálních počítačích Azure, můžete vytvořit virtuální počítače ve skupině dostupnosti pro zajištění vysoké dostupnosti pro vaši aplikaci. Tím se zajistí, že během výpadku nebo události rebootful údržby je k dispozici alespoň jeden virtuální počítač.

V rámci skupiny dostupnosti jednotlivé virtuální počítače jsou rozloženy až 20 aktualizační doména (ud). Během plánované údržby je to jenom jednu aktualizační doménu, která má vliv v daném okamžiku. Mějte na paměti, že pořadí aktualizačních domén ně neměly vliv neproběhne ani nemusí být sekvenčně. 

Škálovací sady virtuálních počítačů jsou výpočetním prostředkem Azure, která umožňuje nasadit a spravovat sadu identických virtuálních počítačů jako jeden prostředek. Škálovací sady se automaticky nasadí napříč doménami aktualizace, jako jsou virtuální počítače ve skupině dostupnosti. Stejně jako se skupinami dostupnosti se škálovacími sadami jenom jednu aktualizační doména má vliv v daném okamžiku během plánované údržby.

Další informace o konfiguraci virtuálních počítačů pro zajištění vysoké dostupnosti najdete v tématu Správa dostupnosti virtuálních počítačů pro [Windows](../articles/virtual-machines/windows/manage-availability.md) nebo [Linux](../articles/virtual-machines/linux/manage-availability.md).

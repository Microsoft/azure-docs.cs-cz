---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/10/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 2daaf9bbdf90029f0aad4333ab94e2d1d1d3d7ff
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2020
ms.locfileid: "79129384"
---
## <a name="limitations"></a>Omezení

- Sady škálování virtuálních počítačů se na vyhrazených hostitelích aktuálně nepodporují.

## <a name="benefits"></a>Výhody 

Udržování celého hostitele přináší následující výhody:

-   Izolace hardwaru na úrovni fyzického serveru. Do hostitelů nebudou umístěny žádné další virtuální počítače. Vyhrazení hostitelé se nasazují ve stejných datových centrech a sdílejí stejnou síť a základní infrastrukturu úložiště jako ostatní, neizolované hostitele.
-   Kontrola nad událostmi údržby iniciované platformou Azure. I když většina událostí údržby nemá na vašich virtuálních počítačích malý vliv, existují některé citlivé úlohy, které mohou mít vliv na každou sekundu pozastavení. U vyhrazených hostitelů můžete vyjádřit souhlas s časovým obdobím údržby, abyste snížili dopad na vaši službu.
-   Díky programu zvýhodněné hybridní využití Azure můžete přenést vlastní licence pro Windows a SQL do Azure. Používání výhod hybridního využití poskytuje další výhody. Další informace najdete v tématu [zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).


## <a name="groups-hosts-and-vms"></a>Skupiny, hostitelé a virtuální počítače  

![Zobrazení nových prostředků pro vyhrazené hostitele.](./media/virtual-machines-common-dedicated-hosts/dedicated-hosts2.png)

**Skupina hostitelů** je prostředek, který představuje kolekci vyhrazených hostitelů. Vytvoříte skupinu hostitelů v oblasti a zóně dostupnosti a přidáte do ní hostitele.

**Hostitel** je prostředek mapovaný na fyzický server v datovém centru Azure. Fyzický server je přidělen při vytvoření hostitele. Hostitel se vytvoří v rámci skupiny hostitelů. Hostitel má SKU popisující, které velikosti virtuálních počítačů je možné vytvořit. Každý hostitel může hostovat několik virtuálních počítačů v různých velikostech, pokud jsou ze stejné řady velikostí.

Při vytváření virtuálního počítače v Azure můžete vybrat vyhrazeného hostitele, který se má použít pro váš virtuální počítač. Máte plnou kontrolu nad tím, které virtuální počítače jsou umístěné na hostitelích.


## <a name="high-availability-considerations"></a>Požadavky na vysokou dostupnost 

Pro zajištění vysoké dostupnosti byste měli nasadit víc virtuálních počítačů, které jsou rozdělené mezi několik hostitelů (minimálně 2). U vyhrazených hostitelů Azure máte k dispozici několik možností, jak zajistit, aby vaše infrastruktura mohla natvarovat hranice izolace chyb.

### <a name="use-availability-zones-for-fault-isolation"></a>Pro izolaci chyb použít Zóny dostupnosti

Zóny dostupnosti jsou jedinečná fyzická umístění v rámci oblasti Azure. Každou zónu tvoří jedno nebo několik datacenter vybavených nezávislým napájením, chlazením a sítí. Skupina hostitelů se vytvoří v jedné zóně dostupnosti. Po vytvoření budou všichni hostitelé umístěni v rámci této zóny. Chcete-li dosáhnout vysoké dostupnosti napříč zónami, je třeba vytvořit více skupin hostitelů (jeden pro každou zónu) a rozložit hostitele odpovídajícím způsobem.

Pokud skupinu hostitelů přiřadíte k zóně dostupnosti, musí se všechny virtuální počítače vytvořené v tomto hostiteli vytvořit ve stejné zóně.

### <a name="use-fault-domains-for-fault-isolation"></a>Použití domén selhání pro izolaci chyb

Hostitele je možné vytvořit v určité doméně selhání. Stejně jako virtuální počítač ve skupině pro škálování nebo v sadě dostupnosti budou hostitelé v různých doménách selhání umístit do různých fyzických skříní v datovém centru. Při vytváření skupiny hostitelů je nutné zadat počet domén selhání. Při vytváření hostitelů v rámci skupiny hostitelů přiřadíte doménu selhání pro každého hostitele. Virtuální počítače nevyžadují žádné přiřazení domény selhání.

Domény selhání se neshodují se stejným způsobem jako společné. Stejná doména selhání u dvou hostitelů neznamená, že jsou mezi sebou vzájemně blízko.

Doména selhání je vymezena pro skupinu hostitelů. Neměli byste provádět žádné předpoklady proti spřažení mezi dvěma skupinami hostitelů (pokud nejsou v různých zónách dostupnosti).

Virtuální počítače nasazené na hostitele s různými doménami selhání budou mít své základní služby spravovaných disků na více razítkech úložiště, aby se zvýšila ochrana před izolací chyb.

### <a name="using-availability-zones-and-fault-domains"></a>Používání Zóny dostupnosti a domén selhání

Obě možnosti můžete použít společně a dosáhnout tak i větší izolace chyb. V takovém případě zadáte zónu dostupnosti a počet domén selhání v nástroji pro každou skupinu hostitelů, přiřadíte doménu selhání ke každému z vašich hostitelů ve skupině a přiřadíte zónu dostupnosti ke každému z vašich virtuálních počítačů.

[Zde](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md) nalezená Ukázková šablona správce prostředků používá zóny a domény selhání pro rozšíření hostitelů na maximální odolnost v oblasti.

## <a name="maintenance-control"></a>Řízení údržby

Infrastruktura, která podporuje vaše virtuální počítače, se občas může aktualizovat, aby vylepšila spolehlivost, výkon, zabezpečení a spouštěla nové funkce. Platforma Azure se snaží minimalizovat dopad údržby platforem, kdykoli to bude možné, ale zákazníci s *citlivými* úlohami údržby se nemůžou tolerovat ještě pár sekund, než se virtuální počítač musí kvůli údržbě ukotvit nebo odpojit.

**Řízení údržby** poskytuje zákazníkům možnost přeskočit běžné aktualizace platforem naplánované na jejich vyhrazených hostitelích a pak je použít v době jejich výběru v rámci 35ho okna.

> [!NOTE]
>  Řízení údržby je aktuálně ve verzi Public Preview. Další informace najdete v tématu **řízení aktualizací pomocí řízení údržby pomocí rozhraní příkazového [řádku](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) nebo [PowerShellu](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-powershell?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json)** .

## <a name="capacity-considerations"></a>Důležité informace o kapacity

Po zřízení vyhrazeného hostitele ho Azure přiřadí k fyzickému serveru. Tím se zajistí dostupnost kapacity v případě, že potřebujete zřídit virtuální počítač. Azure používá celou kapacitu v oblasti (nebo zóně) k výběru fyzického serveru pro hostitele. Také to znamená, že zákazníci můžou očekávat, že budou moci rozšiřovat své vyhrazené hostitele, aniž by to mělo za následek nedostatku místa v clusteru.

## <a name="quotas"></a>Kvóty

Pro vyhrazené hostitele je k dispozici výchozí limit kvóty 3000 vCPU pro jednotlivé oblasti. Počet hostitelů, které můžete nasadit, je taky omezený kvótou pro rodinu velikosti virtuálního počítače, která se používá pro hostitele. Například předplatné s průběžnými **platbami** může mít k dispozici pouze kvótu 10 vCPU pro řadu velikostí Dsv3 v oblasti východní USA. V takovém případě musíte požádat o zvýšení kvóty alespoň na 64 vCPU, než budete moct nasadit vyhrazeného hostitele. V případě potřeby vyberte v pravém horním rohu tlačítko **zvýšení žádosti** , aby se zažádal požadavek.

![Snímek obrazovky se stránkou používání a kvót na portálu](./media/virtual-machines-common-dedicated-hosts/quotas.png)

Další informace najdete v tématu [kvóty VCPU virtuálních počítačů](/azure/virtual-machines/windows/quotas).

Bezplatné zkušební verze a Předplatná MSDN nemají kvótu pro vyhrazené hostitele Azure.

## <a name="pricing"></a>Ceny

Uživatelům se účtují poplatky za vyhrazeného hostitele, bez ohledu na to, kolik virtuálních počítačů se nasazuje. V měsíčním výpisu se zobrazí nový fakturovatelný typ prostředku hostitelů. Virtuální počítače ve vyhrazeném hostiteli se budou dál zobrazovat v prohlášení, ale budou mít cenu 0.

Cena hostitele je nastavená na základě rodiny virtuálních počítačů, typu (velikost hardwaru) a oblasti. Cena za hostitele je relativní vzhledem k největší velikosti virtuálního počítače, která je na hostiteli podporovaná.

Licencování softwaru, úložiště a využití sítě se účtují odděleně od hostitele a virtuálních počítačů. Nedošlo k žádným změnám za tyto fakturovatelné položky.

Další informace najdete v tématu [ceny za vyhrazené hostitele Azure](https://aka.ms/ADHPricing).

Můžete také ušetřit náklady pomocí [rezervované instance vyhrazených hostitelů Azure](../articles/virtual-machines/prepay-dedicated-hosts-reserved-instances.md).
 
## <a name="sizes-and-hardware-generations"></a>Velikosti a generace hardwaru

Pro hostitele je definována SKU, která představuje řadu a typ velikosti virtuálního počítače. V rámci jednoho hostitele můžete kombinovat více virtuálních počítačů s různými velikostmi, pokud mají stejnou řadu velikostí. 

*Typ* je generování hardwaru. Různé typy hardwaru pro stejnou řadu virtuálních počítačů budou od různých dodavatelů CPU a mají různé generace PROCESORů a počet jader. 

Typy velikosti a hardwaru se v jednotlivých oblastech liší. Další informace najdete na [stránce s cenami](https://aka.ms/ADHPricing) hostitele.


## <a name="host-life-cycle"></a>Životní cyklus hostitele


Azure monitoruje a spravuje stav hostitelů. Při dotazování hostitele se vrátí následující stavy:

| Stav   | Popis       |
|----------|----------------|
| Dostupný hostitel     | Neexistují žádné známé problémy s hostitelem.   |
| Hostitel v rámci šetření  | Máme nějaké problémy s hostitelem, který se chystáme najít. Toto je přechodný stav nutný k tomu, aby Azure mohl vyzkoušet a identifikovat rozsah a hlavní příčinu zjištěného problému. Může to mít vliv na virtuální počítače, které běží na hostiteli. |
| Hostitel čeká na zrušení přidělení   | Azure nemůže hostitele obnovit zpátky do stavu v pořádku a požádá o opětovné nasazení virtuálních počítačů z tohoto hostitele. Pokud je povolená možnost `autoReplaceOnFailure`, jsou vaše virtuální počítače *zacelené* na hardwaru v dobrém stavu. V opačném případě je možné, že váš virtuální počítač běží na hostiteli, který selže.|
| Přidělení hostitele  | Z hostitele se odebraly všechny virtuální počítače. Na tohoto hostitele se už neúčtují, protože hardware se vyčerpal z rotace.   |


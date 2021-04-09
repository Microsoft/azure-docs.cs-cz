---
title: Přehled vyhrazených hostitelů Azure pro virtuální počítače
description: Přečtěte si další informace o tom, jak se můžou používat vyhrazené hostitele Azure pro nasazení virtuálních počítačů.
author: cynthn
ms.service: virtual-machines
ms.subservice: dedicated-hosts
ms.topic: conceptual
ms.workload: infrastructure
ms.date: 12/07/2020
ms.author: cynthn
ms.reviewer: zivr
ms.openlocfilehash: 46de182c7acfaf75b2e65fa318717348dd1c4b73
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101667345"
---
# <a name="azure-dedicated-hosts"></a>Vyhrazení hostitelé Azure

Vyhrazený hostitel Azure je služba, která poskytuje fyzické servery – schopné hostovat jeden nebo víc virtuálních počítačů, které jsou vyhrazené pro jedno předplatné Azure. Vyhrazení hostitelé jsou stejné fyzické servery, které se používají v našich datových centrech, které jsou poskytované jako prostředek. Můžete zřídit vyhrazené hostitele v rámci oblasti, zóny dostupnosti a domény selhání. Pak můžete virtuální počítače umístit přímo do zřízených hostitelů, v libovolné konfiguraci nejlépe vyhovuje vašim potřebám.


## <a name="benefits"></a>Výhody 

Udržování celého hostitele přináší následující výhody:

-   Izolace hardwaru na úrovni fyzického serveru. Do hostitelů nebudou umístěny žádné další virtuální počítače. Vyhrazení hostitelé se nasazují ve stejných datových centrech a sdílejí stejnou síť a základní infrastrukturu úložiště jako ostatní, neizolované hostitele.
-   Kontrola nad událostmi údržby iniciované platformou Azure. I když většina událostí údržby nemá na vašich virtuálních počítačích malý vliv, existují některé citlivé úlohy, které mohou mít vliv na každou sekundu pozastavení. U vyhrazených hostitelů můžete vyjádřit souhlas s časovým obdobím údržby, abyste snížili dopad na vaši službu.
-   Díky programu zvýhodněné hybridní využití Azure můžete přenést vlastní licence pro Windows a SQL do Azure. Používání výhod hybridního využití poskytuje další výhody. Další informace najdete v tématu [zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).


## <a name="groups-hosts-and-vms"></a>Skupiny, hostitelé a virtuální počítače  

![Zobrazení nových prostředků pro vyhrazené hostitele.](./media/virtual-machines-common-dedicated-hosts/dedicated-hosts2.png)

**Skupina hostitelů** je prostředek, který představuje kolekci vyhrazených hostitelů. Vytvoříte skupinu hostitelů v oblasti a zóně dostupnosti a přidáte do ní hostitele.

**Hostitel** je prostředek mapovaný na fyzický server v datovém centru Azure. Fyzický server je přidělen při vytvoření hostitele. Hostitel se vytvoří v rámci skupiny hostitelů. Hostitel má SKU popisující, které velikosti virtuálních počítačů je možné vytvořit. Každý hostitel může hostovat několik virtuálních počítačů v různých velikostech, pokud jsou ze stejné řady velikostí.


## <a name="high-availability-considerations"></a>Požadavky na vysokou dostupnost 

Pro zajištění vysoké dostupnosti byste měli nasadit víc virtuálních počítačů, které jsou rozdělené mezi několik hostitelů (minimálně 2). U vyhrazených hostitelů Azure máte k dispozici několik možností, jak zajistit, aby vaše infrastruktura mohla natvarovat hranice izolace chyb.

### <a name="use-availability-zones-for-fault-isolation"></a>Pro izolaci chyb použít Zóny dostupnosti

Zóny dostupnosti jsou jedinečná fyzická umístění v rámci oblasti Azure. Každou zónu tvoří jedno nebo několik datacenter vybavených nezávislým napájením, chlazením a sítí. Skupina hostitelů se vytvoří v jedné zóně dostupnosti. Po vytvoření budou všichni hostitelé umístěni v rámci této zóny. Chcete-li dosáhnout vysoké dostupnosti napříč zónami, je třeba vytvořit více skupin hostitelů (jeden pro každou zónu) a rozložit hostitele odpovídajícím způsobem.

Pokud skupinu hostitelů přiřadíte k zóně dostupnosti, musí se všechny virtuální počítače vytvořené v tomto hostiteli vytvořit ve stejné zóně.

### <a name="use-fault-domains-for-fault-isolation"></a>Použití domén selhání pro izolaci chyb

Hostitele je možné vytvořit v určité doméně selhání. Stejně jako virtuální počítač ve skupině pro škálování nebo v sadě dostupnosti budou hostitelé v různých doménách selhání umístit do různých fyzických skříní v datovém centru. Při vytváření skupiny hostitelů je nutné zadat počet domén selhání. Při vytváření hostitelů v rámci skupiny hostitelů přiřadíte doménu selhání pro každého hostitele. Virtuální počítače nevyžadují žádné přiřazení domény selhání.

Domény selhání nejsou stejné jako společné umístění. Stejná doména selhání u dvou hostitelů neznamená, že jsou mezi sebou vzájemně blízko.

Doména selhání je vymezena pro skupinu hostitelů. Neměli byste provádět žádné předpoklady proti spřažení mezi dvěma skupinami hostitelů (pokud nejsou v různých zónách dostupnosti).

Virtuální počítače nasazené na hostitele s různými doménami selhání budou mít své základní služby spravovaných disků na více razítkech úložiště, aby se zvýšila ochrana před izolací chyb.

### <a name="using-availability-zones-and-fault-domains"></a>Používání Zóny dostupnosti a domén selhání

Obě možnosti můžete použít společně a dosáhnout tak i větší izolace chyb. V takovém případě zadáte zónu dostupnosti a počet domén selhání v nástroji pro každou skupinu hostitelů, přiřadíte doménu selhání ke každému z vašich hostitelů ve skupině a přiřadíte zónu dostupnosti ke každému z vašich virtuálních počítačů.

[Zde](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md) nalezená Ukázková šablona správce prostředků používá zóny a domény selhání pro rozšíření hostitelů na maximální odolnost v oblasti.


## <a name="manual-vs-automatic-placement"></a>Ruční a automatické umístění 

Při vytváření virtuálního počítače v Azure můžete vybrat vyhrazeného hostitele, který se má použít. Můžete také použít možnost k automatickému umístění virtuálních počítačů na existující hostitele v rámci skupiny hostitelů. 

Při vytváření nové skupiny hostitelů se ujistěte, že je vybrané nastavení pro automatické umístění virtuálního počítače. Když vytváříte virtuální počítač, vyberte skupinu hostitelů a umožněte Azure vybrat nejlepšího hostitele pro váš virtuální počítač. 

Skupiny hostitelů, které jsou povolené pro automatické umísťování, nevyžadují, aby se všechny virtuální počítače automaticky umístily. Stále budete moci výslovně vybrat hostitele, i když je pro skupinu hostitelů vybráno automatické umístění. 

### <a name="limitations"></a>Omezení

Známé problémy a omezení při použití automatického umístění virtuálního počítače:

- Na vyhrazených hostitelích nebudete moct využít výhody hybridního využití Azure.
- Nebudete moct znovu nasadit virtuální počítač. 
- Nebudete moct používat virtuální počítače Lsv2, NVasv4, NVsv3, Msv2 nebo M-Series s vyhrazenými hostiteli. 


## <a name="virtual-machine-scale-set-support"></a>Podpora sady škálování virtuálních počítačů

Virtual Machine Scale Sets vám umožní považovat skupinu virtuálních počítačů za jeden prostředek a jako skupinu použít zásady dostupnosti, správy, škálování a orchestrace. Stávající vyhrazené hostitele je možné použít i pro služby Virtual Machine Scale Sets. 

Při vytváření sady škálování virtuálních počítačů můžete zadat existující skupinu hostitelů, aby se všechny instance virtuálních počítačů vytvořily na vyhrazených hostitelích.

Při vytváření sady škálování virtuálních počítačů ve vyhrazené skupině hostitelů platí následující požadavky:

- Automatické umístění virtuálního počítače je nutné povolit.
- Nastavení dostupnosti vaší skupiny hostitelů by se mělo shodovat s vaší sadou škálování. 
    - Místní skupina hostitelů (vytvořená bez určení zóny dostupnosti) by se měla použít pro místní sady škálování.
    - Skupina hostitelů a sada škálování musí používat stejnou zónu dostupnosti. 
    - Počet domén selhání pro úroveň skupiny hostitelů by měl odpovídat počtu domén selhání pro vaši sadu škálování. Azure Portal vám umožní určit *maximální rozprostření* pro sadu škálování, což nastaví počet domén selhání na 1.
- Měli byste nejprve vytvořit vyhrazené hostitele s dostatečnou kapacitou a stejné nastavení pro zóny sady škálování a domény selhání.
- Podporované velikosti virtuálních počítačů pro vaše vyhrazené hostitele by se měly shodovat s velikostí použitou pro vaši sadu škálování.

U vyhrazených hostitelů nejsou podporovaná nastavení Orchestrace a optimalizace sady škálování. U sady škálování použijte následující nastavení: 
- Přezřizování se nedoporučuje a ve výchozím nastavení je zakázané. Můžete povolit převzetí služeb při selhání, ale přidělení sady škálování se nezdaří, pokud skupina hostitelů nebude mít kapacitu pro všechny virtuální počítače, včetně přetížených instancí. 
- Použití režimu orchestrace ScaleSetVM 
- Nepoužívejte pro společné umístění skupiny umístění pro Proximity.



## <a name="maintenance-control"></a>Řízení údržby

Infrastruktura, která podporuje vaše virtuální počítače, se občas může aktualizovat, aby vylepšila spolehlivost, výkon, zabezpečení a spouštěla nové funkce. Platforma Azure se snaží minimalizovat dopad údržby platforem, kdykoli to bude možné, ale zákazníci s *citlivými* úlohami údržby se nemůžou tolerovat ještě pár sekund, než se virtuální počítač musí kvůli údržbě ukotvit nebo odpojit.

**Řízení údržby** poskytuje zákazníkům možnost přeskočit běžné aktualizace platforem naplánované na jejich vyhrazených hostitelích a pak je použít v době jejich výběru v rámci 35ho okna. V okně údržby můžete provádět údržbu přímo na úrovni hostitele, a to v libovolném pořadí. Po překročení časového období údržby přesune Microsoft vpřed a použije nevyřízenou údržbu na hostitele v takovém pořadí, které nemusí následovat po uživatelem definovaných doménách selhání.

Další informace najdete v tématu [Správa aktualizací platformy pomocí řízení údržby](./maintenance-control.md).

## <a name="capacity-considerations"></a>Důležité informace o kapacitách

Po zřízení vyhrazeného hostitele ho Azure přiřadí k fyzickému serveru. Tím se zajistí dostupnost kapacity v případě, že potřebujete zřídit virtuální počítač. Azure používá celou kapacitu v oblasti (nebo zóně) k výběru fyzického serveru pro hostitele. Také to znamená, že zákazníci můžou očekávat, že budou moci rozšiřovat své vyhrazené hostitele, aniž by to mělo za následek nedostatku místa v clusteru.

## <a name="quotas"></a>Kvóty

Existují dva typy kvót, které jsou spotřebovány při nasazení vyhrazeného hostitele.

1. Vyhrazená kvóta vCPU hostitele. Výchozí limit kvóty je 3000 vCPU podle oblasti.
1. Kvóta řady velikostí virtuálních počítačů. Například předplatné s průběžnými **platbami** může mít k dispozici pouze kvótu 10 vCPU pro řadu velikostí Dsv3 v oblasti východní USA. Chcete-li nasadit vyhrazeného hostitele Dsv3, je třeba před nasazením vyhrazeného hostitele požádat o zvýšení kvóty minimálně na 64 vCPU. 

Pokud chcete požádat o zvýšení kvóty, vytvořte žádost o podporu v [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

Zřízení vyhrazeného hostitele bude využívat vyhrazené hostitelské vCPU i kvótu vCPU rodiny virtuálních počítačů, ale nespotřebovává regionální vCPU.


![Snímek obrazovky se stránkou používání a kvót na portálu](./media/virtual-machines-common-dedicated-hosts/quotas.png)

Další informace najdete v tématu [kvóty VCPU virtuálních počítačů](./windows/quotas.md).

Bezplatné zkušební verze a Předplatná MSDN nemají kvótu pro vyhrazené hostitele Azure.

## <a name="pricing"></a>Ceny

Uživatelům se účtují poplatky za vyhrazeného hostitele, bez ohledu na to, kolik virtuálních počítačů se nasazuje. V měsíčním výpisu se zobrazí nový fakturovatelný typ prostředku hostitelů. Virtuální počítače ve vyhrazeném hostiteli se budou dál zobrazovat v prohlášení, ale budou mít cenu 0.

Cena hostitele je nastavená na základě rodiny virtuálních počítačů, typu (velikost hardwaru) a oblasti. Cena za hostitele je relativní vzhledem k největší velikosti virtuálního počítače, která je na hostiteli podporovaná.

Licencování softwaru, úložiště a využití sítě se účtují odděleně od hostitele a virtuálních počítačů. Nedošlo k žádným změnám za tyto fakturovatelné položky.

Další informace najdete v tématu [ceny za vyhrazené hostitele Azure](https://aka.ms/ADHPricing).

Můžete také ušetřit náklady pomocí [rezervované instance vyhrazených hostitelů Azure](prepay-dedicated-hosts-reserved-instances.md).
 
## <a name="sizes-and-hardware-generations"></a>Velikosti a generace hardwaru

Pro hostitele je definována SKU, která představuje řadu a typ velikosti virtuálního počítače. V rámci jednoho hostitele můžete kombinovat více virtuálních počítačů s různými velikostmi, pokud mají stejnou řadu velikostí. 

*Typ* je generování hardwaru. Různé typy hardwaru pro stejnou řadu virtuálních počítačů budou od různých dodavatelů CPU a mají různé generace PROCESORů a počet jader. 

Typy velikosti a hardwaru se v jednotlivých oblastech liší. Další informace najdete na [stránce s cenami](https://aka.ms/ADHPricing) hostitele.

> [!NOTE]
> Jakmile je vyhrazený hostitel zřídit, nemůžete změnit velikost nebo typ. Pokud potřebujete jinou velikost typu, budete muset vytvořit nového hostitele.  

## <a name="host-life-cycle"></a>Životní cyklus hostitele


Azure monitoruje a spravuje stav hostitelů. Při dotazování hostitele se vrátí následující stavy:

| Stav   | Description       |
|----------|----------------|
| Dostupný hostitel     | Neexistují žádné známé problémy s hostitelem.   |
| Hostitel v rámci šetření  | Máme nějaké problémy s hostitelem, který se chystáme najít. Toto je přechodný stav nutný k tomu, aby Azure mohl vyzkoušet a identifikovat rozsah a hlavní příčinu zjištěného problému. Může to mít vliv na virtuální počítače, které běží na hostiteli. |
| Hostitel čeká na zrušení přidělení   | Azure nemůže hostitele obnovit zpátky do stavu v pořádku a požádá o opětovné nasazení virtuálních počítačů z tohoto hostitele. Pokud `autoReplaceOnFailure` je povolený, vaše virtuální počítače budou *zacelené* na hardware v pořádku. V opačném případě je možné, že váš virtuální počítač běží na hostiteli, který selže.|
| Přidělení hostitele  | Z hostitele se odebraly všechny virtuální počítače. Na tohoto hostitele se už neúčtují, protože hardware se vyčerpal z rotace.   |


## <a name="next-steps"></a>Další kroky

- Vyhrazeného hostitele můžete nasadit pomocí [Azure PowerShell](./windows/dedicated-hosts-powershell.md), [portálu](./dedicated-hosts-portal.md)a [Azure CLI](./linux/dedicated-hosts-cli.md).

- [Zde](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)najdete ukázkovou šablonu, která pro maximální odolnost v oblasti používá zóny i domény selhání.

- Můžete také ušetřit náklady pomocí [rezervované instance vyhrazených hostitelů Azure](prepay-dedicated-hosts-reserved-instances.md).

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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79129384"
---
## <a name="limitations"></a>Omezení

- Škálovací sady virtuálních strojů nejsou aktuálně podporovány na vyhrazených hostitelích.

## <a name="benefits"></a>Výhody 

Rezervace celého hostitele poskytuje následující výhody:

-   Izolace hardwaru na úrovni fyzického serveru. Žádné jiné virtuální počítače se umístí na vaše hostitele. Vyhrazení hostitelé jsou nasazeni ve stejných datových centrech a sdílejí stejnou síť ovou a základní infrastrukturu úložiště jako ostatní neizolovaní hostitelé.
-   Kontrola nad událostmi údržby iniciovaných platformou Azure. Zatímco většina událostí údržby mají malý nebo žádný vliv na vaše virtuální počítače, existují některé citlivé úlohy, kde každá sekunda pozastavení může mít vliv. S vyhrazenými hostiteli se můžete přihlásit k oknu údržby, abyste snížili dopad na vaši službu.
-   S hybridní výhodou Azure si do Azure můžete přinést vlastní licence pro Windows a SQL. Použití hybridních výhod vám poskytuje další výhody. Další informace najdete v tématu [Hybridní výhoda Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).


## <a name="groups-hosts-and-vms"></a>Skupiny, hostitelé a virtuální uživatele  

![Zobrazení nových zdrojů pro vyhrazené hostitele.](./media/virtual-machines-common-dedicated-hosts/dedicated-hosts2.png)

**Skupina hostitelů** je prostředek, který představuje kolekci vyhrazených hostitelů. Vytvoříte skupinu hostitelů v oblasti a zóně dostupnosti a přidáte do ní hostitele.

**Hostitel** je prostředek mapovaný na fyzický server v datovém centru Azure. Fyzický server je přidělen při vytvoření hostitele. Hostitel je vytvořen v rámci skupiny hostitelů. Hostitel má skladovou položku popisující, které velikosti virtuálních počítače lze vytvořit. Každý hostitel může hostovat více virtuálních počítačů různých velikostí, pokud jsou ze stejné řady velikostí.

Při vytváření virtuálního počítače v Azure si můžete vybrat, který vyhrazený hostitel se má použít pro váš virtuální počítač. Máte plnou kontrolu nad tím, které virtuální počítače jsou umístěny na hostitele.


## <a name="high-availability-considerations"></a>Důležité informace o vysoké dostupnosti 

Pro vysokou dostupnost byste měli nasadit více virtuálních počítačů, které jsou rozloženy na více hostitelích (minimálně 2). S vyhrazenými hostiteli Azure máte několik možností, jak zřídit infrastrukturu tak, aby utvářela hranice izolace chyb.

### <a name="use-availability-zones-for-fault-isolation"></a>Použití zón dostupnosti pro izolaci poruch

Zóny dostupnosti jsou jedinečná fyzická umístění v rámci oblasti Azure. Každou zónu tvoří jedno nebo několik datacenter vybavených nezávislým napájením, chlazením a sítí. Skupina hostitelů je vytvořena v jedné zóně dostupnosti. Po vytvoření budou všichni hostitelé umístěni do této zóny. Chcete-li dosáhnout vysoké dostupnosti napříč zónami, musíte vytvořit více skupin hostitelů (jednu pro zónu) a odpovídajícím způsobem rozložit hostitele.

Pokud přiřadíte skupinu hostitelů do zóny dostupnosti, všechny virtuální počítače vytvořené na tomto hostiteli musí být vytvořeny ve stejné zóně.

### <a name="use-fault-domains-for-fault-isolation"></a>Použití domén selhání pro izolaci selhání

Hostitele lze vytvořit v určité doméně selhání. Stejně jako virtuální počítač ve škálovací sadě nebo dostupnosti, hostitelé v různých doménách selhání budou umístěni na různé fyzické racky v datovém centru. Při vytváření skupiny hostitelů je nutné zadat počet domén selhání. Při vytváření hostitelů v rámci skupiny hostitelů přiřazujete doménu selhání každému hostiteli. Virtuální servery nevyžadují žádné přiřazení domény selhání.

Domény selhání nejsou stejné jako společné umístění. Mít stejnou doménu selhání pro dva hostitele neznamená, že jsou v blízkosti navzájem.

Domény selhání jsou vymezeny na skupinu hostitelů. Neměli byste provádět žádné předpoklady o protispřavost mezi dvěma skupinami hostitelů (pokud nejsou v různých zónách dostupnosti).

Virtuální počítače nasazené na hostitele s různými doménami selhání budou mít své základní služby spravovaných disků na více razítkách úložiště, aby se zvýšila ochrana proti izolaci chyb.

### <a name="using-availability-zones-and-fault-domains"></a>Použití zón dostupnosti a domén selhání

Obě funkce můžete použít společně k dosažení ještě větší izolace chyb. V takovém případě určíte počet zóny dostupnosti a domény selhání pro každou skupinu hostitelů, přiřadíte doménu selhání každému z hostitelů ve skupině a každému virtuálnímu počítači přiřadíte zónu dostupnosti.

Ukázková šablona Správce prostředků, [která je zde nalezena,](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md) používá zóny a domény selhání k rozložení hostitelů pro maximální odolnost proti chybám v oblasti.

## <a name="maintenance-control"></a>Řízení údržby

Infrastruktura podporující vaše virtuální počítače může být příležitostně aktualizována, aby se zlepšila spolehlivost, výkon, zabezpečení a spustily se nové funkce. Platforma Azure se snaží minimalizovat dopad údržby platformy, kdykoli je to možné, ale zákazníci s *úlohami citlivými na údržbu* nesnese ani několik sekund, že virtuální počítač musí být zmrazený nebo odpojený z důvodu údržby.

**Řízení údržby** poskytuje zákazníkům možnost přeskočit pravidelné aktualizace platformy naplánované na jejich vyhrazených hostitelích a pak je použít v době, kdy si zvolí, v rámci 35denního klouzavého okna.

> [!NOTE]
>  Řízení údržby je aktuálně ve verzi Public Preview. Další informace naleznete v **tématu Řízení aktualizací pomocí řízení údržby pomocí [příkazového příkazového příkazu nebo](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) [prostředí PowerShell](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-powershell?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json)**.

## <a name="capacity-considerations"></a>Důležité informace o kapacitách

Jakmile je zřízen ý vyhrazený hostitel, Azure ho přiřadí fyzickému serveru. To zaručuje dostupnost kapacity, když potřebujete zřídit virtuální počítač. Azure používá celou kapacitu v oblasti (nebo zóny) k výběru fyzického serveru pro vašeho hostitele. To také znamená, že zákazníci mohou očekávat, že budou moci zvýšit svou vyhrazenou stopu hostitele bez obav o nedostatek místa v clusteru.

## <a name="quotas"></a>Kvóty

Pro vyhrazené hostitele pro danou oblast je výchozí limit kvóty 3000 virtuálních procesorů. Ale počet hostitelů, které můžete nasadit, je také omezen kvótou pro rodinu velikostí virtuálního počítače používanou pro hostitele. Například předplatné **s průběžným platbou** může mít k dispozici pouze kvótu 10 virtuálních procesorů pro řadu velikostí Dsv3 v oblasti USA – východ. V takovém případě je třeba požádat o zvýšení kvóty na alespoň 64 virtuálních procesorů, než budete moci nasadit vyhrazeného hostitele. V pravém horním rohu vyberte tlačítko **Zvýšit požadavek** a v případě potřeby jej podejte.

![Snímek obrazovky se stránkou Využití a kvóty na portálu](./media/virtual-machines-common-dedicated-hosts/quotas.png)

Další informace naleznete [v tématu Kvóty virtuálních procesorů virtuálních procesorů virtuálních procesorů virtuálních zařízení](/azure/virtual-machines/windows/quotas).

Bezplatná zkušební verze a předplatná MSDN nemají kvótu pro vyhrazené hostitele Azure.

## <a name="pricing"></a>Ceny

Uživatelé se účtují za vyhrazeného hostitele, bez ohledu na to, kolik virtuálních počítačů se nasazuje. Ve svém měsíčním výpisu uvidíte nový fakturovatelný typ zdrojů hostitelů. Virtuální počítače na vyhrazeném hostiteli se stále zobrazí ve vašem prohlášení, ale ponesou cenu 0.

Cena hostitele se nastavuje na základě rodiny virtuálních počítačů, typu (velikost hardwaru) a oblasti. Cena hostitele je relativní vzhledem k největší velikosti virtuálního počítače podporované na hostiteli.

Licencování softwaru, úložiště a využití sítě se účtují odděleně od hostitele a virtuálních počítače. Tyto fakturovatelné položky se nemění.

Další informace najdete v [tématu Azure Dedicated Host ceny](https://aka.ms/ADHPricing).

Můžete také ušetřit na nákladech s [rezervovanou instanci vyhrazených hostitelů Azure](../articles/virtual-machines/prepay-dedicated-hosts-reserved-instances.md).
 
## <a name="sizes-and-hardware-generations"></a>Generace velikostí a hardwaru

Skladová položka je definována pro hostitele a představuje řadu a typ velikosti virtuálního počítače. Můžete kombinovat více virtuálních počítačů různých velikostí v rámci jednoho hostitele tak dlouho, dokud jsou stejné velikosti řady. 

*Typ* je generování hardwaru. Různé typy hardwaru pro stejnou řadu virtuálních počítačů budou od různých dodavatelů procesoru a budou mít různé generace procesoru a počet jader. 

Velikosti a typy hardwaru se liší podle oblasti. Další informace najdete na [stránce s cenami](https://aka.ms/ADHPricing) hostitele.


## <a name="host-life-cycle"></a>Životní cyklus hostitele


Azure monitoruje a spravuje stav vašich hostitelů. Následující stavy budou vráceny při dotazování hostitele:

| Zdravotní stav   | Popis       |
|----------|----------------|
| Hostitel k dispozici     | Nejsou známy žádné problémy s hostitelem.   |
| Hostitel v šetření  | Máme nějaké problémy s hostitelem, kterého prověřujeme. Toto je přechodný stav, který je vyžadován pro Azure, aby se pokusil identifikovat obor a hlavní příčinu zjištěného problému. Virtuální počítače spuštěné na hostiteli může být ovlivněna. |
| Host čeká na nadepřidělení   | Azure nemůže obnovit hostitele zpět do stavu v pořádku a požádat vás o opětovné nasazení virtuálních počítačů z tohoto hostitele. Pokud `autoReplaceOnFailure` je povolená, vaše virtuální počítače jsou *služby vyléčen* na zdravý hardware. V opačném případě může být váš virtuální počítač spuštěn na hostiteli, který se chystá selhat.|
| Hostitel nabyzený  | Všechny virtuální počítače byly odebrány z hostitele. Již se za tohoto hostitele neúčtujete, protože hardware byl vyřazen z rotace.   |


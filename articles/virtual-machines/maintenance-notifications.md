---
title: Oznámení o údržbě
description: Přehled oznámení o údržbě pro virtuální počítače spuštěné v Azure.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: 68159577cb31145be5063bb19af6db71ca1727bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77115678"
---
# <a name="handling-planned-maintenance-notifications"></a>Zpracování oznámení o plánované údržbě

Azure pravidelně provádí aktualizace za účelem zlepšení spolehlivosti, výkonu a zabezpečení hostitelské infrastruktury pro virtuální počítače. Aktualizace jsou změny, jako je oprava hostitelského prostředí nebo upgrade a vyřazení hardwaru z provozu. Většina těchto aktualizací je dokončena bez jakéhokoli dopadu na hostované virtuální počítače. Existují však případy, kdy aktualizace mají vliv:

- Pokud údržba nevyžaduje restartování, Azure používá migraci na místě pozastavit virtuální počítač, zatímco se aktualizuje hostitel. Tyto typy operací údržby jsou použity doméně selhání doménou selhání. Průběh je zastaven, pokud jsou přijaty všechny signály varovného stavu.

- Pokud údržba vyžaduje restartování, zobrazí se upozornění, kdy je údržba plánována. Dostanete časové období asi 35 dní, kdy můžete spustit údržbu sami, když to funguje pro vás.


Plánovaná údržba, která vyžaduje restartování, je naplánována ve vlnách. Každá vlna má jiný rozsah (regiony).

- Vlna začíná oznámením zákazníkům. Ve výchozím nastavení je oznámení odesláno správci služeb a spolusprávcům. Pomocí [upozornění protokolu aktivit](../service-health/alerts-activity-log-service-notifications.md)můžete přidat další příjemce a možnosti zasílání zpráv, jako jsou e-maily, SMS a webhooky.  
- Jakmile oznámení zhasne, je k dispozici *samoobslužné okno.* Během tohoto okna můžete zjistit, které z vašich virtuálních počítačů jsou ovlivněny a spustit údržbu na základě vlastních potřeb plánování. Samoobslužné okno je obvykle asi 35 dní.
- Po samoobslužné okno okno *plánované údržby* začíná. V určitém okamžiku během tohoto okna Azure naplánuje a použije požadovanou údržbu vašeho virtuálního počítače. 

Cílem dvou oken je poskytnout vám dostatek času na spuštění údržby a restartování virtuálního počítače a zároveň vědět, kdy Azure automaticky spustí údržbu.


Můžete použít portál Azure, PowerShell, ROZHRANÍ API REST a rozhraní příkazového příkazového příkazu k dotazování na okna údržby pro vaše virtuální počítače a spustit samoobslužnou údržbu.

 
## <a name="should-you-start-maintenance-using-during-the-self-service-window"></a>Měli byste začít s údržbou během samoobslužného okna?  

Následující pokyny by vám měly pomoci rozhodnout, zda tuto funkci použít a zahájit údržbu ve svůj vlastní čas. 

> [!NOTE] 
> Samoobslužná údržba nemusí být dostupná pro všechny vaše virtuální počítače. Chcete-li zjistit, zda proaktivní opětovné nasazení je k dispozici pro váš virtuální počítač, vyhledejte **start nyní** ve stavu údržby. Samoobslužná údržba není momentálně k dispozici pro cloudové služby (role webu/pracovního procesu) a service fabric.


Samoobslužná údržba se nedoporučuje pro nasazení pomocí **sad dostupnosti**. Skupiny dostupnosti jsou již aktualizovány pouze jednu aktualizační doménu najednou. 

- Nechte Azure aktivovat údržbu. Pro údržbu, která vyžaduje restartování, bude údržba provedena aktualizace domény podle aktualizační domény. Aktualizační domény nemusí nutně přijímat údržbu postupně a že je 30 minut pauza mezi aktualizační domény. 
- Pokud se jedná o dočasnou ztrátu určité kapacity (1 aktualizační doména), můžete během období údržby přidat instance. 
- Pro údržbu, která nevyžaduje restartování, jsou aktualizace použity na úrovni domény selhání. 

**Nepoužívejte** samoobslužnou údržbu v následujících scénářích: 
- Pokud často vypínáte virtuální počítače, a to buď ručně, pomocí DevTest Labs, pomocí automatického vypnutí nebo podle plánu, může to vrátit stav údržby a proto způsobit další prostoje.
- Na krátkých virtuálních počítačích, o kterých víte, že budou odstraněny před koncem vlny údržby. 
- Pro úlohy s velkým stavem uloženým na místním (dočasném) disku, který je žádoucí udržovat při aktualizaci. 
- V případech, kdy často změníte velikost virtuálního počítače, protože by to mohlo vrátit stav údržby. 
- Pokud jste přijali naplánované události, které umožňují proaktivní převzetí služeb při selhání nebo řádné vypnutí pracovního vytížení, 15 minut před zahájením vypnutí údržby

**Pokud** plánujete spustit virtuální počítač bez přerušení během fáze plánované údržby a žádná z výše uvedených protiindikací se nepoužije, použijte samoobslužnou údržbu. 

Nejlepší je použít samoobslužnou údržbu v následujících případech:
- Musíte sdělit přesné okno údržby vašemu vedení nebo koncovému zákazníkovi. 
- Údržbu je třeba dokončit do daného data. 
- Je třeba řídit pořadí údržby, například vícevrstvé aplikace zaručit bezpečné obnovení.
- Mezi dvěma aktualizačními doménami (UD) je potřeba více než 30 minut doby obnovení virtuálního zařízení. Chcete-li řídit čas mezi aktualizačními doménami, musíte současně aktivovat údržbu na virtuálních počítačích jednu aktualizační doménu (UD).


## <a name="faq"></a>Nejčastější dotazy


**Otázka: Proč je potřeba restartovat virtuální počítače teď?**

**A:** Zatímco většina aktualizací a upgradů na platformu Azure nemá vliv na dostupnost virtuálního počítače, existují případy, kdy se nemůžeme vyhnout restartování virtuálních počítačů hostovaných v Azure. Nashromáždili jsme několik změn, které vyžadují restartování našich serverů, které budou mít za následek restartování virtuálních počítačů.

**Otázka: Pokud se řídím vašimi doporučeními pro vysokou dostupnost pomocí sady dostupnosti, jsem v bezpečí?**

**A:** Virtuální počítače nasazené v sadě dostupnosti nebo škálovací sady virtuálních počítačů mají pojem aktualizační domény (UD). Při provádění údržby Azure respektuje omezení UD a nebude restartovat virtuální počítače z různých UD (ve stejné sadě dostupnosti).  Azure také čeká alespoň 30 minut před přechodem na další skupinu virtuálních počítačů. 

Další informace o vysoké dostupnosti najdete [v tématu dostupnost pro virtuální počítače v Azure](./linux/availability.md).

**Otázka: Jak dostanu upozornění na plánovanou údržbu?**

**A:** Vlna plánované údržby začíná nastavením plánu do jedné nebo více oblastí Azure. Brzy poté se správcům předplatného odešle e-mailové oznámení (jeden e-mail na jedno předplatné). Další kanály a příjemci pro toto oznámení mohou být konfigurováni pomocí výstrah protokolu aktivit. V případě, že nasadíte virtuální počítač do oblasti, kde je již naplánována plánovaná údržba, neobdržíte oznámení, ale budete muset zkontrolovat stav údržby virtuálního počítače.

**Otázka: Nevidím žádné známky plánované údržby na portálu, Powershellu nebo CLI. Co je?**

**A:** Informace týkající se plánované údržby jsou k dispozici během plánované vlny údržby pouze pro virtuální zařízení, které budou ovlivněny. Jinými slovy, pokud nevidíte data, může se zobrazit, že vlna údržby již byla dokončena (nebo nebyla spuštěna) nebo že váš virtuální počítač je již hostovaný na aktualizovaném serveru.

**Otázka: Existuje způsob, jak přesně vědět, kdy bude můj virtuální počítač ovlivněn?**

**A:** Při nastavování plánu definujeme časové období několika dnů. Přesné pořadí serverů (a virtuálních zařízení) v tomto okně však není známo. Zákazníci, kteří by chtěli znát přesný čas pro své virtuální počítače můžete použít [naplánované události](./linux/scheduled-events.md) a dotaz z v rámci virtuálního počítače a přijímat 15 minutové oznámení před restartováním virtuálního počítače.

**Otázka: Jak dlouho vám bude trvat restartování virtuálního počítače?**

**A:**  V závislosti na velikosti virtuálního počítače může restartování trvat až několik minut během okna samoobslužné údržby. Během restartování Azure iniciované v okně plánované údržby restartování obvykle trvá asi 25 minut. Všimněte si, že v případě, že používáte cloudové služby (webová/pracovní role), škálovací sady virtuálních strojů nebo skupiny dostupnosti, budete mít během naplánovaného okna údržby 30 minut mezi každou skupinou virtuálních počítačů (UD).

**Otázka: Jaké jsou zkušenosti v případě škálovacích sad virtuálních strojů?**

**A:** Plánovaná údržba je nyní k dispozici pro škálovací sady virtuálních strojů. Pokyny k zahájení samoobslužné údržby naleznete [v dokumentu plánované údržby pro škálovací sady virtuálních strojů.](../virtual-machine-scale-sets/virtual-machine-scale-sets-maintenance-notifications.md)

**Otázka: Jaké je prostředí v případě cloudových služeb (role webu/pracovního procesu) a service fabric?**

**A:** Zatímco tyto platformy jsou ovlivněny plánovanou údržbou, zákazníci, kteří používají tyto platformy, jsou považováni za bezpečné vzhledem k tomu, že v daném okamžiku budou ovlivněny pouze virtuální servery v jedné doméně upgradu (UD). Samoobslužná údržba není momentálně k dispozici pro cloudové služby (role webu/pracovního procesu) a service fabric.

**Otázka: Na virtuálních počítačích se nezobrazují žádné informace o údržbě. Co se pokazilo?**

**A:** Existuje několik důvodů, proč se vám na virtuálních počítačích nezobrazují žádné informace o údržbě:
1.  Používáte předplatné označené jako interní microsoft.
2.  Vaše virtuální počítače nejsou naplánované na údržbu. Je možné, že vlna údržby byla ukončena, zrušena nebo upravena tak, aby vaše virtuální počítače již nebyly ovlivněny.
3.  Do zobrazení seznamu virtuálních počítače nemáte přidán sloupec **Údržba.** Zatímco jsme tento sloupec přidali do výchozího zobrazení, zákazníci, kteří nakonfigurovali zobrazení nevýchozích sloupců, musí ručně přidat sloupec **Údržba** do zobrazení seznamu virtuálních počítače.

**Otázka: Můj virtuální virtuální ms je naplánováno na údržbu podruhé. Proč?**

**A:** Existuje několik případů použití, kdy se váš virtuální počítač zobrazí naplánované údržby po dokončení údržby redeploy:
1.  Zrušili jsme vlnu údržby a restartovali ji s jiným nákladem. Je možné, že jsme zjistili chybnou datovou část a jednoduše potřebujeme nasadit další datovou část.
2.  Váš virtuální počítač byl *služba vyléčena* do jiného uzlu z důvodu selhání hardwaru.
3.  Vybrali jste zastavit (navrátit) a restartovat virtuální počítač.
4.  Máte pro virtuální počítač zapnuté **automatické vypnutí.**



## <a name="next-steps"></a>Další kroky

Plánovanou údržbu můžete zpracovat pomocí [Azure CLI](maintenance-notifications-cli.md), [Azure PowerShellnebo](maintenance-notifications-powershell.md) [portálu](maintenance-notifications-portal.md).


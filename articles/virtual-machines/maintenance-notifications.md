---
title: Oznámení o údržbě
description: Přehled oznámení o údržbě pro virtuální počítače běžící v Azure.
author: shants123
ms.service: virtual-machines
ms.subservice: maintenance-control
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 8/12/2020
ms.author: shants
ms.openlocfilehash: 92cb780a80f1010fd1c2f5d19fe616e0285de73b
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102564522"
---
# <a name="handling-planned-maintenance-notifications"></a>Zpracování oznámení o plánované údržbě

Azure pravidelně provádí aktualizace za účelem zlepšení spolehlivosti, výkonu a zabezpečení hostitelské infrastruktury pro virtuální počítače. Aktualizace jsou změny, jako je třeba oprava hostitelského prostředí nebo inovace a vyřazení hardwaru z provozu. Většina těchto aktualizací se dokončí bez jakéhokoli dopadu na hostované virtuální počítače. Existují však případy, kdy aktualizace mají dopad:

- Pokud údržba nevyžaduje restart, Azure během aktualizace hostitele pozastaví virtuální počítač o několik sekund. Tyto typy operací údržby používají doménu selhání podle domény selhání. Průběh se zastaví, pokud se obdrží nějaké signály stavu upozornění.

- Pokud údržba vyžaduje restart, dostanete oznámení o tom, kdy se údržba plánuje. Máte k disčase časový interval přibližně 35 dní, kdy můžete zahájit údržbu sami, když to bude fungovat.


Plánovaná údržba, která vyžaduje restart, je naplánována na vlny. Každý vlna má jiný obor (oblasti).

- Wave začíná oznámením pro zákazníky. Ve výchozím nastavení se oznámení posílá správcům předplatného a spolusprávcům. Pomocí [upozornění protokolu aktivit](../service-health/alerts-activity-log-service-notifications-portal.md)můžete přidat další příjemce a možnosti zasílání zpráv, jako jsou E-mail, SMS a Webhooky.  
- Jakmile se oznámení dostane, zpřístupní se *samoobslužné okno* . Během tohoto okna se můžete dotazovat na to, které z vašich virtuálních počítačů jsou ovlivněné, a zahájit údržbu na základě vlastních potřeb plánování. Samoobslužné okno je obvykle přibližně 35 dní.
- Po samoobslužném okně začne *plánované časové období údržby* . V určitém okamžiku v tomto okně Azure plánuje a na virtuálním počítači aplikuje požadovanou údržbu. 

Cílem v aplikaci je mít dvě okna, abyste měli dostatek času na spuštění údržby a restartování virtuálního počítače s vědomím, kdy bude Azure automaticky spouštět údržbu.


K dotazování na časová období údržby pro vaše virtuální počítače můžete použít Azure Portal, PowerShell, REST API a CLI a spustit samoobslužnou údržbu.

 
## <a name="should-you-start-maintenance-using-during-the-self-service-window"></a>Měli byste začít s údržbou pomocí služby během samoobslužného okna?  

Následující pokyny vám pomohou rozhodnout, zda tuto možnost použít, a spustit údržbu ve vašem čase. 

> [!NOTE] 
> Samoobslužná údržba nemusí být k dispozici pro všechny vaše virtuální počítače. Pokud chcete zjistit, jestli je pro váš virtuální počítač k dispozici proaktivní opětovné nasazení, vyhledejte **Spustit nyní** ve stavu údržby. Samoobslužná údržba není v současnosti dostupná pro Cloud Services (Web/role pracovního procesu) a Service Fabric.


Samoobslužná údržba se nedoporučuje pro nasazení pomocí **skupin dostupnosti**. Skupiny dostupnosti již v jednom okamžiku aktualizují pouze jednu aktualizační doménu. 

- Umožněte službě Azure aktivovat údržbu. V případě údržby, která vyžaduje restart, bude údržba dokončena aktualizací podle aktualizační domény. Aktualizační domény nutně nezískají údržbu postupně a že mezi aktualizačními doménami je pozastavení 30 minut. 
- V případě, že se jedná o dočasnou ztrátu nějaké kapacity (1 doména aktualizace), můžete přidat instance během období údržby. 
- V případě údržby, která nevyžaduje restart, se aktualizace aplikují na úrovni domény selhání. 

 Nepoužívejte samoobslužnou údržbu v následujících scénářích: 
- Pokud jste virtuální počítače často vypnuli ručně, pomocí DevTest Labs, pomocí automatického vypnutí nebo podle plánu, může dojít k vrácení stavu údržby, takže dojde k dalšímu výpadku.
- V případě krátkodobých virtuálních počítačů, o kterých víte, že se odstraní před koncem vlny údržby. 
- Pro úlohy s velkým stavem uloženým na místním (dočasném) disku, který je potřeba udržovat při aktualizaci. 
- Pro případy, kdy se virtuální počítač často mění, protože by mohl vrátit stav údržby. 
- Pokud jste přijali naplánované události, které umožní proaktivní převzetí služeb při selhání nebo řádné vypnutí úlohy, 15 minut před vypnutím údržby

**Samoobslužná** údržba – Pokud plánujete, že váš virtuální počítač bude během plánované fáze údržby nepřerušený, a žádná z výše uvedených údajů není platná. 

Služba samoobslužná údržba se doporučuje používat v následujících případech:
- Pro správu nebo koncového zákazníka musíte sdělit přesné časové období údržby. 
- Je potřeba dokončit údržbu do daného data. 
- Musíte řídit sekvencování údržby, například vícevrstvé aplikace pro zajištění bezpečného obnovení.
- Mezi dvěma aktualizačními doménami se vyžaduje více než 30 minut času obnovení virtuálního počítače. Chcete-li řídit dobu mezi aktualizačními doménami, je nutné aktivovat údržbu v rámci virtuálních počítačů po jedné aktualizační doméně (UD).


## <a name="faq"></a>Časté otázky


**Otázka: Proč potřebujete restartovat virtuální počítače nyní?**

**A:** I když většina aktualizací a upgradů na platformu Azure nemá vliv na dostupnost virtuálního počítače, v některých případech se nemůžeme vyhnout restartování virtuálních počítačů hostovaných v Azure. Shromáždili jsme několik změn, které vyžadují restart našich serverů, které způsobí restartování virtuálních počítačů.

**Otázka: Pokud budu dodržovat doporučení pro vysokou dostupnost pomocí skupiny dostupnosti, je to v bezpečí?**

**A:** Virtuální počítače nasazené ve skupině dostupnosti nebo ve službě Virtual Machine Scale Sets mají pojem aktualizačních domén (UD). Při provádění údržby Azure respektuje omezení UD a nerestartuje virtuální počítače z různých UD (v rámci stejné skupiny dostupnosti).  Azure také před přechodem na další skupinu virtuálních počítačů počká aspoň 30 minut. 

Další informace o vysoké dostupnosti najdete v tématu [dostupnost pro virtuální počítače v Azure](availability.md).

**Otázka: Návody dostávat oznámení o plánované údržbě?**

**A:** Spustí se naplánovaná údržba Wave tím, že nastaví plán na jednu nebo více oblastí Azure. Brzy se pošle e-mailové oznámení správcům předplatného, spolupracovníkům, vlastníkům a přispěvatelům (jeden e-mail na předplatné). Další kanály a příjemci pro toto oznámení můžou být nakonfigurované pomocí upozornění protokolu aktivit. V případě, že nasadíte virtuální počítač do oblasti, ve které je plánovaná údržba již naplánována, nebudete dostávat oznámení, ale budete muset kontrolovat stav údržby virtuálního počítače.

**Otázka: v portálu, PowerShellu nebo rozhraní příkazového řádku se nezobrazují žádné informace o plánované údržbě. Co je?**

**A:** Informace týkající se plánované údržby jsou k dispozici během plánované údržby, pouze pro virtuální počítače, na které bude mít vliv. Jinými slovy, Pokud nevidíte data, může to být tím, že už je dokončená údržba nebo že váš virtuální počítač je už hostovaný na aktualizovaném serveru.

**Otázka: existuje způsob, jak přesně zjistit, kdy to bude mít dopad na můj virtuální počítač?**

**A:** Při nastavování plánu definujeme časový interval několik dní. Přesné pořadí serverů (a virtuálních počítačů) v rámci tohoto okna však není známé. Zákazníci, kteří chtějí znát přesný čas pro své virtuální počítače, můžou použít [naplánované události](./linux/scheduled-events.md) a dotaz z virtuálního počítače a získat oznámení o 15 minutách před RESTARTOVÁNÍM virtuálního počítače.

**Otázka: jak dlouho bude trvat restartování virtuálního počítače?**

**A:**  V závislosti na velikosti virtuálního počítače může restartování trvat až několik minut, než se okno samoobslužné údržby. Během restartování za provozu v systému Azure v okně naplánované údržby bude obvykle trvat přibližně 25 minut. Pamatujte na to, že pokud použijete Cloud Services (Web/role pracovního procesu), Virtual Machine Scale Sets nebo sady dostupnosti, budete během naplánovaného časového období údržby přihlášeni za 30 minut mezi jednotlivými skupinami virtuálních počítačů (UD).

**Otázka: Jaké jsou možnosti v případě Virtual Machine Scale Sets?**

**A:** Pro Virtual Machine Scale Sets je nyní k dispozici plánovaná údržba. Pokyny k zahájení samoobslužné údržby najdete v dokumentu o [plánované údržbě pro službu Virtual Machine Scale Sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-maintenance-notifications.md) .

**Otázka: Jaké jsou možnosti v případě Cloud Services (Web/role pracovního procesu) a Service Fabric?**

**A:** I když jsou tyto platformy ovlivněny plánovanou údržbou, zákazníci, kteří tyto platformy používají, se považují za bezpečné, protože budou mít vliv jenom na virtuální počítače v jedné upgradovací doméně (UD). Samoobslužná údržba není v současnosti dostupná pro Cloud Services (Web/role pracovního procesu) a Service Fabric.

**Otázka: na virtuálních počítačích nevidím žádné informace o údržbě. Co se pokazilo?**

**A:** Existuje několik důvodů, proč se na virtuálních počítačích nezobrazuje žádné informace o údržbě:
1.  Používáte odběr označený jako interní Microsoft.
2.  Pro vaše virtuální počítače není naplánovaná údržba. Může to být tím, že se vlna údržby ukončil, zrušil nebo upravil, aby na vaše virtuální počítače už neovlivnila žádná z nich.
3. Nastavili jste virtuální počítač a potom ho spustili. To může způsobit, že se virtuální počítač přesune do umístění, ve kterém se naplánovala plánovaná vlna údržby. Takže virtuální počítač nebude zobrazovat informace o údržbě. 
4.  Do zobrazení seznamu virtuálních počítačů nemáte přidaný sloupec **Údržba** . I když jsme tento sloupec přidali do výchozího zobrazení, zákazníci, kteří si nakonfigurovali, aby viděli jiné než výchozí sloupce, musí do zobrazení seznamu virtuálních počítačů ručně přidat sloupec **Údržba** .

**Otázka: má virtuální počítač naplánovanou údržbu podruhé. Proč?**

**A:** K dispozici je několik případů použití, kde se po dokončení údržby a opětovného nasazení údržby zobrazí váš virtuální počítač naplánovaný k údržbě:
1.  Zrušili jste vlnu údržby a restartovali ji s jinou datovou částí. Je možné, že jsme zjistili chybnou datovou část a jednoduše potřebujete nasadit další datovou část.
2.  Kvůli hardwarové chybě byla *Služba VM zacelená* na jiný uzel.
3.  Vybrali jste možnost zastavit (zrušit přidělení) a restartovat virtuální počítač.
4.  Pro virtuální počítač je zapnuté **Automatické vypnutí** .



## <a name="next-steps"></a>Další kroky

Plánovanou údržbu můžete zvládnout pomocí [Azure CLI](maintenance-notifications-cli.md), [Azure PowerShell](maintenance-notifications-powershell.md) nebo [portálu](maintenance-notifications-portal.md).

---
title: Zpracování oznámení o údržbě pro virtuální počítače s Linuxem v Azure | Dokumentace Microsoftu
description: Zobrazit oznámení o údržbě pro virtuální počítače s Linuxem v Azure a začněte samoobslužné údržby.
services: virtual-machines-linux
documentationcenter: ''
author: shants123
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/02/2018
ms.author: shants
ms.openlocfilehash: e07937710dd36c14e7118caf6028a161ad7dc4ee
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55753618"
---
# <a name="handling-planned-maintenance-notifications-for-linux-virtual-machines"></a>Zpracování oznámení plánované údržby pro virtuální počítače s Linuxem

Azure pravidelně provádí aktualizace za účelem zlepšení spolehlivosti, výkonu a zabezpečení hostitelské infrastruktury pro virtuální počítače. Aktualizace jsou změny, jako jsou opravy hostitelské prostředí nebo upgradu a vyřazení z provozu hardwaru. Většina těchto aktualizací nemá jejich provedení žádný vliv na hostované virtuální počítače. Existují však případy, kdy aktualizace mít vliv:

- Pokud údržbu nevyžaduje restartování, Azure využívá místní migrace se pozastavit virtuální počítač během aktualizace hostitele. Tyto operace údržby bez rebootful jsou použité doména podle domény selhání a průběh je zastaven, pokud jsou přijímány všechny signály stavu upozornění.

- Údržba vyžaduje restartování, dostanete oznámení o při plánované údržby. V těchto případech budete mít časové okno, kde můžete začít údržbu sami, když se vám bude vyhovovat.


Plánované údržby, které vyžaduje restartování počítače je naplánováno ve vlnách. Každé vlně má jiný rozsah (oblasti).

- Vlnu začíná oznámení pro zákazníky. Ve výchozím nastavení odešle se oznámení na předplatné vlastník a spoluvlastníci. Můžete přidat další příjemce a možnosti zasílání zpráv, jako je e-mail, SMS a webhooky, oznámení pomocí Azure [upozornění protokolu aktivit](../../azure-monitor/platform/activity-logs-overview.md).  
- V době oznámení *samoobslužné služby okno* je k dispozici. Během tohoto časového období můžete vyhledat, které z vašich virtuálních počítačů jsou součástí této vlny a proaktivně spustit údržbu podle svých potřeb plánování.
- Po okno samoobslužných služeb *plánované časové období údržby* začíná. Azure někdy během intervalu plánuje a může požadovaná údržba se vztahuje na virtuální počítač. 

Cílem tím, že dvě okna je vám poskytnou dostatek času spuštění údržby a zároveň budete vědět, když Azure automaticky spustit údržbu restartování vašeho virtuálního počítače.


Dotaz pro období údržby pro virtuální počítače a spusťte samoobslužné údržby můžete pomocí webu Azure portal, Powershellu, rozhraní REST API a rozhraní příkazového řádku.

 
## <a name="should-you-start-maintenance-using-during-the-self-service-window"></a>By měl spustit údržbu pomocí během časového intervalu samoobslužné služby?  

Následující pokyny by vám pomohou určit, jestli se má použít tuto funkci a spustit údržbu na vlastní čas. 

> [!NOTE] 
> Samoobslužné údržby nemusí být k dispozici pro všechny vaše virtuální počítače. Chcete-li zjistit, jestli proaktivní opětovného nasazení je k dispozici pro váš virtuální počítač, vyhledejte **spustit** ve stavu údržby. Samoobslužné údržby není aktuálně k dispozici pro cloudové služby (Web/Role pracovního procesu) a Service Fabric.


Nedoporučuje se používat samoobslužné údržby pro nasazení s využitím **dostupnosti** protože to jsou vysoce dostupné nastavení, kde je ovlivněné jenom jedna aktualizační doména v daném okamžiku. 
- Nechejte systém Azure aktivuje údržbu. Kvůli údržbě, která vyžaduje restartování mějte na paměti, že údržba bude provedeno aktualizační doména podle aktualizačních domén, že aktualizačních domén nebudou přijímat nutně údržbu postupně, a že je pozastavit víkend na 30minutové mezi aktualizační domény. 
- Pokud k dočasné ztrátě některých vaší kapacity (počet domén/aktualizace 1) je důležité zajistit, jej lze snadno kompenzovat přidělením přidání instancí během doby údržby. 
- Údržby, které nevyžaduje restartování, mají aktualizace použít na úrovni domény selhání. 

**Není** používat samoobslužné údržby v následujících scénářích: 
- Pokud vypnete virtuální počítače často, buď ručně, pomocí DevTest Labs, pomocí automatické vypnutí, nebo podle plánu, se může vrátit stav údržby a proto způsobí další prostoje.
- Na virtuálních počítačích krátkodobou znát bude odstraněn před koncem vlna údržby. 
- Pro úlohy s velké stavu uloženého v místní disk (dočasný), který je požadován udržovat při aktualizaci. 
- V případech, kde můžete změnit velikost virtuálního počítače často protože může vrátit zpět stav údržby. 
- Pokud si osvojila naplánované události, které umožňují proaktivní převzetí služeb při selhání nebo řádné vypnutí vašich úloh 15 minut před začátkem údržby vypnutí

**Použití** samoobslužné údržby, pokud máte v úmyslu spustit virtuální počítač bez přerušení během fáze plánované údržby a žádné čítače označení uvedeného výše se vztahují. 

Je nejvhodnější použít samoobslužné údržby v následujících případech:
- Potřebujete pro komunikaci přesné časovém intervalu pro správu nebo koncových zákazníků. 
- Potřebujete k dokončení údržby podle daného data. 
- Je nutné určit pořadí údržby, třeba vícevrstvou aplikaci zajistit bezpečné obnovení.
- Potřebujete více než 30 minut od čas obnovení virtuálního počítače mezi dvěma aktualizační doména (ud). K řízení času mezi aktualizačních doménách, musí spustit údržbu na vaše virtuální počítače jedné aktualizační doméně (UD) současně.



## <a name="find-vms-scheduled-for-maintenance-using-cli"></a>Vyhledání virtuálních počítačů s naplánovanou údržbou pomocí CLI

Informace o plánované údržbě můžete zobrazit pomocí [azure vm get-instance-view](/cli/azure/vm?view=azure-cli-latest).
 
Informace o údržbě je vrácen pouze v případě, že se plánované údržby. Pokud není že naplánovaná žádná Údržba této důsledky pro virtuální počítač, příkaz nevrací žádné informace o údržbě. 

```azure-cli
az vm get-instance-view -g rgName -n vmName
```

V části MaintenanceRedeployStatus se vrátí následující hodnoty: 

| Hodnota | Popis   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Určuje, zda lze spustit údržbu na virtuálním počítači v tuto chvíli ||
| PreMaintenanceWindowStartTime         | Začátek samoobslužné časové období údržby při údržby můžete spustit na virtuálním počítači ||
| PreMaintenanceWindowEndTime           | Konec samoobslužné časové období údržby při údržby můžete spustit na virtuálním počítači ||
| MaintenanceWindowStartTime            | Začátek okna plánované údržby, ve kterém Azure zahájí Údržba na vašem virtuálním počítači ||
| MaintenanceWindowEndTime              | Konci okna plánované údržby, ve kterém Azure zahájí Údržba na vašem virtuálním počítači ||
| LastOperationResultCode               | Výsledek posledního pokusu o zahájení údržby ve virtuálním počítači ||




## <a name="start-maintenance-on-your-vm-using-cli"></a>Spustit údržbu na virtuálním počítači pomocí rozhraní příkazového řádku

Následující volání zahájí údržbu na virtuálním počítači, pokud `IsCustomerInitiatedMaintenanceAllowed` je nastavena na hodnotu true.

```azure-cli
az vm perform-maintenance -g rgName -n vmName 
```

[!INCLUDE [virtual-machines-common-maintenance-notifications](../../../includes/virtual-machines-common-maintenance-notifications.md)]

## <a name="classic-deployments"></a>Nasazení Classic

Pokud máte starší verze virtuálních počítačů, které byly nasazené pomocí modelu nasazení classic, můžete použít Azure CLI classic k dotazu pro virtuální počítače a zahájení údržby.

Ujistěte se, že jste do správného režimu pro práci s klasický virtuální počítač tak, že zadáte:

```
azure config mode asm
```

Získat stav údržby virtuálního počítače s názvem *myVM*, typ:

```
azure vm show myVM 
``` 

Chcete-li spustit údržbu na klasickém virtuálním počítači s názvem *myVM* v *Moje_služba* služby a *myDeployment* nasazení, zadejte:

```
azure compute virtual-machine initiate-maintenance --service-name myService --name myDeployment --virtual-machine-name myVM
```


## <a name="faq"></a>Nejčastější dotazy


**Otázka: Proč musí být virtuální počítače restartovat?**

**ODPOVĚĎ:** Přestože většinu aktualizací a upgradů platformy Azure nemají vliv na dostupnost virtuálního počítače, existují případy, kdy Nedokážeme vyhnout restartování virtuálních počítačů hostovaných v Azure. Jsme nashromáždili několik změn, které vyžadují restartování našich serverů, které způsobí restartování virtuálních počítačů.

**Otázka: Pokud mám postupovat podle doporučení pro zajištění vysoké dostupnosti pomocí skupinu dostupnosti, mám bezpečný?**

**ODPOVĚĎ:** Virtuální počítače nasazené ve skupině dostupnosti nebo škálovací sadě virtuálních počítačů rozeznávají aktualizační domény (UD). Při provádění údržby Azure dodržuje omezení UD a nerestartuje virtuální počítače z různých UD (v rámci stejné skupiny dostupnosti).  Azure také čeká aspoň 30 minut před přechodem na další skupinu virtuálních počítačů. 

Další informace o vysoké dostupnosti najdete v tématu [oblasti a dostupnost virtuálních počítačů v Azure](regions-and-availability.MD).

**Otázka: Jak mi oznámení o plánované údržbě?**

**ODPOVĚĎ:** Nastavením plánu na jeden nebo více oblastech Azure začíná vlny plánované údržby. Krátce po e-mailové oznámení, přijde vlastníkům předplatného (jeden e-mail na jedno předplatné). Další kanály a příjemců tohoto oznámení by mohla být nakonfigurovaný pomocí upozornění protokolu aktivit. V případě, že nasazujete virtuální počítač do oblasti, ve kterém je již naplánována plánované údržby, nebude dostávat oznámení ale místo toho budete muset zkontrolovat stav údržby virtuálního počítače.

**Otázka: Můžu nezobrazuje žádný indikátor plánované údržby na portálu, Powershellu nebo rozhraní příkazového řádku. Co je?**

**ODPOVĚĎ:** Informace týkající se plánované údržby je dostupná během vlny plánované údržby pouze pro virtuální počítače, které budou mít vliv. Jinými slovy Pokud se zobrazí ne data, je možné, že vlna údržby má již byla dokončena (nebo nebyla spuštěna) nebo, že váš virtuální počítač už hostuje na aktualizovaném serveru.

**Otázka: Existuje způsob, jak přesně zjistit, kdy ovlivní Můj virtuální počítač?**

**ODPOVĚĎ:** Při nastavování plánu, definujeme časovým intervalem několik dní. Přesné pořadí serverů (a virtuálních počítačů) v rámci tohoto časového období však není znám. Zákazníci, kteří by chtěli zjistit přesný čas pro své virtuální počítače můžete použít [naplánované události](scheduled-events.md) dotaz z v rámci virtuálního počítače a dostanete oznámení 15 minut před restartováním virtuálního počítače.

**Otázka: Jak dlouho bude trvat restartování mého virtuálního počítače?**

**ODPOVĚĎ:**  V závislosti na velikosti virtuálního počítače restartování může trvat až několik minut, než během časového intervalu samoobslužné údržby. Během Azure inicioval restartování počítače v plánované časové období údržby, bude trvat restartování obvykle přibližně 25 minut. Všimněte si, že v případě, že používáte (Web/Role pracovního procesu) Cloud Services, Virtual Machine Scale Sets nebo skupiny dostupnosti, budete mít 30 minut mezi každou skupinu z virtuálních počítačů (UD) během plánované časové období údržby.

**Otázka: Co se děje se Škálovacími sadami virtuálních počítačů?**

**ODPOVĚĎ:** Plánovaná údržba je nyní k dispozici pro Škálovací sady virtuálních počítačů. Pokyny o tom, jak inicializovat samoobslužné údržby najdete v [plánované údržby pro VMSS](../../virtual-machine-scale-sets/virtual-machine-scale-sets-maintenance-notifications.md) dokumentu.

**Otázka: Co se děje Cloud Services (Role Web/Worker) a Service Fabric?**

**ODPOVĚĎ:** Přestože se plánovaná údržba těchto platforem týká, u zákazníků, kteří je využívají, se nepředpokládá žádný problém vzhledem k tomu, že v každém okamžiku bude ovlivňovat jenom virtuální počítače v jedné aktualizační doméně (UD). Samoobslužné údržby není aktuálně k dispozici pro cloudové služby (Web/Role pracovního procesu) a Service Fabric.

**Otázka: Nevidím žádné informace o údržbě na svoje virtuální počítače. K jakému?**

**ODPOVĚĎ:** Tady je několik důvodů, proč nevidíte žádné informace o údržby na virtuálních počítačích:
1.  Používáte předplatné označené jako Microsoft interní.
2.  Nejsou vaše virtuální počítače s naplánovanou údržbou. Je možné, že skončila vlna údržby, zrušené nebo upraví tak, aby vaše virtuální počítače se eliminuje dopad ho.
3.  Nemáte k dispozici **údržby** sloupec přidali do zobrazení seznamu virtuálních počítačů. Když jsme tento sloupec přidali do výchozího zobrazení, třeba ručně přidat zákazníky, kteří nakonfigurovali zobrazíte nevýchozí sloupce **údržby** sloupec k jejich zobrazení seznamu virtuálních počítačů.

**Otázka: Můj virtuální počítač je naplánovaná údržba podruhé. Proč?**

**ODPOVĚĎ:** Existuje několik případů použití, ve kterých se pro virtuální počítač znovu naplánuje údržba bezprostředně poté, co se dokončila:
1.  Jsme zrušena vlna údržby a restartuje s jinou datovou částí. Je možné, že jsme zjistili chybnou datové části a potřebujeme jen nasadit další datové části.
2.  Virtuální počítač byl *službami opraven* do jiného uzlu z důvodu selhání hardwaru.
3.  Vybrali jste zastavení (uvolnění) a restartujte virtuální počítač.
4.  Máte **automatické vypnutí** zapnuta pro daný virtuální počítač.


## <a name="next-steps"></a>Další postup

Zjistěte, jak si zaregistrovat události údržby z v rámci virtuálního počítače pomocí [Scheduled Events](scheduled-events.md).

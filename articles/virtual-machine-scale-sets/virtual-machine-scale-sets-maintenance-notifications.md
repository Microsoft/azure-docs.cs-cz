---
title: Zpracování oznámení o údržbě pro Škálovací sady virtuálních počítačů v Azure | Dokumentace Microsoftu
description: Zobrazit oznámení o údržbě pro Škálovací sady virtuálních počítačů v Azure a začněte samoobslužné údržby.
services: virtual-machine-scale-sets
documentationcenter: ''
author: shants123
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/09/2018
ms.author: shants
ms.openlocfilehash: 4ce984686c2bb320d5d32771d31b81cdec1153af
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38314403"
---
# <a name="handling-planned-maintenance-notifications-for-virtual-machine-scale-sets"></a>Zpracování oznámení plánované údržby pro Škálovací sady virtuálních počítačů

Azure pravidelně provádí aktualizace za účelem zlepšení spolehlivosti, výkonu a zabezpečení hostitelské infrastruktury pro virtuální počítače. Aktualizace jsou změny, jako jsou opravy hostitelské prostředí nebo upgradu a vyřazení z provozu hardwaru. Většina těchto aktualizací nemá jejich provedení žádný vliv na hostované virtuální počítače. Existují však případy, kdy aktualizace mít vliv:

- Pokud údržbu nevyžaduje restartování, Azure využívá místní migrace se pozastavit virtuální počítač během aktualizace hostitele. Tyto operace údržby bez rebootful jsou použité doména podle domény selhání a průběh je zastaven, pokud jsou přijímány všechny signály stavu upozornění.

- Údržba vyžaduje restartování, dostanete oznámení o při plánované údržby. V těchto případech budete mít časové okno, kde můžete začít údržbu sami, když se vám bude vyhovovat.


Plánovaná údržba, která vyžaduje restartování, je naplánováno ve vlnách. Každé vlně má jiný rozsah (oblasti).

- Vlnu začíná oznámení pro zákazníky. Ve výchozím nastavení odešle se oznámení na předplatné vlastník a spoluvlastníci. Můžete přidat další příjemce a možnosti zasílání zpráv, jako je e-mail, SMS a Webhooky, oznámení pomocí Azure [upozornění protokolu aktivit](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).  
- V době oznámení *samoobslužné služby okno* je k dispozici. Během tohoto časového období můžete vyhledat, které z vašich virtuálních počítačů jsou součástí této vlny a proaktivně spustit údržbu podle svých potřeb plánování.
- Po okno samoobslužných služeb *plánované časové období údržby* začíná. Azure někdy během intervalu plánuje a může požadovaná údržba se vztahuje na virtuální počítač. 

Cílem tím, že dvě okna je vám poskytnou dostatek času spuštění údržby a zároveň budete vědět, když Azure automaticky spustit údržbu restartování vašeho virtuálního počítače.


Můžete použít na webu Azure portal, Powershellu, rozhraní REST API a rozhraní příkazového řádku pro dotaz pro období údržby pro virtuální počítač škálovací nastaví virtuální počítače a spustí samoobslužné údržby.

  
## <a name="should-you-start-maintenance-during-the-self-service-window"></a>By měl spustit během časového intervalu samoobslužné údržby?  

Následující pokyny by vám pomohou určit, jestli se má použít tuto funkci a spustit údržbu na vlastní čas.

> [!NOTE] 
> Samoobslužné údržby nemusí být k dispozici pro všechny vaše virtuální počítače. Chcete-li zjistit, jestli proaktivní opětovného nasazení je k dispozici pro váš virtuální počítač, vyhledejte **spustit** ve stavu údržby. Samoobslužné údržby není aktuálně k dispozici pro cloudové služby (Web/Role pracovního procesu) a Service Fabric.


Nedoporučuje se používat samoobslužné údržby pro nasazení s využitím **dostupnosti** protože to jsou vysoce dostupné nastavení, kde je ovlivněné jenom jedna aktualizační doména v daném okamžiku. 

- Nechejte systém Azure aktivuje údržbu. Kvůli údržbě, která vyžaduje restartování mějte na paměti, že údržba bude provedeno aktualizační doména podle aktualizačních domén, že aktualizačních domén nebudou přijímat nutně údržbu postupně, a že je pozastavit víkend na 30minutové mezi aktualizační domény.
- Pokud k dočasné ztrátě některých vaší kapacity (počet domén/aktualizace 1) je důležité zajistit, jej lze snadno kompenzovat přidělením dalších instancí během doby údržby.
- Údržby, které nevyžaduje restartování, mají aktualizace použít na úrovni domény selhání. 
    
**Není** používat samoobslužné údržby v následujících scénářích: 

- Pokud vypnete virtuální počítače často, buď ručně, pomocí DevTest Labs, pomocí automatické vypnutí, nebo podle plánu, se může vrátit stav údržby a proto způsobí další prostoje.
- Na virtuálních počítačích krátkodobou znát bude odstraněn před koncem vlna údržby. 
- Pro úlohy s velké stavu uloženého v místní disk (dočasný), který je požadován udržovat při aktualizaci. 
- V případech, kde můžete změnit velikost virtuálního počítače často protože může vrátit zpět stav údržby. 
- Pokud si osvojila naplánované události, které umožňují 15 minut před začátkem údržby vypnutí proaktivní převzetí služeb při selhání nebo řádné vypnutí vašich úloh.

**Použití** samoobslužné údržby, pokud máte v úmyslu spustit virtuální počítač bez přerušení během fáze plánované údržby a žádné čítače označení uvedeného výše se vztahují. 

Je nejvhodnější použít samoobslužné údržby v následujících případech:

- Potřebujete pro komunikaci přesné časovém intervalu pro správu nebo koncových zákazníků. 
- Potřebujete k dokončení údržby podle daného data. 
- Je nutné určit pořadí údržby, třeba vícevrstvou aplikaci zajistit bezpečné obnovení.
- Potřebujete více než 30 minut od čas obnovení virtuálního počítače mezi dvěma aktualizační doména (ud). K řízení času mezi aktualizačních doménách, musí spustit údržbu na vaše virtuální počítače jedné aktualizační doméně (UD) současně.

 
## <a name="view-virtual-machine-scale-sets-impacted-by-maintenance-in-the-portal"></a>Zobrazit ovlivněné údržby na portálu pro škálovací sady virtuálních počítačů

Když je naplánováno vlny plánované údržby, můžete zobrazit seznam škálovacích sad virtuálních počítačů, které jsou ovlivněny vlna nadcházející údržby pomocí webu Azure portal. 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **všechny služby** na levé straně a zvolte **škálovací sady virtuálních počítačů**.
3. Na **škálovací sady virtuálních počítačů** zvolte **upravit sloupce** možnosti v horní části pro otevření seznamu dostupných sloupců.
4. V **část dostupných sloupců**, vyberte **samoobslužné údržby** položky a přesuňte ho do pomocí tlačítek šipku **vybrané sloupce** seznamu. Můžete přepnout rozevírací seznam v **dostupných sloupců** část **všechny** k **vlastnosti** provést **samoobslužné údržby** Položka snazší najít. Jakmile budete mít **samoobslužné údržby** položky v **vybrané sloupce** vyberte **použít** tlačítko v dolní části stránky. 

Po provedení kroků výše **samoobslužné údržby** sloupce se zobrazí v seznamu škálovací sady virtuálních počítačů. Každou škálovací sadu virtuálních počítačů může mít jednu z následujících hodnot ve sloupci samoobslužné údržby:

| Hodnota | Popis |
|-------|-------------|
| Ano | Nejméně jeden virtuální počítač ve škálovací sadě virtuálního počítače je v okně samoobslužné služby. Spustit údržbu kdykoli během tohoto časového intervalu pro samoobslužné služby. | 
| Ne | Nejsou žádné virtuální počítače, které jsou v okně samoobslužné služby v škálovací ovlivněných virtuálních počítačů nastavené. | 
| - | Škálovací sady virtuálních počítačů, které nejsou součástí vlny plánované údržby.| 

## <a name="notification-and-alerts-in-the-portal"></a>Oznámení a výstrahy na portálu

Azure komunikuje plánu kvůli plánované údržbě e-mailem do skupiny vlastník a spoluvlastníci předplatného. Můžete přidat další příjemce a kanály pro tuto komunikaci tak, že vytvoříte upozornění protokolu aktivit Azure. Další informace najdete v tématu [monitorování aktivit předplatného se protokol aktivit Azure] (.. / monitoring-and-diagnostics/monitoring-overview-activity-logs.md)

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. V nabídce na levé straně vyberte **monitorování**. 
3. V **monitorování – upozornění (klasická)** podokně klikněte na tlačítko **+ přidat upozornění protokolu aktivit**.
4. Zadejte údaje **přidat upozornění protokolu aktivit** stránce a ujistěte se, že jste nastavili následující v **kritéria**:
   - **Kategorie události**: stav služby
   - **Služby**: Škálovací sady virtuálních počítačů a virtuálních počítačů
   - **Typ**: plánovaná údržba 
    
Další informace o tom, jak nakonfigurovat upozornění protokolu aktivit najdete v tématu [vytvoření upozornění protokolu aktivit](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md)
    
    
## <a name="start-maintenance-on-your-virtual-machine-scale-set-from-the-portal"></a>Spustit údržbu na vaši škálovací sadu virtuálních počítačů z portálu

Při prohlížení přehled škálovacích sad virtuálních počítačů, budete moci zobrazit další údržby související podrobnosti. Pokud alespoň jeden virtuální počítač ve škálovací sadě virtuálního počítače je součástí vlny plánované údržby, přidá se nové oznámení pásu karet v horní části stránky. Můžete kliknout na přejděte na pásu karet oznámení **údržby** stránku, abyste viděli, která instance virtuálního počítače je ovlivněna plánované údržby. 

Odtud bude možné začít údržbu zaškrtnutím pole odpovídající ovlivněné virtuální počítač a potom kliknete na **spustit údržbu** možnost.

Po spuštění údržby ovlivněné virtuální počítače ve škálovací sadě virtuálního počítače se provést údržbu a dočasně nedostupný. Pokud jste okno samoobslužné služby, budete moct dál najdete v okně, když se zachová svou škálovací sadu virtuálních počítačů Azure.
 
## <a name="check-maintenance-status-using-powershell"></a>Kontrola stavu údržby pomocí Powershellu

Prostředí Azure Powershell můžete zjistit, kdy jsou virtuální počítače ve škálovací sadě virtuálních počítačů plánu údržby. Informace o plánované údržbě je k dispozici [Get-AzureRmVmss](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss) rutiny při použití `-InstanceView` parametru.
 
Informace o údržbě je vrácen pouze v případě, že se plánované údržby. Pokud žádná údržba je naplánována tak, že má vliv na instanci virtuálního počítače, rutina nevrací žádné informace o údržbě. 

```powershell
Get-AzureRmVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -InstanceView
```

V části MaintenanceRedeployStatus se vrátí následující vlastnosti: 
| Hodnota | Popis   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Určuje, zda lze spustit údržbu na virtuálním počítači v tuto chvíli ||
| PreMaintenanceWindowStartTime         | Začátek samoobslužné časové období údržby při údržby můžete spustit na virtuálním počítači ||
| PreMaintenanceWindowEndTime           | Konec samoobslužné časové období údržby při údržby můžete spustit na virtuálním počítači ||
| MaintenanceWindowStartTime            | Na začátek plánované údržby, ve kterém Azure zahájí Údržba na vašem virtuálním počítači ||
| MaintenanceWindowEndTime              | Konci okna plánované údržby, ve kterém Azure zahájí Údržba na vašem virtuálním počítači ||
| LastOperationResultCode               | Výsledek posledního pokusu o zahájení údržby ve virtuálním počítači ||



### <a name="start-maintenance-on-your-vm-instance-using-powershell"></a>Spustit údržbu na instanci virtuálního počítače pomocí Powershellu

Můžete spustit údržbu na virtuálním počítači, pokud **IsCustomerInitiatedMaintenanceAllowed** nastavena na hodnotu true pomocí [Set-AzureRmVmss](/powershell/module/azurerm.compute/set-azurermvmss) rutinu s `-PerformMaintenance` parametru.

```powershell
Set-AzureRmVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -PerformMaintenance 
```

## <a name="check-maintenance-status-using-cli"></a>Kontrola stavu údržby pomocí CLI

Informace o plánované údržbě můžete zobrazit pomocí [az vmss list-instances](/cli/azure/vmss?view=azure-cli-latest#az-vmss-list-instances).
 
Informace o údržbě je vrácen pouze v případě, že se plánované údržby. Pokud není naplánovaná žádná Údržba, která má vliv na instanci virtuálního počítače, příkaz nevrací žádné informace o údržbě. 

```azure-cli
az vmss list-instances -g rgName -n vmssName --expand instanceView
```

Následující vlastnosti jsou vráceny v rámci MaintenanceRedeployStatus pro všechny instance virtuálních počítačů: 
| Hodnota | Popis   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Určuje, zda lze spustit údržbu na virtuálním počítači v tuto chvíli ||
| PreMaintenanceWindowStartTime         | Začátek samoobslužné časové období údržby při údržby můžete spustit na virtuálním počítači ||
| PreMaintenanceWindowEndTime           | Konec samoobslužné časové období údržby při údržby můžete spustit na virtuálním počítači ||
| MaintenanceWindowStartTime            | Na začátek plánované údržby, ve kterém Azure zahájí Údržba na vašem virtuálním počítači ||
| MaintenanceWindowEndTime              | Konci okna plánované údržby, ve kterém Azure zahájí Údržba na vašem virtuálním počítači ||
| LastOperationResultCode               | Výsledek posledního pokusu o zahájení údržby ve virtuálním počítači ||


### <a name="start-maintenance-on-your-vm-instance-using-cli"></a>Spustit údržbu na instanci virtuálního počítače pomocí rozhraní příkazového řádku

Následující volání zahájí Údržba na instancích virtuálních počítačů, pokud `IsCustomerInitiatedMaintenanceAllowed` je nastavena na hodnotu true.

```azure-cli
az vmss perform-maintenance -g rgName -n vmssName --instance-ids id
```

## <a name="faq"></a>Nejčastější dotazy


**Otázka: Proč musí být virtuální počítače restartovat?**

**Odpověď:** zatímco většina aktualizací a upgradů platformy Azure nemají vliv na dostupnost virtuálního počítače, existují případy, kdy Nedokážeme vyhnout restartování virtuálních počítačů hostovaných v Azure. Jsme nashromáždili několik změn, které vyžadují restartování našich serverů, které způsobí restartování virtuálních počítačů.

**Otázka: Pokud sleduji svá doporučení pro zajištění vysoké dostupnosti s využitím skupinu dostupnosti, mám bezpečný?**

**Odpověď:** nastavení virtuálních počítačů nasazených ve skupině dostupnosti nebo škálovací sady virtuálních počítačů mají pojem aktualizační domény (UD). Při provádění údržby Azure dodržuje omezení UD a nerestartuje virtuální počítače z různých UD (v rámci stejné skupiny dostupnosti).  Azure také čeká aspoň 30 minut před přechodem na další skupinu virtuálních počítačů. 

Další informace o vysoké dostupnosti najdete v tématu [oblasti a dostupnost virtuálních počítačů v Azure](../virtual-machines/windows/regions-and-availability.md).

**Otázka: Jak mi oznámení o plánované údržbě?**

**Odpověď:** nastavením plánu na jeden nebo více oblastech Azure začíná vlny plánované údržby. Krátce po e-mailové oznámení, přijde vlastníkům předplatného (jeden e-mail na jedno předplatné). Další kanály a příjemců tohoto oznámení by mohla být nakonfigurovaný pomocí upozornění protokolu aktivit. V případě, že nasazujete virtuální počítač do oblasti, ve kterém je již naplánována plánované údržby, nebude dostávat oznámení ale místo toho budete muset zkontrolovat stav údržby virtuálního počítače.

**Otázka: nemůžu nezobrazuje žádný indikátor plánované údržby na portálu, Powershellu nebo rozhraní příkazového řádku. Co je?**

**Odpověď:** informace týkající se plánované údržby je dostupná během vlny plánované údržby pouze pro virtuální počítače, které budou mít vliv. Jinými slovy Pokud se zobrazí ne data, je možné, že vlna údržby má již byla dokončena (nebo nebyla spuštěna) nebo, že váš virtuální počítač už hostuje na aktualizovaném serveru.

**Otázka: existuje způsob, jak přesně zjistit, kdy ovlivní Můj virtuální počítač?**

**Odpověď:** při nastavování plánu, definujeme časovým intervalem několik dní. Přesné pořadí serverů (a virtuálních počítačů) v rámci tohoto časového období však není znám. Zákazníci, kteří by chtěli zjistit přesný čas pro své virtuální počítače můžete použít [naplánované události](../virtual-machines/windows/scheduled-events.md) dotaz z v rámci virtuálního počítače a dostanete oznámení 15 minut před restartováním virtuálního počítače.

**Otázka: jak dlouho bude trvat restartování mého virtuálního počítače?**

**Odpověď:** v závislosti na velikosti virtuálního počítače, restartování může trvat několik minut, během časového intervalu samoobslužné údržby. Během Azure inicioval restartování počítače v plánované časové období údržby, bude trvat restartování obvykle přibližně 25 minut. Všimněte si, že v případě, že používáte (Web/Role pracovního procesu) Cloud Services, Virtual Machine Scale Sets nebo skupiny dostupnosti, budete mít 30 minut mezi každou skupinu z virtuálních počítačů (UD) během plánované časové období údržby. 

**D: nevidím žádné informace o údržbě na svoje virtuální počítače. K jakému?**

**Odpověď:** tady je několik důvodů, proč nevidíte žádné informace o údržby na virtuálních počítačích:
   - Používáte předplatné označené jako Microsoft interní.
   - Nejsou vaše virtuální počítače s naplánovanou údržbou. Je možné, že skončila vlna údržby, zrušené nebo upraví tak, aby vaše virtuální počítače se eliminuje dopad ho.
   - Nemáte k dispozici **údržby** sloupec přidali do zobrazení seznamu virtuálních počítačů. Když jsme tento sloupec přidali do výchozího zobrazení, třeba ručně přidat zákazníky, kteří nakonfigurovali zobrazíte nevýchozí sloupce **údržby** sloupec k jejich zobrazení seznamu virtuálních počítačů.

**Otázka: Můj virtuální počítač je naplánovaná údržba podruhé. Proč?**

**Odpověď:** existuje několik případů použití, kde se zobrazí poté, co jste už dokončili Údržba-opětovné nasazení virtuálního počítače:
   - Jsme zrušena vlna údržby a restartuje s jinou datovou částí. Je možné, že jsme zjistili chybnou datové části a potřebujeme jen nasadit další datové části.
   - Virtuální počítač byl *službami opraven* do jiného uzlu z důvodu selhání hardwaru.
   - Vybrali jste zastavení (uvolnění) a restartujte virtuální počítač.
   - Máte **automatické vypnutí** zapnuta pro daný virtuální počítač.

## <a name="next-steps"></a>Další postup

Zjistěte, jak si zaregistrovat události údržby z v rámci virtuálního počítače pomocí [Scheduled Events](../virtual-machines/windows/scheduled-events.md).

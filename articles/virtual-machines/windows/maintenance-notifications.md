---
title: Zpracování oznámení o údržbě pro virtuální počítače s Windows v Azure | Dokumentace Microsoftu
description: Zobrazit oznámení o údržbě pro virtuální počítače s Windows v Azure a začněte samoobslužné údržby.
services: virtual-machines-windows
documentationcenter: ''
author: shants123
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/02/2018
ms.author: shants
ms.openlocfilehash: 7c391e84f335e013ce1914063ccec75ba20f8685
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55980087"
---
# <a name="handling-planned-maintenance-notifications-for-windows-virtual-machines"></a>Zpracování oznámení plánované údržby pro virtuální počítače s Windows

Azure pravidelně provádí aktualizace za účelem zlepšení spolehlivosti, výkonu a zabezpečení hostitelské infrastruktury pro virtuální počítače. Aktualizace jsou změny, jako jsou opravy hostitelské prostředí nebo upgradu a vyřazení z provozu hardwaru. Většina těchto aktualizací nemá jejich provedení žádný vliv na hostované virtuální počítače. Existují však případy, kdy aktualizace mít vliv:

- Pokud údržbu nevyžaduje restartování, Azure využívá místní migrace se pozastavit virtuální počítač během aktualizace hostitele. Tyto operace údržby bez rebootful jsou použité doména podle domény selhání a průběh je zastaven, pokud jsou přijímány všechny signály stavu upozornění. 

- Údržba vyžaduje restartování, dostanete oznámení o při plánované údržby. V těchto případech budete mít časové okno, kde můžete začít údržbu sami, když se vám bude vyhovovat.


Plánovaná údržba, která vyžaduje restartování, je naplánováno ve vlnách. Každé vlně má jiný rozsah (oblasti).

- Vlnu začíná oznámení pro zákazníky. Ve výchozím nastavení odešle se oznámení na předplatné vlastník a spoluvlastníci. Můžete přidat další příjemce a možnosti zasílání zpráv, jako je e-mail, SMS a Webhooky, oznámení pomocí Azure [upozornění protokolu aktivit](../../azure-monitor/platform/activity-logs-overview.md).  
- V době oznámení *samoobslužné služby okno* je k dispozici. Během tohoto časového období můžete vyhledat, které z vašich virtuálních počítačů jsou součástí této vlny a proaktivně spustit údržbu podle svých potřeb plánování.
- Po okno samoobslužných služeb *plánované časové období údržby* začíná. Azure někdy během intervalu plánuje a může požadovaná údržba se vztahuje na virtuální počítač. 

Cílem tím, že dvě okna je vám poskytnou dostatek času spuštění údržby a zároveň budete vědět, když Azure automaticky spustit údržbu restartování vašeho virtuálního počítače.


Dotaz pro období údržby pro virtuální počítače a spusťte samoobslužné údržby můžete pomocí webu Azure portal, Powershellu, rozhraní REST API a rozhraní příkazového řádku.

  
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
- Pokud si osvojila naplánované události, které umožňují proaktivní převzetí služeb při selhání nebo řádné vypnutí vašich úloh 15 minut před začátkem údržby vypnutí

**Použití** samoobslužné údržby, pokud máte v úmyslu spustit virtuální počítač bez přerušení během fáze plánované údržby a žádné čítače označení uvedeného výše se vztahují. 

Je nejvhodnější použít samoobslužné údržby v následujících případech:

- Potřebujete pro komunikaci přesné časovém intervalu pro správu nebo koncových zákazníků. 
- Potřebujete k dokončení údržby podle daného data. 
- Je nutné určit pořadí údržby, třeba vícevrstvou aplikaci zajistit bezpečné obnovení.
- Potřebujete více než 30 minut od čas obnovení virtuálního počítače mezi dvěma aktualizační doména (ud). K řízení času mezi aktualizačních doménách, musí spustit údržbu na vaše virtuální počítače jedné aktualizační doméně (UD) současně.

 

[!INCLUDE [virtual-machines-common-maintenance-notifications](../../../includes/virtual-machines-common-maintenance-notifications.md)]

## <a name="check-maintenance-status-using-powershell"></a>Kontrola stavu údržby pomocí Powershellu

Můžete také pomocí Azure Powershellu a zjistěte, kdy jsou virtuální počítače s naplánovanou údržbou. Informace o plánované údržbě je k dispozici [rutiny Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) rutiny při použití `-status` parametru.
 
Informace o údržbě je vrácen pouze v případě, že se plánované údržby. Pokud žádná údržba je naplánována tak, že má vliv na virtuální počítač, rutina nevrací žádné informace o údržbě. 

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

```powershell
Get-AzVM -ResourceGroupName rgName -Name vmName -Status
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



Můžete také získat stav údržby pro všechny virtuální počítače ve skupině prostředků s použitím [rutiny Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) a bez zadání virtuálního počítače.
 
```powershell
Get-AzVM -ResourceGroupName rgName -Status
```

Následující funkce Powershellu přijímá ID vašeho předplatného a vytiskne seznam virtuálních počítačů, které jsou plánovány pro údržbu.

```powershell

function MaintenanceIterator
{
    Select-AzSubscription -SubscriptionId $args[0]

    $rgList= Get-AzResourceGroup 

    for ($rgIdx=0; $rgIdx -lt $rgList.Length ; $rgIdx++)
    {
        $rg = $rgList[$rgIdx]        
    $vmList = Get-AzVM -ResourceGroupName $rg.ResourceGroupName 
        for ($vmIdx=0; $vmIdx -lt $vmList.Length ; $vmIdx++)
        {
            $vm = $vmList[$vmIdx]
            $vmDetails = Get-AzVM -ResourceGroupName $rg.ResourceGroupName -Name $vm.Name -Status
              if ($vmDetails.MaintenanceRedeployStatus )
            {
                Write-Output "VM: $($vmDetails.Name)  IsCustomerInitiatedMaintenanceAllowed: $($vmDetails.MaintenanceRedeployStatus.IsCustomerInitiatedMaintenanceAllowed) $($vmDetails.MaintenanceRedeployStatus.LastOperationMessage)"               
            }
          }
    }
}

```

### <a name="start-maintenance-on-your-vm-using-powershell"></a>Spustit údržbu na virtuálním počítači pomocí Powershellu

Pomocí informací z funkce v předchozí části, následující začne Údržba na virtuálním počítači Pokud **IsCustomerInitiatedMaintenanceAllowed** je nastavena na hodnotu true.

```powershell
Restart-AzVM -PerformMaintenance -name $vm.Name -ResourceGroupName $rg.ResourceGroupName 
```

## <a name="classic-deployments"></a>Nasazení Classic

Pokud máte starší verze virtuálních počítačů, které byly nasazené pomocí modelu nasazení classic, je použití Powershellu k dotazování služby pro virtuální počítače a zahájení údržby.

Pokud chcete získat stav údržby virtuálního počítače, zadejte:

```
Get-AzureVM -ServiceName <Service name> -Name <VM name>
```

Pokud chcete spustit údržbu na klasickém virtuálním počítači, zadejte:

```
Restart-AzureVM -InitiateMaintenance -ServiceName <service name> -Name <VM name>
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
2.  Virtuální počítač byl *službami opraven* do jiného uzlu kvůli hardwarové chybě.
3.  Vybrali jste zastavení (uvolnění) a restartujte virtuální počítač
4.  Máte **automatické vypnutí** zapnuta pro daný virtuální počítač


## <a name="next-steps"></a>Další postup

Zjistěte, jak si zaregistrovat události údržby z v rámci virtuálního počítače pomocí [Scheduled Events](scheduled-events.md).

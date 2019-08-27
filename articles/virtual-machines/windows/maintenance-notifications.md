---
title: Zpracování oznámení údržby pro virtuální počítače s Windows v Azure | Microsoft Docs
description: Zobrazte si oznámení o údržbě virtuálních počítačů s Windows, které běží v Azure, a spusťte samoobslužnou údržbu.
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
ms.date: 04/30/2019
ms.author: shants
ms.openlocfilehash: 4c8e17cec4ff3595945225dbceb909f8f054a08b
ms.sourcegitcommit: 3f78a6ffee0b83788d554959db7efc5d00130376
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/26/2019
ms.locfileid: "70018865"
---
# <a name="handling-planned-maintenance-notifications-for-windows-virtual-machines"></a>Zpracování oznámení o plánované údržbě pro virtuální počítače s Windows

Azure pravidelně provádí aktualizace za účelem zlepšení spolehlivosti, výkonu a zabezpečení hostitelské infrastruktury pro virtuální počítače. Aktualizace jsou změny, jako je třeba oprava hostitelského prostředí nebo inovace a vyřazení hardwaru z provozu. Většina těchto aktualizací se provádí bez jakéhokoli dopadu na hostované virtuální počítače. Existují však případy, kdy aktualizace mají dopad:

- Pokud údržba nevyžaduje restart, Azure pomocí místní migrace pozastaví virtuální počítač v době, kdy se hostitel aktualizuje. Tyto operace údržby, které nejsou restartováním, používají doménu selhání podle domény selhání a průběh se zastaví, pokud dojde k přijetí jakýchkoli signálů stavu upozornění. 

- Pokud údržba vyžaduje restart, dostanete oznámení o tom, kdy se údržba plánuje. V těchto případech máte časový interval, který je obvykle 35 dní, kdy je možné zahájit údržbu sami, když to bude fungovat.


Plánovaná údržba, která vyžaduje restart, je naplánována na vlny. Každý vlna má jiný obor (oblasti).

- Wave začíná oznámením pro zákazníky. Ve výchozím nastavení se oznámení pošle vlastníkovi předplatného a spoluvlastníkům. K oznámením pomocí [výstrah protokolu aktivit](../../azure-monitor/platform/activity-logs-overview.md)Azure můžete přidat další příjemce a možnosti zasílání zpráv, jako jsou E-mail, SMS a Webhooky.  
- V okamžiku oznámení se zpřístupní *samoobslužné okno* . Během tohoto okna, které je obvykle 35 dní, můžete zjistit, které z vašich virtuálních počítačů jsou součástí tohoto Wave a proaktivně zahájit údržbu podle vašich potřeb plánování.
- Po samoobslužném okně začne *plánované časové období údržby* . V určitém okamžiku v tomto okně Azure plánuje a na virtuálním počítači aplikuje požadovanou údržbu. 

Cílem v aplikaci je mít dvě okna, abyste měli dostatek času na spuštění údržby a restartování virtuálního počítače s vědomím, kdy bude Azure automaticky spouštět údržbu.


K dotazování na časová období údržby pro vaše virtuální počítače můžete použít Azure Portal, PowerShell, REST API a CLI a spustit samoobslužnou údržbu.

  
## <a name="should-you-start-maintenance-during-the-self-service-window"></a>Měli byste začít s údržbou během samoobslužného okna?  

Následující pokyny vám pomohou rozhodnout, zda tuto možnost použít, a spustit údržbu ve vašem čase.

> [!NOTE] 
> Samoobslužná údržba nemusí být k dispozici pro všechny vaše virtuální počítače. Pokud chcete zjistit, jestli je pro váš virtuální počítač k dispozici proaktivní opětovné nasazení, vyhledejte **Spustit nyní** ve stavu údržby. Samoobslužná údržba není v současnosti dostupná pro Cloud Services (Web/role pracovního procesu) a Service Fabric.


Samoobslužná údržba se nedoporučuje pro nasazení pomocí **skupin dostupnosti** , protože se jedná o nastavení s vysokou dostupností, kde je v daném okamžiku ovlivněná jenom jedna aktualizační doména. 
- Umožněte službě Azure aktivovat údržbu. V případě údržby, která vyžaduje restart, je třeba mít na paměti, že údržba bude dokončena aktualizace domény podle aktualizačních domén, že domény aktualizací nutně nezískají průběžnou údržbu a že mezi aktualizačními doménami probíhá prodleva 30 minut. 
- Pokud je k dispozici Dočasná ztráta některé z vaší kapacity (1 nebo aktualizace počtu domén), je možné ji snadno kompenzovat přidělením dalších instancí během období údržby.
- V případě údržby, která nevyžaduje restart, se aktualizace aplikují na úrovni domény selhání.
    
Nepoužívejte samoobslužnou údržbu v následujících scénářích: 
- Pokud jste virtuální počítače často vypnuli ručně, pomocí DevTest Labs, pomocí automatického vypnutí nebo podle plánu, může dojít k vrácení stavu údržby, takže dojde k dalšímu výpadku.
- V případě krátkodobých virtuálních počítačů, o kterých víte, že se odstraní před koncem vlny údržby. 
- Pro úlohy s velkým stavem uloženým na místním (dočasném) disku, který je potřeba udržovat při aktualizaci. 
- Pro případy, kdy se virtuální počítač často mění, protože by mohl vrátit stav údržby.
- Pokud jste přijali naplánované události, které umožní proaktivní převzetí služeb při selhání nebo řádné vypnutí úlohy, 15 minut před vypnutím údržby

Samoobslužná údržba – Pokud plánujete, že váš virtuální počítač bude během plánované fáze údržby nepřerušený, a žádná z výše uvedených údajů není platná. 

Služba samoobslužná údržba se doporučuje používat v následujících případech:

- Pro správu nebo koncového zákazníka musíte sdělit přesné časové období údržby. 
- Je potřeba dokončit údržbu do daného data. 
- Musíte řídit sekvencování údržby, například vícevrstvé aplikace pro zajištění bezpečného obnovení.
- Pro dobu obnovení virtuálních počítačů mezi dvěma aktualizačními doménami potřebujete víc než 30 minut. Chcete-li řídit dobu mezi aktualizačními doménami, je nutné aktivovat údržbu v rámci virtuálních počítačů po jedné aktualizační doméně (UD).

 

[!INCLUDE [virtual-machines-common-maintenance-notifications](../../../includes/virtual-machines-common-maintenance-notifications.md)]

## <a name="check-maintenance-status-using-powershell"></a>Ověření stavu údržby pomocí PowerShellu

Pomocí Azure PowerShellu taky můžete zjistit, kdy se virtuální počítače naplánovaly na údržbu. Informace o plánované údržbě jsou k dispozici pomocí rutiny [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) , když použijete `-status` parametr.
 
Informace o údržbě jsou vráceny pouze v případě, že je naplánována údržba. Pokud není naplánována žádná údržba, která by měla vliv na virtuální počítač, rutina nevrátí žádné informace o údržbě. 

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

```powershell
Get-AzVM -ResourceGroupName rgName -Name vmName -Status
```

V MaintenanceRedeployStatus se vrátí následující vlastnosti: 

| Value | Popis   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Určuje, jestli můžete v tomto okamžiku spustit údržbu virtuálního počítače. |
| PreMaintenanceWindowStartTime         | Začátek samoobslužného okna údržby, když můžete na svém VIRTUÁLNÍm počítači iniciovat údržbu |
| PreMaintenanceWindowEndTime           | Konec samoobslužného okna údržby, když můžete na svém VIRTUÁLNÍm počítači iniciovat údržbu |
| MaintenanceWindowStartTime            | Začátek údržby naplánovaný při zahájení údržby virtuálního počítače v Azure |
| MaintenanceWindowEndTime              | Konec okna naplánované údržby, ve kterém Azure iniciuje údržbu na vašem VIRTUÁLNÍm počítači |
| LastOperationResultCode               | Výsledek posledního pokusu o spuštění údržby virtuálního počítače |



Stav údržby pro všechny virtuální počítače ve skupině prostředků můžete také získat pomocí [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) a nespecifikovat virtuální počítač.
 
```powershell
Get-AzVM -ResourceGroupName rgName -Status
```

Následující funkce PowerShellu převezme vaše ID předplatného a vytiskne seznam virtuálních počítačů, u kterých je naplánovaná údržba.

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

### <a name="start-maintenance-on-your-vm-using-powershell"></a>Spuštění údržby virtuálního počítače pomocí PowerShellu

Při použití informací z funkce v předchozí části spustí následující údržba virtuálního počítače, pokud je **IsCustomerInitiatedMaintenanceAllowed** nastavené na true.

```powershell
Restart-AzVM -PerformMaintenance -name $vm.Name -ResourceGroupName $rg.ResourceGroupName 
```

## <a name="classic-deployments"></a>Nasazení Classic

Pokud stále máte starší virtuální počítače nasazené pomocí modelu nasazení Classic, můžete použít PowerShell k dotazování na virtuální počítače a zahájit údržbu.

Stav údržby virtuálního počítače získáte tak, že zadáte:

```
Get-AzureVM -ServiceName <Service name> -Name <VM name>
```

Pokud chcete spustit údržbu klasického virtuálního počítače, zadejte:

```
Restart-AzureVM -InitiateMaintenance -ServiceName <service name> -Name <VM name>
```


## <a name="faq"></a>Nejčastější dotazy


**Otázka: Proč je potřeba restartovat svoje virtuální počítače hned?**

**Odpověď:** I když většina aktualizací a upgradů na platformu Azure nemá vliv na dostupnost virtuálního počítače, v některých případech se nemůžeme vyhnout restartování virtuálních počítačů hostovaných v Azure. Shromáždili jsme několik změn, které vyžadují restart našich serverů, které způsobí restartování virtuálních počítačů.

**Otázka: Pokud vystupujem podle doporučení pro vysokou dostupnost pomocí skupiny dostupnosti, je I bezpečné?**

**Odpověď:** Virtuální počítače nasazené ve skupině dostupnosti nebo škálovací sadě virtuálních počítačů rozeznávají aktualizační domény (UD). Při provádění údržby Azure respektuje omezení UD a nerestartuje virtuální počítače z různých UD (v rámci stejné skupiny dostupnosti).  Azure také před přechodem na další skupinu virtuálních počítačů počká aspoň 30 minut. 

Další informace o vysoké dostupnosti najdete v tématu [dostupnost pro virtuální počítače v Azure](availability.MD).

**Otázka: Návody dostávat oznámení o plánované údržbě?**

**Odpověď:** Spustí se naplánovaná údržba Wave tím, že nastaví plán na jednu nebo více oblastí Azure. Brzy se pošle e-mailové oznámení vlastníkům předplatného (jeden e-mail na předplatné). Další kanály a příjemci pro toto oznámení můžou být nakonfigurované pomocí upozornění protokolu aktivit. V případě, že nasadíte virtuální počítač do oblasti, ve které je plánovaná údržba již naplánována, nebudete dostávat oznámení, ale budete muset kontrolovat stav údržby virtuálního počítače.

**Otázka: V portálu, PowerShellu nebo rozhraní příkazového řádku se nezobrazují žádné informace o plánované údržbě. Co je?**

**Odpověď:** Informace týkající se plánované údržby jsou k dispozici během plánované údržby, pouze pro virtuální počítače, na které bude mít vliv. Jinými slovy, Pokud nevidíte data, může to být tím, že už je dokončená údržba nebo že váš virtuální počítač je už hostovaný na aktualizovaném serveru.

**Otázka: Existuje způsob, jak přesně zjistit, kdy to bude mít dopad na můj virtuální počítač?**

**Odpověď:** Při nastavování plánu definujeme časový interval několik dní. Přesné pořadí serverů (a virtuálních počítačů) v rámci tohoto okna však není známé. Zákazníci, kteří chtějí znát přesný čas pro své virtuální počítače, můžou použít [naplánované události](scheduled-events.md) a dotaz z virtuálního počítače a získat oznámení o 15 minutách před RESTARTOVÁNÍM virtuálního počítače.

**Otázka: Jak dlouho bude trvat restartování virtuálního počítače?**

**Odpověď:**  V závislosti na velikosti virtuálního počítače může restartování trvat až několik minut, než se okno samoobslužné údržby. Během restartování za provozu v systému Azure v okně naplánované údržby bude obvykle trvat přibližně 25 minut. Pamatujte na to, že pokud použijete Cloud Services (Web/role pracovního procesu), Virtual Machine Scale Sets nebo sady dostupnosti, budete během naplánovaného časového období údržby přihlášeni za 30 minut mezi jednotlivými skupinami virtuálních počítačů (UD). 

**Otázka: Jaké jsou možnosti v případě Virtual Machine Scale Sets?**

**Odpověď:** Pro Virtual Machine Scale Sets je nyní k dispozici plánovaná údržba. Pokyny k zahájení samoobslužné údržby najdete v dokumentu o [plánované údržbě pro VMSS](../../virtual-machine-scale-sets/virtual-machine-scale-sets-maintenance-notifications.md) .

**Otázka: Jaké jsou možnosti v případě Cloud Services (Web/role pracovního procesu) a Service Fabric?**

**Odpověď:** Přestože se plánovaná údržba těchto platforem týká, u zákazníků, kteří je využívají, se nepředpokládá žádný problém vzhledem k tomu, že v každém okamžiku bude ovlivňovat jenom virtuální počítače v jedné aktualizační doméně (UD). Samoobslužná údržba není v současnosti dostupná pro Cloud Services (Web/role pracovního procesu) a Service Fabric.

**Otázka: Na virtuálních počítačích nevidím žádné informace o údržbě. Co se pokazilo?**

**Odpověď:** Existuje několik důvodů, proč se na virtuálních počítačích nezobrazuje žádné informace o údržbě:
1.  Používáte odběr označený jako interní Microsoft.
2.  Pro vaše virtuální počítače není naplánovaná údržba. Může to být tím, že se vlna údržby ukončil, zrušil nebo upravil, aby na vaše virtuální počítače už neovlivnila žádná z nich.
3.  Do zobrazení seznamu virtuálních počítačů nemáte přidaný sloupec **Údržba** . I když jsme tento sloupec přidali do výchozího zobrazení, zákazníci, kteří si nakonfigurovali, aby viděli jiné než výchozí sloupce, musí do zobrazení seznamu virtuálních počítačů ručně přidat sloupec **Údržba** .

**Otázka: Pro tento virtuální počítač se naplánuje údržba podruhé. Proč?**

**Odpověď:** Existuje několik případů použití, ve kterých se pro virtuální počítač znovu naplánuje údržba bezprostředně poté, co se dokončila:
1.  Zrušili jste vlnu údržby a restartovali ji s jinou datovou částí. Je možné, že jsme zjistili chybnou datovou část a jednoduše potřebujete nasadit další datovou část.
2.  Váš virtuální počítač byl *službou* zacelený na jiný uzel kvůli hardwarové chybě.
3.  Zvolili jste zastavení (zrušení přidělení) a restartování virtuálního počítače.
4.  Pro virtuální počítač je zapnuté **Automatické vypnutí**


## <a name="next-steps"></a>Další postup

Přečtěte si, jak můžete registrovat události údržby z virtuálního počítače pomocí [Scheduled Events](scheduled-events.md).

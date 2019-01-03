---
title: Oznámení o údržbě pro virtuální počítač škálovací sady v Azure | Dokumentace Microsoftu
description: Zobrazení oznámení o údržbě a spustit samoobslužné údržby pro škálovací sady virtuálních počítačů v Azure.
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
ms.openlocfilehash: 727ae9bbea4cabc5d27c32baff2123a7c03b531c
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53546857"
---
# <a name="planned-maintenance-notifications-for-virtual-machine-scale-sets"></a>Oznámení o plánované údržbě pro škálovací sady virtuálních počítačů

Azure provádí pravidelné aktualizace zvýšit spolehlivost, výkon a zabezpečení hostitelské infrastruktury pro virtuální počítače (VM). Aktualizace může zahrnovat opravy hostitelské prostředí nebo upgradu a vyřazení z provozu hardwaru. Většinu aktualizací nemají vliv na hostované virtuální počítače. Nicméně aktualizace ovlivní virtuální počítače v těchto scénářích:

- Pokud údržbu nevyžaduje restartování, Azure využívá místní migrace se pozastavit virtuální počítač během aktualizace hostitele. Operace údržby, které nevyžadují restartování jsou použité doména podle domény selhání. Průběh je zastaven, pokud jsou přijímány všechny signály stavu upozornění.

- Údržba vyžaduje restartování, obdržíte oznámení, který zobrazuje, když je naplánovaná údržba. V těchto případech budete mít časový interval, ve které můžete spustit údržbu sami když ji vám bude nejlépe vyhovovat.


Plánované údržby, které vyžaduje restartování počítače je naplánováno ve vlnách. Každé vlně má jiný rozsah (oblasti):

- Vlnu začíná oznámení pro zákazníky. Ve výchozím nastavení odešle se oznámení na předplatné vlastník a spoluvlastníci. Můžete přidat příjemce a možnosti zasílání zpráv, jako je e-mail, SMS a webhooky pro oznámení pomocí služby Azure [upozornění protokolu aktivit](../azure-monitor/platform/activity-logs-overview.md).  
- S oznámením *samoobslužné služby okno* je k dispozici. Během tohoto časového období zjistíte, které vaše virtuální počítače jsou uvedeny ve zprávě the wave. Podle svých potřeb plánování můžete aktivně začít s údržbou.
- Po okno samoobslužných služeb *plánované časové období údržby* začíná. Azure někdy během intervalu plánuje a může požadovaná údržba se vztahuje k vašemu virtuálnímu počítači. 

Cílem tím, že dvě okna je vám poskytnou dostatek času na spuštění údržby a zároveň budete vědět, když Azure automaticky spustit údržbu restartu virtuálního počítače.


Na webu Azure portal, Powershellu, rozhraní REST API a rozhraní příkazového řádku Azure můžete použít k dotazování pro správu a údržbu pro váš virtuální počítač škálovací sadu virtuálních počítačů a provádět samoobslužné údržby.

  
## <a name="should-you-start-maintenance-during-the-self-service-window"></a>By měl spustit během časového intervalu samoobslužné údržby?  

Následující pokyny vám mohou pomoci při rozhodování, jestli se má spustit údržbu v čase, který zvolíte.

> [!NOTE] 
> Samoobslužné údržby nemusí být k dispozici pro všechny vaše virtuální počítače. Chcete-li zjistit, zda je k dispozici pro váš virtuální počítač proaktivní znovu nasaďte, vyhledejte **spustit** ve stavu údržby. V současné době není k dispozici pro Azure Cloud Services (Role Web/Worker) a Azure Service Fabric samoobslužné údržby.


Nedoporučuje se používat samoobslužné údržby pro nasazení, která používají *dostupnosti*. Skupiny dostupnosti jsou vysoce dostupná nastavení v které jenom jedna aktualizační doména má vliv kdykoli. Pro skupiny dostupnosti:

- Nechejte systém Azure aktivuje údržbu. Kvůli údržbě, která vyžaduje restartování údržba se provádí aktualizační doména podle aktualizačních domén. Aktualizační domény nepřijímají nutně údržbu postupně. Není k dispozici víkend na 30minutové pozastavení mezi aktualizační domény.
- Pokud k dočasné ztrátě některých vaší kapacity (počet domén/aktualizace 1) je důležité, můžete snadno kompenzovat ztrátu přidělením dalších instancí během doby údržby.
- Kvůli údržbě, který nevyžaduje restartování se aktualizace na úrovni domény selhání. 
    
**Není** používat samoobslužné údržby v následujících scénářích: 

- Pokud vypnete virtuální počítače často, buď ručně, pomocí DevTest Labs, pomocí automatické vypnutí nebo podle plánu. Samoobslužné údržby v těchto scénářích může vrátit stav údržby a způsobit, že další prostoje.
- Na virtuálních počítačích krátkodobou znát bude odstraněn před koncem vlna údržby. 
- Pro úlohy se stavem velké uložené na místním disku (dočasný), která chcete zachovat po aktualizaci. 
- Pokud změníte velikost virtuálního počítače často. Tento scénář může vrátit stav údržby. 
- Pokud si osvojila naplánované události, které umožňují 15 minut, než začne Údržba vypnutí proaktivní převzetí služeb při selhání nebo řádné vypnutí vašich úloh.

**Proveďte** používat samoobslužné údržby, pokud máte v plánu pro spuštění virtuálního počítače během fáze plánované údržby bez přerušení a předchozí counterindications nepoužijí. 

Je nejvhodnější použít samoobslužné údržby v následujících případech:

- Budete muset komunikaci přesné údržbu pro správu nebo vašich zákazníků. 
- Potřebujete k dokončení údržby podle konkrétního data. 
- Je potřeba řídit pořadí údržby, třeba ve vícevrstvé aplikace, k zajištění bezpečného obnovení.
- Potřebujete více než 30 minut od čas obnovení virtuálního počítače mezi dvě aktualizační domény. K řízení času mezi aktualizační domény, musí spustit údržbu na vaše virtuální počítače jednu aktualizační doménu najednou.

 
## <a name="view-virtual-machine-scale-sets-that-are-affected-by-maintenance-in-the-portal"></a>Zobrazení škálovací sady virtuálních počítačů, které jsou ovlivněny údržby na portálu

Když je naplánováno vlny plánované údržby, můžete zobrazit seznam škálovacích sad virtuálních počítačů, které jsou ovlivněny wave nadcházející údržby pomocí webu Azure portal. 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. V nabídce vlevo vyberte **všechny služby**a pak vyberte **škálovací sady virtuálních počítačů**.
3. V části **škálovací sady virtuálních počítačů**vyberte **upravit sloupce** otevřete seznam dostupných sloupců.
4. V **dostupných sloupců** vyberte **samoobslužné údržby**, a pak ho přesunout do **vybrané sloupce** seznamu. Vyberte **Použít**.  

    Chcete-li **samoobslužné údržby** položky snazší najít, můžete změnit možnost rozevíracího seznamu v **dostupných sloupců** v článku **všechny** k  **Vlastnosti**.

**Samoobslužné údržby** sloupce se zobrazí v seznamu škálovací sady virtuálních počítačů. Každou škálovací sadu virtuálních počítačů může mít jednu z následujících hodnot ve sloupci samoobslužné údržby:

| Hodnota | Popis |
|-------|-------------|
| Ano | Alespoň jeden virtuální počítač ve škálovací sadě virtuálního počítače je v okně samoobslužné služby. Spustit údržbu kdykoli během tohoto časového intervalu pro samoobslužné služby. | 
| Ne | Žádné virtuální počítače v okně samoobslužné služby v škálovací ovlivněných virtuálních počítačů nastavené. | 
| - | Virtuální počítače škálovací sady nejsou součástí vlny plánované údržby.| 

## <a name="notification-and-alerts-in-the-portal"></a>Oznámení a výstrahy na portálu

Azure komunikuje plánu kvůli plánované údržbě e-mailem do skupiny vlastník a spoluvlastníci předplatného. Příjemci a kanály pro tuto komunikaci můžete přidat tak, že vytvoříte upozornění protokolu aktivit. Další informace najdete v tématu [monitorovat aktivitu předplatného se protokol aktivit Azure](../azure-monitor/platform/activity-logs-overview.md).

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. V nabídce vlevo vyberte **monitorování**. 
3. V **monitorování – upozornění (klasická)** vyberte **+ přidat upozornění protokolu aktivit**.
4. Na **přidat upozornění protokolu aktivit** stránky, vyberte nebo zadejte požadované informace. V **kritéria**, nezapomeňte nastavit následující hodnoty:
   - **Kategorie události**: Vyberte **Service Health**.
   - **Služby**: Vyberte **Škálovací sady virtuálních počítačů a virtuálních počítačů**.
   - **Typ**: Vyberte **plánované údržby**. 
    
Další informace o tom, jak nakonfigurovat výstrahy protokolu aktivit najdete v tématu [výstrahy vytvoření protokolu aktivit](../azure-monitor/platform/activity-log-alerts.md)
    
    
## <a name="start-maintenance-on-your-virtual-machine-scale-set-from-the-portal"></a>Spustit údržbu na vaši škálovací sadu virtuálních počítačů z portálu

Zobrazí se další podrobnosti týkající se údržby v přehledu škálovací sady virtuálních počítačů. Pokud alespoň jeden virtuální počítač ve škálovací sadě virtuálního počítače je součástí vlny plánované údržby, přidá se nové oznámení pásu karet v horní části stránky. Vyberte přejděte na pásu karet oznámení **údržby** stránky. 

Na **údržby** stránku, uvidíte, které instance virtuálního počítače je ovlivněna plánované údržby. Chcete-li spustit údržbu, zaškrtněte políčko, která odpovídá ovlivněných virtuálních počítačů. Vyberte **spustit údržbu**.

Po zahájení údržby ovlivněné virtuální počítače ve škálovací sadě virtuálních počítačů provést údržbu a jsou dočasně nedostupné. Pokud jste okno samoobslužných služeb, pořád uvidíte časový interval při se zachová svou škálovací sadu virtuálních počítačů Azure.
 
## <a name="check-maintenance-status-by-using-powershell"></a>Kontrola stavu údržby pomocí Powershellu

Prostředí Azure PowerShell můžete zjistit, kdy jsou virtuální počítače ve škálovací sadě virtuálních počítačů plánu údržby. Informace o plánované údržbě je k dispozici prostřednictvím [Get-AzureRmVmss](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss) rutiny při použití `-InstanceView` parametru.
 
Informace o údržbě se vrátí jenom v případě plánované údržby. Pokud žádná údržba je naplánována tak, že má vliv na instanci virtuálního počítače, rutina nevrací žádné informace o údržbě. 

```powershell
Get-AzureRmVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -InstanceView
```

Následující vlastnosti jsou vráceny v rámci **MaintenanceRedeployStatus**: 
| Hodnota | Popis   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Určuje, zda lze spustit údržbu na virtuálním počítači v tuto chvíli. ||
| PreMaintenanceWindowStartTime         | Začátek samoobslužné časové období údržby při údržby můžete spustit na virtuálním počítači. ||
| PreMaintenanceWindowEndTime           | Konec samoobslužné časové období údržby při údržby můžete spustit na virtuálním počítači. ||
| MaintenanceWindowStartTime            | Na začátek plánované údržby, ve kterém Azure zahájí Údržba na vašem virtuálním počítači. ||
| MaintenanceWindowEndTime              | Konec okna plánované údržby, ve kterém Azure zahájí Údržba na vašem virtuálním počítači. ||
| LastOperationResultCode               | Výsledek posledního pokusu o zahájení údržby ve virtuálním počítači. ||



### <a name="start-maintenance-on-your-vm-instance-by-using-powershell"></a>Spustit údržbu na instanci virtuálního počítače pomocí Powershellu

Můžete spustit údržbu na virtuálním počítači, pokud **IsCustomerInitiatedMaintenanceAllowed** je nastavena na **true**. Použití [Set-AzureRmVmss](/powershell/module/azurerm.compute/set-azurermvmss) rutinu s `-PerformMaintenance` parametru.

```powershell
Set-AzureRmVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -PerformMaintenance 
```

## <a name="check-maintenance-status-by-using-the-cli"></a>Kontrola stavu údržby pomocí rozhraní příkazového řádku

Můžete zobrazit informace o plánované údržbě pomocí [az vmss list-instances](/cli/azure/vmss?view=azure-cli-latest#az-vmss-list-instances).
 
Informace o údržbě se vrátí jenom v případě plánované údržby. Pokud je naplánovaná žádná Údržba, který má vliv na instanci virtuálního počítače, že příkaz nevrací žádné informace o údržbě. 

```azure-cli
az vmss list-instances -g rgName -n vmssName --expand instanceView
```

Následující vlastnosti jsou vráceny v rámci **MaintenanceRedeployStatus** jednotlivých instancí virtuálních počítačů: 
| Hodnota | Popis   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Určuje, zda lze spustit údržbu na virtuálním počítači v tuto chvíli. ||
| PreMaintenanceWindowStartTime         | Začátek samoobslužné časové období údržby při údržby můžete spustit na virtuálním počítači. ||
| PreMaintenanceWindowEndTime           | Konec samoobslužné časové období údržby při údržby můžete spustit na virtuálním počítači. ||
| MaintenanceWindowStartTime            | Na začátek plánované údržby, ve kterém Azure zahájí Údržba na vašem virtuálním počítači. ||
| MaintenanceWindowEndTime              | Konec okna plánované údržby, ve kterém Azure zahájí Údržba na vašem virtuálním počítači. ||
| LastOperationResultCode               | Výsledek posledního pokusu o zahájení údržby ve virtuálním počítači. ||


### <a name="start-maintenance-on-your-vm-instance-by-using-the-cli"></a>Spustit údržbu na instanci virtuálního počítače pomocí rozhraní příkazového řádku

Následující volání zahájí Údržba na instancích virtuálních počítačů, pokud `IsCustomerInitiatedMaintenanceAllowed` je nastavena na **true**:

```azure-cli
az vmss perform-maintenance -g rgName -n vmssName --instance-ids id
```

## <a name="faq"></a>Nejčastější dotazy

**DOTAZ: Proč je potřeba pro svoje virtuální počítače restartovat?**

**ODPOVĚĎ:** I když většina aktualizací a upgradů platformy Azure nemají vliv na dostupnost virtuálních počítačů, v některých případech Nedokážeme vyhnout restartování virtuálních počítačů hostovaných v Azure. Jsme nashromáždili několik změn, které vyžadují restartování našich serverů, které způsobí restartování virtuálního počítače.

**DOTAZ: Pokud sleduji svá doporučení pro zajištění vysoké dostupnosti s využitím dostupnosti nastavit, mám bezpečný?**

**ODPOVĚĎ:** Virtuální počítače nasazené ve skupině dostupnosti nastavena nebo v škálovací sady virtuálních počítačů pomocí aktualizačních domén. Při provádění údržby Azure dodržuje omezení aktualizace domény a nebude restartování virtuálních počítačů z jiné aktualizační doméně (v rámci stejné skupiny dostupnosti). Azure také čeká aspoň 30 minut před přechodem na další skupinu virtuálních počítačů. 

Další informace o vysoké dostupnosti najdete v tématu [oblasti a dostupnost virtuálních počítačů v Azure](../virtual-machines/windows/regions-and-availability.md).

**DOTAZ: Jak mohu nastavit oznamování o plánované údržbě?**

**ODPOVĚĎ:** Nastavením plánu na jeden nebo více oblastech Azure začíná vlny plánované údržby. Krátce po e-mailové oznámení, přijde vlastníkům předplatného (jeden e-mail na jedno předplatné). Kanály a příjemců tohoto oznámení můžete přidat pomocí upozornění protokolu aktivit. Pokud provádíte nasazení virtuálního počítače do oblasti, ve kterém je již naplánována plánované údržby, není dostat oznámení. Místo toho zkontrolujte stav údržby virtuálního počítače.

**DOTAZ: Můžu nezobrazuje žádný indikátor plánované údržby na portálu, Powershellu nebo rozhraní příkazového řádku. Co je?**

**ODPOVĚĎ:** Informace týkající se plánované údržby je k dispozici během vlny plánované údržby pouze pro virtuální počítače, které jsou ovlivněny plánované údržby. Pokud nevidíte data, že vlna údržby již pravděpodobně bylo dokončeno (nebo nespuštění), nebo virtuální počítač už může být hostován na aktualizovaném serveru.

**DOTAZ: Existuje způsob, jak zjistit přesně když můj virtuální počítač bude mít vliv na?**

**ODPOVĚĎ:** Nastavíme plán, nadefinujeme časovým intervalem několik dní. Přesné pořadí serverů (a virtuálních počítačů) v rámci tohoto časového intervalu není známo. Pokud chcete zjistit přesný čas aktualizují vaše virtuální počítače, můžete použít [naplánované události](../virtual-machines/windows/scheduled-events.md). Při použití naplánované události, můžete dotaz z v rámci virtuálního počítače a dostanete oznámení 15 minut před restartováním virtuálního počítače.

**DOTAZ: Jak dlouho bude trvat restartování virtuálního počítače?**

**ODPOVĚĎ:**  V závislosti na velikosti virtuálního počítače restartování může trvat až několik minut, než během časového intervalu samoobslužné údržby. Restartování během restartování iniciované platformou Azure – v plánované časové období údržby, obvykle trvá přibližně 25 minut. Pokud používáte cloudové služby (webové nebo pracovní Role), škálovací sady virtuálních počítačů nebo skupiny dostupnosti, budete mít 30 minut mezi jednotlivými skupinami virtuálních počítačů (aktualizační doména) během plánované časové období údržby. 

**DOTAZ: Nevidím žádné informace o údržbě na svoje virtuální počítače. K jakému?**

**ODPOVĚĎ:** Tady je několik důvodů, proč se nemusí zobrazit informace o libovolné údržby na virtuálních počítačích:
   - Používáte předplatné označen jako *Microsoft Internal*.
   - Nejsou vaše virtuální počítače s naplánovanou údržbou. Může být, že vlna údržby skončila, byla zrušena nebo byla změněna tak, aby vaše virtuální počítače jsou už vliv.
   - Nemáte k dispozici **údržby** sloupec přidali do zobrazení seznamu virtuálních počítačů. I když jsme tento sloupec přidali do výchozího zobrazení, pokud nakonfigurujete zobrazení zobrazíte nevýchozí sloupce, je třeba ručně přidat **údržby** sloupec do zobrazení seznamu virtuálních počítačů.

**DOTAZ: Můj virtuální počítač je naplánovaná údržba podruhé. Proč?**

**ODPOVĚĎ:** V několika případech použití váš virtuální počítač je naplánovaná údržba po již dokončení vaší údržby a znovu nasadit:
   - Jsme zrušena vlna údržby a restartuje s jinou datovou částí. Může být, že jsme zjistili chybnou datové části a potřebujeme jen nasadit další datové části.
   - Virtuální počítač byl *službami opraven* do jiného uzlu z důvodu selhání hardwaru.
   - Vybrali jste zastavení (uvolnění) a restartujte virtuální počítač.
   - Máte **automatické vypnutí** zapnuta pro daný virtuální počítač.

## <a name="next-steps"></a>Další postup

Informace o postupu registrace události údržby ve virtuálním počítači s použitím [naplánované události](../virtual-machines/windows/scheduled-events.md).

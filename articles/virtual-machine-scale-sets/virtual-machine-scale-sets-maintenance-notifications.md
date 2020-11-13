---
title: Oznámení o údržbě pro služby Virtual Machine Scale Sets v Azure
description: Zobrazte si oznámení o údržbě a spusťte samoobslužnou údržbu pro služby Virtual Machine Scale Sets v Azure.
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 11/12/2020
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: 2aa589d237a8cfeb8e0dc947896dba82e755631c
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94564765"
---
# <a name="planned-maintenance-notifications-for-virtual-machine-scale-sets"></a>Oznámení plánované údržby pro škálovací sady virtuálních počítačů


Azure pravidelně provádí aktualizace a vylepšuje tak spolehlivost, výkon a zabezpečení hostitelské infrastruktury pro virtuální počítače (VM). Aktualizace můžou zahrnovat opravy hostitelského prostředí nebo upgrade a vyřazení hardwaru z provozu. Většina aktualizací nemá vliv na hostované virtuální počítače. Aktualizace ale mají vliv na virtuální počítače v těchto scénářích:

- Pokud údržba nevyžaduje restart, Azure během aktualizace hostitele pozastaví virtuální počítač o několik sekund. Tyto typy operací údržby používají doménu selhání podle domény selhání. Průběh se zastaví, pokud se obdrží nějaké signály stavu upozornění.

- Pokud údržba vyžaduje restart, dostanete oznámení o tom, kdy se údržba plánuje. V těchto případech máte časový interval, který je obvykle 35 dní, kdy je možné zahájit údržbu sami, když to bude fungovat.


Plánovaná údržba, která vyžaduje restart, je naplánována na vlny. Každý vlna má jiný obor (oblasti):

- Wave začíná oznámením pro zákazníky. Ve výchozím nastavení se oznámení pošle vlastníkovi předplatného a spoluvlastníkům. K oznámením můžete přidat příjemce a možnosti zasílání zpráv, jako jsou e-maily, SMS a Webhooky, pomocí [upozornění protokolu aktivit](../azure-monitor/platform/platform-logs-overview.md)Azure.  
- K dispozici je *samoobslužné okno* s oznámením. Během tohoto okna, které je obvykle 35 dní, můžete zjistit, které z vašich virtuálních počítačů jsou součástí vlny. Údržbu můžete proaktivně spustit podle vlastních potřeb plánování.
- Po samoobslužném okně začne *plánované časové období údržby* . V určitém okamžiku v tomto okně Azure plánuje a na váš virtuální počítač aplikuje požadovanou údržbu. 

V případě, že máte k dispozici dva systémy Windows, získáte dostatek času na spuštění údržby a restartování virtuálního počítače s vědomím, kdy bude Azure automaticky spouštět údržbu.

Můžete použít Azure Portal, PowerShell, REST API a Azure CLI k dotazování na časová období údržby pro virtuální počítače sady škálování virtuálních počítačů a spustit samoobslužnou údržbu.

## <a name="should-you-start-maintenance-during-the-self-service-window"></a>Měli byste začít s údržbou během samoobslužného okna?  

Následující pokyny vám pomohou při rozhodování, zda spustit údržbu v době, kterou zvolíte.

> [!NOTE] 
> Samoobslužná údržba nemusí být k dispozici pro všechny vaše virtuální počítače. Pokud chcete zjistit, jestli je pro váš virtuální počítač k dispozici proaktivní opětovné nasazení, vyhledejte možnost **Spustit nyní** ve stavu údržby. V současné době není samoobslužná údržba dostupná pro Azure Cloud Services (Web/role pracovního procesu) a Azure Service Fabric.


Samoobslužná údržba se nedoporučuje pro nasazení, která používají *skupiny dostupnosti*. Skupiny dostupnosti jsou nastavení s vysokou dostupností, ve kterém je kdykoli ovlivněna pouze jedna aktualizační doména. Pro skupiny dostupnosti:

- Umožněte službě Azure aktivovat údržbu. V případě údržby, která vyžaduje restart, je údržba dokončena aktualizací aktualizačních domén. Aktualizace domén nutně nezískají průběžnou údržbu. Mezi aktualizačními doménami je pauza o 30 minut.
- Pokud je k dispozici Dočasná ztráta některé z vaší kapacity (1/aktualizace počtu domén), můžete tuto ztrátu snadno kompenzovat přidělením dalších instancí během období údržby.
- V případě údržby, která nevyžaduje restart, se aktualizace aplikují na úrovni domény selhání. 
    
**Don't** Nepoužívejte samoobslužnou údržbu v následujících scénářích: 

- Pokud jste virtuální počítače často vypnuli ručně, pomocí DevTest Labs, pomocí automatického vypnutí nebo podle plánu. Samoobslužná údržba v těchto scénářích může vrátit stav údržby a způsobit další výpadky.
- V případě krátkodobých virtuálních počítačů, o kterých víte, že se odstraní před koncem vlny údržby. 
- Pro úlohy s velkým stavem uloženým v místním (dočasném) disku, který chcete zachovat po aktualizaci. 
- Pokud často měníte velikost virtuálního počítače. Tento scénář může vrátit stav údržby. 
- Pokud jste přijali naplánované události, které umožní proaktivní převzetí služeb při selhání nebo řádné vypnutí úlohy 15 minut před tím, než se zahájí vypnutí údržby.

**Využijte** samoobslužnou údržbu, pokud plánujete spustit virtuální počítač v době plánované fáze údržby bez přerušení a žádný z předchozích counterindications použít. 

Je nejvhodnější použít samoobslužnou údržbu v následujících případech:

- Pro správu nebo zákazníky musíte sdělit přesné časové období údržby. 
- Je potřeba dokončit údržbu do konkrétního data. 
- Aby bylo zajištěno bezpečné obnovení, je třeba řídit sekvencování údržby, například ve vícevrstvé aplikaci.
- Pro dobu obnovení virtuálních počítačů mezi dvěma aktualizačními doménami potřebujete víc než 30 minut. Chcete-li řídit dobu mezi aktualizačními doménami, je nutné aktivovat údržbu pro virtuální počítače s jednou aktualizační doménou v jednom okamžiku.

 
## <a name="view-virtual-machine-scale-sets-that-are-affected-by-maintenance-in-the-portal"></a>Zobrazit sady škálování virtuálních počítačů, které jsou ovlivněné údržbou na portálu

Po naplánování naplánovaných vln údržby můžete zobrazit seznam sad škálování virtuálních počítačů, na které se vztahuje nadcházející údržba Wave, a to pomocí Azure Portal. 

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
2. V nabídce vlevo vyberte **všechny služby** a pak vyberte **Virtual Machine Scale Sets**.
3. V části **Virtual Machine Scale Sets** vyberte **Upravit sloupce** a otevřete seznam dostupných sloupců.
4. V části **Dostupné sloupce** vyberte **samoobslužná údržba** a pak ji přesuňte do seznamu **vybrané sloupce** . Vyberte **Apply** (Použít).  

    Chcete-li umožnit snazší vyhledání položky **samoobslužné údržby** , můžete změnit možnost rozevíracího seznamu v části **Dostupné sloupce** z možnosti **všechny** na **vlastnosti**.

Sloupec **samoobslužná údržba** se teď zobrazuje v seznamu služby Virtual Machine Scale Sets. Každá sada škálování virtuálního počítače může mít jednu z následujících hodnot pro sloupec samoobslužná Údržba:

| Hodnota | Popis |
|-------|-------------|
| Ano | Aspoň jeden virtuální počítač v sadě škálování virtuálního počítače je v samoobslužném okně. Údržbu můžete kdykoli spustit během tohoto samoobslužného okna. | 
| Ne | V rámci příslušné sady škálování virtuálních počítačů nejsou žádné virtuální počítače v okně samoobslužné služby. | 
| - | Vaše služby Virtual Machine Scale Sets nejsou součástí plánovaného formátu Wave pro údržbu.| 

## <a name="notification-and-alerts-in-the-portal"></a>Oznámení a výstrahy na portálu

Azure komunikuje s plánem plánované údržby odesláním e-mailu vlastníkovi předplatného a spoluvlastníci. Do této komunikace můžete přidat příjemce a kanály tím, že vytvoříte výstrahy protokolu aktivit. Další informace najdete v tématu [monitorování aktivity předplatného pomocí protokolu aktivit Azure](../azure-monitor/platform/platform-logs-overview.md).

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
2. V nabídce vlevo vyberte **monitor**. 
3. V podokně **monitor – výstrahy (Classic)** vyberte **+ Přidat upozornění protokolu aktivit**.
4. Na stránce **Přidat upozornění protokolu aktivit** vyberte nebo zadejte požadované informace. V části **kritéria** ověřte, že jste nastavili následující hodnoty:
   - **Kategorie události** : vyberte **Service Health**.
   - **Služby** : vyberte **Virtual Machine Scale Sets a Virtual Machines**.
   - **Typ** : vyberte možnost **plánovaná údržba**. 
    
Další informace o tom, jak nakonfigurovat výstrahy protokolu aktivit, najdete v tématu [Vytvoření upozornění protokolu aktivit](../azure-monitor/platform/activity-log-alerts.md) .
    
    
## <a name="start-maintenance-on-your-virtual-machine-scale-set-from-the-portal"></a>Spuštění údržby sady škálování virtuálních počítačů z portálu

Podrobné informace o údržbě najdete v přehledu virtuálních počítačů s měřítkem. Pokud je v plánovaném vlnovce údržby zahrnutý aspoň jeden virtuální počítač v sadě škálování virtuálního počítače, přidá se v horní části stránky nový pás karet oznámení. Vyberte pás karet oznámení, který chcete přejít na stránku **údržby** . 

Na stránce **Údržba** vidíte, která instance virtuálního počítače je ovlivněna plánovanou údržbou. Pokud chcete spustit údržbu, zaškrtněte políčko, které odpovídá příslušnému virtuálnímu počítači. Pak vyberte  **Spustit údržbu**.

Po spuštění údržby se ovlivněné virtuální počítače v sadě škálování virtuálního počítače procházejí údržbou a dočasně nejsou k dispozici. Pokud jste okno samoobslužné služby nenalezli, můžete i nadále zobrazovat časový interval, kdy Azure zachovává sadu škálování virtuálního počítače.
 
## <a name="check-maintenance-status-by-using-powershell"></a>Ověření stavu údržby pomocí PowerShellu

Pomocí Azure PowerShell můžete zobrazit, kdy se u virtuálních počítačů ve službě Virtual Machine Scale Sets plánuje údržba. Informace o plánované údržbě jsou k dispozici pomocí rutiny [Get-AzVmss](/powershell/module/az.compute/get-azvmss) při použití `-InstanceView` parametru.
 
Informace o údržbě jsou vráceny pouze v případě, že je údržba plánována. Pokud není naplánována žádná údržba, která by měla vliv na instanci virtuálního počítače, rutina nevrátí žádné informace o údržbě. 

```powershell
Get-AzVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -InstanceView
```

V **MaintenanceRedeployStatus** se vrátí následující vlastnosti: 

| Hodnota | Popis   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Určuje, jestli můžete v tomto okamžiku spustit údržbu virtuálního počítače. |
| PreMaintenanceWindowStartTime         | Začátek samoobslužného okna údržby, když můžete na svém VIRTUÁLNÍm počítači zahájit údržbu. |
| PreMaintenanceWindowEndTime           | Konec samoobslužného okna údržby, když můžete na svém VIRTUÁLNÍm počítači iniciovat údržbu. |
| MaintenanceWindowStartTime            | Začátek údržby naplánovaný při zahájení údržby virtuálního počítače v Azure. |
| MaintenanceWindowEndTime              | Konec okna naplánované údržby, ve kterém Azure iniciuje údržbu na vašem VIRTUÁLNÍm počítači. |
| LastOperationResultCode               | Výsledek posledního pokusu o spuštění údržby na virtuálním počítači. |



### <a name="start-maintenance-on-your-vm-instance-by-using-powershell"></a>Spuštění údržby instance virtuálního počítače pomocí PowerShellu

Pokud je **IsCustomerInitiatedMaintenanceAllowed** nastavená na **true** , můžete spustit údržbu na virtuálním počítači. Použijte rutinu [set-AzVmss](/powershell/module/az.compute/set-azvmss) s `-PerformMaintenance` parametrem.

```powershell
Set-AzVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -PerformMaintenance 
```

## <a name="check-maintenance-status-by-using-the-cli"></a>Ověření stavu údržby pomocí rozhraní příkazového řádku

Informace o plánované údržbě můžete zobrazit pomocí [AZ VMSS list-Instances](/cli/azure/vmss?view=azure-cli-latest#az-vmss-list-instances).
 
Informace o údržbě jsou vráceny pouze v případě, že je údržba plánována. Pokud není naplánována žádná údržba, která má vliv na instanci virtuálního počítače, příkaz nevrátí žádné informace o údržbě. 

```azurecli
az vmss list-instances -g rgName -n vmssName --expand instanceView
```

V **MaintenanceRedeployStatus** se vrátí následující vlastnosti pro každou instanci virtuálního počítače: 

| Hodnota | Popis   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Určuje, jestli můžete v tomto okamžiku spustit údržbu virtuálního počítače. |
| PreMaintenanceWindowStartTime         | Začátek samoobslužného okna údržby, když můžete na svém VIRTUÁLNÍm počítači zahájit údržbu. |
| PreMaintenanceWindowEndTime           | Konec samoobslužného okna údržby, když můžete na svém VIRTUÁLNÍm počítači iniciovat údržbu. |
| MaintenanceWindowStartTime            | Začátek údržby naplánovaný při zahájení údržby virtuálního počítače v Azure. |
| MaintenanceWindowEndTime              | Konec okna naplánované údržby, ve kterém Azure iniciuje údržbu na vašem VIRTUÁLNÍm počítači. |
| LastOperationResultCode               | Výsledek posledního pokusu o spuštění údržby na virtuálním počítači. |


### <a name="start-maintenance-on-your-vm-instance-by-using-the-cli"></a>Spuštění údržby instance virtuálního počítače pomocí rozhraní příkazového řádku

Následující volání inicializuje údržbu instance virtuálního počítače `IsCustomerInitiatedMaintenanceAllowed` , pokud je nastavená na **hodnotu true** :

```azurecli
az vmss perform-maintenance -g rgName -n vmssName --instance-ids id
```

## <a name="faq"></a>Nejčastější dotazy

**Otázka: Proč potřebujete restartovat své virtuální počítače nyní?**

**A:** I když většina aktualizací a upgradů na platformu Azure nemá vliv na dostupnost virtuálního počítače, nemůžeme v některých případech vyhýbat restartování virtuálních počítačů hostovaných v Azure. Shromáždili jsme několik změn, které vyžadují restart našich serverů, které způsobí restartování virtuálního počítače.

**Otázka: Pokud budu dodržovat doporučení pro vysokou dostupnost pomocí skupiny dostupnosti, je to v bezpečí?**

**A:** Virtuální počítače nasazené ve skupině dostupnosti nebo v sadě Virtual Machine Scale Sets používají aktualizační domény. Při provádění údržby Azure respektuje omezení domény aktualizace a nerestartuje virtuální počítače z jiné aktualizační domény (ve stejné skupině dostupnosti). Azure také před přechodem na další skupinu virtuálních počítačů počká aspoň 30 minut. 

Další informace o vysoké dostupnosti najdete v tématu [oblasti a dostupnost pro virtuální počítače v Azure](../virtual-machines/availability.md).

**Otázka: Jak mohu dostávat informace o plánované údržbě?**

**A:** Spustí se naplánovaná údržba Wave tím, že nastaví plán na jednu nebo více oblastí Azure. Brzy se pošle e-mailové oznámení správcům předplatného, spolupracovníkům, vlastníkům a přispěvatelům (jeden e-mail na předplatné). Další kanály a příjemci pro toto oznámení můžou být nakonfigurované pomocí upozornění protokolu aktivit. V případě, že nasadíte virtuální počítač do oblasti, ve které je plánovaná údržba již naplánována, nebudete dostávat oznámení. Místo toho se podívejte na stav údržby virtuálního počítače.

**Otázka: v portálu, PowerShellu nebo rozhraní příkazového řádku se nezobrazují žádné informace o plánované údržbě. Co je?**

**A:** Informace týkající se plánované údržby jsou k dispozici v průběhu plánované údržby, pouze pro virtuální počítače, které jsou ovlivněné plánovanou údržbou. Pokud nevidíte data, může se stát, že je již dokončeno (nebo ne spuštěno), nebo že je váš virtuální počítač již hostovaný na aktualizovaném serveru.

**Otázka: existuje způsob, jak přesně zjistit, kdy to bude mít vliv na můj virtuální počítač?**

**A:** Když nastavíme plán, nadefinujeme časový interval několik dní. Přesné pořadí serverů (a virtuálních počítačů) v rámci tohoto časového intervalu není známo. Pokud chcete znát přesný čas, kdy se vaše virtuální počítače budou aktualizovat, můžete použít [naplánované události](../virtual-machines/windows/scheduled-events.md). Když použijete naplánované události, můžete se z virtuálního počítače dotazovat a obdržet oznámení o 15 minutách před restartováním virtuálního počítače.

**Otázka: jak dlouho bude trvat restartování virtuálního počítače?**

**A:**  V závislosti na velikosti virtuálního počítače může restartování trvat až několik minut, než se okno samoobslužné údržby. Během restartování iniciované Azure v plánovaném časovém období údržby trvá restart obvykle přibližně 25 minut. Pokud používáte Cloud Services (Web/role pracovního procesu), sady škálování virtuálních počítačů nebo sady dostupnosti, budete během naplánovaného časového období údržby dodávat 30 minut mezi každou skupinou virtuálních počítačů (aktualizační doména). 

**Otázka: na virtuálních počítačích nevidím žádné informace o údržbě. Co se pokazilo?**

**A:** Existuje několik důvodů, proč se na virtuálních počítačích nemusejí zobrazovat žádné informace o údržbě:
   - Používáte odběr označený jako *interní Microsoft*.
   - Pro vaše virtuální počítače není naplánovaná údržba. Může to být tím, že se ukončila vlna údržby, byla zrušena nebo změněna, aby vaše virtuální počítače už neovlivnily.
   - Do zobrazení seznamu virtuálních počítačů nemáte přidaný sloupec **Údržba** . I když jsme tento sloupec přidali do výchozího zobrazení, pokud nakonfigurujete zobrazení tak, aby se zobrazily jiné než výchozí sloupce, musíte do zobrazení seznamu virtuálních počítačů ručně přidat sloupec **Údržba** .

**Otázka: má virtuální počítač naplánovanou údržbu podruhé. Proč?**

**A:** V několika případech použití je naplánována údržba virtuálního počítače po dokončení údržby a opětovného nasazení:
   - Zrušili jste vlnu údržby a restartovali ji s jinou datovou částí. Může to být tím, že jsme zjistili chybnou datovou část a jednoduše potřebujete nasadit další datovou část.
   - Kvůli hardwarové chybě byla *Služba VM zacelená* na jiný uzel.
   - Vybrali jste možnost zastavit (zrušit přidělení) a restartovat virtuální počítač.
   - Pro virtuální počítač je zapnuté **Automatické vypnutí** .

## <a name="next-steps"></a>Další kroky

Naučte se registrovat události údržby z virtuálního počítače pomocí [naplánovaných událostí](../virtual-machines/windows/scheduled-events.md).

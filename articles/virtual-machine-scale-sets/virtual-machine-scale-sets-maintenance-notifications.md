---
title: Oznámení o údržbě škálovacích sad virtuálních strojů v Azure
description: Zobrazení oznámení o údržbě a spuštění samoobslužné údržby škálovacích sad virtuálních strojů v Azure.
author: shants123
tags: azure-service-management,azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: shants
ms.openlocfilehash: 53ebb7c4710c5455ef90701dc7e94f1b846a874a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062706"
---
# <a name="planned-maintenance-notifications-for-virtual-machine-scale-sets"></a>Oznámení plánované údržby pro škálovací sady virtuálních počítačů


Azure pravidelně provádí aktualizace ke zlepšení spolehlivosti, výkonu a zabezpečení hostitelské infrastruktury pro virtuální počítače (VM). Aktualizace mohou zahrnovat opravu hostitelského prostředí nebo upgrade a vyřazení hardwaru z provozu. Většina aktualizací nemá vliv na hostované virtuální chod. Aktualizace však ovlivňují virtuální společnosti v těchto scénářích:

- Pokud údržba nevyžaduje restartování, Azure používá migraci na místě pozastavit virtuální počítač, zatímco se aktualizuje hostitel. Operace údržby, které nevyžadují restartování, jsou použity doménou selhání doménou selhání. Průběh je zastaven, pokud jsou přijaty všechny signály varovného stavu.

- Pokud údržba vyžaduje restartování, zobrazí se upozornění, kdy je údržba plánována. V těchto případech dostanete časové okno, které je obvykle 35 dní, kdy můžete spustit údržbu sami, když to funguje pro vás.


Plánovaná údržba, která vyžaduje restartování, je naplánována ve vlnách. Každá vlna má jiný rozsah (regiony):

- Vlna začíná oznámením zákazníkům. Ve výchozím nastavení je oznámení odesláno vlastníkovi předplatného a spoluvlastníkům. Můžete přidat příjemce a možnosti zasílání zpráv, jako je e-mail, SMS a webhooky oznámení pomocí [výstrahy protokolu aktivit](../azure-monitor/platform/platform-logs-overview.md)Azure .  
- S oznámením je k dispozici *samoobslužné okno.* Během tohoto okna, které je obvykle 35 dní, můžete zjistit, které z vašich virtuálních stránek jsou zahrnuty do vlny. Údržbu můžete proaktivně zahájit podle vlastních potřeb plánování.
- Po samoobslužné okno okno *plánované údržby* začíná. V určitém okamžiku během tohoto okna Azure naplánuje a použije požadovanou údržbu pro váš virtuální počítač. 

Cílem dvou oken je poskytnout vám dostatek času na spuštění údržby a restartování virtuálního počítače, zatímco budete vědět, kdy Azure automaticky spustí údržbu.

Můžete použít portál Azure, PowerShell, rozhraní REST API a rozhraní api Azure k dotazování na okna údržby pro virtuální počítače škálovací sady virtuálních počítače a ke spuštění samoobslužné údržby.

## <a name="should-you-start-maintenance-during-the-self-service-window"></a>Měli byste spustit údržbu během samoobslužného okna?  

Následující pokyny vám mohou pomoci rozhodnout, zda chcete spustit údržbu v čase, který zvolíte.

> [!NOTE] 
> Samoobslužná údržba nemusí být dostupná pro všechny vaše virtuální počítače. Chcete-li zjistit, zda proaktivní opětovné nasazení je k dispozici pro váš virtuální počítač, vyhledejte **Start now** ve stavu údržby. Samoobslužná údržba momentálně není dostupná pro Cloudové služby Azure (role webu/pracovního procesu) a Azure Service Fabric.


Samoobslužná údržba se nedoporučuje pro nasazení, která používají *skupiny dostupnosti*. Skupiny dostupnosti jsou vysoce dostupná nastavení, ve kterých je kdykoli ovlivněna pouze jedna aktualizační doména. Pro skupiny dostupnosti:

- Nechte Azure aktivovat údržbu. Pro údržbu, která vyžaduje restartování, údržba se provádí aktualizace domény podle aktualizační domény. Aktualizační domény nemusí nutně přijímat údržbu postupně. Mezi aktualizačními doménami je 30minutová pauza.
- Pokud se jedná o dočasnou ztrátu části kapacity (počet domén 1/aktualizace), můžete snadno kompenzovat ztrátu přidělením dalších instancí během období údržby.
- Pro údržbu, která nevyžaduje restartování, jsou aktualizace použity na úrovni domény selhání. 
    
**Nepoužívejte** samoobslužnou údržbu v následujících scénářích: 

- Pokud virtuální počítače často vypínáte, buď ručně, pomocí devTest Labs, pomocí automatického vypnutí nebo podle plánu. Samoobslužná údržba v těchto scénářích může vrátit stav údržby a způsobit další prostoje.
- Na krátkých virtuálních počítačích, o kterých víte, že budou odstraněny před koncem vlny údržby. 
- Pro úlohy s velkým stavem uloženým na místním (dočasném) disku, který chcete udržovat po aktualizaci. 
- Pokud často změníte velikost virtuálního počítače. Tento scénář může vrátit stav údržby. 
- Pokud jste přijali naplánované události, které umožňují proaktivní převzetí služeb při selhání nebo řádné vypnutí úlohy 15 minut před zahájením vypnutí údržby.

**Do** Samoobslužné údržby používejte, pokud plánujete nepřerušené spuštění virtuálního počítače během fáze plánované údržby a neplatí žádná z předchozích protiindikací. 

Samoobslužnou údržbu je nejlepší použít v následujících případech:

- Musíte sdělit přesné okno údržby vedení nebo vašemu zákazníkovi. 
- Údržbu je třeba dokončit do určitého data. 
- Je třeba řídit pořadí údržby, například ve vícevrstvé aplikaci, aby bylo zaručeno bezpečné obnovení.
- Potřebujete více než 30 minut doby obnovení virtuálního mísy mezi dvěma aktualizačními doménami. Chcete-li řídit čas mezi aktualizačními doménami, musíte aktivovat údržbu na virtuálních počítačích po jedné aktualizační doméně.

 
## <a name="view-virtual-machine-scale-sets-that-are-affected-by-maintenance-in-the-portal"></a>Zobrazení škálovacích sad virtuálních strojů, které jsou ovlivněny údržbou na portálu

Když je naplánována plánovaná vlna údržby, můžete zobrazit seznam škálovacích sad virtuálních strojů, které jsou ovlivněny nadcházející vlnou údržby pomocí portálu Azure. 

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. V levé nabídce vyberte **Všechny služby**a pak vyberte **Škálovací sady virtuálních strojů**.
3. V části **Škálovací sady virtuálních strojů**vyberte **Upravit sloupce** a otevřete seznam dostupných sloupců.
4. V části **Dostupné sloupce** vyberte **Samoobslužná údržba**a pak ji přesuňte do seznamu **Vybrané sloupce.** Vyberte **Použít**.  

    Chcete-li usnadnit hledání položky **samoobslužné údržby,** můžete změnit možnost rozevíracího přehledu v části **Dostupné sloupce** z **MožnostI Všechny** na **Vlastnosti**.

Sloupec **Samoobslužná údržba** se nyní zobrazí v seznamu škálovacích sad virtuálních strojů. Každá škálovací sada virtuálních strojů může mít jednu z následujících hodnot pro sloupec samoobslužné údržby:

| Hodnota | Popis |
|-------|-------------|
| Ano | Alespoň jeden virtuální počítač ve škálovací sadě virtuálního počítače je v samoobslužném okně. Údržbu můžete spustit kdykoli během tohoto samoobslužného okna. | 
| Ne | Žádné virtuální počítače jsou v samoobslužné okno v ovlivněné virtuální stroj škálovací sady. | 
| - | Škálovací sady virtuálních počítačů nejsou součástí plánované vlny údržby.| 

## <a name="notification-and-alerts-in-the-portal"></a>Oznámení a upozornění na portálu

Azure komunikuje plán plánované údržby odesláním e-mailu vlastníkovi předplatného a skupině spoluvlastníků. Do této komunikace můžete přidat příjemce a kanály vytvořením výstrah protokolu aktivit. Další informace najdete v tématu [Sledování aktivity předplatného pomocí protokolu aktivit Azure](../azure-monitor/platform/platform-logs-overview.md).

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. V levé nabídce vyberte **monitorovat**. 
3. V podokně **Monitor – výstrahy (klasické)** vyberte **+Přidat upozornění protokolu aktivit**.
4. Na stránce **Přidat upozornění protokolu aktivit** vyberte nebo zadejte požadované informace. V **části Kritéria**zkontrolujte, zda jste nastavili následující hodnoty:
   - **Kategorie události**: Vyberte **možnost Stav služby**.
   - **Služby**: Vyberte **škálovací sady virtuálních počítačů a virtuální počítače**.
   - **Typ**: Vyberte **plánovanou údržbu**. 
    
Další informace o konfiguraci výstrah protokolu aktivit najdete v tématu [Vytvoření výstrah protokolu aktivit](../azure-monitor/platform/activity-log-alerts.md)
    
    
## <a name="start-maintenance-on-your-virtual-machine-scale-set-from-the-portal"></a>Spuštění údržby ve škálovací sadě virtuálních strojů z portálu

Další podrobnosti týkající se údržby najdete v přehledu škálovacích sad virtuálních strojů. Pokud alespoň jeden virtuální počítač ve škálovací sadě virtuálního počítače je součástí plánované vlny údržby, nový pás karet oznámení se přidá v horní části stránky. Vyberte pás karet oznámení, který chcete přejít na stránku **Údržba.** 

Na stránce **Údržba** uvidíte, která instance virtuálního počítače je ovlivněna plánovanou údržbou. Chcete-li zahájit údržbu, zaškrtněte políčko, které odpovídá ovlivněnému virtuálnímu virtuálnímu virtuálnímu ms. Potom vyberte **Spustit údržbu**.

Po spuštění údržby se postižené virtuální počítače ve škálovací sadě virtuálních strojů podrobí údržbě a jsou dočasně nedostupné. Pokud jste vynechal samoobslužné okno, můžete stále vidět časové okno, kdy vaše škálovací sada virtuálního počítače bude udržována Azure.
 
## <a name="check-maintenance-status-by-using-powershell"></a>Kontrola stavu údržby pomocí PowerShellu

Pomocí Azure PowerShellu můžete zjistit, kdy jsou virtuální počítače ve škálovacích sadách virtuálních počítačů naplánované pro údržbu. Informace o plánované údržbě jsou k dispozici pomocí [rutiny Get-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/get-azvmss) při použití parametru. `-InstanceView`
 
Informace o údržbě jsou vráceny pouze v případě, že je plánována údržba. Pokud není naplánována žádná údržba, která by měla vliv na instanci virtuálního trhu, rutina nevrátí žádné informace o údržbě. 

```powershell
Get-AzVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -InstanceView
```

Následující vlastnosti jsou vráceny v rámci **MaintenanceRedeployStatus**: 

| Hodnota | Popis   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Označuje, zda můžete spustit údržbu na virtuálním počítači v tomto okamžiku. |
| Doba předúdržbouoknaStartTime         | Začátek samoobslužného okna údržby, když můžete zahájit údržbu na virtuálním počítači. |
| Doba předúdržbou oknaEndTime           | Konec samoobslužného okna údržby, když můžete zahájit údržbu na virtuálním počítači. |
| ÚdržbaWindowStartTime            | Začátek naplánované údržby, ve kterém Azure iniciuje údržbu na vašem virtuálním počítači. |
| ÚdržbaOknoEndTime              | Konec naplánovaného okna údržby, ve kterém Azure iniciuje údržbu na vašem virtuálním počítači. |
| LastOperationResultCode               | Výsledek posledního pokusu o zahájení údržby na virtuálním počítači. |



### <a name="start-maintenance-on-your-vm-instance-by-using-powershell"></a>Spuštění údržby instance virtuálního počítače pomocí PowerShellu

Údržbu můžete spustit na virtuálním počítači, pokud **je iscustomerinitiatedMaintenanceAllowed** nastavena na **hodnotu true**. Použijte rutinu [Set-AzVmss](/powershell/module/az.compute/set-azvmss) `-PerformMaintenance` s parametrem.

```powershell
Set-AzVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -PerformMaintenance 
```

## <a name="check-maintenance-status-by-using-the-cli"></a>Kontrola stavu údržby pomocí zapisovacího řádku

Informace o plánované údržbě můžete zobrazit pomocí [instancí seznamu az vmss](/cli/azure/vmss?view=azure-cli-latest#az-vmss-list-instances).
 
Informace o údržbě jsou vráceny pouze v případě, že je plánována údržba. Pokud není naplánována žádná údržba, která ovlivňuje instanci virtuálního soudu, příkaz nevrátí žádné informace o údržbě. 

```azurecli
az vmss list-instances -g rgName -n vmssName --expand instanceView
```

Následující vlastnosti jsou vráceny v rámci **MaintenanceRedeployStatus** pro každou instanci virtuálního počítače: 

| Hodnota | Popis   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Označuje, zda můžete spustit údržbu na virtuálním počítači v tomto okamžiku. |
| Doba předúdržbouoknaStartTime         | Začátek samoobslužného okna údržby, když můžete zahájit údržbu na virtuálním počítači. |
| Doba předúdržbou oknaEndTime           | Konec samoobslužného okna údržby, když můžete zahájit údržbu na virtuálním počítači. |
| ÚdržbaWindowStartTime            | Začátek naplánované údržby, ve kterém Azure iniciuje údržbu na vašem virtuálním počítači. |
| ÚdržbaOknoEndTime              | Konec naplánovaného okna údržby, ve kterém Azure iniciuje údržbu na vašem virtuálním počítači. |
| LastOperationResultCode               | Výsledek posledního pokusu o zahájení údržby na virtuálním počítači. |


### <a name="start-maintenance-on-your-vm-instance-by-using-the-cli"></a>Spuštění údržby instance virtuálního počítače pomocí cli

Následující volání iniciuje údržbu `IsCustomerInitiatedMaintenanceAllowed` instance virtuálního počítače, pokud je nastavena na **hodnotu true**:

```azurecli
az vmss perform-maintenance -g rgName -n vmssName --instance-ids id
```

## <a name="faq"></a>Nejčastější dotazy

**Otázka: Proč potřebujete restartovat virtuální počítače teď?**

**A:** I když většina aktualizací a upgradů na platformu Azure nemá vliv na dostupnost virtuálních počítačů, v některých případech se nemůžeme vyhnout restartování virtuálních počítačů hostovaných v Azure. Nashromáždili jsme několik změn, které vyžadují restartování serverů, které budou mít za následek restartování virtuálního počítače.

**Otázka: Pokud se řídím vašimi doporučeními pro vysokou dostupnost pomocí sady dostupnosti, jsem v bezpečí?**

**A:** Virtuální počítače nasazené v sadě dostupnosti nebo ve škálovacích sadách virtuálních počítačů používají aktualizační domény. Při provádění údržby Azure respektuje omezení domény aktualizace a nerestartuje virtuální počítače z jiné domény aktualizace (ve stejné sadě dostupnosti). Azure také čeká alespoň 30 minut před přechodem na další skupinu virtuálních počítačů. 

Další informace o vysoké dostupnosti najdete [v tématu oblasti a dostupnost pro virtuální počítače v Azure](../virtual-machines/windows/availability.md).

**Otázka: Jak mohu být informován/a o plánované údržbě?**

**A:** Vlna plánované údržby začíná nastavením plánu do jedné nebo více oblastí Azure. Brzy poté se majitelům předplatného odešle e-mailové oznámení (jeden e-mail na jedno předplatné). Kanály a příjemce pro toto oznámení můžete přidat pomocí výstrah protokolu aktivit. Pokud nasadíte virtuální ho do oblasti, ve které je již naplánována plánovaná údržba, neobdržíte oznámení. Místo toho zkontrolujte stav údržby virtuálního soudu.

**Otázka: Nevidím žádné známky plánované údržby na portálu, PowerShellu nebo CLI. Co je?**

**A:** Informace týkající se plánované údržby jsou k dispozici během plánované vlny údržby pouze pro virtuální zařízení, které jsou ovlivněny plánovanou údržbou. Pokud nevidíte data, vlna údržby už může být hotová (nebo nespuštěná) nebo váš virtuální počítač už může být hostovaný na aktualizovaném serveru.

**Otázka: Existuje způsob, jak přesně vědět, kdy bude můj virtuální virtuální virtuální mísa ovlivněna?**

**A:** Když nastavíme plán, definujeme časové období několika dnů. Přesné pořadí serverů (a virtuálních počítačů) v rámci tohoto časového intervalu není známo. Pokud chcete vědět přesný čas, kdy se vaše virtuální počítače budou aktualizovat, můžete použít [naplánované události](../virtual-machines/windows/scheduled-events.md). Při použití naplánované události, můžete dotaz z v rámci virtuálního počítače a přijímat 15 minutové oznámení před restartováním virtuálního počítače.

**Otázka: Jak dlouho vám bude trvat restartování virtuálního počítače?**

**A:**  V závislosti na velikosti virtuálního počítače může restartování trvat až několik minut během okna samoobslužné údržby. Během restartování iniciované Azure v okně plánované údržby restartování obvykle trvá asi 25 minut. Pokud používáte cloudové služby (webová/pracovní role), škálovací sady virtuálních strojů nebo skupiny dostupnosti, budete mít 30 minut mezi každou skupinou virtuálních počítačů (aktualizační doména) během okna plánované údržby. 

**Otázka: Na virtuálních počítačích se nezobrazují žádné informace o údržbě. Co se pokazilo?**

**A:** Existuje několik důvodů, proč se vám na virtuálních počítačích nemusí zobrazit žádné informace o údržbě:
   - Používáte předplatné označené jako *Microsoft Internal*.
   - Vaše virtuální počítače nejsou naplánované na údržbu. Je možné, že vlna údržby skončila, byla zrušena nebo byla upravena tak, aby vaše virtuální počítače již nebyly ovlivněny.
   - Do zobrazení seznamu virtuálních počítače nemáte přidán sloupec **Údržba.** I když jsme tento sloupec přidali do výchozího zobrazení, pokud nakonfigurujete zobrazení tak, aby zobrazovalo nevýchozí sloupce, musíte ručně přidat sloupec **Údržba** do zobrazení seznamu virtuálních počítačí.

**Otázka: Můj virtuální virtuální ms je naplánováno na údržbu podruhé. Proč?**

**A:** V několika případech použití je váš virtuální počítač naplánován na údržbu poté, co jste již dokončili údržbu a znovu nasadíte:
   - Zrušili jsme vlnu údržby a restartovali ji s jiným nákladem. Je možné, že jsme zjistili chybnou datovou část a jednoduše potřebujeme nasadit další datovou část.
   - Váš virtuální počítač byl *služba vyléčena* do jiného uzlu z důvodu selhání hardwaru.
   - Vybrali jste zastavit (navrátit) a restartovat virtuální počítač.
   - Máte pro virtuální počítač zapnuté **automatické vypnutí.**

## <a name="next-steps"></a>Další kroky

Zjistěte, jak se zaregistrovat pro události údržby z virtuálního účtu pomocí [naplánovaných událostí](../virtual-machines/windows/scheduled-events.md).

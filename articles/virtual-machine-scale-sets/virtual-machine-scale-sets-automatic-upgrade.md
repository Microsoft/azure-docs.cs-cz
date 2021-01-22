---
title: Automatické upgrady imagí operačního systému pomocí Azure Virtual Machine Scale Sets
description: Přečtěte si, jak automaticky upgradovat image operačního systému na instancích virtuálních počítačů v sadě škálování.
author: avirishuv
ms.author: avverma
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 06/26/2020
ms.reviewer: jushiman
ms.custom: avverma, devx-track-azurecli
ms.openlocfilehash: ff1a29577c0778d6ef88d3523c726f7a48739cdc
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2021
ms.locfileid: "98684606"
---
# <a name="azure-virtual-machine-scale-set-automatic-os-image-upgrades"></a>Automatické upgrady imagí operačních systémů škálovacích sad virtuálních počítačů

Povolení automatických upgradů bitových kopií operačního systému v sadě škálování pomáhá zjednodušit správu aktualizací tím, že bezpečně a automaticky upgraduje disk s operačním systémem pro všechny instance v sadě škálování.

Automatický upgrade operačního systému má následující vlastnosti:

- Po nakonfigurování se pro sadu škálování bez zásahu uživatele automaticky použije nejnovější bitová kopie operačního systému publikovaná vydavateli imagí.
- Pokaždé, když vydavatel publikuje novou image, upgraduje dávky instancí.
- Integruje se s sondami stavu aplikace a s [rozšířením stavu aplikace](virtual-machine-scale-sets-health-extension.md).
- Funguje pro všechny velikosti virtuálních počítačů a pro image Windows i Linux.
- Automatické upgrady můžete kdykoli odhlásit (upgrady operačního systému je možné iniciovat také ručně).
- Disk s operačním systémem virtuálního počítače se nahradí novým diskem s operačním systémem vytvořeným pomocí nejnovější verze image. Nakonfigurovaná rozšíření a vlastní datové skripty se spouštějí, zatímco jsou zachovány trvalé datové disky.
- [Sekvence rozšíření](virtual-machine-scale-sets-extension-sequencing.md) je podporovaná.
- Automatickou aktualizaci image operačního systému lze povolit v sadě škálování libovolné velikosti.

## <a name="how-does-automatic-os-image-upgrade-work"></a>Jak funguje automatická aktualizace image operačního systému?

Upgrade funguje tak, že nahradíte disk s operačním systémem virtuálního počítače novým diskem vytvořeným pomocí nejnovější verze image. Všechna nakonfigurovaná rozšíření a vlastní datové skripty se spouští na disku s operačním systémem, zatímco jsou zachovány trvalé datové disky. Aby se minimalizoval výpadek aplikace, upgrady probíhají v dávkách, a to bez více než 20% upgradu sady škálování v jednom okamžiku. Můžete také integrovat Azure Load Balancer testování stavu aplikace nebo [rozšíření stavu aplikace](virtual-machine-scale-sets-health-extension.md). Doporučujeme, abyste zavedli prezenční signál aplikace a ověřili úspěšnost upgradu pro každou dávku v procesu upgradu.

Proces upgradu funguje takto:
1. Před zahájením procesu upgradu ověří Orchestrator, že v celé sadě škálování není v pořádku (z jakéhokoli důvodu více než 20% instancí).
2. Upgrade Orchestrator identifikuje dávku instancí virtuálních počítačů, které se mají upgradovat, přičemž každá z nich může mít maximálně 20% z celkového počtu instancí, a to v závislosti na minimální velikosti dávky jednoho virtuálního počítače.
3. Disk s operačním systémem vybrané dávky instancí virtuálních počítačů se nahradí novým diskem s operačním systémem vytvořeným z nejnovější bitové kopie. Všechna zadaná rozšíření a konfigurace v modelu sady škálování se aplikují na upgradovanou instanci.
4. U sady škálování s nakonfigurovanými sondami stavu aplikace nebo rozšířením stavu aplikace čeká upgrade až 5 minut, než se instance stane v pořádku, než se přejde na upgrade další dávky. Pokud instance neobnoví svůj stav za 5 minut po upgradu, pak se ve výchozím nastavení obnoví předchozí disk s operačním systémem pro danou instanci.
5. Upgrade Orchestrator také sleduje procento instancí, které se stanou špatnými po upgradu. Upgrade se zastaví, pokud během procesu upgradu dojde k nesprávnému více než 20% upgradovaných instancí.
6. Výše uvedený proces pokračuje, dokud nebudou upgradovány všechny instance v sadě škálování.

Nástroj Orchestrator pro upgrade operačního systému nástroje pro škálování před upgradem každé dávky kontroluje celkový stav sady škálování. Při upgradu dávky mohou existovat i jiné souběžné plánované nebo neplánované aktivity údržby, které by mohly ovlivnit stav instancí sady škálování. V takových případech, pokud se více než 20% instancí sady škálování stane špatným, se upgrade sady škálování zastaví na konci aktuální dávky.

> [!NOTE]
>Automatický upgrade operačního systému neupgraduje SKU referenčních imagí v sadě škálování. Chcete-li změnit skladovou položku (například Ubuntu 16,04-LTS na 18,04-LTS), je nutné aktualizovat [model sady škálování](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model) přímo s požadovanou SKU image. Vydavatele a nabídku obrázků nelze změnit pro existující sadu škálování.  

## <a name="supported-os-images"></a>Podporované image operačních systémů
V současné době jsou podporovány pouze některé image platformy operačního systému. Vlastní image [se podporují](virtual-machine-scale-sets-automatic-upgrade.md#automatic-os-image-upgrade-for-custom-images) , pokud sada škálování používá vlastní image pomocí [Galerie sdílených imagí](../virtual-machines/shared-image-galleries.md).

V současné době se podporují následující SKU platforem (a pravidelně se přidávají další):

| Publisher               | Nabídka OS      |  Skladová jednotka (SKU)               |
|-------------------------|---------------|--------------------|
| Canonical               | UbuntuServer  | 16.04-LTS          |
| Canonical               | UbuntuServer  | 18,04 – LTS          |
| OpenLogic               | CentOS        | 7,5                |
| MicrosoftWindowsServer  | WindowsServer | 2012-R2-Datacenter |
| MicrosoftWindowsServer  | WindowsServer | 2016 – Datacenter    |
| MicrosoftWindowsServer  | WindowsServer | 2016 – Datacenter – Smalldisk |
| MicrosoftWindowsServer  | WindowsServer | 2016 – Datacenter-with-Containers |
| MicrosoftWindowsServer  | WindowsServer | 2019 – Datacenter |
| MicrosoftWindowsServer  | WindowsServer | 2019 – Datacenter – Smalldisk |
| MicrosoftWindowsServer  | WindowsServer | 2019 – Datacenter-with-Containers |
| MicrosoftWindowsServer  | WindowsServer | Datacenter-Core-1903-with-Containers-smalldisk |


## <a name="requirements-for-configuring-automatic-os-image-upgrade"></a>Požadavky na konfiguraci automatického upgradu imagí operačního systému

- Vlastnost *Version* obrázku musí být nastavena na hodnotu *Poslední*.
- Pro neService Fabricé sady škálování použijte sondy stavu aplikace nebo [rozšíření stavu aplikace](virtual-machine-scale-sets-health-extension.md) .
- Použijte COMPUTE API verze 2018-10-01 nebo vyšší.
- Ujistěte se, že jsou dostupné a aktualizované externí prostředky zadané v modelu sady škálování. Mezi příklady patří identifikátor URI SAS pro spouštěcí datovou část ve vlastnostech rozšíření virtuálního počítače, datovou část účtu úložiště, odkaz na tajné klíče v modelu a další.
- Pro sady škálování s použitím virtuálních počítačů s Windows, počínaje službou COMPUTE API verze 2019-03-01, musí být vlastnost *virtualMachineProfile. osProfile. windowsConfiguration. enableAutomaticUpdates* v definici modelu sady škálování nastavená na *hodnotu false* . Výše uvedená vlastnost umožňuje upgrady ve virtuálním počítači, kde "web Windows Update" aplikuje opravy operačního systému bez nahrazení disku s operačním systémem. Díky automatickým aktualizacím imagí operačního systému povoleným ve vaší sadě škálování se nevyžaduje další aktualizace prostřednictvím "web Windows Update".

### <a name="service-fabric-requirements"></a>Service Fabric požadavky

Pokud používáte Service Fabric, ujistěte se, že jsou splněné následující podmínky:
-   [Úroveň odolnosti](../service-fabric/service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) Service Fabric je stříbrná nebo zlatá a není bronzová.
-   Rozšíření Service Fabric v definici modelu sady škálování musí mít TypeHandlerVersion 1,1 nebo vyšší.
-   Úroveň odolnosti by měla být stejná jako u Service Fabric clusteru a rozšíření Service Fabric v definici modelu sady škálování.
- Další sonda stavu nebo použití rozšíření pro stav aplikace se nevyžaduje.

Zajistěte, aby se nastavení odolnosti neshodovalo s Service Fabric clusterem a rozšířením Service Fabric, protože v důsledku neshody dojde k chybám upgradu. Úrovně trvanlivosti se dají upravovat podle pokynů popsaných na [této stránce](../service-fabric/service-fabric-cluster-capacity.md#changing-durability-levels).


## <a name="automatic-os-image-upgrade-for-custom-images"></a>Automatický upgrade bitové kopie operačního systému pro vlastní image

Automatický upgrade image operačního systému se podporuje pro vlastní image nasazené pomocí [Galerie sdílených imagí](../virtual-machines/shared-image-galleries.md). Další vlastní image nejsou podporované pro automatické upgrady imagí operačního systému.

### <a name="additional-requirements-for-custom-images"></a>Další požadavky na vlastní image
- Proces instalace a konfigurace automatického upgradu image operačního systému je stejný pro všechny sady škálování, jak je popsáno v [části Konfigurace](virtual-machine-scale-sets-automatic-upgrade.md#configure-automatic-os-image-upgrade) této stránky.
- Instance sad škálování nakonfigurované pro automatické upgrady imagí operačního systému se budou upgradovat na nejnovější verzi Image Galerie sdílených imagí, když se publikuje nová verze image a [replikuje](../virtual-machines/shared-image-galleries.md#replication) se do oblasti této sady škálování. Pokud se nová bitová kopie nereplikuje do oblasti, ve které je rozšíření nasazeno, instance sady škálování nebudou upgradovány na nejnovější verzi. Replikace místních imagí vám umožní řídit zavedení nového obrázku pro sady škálování.
- Nová verze Image by se neměla z poslední verze této image galerie vyloučit. Verze imagí vyloučené z poslední verze image galerie se nepočítají do sady škálování prostřednictvím automatického upgradu image operačního systému.

> [!NOTE]
>Může trvat až 3 hodiny, než se sada škálování aktivuje při prvním nakonfigurovaném nastavení škálování pro automatické upgrady operačního systému. Jedná se o jednorázovou prodlevu na sadu škálování. Další uvádění obrázků se aktivují v sadě škálování do 30-60 minut.


## <a name="configure-automatic-os-image-upgrade"></a>Konfigurace automatického upgradu image operačního systému
Pokud chcete nakonfigurovat automatický upgrade operačního systému, zajistěte, aby byla vlastnost *automaticOSUpgradePolicy. enableAutomaticOSUpgrade* v definici modelu sady škálování nastavená na *hodnotu true* .

### <a name="rest-api"></a>REST API
Následující příklad popisuje, jak nastavit automatické upgrady operačního systému na modelu sady škálování:

```
PUT or PATCH on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version=2019-12-01`
```

```json
{
  "properties": {
    "upgradePolicy": {
      "automaticOSUpgradePolicy": {
        "enableAutomaticOSUpgrade":  true
      }
    }
  }
}
```

### <a name="azure-powershell"></a>Azure PowerShell
Pomocí rutiny [Update-AzVmss](/powershell/module/az.compute/update-azvmss) nakonfigurujte automatické upgrady bitových kopií operačního systému pro sadu škálování. Následující příklad konfiguruje automatické upgrady pro sadu škálování s názvem *myScaleSet* ve skupině prostředků s názvem *myResourceGroup*:

```azurepowershell-interactive
Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -AutomaticOSUpgrade $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
K nakonfigurování automatických upgradů bitových kopií operačního systému pro sadu škálování použijte [AZ VMSS Update](/cli/azure/vmss#az-vmss-update) . Použijte rozhraní příkazového řádku Azure CLI 2.0.47 nebo vyšší. Následující příklad konfiguruje automatické upgrady pro sadu škálování s názvem *myScaleSet* ve skupině prostředků s názvem *myResourceGroup*:

```azurecli-interactive
az vmss update --name myScaleSet --resource-group myResourceGroup --set UpgradePolicy.AutomaticOSUpgradePolicy.EnableAutomaticOSUpgrade=true
```

> [!NOTE]
>Po nakonfigurování automatických upgradů bitových kopií operačního systému pro sadu škálování musíte virtuální počítače pro škálování nastavit také na nejnovější model sady škálování, pokud vaše sada škálování používá [zásady upgradu](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)ručně.

## <a name="using-application-health-probes"></a>Používání sond stavu aplikace

Během upgradu operačního systému se instance virtuálních počítačů v sadě škálování upgradují po jednom okamžiku. Upgrade by měl pokračovat pouze v případě, že je aplikace zákazníka v dobrém stavu na upgradovaných instancích virtuálních počítačů. Doporučujeme, aby aplikace poskytovala stavové signály modulu pro upgrade operačního systému s možností škálování. Ve výchozím nastavení se během upgradování operačního systému pro platformu považuje stav napájení virtuálního počítače a stav zřizování rozšíření, aby bylo možné zjistit, jestli je instance virtuálního počítače po upgradu v pořádku. Během upgradu instance virtuálního počítače v operačním systému se disk s operačním systémem v instanci virtuálního počítače nahradí novým diskem na základě nejnovější verze image. Po dokončení upgradu operačního systému se na těchto virtuálních počítačích spouštějí nakonfigurovaná rozšíření. Aplikace se považuje za v pořádku jenom v případě, že se úspěšně zřídí všechna rozšíření instance.

Sada škálování se dá volitelně nakonfigurovat s sondami stavu aplikace a poskytnout tak platformě přesné informace o průběžném stavu aplikace. Sondy stavu aplikace jsou vlastní Load Balancer sondy, které se používají jako signály stavu. Aplikace spuštěná v instanci virtuálního počítače sady škálování může reagovat na externí požadavky HTTP nebo TCP, které označují, jestli jsou v pořádku. Další informace o tom, jak vlastní Load Balancer sondy fungují, najdete v tématu [pochopení sond nástroje pro vyrovnávání zatížení](../load-balancer/load-balancer-custom-probe-overview.md). Sondy stavu aplikací nejsou podporované pro Service Fabric sady škálování. Sady škálování bez Service Fabric vyžadují buď Load Balancer testování stavu aplikace, nebo [rozšíření stavu aplikace](virtual-machine-scale-sets-health-extension.md).

Pokud je sada škálování nakonfigurovaná tak, aby používala více skupin umístění, je nutné použít testy pomocí [Standard Load Balancer](../load-balancer/load-balancer-overview.md) .

### <a name="configuring-a-custom-load-balancer-probe-as-application-health-probe-on-a-scale-set"></a>Konfigurace vlastního testu Load Balancer jako sonda stavu aplikace na sadě škálování
Osvědčeným postupem je vytvoření testu nástroje pro vyrovnávání zatížení pro stav nastavení škálování. Je možné použít stejný koncový bod pro existující sondu HTTP nebo test TCP, ale sonda stavu může vyžadovat odlišné chování z tradičního testu nástroje pro vyrovnávání zatížení. Například tradiční test nástroje pro vyrovnávání zatížení může vracet stav není v pořádku, pokud je zatížení instance příliš vysoké, ale to by nebylo vhodné při určování stavu instance během automatického upgradu operačního systému. Nakonfigurujte test tak, aby měl vysokou frekvenci zjišťování kratší než dvě minuty.

Na modul pro vyrovnávání zatížení se dá odkazovat v *networkProfile* sady škálování a dá se přidružit k internímu nebo veřejnému nástroji pro vyrovnávání zatížení následujícím způsobem:

```json
"networkProfile": {
  "healthProbe" : {
    "id": "[concat(variables('lbId'), '/probes/', variables('sshProbeName'))]"
  },
  "networkInterfaceConfigurations":
  ...
}
```

> [!NOTE]
> Pokud používáte automatické upgrady operačního systému pomocí Service Fabric, je nová image operačního systému zahrnutá v aktualizační doméně podle aktualizační domény, aby se zachovala vysoká dostupnost služeb běžících v Service Fabric. Pokud chcete použít automatické upgrady operačního systému v Service Fabric musíte cluster nakonfigurovat tak, aby používal úroveň odolnosti stříbra nebo vyšší. Další informace o vlastnostech odolnosti Service Fabric clusterech najdete v [této dokumentaci](../service-fabric/service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster).

### <a name="keep-credentials-up-to-date"></a>Udržování přihlašovacích údajů v aktuálním stavu
Pokud vaše sada škálování používá pro přístup k externím prostředkům nějaké přihlašovací údaje, jako je třeba rozšíření virtuálního počítače nakonfigurované pro použití tokenu SAS pro účet úložiště, pak ověřte, že se přihlašovací údaje aktualizují. Pokud vypršela platnost jakýchkoli přihlašovacích údajů, včetně certifikátů a tokenů, upgrade selže a první dávka virtuálních počítačů zůstane ve stavu selhání.

Doporučený postup obnovení virtuálních počítačů a opětovného povolení automatického upgradu operačního systému, pokud dojde k selhání ověřování prostředků:

* Znovu vygenerujte token (nebo jakékoli jiné přihlašovací údaje) předané do vašich rozšíření.
* Ujistěte se, že všechny přihlašovací údaje, které se používají uvnitř virtuálního počítače pro komunikaci s externími entitami, jsou aktuální.
* Aktualizace rozšíření v modelu sady škálování s novými tokeny.
* Nasaďte aktualizovanou sadu škálování, která bude aktualizovat všechny instance virtuálních počítačů, včetně neúspěšných.

## <a name="using-application-health-extension"></a>Používání rozšíření pro stav aplikace
Rozšíření pro stav aplikace se nasadí do instance sady škálování virtuálních počítačů a sestavuje zprávy o stavu virtuálního počítače v rámci instance sady škálování. Můžete nakonfigurovat rozšíření na testování koncového bodu aplikace a aktualizovat stav aplikace v této instanci. Tento stav instance kontroluje Azure, aby zjistil, jestli je instance způsobilá pro operace upgradu.

Jelikož rozšíření hlásí stav z virtuálního počítače, dá se použít v situacích, kdy nejde použít externí testy, jako je třeba sondy stavu aplikací (které využívají vlastní [testy](../load-balancer/load-balancer-custom-probe-overview.md)Azure Load Balancer).

Rozšíření stavu aplikace můžete do sady škálování nasadit několika způsoby, jak je popsáno v příkladech v [tomto článku](virtual-machine-scale-sets-health-extension.md#deploy-the-application-health-extension).

## <a name="get-the-history-of-automatic-os-image-upgrades"></a>Získat historii automatických upgradů imagí operačního systému
Historii nejnovějšího upgradu operačního systému, který se provádí v sadě škálování, můžete ověřit pomocí Azure PowerShell, Azure CLI 2,0 nebo rozhraní REST API. V posledních dvou měsících můžete získat historii za posledních pět pokusů o upgrade operačního systému.

### <a name="rest-api"></a>REST API
Následující příklad používá [REST API](/rest/api/compute/virtualmachinescalesets/getosupgradehistory) ke kontrole stavu sady škálování s názvem *myScaleSet* ve skupině prostředků s názvem *myResourceGroup*:

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/osUpgradeHistory?api-version=2019-12-01`
```

Volání GET vrátí vlastnosti podobné následujícímu příkladu výstupu:

```json
{
    "value": [
        {
            "properties": {
        "runningStatus": {
          "code": "RollingForward",
          "startTime": "2018-07-24T17:46:06.1248429+00:00",
          "completedTime": "2018-04-21T12:29:25.0511245+00:00"
        },
        "progress": {
          "successfulInstanceCount": 16,
          "failedInstanceCount": 0,
          "inProgressInstanceCount": 4,
          "pendingInstanceCount": 0
        },
        "startedBy": "Platform",
        "targetImageReference": {
          "publisher": "MicrosoftWindowsServer",
          "offer": "WindowsServer",
          "sku": "2016-Datacenter",
          "version": "2016.127.20180613"
        },
        "rollbackInfo": {
          "successfullyRolledbackInstanceCount": 0,
          "failedRolledbackInstanceCount": 0
        }
      },
      "type": "Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades",
      "location": "westeurope"
    }
  ]
}
```

### <a name="azure-powershell"></a>Azure PowerShell
Pomocí rutiny [Get-AzVmss](/powershell/module/az.compute/get-azvmss) ověřte historii upgradu operačního systému pro vaši sadu škálování. Následující příklad podrobně popisuje, jak zkontrolovat stav upgradu operačního systému pro sadu škálování s názvem *myScaleSet* ve skupině prostředků s názvem *myResourceGroup*:

```azurepowershell-interactive
Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -OSUpgradeHistory
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Pomocí [AZ VMSS Get-OS-Upgrade-History](/cli/azure/vmss#az-vmss-get-os-upgrade-history) ověřte historii upgradu operačního systému pro vaši sadu škálování. Použijte rozhraní příkazového řádku Azure CLI 2.0.47 nebo vyšší. Následující příklad podrobně popisuje, jak zkontrolovat stav upgradu operačního systému pro sadu škálování s názvem *myScaleSet* ve skupině prostředků s názvem *myResourceGroup*:

```azurecli-interactive
az vmss get-os-upgrade-history --resource-group myResourceGroup --name myScaleSet
```

## <a name="how-to-get-the-latest-version-of-a-platform-os-image"></a>Jak získat nejnovější verzi image operačního systému?

Dostupné verze bitových kopií pro automatické aktualizace operačního systému (SKU) můžete získat pomocí níže uvedených příkladů:

### <a name="rest-api"></a>REST API
```
GET on `/subscriptions/subscription_id/providers/Microsoft.Compute/locations/{location}/publishers/{publisherName}/artifacttypes/vmimage/offers/{offer}/skus/{skus}/versions?api-version=2019-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
```azurepowershell-interactive
Get-AzVmImage -Location "westus" -PublisherName "Canonical" -Offer "UbuntuServer" -Skus "16.04-LTS"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
```azurecli-interactive
az vm image list --location "westus" --publisher "Canonical" --offer "UbuntuServer" --sku "16.04-LTS" --all
```

## <a name="manually-trigger-os-image-upgrades"></a>Ručně aktivovat upgrady imagí operačního systému
Když je ve vaší sadě škálování povolený automatický upgrade image operačního systému, nemusíte v sadě škálování ručně spouštět aktualizace obrázků. Nástroj Orchestrator upgrade OS automaticky nainstaluje nejnovější dostupnou verzi image na instance sady škálování bez nutnosti ručního zásahu.

Pro konkrétní případy, kdy nechcete čekat, až nástroj Orchestrator použije nejnovější bitovou kopii, můžete upgradovat bitovou kopii operačního systému ručně pomocí níže uvedených příkladů.

> [!NOTE]
> Ruční aktivační událost pro upgrady bitových kopií operačního systému neposkytuje funkce automatického vrácení zpět. Pokud instance po operaci upgradu neobnoví svůj stav, nebude možné obnovit předchozí disk s operačním systémem.

### <a name="rest-api"></a>REST API
Pomocí volání rozhraní API [Spustit upgrade operačního systému](/rest/api/compute/virtualmachinescalesetrollingupgrades/startosupgrade) můžete spustit postupný upgrade, abyste přesunuli všechny instance sady škálování virtuálních počítačů na nejnovější dostupnou verzi operačního systému image. Instance, ve kterých je již spuštěna nejnovější dostupná verze operačního systému, nebudou ovlivněny. Následující příklad podrobně popisuje, jak můžete spustit upgrade operačního systému v rámci sady škálování s názvem *myScaleSet* ve skupině prostředků s názvem *myResourceGroup*:

```
POST on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/osRollingUpgrade?api-version=2019-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Pomocí rutiny [Start-AzVmssRollingOSUpgrade](/powershell/module/az.compute/Start-AzVmssRollingOSUpgrade) ověřte historii upgradu operačního systému pro vaši sadu škálování. Následující příklad podrobně popisuje, jak můžete spustit upgrade operačního systému v rámci sady škálování s názvem *myScaleSet* ve skupině prostředků s názvem *myResourceGroup*:

```azurepowershell-interactive
Start-AzVmssRollingOSUpgrade -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Pokud chcete zjistit historii upgradu operačního systému pro vaši sadu škálování, použijte příkaz AZ VMSS provedený [upgrade Start](/cli/azure/vmss/rolling-upgrade#az-vmss-rolling-upgrade-start) . Použijte rozhraní příkazového řádku Azure CLI 2.0.47 nebo vyšší. Následující příklad podrobně popisuje, jak můžete spustit upgrade operačního systému v rámci sady škálování s názvem *myScaleSet* ve skupině prostředků s názvem *myResourceGroup*:

```azurecli-interactive
az vmss rolling-upgrade start --resource-group "myResourceGroup" --name "myScaleSet" --subscription "subscriptionId"
```

## <a name="deploy-with-a-template"></a>Nasazení s využitím šablony

Pomocí šablon můžete nasadit sadu škálování s automatickými upgrady operačního systému pro podporované image, jako je například [Ubuntu 16,04-LTS](https://github.com/Azure/vm-scale-sets/blob/master/preview/upgrade/autoupdate.json).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fvm-scale-sets%2Fmaster%2Fpreview%2Fupgrade%2Fautoupdate.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png" alt="Button to Deploy to Azure." /></a>

## <a name="next-steps"></a>Další kroky
Další příklady, jak používat automatické upgrady operačního systému se sadami škálování, najdete v [úložišti GitHub](https://github.com/Azure/vm-scale-sets/tree/master/preview/upgrade).
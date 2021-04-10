---
title: Automatické opravy instancí pomocí Azure Virtual Machine Scale Sets
description: Přečtěte si, jak nakonfigurovat zásady automatických oprav pro instance virtuálních počítačů v sadě škálování.
author: avirishuv
ms.author: avverma
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: instance-protection
ms.date: 02/28/2020
ms.reviewer: jushiman
ms.custom: avverma, devx-track-azurecli
ms.openlocfilehash: b43502e771415c0ca62d821c697516fae16e35ce
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105934525"
---
# <a name="automatic-instance-repairs-for-azure-virtual-machine-scale-sets"></a>Automatické opravy instancí pro škálovací sady virtuálních počítačů Azure

Povolení automatických oprav instancí pro Azure Virtual Machine Scale Sets vám pomůže dosáhnout vysoké dostupnosti pro aplikace tím, že se zachová sada zdravých instancí. Pokud je zjištěna instance v sadě škálování, která je hlášena z [rozšíření pro stav aplikace](./virtual-machine-scale-sets-health-extension.md) nebo [sondy stavu nástroje pro vyrovnávání zatížení](../load-balancer/load-balancer-custom-probe-overview.md), pak tato funkce automaticky provádí opravu instance odstraněním instance, která není v pořádku, a vytvořením nového pro její nahrazení.

## <a name="requirements-for-using-automatic-instance-repairs"></a>Požadavky na používání automatických oprav instancí

**Povolit monitorování stavu aplikací pro sadu škálování**

Sada škálování by měla mít povolený monitoring stavu aplikace pro instance. To se dá udělat buď pomocí [rozšíření stavu aplikace](./virtual-machine-scale-sets-health-extension.md) , nebo [sondy stavu nástroje pro vyrovnávání zatížení](../load-balancer/load-balancer-custom-probe-overview.md). V jednom okamžiku může být povolena pouze jedna z nich. Nástroj pro zjištění stavu aplikace nebo nástroj pro vyrovnávání zatížení otestuje koncový bod aplikace nakonfigurovaný na instancích virtuálních počítačů za účelem určení stavu aplikace. Tento stav používá nástroj Orchestrator set Orchestrator k monitorování stavu instance a v případě potřeby provede opravy.

**Nakonfigurovat koncový bod tak, aby poskytoval stav**

Předtím, než povolíte zásady automatických oprav instancí, zajistěte, aby instance sady škálování měly koncový bod aplikace nakonfigurovaný pro generování stavu aplikace. Když instance vrátí stav 200 (OK) na tomto koncovém bodu aplikace, bude tato instance označena jako "v pořádku". Ve všech ostatních případech je instance označená jako "není v pořádku", včetně následujících scénářů:

- Pokud v instancích virtuálních počítačů není nakonfigurovaný koncový bod aplikace, který by poskytoval stav aplikace
- Pokud je koncový bod aplikace nesprávně nakonfigurován
- Pokud koncový bod aplikace není dosažitelný

U instancí označených jako "není v pořádku" jsou automatické opravy aktivovány sadou škálování. Než povolíte zásady automatických oprav, ujistěte se, že je koncový bod aplikace správně nakonfigurovaný, aby nedocházelo k nezamýšleným opravám instancí, zatímco koncový bod se nakonfiguruje.

**Maximální počet instancí v sadě škálování**

Tato funkce je aktuálně dostupná jenom pro sady škálování, které mají maximálně 200 instancí. Sada škálování se dá nasadit buď jako jediná skupina umístění, nebo do skupiny s více umístěními, ale počet instancí nemůže být vyšší než 200, pokud je pro sadu škálování povolená Automatická oprava instancí.

**Verze rozhraní API**

Pro rozhraní COMPUTE API verze 2018-10-01 nebo vyšší se podporují zásady pro automatické opravy.

**Omezení počtu přesunutí prostředků nebo předplatného**

Přesuny prostředků nebo předplatného se pro sady škálování v tuto chvíli nepodporují, pokud je povolená funkce Automatické opravy.

**Omezení pro sadu Service Fabric Scale Sets**

Tato funkce není v současnosti pro sadu škálování Service Fabric podporována.

## <a name="how-do-automatic-instance-repairs-work"></a>Jak automatické opravy instancí fungují?

Funkce Automatické opravy instancí spoléhá na monitorování stavu jednotlivých instancí v sadě škálování. Instance virtuálních počítačů v sadě škálování je možné nakonfigurovat tak, aby vygenerovaly stav aplikace, a to pomocí [rozšíření stavu aplikace](./virtual-machine-scale-sets-health-extension.md) nebo [sondy stavu nástroje pro vyrovnávání zatížení](../load-balancer/load-balancer-custom-probe-overview.md). Pokud se zjistí, že instance není v pořádku, pak sada škálování provede akci opravy tím, že odstraní instanci, která není v pořádku, a vytvoří novou, aby se nahradila. K vytvoření nové instance se používá nejnovější model sady škálování virtuálního počítače. Tato funkce se dá povolit v modelu sady škálování virtuálních počítačů pomocí objektu *automaticRepairsPolicy* .

### <a name="batching"></a>Dávkování

Automatizované operace opravy instancí se provádějí v dávkách. V jednom okamžiku se v rámci zásad automatických oprav neopraví víc než 5% instancí v sadě škálování. To pomáhá zabránit současnému odstranění a opětovnému vytvoření velkého počtu instancí, pokud se ve stejnou dobu nenajde stav není v pořádku.

### <a name="grace-period"></a>Období odkladu

Když instance projde pomocí operace změny stavu z důvodu akce PUT, PATCH nebo POST provedené v sadě škálování (například obnovení bitové kopie, opětovné nasazení, aktualizace atd.), bude jakákoli akce opravy v této instanci provedena až po čekání na dobu odkladu. Období odkladu je doba, po kterou se má instance vrátit do stavu v pořádku. Období odkladu začíná po dokončení změny stavu. To pomáhá zabránit jakýmkoli předčasnému nebo náhodnému opravení. Doba odkladu se dodržuje u všech nově vytvořených instancí v sadě škálování (včetně toho, která se vytvořila v důsledku operace opravy). Období odkladu je zadáno v minutách ve formátu ISO 8601 a lze ho nastavit pomocí vlastnosti *automaticRepairsPolicy. gracePeriod*. Období odkladu může být v rozmezí 30 minut a 90 minut a má výchozí hodnotu 30 minut.

### <a name="suspension-of-repairs"></a>Pozastavení oprav 

Virtual Machine Scale Sets nabízí možnost dočasného pozastavení automatických oprav instancí v případě potřeby. *ServiceState* pro automatické opravy pod vlastností *orchestrationServices* v zobrazení instance sady škálování virtuálního počítače zobrazuje aktuální stav automatických oprav. Pokud je nastavená škála pro automatické opravy, hodnota parametru *serviceState* je nastavená na *spuštěno*. Při pozastavení automatických oprav pro sadu škálování se parametr *serviceState* nastaví na *pozastaveno*. Je-li v sadě škálování definováno *automaticRepairsPolicy* , ale funkce Automatické opravy není povolená, parametr *serviceState* je nastaven na *nespuštěno*.

Pokud nově vytvořené instance pro nahrazení stavů, které nejsou v pořádku, zůstávají v sadě škálování stále v pořádku i po opakovaném provádění operací opravy, a pak jako bezpečnostní opatření, které platforma aktualizuje *serviceState* , aby se automatické opravy *pozastavily*. Automatické opravy můžete znovu obnovit nastavením hodnoty *serviceState* pro automatické opravy, které se mají *Spustit*. Podrobné pokyny najdete v části [zobrazení a aktualizace stavu služby automatických oprav zásad](#viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy) pro vaši sadu škálování. 

Automatické opravy instancí: proces funguje takto:

1. Test stavu [aplikace](./virtual-machine-scale-sets-health-extension.md) nebo [sondy stavu nástroje pro vyrovnávání zatížení](../load-balancer/load-balancer-custom-probe-overview.md) : Otestujte koncový bod aplikace v každém virtuálním počítači v sadě škálování tak, aby se pro každou instanci získal stav aplikací.
2. Pokud koncový bod odpoví na stav 200 (OK), pak je tato instance označená jako "v pořádku". Ve všech ostatních případech (včetně nedostupnosti koncového bodu) je instance označena jako "není v pořádku".
3. Pokud je zjištěno, že instance není v pořádku, sada škálování aktivuje akci opravy odstraněním instance, která není v pořádku, a vytvořením nového pro její nahrazení.
4. Opravy instancí se provádějí v dávkách. V jednom okamžiku se opraví maximálně 5% z celkového počtu instancí v sadě škálování. Pokud má sada škálování méně než 20 instancí, opravy se provádějí pro jednu instanci, která není v pořádku.
5. Výše uvedený proces pokračuje, dokud nebudou všechny instance, které jsou v pořádku, v sadě škálování opraveny.

## <a name="instance-protection-and-automatic-repairs"></a>Ochrana instancí a automatické opravy

Pokud je instance v sadě škálování chráněná použitím jedné ze [zásad ochrany](./virtual-machine-scale-sets-instance-protection.md), neprovádí se u této instance automatické opravy. To platí pro zásady ochrany: *Ochrana před škálováním* a *Ochrana před akcemi sady škálování* . 

## <a name="terminatenotificationandautomaticrepairs"></a>Ukončení oznámení a automatických oprav

Pokud je funkce [ukončení oznámení](./virtual-machine-scale-sets-terminate-notification.md) v sadě škálování povolená, pak během operace automatické opravy se při odstranění instance, která není v pořádku, provede konfigurace oznámení ukončení. Oznámení o ukončení se odesílá prostřednictvím služby Azure metadata Service – naplánované události a odstranění instance se zpozdí po dobu trvání nakonfigurovaného časového limitu zpoždění. Vytvoření nové instance, která by nahradila stav není v pořádku, ale nečeká na dokončení časového limitu prodlevy.

## <a name="enabling-automatic-repairs-policy-when-creating-a-new-scale-set"></a>Povolení zásad automatických oprav při vytváření nové sady škálování

Aby bylo možné povolit automatické opravy při vytváření nové sady škálování, ujistěte se, že jsou splněné všechny [požadavky](#requirements-for-using-automatic-instance-repairs) pro zapínání s touto funkcí. Koncový bod aplikace by měl být správně nakonfigurovaný pro instance sady škálování, aby nedocházelo k nezamýšleným opravám během nakonfigurovaného koncového bodu. U nově vytvořených sad škálování se všechny opravy instancí provádějí až po uplynutí doby trvání odkladu. Pokud chcete povolit automatickou opravu instancí v sadě škálování, použijte objekt *automaticRepairsPolicy* v modelu sady škálování virtuálního počítače.

Tuto [šablonu pro rychlý Start](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-automatic-repairs-slb-health-probe) můžete také použít k nasazení sady škálování virtuálních počítačů s sondou stavu nástroje pro vyrovnávání zatížení a automatických oprav instancí povolených s dobou odkladu 30 minut.

### <a name="azure-portal"></a>portál Azure
 
Následující kroky umožňují při vytváření nové sady škálování povolit zásady automatických oprav.
 
1. Přejít na **Virtual Machine Scale Sets**.
1. Vyberte **+ Přidat** a vytvořte novou sadu škálování.
1. Přejít na kartu **stav** . 
1. Vyhledejte část **stav** .
1. Povolte možnost **monitorovat stav aplikace** .
1. Vyhledejte oddíl **zásady automatických oprav** .
1. Zapněte možnost **automatických oprav** . 
1. V části lhůta **odkladu (min)** zadejte dobu odkladu v minutách, povolené hodnoty jsou mezi 30 a 90 minutami. 
1. Až budete hotovi s vytvářením nové sady škálování, vyberte tlačítko **zkontrolovat + vytvořit** .

### <a name="rest-api"></a>REST API

Následující příklad ukazuje, jak povolit automatickou opravu instancí v modelu sady škálování. Použijte rozhraní API verze 2018-10-01 nebo vyšší.

```
PUT or PATCH on '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-07-01'
```

```json
{
  "properties": {
    "automaticRepairsPolicy": {
            "enabled": "true",
            "gracePeriod": "PT30M"
        }
    }
}
```

### <a name="azure-powershell"></a>Azure PowerShell

Funkce Automatické opravy instancí se dá povolit při vytváření nové sady škálování pomocí rutiny [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) . Tento vzorový skript vás provede vytvořením sady škálování a přidružených prostředků pomocí konfiguračního souboru: [vytvořte úplnou sadu škálování virtuálního počítače](./scripts/powershell-sample-create-complete-scale-set.md). Zásadu automatické opravy instancí můžete nakonfigurovat přidáním parametrů *EnableAutomaticRepair* a *AutomaticRepairGracePeriod* do objektu konfigurace pro vytvoření sady škálování. Následující příklad povoluje funkci s obdobím odkladu 30 minut.

```azurepowershell-interactive
New-AzVmssConfig `
 -Location "EastUS" `
 -SkuCapacity 2 `
 -SkuName "Standard_DS2" `
 -UpgradePolicyMode "Automatic" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT30M"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

Následující příklad povoluje zásadu automatické opravy při vytváření nové sady škálování pomocí funkce *[AZ VMSS Create](/cli/azure/vmss#az-vmss-create)*. Nejdřív vytvořte skupinu prostředků a pak vytvořte novou sadu škálování s automatickými opravami doba odkladu, která je nastavená na 30 minut.

```azurecli-interactive
az group create --name <myResourceGroup> --location <VMSSLocation>
az vmss create \
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --image UbuntuLTS \
  --admin-username <azureuser> \
  --generate-ssh-keys \
  --load-balancer <existingLoadBalancer> \
  --health-probe <existingHealthProbeUnderLoaderBalancer> \
  --automatic-repairs-grace-period 30
```

Výše uvedený příklad používá existující Nástroj pro vyrovnávání zatížení a sondu stavu pro monitorování stavu instance aplikace. Pokud místo toho chcete použít rozšíření pro stav aplikace pro monitorování, můžete vytvořit sadu škálování, nakonfigurovat rozšíření stavu aplikace a pak povolit zásadu automatických oprav instancí pomocí funkce *AZ VMSS Update*, jak je vysvětleno v další části.

## <a name="enabling-automatic-repairs-policy-when-updating-an-existing-scale-set"></a>Povolení zásad automatických oprav při aktualizaci existující sady škálování

Než povolíte funkci Automatické opravy v existující sadě škálování, ujistěte se, že jsou splněné všechny [požadavky](#requirements-for-using-automatic-instance-repairs) pro zapínání s touto funkcí. Koncový bod aplikace by měl být správně nakonfigurovaný pro instance sady škálování, aby nedocházelo k nezamýšleným opravám během nakonfigurovaného koncového bodu. Pokud chcete povolit automatickou opravu instancí v sadě škálování, použijte objekt *automaticRepairsPolicy* v modelu sady škálování virtuálního počítače.

Po aktualizaci modelu existující sady škálování zajistěte, aby byl na všechny instance stupnice použit nejnovější model. Informace o [tom, jak přenést virtuální počítače do aktuálního stavu pomocí nejnovějšího modelu sady škálování](./virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)

### <a name="azure-portal"></a>portál Azure

Zásady automatických oprav existující sady škálování můžete upravit pomocí Azure Portal. 
 
1. Přejít na existující sadu škálování virtuálního počítače.
1. V nabídce **Nastavení** vlevo vyberte **stav a opravit**.
1. Povolte možnost **monitorovat stav aplikace** .
1. Vyhledejte oddíl **zásady automatických oprav** .
1. Zapněte možnost **automatických oprav** . 
1. V části lhůta **odkladu (min)** zadejte dobu odkladu v minutách, povolené hodnoty jsou mezi 30 a 90 minutami. 
1. Po dokončení vyberte **Uložit**. 

### <a name="rest-api"></a>REST API

Následující příklad povoluje zásadu s obdobím odkladu 40 minut. Použijte rozhraní API verze 2018-10-01 nebo vyšší.

```
PUT or PATCH on '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-07-01'
```

```json
{
  "properties": {
    "automaticRepairsPolicy": {
            "enabled": "true",
            "gracePeriod": "PT40M"
        }
    }
}
```

### <a name="azure-powershell"></a>Azure PowerShell

Pomocí rutiny [Update-AzVmss](/powershell/module/az.compute/update-azvmss) Upravte konfiguraci funkce Automatické opravy instancí v existující sadě škálování. Následující příklad aktualizuje dobu odkladu na 40 minut.

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT40M"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

Následuje příklad aktualizace zásad automatických oprav instancí existující sady škálování pomocí funkce *[AZ VMSS Update](/cli/azure/vmss#az-vmss-update)*.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --enable-automatic-repairs true \
  --automatic-repairs-grace-period 30
```

## <a name="viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy"></a>Zobrazení a aktualizace stavu služby pro zásady automatických oprav instancí

### <a name="rest-api"></a>REST API 

Pomocí funkce [získat zobrazení instance](/rest/api/compute/virtualmachinescalesets/getinstanceview) s rozhraním API verze 2019-12-01 nebo vyšší pro sadu škálování virtuálního počítače můžete zobrazit *serviceState* pro automatické opravy v rámci vlastnosti *orchestrationServices*. 

```http
GET '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/instanceView?api-version=2019-12-01'
```

```json
{
  "orchestrationServices": [
    {
      "serviceName": "AutomaticRepairs",
      "serviceState": "Running"
    }
  ]
}
```

Pro nastavení stavu automatických oprav použijte rozhraní API *setOrchestrationServiceState* s rozhraním api verze 2019-12-01 nebo vyšší na sadě škálování virtuálního počítače. Až se sada škálování zařadí do funkce Automatické opravy, můžete toto rozhraní API použít k pozastavení nebo obnovení automatických oprav pro sadu škálování. 

 ```http
 POST '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/setOrchestrationServiceState?api-version=2019-12-01'
 ```

```json
{
  "orchestrationServices": [
    {
      "serviceName": "AutomaticRepairs",
      "serviceState": "Suspend"
    }
  ]
}
```

### <a name="azure-cli"></a>Azure CLI 

Pomocí rutiny [Get-instance-View](/cli/azure/vmss#az-vmss-get-instance-view) zobrazte *serviceState* pro automatické opravy instancí. 

```azurecli-interactive
az vmss get-instance-view \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```

Pomocí rutiny [set-Orchestration-Service-State](/cli/azure/vmss#az-vmss-set-orchestration-service-state) aktualizujte *serviceState* pro automatické opravy instancí. Až se sada škálování přiřadí do funkce Automatické opravy, můžete tuto rutinu použít k pozastavení nebo obnovení automatických oprav pro sadu škálování. 

```azurecli-interactive
az vmss set-orchestration-service-state \
    --service-name AutomaticRepairs \
    --action Resume \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```
### <a name="azure-powershell"></a>Azure PowerShell

Pomocí rutiny [Get-AzVmss](/powershell/module/az.compute/get-azvmss) s parametrem *InstanceView* můžete zobrazit *servicestate* pro automatické opravy instancí.

```azurepowershell-interactive
Get-AzVmss `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -InstanceView
```

Pomocí rutiny Set-AzVmssOrchestrationServiceState aktualizujte *serviceState* pro automatické opravy instancí. Až se sada škálování přiřadí do funkce Automatické opravy, můžete tuto rutinu použít k pozastavení nebo obnovení automatických oprav pro sadu škálování.

```azurepowershell-interactive
Set-AzVmssOrchestrationServiceState `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -ServiceName "AutomaticRepairs" `
    -Action "Suspend"
```

## <a name="troubleshoot"></a>Řešení potíží

**Nepovedlo se povolit zásady automatických oprav.**

Pokud se zobrazí chyba "důvodu chybného požadavku" se zprávou "nebylo možné najít člena" automaticRepairsPolicy "u objektu typu" Properties "", zkontrolujte verzi rozhraní API použitou pro sadu škálování virtuálního počítače. Pro tuto funkci se vyžaduje rozhraní API verze 2018-10-01 nebo vyšší.

**Instance není opravená, i když je povolená zásada.**

Instance může být v období odkladu. To je doba, po kterou se má čekat po změně stavu instance před provedením oprav. To se vyhnete jakýmkoliv předčasnému nebo náhodnému opravám. Akce opravy by se měla stát po dokončení období odkladu pro instanci.

**Zobrazení stavu aplikace pro instance sady škálování**

K zobrazení stavu aplikace můžete použít [rozhraní API pro zobrazení](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) instancí v sadě škálování virtuálního počítače. Pomocí Azure PowerShell můžete rutinu [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) použít s příznakem *-InstanceView* . Stav aplikace je k dispozici pod vlastností *vmHealth*.

V Azure Portal vidíte i stav také. Přejděte do existující sady škálování, v nabídce vlevo vyberte **instance** a podívejte se na sloupec **stav** a vyhledejte stav každé instance sady škálování. 

## <a name="next-steps"></a>Další kroky

Naučte se konfigurovat testy stavu [aplikací](./virtual-machine-scale-sets-health-extension.md) nebo [sondy stavu nástroje pro vyrovnávání zatížení](../load-balancer/load-balancer-custom-probe-overview.md) pro vaše sady škálování.

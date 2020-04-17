---
title: Automatické opravy instancí pomocí škálovacích sad virtuálních počítačů Azure
description: Zjistěte, jak nakonfigurovat zásady automatických oprav pro instance virtuálních počítačů ve škálovací sadě
author: avirishuv
manager: vashan
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: avverma
ms.openlocfilehash: 8e73ef75b3313656b45d29270d9996c3ad17c630
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538065"
---
# <a name="automatic-instance-repairs-for-azure-virtual-machine-scale-sets"></a>Automatické opravy instancí pro škálovací sady virtuálních počítačů Azure

Povolení automatických oprav instancí pro škálovací sady virtuálních počítačů Azure pomáhá dosáhnout vysoké dostupnosti pro aplikace udržováním sady zdravých instancí. Pokud instance v škálovací sadě je shledána jako nefunkční, jak je uvedeno [rozšířením stavu aplikace](./virtual-machine-scale-sets-health-extension.md) nebo [sondami stavu vyrovnávání zatížení](../load-balancer/load-balancer-custom-probe-overview.md), pak tato funkce automaticky provede opravu instance odstraněním instance není v pořádku a vytvořením nové instance, která ji nahradí.

## <a name="requirements-for-using-automatic-instance-repairs"></a>Požadavky na automatické opravy instancí

**Povolení monitorování stavu aplikace pro škálovací sadu**

Škálovací sada by měla mít povoleno monitorování stavu aplikace pro instance. To lze provést pomocí [rozšíření stavu aplikace](./virtual-machine-scale-sets-health-extension.md) nebo [sondy stavu vyrovnávání zatížení](../load-balancer/load-balancer-custom-probe-overview.md). Současně lze povolit pouze jednu z nich. Rozšíření stavu aplikace nebo nástroj pro vyrovnávání zatížení sonduje ping koncový bod aplikace nakonfigurovaný na instancích virtuálních strojů k určení stavu aplikace. Tento stav ový stav používá orchestrátor škálovací sady ke sledování stavu instance a provádění oprav v případě potřeby.

**Konfigurace koncového bodu pro poskytování stavu**

Před povolením zásad automatických oprav instancí se ujistěte, že instance škálovací sady mají koncový bod aplikace nakonfigurovaný tak, aby vyzařoval stav aplikace. Když instance vrátí stav 200 (OK) v tomto koncovém bodě aplikace, pak je instance označena jako "V pořádku". Ve všech ostatních případech je instance označena jako "Není v pořádku", včetně následujících scénářů:

- Pokud není v instancích virtuálního počítače nakonfigurován žádný koncový bod aplikace, který by poskytoval stav aplikace
- Pokud je koncový bod aplikace nesprávně nakonfigurován
- Pokud koncový bod aplikace není dostupný

V případech označených jako "Není v pořádku", automatické opravy jsou spuštěny škálovací sady. Ujistěte se, že koncový bod aplikace je správně nakonfigurován před povolením zásadautomatických oprav, aby se zabránilo nechtěné opravy instance, zatímco koncový bod je stále konfigurován.

**Povolení skupiny jednoho umístění**

Tato funkce je v současné době k dispozici pouze pro škálovací sady nasazené jako skupina s jedním umístěním. Vlastnost *singlePlacementGroup* by měla být nastavena na *hodnotu true* pro vaši škálovací sadu tak, aby používala funkci automatických oprav instancí. Přečtěte si další informace o [skupinách umístění](./virtual-machine-scale-sets-placement-groups.md#placement-groups).

**Verze rozhraní API**

Zásady automatických oprav jsou podporované pro výpočetní rozhraní API verze 2018-10-01 nebo vyšší.

**Omezení přesunů prostředků nebo předplatného**

Přesuny prostředků nebo odběrů nejsou aktuálně podporovány pro škálovací sady, pokud je povolena funkce automatických oprav.

**Omezení pro sady měřítek výrobních kapacit service fabric**

Tato funkce není aktuálně podporována pro sady škálovacích struktur service fabric.

## <a name="how-do-automatic-instance-repairs-work"></a>Jak fungují automatické opravy instancí?

Funkce automatické opravy instancí závisí na monitorování stavu jednotlivých instancí v škálovací sadě. Instance virtuálních počítače ve škálovací sadě lze nakonfigurovat tak, aby vyzařovalo stav aplikace pomocí [rozšíření Stavu aplikace](./virtual-machine-scale-sets-health-extension.md) nebo sond stavu [nástrojpro vyrovnávání zatížení](../load-balancer/load-balancer-custom-probe-overview.md). Pokud je instance shledána nefunkční, pak škálovací sada provede opravu odstraněním instance není v pořádku a vytvořením nové instance, která ji nahradí. K vytvoření nové instance se používá nejnovější model škálovací sady virtuálních strojů. Tuto funkci lze povolit v modelu škálovací sady virtuálních počítačů pomocí *objektu automaticRepairsPolicy.*

### <a name="batching"></a>Dávkování

Automatické operace opravy instance jsou prováděny v dávkách. V daném okamžiku není více než 5 % případů v škálovací sadě opraveno prostřednictvím zásad automatických oprav. To pomáhá zabránit současnému odstranění a opětovnému vytvoření velkého počtu instancí, pokud jsou nalezeny současně nefunkční.

### <a name="grace-period"></a>Období odkladu

Když instance prochází operace změny stavu z důvodu PUT, PATCH nebo POST akce provedené na škálovací sady (například reimage, redeploy, update, atd.), pak všechny opravy akce na této instanci se provádí pouze po čekání na období odkladu. Období odkladu je doba, po kterou se instance může vrátit do stavu V pořádku. Období odkladu začíná po dokončení změny stavu. To pomáhá zabránit předčasným nebo náhodným opravním. Doba odkladu je dodržena pro všechny nově vytvořené instance v škálovací sadě (včetně té vytvořené v důsledku operace opravy). Období odkladu je specifikováno v minutách ve formátu ISO 8601 a lze jej nastavit pomocí vlastnosti *automaticRepairsPolicy.gracePeriod*. Doba odkladu se může pohybovat mezi 30 minutami a 90 minutami a má výchozí hodnotu 30 minut.

### <a name="suspension-of-repairs"></a>Pozastavení oprav 

Škálovací sady virtuálních počítačů poskytují možnost dočasně pozastavit automatické opravy instancí v případě potřeby. *ServiceState* pro automatické opravy v rámci vlastnosti *orchestraceServices* v instanci zobrazení škálovací sady virtuálního počítače zobrazuje aktuální stav automatických oprav. Pokud škálovací sada je přihlášen a automatické opravy, hodnota parametr *serviceState* je nastavena na *Spuštěno*. Pokud jsou automatické opravy pozastaveny pro škálovací sadu, parametr *serviceState* je nastavena na *Pozastaveno*. Pokud *automatickéRepairsPolicy* je definována na škálovací sadu, ale funkce automatické opravy není povolena, pak parametr *serviceState* je nastavena na *není spuštěna*.

Pokud nově vytvořené instance pro nahrazení není v pořádku v škálovací sadě i nadále nefunkční i po opakovaném provádění operací opravy, pak jako bezpečnostní opatření platforma aktualizuje *serviceState* pro automatické opravy *pozastaveno*. Automatické opravy můžete znovu obnovit nastavením hodnoty *serviceState* pro automatické opravy *spuštěno*. Podrobné pokyny jsou uvedeny v části o [zobrazení a aktualizaci stavu služby automatických oprav](#viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy) pro vaši škálovací sadu. 

Proces automatických oprav instancí funguje takto:

1. [Rozšíření stavu aplikace](./virtual-machine-scale-sets-health-extension.md) nebo [sondy stavu nástroje pro vyrovnávání zatížení](../load-balancer/load-balancer-custom-probe-overview.md) ping koncového bodu aplikace uvnitř každého virtuálního počítače ve škálovací sadě, aby získaly stav aplikace pro každou instanci.
2. Pokud koncový bod odpoví se stavem 200 (OK), pak je instance označena jako "V pořádku". Ve všech ostatních případech (včetně pokud koncový bod je nedostupný), instance je označena "Není v pořádku".
3. Pokud je instance shledána nefunkční, škálovací sada spustí akci opravy odstraněním instance není v pořádku a vytvořením nové instance, která ji nahradí.
4. Opravy instance se provádějí v dávkách. V daném okamžiku není opraveno více než 5 % celkových instancí v škálovací sadě. Pokud škálovací sada má méně než 20 instancí, opravy jsou prováděny pro jednu instanci není v pořádku najednou.
5. Výše uvedený proces pokračuje, dokud nejsou opraveny všechny instance není v pořádku v škálovací sadě.

## <a name="instance-protection-and-automatic-repairs"></a>Ochrana instance a automatické opravy

Pokud je instance v škálovací sadě chráněna použitím jedné ze [zásad ochrany](./virtual-machine-scale-sets-instance-protection.md), nebudou v této instanci provedeny automatické opravy. To platí pro obě zásady ochrany: *Chraňte před škálovat na škálování* a *chránit před akcemi škálování.* 

## <a name="terminatenotificationandautomaticrepairs"></a>Ukončit oznámení a automatické opravy

Pokud je funkce [ukončit oznámení](./virtual-machine-scale-sets-terminate-notification.md) povolena na škálovací sadě, pak během automatické opravy, odstranění instance není v pořádku následuje konfigurace ukončit oznámení. Oznámení o ukončení se odesílá prostřednictvím služby metadat Azure – naplánované události – a odstranění instance je zpožděno po dobu trvání nakonfigurovaného časového času zpoždění. Vytvoření nové instance nahradit není v pořádku jeden však nečeká na dokončení časového režimu zpoždění.

## <a name="enabling-automatic-repairs-policy-when-creating-a-new-scale-set"></a>Povolení zásad automatických oprav při vytváření nové škálovací sady

Chcete-li povolit zásady automatických oprav při vytváření nové škálovací sady, ujistěte se, že jsou splněny všechny [požadavky](#requirements-for-using-automatic-instance-repairs) na přihlášení k této funkci. Koncový bod aplikace by měl být správně nakonfigurován pro instance škálovací sady, aby se zabránilo spuštění nechtěných oprav při konfiguraci koncového bodu. U nově vytvořených škálovacích sad se všechny opravy instancí provádějí až po uplynutí doby odkladu. Chcete-li povolit automatickou opravu instance v škálovací sadě, použijte objekt *automaticRepairsPolicy* v modelu škálovací sady virtuálních počítačů.

### <a name="azure-portal"></a>portál Azure
 
Následující kroky umožňující automatické opravy zásady při vytváření nové škálovací sady.
 
1. Přejděte na **Škálovací sady virtuálních strojů**.
1. Výběrem **možnosti + Přidat** vytvoříte novou škálovací sadu.
1. Přejděte na kartu **Stav.** 
1. Vyhledejte část **Stav.**
1. Povolte možnost **Sledování stavu aplikace.**
1. Vyhledejte část **Zásady automatických oprav.**
1. **Zapněte** možnost **Automatické opravy.**
1. V **období odkladu (min)** určete dobu odkladu v minutách, povolené hodnoty jsou mezi 30 a 90 minutami. 
1. Po vytvoření nové škálovací sady vyberte **Tlačítko Revize + vytvořit.**

### <a name="rest-api"></a>REST API

Následující příklad ukazuje, jak povolit automatickou opravu instance v modelu škálovací sady. Použijte rozhraní API verze 2018-10-01 nebo vyšší.

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

Funkci automatické opravy instancí lze povolit při vytváření nové škálovací sady pomocí rutiny [New-AzVmssConfig.](/powershell/module/az.compute/new-azvmssconfig) Tento ukázkový skript provede vytvoření škálovací sady a přidružených prostředků pomocí konfiguračního souboru: [Vytvořte kompletní škálovací sadu virtuálních počítačů](./scripts/powershell-sample-create-complete-scale-set.md). Zásady automatických oprav instancí můžete nakonfigurovat přidáním parametrů *EnableAutomaticRepair* a *AutomaticRepairGracePeriod* do konfiguračního objektu pro vytvoření škálovací sady. Následující příklad umožňuje funkci s dobou odkladu 30 minut.

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

Následující příklad umožňuje zásady automatických oprav při vytváření nové škálovací sady pomocí *[az vmss create](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-create)*. Nejprve vytvořte skupinu prostředků a pak vytvořte novou škálovací sadu s obdobím odkladu zásad automatických oprav nastavenou na 30 minut.

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
  --automatic-repairs-period 30
```

Výše uvedený příklad používá existující nástroj pro vyrovnávání zatížení a sondu stavu pro sledování stavu aplikace instancí. Pokud dáváte přednost použití rozšíření stavu aplikace pro monitorování, můžete vytvořit škálovací sadu, nakonfigurovat rozšíření stavu aplikace a pak povolit zásady automatických oprav instancí pomocí *aktualizace az vmss*, jak je vysvětleno v další části.

## <a name="enabling-automatic-repairs-policy-when-updating-an-existing-scale-set"></a>Povolení zásad automatických oprav při aktualizaci existující škálovací sady

Před povolením zásad automatických oprav v existující sadě měřítek zajistěte splnění všech [požadavků](#requirements-for-using-automatic-instance-repairs) na přihlášení k této funkci. Koncový bod aplikace by měl být správně nakonfigurován pro instance škálovací sady, aby se zabránilo spuštění nechtěných oprav při konfiguraci koncového bodu. Chcete-li povolit automatickou opravu instance v škálovací sadě, použijte objekt *automaticRepairsPolicy* v modelu škálovací sady virtuálních počítačů.

Po aktualizaci modelu existující škálovací sady se ujistěte, že nejnovější model je použit pro všechny instance škálování. Podívejte se na [pokyny, jak aktualizovat virtuální počítače s nejnovějším modelem škálovací sady](./virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model).

### <a name="azure-portal"></a>portál Azure

Zásady automatických oprav existující škálovací sady můžete upravit prostřednictvím portálu Azure. 
 
1. Přejděte na existující škálovací sadu virtuálních strojů.
1. V části **Nastavení** v nabídce vlevo vyberte **Možnost Zdraví a oprava**.
1. Povolte možnost **Sledování stavu aplikace.**
1. Vyhledejte část **Zásady automatických oprav.**
1. **Zapněte** možnost **Automatické opravy.**
1. V **období odkladu (min)** určete dobu odkladu v minutách, povolené hodnoty jsou mezi 30 a 90 minutami. 
1. Po dokončení vyberte **Uložit**. 

### <a name="rest-api"></a>REST API

Následující příklad umožňuje zásady s obdobím odkladu 40 minut. Použijte rozhraní API verze 2018-10-01 nebo vyšší.

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

Pomocí [rutiny Aktualizace AzVmss](/powershell/module/az.compute/update-azvmss) můžete upravit konfiguraci funkce automatické opravy instancí v existující škálovací sadě. Následující příklad aktualizuje období odkladu na 40 minut.

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT40M"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

Následuje příklad pro aktualizaci zásadautomatické opravy instancí existující škálovací sady pomocí *[aktualizace az vmss](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-update)*.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --enable-automatic-repairs true \
  --automatic-repairs-period 30
```

## <a name="viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy"></a>Zobrazení a aktualizace stavu služby automatické opravy instancí

### <a name="rest-api"></a>REST API 

Použití [získat zobrazení instance](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/getinstanceview) s rozhraním API verze 2019-12-01 nebo vyšší pro škálovací sadu virtuálních počítačů k zobrazení *serviceState* pro automatické opravy v rámci vlastnosti *orchestraceServices*. 

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

Pomocí *rozhraní setOrchestrationServiceState* API s rozhraním API verze 2019-12-01 nebo vyšším na škálovací sadě virtuálních počítačů nastavte stav automatických oprav. Jakmile je škálovací sada přihlášena do funkce automatických oprav, můžete pomocí tohoto rozhraní API pozastavit nebo obnovit automatické opravy škálovací sady. 

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

Pomocí [rutiny get-instance-view](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-get-instance-view) zobrazte *stav serviceState* pro automatické opravy instancí. 

```azurecli-interactive
az vmss get-instance-view \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```

Pomocí [rutiny set-orchestrace-service-state](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-set-orchestration-service-state) aktualizujte *serviceState* pro automatické opravy instancí. Jakmile je škálovací sada přihlášena do funkce automatické opravy, můžete pomocí této rutiny pozastavit nebo obnovit automatické opravy pro vás škálovací sadu. 

```azurecli-interactive
az vmss set-orchestration-service-state \
    --service-name AutomaticRepairs \
    --action Resume \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```
### <a name="azure-powershell"></a>Azure PowerShell

Pomocí [rutiny Get-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/get-azvmss?view=azps-3.7.0) s parametrem *InstanceView* zobrazte *ServiceState* pro automatické opravy instancí.

```azurepowershell-interactive
Get-AzVmss `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -InstanceView
```

Pomocí rutiny Set-AzVmssOrchestrationServiceState aktualizujte *serviceState* pro automatické opravy instancí. Jakmile je škálovací sada přihlášena do funkce automatické opravy, můžete pomocí této rutiny pozastavit nebo obnovit automatické opravy pro vás škálovací sadu.

```azurepowershell-interactive
Set-AzVmssOrchestrationServiceState `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -ServiceName "AutomaticRepairs" `
    -Action "Suspend"
```

## <a name="troubleshoot"></a>Řešení potíží

**Nepovolit zásady automatických oprav**

Pokud se zobrazí chyba "BadRequest" se zprávou "Nelze najít člena 'automaticRepairsPolicy' na objekt typu 'vlastnosti", zkontrolujte verzi rozhraní API použitou pro škálovací sadu virtuálních počítačů. Pro tuto funkci je vyžadována verze rozhraní API 2018-10-01 nebo vyšší.

**Instance není opravena, i když je povolena zásada**

Instance může být v období odkladu. Toto je doba čekání po jakékoli změně stavu instance před provedením oprav. Tím se zabrání předčasným nebo náhodným opravám. Akce opravy by měla proběhnout po dokončení období odkladu pro instanci.

**Zobrazení stavu aplikace pro instance škálovací sady**

K zobrazení stavu aplikace můžete použít [rozhraní API pro zobrazení instance](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) get instance pro instance ve škálovací sadě virtuálního počítače. S Azure PowerShell, můžete použít rutinu [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) s *příznakem -InstanceView.* Stav aplikace je k dispozici v rámci vlastnosti *vmHealth*.

Na webu Azure Portal uvidíte také stav. Přejděte na existující škálovací sadu, vyberte **instance** z nabídky vlevo a podívejte se na sloupec **Stav** stavu pro stav každé instance škálovací sady. 

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak nakonfigurovat [rozšíření stavu aplikace](./virtual-machine-scale-sets-health-extension.md) nebo [sondy stavu vyrovnávání zatížení](../load-balancer/load-balancer-custom-probe-overview.md) pro škálovací sady.

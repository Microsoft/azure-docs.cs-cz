---
title: Automatické opravy instancí pomocí Azure Virtual Machine Scale Sets
description: Přečtěte si, jak nakonfigurovat zásady automatických oprav pro instance virtuálních počítačů v sadě škálování.
author: avirishuv
manager: vashan
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: avverma
ms.openlocfilehash: f335b0fb3396103c321d740bcf6d125e60e95086
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2020
ms.locfileid: "78274811"
---
# <a name="preview-automatic-instance-repairs-for-azure-virtual-machine-scale-sets"></a>Preview: Automatické opravy instancí pro Azure Virtual Machine Scale Sets

Povolení automatických oprav instancí pro Azure Virtual Machine Scale Sets vám pomůže dosáhnout vysoké dostupnosti pro aplikace tím, že se zachová sada zdravých instancí. Pokud je zjištěna instance v sadě škálování, která je hlášena z [rozšíření pro stav aplikace](./virtual-machine-scale-sets-health-extension.md) nebo [sondy stavu nástroje pro vyrovnávání zatížení](../load-balancer/load-balancer-custom-probe-overview.md), pak tato funkce automaticky provádí opravu instance odstraněním instance, která není v pořádku, a vytvořením nového pro její nahrazení.

> [!NOTE]
> Tato funkce ve verzi Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy.

## <a name="requirements-for-using-automatic-instance-repairs"></a>Požadavky na používání automatických oprav instancí

**Vyjádřit výslovný souhlas s automatickými opravami instancí Preview**

Pokud se chcete přihlásit k automatickým opravám instancí Preview, použijte buď REST API, nebo Azure PowerShell. Tyto kroky zaregistrují vaše předplatné pro funkci verze Preview. Poznámka: Toto je pouze jednorázové nastavení potřebné k použití této funkce. Pokud je vaše předplatné už zaregistrované pro automatické opravy instancí verze Preview, nemusíte se znovu registrovat. 

Pomocí rozhraní REST API 

1. Registrace funkce pomocí [funkce – registr](/rest/api/resources/features/register) 

```
POST on '/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/RepairVMScaleSetInstancesPreview/register?api-version=2015-12-01'
```

```json
{
  "properties": {
    "state": "Registering"
  },
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/RepairVMScaleSetInstancesPreview",
  "type": "Microsoft.Features/providers/features",
  "name": "Microsoft.Compute/RepairVMScaleSetInstancesPreview"
}
```

2. Počkejte několik minut, než se *stav* změní na *registrováno*. Tuto možnost můžete potvrdit pomocí následujícího rozhraní API.

```
GET on '/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/RepairVMScaleSetInstancesPreview?api-version=2015-12-01'
```

```json
{
  "properties": {
    "state": "Registered"
  },
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/RepairVMScaleSetInstancesPreview",
  "type": "Microsoft.Features/providers/features",
  "name": "Microsoft.Compute/RepairVMScaleSetInstancesPreview"
}
```

3. Jakmile se *stav* změní na *registrováno*, spusťte následující příkaz.

```
POST on '/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2015-12-01'
```

Použití Azure Powershell

1. Zaregistrujte se na funkci pomocí rutiny [Register-AzureRmResourceProvider](/powershell/module/azurerm.resources/register-azurermresourceprovider) následované [Registry-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature) .

```azurepowershell-interactive
Register-AzureRmResourceProvider `
 -ProviderNamespace Microsoft.Compute

Register-AzureRmProviderFeature `
 -ProviderNamespace Microsoft.Compute `
 -FeatureName RepairVMScaleSetInstancesPreview
```

2. Počkejte několik minut, než se *RegistrationState* změní na *registrováno*. Tuto možnost můžete potvrdit pomocí následující rutiny.

```azurepowershell-interactive
Get-AzureRmProviderFeature `
 -ProviderNamespace Microsoft.Compute `
 -FeatureName RepairVMScaleSetInstancesPreview
 ```

 Odpověď by měla být následující.

| FeatureName                           | ProviderName            | RegistrationState       |
|---------------------------------------|-------------------------|-------------------------|
| RepairVMScaleSetInstancesPreview      | Microsoft.Compute       | Registrované              |

3. Jakmile se *RegistrationState* změní na *registrováno*, spusťte následující rutinu.

```azurepowershell-interactive
Register-AzureRmResourceProvider `
 -ProviderNamespace Microsoft.Compute
```

**Povolit monitorování stavu aplikací pro sadu škálování**

Sada škálování by měla mít povolený monitoring stavu aplikace pro instance. To se dá udělat buď pomocí [rozšíření stavu aplikace](./virtual-machine-scale-sets-health-extension.md) , nebo [sondy stavu nástroje pro vyrovnávání zatížení](../load-balancer/load-balancer-custom-probe-overview.md). V jednom okamžiku může být povolena pouze jedna z nich. Nástroj pro zjištění stavu aplikace nebo nástroj pro vyrovnávání zatížení otestuje koncový bod aplikace nakonfigurovaný na instancích virtuálních počítačů za účelem určení stavu aplikace. Tento stav používá nástroj Orchestrator set Orchestrator k monitorování stavu instance a v případě potřeby provede opravy.

**Nakonfigurovat koncový bod tak, aby poskytoval stav**

Předtím, než povolíte zásady automatických oprav instancí, zajistěte, aby instance sady škálování měly koncový bod aplikace nakonfigurovaný pro generování stavu aplikace. Když instance vrátí stav 200 (OK) na tomto koncovém bodu aplikace, bude tato instance označena jako "v pořádku". Ve všech ostatních případech je instance označená jako "není v pořádku", včetně následujících scénářů:

- Pokud v instancích virtuálních počítačů není nakonfigurovaný koncový bod aplikace, který by poskytoval stav aplikace
- Pokud je koncový bod aplikace nesprávně nakonfigurován
- Pokud koncový bod aplikace není dosažitelný

U instancí označených jako "není v pořádku" jsou automatické opravy aktivovány sadou škálování. Než povolíte zásady automatických oprav, ujistěte se, že je koncový bod aplikace správně nakonfigurovaný, aby nedocházelo k nezamýšleným opravám instancí, zatímco koncový bod se nakonfiguruje.

**Povolit jednu skupinu umístění**

Tato verze Preview je aktuálně dostupná jenom pro sady škálování nasazené jako jediná skupina umístění. Vlastnost *singlePlacementGroup* by měla být nastavená na *hodnotu true* , aby vaše sada škálování používala funkci automatických oprav instancí. Přečtěte si další informace o [skupinách umístění](./virtual-machine-scale-sets-placement-groups.md#placement-groups).

**Verze rozhraní API**

Pro rozhraní COMPUTE API verze 2018-10-01 nebo vyšší se podporují zásady pro automatické opravy.

**Omezení počtu přesunutí prostředků nebo předplatného**

V rámci této verze Preview se pro sady škálování v současné době nepodporují přesuny prostředků nebo předplatného, pokud je povolená Automatická oprava zásad.

**Omezení pro sadu Service Fabric Scale Sets**

Tato funkce Preview není v současnosti podporovaná pro sadu škálování Service Fabric.

## <a name="how-do-automatic-instance-repairs-work"></a>Jak automatické opravy instancí fungují?

Funkce Automatické opravy instancí spoléhá na monitorování stavu jednotlivých instancí v sadě škálování. Instance virtuálních počítačů v sadě škálování je možné nakonfigurovat tak, aby vygenerovaly stav aplikace, a to pomocí [rozšíření stavu aplikace](./virtual-machine-scale-sets-health-extension.md) nebo [sondy stavu nástroje pro vyrovnávání zatížení](../load-balancer/load-balancer-custom-probe-overview.md). Pokud se zjistí, že instance není v pořádku, pak sada škálování provede akci opravy tím, že odstraní instanci, která není v pořádku, a vytvoří novou, aby se nahradila. Tato funkce se dá povolit v modelu sady škálování virtuálních počítačů pomocí objektu *automaticRepairsPolicy* .

### <a name="batching"></a>Dávkování

Automatizované operace opravy instancí se provádějí v dávkách. V jednom okamžiku se v rámci zásad automatických oprav neopraví víc než 5% instancí v sadě škálování. To pomáhá zabránit současnému odstranění a opětovnému vytvoření velkého počtu instancí, pokud se ve stejnou dobu nenajde stav není v pořádku.

### <a name="grace-period"></a>Období odkladu

Když instance projde pomocí operace změny stavu z důvodu akce PUT, PATCH nebo POST provedené v sadě škálování (například obnovení bitové kopie, opětovné nasazení, aktualizace atd.), bude jakákoli akce opravy v této instanci provedena až po čekání na dobu odkladu. Období odkladu je doba, po kterou se má instance vrátit do stavu v pořádku. Období odkladu začíná po dokončení změny stavu. To pomáhá zabránit jakýmkoli předčasnému nebo náhodnému opravení. Doba odkladu se dodržuje u všech nově vytvořených instancí v sadě škálování (včetně toho, která se vytvořila v důsledku operace opravy). Období odkladu je zadáno v minutách ve formátu ISO 8601 a lze ho nastavit pomocí vlastnosti *automaticRepairsPolicy. gracePeriod*. Období odkladu může být v rozmezí 30 minut a 90 minut a má výchozí hodnotu 30 minut.

Automatické opravy instancí: proces funguje takto:

1. Test stavu [aplikace](./virtual-machine-scale-sets-health-extension.md) nebo [sondy stavu nástroje pro vyrovnávání zatížení](../load-balancer/load-balancer-custom-probe-overview.md) : Otestujte koncový bod aplikace v každém virtuálním počítači v sadě škálování tak, aby se pro každou instanci získal stav aplikací.
2. Pokud koncový bod odpoví na stav 200 (OK), pak je tato instance označená jako "v pořádku". Ve všech ostatních případech (včetně nedostupnosti koncového bodu) je instance označena jako "není v pořádku".
3. Pokud je zjištěno, že instance není v pořádku, sada škálování aktivuje akci opravy odstraněním instance, která není v pořádku, a vytvořením nového pro její nahrazení.
4. Opravy instancí se provádějí v dávkách. V jednom okamžiku se opraví maximálně 5% z celkového počtu instancí v sadě škálování. Pokud má sada škálování méně než 20 instancí, opravy se provádějí pro jednu instanci, která není v pořádku.
5. Výše uvedený proces pokračuje, dokud nebudou všechny instance, které jsou v pořádku, v sadě škálování opraveny.

## <a name="instance-protection-and-automatic-repairs"></a>Ochrana instancí a automatické opravy

Pokud je instance v sadě škálování chráněná pomocí *[zásad ochrany akce chránit před škálováním sady](./virtual-machine-scale-sets-instance-protection.md#protect-from-scale-set-actions)* , pak se u této instance neprovádí automatické opravy.

## <a name="enabling-automatic-repairs-policy-when-creating-a-new-scale-set"></a>Povolení zásad automatických oprav při vytváření nové sady škálování

Aby bylo možné povolit automatické opravy při vytváření nové sady škálování, ujistěte se, že jsou splněné všechny [požadavky](#requirements-for-using-automatic-instance-repairs) pro zapínání s touto funkcí. Koncový bod aplikace by měl být správně nakonfigurovaný pro instance sady škálování, aby nedocházelo k nezamýšleným opravám během nakonfigurovaného koncového bodu. U nově vytvořených sad škálování se všechny opravy instancí provádějí až po uplynutí doby trvání odkladu. Pokud chcete povolit automatickou opravu instancí v sadě škálování, použijte objekt *automaticRepairsPolicy* v modelu sady škálování virtuálního počítače.

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

### <a name="azure-powershell"></a>Azure Powershell

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

## <a name="enabling-automatic-repairs-policy-when-updating-an-existing-scale-set"></a>Povolení zásad automatických oprav při aktualizaci existující sady škálování

Než povolíte funkci Automatické opravy v existující sadě škálování, ujistěte se, že jsou splněné všechny [požadavky](#requirements-for-using-automatic-instance-repairs) pro zapínání s touto funkcí. Koncový bod aplikace by měl být správně nakonfigurovaný pro instance sady škálování, aby nedocházelo k nezamýšleným opravám během nakonfigurovaného koncového bodu. Pokud chcete povolit automatickou opravu instancí v sadě škálování, použijte objekt *automaticRepairsPolicy* v modelu sady škálování virtuálního počítače.

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

### <a name="azure-powershell"></a>Azure Powershell

Pomocí rutiny [Update-AzVmss](/powershell/module/az.compute/update-azvmss) Upravte konfiguraci funkce Automatické opravy instancí v existující sadě škálování. Následující příklad aktualizuje dobu odkladu na 40 minut.

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT40M"
```

## <a name="troubleshoot"></a>Řešení potíží

**Nepovedlo se povolit zásady automatických oprav.**

Pokud se zobrazí chyba "důvodu chybného požadavku" se zprávou "nebylo možné najít člena" automaticRepairsPolicy "u objektu typu" Properties "", zkontrolujte verzi rozhraní API použitou pro sadu škálování virtuálního počítače. Pro tuto funkci se vyžaduje rozhraní API verze 2018-10-01 nebo vyšší.

**Instance není opravená, i když je povolená zásada.**

Instance může být v období odkladu. To je doba, po kterou se má čekat po změně stavu instance před provedením oprav. To se vyhnete jakýmkoliv předčasnému nebo náhodnému opravám. Akce opravy by se měla stát po dokončení období odkladu pro instanci.

**Zobrazení stavu aplikace pro instance sady škálování**

K zobrazení stavu aplikace můžete použít [rozhraní API pro zobrazení](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) instancí v sadě škálování virtuálního počítače. Pomocí Azure PowerShell můžete rutinu [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) použít s příznakem *-InstanceView* . Stav aplikace je k dispozici pod vlastností *vmHealth*.

## <a name="next-steps"></a>Další kroky

Naučte se konfigurovat testy stavu [aplikací](./virtual-machine-scale-sets-health-extension.md) nebo [sondy stavu nástroje pro vyrovnávání zatížení](../load-balancer/load-balancer-custom-probe-overview.md) pro vaše sady škálování.

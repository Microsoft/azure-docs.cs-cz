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
ms.openlocfilehash: f335b0fb3396103c321d740bcf6d125e60e95086
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78274811"
---
# <a name="preview-automatic-instance-repairs-for-azure-virtual-machine-scale-sets"></a>Náhled: Automatické opravy instancí pro škálovací sady virtuálních počítačů Azure

Povolení automatických oprav instancí pro škálovací sady virtuálních počítačů Azure pomáhá dosáhnout vysoké dostupnosti pro aplikace udržováním sady zdravých instancí. Pokud instance v škálovací sadě je shledána jako nefunkční, jak je uvedeno [rozšířením stavu aplikace](./virtual-machine-scale-sets-health-extension.md) nebo [sondami stavu vyrovnávání zatížení](../load-balancer/load-balancer-custom-probe-overview.md), pak tato funkce automaticky provede opravu instance odstraněním instance není v pořádku a vytvořením nové instance, která ji nahradí.

> [!NOTE]
> Tato funkce náhledu je k dispozici bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy.

## <a name="requirements-for-using-automatic-instance-repairs"></a>Požadavky na automatické opravy instancí

**Přihlásit se k náhledu automatických oprav instancí**

Pomocí rozhraní REST API nebo Azure PowerShellu se můžete přihlásit k náhledu automatických oprav instancí. Tyto kroky zaregistrují vaše předplatné pro funkci náhledu. Všimněte si, že se jedná pouze o jednorázové nastavení potřebné pro použití této funkce. Pokud je vaše předplatné již zaregistrováno pro náhled automatických oprav instancí, nemusíte se znovu registrovat. 

Pomocí rozhraní REST API 

1. Zaregistrujte se na funkci pomocí [funkce - Registrovat](/rest/api/resources/features/register) 

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

2. Počkejte několik minut, než se *stát* změní na *Registrováno*. Můžete to potvrdit pomocí následujícího rozhraní API.

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

3. Jakmile *se stát* změní na *Registrováno*, spusťte následující.

```
POST on '/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2015-12-01'
```

Použití Azure Powershell

1. Zaregistrujte se pro funkci pomocí rutiny [Register-AzureRmResourceProvider](/powershell/module/azurerm.resources/register-azurermresourceprovider) následované [register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature)

```azurepowershell-interactive
Register-AzureRmResourceProvider `
 -ProviderNamespace Microsoft.Compute

Register-AzureRmProviderFeature `
 -ProviderNamespace Microsoft.Compute `
 -FeatureName RepairVMScaleSetInstancesPreview
```

2. Počkejte několik minut pro *RegistrationState* změnit *registered*. Můžete to potvrdit pomocí následující rutiny.

```azurepowershell-interactive
Get-AzureRmProviderFeature `
 -ProviderNamespace Microsoft.Compute `
 -FeatureName RepairVMScaleSetInstancesPreview
 ```

 Odpověď by měla být následující.

| Název funkce                           | Providername            | Registrační stát       |
|---------------------------------------|-------------------------|-------------------------|
| OpravaVMScaleSetInstancesPreview      | Microsoft.Compute       | Registrované              |

3. Jakmile *RegistrationState* změnit *registered*, pak spusťte následující rutinu.

```azurepowershell-interactive
Register-AzureRmResourceProvider `
 -ProviderNamespace Microsoft.Compute
```

**Povolení monitorování stavu aplikace pro škálovací sadu**

Škálovací sada by měla mít povoleno monitorování stavu aplikace pro instance. To lze provést pomocí [rozšíření stavu aplikace](./virtual-machine-scale-sets-health-extension.md) nebo [sondy stavu vyrovnávání zatížení](../load-balancer/load-balancer-custom-probe-overview.md). Současně lze povolit pouze jednu z nich. Rozšíření stavu aplikace nebo nástroj pro vyrovnávání zatížení sonduje ping koncový bod aplikace nakonfigurovaný na instancích virtuálních strojů k určení stavu aplikace. Tento stav ový stav používá orchestrátor škálovací sady ke sledování stavu instance a provádění oprav v případě potřeby.

**Konfigurace koncového bodu pro poskytování stavu**

Před povolením zásad automatických oprav instancí se ujistěte, že instance škálovací sady mají koncový bod aplikace nakonfigurovaný tak, aby vyzařoval stav aplikace. Když instance vrátí stav 200 (OK) v tomto koncovém bodě aplikace, pak je instance označena jako "V pořádku". Ve všech ostatních případech je instance označena jako "Není v pořádku", včetně následujících scénářů:

- Pokud není v instancích virtuálního počítače nakonfigurován žádný koncový bod aplikace, který by poskytoval stav aplikace
- Pokud je koncový bod aplikace nesprávně nakonfigurován
- Pokud koncový bod aplikace není dostupný

V případech označených jako "Není v pořádku", automatické opravy jsou spuštěny škálovací sady. Ujistěte se, že koncový bod aplikace je správně nakonfigurován před povolením zásadautomatických oprav, aby se zabránilo nechtěné opravy instance, zatímco koncový bod je stále konfigurován.

**Povolení skupiny jednoho umístění**

Tento náhled je v současné době k dispozici pouze pro škálovací sady nasazené jako skupina s jedním umístěním. Vlastnost *singlePlacementGroup* by měla být nastavena na *hodnotu true* pro vaši škálovací sadu tak, aby používala funkci automatických oprav instancí. Přečtěte si další informace o [skupinách umístění](./virtual-machine-scale-sets-placement-groups.md#placement-groups).

**Verze rozhraní API**

Zásady automatických oprav jsou podporované pro výpočetní rozhraní API verze 2018-10-01 nebo vyšší.

**Omezení přesunů prostředků nebo předplatného**

Jako součást tohoto náhledu, prostředky nebo odběr přesune nejsou aktuálně podporovány pro škálovací sady při automatické opravy zásady.

**Omezení pro sady měřítek výrobních kapacit service fabric**

Tato funkce náhledu není aktuálně podporována pro škálovací sady prostředků infrastruktury služeb.

## <a name="how-do-automatic-instance-repairs-work"></a>Jak fungují automatické opravy instancí?

Funkce automatické opravy instancí závisí na monitorování stavu jednotlivých instancí v škálovací sadě. Instance virtuálních počítače ve škálovací sadě lze nakonfigurovat tak, aby vyzařovalo stav aplikace pomocí [rozšíření Stavu aplikace](./virtual-machine-scale-sets-health-extension.md) nebo sond stavu [nástrojpro vyrovnávání zatížení](../load-balancer/load-balancer-custom-probe-overview.md). Pokud je instance shledána nefunkční, pak škálovací sada provede opravu odstraněním instance není v pořádku a vytvořením nové instance, která ji nahradí. Tuto funkci lze povolit v modelu škálovací sady virtuálních počítačů pomocí *objektu automaticRepairsPolicy.*

### <a name="batching"></a>Dávkování

Automatické operace opravy instance jsou prováděny v dávkách. V daném okamžiku není více než 5 % případů v škálovací sadě opraveno prostřednictvím zásad automatických oprav. To pomáhá zabránit současnému odstranění a opětovnému vytvoření velkého počtu instancí, pokud jsou nalezeny současně nefunkční.

### <a name="grace-period"></a>Období odkladu

Když instance prochází operace změny stavu z důvodu PUT, PATCH nebo POST akce provedené na škálovací sady (například reimage, redeploy, update, atd.), pak všechny opravy akce na této instanci se provádí pouze po čekání na období odkladu. Období odkladu je doba, po kterou se instance může vrátit do stavu V pořádku. Období odkladu začíná po dokončení změny stavu. To pomáhá zabránit předčasným nebo náhodným opravním. Doba odkladu je dodržena pro všechny nově vytvořené instance v škálovací sadě (včetně té vytvořené v důsledku operace opravy). Období odkladu je specifikováno v minutách ve formátu ISO 8601 a lze jej nastavit pomocí vlastnosti *automaticRepairsPolicy.gracePeriod*. Doba odkladu se může pohybovat mezi 30 minutami a 90 minutami a má výchozí hodnotu 30 minut.

Proces automatických oprav instancí funguje takto:

1. [Rozšíření stavu aplikace](./virtual-machine-scale-sets-health-extension.md) nebo [sondy stavu nástroje pro vyrovnávání zatížení](../load-balancer/load-balancer-custom-probe-overview.md) ping koncového bodu aplikace uvnitř každého virtuálního počítače ve škálovací sadě, aby získaly stav aplikace pro každou instanci.
2. Pokud koncový bod odpoví se stavem 200 (OK), pak je instance označena jako "V pořádku". Ve všech ostatních případech (včetně pokud koncový bod je nedostupný), instance je označena "Není v pořádku".
3. Pokud je instance shledána nefunkční, škálovací sada spustí akci opravy odstraněním instance není v pořádku a vytvořením nové instance, která ji nahradí.
4. Opravy instance se provádějí v dávkách. V daném okamžiku není opraveno více než 5 % celkových instancí v škálovací sadě. Pokud škálovací sada má méně než 20 instancí, opravy jsou prováděny pro jednu instanci není v pořádku najednou.
5. Výše uvedený proces pokračuje, dokud nejsou opraveny všechny instance není v pořádku v škálovací sadě.

## <a name="instance-protection-and-automatic-repairs"></a>Ochrana instance a automatické opravy

Pokud je instance v škálovací sadě chráněna použitím *[zásad ochrany před akcemi škálování](./virtual-machine-scale-sets-instance-protection.md#protect-from-scale-set-actions)*, nebudou v této instanci provedeny automatické opravy.

## <a name="enabling-automatic-repairs-policy-when-creating-a-new-scale-set"></a>Povolení zásad automatických oprav při vytváření nové škálovací sady

Chcete-li povolit zásady automatických oprav při vytváření nové škálovací sady, ujistěte se, že jsou splněny všechny [požadavky](#requirements-for-using-automatic-instance-repairs) na přihlášení k této funkci. Koncový bod aplikace by měl být správně nakonfigurován pro instance škálovací sady, aby se zabránilo spuštění nechtěných oprav při konfiguraci koncového bodu. U nově vytvořených škálovacích sad se všechny opravy instancí provádějí až po uplynutí doby odkladu. Chcete-li povolit automatickou opravu instance v škálovací sadě, použijte objekt *automaticRepairsPolicy* v modelu škálovací sady virtuálních počítačů.

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

## <a name="enabling-automatic-repairs-policy-when-updating-an-existing-scale-set"></a>Povolení zásad automatických oprav při aktualizaci existující škálovací sady

Před povolením zásad automatických oprav v existující sadě měřítek zajistěte splnění všech [požadavků](#requirements-for-using-automatic-instance-repairs) na přihlášení k této funkci. Koncový bod aplikace by měl být správně nakonfigurován pro instance škálovací sady, aby se zabránilo spuštění nechtěných oprav při konfiguraci koncového bodu. Chcete-li povolit automatickou opravu instance v škálovací sadě, použijte objekt *automaticRepairsPolicy* v modelu škálovací sady virtuálních počítačů.

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

## <a name="troubleshoot"></a>Řešení potíží

**Nepovolit zásady automatických oprav**

Pokud se zobrazí chyba "BadRequest" se zprávou "Nelze najít člena 'automaticRepairsPolicy' na objekt typu 'vlastnosti", zkontrolujte verzi rozhraní API použitou pro škálovací sadu virtuálních počítačů. Pro tuto funkci je vyžadována verze rozhraní API 2018-10-01 nebo vyšší.

**Instance není opravena, i když je povolena zásada**

Instance může být v období odkladu. Toto je doba čekání po jakékoli změně stavu instance před provedením oprav. Tím se zabrání předčasným nebo náhodným opravám. Akce opravy by měla proběhnout po dokončení období odkladu pro instanci.

**Zobrazení stavu aplikace pro instance škálovací sady**

K zobrazení stavu aplikace můžete použít [rozhraní API pro zobrazení instance](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) get instance pro instance ve škálovací sadě virtuálního počítače. S Azure PowerShell, můžete použít rutinu [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) s *příznakem -InstanceView.* Stav aplikace je k dispozici v rámci vlastnosti *vmHealth*.

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak nakonfigurovat [rozšíření stavu aplikace](./virtual-machine-scale-sets-health-extension.md) nebo [sondy stavu vyrovnávání zatížení](../load-balancer/load-balancer-custom-probe-overview.md) pro škálovací sady.

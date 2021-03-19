---
title: Vytvoření sady škálování, která používá Azure spot Virtual Machines
description: Naučte se vytvářet služby Azure Virtual Machine Scale Sets, které používají Azure spot Virtual Machines k uložení za cenu.
author: JagVeerappan
ms.author: jagaveer
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: spot
ms.date: 02/26/2021
ms.reviewer: cynthn
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: a176a30a1e21ec03c2da329785ab895ec67a4faf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104596412"
---
# <a name="azure-spot-virtual-machines-for-virtual-machine-scale-sets"></a>Azure spot Virtual Machines pro Virtual Machine Scale Sets 

Použití služby Azure spot Virtual Machines se škálováním na více verzí vám umožní využít výhod naší nevyužité kapacity s významnou úsporou nákladů. V jakémkoli okamžiku, kdy Azure potřebuje kapacitu zpět, infrastruktura Azure vyřadí instance virtuálních počítačů Azure na místě. Proto jsou instance virtuálních počítačů Azure skvělé pro úlohy, které můžou zpracovávat přerušení, jako jsou úlohy dávkového zpracování, vývojové a testovací prostředí, velké výpočetní úlohy a další.

Množství dostupné kapacity se může lišit v závislosti na velikosti, oblasti, denní době a dalších. Když nasazujete instance virtuálních počítačů Azure ve službě Scale Sets, Azure tuto instanci přidělí jenom v případě, že je dostupná kapacita, ale pro tyto instance není k dispozici žádná smlouva SLA. Sada škálování virtuálních počítačů v Azure je nasazená v jedné doméně selhání a nenabízí žádné záruky vysoké dostupnosti.


## <a name="pricing"></a>Ceny

Ceny za instance virtuálních počítačů Azure jsou proměnlivé na základě oblastí a SKU. Další informace najdete v tématu ceny pro [Linux](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) a [Windows](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/). 


S proměnnými cenami máte možnost nastavit maximální cenu v USD (USD), která používá až pět desetinných míst. Hodnota by měla být například `0.98765` maximální cena $0,98765 USD za hodinu. Pokud nastavíte maximální cenu, instance se nevyřadí na `-1` základě ceny. Cena za instanci bude aktuální cena za virtuální počítač se službou Azure nebo cenu za standardní instanci, která je stále menší, pokud je dostupná kapacita a kvóta.


## <a name="limitations"></a>Omezení

Následující velikosti se pro Azure spot Virtual Machines nepodporují:
 - Řady B-Series
 - Propagační verze libovolné velikosti (například Dv2, NV, NC, H – propagační velikosti)

Virtuální počítač se službou Azure se dá nasadit do libovolné oblasti, s výjimkou Microsoft Azure Čína 21Vianet.

<a name="channel"></a>

V současné době jsou podporovány následující [typy nabídek](https://azure.microsoft.com/support/legal/offer-details/) :

-   Smlouva Enterprise
-   003P kódu nabídky s průběžnými platbami
-   Financovan
- Pro poskytovatele cloudových služeb (CSP) si přečtěte téma [Partnerské centrum](/partner-center/azure-plan-get-started) nebo se obraťte přímo na svého partnera.

## <a name="eviction-policy"></a>Zásady vyřazení

Při vytváření sady škálování pomocí služby Azure spot Virtual Machines můžete nastavit zásadu vyřazení, aby se nastavilo zrušení *přidělení* (výchozí) nebo *odstranění*. 

Zásady zrušení *přidělení* přesouvá vaše vyřazené instance do stavu Zastaveno (přidělení zrušeno), což vám umožní znovu nasadit vyřazené instance. Neexistuje však záruka, že přidělení bude úspěšné. Navrácené virtuální počítače se budou počítat s kvótou instance sady škálování a budou se vám účtovat vaše základní disky. 

Pokud chcete, aby se vaše instance odstranily při jejich vyřazení, můžete nastavit zásadu vyřazení, která se má *Odstranit*. Když je zásada vyřazení nastavená tak, aby se odstranila, můžete vytvořit nové virtuální počítače tím, že zvýšíte vlastnost počet instancí sady škálování. Vyřazení virtuálních počítačů se odstraní společně s jejich podkladovým diskům, takže se za úložiště nebudete účtovat. K automatickému vyzkoušení a kompenzaci vydaných virtuálních počítačů můžete použít také funkci automatického škálování sad škálování, ale nezaručujeme, že přidělení bude úspěšné. Při nastavování zásad vyřazení do služby Virtual Machine Scale Sets se doporučuje používat jenom funkci automatického škálování ve službě Azure bodových škálování virtuálních počítačů, abyste se vyhnuli nákladům na vaše disky a omezením kvót. 

Uživatelé se můžou přihlásit k přijímání oznámení v rámci virtuálního počítače prostřednictvím [Azure Scheduled Events](../virtual-machines/linux/scheduled-events.md). To vám upozorní na to, jestli se virtuální počítače vyloučí a že budete mít 30 sekund na dokončení všech úloh a před vyřazením provést úlohy vypnutí. 

<a name="bkmk_try"></a>
## <a name="try--restore-preview"></a>Vyzkoušet & obnovení (Preview)

Tato nová funkce na úrovni platformy použije AI k automatickému pokusu o obnovení vyřazení instancí virtuálních počítačů se systémem Azure v rámci škálované sady za účelem zachování počtu cílových instancí. 

> [!IMPORTANT]
> Zkuste & obnovení je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Vyzkoušejte & výhod obnovení:
- Došlo k pokusu o obnovení služby Azure bodový Virtual Machines vyřazení z důvodu kapacity.
- U obnovených Virtual Machines Azure se očekává, že se spustí delší dobu, s nižší pravděpodobností aktivované vyřazením kapacity.
- Zvyšuje životnost virtuálního počítače na místě Azure, takže úlohy běží delší dobu.
- Pomáhá Virtual Machine Scale Sets udržovat počet cílů pro Azure Virtual Machines na místě, podobně jako údržba funkcí počtu cílů, které už existují pro virtuální počítače s průběžnými platbami.

Zkuste & obnovení je v sadách škálování, které používají [Automatické škálování](virtual-machine-scale-sets-autoscale-overview.md), zakázané. Počet virtuálních počítačů v sadě škálování je založený na pravidlech automatického škálování.

### <a name="register-for-try--restore"></a>Zaregistrujte se pro try & obnovení

Než budete moct použít funkci try & Restore, musíte zaregistrovat předplatné pro verzi Preview. Dokončení registrace může trvat několik minut. K dokončení registrace funkce můžete použít rozhraní příkazového řádku Azure nebo PowerShell.


**Použití rozhraní příkazového řádku**

K povolení verze Preview pro vaše předplatné použijte [AZ Feature Registry](/cli/azure/feature#az-feature-register) . 

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name SpotTryRestore 
```

Registrace funkce může trvat až 15 minut. Postup kontroly stavu registrace: 

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name SpotTryRestore 
```

Po registraci této funkce pro vaše předplatné dokončete proces výslovných přihlášení tím, že tuto změnu rozšíříte do zprostředkovatele výpočetních prostředků. 

```azurecli-interactive
az provider register --namespace Microsoft.Compute 
```
**Použití PowerShellu** 

K povolení verze Preview pro vaše předplatné použijte rutinu [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) . 

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName SpotTryRestore -ProviderNamespace Microsoft.Compute 
```

Registrace funkce může trvat až 15 minut. Postup kontroly stavu registrace: 

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName SpotTryRestore -ProviderNamespace Microsoft.Compute 
```

Po registraci této funkce pro vaše předplatné dokončete proces výslovných přihlášení tím, že tuto změnu rozšíříte do zprostředkovatele výpočetních prostředků. 

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute 
```

## <a name="placement-groups"></a>Skupiny umístění

Skupina umístění je konstrukce podobná sadě dostupnosti Azure s vlastními doménami selhání a upgradovacími doménami. Ve výchozím nastavení škálovací sada obsahuje jedinou skupinu umístění s maximální velikostí 100 virtuálních počítačů. Pokud je vlastnost Set stupnice nazvaná `singlePlacementGroup` nastavena na *false*, sada škálování se může skládat z více skupin umístění a má rozsah 0 až 1 000 virtuálních počítačů. 

> [!IMPORTANT]
> Pokud nepoužíváte InfiniBand se HPC, důrazně doporučujeme nastavit vlastnost sady škálování na `singlePlacementGroup` *hodnotu false* , aby bylo možné více skupin umístění pro lepší škálování napříč oblastí nebo zónou. 

## <a name="deploying-azure-spot-virtual-machines-in-scale-sets"></a>Nasazení služby Azure spot Virtual Machines v sadě škálování

Pokud chcete nasadit službu Azure bodový Virtual Machines v sadách škálování, můžete nastavit příznak nové *priority* tak, aby byl *bod*. Všechny virtuální počítače ve vaší sadě škálování budou nastavené na bodové. Pokud chcete vytvořit sadu škálování pomocí služby Azure spot Virtual Machines, použijte jednu z následujících metod:
- [Azure Portal](#portal)
- [Azure CLI](#azure-cli)
- [Azure PowerShell](#powershell)
- [Šablony Azure Resource Manageru](#resource-manager-templates)

## <a name="portal"></a>Portál

Proces vytvoření sady škálování, která používá Azure spot Virtual Machines, je stejný, jak je popsáno v [článku Začínáme](quick-create-portal.md). Když nasazujete sadu škálování, můžete nastavit příznak bodu a zásadu vyřazení: ![ Vytvoření sady škálování pomocí Azure Spot Virtual Machines](media/virtual-machine-scale-sets-use-spot/vmss-spot-portal-max-price.png)


## <a name="azure-cli"></a>Azure CLI

Proces vytvoření sady škálování pomocí služby Azure spot Virtual Machines je stejný, jak je popsáno v [článku Začínáme](quick-create-cli.md). Stačí přidat klíčové slovo--priority a přidat `--max-price` . V tomto příkladu používáme `-1` pro, `--max-price` takže instance nebude vyřazení na základě ceny.

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --single-placement-group false \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1 
```

## <a name="powershell"></a>PowerShell

Proces vytvoření sady škálování pomocí služby Azure spot Virtual Machines je stejný, jak je popsáno v [článku Začínáme](quick-create-powershell.md).
Stačí přidat klíčové slovo "– prioritní" a zadat `-max-price` do příkazu [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig).

```powershell
$vmssConfig = New-AzVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Priority "Spot" `
    --max-price -1
```

## <a name="resource-manager-templates"></a>Šablony Resource Manageru

Proces vytvoření sady škálování, která používá Azure spot Virtual Machines, je stejný, jak je popsáno v článku Začínáme pro [Linux](quick-create-template-linux.md) nebo [Windows](quick-create-template-windows.md). 

Pro nasazení šablon virtuálních počítačů se službou Azure použijte `"apiVersion": "2019-03-01"` nebo novější. 

Přidejte `priority` vlastnosti a `evictionPolicy` `billingProfile` do `"virtualMachineProfile":` oddílu a vlastnost do oddílu `"singlePlacementGroup": false,` `"Microsoft.Compute/virtualMachineScaleSets"` v šabloně:

```json

{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  },
  "properties": {
    "singlePlacementGroup": false,
    }

        "virtualMachineProfile": {
              "priority": "Spot",
                "evictionPolicy": "Deallocate",
                "billingProfile": {
                    "maxPrice": -1
                }
            },
```

Chcete-li odstranit instanci poté, co byla vyřazena, změňte `evictionPolicy` parametr na `Delete` .


## <a name="simulate-an-eviction"></a>Simulace vyřazení

Můžete [simulovat vyřazení](/rest/api/compute/virtualmachines/simulateeviction) virtuálních počítačů se systémem Azure na místě, abyste otestovali, jak dobře bude aplikace reagovat na náhlé vyřazení. 

Pro vaše informace nahraďte následující údaje: 

- `subscriptionId`
- `resourceGroupName`
- `vmName`


```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/simulateEviction?api-version=2020-06-01
```

`Response Code: 204` znamená, že simulované vyřazení bylo úspěšné. 

## <a name="faq"></a>Časté otázky

**Otázka:** Po vytvoření je instance virtuálního počítače Azure, která je stejná jako standardní instance?

**A:** Ano, s výjimkou smlouvy SLA pro Azure na místě Virtual Machines a je možné je kdykoli vyřadit.


**Otázka:** Co dělat při vyřazení, ale stále potřebují kapacitu?

**A:** Pokud potřebujete kapacitu hned, doporučujeme místo Azure na místě Virtual Machines používat standardní virtuální počítače.


**Otázka:** Jak se spravuje kvóta pro virtuální počítač se službou Azure na místě?

**A:** Instance virtuálních počítačů se službou Azure a standardní instance budou mít samostatné fondy kvót. Kvóta bodu Azure pro virtuální počítače se bude sdílet mezi virtuálními počítači a instancemi sady škálování. Další informace najdete v tématu [Limity, kvóty a omezení předplatného a služeb Azure](../azure-resource-manager/management/azure-subscription-service-limits.md).


**Otázka:** Můžu požádat o dodatečnou kvótu pro virtuální počítač se službou Azure na místě?

**A:** Ano, žádost bude možné odeslat za účelem zvýšení kvóty pro Azure na místě Virtual Machines prostřednictvím [procesu standardní žádosti o kvótu](../azure-portal/supportability/per-vm-quota-requests.md).


**Otázka:** Můžu převést existující sady škálování na službu Azure na místě Virtual Machine Scale Sets?

**A:** Ne, nastavení `Spot` příznaku se podporuje jenom při vytvoření.


**Otázka:** Pokud používám `low` sadu škálování s nízkou prioritou, musím `Spot` místo toho začít používat?

**A:** Prozatím `low` `Spot` bude fungovat i, ale měli byste začít s přechodem na použití `Spot` .


**Otázka:** Můžu vytvořit sadu škálování s pravidelnými virtuálními počítači i s Azure na místě Virtual Machines?

**A:** Ne, sada škálování nepodporuje více než jeden typ priority.


**Otázka:**  Můžu používat automatické škálování se službou Azure na místě služby Virtual Machine Scale Sets?

**A:** Ano, můžete nastavit pravidla automatického škálování pro sadu škálování virtuálních počítačů Azure na místě. Pokud jsou vaše virtuální počítače vyřazené, automatické škálování se může pokusit vytvořit novou Virtual Machines Azure na místě. Nezapomeňte, že tuto kapacitu nezaručujete. 


**Otázka:**  Funguje automatické škálování podle zásad vyřazení (navrácení a odstranění)?

**A:** Ano, ale doporučujeme, abyste při použití automatického škálování nastavili zásadu vyřazení, která se má odstranit. Důvodem je to, že nepřidělené instance se počítají na základě počtu kapacit v sadě škálování. Při použití automatického škálování se pravděpodobně vám v důsledku navrácených instancí dokončí počet cílových instancí rychleji. I vaše operace škálování můžou být ovlivněné vyřazením na místě. Například instance sady škálování virtuálních počítačů mohou klesnout pod nastavení minimální počet v důsledku několika přímých vyřazení během operace škálování. 


**Otázka:** Kde můžu publikovat otázky?

**A:** Svůj dotaz můžete odeslat a označit `azure-spot` na adrese [Q&A](/answers/topics/azure-spot.html). 

## <a name="next-steps"></a>Další kroky

Podrobnosti o cenách najdete na [stránce s cenami sady škálování virtuálních počítačů](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) .
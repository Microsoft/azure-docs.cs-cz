---
title: Vytvoření sady škálování, která používá virtuální počítače Azure (Preview)
description: Naučte se vytvářet služby Azure Virtual Machine Scale Sets, které k úsporám šetří náklady pomocí virtuálních počítačů na místě.
services: virtual-machine-scale-sets
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.topic: article
ms.date: 10/23/2019
ms.author: cynthn
ms.openlocfilehash: b57c13d4a5c671595a3e82ac7858c027456107f2
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2020
ms.locfileid: "75894077"
---
# <a name="preview-azure-spot-vms-for-virtual-machine-scale-sets"></a>Verze Preview: virtuální počítače Azure ve službě Virtual Machine Scale Sets 

Používání Azure na základě služby škálování na úrovni služby umožňuje využít výhod naší nevyužité kapacity s významnou úsporou nákladů. V jakémkoli okamžiku, kdy Azure potřebuje kapacitu zpět, bude infrastruktura Azure vyřadit instance v přímých intervalech. Proto jsou významné instance pro úlohy, které mohou zpracovávat přerušení jako úlohy dávkového zpracování, vývojová a testovací prostředí, velké výpočetní úlohy a další, velmi Skvělé.

Množství dostupné kapacity se může lišit v závislosti na velikosti, oblasti, denní době a dalších. Při nasazování přímých instancí na sady škálování Azure přidělí tuto instanci jenom v případě, že je dostupná kapacita, ale pro tyto instance neexistuje žádná smlouva SLA. Sada škálování na místě je nasazená v jedné doméně selhání a neposkytuje žádné záruky vysoké dostupnosti.

> [!IMPORTANT]
> Instance přímých instancí jsou momentálně ve verzi Public Preview.
> Tato verze Preview se nedoporučuje pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> V první části verze Public Preview budou mít instance pro tisk pevnou cenu, takže se neúčtují žádné vyřazení na základě cen.

## <a name="pricing"></a>Ceny

Ceny pro instance přímých instancí jsou proměnné na základě oblastí a SKU. Další informace najdete v tématu ceny pro [Linux](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) a [Windows](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/). 


S proměnnými cenami máte možnost nastavit maximální cenu v USD (USD), která používá až 5 desetinných míst. Například hodnota `0.98765`by byla maximální cena $0,98765 USD za hodinu. Pokud nastavíte maximální cenu, která se má `-1`, instance se nevyřadí na základě ceny. Cena za instanci bude aktuální cena za cenu nebo cena standardní instance, která je stále menší, pokud je k dispozici kapacita a kvóta.

## <a name="eviction-policy"></a>Zásada vyřazení

Při vytváření sad s přímým škálováním můžete nastavit zásadu vyřazení, aby se nastavilo zrušení *přidělení* (výchozí) nebo *odstranění*. 

Zásady zrušení *přidělení* přesouvá vaše vyřazené instance do stavu Zastaveno (přidělení zrušeno), což vám umožní znovu nasadit vyřazené instance. Neexistuje však záruka, že přidělení bude úspěšné. Navrácené virtuální počítače se budou počítat s kvótou instance sady škálování a budou se vám účtovat vaše základní disky. 

Pokud chcete, aby se vaše instance na škále vašich přímých škálování odstranily při jejich vyřazení, můžete nastavit zásadu vyřazení, která se má *Odstranit*. Když je zásada vyřazení nastavená tak, aby se odstranila, můžete vytvořit nové virtuální počítače tím, že zvýšíte vlastnost počet instancí sady škálování. Vyřazení virtuálních počítačů se odstraní společně s jejich podkladovým diskům, takže se za úložiště nebudete účtovat. K automatickému vyzkoušení a kompenzaci vydaných virtuálních počítačů můžete použít také funkci automatického škálování sad škálování, ale nezaručujeme, že přidělení bude úspěšné. Pokud nastavíte zásadu vyřazení na hodnotu odstranit, doporučujeme vám používat jenom funkci automatického škálování na škále bodů obnovení, abyste se vyhnuli nákladům na vaše disky a omezeními kvót. 

Uživatelé se můžou přihlásit k přijímání oznámení v rámci virtuálního počítače prostřednictvím [Azure Scheduled Events](../virtual-machines/linux/scheduled-events.md). To vám upozorní na to, jestli se virtuální počítače vyloučí a že budete mít 30 sekund na dokončení všech úloh a před vyřazením provést úlohy vypnutí. 


## <a name="deploying-spot-vms-in-scale-sets"></a>Nasazení virtuálních počítačů na místě v sadách škálování

Pokud chcete nasadit virtuální počítače na místě v sadě škálování, můžete nastavit příznak nové *priority* tak, aby byl *bodový*. Všechny virtuální počítače ve vaší sadě škálování budou nastavené na bodové. Pokud chcete vytvořit sadu škálování s virtuálními počítači, použijte jednu z následujících metod:
- [Azure Portal](#portal)
- [Azure CLI](#azure-cli)
- [Azure PowerShell](#powershell)
- [Šablony Azure Resource Manager](#resource-manager-templates)

## <a name="portal"></a>Portál

Proces vytvoření sady škálování, která používá virtuální počítače na místě, je stejný, jak je popsáno v [článku Začínáme](quick-create-portal.md). Když nasazujete sadu škálování, můžete nastavit příznak bodu a zásadu vyřazení: ![vytvořit sadu škálování s virtuálními počítači s přímým použitím](media/virtual-machine-scale-sets-use-spot/vmss-spot-portal-max-price.png)


## <a name="azure-cli"></a>Azure CLI

Proces vytvoření sady škálování se stejnými virtuálními počítači je stejný, jak je popsáno v [článku Začínáme](quick-create-cli.md). Stačí přidat klíčové slovo--priority a přidat `--max-price`. V tomto příkladu používáme `-1` pro `--max-price`, takže instance nebude vyřazení na základě ceny.

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1 
```

## <a name="powershell"></a>PowerShell

Proces vytvoření sady škálování se stejnými virtuálními počítači je stejný, jak je popsáno v [článku Začínáme](quick-create-powershell.md).
Stačí přidat klíčové slovo "– prioritní" a zadat `-max-price` do [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig).

```powershell
$vmssConfig = New-AzVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Priority "Spot" `
    --max-price -1
```

## <a name="resource-manager-templates"></a>Šablony Správce prostředků

Proces vytvoření sady škálování, která používá bodové virtuální počítače, je stejný, jak je popsáno v článku Začínáme pro [Linux](quick-create-template-linux.md) nebo [Windows](quick-create-template-windows.md). Přidejte do šablony vlastnost priority pro typ prostředku *Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachineProfile* a jako hodnotu zadejte *bod* . Ujistěte se, že používáte verzi rozhraní API *2019-03-01* nebo vyšší. 

Chcete-li nastavit zásadu vyřazení na odstranění, přidejte parametr ' evictionPolicy ' a nastavte jej pro *odstranění*.

Následující příklad vytvoří v *středozápadní USA*sadu s možností škálování na platformě Linux s názvem *myScaleSet* , která *odstraní* virtuální počítače v sadě škálování nastavené na vyřazení:

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2019-03-01",
  "sku": {
    "name": "Standard_DS2_v2",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
       "priority": "Spot",
       "evictionPolicy": "delete",
       "storageProfile": {
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage"
        },
        "imageReference":  {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04-LTS",
          "version": "latest"
        }
      },
      "osProfile": {
        "computerNamePrefix": "myvmss",
        "adminUsername": "azureuser",
        "adminPassword": "P@ssw0rd!"
      }
    }
  }
}
```
## <a name="faq"></a>Časté otázky

**Otázka:** Po vytvoření je stejná jako instance stejné jako standardní instance?

**A:** Ano, s výjimkou smlouvy SLA pro virtuální počítače na místě a jejich vyřazení z provozu kdykoli se dá provést.


**Otázka:** Co dělat při vyřazení, ale stále potřebují kapacitu?

**A:** Pokud potřebujete kapacitu hned, doporučujeme použít virtuální počítače místo přímých virtuálních počítačů.


**Otázka:** Jak se Správa kvót spravuje pro místo?

**A:** Instance bodů a standardní instance budou mít samostatné fondy kvót. Kvóta na místě se bude sdílet mezi virtuálními počítači a instancemi sady škálování. Další informace najdete v tématu [Limity, kvóty a omezení předplatného a služeb Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).


**Otázka:** Můžu požádat o další kvótu na místě?

**A:** Ano, žádost budete moci odeslat, abyste zvýšili kvótu pro virtuální počítače pomocí [procesu žádosti o standardní kvótu](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests).


**Otázka:** Můžu převést existující sady škálování na škálované sady škálování?

**A:** Ne, nastavení příznaku `Spot` se podporuje jenom v době vytváření.


**Otázka:** Pokud používám `low` pro sady škálování s nízkou prioritou, musím místo toho začít používat `Spot`?

**A:** V současné době budou `low` i `Spot` fungovat, ale měli byste začít s přechodem na `Spot`.


**Otázka:** Můžu vytvořit sadu škálování s pravidelnými virtuálními počítači i s virtuálními počítači?

**A:** Ne, sada škálování nepodporuje více než jeden typ priority.


**Otázka:**  Můžu používat automatické škálování se sadami škálování na místě?

**A:** Ano, můžete nastavit pravidla automatického škálování pro sadu škálování na místě. Pokud jsou vaše virtuální počítače vyřazené, automatické škálování se může pokusit vytvořit nové virtuální počítače na místě. Nezapomeňte, že tuto kapacitu nezaručujete. 


**Otázka:**  Funguje automatické škálování podle zásad vyřazení (navrácení a odstranění)?

**A:** Doporučuje se nastavit zásadu vyřazení, která se má odstranit při použití automatického škálování. Důvodem je to, že nepřidělené instance se počítají na základě počtu kapacit v sadě škálování. Při použití automatického škálování se pravděpodobně vám v důsledku navrácených instancí dokončí počet cílových instancí rychleji. 


**Otázka:** Jaké kanály podporují přímé virtuální počítače?

**A:** V následující tabulce najdete informace o dostupnosti virtuálních počítačů.

<a name="channel"></a>

| Kanály Azure               | Dostupnost virtuálních počítačů Azure       |
|------------------------------|-----------------------------------|
| Smlouva Enterprise         | Ano                               |
| Průběžné platby                | Ano                               |
| Poskytovatel cloudových služeb (CSP) | [Obraťte se na svého partnera.](https://docs.microsoft.com/partner-center/azure-plan-get-started) |
| Výhody                     | Není k dispozici                     |
| Sponzorováno                    | Není k dispozici                     |
| Bezplatná zkušební verze                   | Není k dispozici                     |


**Otázka:** Kde můžu publikovat otázky?

**A:** Svůj dotaz můžete vystavit a označit pomocí `azure-spot` na [Q & A](https://docs.microsoft.com/answers/topics/azure-spot.html). 

## <a name="next-steps"></a>Další kroky
Teď, když jste vytvořili sadu škálování s virtuálními počítači, zkuste nasadit naši [šablonu automatického škálování s využitím bodu](https://github.com/Azure/vm-scale-sets/tree/master/preview/lowpri).

Podrobnosti o cenách najdete na [stránce s cenami sady škálování virtuálních počítačů](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) .

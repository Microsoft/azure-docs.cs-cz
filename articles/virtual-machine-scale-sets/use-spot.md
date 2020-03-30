---
title: Vytvoření škálovací sady, která používá virtuální počítače Azure Spot (Preview)
description: Zjistěte, jak vytvořit škálovací sady virtuálních strojů Azure, které používají virtuální počítače Spot k úspoře nákladů.
author: cynthn
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: cynthn
ms.openlocfilehash: 37e914fe6bafe9587be525faf3e01c897cdd8230
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162680"
---
# <a name="preview-azure-spot-vms-for-virtual-machine-scale-sets"></a>Předběžná verze: Virtuální počítače Azure Spot pro škálovací sady virtuálních strojů 

Použití Azure Spot na škálovacích sadách vám umožní využít naši nevyužitou kapacitu s výraznými úsporami nákladů. Kdykoli v okamžiku, kdy Azure potřebuje kapacitu zpět, infrastruktura Azure vystěhovává instance Spot. Instance Spot jsou proto skvělé pro úlohy, které mohou zpracovávat přerušení, jako jsou úlohy dávkového zpracování, vývojová a testovací prostředí, velké výpočetní úlohy a další.

Množství dostupné kapacity se může lišit v závislosti na velikosti, oblasti, denní době a dalších. Při nasazování instancí Spot na škálovacísady Azure přidělí instanci pouze v případě, že je k dispozici kapacita, ale pro tyto instance neexistuje žádná sla. Škálovací sada Spot se nasadí v jedné doméně selhání a nenabízí žádné záruky vysoké dostupnosti.

> [!IMPORTANT]
> Instance spotů jsou aktuálně ve verzi Public Preview.
> Tato verze preview se nedoporučuje pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="pricing"></a>Ceny

Ceny pro spotové instance jsou variabilní na základě oblasti a skladové položky. Další informace naleznete v tématu ceny pro [Linux](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) a [Windows](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/). 


S variabilními cenami máte možnost nastavit maximální cenu v amerických dolarech (USD) s použitím až 5 desetinných míst. Například hodnota `0.98765`by byla maximální cena $0.98765 USD za hodinu. Pokud nastavíte maximální `-1`cenu , instance nebude vystěhována na základě ceny. Cena instance bude aktuální cena spotu nebo cena za standardní instanci, která je vždy nižší, pokud je k dispozici kapacita a kvóta.

## <a name="eviction-policy"></a>Politika vystěhovaní

Při vytváření škálovacích sad Bodů můžete nastavit zásadu vyřazení na *Navrátit* (výchozí) nebo *Odstranit*. 

Zásada *Nadeřit* přesune vaše vyřazené instance do zastaveného stavu, který umožňuje znovu nasadit vyřazené instance. Neexistuje však žádná záruka, že přidělení bude úspěšné. Přidělené virtuální počítače se započítávají do kvóty instance škálovací sady a budou se vám účtovat podkladové disky. 

Pokud chcete, aby byly vaše instance ve škálovací sadě Bodů odstraněny při jejich vyřazení, můžete nastavit zásadu vyřazení tak, aby byla *odstraněna*. Se zásadou vyřazení nastavenou na odstranění můžete vytvořit nové virtuální počítačky zvýšením vlastnosti počet instancí sady škálování. Vyřazené virtuální počítače se odstraní společně s jejich základní disky, a proto se vám nebude účtovat za úložiště. Funkci automatického škálování škálovacích sad můžete také použít k automatickému pokusu o kompenzaci vyřazených virtuálních jevů, ale neexistuje žádná záruka, že přidělení bude úspěšné. Doporučujeme používat funkci automatického škálování pouze v sadách škálovacích stupňů Spot, když nastavíte zásadu vyřazení, aby se zabránilo nákladům na disky a dosažení limitů kvót. 

Uživatelé se můžou přihlásit k odběru oznámení ve virtuálním počítači prostřednictvím [naplánovaných událostí Azure](../virtual-machines/linux/scheduled-events.md). To vás upozorní, pokud vaše virtuální počítače jsou vyřazovány a budete mít 30 sekund k dokončení všech úloh a provedení úloh vypnutí před vyřazení. 


## <a name="deploying-spot-vms-in-scale-sets"></a>Nasazení virtuálních virtuálních jevů ve škálovacích sadách

Chcete-li nasadit virtuální virtuální počítači Spot ve škálovacích sadách, můžete nastavit nový příznak *Priority* na *bod .* Všechny virtuální počítače ve vaší škálovací sadě se nastaví na spot. Chcete-li vytvořit škálovací sadu pomocí virtuálních virtuálních měn Spot, použijte jednu z následujících metod:
- [Portál Azure](#portal)
- [Azure CLI](#azure-cli)
- [Azure PowerShell](#powershell)
- [Šablony Azure Resource Manageru](#resource-manager-templates)

## <a name="portal"></a>Portál

Proces vytvoření škálovací sady, která používá virtuální virtuální chod spotů, je stejný jako podrobně popsaný v [článku Začínáme](quick-create-portal.md). Při nasazování škálovací sady můžete nastavit příznak Spot a zásady vyřazování: ![Vytvořte škálovací sadu pomocí virtuálních počítačích Spot.](media/virtual-machine-scale-sets-use-spot/vmss-spot-portal-max-price.png)


## <a name="azure-cli"></a>Azure CLI

Proces vytvoření škálovací sady s virtuálními virtuálními ms spotů je stejný jako podrobně popsaný v [článku Začínáme](quick-create-cli.md). Stačí přidat '--Priority Spot' `--max-price`a přidat . V tomto příkladu `-1` `--max-price` používáme pro tak instance nebude vystěhována na základě ceny.

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

Proces vytvoření škálovací sady s virtuálními virtuálními ms spotů je stejný jako podrobně popsaný v [článku Začínáme](quick-create-powershell.md).
Stačí přidat '-Priority Spot', `-max-price` a dodat [new-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig).

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

Proces vytvoření škálovací sady, která používá virtuální počítače Spot, je stejný jako podrobně popsaný v článku Začínáme pro [Linux](quick-create-template-linux.md) nebo [Windows](quick-create-template-windows.md). 

Pro nasazení šablony Spot`"apiVersion": "2019-03-01"` použijte nebo novější. Přidejte `priority` `evictionPolicy` vlastnosti `billingProfile` a `"virtualMachineProfile":` do oddílu v šabloně: 

```json
                "priority": "Spot",
                "evictionPolicy": "Deallocate",
                "billingProfile": {
                    "maxPrice": -1
                }
```

Chcete-li instanci odstranit po vyřazení, změňte `evictionPolicy` parametr na `Delete`.

## <a name="faq"></a>Nejčastější dotazy

**Otázka:** Je instance Spot po vytvoření stejná jako standardní instance?

**A:** Ano, kromě toho, že neexistuje žádná smlouva SLA pro virtuální virtuální chod spotů a mohou být kdykoli vystěhovány.


**Otázka:** Co dělat, když se vystěhují, ale stále potřebujete kapacitu?

**A:** Doporučujeme používat standardní virtuální chod místo virtuálních virtuálních měn spot, pokud potřebujete kapacitu hned.


**Otázka:** Jak se spravuje kvóta pro spot?

**A:** Přímé instance a standardní instance budou mít samostatné fondy kvót. Kvóta na místě se bude sdílet mezi virtuálními stránkami a instancemi škálovací sady. Další informace najdete v tématu [Limity, kvóty a omezení předplatného a služeb Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).


**Otázka:** Mohu požádat o další kvótu pro Spot?

**A:** Ano, budete moci odeslat žádost o zvýšení kvóty pro virtuální počítače Spot prostřednictvím [procesu standardní žádosti o kvótu](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests).


**Otázka:** Mohu převést stávající škálovací sady na sady bodových měřítek?

**A:** Ne, nastavení `Spot` příznaku je podporováno pouze v době vytvoření.


**Otázka:** Pokud jsem `low` používal pro škálovací sady s nízkou `Spot` prioritou, musím místo toho začít používat?

**A:** Pro tuto `low` chvíli, a to jak a `Spot` bude `Spot`fungovat, ale měli byste začít přechod na používání .


**Otázka:** Můžu vytvořit škálovací sadu s běžnými virtuálními aplikacemi i virtuálními virtuálními aplikacemi spot?

**A:** Ne, škálovací sada nemůže podporovat více než jeden typ priority.


**Otázka:**  Mohu používat automatické škálování se sadami bodových měřítek?

**A:** Ano, v škálovací sadě Bodů můžete nastavit pravidla automatického škálování. Pokud jsou vaše virtuální počítače vyřazeny, automatické škálování se může pokusit vytvořit nové virtuální počítače Spot. Pamatujte si, že není zaručena tato kapacita ačkoli. 


**Otázka:**  Funguje automatické škálování s oběma zásadami vyřazování (navrátit a odstranit)?

**A:** Doporučujeme nastavit zásady vyřazení odstranit při použití automatického škálování. Důvodem je, že problémové instance se počítají s počtem kapacit na škálovací sadě. Při použití automatického škálování pravděpodobně rychle zasáhnete počet cílových instancí kvůli přidělených, vyřazených instancí. 


**Otázka:** Jaké kanály podporují spotové virtuální aplikace?

**A:** Dostupnost virtuálního virtuálního bodu najdete v tabulce níže.

<a name="channel"></a>

| Kanály Azure               | Dostupnost virtuálních počítače Azure Spot       |
|------------------------------|-----------------------------------|
| Smlouva Enterprise         | Ano                               |
| Pay As You Go                | Ano                               |
| Poskytovatel cloudových služeb (CSP) | [Kontaktujte svého partnera](https://docs.microsoft.com/partner-center/azure-plan-get-started) |
| Výhody                     | Není k dispozici.                     |
| Sponzorované                    | Není k dispozici.                     |
| Bezplatná zkušební verze                   | Není k dispozici.                     |


**Otázka:** Kde mohu psát otázky?

**A:** Svůj dotaz můžete zveřejnit `azure-spot` a označit na [q&A](https://docs.microsoft.com/answers/topics/azure-spot.html). 

## <a name="next-steps"></a>Další kroky
Teď, když jste vytvořili škálovací sadu s virtuálními virtuálními aplikacemi Spot, zkuste nasadit naši [šablonu automatického škálování pomocí spotu](https://github.com/Azure/vm-scale-sets/tree/master/preview/lowpri).

Podívejte se na [stránku s cenami škálovací sady virtuálních strojů, kde](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) najdete podrobnosti o cenách.

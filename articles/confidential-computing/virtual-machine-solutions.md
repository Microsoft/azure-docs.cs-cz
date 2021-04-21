---
title: Řešení důvěrného výpočetního prostředí Azure na virtuálních počítačích
description: Přečtěte si informace o řešeních důvěrného výpočetního prostředí Azure na virtuálních počítačích.
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: JenCook
ms.openlocfilehash: 580c53f311bc8ee70e974df2bc4111e6361d06f6
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818957"
---
# <a name="solutions-on-azure-virtual-machines"></a>Řešení na virtuálních počítačích Azure

Tento článek obsahuje informace o nasazení virtuálních počítačů Azure s důvěrnými výpočetními platformami, které používají procesory Intel zajištěné [rozšířením Intel software Guard](https://software.intel.com/sgx) (Intel SGX). 

## <a name="azure-confidential-computing-vm-sizes"></a>Velikosti virtuálních počítačů pro důvěrné výpočetní služby Azure

Důvěrné výpočetní virtuální počítače Azure jsou navržené tak, aby chránily důvěrnost a integritu vašich dat a kódu během zpracování v cloudu. 

[DCsv2-Series](../virtual-machines/dcv2-series.md) Virtuální počítače jsou nejnovější a nejaktuálnější rodina velikosti důvěrného zpracování. Tyto virtuální počítače podporují větší rozsah možností nasazení, mají dvojnásobnou velikost mezipaměti enklávy Page (EPC) a větší výběr velikostí v porovnání s našimi DC-Series virtuálními počítači. Virtuální počítače [DC-Series](../virtual-machines/sizes-previous-gen.md#preview-dc-series) jsou momentálně ve verzi Preview a budou zastaralé a nejsou zahrnuté do všeobecné dostupnosti.

Začněte s nasazením DCsv2-Series virtuálního počítače přes komerční tržiště Microsoftu pomocí [kurzu rychlý Start](quick-create-marketplace.md).

### <a name="current-available-sizes-and-regions"></a>Aktuální dostupné velikosti a oblasti

Pokud chcete získat seznam všech všeobecně dostupných důvěrných velikost výpočetního virtuálního počítače v dostupných oblastech a zónách dostupnosti, spusťte v [Azure CLI](/cli/azure/install-azure-cli-windows)tento příkaz:

```azurecli-interactive
az vm list-skus `
    --size dc `
    --query "[?family=='standardDCSv2Family'].{name:name,locations:locationInfo[0].location,AZ_a:locationInfo[0].zones[0],AZ_b:locationInfo[0].zones[1],AZ_c:locationInfo[0].zones[2]}" `
    --all `
    --output table
```

Podrobnější pohled na výše uvedené velikosti získáte spuštěním následujícího příkazu:

```azurecli-interactive
az vm list-skus `
    --size dc `
    --query "[?family=='standardDCSv2Family']"
```
### <a name="dedicated-host-requirements"></a>Požadavky na vyhrazený hostitel
Nasazení **Standard_DC8_v2** velikosti virtuálního počítače ve skupině virtuálních počítačů DCSv2-Series zachová celého hostitele a nebude se sdílet s ostatními klienty nebo předplatnými. Tato rodina SKU virtuálního počítače poskytuje izolaci, kterou možná budete potřebovat, aby splňovala zákonné požadavky na dodržování předpisů a zabezpečení, které jsou obvykle splněné pomocí vyhrazené hostitelské služby. Když zvolíte **Standard_DC8_v2** SKU, fyzický hostitelský server bude přidělovat všechny dostupné hardwarové prostředky, včetně paměti EPC jenom pro virtuální počítač. Upozorňujeme, že tato funkce existuje v rámci návrhu infrastruktury a všechny funkce **Standard_DC8_v2** budou podporované. Toto nasazení není stejné jako služba [vyhrazeného hostitele Azure](../virtual-machines/dedicated-hosts.md) , kterou poskytují jiné rodiny virtuálních počítačů Azure.


## <a name="deployment-considerations"></a>Aspekty nasazování

Postupujte podle kurzu rychlý Start a nasaďte DCsv2-Series virtuální počítač za méně než 10 minut. 

- **Předplatné Azure** – Pokud chcete nasadit instanci cloudového počítače s důvěrným výpočetním prostředím, zvažte předplatné s průběžnými platbami nebo jinou možnost nákupu. Pokud používáte [bezplatný účet Azure](https://azure.microsoft.com/free/), nebudete mít kvótu pro příslušné množství výpočetních jader Azure.

- **Ceny a regionální dostupnost** – najdete na stránce s cenami pro DCsv2-Series virtuální počítače na [stránce s cenami virtuálních počítačů](https://azure.microsoft.com/pricing/details/virtual-machines/linux/). Podívejte se na [produkty dostupné v oblasti a](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) dostupnost v oblastech Azure.


- **Kvóty jader** – možná bude potřeba zvýšit kvótu jader v předplatném Azure z výchozí hodnoty. Vaše předplatné může také omezit počet jader, které můžete nasadit v určitých rodinách velikostí virtuálních počítačů, včetně řady DCsv2-Series. Chcete-li požádat o zvýšení kvóty, [otevřete online žádost o zákaznickou podporu](../azure-portal/supportability/per-vm-quota-requests.md) zdarma. Všimněte si, že výchozí omezení se můžou lišit v závislosti na vaší kategorii předplatného.

  > [!NOTE]
  > Pokud máte velké nároky na kapacitu, obraťte se na podporu Azure. Kvóty Azure jsou úvěrovými limity, které nezaručují kapacitu. Bez ohledu na vaši kvótu se účtují jenom ty jádra, které používáte.
  
- **Změna velikosti** – z důvodu jejich specializovaného hardwaru můžete měnit velikost důvěrných výpočetních instancí v rámci stejné řady velikostí. Například můžete změnit velikost virtuálního počítače DCsv2-Series jenom z jedné velikosti řady DCsv2-Series na jinou. Změna velikosti nedůvěrného výpočetního prostředí na velikost důvěrného zpracování není podporovaná.  

- **Image** – pro zajištění podpory rozšíření Intel SGX (software Guard Extension) na důvěrných výpočetních instancích musí být všechna nasazení spuštěná na imagí 2. generace. Důvěrné výpočetní prostředí Azure podporuje úlohy běžící na Ubuntu 18,04 Gen 2, Ubuntu 20,04 Gen 2, Windows Server 2019 Gen2 a Windows Server 2016 Gen 2. Přečtěte si o [podpoře pro virtuální počítače 2. generace v Azure](../virtual-machines/generation-2.md) , kde najdete další informace o podporovaných a nepodporovaných scénářích. 

- **Storage** – datové disky s důvěrnými výpočetními virtuálními počítači Azure a naše dočasné disky s operačním systémem jsou na discích NVMe. Instance podporují pouze SSD úrovně Premium a SSD úrovně Standard disky, nikoli SSD úrovně Ultra nebo HDD úrovně Standard. Velikost virtuálního počítače **DC8_v2** nepodporuje Prémiové úložiště. 

- **Šifrování disku** – důvěrné výpočetní instance v tuto chvíli nepodporují šifrování disku. 

## <a name="high-availability-and-disaster-recovery-considerations"></a>Požadavky na vysokou dostupnost a zotavení po havárii

Při používání virtuálních počítačů v Azure zodpovídáte za implementaci řešení vysoké dostupnosti a zotavení po havárii, abyste se vyhnuli výpadkům. 

Důvěrné výpočetní prostředí Azure v tuto chvíli nepodporuje redundanci zóny prostřednictvím Zóny dostupnosti. Pro zajištění nejvyšší dostupnosti a redundance pro důvěrný výpočetní prostředí použijte [skupiny dostupnosti](../virtual-machines/availability-set-overview.md). Kvůli hardwarovým omezením můžou skupiny dostupnosti pro instance s důvěrnými výpočetními instancemi mít maximálně 10 aktualizačních domén. 

## <a name="deployment-with-azure-resource-manager-arm-template"></a>Nasazení pomocí šablony Azure Resource Manager (ARM)

Azure Resource Manager je služba nasazování a správy pro Azure. Poskytuje vrstvu pro správu, která umožňuje vytvářet, aktualizovat a odstraňovat prostředky v předplatném Azure. K zabezpečení a uspořádání prostředků po nasazení můžete použít funkce správy, jako je řízení přístupu, zámky a značky.

Další informace o šablonách ARM najdete v tématu [přehled Template Deployment](../azure-resource-manager/templates/overview.md).

Pokud chcete nasadit DCsv2-Series virtuální počítač v šabloně ARM, budete používat [prostředek virtuálního počítače](../virtual-machines/windows/template-description.md). Ujistěte se, že jste zadali správné vlastnosti pro **vmSize** a pro **element imagereference**.

### <a name="vm-size"></a>Velikost virtuálního počítače

Zadejte jednu z následujících velikostí v šabloně ARM v prostředku virtuálního počítače. Tento řetězec je vložen jako **vmSize** ve **vlastnostech**.

```json
  [
        "Standard_DC1s_v2",
        "Standard_DC2s_v2",
        "Standard_DC4s_v2",
        "Standard_DC8_v2"
      ],
```

### <a name="gen2-os-image"></a>Image operačního systému Gen2

V části **vlastnosti** bude také nutné odkazovat na obrázek v rámci **storageProfile**. Pro **element imagereference** použijte *jenom jednu* z následujících imagí.

```json
      "2019-datacenter-gensecond": {
        "offer": "WindowsServer",
        "publisher": "MicrosoftWindowsServer",
        "sku": "2019-datacenter-gensecond",
        "version": "latest"
      },
      "2016-datacenter-gensecond": {
        "offer": "WindowsServer",
        "publisher": "MicrosoftWindowsServer",
        "sku": "2016-datacenter-gensecond",
        "version": "latest"
      },
        "18_04-lts-gen2": {
        "offer": "UbuntuServer",
        "publisher": "Canonical",
        "sku": "18_04-lts-gen2",
        "version": "latest"
      },
      "20_04-lts-gen2": {
        "offer": "UbuntuServer",
        "publisher": "Canonical",
        "sku": "20_04-lts-gen2",
        "version": "latest"
      }
```

## <a name="next-steps"></a>Další kroky 

V tomto článku jste se dozvěděli o kvalifikaci a konfiguracích potřebných při vytváření důvěrného výpočetního virtuálního počítače. Nyní můžete Microsoft Azure Marketplace k nasazení DCsv2-Series virtuálního počítače.

> [!div class="nextstepaction"]
> [Nasazení virtuálního počítače s DCsv2-Series v Azure Marketplace](quick-create-marketplace.md)
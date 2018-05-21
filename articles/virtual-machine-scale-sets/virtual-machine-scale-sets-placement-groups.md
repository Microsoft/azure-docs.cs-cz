---
title: Práce s velkými škálovacími sadami virtuálních počítačů Azure | Dokumentace Microsoftu
description: Co potřebujete vědět k používání velkých škálovacích sad virtuálních počítačů Azure
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/9/2017
ms.author: negat
ms.openlocfilehash: 17c8fdd0bc85b9d1a4e1b50cf422b28f32862a7e
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="working-with-large-virtual-machine-scale-sets"></a>Práce s velkými škálovacími sadami virtuálních počítačů
Nyní můžete vytvořit [škálovací sady virtuálních počítačů](/azure/virtual-machine-scale-sets/) Azure s kapacitou až 1 000 virtuálních počítačů. V tomto dokumentu je _velká škálovací sada virtuálních počítačů_ definována jako škálovací sada umožňující škálování na více než 100 virtuálních počítačů. Tato funkce se nastavuje pomocí vlastnosti škálovací sady (_singlePlacementGroup=False_). 

Některé aspekty velkých škálovacích sad, například vyrovnávání zatížení a domény selhání, se chovají jinak než u standardních škálovacích sad. Tento dokument vysvětluje charakteristiky velkých škálovacích sad a popisuje vše, co potřebujete vědět pro jejich úspěšné použití ve vašich aplikacích. 

Běžným přístupem k nasazení rozsáhlé cloudové infrastruktury je vytvoření sady _jednotek škálování_, například vytvořením několika škálovacích sad virtuálních počítačů ve více virtuálních sítích a účtech úložiště. Tento přístup umožňuje v porovnání s jednotlivými virtuálními počítači snadnější správu, a více jednotek škálování je užitečných pro mnoho aplikací, zejména pro ty, které vyžadují další stohovatelné komponenty, jako jsou virtuální sítě a koncové body. Pokud ale vaše aplikace vyžaduje jeden velký cluster, může být jednodušší nasadit jedinou škálovací s až 1 000 virtuálních počítačů. Ukázkové scénáře zahrnují centralizovaná nasazení pro velké objemy dat nebo výpočetní sítě, které vyžadují jednoduchou správu velkého fondu pracovních uzlů. [Připojené datové disky](virtual-machine-scale-sets-attached-disks.md) v kombinaci se škálovací sadou virtuálních počítačů umožňují jedinou operací nasadit škálovatelnou infrastrukturu složenou z tisíců virtuálních procesorů a petabajtů úložišť.

## <a name="placement-groups"></a>Skupiny umístění 
_Velké_ škálovací sady nejsou speciální kvůli počtu virtuálních počítačů, ale kvůli počtu _skupin umístění_, které obsahují. Skupina umístění je konstrukce podobná skupině dostupnosti Azure s vlastními doménami selhání a upgradovacími doménami. Ve výchozím nastavení škálovací sada obsahuje jedinou skupinu umístění s maximální velikostí 100 virtuálních počítačů. Pokud je vlastnost škálovací sady _singlePlacementGroup_ nastavena na hodnotu _false_, škálovací sada se může skládat z více skupin umístění a z 0–1 000 virtuálních počítačů. Když je nastavena výchozí hodnota _true_, škálovací sada se skládá z jediné skupiny umístění a z 0–100 virtuálních počítačů.

## <a name="checklist-for-using-large-scale-sets"></a>Kontrolní seznam pro používání velkých škálovací sad
Následující požadavky vám pomůžou rozhodnout, jestli vaše aplikace můžou efektivně využívat velké škálovací sady:

- Velké škálovací sady vyžadují službu Azure Managed Disks. Škálovací sady vytvořené bez disků služby Managed Disks vyžadují více účtů úložiště (jeden na každých 20 virtuálních počítačů). Velké škálovací sady jsou navržené pro práci výhradně se službou Managed Disks z důvodu snížení režijních nákladů na správu úložiště. Také se díky tomu vyhnete riziku, že narazíte na omezení předplatného pro účty úložiště. Pokud nepoužíváte službu Managed Disks, vaše škálovací sada je omezena na 100 virtuálních počítačů.
- Škálovací sady vytvořené z imagí Azure Marketplace je možné škálovat až na 1 000 virtuálních počítačů.
- Škálovací sady vytvořené z vlastních imagí (image virtuálních počítačů, které si vytvoříte a nahrajete sami) je aktuálně možné škálovat až na 300 virtuálních počítačů.
- Vyrovnávání zatížení úrovně 4 pomocí škálovacích sad, které se skládají z více skupin umístění, vyžaduje [skladovou položku služby Azure Load Balancer úrovně Standard](../load-balancer/load-balancer-standard-overview.md). Skladová položka služby Load Balancer úrovně Standard poskytuje další výhody, jako je například možnost vyrovnávat zatížení mezi několika škálovacími sadami. Standardní skladová položka také vyžaduje, aby škálovací sada byla přidružena ke skupině zabezpečení sítě, jinak fondy NAT nebudou správně fungovat. Pokud potřebujete použít skladovou položku služby Azure Load Balancer úrovně Basic, ujistěte se, že je škálovací sada nakonfigurována k používání jediné skupiny umístění, což je výchozí nastavení.
- Vyrovnávání zatížení úrovně 7 pomocí služby Azure Application Gateway je podporováno pro všechny škálovací sady.
- Škálovací sada je definována s jednou podsítí – ujistěte se, že má vaše podsíť dostatečně velký adresní prostor pro všechny požadované virtuální počítače. Škálovací sada ve výchozím nastavení provádí nadměrné zřizování (během nasazování nebo při horizontálním navyšováním kapacity vytváří virtuální počítače navíc, které se vám neúčtují) pro zvýšení spolehlivosti nasazení a výkonu. Počítejte s adresním prostorem o 20 % větším, než je počet virtuálních počítačů, na který plánujete škálovat.
- Pokud plánujete nasazení mnoha virtuálních počítačů, možná bude nutné navýšit vaše omezení základní kvóty virtuálních procesorů pro službu Compute.
- Domény selhání a upgradovací domény jsou konzistentní pouze v rámci skupiny umístění. Tato architektura nemění celkovou dostupnost škálovací sady, protože virtuální počítače jsou rovnoměrně distribuované mezi rozdílný fyzický hardware. Znamená to ale, že pokud potřebujete zajistit, aby byly dva virtuální počítače na různém hardwaru, nesmíte je zapomenout umístit do různých domén selhání ve stejné skupině umístění. Doména selhání a ID skupiny umístění jsou zobrazené v _zobrazení instance_ virtuálního počítače škálovací sady. Zobrazení instance virtuálního počítače škálovací sady můžete zobrazit v [Průzkumníku prostředků Azure](https://resources.azure.com/).


## <a name="creating-a-large-scale-set"></a>Vytvoření velké škálovací sady
Při vytváření škálovací sady na webu Azure Portal můžete povolit její škálování do více skupin umístění nastavením možnosti _Limit to a single placement group_ (Omezit na jedinou skupinu umístění) na hodnotu _False_ v okně _Basics_ (Základní údaje). Když je tato možnost nastavena na _False_, můžete zadat hodnotu _Instance count_ (Počet instancí) až 1 000.

![](./media/virtual-machine-scale-sets-placement-groups/portal-large-scale.png)

K vytvoření velké škálovací sady virtuálních počítačů můžete použít příkaz [Azure CLI](https://github.com/Azure/azure-cli) _az vmss create_. Tento příkaz v závislosti na argumentu _instance-count_ nastaví inteligentní výchozí hodnoty, jako například velikost podsítě:

```bash
az group create -l southcentralus -n biginfra
az vmss create -g biginfra -n bigvmss --image ubuntults --instance-count 1000
```
Příkaz _vmss create_ použije určité výchozí konfigurační hodnoty, pokud nezadáte jiné. Pokud chcete zobrazit dostupné možnosti, které můžete přepsat, vyzkoušejte:
```bash
az vmss create --help
```

Pokud vytváříte velkou škálovací sadu s využitím šablony Azure Resource Manageru, ujistěte se, že šablona vytváří škálovací sadu založenou na službě Azure Managed Disks. Vlastnost _singlePlacementGroup_ můžete nastavit na hodnotu _false_ v části _vlastnosti_ prostředku _Microsoft.Compute/virtualMAchineScaleSets_. Následující fragment ve formátu JSON ukazuje začátek šablony škálovací sady, která zahrnuje nastavení kapacity na 1 000 virtuálních počítačů a nastavení vlastnosti _"singlePlacementGroup" : false_:
```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "location": "australiaeast",
  "name": "bigvmss",
  "sku": {
    "name": "Standard_DS1_v2",
    "tier": "Standard",
    "capacity": 1000
  },
  "properties": {
    "singlePlacementGroup": false,
    "upgradePolicy": {
      "mode": "Automatic"
    }
```
Kompletní příklad šablony velké škálovací sady najdete na adrese [https://github.com/gbowerman/azure-myriad/blob/master/bigtest/bigbottle.json](https://github.com/gbowerman/azure-myriad/blob/master/bigtest/bigbottle.json).

## <a name="converting-an-existing-scale-set-to-span-multiple-placement-groups"></a>Převod existující škálovací sady do více skupin umístění
Pokud chcete stávající škálovací sadu virtuálních počítačů rozšířit na více než 100 virtuálních počítačů, musíte v modelu škálovací sady změnit hodnotu vlastnosti _singlePlacementGroup_ na _false_. Změnu této vlastnosti můžete otestovat pomocí [Průzkumníka prostředků Azure](https://resources.azure.com/). Vyhledejte existující škálovací sadu, vyberte _Upravit_ a změňte vlastnost _singlePlacementGroup_. Pokud tuto vlastnost nevidíte, možná používáte k zobrazení škálovací sady starší verzi rozhraní Microsoft.Compute API.

>[!NOTE] 
U škálovací sady můžete změnit podporu pouze jedné skupiny umístění (výchozí chování) na podporu více skupin umístění, ale převod opačným směrem možný není. Proto se před převodem ujistěte, že rozumíte vlastnostem velkých škálovacích sad.



---
title: Dostupné velikosti pro Azure Cloud Services (Rozšířená podpora)
description: Dostupné velikosti pro nasazení Azure Cloud Services (Rozšířená podpora)
ms.topic: article
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: c3515b559ef647b9a043a04282b4edfc6e7fa9be
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2021
ms.locfileid: "98744270"
---
# <a name="available-sizes-for-azure-cloud-services-extended-support"></a>Dostupné velikosti pro Azure Cloud Services (Rozšířená podpora)

Tento článek popisuje dostupné velikosti virtuálních počítačů pro instance Cloud Services (rozšířené podpory).   

| Rodina SKU |  ACU/jádro | 
|---|---|
| [A5 – 7](https://docs.microsoft.com/azure/virtual-machines/sizes-previous-gen?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#a-series)| 100 |
|[A8-A11](https://docs.microsoft.com/azure/virtual-machines/sizes-previous-gen?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#a-series---compute-intensive-instances) | 225* |
|[Av2](https://docs.microsoft.com/azure/virtual-machines/av2-series) | 100 | 
|[Trojrozměrné](https://docs.microsoft.com/azure/virtual-machines/sizes-previous-gen?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#d-series) | 160 | 
|[Dv2](https://docs.microsoft.com/azure/virtual-machines/dv2-dsv2-series) | 160 – 190 * |
|[Dv3](https://docs.microsoft.com/azure/virtual-machines/dv3-dsv3-series) | 160 – 190 * |
|[Ev3](https://docs.microsoft.com/azure/virtual-machines/ev3-esv3-series) | 160 – 190 *
|[Věcn](https://docs.microsoft.com/azure/virtual-machines/sizes-previous-gen?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#g-series) | 180-240 * |
|[Y](https://docs.microsoft.com/azure/virtual-machines/h-series) | 290 - 300* | 

>[!NOTE]
> Hodnoty ACU s hvězdičkou označují použití technologie Intel® Turbo, která může zvýšit frekvenci procesoru podle aktuální potřeby. Množství nárůst se může lišit v závislosti na velikosti virtuálního počítače, úlohy a dalších úlohách spuštěných na stejném hostiteli.

## <a name="configure-sizes-for-cloud-services-extended-support"></a>Konfigurace velikostí pro Cloud Services (Rozšířená podpora)

Velikost virtuálního počítače instance role můžete zadat jako součást modelu služby v definičním souboru služby. Velikost role určuje počet jader procesoru, kapacitu paměti a velikost místního systému souborů.

Například nastavení velikosti instance webové role na `Standard_D2` : 

```xml
<WorkerRole name="Worker1" vmsize="Standard_D2"> 
</WorkerRole> 
```

## <a name="change-the-size-of-an-existing-role"></a>Změna velikosti stávající role

Pokud chcete změnit velikost stávající role, změňte velikost virtuálního počítače v souboru definice služby (csdef), znovu zabalite cloudovou službu a znovu ji nasaďte. 

## <a name="get-a-list-of-available-sizes"></a>Získat seznam dostupných velikostí 

Seznam dostupných velikostí najdete v článku [SKU prostředků – seznam](https://docs.microsoft.com/rest/api/compute/resourceskus/list) a použití následujících filtrů:


`ResourceType = virtualMachines ` <br>
`VMDeploymentTypes = PaaS `


## <a name="next-steps"></a>Další kroky 
- Přečtěte si [požadavky na nasazení](deploy-prerequisite.md) pro Cloud Services (Rozšířená podpora).
- Přečtěte si [Nejčastější dotazy](faq.md) k Cloud Services (Rozšířená podpora).
- Nasaďte cloudovou službu (rozšířenou podporu) pomocí [Azure Portal](deploy-portal.md), [PowerShellu](deploy-powershell.md), [šablony](deploy-template.md) nebo sady [Visual Studio](deploy-visual-studio.md).

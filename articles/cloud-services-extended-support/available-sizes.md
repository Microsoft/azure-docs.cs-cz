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
ms.openlocfilehash: 1c1faf14eb101da41679f9f0596e1e750a3c6a51
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2021
ms.locfileid: "106166280"
---
# <a name="available-sizes-for-azure-cloud-services-extended-support"></a>Dostupné velikosti pro Azure Cloud Services (Rozšířená podpora)

Tento článek popisuje dostupné velikosti virtuálních počítačů pro instance Cloud Services (rozšířené podpory).   

| Rodina SKU |  ACU/jádro | 
|---|---|
| [A5 – 7](../virtual-machines/sizes-previous-gen.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#a-series)| 100 |
|[A8-A11](../virtual-machines/sizes-previous-gen.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#a-series---compute-intensive-instances) | 225* |
|[Av2](../virtual-machines/av2-series.md) | 100 | 
|[Trojrozměrné](../virtual-machines/sizes-previous-gen.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#d-series) | 160 | 
|[Dv2](../virtual-machines/dv2-dsv2-series.md) | 160 – 190 * |
|[Dv3](../virtual-machines/dv3-dsv3-series.md) | 160 – 190 * |
|[Dav4](../virtual-machines/dav4-dasv4-series.md) | 230 – 260 |
|[Eav4](../virtual-machines/eav4-easv4-series.md) | 230 – 260 |
|[Ev3](../virtual-machines/ev3-esv3-series.md) | 160 – 190 * |
|[Věcn](../virtual-machines/sizes-previous-gen.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#g-series) | 180-240 * |
|[Y](../virtual-machines/h-series.md) | 290 - 300* | 

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

Seznam dostupných velikostí najdete v článku [SKU prostředků – seznam](/rest/api/compute/resourceskus/list) a použití následujících filtrů:


`ResourceType = virtualMachines ` <br>
`VMDeploymentTypes = PaaS `


## <a name="next-steps"></a>Další kroky 
- Přečtěte si [požadavky na nasazení](deploy-prerequisite.md) pro Cloud Services (Rozšířená podpora).
- Přečtěte si [Nejčastější dotazy](faq.md) k Cloud Services (Rozšířená podpora).
- Nasaďte cloudovou službu (rozšířenou podporu) pomocí [Azure Portal](deploy-portal.md), [PowerShellu](deploy-powershell.md), [šablony](deploy-template.md) nebo sady [Visual Studio](deploy-visual-studio.md).

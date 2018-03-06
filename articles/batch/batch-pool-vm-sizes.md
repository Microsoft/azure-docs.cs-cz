---
title: "Zvolte velikost virtuálního počítače pro fondy Azure Batch | Microsoft Docs"
description: "Jak vybrat z dostupných velikostí virtuálních počítačů pro výpočetní uzly ve fondech Azure Batch"
services: batch
documentationcenter: 
author: dlepow
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: batch
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/01/2018
ms.author: danlep
ms.openlocfilehash: addd1e9314a754b40cc5d49c0299f007580f512f
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2018
---
# <a name="choose-a-vm-size-for-compute-nodes-in-an-azure-batch-pool"></a>Zvolte velikost virtuálního počítače pro výpočetní uzly ve fondu Azure Batch

Když vyberete uzel velikost fondu Azure Batch, můžete některé z těchto téměř všechny velikosti virtuálních počítačů v Azure k dispozici. Azure nabízí celou řadu velikosti pro systémy Linux a virtuálních počítačů Windows pro různé úlohy. 

Existuje několik výjimek a omezení pro výběr velikost virtuálního počítače:
* Některé rodiny virtuálního počítače nebo velikosti virtuálního počítače nejsou podporovány v dávce. 
* Některé velikosti virtuálních počítačů jsou s omezeným přístupem a musí být konkrétně povoleny předtím, než může být přidělen.


## <a name="supported-vm-families-and-sizes"></a>Podporované řady virtuálních počítačů a velikosti

### <a name="pools-in-virtual-machine-configuration"></a>Fondy v konfiguraci virtuálního počítače

Fondy batch v konfiguraci virtuálního počítače podporovat všechny velikosti virtuálních počítačů ([Linux](../virtual-machines/linux/sizes.md), [Windows](../virtual-machines/windows/sizes.md)) *s výjimkou* pro následující:

| Rodina  | Nepodporované velikosti  |
|---------|---------|
| Základní A-series | Basic_A0 (A0) |
| A-Series | Standard_A0 |
| B-Series | Vše |
| Fsv2-series<sup>*</sup> | Vše |

<sup>*</sup>Velikosti této série jsou na plán pro budoucí podpory.

### <a name="pools-in-cloud-service-configuration"></a>Fondy v konfiguraci cloudové služby

Fondy batch v konfiguraci služby cloudu podporovat všechny [velikosti virtuálních počítačů pro cloudové služby](../cloud-services/cloud-services-sizes-specs.md) *s výjimkou* pro následující:

| Rodina  | Nepodporované velikosti  |
|---------|---------|
| A-Series | ExtraSmall |
| Av2-series | Standard_A1_v2, Standard_A2_v2, Standard_A2m_v2 |

## <a name="restricted-vm-families"></a>S omezeným přístupem rodiny virtuálních počítačů
Následující rodiny virtuálních počítačů může být přidělen ve fondech Batch, ale musíte požádat o zvýšení konkrétní kvóty (viz [v tomto článku](batch-quota-limit.md#increase-a-quota)):
* NCv2-Series
* NCv3-series
* ND-Series

Tyto velikosti lze použít pouze ve fondech v konfiguraci virtuálního počítače.

## <a name="size-considerations"></a>Důležité informace o velikosti

* **Požadavky na aplikace** -vezměte v úvahu charakteristiky a požadavky aplikace je potřeba spustit na uzlech. To, jestli je aplikace vícevláknová, kolik paměti používá, a další aspekty vám mohou pomoci při určování nejvhodnější a cenově nejefektivnější velikosti uzlu. Pro více instancemi [úlohy MPI](batch-mpi.md) nebo CUDA aplikace, vezměte v úvahu specializuje [HPC](../virtual-machines/linux/sizes-hpc.md) nebo [grafický procesor s podporou](../virtual-machines/linux/sizes-gpu.md) velikostí virtuálních počítačů, v uvedeném pořadí. (Viz [použití podporující RDMA nebo grafický procesor s podporou instancí ve fondech Batch](batch-pool-compute-intensive-sizes.md).) 

* **Úkolů na uzel** -je typické, vyberte uzlu velikost za předpokladu, že jeden úkol běží na uzlu současně. Však může být výhodné mít více úloh (a tedy několik instancí aplikace) [souběžně](batch-parallel-node-tasks.md) během provádění úlohy na výpočetních uzlech. V takovém případě je běžné zvolit vícejádrovými uzlu velikost pro umístění zvýšené poptávky provádění paralelních úloh.

* **Úrovně pro různé úkoly zatížení** -všechny uzly v rámci fondu mají stejnou velikost. Pokud máte v úmyslu spouštět aplikace s různými požadavky na systém nebo úrovně zatížení, doporučujeme vám používat oddělené fondy. 

* **Dostupnost v oblastech** -rodiny A virtuálních počítačů nebo velikost nemusí být k dispozici v oblastech, kde můžete vytvořit účty Batch. Pokud chcete zkontrolovat, že velikost je k dispozici, najdete v části [produkty podle oblasti](https://azure.microsoft.com/regions/services/).

* **Kvóty** – [jader kvóty](batch-quota-limit.md#resource-quotas) v dávce váš účet může omezit počet uzlů na danou velikost přidáte do fondu služby Batch. Chcete-li požádat o zvýšení kvóty, přečtěte si téma [v tomto článku](batch-quota-limit.md#increase-a-quota). 

* **Pro konfiguraci fondu** – obecně platí, máte další možnosti velikosti virtuálního počítače při vytváření fondu v konfiguraci virtuálního počítače, ve srovnání s konfigurací cloudové služby.

## <a name="next-steps"></a>Další postup

* Podrobnější přehled služby Batch, najdete v tématu [rozsáhlé paralelní vývoj výpočetní řešení pomocí služby Batch](batch-api-basics.md).
* Informace o používání náročné velikosti virtuálních počítačů najdete v tématu [použití podporující RDMA nebo grafický procesor s podporou instancí ve fondech Batch](batch-pool-compute-intensive-sizes.md). 



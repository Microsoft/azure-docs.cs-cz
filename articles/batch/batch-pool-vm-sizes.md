---
title: Zvolte velikosti virtuálních počítačů pro fondy – Azure Batch | Dokumentace Microsoftu
description: Jak si vybrat z dostupných velikostí virtuálních počítačů pro výpočetní uzly ve fondech Azure Batch
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2019
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 787c10ab75a3534a73e04f1bd60462ea02fcf42a
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54191713"
---
# <a name="choose-a-vm-size-for-compute-nodes-in-an-azure-batch-pool"></a>Výběr velikosti virtuálního počítače pro výpočetní uzly ve fondu služby Azure Batch

Když při volbě velikosti uzlu pro fond služby Azure Batch, můžete z téměř všechny velikosti virtuálních počítačů dostupných v Azure. Azure nabízí širokou škálu velikostí pro virtuální počítače Windows a Linux pro různé úlohy. 

Existuje pár výjimek a omezení, která volba velikosti virtuálního počítače:
* Nepodporuje některé rodiny virtuálních počítačů nebo velikosti virtuálních počítačů ve službě Batch. 
* Některé velikosti virtuálních počítačů jsou omezené a musí být konkrétně povolené předtím, než může být přidělen.


## <a name="supported-vm-families-and-sizes"></a>Podporované rodiny virtuálních počítačů a velikosti

### <a name="pools-in-virtual-machine-configuration"></a>Fondy v konfiguraci virtuálního počítače

Fondy služby batch v konfiguraci virtuálního počítače podporují všechny velikosti virtuálních počítačů ([Linux](../virtual-machines/linux/sizes.md), [Windows](../virtual-machines/windows/sizes.md)) *s výjimkou* pro následující:

| Rodina  | Nepodporované velikosti  |
|---------|---------|
| Základní A-series | Basic_A0 (A0) |
| A-Series | Standard_A0 |
| B-Series | Vše |
| Řada DC | Vše | 
| Extrémní optimalizované z hlediska paměti | Vše |
| Řada HB<sup>1</sup> | Vše | 
| Hybridní připojení řady<sup>1</sup> | Vše |
| Řada Lsv2 | Vše |
| Řada NDv2<sup>1</sup> | Vše |
| Řada NVv2<sup>1</sup> | Vše |
| SAP HANA | Vše |

<sup>1</sup> není aktuálně podporováno, ale bude podporovat v budoucnu.

Tyto velikosti virtuálních počítačů jsou podporovány pouze pro uzly s nízkou prioritou:

| Rodina  | Podporované velikosti  |
|---------|---------|
| M-Series | Standard_M64ms |
| M-Series | Standard_M128s |

Ostatní velikosti virtuálních počítačů řady M-series se aktuálně nepodporují.

### <a name="pools-in-cloud-service-configuration"></a>Fondy v konfiguraci cloudové služby

Fondy služby batch v konfiguraci cloudové služby podporují všechny [velikosti virtuálních počítačů pro Cloud Services](../cloud-services/cloud-services-sizes-specs.md) *s výjimkou* pro následující:

| Rodina  | Nepodporované velikosti  |
|---------|---------|
| A-Series | ExtraSmall |
| Av2-series | Standard_A1_v2 Standard_A2_v2, Standard_A2m_v2 |

## <a name="restricted-vm-families"></a>S omezeným přístupem rodiny virtuálních počítačů

Následující rodiny virtuálních počítačů mohou být přiděleny ve fondech služby Batch, ale musíte požádat o zvýšení kvóty specifická (viz [v tomto článku](batch-quota-limit.md#increase-a-quota)):
* NCv2-Series
* NCv3-series
* ND-Series

Tyto velikosti jde použít jenom ve fondech v konfiguraci virtuálního počítače.

## <a name="size-considerations"></a>Důležité informace o velikosti

* **Požadavky aplikace** – vezměte v úvahu charakteristiky a požadavky aplikace je potřeba spustit na uzlech. To, jestli je aplikace vícevláknová, kolik paměti používá, a další aspekty vám mohou pomoci při určování nejvhodnější a cenově nejefektivnější velikosti uzlu. Pro více instancemi [úlohy MPI](batch-mpi.md) nebo aplikace spouští CUDA, vezměte v úvahu speciální [HPC](../virtual-machines/linux/sizes-hpc.md) nebo [s podporou grafického procesoru](../virtual-machines/linux/sizes-gpu.md) velikosti virtuálních počítačů, v uvedeném pořadí. (Viz [použití podporující RDMA nebo s podporou grafického procesoru instancí ve fondech Batch](batch-pool-compute-intensive-sizes.md).) 

* **Úkolů na uzel** – se obvykle volí uzel velikost za předpokladu, že jeden úkol běží na uzlu po druhém. Však může být výhodné mít několik úkolů (a tudíž i několik instancí aplikace) [při paralelním spuštění](batch-parallel-node-tasks.md) během provádění úlohy na výpočetních uzlech. V takovém případě je běžné zvolit velikost vícejádrovými uzlu tak, aby vyhovovaly zvýšené poptávky na paralelní zpracování úkolů.

* **Načíst limity pro různé úlohy** – všechny uzly ve fondu mají stejnou velikost. Pokud máte v úmyslu spouštět aplikace s různými požadavky na systém nebo úrovně zatížení, doporučujeme vám používat oddělené fondy. 

* **Dostupnost v oblastech** – řada virtuálních počítačů nebo velikost nemusí být k dispozici v oblastech, kde můžete vytvořit účty Batch. Zkontrolujte, že velikost je k dispozici, najdete v článku [dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/regions/services/).

* **Kvóty** – [kvóty jader](batch-quota-limit.md#resource-quotas) v dávce účtu můžete omezit počet uzlů danou velikost můžete přidat do fondu služby Batch. Chcete-li požádat o zvýšení kvóty, přečtěte si téma [v tomto článku](batch-quota-limit.md#increase-a-quota). 

* **Konfigurace fondu** – obecně platí, budete mít další možnosti velikosti virtuálního počítače při vytváření fondu v konfiguraci virtuálního počítače, ve srovnání s konfigurací cloudové služby.

## <a name="next-steps"></a>Další postup

* Podrobný přehled služby Batch, najdete v části [vývoj rozsáhlých paralelních výpočetních řešení pomocí služby Batch](batch-api-basics.md).
* Informace o používání náročné na výpočetní velikosti virtuálních počítačů najdete v tématu [použití podporující RDMA nebo s podporou grafického procesoru instancí ve fondech Batch](batch-pool-compute-intensive-sizes.md). 



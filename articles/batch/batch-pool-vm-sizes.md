---
title: Výběr velikostí virtuálních her pro fondy – Azure Batch | Dokumenty společnosti Microsoft
description: Jak si vybrat z dostupných velikostí virtuálních her pro výpočetní uzly ve fondech Azure Batch
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: ''
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/12/2019
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: c18190ec5e5d079d51630a976681717a78a46e00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087042"
---
# <a name="choose-a-vm-size-for-compute-nodes-in-an-azure-batch-pool"></a>Volba velikosti virtuálního počítače pro výpočetní uzly ve fondu Azure Batch

Když vyberete velikost uzlu pro fond Azure Batch, můžete si vybrat z téměř všech velikostí virtuálních her dostupných v Azure. Azure nabízí celou řadu velikostí pro Linux a virtuální počítače s Windows pro různé úlohy.

Existuje několik výjimek a omezení pro výběr velikosti virtuálního počítače:

* Některé řady virtuálních počítače nebo velikosti virtuálních počítače nejsou podporovány v batch.
* Některé velikosti virtuálních zařízení jsou omezené a musí být před přidělením povoleny.

## <a name="supported-vm-series-and-sizes"></a>Podporované řady a velikosti virtuálních počítače

### <a name="pools-in-virtual-machine-configuration"></a>Fondy v konfiguraci virtuálního počítače

Dávkové fondy v konfiguraci virtuálního počítače podporují téměř všechny velikosti virtuálních počítačů[(Linux](../virtual-machines/linux/sizes.md), [Windows](../virtual-machines/windows/sizes.md)). Další informace o podporovaných velikostech a omezeních najdete v následující tabulce.

| Řada virtuálních počítačů  | Podporované velikosti |
|------------|---------|
| Basic A | Všechny velikosti *kromě* Basic_A0 (A0) |
| A | Všechny velikosti *kromě* Standard_A0 |
| Av2 | Všechny velikosti |
| B | Žádný |
| DC | Žádný |
| Dv2, DSv2 | Všechny velikosti |
| Dv3, Dsv3 | Všechny velikosti |
| Dav4, Dasv4 | Žádné - zatím není k dispozici |
| Ev3, Esv3 | Všechny velikosti, s výjimkou E64is_v3 a E64i_v3 |
| Eav4, Easv4 | Žádné - zatím není k dispozici |
| F, Fs | Všechny velikosti |
| Fsv2 | Všechny velikosti |
| G, Gs | Všechny velikosti |
| H | Všechny velikosti |
| HB<sup>1</sup> | Všechny velikosti |
| HBv2<sup>1</sup> | Všechny velikosti |
| HC<sup>1</sup> | Všechny velikosti |
| Ls | Všechny velikosti |
| Lsv2 | Žádné - zatím není k dispozici |
| M<sup>1</sup> | Všechny velikosti, kromě M64, M64m, M128, M128m |
| Mv2 | Žádné - zatím není k dispozici |
| NC | Všechny velikosti |
| NCv2<sup>1</sup> | Všechny velikosti |
| NCv3<sup>1</sup> | Všechny velikosti |
| <sup>1.</sup> | Všechny velikosti |
| NDv2<sup>1</sup> | Žádné - zatím není k dispozici |
| NV | Všechny velikosti |
| NVv3<sup>1</sup> | Všechny velikosti |
| NVv4 | Žádný |
| SAP HANA | Žádný |

<sup>1</sup> Tyto velikosti virtuálních počítačů lze přidělit ve fondech dávek v konfiguraci virtuálního počítače, ale musíte vytvořit nový účet Batch a požádat o konkrétní [zvýšení kvóty](batch-quota-limit.md#increase-a-quota). Toto omezení se odebere, jakmile bude kvóta virtuálního procesoru na řadu virtuálních počítačů plně podporována pro účty Batch.

### <a name="pools-in-cloud-service-configuration"></a>Fondy v konfiguraci cloudové služby

Dávkové fondy v konfiguraci cloudové služby podporují všechny [velikosti virtuálních počítačů pro cloudové služby](../cloud-services/cloud-services-sizes-specs.md) **s výjimkou** následujících případů:

| Řada virtuálních počítačů  | Nepodporované velikosti |
|------------|-------------------|
| A-Series   | Extra malé       |
| Av2-series | Standard_A1_v2, Standard_A2_v2, Standard_A2m_v2 |

## <a name="size-considerations"></a>Informace o velikosti

* **Požadavky na aplikaci** – zvažte charakteristiky a požadavky aplikace, kterou budete spouštět na uzlech. To, jestli je aplikace vícevláknová, kolik paměti používá, a další aspekty vám mohou pomoci při určování nejvhodnější a cenově nejefektivnější velikosti uzlu. Pro [úlohy MPI](batch-mpi.md) s více instancemi nebo aplikace CUDA zvažte specializované velikosti virtuálních počítačů s podporou [HPC](../virtual-machines/linux/sizes-hpc.md) nebo [GPU.](../virtual-machines/linux/sizes-gpu.md) (Viz [Použití instancí podporujících protokoly RDMA nebo GPU ve fondech dávek](batch-pool-compute-intensive-sizes.md).)

* **Úkoly na uzel** – je typické vybrat velikost uzlu za předpokladu, že jeden úkol běží na uzlu najednou. Však může být výhodné mít více úloh (a proto více instancí aplikace) [spustit paralelně](batch-parallel-node-tasks.md) na výpočetníu uzlů během provádění úloh. V tomto případě je běžné zvolit velikost vícejádrového uzlu, aby se přizpůsobila zvýšené poptávce po paralelním provádění úloh.

* **Úrovně zatížení pro různé úkoly** – všechny uzly ve fondu mají stejnou velikost. Pokud máte v úmyslu spouštět aplikace s různými požadavky na systém nebo úrovně zatížení, doporučujeme vám používat oddělené fondy.

* **Dostupnost oblasti** – řada nebo velikost virtuálních počítačů nemusí být k dispozici v oblastech, kde vytváříte dávkové účty. Chcete-li zkontrolovat, zda je k dispozici velikost, přečtěte si informace [o produktech dostupných podle oblastí](https://azure.microsoft.com/regions/services/).

* **Kvóty** – [Kvóty jader](batch-quota-limit.md#resource-quotas) v účtu Batch mohou omezit počet uzlů dané velikosti, které můžete přidat do fondu dávek. Chcete-li požádat o zvýšení kvóty, naleznete [v tomto článku](batch-quota-limit.md#increase-a-quota). 

* **Konfigurace fondu** – obecně máte více možností velikosti virtuálního počítače při vytváření fondu v konfiguraci virtuálního počítače, ve srovnání s konfigurací cloudové služby.

## <a name="next-steps"></a>Další kroky

* Podrobný přehled batch, najdete v tématu [Vývoj rozsáhlých paralelních výpočetních řešení s Batch](batch-api-basics.md).
* Informace o použití velikostí virtuálních počítačích náročných na výpočetní výkon najdete [v tématu Použití instancí podporujících rdma nebo gpu ve fondech dávek](batch-pool-compute-intensive-sizes.md).

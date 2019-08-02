---
title: Zvolit velikosti virtuálních počítačů pro fondy – Azure Batch | Microsoft Docs
description: Jak vybrat z dostupných velikostí virtuálních počítačů pro výpočetní uzly ve fondech Azure Batch
services: batch
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/01/2019
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: fd88f8e9b32b3fe5a0d7ab0caf233098ea19fde0
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323088"
---
# <a name="choose-a-vm-size-for-compute-nodes-in-an-azure-batch-pool"></a>Vyberte velikost virtuálního počítače pro výpočetní uzly ve fondu Azure Batch.

Když vyberete velikost uzlu pro fond Azure Batch, můžete si vybrat z téměř všech velikostí virtuálních počítačů dostupných v Azure. Azure nabízí řadu velikostí pro virtuální počítače s operačním systémem Linux a Windows pro různé úlohy.

Pro výběr velikosti virtuálního počítače je k dispozici několik výjimek a omezení:

* Některé řady virtuálních počítačů nebo velikosti virtuálních počítačů se v dávce nepodporují.
* Některé velikosti virtuálních počítačů jsou omezené a je potřeba je nejdřív povolit, než je budete moct přidělit.

## <a name="supported-vm-series-and-sizes"></a>Podporované řady a velikosti virtuálních počítačů

### <a name="pools-in-virtual-machine-configuration"></a>Fondy v konfiguraci virtuálního počítače

Fondy dávek v konfiguraci virtuálního počítače podporují skoro všechny velikosti virtuálních počítačů ([Linux](../virtual-machines/linux/sizes.md), [Windows](../virtual-machines/windows/sizes.md)). Další informace o podporovaných velikostech a omezeních najdete v následující tabulce.

Všechny propagační nebo ukázkové velikosti virtuálních počítačů, které nejsou uvedené, nejsou zaručené pro podporu.

| Řada virtuálních počítačů  | Podporované velikosti | Režim přidělování fondu účtů Batch<sup>1</sup> |
|------------|---------|-----------------|
| Basic A-Series | Všechny velikosti *kromě* Basic_A0 (a0) | Any |
| A-Series | Všechny velikosti *kromě* Standard_A0 | Any |
| Av2-series | Všechny velikosti | Any |
| B-Series | Žádné | Není k dispozici. |
| Řada DC | Žádné | Není k dispozici. |
| Dv2, Dsv2-Series | Všechny velikosti | Any |
| Dv3, Dsv3-Series | Všechny velikosti | Any |
| [Paměť optimalizované velikosti](../virtual-machines/linux/sizes-memory.md) | Žádné | Není k dispozici. |
| Fsv2-series | Všechny velikosti | Any |
| H-series | Všechny velikosti | Any |
| Řada 2 –<sup>2</sup> | Všechny velikosti | Any |
| HC – řada<sup>2</sup> | Všechny velikosti | Any |
| Řada Ls | Všechny velikosti | Any |
| Řada Lsv2 | Žádné | Není k dispozici. |
| M-Series | Standard_M64ms (jenom s nízkou prioritou), Standard_M128s (jenom s nízkou prioritou) | Any |  
| NCv2-Series<sup>2</sup> | Všechny velikosti | Any |
| NCv3-Series<sup>2</sup> | Všechny velikosti | Any |
| ND-Series<sup>2</sup> | Všechny velikosti | Any |
| NDv2-Series | Všechny velikosti | Režim předplatného uživatele |
| NV-Series | Všechny velikosti | Any |
| Řada NVv3 | Žádný | Není k dispozici. |
| SAP HANA | Žádný | Není k dispozici. |

<sup>1</sup> některé novější série virtuálních počítačů se zpočátku částečně podporují. Tyto řady virtuálních počítačů můžou být přidělené účty Batch s **režimem přidělování fondů** nastaveným na **předplatné uživatele**. Další informace o konfiguraci účtu Batch najdete v tématu [Správa účtů Batch](batch-account-create-portal.md#additional-configuration-for-user-subscription-mode) . V tématu [kvóty a omezení](batch-quota-limit.md) se dozvíte, jak vyžádat kvótu pro tyto částečně podporované řady virtuálních počítačů pro účty Batch pro **předplatné uživatele** .  

<sup>2</sup> tyto velikosti virtuálních počítačů je možné přidělit ve fondech Batch v konfiguraci virtuálního počítače, ale musíte požádat o konkrétní [zvýšení kvóty](batch-quota-limit.md#increase-a-quota).

### <a name="pools-in-cloud-service-configuration"></a>Fondy v konfiguraci cloudové služby

Fondy dávek v konfiguraci cloudové služby podporují všechny [velikosti virtuálních počítačů pro Cloud Services](../cloud-services/cloud-services-sizes-specs.md) **s výjimkou** následujících:

| Řada virtuálních počítačů  | Nepodporované velikosti |
|------------|-------------------|
| A-Series   | Velmi malý       |
| Av2-series | Standard_A1_v2, Standard_A2_v2, Standard_A2m_v2 |

## <a name="size-considerations"></a>Požadavky na velikost

* **Požadavky na aplikaci** – zvažte charakteristiky a požadavky aplikace, které budete spouštět na uzlech. To, jestli je aplikace vícevláknová, kolik paměti používá, a další aspekty vám mohou pomoci při určování nejvhodnější a cenově nejefektivnější velikosti uzlu. Pro [úlohy MPI](batch-mpi.md) nebo CUDA aplikace v několika instancích zvažte specializované velikosti virtuálních počítačů s podporou [HPC](../virtual-machines/linux/sizes-hpc.md) nebo [GPU](../virtual-machines/linux/sizes-gpu.md) . (Viz [použití instancí podporujících technologii RDMA nebo GPU ve fondech Batch](batch-pool-compute-intensive-sizes.md).)

* **Úkoly na uzel** – typický výběr velikosti uzlů za předpokladu, že jedna úloha běží na uzlu v jednom okamžiku. Může však být výhodné mít více úloh (a proto více instancí aplikace) paralelně na [](batch-parallel-node-tasks.md) výpočetních uzlech během provádění úlohy. V tomto případě je běžné zvolit velikost vícejádrových uzlů, která bude vyhovovat zvýšené poptávce při provádění paralelních úkolů.

* **Úrovně zatížení pro různé úlohy** – všechny uzly ve fondu mají stejnou velikost. Pokud máte v úmyslu spouštět aplikace s různými požadavky na systém nebo úrovně zatížení, doporučujeme vám používat oddělené fondy.

* **Dostupnost oblasti** – řada nebo velikost virtuálních počítačů nemusí být k dispozici v oblastech, kde vytvoříte účty Batch. Chcete-li ověřit, zda je velikost k dispozici, přečtěte si téma [Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/regions/services/).

* **Kvóty** – [kvóty jader](batch-quota-limit.md#resource-quotas) v účtu Batch můžou omezit počet uzlů dané velikosti, které můžete přidat do fondu služby Batch. Chcete-li požádat o zvýšení kvóty, přečtěte si [Tento článek](batch-quota-limit.md#increase-a-quota). 

* **Konfigurace fondu** – obecně platí více možností velikosti virtuálních počítačů při vytváření fondu v konfiguraci virtuálního počítače v porovnání s konfigurací cloudové služby.

## <a name="next-steps"></a>Další postup

* Podrobný přehled služby Batch najdete v tématu [vývoj rozsáhlých paralelních výpočetních řešení pomocí služby Batch](batch-api-basics.md).
* Informace o použití velikosti virtuálních počítačů náročných na výpočetní výkon najdete [v tématu použití instancí s podporou RDMA nebo GPU ve fondech služby Batch](batch-pool-compute-intensive-sizes.md).

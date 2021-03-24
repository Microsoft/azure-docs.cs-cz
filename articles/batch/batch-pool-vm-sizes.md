---
title: Volba velikostí a imagí virtuálních počítačů pro fondy
description: Jak vybrat dostupné velikosti virtuálních počítačů a verze operačního systému pro výpočetní uzly ve fondech Azure Batch
ms.topic: conceptual
ms.date: 03/18/2021
ms.custom: seodec18
ms.openlocfilehash: 2c3b90d6188dc6660233ae659fb4280dc1d4f2a5
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "105027376"
---
# <a name="choose-a-vm-size-and-image-for-compute-nodes-in-an-azure-batch-pool"></a>Volba velikosti a obrázku virtuálního počítače pro výpočetní uzly ve fondu Azure Batch

Když vyberete velikost uzlu pro fond Azure Batch, můžete si vybrat z téměř všech velikostí virtuálních počítačů dostupných v Azure. Azure nabízí řadu velikostí pro virtuální počítače s operačním systémem Linux a Windows pro různé úlohy.

## <a name="supported-vm-series-and-sizes"></a>Podporované řady a velikosti virtuálních počítačů

### <a name="pools-in-virtual-machine-configuration"></a>Fondy v konfiguraci virtuálního počítače

Fondy dávek v konfiguraci virtuálního počítače podporují skoro všechny [velikosti virtuálních počítačů](../virtual-machines/sizes.md). Další informace o podporovaných velikostech a omezeních najdete v následující tabulce.

| Řada virtuálních počítačů  | Podporované velikosti |
|------------|---------|
| Basic A | Všechny velikosti *kromě* Basic_A0 (a0) |
| A | Všechny velikosti *kromě* Standard_A0, Standard_A8, Standard_A9, Standard_A10 Standard_A11 |
| Av2 | Všechny velikosti |
| B | Nepodporováno |
| DCsv2 | Všechny velikosti |
| Dv2, DSv2 | Všechny velikosti |
| Dv3, Dsv3 | Všechny velikosti |
| Dav4, Dasv4 | Všechny velikosti |
| Ddv4, Ddsv4 |  Všechny velikosti |
| Dv4, Dsv4 | Nepodporováno |
| Ev3, Esv3 | Všechny velikosti kromě E64is_v3 |
| Eav4, Easv4 | Všechny velikosti |
| Edv4, Edsv4 |  Všechny velikosti |
| Ev4, Esv4 | Nepodporováno |
| F, FS | Všechny velikosti |
| Fsv2 | Všechny velikosti |
| G, GS | Všechny velikosti |
| H | Všechny velikosti |
| HB | Všechny velikosti |
| HBv2 | Všechny velikosti |
| HBv3 | Standard_HB120rs_v3 (Další velikosti ještě nejsou k dispozici) |
| HC | Všechny velikosti |
| Ls | Všechny velikosti |
| Lsv2 | Všechny velikosti |
| M | Všechny velikosti |
| Mv2<sup>1</sup> | Všechny velikosti |
| NC | Všechny velikosti |
| NCv2 | Všechny velikosti |
| NCv3 | Všechny velikosti |
| NCasT4_v3 | Všechny velikosti |
| ND | Všechny velikosti |
| NDv2 | Žádný – zatím není k dispozici |
| NV | Všechny velikosti |
| NVv3 | Všechny velikosti |
| NVv4 | Všechny velikosti |
| SAP HANA | Nepodporováno |

<sup>1</sup> tyto řady virtuálních počítačů se dají použít jenom s IMAGEMI virtuálních počítačů 2. generace.

### <a name="using-generation-2-vm-images"></a>Používání imagí virtuálních počítačů 2. generace

Některé řady virtuálních počítačů, například [Mv2](../virtual-machines/mv2-series.md), se dají použít jenom s [imagemi virtuálního počítače 2. generace](../virtual-machines/generation-2.md). Image virtuálních počítačů 2. generace se zadává jako jakákoli image virtuálního počítače pomocí vlastnosti SKU konfigurace [element imagereference](/rest/api/batchservice/pool/add#imagereference) ; řetězce SKU mají příponu, jako je například "-G2" nebo "-Gen2". Pokud chcete získat seznam imagí virtuálních počítačů, které služba Batch podporuje, včetně imagí generace 2, použijte [možnost seznam podporovaných imagí](/rest/api/batchservice/account/listsupportedimages) API, [PowerShellu](/powershell/module/az.batch/get-azbatchsupportedimage)nebo rozhraní příkazového [řádku Azure CLI](/cli/azure/batch/pool/supported-images).

### <a name="pools-in-cloud-service-configuration"></a>Fondy v konfiguraci cloudové služby

Fondy dávek v konfiguraci cloudové služby podporují všechny [velikosti virtuálních počítačů pro Cloud Services](../cloud-services/cloud-services-sizes-specs.md) **s výjimkou** následujících:

| Řada virtuálních počítačů  | Nepodporované velikosti |
|------------|-------------------|
| A-Series   | Velmi malý       |
| Av2-series | Standard_A1_v2, Standard_A2_v2 Standard_A2m_v2 |

## <a name="size-considerations"></a>Informace o velikosti

- **Požadavky na aplikaci** – zvažte charakteristiky a požadavky aplikace, které budete spouštět na uzlech. To, jestli je aplikace vícevláknová, kolik paměti používá, a další aspekty vám mohou pomoci při určování nejvhodnější a cenově nejefektivnější velikosti uzlu. Pro [úlohy MPI](batch-mpi.md) nebo CUDA aplikace v několika instancích zvažte specializované velikosti virtuálních počítačů s podporou [HPC](../virtual-machines/sizes-hpc.md) nebo [GPU](../virtual-machines/sizes-gpu.md) . Další informace najdete v tématu [použití instancí podporujících technologii RDMA nebo GPU ve fondech služby Batch](batch-pool-compute-intensive-sizes.md).

- **Úkoly na uzel** – typický výběr velikosti uzlů za předpokladu, že jedna úloha běží na uzlu v jednom okamžiku. Může však být výhodné mít více úloh (a proto více instancí aplikace) [paralelně](batch-parallel-node-tasks.md) na výpočetních uzlech během provádění úlohy. V tomto případě je běžné zvolit velikost vícejádrových uzlů, která bude vyhovovat zvýšené poptávce při provádění paralelních úkolů.

- **Úrovně zatížení pro různé úlohy** – všechny uzly ve fondu mají stejnou velikost. Pokud máte v úmyslu spouštět aplikace s různými požadavky na systém nebo úrovně zatížení, doporučujeme vám používat oddělené fondy.

- **Dostupnost oblasti** – řada nebo velikost virtuálních počítačů nemusí být k dispozici v oblastech, kde vytvoříte účty Batch. Chcete-li ověřit, zda je velikost k dispozici, přečtěte si téma [Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/regions/services/).

- **Kvóty** – [kvóty jader](batch-quota-limit.md#resource-quotas) v účtu Batch můžou omezit počet uzlů dané velikosti, které můžete přidat do fondu služby Batch. V případě potřeby můžete [požádat o zvýšení kvóty](batch-quota-limit.md#increase-a-quota).

- **Konfigurace fondu** – obecně platí více možností velikosti virtuálních počítačů při vytváření fondu v konfiguraci virtuálního počítače v porovnání s konfigurací cloudové služby.

## <a name="supported-vm-images"></a>Podporované image virtuálních počítačů

Pomocí některého z následujících rozhraní API vraťte seznam imagí virtuálních počítačů s Windows a Linux, které aktuálně podporuje služba Batch, včetně ID SKU agenta uzlu pro každý obrázek:

- Služba Batch REST API: [seznam podporovaných imagí](/rest/api/batchservice/account/listsupportedimages)
- PowerShell: [Get-AzBatchSupportedImage](/powershell/module/az.batch/get-azbatchsupportedimage)
- Azure CLI: [AZ Batch Pool Supported-images](/cli/azure/batch/pool/supported-images)

Důrazně se doporučuje vyhnout se imagí s blížícím se koncem životnosti konce řádku (Batch support data). Tato data je možné zjistit prostřednictvím [ `ListSupportedImages` rozhraní API](https://docs.microsoft.com/rest/api/batchservice/account/listsupportedimages), [PowerShellu](https://docs.microsoft.com/powershell/module/az.batch/get-azbatchsupportedimage)nebo rozhraní příkazového [řádku Azure CLI](https://docs.microsoft.com/cli/azure/batch/pool/supported-images). Další informace o výběru imagí virtuálních počítačů ve fondu služby Batch najdete v [Průvodci doporučenými postupy pro dávku](best-practices.md) .

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [pracovních postupech služby Batch a primárních prostředcích](batch-service-workflow-features.md) , jako jsou fondy, uzly, úlohy a úkoly.
- Informace o použití velikosti virtuálních počítačů náročných na výpočetní výkon najdete [v tématu použití instancí s podporou RDMA nebo GPU ve fondech služby Batch](batch-pool-compute-intensive-sizes.md).

---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/09/2020
ms.author: alkohli
ms.openlocfilehash: d22d40b21671b148083b48efe9772f118dc3b292
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105582701"
---
Velikost virtuálního počítače určuje množství výpočetních prostředků (jako je CPU, GPU a paměť), které jsou k dispozici pro virtuální počítač. Virtuální počítače byste měli vytvořit pomocí velikosti virtuálního počítače, která je vhodná pro úlohu. I když všechny počítače budou spuštěné na stejném hardwaru, velikosti počítačů mají různá omezení pro přístup k disku. To vám může pomáhat spravovat celkový přístup k disku v rámci virtuálních počítačů. Pokud se zatížení zvýší, můžete také změnit velikost stávajícího virtuálního počítače.

Následující virtuální počítače se podporují pro vytváření Azure Stack hraničních zařízení.

### <a name="dv2-series"></a>Dv2-series
|Velikost     |Virtuální procesory     |Paměť (GiB) | Velikost disku prostředku (GiB)  | Velikost disku operačního systému (GiB) | Max. datových disků | Maximální počet síťových karet |
|-------------------|----|----|-----|----|------|------------|
|**Standard_D1_v2** |1   |3,5 |50   |1000| 4    |2 |
|**Standard_D2_v2** |2   |7   |100  |1000| 8    |4 |
|**Standard_D3_v2** |4   |14  |200  |1000| 16  |4 |
|**Standard_D4_v2** |8   |28  |400  |1000| 32  |8 |
|**Standard_D5_v2** |16  |56  |800  |1000| 64  |8 |
|**Standard_D11_v2** |2   |14  |100  |1000| 8     |2 |
|**Standard_D12_v2** |4   |28  |200  |1000| 16   |4 |
|**Standard_D13_v2** |8   |56  |400  |1000| 32  |8 |

### <a name="dsv2-series"></a>DSv2-series
|Velikost     |Virtuální procesory     |Paměť (GiB) |  Velikost disku prostředku (GiB)  | Velikost disku operačního systému (GiB) | Max. datových disků| Maximální počet síťových karet |
|--------------------|----|----|----|-----|------|-------------|
|**Standard_DS1_v2** |1   |3,5 |7  |4000  |1000 |4  |
|**Standard_DS2_v2** |2   |7   |14 |8000  |1000 |8  |
|**Standard_DS3_v2** |4   |14  |28 |16000 |1000 |16 |
|**Standard_DS4_v2** |8   |28  |56 |32000 |1000 |32 |
|**Standard_DS5_v2** |16  |56  |112|64000 |1000 |64 |
|**Standard_DS11_v2**|2   |14  |28 |8000  |1000 |4  |
|**Standard_DS12_v2**|4   |28  |56 |16000 |1000 |8  |
|**Standard_DS13_v2**|8   |56  |112|32000 |1000 |16 |


Další informace najdete v tématu [Dv2 a DSv2-Series](../articles/virtual-machines/dv2-dsv2-series.md#dv2-series).

### <a name="ncast4_v3-series-preview"></a>NCasT4_v3-Series (Preview)

Tyto velikosti se podporují pro virtuální počítače GPU na vašem zařízení a jsou optimalizované pro aplikace podporující výpočetní výkonové PROCESORy. Tato série se zaměřuje na odvozené úlohy s grafickým procesorem Tesla T4 na NVIDIA. 

|Velikost     |Virtuální procesory     |Paměť (GiB) | Velikost disku prostředku (GiB)  |Velikost disku operačního systému (GiB)| GPU | Paměť GPU (GiB) | Maximální počet síťových karet |
|---------------------|----|----|-----|-----|-------|--------------|---|
|**Standard_NC4as_T4_v3** |4   |28  |180   |1000|1 |16   |4 |
|**Standard_NC8as_T4_v3** |8   |56  |360   |1000|1 |16  |8 |

Další informace najdete v tématu [NCasT4_v3-Series](../articles/virtual-machines/nct4-v3-series.md).

### <a name="f-series"></a>F-series

Tyto řady jsou optimalizované pro výpočetní úlohy a běží na procesorech Intel Xeon. 

| Velikost | vCPU | Paměť: GiB |Velikost disku prostředku (GiB) |Velikost disku operačního systému (GiB)|  Max. datových disků | Maximální počet síťových karet |
|---|---|---|---|---|---|---|
| Standard_F1  | 1  | 2   |16      |1000| 4  |  2 |
| Standard_F2 | 2  | 4 |32      |1000| 8  |  4 |
| Standard_F4  | 4  | 8 |64   |1000| 16 |  4 |
| Standard_F8 | 8 | 16  |132    |1000| 32 |  8 |
| Standard_F16 | 16 | 32  |262   |1000| 64 |  8 |
| Standard_F1s | 1 | 2  | 4  |1000| 4 | 1 |
| Standard_F2s | 2 | 4 |8   |1000| 8 | 4 |
| Standard_F4s | 4 | 8 |16 |1000| 16 |  4 |
| Standard_F8s | 8 | 16 |32 |1000| 32 |  8 |
| Standard_F16s | 16 | 32 |64 |1000| 64 |  8 |

Další informace najdete v tématu [Fsv2-Series](../articles/virtual-machines/fsv2-series.md).


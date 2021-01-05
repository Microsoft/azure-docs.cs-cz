---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/09/2020
ms.author: alkohli
ms.openlocfilehash: 9ea5fb26a52c967c5296f1a83976e748c86c9e18
ms.sourcegitcommit: 799f0f187f96b45ae561923d002abad40e1eebd6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/24/2020
ms.locfileid: "97763768"
---
Velikost virtuálního počítače určuje množství výpočetních prostředků, jako jsou CPU, GPU a paměť, které jsou k dispozici pro virtuální počítač. Virtuální počítače by měly být vytvořené pomocí velikosti virtuálního počítače vhodné pro zatížení. I když všechny počítače budou spuštěné na stejném hardwaru, velikosti počítačů mají různá omezení pro přístup k disku, což vám umožní spravovat celkový přístup k disku napříč virtuálními počítači. Pokud se pracovní zátěž zvýší, je také možné velikost existujícího virtuálního počítače změnit.

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
|**Standard_DS1_v2** |1   |3,5 |7  |4000  |1000 |4  |2 |
|**Standard_DS2_v2** |2   |7   |14 |8000  |1000 |8  |4 |
|**Standard_DS3_v2** |4   |14  |28 |16000 |1000 |16 |4 |
|**Standard_DS4_v2** |8   |28  |56 |32000 |1000 |32 |8 |
|**Standard_DS5_v2** |16  |56  |112|64000 |1000 |64 |8 |
|**Standard_DS11_v2**|2   |14  |28 |8000  |1000 |4  |2 |
|**Standard_DS12_v2**|4   |28  |56 |16000 |1000 |8  |4 |
|**Standard_DS13_v2**|8   |56  |112|32000 |1000 |16 |8 |


Další informace najdete na [Dv2 Series v pro obecné účely velikosti virtuálních počítačů](../articles/virtual-machines/dv2-dsv2-series.md#dv2-series).

### <a name="ncast4_v3-series-preview"></a>NCasT4_v3-Series (Preview)

Tyto velikosti se podporují pro virtuální počítače GPU na vašem zařízení a jsou optimalizované pro aplikace podporující výpočetní výkonové PROCESORy. Tato série se zaměřuje na odvozené úlohy s grafickým procesorem Tesla T4 na NVIDIA. 

|Velikost     |Virtuální procesory     |Paměť (GiB) | Velikost disku prostředku (GiB)  |Velikost disku operačního systému (GiB)| GPU | Paměť GPU (GiB) | Maximální počet síťových karet |
|---------------------|----|----|-----|-----|-------|--------------|
|**Standard_NC4as_T4_v3** |4   |28  |180   |1000|1 |16   |4 |
|**Standard_NC8as_T4_v3** |8   |56  |360   |1000|1 |16  |8 |

Další informace najdete na webu [NCasT4_v3-Series na velikostech virtuálních počítačů optimalizovaných pro grafické procesory](../articles/virtual-machines/nct4-v3-series.md).

### <a name="f-series"></a>F-series

Tyto řady jsou optimalizované pro výpočetní úlohy a běží na procesorech Intel Xeon. 

| Velikost | vCPU | Paměť: GiB |Velikost disku prostředku (GiB) |Velikost disku operačního systému (GiB)|  Max. datových disků | Maximální počet síťových karet |
|---|---|---|---|---|---|---|---|---|
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

Další informace najdete na webu [Fsv2-Series na výpočetních optimalizovaných velikostech virtuálních počítačů](../articles/virtual-machines/fsv2-series.md).


---
title: Velikosti virtuálních počítačů, které jsou podporované ve službě Azure Stack | Dokumentace Microsoftu
description: Referenční informace pro podporované velikosti virtuálních počítačů ve službě Azure Stack.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/17/2018
ms.author: brenduns
ms.openlocfilehash: 52ab831dfaca337cdf803f410d3824b514fd0e8a
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37437150"
---
# <a name="virtual-machine-sizes-supported-in-azure-stack"></a>Velikosti virtuálních počítačů, které jsou podporované ve službě Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Tento článek obsahuje seznam velikostí virtuálních počítačů (VM), které jsou k dispozici ve službě Azure Stack.

## <a name="general-purpose"></a>Obecné účely

Velikosti virtuálních počítačů pro obecné účely poskytují vyvážený poměr procesorů k paměti. Používají se pro testování a vývoj, malé až střední databáze a s nízkým a středním provozem webových serverů.

### <a name="basic-a"></a>Basic A
|Velikost – velikost\název |Virtuální procesory     |Memory (Paměť) | Max. velikost dočasného disku | Operační systém maximální propustnost disku: (IOPS) | Maximální propustnost dočasného úložiště (IOPS) | Maximální propustnost datového disku (IOPS) | Maximální počet síťových karet |    
|-----------------|-----|---------|---------|-----|------|-----------|----|
|**A0\Basic_A0**  |1    |768 MB   | 20 GB   |300  | 300  |1 / 1 × 300  |1   |
|**A1\Basic_A1**  |1    |1,75 GB  | 40 GB   |300  | 300  |2 / 2 x 300  |1   |
|**A2\Basic_A2**  |2    |3,5 GB   | 60 GB   |300  | 300  |4 / 4 x 300  |1   |
|**A3\Basic_A3**  |4    |7 GB     | 120 GB  |300  | 300  |8 / 8 x 300  |1   |
|**A4\Basic_A4**  |8    |14 GB    | 240 GB  |300  | 300  |16 / 16 X 300 |1   |

### <a name="standard-a"></a>Standardní A 
|Velikost     |Virtuální procesory     |Paměť (GiB) | Dočasné úložiště (GiB)  | Operační systém maximální propustnost disku (IOPS) | Maximální propustnost dočasného úložiště (IOPS) | Maximální počet datových disků nebo propustnost (IOPS) | Maximální počet síťových karet / očekávaný šířka pásma (MB/s) |    
|----------------|--|------|----|----|----|-------|---------|
|**Standard_A0** |1 |0,768 |20  |500 |500 |1×500  |1 / 100  |
|**Standard_A1** |1 |1,75  |70  |500 |500 |2×500  |1 / 500  |
|**Standard_A2** |2 |3,5   |135 |500 |500 |4×500  |1 / 500  |
|**Standard_A3** |4 |7     |285 |500 |500 |8×500  |2 / 1 000 |
|**Standard_A4** |8 |14    |605 |500 |500 |16×500 |4 / 2 000 |
|**Standard_A5** |2 |14    |135 |500 |500 |4×500  |2 / 500  |
|**Standard_A6** |4 |28    |285 |500 |500 |8×500  |2 / 1 000 |
|**Standard_A7** |8 |56    |605 |500 |500 |16×500 |4 / 2 000 |

### <a name="av2-series"></a>Av2-series
*Vyžaduje verzi služby Azure Stack 1804 nebo novější*

|Velikost     |Virtuální procesory     |Paměť (GiB) | Dočasné úložiště (GiB)  | Operační systém maximální propustnost disku (IOPS) | Maximální propustnost dočasného úložiště (IOPS) | Maximální počet datových disků nebo propustnost (IOPS) | Maximální počet síťových karet / očekávaný šířka pásma (MB/s) |
|-----------------|----|----|-----|-----|------|--------------|---------|
|**Standard_A1_v2**  |1   |2   |10   |500 |1000  |2 / 2×500   |2 / 250  |
|**Standard_A2_v2**  |2   |4   |20   |500 |2000  |4 / 4×500   |2 / 500  |
|**Standard_A4v2**   |4   |8   |40   |500 |4000  |8 / 8×500   |4 / 1 000 |
|**Standard_A8_v2**  |8   |16  |80   |500 |8000  |16 / 16×500 |8 / 2 000 |
|**Standard_A2m_v2** |2   |16  |20   |500 |2000  |4 / 4×500   |2 / 500  |
|**Standard_A4m_v2** |4   |32  |40   |500 |4000  |8 / 8×500   |4 / 1 000 |
|**Standard_A8m_v2** |8   |64  |80   |500 |8000  |16 / 16×500 |8 / 2 000 |

### <a name="d-series"></a>D-series
|Velikost     |Virtuální procesory     |Paměť (GiB) | Dočasné úložiště (GiB)  | Operační systém maximální propustnost disku (IOPS) | Maximální propustnost dočasného úložiště (IOPS) | Maximální počet datových disků nebo propustnost (IOPS) | Maximální počet síťových karet / očekávaný šířka pásma (MB/s) |
|----------------|----|----|-----|----|------|------------|---------|
|**Standard_D1** |1   |3,5 |50   |500 |3000  |4 / 4×500   |1 / 500  |
|**Standard_D2** |2   |7   |100  |500 |6000  |8 / 8×500   |2 / 1 000 |
|**Standard_D3** |4   |14  |200  |500 |12000 |16 / 16×500 |4 / 2 000 |
|**Standard_D4** |8   |28  |400  |500 |24000 |32 / 32×500 |8 / 4 000 |


### <a name="ds-series"></a>DS-series
|Velikost     |Virtuální procesory     |Paměť (GiB) | Dočasné úložiště (GiB)  | Operační systém maximální propustnost disku (IOPS) | Maximální propustnost dočasného úložiště (IOPS) | Maximální počet datových disků nebo propustnost (IOPS) | Maximální počet síťových karet / očekávaný šířka pásma (MB/s) |
|-----------------|----|----|-----|-----|------|-------------|---------|
|**Standard_DS1** |1   |3,5 |7    |1000 |4000  |4 / 4x2300   |1 / 500  |
|**Standard_DS2** |2   |7   |14   |1000 |8000  |8 / 8 x 2300   |2 / 1 000 |
|**Standard_DS3** |4   |14  |28   |1000 |16000 |16 / 16 x 2300 |4 / 2 000 |
|**Standard_DS4** |8   |28  |56   |1000 |32000 |32 / 32 × 2300 |8 / 4 000 |

### <a name="dv2-series"></a>Dv2-series
|Velikost     |Virtuální procesory     |Paměť (GiB) | Dočasné úložiště (GiB)  | Operační systém maximální propustnost disku (IOPS) | Maximální propustnost dočasného úložiště (IOPS) | Maximální počet datových disků nebo propustnost (IOPS) | Maximální počet síťových karet / očekávaný šířka pásma (MB/s) |
|-------------------|----|----|-----|----|------|------------|---------|
|**Standard_D1_v2** |1   |3,5 |50   |500 |3000  |4 / 4×500   |1 / 500  |
|**Standard_D2_v2** |2   |7   |100  |500 |6000  |8 / 8×500   |2 / 1 000 |
|**Standard_D3_v2** |4   |14  |200  |500 |12000 |16 / 16×500 |4 / 2 000 |
|**Standard_D4_v2** |8   |28  |400  |500 |24000 |32 / 32×500 |8 / 4 000 |
|**Standard_D5_v2** |16  |56  |800  |500 |48000 |64 / 64 x 500 |8 / 8000 |

### <a name="dsv2-series"></a>DSv2-series
|Velikost     |Virtuální procesory     |Paměť (GiB) | Dočasné úložiště (GiB)  | Operační systém maximální propustnost disku (IOPS) | Maximální propustnost dočasného úložiště (IOPS) | Maximální počet datových disků nebo propustnost (IOPS) | Maximální počet síťových karet / očekávaný šířka pásma (MB/s) |
|--------------------|----|----|----|-----|------|-------------|---------|
|**Standard_DS1_v2** |1   |3,5 |7   |1000 |4000  |4 / 4x2300   |1 / 750  |
|**Standard_DS2_v2** |2   |7   |14  |1000 |8000  |8 / 8 x 2300   |2 / 1 500 |
|**Standard_DS3_v2** |4   |14  |28  |1000 |16000 |16 / 16 x 2300 |4 / 3 000 |
|**Standard_DS4_v2** |8   |28  |56  |1000 |32000 |32 / 32 × 2300 |8 / 6 000 |
|**Standard_DS5_v2** |16  |56  |112 |1000 |64000 |64 / 64x2300 |8 / 10000 |


## <a name="compute-optimized"></a>Optimalizované z hlediska výpočetních služeb
### <a name="f-series"></a>F-series
*Vyžaduje verzi služby Azure Stack 1804 nebo novější*

|Velikost     |Virtuální procesory     |Paměť (GiB) | Dočasné úložiště (GiB)  | Operační systém maximální propustnost disku (IOPS) | Maximální propustnost dočasného úložiště (IOPS) | Maximální počet datových disků nebo propustnost (IOPS) | Maximální počet síťových karet / očekávaný šířka pásma (MB/s) |
|-----------------|----|----|-----|----|------|------------|---------|
|**Standard_F1**  |1   |2   |16   |500 |3000  |4 / 4 × 400   |2 / 750  |
|**Standard_F2**  |2   |4   |32   |500 |6000  |8 / 8×500   |2 / 1 500 |
|**Standard_F4**  |4   |8   |64   |500 |12000 |16 / 16×500 |4 / 3 000 |
|**Standard_F8**  |8   |16  |128  |500 |24000 |32 / 32×500 |8 / 6 000 |
|**Standard_F16** |16  |32  |256  |500 |48000 |64 / 64 x 500 |8 / 6000 12000  |


### <a name="fs-series"></a>Řada Fs
*Vyžaduje verzi služby Azure Stack 1804 nebo novější*  

|Velikost     |Virtuální procesory     |Paměť (GiB) | Dočasné úložiště (GiB)  | Operační systém maximální propustnost disku (IOPS) | Maximální propustnost dočasného úložiště (IOPS) | Maximální počet datových disků nebo propustnost (IOPS) | Maximální počet síťových karet / očekávaný šířka pásma (MB/s) |
|------------------|----|----|----|-----|------|-------------|---------|
|**Standard_F1s**  |1   |2   |4   |1000 |4000  |4 / 4x2300   |2 / 750  |
|**Standard_F2s**  |2   |4   |8   |1000 |8000  |8 / 8 x 2300   |2 / 1 500 |
|**Standard_F4s**  |4   |8   |16  |1000 |16000 |16 / 16 x 2300 |4 / 3 000 |
|**Standard_F8s**  |8   |16  |32  |1000 |32000 |32 / 32 × 2300 |8 / 6 000 |
|**Standard_F16s** |16  |32  |64  |1000 |64000 |64 / 64x2300 |8 / 6000 12000  |


### <a name="fsv2-series"></a>Fsv2-series
*Vyžaduje verzi služby Azure Stack 1804 nebo novější* 

|Velikost     |Virtuální procesory     |Paměť (GiB) | Dočasné úložiště (GiB)  | Operační systém maximální propustnost disku (IOPS) | Maximální propustnost dočasného úložiště (IOPS) | Maximální počet datových disků nebo propustnost (IOPS) | Maximální počet síťových karet / očekávaný šířka pásma (MB/s) |
|---------------------|----|----|-----|-----|-------|--------------|---------|
|**Standard_F2s_v2**  |2   |4   |16   |1000 |4000   |4 / 4x2300    |Střední |
|**Standard_F4s_v2**  |4   |8   |32   |1000 |8000   |8 / 8 x 2300    |Střední |
|**Standard_F8s_v2**  |8   |16  |64   |1000 |16000  |16 / 16 x 2300  |Vysoký     |
|**Standard_F16s_v2** |16  |32  |128  |1000 |32000  |32 / 32 × 2300  |Vysoký     |
|**Standard_F32s_v2** |32  |64  |256  |1000 |64000  |32 / 32 × 2300  |Vysoký  |
|**Standard_F64s_v2** |64  |128 |512  |1000 |128000 |32 / 32 × 2300  |Velmi vysoké  |


## <a name="memory-optimized"></a>Optimalizované z hlediska paměti

Paměť optimalizované velikosti virtuálních počítačů poskytuje vysokou poměr paměti na procesor, který je navržený pro servery relačních databází, střední a velké mezipaměti a analýzu v paměti.

### <a name="mo-d"></a>Řady D-series
|Velikost     |Virtuální procesory     |Paměť (GiB) | Dočasné úložiště (GiB)  | Operační systém maximální propustnost disku (IOPS) | Maximální propustnost dočasného úložiště (IOPS) | Maximální počet datových disků nebo propustnost (IOPS) | Maximální počet síťových karet / očekávaný šířka pásma (MB/s) |
|------------------|---|----|----|--------|------|------------|---------|
|**Standard_D11**  |2  |14  |100 |500     |6000  |8 / 8×500   |2 / 1 000 |
|**Standard_D12**  |4  |28  |200 |500     |12000 |16 / 16×500 |4 / 2 000 |
|**Standard_D13**  |8  |56  |400 |500     |24000 |32 / 32×500 |8 / 4 000 |
|**Standard_D14**  |16 |112 |800 |500     |48000 |64 / 64 x 500 |8 / 8000 |

### <a name="mo-ds"></a>Řady DS-series
|Velikost     |Virtuální procesory     |Paměť (GiB) | Dočasné úložiště (GiB)  | Operační systém maximální propustnost disku (IOPS) | Maximální propustnost dočasného úložiště (IOPS) | Maximální počet datových disků nebo propustnost (IOPS) | Maximální počet síťových karet / očekávaný šířka pásma (MB/s) |
|-------------------|---|----|----|--------|------|-------------|---------|
|**Standard_DS11**  |2  |14  |28  |1000    |8000  |8 / 8 x 2300   |2 / 1 000 |
|**Standard_DS12**  |4  |28  |56  |1000    |12000 |16 / 16 x 2300 |4 / 2 000 |
|**Standard_DS13**  |8  |56  |112 |1000    |32000 |32 / 32 × 2300 |8 / 4 000 |
|**Standard_DS14**  |16 |112 |224 |1000    |64000 |64 / 64x2300 |8 / 8000 |

### <a name="mo-dv2"></a>Řady Dv2-series
|Velikost     |Virtuální procesory     |Paměť (GiB) | Dočasné úložiště (GiB)  | Operační systém maximální propustnost disku (IOPS) | Maximální propustnost dočasného úložiště (IOPS) | Maximální počet datových disků nebo propustnost (IOPS) | Maximální počet síťových karet / očekávaný šířka pásma (MB/s) |
|--------------------|----|----|-----|----|-------|-------------|---------|
|**Standard_D11_v2** |2   |14  |100  |500 |6000   |8 / 8×500    |2 / 1 500 |
|**Standard_D12_v2** |4   |28  |200  |500 |12000  |16 / 16×500  |4 / 3 000 |
|**Standard_D13_v2** |8   |56  |400  |500 |24000  |32 / 32×500  |8 / 6 000 |
|**Standard_D14_v2** |16  |112 |800  |500 |48000  |64 / 64 x 500  |8 / 10000 |


### <a name="mo-dsv2"></a>DSv2-series
|Velikost     |Virtuální procesory     |Paměť (GiB) | Dočasné úložiště (GiB)  | Operační systém maximální propustnost disku (IOPS) | Maximální propustnost dočasného úložiště (IOPS) | Maximální počet datových disků nebo propustnost (IOPS) | Maximální počet síťových karet / očekávaný šířka pásma (MB/s) |
|---------------------|----|----|-----|-----|-------|--------------|---------|
|**Standard_DS11_v2** |2   |14  |28   |1000 |8000   |8 / 8 x 2300    |2 / 1 500 |
|**Standard_DS12_v2** |4   |28  |56   |1000 |16000  |16 / 16 x 2300  |4 / 3 000 |
|**Standard_DS13_v2** |8   |56  |112  |1000 |32000  |32 / 32 × 2300  |8 / 6 000 |
|**Standard_DS14_v2** |16  |112 |224  |1000 |64000  |64 / 64x2300  |8 / 10000 |


## <a name="next-steps"></a>Další postup

[Důležité informace týkající se virtuálních počítačů v Azure stacku](azure-stack-vm-considerations.md)

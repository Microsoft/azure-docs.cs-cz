---
author: manish-shukla01
ms.author: cwatson
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 11-20-2018
ms.openlocfilehash: 044f735560eb7d57a90a16c1e3ce1255a9fe0d54
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2018
ms.locfileid: "52742371"
---
# <a name="virtual-machine-size-flexibility-with-reserved-vm-instances"></a>Flexibilita velikost virtuálního počítače s rezervovanými instancemi virtuálních počítačů

Instance rezervovaný virtuální počítač, který je optimalizovaný pro instanci velikost flexibilitu, kterou zakoupíte rezervaci můžete použít velikosti virtuálních počítačů (VM) ve stejné skupině velikost series. Například Pokud koupíte rezervace pro velikost virtuálního počítače, který je uveden v tabulce DSv2-series, jako je Standard_DS5_v2, můžete použít sleva za rezervaci pro ostatní, které jsou uvedeny v této tabulce stejné velikosti čtyři:

- Standard_DS1_v2
- Standard_DS2_v2
- Standard_DS3_v2
- Standard_DS4_v2

Ale tento sleva za rezervaci neplatí pro velikosti virtuálních počítačů, které jsou uvedeny v různých tabulek, jako jsou novinky v tabulce vysoký poměr paměti DSv2-series: Standard_DS11_v2, Standard_DS12_v2, a tak dále.

Ve skupině velikostí řady počet virtuálních počítačů, platí sleva za rezervaci pro závisí na velikosti virtuálního počítače, který vyberete při nákupu rezervace. Také závisí na velikosti virtuálních počítačů, které máte systémem. Poměr sloupec, který je uveden v následujících tabulkách porovnává relativní nároky pro jednotlivé velikosti virtuálních počítačů v této skupině. Hodnota poměr vypočítat, jak sleva za rezervaci se vztahuje k virtuálním počítačům můžete mít systémem.

## <a name="examples"></a>Příklady

Následující příklady používají velikost a poměry v tabulce DSv2-series.

 Můžete si koupit rezervované instance virtuálního počítače s velikostí Standard_DS4_v2, kde je poměr nebo relativní nároky na místo v porovnání s dalších velikostí v této sérii 8.

- Scénář 1: Spuštění osm Standard_DS1_v2 velikostí virtuálních počítačů s poměrem 1. Vaše sleva za rezervaci se vztahuje na všechny osm těchto virtuálních počítačů.
- Scénář 2: Spusťte dvě Standard_DS2_v2 velikostí virtuálních počítačů s poměrem 2. Spustit také Standard_DS3_v2 velikosti virtuálního počítače s poměrem 4. Celkové nároky je 2 + 2 + 4 = 8. Proto váš sleva za rezervaci se vztahuje na všechny tři těchto virtuálních počítačů.
- Scénář 3: Spuštění jedné Standard_DS5_v2 s poměrem 16. Vaše sleva za rezervaci se vztahuje na náklady na výpočetní výkon poloviční tohoto Virtuálního počítače.

Následující části zobrazit, jaké velikosti jsou ve stejné skupině velikost series při nákupu rezervované instance virtuálního počítače optimalizované pro instanci flexibilní velikost.

## <a name="b-series"></a>B-Series

| Velikost | Poměr|
|---|---|
| Standard_B1s | 1 |
|Standard_B2s|4|

Další informace najdete v tématu [velikosti zvládáním výkonových špiček virtuálních počítačů B-series](../articles/virtual-machines/windows/b-series-burstable.md).

## <a name="b-series-high-memory"></a>Vysoký poměr paměti řady B-series

| Velikost | Poměr|
|---|---|
| Standard_B1ms |1|
|Standard_B2ms|4|
|Standard_B4ms|8|
|Standard_B8ms|16|

Další informace najdete v tématu [velikosti zvládáním výkonových špiček virtuálních počítačů B-series](../articles/virtual-machines/windows/b-series-burstable.md).

## <a name="d-series"></a>D-series

| Velikost | Poměr|
|---|---|
| Standard_D1|1|
|Standard_D2|2|
|Standard_D3|4|
|Standard_D4|8|

Další informace najdete v tématu [velikostí virtuálních počítačů předchozí generace](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="d-series-high-memory"></a>Vysoký poměr paměti řady D-series

| Velikost | Poměr|
|---|---|
| Standard_D11|1|
|Standard_D12|2|
|Standard_D13|4|
|Standard_D14|8|

Další informace najdete v tématu [velikostí virtuálních počítačů předchozí generace](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="ds-series"></a>Řady DS-series

| Velikost | Poměr|
|---|---|
|Standard_DS1|1|
|Standard_DS2|2|
|Standard_DS3|4|
|Standard_DS4|8|

Další informace najdete v tématu [velikostí virtuálních počítačů předchozí generace](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="ds-series-high-memory"></a>Vysoký poměr paměti řady DS-series

| Velikost | Poměr|
|---|---|
|Standard_DS11|1|
|Standard_DS12|2|
|Standard_DS13|4|
|Standard_DS14|8|

Další informace najdete v tématu [velikostí virtuálních počítačů předchozí generace](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dsv2-series"></a>DSv2-series

| Velikost | Poměr|
|---|---|
|Standard_DS1_v2|1|
|Standard_DS2_v2|2|
|Standard_DS3_v2|4|
|Standard_DS4_v2|8|
|Standard_DS5_v2|16|

Další informace najdete v tématu [velikostí virtuálních počítačů pro obecné účely](../articles/virtual-machines/windows/sizes-general.md#dv2-series).

## <a name="dsv2-series-high-memory"></a>Vysoký poměr paměti DSv2-series

| Velikost | Poměr|
|---|---|
|Standard_DS11_v2|1|
|Standard_DS11-1_v2|1|
|Standard_DS12_v2|2|
|Standard_DS12-1_v2|2|
|Standard_DS12-2_v2|2|
|Standard_DS13_v2|4|
|Standard_DS13-2_v2|4|
|Standard_DS13-4_v2|4|
|Standard_DS14_v2|8|
|Standard_DS14-4_v2|8|
|Standard_DS14-8_v2|8|
|Standard_DS15_v2|10|

Další informace najdete v tématu [paměťově optimalizované velikosti virtuálních počítačů](../articles/virtual-machines/windows/sizes-memory.md#dsv2-series-11-15).

## <a name="dsv3-series"></a>Řada DSv3-series

| Velikost | Poměr|
|---|---|
|Standard_D2s_v3|1|
|Standard_D4s_v3|2|
|Standard_D8s_v3|4|
|Standard_D16s_v3|8|
|Standard_D32s_v3|16|
|Standard_D64s_v3|32|

Další informace najdete v tématu [velikostí virtuálních počítačů pro obecné účely](../articles/virtual-machines/windows/sizes-general.md#dsv3-series-sup1sup).

## <a name="dv2-series"></a>Dv2-series

| Velikost | Poměr|
|---|---|
|Standard_D1_v2|1|
|Standard_D2_v2|2|
|Standard_D3_v2|4|
|Standard_D4_v2|8|
|Standard_D5_v2|16|

Další informace najdete v tématu [velikostí virtuálních počítačů pro obecné účely](../articles/virtual-machines/windows/sizes-general.md#dv2-series).

## <a name="dv2-series-high-memory"></a>Vysoký poměr paměti řady Dv2-series

| Velikost | Poměr|
|---|---|
|Standard_D11_v2|1|
|Standard_D12_v2|2|
|Standard_D13_v2|4|
|Standard_D14_v2|8|
|Standard_D15_v2|10|

Další informace najdete v tématu [paměťově optimalizované velikosti virtuálních počítačů](../articles/virtual-machines/windows/sizes-memory.md#dv2-series-11-15).

## <a name="dv3-series"></a>Dv3-series

| Velikost | Poměr|
|---|---|
| Standard_D2_v3|1|
|Standard_D4_v3|2|
|Standard_D8_v3|4|
|Standard_D16_v3|8|
|Standard_D32_v3|16|
|Standard_D64_v3|32|

Další informace najdete v tématu [velikostí virtuálních počítačů pro obecné účely](../articles/virtual-machines/windows/sizes-general.md#dv3-series-sup1sup).

## <a name="esv3-series"></a>ESv3-series

| Velikost | Poměr|
|---|---|
|Standard_E2s_v3|1|
|Standard_E4s_v3|2|
|Standard_E4 2s_v3|2|
|Standard_E8s_v3|4|
|Standard_E8 2s_v3|4|
|Standard_E8 4s_v3|4|
|Standard_E16s_v3|8|
|Standard_E16 4s_v3|8|
|Standard_E16 8s_v3|8|
|Standard_E32s_v3|16|
|Standard_E32 8s_v3|16|
|Standard_E32 16s_v3|16|
|Standard_E64s_v3|32|
|Standard_E64 16s_v3|32|
|Standard_E64 32s_v3|32|

Další informace najdete v tématu [paměťově optimalizované velikosti virtuálních počítačů](../articles/virtual-machines/windows/sizes-memory.md#esv3-series).

## <a name="ev3-series"></a>Ev3-series

| Velikost | Poměr|
|---|---|
| Standard_E2_v3|1|
|Standard_E4_v3|2|
|Standard_E8_v3|4|
|Standard_E16_v3|8|
|Standard_E32_v3|16|
|Standard_E64_v3|32|

Další informace najdete v tématu [paměťově optimalizované velikosti virtuálních počítačů](../articles/virtual-machines/windows/sizes-memory.md#ev3-series).

## <a name="f-series"></a>F-series

| Velikost | Poměr|
|---|---|
| Standard_F1|1|
|Standard_F2|2|
|Standard_F4|4|
|Standard_F8|8|
Standard_F16|16|

Další informace najdete v tématu [optimalizované velikosti virtuálních počítačů pro výpočetní](../articles/virtual-machines/windows/sizes-compute.md#f-series).

## <a name="fs-series"></a>Řada FS-series

| Velikost | Poměr|
|---|---|
| Standard_F1s|1|
|Standard_F2s|2|
|Standard_F4s|4|
|Standard_F8s|8|
|Standard_F16s|16|

Další informace najdete v tématu [optimalizované velikosti virtuálních počítačů pro výpočetní](../articles/virtual-machines/windows/sizes-compute.md#fs-series-sup1sup).

## <a name="fsv2-series"></a>Fsv2-series

| Velikost | Poměr|
|---|---|
|Standard_F2s_v2|1|
|Standard_F4s_v2|2|
|Standard_F8s_v2|4|
|Standard_F16s_v2|8|
|Standard_F32s_v2|16|
|Standard_F64s_v2|32|
|Standard_F72s_v2|36|

Další informace najdete v tématu [optimalizované velikosti virtuálních počítačů pro výpočetní](../articles/virtual-machines/windows/sizes-compute.md#fsv2-series-sup1sup).

## <a name="h-series"></a>H-series

| Velikost | Poměr|
|---|---|
| Standard_H8|1|
|Standard_H16|2|

Další informace najdete v tématu [vysoce výkonné výpočetní velikosti virtuálních počítačů](../articles/virtual-machines/windows/sizes-hpc.md).

## <a name="h-series-high-memory"></a>Vysoký poměr paměti řady H-series

| Velikost | Poměr|
|---|---|
| Standard_H8m|1|
|Standard_H16m|2|

Další informace najdete v tématu [vysoce výkonné výpočetní velikosti virtuálních počítačů](../articles/virtual-machines/windows/sizes-hpc.md).

## <a name="ls-series"></a>Řada Ls

| Velikost | Poměr|
|---|---|
| Standard_L4s|1|
|Standard_L8s|2|
|Standard_L16s|4|
|Standard_L32s|8|

Další informace najdete v tématu [úložiště optimalizované velikosti virtuálních počítačů](../articles/virtual-machines/windows/sizes-storage.md).

## <a name="m-series"></a>M-Series

| Velikost | Poměr|
|---|---|
| Standard_M64s|1|
|Standard_M128s|2|

Další informace najdete v tématu [paměťově optimalizované velikosti virtuálních počítačů](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="m-series-fractional"></a>Desetinné řady M-series

| Velikost | Poměr|
|---|---|
| Typu Standard_M16s|1|
|Typu Standard_M32s|2|

Další informace najdete v tématu [paměťově optimalizované velikosti virtuálních počítačů](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="m-series-fractional-high-memory"></a>Řada M-series desetinné části velkého množství paměti

| Velikost | Poměr|
|---|---|
|Typu Standard_M8ms|1|
|Standard_M8 až 2 MS|1|
|Standard_M8 4ms|1|
|Typu Standard_M16ms|2|
|Standard_M16 4ms|2|
|Standard_M16 8ms|2|
|Typu Standard_M32ms|4|
|Standard_M32 8ms|4|
|Standard_M32 16ms|4|

Další informace najdete v tématu [paměťově optimalizované velikosti virtuálních počítačů](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="m-series-fractional-large"></a>M-series desetinné velké

| Velikost | Poměr|
|---|---|
| Typu Standard_M32ls|1|
|Typu Standard_M64ls|2|

Další informace najdete v tématu [paměťově optimalizované velikosti virtuálních počítačů](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="m-series-high-memory"></a>Vysoký poměr paměti řady M-series

| Velikost | Poměr|
|---|---|
| Standard_M64ms|1|
|Standard_M64 16ms|1|
|Standard_M64 32ms|1|
|Standard_M128ms|2|
|Standard_M128 32ms|2|
|Standard_M128 64ms|2|

Další informace najdete v tématu [paměťově optimalizované velikosti virtuálních počítačů](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="nc-series"></a>NC-Series

| Velikost | Poměr|
|---|---|
| Standard_NC6|1|
|Standard_NC12|2|
|Standard_NC24|4|

Další informace najdete v tématu [GPU optimalizované velikosti virtuálních počítačů](../articles/virtual-machines/windows/sizes-gpu.md).

## <a name="ncv2-series"></a>NCv2-Series

| Velikost | Poměr|
|---|---|
| Standard_NC6s_v2|1|
|Standard_NC12s_v2|2|
|Standard_NC24s_v2|4|

Další informace najdete v tématu [GPU optimalizované velikosti virtuálních počítačů](../articles/virtual-machines/windows//sizes-gpu.md#ncv2-series).

## <a name="ncv3-series"></a>NCv3-series

| Velikost | Poměr|
|---|---|
| Standard_NC6s_v3|1|
|Standard_NC12s_v3|2|
|Standard_NC24s_v3|4|

Další informace najdete v tématu [GPU optimalizované velikosti virtuálních počítačů](../articles/virtual-machines/windows//sizes-gpu.md#ncv3-series).

## <a name="nd-series"></a>ND-Series

| Velikost | Poměr|
|---|---|
| Standard_ND6s|1|
|Standard_ND12s|2|
|Standard_ND24s|4|

Další informace najdete v tématu [GPU optimalizované velikosti virtuálních počítačů](../articles/virtual-machines/windows//sizes-gpu.md#nd-series).

## <a name="nv-series"></a>NV-Series

| Velikost | Poměr|
|---|---|
| Standard_NV6|1|
|Standard_NV12|2|
|Standard_NV24|4|

Další informace najdete v tématu [GPU optimalizované velikosti virtuálních počítačů](../articles/virtual-machines/windows//sizes-gpu.md#nv-series).



---
author: yashar
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 11-20-2018
ms.openlocfilehash: 790fb39c3d8964c053ffe1d7ee04418fcbc0913c
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2019
ms.locfileid: "68857346"
---
# <a name="virtual-machine-size-flexibility-with-reserved-vm-instances"></a>Flexibilita velikosti virtuálních počítačů pomocí rezervovaných instancí virtuálních počítačů

V případě rezervované instance virtuálního počítače optimalizované pro flexibilitu velikosti instance se může zakoupovaná rezervace vztahovat na velikosti virtuálních počítačů ve stejné skupině řad. Pokud například koupíte rezervaci pro velikost virtuálního počítače, která je uvedená v tabulce DSv2-Series, jako je Standard_DS5_v2, sleva rezervace se může vztahovat na ostatní čtyři velikosti uvedené v této tabulce:

- Standard_DS1_v2
- Standard_DS2_v2
- Standard_DS3_v2
- Standard_DS4_v2

Tato sleva se ale nevztahuje na velikosti virtuálních počítačů, které jsou uvedené v různých tabulkách, jako je například v tabulce s vysokou pamětí DSv2-series: Standard_DS11_v2, Standard_DS12_v2 a tak dále.

V rámci skupiny Size Series se počet virtuálních počítačů, na které se sleva rezervace vztahuje, závisí na velikosti virtuálního počítače, kterou vyberete při nákupu rezervace. Závisí také na velikostech virtuálních počítačů, které používáte. Sloupec s poměrem, který je uveden v následujících tabulkách, porovnává relativní nároky na velikost každého virtuálního počítače v dané skupině. Pomocí hodnoty poměru můžete vypočítat, jak se sleva rezervace vztahuje na virtuální počítače, které používáte.

## <a name="examples"></a>Příklady

V následujících příkladech se používají velikosti a poměry v tabulce DSv2-Series.

 Koupíte rezervovanou instanci virtuálního počítače s velikostí Standard_DS4_v2, kde poměr nebo relativní nároky v porovnání s ostatními velikostmi v této sérii jsou 8.

- Scénář 1: Spusťte 8 virtuálních počítačů velikosti Standard_DS1_v2 s poměrem 1. Vaše sleva na rezervaci se vztahuje na všechny 8 těchto virtuálních počítačů.
- Scénář 2: Spouštějte dva virtuální počítače velikosti Standard_DS2_v2 s poměrem 2. Také spusťte virtuální počítač s velikostí Standard_DS3_v2 s poměrem 4. Celkové nároky jsou 2 + 2 + 4 = 8. Proto se sleva vaší rezervace vztahuje na všechny tři tyto virtuální počítače.
- Scénář 3: Spusťte jeden Standard_DS5_v2 s poměrem 16. Vaše sleva za rezervaci se vztahuje na polovinu výpočetních nákladů tohoto virtuálního počítače.

V následujících částech se dozvíte, jaké velikosti jsou ve stejné skupině řad, když koupíte rezervovanou instanci virtuálního počítače optimalizovanou pro flexibilitu velikosti instance.

## <a name="b-series"></a>B-Series

| Size | Stran|
|---|---|
| Standard_B1s | 1 |
|Standard_B2s|4|

Další informace najdete v tématu [velikosti virtuálních počítačů s více řadami řady B-Series](../articles/virtual-machines/windows/b-series-burstable.md).

## <a name="b-series-high-memory"></a>Vysoká paměť řady B-Series

| Size | Stran|
|---|---|
| Standard_B1ms |1|
|Standard_B2ms|4|
|Standard_B4ms|8|
|Standard_B8ms|16|

Další informace najdete v tématu [velikosti virtuálních počítačů s více řadami řady B-Series](../articles/virtual-machines/windows/b-series-burstable.md).

## <a name="d-series"></a>D-series

| Size | Stran|
|---|---|
| Standard_D1|1|
|Standard_D2|2|
|Standard_D3|4|
|Standard_D4|8|

Další informace najdete v tématu [předchozí generace velikostí virtuálních počítačů](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="d-series-high-memory"></a>Paměť řady D-Series s vysokou pamětí

| Size | Stran|
|---|---|
| Standard_D11|1|
|Standard_D12|2|
|Standard_D13|4|
|Standard_D14|8|

Další informace najdete v tématu [předchozí generace velikostí virtuálních počítačů](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="ds-series"></a>Řada DS

| Size | Stran|
|---|---|
|Standard_DS1|1|
|Standard_DS2|2|
|Standard_DS3|4|
|Standard_DS4|8|

Další informace najdete v tématu [předchozí generace velikostí virtuálních počítačů](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="ds-series-high-memory"></a>Vysoká paměť řady DS-Series

| Size | Stran|
|---|---|
|Standard_DS11|1|
|Standard_DS12|2|
|Standard_DS13|4|
|Standard_DS14|8|

Další informace najdete v tématu [předchozí generace velikostí virtuálních počítačů](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dsv2-series"></a>DSv2-series

| Size | Stran|
|---|---|
|Standard_DS1_v2|1|
|Standard_DS2_v2|2|
|Standard_DS3_v2|4|
|Standard_DS4_v2|8|
|Standard_DS5_v2|16|

Další informace najdete v tématu [předchozí generace velikostí virtuálních počítačů](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dsv2-series-high-memory"></a>Vysoká paměť řady DSv2-Series

| Size | Stran|
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

Další informace najdete v tématu [předchozí generace velikostí virtuálních počítačů](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dsv2-high-memory-isolated-series"></a>DSv2 s vysokou pamětí – izolované řady

| Size | Stran|
|---|---|
|Standard_DS15i_v2|1|

Další informace najdete v tématu [předchozí generace velikostí virtuálních počítačů](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dsv3-series"></a>DSv3-Series

| Size | Stran|
|---|---|
|Standard_D2s_v3|1|
|Standard_D4s_v3|2|
|Standard_D8s_v3|4|
|Standard_D16s_v3|8|
|Standard_D32s_v3|16|
|Standard_D64s_v3|32|

Další informace najdete v tématu [velikosti virtuálních počítačů pro obecné účely](../articles/virtual-machines/windows/sizes-general.md#dsv3-series-1).

## <a name="dv2-series"></a>Dv2-series

| Size | Stran|
|---|---|
|Standard_D1_v2|1|
|Standard_D2_v2|2|
|Standard_D3_v2|4|
|Standard_D4_v2|8|
|Standard_D5_v2|16|

Další informace najdete v tématu [předchozí generace velikostí virtuálních počítačů](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dv2-series-high-memory"></a>Vysoká paměť řady Dv2-Series

| Size | Stran|
|---|---|
|Standard_D11_v2|1|
|Standard_D12_v2|2|
|Standard_D13_v2|4|
|Standard_D14_v2|8|
|Standard_D15_v2|10|

Další informace najdete v tématu [předchozí generace velikostí virtuálních počítačů](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dv2--high-memory-isolated-series"></a>Dv2 s vysokou pamětí – izolované řady

| Size | Stran|
|---|---|
|Standard_D15i_v2|1|

Další informace najdete v tématu [předchozí generace velikostí virtuálních počítačů](../articles/virtual-machines/windows/sizes-previous-gen.md).


## <a name="dv3-series"></a>Dv3-series

| Size | Stran|
|---|---|
| Standard_D2_v3|1|
|Standard_D4_v3|2|
|Standard_D8_v3|4|
|Standard_D16_v3|8|
|Standard_D32_v3|16|
|Standard_D64_v3|32|

Další informace najdete v tématu [velikosti virtuálních počítačů pro obecné účely](../articles/virtual-machines/windows/sizes-general.md#dv3-series-1).

## <a name="esv3-series"></a>ESv3-series

| Size | Stran|
|---|---|
|Standard_E2s_v3|1|
|Standard_E4s_v3|2|
|Standard_E4-2s_v3|2|
|Standard_E8s_v3|4|
|Standard_E8-2s_v3|4|
|Standard_E8-4s_v3|4|
|Standard_E16s_v3|8|
|Standard_E16-4s_v3|8|
|Standard_E16-8s_v3|8|
|Standard_E20s_v3|10|
|Standard_E32s_v3|16|
|Standard_E32-8s_v3|16|
|Standard_E32-16s_v3|16|
|Standard_E64s_v3|28,8|
|Standard_E64-16s_v3|28,8|
|Standard_E64-32s_v3|28,8|

Další informace najdete v tématu [paměťově optimalizované velikosti virtuálních počítačů](../articles/virtual-machines/windows/sizes-memory.md#esv3-series).

## <a name="esv3-series-isolated-series"></a>ESv3 Series Isolated-Series

| Size | Stran|
|---|---|
|Standard_E64is_v3|1|

## <a name="ev3-series"></a>Ev3-series

| Size | Stran|
|---|---|
|Standard_E2_v3|1|
|Standard_E4_v3|2|
|Standard_E8_v3|4|
|Standard_E16_v3|8|
|Standard_E20_v3|10|
|Standard_E32_v3|16|
|Standard_E64_v3|32|

Další informace najdete v tématu [paměťově optimalizované velikosti virtuálních počítačů](../articles/virtual-machines/windows/sizes-memory.md#ev3-series).

## <a name="ev3-series-isolated-series"></a>Ev3 Series Isolated-Series

| Size | Stran|
|---|---|
|Standard_E64i_v3|1|

Další informace najdete v tématu [paměťově optimalizované velikosti virtuálních počítačů](../articles/virtual-machines/windows/sizes-memory.md#ev3-series).

## <a name="f-series"></a>F-series

| Size | Stran|
|---|---|
| Standard_F1|1|
|Standard_F2|2|
|Standard_F4|4|
|Standard_F8|8|
Standard_F16|16|

Další informace najdete v tématu [předchozí generace velikostí virtuálních počítačů](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="fs-series"></a>Řada FS

| Size | Stran|
|---|---|
| Standard_F1s|1|
|Standard_F2s|2|
|Standard_F4s|4|
|Standard_F8s|8|
|Standard_F16s|16|

Další informace najdete v tématu [předchozí generace velikostí virtuálních počítačů](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="fsv2-series"></a>Fsv2-series

| Size | Stran|
|---|---|
|Standard_F2s_v2|1|
|Standard_F4s_v2|2|
|Standard_F8s_v2|4|
|Standard_F16s_v2|8|
|Standard_F32s_v2|16|
|Standard_F64s_v2|32|
|Standard_F72s_v2|36|

Další informace najdete v tématu [výpočty optimalizované velikosti virtuálních počítačů](../articles/virtual-machines/windows/sizes-compute.md#fsv2-series-1).

## <a name="h-series"></a>H-series

| Size | Stran|
|---|---|
| Standard_H8|1|
|Standard_H16|2|

Další informace najdete v tématu [vysoce výkonné výpočetní virtuální počítače s vysokým výkonem](../articles/virtual-machines/windows/sizes-hpc.md).

## <a name="h-series-high-memory"></a>Vysoká paměť řady H-Series

| Size | Stran|
|---|---|
| Standard_H8m|1|
|Standard_H16m|2|

Další informace najdete v tématu [vysoce výkonné výpočetní virtuální počítače s vysokým výkonem](../articles/virtual-machines/windows/sizes-hpc.md).


## <a name="hcs-series"></a>KLIENTOVI HCS-Series

| Size | Stran|
|---|---|
|Standard_HC44rs|1|

Další informace najdete v tématu [vysoce výkonné výpočetní virtuální počítače s vysokým výkonem](../articles/virtual-machines/windows/sizes-hpc.md).

## <a name="hbs-series"></a>HR-Series

| Size | Stran|
|---|---|
|Standard_HB60rs|1|

Další informace najdete v tématu [vysoce výkonné výpočetní virtuální počítače s vysokým výkonem](../articles/virtual-machines/windows/sizes-hpc.md).

## <a name="hbs-series"></a>HR-Series

| Size | Stran|
|---|---|
|Standard_HB60rs|1|

Další informace najdete v tématu [vysoce výkonné výpočetní virtuální počítače s vysokým výkonem](../articles/virtual-machines/windows/sizes-hpc.md).

## <a name="h-series-low-latency-series"></a>Řada H Series s nízkou latencí

| Size | Stran|
|---|---|
|Standard_H16r|1|

Další informace najdete v tématu [velikosti virtuálních počítačů optimalizované pro úložiště](../articles/virtual-machines/windows/sizes-storage.md).

## <a name="h-series-high-memory-low-latency-series"></a>Řada H řady H s vysokou latencí v řadě

| Size | Stran|
|---|---|
|Standard_H16mr|1|

Další informace najdete v tématu [velikosti virtuálních počítačů optimalizované pro úložiště](../articles/virtual-machines/windows/sizes-storage.md).

## <a name="h-series"></a>H-series

| Size | Stran|
|---|---|
|Standard_H8|1|
|Standard_H16|2|

Další informace najdete v tématu [velikosti virtuálních počítačů optimalizované pro úložiště](../articles/virtual-machines/windows/sizes-storage.md).

## <a name="lsv2-series-series"></a>Série LSv2 Series

| Size | Stran|
|---|---|
|Standard_L8s_v2|1|
|Standard_L16s_v2|2|
|Standard_L32s_v2|4|
|Standard_L48s_v2|6|
|Standard_L64s_v2|8|
|Standard_L80s_v2|10|
|Standard_L96s_v2|12|

Další informace najdete v tématu [velikosti virtuálních počítačů optimalizované pro úložiště](../articles/virtual-machines/windows//sizes-storage.md#lsv2-series).

## <a name="m-series"></a>M-Series

| Size | Stran|
|---|---|
| Standard_M64s|1|
|Standard_M128s|2|

Další informace najdete v tématu [paměťově optimalizované velikosti virtuálních počítačů](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="m-series-fractional"></a>Zlomky řady M-Series

| Size | Stran|
|---|---|
| Standard_M16s|1|
|Standard_M32s|2|

Další informace najdete v tématu [paměťově optimalizované velikosti virtuálních počítačů](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="m-series-fractional-high-memory"></a>Velký zlom paměti řady M-Series

| Size | Stran|
|---|---|
|Standard_M8ms|1|
|Standard_M8-2ms|1|
|Standard_M8-4ms|1|
|Standard_M16ms|2|
|Standard_M16-4ms|2|
|Standard_M16-8ms|2|
|Standard_M32ms|4|
|Standard_M32-8ms|4|
|Standard_M32-16ms|4|

Další informace najdete v tématu [paměťově optimalizované velikosti virtuálních počítačů](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="m-series-fractional-large"></a>Zlomky řady M-large

| Size | Stran|
|---|---|
|Standard_M32ls|1|
|Standard_M64ls|2|

Další informace najdete v tématu [paměťově optimalizované velikosti virtuálních počítačů](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="m-series-high-memory"></a>Řada M-Series s vysokou pamětí

| Size | Stran|
|---|---|
|Standard_M64ms|1|
|Standard_M64-16ms|1|
|Standard_M64-32ms|1|
|Standard_M128ms|2|
|Standard_M128-32ms|2|
|Standard_M128-64ms|2|

Další informace najdete v tématu [paměťově optimalizované velikosti virtuálních počítačů](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="ms-series-fractional-tiny"></a>Řada MS s malými částmi

| Size | Stran|
|---|---|
|Standard_M32ls|1|

Další informace najdete v tématu [paměťově optimalizované velikosti virtuálních počítačů](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="msv2-series-high-memory-series"></a>Řada MSv2 řady s vysokou pamětí

| Size | Stran|
|---|---|
|Standard_M208ms_v2|1|
|Standard_M416ms_v2|2|

Další informace najdete v tématu [paměťově optimalizované velikosti virtuálních počítačů](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="msv2-series"></a>MSv2-Series

| Size | Stran|
|---|---|
|Standard_M208s_v2|1|
|Standard_M416s_v2|2|

Další informace najdete v tématu [paměťově optimalizované velikosti virtuálních počítačů](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="nc-series"></a>NC-Series

| Size | Stran|
|---|---|
|Standard_NC6|1|
|Standard_NC12|2|
|Standard_NC24|4|

Další informace najdete v tématu [velikosti virtuálních počítačů optimalizované pro procesory GPU](../articles/virtual-machines/windows/sizes-gpu.md).

## <a name="ncv2-series"></a>NCv2-Series

| Size | Stran|
|---|---|
| Standard_NC6s_v2|1|
|Standard_NC12s_v2|2|
|Standard_NC24s_v2|4|

Další informace najdete v tématu [velikosti virtuálních počítačů optimalizované pro procesory GPU](../articles/virtual-machines/windows//sizes-gpu.md#ncv2-series).

## <a name="ncv3-series"></a>NCv3-series

| Size | Stran|
|---|---|
| Standard_NC6s_v3|1|
|Standard_NC12s_v3|2|
|Standard_NC24s_v3|4|

Další informace najdete v tématu [velikosti virtuálních počítačů optimalizované pro procesory GPU](../articles/virtual-machines/windows//sizes-gpu.md#ncv3-series).

## <a name="nd-series"></a>ND-Series

| Size | Stran|
|---|---|
| Standard_ND6s|1|
|Standard_ND12s|2|
|Standard_ND24s|4|

Další informace najdete v tématu [velikosti virtuálních počítačů optimalizované pro procesory GPU](../articles/virtual-machines/windows//sizes-gpu.md#nd-series).

## <a name="nv-series"></a>NV-Series

| Size | Stran|
|---|---|
| Standard_NV6|1|
|Standard_NV12|2|
|Standard_NV24|4|

## <a name="nvsv3-series"></a>NVSv3-Series

| Size | Stran|
|---|---|
|Standard_NV12s_v3|1|
|Standard_NV24s_v3|2|
|Standard_NV48s_v3|4|

## <a name="nds-series-low-latency-series"></a>Řada NDS – řada s nízkou latencí

| Size | Stran|
|---|---|
|Standard_ND24rs|1|

## <a name="ncsv3-series-low-latency-series"></a>Série NCSv3 s nízkou latencí

| Size | Stran|
|---|---|
|Standard_NC24rs_v3|1|

Další informace najdete v tématu [velikosti virtuálních počítačů optimalizované pro procesory GPU](../articles/virtual-machines/windows//sizes-gpu.md#ncv3-series).

## <a name="ncsv2-series-low-latency-series"></a>Série NCSv2 s nízkou latencí

| Size | Stran|
|---|---|
|Standard_NC24rs_v2|1|

Další informace najdete v tématu [velikosti virtuálních počítačů optimalizované pro procesory GPU](../articles/virtual-machines/windows//sizes-gpu.md#ncv2-series).

## <a name="nc-series-low-latency-series"></a>Řada NC s nízkou latencí – řada

| Size | Stran|
|---|---|
|Standard_NC24r|1|

Další informace najdete v tématu [velikosti virtuálních počítačů optimalizované pro procesory GPU](../articles/virtual-machines/windows//sizes-gpu.md#ncv2-series).






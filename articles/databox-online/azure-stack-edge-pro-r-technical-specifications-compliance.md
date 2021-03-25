---
title: Technické specifikace a dodržování předpisů v Microsoft Azure Stack Edge pro R | Microsoft Docs
description: Seznamte se s technickými specifikacemi a dodržováním předpisů pro zařízení Azure Stack Edge pro R.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 03/24/2021
ms.author: alkohli
ms.openlocfilehash: aa1b861555cff65c9e432ea711af3f7c6e410625
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2021
ms.locfileid: "105109161"
---
# <a name="azure-stack-edge-pro-r-technical-specifications"></a>Azure Stack Edge pro R – technické specifikace

Hardwarové součásti Azure Stack hraničního zařízení pro R vyhovují technickým specifikacím, které jsou uvedené v tomto článku. Technické specifikace popisují jednotky zdroje napájení (PSUs), kapacitu úložiště, skříně a environmentální standardy.


## <a name="compute-memory-specifications"></a>Výpočetní prostředí, specifikace paměti

Zařízení Azure Stack Edge pro R má následující specifikace pro výpočetní výkon a paměť:

| Specifikace       | Hodnota                  |
|---------------------|------------------------|
| Procesor    | 2 X procesor Intel Xeon stříbrné 4114<br>20 phsyical jader (10 na procesor)<br>40 logických jader (vCPU) (20 na procesor)  |
| Memory (Paměť)              | 256 GB RAM (2666 MT/s)     |


## <a name="compute-acceleration-specifications"></a>Specifikace zrychlení výpočtů

Grafický procesor (GPU) je součástí každého zařízení, které umožňuje scénáře Kubernetes, hloubkového učení a strojového učení.

| Specifikace           | Hodnota                  |
|-------------------------|----------------------------|
| GPU   | Jeden grafický procesor nVidia T4 <br> Další informace najdete v tématu [NVIDIA T4](https://www.nvidia.com/en-us/data-center/tesla-t4/).| 

## <a name="power-supply-unit-specifications"></a>Specifikace jednotek napájení

Zařízení Azure Stack Edge pro R má dvě jednotky napájení 100-240 V (PSUs) s vysoce výkonnými ventilátory. Tato dvě PSUsa poskytují redundantní konfiguraci napájení. Pokud dojde k selhání PSU, zařízení bude nadále fungovat normálně na ostatních PSU, dokud se neúspěšně nenahradí modul. V následující tabulce jsou uvedeny technické specifikace PSUs.

| Specifikace           | 550 W PSU                  |
|-------------------------|----------------------------|
| Maximální výstupní výkon    | 550 W                      |
| Rozptyl tepla (maximum)                   | 2891 BTU/hod                |
| Frekvence               | 50/60 Hz                   |
| Výběr rozsahu napětí | Automatické rozsahy: 115-230 V AC |
| Horká, připojitelná           | Yes                        |

## <a name="network-specifications"></a>Specifikace sítě

Zařízení Azure Stack Edge pro R má čtyři síťová rozhraní PORT1-PORT4. 


|Specifikace  |Description                              |
|----------------------|----------------------------------|
|Síťová rozhraní    |**2 x 1 GbE RJ45** <br> PORT 1 se používá jako rozhraní pro správu pro počáteční instalaci a ve výchozím nastavení je statický. Po dokončení počáteční instalace můžete použít rozhraní pro data s libovolnou IP adresou. Při resetování se ale rozhraní vrátí zpět na statickou IP adresu. <br>Druhý PORT rozhraní 2 je uživatelsky konfigurovatelné, dá se použít k přenosu dat a je ve výchozím nastavení DHCP.     |
|Síťová rozhraní    |**2 × 25 GbE SFP28** <br> Tato datová rozhraní PORT 3 a PORT 4 je možné nakonfigurovat jako DHCP (výchozí) nebo static.            |

Zařízení Azure Stack Edge pro R má následující síťový hardware:

* **Mellanox Dual Port 25G ConnectX-4 síťový adaptér kanálu** – port 3 a port 4. 

<!--Here are the details for the Mellanox card: MCX4421A-ACAN

| Parameter           | Description                 |
|-------------------------|----------------------------|
| Model    | ConnectX®-4 Lx EN network interface card                      |
| Model Description               | 25GbE dual-port SFP28; PCIe3.0 x8; ROHS R6                    |
| Device Part Number (XR2) | MCX4421A-ACAN  |
| PSID (R640)           | MT_2420110034                         |-->
<!-- confirm w/ Ravi what is this-->

Úplný seznam podporovaných kabelů, přepínačů a vysílačů pro tyto síťové karty najdete v následujících [produktech: Mellanox Dual Port 25G ConnectX-4 kompatibilní produkty síťového adaptéru](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products).

## <a name="storage-specifications"></a>Specifikace úložiště

Zařízení Azure Stack Edge pro R mají 8 datových disků a 2 M. 2 disky SATA, které slouží jako disky s operačním systémem. Další informace najdete na [discích SATA. 2](https://en.wikipedia.org/wiki/M.2).

#### <a name="storage-for-1-node-device"></a>Úložiště pro zařízení s jedním uzlem

Následující tabulka obsahuje podrobné informace o kapacitě úložiště zařízení s jedním uzlem.

|     Specifikace                          |     Hodnota             |
|--------------------------------------------|-----------------------|
|    Počet jednotek SSD (Solid-State Drive) (SSD)     |    8                  |
|    Jedna kapacita SSD                     |    8 TB               |
|    Celková kapacita                          |    64 TB              |
|    Celková použitelná kapacita *                  |    ~ 42 TB          |

**Místo pro interní použití je rezervované.*

<!--#### Storage for 4-node device

The following table has the details for the storage capacity of the 4-node device.

|     Specification                          |     Value             |
|--------------------------------------------|-----------------------|
|    Number of solid-state drives (SSDs)     |    32 (4 X 8 disks for 4 devices)                |
|    Single SSD capacity                     |    8 TB               |
|    Total capacity                          |    256 TB              |
|    Total usable capacity*                  |    ~ 163 TB          |

**After mirroring and parity, and reserving some space for internal use.* -->


## <a name="enclosure-dimensions-and-weight-specifications"></a>Rozměry skříně a specifikace váhy

V následujících tabulkách jsou uvedeny různé specifikace velikosti skříně pro rozměry a váhu.

### <a name="enclosure-dimensions"></a>Rozměry skříně 

V následující tabulce jsou uvedeny rozměry zařízení a UPS s robustním případem v milimetrech a palcích.

|     Skříně     |     Milimetrech     |     Cm     |
|-------------------|---------------------|----------------|
|    Height (Výška)         |    301,2            |    11,86       |
|    Width (Šířka)          |    604,5            |    23,80       |
|    Délka         |    740,4            |    35,50       |

<!--#### For the 4-node system

For the 4-node system, the servers and the heater are shipped in a 5U case and the UPS are shipped in a 4U case.

The following table lists the dimensions of the 5U device case:  

|     Enclosure     |     Millimeters   |     Inches     |
|-------------------|-------------------|----------------|
|    Height         |    387.4          |    15.25       |
|    Width          |    604.5          |    23.80       |
|    Length         |    901.7          |    35.50       |

The following table lists the dimensions of the 4U UPS case: 

|     Enclosure     |     Millimeters   |     Inches    |
|-------------------|-------------------|---------------|
|    Height         |    342.9          |    13.5       |
|    Width          |    604.5          |   23.80       |
|    Length         |    901.7          |   35.50       |
-->

### <a name="enclosure-weight"></a>Váha skříně 

Váha zařízení závisí na konfiguraci skříně.

|     Skříně                                 |     Hmotnost          |
|-----------------------------------------------|---------------------|
|    Celková váha 1-Node zařízení + robustní případ s koncovými písmeny     |    ~ 114 kg.          |

<!--#### For the 4-node system

|     Enclosure                                 |     Weight          |
|-----------------------------------------------|---------------------|
|   Approximate weight of fully populated 4 devices + heater in 5U case     |    ~200 lbs.          |
|   Approximate weight of fully populated 4 UPS in 4U case    |    ~145 lbs.          |
-->

## <a name="enclosure-environment-specifications"></a>Specifikace prostředí skříně

V této části jsou uvedeny specifikace týkající se prostředí skříně, například teploty, vibrací, nárazu a nadmořského nadmořského prostředí.


|     Specifikace              |     Hodnota    |
|--------------------------------|-------------------------------------------------------------------|
|     Rozsah teploty          |     0 – 43 °c (provozní)    |
|     Vibrační                  |     MIL-STD-810 metoda 514,7 *<br>Postup I CAT 4, 20                  |
|     Náraz                      |     MIL-STD-810 metoda 516,7 *<br>Postup IV, logistická                 |
|     Mořské                   |     Provoz: 10 000 metrů<br>Nefunkční: 40 000 metrů          |

**Všechny odkazy jsou MIL-STD-810G Change 1 (2014)*

## <a name="next-steps"></a>Další kroky

- [Nasazení Azure Stack Edge](azure-stack-edge-placeholder.md)

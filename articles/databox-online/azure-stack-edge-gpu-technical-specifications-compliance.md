---
title: Microsoft Azure Stack Edge pro s technickými specifikacemi GPU a dodržování předpisů | Microsoft Docs
description: Seznamte se s technickými specifikacemi a dodržováním předpisů pro zařízení Azure Stack Edge pro s grafickým procesorem.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 01/19/2021
ms.author: alkohli
ms.openlocfilehash: d40d26e8fab0832a37a43c353e11189f8f193f14
ms.sourcegitcommit: 65cef6e5d7c2827cf1194451c8f26a3458bc310a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2021
ms.locfileid: "98573322"
---
# <a name="technical-specifications-and-compliance-for-azure-stack-edge-pro-with-gpu"></a>Technické specifikace a dodržování předpisů pro Azure Stack Edge pro pomocí GPU 

Hardwarové součásti vaší Azure Stack Edge pro s integrovaným grafickým procesorem (GPU) se řídí technickými specifikacemi a regulativními normami, které jsou uvedené v tomto článku. Technické specifikace popisují hardware, jednotky zdroje napájení (PSUs), kapacitu úložiště, skříně a environmentální standardy.

## <a name="compute-and-memory-specifications"></a>Specifikace výpočtů a paměti

Zařízení Azure Stack Edge pro má následující specifikace pro výpočetní výkon a paměť:

| Specifikace           | Hodnota                  |
|-------------------------|----------------------------|
| Procesor                     | 2 X procesor Intel Xeon stříbrné 4214 (Cascade Lake) – procesor            |
| Memory (Paměť)                  | 128 (8x16 GB) GB RAM <br> Dell kompatibilní 16 GB PC4-23400 DDR4-2933Mhz 2Rx8 1.2 v ECC registrovaný RDIMM       |


## <a name="compute-acceleration-specifications"></a>Specifikace zrychlení výpočtů

Grafický procesor (GPU) je součástí každého zařízení Azure Stack Edge pro, které umožňuje scénáře Kubernetes, hloubkového učení a strojového učení.

| Specifikace           | Hodnota                  |
|-------------------------|----------------------------|
| GPU   | Jeden nebo dva grafické procesory nVidia T4 <br> Další informace najdete v tématu [NVIDIA T4](https://www.nvidia.com/en-us/data-center/tesla-t4/).| 


## <a name="power-supply-unit-specifications"></a>Specifikace jednotek napájení

Zařízení Azure Stack Edge pro má dvě 100-240 V jednotkách napájení (PSUs) s vysoce výkonnými ventilátory. Tato dvě PSUsa poskytují redundantní konfiguraci napájení. Pokud dojde k selhání PSU, zařízení bude nadále fungovat normálně na ostatních PSU, dokud se neúspěšně nenahradí modul. V následující tabulce jsou uvedeny technické specifikace PSUs.

| Specifikace           | 750 W PSU                  |
|-------------------------|----------------------------|
| Maximální výstupní výkon    | 750 W                     |
| Frekvence               | 50/60 Hz                   |
| Výběr rozsahu napětí | Automatické rozsahy: 100-240 V AC |
| Horká, připojitelná           | Ano                        |


## <a name="network-interface-specifications"></a>Specifikace síťového rozhraní

Vaše zařízení Azure Stack Edge pro má šest síťových rozhraní PORT1-PORT6.

| Specifikace           | Popis                 |
|-------------------------|----------------------------|
|  Síťová rozhraní    | **rozhraní 2 X 1 GbE** – 1 rozhraní pro správu se používá pro počáteční instalaci a ve výchozím nastavení je statický. Po dokončení počáteční instalace můžete použít rozhraní pro data s libovolnou IP adresou. Při resetování se ale rozhraní vrátí zpět na statickou IP adresu. <br>Druhý port rozhraní 2 je uživatelsky konfigurovatelné, dá se použít k přenosu dat a je ve výchozím nastavení DHCP. <br>**4 X 25 rozhraní rozhraní** – tato datová rozhraní, port 3 až port 6, můžou být nakonfigurovaná uživatelem jako DHCP (výchozí) nebo static. Můžou také fungovat jako rozhraní 10 GbE.  | 

Vaše zařízení Azure Stack Edge pro má následující síťový hardware:

* **Vlastní Microsoft QLogic Cavium 25G Norwegian Developers Conference Adapter** -port 1 až 4.
* **Mellanox Dual Port 25G ConnectX-4 síťový adaptér kanálu** – port 5 a port 6.

Tady jsou podrobnosti o kartě Mellanox:

| Parametr           | Popis                 |
|-------------------------|----------------------------|
| Modelování    | ConnectX®-4 LX EN síťová karta                      |
| Popis modelu               | 25 GbE Dual-Port SFP28; PCIe 3.0 x8; ROHS R6                    |
| Číslo součásti zařízení (R640) | MCX4121A-ACAT  |
| PSID MÁ (R640)           | MT_2420110034                         |

Úplný seznam podporovaných kabelů, přepínačů a vysílačů pro tyto síťové karty najdete tady:

- [Cavium 25G pro interoperabilitu adaptéru Norwegian Developers Conference pro adaptér QLogic](https://www.marvell.com/documents/xalflardzafh32cfvi0z/).
- [Mellanox Dual Port 25G ConnectX-4 kompatibilní produkty síťového adaptéru sítě](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products)VLAN.  

## <a name="storage-specifications"></a>Specifikace úložiště

Zařízení Azure Stack Edge pro mají pět 2,5 DC P4610 SSD, každé s kapacitou 1,6 TB. Spouštěcí jednotka je 240 GB SATA SSD. Celková použitelná kapacita pro zařízení je zhruba 4,19 TB. V následující tabulce je uvedena kapacita úložiště zařízení.

|     Specifikace                          |     Hodnota             |
|--------------------------------------------|-----------------------|
|    Počet NVMe SSD                     |    5                  |
|    Jedna kapacita NVMe SSD                |    1,6 TB             |
|    Spouštěcí SATA jednotky SSD (Solid-State Drive)      |    1                  |
|    Spouštěcí kapacita SSD                       |    240 GB             |
|    Celková kapacita                          |    8,0 TB             |
|    Celková použitelná kapacita                   |    ~ 4,19 TB          |
|    Konfigurace RAID                      |    Prostory úložiště s přímým přístupem s kombinací zrcadlení a parity  |
|    Řadič SAS                          |    HBA330 12 GB/s     |

<!--Remove based on feedback from Ravi
## Other hardware specifications

Your Azure Stack Edge Pro device also contains the following hardware:

* iDRAC baseboard management
* Performance fans
* Custom ID module-->

## <a name="enclosure-dimensions-and-weight-specifications"></a>Rozměry skříně a specifikace váhy

V následujících tabulkách jsou uvedeny různé specifikace velikosti skříně pro rozměry a váhu.

### <a name="enclosure-dimensions"></a>Rozměry skříně

V následující tabulce jsou uvedeny rozměry skříně zařízení 1U v milimetrech a palcích.

|     Skříně     |     Milimetrech     |     Cm     |
|-------------------|---------------------|----------------|
|    Height (Výška)         |    44,45            |    1,75 "       |
|    Width (Šířka)          |    434,1            |    17,09 "      |
|    Délka         |    740,4            |    29,15 "      |

V následující tabulce jsou uvedeny rozměry balíčku pro expedici v milimetrech a palcích.

|     Balíček     |     Milimetrech     |     Cm     |
|-------------------|---------------------|----------------|
|    Height (Výška)         |    311,2            |    12,25 "          |
|    Width (Šířka)          |    642,8            |    25,31 "          |
|    Délka         |    1 051,1          |    41,38 "          |

### <a name="enclosure-weight"></a>Váha skříně

Balíček zařízení má hmotnost 66 kg. a ke zpracování vyžaduje dvě osoby. Váha zařízení závisí na konfiguraci skříně.

|     Skříně                                 |     Hmotnost          |
|-----------------------------------------------|---------------------|
|    Celková váha včetně balení       |    61 kg          |
|    Váha zařízení                       |    35 kg          |

## <a name="enclosure-environment-specifications"></a>Specifikace prostředí skříně

V této části jsou uvedeny specifikace týkající se prostředí skříně, jako je teplota, vlhkost a nadmořská výška.

### <a name="temperature-and-humidity"></a>Teplota a vlhkost

|     Skříně         |     Rozsah okolních teplot     |     Relativní vlhkost okolí     |     Maximální bod Dew     |
|-----------------------|--------------------------------------|--------------------------------------|---------------------------|
|    Provoz        |    10 °C-35 OC (50 °F-86 °F)         |    10% až 80% nekondenzující.         |    29 °C (84 °F)            |
|    Není funkční    |    -40 oC až 65 oC (-40 °F-149 °F)     |    5% až 95% nekondenzující.          |    33 OC (91 °F)            |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>Provozní flow, nadmořská, náraz, vibrace, orientace, bezpečnost a EMC

|     Skříně                           |     Provozní specifikace                                                                                                                                                                                         |
|-----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    Tok dat                              |    Tok systému je zepředu zezadu. Systém musí být provozován s nízkým tlakem na zadní výfukovou instalaci. <!--Back pressure created by rack doors and obstacles should not exceed 5 pascals (0.5 mm water gauge).-->    |
| Příchozí ochrana (IP)                 |    Tento typ zařízení připojeného k racku pro vnitřní použití se většinou netestuje na ochranu příchozích dat (pro ochranu proti pevným a kapalinám pro elektrickou skříň). Posouzení bezpečnosti od výrobce ukazuje IPXO (bez ochrany příchozích dat).  |
|    Maximální výška, provozní        |    3048 měřičů (10 000 metrů) s maximální provozní teplotou rozhodnocenou [specifikací pro stanovení provozní teploty](#operating-temperature-de-rating-specifications).                                                                                |
|    Maximální nadlimitní hodnota, která není funkční    |    12 000 měřičů (39 370 metrů)                                                                                                                                                                                         |
|    Úraz, provozní                   |    6 G až 11 milisekund v 6 orientaci                                                                                                                                                                         |
|    Úraz, jiný než provozní               |    71 G na 2 milisekundy v 6 orientaci                                                                                                                                                                           |
|    Vibrace, provozní               |    0,26 G<sub>RMS</sub> 5 Hz až 350 Hz náhodně                                                                                                                                                                                     |
|    Vibrace, jiné než provozní           |    1,88 G<sub>RMS</sub> 10 Hz až 500 Hz po dobu 15 minut (všech šest stran testováno.)                                                                                                                                                  |
|    Orientace a připojení             |    19. Standard připojení racku (1U)                                                                                                                                                                                       |
|    Bezpečnost a schválení                 |    EN 60950-1:2006 + a1:2010 + a2:2013 + A11:2009 + A12:2011/IEC 60950-1:2005 ED2 + a1:2009 + a2:2013 EN 62311:2008                                                                                                                                                                       |
|    SOFTWARE                                  |    FCC A, ICES – 003 <br>EN 55032:2012/CISPR 32:2012  <br>EN 55032:2015/CISPR 32:2015  <br>EN 55024:2010 + A1:2015/CISPR 24:2010 + A1:2015  <br>EN 61000-3-2:2014/IEC 61000-3-2:2014 (třída D)   <br>EN 61000-3-3:2013/IEC 61000-3-3:2013                                                                                                                                                                                         |
|    Energetický sektor             |    Nařízení Komise (EU) – ne. 617/2013                                                                                                                                                                                        |
|    RoHS           |    EN 50581:2012                                                                                                                                                                                        |

### <a name="operating-temperature-de-rating-specifications"></a>Specifikace pro dehodnocení provozní teploty

|     Dehodnocení provozní teploty     |     Rozsah okolních teplot                                                         |
|--------------------------------------------|------------------------------------------------------------------------------------------|
|    Až 35 oC (95 °F)                       |    Maximální teplota se sníží o 1 °C/300 m (1 °F/547 ft) nad 950 m (3 117 ft).    |
|    35 oC až 40 oC (95 °F až 104 °F)            |    Maximální teplota se zmenší o 1 °C/175 m (1 °F/319 ft) nad 950 m (3 117 ft).    |
|    40 oC až 45 °C (104 °F až 113 °F)           |    Maximální teplota se sníží o 1 °C/125 m (1 °F/228 ft) nad 950 m (3 117 ft).    |

## <a name="next-steps"></a>Další kroky

[Nasazení Azure Stack Edge pro](azure-stack-edge-gpu-deploy-prep.md)

---
title: Identifikace požadovaných zařízení
description: Seznamte se s hardwarem a virtuálním zařízením pro certifikovaného Defenderu pro senzory IoT a místní konzolu pro správu.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 01/13/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 2ad5bf08542cd98f7acae36827b1a7b284a893b0
ms.sourcegitcommit: 6776f0a27e2000fb1acb34a8dddc67af01ac14ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "103149290"
---
# <a name="identify-required-appliances"></a>Identifikace požadovaných zařízení

Tento článek poskytuje informace o certifikovaném programu Defender pro zařízení IoT snímače. V programu Defender Fort IoT se dá nasadit na fyzických i virtuálních zařízeních.

To zahrnuje certifikované *předem nakonfigurovaná* zařízení, na kterých je už nainstalovaný software, a také nenakonfigurovaná certifikovaná zařízení, na kterých si můžete stáhnout a nainstalovat požadovaný software.

Článek také poskytuje specifikace pro místní zařízení konzoly pro správu. Místní Konzola pro správu není k dispozici jako předkonfigurované zařízení.

- Pokud si chcete koupit předem nakonfigurovaný senzor, Prohlédněte si modely dostupné v části [zařízení snímače](#sensor-appliances) a potom pokračujte v nákupu.

- Pokud si chcete koupit vlastní zařízení, Projděte si modely dostupné v části [zařízení senzorů](#sensor-appliances) a v části [Další certifikované zařízení](#additional-certified-appliances) . Po získání zařízení si můžete software stáhnout a nainstalovat.

- Pokud chcete zakoupit místní konzolu pro správu, přečtěte si informace v části věnované [zařízení místní konzoly pro správu](#on-premises-management-console-appliance) . Po získání zařízení si můžete software stáhnout a nainstalovat.

Po dokončení úkolů můžete nainstalovat software a nastavit síť.

## <a name="sensor-appliances"></a>Senzorová zařízení

Defender for IoT podporuje fyzické i virtuální nasazení.

### <a name="physical-sensors"></a>Fyzické senzory

V této části najdete přehled dostupných modelů fyzického senzoru. Můžete si koupit senzory pomocí předem nakonfigurovaného softwaru nebo senzorů nákupu, které nejsou předem nakonfigurované.

| Typ nasazení | Firemní | Enterprise | Připojení stojanu SMB| Robustní protokol SMB|
|--|--|--|--|--|
| Image | :::image type="content" source="media/how-to-prepare-your-network/corporate-hpe-proliant-dl360-v2.png" alt-text="Model podnikové úrovně."::: | :::image type="content" source="media/how-to-prepare-your-network/enterprise-and-smb-hpe-proliant-dl20-v2.png" alt-text="Model podnikové úrovně."::: | :::image type="content" source="media/how-to-prepare-your-network/enterprise-and-smb-hpe-proliant-dl20-v2.png" alt-text="Model na úrovni protokolu SMB."::: | :::image type="content" source="media/how-to-prepare-your-network/office-ruggedized.png" alt-text="Modelem robustních úrovní SMB."::: |
| Modelování | HPE DL360 | HPE DL20 | HPE DL20 | HPE EL300 |
| Porty monitorování | Až 15 RJ45 nebo 8 OPT | Až 8 RJ45 nebo 6 OPT | 4 RJ45 | Až 5 |
| Maximální šířka pásma [1](#anchortext) | 3 GB/s | 1 GB/s | 200 MB/s | 100 MB/s |
| Maximální počet chráněných zařízení | 30 000 | 15 000 | 1 000 | 800 |

Podrobnosti o dodavatelích najdete v tématu [specifikace zařízení](#appliance-specifications) .

O předkonfigurovaných senzorech: Společnost Microsoft se dostavila s šipkou, aby poskytovala předem nakonfigurované senzory. Pokud chcete koupit předkonfigurovaný senzor, šipka kontaktu na následující adrese: <hardware.sales@arrow.com>

Informace o zavedení vlastního zařízení: Projděte si podporované modely popsané tady. Po získání zařízení si přečtěte tento software v programu **Defender for IoT**  >  **Network snímače ISO**  >   .

:::image type="content" source="media/how-to-prepare-your-network/azure-defender-for-iot-sensor-download-software-screen.png" alt-text="Síťové senzory ISO.":::

<a id="anchortext">1</a> kapacita šířky pásma se může lišit v závislosti na distribuci protokolů.

### <a name="virtual-sensors"></a>Virtuální senzory

Tato část poskytuje přehled o virtuálních senzorech, které jsou k dispozici.

| Typ nasazení | Firemní | Enterprise | SMB |
|--|--|--|--|
| Maximální šířka pásma | 2,5 GB/s | 800 MB/s | 160 MB/s |
| Maximální počet chráněných zařízení | 30 000 | 10 000 | 2,500 |

## <a name="on-premises-management-console-appliance"></a>Místní zařízení konzoly pro správu

Konzola pro správu je k dispozici jako virtuální nasazení.

| Typ nasazení | Enterprise |
|--|--|
| Typ zařízení | HPE DL20, VIRTUÁLNÍ POČÍTAČ |
| Počet spravovaných senzorů | Až 300 |

Po získání místní konzoly pro správu přejdete do **programu Defender for IoT**  >  **místní konzoly pro správu**  >   a Stáhněte ISO.

:::image type="content" source="media/how-to-prepare-your-network/azure-defender-for-iot-iso-download-screen.png" alt-text="Místní Konzola pro správu.":::

## <a name="appliance-specifications"></a>Specifikace zařízení

Tato část popisuje hardwarové specifikace pro následující zařízení:

- Firemní nasazení: HPE prozávislé DL360

- Podnikové nasazení: HPE prozávislé DL20

- Nasazení protokolu SMB: HPE prozávislé DL20

- Specifikace virtuálního zařízení

## <a name="corporate-deployment-hpe-proliant-dl360"></a>Firemní nasazení: HPE prozávislé DL360

| Součást | Technické specifikace |
|--|--|
| Skříň | rackový server 1U |
| Dimenze | 42,9 x 43,46 x 70,7 (cm)/1.69 "x 17,11" x 27,83 "(in) |
| Hmotnost | Max 16,27 kg (35,86 kg) |
| Procesor | Intel Xeon stříbrné 4215 R 3,2 GHz, 11 min. cache, 8c/16T, 130 W |
| Sad | Intel C621 |
| Memory (Paměť) | 32 GB = 2 × 16 až GB 2666MT/s DDR4 ECC UDIMM |
| Storage | 6 x 1,2-TB SAS 12G Enterprise 10 000 SFF (2,5 v) v Hot-Plug pevný disk – RAID 5 |
| Síťový adaptér | Na desce: 2 × 1-GB Broadcom BCM5720<br>On-Board dvěma: karta portů iDRAC 1-GB Broadcom BCM5720<br><br>Externí: 1 x Intel Ethernet i350 QP 1-GB Server Adapter, nízký profil |
| Správa | HPE MOP Advanced |
| Přístup k zařízení | Dvě zadní USB 3,0<br>Jeden front-USB 2,0<br>Jedna interní sběrnice USB 3,0 |
| Napájení | 2 x HPE 500 W pružný slot Platinum Hot Plug konektor low Halogening Kit |
| Podpora racku | HPE 1U Gen10 SFF Easy Install železniční Kit |

### <a name="appliance-bom"></a>Kusovník zařízení

| PN | Description | Množství |
|--|--|--|
| P19766-B21 | HPE DL360 Gen10 8SFF NC technický ředitel Server | 1 |
| P19766-B21 | Evropa – lokalizace více jazyků | 1 |
| P24479-L21 | Intel Xeon-S 4215 R FIO Kit pro DL360 G10 | 1 |
| P24479-B21 | Intel Xeon-S 4215 R Kit pro DL360 Gen10 | 1 |
| P00922-B21 | HPE 16 GB 2Rx8 PC4-2933Y-R Smart Kit | 2 |
| 872479 – B21 | HPE 1,2-TB SAS 10 000 SFF SC DS – HDD | 6 |
| 811546 – B21 | HPE 1 – GbE 4-p BASE-T i350 Adapter | 1 |
| P02377-B21 | HPE Smart Hybrid capacitor w \_ 145 mm – kabel | 1 |
| 804331 – B21 | HPE Smart Array P408i – řadič SR Gen10 | 1 |
| 665240 – B21 | HPE 1-GbE 4-p FLR-T i350 Adapter | 1 |
| 871244 – B21 | Sada HPE DL360 Gen10 High Performance ventilátor | 1 |
| 865408 – B21 | HPE 500-W FS hot plug-in LH Power Resource Kit | 2 |
| 512485 – B21 | HPE MOP adv 1 – Podpora pro serverová licence 1 – rok | 1 |
| 874543 – B21 | HPE 1U Gen10 SFF Easy Install železniční Kit | 1 |

## <a name="enterprise-deployment-hpe-proliant-dl20"></a>Podnikové nasazení: HPE prozávislé DL20

| Součást | Technické specifikace |
|--|--|
| Skříň | rackový server 1U |
| Rozměry (výška × šířka × hloubka) | 4,32 x 43,46 × 38,22 cm/1.70 × 17,11 × 15,05 palce |
| Hmotnost | 7,9 kg/17.41. kg |
| Procesor | Intel Xeon E-2234, 3,6 GHz, 4C/8T, 71 W |
| Sad | Intel C242 |
| Memory (Paměť) | 2 × 16 GB duální hodnocení x8 DDR4-2666 |
| Storage | 3 × 1 TB SATA 6G midline 7,2 K SFF (2,5 in) – RAID 5 s Smart Array P408i-The SR Controller |
| Síťový adaptér | Na desce: 2 × 1 GB <br>Na desce: karta portu MOP 1 GB <br>Externí: 1 x HPE Ethernet 1-GB 4-port 366FLR Adapter |
| Správa | HPE MOP Advanced |
| Přístup k zařízení | Vpřed: 1 x USB 3,0, 1 x USB MOP Service port <br>Vzadu: 2 x USB 3,0 <br>Interní: 1 x USB 3,0 |
| Napájení | Spotřeba napájení typu Dual horké 500 W |
| Podpora racku | HPE 1U – krátká kolejnice Kit |

### <a name="appliance-bom"></a>Kusovník zařízení

| PN | Popis: horní konec | Množství |
|--|--|--|
| P06963-B21 | HPE DL20 Gen10 4SFF technický ředitel Server | 1 |
| P06963-B21 | HPE DL20 Gen10 4SFF technický ředitel Server | 1 |
| P17104-L21 | HPE DL20 Gen10 E-2234 FIO Kit | 1 |
| 879507 – B21 | HPE 16 GB 2Rx8 PC4-2666V-E STND Kit | 2 |
| 655710 – B21 | HPE 1 TB SATA 7,2 K SFF SC DS HDD | 3 |
| P06667-B21 | HPE DL20 Gen10 x8x16 FLOM riser Kit | 1 |
| 665240 – B21 | HPE Ethernet 1 – GB 4 – port 366FLR Adapter | 1 |
| 782961 – B21 | HPE 12-W – baterie inteligentního úložiště | 1 |
| 869081 – B21 | HPE Smart Array P408i – kontroler SR G10 | 1 |
| 865408 – B21 | HPE 500-W FS hot plug-in LH Power Resource Kit | 2 |
| 512485 – B21 | HPE MOP adv 1 – Podpora pro serverová licence 1 – rok | 1 |
| P06722-B21 | HPE DL20 Gen10 RPS Enable FIO Kit | 1 |
| 775612 – B21 | HPE 1U – krátká kolejnice Kit | 1 |

## <a name="smb-deployment-hpe-proliant-dl20"></a>Nasazení protokolu SMB: HPE prozávislé DL20

| Součást | Technické specifikace |
|--|--|
| Skříň | rackový server 1U |
| Rozměry (výška × šířka × hloubka) | 4,32 x 43,46 × 38,22 cm/1.70 × 17,11 × 15,05 palce |
| Hmotnost | 7,88 kg/17.37. kg |
| Procesor | Intel Xeon E-2224, 3,4 GHz, 4C, 71 W |
| Sad | Intel C242 |
| Memory (Paměť) | 1 × 8 GB duální hodnocení x8 DDR4-2666 |
| Storage | 2 × 1 TB SATA 6G midline 7,2 K SFF (2,5 in) – RAID 1 s inteligentním polem P208i-a |
| Síťový adaptér | Na desce: 2 × 1 GB <br>Na desce: karta portu MOP 1 GB <br>Externí: 1 x HPE Ethernet 1-GB 4-port 366FLR Adapter |
| Správa | HPE MOP Advanced |
| Přístup k zařízení | Vpřed: 1 x USB 3,0, 1 x USB MOP Service port <br>Vzadu: 2 x USB 3,0 <br>Interní: 1 x USB 3,0 |
| Napájení | Přívod napájení hot plug 290 W |
| Podpora racku | HPE 1U – krátká kolejnice Kit |

### <a name="appliance-bom"></a>Kusovník zařízení

| PN | Description | Množství |
|--|--|--|
| P06961-B21 | HPE DL20 Gen10 NHP 2LFF technický ředitel Server | 1 |
| P06961-B21 | HPE DL20 Gen10 NHP 2LFF technický ředitel Server | 1 |
| P17102-L21 | HPE DL20 Gen10 E-2224 FIO Kit | 1 |
| 879505 – B21 | HPE 8 GB 1Rx8 PC4-2666V-E STND Kit | 1 |
| 801882 – B21 | HPE 1 TB SATA 7,2 K LFF RW HDD | 2 |
| P06667-B21 | HPE DL20 Gen10 x8x16 FLOM riser Kit | 1 |
| 665240 – B21 | HPE Ethernet 1 – GB 4 – port 366FLR Adapter | 1 |
| 869079 – B21 | HPE Smart Array E208i – kontroler SR G10 | 1 |
| P21649-B21 | HPE DL20 Gen10 plat 290 W FIO PSU Kit | 1 |
| P06683-B21 | HPE DL20 Gen10 M. 2 SATA/LFF AROC Cable Kit | 1 |
| 512485 – B21 | HPE MOP adv 1 – Podpora pro serverová licence 1 – rok | 1 |
| 775612 – B21 | HPE 1U – krátká kolejnice Kit | 1 |

## <a name="smb-rugged-hpe-edgeline-el300"></a>Protokol SMB robustní: HPE Edgeline EL300

| Součást | Technické specifikace |
|--|--|
| Stavebnictví | Aluminum, Fanless & design pro prach – návrh |
| Rozměry (výška × šířka × hloubka) | 200.5 mm (7,9 ") na výšku, 232mm (9,14") na šířku 100mm (3,9) hluboko |
| Hmotnost | 4,91 KG (10,83 kg) |
| Procesor | Intel Core i7-8650U (1,9 GHz/4-Core/15W) |
| Sad | Rozbočovač řadiče platformy Intel® Q170 |
| Memory (Paměť) | 8 GB DDR4 2133MHz pro velké teploty SODIMM |
| Storage | 128 GB 3ME3 pro velké teploty mSATA SSD |
| Síťový adaptér | porty gigabitového Ethernetu 6x podle Intel® i219 |
| Přístup k zařízení  | 4 USBs: 2 přední strany; 2 vzadu; 1 interní |
| Adaptér napájení | 250V/10A |
| Připojení | Montážní Kit, DIN kolejnice |
| Provozní teplota | 0C na + 70C  |
| Vlhkost | 10% ~ 90%, nekondenzující |
| Vibrační | 0,3 Grms 10Hz pro 300Hz, 15 minut na osu – DIN kolejnice   |
| Náraz | 10ms 10G, poloviční sinus, tři pro každou osu. (Kladné & záporné Pulse) – DIN kolejnice |

### <a name="appliance-bom"></a>Kusovník zařízení
| Produkt | Description |
|--|--|
| P25828-B21 | HPE Edgeline EL300 v2 – sblížený hraniční systém |
| P25828-B21 B19 | HPE EL300 v2 – sblížený hraniční systém |
| P25833-B21 | Intel Core i7-8650U (1,9 GHz/4 – Core/15W) FIO Basic Processor Kit pro HPE Edgeline EL300 |
| P09176-B21 | HPE Edgeline 8 GB (1x8GB) Dual Rank x8 DDR4-2666 SODIMM WT CAS-19-19-19 registered Memory FIO Kit |
| P09188-B21 | HPE Edgeline 256GB SATA 6G čtení náročné M. 2 2242 3yr Wty celosvětová jednotka SSD |
| P04054-B21 | HPE Edgeline EL300 SFF až M. 2 povolení sady |
| P08120-B21 | HPE Edgeline EL300 12VDC FIO pro přenosovou desku |
| P08641-B21 | Zdroj napájení HPE Edgeline EL300 80W 12VDC |
| AF564A | HPE C13-SI-32 IL 250V 10Amp 1.83 m napájecí šňůra |
| P25835-B21 | HPE EL300 v2 FIO Board |
| R1P49AAE | HPE EL300 iSM ADV 3yr nepřetržitě Sup_Upd E-LTU |
| P08018 – B21 – nepovinné | HPE Edgeline EL300 nízká profilová sada závorek  |
| P08019 – B21 – nepovinné | HPE Edgeline EL300 DIN kolejnice Mount Kit |
| P08020 – B21 – nepovinné | HPE Edgeline EL300 Wall Mount Kit |
| P03456 – B21 – nepovinné | HPE Edgeline 10 GbE LOM 4 – Dceřiná karta portu TSN FIO |

## <a name="virtual-appliance-specifications"></a>Specifikace virtuálního zařízení

### <a name="sensors"></a>Čidl

| Typ | Firemní | Enterprise | SMB |
|--|--|--|--|
| Virtuální procesory | 32 | 8 | 4 |
| Memory (Paměť) | 32 GB | 32 GB | 8 GB |
| Storage | 5,6 TB | 1,8 TB | 500 GB |

### <a name="on-premises-management-console-appliance"></a>Místní zařízení konzoly pro správu

| Typ | Enterprise |
|--|--|
| Description | Virtuální zařízení pro podnikové typy nasazení |
| Virtuální procesory | 8 |
| Memory (Paměť) | 32 GB |
| Storage | 1,8 TB |

Podporované hypervisory: VMware ESXi verze 5,0 a novější, Hyper-V

## <a name="additional-certified-appliances"></a>Další certifikovaná zařízení

Tato část podrobně popisuje další zařízení, která byla certifikována společností Microsoft, ale nejsou nabízena jako předkonfigurovaná zařízení.

| Typ nasazení | Enterprise |
|--|--|
| Image | :::image type="content" source="media/how-to-prepare-your-network/deployment-type-enterprise-for-azure-defender-for-iot-v2.png" alt-text="Typ podnikového nasazení."::: |
| Modelování | Dell PowerEdge R340 XL |
| Porty monitorování | Až devět RJ45 nebo šest OPT |
| Maximální šířka pásma [1](#anchortext2)| 1 GB/s |
| Maximální počet chráněných zařízení | 10 000 |

<a id="anchortext2">Jedna</a> Kapacita šířky pásma se může lišit v závislosti na distribuci protokolů.

Po zakoupení zařízení si přečtěte tento software v **programu Defender for IoT**  >  **Network snímače ISO**  >   .

:::image type="content" source="media/how-to-prepare-your-network/azure-defender-for-iot-sensor-download-software-screen.png" alt-text="Síťové senzory ISO.":::

## <a name="enterprise-deployment-dell-poweredge-r340-xl"></a>Podnikové nasazení: Dell PowerEdge R340 XL

| Součást | Technické specifikace |
|--|--|
| Skříň | rackový server 1U
| Dimenze | 42,8 x 434,0 x 596 (mm)/1,67 "x 17,09" x 23,5 "(in) |
| Hmotnost | Maximální 29,98 kg/13.6 kg |
| Procesor | Intel Xeon E-2144G 3,6 GHz, 8 min cache, 4C/8T, Turbo (71 W) |
| Sad | Intel C246 |
| Memory (Paměť) | 32 GB = 2 × 16 až GB 2666MT/s DDR4 ECC UDIMM |
| Storage | 3 × 2 – TB 7,2 K ot./min. SATA 6 až 2 GB 512n 3,5-in Hot-Plug hard disk-RAID 5 |
| Síťový adaptér | Na desce: 2 × 1-GB Broadcom BCM5720<br>On-Board dvěma: karta portů iDRAC 1-GB Broadcom BCM5720 <br><br>Externí: 1 x Intel Ethernet i350 QP 1-GB Server Adapter, nízký profil |
| Správa | iDRAC devět Enterprise |
| Přístup k zařízení | Dvě zadní USB 3,0 <br> Jeden front-USB 3,0 |
| Napájení | Spotřeba napájení typu Dual horké 350 W |
| Podpora racku | ReadyRails II klouzavé kolejnice pro připojení nástroje bez nástrojů v sestavách na 4 koncových skříních se čtvercovými nebo nezávitými otvory nebo nástrojem připojenými k nástroji ve 4-post vláknech s podporou pro správu kabelů bez nástrojů. |

## <a name="dell-r340-bom"></a>Dell R340 BOM

:::image type="content" source="media/how-to-prepare-your-network/enterprise-deployment-for-azure-defender-for-iot-dell-r340-bom.png" alt-text="Dell R340 BOM.":::

## <a name="next-steps"></a>Další kroky

[O instalaci Azure Defenderu pro IoT](how-to-install-software.md)

[O programu Azure Defender pro síť IoT](how-to-set-up-your-network.md)


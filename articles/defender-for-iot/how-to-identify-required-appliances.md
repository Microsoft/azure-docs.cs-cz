---
title: Identifikace požadovaných zařízení
description: Seznamte se s hardwarem a virtuálním zařízením pro certifikovaného Defenderu pro senzory IoT a místní konzolu pro správu.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/21/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: b22f880eee1b691b6b50b8151a64a2d501b891b3
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/31/2020
ms.locfileid: "97838976"
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

| Typ nasazení | Firemní | Enterprise | SMB |
|--|--|--|--|
| Image | :::image type="content" source="media/how-to-prepare-your-network/corporate-hpe-proliant-dl360-v2.png" alt-text="Model podnikové úrovně."::: | :::image type="content" source="media/how-to-prepare-your-network/enterprise-and-smb-hpe-proliant-dl20-v2.png" alt-text="Model podnikové úrovně."::: | :::image type="content" source="media/how-to-prepare-your-network/enterprise-and-smb-hpe-proliant-dl20-v2.png" alt-text="Model na úrovni protokolu SMB."::: |
| Model | HPE DL360 | HPE DL20 | HPE DL20 |
| Porty monitorování | Až 15 RJ45 nebo 8 OPT | Až 8 RJ45 nebo 6 OPT | 4 RJ45 |
| Maximální šířka pásma [1](#anchortext) | 3 GB za sekundu | 1 GB za sekundu | 200 MB za sekundu |
| Maximální počet chráněných zařízení | 30 000 | 15 000 | 1 000 |

Podrobnosti o dodavatelích najdete v tématu [specifikace zařízení](#appliance-specifications) .

O předkonfigurovaných senzorech: Společnost Microsoft se dostavila s šipkou, aby poskytovala předem nakonfigurované senzory. Pokud chcete koupit předkonfigurovaný senzor, šipka kontaktu na následující adrese: <hardware.sales@arrow.com>

Informace o tom, jak se přinášejí vlastní zařízení: Projděte si podporované modely popsané tady. Po získání zařízení si přečtěte tento software v programu **Defender for IoT**  >  **Network snímače ISO**  >   .

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

| Komponenta | Technické specifikace |
|--|--|
| Skříň | rackový server 1U |
| Dimenze | 42,9 x 43,46 x 70,7 (cm)/1.69 "x 17,11" x 27,83 "(in) |
| Hmotnost | Max 16,27 kg (35,86 kg) |
| Procesor | Intel Xeon stříbrné 4215 R 3,2 GHz, 11 min. cache, 8c/16T, 130 W |
| Sad | Intel C621 |
| Paměť | 32 GB = 2 × 16 až GB 2666MT/s DDR4 ECC UDIMM |
| Úložiště | 6 x 1,2-TB SAS 12G Enterprise 10 000 SFF (2,5 v) v Hot-Plug pevný disk – RAID 5 |
| Síťový adaptér | Na desce: 2 × 1-GB Broadcom BCM5720<br>On-Board dvěma: karta portů iDRAC 1-GB Broadcom BCM5720<br><br>Externí: 1 x Intel Ethernet i350 QP 1-GB Server Adapter, nízký profil |
| Správa | HPE MOP Advanced |
| Přístup k zařízení | Dvě zadní USB 3,0<br>Jeden front-USB 2,0<br>Jedna interní sběrnice USB 3,0 |
| Napájení | 2 x HPE 500 W pružný slot Platinum Hot Plug konektor low Halogening Kit |
| Podpora racku | HPE 1U Gen10 SFF Easy Install železniční Kit |

### <a name="appliance-bom"></a>Kusovník zařízení

| PN | Popis | Množství |
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

| Komponenta | Technické specifikace |
|--|--|
| Skříň | rackový server 1U |
| Rozměry (výška × šířka × hloubka) | 4,32 x 43,46 × 38,22 cm/1.70 × 17,11 × 15,05 palce |
| Hmotnost | 7,9 kg/17.41. kg |
| Procesor | Intel Xeon E-2234, 3,6 GHz, 4C/8T, 71 W |
| Sad | Intel C242 |
| Paměť | 2 × 16 GB duální hodnocení x8 DDR4-2666 |
| Úložiště | 3 × 1 TB SATA 6G midline 7,2 K SFF (2,5 in) – RAID 5 s Smart Array P408i-The SR Controller |
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

| Komponenta | Technické specifikace |
|--|--|
| Skříň | rackový server 1U |
| Rozměry (výška × šířka × hloubka) | 4,32 x 43,46 × 38,22 cm/1.70 × 17,11 × 15,05 palce |
| Hmotnost | 7,88 kg/17.37. kg |
| Procesor | Intel Xeon E-2224, 3,4 GHz, 4C, 71 W |
| Sad | Intel C242 |
| Paměť | 1 × 8 GB duální hodnocení x8 DDR4-2666 |
| Úložiště | 2 × 1 TB SATA 6G midline 7,2 K SFF (2,5 in) – RAID 1 s inteligentním polem P208i-a |
| Síťový adaptér | Na desce: 2 × 1 GB <br>Na desce: karta portu MOP 1 GB <br>Externí: 1 x HPE Ethernet 1-GB 4-port 366FLR Adapter |
| Správa | HPE MOP Advanced |
| Přístup k zařízení | Vpřed: 1 x USB 3,0, 1 x USB MOP Service port <br>Vzadu: 2 x USB 3,0 <br>Interní: 1 x USB 3,0 |
| Napájení | Přívod napájení hot plug 290 W |
| Podpora racku | HPE 1U – krátká kolejnice Kit |

### <a name="appliance-bom"></a>Kusovník zařízení

| PN | Popis | Množství |
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

## <a name="virtual-appliance-specifications"></a>Specifikace virtuálního zařízení

### <a name="sensors"></a>Čidl

| Typ | Firemní | Enterprise | SMB |
|--|--|--|--|
| Virtuální procesory | 32 | 8 | 4 |
| Paměť | 32 GB | 32 GB | 8 GB |
| Úložiště | 5,6 TB | 1,8 TB | 500 GB |

### <a name="on-premises-management-console-appliance"></a>Místní zařízení konzoly pro správu

| Typ | Enterprise |
|--|--|
| Popis | Virtuální zařízení pro podnikové typy nasazení |
| Virtuální procesory | 8 |
| Paměť | 32 GB |
| Úložiště | 1,8 TB |

Podporované hypervisory: VMware ESXi verze 5,0 a novější, Hyper-V

## <a name="additional-certified-appliances"></a>Další certifikovaná zařízení

Tato část podrobně popisuje další zařízení, která byla certifikována společností Microsoft, ale nejsou nabízena jako předkonfigurovaná zařízení.

| Typ nasazení | Enterprise |
|--|--|
| Image | :::image type="content" source="media/how-to-prepare-your-network/deployment-type-enterprise-for-azure-defender-for-iot-v2.png" alt-text="Typ podnikového nasazení."::: |
| Model | Dell PowerEdge R340 XL |
| Porty monitorování | Až devět RJ45 nebo šest OPT |
| Maximální šířka pásma [1](#anchortext2)| 1G MB/s |
| Maximální počet chráněných zařízení | 10 000 |

<a id="anchortext2">Jedna</a> Kapacita šířky pásma se může lišit v závislosti na distribuci protokolů.

Po zakoupení zařízení si přečtěte tento software v **programu Defender for IoT**  >  **Network snímače ISO**  >   .

:::image type="content" source="media/how-to-prepare-your-network/azure-defender-for-iot-sensor-download-software-screen.png" alt-text="Síťové senzory ISO.":::

## <a name="enterprise-deployment-dell-poweredge-r340-xl"></a>Podnikové nasazení: Dell PowerEdge R340 XL

| Komponenta | Technické specifikace |
|--|--|
| Skříň | rackový server 1U |
| Dimenze | 42,8 x 434,0 x 596 (mm)/1,67 "x 17,09" x 23,5 "(in) |
| Hmotnost | Maximální 29,98 kg/13.6 kg |
| Procesor | Intel Xeon E-2144G 3,6 GHz, 8 min cache, 4C/8T, Turbo (71 W) |
| Sad | Intel C246 |
| Paměť | 32 GB = 2 × 16 až GB 2666MT/s DDR4 ECC UDIMM |
| Úložiště | 3 × 2 – TB 7,2 K ot./min. SATA 6 až 2 GB 512n 3,5-in Hot-Plug hard disk-RAID 5 |
| Síťový adaptér | Na desce: 2 × 1-GB Broadcom BCM5720<br>On-Board dvěma: karta portů iDRAC 1-GB Broadcom BCM5720 <br><br>Externí: 1 x Intel Ethernet i350 QP 1-GB Server Adapter, nízký profil |
| Správa | iDRAC devět Enterprise |
| Přístup k zařízení | Dvě zadní USB 3,0 <br> Jeden front-USB 3,0 |
| Napájení | Spotřeba napájení typu Dual horké 350 W |
| Podpora racku | ReadyRails II klouzavé kolejnice pro připojení nástroje bez nástrojů v sestavách na 4 koncových skříních se čtvercovými nebo nezávitými otvory nebo nástrojem připojenými k nástroji ve 4-post vláknech s podporou pro správu kabelů bez nástrojů. |

## <a name="dell-r340-bom"></a>Dell R340 BOM

:::image type="content" source="media/how-to-prepare-your-network/enterprise-deployment-for-azure-defender-for-iot-dell-r340-bom.png" alt-text="Dell R340 BOM.":::

## <a name="smb-deployment-neousys-nuvo-5006lp"></a>Nasazení SMB: Neousys NUVO – 5006LP

| Komponenta | Technické specifikace |
|--|--|
| Stavebnictví | Aluminum, Fanless a design pro prach – návrh |
| Dimenze | 240 mm (W) x 225 mm (D) × 77 mm (H) |
| Hmotnost | 3,1 kg (včetně procesoru, paměti a pevného disku) |
| Procesor | Intel Core i5-6500TE (6 min cache, až 3,30 GHz) S1151 |
| Sad | Centrum řadičů platformy Intel Q170 |
| Paměť | 8 GB DDR4 2133 MHz (SODIMM) s velkou teplotou |
| Úložiště | 128 GB mSATA SSD pro 3ME3 s velkou teplotou |
| Síťový adaptér | porty gigabitového Ethernetu 6x od Intel i219 |
| Přístup k zařízení | 4 USBs: dvě přední strany, dvě zadní kola, jedna interní |
| Adaptér napájení | 120/240VAC-20VDC/6A |
| Připojení | Montážní Kit, DIN kolejnice |
| Provozní teplota | \-25 °C ~ 70 OC |
| Teplota úložiště | \-40 OC ~ 85 OC |
| Vlhkost | 10% ~ 90%, nekondenzující |
| Vibrační | Provozní, 5 Grms, 5-500 Hz, 3 osy <br>(w/SSD, podle IEC60068-2-64) |
| Náraz | Provozní, 50 Grms, poloviční kosinus 11 – doba trvání (w/SSD, podle IEC60068-2 – 27) |
| SOFTWARE | CE/FCC Class A, podle EN 55022, EN 55024 a EN 55032 |

## <a name="next-steps"></a>Další kroky

[O instalaci Azure Defenderu pro IoT](how-to-install-software.md)

[O programu Azure Defender pro síť IoT](how-to-set-up-your-network.md)

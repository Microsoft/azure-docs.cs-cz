---
title: Image Red Hat Enterprise Linux v Azure | Dokumenty společnosti Microsoft
description: Informace o inacích Red Hat Enterprise Linux v Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: 40a715ea27be161f09dea59e57799b08406e8ca7
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393761"
---
# <a name="red-hat-enterprise-linux-rhel-images-available-in-azure"></a>Image Red Hat Enterprise Linux (RHEL) dostupné v Azure
Azure nabízí celou řadu ibi RHEL pro různé případy použití.

> [!NOTE]
> Všechny image RHEL jsou dostupné v cloudech Azure public a Azure Government. Nejsou k dispozici v cloudech Azure China.

## <a name="list-of-rhel-images"></a>Seznam obrázků RHEL
Toto je seznam imitek RHEL dostupných v Azure. Pokud není uvedeno jinak, všechny obrázky jsou rozděleny do LVM a připojeny k běžným úložištím RHEL (nikoli EUS, nikoli E4S). Pro obecné použití jsou v současné době k dispozici následující obrázky:

Nabídka| Skladová jednotka (SKU) | Dělení | Zřizování | Poznámky
:----|:----|:-------------|:-------------|:-----
RHEL          | 6.7      | Syrové    | Linuxový agent |
|             | 6.8      | Syrové    | Linuxový agent |
|             | 6.9      | Syrové    | Linuxový agent |
|             | 6.10     | Syrové    | Linuxový agent |
|             | 7-SUROVÝ    | Syrové    | Linuxový agent | RHEL 7.x rodina obrazů. <br> Ve výchozím nastavení je připojeno k běžným úložištím (nikoli eus).
|             | 7-LVM    | Lvm    | Linuxový agent | RHEL 7.x rodina obrazů. <br> Ve výchozím nastavení je připojeno k běžným úložištím (nikoli eus). Pokud hledáte standardní image RHEL k nasazení, použijte tuto sadu bitových kopií a /nebo jeho protějšek generace 2.
|             | 7lvm-gen2| Lvm    | Linuxový agent | Generace 2, RHEL 7.x rodina obrazů. <br> Ve výchozím nastavení je připojeno k běžným úložištím (nikoli eus). Pokud hledáte standardní image RHEL k nasazení, použijte tuto sadu bitových kopií a/nebo její protějšek generace 1.
|             | 7-RAW-CI | RAW-CI | cloud-init  | RHEL 7.x rodina obrazů. <br> Ve výchozím nastavení je připojeno k běžným úložištím (nikoli eus).
|             | 7.2      | Syrové    | Linuxový agent |
|             | 7.3      | Syrové    | Linuxový agent |
|             | 7.4      | Syrové    | Linuxový agent | K úložištím EUS je standardně připojenod dubna 2019.
|             | 74-gen2  | Syrové    | Linuxový agent | Ve výchozím nastavení připojeno k úložištím EUS.
|             | 7,5      | Syrové    | Linuxový agent | K úložištím EUS je standardně připojeno k červnu 2019.
|             | 75-gen2  | Syrové    | Linuxový agent | Ve výchozím nastavení připojeno k úložištím EUS.
|             | 7.6      | Syrové    | Linuxový agent | K úložištím EUS je standardně připojenod května 2019.
|             | 76-gen2  | Syrové    | Linuxový agent | Ve výchozím nastavení připojeno k úložištím EUS.
|             | 7.7      | Lvm    | Linuxový agent | Ve výchozím nastavení připojeno k úložištím EUS.
|             | 8        | Lvm    | Linuxový agent | Rhel 8.x rodina obrázků
|             | 8 gen2   | Lvm    | Linuxový agent | Hyper-V Generace 2 - RHEL 8.x rodina obrazů.
RHEL-SAP      | 7.4      | Lvm    | Linuxový agent | RHEL 7.4 pro SAP HANA a obchodní aplikace. Připojeno k úložištím E4S, bude účtovat prémii za SAP a RHEL, stejně jako základní výpočetní poplatek.
|             | 74sap-gen2| Lvm    | Linuxový agent | RHEL 7.4 pro SAP HANA a obchodní aplikace. Generace 2 image. Připojeno k úložištím E4S, bude účtovat prémii za SAP a RHEL, stejně jako základní výpočetní poplatek.
|             | 7,5       | Lvm    | Linuxový agent | RHEL 7.5 pro SAP HANA a obchodní aplikace. Připojeno k úložištím E4S, bude účtovat prémii za SAP a RHEL, stejně jako základní výpočetní poplatek.
|             | 75sap-gen2| Lvm    | Linuxový agent | RHEL 7.5 pro SAP HANA a obchodní aplikace. Generace 2 image. Připojeno k úložištím E4S, bude účtovat prémii za SAP a RHEL, stejně jako základní výpočetní poplatek.
|             | 7.6       | Lvm    | Linuxový agent | RHEL 7.6 pro SAP HANA a obchodní aplikace. Připojeno k úložištím E4S, bude účtovat prémii za SAP a RHEL, stejně jako základní výpočetní poplatek.
|             | 76sap-gen2| Lvm    | Linuxový agent | RHEL 7.6 pro SAP HANA a obchodní aplikace. Generace 2 image. Připojeno k úložištím E4S, bude účtovat prémii za SAP a RHEL, stejně jako základní výpočetní poplatek.
|             | 7.7       | Lvm    | Linuxový agent | RHEL 7.7 pro SAP HANA a obchodní aplikace. Připojeno k úložištím E4S, bude účtovat prémii za SAP a RHEL, stejně jako základní výpočetní poplatek.
RHEL-SAP-HANA | 6.7       | Syrové    | Linuxový agent | RHEL 6.7 pro SAP HANA. Zastaralé ve prospěch obrazů RHEL-SAP.
|             | 7.2       | Lvm    | Linuxový agent | RHEL 7.2 pro SAP HANA. Zastaralé ve prospěch obrazů RHEL-SAP.
|             | 7.3       | Lvm    | Linuxový agent | RHEL 7.3 pro SAP HANA. Zastaralé ve prospěch obrazů RHEL-SAP.
RHEL-SAP-APPS | 6.8       | Syrové    | Linuxový agent | RHEL 6.8 pro SAP Business Applications. Zastaralé ve prospěch obrazů RHEL-SAP.
|             | 7.3       | Lvm    | Linuxový agent | RHEL 7.3 pro SAP Business Applications. Zastaralé ve prospěch obrazů RHEL-SAP.
RÝM-HA       | 7.4       | Lvm    | Linuxový agent | RHEL 7.4 s doplňkem HA. Bude účtovat prémii za HA a RHEL nad základní výpočetní poplatek.
|             | 7,5       | Lvm    | Linuxový agent | RHEL 7.5 s doplňkem HA. Bude účtovat prémii za HA a RHEL nad základní výpočetní poplatek.
|             | 7.6       | Lvm    | Linuxový agent | RHEL 7.6 s doplňkem HA. Bude účtovat prémii za HA a RHEL nad základní výpočetní poplatek.
RHEL-SAP-HA   | 7.4          | Lvm    | Linuxový agent | RHEL 7.4 pro SAP s HA a aktualizačními službami. Připojeno k úložištím E4S. Bude účtovat prémii za úložiště SAP a HA, stejně jako RHEL, navíc k základním výpočetním poplatkům.
|             | 74sapha-gen2 | Lvm    | Linuxový agent | RHEL 7.4 pro SAP s HA a aktualizačními službami. Generace 2 image. Připojeno k úložištím E4S. Bude účtovat prémii za úložiště SAP a HA, stejně jako RHEL, navíc k základním výpočetním poplatkům.
|             | 7,5          | Lvm    | Linuxový agent | RHEL 7.5 pro SAP s HA a aktualizačními službami. Připojeno k úložištím E4S. Bude účtovat prémii za úložiště SAP a HA, stejně jako RHEL, navíc k základním výpočetním poplatkům.
|             | 7.6          | Lvm    | Linuxový agent | RHEL 7.6 pro SAP s HA a aktualizačními službami. Připojeno k úložištím E4S. Bude účtovat prémii za úložiště SAP a HA, stejně jako RHEL, navíc k základním výpočetním poplatkům.
|             | 76sapha-gen2 | Lvm    | Linuxový agent | RHEL 7.6 pro SAP s HA a aktualizačními službami. Generace 2 image. Připojeno k úložištím E4S. Bude účtovat prémii za úložiště SAP a HA, stejně jako RHEL, navíc k základním výpočetním poplatkům.
|             | 7.7          | Lvm    | Linuxový agent | RHEL 7.7 pro SAP s HA a aktualizačními službami. Připojeno k úložištím E4S. Bude účtovat prémii za úložiště SAP a HA, stejně jako RHEL, navíc k základním výpočetním poplatkům.
|             | 77sapha-gen2 | Lvm    | Linuxový agent | RHEL 7.7 pro SAP s HA a aktualizačními službami. Generace 2 image. Připojeno k úložištím E4S. Bude účtovat prémii za úložiště SAP a HA, stejně jako RHEL, navíc k základním výpočetním poplatkům.
rhel-byos     |rým-lvm74| Lvm    | Linuxový agent | Obrázky RHEL 7.4 BYOS, které nejsou připojeny k žádnému zdroji aktualizací, nebudou účtovat prémii RHEL.
|             |rým-lvm75| Lvm    | Linuxový agent | Obrázky RHEL 7.5 BYOS, které nejsou připojeny k žádnému zdroji aktualizací, nebudou účtovat prémii RHEL.
|             |rým-lvm76| Lvm    | Linuxový agent | Obrázky RHEL 7.6 BYOS, které nejsou připojeny k žádnému zdroji aktualizací, nebudou účtovat prémii RHEL.
|             |rým-lvm77| Lvm    | Linuxový agent | Obrázky RHEL 7.7 BYOS, které nejsou připojeny k žádnému zdroji aktualizací, nebudou účtovat prémii RHEL.
|             |rým-lvm8 | Lvm    | Linuxový agent | Obrázky RHEL 8 BYOS (dílčí verze RHEL je zobrazena v hodnotě verze obrázku), která není připojena k žádnému zdroji aktualizací, nebude účtovat prémii RHEL.

> [!NOTE]
> Nabídka produktů RHEL-SAP-HANA je považována za konec životnosti red hatu. Stávající nasazení budou i nadále fungovat normálně, ale Red Hat doporučuje zákazníkům migrovat z reionací RHEL-SAP-HANA do bitových kopií RHEL-SAP-HA, které zahrnují úložiště SAP HANA a doplněk HA. Více informací o cloudových nabídkách SAP společnosti Red Hat naleznete [zde](https://access.redhat.com/articles/3751271).

## <a name="next-steps"></a>Další kroky
* Další informace o [inacích Red Hatu v Azure](./redhat-images.md).
* Další informace o [infrastruktuře aktualizací Red Hat](./redhat-rhui.md).
* Další informace o [nabídce RHEL BYOS](./byos.md).
* Informace o zásadách podpory Red Hat pro všechny verze RHEL naleznete na stránce [životního cyklu Red Hat Enterprise Linux.](https://access.redhat.com/support/policy/updates/errata)

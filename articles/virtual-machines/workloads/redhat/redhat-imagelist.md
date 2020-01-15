---
title: Red Hat Enterprise Linux imagí v Azure | Microsoft Docs
description: Přečtěte si o Red Hat Enterprise Linuxch imagí v Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/18/2019
ms.author: alsin
ms.openlocfilehash: 32df17ffed400af80eadadbdeaafbaa1e3e1dbce
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2020
ms.locfileid: "75941704"
---
# <a name="red-hat-enterprise-linux-rhel-images-available-in-azure"></a>Image Red Hat Enterprise Linux (RHEL) dostupné v Azure
Azure nabízí celou řadu imagí RHEL pro různé případy použití.

## <a name="list-of-rhel-images"></a>Seznam imagí RHEL
Toto je seznam imagí RHEL dostupných v Azure. Pokud není uvedeno jinak, všechny image jsou LVM rozdělené a připojené k běžným RHEL úložištím (ne EUS, ne E4S). Pro obecné použití jsou aktuálně k dispozici následující Image:

Nabídka| Skladová položka | Dělení | Zřizování | Poznámky
:----|:----|:-------------|:-------------|:-----
RHEL          | 6.7      | ZÍSKÁNÍ    | Linuxový agent |
|             | 6.8      | ZÍSKÁNÍ    | Linuxový agent |
|             | 6.9      | ZÍSKÁNÍ    | Linuxový agent |
|             | 6.10     | ZÍSKÁNÍ    | Linuxový agent |
|             | 7 – RAW    | ZÍSKÁNÍ    | Linuxový agent | RHEL 7. x rodina imagí. <br> Ve výchozím nastavení připojené k pravidelným úložištím (ne EUS).
|             | 7 – LVM    | LVM    | Linuxový agent | RHEL 7. x rodina imagí. <br> Ve výchozím nastavení připojené k pravidelným úložištím (ne EUS).
|             | 7-RAW-CI | RAW-CI | Cloud-init  | RHEL 7. x rodina imagí. <br> Ve výchozím nastavení připojené k pravidelným úložištím (ne EUS).
|             | 7.2      | ZÍSKÁNÍ    | Linuxový agent |
|             | 7.3      | ZÍSKÁNÍ    | Linuxový agent |
|             | 7.4      | ZÍSKÁNÍ    | Linuxový agent | Připojí se k úložištím EUS ve výchozím nastavení od dubna 2019.
|             | 7.5      | ZÍSKÁNÍ    | Linuxový agent | Připojeno k úložištím EUS ve výchozím nastavení od června 2019.
|             | 7.6      | ZÍSKÁNÍ    | Linuxový agent | Připojeno k úložištím EUS ve výchozím nastavení jako květen 2019.
|             | 7.7      | LVM    | Linuxový agent | Ve výchozím nastavení připojené k úložištím EUS.
|             | 8        | LVM    | Linuxový agent | RHEL 8. x řada imagí
|             | 8 – Gen2   | LVM    | Linuxový agent | Hyper-V Generation 2 – RHEL 8. x řada imagí.
RHEL-SAP      | 7.4      | LVM    | Linuxový agent | RHEL 7,4 pro SAP HANA a obchodní aplikace. Připojené k úložištím E4S budou účtovat prémii za SAP a RHEL a také základní výpočetní poplatek.
|             | 7.5      | LVM    | Linuxový agent | RHEL 7,5 pro SAP HANA a obchodní aplikace. Připojené k úložištím E4S budou účtovat prémii za SAP a RHEL a také základní výpočetní poplatek.
|             | 7.6      | LVM    | Linuxový agent | RHEL 7,5 pro SAP HANA a obchodní aplikace. Připojené k úložištím E4S budou účtovat prémii za SAP a RHEL a také základní výpočetní poplatek.
|             | 7.7      | LVM    | Linuxový agent | RHEL 7,5 pro SAP HANA a obchodní aplikace. Připojené k úložištím E4S budou účtovat prémii za SAP a RHEL a také základní výpočetní poplatek.
RHEL-SAP-HANA | 6.7      | ZÍSKÁNÍ    | Linuxový agent | RHEL 6,7 pro SAP HANA. Zastaralé ve prospěch imagí RHEL-SAP.
|             | 7.2      | LVM    | Linuxový agent | RHEL 7,2 pro SAP HANA. Zastaralé ve prospěch imagí RHEL-SAP.
|             | 7.3      | LVM    | Linuxový agent | RHEL 7,3 pro SAP HANA. Zastaralé ve prospěch imagí RHEL-SAP.
RHEL-SAP-APPS | 6.8      | ZÍSKÁNÍ    | Linuxový agent | RHEL 6,8 pro SAP Business Applications. Zastaralé ve prospěch imagí RHEL-SAP.
|             | 7.3      | LVM    | Linuxový agent | RHEL 7,3 pro SAP Business Applications. Zastaralé ve prospěch imagí RHEL-SAP.
RHEL-HA       | 7.4      | LVM    | Linuxový agent | RHEL 7,4 s doplňkem HA. Poplatky za HA a RHEL se účtují za základní výpočetní poplatek.
|             | 7.5      | LVM    | Linuxový agent | RHEL 7,5 s doplňkem HA. Poplatky za HA a RHEL se účtují za základní výpočetní poplatek.
|             | 7.6      | LVM    | Linuxový agent | RHEL 7,6 s doplňkem HA. Poplatky za HA a RHEL se účtují za základní výpočetní poplatek.
RHEL-SAP-HA   | 7.4      | LVM    | Linuxový agent | RHEL 7,4 pro SAP s doplňkem HA. Připojeno k úložištím E4S. Za základní výpočetní poplatky účtujeme za úložiště SAP a HA a také RHEL.
|             | 7.5      | LVM    | Linuxový agent | RHEL 7,5 pro SAP s doplňkem HA. Připojeno k úložištím E4S. Za základní výpočetní poplatky účtujeme za úložiště SAP a HA a také RHEL.
|             | 7.6      | LVM    | Linuxový agent | RHEL 7,6 pro SAP s doplňkem HA. Připojeno k úložištím E4S. Za základní výpočetní poplatky účtujeme za úložiště SAP a HA a také RHEL.
RHEL – BYOS     |RHEL – lvm74| LVM    | Linuxový agent | Image RHEL 7,4 BYOS, které nejsou připojené k žádnému zdroji aktualizací, nebudou účtovat RHEL Premium.
|             |RHEL – lvm75| LVM    | Linuxový agent | Image RHEL 7,5 BYOS, které nejsou připojené k žádnému zdroji aktualizací, nebudou účtovat RHEL Premium.
|             |RHEL – lvm76| LVM    | Linuxový agent | Image RHEL 7,6 BYOS, které nejsou připojené k žádnému zdroji aktualizací, nebudou účtovat RHEL Premium.
|             |RHEL – lvm77| LVM    | Linuxový agent | Image RHEL 7,7 BYOS, které nejsou připojené k žádnému zdroji aktualizací, nebudou účtovat RHEL Premium.
|             |RHEL – lvm8 | LVM    | Linuxový agent | Image RHEL 8 BYOS (v hodnotě verze image se zobrazuje i podverze RHEL), která není připojená k žádnému zdroji aktualizací, neúčtuje RHEL Premium.

## <a name="next-steps"></a>Další kroky
* Přečtěte si další informace o [obrázcích Red Hat v Azure](./redhat-images.md).
* Přečtěte si další informace o [infrastruktuře aktualizace Red Hat](./redhat-rhui.md).
* Přečtěte si další informace o [nabídce RHEL BYOS](./byos.md).
* Informace o zásadách podpory Red Hat pro všechny verze RHEL najdete na stránce [Red Hat Enterprise Linux životní cyklus](https://access.redhat.com/support/policy/updates/errata) .

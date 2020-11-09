---
title: Red Hat Enterprise Linux imagí dostupných v Azure
description: Přečtěte si o Red Hat Enterprise Linuxch imagí v Microsoft Azure
author: asinn826
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 04/16/2020
ms.author: alsin
ms.reviewer: cynthn
ms.openlocfilehash: 66e2f70a5d55d263f7da343c0987e77a9f60c3b0
ms.sourcegitcommit: 65d518d1ccdbb7b7e1b1de1c387c382edf037850
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2020
ms.locfileid: "94372672"
---
# <a name="red-hat-enterprise-linux-rhel-images-available-in-azure"></a>Image Red Hat Enterprise Linux (RHEL) dostupné v Azure
Azure nabízí celou řadu imagí RHEL pro různé případy použití.

> [!NOTE]
> Všechny image RHEL jsou k dispozici ve veřejných a Azure Governmentch cloudech Azure. V cloudech Azure Čína nejsou k dispozici.

## <a name="list-of-rhel-images"></a>Seznam imagí RHEL
Toto je seznam imagí RHEL dostupných v Azure. Pokud není uvedeno jinak, všechny image jsou LVM rozdělené a připojené k běžným RHEL úložištím (ne EUS, ne E4S). Pro obecné použití jsou aktuálně k dispozici následující Image:

> [!NOTE]
> Nezpracované obrázky se už nevytvářejí ve prospěch LVM imagí. LVM nabízí několik výhod oproti staršímu schématu oddílu RAW (bez LVM), včetně významně flexibilnějších možností změny velikosti oddílů.

Nabídka| SKU | Dělení | Zřizování | Poznámky
:----|:----|:-------------|:-------------|:-----
RHEL          | 6.7      | ZÍSKÁNÍ    | Linuxový agent | Rozšířená podpora životního cyklu je dostupná od 1. prosince. [Další podrobnosti najdete tady.](redhat-extended-lifecycle-support.md)
|             | 6.8      | ZÍSKÁNÍ    | Linuxový agent | Rozšířená podpora životního cyklu je dostupná od 1. prosince. [Další podrobnosti najdete tady.](redhat-extended-lifecycle-support.md)
|             | 6.9      | ZÍSKÁNÍ    | Linuxový agent | Rozšířená podpora životního cyklu je dostupná od 1. prosince. [Další podrobnosti najdete tady.](redhat-extended-lifecycle-support.md)
|             | 6,10     | ZÍSKÁNÍ    | Linuxový agent | Rozšířená podpora životního cyklu je dostupná od 1. prosince. [Další podrobnosti najdete tady.](redhat-extended-lifecycle-support.md)
|             | 7 – RAW    | ZÍSKÁNÍ    | Linuxový agent | RHEL 7. x rodina imagí. <br> Ve výchozím nastavení připojené k pravidelným úložištím (ne EUS).
|             | 7 – LVM    | LVM    | Linuxový agent | RHEL 7. x rodina imagí. <br> Ve výchozím nastavení připojené k pravidelným úložištím (ne EUS). Pokud hledáte standardní image RHEL, která se má nasadit, použijte tuto sadu imagí a/nebo její protějšek 2. generace.
|             | 7lvm – Gen2| LVM    | Linuxový agent | Generace 2, RHEL 7. x řady imagí. <br> Ve výchozím nastavení připojené k pravidelným úložištím (ne EUS). Pokud hledáte standardní image RHEL, která se má nasadit, použijte tuto sadu imagí a/nebo její protějšek 1. generace.
|             | 7-RAW-CI | RAW-CI | Cloud-init  | RHEL 7. x rodina imagí. <br> Ve výchozím nastavení připojené k pravidelným úložištím (ne EUS).
|             | 7.2      | ZÍSKÁNÍ    | Linuxový agent |
|             | 7.3      | ZÍSKÁNÍ    | Linuxový agent |
|             | 7,4      | ZÍSKÁNÍ    | Linuxový agent | Připojí se k úložištím EUS ve výchozím nastavení od dubna 2019.
|             | 74 – Gen2  | ZÍSKÁNÍ    | Linuxový agent | Ve výchozím nastavení připojené k úložištím EUS.
|             | 7,5      | ZÍSKÁNÍ    | Linuxový agent | Připojeno k úložištím EUS ve výchozím nastavení od června 2019.
|             | 75 – Gen2  | ZÍSKÁNÍ    | Linuxový agent | Ve výchozím nastavení připojené k úložištím EUS.
|             | 7.6      | ZÍSKÁNÍ    | Linuxový agent | Připojeno k úložištím EUS ve výchozím nastavení jako květen 2019.
|             | 76 – Gen2  | ZÍSKÁNÍ    | Linuxový agent | Ve výchozím nastavení připojené k úložištím EUS.
|             | 7.7      | LVM    | Linuxový agent | Ve výchozím nastavení připojené k úložištím EUS.
|             | 77 – Gen2  | LVM    | Linuxový agent | Ve výchozím nastavení připojené k úložištím EUS.
|             | 7,8      | LVM    | Linuxový agent | Připojeno k pravidelným úložištím (EUS není k dispozici pro RHEL 7,8)
|             | 78 – Gen2  | LVM    | Linuxový agent | Připojeno k pravidelným úložištím (EUS není k dispozici pro RHEL 7,8)
|             | 7.9      | LVM    | Linuxový agent | Připojeno k pravidelným úložištím (EUS není k dispozici pro RHEL 7,9)
|             | 79 – Gen2  | LVM    | Linuxový agent | Připojeno k pravidelným úložištím (EUS není k dispozici pro RHEL 7,9)
|             | 8 – LVM    | LVM    | Linuxový agent | RHEL 8. x řady imagí. Připojeno k pravidelným úložištím.
|             | 8. LVM – Gen2| LVM    | Linuxový agent | Hyper-V Generation 2 – RHEL 8. x řada imagí. Připojeno k pravidelným úložištím.
|             | 8        | LVM    | Linuxový agent | Image RHEL 8,0.
|             | 8 – Gen2   | LVM    | Linuxový agent | Hyper-V generace 2 – image RHEL 8,0.
|             | 8.1      | LVM    | Linuxový agent | Image RHEL 8,2. Aktuálně připojeno k pravidelným úložištím.
|             | 81gen2   | LVM    | Linuxový agent | Hyper-V generace 2 – image RHEL 8,1. Aktuálně připojeno k pravidelným úložištím.
|             | 8,1 – CI   | LVM    | Linuxový agent | Image RHEL 8,1 s použitím Cloud-init jako zřizovacího agenta. Aktuálně připojeno k pravidelným úložištím.
|             | 81-CI-Gen2| LVM    | Linuxový agent | Hyper-V generace 2 – image RHEL 8,1 pomocí Cloud-init jako zřizovacího agenta. Aktuálně připojeno k pravidelným úložištím.
|             | 8.2      | LVM    | Linuxový agent | Image RHEL 8,2. Aktuálně připojeno k pravidelným úložištím.
|             | 82gen2   | LVM    | Linuxový agent | Hyper-V generace 2 – image RHEL 8,1. Aktuálně připojeno k pravidelným úložištím.
RHEL – SAP      | 7,4      | LVM    | Linuxový agent | RHEL 7,4 pro SAP HANA a obchodní aplikace. Připojené k úložištím E4S budou účtovat prémii za SAP a RHEL a také základní výpočetní poplatek.
|             | 74sap – Gen2| LVM    | Linuxový agent | RHEL 7,4 pro SAP HANA a obchodní aplikace. Obrázek generace 2 Připojené k úložištím E4S budou účtovat prémii za SAP a RHEL a také základní výpočetní poplatek.
|             | 7,5       | LVM    | Linuxový agent | RHEL 7,5 pro SAP HANA a obchodní aplikace. Připojené k úložištím E4S budou účtovat prémii za SAP a RHEL a také základní výpočetní poplatek.
|             | 75sap – Gen2| LVM    | Linuxový agent | RHEL 7,5 pro SAP HANA a obchodní aplikace. Obrázek generace 2 Připojené k úložištím E4S budou účtovat prémii za SAP a RHEL a také základní výpočetní poplatek.
|             | 7.6       | LVM    | Linuxový agent | RHEL 7,6 pro SAP HANA a obchodní aplikace. Připojené k úložištím E4S budou účtovat prémii za SAP a RHEL a také základní výpočetní poplatek.
|             | 76sap – Gen2| LVM    | Linuxový agent | RHEL 7,6 pro SAP HANA a obchodní aplikace. Obrázek generace 2 Připojené k úložištím E4S budou účtovat prémii za SAP a RHEL a také základní výpočetní poplatek.
|             | 7.7       | LVM    | Linuxový agent | RHEL 7,7 pro SAP HANA a obchodní aplikace. Připojené k úložištím E4S budou účtovat prémii za SAP a RHEL a také základní výpočetní poplatek.
RHEL-SAP-HANA (bude odebráno v listopadu 2020) | 6.7       | ZÍSKÁNÍ    | Linuxový agent | RHEL 6,7 pro SAP HANA. Zastaralé ve prospěch imagí RHEL-SAP. Tato bitová kopie bude odebrána v listopadu 2020. Další podrobnosti o nabídkách cloudu pro Red Hat jsou k dispozici [zde](https://access.redhat.com/articles/3751271).
|             | 7.2       | LVM    | Linuxový agent | RHEL 7,2 pro SAP HANA. Zastaralé ve prospěch imagí RHEL-SAP. Tato bitová kopie bude odebrána v listopadu 2020. Další podrobnosti o nabídkách cloudu pro Red Hat jsou k dispozici [zde](https://access.redhat.com/articles/3751271).
|             | 7.3       | LVM    | Linuxový agent | RHEL 7,3 pro SAP HANA. Zastaralé ve prospěch imagí RHEL-SAP. Tato bitová kopie bude odebrána v listopadu 2020. Další podrobnosti o nabídkách cloudu pro Red Hat jsou k dispozici [zde](https://access.redhat.com/articles/3751271).
RHEL-SAP-APLIKACE | 6.8       | ZÍSKÁNÍ    | Linuxový agent | RHEL 6,8 pro SAP Business Applications. Zastaralé ve prospěch imagí RHEL-SAP.
|             | 7.3       | LVM    | Linuxový agent | RHEL 7,3 pro SAP Business Applications. Zastaralé ve prospěch imagí RHEL-SAP.
|             | 7,4       | LVM    | Linuxový agent | RHEL 7,4 pro SAP Business Applications.
|             | 7.6       | LVM    | Linuxový agent | RHEL 7,6 pro SAP Business Applications.
|             | 7.7       | LVM    | Linuxový agent | RHEL 7,7 pro SAP Business Applications.
RHEL-HA       | 7,4       | LVM    | Linuxový agent | RHEL 7,4 s doplňkem HA. Poplatky za HA a RHEL se účtují za základní výpočetní poplatek. Zastaralé ve prospěch imagí RHEL-SAP-HA.
|             | 7,5       | LVM    | Linuxový agent | RHEL 7,5 s doplňkem HA. Poplatky za HA a RHEL se účtují za základní výpočetní poplatek. Zastaralé ve prospěch imagí RHEL-SAP-HA.
|             | 7.6       | LVM    | Linuxový agent | RHEL 7,6 s doplňkem HA. Poplatky za HA a RHEL se účtují za základní výpočetní poplatek. Zastaralé ve prospěch imagí RHEL-SAP-HA.
RHEL-SAP-HA   | 7,4          | LVM    | Linuxový agent | RHEL 7,4 pro SAP se službami HA a Update. Připojeno k úložištím E4S. Za základní výpočetní poplatky účtujeme za úložiště SAP a HA a také RHEL.
|             | 74sapha – Gen2 | LVM    | Linuxový agent | RHEL 7,4 pro SAP se službami HA a Update. Obrázek generace 2 Připojeno k úložištím E4S. Za základní výpočetní poplatky účtujeme za úložiště SAP a HA a také RHEL.
|             | 7,5          | LVM    | Linuxový agent | RHEL 7,5 pro SAP se službami HA a Update. Připojeno k úložištím E4S. Za základní výpočetní poplatky účtujeme za úložiště SAP a HA a také RHEL.
|             | 7.6          | LVM    | Linuxový agent | RHEL 7,6 pro SAP se službami HA a Update. Připojeno k úložištím E4S. Za základní výpočetní poplatky účtujeme za úložiště SAP a HA a také RHEL.
|             | 76sapha – Gen2 | LVM    | Linuxový agent | RHEL 7,6 pro SAP se službami HA a Update. Obrázek generace 2 Připojeno k úložištím E4S. Za základní výpočetní poplatky účtujeme za úložiště SAP a HA a také RHEL.
|             | 7.7          | LVM    | Linuxový agent | RHEL 7,7 pro SAP se službami HA a Update. Připojeno k úložištím E4S. Za základní výpočetní poplatky účtujeme za úložiště SAP a HA a také RHEL.
|             | 77sapha – Gen2 | LVM    | Linuxový agent | RHEL 7,7 pro SAP se službami HA a Update. Obrázek generace 2 Připojeno k úložištím E4S. Za základní výpočetní poplatky účtujeme za úložiště SAP a HA a také RHEL.
RHEL – BYOS     |RHEL – lvm74| LVM    | Linuxový agent | Image RHEL 7,4 BYOS, které nejsou připojené k žádnému zdroji aktualizací, nebudou účtovat RHEL Premium.
|             |RHEL – lvm75| LVM    | Linuxový agent | Image RHEL 7,5 BYOS, které nejsou připojené k žádnému zdroji aktualizací, nebudou účtovat RHEL Premium.
|             |RHEL – lvm76| LVM    | Linuxový agent | Image RHEL 7,6 BYOS, které nejsou připojené k žádnému zdroji aktualizací, nebudou účtovat RHEL Premium.
|             |RHEL-lvm76-Gen2| LVM    | Linuxový agent | RHEL 7,6 generace 2 BYOS images, které nejsou připojené k žádnému zdroji aktualizací, se neúčtují RHEL Premium.
|             |RHEL – lvm77| LVM    | Linuxový agent | Image RHEL 7,7 BYOS, které nejsou připojené k žádnému zdroji aktualizací, nebudou účtovat RHEL Premium.
|             |RHEL-lvm77-Gen2| LVM    | Linuxový agent | RHEL 7,7 generace 2 BYOS images, které nejsou připojené k žádnému zdroji aktualizací, se neúčtují RHEL Premium.
|             |RHEL – lvm78| LVM    | Linuxový agent | Image RHEL 7,8 BYOS, které nejsou připojené k žádnému zdroji aktualizací, nebudou účtovat RHEL Premium.
|             |RHEL-lvm78-Gen2| LVM    | Linuxový agent | RHEL 7,8 generace 2 BYOS images, které nejsou připojené k žádnému zdroji aktualizací, se neúčtují RHEL Premium.
|             |RHEL – lvm8 | LVM    | Linuxový agent | Image RHEL 8,0 BYOS, které nejsou připojené k žádnému zdroji aktualizací, nebudou účtovat RHEL Premium.
|             |RHEL-lvm8-Gen2 | LVM    | Linuxový agent | RHEL 8,0 generace 2 BYOS images, které nejsou připojené k žádnému zdroji aktualizací, se neúčtují RHEL Premium.
|             |RHEL – lvm81 | LVM    | Linuxový agent | Image RHEL 8,1 BYOS, které nejsou připojené k žádnému zdroji aktualizací, nebudou účtovat RHEL Premium.
|             |RHEL-lvm81-Gen2 | LVM    | Linuxový agent | RHEL 8,1 generace 2 BYOS images, které nejsou připojené k žádnému zdroji aktualizací, se neúčtují RHEL Premium.
|             |RHEL – lvm82 | LVM    | Linuxový agent | Image RHEL 8,2 BYOS, které nejsou připojené k žádnému zdroji aktualizací, nebudou účtovat RHEL Premium.
|             |RHEL-lvm82-Gen2 | LVM    | Linuxový agent | RHEL 8,2 generace 2 BYOS images, které nejsou připojené k žádnému zdroji aktualizací, se neúčtují RHEL Premium.

> [!NOTE]
> Nabídka produktů RHEL-SAP-HANA se Red Hat považuje za konec životnosti. Existující nasazení budou i nadále fungovat normálně, ale Red Hat doporučuje, aby zákazníci migrovali z imagí RHEL-SAP-HANA do imagí RHEL-SAP-HA, které zahrnují úložiště SAP HANA a také doplněk HA. Další podrobnosti o nabídkách cloudu pro Red Hat jsou k dispozici [zde](https://access.redhat.com/articles/3751271).

## <a name="next-steps"></a>Další kroky
* Přečtěte si další informace o [obrázcích Red Hat v Azure](./redhat-images.md).
* Přečtěte si další informace o [infrastruktuře aktualizace Red Hat](./redhat-rhui.md).
* Přečtěte si další informace o [nabídce RHEL BYOS](./byos.md).
* Informace o zásadách podpory Red Hat pro všechny verze RHEL najdete na stránce [Red Hat Enterprise Linux životní cyklus](https://access.redhat.com/support/policy/updates/errata) .

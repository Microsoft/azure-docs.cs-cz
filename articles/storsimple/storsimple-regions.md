---
title: Dostupnost v oblastech StorSimple | Dokumentace Microsoftu
description: Vysvětluje oblastí Azure, ve kterých jsou k dispozici různé modely zařízení StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2017
ms.author: alkohli
ms.openlocfilehash: 7e35aa8120c455b8cc0d0881a9ceb5120c57deff
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52969664"
---
# <a name="available-regions-for-your-storsimple"></a>Dostupné oblasti pro StorSimple

## <a name="overview"></a>Přehled

Datacentra Azure fungovat v různých geografických oblastech po celém světě, které splňují požadavky zákazníků výkonu, požadavky a preference týkající se umístění dat. Zeměpisná oblast Azure je definované oblasti na světě, který obsahuje alespoň jedné oblasti Azure. Oblast Azure je oblast v rámci zeměpisné oblasti, který obsahuje jeden nebo více datových centrech.

Volba určitá oblast Azure je velmi důležité a volbou oblasti je ovlivněno faktory, jako je rezidence dat a suverenitu, služba dostupnost, výkon, náklady a redundance. Další informace o tom, jak vybrat oblast, přejít na [oblast Azure který je pro mě nejlepší?](https://azure.microsoft.com/overview/datacenters/how-to-choose/)

U řešení StorSimple volbou oblasti konkrétně určena následující faktory:

- Oblastech, kde je k dispozici ve službě Správce zařízení StorSimple.
- Zemí, kde je k dispozici fyzické zařízení StorSimple, cloud nebo virtuální zařízení.
- Oblasti, kde by mělo být účty úložiště, které ukládají StorSimple data pro optimální výkon.

Tento kurz popisuje dostupnosti oblastí pro službu Správce zařízení StorSimple, fyzické místní a cloudové zařízení. Informace obsažené v tomto článku se vztahuje na StorSimple 8000 a zařízení 1200 series.

## <a name="region-availability-for-storsimple-device-manager-service"></a>Dostupnost v oblastech pro službu Správce zařízení StorSimple

Služba Správce zařízení StorSimple je aktuálně podporován ve 12 veřejných oblastech a 2 oblasti Azure Government.

Při prvním vytvoření služby StorSimple Device Manager definujte oblast nebo umístění. Obecně platí je vybrán umístění co nejblíže zeměpisné oblasti, ve kterém je nasazená zařízení. Ale zařízení a služby je také nasadit v různých umístěních.

Tady je seznam oblastí, kde služby Správce zařízení StorSimple je k dispozici pro veřejný cloud Azure a je možné nasadit.

![storsimple zařízení – Správce service oblastí](./media/storsimple-region/storsimple-device-manager-service-regions.png)

Pro cloud Azure Government je dostupná v datacentrech v Iowě státní správy USA a US Gov Virginie ve službě Správce zařízení StorSimple.

## <a name="region-availability-for-data-stored-in-storsimple"></a>Regionální dostupnost dat uložených v StorSimple

StorSimple data fyzicky uložená v účtech úložiště Azure a tyto účty jsou k dispozici ve všech oblastech Azure. Při vytváření účtu služby Azure storage je vybrán primární umístění účtu úložiště a, který určuje oblast, ve které se nachází data.

Při prvním vytvoření služby Správce zařízení StorSimple a přidružit účet úložiště, může být služba Správce zařízení StorSimple a Azure storage ve dvou různých umístěních. V takovém případě je nutné vytvořit Správce zařízení StorSimple a účet úložiště Azure odděleně.

Obecně platí vyberte nejbližší oblast, do služby pro účet úložiště. Nicméně je nejbližší oblast Microsoft Azure ve skutečnosti nemusí být oblasti s nejnižší latenci. Je latence, který určuje služba výkon sítě a výkon řešení. Pokud zvolíte účet úložiště v jiné oblasti, je tedy důležité věděli, jaké latence mezi službou a oblasti přidružené k účtu úložiště.

Pokud používáte řešení StorSimple Cloud Appliance, pak doporučujeme, služby a přidružený účet úložiště jsou ve stejné oblasti. Účty úložiště v jiné oblasti může vést k nižšímu výkonu.

## <a name="availability-of-storsimple-device"></a>Dostupnost zařízení StorSimple

V závislosti na modelu, zařízení StorSimple může být k dispozici v různých zeměpisných oblastech nebo země.

### <a name="storsimple-physical-device-models-81008600"></a>Fyzické zařízení StorSimple (modely 8100 nebo 8600)

Pokud používáte StorSimple 8100 nebo 8600 fyzické zařízení, zařízení je dostupná v těchto zemích.

| #  | Země        | #  | Země     | #  | Země      | #  | Země              |
|----|----------------|----|-------------|----|--------------|----|----------------------|
| 1  | Austrálie      | 16 | Hongkong   | 31 | Nový Zéland  | 46 | Jižní Afrika         |
| 2  | Rakousko        | 17 | Maďarsko     | 32 | Nigérie      | 47 | Jižní Korea          |
| 3  | Bahrajn        | 18 | Island     | 33 | Norsko       | 48 | Španělsko                |
| 4  | Belgie        | 19 | Indie       | 34 | Peru         | 49 | Srí Lanka            |
| 5  | Brazílie         | 20 | Indonésie   | 35 | Filipíny  | 50 | Švédsko               |
| 6  | Kanada         | 21 | Irsko     | 36 | Polsko       | 51 | Švýcarsko          |
| 7  | Chile          | 22 | Izrael      | 37 | Portugalsko     | 52 | Tchaj-wan               |
| 8  | Kolumbie       | 23 | Itálie       | 38 | Portoriko  | 53 | Thajsko             |
| 9  | Česká republika | 24 | Japonsko       | 39 | Katar        | 54 | Turecko               |
| 10 | Dánsko        | 25 | Keňa       | 40 | Rumunsko      | 55 | Ukrajina              |
| 11 | Egypt          | 26 | Kuvajt      | 41 | Rusko       | 56 | Spojené arabské emiráty |
| 12 | Finsko        | 27 | Macao       | 42 | Saúdská Arábie | 57 | Spojené království       |
| 13 | Francie         | 28 | Malajsie    | 43 | Singapur    | 58 | Spojené státy        |
| 14 | Německo        | 29 | Mexiko      | 44 | Slovensko     | 59 | Vietnam              |
| 15 | Řecko         | 30 | Nizozemsko | 45 | Slovinsko     | 60 | Chorvatsko              |

Tento seznam se změní při přidání více zemí. Nejaktuálnější seznam zeměpisných oblastech, najdete v dodatku podmínky pro pole úložiště v [podmínkách](https://www.microsoft.com/en-us/licensing/product-licensing/products).

Microsoft může dodávat fyzický hardware a poskytují náhradní díly výměna hardwaru pro StorSimple na geografické oblasti jako v předchozím seznamu.

> [!IMPORTANT]
> Neumísťujte v oblasti, kde není podporovaná StorSimple fyzickým zařízením StorSimple. Společnost Microsoft nebude moct dodávat náhradní díly do zemí, kde se StorSimple nepodporuje.

### <a name="storsimple-cloud-appliance-models-80108020"></a>Řešení StorSimple Cloud Appliance (modely 8010/8020)

Pokud používáte cloudové zařízení StorSimple 8010 nebo 8020, pak zařízení je podporována a k dispozici ve všech oblastech, kde je podporován základního virtuálního počítače. 8010 používá _Standard_A3_ virtuálního počítače, který je podporován ve všech oblastech Azure.

Používá 8020 úložiště premium a _Standard_DS3_ virtuálního počítače k vytvoření cloudového zařízení. V oblasti Azure, které podporují službu Premium Storage se podporuje 8020 a _Standard_DS3_ virtuální počítače Azure. Použijte [tento seznam](https://azure.microsoft.com/regions/services/), abyste zjistili, jestli jsou ve vaší oblasti dostupné obě možnosti **Virtuální počítače > DS-series** a **Úložiště > Diskové úložiště**.

### <a name="storsimple-virtual-array-model-1200"></a>StorSimple Virtual Array (Model 1200)

Pokud používáte 1200 series StorSimple Virtual Array, image virtuálního disku je podporován ve všech oblastech Azure.

## <a name="next-steps"></a>Další postup

* Další informace o [ceny pro různé modely StorSimple](https://azure.microsoft.com/pricing/calculator/#storsimple2).
* Další informace o [správou vašeho účtu úložiště StorSimple](storsimple-8000-manage-storage-accounts.md).
* Další informace o tom, jak [použití služby Správce zařízení StorSimple ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

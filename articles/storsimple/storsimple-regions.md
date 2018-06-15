---
title: Dostupnost v oblastech StorSimple | Microsoft Docs
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
ms.openlocfilehash: d47109d541a3df93d9234e27e53d1538f6bc4c6e
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2017
ms.locfileid: "24879054"
---
# <a name="available-regions-for-your-storsimple"></a>Dostupné oblasti pro vaše zařízení StorSimple

## <a name="overview"></a>Přehled

V různých zeměpisných po celém světě, aby splnily požadavky zákazníka výkonu, požadavky a předvolby týkající se umístění dat fungovat datových centrech Azure. Azure geography je definovaný oblasti na světě, který obsahuje alespoň jedné oblasti Azure. Oblast Azure je oblasti v rámci geography, který obsahuje jeden nebo více datových centrech.

Výběr oblasti Azure je velmi důležité a vybraná oblast je vliv faktory, například data sídlo a suverenity, dostupnost služby, výkon, náklady a redundance. Další informace o tom, jak vyberte oblast, přejděte na [oblast Azure který je pro mě nejlepší?](https://azure.microsoft.com/overview/datacenters/how-to-choose/)

Pro řešení StorSimple je volba oblast speciálně určený následující faktory:

- Oblasti, kde je k dispozici služby StorSimple Manager zařízení.
- Zemí, kde je k dispozici fyzického zařízení StorSimple, cloud nebo virtuální zařízení.
- Oblasti, kde má být účty úložiště, které ukládají StorSimple data pro optimální výkon.

Tento kurz popisuje dostupnost oblast pro službu Správce zařízení StorSimple, fyzický místní a cloudové zařízení. Informace obsažené v tomto článku se vztahuje na StorSimple 8000 a 1200 řady zařízení.

## <a name="region-availability-for-storsimple-device-manager-service"></a>Dostupnost v oblastech pro službu Správce zařízení StorSimple

Služba StorSimple Manager zařízení se aktuálně podporuje v 12 veřejných oblastí a 2 oblasti Azure Government.

Při prvním vytvoření služby StorSimple Manager zařízení definujete oblast nebo umístění. Obecně je zvolen umístění nejblíže geografickému regionu, kde je nasazená zařízení. Ale zařízení a služby je možné nasadit také v různých umístěních.

Tady je seznam oblastí, kde služba Manager zařízení StorSimple je k dispozici pro veřejného cloudu Azure a dá se nasadit.

![storsimple zařízení manager-service oblastí](./media/storsimple-region/storsimple-device-manager-service-regions.png)

Pro cloud Azure Government je k dispozici v datových centrech US verze pro státní správu Iowa a Virginia nám verze pro státní správu služby StorSimple Manager zařízení.

## <a name="region-availability-for-data-stored-in-storsimple"></a>Dostupnost v oblastech dat uložených v zařízení StorSimple

StorSimple data je fyzicky uložena v účtech úložiště Azure a tyto účty jsou k dispozici ve všech oblastech Azure. Když vytvoříte účet úložiště Azure, je vybrán primární umístění účtu úložiště a oblasti, kde se nachází data, která určují.

Při prvním vytvoření služby StorSimple Manager zařízení a přidružit účet úložiště, může být služba Manager zařízení StorSimple a úložištěm Azure na dvě samostatná místa. V takovém případě je nutné vytvořit Správce zařízení StorSimple a účet úložiště Azure odděleně.

Obecně platí vyberte nejbližší oblast pro vaši službu pro váš účet úložiště. Nejbližší oblast Microsoft Azure, ale ve skutečnosti pravděpodobně oblasti s nejnižší latencí. Je latence, která určuje výkon služby sítě a proto výkon řešení. Proto pokud zvolíte účet úložiště v jiné oblasti, je důležité vědět, jaké jsou latence mezi službou a oblasti přidružené k účtu úložiště.

Pokud používáte o cloudu zařízení StorSimple, pak doporučujeme, aby služba a přidruženého účtu úložiště jsou ve stejné oblasti. Účty úložiště v jiné oblasti může vést k nižšímu výkonu.

## <a name="availability-of-storsimple-device"></a>Dostupnost zařízení StorSimple

V závislosti na modelu, zařízení StorSimple může být k dispozici v různých zeměpisných regionech nebo zemích.

### <a name="storsimple-physical-device-models-81008600"></a>Fyzického zařízení StorSimple (modely 8100/8600)

Pokud používáte StorSimple 8100 nebo 8600 fyzického zařízení, zařízení, je k dispozici v těchto zemích.

| #  | Země        | #  | Země     | #  | Země      | #  | Země              |
|----|----------------|----|-------------|----|--------------|----|----------------------|
| 1  | Austrálie      | 16 | Hongkong   | 31 | Nový Zéland  | 46 | Jihoafrická republika         |
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

Tento seznam se změní při přidávání více zemích. Nejnovější seznam zeměpisných oblastí, přejděte na Příloha podmínky pole úložiště v [produktu podmínky](https://www.microsoft.com/en-us/Licensing/product-licensing).

Společnost Microsoft může dodávat fyzický hardware a zadejte náhradní díly výměna hardwaru pro zařízení StorSimple na zeměpisných v předchozím seznamu.

> [!IMPORTANT]
> Neumísťujte fyzického zařízení StorSimple v oblasti, kde se StorSimple nepodporuje. Společnost Microsoft nebude moct dodávat náhradní díly do zemí, kde se StorSimple nepodporuje.

### <a name="storsimple-cloud-appliance-models-80108020"></a>Zařízení StorSimple cloudu (modely 8010/8020)

Pokud používáte StorSimple cloudu zařízení 8010 nebo 8020, pak zařízení je podporována a k dispozici ve všech oblastech, kde je podporováno základní virtuální počítač. 8010 používá _Standard_A3_ virtuálního počítače, který není podporován ve všech oblastech Azure.

Používá 8020 úložiště premium a _Standard_DS3_ virtuálních počítačů k vytvoření cloudové zařízení. V oblasti Azure, které podporují službu Premium Storage je podporována 8020 a _Standard_DS3_ virtuálních počítačích Azure. Použijte [tento seznam](https://azure.microsoft.com/regions/services/), abyste zjistili, jestli jsou ve vaší oblasti dostupné obě možnosti **Virtuální počítače > DS-series** a **Úložiště > Diskové úložiště**.

### <a name="storsimple-virtual-array-model-1200"></a>Pole virtuální zařízení StorSimple (Model 1200)

Pokud používáte pole virtuální zařízení StorSimple řady 1200, bitové kopie virtuálního disku je podporován ve všech oblastech Azure.

## <a name="next-steps"></a>Další kroky

* Další informace o [ceny pro různé modely StorSimple](https://azure.microsoft.com/pricing/calculator/#storsimple2).
* Další informace o [Správa účtu úložiště StorSimple](storsimple-8000-manage-storage-accounts.md).
* Další informace o tom, jak [použít službu StorSimple Manager zařízení ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

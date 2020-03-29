---
title: Dostupnost oblasti StorSimple
description: Vysvětluje oblasti Azure, ve kterých jsou k dispozici různé modely zařízení StorSimple.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 11/16/2017
ms.author: alkohli
ms.openlocfilehash: a2f04d6faa7770eef7768437ae0e624b76713f98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76275112"
---
# <a name="available-regions-for-your-storsimple"></a>Dostupné oblasti pro váš StorSimple

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Přehled

Datová centra Azure fungují v několika zeměpisných oblastech po celém světě, aby splňovala požadavky zákazníků na výkon, požadavky a preference týkající se umístění dat. Azure geografie je definovaná oblast světa, která obsahuje alespoň jednu oblast Azure. Oblast Azure je oblast v rámci zeměpisné oblasti, obsahující jedno nebo více datových center.

Výběr oblasti Azure je velmi důležité a výběr oblasti je ovlivněnfaktory, jako je rezidence dat a suverenity, dostupnost služeb, výkon, náklady a redundance. Další informace o tom, jak si vybrat oblast, najdete v [tématu Která oblast Azure je pro mě ta pravá?](https://azure.microsoft.com/overview/datacenters/how-to-choose/)

Pro řešení StorSimple je volba oblasti specificky určena následujícími faktory:

- Oblasti, kde je k dispozici služba StorSimple Device Manager.
- Země nebo oblasti, kde je k dispozici fyzické, cloudové nebo virtuální zařízení StorSimple.
- Oblasti, kde účty úložiště, které ukládají data StorSimple by měla být umístěna pro optimální výkon.

Tento kurz popisuje dostupnost oblasti pro službu StorSimple Device Manager, místní fyzické a cloudová zařízení. Informace obsažené v tomto článku je použitelná pro zařízení řady StorSimple 8000 a 1200.

## <a name="region-availability-for-storsimple-device-manager-service"></a>Dostupnost oblasti pro službu StorSimple Device Manager

Služba StorSimple Device Manager je momentálně podporovaná ve 12 veřejných oblastech a ve 2 oblastech Azure Government.

Při prvním vytvoření služby StorSimple Device Manager definujete oblast nebo umístění. Obecně je vybráno umístění, které je nejblíže zeměpisné oblasti, kde je zařízení nasazeno. Ale zařízení a služba mohou být také nasazeny na různých místech.

Tady je seznam oblastí, kde je služba StorSimple Device Manager dostupná pro veřejný cloud Azure a dá se nasadit.

![storsimple-device-manager-service-regions](./media/storsimple-region/storsimple-device-manager-service-regions.png)

Pro cloud Azure Government je služba StorSimple Device Manager dostupná v datových centrech US Gov Iowa a US Gov Virginia.

## <a name="region-availability-for-data-stored-in-storsimple"></a>Dostupnost oblasti pro data uložená v StorSimple

Data StorSimple se fyzicky ukládají v účtech úložiště Azure a tyto účty jsou dostupné ve všech oblastech Azure. Když vytvoříte účet úložiště Azure, vybere se primární umístění účtu úložiště, které určuje oblast, kde se data nacházejí.

Při prvním vytvoření služby StorSimple Device Manager a přidružit účet úložiště s ním, vaše služba StorSimple Device Manager a úložiště Azure může být ve dvou samostatných umístěních. V takovém případě je nutné vytvořit Správce zařízení StorSimple a účet úložiště Azure odděleně.

Obecně zvolte nejbližší oblast služby pro váš účet úložiště. Nejbližší oblast Microsoft Azure však nemusí být ve skutečnosti oblast s nejnižší latencí. Je latence, která určuje výkon síťové služby a tedy výkon řešení. Pokud tedy vybíráte účet úložiště v jiné oblasti, je důležité vědět, jaké jsou latence mezi vaší službou a oblastí přidruženou k vašemu účtu úložiště.

Pokud používáte StorSimple Cloud Appliance, pak doporučujeme, aby služba a přidružený účet úložiště jsou ve stejné oblasti. Účty úložiště v jiné oblasti může mít za následek nízký výkon.

## <a name="availability-of-storsimple-device"></a>Dostupnost zařízení StorSimple

V závislosti na modelu mohou být zařízení StorSimple k dispozici v různých zeměpisných oblastech nebo zemích nebo oblastech.

### <a name="storsimple-physical-device-models-81008600"></a>StorJednoduché fyzické zařízení (modely 8100/8600)

Pokud používáte fyzické zařízení StorSimple 8100 nebo 8600, je zařízení k dispozici v následujících zemích nebo oblastech.

| #  | Země/region        | #  | Země/region     | #  | Země/region      | #  | Země/region             |
|----|-----------------------|----|--------------------|----|---------------------|----|----------------------------|
| 1  | Austrálie             | 16 | Hongkong – zvláštní správní oblast      | 31 | Nový Zéland         | 46 | Jižní Afrika               |
| 2  | Rakousko               | 17 | Maďarsko            | 32 | Nigérie             | 47 | Jižní Korea                |
| 3  | Bahrajn               | 18 | Island            | 33 | Norsko              | 48 | Španělsko                      |
| 4  | Belgie               | 19 | Indie              | 34 | Peru                | 49 | Srí Lanka                  |
| 5  | Brazílie                | 20 | Indonésie          | 35 | Filipíny         | 50 | Švédsko                     |
| 6  | Kanada                | 21 | Irsko            | 36 | Polsko              | 51 | Švýcarsko                |
| 7  | Chile                 | 22 | Izrael             | 37 | Portugalsko            | 52 | Tchaj-wan                     |
| 8  | Kolumbie              | 23 | Itálie              | 38 | Portoriko         | 53 | Thajsko                   |
| 9  | Česká republika        | 24 | Japonsko              | 39 | Katar               | 54 | Turecko                     |
| 10 | Dánsko               | 25 | Keňa              | 40 | Rumunsko             | 55 | Ukrajina                    |
| 11 | Egypt                 | 26 | Kuvajt             | 41 | Rusko              | 56 | Spojené arabské emiráty       |
| 12 | Finsko               | 27 | Macao – zvláštní administrativní oblast          | 42 | Saúdská Arábie        | 57 | Spojené království             |
| 13 | Francie                | 28 | Malajsie           | 43 | Singapur           | 58 | Spojené státy              |
| 14 | Německo               | 29 | Mexiko             | 44 | Slovensko            | 59 | Vietnam                    |
| 15 | Řecko                | 30 | Nizozemsko        | 45 | Slovinsko            | 60 | Chorvatsko                    |

Tento seznam se mění s přidáním dalších zemí nebo oblastí. Nejaktuálnější seznam zeměpisných oblastí naleznete v dodatku podmínek pole úložiště v [podmínkách produktu](https://www.microsoft.com/en-us/licensing/product-licensing/products).

Společnost Microsoft může doručovat fyzický hardware a poskytnout náhradu náhradních dílů pro náhradní díly Pro StorSimple do zeměpisných oblastí v předchozím seznamu.

> [!IMPORTANT]
> Neumisťuj fyzické zařízení StorSimple do oblasti, kde není podporováno StorSimple. Společnost Microsoft nebude moci odesílat žádné náhradní díly do zemí nebo oblastí, kde není podporována služba StorSimple.

### <a name="storsimple-cloud-appliance-models-80108020"></a>StorSimple Cloud Appliance (modely 8010/8020)

Pokud používáte StorSimple Cloud Appliance 8010 nebo 8020, pak zařízení je podporované a dostupné ve všech oblastech, kde je podporován základní virtuální modul. 8010 používá _virtuální počítač Standard_A3,_ který je podporovaný ve všech oblastech Azure.

8020 využívá prvotřídní úložiště a Standard_DS3 virtuálním _zařízení_ k vytvoření cloudového zařízení. 8020 je podporovaná v oblastech Azure, které podporují úložiště Premium a _Standard_DS3_ virtuálních počítačích Azure. Použijte [tento seznam](https://azure.microsoft.com/regions/services/), abyste zjistili, jestli jsou ve vaší oblasti dostupné obě možnosti **Virtuální počítače > DS-series** a **Úložiště > Diskové úložiště**.

### <a name="storsimple-virtual-array-model-1200"></a>StorSimple Virtuální pole (Model 1200)

Pokud používáte 1200 řady StorSimple Virtual Array, pak image virtuálního disku je podporována ve všech oblastech Azure.

## <a name="next-steps"></a>Další kroky

* Další informace o [cenách pro různé modely StorSimple](https://azure.microsoft.com/pricing/calculator/#storsimple2).
* Přečtěte si další informace o [správě účtu úložiště StorSimple](storsimple-8000-manage-storage-accounts.md).
* Další informace o tom, jak [používat službu StorSimple Device Manager ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

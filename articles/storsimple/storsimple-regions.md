---
title: Dostupnost oblasti StorSimple
description: Vysvětluje oblasti Azure, ve kterých jsou k dispozici různé modely zařízení StorSimple.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 11/16/2017
ms.author: alkohli
ms.openlocfilehash: 5efb87ad674474b2e21b86b5cdc48785753a51dc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "90564147"
---
# <a name="available-regions-for-your-storsimple"></a>Dostupné oblasti pro váš StorSimple

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Přehled

Datacentra Azure pracují v různých geografických oblastech po celém světě, aby splňovala požadavky zákazníků na výkon, požadavky a preference týkající se umístění dat. Zeměpisná oblast Azure je definovaná oblast světa, která obsahuje alespoň jednu oblast Azure. Oblast Azure je oblast v rámci geografické oblasti, která obsahuje jedno nebo více datových center.

Výběr oblasti Azure je velmi důležitý a volba oblasti je ovlivněna činiteli, jako je třeba umístění a svrchovanost dat, dostupnost služeb, výkon, náklady a redundance. Další informace o tom, jak zvolit oblast, najdete v článku o tom, [která oblast Azure je pro mě ta pravá?](https://azure.microsoft.com/overview/datacenters/how-to-choose/)

V případě řešení StorSimple je volba oblasti určena konkrétně následujícími faktory:

- Oblasti, ve kterých je dostupná služba StorSimple Správce zařízení.
- Země nebo oblasti, kde je k dispozici fyzický, Cloud nebo virtuální zařízení StorSimple.
- Pro optimální výkon by měly být umístěny oblasti, ve kterých jsou uloženy účty úložiště s StorSimple daty.

Tento kurz popisuje dostupnost oblasti pro službu StorSimple Správce zařízení, místní fyzické a cloudová zařízení. Informace obsažené v tomto článku se vztahují na zařízení StorSimple 8000 a 1200 Series.

## <a name="region-availability-for-storsimple-device-manager-service"></a>Dostupnost oblasti pro službu StorSimple Správce zařízení

Služba StorSimple Správce zařízení se v současné době podporuje ve 12 veřejných oblastech a 2 Azure Government oblastech.

Oblast nebo umístění definujete při prvním vytvoření služby StorSimple Správce zařízení. Obecně se volí umístění nejbližší k geografické oblasti, kde je zařízení nasazené. Zařízení a služba se ale dají nasadit i v různých umístěních.

Tady je seznam oblastí, kde je služba StorSimple Správce zařízení pro veřejný cloud Azure dostupná a dá se nasadit.

![Snímek obrazovky zobrazuje StorSimple Správce zařízení se zvýrazněným seznamem umístění.](./media/storsimple-region/storsimple-device-manager-service-regions.png)

Pro Azure Government Cloud je služba Správce zařízení StorSimple dostupná v datových centrech US Gov – Iowa a US Gov – Virginie.

## <a name="region-availability-for-data-stored-in-storsimple"></a>Dostupnost oblastí pro data uložená v StorSimple

Data StorSimple se fyzicky ukládají v účtech Azure Storage a tyto účty jsou dostupné ve všech oblastech Azure. Když vytvoříte účet úložiště Azure, vybere se primární umístění účtu úložiště a určí se oblast, kde se data nacházejí.

Když poprvé vytvoříte službu StorSimple Správce zařízení a přidružíte k ní účet úložiště, vaše služba StorSimple Správce zařízení a Azure Storage můžou být ve dvou různých umístěních. V takovém případě je nutné vytvořit Správce zařízení StorSimple a účet úložiště Azure odděleně.

V části Obecné vyberte do služby pro svůj účet úložiště nejbližší oblast. Nejbližší Microsoft Azure oblast ale nemusí být ve skutečnosti oblastí s nejnižší latencí. Jedná se o latenci, která určuje výkon síťové služby, a to i výkon řešení. Takže pokud zvolíte účet úložiště v jiné oblasti, je důležité znát, co jsou latence mezi vaší službou a oblastí přidruženou k vašemu účtu úložiště.

Pokud používáte StorSimple Cloud Appliance, doporučujeme, aby služba a přidružený účet úložiště byly ve stejné oblasti. Účty úložiště v jiné oblasti můžou mít za následek špatný výkon.

## <a name="availability-of-storsimple-device"></a>Dostupnost zařízení StorSimple

V závislosti na modelu mohou být zařízení StorSimple k dispozici v různých geografických oblastech nebo zemích nebo oblastech.

### <a name="storsimple-physical-device-models-81008600"></a>StorSimple fyzické zařízení (modely 8100/8600)

Pokud používáte fyzické zařízení s StorSimple 8100 nebo 8600, je zařízení dostupné v následujících zemích nebo oblastech.

| #  | Země/oblast        | #  | Země/oblast     | #  | Země/oblast      | #  | Země/oblast             |
|----|-----------------------|----|--------------------|----|---------------------|----|----------------------------|
| 1  | Austrálie             | 16 | Hongkong – zvláštní administrativní oblast      | 31 | Nový Zéland         | 46 | Jižní Afrika               |
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
| 13 | Francie                | 28 | Malajsie           | 43 | Singapur           | 58 | USA              |
| 14 | Německo               | 29 | Mexiko             | 44 | Slovensko            | 59 | Vietnam                    |
| 15 | Řecko                | 30 | Nizozemsko        | 45 | Slovinsko            | 60 | Chorvatsko                    |

Tento seznam se změní při přidání dalších zemí nebo oblastí. Nejaktuálnější seznam geografických oblastí najdete v dodatku k podmínkám pole úložiště v části s [podmínkami produktu](https://www.microsoft.com/en-us/licensing/product-licensing/products).

Společnost Microsoft může dodávat fyzický hardware a dodávat náhradní součásti hardwaru pro StorSimple do geografických oblastí v předchozím seznamu.

> [!IMPORTANT]
> StorSimple fyzické zařízení neumísťujte do oblasti, kde není podporována StorSimple. Společnost Microsoft nebude schopna dodávat žádné náhradní části do zemí nebo oblastí, kde StorSimple není podporováno.

### <a name="storsimple-cloud-appliance-models-80108020"></a>StorSimple Cloud Appliance (modely 8010/8020)

Pokud používáte StorSimple Cloud Appliance 8010 nebo 8020, bude zařízení podporované a dostupné ve všech oblastech, kde je podporovaný příslušný virtuální počítač. 8010 používá virtuální počítač _Standard_A3_ , který je podporovaný ve všech oblastech Azure.

8020 používá úložiště Premium a _Standard_DS3_ virtuální počítač k vytvoření cloudového zařízení. 8020 se podporuje v oblastech Azure, které podporují Premium Storage a _Standard_DS3_ virtuálních počítačů Azure. Použijte [tento seznam](https://azure.microsoft.com/regions/services/), abyste zjistili, jestli jsou ve vaší oblasti dostupné obě možnosti **Virtuální počítače > DS-series** a **Úložiště > Diskové úložiště**.

### <a name="storsimple-virtual-array-model-1200"></a>Virtuální pole StorSimple (model 1200)

Pokud používáte virtuální pole řady 1200 Series StorSimple, je image virtuálního disku podporovaná ve všech oblastech Azure.

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [cenách různých modelů StorSimple](https://azure.microsoft.com/pricing/calculator/#storsimple2).
* Přečtěte si další informace o [správě účtu úložiště StorSimple](storsimple-8000-manage-storage-accounts.md).
* Přečtěte si další informace o tom, jak [používat službu StorSimple Správce zařízení ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

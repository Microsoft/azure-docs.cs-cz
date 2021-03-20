---
title: Přehled Data Manager Microsoft Azure StorSimple | Microsoft Docs
description: Přečtěte si o řešení StorSimple Data Manager a o tom, jak tuto službu můžete použít k psaní aplikací, které používají StorSimple data a další služby Azure.
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/21/2018
ms.author: alkohli
ms.openlocfilehash: d683f49cadb384ef59d3bae819156733691813cd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88183492"
---
# <a name="storsimple-data-manager-solution-overview"></a>Přehled řešení StorSimple Data Manager

## <a name="overview"></a>Přehled

Microsoft Azure StorSimple používá cloudové úložiště jako rozšíření místního řešení a automaticky využívá data napříč místním úložištěm a cloudem. Data jsou ukládána v cloudu v případě maximální efektivity a komprimovaného formátu pro maximální efektivitu a snížení nákladů. Vzhledem k tomu, že jsou data uložená ve formátu StorSimple, není možné je snadno použít pro jiné cloudové aplikace, které můžete chtít použít.

StorSimple Data Manager vám umožní bezproblémově přistupovat k StorSimple formátu dat v cloudu a používat je. Dělá to tak, že transformuje formát StorSimple do nativních objektů BLOB a souborů, které můžete použít s jinými službami, jako jsou Azure Media Services, Azure HDInsights a Azure Machine Learning.

Tento článek poskytuje přehled řešení StorSimple Data Manager. Vysvětluje taky, jak můžete tuto službu použít k psaní aplikací, které používají StorSimple data a další služby Azure v cloudu.

## <a name="how-it-works"></a>Jak to funguje?

Služba StorSimple Data Manager identifikuje StorSimple data v cloudu z řady StorSimple 8000 na místním zařízení. StorSimple data v cloudu jsou deduplicitované, komprimovaný formát StorSimple. Služba Data Manager poskytuje rozhraní API pro extrakci dat formátu StorSimple a jejich transformaci do jiných formátů, jako jsou objekty blob Azure a soubory Azure. Tato transformovaná data pak budou snadno spotřebovaná službou Azure HDInsight a Azure Media Services. Transformace dat tak umožňuje, aby tyto služby pracovaly s transformovanými StorSimple daty z řady StorSimple 8000 na místním zařízení. Tento tok je znázorněný v následujícím diagramu.

![Diagram vysoké úrovně](./media/storsimple-data-manager-overview/storsimple-data-manager-overview2.png)


## <a name="data-manager-use-cases"></a>Případy použití Data Manager

Data Manager s Azure Functions, Azure Automation a Azure Data Factory můžete použít k tomu, aby byly pracovní postupy běžící na vašich datech, jak jsou součástí StorSimple. Můžete chtít zpracovat mediální obsah, který budete ukládat na StorSimple, pomocí Azure Media Services, nebo můžete pro tato data spustit algoritmus Machine Learning nebo vytvořit cluster Hadoop, který bude analyzovat data uložená v StorSimple. Díky rozsáhlému poli služeb, které jsou dostupné v Azure, v kombinaci s daty na StorSimple můžete odemknout sílu svých dat.


## <a name="region-availability"></a>Dostupnost v oblastech

StorSimple Data Manager je k dispozici v následujících 7 oblastech:

 - Southeast Asia
 - East US
 - USA – západ
 - Západní USA 2
 - USA – středozápad
 - Severní Evropa
 - West Europe

StorSimple Data Manager lze však použít k transformaci dat v následujících oblastech. 

![Oblasti dostupné pro data](./media/storsimple-data-manager-overview/data-manager-job-definition-different-regions-m.png)

Tato sada je větší, protože nasazení prostředků v některé z výše uvedených oblastí podporuje proces transformace v níže uvedených oblastech. Takže pokud se vaše data nacházejí v některé z 19 oblastí, můžete data transformovat pomocí této služby.


## <a name="choosing-a-region"></a>Výběr oblasti

Naše doporučení:
 - Váš zdrojový účet úložiště (ten přidružený k vašemu zařízení StorSimple) a cílový účet úložiště (kde mají být data v nativním formátu) ve stejné oblasti Azure.
 - Data Manager a definice úlohy se zobrazí v oblasti, která obsahuje účet úložiště StorSimple. Pokud to není možné, uveďte Data Manager v nejbližší oblasti Azure a pak vytvořte definici úlohy ve stejné oblasti jako účet úložiště StorSimple. 

    Pokud váš účet úložiště StorSimple není v 26 oblastech, které podporují vytvoření definice úlohy, doporučujeme, abyste nespouštěli StorSimple Data Manager, jak uvidíte dlouhou latenci a potenciální náklady na výstup.
    
Microsoft usiluje o to, aby byly služby Azure vždycky dostupné ve všech oblastech. Neplánované výpadky služby se ale můžou vyskytnout pro krátká období v určité oblasti. V takových případech můžete uvést Data Manager a definici úlohy v oblasti, která není ovlivněná výpadkem, a spustit úlohu transformace. V takovém případě může dojít k nějaké další latenci, ale může to být vaše strategie obnovení ve vzácných případech v oblasti regionu výpadku.

## <a name="security-considerations"></a>Důležité informace o zabezpečení

StorSimple Data Manager potřebuje šifrovací klíč dat služby k transformaci z formátu StorSimple do nativního formátu. Šifrovací klíč dat služby se vygeneruje, když se první zařízení zaregistruje ve službě StorSimple. Další informace o tomto klíči najdete v [StorSimple Security](storsimple-8000-security.md).

Šifrovací klíč dat služby, který jste zadali jako vstup, je uložený v trezoru klíčů, který se vytvoří při vytváření Data Manager. Trezor se nachází ve stejné oblasti Azure jako vaše StorSimple Data Manager. Tento klíč se odstraní při odstranění služby Data Manager.

Tento klíč se používá ve výpočetních prostředcích k provedení transformace. Tyto výpočetní prostředky se nachází ve stejné oblasti Azure jako vaše definice úlohy. Tato oblast může nebo nemusí být shodná s oblastí, ve které jste zavedli Data Manager.

Pokud je vaše oblast Data Manager odlišná od oblasti definice úlohy, je důležité pochopit, jaká data nebo metadata se nacházejí v každé z těchto oblastí. Následující diagram znázorňuje účinek používání různých oblastí Data Manager a definice úlohy.

![Definice služeb a úloh v různých oblastech](./media/storsimple-data-manager-overview/data-manager-job-different-regions.png)

## <a name="managing-personal-information"></a>Správa osobních údajů

StorSimple Data Manager neshromažďuje ani nezobrazuje žádné osobní údaje. Další informace najdete v Zásadách ochrany osobních údajů společnosti Microsoft v [Centru zabezpečení](https://www.microsoft.com/trustcenter).

## <a name="known-limitations"></a>Známá omezení

Služba má v současnosti tato omezení:
- StorSimple Data Manager aktuálně nepracuje se svazky, které jsou šifrovaný bitlockerem. Pokud se pokusíte spustit službu se zašifrovanou jednotkou, zobrazí se selhání úlohy.
- Některá metadata souborů (včetně seznamů ACL) se nebudou uchovávat v transformovaných datech.
- Tato služba funguje jenom se svazky NTFS.
- Délka cesty k souboru musí být kratší než 256 znaků, jinak se úloha nezdaří.

## <a name="next-steps"></a>Další kroky

[K transformaci dat použijte StorSimple data Manager uživatelské rozhraní](storsimple-data-manager-ui.md).

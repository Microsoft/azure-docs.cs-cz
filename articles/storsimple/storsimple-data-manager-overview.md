---
title: Přehled Microsoft Azure StorSimple Data Manager | Dokumentace Microsoftu
description: Poskytuje přehled služby Správce dat StorSimple
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
ms.author: vidarmsft
ms.openlocfilehash: 5845fd246b20d29739eb6d60bbc8621489ccc0d6
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39009165"
---
# <a name="storsimple-data-manager-solution-overview"></a>Přehled řešení StorSimple Data Manageru

## <a name="overview"></a>Přehled

Microsoft Azure StorSimple používá cloudové úložiště jako rozšíření místního řešení a automaticky úrovně dat napříč v místním úložišti a cloudu. Data se ukládají v cloudu ve formátu s odstraněnými duplicitami a komprimovaný maximální efektivitu a snížit náklady. Jak jsou data uložená ve formátu StorSimple, není snadno použitelné jiné cloudové aplikace, které chcete použít.

Správce dat StorSimple umožňuje bez problémů přistupovat a používat StorSimple formátování dat v cloudu. Dělá to pomocí transformace formátu StorSimple na nativní objekty BLOB a soubory, které můžete použít s jinými službami, jako jsou Azure Media Services, Azure HDInsights a Azure Machine Learning.

Tento článek obsahuje přehled řešení StorSimple Data Manageru. Také vysvětluje, jak lze pomocí této služby pro psaní aplikací, které používají StorSimple dat a dalšími službami Azure v cloudu.

## <a name="how-it-works"></a>Jak to funguje?

Služba StorSimple Data Manager identifikuje datům StorSimple v cloudu zařízení StorSimple řady 8000 místní. StorSimple data v cloudu je zajištěná, komprimované StorSimple formátu. Služba Data Manager poskytuje rozhraní API extrahovat StorSimple formátu data a transformovali je do jiných formátů, jako objekty BLOB Azure a službou soubory Azure. To transformuje data je pak snadno využívaná Azure HDInsight a Azure Media services. Transformace dat umožňuje proto tyto služby pro zpracování Transformovaná data z StorSimple 8000 series místního zařízení StorSimple. Tento tok je znázorněn v následujícím diagramu.

![Vysokoúrovňový diagram](./media/storsimple-data-manager-overview/storsimple-data-manager-overview2.png)


## <a name="data-manager-use-cases"></a>Případy použití Správce dat

Správce dat pomocí Azure Functions, Azure Automation a Azure Data Factory můžete mít pracovních postupů spuštěných na data po jejich vstupu do StorSimple. Můžete chtít zpracovat mediálního obsahu ukládat na StorSimple se službou Azure Media Services, nebo spustit algoritmus strojového učení na těchto datech nebo převést cluster Hadoop k analýze dat, které ukládáte na StorSimple. Pomocí velkého množství služeb, které jsou k dispozici v Azure v kombinaci s daty na StorSimple můžete odemknout sílu vaše data.


## <a name="region-availability"></a>Dostupnost v oblastech

Správce dat StorSimple je dostupná v následujících 7 oblastech:

 - Jihovýchodní Asie
 - USA – východ
 - USA – západ
 - USA – západ 2
 - Západní střed USA
 - Severní Evropa
 - Západní Evropa

Však správce dat StorSimple umožňuje transformovat data v těchto oblastech. 

![Pro data dostupných oblastí](./media/storsimple-data-manager-overview/data-manager-job-definition-different-regions-m.png)

Tato sada je větší, protože je schopen spustit až proces transformace v nasazení prostředků v některém z výše uvedených oblastech pod oblastí. Ano tak dlouho, dokud máte data uložená v některém z 19 oblastí, můžete transformaci dat pomocí této služby.


## <a name="choosing-a-region"></a>Výběr oblasti

Doporučujeme:
 - Váš účet úložiště zdroje (je přidružený k zařízení StorSimple) a cílový účet úložiště (ve kterém chcete daná data v nativním formátu) být ve stejné oblasti Azure.
 - Použijete v oblasti, která obsahuje účet úložiště StorSimple Data Manager a úlohy definice. Pokud to není možné přenést do Správce dat v nejbližší oblasti Azure a pak vytvořte definici úlohy ve stejné oblasti jako váš účet úložiště StorSimple. 

    Pokud váš účet úložiště StorSimple není v 26 oblastech, které podporují vytváření definice úlohy, doporučujeme, abyste jako uvidíte dlouhá latenci a potenciálně velkému spojeným nelze spustit Správce dat StorSimple.

## <a name="security-considerations"></a>Aspekty zabezpečení

Správce dat StorSimple musí šifrovací klíč dat služby k transformaci z formátu StorSimple k nativním formátu. Šifrovací klíč dat služby se vygeneruje, když je první zařízení zaregistruje ve službě StorSimple. Další informace o tomto klíči najdete v části [zabezpečení zařízení StorSimple](storsimple-8000-security.md).

Šifrovací klíč dat služby k dispozici jako vstup je uložené v trezoru klíčů, který je vytvořen při vytváření Data Manager. Trezor se nachází ve stejné oblasti Azure jako správce dat StorSimple. Tento klíč je odstraněna při odstranění služby Správce Data.

Tento klíč se používá ve výpočetní prostředky k provedení transformace. Tyto výpočetní prostředky jsou umístěny ve stejné oblasti Azure jako vaší definice úlohy. Tuto oblast může nebo nemusí být stejná jako oblast, kde přinesete si vaše Data Manager.

Pokud vaše oblast správce dat se liší od vaší oblasti definice úlohy, je důležité pochopit, jaká data nebo metadata se nachází v každém z těchto oblastí. Následující diagram znázorňuje vliv mají různé oblasti pro definici správce dat a úlohy.

![Definice služby a úlohy v různých oblastech](./media/storsimple-data-manager-overview/data-manager-job-different-regions.png)

## <a name="managing-personal-information"></a>Správa osobních údajů

StorSimple Data Manager neshromažďuje ani zobrazit žádné osobní údaje. Další informace najdete v tématu Zásady Microsoft Privacy na [centrum](https://www.microsoft.com/trustcenter).

## <a name="known-limitations"></a>Známá omezení

Službu aktuálně má následující omezení:
- StorSimple Data Manager aktuálně nefunguje s svazky, které jsou šifrovaná nástrojem bitlocker. Selhání úlohy se zobrazí, pokud se pokusíte spustit službu s šifrované jednotce.
- V Transformovaná data nebudou uchována některých metadat souborů (včetně seznamy ACL).
- Tato služba funguje pouze s svazky systému souborů NTFS.
- Délky cesty souboru musí být jiný méně než 256 znaků. úloha se nezdaří.

## <a name="next-steps"></a>Další postup

[Použití StorSimple Data Manager uživatelského rozhraní pro transformaci dat](storsimple-data-manager-ui.md).

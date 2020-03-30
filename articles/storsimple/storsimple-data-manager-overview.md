---
title: Microsoft Azure StorSimple Správce dat – přehled | Dokumenty společnosti Microsoft
description: Poskytuje přehled služby StorSimple Data Manager.
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
ms.openlocfilehash: 2ffe17bf7ef4f01c18d2c26f4a045add7302272d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67876102"
---
# <a name="storsimple-data-manager-solution-overview"></a>Přehled řešení StorSimple Data Manager

## <a name="overview"></a>Přehled

Microsoft Azure StorSimple využívá cloudové úložiště jako rozšíření místního řešení a automaticky vrství data napříč místním úložištěm a cloudem. Data jsou uložena v cloudu v depozitovaném a komprimovaném formátu pro maximální efektivitu a nižší náklady. Vzhledem k tomu, že data jsou uložena ve formátu StorSimple, není snadno použitelná jinými cloudovými aplikacemi, které budete chtít použít.

Správce dat StorSimple umožňuje bezproblémový přístup a použití dat formátu StorSimple v cloudu. Je to tím, že transformuje Formát StorSimple na nativní objekty BLOB a soubory, které můžete použít s jinými službami, jako jsou Azure Media Services, Azure HDInsights a Azure Machine Learning.

Tento článek obsahuje přehled řešení StorSimple Data Manager. Také vysvětluje, jak můžete tuto službu použít k zápisu aplikací, které používají data StorSimple a další služby Azure v cloudu.

## <a name="how-it-works"></a>Jak to funguje?

Služba StorSimple Data Manager identifikuje data StorSimple v cloudu z místního zařízení řady StorSimple 8000. StorSimple data v cloudu je deduped, komprimovaný Formát StorSimple. Služba Data Manager poskytuje api extrahovat storSimple formát dat a transformovat je do jiných formátů, jako jsou objekty BLOB Azure a Soubory Azure. Tato transformovaná data se pak snadno spotřebovávají službami Azure HDInsight a Azure Media. Transformace dat tak umožňuje tyto služby pracovat s transformovaná Data StorSimple z řady StorSimple 8000 místní zařízení. Tento tok je znázorněn o následujícím diagramu.

![Diagram na vysoké úrovni](./media/storsimple-data-manager-overview/storsimple-data-manager-overview2.png)


## <a name="data-manager-use-cases"></a>Případy použití správce dat

Pomocí Správce dat s funkcemi Azure, Azure Automation a Azure Data Factory můžete mít pracovní postupy spuštěné na vašich datech, jakmile se dostane do StorSimple. Můžete chtít zpracovat mediální obsah, který ukládáte na StorSimple s Azure Media Services nebo spustit algoritmus machine learningu na tato data nebo vyvolat cluster Hadoop k analýze dat, která ukládáte na StorSimple. Díky obrovské škále služeb dostupných v Azure v kombinaci s daty na StorSimple můžete odemknout výkon dat.


## <a name="region-availability"></a>Dostupnost v oblastech

Správce dat StorSimple je k dispozici v následujících 7 oblastech:

 - Jihovýchodní Asie
 - USA – východ
 - USA – západ
 - USA – západ 2
 - USA – středozápad
 - Severní Evropa
 - Západní Evropa

Správce dat StorSimple však lze použít k transformaci dat v následujících oblastech. 

![Oblasti dostupné pro data](./media/storsimple-data-manager-overview/data-manager-job-definition-different-regions-m.png)

Tato sada je větší, protože nasazení prostředků v některé z výše uvedených oblastí je schopen vyvolat proces transformace v níže uvedených oblastech. Takže tak dlouho, dokud vaše data jsou umístěny v některém z 19 oblastí, můžete transformovat data pomocí této služby.


## <a name="choosing-a-region"></a>Výběr oblasti

Naše doporučení:
 - Váš účet zdrojového úložiště (účet přidružený k vašemu zařízení StorSimple) a cílový účet úložiště (kde chcete data v nativním formátu) být ve stejné oblasti Azure.
 - Můžete vyvolat správce dat a definice úlohy v oblasti, která obsahuje účet úložiště StorSimple. Pokud to není možné, zaregistrujte Správce dat v nejbližší oblasti Azure a pak vytvořte definici úlohy ve stejné oblasti jako váš účet úložiště StorSimple. 

    Pokud váš účet úložiště StorSimple není ve 26 oblastech, které podporují vytváření definic úloh, doporučujeme nespouštět StorSimple Data Manager, protože se zobrazí dlouhé latence a potenciální odchozí poplatky.
    
Microsoft se snaží zajistit, aby služby Azure byly vždy dostupné ve všech oblastech. K neplánovaným výpadkům služeb však může v určité oblasti dojít na krátkou dobu. V takových případech můžete vyvolat správce dat a definici úlohy v oblasti, která není ovlivněna výpadku a spustit úlohu transformace. V takovém scénáři může dojít k nějaké další latenci, ale může to být vaše strategie obnovení ve výjimečných případech regionálního výpadku.

## <a name="security-considerations"></a>Důležité informace o zabezpečení

Správce dat StorSimple potřebuje šifrovací klíč dat služby k transformaci z formátu StorSimple do nativního formátu. Šifrovací klíč dat služby je generován při registraci prvního zařízení se službou StorSimple. Další informace o tomto klíči naleznete v tématu [Zabezpečení StorSimple](storsimple-8000-security.md).

Šifrovací klíč dat služby poskytnutý jako vstup je uložen v trezoru klíčů, který je vytvořen při vytváření Správce dat. Trezor se nachází ve stejné oblasti Azure jako váš Správce dat StorSimple. Tento klíč se odstraní při odstranění služby Správce dat.

Tento klíč se používá výpočetní prostředky k provedení transformace. Tyto výpočetní prostředky se nacházejí ve stejné oblasti Azure jako definice úlohy. Tato oblast může nebo nemusí být stejná jako oblast, kde můžete vyvolat správce dat.

Pokud se oblast správce dat liší od oblasti definice úlohy, je důležité, abyste pochopili, jaká data nebo metadata jsou uložena v každé z těchto oblastí. Následující diagram znázorňuje účinek mají různé oblasti pro Správce dat a definice úlohy.

![Služba a definice práce v různých regionech](./media/storsimple-data-manager-overview/data-manager-job-different-regions.png)

## <a name="managing-personal-information"></a>Správa osobních údajů

Správce dat StorSimple neshromažďuje ani nezobrazuje žádné osobní údaje. Další informace najdete v Zásadách ochrany osobních údajů společnosti Microsoft v [Centru zabezpečení](https://www.microsoft.com/trustcenter).

## <a name="known-limitations"></a>Známá omezení

Služba má aktuálně následující omezení:
- Správce dat StorSimple aktuálně nefunguje se svazky, které jsou zašifrovány nástrojem BitLocker. Pokud se pokusíte spustit službu se šifrovanou jednotkou, zobrazí se selhání úloh.
- Některá metadata souborů (včetně seznamů AC) nebudou v transformovaných datech zachována.
- Tato služba funguje pouze se svazky NTFS.
- Délka cesty k souboru musí být menší než 256 znaků, jinak úloha selže.

## <a name="next-steps"></a>Další kroky

[Pomocí ui Správce dat StorSimple pro transformaci dat](storsimple-data-manager-ui.md).

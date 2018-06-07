---
title: Přehled Microsoft Azure StorSimple Data Manager | Microsoft Docs
description: Obsahuje přehled služby StorSimple Manager dat
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
ms.openlocfilehash: d57229ad79909aa0334cc623d727b733a1ec73f9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34652004"
---
# <a name="storsimple-data-manager-solution-overview"></a>Přehled řešení StorSimple Manager dat

## <a name="overview"></a>Přehled

Microsoft Azure StorSimple používá úložiště v cloudu jako rozšíření v případě místních řešení a automaticky úrovně dat mezi místní úložiště a cloudem. Data se ukládají v cloudu ve formátu odstraněnou a komprimované pro maximální efektivity a snížení nákladů. Jak jsou data uložena ve formátu StorSimple, není snadno použití jiné cloudové aplikace, které chcete použít.

Data StorSimple Manager umožňuje bezproblémově přístup a použít StorSimple formátování dat v cloudu. Dělá to pomocí transformace StorSimple formátu do nativní objektů BLOB a soubory, které můžete používat s dalšími službami, například Azure Media Services, Azure HDInsights a Azure Machine Learning.

Tento článek obsahuje přehled řešení StorSimple Data Manager. Také vysvětluje, jak lze pomocí této služby pro psaní aplikací, které používají StorSimple data a jinými službami Azure v cloudu.

## <a name="how-it-works"></a>Jak to funguje?

Služba StorSimple Data Manager identifikuje StorSimple data v cloudu na místní zařízení řady StorSimple 8000. StorSimple dat v cloudu je zajištěná, komprimované StorSimple formátu. Služba Data Manager poskytuje rozhraní API pro extrahovat StorSimple formátu data a transformují je do jiných formátů, jako je objektů BLOB Azure a Azure Files. To transformovat je pak snadno spotřebovávají data Azure HDInsight a Azure Media services. Transformace dat umožňuje proto tyto služby pro zpracování Transformovaná data z místního zařízení StorSimple 8000 řady StorSimple. Tento tok je znázorněno v následujícím diagramu.

![Vysokoúrovňový diagram](./media/storsimple-data-manager-overview/storsimple-data-manager-overview2.png)


## <a name="data-manager-use-cases"></a>Případy použití data Manager

Správce dat pomocí funkce Azure, Azure Automation a Azure Data Factory slouží k mít pracovních postupů spuštěných na vaše data, jak jde do zařízení StorSimple. Můžete chtít zpracovat médiu obsah uložit na StorSimple pomocí služby Azure Media Services, nebo spustit algoritmus Machine Learning na data nebo zprovoznit clusteru Hadoop k analýze dat, která ukládáte na StorSimple. S záplavách služeb dostupných v kombinaci s daty na StorSimple Azure můžete odemknout power vaše data.


## <a name="region-availability"></a>Dostupnost v oblastech

Data Manager zařízení StorSimple je k dispozici v následujících oblastech 7:

 - Jihovýchodní Asie
 - Východ USA
 - Západní USA
 - Západní USA 2
 - Západní střed USA
 - Severní Evropa
 - Západní Evropa

Ale StorSimple Manager dat slouží k transformaci dat v následujících oblastech. 

![Oblasti, které jsou dostupné pro data](./media/storsimple-data-manager-overview/data-manager-job-definition-different-regions-m.png)

Tato sada je větší, protože je dát uvést až proces transformace v nasazení prostředků v některém z výše uvedených oblastí pod oblastí. Ano tak dlouho, dokud máte data uložená v některém z 19 oblasti, můžete transformaci dat pomocí této služby.


## <a name="choosing-a-region"></a>Výběr oblasti

Doporučujeme, aby:
 - Váš účet úložiště (ten spojené s vaším zařízením StorSimple) zdrojového a cílového účtu úložiště (kde chcete data v nativním formátu) být ve stejné oblasti Azure.
 - Zprovoznit svou definici. správce dat a úlohy v oblasti, která obsahuje účet úložiště StorSimple. Pokud to není možné, uveďte do Správce dat v nejbližší oblast Azure a pak ve stejné oblasti jako váš účet úložiště StorSimple vytvořit definici úlohy. 

    Pokud váš účet úložiště StorSimple není v 26 oblastí, které podporují vytvoření definice úlohy, doporučujeme vám spustit StorSimple Manager dat, není jako zobrazí dlouho latenci a potenciálně vysoké nimi spojeným nákladům.

## <a name="security-considerations"></a>Aspekty zabezpečení

Data Manager zařízení StorSimple musí šifrovacího klíče dat služby k transformaci z formátu StorSimple na nativní formát. Šifrovací klíč dat služby se vygeneruje, když první zařízení zaregistruje ve službě StorSimple. Další informace pro tento klíč, přejděte na [zabezpečení zařízení StorSimple](storsimple-8000-security.md).

Šifrovací klíč dat služby zadaný jako vstup je uložené v trezoru klíčů, který se vytvoří při vytváření Data Manager. Úložiště se nachází ve stejné oblasti Azure jako vaše StorSimple Data Manager. Tento klíč je odstraněna při odstranění služby Data Manager.

Tento klíč se používá ve výpočetní prostředky k provedení transformace. Tyto výpočetní prostředky jsou umístěny ve stejné oblasti Azure jako svou definici úlohy. Tato oblast může nebo nemusí být stejný jako oblasti, kde přineste si vaše Data Manager.

Pokud vaše Data Manager oblasti se liší od vaší oblasti definice úlohy, je důležité pochopit, jaká data nebo metadata se nachází v každé z těchto oblastí. Následující diagram znázorňuje účinek použití různých oblastech pro definici Data Manager a úlohy.

![Definice služby a úlohy v různých oblastech](./media/storsimple-data-manager-overview/data-manager-job-different-regions.png)

## <a name="managing-personal-information"></a>Správa osobní údaje

Data Manager zařízení StorSimple neshromažďuje ani zobrazí nějaké osobní informace. Další informace najdete v tématu Zásady Microsoft Privacy v [Centrum zabezpečení](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Další postup

[Data Manager zařízení StorSimple pomocí uživatelského rozhraní pro transformaci dat](storsimple-data-manager-ui.md).

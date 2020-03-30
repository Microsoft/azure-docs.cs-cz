---
title: Případ použití – profilování odběratele
description: Zjistěte, jak se Azure Data Factory používá k vytvoření pracovního postupu (kanálu) řízeného daty pro zákazníky s profilovými herními profily.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: c570f988dea894b8106405f4e427edb386a3e74a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75969291"
---
# <a name="use-case---customer-profiling"></a>Případ použití – profilování odběratele
Azure Data Factory je jednou z mnoha služeb používaných k implementaci akcelerátorů řešení Cortana Intelligence Suite.  Další informace o Cortana Intelligence najdete na stránce [Cortana Intelligence Suite](https://www.microsoft.com/cortanaanalytics). V tomto dokumentu popisujeme jednoduchý případ použití, který vám pomůže začít s pochopením toho, jak Azure Data Factory dokáže vyřešit běžné problémy s analýzou.

## <a name="scenario"></a>Scénář
Contoso je herní společnost, která vytváří hry pro více platforem: herní konzole, ruční zařízení a osobní počítače (PC). Jako hráči hrají tyto hry, velký objem dat protokolu je produkován, který sleduje vzorce použití, herní styl a preference uživatele.  V kombinaci s demografickými, regionálními a produktovými daty může společnost Contoso provádět analýzy, které je budou řídit tím, jak zlepšit zkušenosti hráčů a zacílit je na upgrady a nákupy ve hře. 

Cílem společnosti Contoso je identifikovat příležitosti k up-sell /cross-sell na základě herní historie svých hráčů a přidat přesvědčivé funkce, které pohánějí růst podnikání a poskytují zákazníkům lepší zážitek. V tomto případě použití používáme herní společnost jako příklad podnikání. Společnost chce optimalizovat své hry na základě chování hráčů. Tyto zásady se vztahují na všechny podniky, které chtějí zaujmout své zákazníky kolem svého zboží a služeb a zlepšit zkušenosti svých zákazníků.

V tomto řešení chce společnost Contoso vyhodnotit efektivitu marketingové kampaně, kterou nedávno spustila. Začneme s nezpracovanými herními protokoly, zpracováváme je a obohacujeme o geolokační data, spojujeme je s referenčními daty o reklamě a nakonec je zkopírujeme do databáze Azure SQL Database, abychom analyzovali dopad kampaně.

## <a name="deploy-solution"></a>Nasadit řešení
Jediné, co potřebujete k přístupu a vyzkoušení tohoto případu jednoduchého použití, je [předplatné Azure](https://azure.microsoft.com/pricing/free-trial/), účet úložiště [objektů Blob Azure](../../storage/common/storage-account-create.md)a Azure SQL [Database](../../sql-database/sql-database-get-started.md). Kanál profilování zákazníků nasadíte z dlaždice **Ukázkové kanály** na domovské stránce vaší datové továrny.

1. Vytvořte datovou továrnu nebo otevřete existující datovou továrnu. Postup vytvoření datové továrny najdete v tématu [Kopírování dat z úložiště objektů blob do databáze SQL pomocí Data Factory.](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
2. V okně **DATA FACTORY** pro datovou továrnu klikněte na dlaždici **Ukázkové kanály.**

    ![Ukázková dlaždice potrubí](./media/data-factory-samples/SamplePipelinesTile.png)
3. V okně **Ukázkové kanály** klikněte na **profilování zákazníka,** které chcete nasadit.

    ![Vzorové potrubí čepel](./media/data-factory-samples/SampleTile.png)
4. Určete nastavení konfigurace pro ukázku. Například název a klíč účtu úložiště Azure, název serveru Azure SQL, databáze, ID uživatele a heslo.

    ![Čepel vzorku](./media/data-factory-samples/SampleBlade.png)
5. Po dokončení s určením nastavení konfigurace, klepněte na tlačítko **Vytvořit** vytvořit nebo nasadit ukázkové kanály a propojené služby/tabulky používané kanály.
6. Stav nasazení se zobrazí na ukázkové dlaždici, na kterou jste klikli dříve v okně **Ukázkové kanály.**

    ![Stav nasazení](./media/data-factory-samples/DeploymentStatus.png)
7. Když se zobrazí **zpráva deployment succeeded** na dlaždici pro ukázku, zavřete okno **ukázkové kanály.**  
8. V okně **DATA FACTORY** uvidíte, že propojené služby, datové sady a kanály jsou přidány do vaší datové továrny.  

    ![Okno Objekt pro vytváření dat](./media/data-factory-samples/DataFactoryBladeAfter.png)

## <a name="solution-overview"></a>Přehled řešení
Tento jednoduchý případ použití lze použít jako příklad, jak můžete použít Azure Data Factory k ingestování, přípravě, transformaci, analýze a publikování dat.

![Ucelený pracovní postup](./media/data-factory-customer-profiling-usecase/EndToEndWorkflow.png)

Tento obrázek znázorňuje, jak se datové kanály zobrazí na webu Azure Portal po jejich nasazení.

1. **PartitionGameLogsPipeline** čte nezpracované herní události z úložiště objektů blob a vytváří oddíly na základě roku, měsíce a dne.
2. **EnrichGameLogsPipeline** spojuje rozdělené herní události s referenčními daty geografického kódu a obohacuje data mapováním IP adres na odpovídající geografická umístění.
3. Kanál **AnalyzeMarketingCampaignPipeline** používá obohacená data a zpracovává je s reklamními daty k vytvoření konečného výstupu, který obsahuje efektivitu marketingové kampaně.

V tomto příkladu data factory se používá k orchestrací aktivity, které kopírují vstupní data, transformovat a zpracovávat data a výstup konečných dat do databáze Azure SQL.  Můžete také vizualizovat síť datových kanálů, spravovat je a sledovat jejich stav z uj.

## <a name="benefits"></a>Výhody
Optimalizací analýzy uživatelských profilů a jejím sladěním s obchodními cíli je herní společnost schopna rychle shromažďovat vzorce používání a analyzovat účinnost svých marketingových kampaní.


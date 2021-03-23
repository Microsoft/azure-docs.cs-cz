---
title: Případ použití – profilace zákazníků
description: Přečtěte si, jak Azure Data Factory použít k vytvoření pracovního postupu řízeného daty (kanálu) pro profilování herních zákazníků.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: b8193039edd21db5a52eabca37c1e61880e49cfe
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2021
ms.locfileid: "104779745"
---
# <a name="use-case---customer-profiling"></a>Případ použití – profilace zákazníků
Azure Data Factory je jedna z mnoha služeb, které se používají k implementaci Cortana Intelligence Suite akcelerátorů řešení.  Další informace o Cortana Intelligence najdete v [Cortana Intelligence Suite](https://www.microsoft.com/cortanaanalytics). V tomto dokumentu popisujeme jednoduchý případ použití, který vám pomůžeme začít s porozuměním, jak Azure Data Factory řešit běžné problémy s analýzou.

## <a name="scenario"></a>Scenario
Contoso je herní společnost, která vytváří hry pro různé platformy: herní konzoly, ruční zařízení a osobní počítače (počítače). Když hráči hrají tyto hry, je vytvořen velký objem dat protokolu, který sleduje vzorce používání, herní styl a předvolby uživatele.  V kombinaci s demografickými, regionálními a produktovými daty může společnost Contoso provádět analýzy, aby je bylo možné vylepšit na základě zkušeností hráčů a zaměřit se na upgrady a nákupy v herním prostředí. 

Cílem společnosti Contoso je identifikovat příležitosti pro prodej/prodej v závislosti na historii herních přehrávačů a přidat působivé funkce pro zajištění obchodního růstu a poskytování lepší zkušenosti zákazníkům. Pro účely tohoto případu použití používáme herní společnost jako příklad firmy. Společnost chce optimalizovat své hry na základě chování přehrávačů. Tyto zásady se vztahují na všechny firmy, které chtějí zapojit své zákazníky do svého zboží a služeb a zdokonalit možnosti jejich zákazníků.

V tomto řešení contoso chce vyhodnotit efektivitu marketingové kampaně, kterou nedávno spustili. Začali jsme s nezpracovanými herními protokoly, zpracovávat je a rozšiřovat je pomocí dat geografického umístění, spojit je s reklamními referenčními daty a nakonec je zkopírovat do Azure SQL Database a analyzovat dopad kampaně.

## <a name="deploy-solution"></a>Nasadit řešení
K tomuto jednoduchému případu použití stačí získat přístup a vyzkoušet si tento jednoduchý případ použití je [předplatné Azure](https://azure.microsoft.com/pricing/free-trial/), [účet úložiště azure BLOB](../../storage/common/storage-account-create.md)a [Azure SQL Database](../../azure-sql/database/single-database-create-quickstart.md). Kanál profilace zákazníka nasadíte z dlaždice **ukázek kanálů** na domovské stránce vaší datové továrny.

1. Vytvořte datovou továrnu nebo otevřete existující datovou továrnu. Postup vytvoření datové továrny najdete v tématu [kopírování dat z BLOB Storage. SQL Database pomocí Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .
2. V okně **objekt** pro vytváření dat pro objekt pro vytváření dat klikněte na dlaždici **ukázkové kanály** .

    ![Dlaždice ukázek kanálů](./media/data-factory-samples/SamplePipelinesTile.png)
3. V okně **ukázkové kanály** klikněte na **profilování zákazníků** , které chcete nasadit.

    ![Okno ukázkové kanály](./media/data-factory-samples/SampleTile.png)
4. Zadejte konfigurační nastavení pro ukázku. Například název a klíč účtu úložiště Azure, název logického SQL serveru, databáze, ID uživatele a heslo.

    ![Okno vzorku](./media/data-factory-samples/SampleBlade.png)
5. Po dokončení zadání nastavení konfigurace kliknutím na **vytvořit** vytvořte nebo nasadíte ukázkové kanály a propojené služby nebo tabulky používané kanály.
6. Stav nasazení se zobrazí na dlaždici ukázka, na kterou jste klikli v okně **ukázkové kanály** .

    ![Stav nasazení](./media/data-factory-samples/DeploymentStatus.png)
7. Po zobrazení zprávy o **úspěšném nasazení** na dlaždici pro ukázku zavřete okno **ukázkové kanály** .  
8. V okně **Data Factory** vidíte, že propojené služby, datové sady a kanály se přidávají do vaší datové továrny.  

    ![Okno Objekt pro vytváření dat](./media/data-factory-samples/DataFactoryBladeAfter.png)

## <a name="solution-overview"></a>Přehled řešení
Tento jednoduchý případ použití lze použít jako příklad toho, jak můžete použít Azure Data Factory k ingestování, přípravě, transformaci, analýze a publikování dat.

![Ucelený pracovní postup](./media/data-factory-customer-profiling-usecase/EndToEndWorkflow.png)

Tento obrázek znázorňuje, jak se datové kanály po nasazení zobrazí v Azure Portal.

1. **PartitionGameLogsPipeline** přečte nezpracované herní události ze služby Blob Storage a vytvoří oddíly na základě roku, měsíce a dne.
2. **EnrichGameLogsPipeline** spojuje herní události dělené pomocí referenčních dat geografického kódu a vylepšuje data mapováním IP adres na odpovídající geografická umístění.
3. Kanál **AnalyzeMarketingCampaignPipeline** využívá obohacená data a zpracovává je s reklamními daty a vytváří konečný výstup, který obsahuje efektivitu marketingové kampaně.

V tomto příkladu Data Factory slouží k orchestraci aktivit, které kopírují vstupní data, transformují a zpracovávají data a výstupují konečná data do Azure SQL Database.  Můžete také vizualizovat síť datových kanálů, spravovat je a monitorovat jejich stav z uživatelského rozhraní.

## <a name="benefits"></a>Výhody
Díky optimalizaci svých uživatelských profilů a jejich vyrovnání s obchodními cíli může herní společnost rychle shromažďovat vzorce používání a analyzovat efektivitu svých marketingových kampaní.
---
title: Příklad použití – profilace zákazníka
description: Zjistěte, jak se Azure Data Factory používá k vytvoření postupu řízeného daty (kanálu) Chcete-li Profilovat herní zákazníků.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: e07d55cf-8051-4203-9966-bdfa1035104b
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: b60e4f3547e049dc35c08ce115bdfcbbf6ebb18a
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2018
ms.locfileid: "42057215"
---
# <a name="use-case---customer-profiling"></a>Příklad použití – profilace zákazníka
Azure Data Factory je jednou z mnoha služeb používaný k implementaci akcelerátorů řešení Cortana Intelligence Suite.  Další informace o Cortana Intelligence [Cortana Intelligence Suite](http://www.microsoft.com/cortanaanalytics). V tomto dokumentu popisujeme jednoduchý případ použití pro zahájení práce při pochopení, jak Azure Data Factory můžete vyřešit běžné problémy analýzy.

## <a name="scenario"></a>Scénář
Contoso je společnost vyrábějící, která vytvoří her pro různé platformy: her konzol, dlaně zařízení a osobních počítačů (počítače). Jak hráči hraní her, je vytvořen velkého objemu dat protokolu, který sleduje vzorce používání, herní stylu a předvolby uživatele.  V kombinaci s demografické, místní a data produktu Contoso můžete provádět analýzy a provede je o tom, jak zlepšit přimět hráče zahrát prostředí a nákupech cíl pro inovace a hry. 

Cílem společnosti Contoso je a odhalte příležitosti nahoru – prodej a následného prodeje na základě historie herní své hráče a přidejte zajímavé funkce podporující obchodní růst a poskytovat lepší prostředí pro zákazníky. Pro tento případ použití používáme jako příklad podniku herní společnost. Společnost chce k optimalizaci svých her na základě chování hráčů. Tyto zásady platí i pro jakoukoli firmu, která chce zapojily své zákazníky po jeho zboží a služeb a zajištění lepších možností svým zákazníkům.

V tomto řešení Contoso chce zhodnotit účinnost marketingovou kampaň, která byla nedávno spuštěna. Jsme začínat herní nezpracovaných protokolů, zpracování a rozšiřovat s geografická data, připojte ho s inzerování referenčních dat a nakonec zkopírujte je do Azure SQL Database a analyzovat dopad na kampaň.

## <a name="deploy-solution"></a>Nasazení řešení
Všechno, co potřebujete pro přístup a vyzkoušejte si tento jednoduchý případ použití je [předplatného Azure](https://azure.microsoft.com/pricing/free-trial/), [účtu úložiště objektů Blob v Azure](../../storage/common/storage-quickstart-create-account.md)a [Azure SQL Database](../../sql-database/sql-database-get-started.md). Nasazení z kanálu – profilace zákazníka **ukázkové kanály** dlaždici na domovské stránce vaší datové továrně.

1. Vytvořte objekt pro vytváření dat nebo otevřete existující datové továrny. Zobrazit [kopírování dat z Blob Storage do SQL Database s použitím služby Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) pokyny k vytvoření datové továrny.
2. V **služby DATA FACTORY** okno služby data factory, klikněte **ukázkové kanály** dlaždici.

    ![Ukázkové kanály dlaždice](./media/data-factory-samples/SamplePipelinesTile.png)
3. V **ukázkové kanály** okna, klikněte na tlačítko **profilace zákazníka** , kterou chcete nasadit.

    ![Okno ukázkové kanály](./media/data-factory-samples/SampleTile.png)
4. Určení nastavení konfigurace pro vzorku. Například váš název účtu služby Azure storage a klíč, název serveru Azure SQL, databáze, ID uživatele a heslo.

    ![Okno ukázkové](./media/data-factory-samples/SampleBlade.png)
5. Jakmile budete hotovi s určení nastavení konfigurace, klikněte na tlačítko **vytvořit** vytvořit a nasadit ukázkové kanály a propojených služeb/tabulek používají kanály.
6. Zobrazí stav nasazení na dlaždici ukázka předtím jste klikli **ukázkové kanály** okno.

    ![Stav nasazení](./media/data-factory-samples/DeploymentStatus.png)
7. Když se zobrazí **nasazení bylo úspěšné** zprávy na dlaždici pro ukázku, zavřete **ukázkové kanály** okno.  
8. Na **služby DATA FACTORY** okně uvidíte, že propojené služby, datové sady a kanály jsou přidány do služby data factory.  

    ![Okno Objekt pro vytváření dat](./media/data-factory-samples/DataFactoryBladeAfter.png)

## <a name="solution-overview"></a>Přehled řešení
Tento jednoduchý případ použití slouží jako příklad použití Azure Data Factory k ingestování, Příprava, transformaci, analyzovat a publikovat data.

![Ucelený pracovní postup](./media/data-factory-customer-profiling-usecase/EndToEndWorkflow.png)

Tento obrázek znázorňuje, jak datové kanály se zobrazí na webu Azure Portal po jejich nasazení.

1. **PartitionGameLogsPipeline** přečte herních nezpracovaných událostí z úložiště objektů blob a vytvoří oddíly používající rok, měsíc a den.
2. **EnrichGameLogsPipeline** připojí dělené herních událostí s referenčními daty geograficky kódu a vylepšuje data pomocí mapování IP adres na odpovídající geografické umístění.
3. **AnalyzeMarketingCampaignPipeline** kanál používá bohatších možností data a procesy s reklamy data k vytvoření konečného výstupu, který obsahuje efektivity marketingové kampaně.

V tomto příkladu se používá služby Data Factory k orchestraci aktivit, které zkopírujte vstupních dat, transformace a zpracovávat je a výstupní poslední data ke službě Azure SQL Database.  Můžete také vizualizovat síť datových kanálů, spravovat a monitorovat jejich stav v uživatelském rozhraní.

## <a name="benefits"></a>Výhody
Optimalizace jejich profil analýzy chování uživatelů a zarovnání s obchodními cíli, herní společnost se rychle shromažďovat vzorce používání a analyzovat efektivity marketingové kampaně.


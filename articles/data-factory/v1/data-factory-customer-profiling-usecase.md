---
title: Příklad použití – profilace zákazníka
description: Zjistěte, jak Azure Data Factory slouží k vytvoření datové pracovního postupu (kanál) profilu herní zákazníků.
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
ms.openlocfilehash: cc9dad4584c8edc47181e4a73ffe11a2e08de2f1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34620808"
---
# <a name="use-case---customer-profiling"></a>Příklad použití – profilace zákazníka
Azure Data Factory je jedním z mnoha služby použít k implementaci řešení akcelerátorů Cortana Intelligence Suite.  Další informace o Cortana Intelligence najdete v článku [Cortana Intelligence Suite](http://www.microsoft.com/cortanaanalytics). V tomto dokumentu jsme popisují případu použití jednoduchého můžete začít pracovat s pochopení, jak Azure Data Factory můžete řešení běžných potíží analytics.

## <a name="scenario"></a>Scénář
Contoso je herní společnost, která vytvoří hry pro různé platformy: her konzoly, dlaně zařízení a osobních počítačů (počítače). Jako přehrávače hraní her, se vytvářejí velkého objemu dat protokolu počítající vzorce, herní styl a předvolby uživatele.  V kombinaci s demografické údaje, místní a data produktu Contoso můžete provádět analýzy na obsahovat pokyny o tom, jak zajištění lepších možností hráčů a uzavře cílovou ve hře a je pro upgrade. 

Cílem společnosti Contoso je identifikace příležitostí až zákazník nebo cross zákazník na základě historie herní jeho přehrávačů, přidejte zajímavé funkce do růstu podniku a zajistit lepší prostředí pro zákazníky. Pro tento případ použití používáme herní společnosti jako příklad firmy. Společnost chce optimalizovat jeho hry na základě chování hráčů. Tyto zásady se vztahují na všechny podnikání, které chce zaujmout svým zákazníkům kolem jeho zboží a služeb a zajištění lepších možností svým zákazníkům.

V tomto řešení Contoso chce vyhodnotit efektivitu marketingové kampaně, které se nedávno spuštěna. Jsme začínat nezpracovaná herní protokoly, zpracování a zlepšit komunikaci oddělení je s daty zeměpisnou polohu, připojení s inzerování referenční data a nakonec zkopírujte je do Azure SQL Database pro analýzu dopadu kampaně.

## <a name="deploy-solution"></a>Nasazení řešení.
Potřebujete pro přístup k a vyzkoušet tento případ použití jednoduchého [předplatného Azure](https://azure.microsoft.com/pricing/free-trial/), [účtu Azure Blob storage](../../storage/common/storage-create-storage-account.md#create-a-storage-account)a [Azure SQL Database](../../sql-database/sql-database-get-started.md). Nasazení odběratele profilace kanálu z **ukázkové kanály** na domovské stránce objektu pro vytváření dat dlaždici.

1. Objekt pro vytváření dat vytvořit nebo otevřít existující datovou továrnu. V tématu [kopírování dat z úložiště objektů Blob do SQL Database pomocí služby Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) kroky pro vytvoření služby data factory.
2. V **DATA FACTORY** služby data Factory klikněte na **ukázkové kanály** dlaždici.

    ![Ukázka kanály dlaždice](./media/data-factory-samples/SamplePipelinesTile.png)
3. V **ukázkové kanály** okně klikněte na tlačítko **odběratele profilace** , kterou chcete nasadit.

    ![Okno ukázku kanálů](./media/data-factory-samples/SampleTile.png)
4. Zadejte nastavení konfigurace pro vzorovou. Například váš název účtu úložiště Azure a klíč, název serveru Azure SQL, databáze, ID uživatele a heslo.

    ![Ukázka okna](./media/data-factory-samples/SampleBlade.png)
5. Jakmile jste hotovi s určujícím nastavení konfigurace, klikněte na tlačítko **vytvořit** k vytvoření nebo nasazení ukázkové kanálů a propojených služeb/tabulek používané kanály.
6. Stav nasazení se zobrazí na dlaždici ukázka jste klikli na dříve **ukázkové kanály** okno.

    ![Stav nasazení](./media/data-factory-samples/DeploymentStatus.png)
7. Až se zobrazí **nasazení bylo úspěšné** zpráva na dlaždici pro ukázku, zavřete **ukázkové kanály** okno.  
8. Na **DATA FACTORY** okně se zobrazí, propojených služeb, datových sad a kanálů jsou přidány do vaší služby data factory.  

    ![Okno Objekt pro vytváření dat](./media/data-factory-samples/DataFactoryBladeAfter.png)

## <a name="solution-overview"></a>Přehled řešení
Tento případ použití jednoduchého slouží jako příklad použití Azure Data Factory k ingestování, Příprava, transformace, analýze a publikovat data.

![Ucelený pracovní postup](./media/data-factory-customer-profiling-usecase/EndToEndWorkflow.png)

Následující obrázek znázorňuje, jak datových kanálů zobrazovat na portálu Azure, potom, co byly nasazené.

1. **PartitionGameLogsPipeline** čte nezpracovaná herní události z úložiště objektů blob a vytvoří oddíly na základě rok, měsíc a den.
2. **EnrichGameLogsPipeline** připojí oddílů herní události u referenčních dat geografické kódu a vylepšuje data tím mapování IP adres do odpovídajících geografické umístění.
3. **AnalyzeMarketingCampaignPipeline** kanálu používá Obohacená data a zpracovává je s daty inzerování vytvořit finální výstup, který obsahuje efektivitu marketingu kampaně.

V tomto příkladu se používá pro vytváření dat k orchestraci aktivity, které zkopírujte vstupních dat, transformace a proces data a výstupní poslední data do Azure SQL Database.  Také můžete vizualizovat sítě datových kanálů, spravovat a monitorovat jejich stav v uživatelském rozhraní.

## <a name="benefits"></a>Výhody
Optimalizace analýzy profilu uživatele a zarovnání s obchodními cíli, je moct rychle vzorce používání shromažďovat a analyzovat účinnost jeho marketingových kampaní herní společnosti.


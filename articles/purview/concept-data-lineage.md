---
title: Datové řádky v Azure dosah (Preview)
description: Popisuje koncepty pro datovou řadu.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/30/2020
ms.openlocfilehash: 476355f41de5e0e6aaffdedea8947cab5221767a
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200733"
---
# <a name="data-lineage-in-azure-purview-data-catalog-client"></a>Data Catalog klient služby Azure dosah na datovém řádku

Tento článek poskytuje přehled datového typu datové řady v Azure dosah Data Catalog. Také podrobně popisuje, jak mohou být datové systémy integrovány s katalogem, aby bylo možné zachytit řádky dat. Dosah může zachytit řádky pro data v různých částech majetku vaší organizace a na různých úrovních přípravy, včetně:

- Zcela nezpracovaná data připravené z různých platforem
- Transformovaná a připravená data
- Data používaná platformami vizualizace.

## <a name="use-cases"></a>Případy použití

Datový základ se považuje za životní cyklus, který pokrývá počátek dat, a tam, kde se pohybuje v čase napříč datovou nemovitosti. Používá se pro různé druhy zpětně vypadajících scénářů, jako je například řešení potíží, trasování hlavní příčiny v datových kanálech a ladění. Pro analýzu kvality dat, dodržování předpisů a scénářů "Co kdyby" se používá také způsob, jakým je často označována analýza dopadu. Pro zobrazení přesunu dat ze zdroje do cíle, včetně způsobu transformace dat, je znázorněno podtržení. Vzhledem ke složitosti většiny podnikových podnikových dat můžou být tato zobrazení těžká porozumět bez provádění některé konsolidace nebo maskování periferních datových bodů.

## <a name="lineage-experience-in-azure-purview-data-catalog"></a>Prostředí Azure dosah Data Catalog

Dosah Data Catalog se připojí k ostatním systémům zpracování dat, úložiště a analytických systémů a extrahuje informace o vydaných řádcích. Tyto informace jsou zkombinovány tak, aby představovaly obecný způsob, jakým se v katalogu používá obecný způsob práce s řádky.

:::image type="content" source="media/concept-lineage/lineage-end-end.png" alt-text="Koncová koncová osa ukazující data zkopírovaná z úložiště objektů blob do Power BIho řídicího panelu":::

Vaše nemovitosti mohou zahrnovat systémy, které provádějí extrakci dat, transformaci (systémy ETL/ELT), analýzy a vizualizace. Každý ze systémů zachycuje bohatě statická a provozní metadata, která popisují stav a kvalitu dat v rámci hranice systémů. Cílem oddělení služby Data Catalog je extrahovat přesun, transformaci a provozní metadata z každého datového systému na nejnižší možné úrovni.

V následujícím příkladu je typický případ použití dat přesunu mezi více systémy, kde se Data Catalog k jednotlivým systémům připojovat za účelem započtu řádků.

- Data Factory kopíruje data ze zóny Prem/RAW do cílové zóny v cloudu. 
- Systémy zpracování dat, jako je synapse, datacihly budou zpracovávat a transformovat data z cílové zóny do spravované zóny pomocí poznámkových bloků.
- Další zpracování dat na analytické modely pro optimální výkon a agregaci dotazů. 
- Systémy pro vizualizaci dat budou využívat datové sady a procesy prostřednictvím jejich meta modelu, aby bylo možné vytvořit řídicí panel BI, experimenty ML a tak dále.

## <a name="lineage-granularity"></a>Členitost čáry

V této části jsou uvedeny podrobnosti o členitosti, které se shromažďují informace o prořádcích v katalogu dat. Tato členitost se může lišit v závislosti na datových systémech, které jsou.

### <a name="entity-level-lineage-sources--process--targets"></a>Čára na úrovni entity: zdroje > > cílů procesů 

- Ve formě grafu je reprezentace, obvykle obsahuje zdrojové a cílové entity v systémech úložiště dat, které jsou propojeny procesem vyvolaným výpočetním systémem. 
- Datové systémy se připojují ke katalogu Data Catalog, aby vygenerovaly a nahlásily jedinečný objekt odkazující na fyzický objekt podkladového datového systému, například: uložený postup SQL, poznámkové bloky atd.
- V případě, že je pro zdrojové entity & cílovými entitami, je zachycena čára s vysokou přesností s dalšími metadaty, jako je vlastnictví. Příklad: vyrovnávání na úrovni tabulky podregistru se spíše nemění na oddíly nebo na úrovni souborů.

### <a name="column-or-attribute-level-lineage"></a>Čára na úrovni sloupce nebo atributu

Identifikujte atributy zdrojové entity, která se používá k vytvoření nebo odvození atributů v cílové entitě. Název zdrojového atributu se může v cíli zachovat nebo přejmenovat. Systémy, jako je ADF, můžou provést kopii z místního prostředí do cloudu. Příklad: `Table1/ColumnA -> Table2/ColumnA`.

### <a name="process-execution-status"></a>Stav spuštění procesu

Pro podporu analýz základních příčin a scénářů kvality dat zaznamenáváme stav provádění úloh v systémech zpracování dat. Tento požadavek nemá žádný účinek na to, abyste mohli nahradit možnosti monitorování jiných systémů zpracování dat, ani to, že je nenahrazuje. 

## <a name="summary"></a>Souhrn

Je kritická funkce dosah Data Catalog pro podporu scénářů kvality, vztahu důvěryhodnosti a auditu. Cílem katalogu Data Catalog je vytvořit robustní rozhraní, ve kterém se všechny datové systémy v rámci vašeho prostředí mohou přirozeně připojit a vykazovat. Jakmile jsou metadata k dispozici, může Katalog dat začlenit metadata poskytovaná datovými systémy k případům použití zásad správného řízení dat.

## <a name="next-steps"></a>Další kroky

* [Rychlý Start: vytvoření účtu Azure dosah v Azure Portal](create-catalog-portal.md)
* [Rychlý Start: vytvoření účtu Azure dosah pomocí Azure PowerShell/Azure CLI](create-catalog-powershell.md)
* [Rychlý Start: použití dosah studia](use-purview-studio.md)

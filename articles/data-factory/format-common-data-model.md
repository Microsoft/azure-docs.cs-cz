---
title: Formát modelu Common data model
description: Transformace dat pomocí systému metadat modelu Common data model
author: djpmsft
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: daperlov
ms.openlocfilehash: 2faa14ef7724bb9cccfe425569539f5ef0621a28
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2020
ms.locfileid: "84435391"
---
# <a name="common-data-model-format-in-azure-data-factory"></a>Formát modelu Common data model v Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Systém metadat CDM (Common data model) umožňuje, aby data a jejich význam bylo možné snadno sdílet napříč aplikacemi a obchodními procesy. Další informace najdete v tématu Přehled [modelu Common data model](https://docs.microsoft.com/common-data-model/) .

V Azure Data Factory mohou uživatelé transformovat entity CDM uložené v [Azure Data Lake Store Gen2](connector-azure-data-lake-storage.md) (adls Gen2) pomocí mapování toků dat.

## <a name="mapping-data-flow-properties"></a>Mapování vlastností toku dat

Model Common data model je k dispozici jako [vložená datová sada](data-flow-source.md#inline-datasets) v mapování dat toků jako zdroj i jímka.

### <a name="source-properties"></a>Vlastnosti zdroje

V níže uvedené tabulce jsou uvedeny vlastnosti podporované zdrojem CDM.

| Name | Description | Vyžadováno | Povolené hodnoty | Vlastnost skriptu toku dat |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Formát | Je vždycky`cdm` | ano | `cdm` | formát |
| Formát metadat | Kde se nachází odkaz na entitu na data. Pokud používáte CDM verze 1,0, vyberte manifest. Pokud používáte verzi CDM před 1,0, vyberte model. JSON. | Ano | `'manifest'` nebo `'model'` | manifestType |
| Kořen umístění: kontejner | Název kontejneru složky CDM | ano | Řetězec | Systému souborů |
| Kořenové umístění: cesta ke složce | Umístění kořenové složky složky CDM | ano | Řetězec | folderPath |
| Soubor manifestu: cesta k entitě | Cesta ke složce entity v kořenové složce | ne | Řetězec | entityPath |
| Soubor manifestu: název manifestu | Název souboru manifestu  | Ano, pokud používáte manifest | Řetězec | manifest |
| Filtrovat podle poslední změny | Zvolit filtrování souborů podle toho, kdy se naposledy změnily | ne | Časové razítko | modifiedAfter <br> modifiedBefore | 
| Propojená služba schématu | Propojená služba, ve které se nachází Corpus | Ano, pokud používáte manifest | `'adlsgen2'` nebo `'github'` | corpusStore | 
| Kontejner odkazů na entity | Corpus kontejneru je v | Ano, pokud používáte manifest a corpus v ADLS Gen2 | Řetězec | adlsgen2_fileSystem |
| Úložiště referencí k entitám | GitHub repository name | Ano, pokud používáte manifest a corpus v GitHubu | Řetězec | github_repository |
| Referenční větev entit | Větev úložiště GitHub | Ano, pokud používáte manifest a corpus v GitHubu | Řetězec |  github_branch |
| Corpus složka | kořenové umístění Corpus | Ano, pokud používáte manifest | Řetězec | corpusPath |
| Corpus – entita | Cesta k odkazu na entitu | ano | Řetězec | entita |
| Nenalezeny žádné soubory | Pokud je nastaveno na true, chyba není vyvolána, pokud nebyly nalezeny žádné soubory. | ne | `true` nebo `false` | ignoreNoFilesFound |

![CDM zdroj](media/format-common-data-model/data-flow-source.png)

### <a name="sink-properties"></a>Vlastnosti jímky

V níže uvedené tabulce jsou uvedeny vlastnosti, které CDM jímka podporuje.

| Name | Description | Vyžadováno | Povolené hodnoty | Vlastnost skriptu toku dat |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Formát | Je vždycky`cdm` | ano | `cdm` | formát |
| Kořen umístění: kontejner | Název kontejneru složky CDM | ano | Řetězec | Systému souborů |
| Kořenové umístění: cesta ke složce | Umístění kořenové složky složky CDM | ano | Řetězec | folderPath |
| Soubor manifestu: cesta k entitě | Cesta ke složce entity v kořenové složce | ne | Řetězec | entityPath |
| Soubor manifestu: název manifestu | Název souboru manifestu  | ano | Řetězec | manifest |
| Propojená služba schématu | Propojená služba, ve které se nachází Corpus | ano | `'adlsgen2'` nebo `'github'` | corpusStore | 
| Kontejner odkazů na entity | Corpus kontejneru je v | Ano, pokud se corpus v ADLS Gen2 | Řetězec | adlsgen2_fileSystem |
| Úložiště referencí k entitám | GitHub repository name | Ano, pokud je corpus v GitHubu | Řetězec | github_repository |
| Referenční větev entit | Větev úložiště GitHub | Ano, pokud je corpus v GitHubu | Řetězec |  github_branch |
| Corpus složka | kořenové umístění Corpus | ano | Řetězec | corpusPath |
| Corpus – entita | Cesta k odkazu na entitu | ano | Řetězec | entita |
| Cesta oddílu | Umístění, kam se bude zapisovat oddíl | ne | Řetězec | partitionPath |
| Vymazat složku | Pokud před zápisem není cílová složka smazána | ne | `true` nebo `false` | zkrátit |
| Typ formátu | Zvolte, pokud chcete zadat formát Parquet. | ne | `parquet`je-li zadáno | podformát |
| Oddělovač sloupců | Postup při zápisu do DelimitedText, jak vymezují sloupce | Ano, pokud se zapisuje do DelimitedText | Řetězec | columnDelimiter |
| První řádek jako záhlaví | Pokud používáte DelimitedText, určuje, jestli se názvy sloupců přidávají jako záhlaví. | ne | `true` nebo `false` | columnNamesAsHeader |

![CDM zdroj](media/format-common-data-model/data-flow-sink.png)

## <a name="next-steps"></a>Další kroky

Vytvoří [transformaci zdroje](data-flow-source.md) v toku dat mapování.
---
title: Formát Common Data Modelu
description: Transformace dat pomocí systému metadat modelu Common data model
author: kromerm
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: makromer
ms.openlocfilehash: 45f5334ebee3365c17bfa52c8d47ed75b82bdfa1
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100387695"
---
# <a name="common-data-model-format-in-azure-data-factory"></a>Formát modelu Common data model v Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Systém metadat CDM (Common data model) umožňuje, aby data a jejich význam bylo možné snadno sdílet napříč aplikacemi a obchodními procesy. Další informace najdete v tématu Přehled [modelu Common data model](/common-data-model/) .

V Azure Data Factory můžou uživatelé transformovat data z entit CDM v model.jsna a ve formě manifestu uložené v [Azure Data Lake Store Gen2](connector-azure-data-lake-storage.md) (adls Gen2) pomocí mapování datových toků. Data můžete také ve formátu CDM založit pomocí odkazů na entity CDM, které budou vaše data uvádět ve formátu CSV nebo Parquet v dělených složkách. 

## <a name="mapping-data-flow-properties"></a>Mapování vlastností toku dat

Model Common data model je k dispozici jako [vložená datová sada](data-flow-source.md#inline-datasets) v mapování dat toků jako zdroj i jímka.

> [!NOTE]
> Při psaní entit CDM je nutné mít existující definici entity CDM (schématu metadat), která je již definována pro použití jako odkaz. Jímka toku dat ADF načte tento soubor entity CDM a naimportuje schéma do jímky pro mapování polí.

### <a name="source-properties"></a>Vlastnosti zdroje

V níže uvedené tabulce jsou uvedeny vlastnosti podporované zdrojem CDM. Tyto vlastnosti můžete upravit na kartě **Možnosti zdrojového kódu** .

| Název | Popis | Povinné | Povolené hodnoty | Vlastnost skriptu toku dat |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Formát | Formát musí být `cdm` | ano | `cdm` | formát |
| Formát metadat | Kde se nachází odkaz na entitu na data. Pokud používáte CDM verze 1,0, vyberte manifest. Pokud používáte verzi CDM před 1,0, vyberte model.jszapnuto. | Yes | `'manifest'` nebo `'model'` | manifestType |
| Kořen umístění: kontejner | Název kontejneru složky CDM | ano | Řetězec | Systému souborů |
| Kořenové umístění: cesta ke složce | Umístění kořenové složky složky CDM | ano | Řetězec | folderPath |
| Soubor manifestu: cesta k entitě | Cesta ke složce entity v kořenové složce | ne | Řetězec | entityPath |
| Soubor manifestu: název manifestu | Název souboru manifestu Výchozí hodnota je Default.  | No | Řetězec | manifest |
| Filtrovat podle poslední změny | Zvolit filtrování souborů podle toho, kdy se naposledy změnily | ne | Timestamp | modifiedAfter <br> modifiedBefore | 
| Propojená služba schématu | Propojená služba, ve které se nachází Corpus | Ano, pokud používáte manifest | `'adlsgen2'` nebo `'github'` | corpusStore | 
| Kontejner odkazů na entity | Corpus kontejneru je v | Ano, pokud používáte manifest a corpus v ADLS Gen2 | Řetězec | adlsgen2_fileSystem |
| Úložiště referencí k entitám | GitHub repository name | Ano, pokud používáte manifest a corpus v GitHubu | Řetězec | github_repository |
| Referenční větev entit | Větev úložiště GitHub | Ano, pokud používáte manifest a corpus v GitHubu | Řetězec |  github_branch |
| Corpus složka | kořenové umístění Corpus | Ano, pokud používáte manifest | Řetězec | corpusPath |
| Corpus – entita | Cesta k odkazu na entitu | ano | Řetězec | entita |
| Nenalezeny žádné soubory | Pokud je nastaveno na true, chyba není vyvolána, pokud nebyly nalezeny žádné soubory. | ne | `true` nebo `false` | ignoreNoFilesFound |

Při výběru možnosti odkaz na entitu v transformaci zdroje i jímky můžete pro umístění odkazu na entitu vybrat z těchto tří možností:

* Místní používá entitu definovanou v souboru manifestu, který už používá ADF.
* Vlastní dotaz vás vyzve, abyste odkazovali na soubor manifestu entity, který se liší od souboru manifestu, který používá ADF.
* Standard bude používat odkaz na entitu ze standardní knihovny CDM entit udržované v ```Github``` .

### <a name="sink-settings"></a>Nastavení jímky

* Přejděte na referenční soubor entity CDM, který obsahuje definici entity, kterou chcete zapsat.

![nastavení entit](media/data-flow/common-data-model-111.png "Referenční údaje k entitám")

* Zadejte cestu oddílu a formát výstupních souborů, které mají být použity pro vytváření ADF pro psaní entit.

![Formát entity](media/data-flow/common-data-model-222.png "Formát entity")

* Nastavte umístění výstupního souboru a umístění a název souboru manifestu.

![umístění CDM](media/data-flow/common-data-model-333.png "Umístění CDM")


#### <a name="import-schema"></a>Importovat schéma

CDM je k dispozici pouze jako vložená datová sada a ve výchozím nastavení nemá přidružené schéma. Chcete-li získat metadata sloupce, klikněte na tlačítko **importovat schéma** na kartě **projekce** . To vám umožní odkazovat na názvy sloupců a datové typy určené parametrem corpus. Pro import schématu musí být [relace ladění toku dat](concepts-data-flow-debug-mode.md) aktivní a musíte mít existující definiční soubor entity CDM, na který odkazuje.

Při mapování sloupců toku dat na vlastnosti entity v transformaci jímky klikněte na kartu mapování a vyberte Importovat schéma. ADF načte odkaz na entitu, na který jste odkazovali v možnostech jímky, což vám umožní mapovat na cílové schéma CDM.

![Nastavení jímky CDM](media/data-flow/common-data-model-444.png "Mapování CDM")

> [!NOTE]
>  Při použití model.jsu typu zdroje, který pochází z Power BI nebo datového toku Power Platform, se můžete setkat s chybou "corpus cesta je null nebo prázdná" z transformace zdroje. To je pravděpodobně způsobeno problémy s formátováním cesty k umístění oddílu v model.jssouboru. Chcete-li tento problém vyřešit, postupujte podle následujících kroků: 

1. Otevřít model.jsv souboru v textovém editoru
2. Najděte oddíly. Location – vlastnost 
3. Změňte "blob.core.windows.net" na "dfs.core.windows.net"
4. Opravte jakékoli kódování "% 2F" v adrese URL na "/".
5. Při použití datových toků ADF je potřeba, aby se speciální znaky v cestě k souboru oddílu nahradily pomocí alfanumerických hodnot nebo přepnuly na toky dat synapse.

### <a name="cdm-source-data-flow-script-example"></a>Příklad skriptu zdrojového toku dat CDM

```
source(output(
        ProductSizeId as integer,
        ProductColor as integer,
        CustomerId as string,
        Note as string,
        LastModifiedDate as timestamp
    ),
    allowSchemaDrift: true,
    validateSchema: false,
    entity: 'Product.cdm.json/Product',
    format: 'cdm',
    manifestType: 'manifest',
    manifestName: 'ProductManifest',
    entityPath: 'Product',
    corpusPath: 'Products',
    corpusStore: 'adlsgen2',
    adlsgen2_fileSystem: 'models',
    folderPath: 'ProductData',
    fileSystem: 'data') ~> CDMSource
```

### <a name="sink-properties"></a>Vlastnosti jímky

V níže uvedené tabulce jsou uvedeny vlastnosti, které CDM jímka podporuje. Tyto vlastnosti můžete upravit na kartě **Nastavení** .

| Název | Popis | Povinné | Povolené hodnoty | Vlastnost skriptu toku dat |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Formát | Formát musí být `cdm` | ano | `cdm` | formát |
| Kořen umístění: kontejner | Název kontejneru složky CDM | ano | Řetězec | Systému souborů |
| Kořenové umístění: cesta ke složce | Umístění kořenové složky složky CDM | ano | Řetězec | folderPath |
| Soubor manifestu: cesta k entitě | Cesta ke složce entity v kořenové složce | ne | Řetězec | entityPath |
| Soubor manifestu: název manifestu | Název souboru manifestu Výchozí hodnota je Default. | No | Řetězec | manifest |
| Propojená služba schématu | Propojená služba, ve které se nachází Corpus | ano | `'adlsgen2'` nebo `'github'` | corpusStore | 
| Kontejner odkazů na entity | Corpus kontejneru je v | Ano, pokud se corpus v ADLS Gen2 | Řetězec | adlsgen2_fileSystem |
| Úložiště referencí k entitám | GitHub repository name | Ano, pokud je corpus v GitHubu | Řetězec | github_repository |
| Referenční větev entit | Větev úložiště GitHub | Ano, pokud je corpus v GitHubu | Řetězec |  github_branch |
| Corpus složka | kořenové umístění Corpus | ano | Řetězec | corpusPath |
| Corpus – entita | Cesta k odkazu na entitu | ano | Řetězec | entita |
| Cesta oddílu | Umístění, kam se bude zapisovat oddíl | ne | Řetězec | partitionPath |
| Vymazat složku | Pokud před zápisem není cílová složka smazána | ne | `true` nebo `false` | zkrátit |
| Typ formátu | Zvolte, pokud chcete zadat formát Parquet. | ne | `parquet` je-li zadáno | podformát |
| Oddělovač sloupců | Postup při zápisu do DelimitedText, jak vymezují sloupce | Ano, pokud se zapisuje do DelimitedText | Řetězec | columnDelimiter |
| První řádek jako záhlaví | Pokud používáte DelimitedText, určuje, jestli se názvy sloupců přidávají jako záhlaví. | ne | `true` nebo `false` | columnNamesAsHeader |

### <a name="cdm-sink-data-flow-script-example"></a>Příklad skriptu toku dat jímky CDM

Přidružený skript toku dat je:

```
CDMSource sink(allowSchemaDrift: true,
    validateSchema: false,
    entity: 'Product.cdm.json/Product',
    format: 'cdm',
    entityPath: 'ProductSize',
    manifestName: 'ProductSizeManifest',
    corpusPath: 'Products',
    partitionPath: 'adf',
    folderPath: 'ProductSizeData',
    fileSystem: 'cdm',
    subformat: 'parquet',
    corpusStore: 'adlsgen2',
    adlsgen2_fileSystem: 'models',
    truncate: true,
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> CDMSink

```

## <a name="next-steps"></a>Další kroky

Vytvoří [transformaci zdroje](data-flow-source.md) v toku dat mapování.

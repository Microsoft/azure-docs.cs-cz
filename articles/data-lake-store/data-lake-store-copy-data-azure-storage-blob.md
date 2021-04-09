---
title: Kopírování dat z objektů blob Azure Storage do Data Lake Storage Gen1
description: Kopírování dat z objektů blob Azure Storage do Azure Data Lake Storage Gen1 pomocí nástroje AdlCopy
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 84ee65b05af4393f49696875bda41df39e283d5d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "85980085"
---
# <a name="copy-data-from-azure-storage-blobs-to-azure-data-lake-storage-gen1"></a>Kopírování dat z objektů blob Azure Storage do Azure Data Lake Storage Gen1

> [!div class="op_single_selector"]
> * [Pomocí DistCp](data-lake-store-copy-data-wasb-distcp.md)
> * [Pomocí AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Data Lake Storage Gen1 poskytuje nástroj příkazového řádku [AdlCopy](https://www.microsoft.com/download/details.aspx?id=50358), který umožňuje kopírovat data z následujících zdrojů:

* Z Azure Storage objektů blob do Data Lake Storage Gen1. AdlCopy nelze použít ke kopírování dat z Data Lake Storage Gen1 do objektů blob Azure Storage.
* Mezi dvěma účty Data Lake Storage Gen1.

Nástroj AdlCopy můžete také použít ve dvou různých režimech:

* **Standalone**, kde nástroj používá k provedení úlohy Data Lake Storage Gen1 prostředky.
* **Pomocí účtu Data Lake Analytics**, kde se k provedení operace kopírování použijí jednotky přiřazené vašemu účtu Data Lake Analytics. Tuto možnost můžete použít, pokud chcete provádět úlohy kopírování předvídatelným způsobem.

## <a name="prerequisites"></a>Požadavky

Je nutné, abyste před zahájením tohoto článku měli tyto položky:

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure Storage kontejner objektů BLOB** s některými daty.
* **Účet Data Lake Storage Gen1**. Pokyny, jak ho vytvořit, najdete v tématu Začínáme [s Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md) .
* **Účet Data Lake Analytics (volitelné)** – pokyny k vytvoření účtu Data Lake Analytics najdete v části začínáme [s Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md) .
* **Nástroj AdlCopy** Nainstalujte [Nástroj AdlCopy](https://www.microsoft.com/download/details.aspx?id=50358).

## <a name="syntax-of-the-adlcopy-tool"></a>Syntaxe nástroje AdlCopy

Pomocí následující syntaxe můžete pracovat s nástrojem AdlCopy.

```console
AdlCopy /Source <Blob or Data Lake Storage Gen1 source> /Dest <Data Lake Storage Gen1 destination> /SourceKey <Key for Blob account> /Account <Data Lake Analytics account> /Units <Number of Analytics units> /Pattern
```

Parametry v syntaxi jsou popsány níže:

| Možnost | Popis |
| --- | --- |
| Zdroj |Určuje umístění zdrojových dat v objektu BLOB služby Azure Storage. Zdrojem může být kontejner objektů blob, objekt BLOB nebo jiný Data Lake Storage Gen1 účet. |
| Propojovací |Určuje Data Lake Storage Gen1 cíl kopírování. |
| SourceKey |Určuje přístupový klíč úložiště pro zdroj objektu BLOB služby Azure Storage. To se vyžaduje jenom v případě, že zdrojem je kontejner objektů BLOB nebo objekt BLOB. |
| Účet |**Volitelné**. Tuto funkci použijte, pokud chcete použít účet Azure Data Lake Analytics ke spuštění úlohy kopírování. Použijete-li v syntaxi možnost/Account, ale nezadáte účet Data Lake Analytics, použije AdlCopy výchozí účet ke spuštění úlohy. Pokud použijete tuto možnost, musíte přidat zdroj (Azure Storage Blob) a cíl (Azure Data Lake Storage Gen1) jako zdroje dat pro účet Data Lake Analytics. |
| Jednotky |Určuje počet Data Lake Analytics jednotek, které budou použity pro úlohu kopírování. Tato možnost je povinná, pokud k určení Data Lake Analytics účtu použijete možnost **/account** . |
| Vzor |Určuje vzor regulárního výrazu, který určuje, které objekty blob nebo soubory ke zkopírování. AdlCopy používá porovnávání rozlišující velká a malá písmena. Výchozí způsob, kdy není zadán žádný vzor, je kopírovat všechny položky. Určení více vzorů souborů není podporováno. |

## <a name="use-adlcopy-as-standalone-to-copy-data-from-an-azure-storage-blob"></a>Použití AdlCopy (jako samostatné) ke kopírování dat z Azure Storageého objektu BLOB

1. Otevřete příkazový řádek a přejděte do adresáře, kde je nainstalovaný AdlCopy, obvykle `%HOMEPATH%\Documents\adlcopy` .
1. Spusťte následující příkaz ke zkopírování konkrétního objektu BLOB ze zdrojového kontejneru do složky Data Lake Storage Gen1:

    ```console
    AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>
    ```

    Například:

    ```console
    AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log /dest swebhdfs://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==
    ```

    >[!NOTE]
    >Výše uvedená syntaxe určuje soubor, který se má zkopírovat do složky v Data Lake Storage Gen1ovém účtu. Nástroj AdlCopy vytvoří složku, pokud zadaný název složky neexistuje.

    Zobrazí se výzva k zadání přihlašovacích údajů k předplatnému Azure, pod kterým máte účet Data Lake Storage Gen1. Zobrazí se výstup podobný následujícímu:

    ```output
    Initializing Copy.
    Copy Started.
    100% data copied.
    Finishing Copy.
    Copy Completed. 1 file copied.
    ```

1. Všechny objekty BLOB z jednoho kontejneru můžete také zkopírovat do účtu Data Lake Storage Gen1 pomocí následujícího příkazu:

    ```console
    AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/ /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>  
    ```      

    Například:

    ```console
    AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest adl://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==
    ```

### <a name="performance-considerations"></a>Otázky výkonu

Pokud provádíte kopírování z účtu Azure Blob Storage, můžete se během kopírování na straně úložiště objektů BLOB omezit. Tím dojde ke snížení výkonu úlohy kopírování. Další informace o limitech Azure Blob Storage najdete v tématu omezení Azure Storage v rámci [předplatného a služeb Azure](../azure-resource-manager/management/azure-subscription-service-limits.md).

## <a name="use-adlcopy-as-standalone-to-copy-data-from-another-data-lake-storage-gen1-account"></a>Použití AdlCopy (jako samostatné) ke kopírování dat z jiného Data Lake Storage Gen1 účtu

Pomocí AdlCopy můžete také kopírovat data mezi dvěma Data Lake Storage Gen1 účty.

1. Otevřete příkazový řádek a přejděte do adresáře, kde je nainstalovaný AdlCopy, obvykle `%HOMEPATH%\Documents\adlcopy` .
1. Spusťte následující příkaz ke zkopírování konkrétního souboru z jednoho Data Lake Storage Gen1 účtu do jiného.

    ```console
    AdlCopy /Source adl://<source_adlsg1_account>.azuredatalakestore.net/<path_to_file> /dest adl://<dest_adlsg1_account>.azuredatalakestore.net/<path>/
    ```

    Například:

    ```console
    AdlCopy /Source adl://mydatastorage.azuredatalakestore.net/mynewfolder/909f2b.log /dest adl://mynewdatalakestorage.azuredatalakestore.net/mynewfolder/
    ```

   > [!NOTE]
   > Výše uvedená syntaxe určuje soubor, který se má zkopírovat do složky v cílovém Data Lake Storage Gen1 účtu. Nástroj AdlCopy vytvoří složku, pokud zadaný název složky neexistuje.
   >
   >

    Zobrazí se výzva k zadání přihlašovacích údajů k předplatnému Azure, pod kterým máte účet Data Lake Storage Gen1. Zobrazí se výstup podobný následujícímu:

    ```output
    Initializing Copy.
    Copy Started.|
    100% data copied.
    Finishing Copy.
    Copy Completed. 1 file copied.
    ```
1. Následující příkaz zkopíruje všechny soubory z konkrétní složky ve zdrojovém Data Lake Storage Gen1 účtu do složky v cílovém Data Lake Storage Gen1 účtu.

    ```console
    AdlCopy /Source adl://mydatastorage.azuredatalakestore.net/mynewfolder/ /dest adl://mynewdatalakestorage.azuredatalakestore.net/mynewfolder/
    ```

### <a name="performance-considerations"></a>Otázky výkonu

Při použití AdlCopy jako samostatného nástroje se kopie spouští na sdílených prostředcích spravovaných Azure. Výkon, který se může v tomto prostředí zobrazit, závisí na zatížení systému a dostupných prostředcích. Tento režim se nejlépe používá pro malé přenosy na základě ad hoc. Při použití AdlCopy jako samostatného nástroje není nutné vyladit žádné parametry.

## <a name="use-adlcopy-with-data-lake-analytics-account-to-copy-data"></a>Kopírování dat pomocí AdlCopy (s Data Lake Analyticsm účtem)

Účet Data Lake Analytics můžete použít také ke spuštění úlohy AdlCopy ke zkopírování dat z objektů BLOB služby Azure Storage do Data Lake Storage Gen1. Tuto možnost obvykle použijete, když přesunete data v rozsahu gigabajtů a terabajtů a potřebujete lepší a předvídatelné propustnost výkonu.

Chcete-li použít účet Data Lake Analytics s AdlCopy ke zkopírování z Azure Storage Blob, je třeba jako zdroj dat pro účet Data Lake Analytics přidat zdroj (Azure Storage Blob). Pokyny k přidání dalších zdrojů dat do účtu Data Lake Analytics najdete v tématu [Správa zdrojů dat účtu Data Lake Analytics](../data-lake-analytics/data-lake-analytics-manage-use-portal.md#manage-data-sources).

> [!NOTE]
> Pokud kopírujete z účtu Azure Data Lake Storage Gen1 jako zdroj pomocí účtu Data Lake Analytics, není nutné přidružit účet Data Lake Storage Gen1 k účtu Data Lake Analytics. Požadavek na přidružení zdrojového úložiště s účtem Data Lake Analytics je pouze v případě, že zdrojem je účet Azure Storage.
>
>

Spusťte následující příkaz ke zkopírování z Azure Storageého objektu blob do účtu Data Lake Storage Gen1 pomocí účtu Data Lake Analytics:

```console
AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Account <data_lake_analytics_account> /Units <number_of_data_lake_analytics_units_to_be_used>
```

Například:

```console
AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest swebhdfs://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Account mydatalakeanalyticaccount /Units 2
```

Podobně spusťte následující příkaz, který zkopíruje všechny soubory z konkrétní složky ve zdrojovém Data Lake Storage Gen1 účtu do složky v cílovém Data Lake Storage Gen1 účtu pomocí účtu Data Lake Analytics:

```console
AdlCopy /Source adl://mysourcedatalakestorage.azuredatalakestore.net/mynewfolder/ /dest adl://mydestdatastorage.azuredatalakestore.net/mynewfolder/ /Account mydatalakeanalyticaccount /Units 2
```

### <a name="performance-considerations"></a>Otázky výkonu

Při kopírování dat v rozsahu terabajtů používá AdlCopy s vlastním účtem Azure Data Lake Analytics lepší a předvídatelný výkon. Parametr, který se má ladit, je počet Azure Data Lake Analytics jednotek, které se mají použít pro úlohu kopírování. Zvýšením počtu jednotek dojde ke zvýšení výkonu úlohy kopírování. Každý soubor, který se má zkopírovat, může používat maximálně jednu jednotku. Zadáním více jednotek, než je počet kopírovaných souborů, nedojde ke zvýšení výkonu.

## <a name="use-adlcopy-to-copy-data-using-pattern-matching"></a>Použití AdlCopy ke kopírování dat pomocí porovnávání vzorů

V této části se dozvíte, jak pomocí AdlCopy kopírovat data ze zdroje (v našem příkladu níže používáme Azure Storage Blob) k cílovému Data Lake Storage Gen1mu účtu pomocí porovnávání vzorů. Pomocí následujících kroků můžete například zkopírovat všechny soubory s příponou. csv ze zdrojového objektu blob do cílového umístění.

1. Otevřete příkazový řádek a přejděte do adresáře, kde je nainstalovaný AdlCopy, obvykle `%HOMEPATH%\Documents\adlcopy` .
1. Spusťte následující příkaz, který zkopíruje všechny soubory s příponou *. CSV z konkrétního objektu BLOB ze zdrojového kontejneru do složky Data Lake Storage Gen1:

    ```console
    AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Pattern *.csv
    ```

    Například:

    ```console
    AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/FoodInspectionData/ /dest adl://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Pattern *.csv
    ```

## <a name="billing"></a>Fakturace

* Pokud použijete nástroj AdlCopy jako samostatný, budou se vám účtovat náklady na výstup pro přesun dat, pokud zdrojový Azure Storage účet není ve stejné oblasti jako Data Lake Storage Gen1 účet.
* Pokud použijete nástroj AdlCopy s vaším účtem Data Lake Analytics, budou platit standardní [Data Lake Analytics fakturační sazby](https://azure.microsoft.com/pricing/details/data-lake-analytics/) .

## <a name="considerations-for-using-adlcopy"></a>Předpoklady pro používání AdlCopy

* AdlCopy (pro verzi 1.0.5) podporuje kopírování dat ze zdrojů, které společně mají více než tisíce souborů a složek. Pokud však narazíte na problémy s kopírováním velké datové sady, můžete soubory nebo složky distribuovat do různých podsložek a místo toho použít cestu k těmto podsložkám jako zdroj.

## <a name="performance-considerations-for-using-adlcopy"></a>Požadavky na výkon pro použití AdlCopy

AdlCopy podporuje kopírování dat obsahujících tisíce souborů a složek. Pokud ale dojde k problémům s kopírováním velké datové sady, můžete soubory nebo složky distribuovat do menších podsložek. AdlCopy byl sestaven pro ad hoc kopie. Pokud se snažíte kopírovat data opakovaně, měli byste zvážit použití [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) , která poskytuje úplnou správu kolem operací kopírování.

## <a name="release-notes"></a>Poznámky k verzi

* 1.0.13 – Pokud kopírujete data do stejného Azure Data Lake Storage Gen1 účtu v několika příkazech AdlCopy, nemusíte už znovu zadávat svoje přihlašovací údaje pro každý běh. AdlCopy nyní bude tyto informace ukládat do mezipaměti v různých spuštěních.

## <a name="next-steps"></a>Další kroky

* [Zabezpečení dat ve službě Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Použití Azure Data Lake Analytics s Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Použití Azure HDInsight s Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

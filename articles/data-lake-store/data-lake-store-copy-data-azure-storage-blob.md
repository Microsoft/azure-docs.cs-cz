---
title: Kopírování dat z objektů BLOB úložiště Azure do data Lake Storage Gen1
description: Kopírování dat z objektů Blob azure storage do Azure Data Lake Storage Storage Gen1 pomocí nástroje AdlCopy
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: ad408df140be49da2e50ef810285dd850e9da6a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638863"
---
# <a name="copy-data-from-azure-storage-blobs-to-azure-data-lake-storage-gen1"></a>Kopírování dat z objektů BLOB úložiště Azure do Azure Data Lake Storage Gen1

> [!div class="op_single_selector"]
> * [Pomocí DistCp](data-lake-store-copy-data-wasb-distcp.md)
> * [Pomocí AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Data Lake Storage Gen1 poskytuje nástroj příkazového řádku [AdlCopy](https://www.microsoft.com/download/details.aspx?id=50358), který kopíruje data z následujících zdrojů:

* Z objektů BLOB úložiště Azure do data lake storage Gen1. AdlCopy nelze použít ke kopírování dat z dat Lake Storage Gen1 do objektů blob služby Azure Storage.
* Mezi dvěma účty Data Lake Storage Gen1.

Nástroj AdlCopy můžete také použít ve dvou různých režimech:

* **Samostatný**, kde nástroj používá data lake storage gen1 prostředky k provedení úkolu.
* **Pomocí účtu Data Lake Analytics**, kde se k operaci kopírování používají jednotky přiřazené k vašemu účtu Data Lake Analytics. Tuto možnost můžete použít, pokud chcete provádět úlohy kopírování předvídatelným způsobem.

## <a name="prerequisites"></a>Požadavky

Je nutné, abyste před zahájením tohoto článku měli tyto položky:

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Kontejner objektů blob** Azure Storage s některými daty.
* **Účet Gen1 úložiště datového jezera**. Pokyny k jeho vytvoření najdete v tématu [Začínáme s Azure Data Lake Storage Gen1.](data-lake-store-get-started-portal.md)
* **Účet Data Lake Analytics (volitelné)** – informace o tom, jak vytvořit účet Data Lake Analytics, najdete v tématu [Začínáme s Azure Data Lake Analytics.](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* **Nástroj AdlCopy**. Nainstalujte [nástroj AdlCopy](https://www.microsoft.com/download/details.aspx?id=50358).

## <a name="syntax-of-the-adlcopy-tool"></a>Syntaxe nástroje AdlCopy

Použití následující syntaxe pro práci s nástrojem AdlCopy

    AdlCopy /Source <Blob or Data Lake Storage Gen1 source> /Dest <Data Lake Storage Gen1 destination> /SourceKey <Key for Blob account> /Account <Data Lake Analytics account> /Units <Number of Analytics units> /Pattern

Parametry v syntaxi jsou popsány níže:

| Možnost | Popis |
| --- | --- |
| Zdroj |Určuje umístění zdrojových dat v objektu blob úložiště Azure. Zdrojem může být kontejner objektů blob, objekt blob nebo jiný účet Data Lake Storage Gen1. |
| Dest |Určuje cíl Data Lake Storage Gen1, do který se má kopírovat. |
| Zdrojový klíč |Určuje přístupový klíč úložiště pro zdroj objektů blob úložiště Azure. To je vyžadováno pouze v případě, že zdroj je kontejner objektů blob nebo objekt blob. |
| Účet |**Nepovinné**. Tuto funkci použijte, pokud chcete ke spuštění úlohy kopírování použít účet Azure Data Lake Analytics. Pokud použijete možnost /Account v syntaxi, ale nezadáte účet Data Lake Analytics, AdlCopy použije ke spuštění úlohy výchozí účet. Pokud použijete tuto možnost, musíte také přidat zdroj (Objekt blob úložiště Azure) a cíl (Azure Data Lake Storage Gen1) jako zdroje dat pro váš účet Data Lake Analytics. |
| Jednotky |Určuje počet jednotek Data Lake Analytics, které budou použity pro úlohu kopírování. Tato možnost je povinná, pokud použijete možnost **/Account** k zadání účtu Data Lake Analytics. |
| Vzor |Určuje vzorek regulárního výrazu, který označuje, které objekty BLOB nebo soubory mají být zkopírovány. AdlCopy používá porovnávání s rozlišování velkých a malých písmen. Výchozí vzorek, pokud není zadán žádný vzorek, je zkopírovat všechny položky. Určení více vzorů souborů není podporováno. |

## <a name="use-adlcopy-as-standalone-to-copy-data-from-an-azure-storage-blob"></a>Použití AdlCopy (jako samostatné) ke kopírování dat z objektu blob úložiště Azure

1. Otevřete příkazový řádek a přejděte do adresáře, `%HOMEPATH%\Documents\adlcopy`ve kterém je nainstalována aplikace AdlCopy, obvykle .
1. Spusťte následující příkaz pro kopírování konkrétního objektu blob ze zdrojového kontejneru do složky Gen1 úložiště datového jezera:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

    Například:

        AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log /dest swebhdfs://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

    >[!NOTE]
    >Výše uvedená syntaxe určuje soubor, který má být zkopírován do složky v účtu Data Lake Storage Gen1. Nástroj AdlCopy vytvoří složku, pokud zadaný název složky neexistuje.

    Budete vyzváni k zadání přihlašovacích údajů pro předplatné Azure, pod kterým máte účet Data Lake Storage Gen1. Zobrazí se výstup podobný následujícímu:

        Initializing Copy.
        Copy Started.
        100% data copied.
        Finishing Copy.
        Copy Completed. 1 file copied.

1. Můžete také zkopírovat všechny objekty BLOB z jednoho kontejneru do účtu Data Lake Storage Gen1 pomocí následujícího příkazu:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/ /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>        

    Například:

        AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest adl://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

### <a name="performance-considerations"></a>Otázky výkonu

Pokud kopírujete z účtu Azure Blob Storage, může být omezena během kopírování na straně úložiště objektů blob. Tím se sníží výkon úlohy kopírování. Další informace o limitech azure blob storage najdete v tématu Limity úložiště Azure na [limitech předplatného Azure a služeb](../azure-resource-manager/management/azure-subscription-service-limits.md).

## <a name="use-adlcopy-as-standalone-to-copy-data-from-another-data-lake-storage-gen1-account"></a>Použití AdlCopy (jako samostatné) ke kopírování dat z jiného účtu Data Lake Storage Gen1

AdlCopy můžete také použít ke kopírování dat mezi dvěma účty Data Lake Storage Gen1.

1. Otevřete příkazový řádek a přejděte do adresáře, `%HOMEPATH%\Documents\adlcopy`ve kterém je nainstalována aplikace AdlCopy, obvykle .
1. Spusťte následující příkaz pro kopírování určitého souboru z jednoho účtu Data Lake Storage Gen1 do jiného.

        AdlCopy /Source adl://<source_adlsg1_account>.azuredatalakestore.net/<path_to_file> /dest adl://<dest_adlsg1_account>.azuredatalakestore.net/<path>/

    Například:

        AdlCopy /Source adl://mydatastorage.azuredatalakestore.net/mynewfolder/909f2b.log /dest adl://mynewdatalakestorage.azuredatalakestore.net/mynewfolder/

   > [!NOTE]
   > Výše uvedená syntaxe určuje soubor, který má být zkopírován do složky v cílovém účtu Data Lake Storage Gen1. Nástroj AdlCopy vytvoří složku, pokud zadaný název složky neexistuje.
   >
   >

    Budete vyzváni k zadání přihlašovacích údajů pro předplatné Azure, pod kterým máte účet Data Lake Storage Gen1. Zobrazí se výstup podobný následujícímu:

        Initializing Copy.
        Copy Started.|
        100% data copied.
        Finishing Copy.
        Copy Completed. 1 file copied.
1. Následující příkaz zkopíruje všechny soubory z určité složky ve zdrojovém účtu Data Lake Storage Gen1 do složky v cílovém účtu Data Lake Storage Gen1.

        AdlCopy /Source adl://mydatastorage.azuredatalakestore.net/mynewfolder/ /dest adl://mynewdatalakestorage.azuredatalakestore.net/mynewfolder/

### <a name="performance-considerations"></a>Otázky výkonu

Při použití AdlCopy jako samostatný nástroj, kopie se spustí na sdílené prostředky spravované Azure. Výkon, který můžete získat v tomto prostředí, závisí na zatížení systému a dostupných prostředcích. Tento režim se nejlépe používá pro malé přenosy ad hoc. Při použití AdlCopy jako samostatného nástroje není třeba vyladit žádné parametry.

## <a name="use-adlcopy-with-data-lake-analytics-account-to-copy-data"></a>Kopírování dat pomocí AdlCopy (s účtem Data Lake Analytics)

Pomocí účtu Data Lake Analytics můžete taky spouštět úlohu AdlCopy ke kopírování dat z objektů BLOB úložiště Azure do data Lake Storage Gen1. Tuto možnost byste obvykle použili, pokud jsou data, která mají být přesunuta, v rozsahu gigabajtů a terabajtů a chcete lepší a předvídatelnější propustnost výkonu.

Chcete-li použít svůj účet Data Lake Analytics s AdlCopy ke kopírování z objektu blob úložiště Azure, musí být zdroj (Blob úložiště Azure) přidán jako zdroj dat pro váš účet Data Lake Analytics. Pokyny k přidání dalších zdrojů dat do účtu Data Lake Analytics najdete v [tématu Správa zdrojů dat účtu Data Lake Analytics](../data-lake-analytics/data-lake-analytics-manage-use-portal.md#manage-data-sources).

> [!NOTE]
> Pokud kopírujete z účtu Azure Data Lake Storage Gen1 jako zdroj pomocí účtu Data Lake Analytics, nemusíte účet Data Lake Storage Gen1 přidružit k účtu Data Lake Analytics. Požadavek na přidružení zdrojového úložiště k účtu Data Lake Analytics je jenom v případě, že zdroj je účet Azure Storage.
>
>

Spusťte následující příkaz pro kopírování z objektu blob úložiště Azure do účtu Data Lake Storage Gen1 pomocí účtu Data Lake Analytics:

    AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Account <data_lake_analytics_account> /Units <number_of_data_lake_analytics_units_to_be_used>

Například:

    AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest swebhdfs://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Account mydatalakeanalyticaccount /Units 2

Podobně spusťte následující příkaz ke zkopírování všech souborů z určité složky ve zdrojovém účtu Data Lake Storage Gen1 do složky v cílovém účtu Data Lake Storage Gen1 pomocí účtu Data Lake Analytics:

    AdlCopy /Source adl://mysourcedatalakestorage.azuredatalakestore.net/mynewfolder/ /dest adl://mydestdatastorage.azuredatalakestore.net/mynewfolder/ /Account mydatalakeanalyticaccount /Units 2

### <a name="performance-considerations"></a>Otázky výkonu

Při kopírování dat v rozsahu terabajtů poskytuje použití AdlCopy s vlastním účtem Azure Data Lake Analytics lepší a předvídatelnější výkon. Parametr, který by měl být naladěn, je počet jednotek Azure Data Lake Analytics, které se mají použít pro úlohu kopírování. Zvýšenípočtu jednotek zvýší výkon úlohy kopírování. Každý soubor, který má být zkopírován, může používat maximálně jednu jednotku. Zadání více jednotek, než je počet kopírovaných souborů, nezvýší výkon.

## <a name="use-adlcopy-to-copy-data-using-pattern-matching"></a>Použití AdlCopy ke kopírování dat pomocí porovnávání vzorků

V této části se dozvíte, jak pomocí AdlCopy kopírovat data ze zdroje (v našem příkladu níže používáme objekt blob úložiště Azure) do cílového účtu Data Lake Storage Gen1 pomocí porovnávání vzorů. Pomocí následujících kroků můžete například zkopírovat všechny soubory s příponou .csv ze zdrojového objektu blob do cíle.

1. Otevřete příkazový řádek a přejděte do adresáře, `%HOMEPATH%\Documents\adlcopy`ve kterém je nainstalována aplikace AdlCopy, obvykle .
1. Spuštěním následujícího příkazu zkopírujte všechny soubory s příponou *.csv z konkrétního objektu blob ze zdrojového kontejneru do složky Gen1 úložiště datového jezera:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Pattern *.csv

    Například:

        AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/FoodInspectionData/ /dest adl://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Pattern *.csv

## <a name="billing"></a>Fakturace

* Pokud použijete nástroj AdlCopy jako samostatný, budou se vám účtovat náklady na odchozí přenos dat, pokud zdrojový účet úložiště Azure není ve stejné oblasti jako účet Gen1 úložiště datového jezera.
* Pokud používáte nástroj AdlCopy s účtem Data Lake Analytics, budou platit standardní [fakturační sazby Data Lake Analytics.](https://azure.microsoft.com/pricing/details/data-lake-analytics/)

## <a name="considerations-for-using-adlcopy"></a>Důležité informace o použití adlcopy

* AdlCopy (pro verzi 1.0.5) podporuje kopírování dat ze zdrojů, které mají společně více než tisíce souborů a složek. Pokud však narazíte na problémy s kopírováním velké datové sady, můžete soubory/složky distribuovat do různých podsložek a místo toho použít cestu k těmto podsložkám jako zdroj.

## <a name="performance-considerations-for-using-adlcopy"></a>Důležité informace o výkonu pro použití AdlCopy

AdlCopy podporuje kopírování dat obsahujících tisíce souborů a složek. Pokud však narazíte na problémy s kopírováním velké datové sady, můžete soubory nebo složky distribuovat do menších podsložek. AdlCopy byl vytvořen pro kopie ad hoc. Pokud se pokoušíte kopírovat data opakovaně, měli byste zvážit použití [Azure Data Factory,](../data-factory/connector-azure-data-lake-store.md) která poskytuje úplnou správu kolem operací kopírování.

## <a name="release-notes"></a>Poznámky k verzi

* 1.0.13 – Pokud kopírujete data do stejného účtu Azure Data Lake Storage Gen1 přes více příkazů adlcopy, už nemusíte znovu zadávat přihlašovací údaje pro každé spuštění. Adlcopy nyní uloží tyto informace do mezipaměti ve více spuštěních.

## <a name="next-steps"></a>Další kroky

* [Zabezpečení dat ve službě Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Použití Azure Data Lake Analytics s datelake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Použití Azure HDInsight s úložištěm datových jezer Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

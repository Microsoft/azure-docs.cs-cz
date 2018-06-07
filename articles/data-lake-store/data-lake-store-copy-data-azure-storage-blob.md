---
title: Ke zkopírování dat z úložiště objektů BLOB Azure do Data Lake Store | Microsoft Docs
description: Pomocí nástroje AdlCopy ke zkopírování dat z úložiště objektů BLOB Azure do Data Lake Store
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: dc273ef8-96ef-47a6-b831-98e8a777a5c1
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 7422c84851c766b608bc5951ec0472c8976e7ec0
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34624263"
---
# <a name="copy-data-from-azure-storage-blobs-to-data-lake-store"></a>Kopírování dat z Azure Storage Blob do služby Data Lake Store
> [!div class="op_single_selector"]
> * [Pomocí DistCp](data-lake-store-copy-data-wasb-distcp.md)
> * [Pomocí AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Azure Data Lake Store poskytuje nástroj příkazového řádku, [AdlCopy](http://aka.ms/downloadadlcopy), ke zkopírování dat z následujících zdrojů:

* Z Azure úložiště objektů BLOB do Data Lake Store. AdlCopy nelze použít ke zkopírování dat z Data Lake Store do objektů BLOB služby Azure Storage.
* Mezi dvěma účty Azure Data Lake Store.

Navíc můžete použít nástroj AdlCopy ve dvou různých režimech:

* **Samostatné**, kde nástroj používá Data Lake Store prostředky k provedení úlohy.
* **Pomocí účtu Data Lake Analytics**, kdy se jednotky přiřazené k vašemu účtu Data Lake Analytics používá k provedení operace kopírování. Můžete chtít tuto možnost použijte, pokud chcete provádět úlohy kopírování předvídatelný způsobem.

## <a name="prerequisites"></a>Požadavky
Je nutné, abyste před zahájením tohoto článku měli tyto položky:

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Objektů BLOB služby Azure Storage** kontejneru určitými daty.
* **Účet Azure Data Lake Store**. Pokyny o tom, jak vytvořit najdete v tématu [Začínáme s Azure Data Lake Store](data-lake-store-get-started-portal.md)
* **Účtu Azure Data Lake Analytics (volitelné)** -najdete v části [Začínáme s Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md) pokyny o tom, jak vytvořit účet Data Lake Store.
* **Nástroj AdlCopy**. Nainstalujte nástroj AdlCopy z [ http://aka.ms/downloadadlcopy ](http://aka.ms/downloadadlcopy).

## <a name="syntax-of-the-adlcopy-tool"></a>Syntaxe nástroje AdlCopy
Použijte následující syntaxi pro práci s nástrojem AdlCopy

    AdlCopy /Source <Blob or Data Lake Store source> /Dest <Data Lake Store destination> /SourceKey <Key for Blob account> /Account <Data Lake Analytics account> /Unit <Number of Analytics units> /Pattern

Parametry v syntaxi jsou následující:

| Možnost | Popis |
| --- | --- |
| Zdroj |Určuje umístění zdrojových dat v objektu blob úložiště Azure. Zdrojem může být kontejner objektů blob, objekt blob nebo jiný účet Data Lake Store. |
| Cíle |Určuje cíl Data Lake Store pro kopírování. |
| SourceKey |Určuje přístupový klíč úložiště pro zdrojový objekt blob úložiště Azure. To je potřeba, pouze pokud je zdroj kontejner objektů blob nebo objekt blob. |
| Účet |**Volitelné**. Použijte, pokud chcete spustit úlohu kopírování pomocí účtu Azure Data Lake Analytics. Pokud použijete možnost /Account v syntaxi, ale nezadávejte účet Data Lake Analytics, AdlCopy používá výchozí účet pro spuštění úlohy. Navíc pokud použijete tuto možnost, musíte přidat zdroj (Azure Storage Blob) a cíl (Azure Data Lake Store) jako zdroje dat pro váš účet Data Lake Analytics. |
| Jednotky |Určuje počet jednotek Data Lake Analytics, které se použijí pro úlohu kopírování. Tato možnost je povinná, pokud použijete **/účet** možnost zadat účet Data Lake Analytics. |
| Vzor |Určuje vzor regulárního výrazu, která určuje, které objekty BLOB nebo připojené soubory. AdlCopy používá odpovídající malá a velká písmena. Výchozím způsobem používaným při zadat žádné vzor je zkopírujte všechny položky. Zadání více vzorů souborů není podporováno. |

## <a name="use-adlcopy-as-standalone-to-copy-data-from-an-azure-storage-blob"></a>Použít AdlCopy (jako samostatná) ke zkopírování dat z objektu blob Azure Storage
1. Otevřete příkazový řádek a přejděte do adresáře, kde AdlCopy je nainstalován, obvykle `%HOMEPATH%\Documents\adlcopy`.
2. Spusťte následující příkaz pro kopírování konkrétní objekt blob z kontejneru zdroje do Data Lake Store:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

    Příklad:

        AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log /dest swebhdfs://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

    >[AZURE.NOTE] Syntaxi výše Určuje soubor, který se má zkopírovat do složky v účtu Data Lake Store. Nástroj AdlCopy vytvoří složku, pokud zadaný název složky neexistuje.

    Zobrazí se výzva k zadání přihlašovacích údajů pro předplatné Azure, ve kterém máte účtu Data Lake Store. Zobrazí se výstup podobný následujícímu:

        Initializing Copy.
        Copy Started.
        100% data copied.
        Finishing Copy.
        Copy Completed. 1 file copied.

1. Všechny objekty BLOB můžete také zkopírovat z jednoho kontejneru typu do účtu Data Lake Store pomocí následujícího příkazu:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/ /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>        

    Příklad:

        AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest adl://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

### <a name="performance-considerations"></a>Otázky výkonu

Pokud kopírujete z účtu úložiště objektů Blob Azure, můžete být omezeny během kopírování na straně úložiště objektů blob. To způsobí snížení výkonu úlohu kopírování. Další informace o omezení Azure Blob Storage najdete v tématu omezení Azure Storage v [předplatného Azure a omezení služby](../azure-subscription-service-limits.md).

## <a name="use-adlcopy-as-standalone-to-copy-data-from-another-data-lake-store-account"></a>Použít AdlCopy (jako samostatná) ke zkopírování dat z jiného účtu Data Lake Store
Můžete taky AdlCopy ke kopírování dat mezi dvěma účty Data Lake Store.

1. Otevřete příkazový řádek a přejděte do adresáře, kde AdlCopy je nainstalován, obvykle `%HOMEPATH%\Documents\adlcopy`.
2. Spusťte následující příkaz pro kopírování konkrétní soubor z jednoho účtu Data Lake Store do jiného.

        AdlCopy /Source adl://<source_adls_account>.azuredatalakestore.net/<path_to_file> /dest adl://<dest_adls_account>.azuredatalakestore.net/<path>/

    Příklad:

        AdlCopy /Source adl://mydatastore.azuredatalakestore.net/mynewfolder/909f2b.log /dest adl://mynewdatalakestore.azuredatalakestore.net/mynewfolder/

   > [!NOTE]
   > Syntaxi výše Určuje soubor, který se má zkopírovat do složky v cílovém účtu Data Lake Store. Nástroj AdlCopy vytvoří složku, pokud zadaný název složky neexistuje.
   >
   >

    Zobrazí se výzva k zadání přihlašovacích údajů pro předplatné Azure, ve kterém máte účtu Data Lake Store. Zobrazí se výstup podobný následujícímu:

        Initializing Copy.
        Copy Started.|
        100% data copied.
        Finishing Copy.
        Copy Completed. 1 file copied.
3. Následující příkaz zkopíruje všechny soubory z určité složky ve zdrojovém účtu Data Lake Store do složky v cílovém účtu Data Lake Store.

        AdlCopy /Source adl://mydatastore.azuredatalakestore.net/mynewfolder/ /dest adl://mynewdatalakestore.azuredatalakestore.net/mynewfolder/

### <a name="performance-considerations"></a>Otázky výkonu

Při použití AdlCopy jako samostatný nástroj, kopie je spustit na sdílené, spravované prostředky Azure. Výkon, které může dojít v tomto prostředí závisí na zatížení systému a dostupné prostředky. Tento režim je nejvhodnější pro malé přenosy na základě ad hoc. Žádné parametry musí být přizpůsobená při použití AdlCopy jako samostatný nástroj.

## <a name="use-adlcopy-with-data-lake-analytics-account-to-copy-data"></a>Pomocí AdlCopy (s účtem Data Lake Analytics) ke kopírování dat
Váš účet Data Lake Analytics můžete taky spustit úlohu AdlCopy ke zkopírování dat z Azure úložiště objektů BLOB do Data Lake Store. Tato možnost by obvykle použijte, pokud data, která mají být přesunuta, je v rozsahu gigabajtech a terabajty a chcete, aby propustnost lepší a předvídatelný výkon.

Pomocí účtu Data Lake Analytics s AdlCopy zkopírovat z objektu Blob úložiště Azure, je nutné přidat zdroj (Azure Storage Blob) jako zdroj dat pro váš účet Data Lake Analytics. Pokyny k přidání dalších zdrojů dat do účtu Data Lake Analytics najdete v tématu [zdrojů dat účtu Správa Data Lake Analytics](../data-lake-analytics/data-lake-analytics-manage-use-portal.md#manage-data-sources).

> [!NOTE]
> Pokud kopírujete z účtu Azure Data Lake Store jako zdroj pomocí účtu Data Lake Analytics, není potřeba přidružit účet Data Lake Store účtu Data Lake Analytics. Požadavek na zdrojové úložiště přidružit účet Data Lake Analytics je jenom v případě, že zdroj je účet úložiště Azure.
>
>

Spusťte následující příkaz pro kopírování z objektu blob Azure Storage k účtu Data Lake Store pomocí účtu Data Lake Analytics:

    AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Account <data_lake_analytics_account> /Unit <number_of_data_lake_analytics_units_to_be_used>

Příklad:

    AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest swebhdfs://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Account mydatalakeanalyticaccount /Units 2

Podobně spusťte následující příkaz, který zkopírujte všechny soubory z konkrétní složky ve zdrojovém účtu Data Lake Store do složky v cílový účet Data Lake Store pomocí účtu Data Lake Analytics:

    AdlCopy /Source adl://mysourcedatalakestore.azuredatalakestore.net/mynewfolder/ /dest adl://mydestdatastore.azuredatalakestore.net/mynewfolder/ /Account mydatalakeanalyticaccount /Units 2

### <a name="performance-considerations"></a>Otázky výkonu

Při kopírování dat v rozsahu terabajtů, AdlCopy pomocí účtu Azure Data Lake Analytics poskytuje lepší a více předvídatelný výkon. Parametr, který by měl být přizpůsobená je počet Azure Data Lake Analytics jednotky pro úlohu kopírování. Zvýšením počtu jednotek zvýší výkon vaší úlohu kopírování. Každý soubor, který se má zkopírovat, můžete použít maximální jednu jednotku. Určení další jednotky, než je počet souborů, které jsou kopírovány nebude zvýšit výkon.

## <a name="use-adlcopy-to-copy-data-using-pattern-matching"></a>Použít AdlCopy ke zkopírování dat pomocí shoda vzoru
V této části můžete další informace o použití AdlCopy ke kopírování dat ze zdroje (v našem příkladu níže používáme Azure Storage Blob) na účet Data Lake Store cílové použití porovnávání vzorů. Například můžete použít následující postup pro kopírování všech souborů s příponou CSV z zdrojový objekt blob do cílového umístění.

1. Otevřete příkazový řádek a přejděte do adresáře, kde AdlCopy je nainstalován, obvykle `%HOMEPATH%\Documents\adlcopy`.
2. Spusťte následující příkaz pro kopírování všech souborů s příponou *.csv z konkrétní objekt blob z kontejneru zdroje do Data Lake Store:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Pattern *.csv

    Příklad:

        AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/FoodInspectionData/ /dest adl://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Pattern *.csv

## <a name="billing"></a>Fakturace
* Pokud použijete nástroj AdlCopy jako samostatné vám bude účtován pro náklady na celkový výstup pro přesun dat, pokud není zdrojový účet úložiště Azure ve stejné oblasti jako Data Lake Store.
* Pokud použijete nástroj AdlCopy s vaše Data Lake Analytics účet, standardní [Data Lake Analytics fakturace sazby](https://azure.microsoft.com/pricing/details/data-lake-analytics/) budou platit.

## <a name="considerations-for-using-adlcopy"></a>Důležité informace týkající se použití AdlCopy
* AdlCopy (pro verzi 1.0.5), podporuje kopírování dat ze zdrojů, které se souhrnně mít víc než tisíce souborů a složek. Ale pokud dojde k potížím kopírování velké datové sady, můžete distribuovat soubory a složky do jiné podřízené složky a místo toho použijte cestu pro tyto podsložky jako zdroj.

## <a name="performance-considerations-for-using-adlcopy"></a>Důležité informace o výkonu pro používání AdlCopy

AdlCopy podporuje kopírování dat obsahující tisíce souborů a složek. Ale pokud dojde k potížím kopírování velké datové sady, můžete distribuovat soubory a složky do menší podsložky. AdlCopy bylo vytvořeno pro ad hoc kopie. Pokud chcete zkopírovat data na základě opakování, měli byste zvážit použití [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) poskytuje úplné správy kolem operace kopírování.

## <a name="release-notes"></a>Poznámky k verzi
* 1.0.13 - li zkopírovat data na stejný účet Azure Data Lake Store napříč více příkazů adlcopy, není nutné znovu zadat přihlašovací údaje při každém spuštění už. Adlcopy nyní v mezipaměti tyto informace napříč více spustí.

## <a name="next-steps"></a>Další postup
* [Zabezpečení dat ve službě Data Lake Store](data-lake-store-secure-data.md)
* [Použití Azure Data Lake Analytics se službou Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Použití Azure HDInsight se službou Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)

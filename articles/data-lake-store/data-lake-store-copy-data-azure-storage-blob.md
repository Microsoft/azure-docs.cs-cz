---
title: Kopírování dat z úložiště objektů BLOB Azure do Azure Data Lake Storage Gen1 | Dokumentace Microsoftu
description: Pomocí AdlCopy nástroje pro kopírování dat z úložiště objektů BLOB Azure do Azure Data Lake Storage Gen1
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
ms.openlocfilehash: 0cf5ace29dabd3a55524fe38403a07e3916ea7d6
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2018
ms.locfileid: "44390888"
---
# <a name="copy-data-from-azure-storage-blobs-to-azure-data-lake-storage-gen1"></a>Kopírování dat z úložiště objektů BLOB Azure do Azure Data Lake Storage Gen1
> [!div class="op_single_selector"]
> * [Pomocí DistCp](data-lake-store-copy-data-wasb-distcp.md)
> * [Pomocí AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Poskytuje nástroj příkazového řádku Azure Data Lake Storage Gen1 [AdlCopy](http://aka.ms/downloadadlcopy), zkopírovat data z následujících zdrojů:

* Z Azure Storage BLOB do Data Lake Storage Gen1. Nelze použití AdlCopy ke kopírování dat z Data Lake Storage Gen1 do objektů BLOB Azure Storage.
* Mezi dva účty Azure Data Lake Storage Gen1.

Navíc můžete použít nástroj AdlCopy ve dvou různých režimech:

* **Samostatné**, kde nástroj používá Data Lake Storage Gen1 prostředky k provedení úlohy.
* **Účet Data Lake Analytics pomocí**, kde se jednotky přiřazené k vašemu účtu Data Lake Analytics používá pro operaci kopírování. Můžete chtít tuto možnost použijte, pokud chcete provádět úlohy kopírování předvídatelným způsobem.

## <a name="prerequisites"></a>Požadavky
Je nutné, abyste před zahájením tohoto článku měli tyto položky:

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure Storage blob** kontejneru s daty.
* **Účet Azure Data Lake Storage Gen1**. Pokyny k jeho vytvoření najdete v tématu [Začínáme s Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* **Účet Azure Data Lake Analytics (volitelné)** – viz [Začínáme s Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md) pokyny o tom, jak vytvořit účet Data Lake Analytics.
* **Nástroj AdlCopy**. Nainstalujte nástroj AdlCopy z [ http://aka.ms/downloadadlcopy ](http://aka.ms/downloadadlcopy).

## <a name="syntax-of-the-adlcopy-tool"></a>Syntaxi nástroje pomocí AdlCopy
Použijte následující syntaxi pro práci s nástrojem AdlCopy

    AdlCopy /Source <Blob or Data Lake Storage Gen1 source> /Dest <Data Lake Storage Gen1 destination> /SourceKey <Key for Blob account> /Account <Data Lake Analytics account> /Units <Number of Analytics units> /Pattern

Parametry v syntaxi popsané níže:

| Možnost | Popis |
| --- | --- |
| Zdroj |Určuje umístění zdrojových dat v Azure storage blob. Zdrojem může být kontejner objektů blob, objekt blob nebo jiného účtu Data Lake Storage Gen1. |
| cíl |Určuje cíl Gen1 úložiště Data Lake pro kopírování. |
| SourceKey |Určuje přístupový klíč úložiště pro zdrojový objekt blob úložiště Azure. To je potřeba, pouze pokud je zdroj kontejner objektů blob nebo objekt blob. |
| Účet |**Volitelné**. Použijte, pokud chcete spustit úlohu kopírování pomocí účtu Azure Data Lake Analytics. Pokud používáte parametr/Account v syntaxi, ale nezadávejte účet Data Lake Analytics, AdlCopy používá výchozí účet pro spuštění úlohy. Navíc pokud použijete tuto možnost, musíte přidat zdroj (Azure Storage Blob) a cíl (Azure Data Lake Storage Gen1) jako zdroj dat pro svůj účet Data Lake Analytics. |
| Jednotky |Určuje počet jednotek Data Lake Analytics, které se použije pro úlohu kopírování. Tato možnost je povinná, pokud použijete **účtu** můžete zadat účet Data Lake Analytics. |
| Vzor |Určuje vzor regulárního výrazu, která určuje, jaké objekty BLOB nebo soubory ke zkopírování. AdlCopy používá odpovídající malá a velká písmena. Výchozím způsobem používaným-li zadána žádná vzor je zkopírovat všechny položky. Zadávání více vzory souborů se nepodporuje. |

## <a name="use-adlcopy-as-standalone-to-copy-data-from-an-azure-storage-blob"></a>Použití AdlCopy ke kopírování dat z objektu blob služby Azure Storage (jako samostatný)
1. Otevřete příkazový řádek a přejděte do adresáře, kde AdlCopy je nainstalovaný, obvykle `%HOMEPATH%\Documents\adlcopy`.
2. Spusťte následující příkaz pro kopírování jen konkrétní objekt blob ze zdrojového kontejneru do složky Data Lake Storage Gen1:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

    Příklad:

        AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log /dest swebhdfs://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

    >[!NOTE] 
    >Výše uvedené syntaxe Určuje soubor, který má být zkopírován do složky v účtu Data Lake Storage Gen1. Nástroj AdlCopy vytvoří složku, pokud neexistuje zadaný název složky.

    Zobrazí se výzva k zadání přihlašovacích údajů předplatného Azure, ve kterém máte účtu Data Lake Storage Gen1. Zobrazí se výstup podobný následujícímu:

        Initializing Copy.
        Copy Started.
        100% data copied.
        Finishing Copy.
        Copy Completed. 1 file copied.

1. Můžete také zkopírovat všechny objekty BLOB z jednoho kontejneru do účtu Data Lake Storage Gen1 pomocí následujícího příkazu:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/ /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>        

    Příklad:

        AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest adl://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

### <a name="performance-considerations"></a>Otázky výkonu

Pokud kopírujete z účtu služby Azure Blob Storage, může omezí během kopírování na straně úložiště objektů blob. To bude mít negativní dopad na výkon úlohy kopírování. Další informace o omezeních úložiště objektů Blob v Azure najdete v tématu limity služby Azure Storage na [předplatného Azure a omezení služeb](../azure-subscription-service-limits.md).

## <a name="use-adlcopy-as-standalone-to-copy-data-from-another-data-lake-storage-gen1-account"></a>Použití AdlCopy ke kopírování dat z jiného účtu Data Lake Storage Gen1 (jako samostatný)
Můžete také použití AdlCopy ke kopírování dat mezi dva účty Data Lake Storage Gen1.

1. Otevřete příkazový řádek a přejděte do adresáře, kde AdlCopy je nainstalovaný, obvykle `%HOMEPATH%\Documents\adlcopy`.
2. Spusťte následující příkaz pro kopírování z jednoho účtu Data Lake Storage Gen1 konkrétního souboru do jiného.

        AdlCopy /Source adl://<source_adlsg1_account>.azuredatalakestore.net/<path_to_file> /dest adl://<dest_adlsg1_account>.azuredatalakestore.net/<path>/

    Příklad:

        AdlCopy /Source adl://mydatastorage.azuredatalakestore.net/mynewfolder/909f2b.log /dest adl://mynewdatalakestorage.azuredatalakestore.net/mynewfolder/

   > [!NOTE]
   > Výše uvedené syntaxe Určuje soubor, který má být zkopírován do složky v cílovém účtu Data Lake Storage Gen1. Nástroj AdlCopy vytvoří složku, pokud neexistuje zadaný název složky.
   >
   >

    Zobrazí se výzva k zadání přihlašovacích údajů předplatného Azure, ve kterém máte účtu Data Lake Storage Gen1. Zobrazí se výstup podobný následujícímu:

        Initializing Copy.
        Copy Started.|
        100% data copied.
        Finishing Copy.
        Copy Completed. 1 file copied.
3. Následující příkaz zkopíruje všechny soubory z konkrétní složky v účtu Data Lake Storage Gen1 zdroje do složky v účtu Data Lake Storage Gen1 cíli.

        AdlCopy /Source adl://mydatastorage.azuredatalakestore.net/mynewfolder/ /dest adl://mynewdatalakestorage.azuredatalakestore.net/mynewfolder/

### <a name="performance-considerations"></a>Otázky výkonu

Při použití AdlCopy jako samostatný nástroj, kopie je spustit na sdílené, spravované prostředky Azure. Výkon, který se může zobrazit v tomto prostředí závisí na zatížení systému a dostupné prostředky. Tento režim je nejvhodnější pro malé přenosy na základě ad hoc. Žádné parametry musí být vyladěné při použití AdlCopy jako samostatný nástroj.

## <a name="use-adlcopy-with-data-lake-analytics-account-to-copy-data"></a>Použití AdlCopy ke kopírování dat (s účtem Data Lake Analytics)
Váš účet Data Lake Analytics můžete použít také ke spuštění úlohy AdlCopy ke kopírování dat z Azure storage BLOB do Data Lake Storage Gen1. Tuto možnost byste obvykle použili, když jsou data přesunout v rozsahu od terabajtů po gigabajtech a chcete, aby propustnost lepší a předvídatelný výkon.

Použití účtu Data Lake Analytics pomocí AdlCopy ke kopírování z úložiště objektů Blob Azure, je nutné přidat zdroj (Azure Storage Blob) jako zdroj dat pro svůj účet Data Lake Analytics. Pokyny k přidání dalších zdrojů dat do účtu Data Lake Analytics najdete v tématu [zdrojů dat účtu Správa služby Data Lake Analytics](../data-lake-analytics/data-lake-analytics-manage-use-portal.md#manage-data-sources).

> [!NOTE]
> Pokud kopírujete z účtu služby Azure Data Lake Storage Gen1 jako zdroj, který používá účet Data Lake Analytics, není nutné přidružit účet Data Lake Storage Gen1 k účtu Data Lake Analytics. Požadavek na zdrojovém úložišti přidružit k účtu Data Lake Analytics je pouze v případě, že zdroj je účet služby Azure Storage.
>
>

Spusťte následující příkaz pro kopírování z Azure Storage blob do účtu Data Lake Storage Gen1 pomocí účtu Data Lake Analytics:

    AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Account <data_lake_analytics_account> /Units <number_of_data_lake_analytics_units_to_be_used>

Příklad:

    AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest swebhdfs://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Account mydatalakeanalyticaccount /Units 2

Podobně spusťte následující příkaz zkopíruje všechny soubory z konkrétní složky v účtu Data Lake Storage Gen1 zdroje do složky v cílovém účtu Data Lake Storage Gen1 pomocí účtu Data Lake Analytics:

    AdlCopy /Source adl://mysourcedatalakestorage.azuredatalakestore.net/mynewfolder/ /dest adl://mydestdatastorage.azuredatalakestore.net/mynewfolder/ /Account mydatalakeanalyticaccount /Units 2

### <a name="performance-considerations"></a>Otázky výkonu

Při kopírování dat v rozsahu od terabajtů, pomocí účtu Azure Data Lake Analytics pomocí AdlCopy poskytuje lepší a více předvídatelný výkon. Parametr, který by měly být vyladěné je počet jednotek Azure Data Lake Analytics má použít pro úlohu kopírování. Zvýšením počtu jednotek zvýší výkon vaší úlohy kopírování. Každý soubor, které se mají zkopírovat, můžete použít maximální jednu jednotku. Určení víc jednotek, než je počet souborů, které jsou kopírovány nedojde ke zvýšení výkonu.

## <a name="use-adlcopy-to-copy-data-using-pattern-matching"></a>Použití AdlCopy ke kopírování dat pomocí porovnávání vzorů
V této části se dozvíte, jak k použití AdlCopy ke kopírování ze zdroje dat (v našem příkladu používáme Azure Storage Blob) k určení Data Lake Storage Gen1 účtu pomocí porovnávání vzorů. Například můžete použít následující postup bude kopírováno všechny soubory s příponou CSV zdrojového objektu blob do cíle.

1. Otevřete příkazový řádek a přejděte do adresáře, kde AdlCopy je nainstalovaný, obvykle `%HOMEPATH%\Documents\adlcopy`.
2. Spusťte následující příkaz pro kopírování všech souborů s příponou *.csv z konkrétní objekt blob z kontejneru zdroje do složky Data Lake Storage Gen1:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Pattern *.csv

    Příklad:

        AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/FoodInspectionData/ /dest adl://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Pattern *.csv

## <a name="billing"></a>Fakturace
* Pokud použijete nástroj AdlCopy jako samostatné vám budou účtovat náklady na odchozí přenosy pro přesun dat, pokud není zdrojový účet úložiště Azure ve stejné oblasti jako účet Data Lake Storage Gen1.
* Pokud se vaše Data Lake Analytics pomocí nástroje AdlCopy účtu, standardní [Data Lake Analytics sazeb za](https://azure.microsoft.com/pricing/details/data-lake-analytics/) platit.

## <a name="considerations-for-using-adlcopy"></a>Důležité informace týkající se pomocí AdlCopy
* AdlCopy (pro verzi 1.0.5), podporuje kopírování dat ze zdroje, které společně existovat více než tisíce souborů a složek. Ale pokud narazíte na problémy kopírování velkou datovou sadu, můžete distribuovat soubory nebo složky do jiné podřízené složky a místo toho použijte cestu pro tyto podřízené složky jako zdroj.

## <a name="performance-considerations-for-using-adlcopy"></a>Faktory ovlivňující výkon u pomocí AdlCopy

AdlCopy podporuje kopírování dat, který obsahuje tisíce souborů a složek. Pokud narazíte na problémy kopírování velkou datovou sadu, můžete do menších podsložky můžete distribuovat soubory či složky. AdlCopy byla vytvořena pro ad hoc kopie. Pokud se pokoušíte kopírování dat pravidelně, měli byste zvážit použití [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) , která poskytuje úplné řízení kolem operace kopírování.

## <a name="release-notes"></a>Poznámky k verzi
* 1.0.13 – Pokud se kopírování dat do stejného účtu Azure Data Lake Storage Gen1 napříč více příkazů adlcopy, nepotřebujete znovu zadat přihlašovací údaje pro každé spuštění už. Adlcopy nyní ukládá tyto informace během různých spuštění.

## <a name="next-steps"></a>Další postup
* [Zabezpečení dat ve službě Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Použití Azure Data Lake Analytics s Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Použití Azure HDInsight s Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

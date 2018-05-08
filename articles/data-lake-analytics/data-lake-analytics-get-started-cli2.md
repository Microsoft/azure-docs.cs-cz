---
title: Začínáme s Azure Data Lake Analytics pomocí Azure CLI 2.0 | Dokumentace Microsoftu
description: 'Naučte se používat rozhraní příkazového řádku Azure CLI 2.0 k vytvoření účtu Data Lake Analytics, vytvoření úlohy Data Lake Analytics pomocí U-SQL a odeslání úlohy. '
services: data-lake-analytics
documentationcenter: ''
author: saveenr
manager: saveenr
editor: cgronlun
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/18/2017
ms.author: jgao
ms.openlocfilehash: e7ce65446a6af49f15b3ce1881757b16f8fcaa35
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2018
---
# <a name="get-started-with-azure-data-lake-analytics-using-azure-cli-20"></a>Začínáme s Azure Data Lake Analytics s využitím rozhraní Azure CLI 2.0
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

V tomto kurzu vyvinete úlohu, která načte soubor hodnot oddělených tabulátory (TSV) a převede ho na soubor hodnot oddělených čárkami (CSV). Pokud chcete použít jiné podporované nástroje a absolvovat stejný kurz, použijte rozevírací seznam nahoře v této části.

## <a name="prerequisites"></a>Požadavky
Před zahájením tohoto kurzu musíte mít tyto položky:

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure CLI 2.0**. Viz téma [Instalace a konfigurace rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="log-in-to-azure"></a>Přihlášení k Azure

Přihlášení k předplatnému Azure:

```
azurecli
az login
```

Zobrazí se výzva k přechodu na adresu URL a zadání ověřovacího kódu.  Pak postupujte podle pokynů k zadání přihlašovacích údajů.

Jakmile jste přihlášení, příkaz login zobrazí seznam vašich předplatných.

Použití konkrétního předplatného:

```
az account set --subscription <subscription id>
```

## <a name="create-data-lake-analytics-account"></a>Vytvoření účtu Data Lake Analytics
Je nutné, abyste před spuštěním jakékoli úlohy měli účet Data Lake Analytics. Pokud chcete vytvořit účet Data Lake Analytics, je nutné zadat následující položky:

* **Skupina prostředků Azure**. Účet Data Lake Analytics se musí vytvořit v rámci Skupiny prostředků Azure. [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) umožňuje pracovat s prostředky v aplikaci jako se skupinou. Všechny prostředky pro aplikaci můžete nasadit, aktualizovat nebo odstranit v rámci jediné koordinované operace.  

Výpis seznamu stávajících skupin prostředků v rámci vašeho předplatného:

```
az group list
```

Pokud chcete vytvořit novou skupinu prostředků:

```
az group create --name "<Resource Group Name>" --location "<Azure Location>"
```

* **Název účtu Data Lake Analytics**. Každý účtu Data Lake Analytics má název.
* **Umístění**. Použijte jedno z datových center Azure, které podporuje Data Lake Analytics.
* **Výchozí účet Data Lake Store:** Každý účet Data Lake Analytics má výchozí účet Data Lake Store.

Zobrazení seznamu stávajícího účtu Data Lake Store:

```
az dls account list
```

Vytvoření nového účtu Data Lake Store:

```azurecli
az dls account create --account "<Data Lake Store Account Name>" --resource-group "<Resource Group Name>"
```

Pokud chcete vytvořit účet Data Lake Analytics, použijte následující syntaxi:

```
az dla account create --account "<Data Lake Analytics Account Name>" --resource-group "<Resource Group Name>" --location "<Azure location>" --default-data-lake-store "<Default Data Lake Store Account Name>"
```

Po vytvoření účtu můžete použít následující příkazy k vypsání seznamu účtů a zobrazení podrobností o účtu:

```
az dla account list
az dla account show --account "<Data Lake Analytics Account Name>"            
```

## <a name="upload-data-to-data-lake-store"></a>Nahrání dat do Data Lake Store
V tomto kurzu zpracujete několik protokolů hledání.  Protokol hledání se dá uložit buď do úložiště Data Lake Store, nebo do úložiště objektů Azure Blob.

Azure Portal poskytuje uživatelské rozhraní pro kopírování některých ukázkových datových souborů (včetně souboru protokolu hledání) do výchozího účtu Data Lake Store. Pokud chcete nahrát data do výchozího účtu Data Lake Store, informace najdete v části [Příprava zdrojových dat](data-lake-analytics-get-started-portal.md).

Pokud chcete nahrát soubory pomocí rozhraní příkazového řádku CLI 2.0, použijte následující příkazy:

```
az dls fs upload --account "<Data Lake Store Account Name>" --source-path "<Source File Path>" --destination-path "<Destination File Path>"
az dls fs list --account "<Data Lake Store Account Name>" --path "<Path>"
```

Data Lake Analytics má také přístup k úložišti objektů Azure Blob.  Pokud chcete nahrát data do úložiště objektů Azure Blob, informace najdete v tématu [Použití rozhraní příkazového řádku Azure s Azure Storage](../storage/common/storage-azure-cli.md).

## <a name="submit-data-lake-analytics-jobs"></a>Odesílání úloh Data Lake Analytics
Úlohy Data Lake Analytics se píšou v jazyce U-SQL. Další informace o U-SQL najdete v tématu [Začínáme s jazykem U-SQL](data-lake-analytics-u-sql-get-started.md) a [Referenční informace pro jazyk U-SQL](http://go.microsoft.com/fwlink/?LinkId=691348).

**Postup vytvoření skriptu úlohy Data Lake Analytics**

Vytvořte textový soubor s následujícím skriptem U-SQL a uložte ho do pracovní stanice:

```
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
```

Tento skript U-SQL přečte zdrojový datový soubor pomocí **Extractors.Tsv()** a potom pomocí **Outputters.Csv()** vytvoří soubor .csv.

Pokud zdrojový soubor nekopírujete do jiného umístění, tyto dvě cesty neupravujte.  Data Lake Analytics vytvoří výstupní složku, pokud neexistuje.

U souborů uložených ve výchozích účtech Data Lake Store je jednodušší použít relativní cesty. Můžete také použít absolutní cesty.  Příklad:

```
adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
```

Pro přístup k souborům v propojených účtech Storage je nutné použít absolutní cesty.  V případě souborů uložených v propojeném účtu Azure Storage je syntaxe následující:

```
wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv
```

> [!NOTE]
> Kontejner Azure Blob s veřejnými objekty blob není podporován.      
> Kontejner Azure Blob s veřejnými kontejnery není podporován.      
>

**Odesílání úloh**

Při odeslání úlohy použijte následující syntaxi.

```
az dla job submit --account "<Data Lake Analytics Account Name>" --job-name "<Job Name>" --script "<Script Path and Name>"
```

Příklad:

```
az dla job submit --account "myadlaaccount" --job-name "myadlajob" --script @"C:\DLA\myscript.txt"
```

**Seznam úloh a zobrazení podrobností o úloze**

```
azurecli
az dla job list --account "<Data Lake Analytics Account Name>"
az dla job show --account "<Data Lake Analytics Account Name>" --job-identity "<Job Id>"
```

**Zrušení úloh**

```
az dla job cancel --account "<Data Lake Analytics Account Name>" --job-identity "<Job Id>"
```

## <a name="retrieve-job-results"></a>Načtení výsledků úlohy

Po dokončení úlohy můžete pomocí následujících příkazů zobrazit výpis výstupních souborů a soubory stáhnout:

```
az dls fs list --account "<Data Lake Store Account Name>" --source-path "/Output" --destination-path "<Destination>"
az dls fs preview --account "<Data Lake Store Account Name>" --path "/Output/SearchLog-from-Data-Lake.csv"
az dls fs preview --account "<Data Lake Store Account Name>" --path "/Output/SearchLog-from-Data-Lake.csv" --length 128 --offset 0
az dls fs download --account "<Data Lake Store Account Name>" --source-path "/Output/SearchLog-from-Data-Lake.csv" --destination-path "<Destination Path and File Name>"
```

Příklad:

```
az dls fs download --account "myadlsaccount" --source-path "/Output/SearchLog-from-Data-Lake.csv" --destination-path "C:\DLA\myfile.csv"
```

## <a name="next-steps"></a>Další kroky

* Referenční dokument Data Lake Analytics CLI 2.0 najdete v tématu [Data Lake Analytics](https://docs.microsoft.com/cli/azure/dla).
* Referenční dokument Data Lake Store CLI 2.0 najdete v tématu [Data Lake Store](https://docs.microsoft.com/cli/azure/dls).
* Pokud chcete zobrazit komplexnější dotaz, přejděte k tématu [Analýza webových protokolů pomocí Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).

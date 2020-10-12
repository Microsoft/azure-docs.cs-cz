---
title: Správa Azure Data Lake Analytics pomocí rozhraní příkazového řádku Azure
description: Tento článek popisuje, jak pomocí rozhraní příkazového řádku Azure spravovat Data Lake Analytics úlohy, zdroje dat & uživatele.
services: data-lake-analytics
ms.assetid: 4e5a3a0a-6d7f-43ed-aeb5-c3b3979a1e0a
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 01/29/2018
ms.openlocfilehash: f91619860b577981d9717904a3d4a3074c2eaf0f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91320842"
---
# <a name="manage-azure-data-lake-analytics-using-the-azure-command-line-interface-cli"></a>Správa Azure Data Lake Analytics pomocí rozhraní příkazového řádku Azure (CLI)

[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Naučte se spravovat účty Azure Data Lake Analytics, zdroje dat, uživatele a úlohy pomocí Azure CLI. Pokud chcete zobrazit témata správy pomocí jiných nástrojů, klikněte na kartu nahoře.


## <a name="prerequisites"></a>Požadavky

Než začnete tento kurz, musíte mít následující prostředky:

* Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* Azure CLI Viz téma [Instalace a konfigurace rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

  * Pokud chcete absolvovat tuto ukázku, stáhněte a nainstalujte **předběžnou verzi** [nástrojů příkazového řádku Azure](https://github.com/MicrosoftBigData/AzureDataLake/releases).

* Proveďte ověření pomocí `az login` příkazu a vyberte předplatné, které chcete použít. Další informace týkající se ověřování pomocí pracovního nebo školního účtu najdete v tématu [Připojení k předplatnému Azure z rozhraní příkazového řádku Azure](/cli/azure/authenticate-azure-cli).

   ```azurecli
   az login
   az account set --subscription <subscription id>
   ```

   Nyní máte přístup k příkazům Data Lake Analytics a Data Lake Store. Spusťte následující příkaz pro výpis Data Lake Store a Data Lake Analytics příkazů:

   ```azurecli
   az dls -h
   az dla -h
   ```

## <a name="manage-accounts"></a>Správa účtů

Před spuštěním Data Lake Analytics úloh musíte mít účet Data Lake Analytics. Na rozdíl od Azure HDInsight neplatíte za účet Analytics, pokud není úloha spuštěná. Platíte jenom za čas, kdy je úloha spuštěná.  Další informace najdete v tématu [přehled Azure Data Lake Analytics](data-lake-analytics-overview.md).  

### <a name="create-accounts"></a>Vytváření účtů

Spusťte následující příkaz, který vytvoří účet Data Lake. 

   ```azurecli
   az dla account create --account "<Data Lake Analytics account name>" --location "<Location Name>" --resource-group "<Resource Group Name>" --default-data-lake-store "<Data Lake Store account name>"
   ```

### <a name="update-accounts"></a>Aktualizace účtů

Následující příkaz aktualizuje vlastnosti existujícího účtu Data Lake Analytics.

   ```azurecli
   az dla account update --account "<Data Lake Analytics Account Name>" --firewall-state "Enabled" --query-store-retention 7
   ```

### <a name="list-accounts"></a>Výpis účtů

Výpis účtů Data Lake Analytics v rámci konkrétní skupiny prostředků

   ```azurecli
   az dla account list "<Resource group name>"
   ```

## <a name="get-details-of-an-account"></a>Získání podrobností o účtu

   ```azurecli
   az dla account show --account "<Data Lake Analytics account name>" --resource-group "<Resource group name>"
   ```

### <a name="delete-an-account"></a>Odstranění účtu

   ```azurecli
   az dla account delete --account "<Data Lake Analytics account name>" --resource-group "<Resource group name>"
   ```

## <a name="manage-data-sources"></a>Správa zdrojů dat

Data Lake Analytics aktuálně podporuje následující dva zdroje dat:

* [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
* [Azure Storage](../storage/common/storage-introduction.md)

Když vytváříte účet Analytics, musíte určit účet Azure Data Lake Storage jako výchozí účet úložiště. Výchozí Data Lake účet úložiště se používá k ukládání metadat úlohy a protokolů auditu úlohy. Po vytvoření účtu Analytics můžete přidat další účty Data Lake Storage a účet Azure Storage. 

### <a name="find-the-default-data-lake-store-account"></a>Najít výchozí účet Data Lake Store

Výchozí účet Data Lake Store, který se používá, můžete zobrazit spuštěním `az dla account show` příkazu. Výchozí název účtu je uvedený pod vlastností defaultDataLakeStoreAccount.

   ```azurecli
   az dla account show --account "<Data Lake Analytics account name>"
   ```

### <a name="add-additional-blob-storage-accounts"></a>Přidat další účty úložiště BLOB

   ```azurecli
   az dla account blob-storage add --access-key "<Azure Storage Account Key>" --account "<Data Lake Analytics account name>" --storage-account-name "<Storage account name>"
   ```

> [!NOTE]
> Podporovány jsou pouze krátké názvy služby Blob Storage. Nepoužívejte plně kvalifikovaný název domény, například "myblob.blob.core.windows.net".
>

### <a name="add-additional-data-lake-store-accounts"></a>Přidat další účty Data Lake Store

Následující příkaz aktualizuje zadaný Data Lake Analytics účet o další Data Lake Store účet:

   ```azurecli
   az dla account data-lake-store add --account "<Data Lake Analytics account name>" --data-lake-store-account-name "<Data Lake Store account name>"
   ```

### <a name="update-existing-data-source"></a>Aktualizovat existující zdroj dat

Aktualizace existujícího klíče účtu úložiště objektů BLOB:

   ```azurecli
   az dla account blob-storage update --access-key "<New Blob Storage Account Key>" --account "<Data Lake Analytics account name>" --storage-account-name "<Data Lake Store account name>"
   ```

### <a name="list-data-sources"></a>Seznam zdrojů dat:

Výpis účtů Data Lake Store:

   ```azurecli
   az dla account data-lake-store list --account "<Data Lake Analytics account name>"
   ```

Výpis účtu úložiště objektů BLOB:

   ```azurecli
   az dla account blob-storage list --account "<Data Lake Analytics account name>"
   ```

![Snímek obrazovky, který zobrazuje Azure C L I se zvýrazněnými informacemi o "dataLakeStoreAccounts:".](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-data-source.png)

### <a name="delete-data-sources"></a>Odstranit zdroje dat:

Odstranění účtu Data Lake Store:

   ```azurecli
   az dla account data-lake-store delete --account "<Data Lake Analytics account name>" --data-lake-store-account-name "<Azure Data Lake Store account name>"
   ```

Postup odstranění účtu úložiště BLOB:

   ```azurecli
   az dla account blob-storage delete --account "<Data Lake Analytics account name>" --storage-account-name "<Data Lake Store account name>"
   ```

## <a name="manage-jobs"></a>Správa úloh

Než budete moct vytvořit úlohu, musíte mít účet Data Lake Analytics.  Další informace najdete v tématu [Správa účtů Data Lake Analytics](#manage-accounts).

### <a name="list-jobs"></a>Vypsat úlohy

   ```azurecli
   az dla job list --account "<Data Lake Analytics account name>"
   ```

   ![Zdroj dat seznamu Data Lake Analytics](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-jobs.png)

### <a name="get-job-details"></a>Získat podrobnosti o úloze

   ```azurecli
   az dla job show --account "<Data Lake Analytics account name>" --job-identity "<Job Id>"
   ```

### <a name="submit-jobs"></a>Odeslat úlohy

> [!NOTE]
> Výchozí priorita úlohy je 1000 a výchozí stupeň paralelismu pro úlohu je 1.
>
>    ```azurecli
>    az dla job submit --account "<Data Lake Analytics account name>" --job-name "<Name of your job>" --script "<Script to submit>"
>    ```

### <a name="cancel-jobs"></a>Zrušit úlohy
Pomocí příkazu list Najděte ID úlohy a pak pomocí tlačítka Zrušit úlohu zrušte.

   ```azurecli
   az dla job cancel --account "<Data Lake Analytics account name>" --job-identity "<Job Id>"
   ```

## <a name="pipelines-and-recurrences"></a>Kanály a opakování

### <a name="get-information-about-pipelines-and-recurrences"></a>Získání informací o kanálech a opakováních

Pomocí příkazů `az dla job pipeline` můžete zobrazit informace o kanálu dříve odeslaných úloh.

```azurecli
az dla job pipeline list --account "<Data Lake Analytics Account Name>"

az dla job pipeline show --account "<Data Lake Analytics Account Name>" --pipeline-identity "<Pipeline ID>"
```

Pomocí příkazů `az dla job recurrence` můžete zobrazit informace o opakování dříve odeslaných úloh.

```azurecli
az dla job recurrence list --account "<Data Lake Analytics Account Name>"

az dla job recurrence show --account "<Data Lake Analytics Account Name>" --recurrence-identity "<Recurrence ID>"
```

## <a name="next-steps"></a>Další kroky
* [Přehled služby Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Začínáme s Data Lake Analytics pomocí Azure Portal](data-lake-analytics-get-started-portal.md)
* [Správa Azure Data Lake Analytics pomocí Azure Portal](data-lake-analytics-manage-use-portal.md)
* [Monitorování úloh Azure Data Lake Analytics a odstraňování potíží pomocí webu Azure Portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

---
title: Správa Azure Data Lake Analytics pomocí Azure CLI
description: Tento článek popisuje, jak používat azure cli ke správě úloh Data Lake Analytics, zdroje dat, & uživatele.
services: data-lake-analytics
author: jasonwhowell
ms.author: jasonh
ms.assetid: 4e5a3a0a-6d7f-43ed-aeb5-c3b3979a1e0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: 69a48952ef273acb8cf7eb0ec5968e12b962b622
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454359"
---
# <a name="manage-azure-data-lake-analytics-using-the-azure-command-line-interface-cli"></a>Správa Azure Data Lake Analytics pomocí rozhraní Příkazového řádku Azure (CLI)

[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Zjistěte, jak spravovat účty Azure Data Lake Analytics, zdroje dat, uživatele a úlohy pomocí azure CLI. Chcete-li zobrazit témata správy pomocí jiných nástrojů, klepněte na kartu, která je k vídám výše.


**Požadavky**

Než začnete tento kurz, musíte mít následující zdroje:

* Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* Azure CLI. Viz téma [Instalace a konfigurace rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

   * Pokud chcete absolvovat tuto ukázku, stáhněte a nainstalujte **předběžnou verzi** [nástrojů příkazového řádku Azure](https://github.com/MicrosoftBigData/AzureDataLake/releases).

* Ověřte `az login` pomocí příkazu a vyberte předplatné, které chcete použít. Další informace týkající se ověřování pomocí pracovního nebo školního účtu najdete v tématu [Připojení k předplatnému Azure z rozhraní příkazového řádku Azure](/cli/azure/authenticate-azure-cli).

   ```azurecli
   az login
   az account set --subscription <subscription id>
   ```

   Nyní máte přístup k příkazům Data Lake Analytics a Data Lake Store. Spuštěním následujícího příkazu zobrazíte příkazy Data Lake Store a Data Lake Analytics:

   ```azurecli
   az dls -h
   az dla -h
   ```

## <a name="manage-accounts"></a>Správa účtů

Před spuštěním úloh Data Lake Analytics musíte mít účet Data Lake Analytics. Na rozdíl od Azure HDInsight neplatíte za účet Analytics, když neběží úlohu. Platíte pouze za čas, kdy je spuštěna úloha.  Další informace najdete v [tématu Přehled Azure Data Lake Analytics](data-lake-analytics-overview.md).  

### <a name="create-accounts"></a>Vytvořit účty

Chcete-li vytvořit účet Data Lake, spusťte následující příkaz. 

   ```azurecli
   az dla account create --account "<Data Lake Analytics account name>" --location "<Location Name>" --resource-group "<Resource Group Name>" --default-data-lake-store "<Data Lake Store account name>"
   ```

### <a name="update-accounts"></a>Aktualizovat účty

Následující příkaz aktualizuje vlastnosti existujícího účtu Data Lake Analytics

   ```azurecli
   az dla account update --account "<Data Lake Analytics Account Name>" --firewall-state "Enabled" --query-store-retention 7
   ```

### <a name="list-accounts"></a>Výpis účtů

Seznam účtů Data Lake Analytics v rámci určité skupiny prostředků

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

Služba Data Lake Analytics v současné době podporuje následující dva zdroje dat:

* [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
* [Azure Storage](../storage/common/storage-introduction.md)

Když si vytvoříte účet Analytics, musíte určit účet Azure Data Lake Storage jako výchozí účet úložiště. Výchozí účet úložiště datového jezera se používá k ukládání metadat úlohy a protokolů auditu úloh. Po vytvoření účtu Analytics můžete přidat další účty úložiště data lake storage nebo účet Azure Storage. 

### <a name="find-the-default-data-lake-store-account"></a>Vyhledání výchozího účtu Data Lake Store

Spuštěním příkazu můžete zobrazit výchozí účet `az dla account show` Data Lake Store, který se používá při spuštění příkazu. Výchozí název účtu je uveden pod vlastností defaultDataLakeStoreAccount.

   ```azurecli
   az dla account show --account "<Data Lake Analytics account name>"
   ```

### <a name="add-additional-blob-storage-accounts"></a>Přidání dalších účtů úložiště objektů Blob

   ```azurecli
   az dla account blob-storage add --access-key "<Azure Storage Account Key>" --account "<Data Lake Analytics account name>" --storage-account-name "<Storage account name>"
   ```

> [!NOTE]
> Podporovány jsou pouze krátké názvy úložiště objektů blob. Nepoužívejte fqdn, například "myblob.blob.core.windows.net".
> 

### <a name="add-additional-data-lake-store-accounts"></a>Přidání dalších účtů úložiště datových jezer

Následující příkaz aktualizuje zadaný účet Data Lake Analytics pomocí dalšího účtu Data Lake Store:

   ```azurecli
   az dla account data-lake-store add --account "<Data Lake Analytics account name>" --data-lake-store-account-name "<Data Lake Store account name>"
   ```

### <a name="update-existing-data-source"></a>Aktualizace existujícího zdroje dat

Aktualizace existujícího klíče účtu úložiště objektů Blob:

   ```azurecli
   az dla account blob-storage update --access-key "<New Blob Storage Account Key>" --account "<Data Lake Analytics account name>" --storage-account-name "<Data Lake Store account name>"
   ```

### <a name="list-data-sources"></a>Seznam zdrojů dat:

Seznam účtů Úložiště datových jezer:

   ```azurecli
   az dla account data-lake-store list --account "<Data Lake Analytics account name>"
   ```

Seznam účtu úložiště objektů Blob:

   ```azurecli
   az dla account blob-storage list --account "<Data Lake Analytics account name>"
   ```

![Zdroj dat seznamu Data Lake Analytics](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-data-source.png)

### <a name="delete-data-sources"></a>Odstranění zdrojů dat:
Odstranění účtu Data Lake Store:

   ```azurecli
   az dla account data-lake-store delete --account "<Data Lake Analytics account name>" --data-lake-store-account-name "<Azure Data Lake Store account name>"
   ```

Odstranění účtu úložiště objektů Blob:

   ```azurecli
   az dla account blob-storage delete --account "<Data Lake Analytics account name>" --storage-account-name "<Data Lake Store account name>"
   ```

## <a name="manage-jobs"></a>Správa úloh
Před vytvořením úlohy musíte mít účet Data Lake Analytics.  Další informace najdete v [tématu Správa účtů Data Lake Analytics](#manage-accounts).

### <a name="list-jobs"></a>Seznam úloh

   ```azurecli
   az dla job list --account "<Data Lake Analytics account name>"
   ```

   ![Zdroj dat seznamu Data Lake Analytics](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-jobs.png)

### <a name="get-job-details"></a>Získat podrobnosti o práci

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
Pomocí příkazu seznam vyhledejte ID úlohy a potom ji zrušte pomocí příkazu Cancel.

   ```azurecli
   az dla job cancel --account "<Data Lake Analytics account name>" --job-identity "<Job Id>"
   ```

## <a name="pipelines-and-recurrences"></a>Kanály a opakování

**Získání informací o kanálech a opakováních**

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

## <a name="see-also"></a>Viz také
* [Přehled služby Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Začínáme s Analýzou datových jezer pomocí portálu Azure](data-lake-analytics-get-started-portal.md)
* [Správa Azure Data Lake Analytics pomocí portálu Azure](data-lake-analytics-manage-use-portal.md)
* [Monitorování úloh Azure Data Lake Analytics a odstraňování potíží pomocí webu Azure Portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)


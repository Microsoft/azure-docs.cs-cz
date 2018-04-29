---
title: Správa Azure Data Lake Analytics pomocí rozhraní příkazového řádku Azure | Microsoft Docs
description: Naučte se spravovat účty Data Lake Analytics, zdrojů dat, úlohy a uživatele pomocí rozhraní příkazového řádku Azure
services: data-lake-analytics
documentationcenter: ''
author: SnehaGunda
manager: Kfile
ms.assetid: 4e5a3a0a-6d7f-43ed-aeb5-c3b3979a1e0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/29/2018
ms.author: sngun
ms.openlocfilehash: a78a7ea619be28f01372a7b80d3cb4a5d35bd50e
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="manage-azure-data-lake-analytics-using-azure-command-line-interface-cli"></a>Správa Azure Data Lake Analytics pomocí rozhraní příkazového řádku Azure (CLI)

[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Zjistěte, jak pro správu účtů Azure Data Lake Analytics, zdroje dat, uživatelů a úloh pomocí rozhraní příkazového řádku Azure. Pokud chcete zobrazit témata týkající se řízení pomocí jiných nástrojů, klikněte na výběr karty výše.


**Požadavky**

Než začnete tento kurz, musíte mít následující prostředky:

* Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* Azure CLI. Viz téma [Instalace a konfigurace rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

   * Pokud chcete absolvovat tuto ukázku, stáhněte a nainstalujte **předběžnou verzi** [nástrojů příkazového řádku Azure](https://github.com/MicrosoftBigData/AzureDataLake/releases).

* Ověřit pomocí `az login` příkazů a vyberte odběr, který chcete použít. Další informace týkající se ověřování pomocí pracovního nebo školního účtu najdete v tématu [Připojení k předplatnému Azure z rozhraní příkazového řádku Azure](/cli/azure/authenticate-azure-cli).

   ```azurecli
   az login
   az account set --subscription <subscription id>
   ```

   Nyní můžete zobrazit příkazy Data Lake Analytics a Data Lake Store. Spusťte následující příkaz, který zobrazí seznam příkazů Data Lake Store a Data Lake Analytics:

   ```azurecli
   az dls -h
   az dla -h
   ```

## <a name="manage-accounts"></a>Správa účtů

Před spuštěním jakékoli úlohy Data Lake Analytics, musí mít účet Data Lake Analytics. Na rozdíl od Azure HDInsight nemáte platí pro účet Analytics když úlohu není spuštěn. Platíte jenom čas, kdy je spuštěná úloha.  Další informace najdete v tématu [přehled Azure Data Lake Analytics](data-lake-analytics-overview.md).  

### <a name="create-accounts"></a>Vytvoření účtů

Pomocí následujícího příkazu vytvořte účet Data Lake 

   ```azurecli
   az dla account create --account "<Data Lake Analytics account name>" --location "<Location Name>" --resource-group "<Resource Group Name>" --default-data-lake-store "<Data Lake Store account name>"
   ```

### <a name="update-accounts"></a>Aktualizace účtů

Příkaz aktualizuje vlastnosti existující účet Data Lake Analytics

   ```azurecli
   az dla account update --account "<Data Lake Analytics Account Name>" --firewall-state "Enabled" --query-store-retention 7
   ```

### <a name="list-accounts"></a>Výpis účtů

Seznam Data Lake Analytics účtů v rámci určité skupiny zdrojů

   ```azurecli
   az dla account list "<Resource group name>"
   ```

## <a name="get-details-of-an-account"></a>Podrobnosti o účtu

   ```azurecli
   az dla account show --account "<Data Lake Analytics account name>" --resource-group "<Resource group name>"
   ```

### <a name="delete-an-account"></a>Odstranit účet

   ```azurecli
   az dla account delete --account "<Data Lake Analytics account name>" --resource-group "<Resource group name>"
   ```

## <a name="manage-data-sources"></a>Správa zdrojů dat

Data Lake Analytics teď podporuje následující zdroje dat dvě:

* [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
* [Azure Storage](../storage/common/storage-introduction.md)

Když vytvoříte účet Analytics, je třeba určit účet Azure Data Lake Storage výchozí účet úložiště. Výchozí účet Data Lake úložiště se používá k ukládání metadat a úlohy auditu v protokolech úloh. Po vytvoření účtu Analytics můžete přidat další účty Data Lake Storage a účet úložiště Azure. 

### <a name="find-the-default-data-lake-store-account"></a>Najít výchozího účtu Data Lake Store

Můžete zobrazit výchozí účet Data Lake Store používat spuštěním `az dla account show` příkaz. Výchozí název účtu je uvedena pod vlastnost defaultDataLakeStoreAccount.

   ```azurecli
   az dla account show --account "<Data Lake Analytics account name>"
   ```

### <a name="add-additional-blob-storage-accounts"></a>Přidat další účty úložiště Blob

   ```azurecli
   az dla account blob-storage add --access-key "<Azure Storage Account Key>" --account "<Data Lake Analytics account name>" --storage-account-name "<Storage account name>"
   ```

> [!NOTE]
> Jsou podporovány pouze objekt Blob úložiště krátké názvy. Nepoužívejte plně kvalifikovaný název domény, například "myblob.blob.core.windows.net".
> 

### <a name="add-additional-data-lake-store-accounts"></a>Přidat další účty Data Lake Store

Příkaz aktualizuje zadaný účet Data Lake Analytics pomocí další účtu Data Lake Store:

   ```azurecli
   az dla account data-lake-store add --account "<Data Lake Analytics account name>" --data-lake-store-account-name "<Data Lake Store account name>"
   ```

### <a name="update-existing-data-source"></a>Aktualizovat stávající zdroj dat

Aktualizace existujícího klíče účtu úložiště objektů Blob:

   ```azurecli
   az dla account blob-storage update --access-key "<New Blob Storage Account Key>" --account "<Data Lake Analytics account name>" --storage-account-name "<Data Lake Store account name>"
   ```

### <a name="list-data-sources"></a>Seznam zdrojů dat:

Seznam účtů Data Lake Store:

   ```azurecli
   az dla account data-lake-store list --account "<Data Lake Analytics account name>"
   ```

Do seznamu účtu úložiště Blob:

   ```azurecli
   az dla account blob-storage list --account "<Data Lake Analytics account name>"
   ```

![Data Lake Analytics seznamu zdroj dat](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-data-source.png)

### <a name="delete-data-sources"></a>Odstraňte zdroje dat:
Odstranění účtu Data Lake Store:

   ```azurecli
   az dla account data-lake-store delete --account "<Data Lake Analytics account name>" --data-lake-store-account-name "<Azure Data Lake Store account name>"
   ```

Chcete odstranit účet úložiště objektů Blob:

   ```azurecli
   az dla account blob-storage delete --account "<Data Lake Analytics account name>" --storage-account-name "<Data Lake Store account name>"
   ```

## <a name="manage-jobs"></a>Správa úloh
Abyste mohli vytvořit úlohu, musí mít účet Data Lake Analytics.  Další informace najdete v tématu [Správa Data Lake Analytics účty](#manage-accounts).

### <a name="list-jobs"></a>Seznam úloh

   ```azurecli
   az dla job list --account "<Data Lake Analytics account name>"
   ```

   ![Data Lake Analytics seznamu zdroj dat](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-jobs.png)

### <a name="get-job-details"></a>Získání podrobností o úlohách

   ```azurecli
   az dla job show --account "<Data Lake Analytics account name>" --job-identity "<Job Id>"
   ```

### <a name="submit-jobs"></a>Odesílání úloh

> [!NOTE]
> Výchozí prioritu úlohy je 1000 a výchozí stupně paralelního zpracování v rámci úlohy je 1.
> 
   ```azurecli
   az dla job submit --account "<Data Lake Analytics account name>" --job-name "<Name of your job>" --script "<Script to submit>"
   ```

### <a name="cancel-jobs"></a>Zrušení úlohy
Pomocí příkazu seznamu najít id úlohy a pak použijte Storno Chcete zrušit úlohu.

   ```azurecli
   az dla job cancel --account "<Data Lake Analytics account name>" --job-identity "<Job Id>"
   ```

## <a name="use-azure-resource-manager-groups"></a>Pomocí skupin Azure Resource Manager
Aplikace obvykle obsahují celou řadu součástí, například webovou aplikaci, databázi, databázový server, úložiště a služby třetích stran. Azure Resource Manager umožňuje pracovat s prostředky v aplikaci jako se skupinou, označuje jako skupiny prostředků Azure. Můžete nasadit, aktualizovat, sledovat nebo odstranit všechny prostředky pro aplikaci v rámci jediné koordinované operace. Pro nasazení použijete šablonu a tato šablona může fungovat v různých prostředích, jako například v testovacím, přípravném nebo produkčním prostředí. Můžete zpřehlednit fakturaci ve své organizaci tím, že zobrazíte souhrnné náklady za celou skupinu. Další informace najdete v tématu [Přehled Azure Resource Manageru](../azure-resource-manager/resource-group-overview.md). 

Služba Data Lake Analytics může zahrnovat následující součásti:

* Účet Azure Data Lake Analytics
* Vyžaduje výchozí účet Azure Data Lake Storage
* Účty úložiště další Azure Data Lake
* Další účty Azure Storage

Můžete vytvořit všechny tyto součásti v rámci jedné skupiny Resource Manager, aby je bylo snazší spravovat.

![Účet Azure Data Lake Analytics a úložiště](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

Účet Data Lake Analytics a účty závislého úložiště musí být umístěné ve stejném centru dat Azure.
Skupiny prostředků správce ale může být umístěno v různých datových center.  

## <a name="see-also"></a>Další informace najdete v tématech
* [Přehled služby Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Začínáme se službou Data Lake Analytics pomocí webu Azure Portal](data-lake-analytics-get-started-portal.md)
* [Správa Azure Data Lake Analytics pomocí webu Azure Portal](data-lake-analytics-manage-use-portal.md)
* [Monitorování úloh Azure Data Lake Analytics a odstraňování potíží pomocí webu Azure Portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)


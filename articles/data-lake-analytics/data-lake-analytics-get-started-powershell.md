---
title: Začínáme s Azure Data Lake Analytics s využitím Azure PowerShellu
description: Vytvořte pomocí Azure PowerShellu účet služby Azure Data Lake Analytics a odešlete úlohu U-SQL.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 8a4e901e-9656-4a60-90d0-d78ff2f00656
ms.topic: conceptual
ms.date: 05/04/2017
ms.openlocfilehash: 2d1f5cc4883ce17da76eea815b42833b84031549
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56231820"
---
# <a name="get-started-with-azure-data-lake-analytics-using-azure-powershell"></a>Začínáme s Azure Data Lake Analytics s využitím Azure PowerShellu
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Naučíte se, jak pomocí Azure PowerShellu vytvořit účty Azure Data Lake Analytics a následně odeslat a spustit úlohy U-SQL. Další informace o Data Lake Analytics najdete v tématu [Přehled Azure Data Lake Analytics](data-lake-analytics-overview.md).

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Před zahájením tohoto kurzu musíte mít následující informace:

* **Účet služby Azure Data Lake Analytics**. Zobrazit téma [Začínáme s Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal).
* **Pracovní stanice s prostředím Azure PowerShell**. Viz téma [Instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview).

## <a name="log-in-to-azure"></a>Přihlášení k Azure

Tento kurz předpokládá, že jste už s používáním Azure Powershellu obeznámení. Konkrétně musíte vědět, jak se k Azure přihlásit. Pokud potřebujete pomoc, přejděte na téma [Začínáme s Azure PowerShellem](https://docs.microsoft.com/powershell/azure/get-started-azureps).

Přihlášení pomocí názvu předplatného:

```
Connect-AzAccount -SubscriptionName "ContosoSubscription"
```

Místo názvu předplatného můžete také pro přihlášení použít ID předplatného:

```
Connect-AzAccount -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

Pokud se vám to podaří, výstup tohoto příkazu vypadá jako následující text:

```
Environment           : AzureCloud
Account               : joe@contoso.com
TenantId              : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
SubscriptionId        : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
SubscriptionName      : ContosoSubscription
CurrentStorageAccount :
```

## <a name="preparing-for-the-tutorial"></a>Příprava pro tento kurz

Fragment kódu PowerShellu v tomto kurzu používá následující proměnné k ukládání příslušných informací:

```
$rg = "<ResourceGroupName>"
$adls = "<DataLakeStoreAccountName>"
$adla = "<DataLakeAnalyticsAccountName>"
$location = "East US 2"
```

## <a name="get-information-about-a-data-lake-analytics-account"></a>Získání informací o účtu Data Lake Analytics

```
Get-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla  
```

## <a name="submit-a-u-sql-job"></a>Odeslání úlohy U-SQL

Vytvořte proměnnou Powershellu, aby uchovala skript U-SQL.

```
$script = @"
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

"@
```

Odešlete text skriptu pomocí rutiny `Submit-AdlJob` a parametru `-Script`.

```
$job = Submit-AdlJob -Account $adla -Name "My Job" �Script $script
```

Jako alternativu můžete odeslat soubor skriptu pomocí parametru `-ScriptPath`:

```
$filename = "d:\test.usql"
$script | out-File $filename
$job = Submit-AdlJob -Account $adla -Name "My Job" �ScriptPath $filename
```

Získejte stav úlohy pomocí rutiny `Get-AdlJob`. 

```
$job = Get-AdlJob -Account $adla -JobId $job.JobId
```

Místo opakovaného volání rutiny Get-AdlJob, dokud se úloha nedokončí, použijte rutinu `Wait-AdlJob`.

```
Wait-AdlJob -Account $adla -JobId $job.JobId
```

Stáhněte výstupní soubor pomocí rutiny `Export-AdlStoreItem`.

```
Export-AdlStoreItem -Account $adls -Path "/data.csv" -Destination "C:\data.csv"
```

## <a name="see-also"></a>Další informace najdete v tématech
* Pokud chcete použít jiné podporované nástroje a zobrazit stejný kurz, klikněte na selektory karet v horní části stránky.
* Pokud se chcete naučit jazyk U-SQL, informace najdete v tématu [Začínáme s jazykem U-SQL Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
* Informace týkající se úloh správy najdete v tématu [Správa služby Azure Data Lake Analytics pomocí webu Azure Portal](data-lake-analytics-manage-use-portal.md).

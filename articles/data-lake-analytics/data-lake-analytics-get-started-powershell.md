---
title: Vytvoření Azure Data Lake Analytics dotazů & – PowerShell
description: Vytvořte pomocí Azure PowerShellu účet služby Azure Data Lake Analytics a odešlete úlohu U-SQL.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 05/04/2017
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 93a05231bc971737a08d74ad04150e5449dfc792
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/20/2020
ms.locfileid: "92220937"
---
# <a name="get-started-with-azure-data-lake-analytics-using-azure-powershell"></a>Začínáme s Azure Data Lake Analytics s využitím Azure PowerShellu

[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Naučíte se, jak pomocí Azure PowerShellu vytvořit účty Azure Data Lake Analytics a následně odeslat a spustit úlohy U-SQL. Další informace o Data Lake Analytics najdete v tématu [Azure Data Lake Analytics Overview](data-lake-analytics-overview.md).

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Před zahájením tohoto kurzu musíte mít následující informace:

* **Účet služby Azure Data Lake Analytics**. Zobrazit téma [Začínáme s Data Lake Analytics](./data-lake-analytics-get-started-portal.md).
* **Pracovní stanice s prostředím Azure PowerShell**. Viz téma [Instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/).

## <a name="log-in-to-azure"></a>Přihlaste se k Azure.

Tento kurz předpokládá, že jste už s používáním Azure Powershellu obeznámení. Konkrétně musíte vědět, jak se k Azure přihlásit. Pokud potřebujete pomoc, přejděte na téma [Začínáme s Azure PowerShellem](/powershell/azure/get-started-azureps).

Přihlášení pomocí názvu předplatného:

```powershell
Connect-AzAccount -SubscriptionName "ContosoSubscription"
```

Místo názvu předplatného můžete také pro přihlášení použít ID předplatného:

```powershell
Connect-AzAccount -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

Pokud se vám to podaří, výstup tohoto příkazu vypadá jako následující text:

```text
Environment           : AzureCloud
Account               : joe@contoso.com
TenantId              : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
SubscriptionId        : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
SubscriptionName      : ContosoSubscription
CurrentStorageAccount :
```

## <a name="preparing-for-the-tutorial"></a>Příprava pro tento kurz

Fragment kódu PowerShellu v tomto kurzu používá následující proměnné k ukládání příslušných informací:

```powershell
$rg = "<ResourceGroupName>"
$adls = "<DataLakeStoreAccountName>"
$adla = "<DataLakeAnalyticsAccountName>"
$location = "East US 2"
```

## <a name="get-information-about-a-data-lake-analytics-account"></a>Získání informací o účtu Data Lake Analytics

```powershell
Get-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla  
```

## <a name="submit-a-u-sql-job"></a>Odeslání úlohy U-SQL

Vytvořte proměnnou Powershellu, aby uchovala skript U-SQL.

```powershell
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

```powershell
$job = Submit-AdlJob -Account $adla -Name "My Job" -Script $script
```

Jako alternativu můžete odeslat soubor skriptu pomocí parametru `-ScriptPath`:

```powershell
$filename = "d:\test.usql"
$script | out-File $filename
$job = Submit-AdlJob -Account $adla -Name "My Job" -ScriptPath $filename
```

Získejte stav úlohy pomocí rutiny `Get-AdlJob`. 

```powershell
$job = Get-AdlJob -Account $adla -JobId $job.JobId
```

Místo opakovaného volání rutiny Get-AdlJob, dokud se úloha nedokončí, použijte rutinu `Wait-AdlJob`.

```powershell
Wait-AdlJob -Account $adla -JobId $job.JobId
```

Stáhněte výstupní soubor pomocí rutiny `Export-AdlStoreItem`.

```powershell
Export-AdlStoreItem -Account $adls -Path "/data.csv" -Destination "C:\data.csv"
```

## <a name="see-also"></a>Viz také

* Pokud chcete použít jiné podporované nástroje a zobrazit stejný kurz, klikněte na selektory karet v horní části stránky.
* Pokud se chcete naučit jazyk U-SQL, informace najdete v tématu [Začínáme s jazykem U-SQL Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
* Informace o úlohách správy najdete v tématu [správa Azure Data Lake Analytics pomocí Azure Portal](data-lake-analytics-manage-use-portal.md).
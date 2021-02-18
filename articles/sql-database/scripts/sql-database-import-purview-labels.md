---
title: Klasifikace dat SQL Azure pomocí popisků Azure dosah
description: Import klasifikace z Azure dosah ve vašich Azure SQL Database a Azure Synpase Analytics
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: azurepowershell
ms.topic: sample
author: davidtrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 02/16/2021
ms.openlocfilehash: 71444efa606e15ac744b323aa8826828454f8149
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100643609"
---
# <a name="classify-your-azure-sql-data-using-azure-purview-labels"></a>Klasifikace dat SQL Azure pomocí popisků Azure dosah
[!INCLUDE[appliesto-sqldb-asa](../../azure-sql/includes/appliesto-sqldb-asa.md)]

Tento dokument popisuje, jak přidat dosah popisky Azure do vašich Azure SQL Database a Azure synapse Analytics (dřív SQL DW).

## <a name="create-an-application"></a>Vytvoření aplikace

1. Z Azure Portal otevřete **Azure Active Directory**.
2. V části **Spravovat** vyberte **Registrace aplikace**.
3. Novou aplikaci Azure Active Directory vytvoříte tak, že vyberete **Nová aplikace**.
4. Zadejte název vaší aplikace a vyberte **Registrovat**.
5. Po vytvoření aplikace otevřete **certifikáty & tajných** kódů v části **správce**.
6. Vytvořte nový tajný klíč klienta tak, že v části **tajné klíče klienta** vyberete **nový tajný klíč klienta** .
7. Přidejte k tajnému kódu klienta popis, vyberte období vypršení platnosti a vyberte **Přidat**.
8. Ponechte **hodnotu** pro budoucí použití.

   > [!NOTE]
   > Po zavření stránky bude hodnota maskována. Pokud se vrátíte zpět na stránku, nebudete moct načíst hodnotu tajného klíče klienta. Budete muset vygenerovat nový tajný klíč klienta.

9. Vraťte se na stránku Přehled vaší nově vytvořené aplikace a zkopírujte následující hodnoty pro budoucí použití:
    1. ID aplikace (klienta)
    1. ID adresáře (tenanta)

## <a name="provide-permissions-to-the-application"></a>Poskytnutí oprávnění k aplikaci

1. V Azure Portal vyhledejte **účty dosah**.
2. Vyberte účet dosah, ve kterém jsou klasifikované vaše databáze SQL a synapse.
3. Otevřete **řízení přístupu (IAM)**, vyberte **Přidat**.

4. Vyberte **Přidat přiřazení role**.
5. V části **role** vyhledejte **data Reader dosah** a vyberte je.
6. V části **Vybrat** vyhledejte aplikaci, kterou jste předtím vytvořili, vyberte ji a stiskněte **Uložit**.

## <a name="extract-the-classification-from-azure-purview"></a>Extrakce klasifikace z Azure dosah

1. Otevřete účet dosah a na domovské stránce vyhledejte Azure SQL Database nebo Azure synapse Analytics, kam chcete popisky zkopírovat.
2. V části **Properties (vlastnosti**) zkopírujte kvalifikovaný. a zachovejte ho pro budoucí použití.
3. Otevřete prostředí PowerShell.

4. Zkopírujte jeden z následujících skriptů podle vašeho typu assetu SQL (Azure SQL Database nebo Azure synapse).
5. Vyplňte parametry hodnotami, které jste zkopírovali výše:

   a. $TenantID: oddíl 1, krok 9
   
   b. $ClientID: oddíl 1, krok 9
   
   c. $SecretID: oddíl 1, krok 8
   
   d. $purviewAccountName: oddíl 2, krok 2
   
   e. $sqlDatabaseName: oddíl 3, krok 2

6. Zkopírujte výstup skriptu pro budoucí použití.

### <a name="for-azure-sql-database"></a>Pro Azure SQL Database

```powershell
# Replace the values below with the relevant values for your environment
$TenantID = 'your_tenant_id'
$ClientID = 'your_client_id'
$SecretID = 'your_secret_id'
$purviewAccountName='purview_account_name'
$sqlDatabaseName="mssql://sql_server_name.database.windows.net/db_name"

###############################################################################
# Get an access accessToken, and build REST Request Header.
###############################################################################
$cmdletParameters = @{
  Method  = "POST"
  URI     = "https://login.microsoftonline.com/$TenantID/oauth2/token"
  Headers = @{
    "Content-Type" = 'application/x-www-form-urlencoded'
  }
  Body    = "client_id=$ClientID&client_secret=$SecretID&resource=https://projectbabylon.azure.net&grant_type=client_credentials"
}

$invokeResult = Invoke-RestMethod @cmdletParameters;
$accessToken = $invokeResult.access_token; 

$restRequestHeader = @{
  "authorization" = "Bearer $accessToken"
};

###############################################################################
# Get database entity.
###############################################################################
$getDatabaseEntityEntryEndpoint = "https://" + $purviewAccountName + ".catalog.purview.azure.com/api/atlas/v2/entity/uniqueAttribute/type/azure_sql_db?attr:qualifiedName=" + $sqlDatabaseName;
$cmdletParameters = @{
  Method      = "Get"
  Uri         = $getDatabaseEntityEntryEndpoint
  Headers     = $restRequestHeader
  ContentType = "application/json"        
};

$invokeResult =  Invoke-RestMethod @cmdletParameters;
$referredEntities = $invokeResult.referredEntities;
if ($null -eq $referredEntities) {
  Write-Output "No referred entities found under database entity!";
  exit;
}

###############################################################################
# Iterate database referred entities, find classified columns, and build T-SQL.
###############################################################################
foreach ($referredEntity in $referredEntities.psobject.Properties.GetEnumerator()) {
  $typeName = $referredEntity.Value.typeName;
  if ($null -eq $typeName -or $typeName -ne 'azure_sql_column') {
    continue;
  }
  
  $classifications = $referredEntity.Value.classifications;
  if ($null -eq $classifications) {
    continue;
  }
  
  foreach ($classification in $classifications.GetEnumerator())
  {
    if ($classification.typeName -notmatch 'Microsoft\.Label\.(?<labelId>.+)') { 
        continue;
    }

      $labelId = $Matches.labelId -replace "_", "-";

      $attributes = $referredEntity.Value.attributes;
      if ($null -eq $attributes) {
        continue;
      }
    
      $qualifiedName = $attributes.qualifiedName;
      if ($qualifiedName -notmatch '.+\.database\.windows\.net\/.+\/(?<schemaName>.+)\/(?<tableName>.+)#(?<columnName>.+)') {
        continue;
      }

      $schemaName = $Matches.schemaName;
      $tableName = $Matches.tableName;
      $columnName = $Matches.columnName;
      
      Write-Output "ADD SENSITIVITY CLASSIFICATION TO ${schemaName}.${tableName}.${columnName} WITH (LABEL='Azure Purview Label', LABEL_ID='${labelId}');";
  }
}
```

### <a name="for-azure-synapse-analytics"></a>Pro Azure synapse Analytics

```powershell
# Replace the values below with the relevant values for your environment
$TenantID = 'your_tenant_id'
$ClientID = 'your_client_id'
$SecretID = 'your_secret_id'
$purviewAccountName='purview_account_name'
$dwDatabaseName="mssql://dw_server_name.database.windows.net/dw_name"

###############################################################################
# Get an access accessToken, and build REST Request Header.
###############################################################################
$cmdletParameters = @{
  Method  = "POST"
  URI     = "https://login.microsoftonline.com/$TenantID/oauth2/token"
  Headers = @{
    "Content-Type" = 'application/x-www-form-urlencoded'
  }
  Body    = "client_id=$ClientID&client_secret=$SecretID&resource=https://projectbabylon.azure.net&grant_type=client_credentials"
}

$invokeResult = Invoke-RestMethod @cmdletParameters;
$accessToken = $invokeResult.access_token; 

$restRequestHeader = @{
  "authorization" = "Bearer $accessToken"
};

###############################################################################
# Get database entity.
###############################################################################
$getDatabaseEntityEntryEndpoint = "https://" + $purviewAccountName + ".catalog.purview.azure.com/api/atlas/v2/entity/uniqueAttribute/type/azure_sql_dw?attr:qualifiedName=" + $dwDatabaseName;
$cmdletParameters = @{
  Method      = "Get"
  Uri         = $getDatabaseEntityEntryEndpoint
  Headers     = $restRequestHeader
  ContentType = "application/json"        
};

$invokeResult =  Invoke-RestMethod @cmdletParameters;
$referredEntities = $invokeResult.referredEntities;
if ($null -eq $referredEntities) {
  Write-Output "No referred entities found under database entity!";
  exit;
}

###############################################################################
# Iterate database referred entities, find classified columns, and build T-SQL.
###############################################################################
foreach ($referredEntity in $referredEntities.psobject.Properties.GetEnumerator()) {
  $typeName = $referredEntity.Value.typeName;
  if ($null -eq $typeName -or $typeName -ne 'azure_sql_dw_column') {
    continue;
  }
  
  $classifications = $referredEntity.Value.classifications;
  if ($null -eq $classifications) {
    continue;
  }
  
  foreach ($classification in $classifications.GetEnumerator())
  {
    if ($classification.typeName -notmatch 'Microsoft\.Label\.(?<labelId>.+)') { 
        continue;
    }

      $labelId = $Matches.labelId -replace "_", "-";

      $attributes = $referredEntity.Value.attributes;
      if ($null -eq $attributes) {
        continue;
      }
    
      $qualifiedName = $attributes.qualifiedName;
      if ($qualifiedName -notmatch '.+\.database\.windows\.net\/.+\/(?<schemaName>.+)\/(?<tableName>.+)#(?<columnName>.+)') {
        continue;
      }

      $schemaName = $Matches.schemaName;
      $tableName = $Matches.tableName;
      $columnName = $Matches.columnName;
      
      Write-Output "ADD SENSITIVITY CLASSIFICATION TO ${schemaName}.${tableName}.${columnName} WITH (LABEL='Azure Purview Label', LABEL_ID='${labelId}');";
  }
}
```

## <a name="run-the-t-sql-command-on-your-sql-asset"></a>Spuštění příkazu T-SQL v prostředku SQL

1. Připojte se k vašemu Azure SQL Database nebo synapse Azure pomocí vašeho nástroje podle vlastního výběru.
2. Spusťte příkaz T-SQL, který jste zkopírovali z předchozí části.

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](https://docs.microsoft.com/powershell/).

Další informace o Azure dosah najdete v [dokumentaci ke službě Azure dosah](https://docs.microsoft.com/azure/purview/).

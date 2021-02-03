---
title: Pomocí Azure PowerShell můžete nakonfigurovat průběžné zálohování a obnovení bodu v čase v Azure Cosmos DB.
description: Naučte se zřídit účet s průběžným zálohováním a obnovením dat pomocí Azure PowerShell.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 00d9e2c3b77ce4ddae105d8115cd62b5f7796e30
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527402"
---
# <a name="configure-and-manage-continuous-backup-and-point-in-time-restore---using-azure-powershell"></a>Konfigurace a Správa průběžného zálohování a obnovení k určitému bodu v čase – pomocí Azure PowerShell

Funkce obnovení k určitému bodu v čase umožňuje obnovení z náhodné změny v rámci kontejneru, obnovení odstraněného účtu, databáze nebo kontejneru nebo obnovení do libovolné oblasti (kde zálohy existovaly). Azure Cosmos DB Režim průběžné zálohování umožňuje obnovení do libovolného časového okamžiku za posledních 30 dní.

Tento článek popisuje, jak zřídit účet s průběžným zálohováním a obnovením dat pomocí Azure PowerShell.

## <a name="install-azure-powershell"></a><a id="install-powershell"></a>Instalace prostředí Azure PowerShell

1. Spuštěním následujícího příkazu z Azure PowerShell nainstalujte `Az.CosmosDB` modul Preview, který obsahuje příkazy týkající se obnovení bodu v čase:

   ```azurepowershell
   Install-Module -Name Az.CosmosDB -AllowPrerelease
   ```

1. Po instalaci modulů se přihlaste k Azure pomocí

   ```azurepowershell
   Connect-AzAccount
   ```

1. Vyberte konkrétní předplatné s následujícím příkazem:

   ```azurepowershell
   Select-AzSubscription -Subscription <SubscriptionName>
   ```

## <a name="provision-a-sql-api-account-with-continuous-backup"></a><a id="provision-sql-api"></a>Zřízení účtu rozhraní SQL API pomocí průběžného zálohování

Pokud chcete zřídit účet s průběžným zálohováním, přidejte do `-BackupPolicyType Continuous` něj argument spolu s běžným příkazem zřizování.

Následující rutina představuje příklad účtu pro zápis do jedné oblasti `pitracct2` se zásadami průběžné zálohování vytvořenými v oblasti "západní USA" v části Skupina prostředků "myrg":

```azurepowershell

New-AzCosmosDBAccount `
  -ResourceGroupName "myrg" `
  -Location "West US" `
  -BackupPolicyType Continuous `
  -Name "pitracct2" `
  -ApiKind "Sql"
      
```

## <a name="provision-a-mongodb-api-account-with-continuous-backup"></a><a id="provision-mongodb-api"></a>Zřízení účtu MongoDB API pomocí průběžného zálohování

Následující rutina je příkladem účtu průběžné zálohy "pitracct2" vytvořeného v oblasti "Západní USA" v části "myrg" skupiny prostředků:

```azurepowershell

New-AzCosmosDBAccount `
  -ResourceGroupName "myrg" `
  -Location "West US" `
  -BackupPolicyType Continuous `
  -Name "pitracct3" `
  -ApiKind "MongoDB" `
  -ServerVersion "3.6"

```

## <a name="trigger-a-restore-operation"></a><a id="trigger-restore"></a>Aktivovat operaci obnovení

Následující rutina je příkladem aktivace operace obnovení pomocí příkazu RESTORE pomocí cílového účtu, zdrojového účtu, umístění, skupiny prostředků a časového razítka:

```azurepowershell

Restore-AzCosmosDBAccount `
  -TargetResourceGroupName <resourceGroupName> `
  -TargetDatabaseAccountName <restored-account-name> `
  -SourceDatabaseAccountName <sourceDatabaseAccountName> `
  -RestoreTimestampInUtc <UTC time> `
  -Location <Azure Region Name>

```

**Příklad 1:** Obnovování celého účtu:

```azurepowershell

Restore-AzCosmosDBAccount `
  -TargetResourceGroupName "rg" `
  -TargetDatabaseAccountName "pitrbb-ps-1" `
  -SourceDatabaseAccountName "source-sql" `
  -RestoreTimestampInUtc "2021-01-05T22:06:00" `
  -Location "West US"

```

**Příklad 2:** Obnovují se konkrétní kolekce a databáze. Tento příklad obnoví kolekce myCol1, myCol2 z myDB1 a celé databáze myDB2, která zahrnuje všechny kontejnery pod ní.

```azurepowershell
$datatabaseToRestore1 = New-AzCosmosDBDatabaseToRestore -DatabaseName "myDB1" -CollectionName "myCol1", "myCol2"
$datatabaseToRestore2 = New-AzCosmosDBDatabaseToRestore -DatabaseName "myDB2"

Restore-AzCosmosDBAccount `
  -TargetResourceGroupName "rg" `
  -TargetDatabaseAccountName "pitrbb-ps-1" `
  -SourceDatabaseAccountName "source-sql" `
  -RestoreTimestampInUtc "2021-01-05T22:06:00" `
  -DatabasesToRestore $datatabaseToRestore1, $datatabaseToRestore2 `
  -Location "West US"

```

## <a name="enumerate-restorable-resources-for-sql-api"></a><a id="enumerate-sql-api"></a>Zobrazení výčtu prostředků obnovitelné pro SQL API

Rutiny výčtu vám pomohou zjistit prostředky, které jsou k dispozici pro obnovení v různých časových razítkech. Kromě toho poskytují také informační kanály pro prostředky účtu obnovitelné, databáze a kontejnerů.

**Vypíše všechny účty, které se dají obnovit v aktuálním předplatném.**

Spuštěním `Get-AzCosmosDBRestorableDatabaseAccount` příkazu PowerShellu zobrazíte seznam všech účtů, které se dají obnovit v aktuálním předplatném.

Odpověď zahrnuje všechny účty databáze (živé i odstraněné), které je možné obnovit, a oblasti, ze kterých se dají obnovit.

```console
{
    "accountName": "sampleaccount",
    "apiType": "Sql",
    "creationTime": "2020-08-08T01:04:52.070190+00:00",
    "deletionTime": null,
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/23e99a35-cd36-4df4-9614-f767a03b9995",
    "identity": null,
    "location": "West US",
    "name": "23e99a35-cd36-4df4-9614-f767a03b9995",
    "restorableLocations": [
      {
        "creationTime": "2020-08-08T01:04:52.945185+00:00",
        "deletionTime": null,
        "locationName": "West US",
        "regionalDatabaseAccountInstanceId": "30701557-ecf8-43ce-8810-2c8be01dccf9"
      },
      {
        "creationTime": "2020-08-08T01:15:43.507795+00:00",
        "deletionTime": null,
        "locationName": "East US",
        "regionalDatabaseAccountInstanceId": "8283b088-b67d-4975-bfbe-0705e3e7a599"
      }
    ],
    "tags": null,
    "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts"
  },
```

Stejně jako "CreationTime" nebo "DeletionTime" pro tento účet je také pro tuto oblast k dispozici "CreationTime" nebo "DeletionTime". Tyto časy umožňují zvolit správnou oblast a platný časový rozsah pro obnovení do této oblasti.

**Vypíše všechny verze databází SQL v živém databázovém účtu.**

Seznam všech verzí databází vám umožní zvolit správnou databázi ve scénáři, kdy je neznámou skutečnou dobu existence databáze.

Chcete-li zobrazit seznam všech verzí databází, spusťte následující příkaz prostředí PowerShell. Tento příkaz funguje jenom s živými účty. Parametry "DatabaseAccountInstanceId" a "Location" jsou získány z vlastností "název" a "umístění" v odpovědi `Get-AzCosmosDBRestorableDatabaseAccount` rutiny. Atribut "DatabaseAccountInstanceId" odkazuje na vlastnost instanceId obnoveného účtu zdrojové databáze:


```azurepowershell

Get-AzCosmosdbSqlRestorableDatabase `
  -LocationName "East US" `
  -DatabaseAccountInstanceId <DatabaseAccountInstanceId>

```

**Vypíše všechny verze kontejnerů SQL pro databázi v živém databázovém účtu.**

K vypsání všech verzí kontejnerů SQL použijte následující příkaz. Tento příkaz funguje jenom s živými účty. Parametr "DatabaseRid" je "ResourceId" databáze, kterou chcete obnovit. Jedná se o hodnotu atributu "ownerResourceid" nalezenou v odpovědi `Get-AzCosmosdbSqlRestorableDatabase` rutiny. Odpověď obsahuje taky seznam operací provedených na všech kontejnerech uvnitř této databáze.

```azurepowershell

Get-AzCosmosdbSqlRestorableContainer `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -DatabaseRid "AoQ13r==" `
  -LocationName "West US"

```

**Vyhledá databáze nebo kontejnery, které se dají obnovit v libovolném daném časovém razítku.**

Pomocí následujícího příkazu můžete získat seznam databází nebo kontejnerů, které se dají obnovit v kterémkoli daném časovém razítku. Tento příkaz funguje jenom s živými účty.

```azurepowershell

Get-AzCosmosdbSqlRestorableResource `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -LocationName "West US" `
  -RestoreLocation "East US" `
  -RestoreTimestamp "2020-07-20T16:09:53+0000"

```

## <a name="enumerate-restorable-resources-for-mongodb"></a><a id="enumerate-mongodb-api"></a>Zobrazení výčtu prostředků obnovitelné pro MongoDB

Příkazy výčtu popsané níže vám pomohou zjistit prostředky, které jsou k dispozici pro obnovení v různých časových razítkech. Kromě toho poskytují také informační kanály pro prostředky účtu obnovitelné, databáze a kontejnerů. Tyto příkazy fungují pouze u živých účtů a jsou podobné příkazům rozhraní SQL API, ale v názvu příkazu "MongoDB" místo "SQL".

**Vypíše všechny verze MongoDB databází v živém databázovém účtu.**

```azurepowershell

Get-AzCosmosdbMongoDBRestorableDatabase `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -LocationName "West US"

```

**Vypíše všechny verze MongoDB kolekcí databáze v živém databázovém účtu.**

```azurepowershell

Get-AzCosmosdbMongoDBRestorableCollection `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -DatabaseRid "AoQ13r==" `
  -LocationName "West US"
```

**Zobrazí seznam všech prostředků databázového účtu MongoDB, které jsou k dispozici pro obnovení v daném časovém razítku a oblasti.**

```azurepowershell

Get-AzCosmosdbMongoDBRestorableResource `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -LocationName "West US" `
  -RestoreLocation "West US" `
  -RestoreTimestamp "2020-07-20T16:09:53+0000"
```

## <a name="next-steps"></a>Další kroky

* Nakonfigurujte a spravujte průběžné zálohování pomocí [Azure CLI](continuous-backup-restore-command-line.md), [Správce prostředků](continuous-backup-restore-template.md)nebo [Azure Portal](continuous-backup-restore-portal.md).
* [Model prostředků režimu průběžné zálohování](continuous-backup-restore-resource-model.md)
* [Správa oprávnění](continuous-backup-restore-permissions.md) potřebných pro obnovení dat pomocí režimu průběžné zálohování.

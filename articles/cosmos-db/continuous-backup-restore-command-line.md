---
title: Pomocí Azure CLI nakonfigurujte průběžné zálohování a obnovení v časovém bodě v Azure Cosmos DB.
description: Naučte se zřídit účet s průběžným zálohováním a obnovením dat pomocí Azure CLI.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 2e09542cbe56df7c8d6984a98fe77142f543ec03
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539185"
---
# <a name="configure-and-manage-continuous-backup-and-point-in-time-restore-preview---using-azure-cli"></a>Konfigurace a Správa průběžného zálohování a obnovení k určitému bodu v čase (Preview) – použití rozhraní příkazového řádku Azure
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> Funkce obnovení bodu v čase (režim průběžného zálohování) pro Azure Cosmos DB je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Funkce obnovení k určitému bodu v čase služby Azure Cosmos DB (Preview) vám pomůže s obnovením z náhodné změny v rámci kontejneru, k obnovení odstraněného účtu, databáze nebo kontejneru nebo k obnovení do libovolné oblasti (kde zálohy existovaly). Režim průběžné zálohování umožňuje obnovení do libovolného časového okamžiku za posledních 30 dní.

Tento článek popisuje, jak zřídit účet s průběžným zálohováním a obnovením dat pomocí Azure CLI.

## <a name="install-azure-cli"></a><a id="install"></a>Instalace rozhraní příkazového řádku Azure

1. Nainstalovat nejnovější verzi rozhraní příkazového řádku Azure

   * Nainstalujte nejnovější verzi rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli) nebo novější než 2.17.1.
   * Pokud jste již nainstalovali rozhraní příkazového řádku, spusťte `az upgrade` příkaz k aktualizaci na nejnovější verzi. Tento příkaz bude fungovat jenom s verzí CLI vyšší než 2,11. Pokud máte starší verzi, nainstalujte nejnovější verzi pomocí odkazu výše.

1. Nainstalujte `cosmosdb-preview` rozšíření CLI.

   * Příkazy obnovení k bodu v čase jsou k dispozici v části `cosmosdb-preview` rozšíření.
   * Toto rozšíření můžete nainstalovat spuštěním následujícího příkazu: `az extension add --name cosmosdb-preview`
   * Toto rozšíření můžete odinstalovat spuštěním následujícího příkazu: `az extension remove --name cosmosdb-preview`

1. Přihlaste se a vyberte své předplatné.

   * Přihlaste se k účtu Azure pomocí `az login` příkazu.
   * Vyberte požadované předplatné pomocí `az account set -s <subscriptionguid>` příkazu.

## <a name="provision-a-sql-api-account-with-continuous-backup"></a><a id="provision-sql-api"></a>Zřízení účtu rozhraní SQL API pomocí průběžného zálohování

Pokud chcete zřídit účet rozhraní SQL API se průběžným zálohováním, je `--backup-policy-type Continuous` potřeba předat další argument spolu s běžným příkazem zřizování. Následující příkaz je příkladem jedné oblasti účet pro zápis s názvem `pitracct2` se zásadami průběžné zálohování vytvořenými v oblasti "západní USA" v části "myrg" skupiny prostředků:

```azurecli-interactive

az cosmosdb create \
  --name pitracct2 \
  --resource-group myrg \
  --backup-policy-type Continuous \
  --default-consistency-level Session \
  --locations regionName="West US"

```

## <a name="provision-an-azure-cosmos-db-api-for-mongodb-account-with-continuous-backup"></a><a id="provision-mongo-api"></a>Zřízení Azure Cosmos DB API pro účet MongoDB s průběžným zálohováním

Následující příkaz ukazuje příklad účtu pro zápis s jednou oblastí s názvem `pitracct3` with Continuous Backup, který vytvořil region "západní USA" v rámci skupiny prostředků "myrg":

```azurecli-interactive

az cosmosdb create \
  --name pitracct3 \
  --kind MongoDB \
  --resource-group myrg \
  --server-version "3.6" \
  --backup-policy-type Continuous \
  --default-consistency-level Session \
  --locations regionName="West US"

```

## <a name="trigger-a-restore-operation-with-cli"></a><a id="trigger-restore"></a>Aktivace operace obnovení pomocí rozhraní příkazového řádku

Nejjednodušší způsob, jak spustit obnovení, je vydání příkazu RESTORE s názvem cílového účtu, zdrojového účtu, umístění, skupiny prostředků, časového razítka (ve standardu UTC) a volitelně názvu databáze a kontejneru. Následuje několik příkladů, jak spustit operaci obnovení:

1. Vytvořte nový účet Azure Cosmos DB obnovením z existujícího účtu.

   ```azurecli-interactive

   az cosmosdb restore \
    --target-database-account-name MyRestoredCosmosDBDatabaseAccount \
    --account-name MySourceAccount \
    --restore-timestamp 2020-07-13T16:03:41+0000 \
    --resource-group MyResourceGroup \
    --location "West US"

   ```

2. Vytvořte nový účet Azure Cosmos DB tím, že obnovíte jenom vybrané databáze a kontejnery z existujícího databázového účtu.

   ```azurecli-interactive

   az cosmosdb restore \
    --resource-group MyResourceGroup \
    --target-database-account-name MyRestoredCosmosDBDatabaseAccount \
    --account-name MySourceAccount \
    --restore-timestamp 2020-07-13T16:03:41+0000 \
    --location "West US" \
    --databases-to-restore name=MyDB1 collections=collection1 collection2 \
    --databases-to-restore name=MyDB2 collections=collection3 collection4

   ```

## <a name="enumerate-restorable-resources-for-sql-api"></a><a id="enumerate-sql-api"></a>Zobrazení výčtu prostředků obnovitelné pro SQL API

Příkazy výčtu popsané níže vám pomohou zjistit prostředky, které jsou k dispozici pro obnovení v různých časových razítkech. Kromě toho poskytují také informační kanály pro prostředky účtu obnovitelné, databáze a kontejnerů.

**Vypíše všechny účty, které se dají obnovit v aktuálním předplatném.**

Spuštěním následujícího příkazu rozhraní příkazového řádku zobrazíte seznam všech účtů, které se dají obnovit v aktuálním předplatném.

```azurecli-interactive
az cosmosdb restorable-database-account list --account-name "pitrbb"
```

Odpověď zahrnuje všechny účty databáze (živé i odstraněné), které je možné obnovit, a oblasti, ze kterých se dají obnovit:

```json
{
    "accountName": "pitrbb",
    "apiType": "Sql",
    "creationTime": "2021-01-08T23:34:11.095870+00:00",
    "deletionTime": null,
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/7133a59a-d1c0-4645-a699-6e296d6ac865",
    "identity": null,
    "location": "West US",
    "name": "7133a59a-d1c0-4645-a699-6e296d6ac865",
    "restorableLocations": [
      {
        "creationTime": "2021-01-08T23:34:11.095870+00:00",
        "deletionTime": null,
        "locationName": "West US",
        "regionalDatabaseAccountInstanceId": "f02df26b-c0ec-4829-8bef-3482d36e6230"
      }
    ],
    "tags": null,
    "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts"
  }
```

Stejně jako "CreationTime" nebo "DeletionTime" pro tento účet je také pro tuto oblast k dispozici "CreationTime" nebo "DeletionTime". Tyto časy umožňují zvolit správnou oblast a platný časový rozsah pro obnovení do této oblasti.

**Vypíše všechny verze databází v živém databázovém účtu.**

Seznam všech verzí databází vám umožní zvolit správnou databázi ve scénáři, kdy je neznámou skutečnou dobu existence databáze.

Spuštěním následujícího příkazu rozhraní příkazového řádku zobrazíte seznam všech verzí databází. Tento příkaz funguje jenom s živými účty. Parametry instanceId a Location jsou získány z vlastností "Name" a "Location" v odpovědi `az cosmosdb restorable-database-account list` příkazu. Atribut instanceId je zároveň vlastností obnoveného účtu zdrojové databáze:

```azurecli-interactive
az cosmosdb sql restorable-database list \
  --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
  --location "West US"
```

Tento výstup příkazu se teď zobrazuje, když se databáze vytvořila a odstranila.

```json
[
  {
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/7133a59a-d1c0-4645-a699-6e296d6ac865/restorableSqlDatabases/40e93dbd-2abe-4356-a31a-35567b777220",
    ..
    "name": "40e93dbd-2abe-4356-a31a-35567b777220",
    "resource": {
      "database": {
        "id": "db1"
      },
      "eventTimestamp": "2021-01-08T23:27:25Z",
      "operationType": "Create",
      "ownerId": "db1",
      "ownerResourceId": "YuZAAA=="
    },
    ..
  },
  {
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/7133a59a-d1c0-4645-a699-6e296d6ac865/restorableSqlDatabases/243c38cb-5c41-4931-8cfb-5948881a40ea",
    ..
    "name": "243c38cb-5c41-4931-8cfb-5948881a40ea",
    "resource": {
      "database": {
        "id": "spdb1"
      },
      "eventTimestamp": "2021-01-08T23:25:25Z",
      "operationType": "Create",
      "ownerId": "spdb1",
      "ownerResourceId": "OIQ1AA=="
    },
   ..
  }
]
```

**Vypíše všechny verze kontejnerů SQL databáze v živém databázovém účtu.**

K vypsání všech verzí kontejnerů SQL použijte následující příkaz. Tento příkaz funguje jenom s živými účty. Parametr "databaseRid" je "ResourceId" databáze, kterou chcete obnovit. Jedná se o hodnotu atributu "ownerResourceid" nalezenou v odpovědi `az cosmosdb sql restorable-database list` příkazu.

```azurecli-interactive
az cosmosdb sql restorable-container list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --database-rid "OIQ1AA==" \
    --location "West US"
```

Výstup příkazu obsahuje seznam operací provedených na všech kontejnerech uvnitř této databáze:

```json
[
  {
    ...
    
      "eventTimestamp": "2021-01-08T23:25:29Z",
      "operationType": "Replace",
      "ownerId": "procol3",
      "ownerResourceId": "OIQ1APZ7U18="
...
  },
  {
    ...
      "eventTimestamp": "2021-01-08T23:25:26Z",
      "operationType": "Create",
      "ownerId": "procol3",
      "ownerResourceId": "OIQ1APZ7U18="
  },
]
```

**Vyhledá databáze nebo kontejnery, které se dají obnovit v libovolném daném časovém razítku.**

Pomocí následujícího příkazu můžete získat seznam databází nebo kontejnerů, které se dají obnovit v kterémkoli daném časovém razítku. Tento příkaz funguje jenom s živými účty.

```azurecli-interactive

az cosmosdb sql restorable-resource list \
  --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
  --location "West US" \
  --restore-location "West US" \  
  --restore-timestamp "2021-01-10 T01:00:00+0000"

```

```json
[
  {
    "collectionNames": [
      "procol1",
      "procol2"
    ],
    "databaseName": "db1"
  },
  {
    "collectionNames": [
      "procol3",
       "spcol1"
    ],
    "databaseName": "spdb1"
  }
]
```

## <a name="enumerate-restorable-resources-for-mongodb-api-account"></a><a id="enumerate-mongodb-api"></a>Zobrazení výčtu prostředků obnovitelné pro účet rozhraní MongoDB API

Příkazy výčtu popsané níže vám pomohou zjistit prostředky, které jsou k dispozici pro obnovení v různých časových razítkech. Kromě toho poskytují také informační kanály pro prostředky účtu obnovitelné, databáze a kontejnerů. Podobně jako u SQL API můžete použít příkaz, `az cosmosdb` ale s parametrem "MongoDB" namísto "SQL". Tyto příkazy fungují pouze pro živé účty.

**Vypíše všechny verze MongoDB databází v živém databázovém účtu.**

```azurecli-interactive
az cosmosdb mongodb restorable-database list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --location "West US"
```

**Vypíše všechny verze MongoDB kolekcí databáze v živém databázovém účtu.**

```azurecli-interactive
az cosmosdb mongodb restorable-collection list \
    --instance-id "<InstanceID>" \
    --database-rid "AoQ13r==" \
    --location "West US"
```

**Zobrazí seznam všech prostředků databázového účtu MongoDB, které jsou k dispozici pro obnovení v daném časovém razítku a oblasti.**

```azurecli-interactive
az cosmosdb mongodb restorable-resource list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --location "West US" \
    --restore-location "West US" \
    --restore-timestamp "2020-07-20T16:09:53+0000"
```

## <a name="next-steps"></a>Další kroky

* Konfigurace a Správa průběžného zálohování pomocí [Azure Portal](continuous-backup-restore-portal.md), [powershellu](continuous-backup-restore-powershell.md)nebo [Azure Resource Manager](continuous-backup-restore-template.md).
* [Model prostředků režimu průběžné zálohování](continuous-backup-restore-resource-model.md)
* [Správa oprávnění](continuous-backup-restore-permissions.md) potřebných pro obnovení dat pomocí režimu průběžné zálohování.

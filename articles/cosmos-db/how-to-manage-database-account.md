---
title: Informace o správě účtů databáze ve službě Azure Cosmos DB
description: Zjistěte, jak spravovat prostředky Azure Cosmos DB pomocí azure portalu, PowerShellu, CLI a Azure Resource Manageru.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 61670d757611bd0c1dd11c389282b18edb3d7fa1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247381"
---
# <a name="manage-an-azure-cosmos-account"></a>Správa účtu služby Azure Cosmos DB

Tento článek popisuje, jak spravovat různé úlohy účtu služby Azure Cosmos DB pomocí webu Azure Portal, Azure PowerShellu, Azure CLI a šablon Azure Resource Manageru.

## <a name="create-an-account"></a>Vytvoření účtu

### <a name="azure-portal"></a><a id="create-database-account-via-portal"></a>Portál Azure

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

### <a name="azure-cli"></a><a id="create-database-account-via-cli"></a>Azure CLI

Přečtěte si článek [Vytvoření účtu Azure Cosmos DB pomocí azure CLI](manage-with-cli.md#create-an-azure-cosmos-db-account)

### <a name="azure-powershell"></a><a id="create-database-account-via-ps"></a>Azure PowerShell

Přečtěte si článek [Vytvoření účtu Azure Cosmos DB s Powershellem.](manage-with-powershell.md#create-account)

### <a name="azure-resource-manager-template"></a><a id="create-database-account-via-arm-template"></a>Šablona Azure Resource Manageru

Tato šablona Azure Resource Manager vytvoří účet Azure Cosmos pro ROZHRANÍ SQL API nakonfigurovaný se dvěma oblastmi a možnostmi pro výběr úrovně konzistence, automatického převzetí služeb při selhání a více hlavních. Chcete-li tuto šablonu nasadit, klikněte na tlačítko Nasazení do Azure na stránce [Readme, Vytvořit účet Azure Cosmos.](https://github.com/Azure/azure-quickstart-templates/tree/master/101-cosmosdb-sql)

## <a name="addremove-regions-from-your-database-account"></a>Přidání oblastí do účtu databáze nebo jejich odebrání

### <a name="azure-portal"></a><a id="add-remove-regions-via-portal"></a>Portál Azure

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

1. Přejděte na svůj účet Azure Cosmos a otevřete **globální nabídku Replikovat data.**

1. Chcete-li přidat oblasti, vyberte šestiúhelníky na mapě s popiskem, **+** který odpovídá požadované oblasti(s). Chcete-li přidat oblast, vyberte možnost **+ Přidat oblast** a z rozevírací nabídky zvolte oblast.

1. Chcete-li odebrat oblasti, vymažte jednu nebo více oblastí z mapy výběrem modrých šestiúhelníků se značkami zaškrtnutí. Nebo vyberte ikonu "odpadkový koš" (🗑) vedle oblasti na pravé straně.

1. Chcete-li změny uložit, vyberte **možnost OK**.

   ![Nabídka Přidat nebo odebrat oblasti](./media/how-to-manage-database-account/add-region.png)

V režimu zápisu do jedné oblasti není možné odebrat oblast zápisu. Před odstraněním aktuální oblasti zápisu je potřeba provést převzetí služeb při selhání do jiné oblasti.

V režimu zápisu do více oblastí můžete přidat nebo odebrat libovolnou oblast, pokud máte alespoň jednu oblast.

### <a name="azure-cli"></a><a id="add-remove-regions-via-cli"></a>Azure CLI

Přečtěte si informace [o přidání nebo odebrání oblastí pomocí příkazového příkazu k příkazu Azure.](manage-with-cli.md#add-or-remove-regions)

### <a name="azure-powershell"></a><a id="add-remove-regions-via-ps"></a>Azure PowerShell

Přečtěte si informace [o přidání nebo odebrání oblastí pomocí prostředí Powershell.](manage-with-powershell.md#update-account)

## <a name="configure-multiple-write-regions"></a><a id="configure-multiple-write-regions"></a>Konfigurace několika oblastí zápisu

### <a name="azure-portal"></a><a id="configure-multiple-write-regions-portal"></a>Portál Azure

Otevřete kartu **Replikovat data globálně** a vyberte **Povolit,** chcete-li povolit zápisy ve více oblastech. Po povolení zápisů ve více oblastech se všechny oblasti čtení, které aktuálně máte na účtu, stanou oblastmi pro čtení a zápis.

![Účet Azure Cosmos konfiguruje snímek obrazovky s více servery](./media/how-to-manage-database-account/single-to-multi-master.png)

### <a name="azure-cli"></a><a id="configure-multiple-write-regions-cli"></a>Azure CLI

Přečtěte si, [že pomocí azure CLI najdete v tématu Povolení k zápisu s více násobnými zápisy.](manage-with-cli.md#enable-multiple-write-regions)

### <a name="azure-powershell"></a><a id="configure-multiple-write-regions-ps"></a>Azure PowerShell

Viz [Povolení oblastí více zápisů pomocí prostředí Powershell](manage-with-powershell.md#multi-master)

### <a name="resource-manager-template"></a><a id="configure-multiple-write-regions-arm"></a>Šablona Správce prostředků

Účet lze migrovat z jednoho hlavního serveru na více hlavních zdrojů nasazením `enableMultipleWriteLocations: true`šablony Správce prostředků použité k vytvoření účtu a nastavení . Následující šablona Azure Resource Manager je holé minimální šablona, která nasadí účet Azure Cosmos pro SQL API se dvěma oblastmi a více povolených úložišť zápisu.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "String"
        },
        "location": {
            "type": "String",
            "defaultValue": "[resourceGroup().location]"
        },
        "primaryRegion":{
            "type":"string",
            "metadata": {
                "description": "The primary replica region for the Cosmos DB account."
            }
        },
        "secondaryRegion":{
            "type":"string",
            "metadata": {
              "description": "The secondary replica region for the Cosmos DB account."
          }
        }
    },
    "resources": [
        {
            "type": "Microsoft.DocumentDb/databaseAccounts",
            "kind": "GlobalDocumentDB",
            "name": "[parameters('name')]",
            "apiVersion": "2019-08-01",
            "location": "[parameters('location')]",
            "tags": {},
            "properties": {
                "databaseAccountOfferType": "Standard",
                "consistencyPolicy": { "defaultConsistencyLevel": "Session" },
                "locations":
                [
                    {
                        "locationName": "[parameters('primaryRegion')]",
                        "failoverPriority": 0,
                        "isZoneRedundant": false
                    },
                    {
                        "locationName": "[parameters('secondaryRegion')]",
                        "failoverPriority": 1,
                        "isZoneRedundant": false
                    }
                ],
                "enableMultipleWriteLocations": true
            }
        }
    ]
}
```

## <a name="enable-automatic-failover-for-your-azure-cosmos-account"></a><a id="automatic-failover"></a>Povolení automatického převzetí služeb při selhání u účtu služby Azure Cosmos DB

Možnost automaticképřevzetí služeb při selhání umožňuje Azure Cosmos DB převzetí služeb při selhání do oblasti s nejvyšší prioritou převzetí služeb při selhání bez akce uživatele by oblast nebude k dispozici. Pokud je povoleno automatické převzetí služeb při selhání, lze změnit prioritu oblasti. Účet musí mít dvě nebo více oblastí, aby bylo možné automaticky přepojit na znovu.

### <a name="azure-portal"></a><a id="enable-automatic-failover-via-portal"></a>Portál Azure

1. Z vašeho účtu Azure Cosmos otevřete globální podokno **Replikovat data.**

2. V horní části podokna vyberte **Možnost Automatické převzetí služeb při selhání**.

   ![Nabídka Globální replikace dat](./media/how-to-manage-database-account/replicate-data-globally.png)

3. V podokně **Automatické převzetí služeb při selhání** zkontrolujte, zda je možnost **Povolit automatické převzetí služeb při selhání** nastavena na **zapnuto**. 

4. Vyberte **Uložit**.

   ![Nabídka portálu Automatické převzetí služeb při selhání](./media/how-to-manage-database-account/automatic-failover.png)

### <a name="azure-cli"></a><a id="enable-automatic-failover-via-cli"></a>Azure CLI

Přečtěte si informace [o povolení automatického převzetí služeb při selhání pomocí příkazového příkazu k řešení Azure.](manage-with-cli.md#enable-automatic-failover)

### <a name="azure-powershell"></a><a id="enable-automatic-failover-via-ps"></a>Azure PowerShell

Viz [Povolení automatického převzetí služeb při selhání pomocí prostředí Powershell](manage-with-powershell.md#enable-automatic-failover)

## <a name="set-failover-priorities-for-your-azure-cosmos-account"></a>Nastavení priorit převzetí služeb při selhání u účtu služby Azure Cosmos DB

Po konfiguraci účtu Cosmos pro automatické převzetí služeb při selhání, prioritu převzetí služeb při selhání pro oblasti lze změnit.

> [!IMPORTANT]
> Oblast zápisu (priorita převzetí služeb při selhání nula) nelze změnit, pokud je účet nakonfigurován pro automatické převzetí služeb při selhání. Chcete-li změnit oblast zápisu, je nutné zakázat automatické převzetí služeb při selhání a provést ruční převzetí služeb při selhání.

### <a name="azure-portal"></a><a id="set-failover-priorities-via-portal"></a>Portál Azure

1. Z vašeho účtu Azure Cosmos otevřete globální podokno **Replikovat data.**

2. V horní části podokna vyberte **Možnost Automatické převzetí služeb při selhání**.

   ![Nabídka Globální replikace dat](./media/how-to-manage-database-account/replicate-data-globally.png)

3. V podokně **Automatické převzetí služeb při selhání** zkontrolujte, zda je možnost **Povolit automatické převzetí služeb při selhání** nastavena na **zapnuto**.

4. Chcete-li upravit prioritu převzetí služeb při selhání, přetáhněte oblasti čtení přes tři tečky na levé straně řádku, které se zobrazí, když na ně najedete myší.

5. Vyberte **Uložit**.

   ![Nabídka portálu Automatické převzetí služeb při selhání](./media/how-to-manage-database-account/automatic-failover.png)

### <a name="azure-cli"></a><a id="set-failover-priorities-via-cli"></a>Azure CLI

Přečtěte [si, že najdete v tématu Nastavení priority převzetí služeb při selhání pomocí příkazového příkazu k řešení Azure.](manage-with-cli.md#set-failover-priority)

### <a name="azure-powershell"></a><a id="set-failover-priorities-via-ps"></a>Azure PowerShell

Přečtěte si informace [o nastavení priority převzetí služeb při selhání pomocí prostředí Powershell.](manage-with-powershell.md#modify-failover-priority)

## <a name="perform-manual-failover-on-an-azure-cosmos-account"></a><a id="manual-failover"></a>Provedení ručního převzetí služeb při selhání účtu služby Azure Cosmos DB

> [!IMPORTANT]
> Účet Azure Cosmos musí být nakonfigurovaný pro ruční převzetí služeb při selhání, aby tato operace byla úspěšná.

Proces provedení ručnípřevzetí služeb při selhání zahrnuje změnu oblasti zápisu účtu (priorita převzetí služeb při selhání = 0) na jinou oblast nakonfigurovanou pro účet.

> [!NOTE]
> Vícehlavních účtů nelze ručně převzetí mísy při selhání. Pro aplikace pomocí sady Azure Cosmos SDK sada SDK zjistí, kdy oblast přestane být k dispozici, pak přesměrovat automaticky do nejbližší oblasti, pokud používáte více naváděcí rozhraní API v sdk.

### <a name="azure-portal"></a><a id="enable-manual-failover-via-portal"></a>Portál Azure

1. Přejděte na svůj účet Azure Cosmos a otevřete **globální nabídku Replikovat data.**

2. V horní části nabídky vyberte **ruční převzetí služeb při selhání**.

   ![Nabídka Globální replikace dat](./media/how-to-manage-database-account/replicate-data-globally.png)

3. V nabídce **Ruční převzetí služeb při selhání** vyberte novou oblast zápisu. Zaškrtnutím tohoto políčka označíte, že tato možnost mění oblast zápisu.

4. Chcete-li spustit převzetí služeb při selhání, vyberte **možnost OK**.

   ![Nabídka portálu Ruční převzetí služeb při selhání](./media/how-to-manage-database-account/manual-failover.png)

### <a name="azure-cli"></a><a id="enable-manual-failover-via-cli"></a>Azure CLI

Viz [Aktivační událost s převzetím služeb při selhání pomocí příkazového příkazového příkazu Azure](manage-with-cli.md#trigger-manual-failover)

### <a name="azure-powershell"></a><a id="enable-manual-failover-via-ps"></a>Azure PowerShell

Viz [Aktivační událost s převzetím služeb při selhání pomocí prostředí Powershell](manage-with-powershell.md#trigger-manual-failover)

## <a name="next-steps"></a>Další kroky

Další informace a příklady správy účtu Azure Cosmos, databáze a kontejnery našly, načtete následující články:

* [Správa Azure Cosmos DB pomocí Azure PowerShellu](manage-with-powershell.md)
* [Správa služby Azure Cosmos DB pomocí Azure CLI](manage-with-cli.md)

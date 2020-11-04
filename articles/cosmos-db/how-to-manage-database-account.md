---
title: Informace o správě účtů databáze ve službě Azure Cosmos DB
description: Naučte se spravovat Azure Cosmos DB prostředky pomocí šablon Azure Portal, PowerShellu, CLI a Azure Resource Manager.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 09/18/2020
ms.author: mjbrown
ms.openlocfilehash: d8763a794d2fb96d0c464fb1249b9eb400fd23e7
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93339865"
---
# <a name="manage-an-azure-cosmos-account"></a>Správa účtu služby Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Tento článek popisuje, jak spravovat různé úlohy účtu služby Azure Cosmos DB pomocí webu Azure Portal, Azure PowerShellu, Azure CLI a šablon Azure Resource Manageru.

## <a name="create-an-account"></a>Vytvoření účtu

### <a name="azure-portal"></a><a id="create-database-account-via-portal"></a>portál Azure

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

### <a name="azure-cli"></a><a id="create-database-account-via-cli"></a>Azure CLI

Přečtěte si prosím téma [Vytvoření účtu Azure Cosmos DB pomocí Azure CLI](manage-with-cli.md#create-an-azure-cosmos-db-account) .

### <a name="azure-powershell"></a><a id="create-database-account-via-ps"></a>Azure PowerShell

Další informace najdete v tématu [Vytvoření účtu Azure Cosmos DB pomocí PowerShellu](manage-with-powershell.md#create-account) .

### <a name="azure-resource-manager-template"></a><a id="create-database-account-via-arm-template"></a>Šablona Azure Resource Manageru

Další informace najdete v tématu [Vytvoření účtu Azure Cosmos DB s využitím šablon Azure Resource Manager](./manage-with-templates.md)

## <a name="addremove-regions-from-your-database-account"></a>Přidání oblastí do účtu databáze nebo jejich odebrání

### <a name="azure-portal"></a><a id="add-remove-regions-via-portal"></a>portál Azure

1. Přihlaste se k [Azure Portal](https://portal.azure.com).

1. Přejděte k účtu Azure Cosmos a otevřete nabídku **replikovat data globálně** .

1. Chcete-li přidat oblasti, vyberte šestiúhelníky na mapě pomocí **+** popisku, který odpovídá vašim požadovaným oblastem (y). Pokud chcete přidat oblast, vyberte možnost **+ Přidat oblast** a v rozevírací nabídce vyberte oblast.

1. Chcete-li odebrat oblasti, zrušte zaškrtnutí jedné nebo více oblastí z mapy tak, že vyberete modré šestiúhelníky se značkami zaškrtnutí. Nebo vyberte ikonu "wastebasket" ( 🗑 ) vedle oblasti na pravé straně.

1. Pokud chcete změny uložit, vyberte **OK**.

   :::image type="content" source="./media/how-to-manage-database-account/add-region.png" alt-text="Nabídka Přidat nebo odebrat oblasti":::

V režimu zápisu do jedné oblasti není možné odebrat oblast zápisu. Před odstraněním aktuální oblasti zápisu je potřeba provést převzetí služeb při selhání do jiné oblasti.

V režimu zápisu do více oblastí můžete přidat nebo odebrat libovolnou oblast, pokud máte alespoň jednu oblast.

### <a name="azure-cli"></a><a id="add-remove-regions-via-cli"></a>Azure CLI

Podívejte se prosím na téma [Přidání nebo odebrání oblastí pomocí Azure CLI](manage-with-cli.md#add-or-remove-regions) .

### <a name="azure-powershell"></a><a id="add-remove-regions-via-ps"></a>Azure PowerShell

Podívejte se prosím na téma [Přidání nebo odebrání oblastí pomocí PowerShellu](manage-with-powershell.md#update-account) .

## <a name="configure-multiple-write-regions"></a><a id="configure-multiple-write-regions"></a>Konfigurace několika oblastí zápisu

### <a name="azure-portal"></a><a id="configure-multiple-write-regions-portal"></a>portál Azure

Otevřete kartu **replikace dat globálně** a výběrem **Povolit** povolte zápisy ve více oblastech. Po povolení zápisů ve více oblastech se všechny oblasti čtení, které v daném účtu aktuálně máte, stanou oblastmi pro čtení a zápis.

:::image type="content" source="./media/how-to-manage-database-account/single-to-multi-master.png" alt-text="Účet Azure Cosmos konfiguruje snímek obrazovky s zápisy ve více oblastech.":::

### <a name="azure-cli"></a><a id="configure-multiple-write-regions-cli"></a>Azure CLI

Přečtěte si prosím téma [Povolení oblastí s více zápisy pomocí Azure CLI](manage-with-cli.md#enable-multiple-write-regions) .

### <a name="azure-powershell"></a><a id="configure-multiple-write-regions-ps"></a>Azure PowerShell

Viz téma [Povolení oblastí s několika zápisy pomocí prostředí PowerShell](manage-with-powershell.md#multi-region-writes) .

### <a name="resource-manager-template"></a><a id="configure-multiple-write-regions-arm"></a>Šablona Resource Manageru

Účet se dá migrovat z jedné oblasti zápisu do více oblastí pro zápis nasazením šablony Správce prostředků, která se používá k vytvoření účtu a nastavení `enableMultipleWriteLocations: true` . Následující šablona Azure Resource Manager je minimální šablona, která nasadí účet Azure Cosmos pro rozhraní SQL API se dvěma oblastmi a více umístěními pro zápis povolených.

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

Možnost automatického převzetí služeb při selhání umožňuje Azure Cosmos DB převzetí služeb při selhání do oblasti s nejvyšší prioritou převzetí služeb při selhání bez akce uživatele, pokud se oblast stane nedostupnou. Když je povolené automatické převzetí služeb při selhání, může se upravit priorita oblasti. Aby bylo možné povolit automatické převzetí služeb při selhání, musí mít účet dvě nebo více oblastí.

### <a name="azure-portal"></a><a id="enable-automatic-failover-via-portal"></a>portál Azure

1. Z účtu Azure Cosmos otevřete podokno **globálně replikovat data** .

2. V horní části podokna vyberte **automatické převzetí služeb při selhání**.

   :::image type="content" source="./media/how-to-manage-database-account/replicate-data-globally.png" alt-text="Nabídka Globální replikace dat":::

3. V podokně **automatické převzetí služeb při selhání** ověřte, že je **možnost povolit automatické převzetí služeb při** selhání nastavena na **zapnuto**. 

4. Vyberte **Uložit**.

   :::image type="content" source="./media/how-to-manage-database-account/automatic-failover.png" alt-text="Nabídka portálu Automatické převzetí služeb při selhání":::

### <a name="azure-cli"></a><a id="enable-automatic-failover-via-cli"></a>Azure CLI

Viz [Povolení automatického převzetí služeb při selhání pomocí Azure CLI](manage-with-cli.md#enable-automatic-failover) .

### <a name="azure-powershell"></a><a id="enable-automatic-failover-via-ps"></a>Azure PowerShell

Viz [Povolení automatického převzetí služeb při selhání pomocí PowerShellu](manage-with-powershell.md#enable-automatic-failover)

## <a name="set-failover-priorities-for-your-azure-cosmos-account"></a>Nastavení priorit převzetí služeb při selhání u účtu služby Azure Cosmos DB

Po nakonfigurování účtu Cosmos pro automatické převzetí služeb při selhání je možné změnit prioritu převzetí služeb při selhání pro oblasti.

> [!IMPORTANT]
> Pokud je účet nakonfigurovaný na automatické převzetí služeb při selhání, nemůžete upravit oblast zápisu (priorita převzetí služeb při selhání nula). Chcete-li změnit oblast zápisu, je nutné zakázat automatické převzetí služeb při selhání a provést ruční převzetí služeb při selhání.

### <a name="azure-portal"></a><a id="set-failover-priorities-via-portal"></a>portál Azure

1. Z účtu Azure Cosmos otevřete podokno **globálně replikovat data** .

2. V horní části podokna vyberte **automatické převzetí služeb při selhání**.

   :::image type="content" source="./media/how-to-manage-database-account/replicate-data-globally.png" alt-text="Nabídka Globální replikace dat":::

3. V podokně **automatické převzetí služeb při selhání** ověřte, že je **možnost povolit automatické převzetí služeb při** selhání nastavena na **zapnuto**.

4. Pokud chcete změnit prioritu převzetí služeb při selhání, přetáhněte oblasti čtení přes tři tečky na levé straně řádku, která se zobrazí, když na ně najedete myší.

5. Vyberte **Uložit**.

   :::image type="content" source="./media/how-to-manage-database-account/automatic-failover.png" alt-text="Nabídka portálu Automatické převzetí služeb při selhání":::

### <a name="azure-cli"></a><a id="set-failover-priorities-via-cli"></a>Azure CLI

Podívejte se prosím na [Nastavení priority převzetí služeb při selhání pomocí Azure CLI](manage-with-cli.md#set-failover-priority) .

### <a name="azure-powershell"></a><a id="set-failover-priorities-via-ps"></a>Azure PowerShell

Viz [Nastavení priority převzetí služeb při selhání pomocí PowerShellu](manage-with-powershell.md#modify-failover-priority) .

## <a name="perform-manual-failover-on-an-azure-cosmos-account"></a><a id="manual-failover"></a>Provedení ručního převzetí služeb při selhání účtu služby Azure Cosmos DB

> [!IMPORTANT]
> Aby tato operace proběhla úspěšně, musí být účet Azure Cosmos nakonfigurovaný na ruční převzetí služeb při selhání.

Proces ručního převzetí služeb při selhání zahrnuje změnu oblasti zápisu účtu (priorita převzetí služeb při selhání = 0) na jinou oblast nakonfigurovanou pro tento účet.

> [!NOTE]
> U účtů s více oblastmi zápisu nejde provést ruční převzetí služeb při selhání. V případě aplikací, které používají sadu Azure Cosmos SDK, bude sada SDK detekovat, kdy se oblast přestane používat, a pak automaticky přesměruje na nejbližší nejbližší oblast, pokud v sadě SDK používáte rozhraní API pro více koncových míst.

### <a name="azure-portal"></a><a id="enable-manual-failover-via-portal"></a>portál Azure

1. Přejděte k účtu Azure Cosmos a otevřete nabídku **replikovat data globálně** .

2. V horní části nabídky vyberte **ruční převzetí služeb při selhání**.

   :::image type="content" source="./media/how-to-manage-database-account/replicate-data-globally.png" alt-text="Nabídka Globální replikace dat":::

3. V nabídce **ruční převzetí služeb při selhání** vyberte novou oblast pro zápis. Zaškrtnutím políčka označíte, že pochopení této možnosti mění vaši oblast zápisu.

4. Pokud chcete převzetí služeb při selhání aktivovat, vyberte **OK**.

   :::image type="content" source="./media/how-to-manage-database-account/manual-failover.png" alt-text="Nabídka portálu Ruční převzetí služeb při selhání":::

### <a name="azure-cli"></a><a id="enable-manual-failover-via-cli"></a>Azure CLI

Podívejte se prosím na [ruční převzetí služeb při selhání pomocí Azure CLI](manage-with-cli.md#trigger-manual-failover) .

### <a name="azure-powershell"></a><a id="enable-manual-failover-via-ps"></a>Azure PowerShell

Podívejte se prosím na [ruční převzetí služeb při selhání pomocí PowerShellu](manage-with-powershell.md#trigger-manual-failover)

## <a name="next-steps"></a>Další kroky

Další informace a příklady, jak spravovat účet Azure Cosmos i databáze a kontejnery, najdete v následujících článcích:

* [Správa Azure Cosmos DB pomocí Azure PowerShell](manage-with-powershell.md)
* [Správa služby Azure Cosmos DB pomocí Azure CLI](manage-with-cli.md)
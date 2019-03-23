---
title: 'Azure Resource Manageru: Vytvoření izolované databáze – Azure SQL Database | Dokumentace Microsoftu'
description: Vytvoření izolované databáze ve službě Azure SQL Database pomocí šablony Azure Resource Manageru.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: carlrab
manager: craigg
ms.date: 03/22/2019
ms.openlocfilehash: 01e14f86b16db6d998d60e74211ae5ad77381461
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2019
ms.locfileid: "58373018"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-the-azure-resource-manager-template"></a>Rychlý start: Vytvoření izolované databáze ve službě Azure SQL Database pomocí šablony Azure Resource Manageru

Vytváření [izolované databáze](sql-database-single-database.md) je možnost nejrychlejší a nejjednodušší nasazení pro vytvoření databáze ve službě Azure SQL Database. V tomto rychlém startu se dozvíte, jak vytvořit izolované databáze pomocí šablony Azure Resource Manageru. Další informace najdete v tématu [dokumentace ke službě Azure Resource Manageru](/azure/azure-resource-manager/).

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/).

## <a name="create-a-single-database"></a>Vytvoření izolované databáze

Izolované databáze má definovanou sadu výpočetních, paměťových, vstupně-výstupní operace a úložiště prostředků pomocí jednoho ze dvou [zakoupení modely](sql-database-purchase-models.md). Při vytvoření izolované databáze také definovat [serveru služby SQL Database](sql-database-servers.md) ho spravovat a umístěte ji v rámci [skupiny prostředků Azure](../azure-resource-manager/resource-group-overview.md) v určité oblasti.

Šablona použitá v tomto rychlém startu je z [šablon rychlého startu Azure](https://azure.microsoft.com/resources/templates/201-sql-threat-detection-server-policy-optional-db/). Následující soubor JSON je šablonu, která se používá v tomto článku. Další ukázkové šablony Azure SQL database najdete [tady](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sql&pageNumber=1&sort=Popular).

[!code-json[create-azure-sql-database](~/quickstart-templates/201-sql-threat-detection-server-policy-optional-db/azuredeploy.json)]

1. Vyberte následující obrázek a přihlaste se k Azure a otevřete šablonu.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-sql-threat-detection-server-policy-optional-db%2Fazuredeploy.json"><img src="./media/sql-database-single-database-get-started-template/deploy-to-azure.png" alt="deploy to azure"/></a>

2. Vyberte nebo zadejte následující hodnoty.  

    ![Šablony Resource Manageru vytvořte azure sql database](./media/sql-database-single-database-get-started-template/create-azure-sql-database-resource-manager-template.png)

    Pokud není zadána, použijte výchozí hodnoty.

    * **Předplatné:** Vyberte předplatné Azure.
    * **Skupina prostředků**: vyberte **vytvořit nový**, zadejte jedinečný název pro skupinu prostředků a pak klikněte na tlačítko **OK**. 
    * **Umístění:** Vyberte prosím umístění.  Například **USA (střed)**.
    * **Uživatel s rolí správce**: Zadejte uživatelské jméno správce serveru databáze SQL.
    * **Heslo správce**: Zadejte heslo správce. 
    * **Souhlasím s podmínkami a ujednáními stavu výše**: Vybrat
3. Vyberte **Koupit**.

## <a name="query-the-database"></a>Dotaz na databázi

Dotaz na databázi, naleznete v tématu [dotaz na databázi](./sql-database-single-database-get-started.md#query-the-database).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete přejít na zachovat tuto skupinu prostředků, serveru databáze a izolované databáze [další kroky](#next-steps). Následující kroky ukazují, jak k připojení a dotazování databáze pomocí různých metod.

Odstranit skupinu prostředků pomocí Azure Powershellu nebo rozhraní příkazového řádku Azure:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName 
```

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName 
```

## <a name="next-steps"></a>Další postup

- Vytvořte pravidlo brány firewall na úrovni serveru pro připojení k izolované databáze z místní nebo vzdálené nástroje. Další informace najdete v tématu [vytvořit pravidlo brány firewall na úrovni serveru](sql-database-server-level-firewall-rule.md).
- Po vytvoření pravidla brány firewall na úrovni serveru, [připojení a dotazování](sql-database-connect-query.md) vaší databáze pomocí několika různých nástrojů a jazyků.
  - [Připojení a dotazování pomocí SQL Server Management Studia](sql-database-connect-query-ssms.md)
  - [Připojení a dotazování pomocí Azure Data Studia](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Vytvoření izolované databáze pomocí Azure CLI najdete v tématu [ukázky v Azure CLI](sql-database-cli-samples.md).
- Vytvoření izolované databáze pomocí Azure Powershellu najdete v tématu [ukázky Azure Powershellu](sql-database-powershell-samples.md).

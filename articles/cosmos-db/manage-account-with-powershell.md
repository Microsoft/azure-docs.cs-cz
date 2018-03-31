---
title: Automatizace Azure Cosmos DB - Management pomocí prostředí Powershell | Microsoft Docs
description: Použití Azure Powershell spravovat své účty Azure Cosmos DB.
services: cosmos-db
author: dmakwana
manager: jhubbard
editor: ''
tags: azure-resource-manager
documentationcenter: ''
ms.assetid: ''
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/21/2017
ms.author: dimakwan
ms.openlocfilehash: 12c7800ee2a823cca5394841cae86eeacc20fb1b
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="create-an-azure-cosmos-db-account-using-powershell"></a>Vytvoření účtu Azure Cosmos DB pomocí prostředí PowerShell

Následující příručka popisuje příkazy, které automatizují správu svých účtů databáze Azure Cosmos DB pomocí Azure Powershell. Zahrnuje také příkazy pro správu klíčů k účtu a priorit převzetí služeb při selhání v [účty databáze více oblast][scaling-globally]. Aktualizace databázového účtu můžete upravit zásady konzistence a přidat nebo odebrat oblasti. Pro různé platformy správy účtu Azure Cosmos DB, můžete použít buď [rozhraní příkazového řádku Azure](cli-samples.md), [rozhraní API REST zprostředkovatele prostředků][rp-rest-api], nebo [portálu Azure ](create-sql-api-dotnet.md#create-account).

## <a name="getting-started"></a>Začínáme

Postupujte podle pokynů v [postup instalace a konfigurace prostředí Azure PowerShell] [ powershell-install-configure] k instalaci a přihlaste se k účtu Azure Resource Manager v prostředí Powershell.

### <a name="notes"></a>Poznámky

* Pokud chcete spustit následující příkazy bez nutnosti potvrzení uživatelem, připojte `-Force` příznak k příkazu.
* Následující příkazy jsou synchronní.

## <a id="create-documentdb-account-powershell"></a> Vytvoření účtu Azure Cosmos DB

Tento příkaz umožňuje vytvoření účtu Azure Cosmos DB databáze. Konfigurace nového účtu databáze jako buď jedné oblasti nebo [více oblast] [ scaling-globally] s určitým [konzistence zásad](consistency-levels.md).

    $locations = @(@{"locationName"="<write-region-location>"; "failoverPriority"=0}, @{"locationName"="<read-region-location>"; "failoverPriority"=1})
    $iprangefilter = "<ip-range-filter>"
    $consistencyPolicy = @{"defaultConsistencyLevel"="<default-consistency-level>"; "maxIntervalInSeconds"="<max-interval>"; "maxStalenessPrefix"="<max-staleness-prefix>"}
    $CosmosDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    New-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName <resource-group-name>  -Location "<resource-group-location>" -Name <database-account-name> -Properties $CosmosDBProperties
    
* `<write-region-location>` Název umístění oblasti zápisu databázového účtu. Toto umístění je potřeba mít hodnotu převzetí služeb při selhání s prioritou 0. Musí mít přesně jeden zápis oblast na databázového účtu.
* `<read-region-location>` Název umístění oblasti čtení databázového účtu. Toto umístění je potřeba mít hodnotu převzetí služeb při selhání s prioritou větší než 0. Může existovat více než jeden pro čtení oblasti za databázového účtu.
* `<ip-range-filter>` Určuje sadu IP adresy nebo rozsahy IP adres ve formátu CIDR zahrnuty jako seznam povolených IP adresy klienta pro účet danou databázi. IP adresy a rozsahy musí být oddělené čárkami a nesmí obsahovat žádné mezery. Další informace najdete v tématu [podpora brány Firewall databáze Azure Cosmos](firewall-support.md)
* `<default-consistency-level>` Výchozí úroveň konzistence účtu Azure Cosmos DB. Další informace najdete v tématu [úrovně konzistence v Azure Cosmos DB](consistency-levels.md).
* `<max-interval>` Při použití s konzistence typu s ohraničenou Prošlostí, tato hodnota představuje čas množství typu prošlostí (v sekundách) tolerovat. Přijatém rozsahu pro tuto hodnotu je 1 až 100.
* `<max-staleness-prefix>` Při použití s konzistence typu s ohraničenou Prošlostí, tato hodnota představuje počet zastaralých požadavků tolerovat. Přijatém rozsahu pro tuto hodnotu je 1 – 2 147 483 647.
* `<resource-group-name>` Název [skupiny prostředků Azure] [ azure-resource-groups] , ke které se nový účet Azure Cosmos DB databáze patří do.
* `<resource-group-location>` Umístění skupiny prostředků Azure, do které patří nový databázový účet Azure Cosmos DB.
* `<database-account-name>` Název účtu Azure Cosmos DB databázi vytvořit. Lze použít pouze malá písmena, čísla, '-' znak a musí být v rozmezí 3 až 50 znaků.

Příklad: 

    $locations = @(@{"locationName"="West US"; "failoverPriority"=0}, @{"locationName"="East US"; "failoverPriority"=1})
    $iprangefilter = ""
    $consistencyPolicy = @{"defaultConsistencyLevel"="BoundedStaleness"; "maxIntervalInSeconds"=5; "maxStalenessPrefix"=100}
    $CosmosDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    New-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Location "West US" -Name "docdb-test" -Properties $CosmosDBProperties

### <a name="notes"></a>Poznámky
* V předchozím příkladu vytvoří databázový účet se dvěma oblastmi. Je také možné vytvoření databázového účtu pomocí jedné oblasti (který je určený jako oblasti zápisu a mít hodnotu převzetí služeb při selhání s prioritou 0) nebo víc než dvou oblastech. Další informace najdete v tématu [účty databáze více oblast][scaling-globally].
* Umístění musí být oblasti, ve kterých je Azure Cosmos DB všeobecně dostupná. Aktuální seznam oblastí je uvedený na [oblasti Azure stránky](https://azure.microsoft.com/regions/#services).

## <a id="update-documentdb-account-powershell"></a> Aktualizace databáze účtu Azure Cosmos DB

Tento příkaz umožňuje aktualizovat vlastnostech účtu databáze Azure Cosmos DB. To zahrnuje konzistence zásad a které databázový účet existuje v umístění.

> [!NOTE]
> Tento příkaz umožňuje přidávat a odebírat oblasti, ale neumožňuje změnit priorit převzetí služeb při selhání. Chcete-li upravit priorit převzetí služeb při selhání, přečtěte si téma [pod](#modify-failover-priority-powershell).

    $locations = @(@{"locationName"="<write-region-location>"; "failoverPriority"=0}, @{"locationName"="<read-region-location>"; "failoverPriority"=1})
    $iprangefilter = "<ip-range-filter>"
    $consistencyPolicy = @{"defaultConsistencyLevel"="<default-consistency-level>"; "maxIntervalInSeconds"="<max-interval>"; "maxStalenessPrefix"="<max-staleness-prefix>"}
    $CosmosDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    Set-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName <resource-group-name> -Name <database-account-name> -Properties $CosmosDBProperties
    
* `<write-region-location>` Název umístění oblasti zápisu databázového účtu. Toto umístění je potřeba mít hodnotu převzetí služeb při selhání s prioritou 0. Musí mít přesně jeden zápis oblast na databázového účtu.
* `<read-region-location>` Název umístění oblasti čtení databázového účtu. Toto umístění je potřeba mít hodnotu převzetí služeb při selhání s prioritou větší než 0. Může existovat více než jeden pro čtení oblasti za databázového účtu.
* `<default-consistency-level>` Výchozí úroveň konzistence účtu Azure Cosmos DB. Další informace najdete v tématu [úrovně konzistence v Azure Cosmos DB](consistency-levels.md).
* `<ip-range-filter>` Určuje sadu IP adresy nebo rozsahy IP adres ve formátu CIDR zahrnuty jako seznam povolených IP adresy klienta pro účet danou databázi. IP adresy a rozsahy musí být oddělené čárkami a nesmí obsahovat žádné mezery. Další informace najdete v tématu [podpora brány Firewall databáze Azure Cosmos](firewall-support.md)
* `<max-interval>` Při použití s konzistence typu s ohraničenou Prošlostí, tato hodnota představuje čas množství typu prošlostí (v sekundách) tolerovat. Přijatém rozsahu pro tuto hodnotu je 1 až 100.
* `<max-staleness-prefix>` Při použití s konzistence typu s ohraničenou Prošlostí, tato hodnota představuje počet zastaralých požadavků tolerovat. Přijatém rozsahu pro tuto hodnotu je 1 – 2 147 483 647.
* `<resource-group-name>` Název [skupiny prostředků Azure] [ azure-resource-groups] , ke které se nový účet Azure Cosmos DB databáze patří do.
* `<resource-group-location>` Umístění skupiny prostředků Azure, do které patří nový databázový účet Azure Cosmos DB.
* `<database-account-name>` Název účtu Azure Cosmos DB databáze aktualizovat.

Příklad: 

    $locations = @(@{"locationName"="West US"; "failoverPriority"=0}, @{"locationName"="East US"; "failoverPriority"=1})
    $iprangefilter = ""
    $consistencyPolicy = @{"defaultConsistencyLevel"="BoundedStaleness"; "maxIntervalInSeconds"=5; "maxStalenessPrefix"=100}
    $CosmosDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    Set-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test" -Properties $CosmosDBProperties

## <a id="delete-documentdb-account-powershell"></a> Odstranit účet Azure Cosmos DB databáze

Tento příkaz umožňuje odstranit existující databáze účet Azure Cosmos DB.

    Remove-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"
    
* `<resource-group-name>` Název [skupiny prostředků Azure] [ azure-resource-groups] , ke které se nový účet Azure Cosmos DB databáze patří do.
* `<database-account-name>` Název účtu databáze Azure Cosmos DB k odstranění.

Příklad:

    Remove-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="get-documentdb-properties-powershell"></a> Získat vlastnosti účtu databáze Azure Cosmos DB

Tento příkaz umožňuje získat vlastnosti existující databáze účet Azure Cosmos DB.

    Get-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"

* `<resource-group-name>` Název [skupiny prostředků Azure] [ azure-resource-groups] , ke které se nový účet Azure Cosmos DB databáze patří do.
* `<database-account-name>` Název databázového účtu Azure Cosmos DB.

Příklad:

    Get-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="update-tags-powershell"></a> Značky aktualizace účtu databáze Azure Cosmos DB

Následující příklad popisuje, jak nastavit [značky prostředku Azure] [ azure-resource-tags] pro vaši Azure DB Cosmos databáze účtu.

> [!NOTE]
> Tento příkaz je možné kombinovat s příkazy vytvoření nebo aktualizace připojením `-Tags` příznak s odpovídající parametr.

Příklad:

    $tags = @{"dept" = "Finance”; environment = “Production”}
    Set-AzureRmResource -ResourceType “Microsoft.DocumentDB/databaseAccounts”  -ResourceGroupName "rg-test" -Name "docdb-test" -Tags $tags

## <a id="list-account-keys-powershell"></a> Klíče účtu seznamu

Když vytvoříte účet Azure Cosmos DB, generuje tato služba dva hlavní přístupové klíče, které lze použít pro ověření při přístupu k účtu Azure Cosmos DB. Poskytnutím dvou přístupových klíčů Azure Cosmos DB umožňuje znovu vygenerovat klíče bez přerušení ke svému účtu Azure Cosmos DB. Jen pro čtení klíče pro ověřování operacím jen pro čtení jsou také k dispozici. (Primární i sekundární) existují dva klíče pro čtení a zápis (primární i sekundární) a dva klíče jen pro čtení.

    $keys = Invoke-AzureRmResourceAction -Action listKeys -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"

* `<resource-group-name>` Název [skupiny prostředků Azure] [ azure-resource-groups] , ke které se nový účet Azure Cosmos DB databáze patří do.
* `<database-account-name>` Název databázového účtu Azure Cosmos DB.

Příklad:

    $keys = Invoke-AzureRmResourceAction -Action listKeys -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="list-connection-strings-powershell"></a> Seznamu připojovacích řetězců

Pro účty MongoDB mohou být načteny připojovacího řetězce pro připojení k účtu databázi MongoDB aplikace, pomocí následujícího příkazu.

    $keys = Invoke-AzureRmResourceAction -Action listConnectionStrings -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"

* `<resource-group-name>` Název [skupiny prostředků Azure] [ azure-resource-groups] , ke které se nový účet Azure Cosmos DB databáze patří do.
* `<database-account-name>` Název databázového účtu Azure Cosmos DB.

Příklad:

    $keys = Invoke-AzureRmResourceAction -Action listConnectionStrings -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="regenerate-account-key-powershell"></a> Znovu vygenerovat klíč účtu

Měli byste změnit přístupové klíče k účtu Azure Cosmos DB pravidelně pro zvýšení zabezpečení připojení. Abyste mohli udržovat připojení k účtu Azure Cosmos DB používat jeden přístupový klíč, zatímco si znovu vygenerujete druhý přístupový klíč jsou přiřazeny dva přístupové klíče.

    Invoke-AzureRmResourceAction -Action regenerateKey -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>" -Parameters @{"keyKind"="<key-kind>"}

* `<resource-group-name>` Název [skupiny prostředků Azure] [ azure-resource-groups] , ke které se nový účet Azure Cosmos DB databáze patří do.
* `<database-account-name>` Název databázového účtu Azure Cosmos DB.
* `<key-kind>` Jedním ze čtyř typů klíčů: ["Primární" | " Sekundární "|" PrimaryReadonly "|" SecondaryReadonly"], které chcete znovu vygenerovat.

Příklad:

    Invoke-AzureRmResourceAction -Action regenerateKey -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test" -Parameters @{"keyKind"="Primary"}

## <a id="modify-failover-priority-powershell"></a> Upravte prioritu převzetí služeb při selhání databáze účtu Azure Cosmos DB

Pro účty určené pro více oblastí databáze můžete změnit prioritu převzetí služeb při selhání různých oblastech, které v Azure Cosmos DB databázový účet existuje. Další informace o převzetí služeb při selhání ve vašem účtu Azure Cosmos DB databáze najdete v tématu [distribuci dat globálně pomocí Azure Cosmos DB][distribute-data-globally].

    $failoverPolicies = @(@{"locationName"="<write-region-location>"; "failoverPriority"=0},@{"locationName"="<read-region-location>"; "failoverPriority"=1})
    Invoke-AzureRmResourceAction -Action failoverPriorityChange -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>" -Parameters @{"failoverPolicies"=$failoverPolicies}

* `<write-region-location>` Název umístění oblasti zápisu databázového účtu. Toto umístění je potřeba mít hodnotu převzetí služeb při selhání s prioritou 0. Musí mít přesně jeden zápis oblast na databázového účtu.
* `<read-region-location>` Název umístění oblasti čtení databázového účtu. Toto umístění je potřeba mít hodnotu převzetí služeb při selhání s prioritou větší než 0. Může existovat více než jeden pro čtení oblasti za databázového účtu.
* `<resource-group-name>` Název [skupiny prostředků Azure] [ azure-resource-groups] , ke které se nový účet Azure Cosmos DB databáze patří do.
* `<database-account-name>` Název databázového účtu Azure Cosmos DB.

Příklad:

    $failoverPolicies = @(@{"locationName"="East US"; "failoverPriority"=0},@{"locationName"="West US"; "failoverPriority"=1})
    Invoke-AzureRmResourceAction -Action failoverPriorityChange -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test" -Parameters @{"failoverPolicies"=$failoverPolicies}

## <a name="next-steps"></a>Další postup

* Připojení pomocí rozhraní .NET, najdete v části [připojit a zadávat dotazy pomocí .NET](create-sql-api-dotnet.md).
* Připojení pomocí Node.js, najdete v části [připojit a zadávat dotazy s Node.js a aplikace pro práci s MongoDB](create-mongodb-nodejs.md).

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[powershell-install-configure]: https://docs.microsoft.com/azure/powershell-install-configure
[scaling-globally]: distribute-data-globally.md#EnableGlobalDistribution
[distribute-data-globally]: distribute-data-globally.md
[azure-resource-groups]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups
[azure-resource-tags]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags
[rp-rest-api]: https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/

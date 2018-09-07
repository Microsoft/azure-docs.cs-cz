---
title: Automatizace služby Azure Cosmos DB – správa pomocí Powershellu | Dokumentace Microsoftu
description: Použití Azure Powershellu spravovat účty služby Azure Cosmos DB.
services: cosmos-db
author: dmakwana
manager: kfile
editor: ''
tags: azure-resource-manager
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 04/21/2017
ms.author: dimakwan
ms.openlocfilehash: 90de671d8e57244765f1da439649e57485814533
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44051659"
---
# <a name="create-an-azure-cosmos-db-account-using-powershell"></a>Vytvoření účtu služby Azure Cosmos DB pomocí Powershellu

Následující průvodci popisuje příkazy, které automatizují správu účtů databáze Azure Cosmos DB pomocí Azure Powershellu. Zahrnuje také příkazy pro správu klíčů účtu a priority převzetí služeb při selhání v [účty databáze ve více oblastech][scaling-globally]. Aktualizace vašeho účtu databáze můžete upravit zásady konzistence a přidat/odebrat oblasti. Pro různé platformy správy účtu služby Azure Cosmos DB, můžete použít buď [rozhraní příkazového řádku Azure](cli-samples.md), [rozhraní REST API poskytovatele prostředků][rp-rest-api], nebo [webu Azure portal ](create-sql-api-dotnet.md#create-account).

## <a name="getting-started"></a>Začínáme

Postupujte podle pokynů v [instalace a konfigurace Azure Powershellu] [ powershell-install-configure] k instalaci a přihlaste se ke svému účtu Azure Resource Manageru v Powershellu.

### <a name="notes"></a>Poznámky

* Pokud chcete spustit následující příkazy bez nutnosti potvrzení uživatelem, připojte `-Force` příznak, který tento příkaz.
* Následující příkazy jsou synchronní.

## <a id="create-documentdb-account-powershell"></a> Vytvoření účtu služby Azure Cosmos DB

Tento příkaz umožňuje vytvoření účtu databáze Azure Cosmos DB. Konfigurace nového účtu databáze jako buď jedné oblasti nebo [ve více oblastech] [ scaling-globally] s určitým [zásady konzistence](consistency-levels.md).

    $locations = @(@{"locationName"="<write-region-location>"; "failoverPriority"=0}, @{"locationName"="<read-region-location>"; "failoverPriority"=1})
    $iprangefilter = "<ip-range-filter>"
    $consistencyPolicy = @{"defaultConsistencyLevel"="<default-consistency-level>"; "maxIntervalInSeconds"="<max-interval>"; "maxStalenessPrefix"="<max-staleness-prefix>"}
    $CosmosDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    New-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName <resource-group-name>  -Location "<resource-group-location>" -Name <database-account-name> -Properties $CosmosDBProperties
    
* `<write-region-location>` Název umístění oblasti databázový účet pro zápis. Toto umístění musí mít hodnotu priority převzetí služeb při selhání z 0. Musí obsahovat přesně jednu oblast zápisu a databázový účet.
* `<read-region-location>` Název umístění oblasti čtení účtu databáze. Toto umístění musí mít hodnotu priority převzetí služeb při selhání větší než 0. Může existovat více oblastí čtení za databázový účet.
* `<ip-range-filter>` Určuje sadu IP adres nebo rozsahy IP adres ve formátu CIDR. zahrnou se jako seznam povolených klientských IP adres pro danou databázi účet. IP adresách/rozsazích musí být oddělené čárkami a nesmí obsahovat žádné mezery. Další informace najdete v tématu [podporu brány Firewall pro Azure Cosmos DB](firewall-support.md)
* `<default-consistency-level>` Výchozí úroveň konzistence účtu služby Azure Cosmos DB. Další informace najdete v tématu [úrovně konzistence ve službě Azure Cosmos DB](consistency-levels.md).
* `<max-interval>` Při použití s konzistenci omezená Neaktuálnost, tato hodnota představuje čas množství neaktuálnost (v sekundách) tolerovat. Pro tuto hodnotu přípustný rozsah je 1-100.
* `<max-staleness-prefix>` Při použití s konzistenci omezená Neaktuálnost, tato hodnota představuje počet zastaralých požadavků tolerovat. Pro tuto hodnotu přípustný rozsah je 1 – 2 147 483 647.
* `<resource-group-name>` Název [skupiny prostředků Azure] [ azure-resource-groups] , ke které patří nový účet databáze Azure Cosmos DB k.
* `<resource-group-location>` Umístění skupiny prostředků Azure, ke kterému patří nový účet databáze Azure Cosmos DB.
* `<database-account-name>` Název účtu databáze Azure Cosmos DB má být vytvořen. Může použít jenom malá písmena, číslice, "-" znaků a musí být dlouhý 3 až 50 znaků.

Příklad: 

    $locations = @(@{"locationName"="West US"; "failoverPriority"=0}, @{"locationName"="East US"; "failoverPriority"=1})
    $iprangefilter = ""
    $consistencyPolicy = @{"defaultConsistencyLevel"="BoundedStaleness"; "maxIntervalInSeconds"=5; "maxStalenessPrefix"=100}
    $CosmosDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    New-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Location "West US" -Name "docdb-test" -Properties $CosmosDBProperties

### <a name="notes"></a>Poznámky
* V předchozím příkladu vytvoří databázový účet se dvěma oblastmi. Je také možné vytvořit účet databáze s využitím jedné oblasti (která je určena jako oblast zápisu a mít hodnotu priority převzetí služeb při selhání 0) nebo více než dvou oblastech. Další informace najdete v tématu [účty databáze ve více oblastech][scaling-globally].
* Umístění musí být oblasti služby Azure Cosmos DB je obecně dostupná. Aktuální seznam oblastí je k dispozici na [oblastí Azure stránky](https://azure.microsoft.com/regions/#services).

## <a id="update-documentdb-account-powershell"></a> Aktualizace účtu databáze Azure Cosmos DB

Tento příkaz umožňuje aktualizovat vlastnosti svého účtu databáze Azure Cosmos DB. To zahrnuje zásady konzistence a které databázový účet existuje v umístění.

> [!NOTE]
> Tento příkaz umožňuje přidávat a odebírat oblastech, ale neumožňuje úpravy priorit převzetí služeb při selhání. Změna priority převzetí služeb při selhání, najdete v článku [níže](#modify-failover-priority-powershell).

    $locations = @(@{"locationName"="<write-region-location>"; "failoverPriority"=0}, @{"locationName"="<read-region-location>"; "failoverPriority"=1})
    $iprangefilter = "<ip-range-filter>"
    $consistencyPolicy = @{"defaultConsistencyLevel"="<default-consistency-level>"; "maxIntervalInSeconds"="<max-interval>"; "maxStalenessPrefix"="<max-staleness-prefix>"}
    $CosmosDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    Set-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName <resource-group-name> -Name <database-account-name> -Properties $CosmosDBProperties
    
* `<write-region-location>` Název umístění oblasti databázový účet pro zápis. Toto umístění musí mít hodnotu priority převzetí služeb při selhání z 0. Musí obsahovat přesně jednu oblast zápisu a databázový účet.
* `<read-region-location>` Název umístění oblasti čtení účtu databáze. Toto umístění musí mít hodnotu priority převzetí služeb při selhání větší než 0. Může existovat více oblastí čtení za databázový účet.
* `<default-consistency-level>` Výchozí úroveň konzistence účtu služby Azure Cosmos DB. Další informace najdete v tématu [úrovně konzistence ve službě Azure Cosmos DB](consistency-levels.md).
* `<ip-range-filter>` Určuje sadu IP adres nebo rozsahy IP adres ve formátu CIDR. zahrnou se jako seznam povolených klientských IP adres pro danou databázi účet. IP adresách/rozsazích musí být oddělené čárkami a nesmí obsahovat žádné mezery. Další informace najdete v tématu [podporu brány Firewall pro Azure Cosmos DB](firewall-support.md)
* `<max-interval>` Při použití s konzistenci omezená Neaktuálnost, tato hodnota představuje čas množství neaktuálnost (v sekundách) tolerovat. Pro tuto hodnotu přípustný rozsah je 1-100.
* `<max-staleness-prefix>` Při použití s konzistenci omezená Neaktuálnost, tato hodnota představuje počet zastaralých požadavků tolerovat. Pro tuto hodnotu přípustný rozsah je 1 – 2 147 483 647.
* `<resource-group-name>` Název [skupiny prostředků Azure] [ azure-resource-groups] , ke které patří nový účet databáze Azure Cosmos DB k.
* `<resource-group-location>` Umístění skupiny prostředků Azure, ke kterému patří nový účet databáze Azure Cosmos DB.
* `<database-account-name>` Název účtu databáze Azure Cosmos DB má být aktualizován.

Příklad: 

    $locations = @(@{"locationName"="West US"; "failoverPriority"=0}, @{"locationName"="East US"; "failoverPriority"=1})
    $iprangefilter = ""
    $consistencyPolicy = @{"defaultConsistencyLevel"="BoundedStaleness"; "maxIntervalInSeconds"=5; "maxStalenessPrefix"=100}
    $CosmosDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    Set-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test" -Properties $CosmosDBProperties

## <a id="delete-documentdb-account-powershell"></a> Odstranit účet databáze Azure Cosmos DB

Tento příkaz umožňuje odstranit účet databáze Azure Cosmos DB.

    Remove-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"
    
* `<resource-group-name>` Název [skupiny prostředků Azure] [ azure-resource-groups] , ke které patří nový účet databáze Azure Cosmos DB k.
* `<database-account-name>` Název účtu databáze Azure Cosmos DB má být odstraněn.

Příklad:

    Remove-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="get-documentdb-properties-powershell"></a> Získání vlastností účtu databáze Azure Cosmos DB

Tento příkaz umožňuje získat vlastnosti existujícího účtu databáze Azure Cosmos DB.

    Get-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"

* `<resource-group-name>` Název [skupiny prostředků Azure] [ azure-resource-groups] , ke které patří nový účet databáze Azure Cosmos DB k.
* `<database-account-name>` Název účtu databáze Azure Cosmos DB.

Příklad:

    Get-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="update-tags-powershell"></a> Aktualizace značky k účtu databáze Azure Cosmos DB

Následující příklad popisuje, jak nastavit [značek prostředků Azure] [ azure-resource-tags] účet databáze Azure Cosmos DB.

> [!NOTE]
> Tento příkaz je možné kombinovat s příkazy vytvořit nebo aktualizovat přidáním `-Tags` příznak s odpovídajícím parametrem.

Příklad:

    $tags = @{"dept" = "Finance"; environment = "Production"}
    Set-AzureRmResource -ResourceType "Microsoft.DocumentDB/databaseAccounts"  -ResourceGroupName "rg-test" -Name "docdb-test" -Tags $tags

## <a id="list-account-keys-powershell"></a> Vypsat klíče účtu

Při vytváření účtu služby Azure Cosmos DB, generuje tato služba dva hlavní přístupové klíče, které se dá použít pro ověření při přístupu k účtu Azure Cosmos DB. Poskytnutím dvou přístupových klíčů služby Azure Cosmos DB umožňuje znovu vygenerovat klíče bez přerušení ke svému účtu Azure Cosmos DB. Klíče jen pro čtení pro ověřování jen pro čtení operace jsou také k dispozici. (Primární i sekundární) existují dva klíče pro čtení i zápis (primární i sekundární) a dva klíče jen pro čtení.

    $keys = Invoke-AzureRmResourceAction -Action listKeys -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"

* `<resource-group-name>` Název [skupiny prostředků Azure] [ azure-resource-groups] , ke které patří nový účet databáze Azure Cosmos DB k.
* `<database-account-name>` Název účtu databáze Azure Cosmos DB.

Příklad:

    $keys = Invoke-AzureRmResourceAction -Action listKeys -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="list-connection-strings-powershell"></a> Seznam připojovacích řetězců

Pro účty MongoDB můžete načíst připojovací řetězec pro připojení aplikace MongoDB k účtu databáze pomocí následujícího příkazu.

    $keys = Invoke-AzureRmResourceAction -Action listConnectionStrings -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"

* `<resource-group-name>` Název [skupiny prostředků Azure] [ azure-resource-groups] , ke které patří nový účet databáze Azure Cosmos DB k.
* `<database-account-name>` Název účtu databáze Azure Cosmos DB.

Příklad:

    $keys = Invoke-AzureRmResourceAction -Action listConnectionStrings -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="regenerate-account-key-powershell"></a> Znovu vygenerovat klíč účtu

Měli byste změnit přístupové klíče ke svému účtu Azure Cosmos DB pravidelně, aby lépe zabezpečit vaše připojení. Dva přístupové klíče jsou přiřazeny vám umožní spravovat připojení k účtu Azure Cosmos DB používat jeden přístupový klíč, zatímco znovu vygenerujete druhý přístupový klíč.

    Invoke-AzureRmResourceAction -Action regenerateKey -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>" -Parameters @{"keyKind"="<key-kind>"}

* `<resource-group-name>` Název [skupiny prostředků Azure] [ azure-resource-groups] , ke které patří nový účet databáze Azure Cosmos DB k.
* `<database-account-name>` Název účtu databáze Azure Cosmos DB.
* `<key-kind>` Jedním ze čtyř typů klíčů: ["Primární" | " Sekundární "|" PrimaryReadonly "|" SecondaryReadonly"], který chcete znovu vygenerovat.

Příklad:

    Invoke-AzureRmResourceAction -Action regenerateKey -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test" -Parameters @{"keyKind"="Primary"}

## <a id="modify-failover-priority-powershell"></a> Změna Priority převzetí služeb při selhání k účtu databáze Azure Cosmos DB

Pro účty databáze ve více oblastech můžete změnit prioritu převzetí služeb při selhání různé oblasti, ve kterých existuje účet databáze Azure Cosmos DB v. Další informace o převzetí služeb při selhání ve vašem účtu databáze Azure Cosmos DB najdete v tématu [globální distribuce dat pomocí služby Azure Cosmos DB][distribute-data-globally].

    $failoverPolicies = @(@{"locationName"="<write-region-location>"; "failoverPriority"=0},@{"locationName"="<read-region-location>"; "failoverPriority"=1})
    Invoke-AzureRmResourceAction -Action failoverPriorityChange -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>" -Parameters @{"failoverPolicies"=$failoverPolicies}

* `<write-region-location>` Název umístění oblasti databázový účet pro zápis. Toto umístění musí mít hodnotu priority převzetí služeb při selhání z 0. Musí obsahovat přesně jednu oblast zápisu a databázový účet.
* `<read-region-location>` Název umístění oblasti čtení účtu databáze. Toto umístění musí mít hodnotu priority převzetí služeb při selhání větší než 0. Může existovat více oblastí čtení za databázový účet.
* `<resource-group-name>` Název [skupiny prostředků Azure] [ azure-resource-groups] , ke které patří nový účet databáze Azure Cosmos DB k.
* `<database-account-name>` Název účtu databáze Azure Cosmos DB.

Příklad:

    $failoverPolicies = @(@{"locationName"="East US"; "failoverPriority"=0},@{"locationName"="West US"; "failoverPriority"=1})
    Invoke-AzureRmResourceAction -Action failoverPriorityChange -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test" -Parameters @{"failoverPolicies"=$failoverPolicies}

## <a name="next-steps"></a>Další postup

* Připojení pomocí .NET najdete v tématu [připojení a dotazování pomocí .NET](create-sql-api-dotnet.md).
* Připojení pomocí Node.js najdete v tématu [připojení a dotazování pomocí Node.js a MongoDB aplikace](create-mongodb-nodejs.md).

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[powershell-install-configure]: https://docs.microsoft.com/azure/powershell-install-configure
[scaling-globally]: distribute-data-globally.md#EnableGlobalDistribution
[distribute-data-globally]: distribute-data-globally.md
[azure-resource-groups]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups
[azure-resource-tags]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags
[rp-rest-api]: https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/

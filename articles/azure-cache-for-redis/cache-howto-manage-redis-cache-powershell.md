---
title: Spravovat Azure mezipaměti Redis pomocí Azure Powershellu | Dokumentace Microsoftu
description: Zjistěte, jak k provádění úloh správy pro Azure Cache pro Redis pomocí Azure Powershellu.
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 1136efe5-1e33-4d91-bb49-c8e2a6dca475
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: yegu
ms.openlocfilehash: f7f4f9ae6a80052e06b2cafa68cb5c11dfa1333a
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56233842"
---
# <a name="manage-azure-cache-for-redis-with-azure-powershell"></a>Spravovat Azure mezipaměti Redis pomocí Azure Powershellu
> [!div class="op_single_selector"]
> * [PowerShell](cache-howto-manage-redis-cache-powershell.md)
> * [Azure CLI](cache-manage-cli.md)
> 
> 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Toto téma ukazuje, jak provádět běžné úkoly jako je například vytvoření, aktualizace ale instance, jak znovu vygenerovat přístupové klíče a jak zobrazit informace o mezipaměti Azure Cache škálovat Redis. Úplný seznam mezipaměti Azure pro rutiny Powershellu pro redis Cache najdete v tématu [mezipaměti Azure Redis rutin](https://docs.microsoft.com/powershell/module/az.rediscache).

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

Další informace o modelu nasazení classic najdete v tématu [Azure Resource Manageru a klasického nasazení: Vysvětlení modelů nasazení a stavu prostředků](../azure-resource-manager/resource-manager-deployment-model.md).

## <a name="prerequisites"></a>Požadavky
Pokud jste již nainstalovali Azure PowerShell, musíte mít Azure PowerShell verze 1.0.0 nebo novějším. Můžete zkontrolovat verzi Azure Powershellu, který jste nainstalovali pomocí tohoto příkazu na příkazovém řádku prostředí Azure PowerShell.

    Get-Module Az | format-table version


Nejprve musíte se přihlásit do Azure pomocí tohoto příkazu.

    Connect-AzAccount

Zadejte e-mailovou adresu svého účtu Azure a jeho heslo v dialogovém okně přihlašovací Microsoft Azure.

Dále pokud máte více předplatných Azure, musíte nastavit vašeho předplatného Azure. Chcete-li zobrazit seznam aktuálních předplatných. Spusťte tento příkaz.

    Get-AzSubscription | sort SubscriptionName | Select SubscriptionName

Pokud chcete zadat předplatné, spusťte následující příkaz. V následujícím příkladu je název předplatného `ContosoSubscription`.

    Select-AzSubscription -SubscriptionName ContosoSubscription

Před použitím prostředí Windows PowerShell s Azure Resource Managerem, budete potřebovat následující:

* Windows PowerShell, verze 3.0 nebo 4.0. Chcete-li najít verzi prostředí Windows PowerShell, zadejte:`$PSVersionTable` a ověřte hodnotu `PSVersion` 3.0 nebo 4.0. Pokud chcete nainstalovat kompatibilní verze, najdete v článku [Windows Management Framework 3.0](https://www.microsoft.com/download/details.aspx?id=34595) nebo [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855).

Chcete-li získat podrobnou nápovědu pro všechny rutiny, které se zobrazí v tomto kurzu, použijte rutinu Get-Help.

    Get-Help <cmdlet-name> -Detailed

Například, chcete-li získat nápovědu pro `New-AzRedisCache` rutiny, typ:

    Get-Help New-AzRedisCache -Detailed

### <a name="how-to-connect-to-other-clouds"></a>Jak se připojit k jiné cloudy
Ve výchozím nastavení Azure je prostředí `AzureCloud`, která představuje instanci globální cloud Azure. Pro připojení k jiné instanci, použijte `Connect-AzAccount` příkazů `-Environment` nebo -`EnvironmentName` přepínač příkazového řádku s požadované prostředí nebo název prostředí.

Pokud chcete zobrazit seznam dostupných prostředí, spusťte `Get-AzEnvironment` rutiny.

### <a name="to-connect-to-the-azure-government-cloud"></a>K připojení ke cloudu Azure Government
Pokud chcete připojit ke cloudu Azure Government, použijte jednu z následujících příkazů.

    Connect-AzAccount -EnvironmentName AzureUSGovernment

nebo

    Connect-AzAccount -Environment (Get-AzEnvironment -Name AzureUSGovernment)

K vytvoření mezipaměti v cloudu Azure Government, použijte jednu z následujících umístění.

* USGov (Virginie)
* USGov (Iowa)

Další informace o cloudu Azure Government najdete v tématu [Microsoft Azure Government](https://azure.microsoft.com/features/gov/) a [příručce pro vývojáře k Microsoft Azure Government](../azure-government-developer-guide.md).

### <a name="to-connect-to-the-azure-china-cloud"></a>Pro připojení ke cloudu Azure Čína
Pro připojení k Azure China Cloud, použijte jednu z následujících příkazů.

    Connect-AzAccount -EnvironmentName AzureChinaCloud

nebo

    Connect-AzAccount -Environment (Get-AzEnvironment -Name AzureChinaCloud)

Pokud chcete vytvořit mezipaměť v Azure China Cloud, použijte jednu z následujících umístění.

* Čína – východ
* Čína – sever

Další informace o Azure China Cloud, naleznete v tématu [AzureChinaCloud pro Azure provozovaný společností 21Vianet v Číně](http://www.windowsazure.cn/).

### <a name="to-connect-to-microsoft-azure-germany"></a>Pro připojení k Microsoft Azure Germany
Pro připojení k Microsoft Azure Germany, použijte jednu z následujících příkazů.

    Connect-AzAccount -EnvironmentName AzureGermanCloud


nebo

    Connect-AzAccount -Environment (Get-AzEnvironment -Name AzureGermanCloud)

Pokud chcete vytvořit mezipaměť v Microsoft Azure Germany, použijte jednu z následujících umístění.

* Německo – střed
* Německo – severovýchod

Další informace o Microsoft Azure Germany najdete v tématu [Microsoft Azure Germany](https://azure.microsoft.com/overview/clouds/germany/).

### <a name="properties-used-for-azure-cache-for-redis-powershell"></a>Vlastnosti použitých pro mezipaměť Azure redis Cache prostředí PowerShell
Následující tabulka obsahuje vlastnosti a popisy pro běžně používané parametry při vytváření a Správa mezipaměti Azure Redis instance pomocí Azure Powershellu.

| Parametr | Popis | Výchozí |
| --- | --- | --- |
| Název |Název mezipaměti | |
| Umístění |Umístění mezipaměti | |
| ResourceGroupName |Název skupiny prostředků, ve kterém chcete vytvořit mezipaměť | |
| Velikost |Velikost mezipaměti. Platné hodnoty jsou: P1, P2, P3, P4, C0, C1, C2, C3, C4, C5, C6, 250MB, 1GB, 2,5 GB, 6 GB, 13 GB, 26 GB, 53 GB |1GB |
| ShardCount |Počet horizontálních oddílů k vytvoření při vytváření cache ve verzi premium s aktivovaným clusteringem. Platné hodnoty jsou: 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 | |
| Skladová jednotka (SKU) |Určuje skladovou Položku mezipaměti. Platné hodnoty jsou: Basic, Standard a Premium |Standard |
| RedisConfiguration |Určuje nastavení konfigurace Redis. Podrobnosti o každém nastavení, naleznete na následujícím [RedisConfiguration vlastnosti](#redisconfiguration-properties) tabulky. | |
| EnableNonSslPort |Určuje, zda je povoleno port bez SSL. |False |
| MaxMemoryPolicy |Tento parametr se již nepoužívá – místo toho použijte RedisConfiguration. | |
| StaticIP |Při hostování mezipaměti ve virtuální síti, určuje jedinečnou IP adresu v podsíti pro ukládání do mezipaměti. Pokud se nezadá, jeden je vybrán pro vás z podsítě. | |
| Podsíť |Při hostování mezipaměti ve virtuální síti, určuje název podsítě, ve které se má nasadit do mezipaměti. | |
| VirtualNetwork |Při hostování mezipaměti ve virtuální síti, určuje Identifikátor prostředku virtuální sítě, ve které se má nasadit do mezipaměti. | |
| KeyType |Určuje, které přístupový klíč se znovu vygenerovat při obnovování přístupových klíčů. Platné hodnoty jsou: Primární, sekundární | |

### <a name="redisconfiguration-properties"></a>Vlastnosti RedisConfiguration
| Vlastnost | Popis | Cenové úrovně |
| --- | --- | --- |
| povolené zálohování RDB |Zda [trvalost dat Redis](cache-how-to-premium-persistence.md) je povoleno |Jenom úrovně Premium |
| rdb-storage-connection-string |Připojovací řetězec pro účet úložiště pro [trvalost dat Redis](cache-how-to-premium-persistence.md) |Jenom úrovně Premium |
| rdb-backup-frequency |Četnost záloh pro [trvalost dat Redis](cache-how-to-premium-persistence.md) |Jenom úrovně Premium |
| maxmemory-reserved |Konfiguruje [paměti vyhrazená](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) pro procesy nesouvisející s mezipamětí |Standard a Premium |
| maxmemory-policy |Konfiguruje [zásadu vyřazení](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) pro ukládání do mezipaměti |Všechny cenové úrovně |
| notify-keyspace-events |Nakonfiguruje [oznámení keyspace](cache-configure.md#keyspace-notifications-advanced-settings) |Standard a Premium |
| hash-max-ziplist-entries |Nakonfiguruje [optimalizace paměti](https://redis.io/topics/memory-optimization) pro malé datové agregační typy |Standard a Premium |
| hash-max-ziplist-value |Nakonfiguruje [optimalizace paměti](https://redis.io/topics/memory-optimization) pro malé datové agregační typy |Standard a Premium |
| set-max-intset-entries |Nakonfiguruje [optimalizace paměti](https://redis.io/topics/memory-optimization) pro malé datové agregační typy |Standard a Premium |
| zset-max-ziplist-entries |Nakonfiguruje [optimalizace paměti](https://redis.io/topics/memory-optimization) pro malé datové agregační typy |Standard a Premium |
| zset-max-ziplist-value |Nakonfiguruje [optimalizace paměti](https://redis.io/topics/memory-optimization) pro malé datové agregační typy |Standard a Premium |
| databáze |Konfiguruje počet databází. Tuto vlastnost lze nastavit pouze při vytvoření mezipaměti. |Standard a Premium |

## <a name="to-create-an-azure-cache-for-redis"></a>Vytvoření Azure Cache pro Redis
Novou mezipaměť Azure Redis instance jsou vytvořeny pomocí [New-AzRedisCache](https://docs.microsoft.com/powershell/module/az.rediscache/new-azrediscache) rutiny.

> [!IMPORTANT]
> Při prvním vytvoření Azure Cache pro Redis v rámci předplatného pomocí webu Azure portal, na portálu zaregistruje `Microsoft.Cache` obor názvů pro toto předplatné. Při pokusu o vytvoření první mezipaměti Azure pro Redis v rámci předplatného pomocí Powershellu, musíte se nejprve zaregistrovat tento obor názvů pomocí následujícího příkazu; jinak rutin, jako `New-AzRedisCache` a `Get-AzRedisCache` nezdaří.
> 
> `Register-AzResourceProvider -ProviderNamespace "Microsoft.Cache"`
> 
> 

Pokud chcete zobrazit seznam dostupných parametrů a jejich popisy pro `New-AzRedisCache`, spusťte následující příkaz.

    PS C:\> Get-Help New-AzRedisCache -detailed

    NAME
        New-AzRedisCache

    SYNOPSIS
        Creates a new Azure Cache for Redis.


    SYNTAX
        New-AzRedisCache -Name <String> -ResourceGroupName <String> -Location <String> [-RedisVersion <String>]
        [-Size <String>] [-Sku <String>] [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort
        <Boolean>] [-ShardCount <Integer>] [-VirtualNetwork <String>] [-Subnet <String>] [-StaticIP <String>]
        [<CommonParameters>]


    DESCRIPTION
        The New-AzRedisCache cmdlet creates a new Azure Cache for Redis.


    PARAMETERS
        -Name <String>
            Name of the Azure Cache for Redis to create.

        -ResourceGroupName <String>
            Name of resource group in which to create the Azure Cache for Redis.

        -Location <String>
            Location in which to create the Azure Cache for Redis.

        -RedisVersion <String>
            RedisVersion is deprecated and will be removed in future release.

        -Size <String>
            Size of the Azure Cache for Redis. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
            C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.

        -Sku <String>
            Sku of Azure Cache for Redis. The default value is Standard. Possible values are Basic, Standard and Premium.

        -MaxMemoryPolicy <String>
            The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
            MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}

        -RedisConfiguration <Hashtable>
            All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
            rdb-backup-frequency, maxmemory-reserved, maxmemory-policy, notify-keyspace-events, hash-max-ziplist-entries,
            hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries, zset-max-ziplist-value, databases.

        -EnableNonSslPort <Boolean>
            EnableNonSslPort is used by Azure Cache for Redis. If no value is provided, the default value is false and the
            non-SSL port will be disabled. Possible values are true and false.

        -ShardCount <Integer>
            The number of shards to create on a Premium Cluster Cache.

        -VirtualNetwork <String>
            The exact ARM resource ID of the virtual network to deploy the Azure Cache for Redis in. Example format: /subscriptions/{
            subid}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicNetwork/VirtualNetworks/{vnetName}

        -Subnet <String>
            Required when deploying an Azure Cache for Redis inside an existing Azure Virtual Network.

        -StaticIP <String>
            Required when deploying an Azure Cache for Redis inside an existing Azure Virtual Network.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

Pokud chcete vytvořit mezipaměť s výchozími parametry, spusťte následující příkaz.

    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US"

`ResourceGroupName`, `Name`, a `Location` parametry jsou povinné, ale ostatní jsou volitelná a mají výchozí hodnoty. Předchozí příkaz vytvoří standardní Azure Cache SKU pro instanci Redis s zadaný název, umístění a zdrojů skupině, který je 1 GB ve velikosti port bez SSL, který je zakázaný.

Chcete-li vytvořit mezipaměť premium, určení velikosti P1 (6 GB – 60 GB), P2 (13 GB 130 GB), P3 (26 GB – 260 GB), nebo P4 (53 GB 530 GB). Pokud chcete povolit, clustering, zadejte počet horizontálních oddílů pomocí `ShardCount` parametru. Následující příklad vytvoří mezipaměť premium P1 s 3 horizontálních oddílů. Mezipaměť premium P1 6 GB velikost, a protože jsme zadali tři horizontální oddíly celková velikost je 18 GB (3 × 6 GB).

    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P1 -ShardCount 3

Můžete zadat hodnoty pro `RedisConfiguration` parametr, uzavřete hodnoty uvnitř `{}` jako klíč/hodnota, jako jsou páry `@{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}`. Následující příklad vytvoří standardní 1 GB mezipaměti s `allkeys-random` maxmemory zásad a keyspace oznámení nakonfigurované `KEA`. Další informace najdete v tématu [oznámení Keyspace (rozšířené nastavení)](cache-configure.md#keyspace-notifications-advanced-settings) a [paměti zásady](cache-configure.md#memory-policies).

    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}

<a name="databases"></a>

## <a name="to-configure-the-databases-setting-during-cache-creation"></a>Chcete-li konfigurovat nastavení během vytváření mezipaměti databáze
`databases` Nastavení se dá nakonfigurovat jenom během vytváření mezipaměti. Následující příklad vytvoří na úrovni premium P3 (26 GB) mezipaměti 48 s databázemi s využitím [New-AzRedisCache](https://docs.microsoft.com/powershell/module/az.rediscache/New-azRedisCache) rutiny.

    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P3 -RedisConfiguration @{"databases" = "48"}

Další informace o `databases` vlastnost, naleznete v tématu [výchozí mezipaměti Azure pro konfiguraci serveru Redis](cache-configure.md#default-redis-server-configuration). Další informace o vytváření potřebnou mezipaměť [AzRedisCache nový](https://docs.microsoft.com/powershell/module/az.rediscache/new-azrediscache) rutiny, naleznete v předchozím vytvoření Azure Cache pro oddíl Redis.

## <a name="to-update-an-azure-cache-for-redis"></a>Aktualizace pro Redis Azure Cache
Mezipaměť Azure Redis instance se aktualizují pomocí [Set-AzRedisCache](https://docs.microsoft.com/powershell/module/az.rediscache/Set-azRedisCache) rutiny.

Pokud chcete zobrazit seznam dostupných parametrů a jejich popisy pro `Set-AzRedisCache`, spusťte následující příkaz.

    PS C:\> Get-Help Set-AzRedisCache -detailed

    NAME
        Set-AzRedisCache

    SYNOPSIS
        Set Azure Cache for Redis updatable parameters.

    SYNTAX
        Set-AzRedisCache -Name <String> -ResourceGroupName <String> [-Size <String>] [-Sku <String>]
        [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort <Boolean>] [-ShardCount
        <Integer>] [<CommonParameters>]

    DESCRIPTION
        The Set-AzRedisCache cmdlet sets Azure Cache for Redis parameters.

    PARAMETERS
        -Name <String>
            Name of the Azure Cache for Redis to update.

        -ResourceGroupName <String>
            Name of the resource group for the cache.

        -Size <String>
            Size of the Azure Cache for Redis. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
            C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.

        -Sku <String>
            Sku of Azure Cache for Redis. The default value is Standard. Possible values are Basic, Standard and Premium.

        -MaxMemoryPolicy <String>
            The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
            MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}

        -RedisConfiguration <Hashtable>
            All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
            rdb-backup-frequency, maxmemory-reserved, maxmemory-policy, notify-keyspace-events, hash-max-ziplist-entries,
            hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries, zset-max-ziplist-value.

        -EnableNonSslPort <Boolean>
            EnableNonSslPort is used by Azure Cache for Redis. The default value is null and no change will be made to the
            currently configured value. Possible values are true and false.

        -ShardCount <Integer>
            The number of shards to create on a Premium Cluster Cache.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

`Set-AzRedisCache` Rutina slouží k aktualizaci vlastností, jako `Size`, `Sku`, `EnableNonSslPort`a `RedisConfiguration` hodnoty. 

Následující příkaz aktualizace pro Redis s názvem myCache zásady maxmemory. pro službu Azure Cache.

    Set-AzRedisCache -ResourceGroupName "myGroup" -Name "myCache" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random"}

<a name="scale"></a>

## <a name="to-scale-an-azure-cache-for-redis"></a>Škálování pro Redis Azure Cache
`Set-AzRedisCache` je možné škálovat mezipaměti Azure Redis instance, kdy `Size`, `Sku`, nebo `ShardCount` jsou upraveny vlastnosti Description. 

> [!NOTE]
> Škálování mezipaměti pomocí prostředí PowerShell je v souladu s stejné omezení a pokyny jako horizontální mezipaměť na webu Azure Portal. Je možné škálovat na jinou cenovou úroveň s následujícími omezeními.
> 
> * Nejde škálovat z vyšší cenovou úroveň na nižší cenovou úroveň.
> * Nejde škálovat z **Premium** dolů na do mezipaměti **standardní** nebo **základní** mezipaměti.
> * Nejde škálovat z **standardní** dolů na do mezipaměti **základní** mezipaměti.
> * Je možné škálovat od **základní** mezipaměti tak, aby **standardní** mezipaměti, ale nemůže změnit velikost ve stejnou dobu. Pokud potřebujete jinou velikost, můžete provést následující operaci škálování na požadovanou velikost.
> * Nejde škálovat z **základní** přímo do mezipaměti **Premium** mezipaměti. Je třeba navyšovat kapacitu z **základní** k **standardní** v rámci jedné operace škálování a pak **standardní** k **Premium** následné škálování operace.
> * Nejde škálovat z větší velikost dolů na **C0 (250 MB)** velikost.
> 
> Další informace najdete v tématu [postup škálování Azure mezipaměti Redis](cache-how-to-scale.md).
> 
> 

Následující příklad ukazuje, jak na škálování mezipaměti s názvem `myCache` 2,5 GB mezipaměti. Všimněte si, že tento příkaz funguje pro základní nebo standardní mezipaměti.

    Set-AzRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB

Po vydání tohoto příkazu se vrátí stav mezipaměti (podobně jako volání `Get-AzRedisCache`). Všimněte si, `ProvisioningState` je `Scaling`.

    PS C:\> Set-AzRedisCache -Name myCache -ResourceGroupName myGroup -Size 2.5GB


    Name               : mycache
    Id                 : /subscriptions/12ad12bd-abdc-2231-a2ed-a2b8b246bbad4/resourceGroups/mygroup/providers/Mi
                         crosoft.Cache/Redis/mycache
    Location           : South Central US
    Type               : Microsoft.Cache/Redis
    HostName           : mycache.redis.cache.windows.net
    Port               : 6379
    ProvisioningState  : Scaling
    SslPort            : 6380
    RedisConfiguration : {[maxmemory-policy, volatile-lru], [maxmemory-reserved, 150], [notify-keyspace-events, KEA],
                         [maxmemory-delta, 150]...}
    EnableNonSslPort   : False
    RedisVersion       : 3.0
    Size               : 1GB
    Sku                : Standard
    ResourceGroupName  : mygroup
    PrimaryKey         : ....
    SecondaryKey       : ....
    VirtualNetwork     :
    Subnet             :
    StaticIP           :
    TenantSettings     : {}
    ShardCount         :

Po dokončení operace škálování `ProvisioningState` změny `Succeeded`. Pokud potřebujete provést následné škálování operace, jako je například změna z úrovně Basic na úroveň Standard a potom změny velikosti, musíte počkat, dokud dokončení předchozí operace nebo zobrazí chybová zpráva podobná následující.

    Set-AzRedisCache : Conflict: The resource '...' is not in a stable state, and is currently unable to accept the update request.

## <a name="to-get-information-about-an-azure-cache-for-redis"></a>Chcete-li získat informace o Azure Cache pro Redis
Můžete načíst informace o potřebnou mezipaměť [Get-AzRedisCache](https://docs.microsoft.com/powershell/module/az.rediscache/get-azrediscache) rutiny.

Pokud chcete zobrazit seznam dostupných parametrů a jejich popisy pro `Get-AzRedisCache`, spusťte následující příkaz.

    PS C:\> Get-Help Get-AzRedisCache -detailed

    NAME
        Get-AzRedisCache

    SYNOPSIS
        Gets details about a single cache or all caches in the specified resource group or all caches in the current
        subscription.

    SYNTAX
        Get-AzRedisCache [-Name <String>] [-ResourceGroupName <String>] [<CommonParameters>]

    DESCRIPTION
        The Get-AzRedisCache cmdlet gets the details about a cache or caches depending on input parameters. If both
        ResourceGroupName and Name parameters are provided then Get-AzRedisCache will return details about the
        specific cache name provided.

        If only ResourceGroupName is provided than it will return details about all caches in the specified resource group.

        If no parameters are given than it will return details about all caches the current subscription.

    PARAMETERS
        -Name <String>
            The name of the cache. When this parameter is provided along with ResourceGroupName, Get-AzRedisCache
            returns the details for the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache or caches. If ResourceGroupName is provided with Name
            then Get-AzRedisCache returns the details of the cache specified by Name. If only the ResourceGroup
            parameter is provided, then details for all caches in the resource group are returned.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

Chcete-li vrátit informace o všech mezipamětí v aktuálním předplatném, spusťte `Get-AzRedisCache` bez parametrů.

    Get-AzRedisCache

Chcete-li vrátit informace o všech mezipamětí v konkrétní skupině prostředků, spusťte `Get-AzRedisCache` s `ResourceGroupName` parametru.

    Get-AzRedisCache -ResourceGroupName myGroup

K vrácení informací o určité mezipaměti, spuštění `Get-AzRedisCache` s `Name` parametr, který obsahuje název mezipaměti a `ResourceGroupName` parametr s vybranou skupinou prostředků obsahující tuto mezipaměť.

    PS C:\> Get-AzRedisCache -Name myCache -ResourceGroupName myGroup

    Name               : mycache
    Id                 : /subscriptions/12ad12bd-abdc-2231-a2ed-a2b8b246bbad4/resourceGroups/myGroup/providers/Mi
                         crosoft.Cache/Redis/mycache
    Location           : South Central US
    Type               : Microsoft.Cache/Redis
    HostName           : mycache.redis.cache.windows.net
    Port               : 6379
    ProvisioningState  : Succeeded
    SslPort            : 6380
    RedisConfiguration : {[maxmemory-policy, volatile-lru], [maxmemory-reserved, 62], [notify-keyspace-events, KEA],
                         [maxclients, 1000]...}
    EnableNonSslPort   : False
    RedisVersion       : 3.0
    Size               : 1GB
    Sku                : Standard
    ResourceGroupName  : myGroup
    VirtualNetwork     :
    Subnet             :
    StaticIP           :
    TenantSettings     : {}
    ShardCount         :

## <a name="to-retrieve-the-access-keys-for-an-azure-cache-for-redis"></a>Chcete-li získat přístupové klíče pro Azure Cache pro Redis
Chcete-li získat přístupové klíče ke svojí mezipaměti, můžete použít [Get-AzRedisCacheKey](https://docs.microsoft.com/powershell/module/az.rediscache/Get-azRedisCacheKey) rutiny.

Pokud chcete zobrazit seznam dostupných parametrů a jejich popisy pro `Get-AzRedisCacheKey`, spusťte následující příkaz.

    PS C:\> Get-Help Get-AzRedisCacheKey -detailed

    NAME
        Get-AzRedisCacheKey

    SYNOPSIS
        Gets the accesskeys for the specified Azure Cache for Redis.


    SYNTAX
        Get-AzRedisCacheKey -Name <String> -ResourceGroupName <String> [<CommonParameters>]

    DESCRIPTION
        The Get-AzRedisCacheKey cmdlet gets the access keys for the specified cache.

    PARAMETERS
        -Name <String>
            Name of the Azure Cache for Redis.

        -ResourceGroupName <String>
            Name of the resource group for the cache.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

Chcete-li získat klíče pro mezipaměť, zavolejte `Get-AzRedisCacheKey` rutiny a předejte mu název mezipaměti název skupiny prostředků, která obsahuje mezipaměti.

    PS C:\> Get-AzRedisCacheKey -Name myCache -ResourceGroupName myGroup

    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : ABhfB757JgjIgt785JgKH9865eifmekfnn649303JKL=

## <a name="to-regenerate-access-keys-for-your-azure-cache-for-redis"></a>Chcete-li znovu vygenerovat přístupové klíče pro mezipaměť Azure Redis
Chcete-li znovu vygenerovat přístupové klíče ke svojí mezipaměti, můžete použít [New-AzRedisCacheKey](https://docs.microsoft.com/powershell/module/az.rediscache/New-azRedisCacheKey) rutiny.

Pokud chcete zobrazit seznam dostupných parametrů a jejich popisy pro `New-AzRedisCacheKey`, spusťte následující příkaz.

    PS C:\> Get-Help New-AzRedisCacheKey -detailed

    NAME
        New-AzRedisCacheKey

    SYNOPSIS
        Regenerates the access key of an Azure Cache for Redis.

    SYNTAX
        New-AzRedisCacheKey -Name <String> -ResourceGroupName <String> -KeyType <String> [-Force] [<CommonParameters>]

    DESCRIPTION
        The New-AzRedisCacheKey cmdlet regenerate the access key of an Azure Cache for Redis.

    PARAMETERS
        -Name <String>
            Name of the Azure Cache for Redis.

        -ResourceGroupName <String>
            Name of the resource group for the cache.

        -KeyType <String>
            Specifies whether to regenerate the primary or secondary access key. Possible values are Primary or Secondary.

        -Force
            When the Force parameter is provided, the specified access key is regenerated without any confirmation prompts.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

Chcete-li znovu vygenerovat primární nebo sekundární klíč pro mezipaměť, zavolejte `New-AzRedisCacheKey` rutiny a předejte název skupiny prostředků a zadat buď `Primary` nebo `Secondary` pro `KeyType` parametr. V následujícím příkladu je znovu vygenerovat sekundární přístupový klíč pro mezipaměť.

    PS C:\> New-AzRedisCacheKey -Name myCache -ResourceGroupName myGroup -KeyType Secondary

    Confirm
    Are you sure you want to regenerate Secondary key for Azure Cache for Redis 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : c53hj3kh4jhHjPJk8l0jji785JgKH9865eifmekfnn6=

## <a name="to-delete-an-azure-cache-for-redis"></a>Chcete-li odstranit Azure Cache pro Redis
Chcete-li odstranit Azure Cache pro Redis, použijte [odebrat AzRedisCache](https://docs.microsoft.com/powershell/module/az.rediscache/remove-azrediscache) rutiny.

Pokud chcete zobrazit seznam dostupných parametrů a jejich popisy pro `Remove-AzRedisCache`, spusťte následující příkaz.

    PS C:\> Get-Help Remove-AzRedisCache -detailed

    NAME
        Remove-AzRedisCache

    SYNOPSIS
        Remove Azure Cache for Redis if exists.

    SYNTAX
        Remove-AzRedisCache -Name <String> -ResourceGroupName <String> [-Force] [-PassThru] [<CommonParameters>

    DESCRIPTION
        The Remove-AzRedisCache cmdlet removes an Azure Cache for Redis if it exists.

    PARAMETERS
        -Name <String>
            Name of the Azure Cache for Redis to remove.

        -ResourceGroupName <String>
            Name of the resource group of the cache to remove.

        -Force
            When the Force parameter is provided, the cache is removed without any confirmation prompts.

        -PassThru
            By default Remove-AzRedisCache removes the cache and does not return any value. If the PassThru par
            is provided then Remove-AzRedisCache returns a boolean value indicating the success of the operatio

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

V následujícím příkladu, s názvem mezipaměti `myCache` se odebere.

    PS C:\> Remove-AzRedisCache -Name myCache -ResourceGroupName myGroup

    Confirm
    Are you sure you want to remove Azure Cache for Redis 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


## <a name="to-import-an-azure-cache-for-redis"></a>Chcete-li importovat Azure Cache pro Redis
Můžete importovat data do Azure Cache pro instanci Redis pomocí `Import-AzRedisCache` rutiny.

> [!IMPORTANT]
> Import/Export je dostupná jenom pro [úroveň premium](cache-premium-tier-intro.md) ukládá do mezipaměti. Další informace o importu a exportu, naleznete v tématu [importovat a exportovat data v mezipaměti Azure Redis](cache-how-to-import-export-data.md).
> 
> 

Pokud chcete zobrazit seznam dostupných parametrů a jejich popisy pro `Import-AzRedisCache`, spusťte následující příkaz.

    PS C:\> Get-Help Import-AzRedisCache -detailed

    NAME
        Import-AzRedisCache

    SYNOPSIS
        Import data from blobs to Azure Cache for Redis.


    SYNTAX
        Import-AzRedisCache -Name <String> -ResourceGroupName <String> -Files <String[]> [-Format <String>] [-Force]
        [-PassThru] [<CommonParameters>]


    DESCRIPTION
        The Import-AzRedisCache cmdlet imports data from the specified blobs into Azure Cache for Redis.


    PARAMETERS
        -Name <String>
            The name of the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache.

        -Files <String[]>
            SAS urls of blobs whose content should be imported into the cache.

        -Format <String>
            Format for the blob.  Currently "rdb" is the only supported, with other formats expected in the future.

        -Force
            When the Force parameter is provided, import will be performed without any confirmation prompts.

        -PassThru
            By default Import-AzRedisCache imports data in cache and does not return any value. If the PassThru
            parameter is provided then Import-AzRedisCache returns a boolean value indicating the success of the
            operation.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).


Následující příkaz importuje data z objektu blob určený identifikátor uri SAS do mezipaměti Azure pro Redis.

    PS C:\>Import-AzRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Files @("https://mystorageaccount.blob.core.windows.net/mycontainername/blobname?sv=2015-04-05&sr=b&sig=caIwutG2uDa0NZ8mjdNJdgOY8%2F8mhwRuGNdICU%2B0pI4%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwd") -Force

## <a name="to-export-an-azure-cache-for-redis"></a>Chcete-li exportovat Azure Cache pro Redis
Data můžete exportovat z Azure Cache pro instanci Redis pomocí `Export-AzRedisCache` rutiny.

> [!IMPORTANT]
> Import/Export je dostupná jenom pro [úroveň premium](cache-premium-tier-intro.md) ukládá do mezipaměti. Další informace o importu a exportu, naleznete v tématu [importovat a exportovat data v mezipaměti Azure Redis](cache-how-to-import-export-data.md).
> 
> 

Pokud chcete zobrazit seznam dostupných parametrů a jejich popisy pro `Export-AzRedisCache`, spusťte následující příkaz.

    PS C:\> Get-Help Export-AzRedisCache -detailed

    NAME
        Export-AzRedisCache

    SYNOPSIS
        Exports data from Azure Cache for Redis to a specified container.


    SYNTAX
        Export-AzRedisCache -Name <String> -ResourceGroupName <String> -Prefix <String> -Container <String> [-Format
        <String>] [-PassThru] [<CommonParameters>]


    DESCRIPTION
        The Export-AzRedisCache cmdlet exports data from Azure Cache for Redis to a specified container.


    PARAMETERS
        -Name <String>
            The name of the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache.

        -Prefix <String>
            Prefix to use for blob names.

        -Container <String>
            SAS url of container where data should be exported.

        -Format <String>
            Format for the blob.  Currently "rdb" is the only supported, with other formats expected in the future.

        -PassThru
            By default Export-AzRedisCache does not return any value. If the PassThru parameter is provided
            then Export-AzRedisCache returns a boolean value indicating the success of the operation.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).


Následující příkaz exportuje data z Azure Cache pro instanci Redis do kontejneru určený identifikátor uri SAS.

        PS C:\>Export-AzRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Prefix "blobprefix"
        -Container "https://mystorageaccount.blob.core.windows.net/mycontainer?sv=2015-04-05&sr=c&sig=HezZtBZ3DURmEGDduauE7
        pvETY4kqlPI8JCNa8ATmaw%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwdl"

## <a name="to-reboot-an-azure-cache-for-redis"></a>Až po restartování počítače mezipaměti Azure Redis
Restartujete mezipaměti Azure pro instanci Redis pomocí `Reset-AzRedisCache` rutiny.

> [!IMPORTANT]
> Restart je dostupná jenom pro [úroveň premium](cache-premium-tier-intro.md) ukládá do mezipaměti. Další informace o restartování vaší mezipaměti, naleznete v tématu [mezipaměti správy – restartování](cache-administration.md#reboot).
> 
> 

Pokud chcete zobrazit seznam dostupných parametrů a jejich popisy pro `Reset-AzRedisCache`, spusťte následující příkaz.

    PS C:\> Get-Help Reset-AzRedisCache -detailed

    NAME
        Reset-AzRedisCache

    SYNOPSIS
        Reboot specified node(s) of an Azure Cache for Redis instance.


    SYNTAX
        Reset-AzRedisCache -Name <String> -ResourceGroupName <String> -RebootType <String> [-ShardId <Integer>]
        [-Force] [-PassThru] [<CommonParameters>]


    DESCRIPTION
        The Reset-AzRedisCache cmdlet reboots the specified node(s) of an Azure Cache for Redis instance.


    PARAMETERS
        -Name <String>
            The name of the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache.

        -RebootType <String>
            Which node to reboot. Possible values are "PrimaryNode", "SecondaryNode", "AllNodes".

        -ShardId <Integer>
            Which shard to reboot when rebooting a premium cache with clustering enabled.

        -Force
            When the Force parameter is provided, reset will be performed without any confirmation prompts.

        -PassThru
            By default Reset-AzRedisCache does not return any value. If the PassThru parameter is provided
            then Reset-AzRedisCache returns a boolean value indicating the success of the operation.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).


Následující příkaz restartuje oba uzly zadané mezipaměti.

        PS C:\>Reset-AzRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -RebootType "AllNodes"
        -Force


## <a name="next-steps"></a>Další postup
Další informace o použití prostředí Windows PowerShell s Azure, najdete v následujících odkazech:

* [Mezipaměť Azure Redis rutiny dokumentaci na webu MSDN](https://docs.microsoft.com/powershell/module/az.rediscache)
* [Rutiny Azure Resource Manageru](https://go.microsoft.com/fwlink/?LinkID=394765): Zjistěte, jak pomocí rutin v modulu Azure Resource Manageru.
* [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/resource-group-template-deploy-portal.md): Zjistěte, jak vytvářet a spravovat skupiny prostředků na webu Azure Portal.
* [Blog o Azure](https://azure.microsoft.com/blog/): Informace o nových funkcích v Azure.
* [Blog o Windows Powershellu](https://blogs.msdn.com/powershell): Informace o nových funkcích v prostředí Windows PowerShell.
* ["Hey, Scripting Guy!" Blog](https://blogs.technet.com/b/heyscriptingguy/): Získejte skutečné tipy a triky od komunity prostředí Windows PowerShell.


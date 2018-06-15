---
title: Spravovat mezipaměť Redis systému Azure pomocí Azure Powershellu | Microsoft Docs
description: Zjistěte, jak k provádění úloh správy pro Azure Redis Cache pomocí Azure PowerShell.
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 1136efe5-1e33-4d91-bb49-c8e2a6dca475
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: wesmc
ms.openlocfilehash: 38b2f57811b0e952d3020c06d39350918f2f0391
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
ms.locfileid: "31599318"
---
# <a name="manage-azure-redis-cache-with-azure-powershell"></a>Spravovat mezipaměť Redis systému Azure pomocí Azure Powershellu
> [!div class="op_single_selector"]
> * [PowerShell](cache-howto-manage-redis-cache-powershell.md)
> * [Azure CLI](cache-manage-cli.md)
> 
> 

Toto téma ukazuje, jak provádět běžné úkoly, jako vytvořit, aktualizujete a škálovat vaše instance služby Azure Redis Cache, postup opětovné vygenerování přístupových klíčů a k zobrazení informací o své mezipaměti. Úplný seznam rutin Powershellu pro Azure Redis Cache najdete v tématu [rutiny Azure Redis Cache](https://msdn.microsoft.com/library/azure/mt634513.aspx).

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

Další informace o modelu nasazení classic najdete v tématu [Azure Resource Manager oproti nasazení classic: pochopení modely nasazení a stav svých prostředků](../azure-resource-manager/resource-manager-deployment-model.md).

## <a name="prerequisites"></a>Požadavky
Pokud jste již Azure PowerShell nainstalovali, musíte mít prostředí Azure PowerShell verze 1.0.0 nebo později. Můžete zkontrolovat verzi prostředí Azure PowerShell, který jste nainstalovali pomocí tohoto příkazu na příkazovém řádku prostředí Azure PowerShell.

    Get-Module azure | format-table version


Nejdřív musíte být přihlášení do Azure pomocí tohoto příkazu.

    Connect-AzureRmAccount

Zadejte e-mailovou adresu účtu Azure a jeho heslo v dialogovém okně sign-in Microsoft Azure.

Dále pokud máte víc předplatných Azure, budete muset nastavit vašeho předplatného Azure. Chcete-li zobrazit seznam aktuální předplatných, spusťte tento příkaz.

    Get-AzureRmSubscription | sort SubscriptionName | Select SubscriptionName

Pro určení předplatného, spusťte následující příkaz. V následujícím příkladu je název odběru `ContosoSubscription`.

    Select-AzureRmSubscription -SubscriptionName ContosoSubscription

Než budete moct použít prostředí Windows PowerShell s Azure Resource Managerem, budete potřebovat následující:

* Prostředí Windows PowerShell, verze 3.0 nebo 4.0. Chcete-li najít verzi prostředí Windows PowerShell, zadejte:`$PSVersionTable` a ověřte hodnotu `PSVersion` je 3.0 nebo 4.0. K instalaci kompatibilní verze, najdete v části [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) nebo [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).

Podrobnou nápovědu pro všechny rutiny, které se zobrazí v tomto kurzu získáte pomocí rutiny Get-Help.

    Get-Help <cmdlet-name> -Detailed

Například pro získání nápovědy pro `New-AzureRmRedisCache` rutiny, zadejte:

    Get-Help New-AzureRmRedisCache -Detailed

### <a name="how-to-connect-to-other-clouds"></a>Jak se připojit k ostatních cloudů
Ve výchozím nastavení Azure je prostředí `AzureCloud`, který představuje instanci globální cloudu Azure. Chcete-li se připojit k jiné instanci, použijte `Connect-AzureRmAccount` s `-Environment` nebo -`EnvironmentName` přepínač příkazového řádku s názvem prostředí nebo požadované prostředí.

Chcete-li zobrazit seznam dostupných prostředí, spusťte `Get-AzureRmEnvironment` rutiny.

### <a name="to-connect-to-the-azure-government-cloud"></a>Pro připojení k Azure Government cloudu
Pokud chcete připojit ke cloudu Azure Government, použijte jednu z následujících příkazů.

    Connect-AzureRmAccount -EnvironmentName AzureUSGovernment

nebo

    Connect-AzureRmAccount -Environment (Get-AzureRmEnvironment -Name AzureUSGovernment)

K vytvoření mezipaměti se v cloudu Azure Government, použijte jednu z následujících umístění.

* USGov (Virginie)
* USGov (Iowa)

Další informace o Cloud vlády Azure najdete v tématu [Microsoft Azure Government](https://azure.microsoft.com/features/gov/) a [Průvodce pro vývojáře k Microsoft Azure Government](../azure-government-developer-guide.md).

### <a name="to-connect-to-the-azure-china-cloud"></a>Pro připojení k Azure Cloud Čína
Pro připojení k Azure Cloud Čína, použijte jednu z následujících příkazů.

    Connect-AzureRmAccount -EnvironmentName AzureChinaCloud

nebo

    Connect-AzureRmAccount -Environment (Get-AzureRmEnvironment -Name AzureChinaCloud)

K vytvoření mezipaměti se v Číně cloudu Azure, použijte jednu z následujících umístění.

* Čína – východ
* Čína – sever

Další informace o cloudu Číně Azure najdete v tématu [AzureChinaCloud pro Azure provozované v Číně společností 21Vianet](http://www.windowsazure.cn/).

### <a name="to-connect-to-microsoft-azure-germany"></a>Pro připojení k Microsoft Azure v Německu
Pro připojení k Microsoft Azure v Německu, použijte jednu z následujících příkazů.

    Connect-AzureRmAccount -EnvironmentName AzureGermanCloud


nebo

    Connect-AzureRmAccount -Environment (Get-AzureRmEnvironment -Name AzureGermanCloud)

K vytvoření mezipaměti se v Microsoft Azure v Německu, použijte jednu z následujících umístění.

* Německo – střed
* Německo – severovýchod

Další informace o Microsoft Azure v Německu najdete v tématu [Microsoft Azure v Německu](https://azure.microsoft.com/overview/clouds/germany/).

### <a name="properties-used-for-azure-redis-cache-powershell"></a>Vlastnosti používané pro Azure Redis Cache prostředí PowerShell
Následující tabulka obsahuje vlastnosti a popisy pro běžně používané parametry při vytváření a správě vaší instance služby Azure Redis Cache pomocí Azure PowerShell.

| Parametr | Popis | Výchozí |
| --- | --- | --- |
| Název |Název mezipaměti | |
| Umístění |Umístění mezipaměti | |
| ResourceGroupName |Název skupiny prostředků, ve kterém k vytvoření mezipaměti | |
| Velikost |Velikost mezipaměti. Platné hodnoty jsou: P1, P2, P3, P4, C0, C1, C2, C3, C4, C5, C6, 250MB, 1GB, 2,5 GB, 6 GB, 13 GB, 26 GB, 53 GB |1GB |
| ShardCount |Počet horizontálních oddílů vytvořit při vytváření cache ve verzi premium s povoleným clusteringem. Platné hodnoty jsou: 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 | |
| Skladová jednotka (SKU) |Určuje skladová položka mezipaměti. Platné hodnoty jsou: Basic, Standard a Premium |Standard |
| RedisConfiguration |Určuje nastavení konfigurace Redis. Podrobnosti o jednotlivých nastaveních najdete v tématu následující [RedisConfiguration vlastnosti](#redisconfiguration-properties) tabulky. | |
| EnableNonSslPort |Určuje, zda je povoleno port bez SSL. |False |
| MaxMemoryPolicy |Tento parametr se již nepoužívá – místo toho použijte RedisConfiguration. | |
| StaticIP |Při hostování vaší mezipaměti ve virtuální síti, určuje jedinečnou IP adresu v mezipaměti v podsíti. Pokud není zadaná, jeden z podsítě vybrali za vás. | |
| Podsíť |Při hostování vaší mezipaměti ve virtuální síti, určuje název podsítě, ve které chcete nasadit do mezipaměti. | |
| VirtualNetwork |Při hostování vaší mezipaměti ve virtuální síti, určuje ID prostředku sítě vnet, ve které chcete nasadit do mezipaměti. | |
| Typ_klíče. |Určuje, které přístupový klíč se znovu vygenerovat při obnovování přístupové klíče. Platné hodnoty jsou: primární, sekundární | |

### <a name="redisconfiguration-properties"></a>Vlastnosti RedisConfiguration
| Vlastnost | Popis | Cenové úrovně |
| --- | --- | --- |
| Povolit zálohování RDB |Jestli [trvalosti dat Redis](cache-how-to-premium-persistence.md) je povoleno |Pouze Premium |
| RDB úložiště připojovacího řetězce |Připojovací řetězec k účtu úložiště pro [trvalosti dat Redis](cache-how-to-premium-persistence.md) |Pouze Premium |
| četnost záloh RDB |Četnost záloh pro [trvalosti dat Redis](cache-how-to-premium-persistence.md) |Pouze Premium |
| vyhrazené maxmemory |Nakonfiguruje [paměti vyhrazené](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) pro procesy bez ukládání do mezipaměti |Standard a Premium |
| maxmemory zásady |Nakonfiguruje [zásady vyřazení](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) mezipaměti |Všechny cenové úrovně |
| oznámení události keyspace |Nakonfiguruje [oznámení keyspace](cache-configure.md#keyspace-notifications-advanced-settings) |Standard a Premium |
| Hodnota hash-max-ziplist – položky |Nakonfiguruje [optimalizace paměti](http://redis.io/topics/memory-optimization) pro malé agregační datové typy |Standard a Premium |
| max-ziplist hodnota hash |Nakonfiguruje [optimalizace paměti](http://redis.io/topics/memory-optimization) pro malé agregační datové typy |Standard a Premium |
| set-max-intset – položky |Nakonfiguruje [optimalizace paměti](http://redis.io/topics/memory-optimization) pro malé agregační datové typy |Standard a Premium |
| zset-max-ziplist – položky |Nakonfiguruje [optimalizace paměti](http://redis.io/topics/memory-optimization) pro malé agregační datové typy |Standard a Premium |
| zset-max-ziplist-value |Nakonfiguruje [optimalizace paměti](http://redis.io/topics/memory-optimization) pro malé agregační datové typy |Standard a Premium |
| databáze |Konfiguruje počet databází. Tuto vlastnost lze nastavit pouze při vytváření mezipaměti. |Standard a Premium |

## <a name="to-create-a-redis-cache"></a>K vytvoření mezipaměti Redis
Nové instance služby Azure Redis Cache lze vytvořit pomocí [New-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634517.aspx) rutiny.

> [!IMPORTANT]
> Při prvním vytvoření mezipaměti Redis v předplatném pomocí portálu Azure portálu zaregistruje `Microsoft.Cache` obor názvů pro toto předplatné. Pokud se pokusíte vytvořit první mezipaměti Redis v předplatném pomocí prostředí PowerShell, je nutné nejprve zaregistrovat tento obor názvů pomocí následujícího příkazu; jinak rutin, jako `New-AzureRmRedisCache` a `Get-AzureRmRedisCache` nezdaří.
> 
> `Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Cache"`
> 
> 

Chcete-li zobrazit seznam dostupných parametrů a jejich popisy pro `New-AzureRmRedisCache`, spusťte následující příkaz.

    PS C:\> Get-Help New-AzureRmRedisCache -detailed

    NAME
        New-AzureRmRedisCache

    SYNOPSIS
        Creates a new redis cache.


    SYNTAX
        New-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -Location <String> [-RedisVersion <String>]
        [-Size <String>] [-Sku <String>] [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort
        <Boolean>] [-ShardCount <Integer>] [-VirtualNetwork <String>] [-Subnet <String>] [-StaticIP <String>]
        [<CommonParameters>]


    DESCRIPTION
        The New-AzureRmRedisCache cmdlet creates a new redis cache.


    PARAMETERS
        -Name <String>
            Name of the redis cache to create.

        -ResourceGroupName <String>
            Name of resource group in which to create the redis cache.

        -Location <String>
            Location in which to create the redis cache.

        -RedisVersion <String>
            RedisVersion is deprecated and will be removed in future release.

        -Size <String>
            Size of the redis cache. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
            C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.

        -Sku <String>
            Sku of redis cache. The default value is Standard. Possible values are Basic, Standard and Premium.

        -MaxMemoryPolicy <String>
            The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
            MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}

        -RedisConfiguration <Hashtable>
            All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
            rdb-backup-frequency, maxmemory-reserved, maxmemory-policy, notify-keyspace-events, hash-max-ziplist-entries,
            hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries, zset-max-ziplist-value, databases.

        -EnableNonSslPort <Boolean>
            EnableNonSslPort is used by Azure Redis Cache. If no value is provided, the default value is false and the
            non-SSL port will be disabled. Possible values are true and false.

        -ShardCount <Integer>
            The number of shards to create on a Premium Cluster Cache.

        -VirtualNetwork <String>
            The exact ARM resource ID of the virtual network to deploy the redis cache in. Example format: /subscriptions/{
            subid}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicNetwork/VirtualNetworks/{vnetName}

        -Subnet <String>
            Required when deploying a redis cache inside an existing Azure Virtual Network.

        -StaticIP <String>
            Required when deploying a redis cache inside an existing Azure Virtual Network.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

K vytvoření mezipaměti se výchozí parametry, spusťte následující příkaz.

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US"

`ResourceGroupName`, `Name`, a `Location` jsou požadované parametry, ale zbývající jsou volitelné a mají výchozí hodnoty. Předchozí příkaz vytvoří instanci standardní SKU Azure Redis Cache s zadaný název, umístění a skupinu prostředků, který je 1 GB velikost port bez SSL zakázána.

Chcete-li vytvořit cache ve verzi premium, zadejte velikost P1 (6 GB - 60 GB), P2 (13 GB - 130 GB), P3 (26 GB - 260 GB), nebo P4 (53 GB - 530 GB). Pokud chcete povolit, clustering, zadejte počet horizontálních pomocí `ShardCount` parametr. Následující příklad vytvoří mezipaměť premium P1 s 3 horizontálními oddíly. Mezipaměť premium P1 je 6 GB velikost a vzhledem k tomu, že jsme zadali tři horizontálních oddílů je celková velikost 18 GB (3 × 6 GB).

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P1 -ShardCount 3

Zadat hodnoty `RedisConfiguration` parametr, uzavřete hodnoty uvnitř `{}` jako klíč/hodnota, jako páry `@{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}`. Následující příklad vytvoří standardní 1 GB mezipaměti s `allkeys-random` maxmemory zásady a keyspace oznámení nakonfigurované s `KEA`. Další informace najdete v tématu [oznámení Keyspace (rozšířené nastavení)](cache-configure.md#keyspace-notifications-advanced-settings) a [paměti zásady](cache-configure.md#memory-policies).

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}

<a name="databases"></a>

## <a name="to-configure-the-databases-setting-during-cache-creation"></a>Chcete-li konfigurovat nastavení během vytváření mezipaměti databáze
`databases` Nastavení se dá nakonfigurovat jenom během vytváření mezipaměti. Následující příklad vytvoří premium P3 (26 GB) mezipaměti s 48 databází pomocí [New-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634517.aspx) rutiny.

    New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P3 -RedisConfiguration @{"databases" = "48"}

Další informace o `databases` vlastnost, najdete v části [konfigurace serveru výchozí Azure Redis Cache](cache-configure.md#default-redis-server-configuration). Další informace o vytvoření mezipaměti pomocí [New-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634517.aspx) rutiny, najdete v předchozí [k vytvoření mezipaměti Redis](#to-create-a-redis-cache) části.

## <a name="to-update-a-redis-cache"></a>Aktualizace mezipaměti Redis
Instance služby Azure Redis Cache jsou aktualizovány pomocí [Set-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634518.aspx) rutiny.

Chcete-li zobrazit seznam dostupných parametrů a jejich popisy pro `Set-AzureRmRedisCache`, spusťte následující příkaz.

    PS C:\> Get-Help Set-AzureRmRedisCache -detailed

    NAME
        Set-AzureRmRedisCache

    SYNOPSIS
        Set redis cache updatable parameters.

    SYNTAX
        Set-AzureRmRedisCache -Name <String> -ResourceGroupName <String> [-Size <String>] [-Sku <String>]
        [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort <Boolean>] [-ShardCount
        <Integer>] [<CommonParameters>]

    DESCRIPTION
        The Set-AzureRmRedisCache cmdlet sets redis cache parameters.

    PARAMETERS
        -Name <String>
            Name of the redis cache to update.

        -ResourceGroupName <String>
            Name of the resource group for the cache.

        -Size <String>
            Size of the redis cache. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
            C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.

        -Sku <String>
            Sku of redis cache. The default value is Standard. Possible values are Basic, Standard and Premium.

        -MaxMemoryPolicy <String>
            The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
            MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}

        -RedisConfiguration <Hashtable>
            All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
            rdb-backup-frequency, maxmemory-reserved, maxmemory-policy, notify-keyspace-events, hash-max-ziplist-entries,
            hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries, zset-max-ziplist-value.

        -EnableNonSslPort <Boolean>
            EnableNonSslPort is used by Azure Redis Cache. The default value is null and no change will be made to the
            currently configured value. Possible values are true and false.

        -ShardCount <Integer>
            The number of shards to create on a Premium Cluster Cache.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

`Set-AzureRmRedisCache` Rutinu můžete použít k aktualizaci vlastností, jako `Size`, `Sku`, `EnableNonSslPort`a `RedisConfiguration` hodnoty. 

Příkaz aktualizuje maxmemory zásady pro Redis Cache s názvem myCache.

    Set-AzureRmRedisCache -ResourceGroupName "myGroup" -Name "myCache" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random"}

<a name="scale"></a>

## <a name="to-scale-a-redis-cache"></a>Škálování mezipaměti Redis
`Set-AzureRmRedisCache` umožňuje škálovat mezipamětí Azure Redis instance, kdy `Size`, `Sku`, nebo `ShardCount` jsou upraveny vlastnosti. 

> [!NOTE]
> Změna velikosti mezipaměti pomocí prostředí PowerShell je za stejné omezení a pokyny jako škálování mezipaměti z portálu Azure. Je možné škálovat na jinou cenovou úroveň, s následujícími omezeními.
> 
> * Z používat vyšší cenová úroveň je nelze škálovat na nižší cenovou úroveň.
> * Nelze škálovat od **Premium** dolů do mezipaměti **standardní** nebo **základní** mezipaměti.
> * Nelze škálovat od **standardní** dolů do mezipaměti **základní** mezipaměti.
> * Je možné škálovat od **základní** mezipaměti tak, aby **standardní** mezipaměti, ale nemůže změnit velikost ve stejnou dobu. Pokud potřebujete jinou velikost, můžete provést následující operaci škálování na požadovanou velikost.
> * Nelze škálovat od **základní** přímo do mezipaměti **Premium** mezipaměti. Musí škálování z **základní** k **standardní** v rámci jedné operace škálování a potom z **standardní** k **Premium** v následných operaci škálování.
> * Nelze škálovat z větší velikost dolů na **C0 (250 MB)** velikost.
> 
> Další informace najdete v tématu [postup škálování Azure Redis Cache](cache-how-to-scale.md).
> 
> 

Následující příklad ukazuje postup škálování mezipaměti s názvem `myCache` do mezipaměti 2,5 GB. Všimněte si, že tento příkaz lze použít pro základní nebo standardní mezipaměti.

    Set-AzureRmRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB

Po vydání tento příkaz, je vrácen stav mezipaměti (podobně jako volání `Get-AzureRmRedisCache`). Všimněte si, že `ProvisioningState` je `Scaling`.

    PS C:\> Set-AzureRmRedisCache -Name myCache -ResourceGroupName myGroup -Size 2.5GB


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

Po dokončení operace škálování `ProvisioningState` změny `Succeeded`. Pokud potřebujete provést následné škálování operace, jako je například změna z Basic na Standard a pak změny velikosti, musíte počkat, až po dokončení předchozí operace nebo obdržíte chybu podobný následujícímu.

    Set-AzureRmRedisCache : Conflict: The resource '...' is not in a stable state, and is currently unable to accept the update request.

## <a name="to-get-information-about-a-redis-cache"></a>Chcete-li získat informace o mezipaměti Redis
Můžete načíst informace o mezipaměti pomocí [Get-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634514.aspx) rutiny.

Chcete-li zobrazit seznam dostupných parametrů a jejich popisy pro `Get-AzureRmRedisCache`, spusťte následující příkaz.

    PS C:\> Get-Help Get-AzureRmRedisCache -detailed

    NAME
        Get-AzureRmRedisCache

    SYNOPSIS
        Gets details about a single cache or all caches in the specified resource group or all caches in the current
        subscription.

    SYNTAX
        Get-AzureRmRedisCache [-Name <String>] [-ResourceGroupName <String>] [<CommonParameters>]

    DESCRIPTION
        The Get-AzureRmRedisCache cmdlet gets the details about a cache or caches depending on input parameters. If both
        ResourceGroupName and Name parameters are provided then Get-AzureRmRedisCache will return details about the
        specific cache name provided.

        If only ResourceGroupName is provided than it will return details about all caches in the specified resource group.

        If no parameters are given than it will return details about all caches the current subscription.

    PARAMETERS
        -Name <String>
            The name of the cache. When this parameter is provided along with ResourceGroupName, Get-AzureRmRedisCache
            returns the details for the cache.

        -ResourceGroupName <String>
            The name of the resource group that contains the cache or caches. If ResourceGroupName is provided with Name
            then Get-AzureRmRedisCache returns the details of the cache specified by Name. If only the ResourceGroup
            parameter is provided, then details for all caches in the resource group are returned.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

K vrácení informací o všechny mezipaměti v aktuálním předplatném, spusťte `Get-AzureRmRedisCache` bez parametrů.

    Get-AzureRmRedisCache

K vrácení informací o všechny mezipaměti v určité skupiny zdrojů, spusťte `Get-AzureRmRedisCache` s `ResourceGroupName` parametr.

    Get-AzureRmRedisCache -ResourceGroupName myGroup

Chcete-li vrátit informace o konkrétní mezipaměti, spusťte `Get-AzureRmRedisCache` s `Name` parametr, který obsahuje název mezipaměti a `ResourceGroupName` parametr s skupinu prostředků obsahující mezipaměť.

    PS C:\> Get-AzureRmRedisCache -Name myCache -ResourceGroupName myGroup

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

## <a name="to-retrieve-the-access-keys-for-a-redis-cache"></a>Chcete-li získat přístupové klíče pro Redis cache
Chcete-li získat přístupové klíče pro mezipaměť, můžete použít [Get-AzureRmRedisCacheKey](https://msdn.microsoft.com/library/azure/mt634516.aspx) rutiny.

Chcete-li zobrazit seznam dostupných parametrů a jejich popisy pro `Get-AzureRmRedisCacheKey`, spusťte následující příkaz.

    PS C:\> Get-Help Get-AzureRmRedisCacheKey -detailed

    NAME
        Get-AzureRmRedisCacheKey

    SYNOPSIS
        Gets the accesskeys for the specified redis cache.


    SYNTAX
        Get-AzureRmRedisCacheKey -Name <String> -ResourceGroupName <String> [<CommonParameters>]

    DESCRIPTION
        The Get-AzureRmRedisCacheKey cmdlet gets the access keys for the specified cache.

    PARAMETERS
        -Name <String>
            Name of the redis cache.

        -ResourceGroupName <String>
            Name of the resource group for the cache.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

Chcete-li získat klíče pro mezipaměť, zavolejte `Get-AzureRmRedisCacheKey` rutiny a předat názvu vaší mezipaměti název skupiny prostředků, který obsahuje mezipaměti.

    PS C:\> Get-AzureRmRedisCacheKey -Name myCache -ResourceGroupName myGroup

    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : ABhfB757JgjIgt785JgKH9865eifmekfnn649303JKL=

## <a name="to-regenerate-access-keys-for-your-redis-cache"></a>Chcete-li znovu vygenerovat přístupové klíče pro vaše mezipaměť Redis
Chcete-li znovu vygenerovat přístupové klíče pro mezipaměť, můžete použít [New-AzureRmRedisCacheKey](https://msdn.microsoft.com/library/azure/mt634512.aspx) rutiny.

Chcete-li zobrazit seznam dostupných parametrů a jejich popisy pro `New-AzureRmRedisCacheKey`, spusťte následující příkaz.

    PS C:\> Get-Help New-AzureRmRedisCacheKey -detailed

    NAME
        New-AzureRmRedisCacheKey

    SYNOPSIS
        Regenerates the access key of a redis cache.

    SYNTAX
        New-AzureRmRedisCacheKey -Name <String> -ResourceGroupName <String> -KeyType <String> [-Force] [<CommonParameters>]

    DESCRIPTION
        The New-AzureRmRedisCacheKey cmdlet regenerate the access key of a redis cache.

    PARAMETERS
        -Name <String>
            Name of the redis cache.

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
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

Chcete-li znovu vygenerovat primární nebo sekundární klíč pro mezipaměť, volejte `New-AzureRmRedisCacheKey` rutiny a předejte název skupiny prostředků a zadejte buď `Primary` nebo `Secondary` pro `KeyType` parametr. V následujícím příkladu je znovu vygenerovat sekundární přístupový klíč pro mezipaměť.

    PS C:\> New-AzureRmRedisCacheKey -Name myCache -ResourceGroupName myGroup -KeyType Secondary

    Confirm
    Are you sure you want to regenerate Secondary key for redis cache 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : c53hj3kh4jhHjPJk8l0jji785JgKH9865eifmekfnn6=

## <a name="to-delete-a-redis-cache"></a>Chcete-li odstranit mezipaměti Redis
Pokud chcete odstranit mezipaměti Redis, použijte [odebrat AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634515.aspx) rutiny.

Chcete-li zobrazit seznam dostupných parametrů a jejich popisy pro `Remove-AzureRmRedisCache`, spusťte následující příkaz.

    PS C:\> Get-Help Remove-AzureRmRedisCache -detailed

    NAME
        Remove-AzureRmRedisCache

    SYNOPSIS
        Remove redis cache if exists.

    SYNTAX
        Remove-AzureRmRedisCache -Name <String> -ResourceGroupName <String> [-Force] [-PassThru] [<CommonParameters>

    DESCRIPTION
        The Remove-AzureRmRedisCache cmdlet removes a redis cache if it exists.

    PARAMETERS
        -Name <String>
            Name of the redis cache to remove.

        -ResourceGroupName <String>
            Name of the resource group of the cache to remove.

        -Force
            When the Force parameter is provided, the cache is removed without any confirmation prompts.

        -PassThru
            By default Remove-AzureRmRedisCache removes the cache and does not return any value. If the PassThru par
            is provided then Remove-AzureRmRedisCache returns a boolean value indicating the success of the operatio

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

V následujícím příkladu, s názvem mezipaměti `myCache` se odebere.

    PS C:\> Remove-AzureRmRedisCache -Name myCache -ResourceGroupName myGroup

    Confirm
    Are you sure you want to remove redis cache 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


## <a name="to-import-a-redis-cache"></a>Chcete-li importovat mezipaměti Redis
Data můžete importovat do instance Azure Redis Cache pomocí `Import-AzureRmRedisCache` rutiny.

> [!IMPORTANT]
> Import a Export je k dispozici pouze [úroveň premium](cache-premium-tier-intro.md) ukládá do mezipaměti. Další informace o importu a exportu najdete v tématu [importu a exportu dat ve službě Azure Redis Cache](cache-how-to-import-export-data.md).
> 
> 

Chcete-li zobrazit seznam dostupných parametrů a jejich popisy pro `Import-AzureRmRedisCache`, spusťte následující příkaz.

    PS C:\> Get-Help Import-AzureRmRedisCache -detailed

    NAME
        Import-AzureRmRedisCache

    SYNOPSIS
        Import data from blobs to Azure Redis Cache.


    SYNTAX
        Import-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -Files <String[]> [-Format <String>] [-Force]
        [-PassThru] [<CommonParameters>]


    DESCRIPTION
        The Import-AzureRmRedisCache cmdlet imports data from the specified blobs into Azure Redis Cache.


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
            By default Import-AzureRmRedisCache imports data in cache and does not return any value. If the PassThru
            parameter is provided then Import-AzureRmRedisCache returns a boolean value indicating the success of the
            operation.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).


Následující příkaz importuje data z objektu blob určeného identifikátor uri SAS do Azure Redis Cache.

    PS C:\>Import-AzureRmRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Files @("https://mystorageaccount.blob.core.windows.net/mycontainername/blobname?sv=2015-04-05&sr=b&sig=caIwutG2uDa0NZ8mjdNJdgOY8%2F8mhwRuGNdICU%2B0pI4%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwd") -Force

## <a name="to-export-a-redis-cache"></a>Chcete-li exportovat mezipaměti Redis
Data můžete exportovat z instance Azure Redis Cache pomocí `Export-AzureRmRedisCache` rutiny.

> [!IMPORTANT]
> Import a Export je k dispozici pouze [úroveň premium](cache-premium-tier-intro.md) ukládá do mezipaměti. Další informace o importu a exportu najdete v tématu [importu a exportu dat ve službě Azure Redis Cache](cache-how-to-import-export-data.md).
> 
> 

Chcete-li zobrazit seznam dostupných parametrů a jejich popisy pro `Export-AzureRmRedisCache`, spusťte následující příkaz.

    PS C:\> Get-Help Export-AzureRmRedisCache -detailed

    NAME
        Export-AzureRmRedisCache

    SYNOPSIS
        Exports data from Azure Redis Cache to a specified container.


    SYNTAX
        Export-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -Prefix <String> -Container <String> [-Format
        <String>] [-PassThru] [<CommonParameters>]


    DESCRIPTION
        The Export-AzureRmRedisCache cmdlet exports data from Azure Redis Cache to a specified container.


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
            By default Export-AzureRmRedisCache does not return any value. If the PassThru parameter is provided
            then Export-AzureRmRedisCache returns a boolean value indicating the success of the operation.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).


Tento příkaz exportuje data z instance Azure Redis Cache do kontejneru určeného identifikátor uri SAS.

        PS C:\>Export-AzureRmRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Prefix "blobprefix"
        -Container "https://mystorageaccount.blob.core.windows.net/mycontainer?sv=2015-04-05&sr=c&sig=HezZtBZ3DURmEGDduauE7
        pvETY4kqlPI8JCNa8ATmaw%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwdl"

## <a name="to-reboot-a-redis-cache"></a>Restartování mezipaměti Redis
Restartování počítače pomocí instance Azure Redis Cache `Reset-AzureRmRedisCache` rutiny.

> [!IMPORTANT]
> Restart je k dispozici pouze [úroveň premium](cache-premium-tier-intro.md) ukládá do mezipaměti. Další informace o restartování mezipaměti najdete v tématu [mezipaměti správy – restartovat](cache-administration.md#reboot).
> 
> 

Chcete-li zobrazit seznam dostupných parametrů a jejich popisy pro `Reset-AzureRmRedisCache`, spusťte následující příkaz.

    PS C:\> Get-Help Reset-AzureRmRedisCache -detailed

    NAME
        Reset-AzureRmRedisCache

    SYNOPSIS
        Reboot specified node(s) of an Azure Redis Cache instance.


    SYNTAX
        Reset-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -RebootType <String> [-ShardId <Integer>]
        [-Force] [-PassThru] [<CommonParameters>]


    DESCRIPTION
        The Reset-AzureRmRedisCache cmdlet reboots the specified node(s) of an Azure Redis Cache instance.


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
            By default Reset-AzureRmRedisCache does not return any value. If the PassThru parameter is provided
            then Reset-AzureRmRedisCache returns a boolean value indicating the success of the operation.

        <CommonParameters>
            This cmdlet supports the common parameters: Verbose, Debug,
            ErrorAction, ErrorVariable, WarningAction, WarningVariable,
            OutBuffer, PipelineVariable, and OutVariable. For more information, see
            about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).


Následující příkaz restartuje oba uzly zadané mezipaměti.

        PS C:\>Reset-AzureRmRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -RebootType "AllNodes"
        -Force


## <a name="next-steps"></a>Další postup
Další informace o používání prostředí Windows PowerShell s Azure, najdete v následujících zdrojích informací:

* [Azure Redis Cache rutiny dokumentaci na webu MSDN](https://msdn.microsoft.com/library/azure/mt634513.aspx)
* [Rutiny Azure Resource Manager](http://go.microsoft.com/fwlink/?LinkID=394765): Naučte se používat rutiny v modulu Azure Resource Manager.
* [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/resource-group-template-deploy-portal.md): Naučte se vytvářet a spravovat skupiny prostředků na portálu Azure.
* [Azure blog](http://blogs.msdn.com/windowsazure): Další informace o nových funkcích v Azure.
* [Blogu o prostředí Windows PowerShell](http://blogs.msdn.com/powershell): Další informace o nové funkce v prostředí Windows PowerShell.
* ["Hey, Scripting Guy!" Blog](http://blogs.technet.com/b/heyscriptingguy/): získat reálného tipy a triky od komunity prostředí Windows PowerShell.


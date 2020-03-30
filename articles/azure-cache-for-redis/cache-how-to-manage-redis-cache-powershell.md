---
title: Správa mezipaměti Azure pro Redis pomocí Azure PowerShellu
description: Zjistěte, jak provádět úlohy správy pro Azure Cache for Redis pomocí Azure PowerShellu.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/13/2017
ms.author: yegu
ms.openlocfilehash: a385d3ed7ef46389f96de72c98ffc29cebf60ec4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278529"
---
# <a name="manage-azure-cache-for-redis-with-azure-powershell"></a>Správa mezipaměti Azure pro Redis pomocí Azure PowerShellu
> [!div class="op_single_selector"]
> * [PowerShell](cache-how-to-manage-redis-cache-powershell.md)
> * [Azure CLI](cache-manage-cli.md)
> 
> 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Toto téma ukazuje, jak provádět běžné úkoly, jako je vytváření, aktualizace a škálování instancí Azure Cache for Redis, jak znovu vygenerovat přístupové klíče a jak zobrazit informace o mezipamětích. Úplný seznam rutin Azure Cache for Redis PowerShell najdete v tématu [Rutiny Azure Cache for Redis](https://docs.microsoft.com/powershell/module/az.rediscache).

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

Další informace o klasickém modelu nasazení najdete v [tématu Azure Resource Manager vs. klasické nasazení: Principy modelů nasazení a stavu vašich prostředků](../azure-resource-manager/management/deployment-models.md).

## <a name="prerequisites"></a>Požadavky
Pokud jste už nainstalovali Azure PowerShell, musíte mít Azure PowerShell verze 1.0.0 nebo novější. Verzi Azure PowerShellu, kterou jste nainstalovali pomocí tohoto příkazu, můžete zkontrolovat na příkazovém řádku Azure PowerShellu.

    Get-Module Az | format-table version


Nejprve se musíte přihlásit do Azure pomocí tohoto příkazu.

    Connect-AzAccount

V přihlašovacím dialogovém okně Microsoft Azure zadejte e-mailovou adresu svého účtu Azure a jeho heslo.

Dále pokud máte více předplatných Azure, musíte nastavit předplatné Azure. Chcete-li zobrazit seznam aktuálních předplatných, spusťte tento příkaz.

    Get-AzSubscription | sort SubscriptionName | Select SubscriptionName

Chcete-li zadat odběr, spusťte následující příkaz. V následujícím příkladu je `ContosoSubscription`název předplatného .

    Select-AzSubscription -SubscriptionName ContosoSubscription

Než budete moct používat Windows PowerShell se Správcem prostředků Azure, budete potřebovat následující:

* Prostředí Windows PowerShell verze 3.0 nebo 4.0. Chcete-li najít verzi prostředí Windows`$PSVersionTable` PowerShell, `PSVersion` zadejte: a ověřte hodnotu 3.0 nebo 4.0. Informace o instalaci kompatibilní verze naleznete v [rozhraní Windows Management Framework 3.0](https://www.microsoft.com/download/details.aspx?id=34595) nebo Windows Management Framework [4.0](https://www.microsoft.com/download/details.aspx?id=40855).

Chcete-li získat podrobnou nápovědu pro libovolnou rutinu, kterou vidíte v tomto kurzu, použijte rutinu Get-Help.

    Get-Help <cmdlet-name> -Detailed

Chcete-li například získat `New-AzRedisCache` nápovědu k rutině, zadejte:

    Get-Help New-AzRedisCache -Detailed

### <a name="how-to-connect-to-other-clouds"></a>Jak se připojit k jiným cloudům
Ve výchozím nastavení `AzureCloud`je prostředí Azure , které představuje globální instanci cloudu Azure. Chcete-li se připojit k `Connect-AzAccount` jiné `-Environment` instanci, použijte příkaz s přepínačem příkazového řádku nebo s`EnvironmentName` požadovaným názvem prostředí nebo prostředí.

Chcete-li zobrazit seznam dostupných `Get-AzEnvironment` prostředí, spusťte rutinu.

### <a name="to-connect-to-the-azure-government-cloud"></a>Připojení k Azure Government Cloud
Chcete-li se připojit k Azure Government Cloud, použijte jeden z následujících příkazů.

    Connect-AzAccount -EnvironmentName AzureUSGovernment

– nebo –

    Connect-AzAccount -Environment (Get-AzEnvironment -Name AzureUSGovernment)

Chcete-li vytvořit mezipaměť v Azure Government Cloud, použijte jedno z následujících umístění.

* USGov Virginie
* USGov Iowa

Další informace o Azure Government Cloud najdete v [tématu Microsoft Azure Government](https://azure.microsoft.com/features/gov/) a [Microsoft Azure Government Developer Guide](../azure-government-developer-guide.md).

### <a name="to-connect-to-the-azure-china-cloud"></a>Připojení k Azure China Cloud
Chcete-li se připojit k Azure China Cloud, použijte jeden z následujících příkazů.

    Connect-AzAccount -EnvironmentName AzureChinaCloud

– nebo –

    Connect-AzAccount -Environment (Get-AzEnvironment -Name AzureChinaCloud)

Chcete-li vytvořit mezipaměť v Azure China Cloud, použijte jedno z následujících umístění.

* Čína – východ
* Čína – sever

Další informace o Azure China Cloud najdete v tématu [AzureChinaCloud pro Azure provozovaný 21Vianet v Číně](https://www.windowsazure.cn/).

### <a name="to-connect-to-microsoft-azure-germany"></a>Připojení k Microsoft Azure V Německu
Chcete-li se připojit k Microsoft Azure Germany, použijte jeden z následujících příkazů.

    Connect-AzAccount -EnvironmentName AzureGermanCloud


– nebo –

    Connect-AzAccount -Environment (Get-AzEnvironment -Name AzureGermanCloud)

Chcete-li vytvořit mezipaměť v Microsoft Azure Germany, použijte jedno z následujících umístění.

* Německo – střed
* Německo – severovýchod

Další informace o Microsoft Azure Germany najdete v [tématu Microsoft Azure Germany](https://azure.microsoft.com/overview/clouds/germany/).

### <a name="properties-used-for-azure-cache-for-redis-powershell"></a>Vlastnosti používané pro azure mezipaměť pro Redis PowerShell
Následující tabulka obsahuje vlastnosti a popisy běžně používaných parametrů při vytváření a správě instancí Azure Cache for Redis pomocí Azure PowerShellu.

| Parametr | Popis | Výchozí |
| --- | --- | --- |
| Name (Název) |Název mezipaměti | |
| Umístění |Umístění mezipaměti | |
| ResourceGroupName |Název skupiny prostředků, ve kterém chcete vytvořit mezipaměť | |
| Velikost |Velikost mezipaměti. Platné hodnoty jsou: P1, P2, P3, P4, C0, C1, C2, C3, C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB |1 GB |
| ShardCount |Počet úlomků, které chcete vytvořit při vytváření mezipaměti premium s povoleným clusteringem. Platné hodnoty jsou: 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 | |
| Skladová jednotka (SKU) |Určuje skladovou položku mezipaměti. Platné hodnoty jsou: Základní, Standardní, Premium |Standard |
| RedisKonfigurace |Určuje nastavení konfigurace Redis. Podrobnosti o jednotlivých nastaveních naleznete v následující tabulce [vlastností RedisConfiguration.](#redisconfiguration-properties) | |
| EnableNonSslPort |Označuje, zda je povolen port bez SSL. |False |
| Zásady maximální paměti |Tento parametr byl zastaralá - místo toho použijte RedisConfiguration. | |
| Statické IP adresy |Při hostování mezipaměti ve virtuální síti určuje jedinečnou ADRESU IP v podsíti pro mezipaměť. Pokud není k dispozici, jeden je vybrán pro vás z podsítě. | |
| Podsíť |Při hostování mezipaměti ve virtuální síti určuje název podsítě, ve které chcete ji nasadit. | |
| VirtualNetwork |Při hostování mezipaměti ve virtuální síti určuje ID prostředku virtuální sítě, ve kterém chcete ji nasadit. | |
| Keytype |Určuje, který přístupový klíč se má při obnovování přístupových klíčů obnovit. Platné hodnoty jsou: Primární, Sekundární | |

### <a name="redisconfiguration-properties"></a>Vlastnosti rediskonfigurace
| Vlastnost | Popis | Cenové úrovně |
| --- | --- | --- |
| možnost zálohování na rdb |Zda je [povolena trvalosti dat Redis](cache-how-to-premium-persistence.md) |Pouze prémie |
| rdb-storage-connection-string |Připojovací řetězec k účtu úložiště pro [trvalost dat Redis](cache-how-to-premium-persistence.md) |Pouze prémie |
| rdb-zálohování-frekvence |Frekvence zálohování [pro trvalost dat Redis](cache-how-to-premium-persistence.md) |Pouze prémie |
| maxmemory-reserved |Konfiguruje [paměť vyhrazenou](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) pro procesy, které nejsou v mezipaměti. |Standardní a prémiové |
| zásady maxmemory |Konfiguruje [zásady vyřazení](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) mezipaměti. |Všechny cenové úrovně |
| upozornit-keyspace-události |Konfiguruje [oznámení o prostoru klíčů.](cache-configure.md#keyspace-notifications-advanced-settings) |Standardní a prémiové |
| hash-max-ziplist-entries |Konfiguruje [optimalizaci paměti](https://redis.io/topics/memory-optimization) pro malé agregační datové typy. |Standardní a prémiové |
| hash-max-ziplist-hodnota |Konfiguruje [optimalizaci paměti](https://redis.io/topics/memory-optimization) pro malé agregační datové typy. |Standardní a prémiové |
| set-max-intset-položky |Konfiguruje [optimalizaci paměti](https://redis.io/topics/memory-optimization) pro malé agregační datové typy. |Standardní a prémiové |
| zset-max-ziplist-položky |Konfiguruje [optimalizaci paměti](https://redis.io/topics/memory-optimization) pro malé agregační datové typy. |Standardní a prémiové |
| zset-max-ziplist-hodnota |Konfiguruje [optimalizaci paměti](https://redis.io/topics/memory-optimization) pro malé agregační datové typy. |Standardní a prémiové |
| databáze |Konfiguruje počet databází. Tuto vlastnost lze nakonfigurovat pouze při vytváření mezipaměti. |Standardní a prémiové |

## <a name="to-create-an-azure-cache-for-redis"></a>Vytvoření mezipaměti Azure pro Redis
Nové instance Azure Cache for Redis se vytvářejí pomocí rutiny [New-AzRedisCache.](https://docs.microsoft.com/powershell/module/az.rediscache/new-azrediscache)

> [!IMPORTANT]
> Při prvním vytvoření azure cache pro Redis v předplatném pomocí portálu `Microsoft.Cache` Azure, portál zaregistruje obor názvů pro toto předplatné. Pokud se pokusíte vytvořit první mezipaměť Azure pro Redis v předplatném pomocí Prostředí PowerShell, musíte nejprve zaregistrovat tento obor názvů pomocí následujícího příkazu; jinak rutiny, jako `New-AzRedisCache` `Get-AzRedisCache` je například a selhání.
> 
> `Register-AzResourceProvider -ProviderNamespace "Microsoft.Cache"`
> 
> 

Chcete-li zobrazit seznam dostupných parametrů `New-AzRedisCache`a jejich popisy pro , spusťte následující příkaz.

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

Chcete-li vytvořit mezipaměť s výchozími parametry, spusťte následující příkaz.

    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US"

`ResourceGroupName`, `Name`a `Location` jsou požadované parametry, ale zbytek jsou volitelné a mají výchozí hodnoty. Spuštění předchozího příkazu vytvoří standardní skladovou položku Azure Cache for Redis s zadaným názvem, umístěním a skupinou prostředků, která má velikost 1 GB s zakázaným portem bez SSL.

Chcete-li vytvořit prémiovou mezipaměť, zadejte velikost P1 (6 GB - 60 GB), P2 (13 GB - 130 GB), P3 (26 GB - 260 GB) nebo P4 (53 GB - 530 GB). Chcete-li povolit clustering, zadejte počet úlomků pomocí parametru. `ShardCount` Následující příklad vytvoří prémiovou mezipaměť P1 se 3 úlomky. Prémiová vyrovnávací paměť P1 má velikost 6 GB a vzhledem k tomu, že jsme zadali tři úlomky, celková velikost je 18 GB (3 x 6 GB).

    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P1 -ShardCount 3

Chcete-li zadat `RedisConfiguration` hodnoty parametru, `{}` uzavřete hodnoty uvnitř `@{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}`jako páry klíč/hodnota, jako je . Následující příklad vytvoří standardní mezipaměť `allkeys-random` 1 GB s maximální zásadou paměti a oznámeními o klíčovém prostoru nakonfigurovanými pomocí aplikace `KEA`. Další informace naleznete v [tématu Oznámení keyspace (upřesňující nastavení)](cache-configure.md#keyspace-notifications-advanced-settings) a [zásady paměti](cache-configure.md#memory-policies).

    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}

<a name="databases"></a>

## <a name="to-configure-the-databases-setting-during-cache-creation"></a>Konfigurace nastavení databází během vytváření mezipaměti
Nastavení `databases` lze konfigurovat pouze při vytváření mezipaměti. Následující příklad vytvoří prémiovou mezipaměť P3 (26 GB) se 48 databázemi pomocí rutiny [New-AzRedisCache.](https://docs.microsoft.com/powershell/module/az.rediscache/New-azRedisCache)

    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P3 -RedisConfiguration @{"databases" = "48"}

Další informace o `databases` vlastnosti najdete [v tématu Výchozí mezipaměť Azure pro konfiguraci serveru Redis](cache-configure.md#default-redis-server-configuration). Další informace o vytvoření mezipaměti pomocí rutiny [New-AzRedisCache](https://docs.microsoft.com/powershell/module/az.rediscache/new-azrediscache) najdete v předchozím tématu Chcete-li vytvořit azure cache pro Redis části.

## <a name="to-update-an-azure-cache-for-redis"></a>Aktualizace mezipaměti Azure pro Redis
Instance Azure Cache for Redis se aktualizují pomocí rutiny [Set-AzRedisCache.](https://docs.microsoft.com/powershell/module/az.rediscache/Set-azRedisCache)

Chcete-li zobrazit seznam dostupných parametrů `Set-AzRedisCache`a jejich popisy pro , spusťte následující příkaz.

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

`Set-AzRedisCache` Rutinu lze použít k aktualizaci `Size`vlastností, jako jsou , `Sku`, `EnableNonSslPort`a `RedisConfiguration` hodnoty. 

Následující příkaz aktualizuje zásady maximální paměti pro Azure Cache pro Redis s názvem myCache.

    Set-AzRedisCache -ResourceGroupName "myGroup" -Name "myCache" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random"}

<a name="scale"></a>

## <a name="to-scale-an-azure-cache-for-redis"></a>Škálování mezipaměti Azure pro Redis
`Set-AzRedisCache`lze škálovat azure cache pro redis `Size`instance `Sku`při `ShardCount` změně , nebo vlastnosti. 

> [!NOTE]
> Škálování mezipaměti pomocí PowerShellu podléhá stejným omezením a pokynům jako škálování mezipaměti z portálu Azure. Můžete škálovat na jinou cenovou úroveň s následujícími omezeními.
> 
> * Nelze škálovat z vyšší cenové úrovně na nižší cenovou úroveň.
> * Z mezipaměti **Premium** nelze škálovat na **standardní** nebo **základní** mezipaměť.
> * Nelze škálovat ze **standardní** mezipaměti na **základní** mezipaměť.
> * Můžete škálovat ze **základní** mezipaměti na **standardní** mezipaměť, ale nelze změnit velikost současně. Pokud potřebujete jinou velikost, můžete provést následnou operaci škálování na požadovanou velikost.
> * Z **mezipaměti Basic** nelze škálovat přímo do mezipaměti **Premium.** V jedné operaci škálování je nutné škálovat ze **základního** na **standardní** a v následné operaci škálování ze **standardního** na **premium.**
> * Nelze škálovat z větší velikosti až na velikost **C0 (250 MB).**
> 
> Další informace najdete v tématu [Jak škálovat azure cache pro Redis](cache-how-to-scale.md).
> 
> 

Následující příklad ukazuje, jak škálovat mezipaměť s názvem `myCache` do mezipaměti o velikosti 2,5 GB. Všimněte si, že tento příkaz funguje pro základní nebo standardní mezipaměti.

    Set-AzRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB

Po vydání tohoto příkazu je vrácen stav mezipaměti `Get-AzRedisCache`(podobně jako volání ). Všimněte `ProvisioningState` si, že je `Scaling`.

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

Po dokončení operace škálování `ProvisioningState` se `Succeeded`změní na . Pokud potřebujete provést následnou operaci škálování, jako je například změna ze základní na standardní a potom změna velikosti, musíte počkat, dokud nebude dokončena předchozí operace nebo se zobrazí chyba podobná následující.

    Set-AzRedisCache : Conflict: The resource '...' is not in a stable state, and is currently unable to accept the update request.

## <a name="to-get-information-about-an-azure-cache-for-redis"></a>Získání informací o azure mezipaměti pro Redis
Informace o mezipaměti můžete načíst pomocí rutiny [Get-AzRedisCache.](https://docs.microsoft.com/powershell/module/az.rediscache/get-azrediscache)

Chcete-li zobrazit seznam dostupných parametrů `Get-AzRedisCache`a jejich popisy pro , spusťte následující příkaz.

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

Chcete-li vrátit informace o všech mezipamětích v aktuálním předplatném, spusťte `Get-AzRedisCache` bez jakýchkoli parametrů.

    Get-AzRedisCache

Chcete-li vrátit informace o všech mezipamětích `Get-AzRedisCache` v `ResourceGroupName` určité skupině prostředků, spusťte s parametrem.

    Get-AzRedisCache -ResourceGroupName myGroup

Chcete-li vrátit informace o `Get-AzRedisCache` konkrétní `Name` mezipaměti, spusťte parametr `ResourceGroupName` obsahující název mezipaměti a parametr se skupinou prostředků obsahující tuto mezipaměť.

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

## <a name="to-retrieve-the-access-keys-for-an-azure-cache-for-redis"></a>Načtení přístupových klíčů pro Azure Cache pro Redis
Chcete-li načíst přístupové klíče pro mezipaměť, můžete použít rutinu [Get-AzRedisCacheKey.](https://docs.microsoft.com/powershell/module/az.rediscache/Get-azRedisCacheKey)

Chcete-li zobrazit seznam dostupných parametrů `Get-AzRedisCacheKey`a jejich popisy pro , spusťte následující příkaz.

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

Chcete-li načíst klíče pro `Get-AzRedisCacheKey` mezipaměť, zavolejte rutinu a předejte název mezipaměti název skupiny prostředků, která obsahuje mezipaměť.

    PS C:\> Get-AzRedisCacheKey -Name myCache -ResourceGroupName myGroup

    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : ABhfB757JgjIgt785JgKH9865eifmekfnn649303JKL=

## <a name="to-regenerate-access-keys-for-your-azure-cache-for-redis"></a>Obnovení přístupových klíčů pro azure cache pro Redis
Chcete-li znovu vygenerovat přístupové klíče pro mezipaměť, můžete použít rutinu [New-AzRedisCacheKey.](https://docs.microsoft.com/powershell/module/az.rediscache/New-azRedisCacheKey)

Chcete-li zobrazit seznam dostupných parametrů `New-AzRedisCacheKey`a jejich popisy pro , spusťte následující příkaz.

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

Chcete-li znovu vygenerovat primární nebo sekundární `New-AzRedisCacheKey` klíč mezipaměti, zavolejte rutinu a `Primary` předejte `KeyType` název, skupinu prostředků a zadejte buď parametr. `Secondary` V následujícím příkladu je obnoven sekundární přístupový klíč pro mezipaměť.

    PS C:\> New-AzRedisCacheKey -Name myCache -ResourceGroupName myGroup -KeyType Secondary

    Confirm
    Are you sure you want to regenerate Secondary key for Azure Cache for Redis 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : c53hj3kh4jhHjPJk8l0jji785JgKH9865eifmekfnn6=

## <a name="to-delete-an-azure-cache-for-redis"></a>Odstranění mezipaměti Azure pro Redis
Chcete-li odstranit mezipaměť Azure pro Redis, použijte rutinu [Remove-AzRedisCache.](https://docs.microsoft.com/powershell/module/az.rediscache/remove-azrediscache)

Chcete-li zobrazit seznam dostupných parametrů `Remove-AzRedisCache`a jejich popisy pro , spusťte následující příkaz.

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

V následujícím příkladu je `myCache` odebrána mezipaměť s názvem.

    PS C:\> Remove-AzRedisCache -Name myCache -ResourceGroupName myGroup

    Confirm
    Are you sure you want to remove Azure Cache for Redis 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


## <a name="to-import-an-azure-cache-for-redis"></a>Import azure mezipaměti pro Redis
Data můžete importovat do instance Azure Cache `Import-AzRedisCache` for Redis pomocí rutiny.

> [!IMPORTANT]
> Import a export je k dispozici pouze pro mezipaměti [úrovně premium.](cache-premium-tier-intro.md) Další informace o importu a exportu najdete [v tématu Import a export dat v mezipaměti Azure pro Redis](cache-how-to-import-export-data.md).
> 
> 

Chcete-li zobrazit seznam dostupných parametrů `Import-AzRedisCache`a jejich popisy pro , spusťte následující příkaz.

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


Následující příkaz importuje data z objektu blob určeného uri SAS do mezipaměti Azure pro Redis.

    PS C:\>Import-AzRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Files @("https://mystorageaccount.blob.core.windows.net/mycontainername/blobname?sv=2015-04-05&sr=b&sig=caIwutG2uDa0NZ8mjdNJdgOY8%2F8mhwRuGNdICU%2B0pI4%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwd") -Force

## <a name="to-export-an-azure-cache-for-redis"></a>Export azure mezipaměti pro Redis
Data můžete exportovat z instance Azure Cache `Export-AzRedisCache` for Redis pomocí rutiny.

> [!IMPORTANT]
> Import a export je k dispozici pouze pro mezipaměti [úrovně premium.](cache-premium-tier-intro.md) Další informace o importu a exportu najdete [v tématu Import a export dat v mezipaměti Azure pro Redis](cache-how-to-import-export-data.md).
> 
> 

Chcete-li zobrazit seznam dostupných parametrů `Export-AzRedisCache`a jejich popisy pro , spusťte následující příkaz.

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


Následující příkaz exportuje data z instance Azure Cache for Redis do kontejneru určeného identifikátorem Uri SAS.

        PS C:\>Export-AzRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Prefix "blobprefix"
        -Container "https://mystorageaccount.blob.core.windows.net/mycontainer?sv=2015-04-05&sr=c&sig=HezZtBZ3DURmEGDduauE7
        pvETY4kqlPI8JCNa8ATmaw%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwdl"

## <a name="to-reboot-an-azure-cache-for-redis"></a>Restartování mezipaměti Azure pro Redis
Můžete restartovat azure cache pro Redis instance pomocí rutiny. `Reset-AzRedisCache`

> [!IMPORTANT]
> Restartování je k dispozici pouze pro mezipaměti [úrovně premium.](cache-premium-tier-intro.md) Další informace o restartování mezipaměti naleznete v [tématu Správa mezipaměti – restartování](cache-administration.md#reboot).
> 
> 

Chcete-li zobrazit seznam dostupných parametrů `Reset-AzRedisCache`a jejich popisy pro , spusťte následující příkaz.

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


## <a name="next-steps"></a>Další kroky
Další informace o používání Windows PowerShellu s Azure najdete v následujících zdrojích informací:

* [Dokumentace rutiny Azure Cache for Redis na webu MSDN](https://docs.microsoft.com/powershell/module/az.rediscache)
* [Rutiny Správce prostředků Azure](https://go.microsoft.com/fwlink/?LinkID=394765): Naučte se používat rutiny v modulu Azure Resource Manager.
* [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/templates/deploy-portal.md): Zjistěte, jak vytvořit a spravovat skupiny prostředků na webu Azure Portal.
* [Blog Azure:](https://azure.microsoft.com/blog/)Další informace o nových funkcích v Azure.
* [Blog o prostředí Windows PowerShell:](https://blogs.msdn.com/powershell)Další informace o nových funkcích v prostředí Windows PowerShell.
* ["Ahoj, scénárista!" Blog](https://blogs.technet.com/b/heyscriptingguy/): Získejte skutečné tipy a triky od komunity Windows PowerShell.


---
title: Správa mezipaměti Azure pro Redis s využitím Azure PowerShell
description: Naučte se provádět úlohy správy pro Azure cache pro Redis pomocí Azure PowerShell.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/13/2017
ms.author: yegu
ms.openlocfilehash: 88e433dbfa87d8ea483789b1fd838c62a6a481c0
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92536755"
---
# <a name="manage-azure-cache-for-redis-with-azure-powershell"></a>Správa mezipaměti Azure pro Redis s využitím Azure PowerShell
> [!div class="op_single_selector"]
> * [PowerShell](cache-how-to-manage-redis-cache-powershell.md)
> * [Azure CLI](cache-manage-cli.md)
> 
> 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

V tomto tématu se dozvíte, jak provádět běžné úlohy, jako je vytváření, aktualizace a škálování mezipaměti Azure pro instance Redis, způsob opětovného generování přístupových klíčů a postup zobrazení informací o vašich mezipamětech. Úplný seznam rutin Azure cache pro rutiny prostředí PowerShell pro Redis najdete v tématu [rutiny Azure cache pro Redis](/powershell/module/az.rediscache).

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

Další informace o modelu nasazení Classic najdete v tématu [Azure Resource Manager vs. Classic Deployment: Principy modelů nasazení a stavu vašich prostředků](../azure-resource-manager/management/deployment-models.md).

## <a name="prerequisites"></a>Požadavky
Pokud jste již nainstalovali Azure PowerShell, je nutné, abyste měli Azure PowerShell verze 1.0.0 nebo novější. Verzi Azure PowerShell, kterou jste nainstalovali s tímto příkazem, můžete zjistit na příkazovém řádku Azure PowerShell.

```azurepowershell
    Get-Module Az | format-table version
```

Nejdřív se musíte pomocí tohoto příkazu přihlásit k Azure.

```azurepowershell
    Connect-AzAccount
```

V dialogovém okně Microsoft Azure přihlášení zadejte e-mailovou adresu svého účtu Azure a jeho heslo.

Pokud máte více předplatných Azure, budete muset nastavit předplatné Azure. Pokud chcete zobrazit seznam aktuálních předplatných, spusťte tento příkaz.

```azurepowershell
    Get-AzSubscription | sort SubscriptionName | Select SubscriptionName
```

Chcete-li zadat odběr, spusťte následující příkaz. V následujícím příkladu je název předplatného `ContosoSubscription` .

```azurepowershell
    Select-AzSubscription -SubscriptionName ContosoSubscription
```

Předtím, než budete moci použít prostředí Windows PowerShell s Azure Resource Manager, potřebujete následující:

* Prostředí Windows PowerShell verze 3,0 nebo 4,0. Pokud chcete najít verzi Windows PowerShellu, zadejte: `$PSVersionTable` a ověřte hodnotu `PSVersion` 3,0 nebo 4,0. Informace o instalaci kompatibilní verze najdete v tématu [Windows Management Framework 3,0](https://www.microsoft.com/download/details.aspx?id=34595).

Pokud chcete získat podrobnou nápovědu k jakékoli rutině, kterou vidíte v tomto kurzu, použijte rutinu Get-Help.

```azurepowershell
    Get-Help <cmdlet-name> -Detailed
```

Chcete-li například získat nápovědu pro `New-AzRedisCache` rutinu, zadejte:

```azurepowershell
    Get-Help New-AzRedisCache -Detailed
```

### <a name="how-to-connect-to-other-clouds"></a>Jak se připojit k jiným cloudům
Ve výchozím nastavení je prostředí Azure `AzureCloud` , které představuje globální cloudovou instanci Azure. Pokud se chcete připojit k jiné instanci, použijte `Connect-AzAccount` příkaz s `-Environment` `EnvironmentName` přepínačem nebo příkazového řádku s požadovaným názvem prostředí nebo prostředí.

Pokud chcete zobrazit seznam dostupných prostředí, spusťte `Get-AzEnvironment` rutinu.

### <a name="to-connect-to-the-azure-government-cloud"></a>Připojení ke cloudu Azure Government
Pokud se chcete připojit ke cloudu Azure Government, použijte jeden z následujících příkazů.

```azurepowershell
    Connect-AzAccount -EnvironmentName AzureUSGovernment
```

nebo

```azurepowershell
    Connect-AzAccount -Environment (Get-AzEnvironment -Name AzureUSGovernment)
```

Pokud chcete vytvořit mezipaměť v cloudu Azure Government, použijte jedno z následujících umístění.

* USGov Virginie
* USGov Iowa

Další informace o Azure Government cloudu najdete v tématu [Microsoft Azure Government](https://azure.microsoft.com/features/gov/) a [Microsoft Azure Government příručka pro vývojáře](../azure-government/documentation-government-developer-guide.md).

### <a name="to-connect-to-the-azure-china-cloud"></a>Připojení ke cloudu Azure Čína
Pokud se chcete připojit ke cloudu Azure Čína, použijte jeden z následujících příkazů.

```azurepowershell
    Connect-AzAccount -EnvironmentName AzureChinaCloud
```

nebo

```azurepowershell
    Connect-AzAccount -Environment (Get-AzEnvironment -Name AzureChinaCloud)
```

Pokud chcete vytvořit mezipaměť v cloudu Azure Čína, použijte jedno z následujících umístění.

* Čína – východ
* Čína – sever

Další informace o cloudu Azure Čína najdete v tématu [AzureChinaCloud for Azure provozovaného společností 21Vianet v Číně](https://www.windowsazure.cn/).

### <a name="to-connect-to-microsoft-azure-germany"></a>Připojení k Microsoft Azure (Německo)
Pokud se chcete připojit k Microsoft Azure (Německo), použijte jeden z následujících příkazů.

```azurepowershell
    Connect-AzAccount -EnvironmentName AzureGermanCloud
```

nebo

```azurepowershell
    Connect-AzAccount -Environment (Get-AzEnvironment -Name AzureGermanCloud)
```

Pokud chcete vytvořit mezipaměť v Microsoft Azure (Německo), použijte jedno z následujících umístění.

* Německo – střed
* Německo – severovýchod

Další informace o Microsoft Azure (Německo) najdete v tématu [Microsoft Azure (Německo)](https://azure.microsoft.com/overview/clouds/germany/).

### <a name="properties-used-for-azure-cache-for-redis-powershell"></a>Vlastnosti používané pro Azure cache pro Redis PowerShell
Následující tabulka obsahuje vlastnosti a popisy běžně používaných parametrů při vytváření a správě mezipaměti Azure pro instance Redis pomocí Azure PowerShell.

| Parametr | Popis | Výchozí |
| --- | --- | --- |
| Název |Název mezipaměti | |
| Umístění |Umístění mezipaměti | |
| ResourceGroupName |Název skupiny prostředků, ve které se má mezipaměť vytvořit | |
| Velikost |Velikost mezipaměti. Platné hodnoty jsou: P1, P2, P3, P4, C0, C1, C2, C3, C4, C5, C6, 250MB, 1 GB, 2,5 GB, 6 GB, 13GB, 26GB, 53GB. |PAMĚT |
| ShardCount |Počet horizontálních oddílů, které se mají vytvořit při vytváření mezipaměti Premium s povoleným clusteringem Platné hodnoty jsou: 1, 2, 3, 4, 5, 6, 7, 8, 9, 10. | |
| Skladová položka |Určuje SKLADOVOU položku mezipaměti. Platné hodnoty jsou: Basic, Standard, Premium. |Standard |
| RedisConfiguration |Určuje nastavení konfigurace Redis. Podrobnosti o jednotlivých nastaveních najdete v následujících tabulkách [vlastností RedisConfiguration](#redisconfiguration-properties) . | |
| EnableNonSslPort |Určuje, jestli je povolený port bez SSL. |Ne |
| MaxMemoryPolicy |Tento parametr je zastaralý – místo toho použijte RedisConfiguration. | |
| StaticIP |Při hostování mezipaměti ve virtuální síti určuje jedinečná IP adresa v podsíti pro mezipaměť. Pokud tato možnost není k dispozici, je pro vás z podsítě zvolena jedna. | |
| Podsíť |Při hostování mezipaměti ve virtuální síti Určuje název podsítě, do které se má mezipaměť nasadit. | |
| VirtualNetwork |Při hostování mezipaměti ve virtuální síti Určuje ID prostředku virtuální sítě, do které se má mezipaměť nasadit. | |
| KeyType |Určuje, který přístupový klíč se má znovu vygenerovat při obnovování přístupových klíčů. Platné hodnoty jsou: primární, sekundární | |

### <a name="redisconfiguration-properties"></a>Vlastnosti RedisConfiguration
| Vlastnost | Popis | Cenové úrovně |
| --- | --- | --- |
| RDB – povoleno zálohování |Zda je povoleno [Trvalost dat Redis](cache-how-to-premium-persistence.md) |Jenom Premium |
| RDB – Storage-Connection-String |Připojovací řetězec k účtu úložiště pro [Trvalost dat Redis](cache-how-to-premium-persistence.md) |Jenom Premium |
| RDB – frekvence zálohování |Četnost záloh pro [Trvalost dat Redis](cache-how-to-premium-persistence.md) |Jenom Premium |
| maxmemory – rezervováno |Nakonfiguruje [paměť rezervovanou](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) pro procesy, které nejsou v mezipaměti. |Standard a Premium |
| maxmemory – zásada |Nakonfiguruje [zásadu vyřazení](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) pro mezipaměť. |Všechny cenové úrovně |
| oznamovat – události |Konfiguruje [oznámení o prostorech](cache-configure.md#keyspace-notifications-advanced-settings) . |Standard a Premium |
| hash – Max – ZipList – položky |Konfiguruje [optimalizaci paměti](https://redis.io/topics/memory-optimization) pro malé agregované datové typy. |Standard a Premium |
| hodnota hash-Max-ZipList-Value |Konfiguruje [optimalizaci paměti](https://redis.io/topics/memory-optimization) pro malé agregované datové typy. |Standard a Premium |
| set-max-intset-Entries |Konfiguruje [optimalizaci paměti](https://redis.io/topics/memory-optimization) pro malé agregované datové typy. |Standard a Premium |
| zset-Max-ZipList-Entries |Konfiguruje [optimalizaci paměti](https://redis.io/topics/memory-optimization) pro malé agregované datové typy. |Standard a Premium |
| zset-Max-ZipList-Value |Konfiguruje [optimalizaci paměti](https://redis.io/topics/memory-optimization) pro malé agregované datové typy. |Standard a Premium |
| V databázích |Konfiguruje počet databází. Tato vlastnost se dá nakonfigurovat jenom při vytváření mezipaměti. |Standard a Premium |

## <a name="to-create-an-azure-cache-for-redis"></a>Vytvoření mezipaměti Azure pro Redis
Nová mezipaměť Azure pro instance Redis se vytvoří pomocí rutiny [New-AzRedisCache](/powershell/module/az.rediscache/new-azrediscache) .

> [!IMPORTANT]
> Při prvním vytvoření mezipaměti Azure pro Redis v předplatném pomocí Azure Portal portál registruje `Microsoft.Cache` obor názvů pro toto předplatné. Pokud se pokusíte vytvořit první mezipaměť Azure pro Redis v předplatném pomocí PowerShellu, musíte nejdřív tento obor názvů zaregistrovat pomocí následujícího příkazu. v opačném případě rutin, například `New-AzRedisCache` a `Get-AzRedisCache` selhání.
> 
> `Register-AzResourceProvider -ProviderNamespace "Microsoft.Cache"`
> 
> 

Pokud chcete zobrazit seznam dostupných parametrů a jejich popis pro `New-AzRedisCache` , spusťte následující příkaz.

```azurepowershell
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
```

Pokud chcete vytvořit mezipaměť s výchozími parametry, spusťte následující příkaz.

```azurepowershell
    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US"
```

`ResourceGroupName`, `Name` a `Location` jsou vyžadovány parametry, ale zbytek je nepovinný a má výchozí hodnoty. Když spustíte předchozí příkaz, vytvoří se standardní skladová položka SKU Azure cache pro instanci Redis se zadaným názvem, umístěním a skupinou prostředků, která má nepovolený port SSL o velikosti 1 GB.

Pokud chcete vytvořit mezipaměť Premium, zadejte velikost P1 (6 GB až 60 GB), P2 (13 GB-130 GB), P3 (26 GB-260 GB) nebo P4 (53 GB-530 GB). Pokud chcete povolit clusteringu, zadejte počet horizontálních oddílů pomocí `ShardCount` parametru. Následující příklad vytvoří mezipaměť Premium P1 se 3 horizontálních oddílů. Mezipaměť P1 Premium má velikost 6 GB. vzhledem k tomu, že jsme určili tři horizontálních oddílů, celková velikost je 18 GB (3 × 6 GB).

```azurepowershell
    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P1 -ShardCount 3
```

Chcete-li zadat hodnoty pro parametr, sestavte `RedisConfiguration` hodnoty uvnitř `{}` jako páry klíč/hodnota jako `@{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}` . Následující příklad vytvoří mezipaměť o velikosti 1 GB, která má `allkeys-random` nakonfigurovanou zásadu maxmemory a oznámení na místo na úrovni `KEA` . Další informace najdete v tématech [Upozornění na místo na disku (rozšířené nastavení)](cache-configure.md#keyspace-notifications-advanced-settings) a [zásady paměti](cache-configure.md#memory-policies).

```azurepowershell
    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}
```

<a name="databases"></a>

## <a name="to-configure-the-databases-setting-during-cache-creation"></a>Konfigurace nastavení databáze během vytváření mezipaměti
`databases`Nastavení se dá nakonfigurovat jenom během vytváření mezipaměti. Následující příklad vytvoří mezipaměť Premium P3 (26 GB) s 48 databázemi pomocí rutiny [New-AzRedisCache](/powershell/module/az.rediscache/New-azRedisCache) .

```azurepowershell
    New-AzRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P3 -RedisConfiguration @{"databases" = "48"}
```

Další informace o této `databases` vlastnosti najdete v tématu [výchozí mezipaměť Azure pro konfiguraci serveru Redis](cache-configure.md#default-redis-server-configuration). Další informace o vytvoření mezipaměti pomocí rutiny [New-AzRedisCache](/powershell/module/az.rediscache/new-azrediscache) najdete v části předchozí vytvoření mezipaměti Azure pro Redis.

## <a name="to-update-an-azure-cache-for-redis"></a>Aktualizace mezipaměti Azure pro Redis
Mezipaměť Azure pro instance Redis se aktualizuje pomocí rutiny [set-AzRedisCache](/powershell/module/az.rediscache/Set-azRedisCache) .

Pokud chcete zobrazit seznam dostupných parametrů a jejich popis pro `Set-AzRedisCache` , spusťte následující příkaz.

```azurepowershell
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
```

`Set-AzRedisCache`Rutinu lze použít k aktualizaci vlastností, například, `Size` `Sku` , `EnableNonSslPort` a `RedisConfiguration` hodnot. 

Následující příkaz aktualizuje zásady maxmemory pro Azure cache pro Redis s názvem myCache.

```azurepowershell
    Set-AzRedisCache -ResourceGroupName "myGroup" -Name "myCache" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random"}
```

<a name="scale"></a>

## <a name="to-scale-an-azure-cache-for-redis"></a>Škálování mezipaměti Azure pro Redis
`Set-AzRedisCache` dá se použít ke škálování mezipaměti Azure pro instanci Redis při `Size` `Sku` úpravě vlastností,, nebo `ShardCount` . 

> [!NOTE]
> Škálování mezipaměti pomocí prostředí PowerShell podléhá stejným omezením a pokynům pro škálování mezipaměti z Azure Portal. Pomocí následujících omezení můžete škálovat na jinou cenovou úroveň.
> 
> * Nemůžete škálovat z vyšší cenové úrovně na nižší cenovou úroveň.
> * Mezipaměť **Premium** nejde škálovat do **úrovně Standard** nebo **Basic** .
> * **Standardní** mezipaměť nejde škálovat do **základní** mezipaměti.
> * Můžete škálovat ze **základní** mezipaměti na **standardní** mezipaměť, ale nemůžete změnit velikost současně. Pokud potřebujete jinou velikost, můžete provést následnou operaci škálování na požadovanou velikost.
> * Nemůžete škálovat ze **základní** mezipaměti přímo do mezipaměti **Premium** . V rámci jedné operace škálování musíte škálovat **základní** na **Standard** a pak od **úrovně Standard** až **Premium** .
> * Velikost se nedá škálovat z větší velikosti na velikost **C0 (250 MB/s)** .
> 
> Další informace najdete v tématu [Jak škálovat Azure cache pro Redis](cache-how-to-scale.md).
> 
> 

Následující příklad ukazuje, jak škálovat mezipaměť s názvem `myCache` na 2,5 GB mezipaměti. Všimněte si, že tento příkaz funguje pro základní nebo standardní mezipaměť.

```azurepowershell
    Set-AzRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB
```

Po vydání tohoto příkazu se vrátí stav mezipaměti (podobně jako volání `Get-AzRedisCache` ). Všimněte si, že `ProvisioningState` je `Scaling` .

```azurepowershell
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
```

Po dokončení operace škálování se `ProvisioningState` změní na `Succeeded` . Pokud potřebujete provést následné operace škálování, jako je například změna z úrovně Basic na standard a změna velikosti, je nutné počkat na dokončení předchozí operace nebo obdržet chybu podobnou následující.

```azurepowershell
    Set-AzRedisCache : Conflict: The resource '...' is not in a stable state, and is currently unable to accept the update request.
```

## <a name="to-get-information-about-an-azure-cache-for-redis"></a>Získání informací o službě Azure cache pro Redis
Informace o mezipaměti můžete načíst pomocí rutiny [Get-AzRedisCache](/powershell/module/az.rediscache/get-azrediscache) .

Pokud chcete zobrazit seznam dostupných parametrů a jejich popis pro `Get-AzRedisCache` , spusťte následující příkaz.

```azurepowershell
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
```

Pokud chcete vrátit informace o všech mezipamětích v aktuálním předplatném, spusťte `Get-AzRedisCache` bez parametrů.

```azurepowershell
    Get-AzRedisCache
```

Pokud chcete vrátit informace o všech mezipamětích v konkrétní skupině prostředků, spusťte `Get-AzRedisCache` s `ResourceGroupName` parametrem.

```azurepowershell
    Get-AzRedisCache -ResourceGroupName myGroup
```

Chcete-li vrátit informace o konkrétní mezipaměti, spusťte příkaz `Get-AzRedisCache` s `Name` parametrem obsahujícím název mezipaměti a `ResourceGroupName` parametr se skupinou prostředků obsahující tuto mezipaměť.

```azurepowershell
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
```

## <a name="to-retrieve-the-access-keys-for-an-azure-cache-for-redis"></a>Načtení přístupových klíčů pro Azure cache pro Redis
K načtení přístupových klíčů pro mezipaměť můžete použít rutinu [Get-AzRedisCacheKey](/powershell/module/az.rediscache/Get-azRedisCacheKey) .

Pokud chcete zobrazit seznam dostupných parametrů a jejich popis pro `Get-AzRedisCacheKey` , spusťte následující příkaz.

```azurepowershell
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
```

Pokud chcete načíst klíče pro vaši mezipaměť, zavolejte `Get-AzRedisCacheKey` rutinu a předejte název vaší mezipaměti název skupiny prostředků, která obsahuje mezipaměť.

```azurepowershell
    PS C:\> Get-AzRedisCacheKey -Name myCache -ResourceGroupName myGroup

    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : ABhfB757JgjIgt785JgKH9865eifmekfnn649303JKL=
```

## <a name="to-regenerate-access-keys-for-your-azure-cache-for-redis"></a>Opětovné generování přístupových klíčů pro službu Azure cache pro Redis
K opětovnému vygenerování přístupových klíčů pro mezipaměť můžete použít rutinu [New-AzRedisCacheKey](/powershell/module/az.rediscache/New-azRedisCacheKey) .

Pokud chcete zobrazit seznam dostupných parametrů a jejich popis pro `New-AzRedisCacheKey` , spusťte následující příkaz.

```azurepowershell
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
```

Pokud chcete znovu vygenerovat primární nebo sekundární klíč pro vaši mezipaměť, zavolejte `New-AzRedisCacheKey` rutinu a předejte ji do názvu, skupiny prostředků a zadejte buď `Primary` nebo `Secondary` pro `KeyType` parametr. V následujícím příkladu je znovu vygenerován sekundární přístupový klíč pro mezipaměť.

```azurepowershell
    PS C:\> New-AzRedisCacheKey -Name myCache -ResourceGroupName myGroup -KeyType Secondary

    Confirm
    Are you sure you want to regenerate Secondary key for Azure Cache for Redis 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


    PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
    SecondaryKey : c53hj3kh4jhHjPJk8l0jji785JgKH9865eifmekfnn6=
```

## <a name="to-delete-an-azure-cache-for-redis"></a>Postup odstranění mezipaměti Azure pro Redis
K odstranění mezipaměti Azure pro Redis použijte rutinu [Remove-AzRedisCache](/powershell/module/az.rediscache/remove-azrediscache) .

Pokud chcete zobrazit seznam dostupných parametrů a jejich popis pro `Remove-AzRedisCache` , spusťte následující příkaz.

```azurepowershell
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
```

V následujícím příkladu je mezipaměť s názvem `myCache` odebrána.

```azurepowershell
    PS C:\> Remove-AzRedisCache -Name myCache -ResourceGroupName myGroup

    Confirm
    Are you sure you want to remove Azure Cache for Redis 'myCache'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
```


## <a name="to-import-an-azure-cache-for-redis"></a>Import mezipaměti Azure pro Redis
Pomocí rutiny můžete importovat data do mezipaměti Azure pro instanci Redis `Import-AzRedisCache` .

> [!IMPORTANT]
> Import/Export je k dispozici jenom pro mezipaměti [úrovně Premium](cache-overview.md#service-tiers) . Další informace o importu a exportu najdete v tématu [Import a export dat v Azure cache pro Redis](cache-how-to-import-export-data.md).
> 
> 

Pokud chcete zobrazit seznam dostupných parametrů a jejich popis pro `Import-AzRedisCache` , spusťte následující příkaz.

```azurepowershell
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
```


Následující příkaz importuje data z objektu BLOB určeného identifikátorem URI SAS do Azure cache pro Redis.

```azurepowershell
    PS C:\>Import-AzRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Files @("https://mystorageaccount.blob.core.windows.net/mycontainername/blobname?sv=2015-04-05&sr=b&sig=caIwutG2uDa0NZ8mjdNJdgOY8%2F8mhwRuGNdICU%2B0pI4%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwd") -Force
```

## <a name="to-export-an-azure-cache-for-redis"></a>Export mezipaměti Azure pro Redis
Pomocí rutiny můžete exportovat data z mezipaměti Azure pro instanci Redis `Export-AzRedisCache` .

> [!IMPORTANT]
> Import/Export je k dispozici jenom pro mezipaměti [úrovně Premium](cache-overview.md#service-tiers) . Další informace o importu a exportu najdete v tématu [Import a export dat v Azure cache pro Redis](cache-how-to-import-export-data.md).
> 
> 

Pokud chcete zobrazit seznam dostupných parametrů a jejich popis pro `Export-AzRedisCache` , spusťte následující příkaz.

```azurepowershell
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
```


Následující příkaz exportuje data z mezipaměti Azure pro instanci Redis do kontejneru určeného identifikátorem URI SAS.

```azurepowershell
    PS C:\>Export-AzRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -Prefix "blobprefix"
    -Container "https://mystorageaccount.blob.core.windows.net/mycontainer?sv=2015-04-05&sr=c&sig=HezZtBZ3DURmEGDduauE7
    pvETY4kqlPI8JCNa8ATmaw%3D&st=2016-05-27T00%3A00%3A00Z&se=2016-05-28T00%3A00%3A00Z&sp=rwdl"
```

## <a name="to-reboot-an-azure-cache-for-redis"></a>Restartování mezipaměti Azure pro Redis
Pomocí rutiny můžete restartovat službu Azure cache pro instanci Redis `Reset-AzRedisCache` .

> [!IMPORTANT]
> Restart je dostupný jenom pro mezipaměti [úrovně Premium](cache-overview.md#service-tiers) . Další informace o restartování mezipaměti najdete v tématu [Správa mezipaměti – restart](cache-administration.md#reboot).
> 
> 

Pokud chcete zobrazit seznam dostupných parametrů a jejich popis pro `Reset-AzRedisCache` , spusťte následující příkaz.

```azurepowershell
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
```


Následující příkaz restartuje oba uzly zadané mezipaměti.

```azurepowershell
    PS C:\>Reset-AzRedisCache -ResourceGroupName "resourceGroupName" -Name "cacheName" -RebootType "AllNodes"
    -Force
```


## <a name="next-steps"></a>Další kroky
Další informace o používání prostředí Windows PowerShell s Azure najdete v následujících zdrojích informací:

* [Dokumentace k rutinám Azure cache for Redis na webu MSDN](/powershell/module/az.rediscache)
* [Rutiny Azure Resource Manager](/powershell/module/): Naučte se používat rutiny v modulu Azure Resource Manager.
* [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/templates/deploy-portal.md): Naučte se vytvářet a spravovat skupiny prostředků v Azure Portal.
* [Blog o Azure](https://azure.microsoft.com/blog/): Seznamte se s novými funkcemi v Azure.
* [Blog k Windows PowerShellu](https://devblogs.microsoft.com/powershell/): Přečtěte si o nových funkcích Windows PowerShellu.
* ["Hey, Scripting Guy!" Blog](https://devblogs.microsoft.com/scripting/tag/hey-scripting-guy/): Získejte z komunity Windows PowerShellu reálné tipy a triky.
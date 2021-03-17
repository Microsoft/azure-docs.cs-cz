---
title: Správa Azure cache pro Redis pomocí klasického rozhraní příkazového řádku Azure
description: Naučte se nainstalovat rozhraní příkazového řádku Azure Classic na jakékoli platformě, jak ji použít pro připojení k účtu Azure a jak vytvořit a spravovat službu Azure cache pro Redis z klasického rozhraní příkazového řádku.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 01/23/2017
ms.author: yegu
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7643f882d5ac330046c169e0a3f2fa4920331d4e
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92537690"
---
# <a name="how-to-create-and-manage-azure-cache-for-redis-using-the-azure-classic-cli"></a>Jak vytvořit a spravovat službu Azure cache pro Redis s využitím rozhraní příkazového řádku Azure Classic
> [!div class="op_single_selector"]
> * [PowerShell](cache-how-to-manage-redis-cache-powershell.md)
> * [Rozhraní příkazového řádku Azure Classic](cache-manage-cli.md)
>

Azure Classic CLI představuje skvělý způsob, jak spravovat infrastrukturu Azure z jakékoli platformy. V tomto článku se dozvíte, jak vytvořit a spravovat mezipaměť Azure pro instance Redis pomocí Azure Classic CLI.

[!INCLUDE [outdated-cli-content](../../includes/contains-classic-cli-content.md)]
> [!NOTE]
> Nejnovější ukázkové skripty Azure CLI najdete v tématu [ukázky Redis Azure CLI Azure cache](cli-samples.md).

## <a name="prerequisites"></a>Požadavky
Pokud chcete vytvořit a spravovat službu Azure cache pro instance Redis pomocí Azure Classic CLI, musíte provést následující kroky.

* Musíte mít účet Azure. Pokud ho nemáte, můžete si během chvilky vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) .
* [Nainstalujte rozhraní příkazového řádku Azure Classic](/cli/azure/install-classic-cli).
* Připojte si instalaci Azure CLI pomocí osobního účtu Azure nebo pomocí pracovního nebo školního účtu Azure a přihlaste se z klasického rozhraní `azure login` příkazového řádku pomocí příkazu.
* Před spuštěním kteréhokoli z následujících příkazů přepněte klasický příkaz CLI do režimu Správce prostředků spuštěním `azure config mode arm` příkazu. Další informace najdete v tématu věnovaném [použití rozhraní příkazového řádku Azure Classic ke správě prostředků a skupin prostředků Azure](../azure-resource-manager/management/manage-resources-cli.md).

## <a name="azure-cache-for-redis-properties"></a>Azure cache pro vlastnosti Redis
Při vytváření a aktualizaci mezipaměti Azure pro instance Redis se používají následující vlastnosti.

| Vlastnost | Přepínač | Popis |
| --- | --- | --- |
| name |-n,--Name |Název mezipaměti Azure pro Redis. |
| skupina prostředků |-g,--Resource-Group |Název skupiny prostředků. |
| location |-l,--Location |Umístění pro vytvoření mezipaměti |
| size |– z,--velikost |Velikost mezipaměti Azure pro Redis. Platné hodnoty: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4] |
| skladové |-x,--SKU |Skladová položka Redis. Musí mít jednu z těchto: [Basic, Standard, Premium] |
| EnableNonSslPort |-e,--Enable-bez SSL-port |Vlastnost EnableNonSslPort mezipaměti Azure pro Redis. Přidejte tento příznak, pokud chcete pro mezipaměť povolit port bez TLS/SSL. |
| Konfigurace Redis |-c,--Redis-konfigurace |Konfigurace Redis. Sem zadejte řetězec konfiguračních klíčů a hodnot formátu JSON. Formát: "{" ":" "," ":" "}" |
| Konfigurace Redis |-f,--Redis-Configuration-File |Konfigurace Redis. Sem zadejte cestu k souboru obsahujícímu konfigurační klíče a hodnoty. Formát pro položku souboru: {"": "", "" ":" "} |
| Počet horizontálních oddílů |-r,--horizontálních oddílů-Count |Počet horizontálních oddílů, které se mají vytvořit v mezipaměti clusteru Premium s clusteringem |
| Virtual Network |-v,--Virtual-Network |Při hostování mezipaměti ve virtuální síti určuje přesné ID prostředku ARM virtuální sítě, ve kterém se nasadí mezipaměť Azure pro Redis. Příklad formátu:/subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| typ klíče |-t,--typ klíče |Typ klíče, který se má obnovit Platné hodnoty: [primární, sekundární] |
| StaticIP |-p,--Static-IP \<static-ip\> |Při hostování mezipaměti ve virtuální síti určuje jedinečná IP adresa v podsíti pro mezipaměť. Pokud tato možnost není k dispozici, je pro vás z podsítě zvolena jedna. |
| Podsíť |t,--Subnet \<subnet\> |Při hostování mezipaměti ve virtuální síti Určuje název podsítě, do které se má mezipaměť nasadit. |
| VirtualNetwork |-v,--Virtual-Network \<virtual-network\> |Při hostování mezipaměti ve virtuální síti určuje přesné ID prostředku ARM virtuální sítě, ve kterém se nasadí mezipaměť Azure pro Redis. Příklad formátu:/subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| Předplatné |-s,--předplatné |Identifikátor předplatného. |

## <a name="see-all-azure-cache-for-redis-commands"></a>Zobrazit všechny příkazy Azure cache pro Redis
Pokud chcete zobrazit všechny příkazy Azure cache for Redis a jejich parametry, použijte `azure rediscache -h` příkaz.

```azurecli
C:\>azure rediscache -h
help:    Commands to manage your Azure Cache for Redis(s)
help:
help:    Create an Azure Cache for Redis
help:      rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
help:
help:    Delete an existing Azure Cache for Redis
help:      rediscache delete [--name <name> --resource-group <resource-group> ]
help:
help:    List all Azure Cache for Redis within your Subscription or Resource Group
help:      rediscache list [options]
help:
help:    Show properties of an existing Azure Cache for Redis
help:      rediscache show [--name <name> --resource-group <resource-group>]
help:
help:    Change settings of an existing Azure Cache for Redis
help:      rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
help:
help:    Renew the authentication key for an existing Azure Cache for Redis
help:      rediscache renew-key [--name <name> --resource-group <resource-group> ]
help:
help:    Lists Primary and Secondary key of an existing Azure Cache for Redis
help:      rediscache list-keys [--name <name> --resource-group <resource-group>]
help:
help:    Options:
help:      -h, --help  output usage information
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="create-an-azure-cache-for-redis"></a>Vytvoření instance Azure Cache for Redis
K vytvoření mezipaměti Azure pro Redis použijte následující příkaz:

```azurecli
    azure rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
```

Další informace o tomto příkazu získáte spuštěním `azure rediscache create -h` příkazu.

```azurecli
C:\>azure rediscache create -h
help:    Create an Azure Cache for Redis
help:
help:    Usage: rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
help:
help:    Options:
help:      -h, --help                                               output usage information
help:      -v, --verbose                                            use verbose output
help:      -vv                                                      more verbose with debug output
help:      --json                                                   use json output
help:      -n, --name <name>                                        Name of the Azure Cache for Redis.
help:      -g, --resource-group <resource-group>                    Name of the Resource Group
help:      -l, --location <location>                                Location to create cache.
help:      -z, --size <size>                                        Size of the Azure Cache for Redis. Valid values: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4]
help:      -x, --sku <sku>                                          Redis SKU. Should be one of : [Basic, Standard, Premium]
help:      -e, --enable-non-ssl-port                                EnableNonSslPort property of the Azure Cache for Redis. Add this flag if you want to enable the non-TLS/SSL Port for your cache
help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here. Format:"{"<key1>":"<value1>","<key2>":"<value2>"}"
help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here. Format for the file entry: {"<key1>":"<value1>","<key2>":"<value2>"}
help:      -r, --shard-count <shard-count>                          Number of Shards to create on a Premium Cluster Cache
help:      -v, --virtual-network <virtual-network>                  The exact ARM resource ID of the virtual network to deploy the Azure Cache for Redis in. Example format: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1
help:      -t, --subnet <subnet>                                    Required when deploying an Azure Cache for Redis inside an existing Azure Virtual Network
help:      -p, --static-ip <static-ip>                              Required when deploying an Azure Cache for Redis inside an existing Azure Virtual Network
help:      -s, --subscription <id>                                  the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="delete-an-existing-azure-cache-for-redis"></a>Odstraní stávající mezipaměť Azure pro Redis.
K odstranění mezipaměti Azure pro Redis použijte následující příkaz:

```azurecli
    azure rediscache delete [--name <name> --resource-group <resource-group> ]
```

Další informace o tomto příkazu získáte spuštěním `azure rediscache delete -h` příkazu.

```azurecli
C:\>azure rediscache delete -h
help:    Delete an existing Azure Cache for Redis
help:
help:    Usage: rediscache delete [--name <name> --resource-group <resource-group> ]
help:
help:    Options:
help:      -h, --help                             output usage information
help:      -v, --verbose                          use verbose output
help:      -vv                                    more verbose with debug output
help:      --json                                 use json output
help:      -n, --name <name>                      Name of the Azure Cache for Redis.
help:      -g, --resource-group <resource-group>  Name of the Resource Group under which the cache exists
help:      -s, --subscription <subscription>      the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="list-all-azure-cache-for-redis-within-your-subscription-or-resource-group"></a>Vypíše všechny služby Azure cache for Redis v rámci předplatného nebo skupiny prostředků.
Pokud chcete zobrazit seznam všech Azure cache for Redis v rámci předplatného nebo skupiny prostředků, použijte následující příkaz:

```azurecli
    azure rediscache list [options]
```

Další informace o tomto příkazu získáte spuštěním `azure rediscache list -h` příkazu.

```azurecli
C:\>azure rediscache list -h
help:    List all Azure Cache for Redis within your Subscription or Resource Group
help:
help:    Usage: rediscache list [options]
help:
help:    Options:
help:      -h, --help                             output usage information
help:      -v, --verbose                          use verbose output
help:      -vv                                    more verbose with debug output
help:      --json                                 use json output
help:      -g, --resource-group <resource-group>  Name of the Resource Group
help:      -s, --subscription <subscription>      the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="show-properties-of-an-existing-azure-cache-for-redis"></a>Zobrazit vlastnosti existující mezipaměti Azure pro Redis
Chcete-li zobrazit vlastnosti existující mezipaměti Azure pro Redis, použijte následující příkaz:

```azurecli
    azure rediscache show [--name <name> --resource-group <resource-group>]
```

Další informace o tomto příkazu získáte spuštěním `azure rediscache show -h` příkazu.

```azurecli
C:\>azure rediscache show -h
help:    Show properties of an existing Azure Cache for Redis
help:
help:    Usage: rediscache show [--name <name> --resource-group <resource-group>]
help:
help:    Options:
help:      -h, --help                             output usage information
help:      -v, --verbose                          use verbose output
help:      -vv                                    more verbose with debug output
help:      --json                                 use json output
help:      -n, --name <name>                      Name of the Azure Cache for Redis.
help:      -g, --resource-group <resource-group>  Name of the Resource Group
help:      -s, --subscription <subscription>      the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

<a name="scale"></a>

## <a name="change-settings-of-an-existing-azure-cache-for-redis"></a>Změna nastavení stávající mezipaměti Azure pro Redis
Pokud chcete změnit nastavení existující mezipaměti Azure pro Redis, použijte následující příkaz:

```azurecli
    azure rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
```

Další informace o tomto příkazu získáte spuštěním `azure rediscache set -h` příkazu.

```azurecli
C:\>azure rediscache set -h
help:    Change settings of an existing Azure Cache for Redis
help:
help:    Usage: rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
help:
help:    Options:
help:      -h, --help                                               output usage information
help:      -v, --verbose                                            use verbose output
help:      -vv                                                      more verbose with debug output
help:      --json                                                   use json output
help:      -n, --name <name>                                        Name of the Azure Cache for Redis.
help:      -g, --resource-group <resource-group>                    Name of the Resource Group
help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here.
help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here.
help:      -s, --subscription <subscription>                        the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="renew-the-authentication-key-for-an-existing-azure-cache-for-redis"></a>Obnovení ověřovacího klíče pro stávající mezipaměť Azure pro Redis
K obnovení ověřovacího klíče pro stávající mezipaměť Azure pro Redis použijte následující příkaz:

```azurecli
    azure rediscache renew-key [--name <name> --resource-group <resource-group> --key-type <key-type>]
```

Zadejte `Primary` nebo `Secondary` pro `key-type` .

Další informace o tomto příkazu získáte spuštěním `azure rediscache renew-key -h` příkazu.

```azurecli
C:\>azure rediscache renew-key -h
help:    Renew the authentication key for an existing Azure Cache for Redis
help:
help:    Usage: rediscache renew-key [--name <name> --resource-group <resource-group> ]
help:
help:    Options:
help:      -h, --help                             output usage information
help:      -v, --verbose                          use verbose output
help:      -vv                                    more verbose with debug output
help:      --json                                 use json output
help:      -n, --name <name>                      Name of the Azure Cache for Redis.
help:      -g, --resource-group <resource-group>  Name of the Resource Group under which cache exists
help:      -t, --key-type <key-type>              type of key to renew. Valid values are: 'Primary', 'Secondary'.
help:      -s, --subscription <subscription>      the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="list-primary-and-secondary-keys-of-an-existing-azure-cache-for-redis"></a>Seznam primárních a sekundárních klíčů existující mezipaměti Azure pro Redis
K vypsání primárního a sekundárního klíče existující mezipaměti Azure pro Redis použijte následující příkaz:

```azurecli
    azure rediscache list-keys [--name <name> --resource-group <resource-group>]
```

Další informace o tomto příkazu získáte spuštěním `azure rediscache list-keys -h` příkazu.

```azurecli
C:\>azure rediscache list-keys -h
help:    Lists Primary and Secondary key of an existing Azure Cache for Redis
help:
help:    Usage: rediscache list-keys [--name <name> --resource-group <resource-group>]
help:
help:    Options:
help:      -h, --help                             output usage information
help:      -v, --verbose                          use verbose output
help:      -vv                                    more verbose with debug output
help:      --json                                 use json output
help:      -n, --name <name>                      Name of the Azure Cache for Redis.
help:      -g, --resource-group <resource-group>  Name of the Resource Group under which Cache exists
help:      -s, --subscription <subscription>      the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```
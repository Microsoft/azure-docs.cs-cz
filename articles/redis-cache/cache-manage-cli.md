---
title: Správa Azure Redis Cache pomocí rozhraní příkazového řádku Azure | Microsoft Docs
description: Zjistěte, jak nainstalovat rozhraní příkazového řádku Azure na jakékoli platformě, jak používat pro připojení k účtu Azure a jak vytvořit a spravovat mezipaměti Redis z příkazového řádku Azure.
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 964ff245-859d-4bc1-bccf-62e4b3c1169f
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: wesmc
ms.openlocfilehash: fdb0989af2215166b69f10474a0d22aab7b4d593
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2018
ms.locfileid: "27911274"
---
# <a name="how-to-create-and-manage-azure-redis-cache-using-the-azure-command-line-interface-azure-cli"></a>Jak vytvořit a spravovat Azure Redis Cache pomocí rozhraní příkazového řádku Azure (Azure CLI)
> [!div class="op_single_selector"]
> * [PowerShell](cache-howto-manage-redis-cache-powershell.md)
> * [Azure CLI](cache-manage-cli.md)
>
>

Rozhraní příkazového řádku Azure je skvělým způsobem, jak spravovat infrastrukturu Azure z libovolné platformě. Tento článek ukazuje, jak vytvořit a spravovat vaše instance služby Azure Redis Cache pomocí rozhraní příkazového řádku Azure.

> [!NOTE]
> Tento článek se týká předchozí verze rozhraní příkazového řádku Azure. Nejnovější ukázkové skripty Azure CLI 2.0, naleznete v části [ukázky rozhraní příkazového řádku služby Azure Redis cache](cli-samples.md).
> 
> 

## <a name="prerequisites"></a>Požadavky
Chcete-li vytvořit a spravovat instance služby Azure Redis Cache pomocí rozhraní příkazového řádku Azure, musíte provést následující kroky.

* Musí mít účet Azure. Pokud nemáte, můžete vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) za pár chvil.
* [Instalace rozhraní příkazového řádku Azure CLI](../cli-install-nodejs.md).
* Připojit vaše instalace rozhraní příkazového řádku Azure osobní účet Azure, nebo s pracovní nebo školní účet Azure a přihlaste se pomocí rozhraní příkazového řádku Azure `azure login` příkaz. Porozumět rozdílům a zvolte najdete v tématu [připojení k předplatnému Azure z rozhraní příkazového řádku (Azure CLI)](/cli/azure/authenticate-azure-cli).
* Před s některým z následujících příkazů, přepínač příkazového řádku Azure CLI do režimu Resource Manager tak, že spustíte `azure config mode arm` příkaz. Další informace najdete v tématu [používat rozhraní příkazového řádku Azure ke správě prostředků Azure a skupiny prostředků](../xplat-cli-azure-resource-manager.md).

## <a name="redis-cache-properties"></a>Vlastnosti mezipaměti redis
Následující vlastnosti se používají při vytváření nebo aktualizaci instance služby Redis Cache.

| Vlastnost | Přepínač | Popis |
| --- | --- | --- |
| jméno |-n, --name |Název mezipaměti Redis. |
| skupina prostředků |-g, – skupinu prostředků |Název skupiny prostředků. |
| location |-l, – umístění |Umístění pro vytvoření mezipaměti. |
| velikost |-z, --size |Velikost mezipaměti Redis. Platné hodnoty: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4] |
| sku |-x, --sku |Redis SKU. By měl být jeden z: [Basic, Standard, Premium] |
| EnableNonSslPort |-e, – enable bez--port ssl |Vlastnost EnableNonSslPort Redis Cache. Přidejte tento příznak, pokud chcete povolit Port bez SSL pro mezipaměť |
| Konfigurace redis |-c, --redis-configuration |Redis konfigurace. Zadejte řetězec formátu JSON konfigurace klíčů a hodnot v tomto poli. Formát: "{" ":""," ":" "}" |
| Konfigurace redis |-f, – redis. konfigurační soubor |Redis konfigurace. Zadejte cestu k souboru obsahující konfigurační klíče a hodnoty v tomto poli. Formát vstupního souboru: {"": "","": ""} |
| Počet horizontálních |-r, – počet horizontálních |Počet horizontálních oddílů vytvořit na clusteru Cache ve verzi Premium s clusteringem. |
| Virtual Network |-v, --virtual-network |Při hostování vaší mezipaměti ve virtuální síti, určuje přesné ARM ID prostředku virtuální sítě pro nasazení mezipaměti redis v. Příklad formátu: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| Typ klíče |-t, – typ klíče |Typ klíče pro obnovení. Platné hodnoty: [primární, sekundární] |
| StaticIP |-p, – statické ip < statické ip > |Při hostování vaší mezipaměti ve virtuální síti, určuje jedinečnou IP adresu v mezipaměti v podsíti. Pokud není zadaná, jeden z podsítě vybrali za vás. |
| Podsíť |t, --subnet <subnet> |Při hostování vaší mezipaměti ve virtuální síti, určuje název podsítě, ve které chcete nasadit do mezipaměti. |
| VirtualNetwork |-v, – virtuální sítě < virtuálních sítí > |Při hostování vaší mezipaměti ve virtuální síti, určuje přesné ARM ID prostředku virtuální sítě pro nasazení mezipaměti redis v. Příklad formátu: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| Předplatné |-s, – předplatné |Identifikátor předplatného. |

## <a name="see-all-redis-cache-commands"></a>Zobrazit všechny příkazy pro Redis Cache
Pokud chcete zobrazit všechny příkazy Redis Cache a jejich parametrů, použijte `azure rediscache -h` příkaz.

    C:\>azure rediscache -h
    help:    Commands to manage your Azure Redis Cache(s)
    help:
    help:    Create a Redis Cache
    help:      rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
    help:
    help:    Delete an existing Redis Cache
    help:      rediscache delete [--name <name> --resource-group <resource-group> ]
    help:
    help:    List all Redis Caches within your Subscription or Resource Group
    help:      rediscache list [options]
    help:
    help:    Show properties of an existing Redis Cache
    help:      rediscache show [--name <name> --resource-group <resource-group>]
    help:
    help:    Change settings of an existing Redis Cache
    help:      rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
    help:
    help:    Renew the authentication key for an existing Redis Cache
    help:      rediscache renew-key [--name <name> --resource-group <resource-group> ]
    help:
    help:    Lists Primary and Secondary key of an existing Redis Cache
    help:      rediscache list-keys [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help  output usage information
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="create-a-redis-cache"></a>Vytvoření Redis Cache
Chcete-li vytvořit Redis Cache, použijte následující příkaz:

    azure rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]

Další informace o tomto příkazu, spusťte `azure rediscache create -h` příkaz.

    C:\>azure rediscache create -h
    help:    Create a Redis Cache
    help:
    help:    Usage: rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
    help:
    help:    Options:
    help:      -h, --help                                               output usage information
    help:      -v, --verbose                                            use verbose output
    help:      -vv                                                      more verbose with debug output
    help:      --json                                                   use json output
    help:      -n, --name <name>                                        Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>                    Name of the Resource Group
    help:      -l, --location <location>                                Location to create cache.
    help:      -z, --size <size>                                        Size of the Redis Cache. Valid values: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4]
    help:      -x, --sku <sku>                                          Redis SKU. Should be one of : [Basic, Standard, Premium]
    help:      -e, --enable-non-ssl-port                                EnableNonSslPort property of the Redis Cache. Add this flag if you want to enable the Non SSL Port for your cache
    help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here. Format:"{"<key1>":"<value1>","<key2>":"<value2>"}"
    help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here. Format for the file entry: {"<key1>":"<value1>","<key2>":"<value2>"}
    help:      -r, --shard-count <shard-count>                          Number of Shards to create on a Premium Cluster Cache
    help:      -v, --virtual-network <virtual-network>                  The exact ARM resource ID of the virtual network to deploy the redis cache in. Example format: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1
    help:      -t, --subnet <subnet>                                    Required when deploying a redis cache inside an existing Azure Virtual Network
    help:      -p, --static-ip <static-ip>                              Required when deploying a redis cache inside an existing Azure Virtual Network
    help:      -s, --subscription <id>                                  the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="delete-an-existing-redis-cache"></a>Odstraňte existující Redis Cache
Pokud chcete odstranit Redis Cache, použijte následující příkaz:

    azure rediscache delete [--name <name> --resource-group <resource-group> ]

Další informace o tomto příkazu, spusťte `azure rediscache delete -h` příkaz.

    C:\>azure rediscache delete -h
    help:    Delete an existing Redis Cache
    help:
    help:    Usage: rediscache delete [--name <name> --resource-group <resource-group> ]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which the cache exists
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="list-all-redis-caches-within-your-subscription-or-resource-group"></a>Zobrazí seznam všech mezipaměti Redis v rámci předplatného nebo skupinu prostředků
K zobrazení seznamu všech mezipaměti Redis v rámci předplatného nebo skupinu prostředků, použijte následující příkaz:

    azure rediscache list [options]

Další informace o tomto příkazu, spusťte `azure rediscache list -h` příkaz.

    C:\>azure rediscache list -h
    help:    List all Redis Caches within your Subscription or Resource Group
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

## <a name="show-properties-of-an-existing-redis-cache"></a>Zobrazit vlastnosti existující Redis Cache
Chcete-li zobrazit vlastnosti existující Redis Cache, použijte následující příkaz:

    azure rediscache show [--name <name> --resource-group <resource-group>]

Další informace o tomto příkazu, spusťte `azure rediscache show -h` příkaz.

    C:\>azure rediscache show -h
    help:    Show properties of an existing Redis Cache
    help:
    help:    Usage: rediscache show [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

<a name="scale"></a>

## <a name="change-settings-of-an-existing-redis-cache"></a>Změňte nastavení existující Redis Cache
Chcete-li změnit nastavení existujícího Redis Cache, použijte následující příkaz:

    azure rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]

Další informace o tomto příkazu, spusťte `azure rediscache set -h` příkaz.

    C:\>azure rediscache set -h
    help:    Change settings of an existing Redis Cache
    help:
    help:    Usage: rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
    help:
    help:    Options:
    help:      -h, --help                                               output usage information
    help:      -v, --verbose                                            use verbose output
    help:      -vv                                                      more verbose with debug output
    help:      --json                                                   use json output
    help:      -n, --name <name>                                        Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>                    Name of the Resource Group
    help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here.
    help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here.
    help:      -s, --subscription <subscription>                        the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="renew-the-authentication-key-for-an-existing-redis-cache"></a>Obnovit ověřovací klíč pro existující Redis Cache
Chcete-li obnovit ověřovací klíč pro existující Redis Cache, použijte následující příkaz:

    azure rediscache renew-key [--name <name> --resource-group <resource-group> --key-type <key-type>]

Zadejte `Primary` nebo `Secondary` pro `key-type`.

Další informace o tomto příkazu, spusťte `azure rediscache renew-key -h` příkaz.

    C:\>azure rediscache renew-key -h
    help:    Renew the authentication key for an existing Redis Cache
    help:
    help:    Usage: rediscache renew-key [--name <name> --resource-group <resource-group> ]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which cache exists
    help:      -t, --key-type <key-type>              type of key to renew. Valid values are: 'Primary', 'Secondary'.
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="list-primary-and-secondary-keys-of-an-existing-redis-cache"></a>Seznam primární a sekundární klíče existující mezipaměti Redis
Do seznamu primární a sekundární klíče existující Redis Cache použijte následující příkaz:

    azure rediscache list-keys [--name <name> --resource-group <resource-group>]

Další informace o tomto příkazu, spusťte `azure rediscache list-keys -h` příkaz.

    C:\>azure rediscache list-keys -h
    help:    Lists Primary and Secondary key of an existing Redis Cache
    help:
    help:    Usage: rediscache list-keys [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which Cache exists
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

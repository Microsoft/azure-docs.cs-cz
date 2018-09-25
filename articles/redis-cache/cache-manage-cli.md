---
title: Správa Azure Redis Cache pomocí příkazového řádku Azure classic | Dokumentace Microsoftu
description: Zjistěte, jak nainstalovat Azure classic CLI na libovolné platformě, jak používat pro připojení k vašemu účtu Azure a tom, jak vytvořit a spravovat Redis cache z rozhraní příkazového řádku classic.
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
ms.openlocfilehash: 0e8bbaad920f35028c51641779a3272f73f81f37
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46978399"
---
# <a name="how-to-create-and-manage-azure-redis-cache-using-the-azure-classic-cli"></a>Jak vytvořit a spravovat Azure Redis Cache pomocí Azure classic CLI
> [!div class="op_single_selector"]
> * [PowerShell](cache-howto-manage-redis-cache-powershell.md)
> * [Klasické rozhraní příkazového řádku Azure](cache-manage-cli.md)
>

Azure classic CLI je skvělý způsob, jak spravovat infrastrukturu Azure z libovolné platformy. V tomto článku se dozvíte, jak vytvořit a spravovat vaše instance Azure Redis Cache pomocí Azure classic CLI.

[!INCLUDE [outdated-cli-content](../../includes/contains-classic-cli-content.md)]
> [!NOTE]
> Nejnovější ukázkové skripty rozhraní příkazového řádku Azure, najdete v části [ukázky rozhraní příkazového řádku služby Azure Redis cache](cli-samples.md).

## <a name="prerequisites"></a>Požadavky
Vytvoření a správa instancí služby Azure Redis Cache pomocí příkazového řádku Azure classic, musíte dokončit následující kroky.

* Musíte mít účet Azure. Pokud ho nemáte, můžete vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) za několik okamžiků.
* [Instalace Azure classic CLI](../cli-install-nodejs.md).
* Připojení vaší instalace rozhraní příkazového řádku Azure pomocí osobního účtu Azure, nebo pracovní nebo školní účet Azure a přihlášení z classic pomocí rozhraní příkazového řádku `azure login` příkazu.
* Před spuštěním následujících příkazů, přepněte rozhraní příkazového řádku classic do režimu Resource Manageru spuštěním `azure config mode arm` příkazu. Další informace najdete v tématu [použití Azure classic CLI ke správě prostředků a skupin prostředků Azure](../xplat-cli-azure-resource-manager.md).

## <a name="redis-cache-properties"></a>Vlastnosti mezipaměti redis
Následující vlastnosti se používají při vytváření nebo aktualizaci instance služby Redis Cache.

| Vlastnost | Přepínač | Popis |
| --- | --- | --- |
| jméno |-n, --name |Název mezipaměti Redis. |
| skupina prostředků |-g,--& gt; resource-group |Název skupiny prostředků. |
| location |-l,--umístění |Umístění pro vytvoření mezipaměti. |
| velikost |-z, --size |Velikost mezipaměti Redis. Platné hodnoty: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4] |
| Skladová položka |-x, --sku |Redis SKU. By měla být jedna z: [Basic, Standard, Premium] |
| EnableNonSslPort |-e,--enable bez--port ssl |Vlastnost EnableNonSslPort služby Redis Cache. Přidejte tento příznak, pokud chcete povolit Port bez SSL ke svojí mezipaměti |
| Konfigurace mezipaměti redis |-c, - redis konfigurace |Konfigurace mezipaměti redis. Zadejte řetězec ve formátu JSON konfigurace klíčů a hodnot v tomto poli. Formát: "{" ":""," ":" "}" |
| Konfigurace mezipaměti redis |-f,--redis. konfigurační soubor |Konfigurace mezipaměti redis. Zadejte cestu k souboru, který obsahuje konfigurační klíče a hodnoty tady. Formát pro vstupní soubor: {"": "","": ""} |
| Počet horizontálních oddílů |-r,--počet horizontálních oddílů |Počet horizontálních oddílů k vytvoření v clusteru mezipaměti úrovně Premium s clusteringem. |
| Virtual Network |-v,--virtuální sítě |Při hostování mezipaměti ve virtuální síti, určuje přesné ARM ID prostředku virtuální sítě pro nasazení mezipaměti redis. Příklad formátu: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| Typ klíče |-t,--key-type |Typ klíče pro obnovení. Platné hodnoty: [primární, sekundární] |
| StaticIP |-p, – statické ip < statické ip > |Při hostování mezipaměti ve virtuální síti, určuje jedinečnou IP adresu v podsíti pro ukládání do mezipaměti. Pokud se nezadá, jeden je vybrán pro vás z podsítě. |
| Podsíť |t, – podsítě <subnet> |Při hostování mezipaměti ve virtuální síti, určuje název podsítě, ve které se má nasadit do mezipaměti. |
| VirtualNetwork |-v, – virtuální sítě < virtual-network > |Při hostování mezipaměti ve virtuální síti, určuje přesné ARM ID prostředku virtuální sítě pro nasazení mezipaměti redis. Příklad formátu: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| Předplatné |-s, – předplatné |Identifikátor předplatného. |

## <a name="see-all-redis-cache-commands"></a>Zobrazit všechny příkazy Redis Cache
Chcete-li zobrazit všechny příkazy Redis Cache a jejich parametrů, použijte `azure rediscache -h` příkazu.

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
Vytvoření mezipaměti redis cache, použijte následující příkaz:

    azure rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]

Další informace o tomto příkazu Spustit `azure rediscache create -h` příkazu.

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

## <a name="delete-an-existing-redis-cache"></a>Odstranit existující služby Redis Cache
Pokud chcete odstranit Redis Cache, použijte následující příkaz:

    azure rediscache delete [--name <name> --resource-group <resource-group> ]

Další informace o tomto příkazu Spustit `azure rediscache delete -h` příkazu.

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

## <a name="list-all-redis-caches-within-your-subscription-or-resource-group"></a>Vypsat všechny mezipaměti Redis v rámci předplatného nebo skupiny prostředků
Chcete-li vypsat všechny mezipaměti Redis v rámci předplatného nebo skupiny prostředků, použijte následující příkaz:

    azure rediscache list [options]

Další informace o tomto příkazu Spustit `azure rediscache list -h` příkazu.

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

## <a name="show-properties-of-an-existing-redis-cache"></a>Zobrazit vlastnosti existující služby Redis Cache
Chcete-li zobrazit vlastnosti existující služby Redis cache, použijte následující příkaz:

    azure rediscache show [--name <name> --resource-group <resource-group>]

Další informace o tomto příkazu Spustit `azure rediscache show -h` příkazu.

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

## <a name="change-settings-of-an-existing-redis-cache"></a>Změnit nastavení existující služby Redis Cache
Chcete-li změnit nastavení existující služby Redis cache, použijte následující příkaz:

    azure rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]

Další informace o tomto příkazu Spustit `azure rediscache set -h` příkazu.

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

## <a name="renew-the-authentication-key-for-an-existing-redis-cache"></a>Obnovit ověřovací klíč pro existující mezipaměť redis Cache
Chcete-li obnovit ověřovací klíč pro existující mezipaměť redis cache, použijte následující příkaz:

    azure rediscache renew-key [--name <name> --resource-group <resource-group> --key-type <key-type>]

Zadejte `Primary` nebo `Secondary` pro `key-type`.

Další informace o tomto příkazu Spustit `azure rediscache renew-key -h` příkazu.

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

## <a name="list-primary-and-secondary-keys-of-an-existing-redis-cache"></a>Seznam primárního a sekundárního klíče existující služby Redis Cache
Do seznamu primárního a sekundárního klíče stávající mezipaměti redis Cache použijte následující příkaz:

    azure rediscache list-keys [--name <name> --resource-group <resource-group>]

Další informace o tomto příkazu Spustit `azure rediscache list-keys -h` příkazu.

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

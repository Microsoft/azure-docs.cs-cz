---
title: Správa Azure Cache pro Redis pomocí klasického příkazového příkazového příkazu Azure
description: Zjistěte, jak nainstalovat klasické příkazové příkazové příkazové příkazy Azure na libovolnou platformu, jak ji použít k připojení k účtu Azure a jak vytvořit a spravovat Azure Cache pro Redis z klasického příkazového příkazového příkazu.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 01/23/2017
ms.author: yegu
ms.openlocfilehash: e2b1ed693ea57e3414d465a57a5ba2b1203f67c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277983"
---
# <a name="how-to-create-and-manage-azure-cache-for-redis-using-the-azure-classic-cli"></a>Jak vytvořit a spravovat Azure Cache pro Redis pomocí klasického příkazového příkazového příkazu Azure
> [!div class="op_single_selector"]
> * [PowerShell](cache-how-to-manage-redis-cache-powershell.md)
> * [Azure Classic CLI](cache-manage-cli.md)
>

Klasické příkazové příkazové příkazové příkazové příkazy Azure je skvělý způsob, jak spravovat infrastrukturu Azure z libovolné platformy. Tento článek ukazuje, jak vytvořit a spravovat azure cache pro redis instance pomocí klasické holicí příkazové kliby azure.

[!INCLUDE [outdated-cli-content](../../includes/contains-classic-cli-content.md)]
> [!NOTE]
> Nejnovější ukázkové skripty Azure CLI najdete v [tématu Azure CLI Azure Cache for Redis ukázky](cli-samples.md).

## <a name="prerequisites"></a>Požadavky
Pokud chcete vytvořit a spravovat instance Azure Cache for Redis pomocí klasického příkazového příkazového příkazu Konstatování Azure, musíte provést následující kroky.

* Musíte mít účet Azure. Pokud ho nemáte, můžete si za pár okamžiků vytvořit [bezplatný účet.](https://azure.microsoft.com/pricing/free-trial/)
* [Nainstalujte klasické příkazové příkazové příkazové příkazové příkazy Azure](../cli-install-nodejs.md).
* Připojte instalaci azure cli s osobním účtem Azure nebo s pracovním nebo školním účtem `azure login` Azure a přihlaste se z klasického příkazového příkazu příkazu.
* Před spuštěním některého z následujících příkazů přepněte klasické příkazové příkazové příkazy do režimu Správce prostředků spuštěním příkazu. `azure config mode arm` Další informace najdete [v tématu Použití klasického příkazového příkazového příkazového příkazu Azure ke správě prostředků azure a skupin prostředků](../xplat-cli-azure-resource-manager.md).

## <a name="azure-cache-for-redis-properties"></a>Azure Cache pro vlastnosti Redis
Následující vlastnosti se používají při vytváření a aktualizaci azure cache pro redis instance.

| Vlastnost | Přepínač | Popis |
| --- | --- | --- |
| jméno |-n, --název |Název mezipaměti Azure pro Redis. |
| skupina prostředků |-g, --skupina prostředků |Název skupiny prostředků. |
| location |-l, --umístění |Umístění pro vytvoření mezipaměti. |
| velikost |-z, --velikost |Velikost mezipaměti Azure pro Redis. Platné hodnoty: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4] |
| Sku |-x, --sku |Skladová položka Redis. By měl být jedním z: [Basic, Standard, Premium] |
| EnableNonSslPort |-e, --enable-non-ssl-port |Vlastnost EnableNonSslPort služby Azure Cache for Redis. Tento příznak přidejte, pokud chcete povolit port Non SSL pro vaši mezipaměť |
| Konfigurace Redis |-c, --redis-konfigurace |Redis konfigurace. Zde zadejte řetězec konfiguračních klíčů a hodnot ve formátu JSON. Formát:"{"":"","":""}" |
| Konfigurace Redis |-f, --redis-configuration-file |Redis konfigurace. Zde zadejte cestu k souboru obsahujícího konfigurační klíče a hodnoty. Formát pro položku souboru: {"":"",","":""}} |
| Počet úlomků |-r, --počet úlomků |Počet úlomků stelivek, které mají být v mezipaměť premium clusteru s clusteringem. |
| Virtual Network |-v, --virtuální síť |Při hostování mezipaměti ve virtuální síti určuje přesné ID prostředků ARM virtuální sítě pro nasazení Azure Cache for Redis. Příklad formátu: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| typ klíče |-t, --typ klíče |Typ klíče, který chcete obnovit. Platné hodnoty: [Primární, Sekundární] |
| Statické IP adresy |-p, --static-ip \<static-ip\> |Při hostování mezipaměti ve virtuální síti určuje jedinečnou ADRESU IP v podsíti pro mezipaměť. Pokud není k dispozici, jeden je vybrán pro vás z podsítě. |
| Podsíť |t, --podsíť podsítě \<\> |Při hostování mezipaměti ve virtuální síti určuje název podsítě, ve které chcete ji nasadit. |
| VirtualNetwork |-v, --virtuální \<síť virtuální sítě\> |Při hostování mezipaměti ve virtuální síti určuje přesné ID prostředků ARM virtuální sítě pro nasazení Azure Cache for Redis. Příklad formátu: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| Předplatné |-s, --předplatné |Identifikátor předplatného. |

## <a name="see-all-azure-cache-for-redis-commands"></a>Zobrazit všechny příkazy Azure Cache for Redis
Chcete-li zobrazit všechny příkazy Azure Cache for `azure rediscache -h` Redis a jejich parametry, použijte příkaz.

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

## <a name="create-an-azure-cache-for-redis"></a>Vytvoření instance Azure Cache for Redis
Chcete-li vytvořit mezipaměť Azure pro redis, použijte následující příkaz:

    azure rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]

Další informace o tomto příkazu získáte spuštěním příkazu. `azure rediscache create -h`

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
    help:      -e, --enable-non-ssl-port                                EnableNonSslPort property of the Azure Cache for Redis. Add this flag if you want to enable the Non SSL Port for your cache
    help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here. Format:"{"<key1>":"<value1>","<key2>":"<value2>"}"
    help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here. Format for the file entry: {"<key1>":"<value1>","<key2>":"<value2>"}
    help:      -r, --shard-count <shard-count>                          Number of Shards to create on a Premium Cluster Cache
    help:      -v, --virtual-network <virtual-network>                  The exact ARM resource ID of the virtual network to deploy the Azure Cache for Redis in. Example format: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1
    help:      -t, --subnet <subnet>                                    Required when deploying an Azure Cache for Redis inside an existing Azure Virtual Network
    help:      -p, --static-ip <static-ip>                              Required when deploying an Azure Cache for Redis inside an existing Azure Virtual Network
    help:      -s, --subscription <id>                                  the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="delete-an-existing-azure-cache-for-redis"></a>Odstranění existující mezipaměti Azure pro Redis
Chcete-li odstranit mezipaměť Azure pro redis, použijte následující příkaz:

    azure rediscache delete [--name <name> --resource-group <resource-group> ]

Další informace o tomto příkazu získáte spuštěním příkazu. `azure rediscache delete -h`

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

## <a name="list-all-azure-cache-for-redis-within-your-subscription-or-resource-group"></a>Seznam všech Azure Cache pro Redis v rámci předplatného nebo skupiny prostředků
Chcete-li v rámci předplatného nebo skupiny prostředků zobrazit všechny mezipaměť Azure Pro redis, použijte následující příkaz:

    azure rediscache list [options]

Další informace o tomto příkazu získáte spuštěním příkazu. `azure rediscache list -h`

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

## <a name="show-properties-of-an-existing-azure-cache-for-redis"></a>Zobrazit vlastnosti existující mezipaměti Azure pro Redis
Chcete-li zobrazit vlastnosti existující mezipaměti Azure pro redis, použijte následující příkaz:

    azure rediscache show [--name <name> --resource-group <resource-group>]

Další informace o tomto příkazu získáte spuštěním příkazu. `azure rediscache show -h`

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

<a name="scale"></a>

## <a name="change-settings-of-an-existing-azure-cache-for-redis"></a>Změna nastavení existující mezipaměti Azure pro Redis
Chcete-li změnit nastavení existující mezipaměti Azure pro redis, použijte následující příkaz:

    azure rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]

Další informace o tomto příkazu získáte spuštěním příkazu. `azure rediscache set -h`

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

## <a name="renew-the-authentication-key-for-an-existing-azure-cache-for-redis"></a>Obnovení ověřovacího klíče pro existující mezipaměť Azure pro Redis
Pokud chcete obnovit ověřovací klíč pro existující mezipaměť Azure pro Redis, použijte následující příkaz:

    azure rediscache renew-key [--name <name> --resource-group <resource-group> --key-type <key-type>]

`Primary` Zadejte `Secondary` `key-type`nebo pro .

Další informace o tomto příkazu získáte spuštěním příkazu. `azure rediscache renew-key -h`

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

## <a name="list-primary-and-secondary-keys-of-an-existing-azure-cache-for-redis"></a>Seznam primárních a sekundárních klíčů existující mezipaměti Azure pro Redis
Chcete-li vypsat primární a sekundární klíče existující mezipaměti Azure pro redis, použijte následující příkaz:

    azure rediscache list-keys [--name <name> --resource-group <resource-group>]

Další informace o tomto příkazu získáte spuštěním příkazu. `azure rediscache list-keys -h`

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

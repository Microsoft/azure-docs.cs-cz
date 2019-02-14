---
title: Správa mezipaměti Azure pro Redis pomocí příkazového řádku Azure classic | Dokumentace Microsoftu
description: Zjistěte, jak nainstalovat Azure classic CLI na libovolné platformě, jak používat pro připojení k vašemu účtu Azure a jak vytvořit a spravovat mezipaměti Azure Redis z klasického příkazového řádku.
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 964ff245-859d-4bc1-bccf-62e4b3c1169f
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: yegu
ms.openlocfilehash: 99148332a8310428cb73287d861ef71c35d59a26
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56233196"
---
# <a name="how-to-create-and-manage-azure-cache-for-redis-using-the-azure-classic-cli"></a>Jak vytvořit a spravovat Azure mezipaměti Redis pomocí Azure classic CLI
> [!div class="op_single_selector"]
> * [PowerShell](cache-howto-manage-redis-cache-powershell.md)
> * [Azure Classic CLI](cache-manage-cli.md)
>

Azure classic CLI je skvělý způsob, jak spravovat infrastrukturu Azure z libovolné platformy. Tento článek ukazuje, jak vytvářet a spravovat Azure Cache instance Redis pomocí Azure classic CLI.

[!INCLUDE [outdated-cli-content](../../includes/contains-classic-cli-content.md)]
> [!NOTE]
> Nejnovější ukázkové skripty rozhraní příkazového řádku Azure, najdete v části [mezipaměti Azure Azure CLI pro ukázky Redis](cli-samples.md).

## <a name="prerequisites"></a>Požadavky
Vytvoření a Správa mezipaměti Azure pro instance Redis pomocí příkazového řádku Azure classic, musíte dokončit následující kroky.

* Musíte mít účet Azure. Pokud ho nemáte, můžete vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) za několik okamžiků.
* [Instalace Azure classic CLI](../cli-install-nodejs.md).
* Připojení vaší instalace rozhraní příkazového řádku Azure pomocí osobního účtu Azure, nebo pracovní nebo školní účet Azure a přihlášení z classic pomocí rozhraní příkazového řádku `azure login` příkazu.
* Před spuštěním následujících příkazů, přepněte rozhraní příkazového řádku classic do režimu Resource Manageru spuštěním `azure config mode arm` příkazu. Další informace najdete v tématu [použití Azure classic CLI ke správě prostředků a skupin prostředků Azure](../xplat-cli-azure-resource-manager.md).

## <a name="azure-cache-for-redis-properties"></a>Mezipaměť Azure Redis vlastností
Následující vlastnosti se používají při vytváření a aktualizaci mezipaměti Azure pro instance Redis.

| Vlastnost | Přepínač | Popis |
| --- | --- | --- |
| jméno |-n, --name |Název mezipaměti Azure redis Cache. |
| skupina prostředků |-g, --resource-group |Název skupiny prostředků. |
| location |-l,--umístění |Umístění pro vytvoření mezipaměti. |
| velikost |-z, --size |Velikost mezipaměti pro Azure redis Cache. Platné hodnoty: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4] |
| SKU |-x, --sku |Redis SKU. By měla být jedna z: [Basic, Standard, Premium] |
| EnableNonSslPort |-e,--enable bez--port ssl |Vlastnost EnableNonSslPort ukládání do mezipaměti Azure pro Redis. Přidejte tento příznak, pokud chcete povolit Port bez SSL ke svojí mezipaměti |
| Konfigurace mezipaměti redis |-c, --redis-configuration |Konfigurace mezipaměti redis. Zadejte řetězec ve formátu JSON konfigurace klíčů a hodnot v tomto poli. Formát: "{" ":""," ":" "}" |
| Konfigurace mezipaměti redis |-f, --redis-configuration-file |Konfigurace mezipaměti redis. Zadejte cestu k souboru, který obsahuje konfigurační klíče a hodnoty tady. Formát pro vstupní soubor: {"": "","": ""} |
| Počet horizontálních oddílů |-r,--počet horizontálních oddílů |Počet horizontálních oddílů k vytvoření v clusteru mezipaměti úrovně Premium s clusteringem. |
| Virtual Network |-v,--virtuální sítě |Při hostování mezipaměti ve virtuální síti, určuje přesné ARM ID prostředku virtuální sítě pro nasazení mezipaměti Azure redis cache v. Příklad formátu: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| Typ klíče |-t,--key-type |Typ klíče pro obnovení. Platné hodnoty: [primární, sekundární] |
| StaticIP |-p, --static-ip <static-ip> |Při hostování mezipaměti ve virtuální síti, určuje jedinečnou IP adresu v podsíti pro ukládání do mezipaměti. Pokud se nezadá, jeden je vybrán pro vás z podsítě. |
| Podsíť |t, --subnet <subnet> |Při hostování mezipaměti ve virtuální síti, určuje název podsítě, ve které se má nasadit do mezipaměti. |
| VirtualNetwork |-v, – virtuální sítě < virtual-network > |Při hostování mezipaměti ve virtuální síti, určuje přesné ARM ID prostředku virtuální sítě pro nasazení mezipaměti Azure redis cache v. Příklad formátu: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| Předplatné |-s, – předplatné |Identifikátor předplatného. |

## <a name="see-all-azure-cache-for-redis-commands"></a>Zobrazit všechny mezipaměti Azure pro příkazy Redis
Pokud chcete zobrazit všechny mezipaměti Azure Redis příkazy a jejich parametrů, použijte `azure rediscache -h` příkazu.

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

## <a name="create-an-azure-cache-for-redis"></a>Vytvoření Azure Cache pro Redis
Vytvoření Azure Cache pro Redis, použijte následující příkaz:

    azure rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]

Další informace o tomto příkazu Spustit `azure rediscache create -h` příkazu.

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

## <a name="delete-an-existing-azure-cache-for-redis"></a>Odstranit existující mezipaměti Azure Redis
Chcete-li odstranit Azure Cache pro Redis, použijte následující příkaz:

    azure rediscache delete [--name <name> --resource-group <resource-group> ]

Další informace o tomto příkazu Spustit `azure rediscache delete -h` příkazu.

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

## <a name="list-all-azure-cache-for-redis-within-your-subscription-or-resource-group"></a>Vypsat všechny mezipaměti Azure pro Redis v rámci předplatného nebo skupiny prostředků
Chcete-li vypsat všechny mezipaměti Azure pro Redis v rámci předplatného nebo skupiny prostředků, použijte následující příkaz:

    azure rediscache list [options]

Další informace o tomto příkazu Spustit `azure rediscache list -h` příkazu.

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

## <a name="show-properties-of-an-existing-azure-cache-for-redis"></a>Zobrazit vlastnosti existujícího mezipaměti Azure Redis
Chcete-li zobrazit vlastnosti existujícího mezipaměti Azure pro Redis, použijte následující příkaz:

    azure rediscache show [--name <name> --resource-group <resource-group>]

Další informace o tomto příkazu Spustit `azure rediscache show -h` příkazu.

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

## <a name="change-settings-of-an-existing-azure-cache-for-redis"></a>Změnit nastavení existujícího mezipaměti Azure Redis
Chcete-li změnit nastavení existujícího mezipaměti Azure Redis, použijte následující příkaz:

    azure rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]

Další informace o tomto příkazu Spustit `azure rediscache set -h` příkazu.

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

## <a name="renew-the-authentication-key-for-an-existing-azure-cache-for-redis"></a>Obnovit ověřovací klíč pro existující mezipaměti Azure pro Redis
Chcete-li obnovit ověřovací klíč pro existující mezipaměti Azure pro Redis, použijte následující příkaz:

    azure rediscache renew-key [--name <name> --resource-group <resource-group> --key-type <key-type>]

Zadejte `Primary` nebo `Secondary` pro `key-type`.

Další informace o tomto příkazu Spustit `azure rediscache renew-key -h` příkazu.

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

## <a name="list-primary-and-secondary-keys-of-an-existing-azure-cache-for-redis"></a>Seznam primárního a sekundárního klíče pro Redis stávající mezipaměti Azure
Na seznamu primárního a sekundárního klíče stávající mezipaměti Azure pro Redis použijte následující příkaz:

    azure rediscache list-keys [--name <name> --resource-group <resource-group>]

Další informace o tomto příkazu Spustit `azure rediscache list-keys -h` příkazu.

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

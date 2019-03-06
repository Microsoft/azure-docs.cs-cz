---
title: zahrnout soubor
description: zahrnout soubor
services: redis-cache
author: wesmc7777
ms.service: cache
ms.topic: include
ms.date: 03/28/2018
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: 778151e401624398b70101a242e4cf0be8e0a1b3
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2019
ms.locfileid: "57443685"
---
### <a name="retrieve-host-name-ports-and-access-keys-using-azure-cli"></a>Načtení názvu hostitele, portů a přístupových klíčů pomocí Azure CLI

Chcete-li načíst název hostitele a porty pomocí Azure CLI můžete volat [az redis show](https://docs.microsoft.com/cli/azure/redis#az_redis_show)a k načtení klíčů můžete zavolat [az redis list-keys](https://docs.microsoft.com/cli/azure/redis#az_redis_list_keys). Následující skript volá tyto dva příkazy a vrátí název hostitele, porty a klíče na konzolu.

```azurecli
#/bin/bash

# Retrieve the hostname, ports, and keys for contosoCache located in contosoGroup

# Retrieve the hostname and ports for an Azure Cache for Redis instance
redis=($(az redis show --name contosoCache --resource-group contosoGroup --query [hostName,enableNonSslPort,port,sslPort] --output tsv))

# Retrieve the keys for an Azure Cache for Redis instance
keys=($(az redis list-keys --name contosoCache --resource-group contosoGroup --query [primaryKey,secondaryKey] --output tsv))

# Display the retrieved hostname, keys, and ports
echo "Hostname:" ${redis[0]}
echo "Non SSL Port:" ${redis[2]}
echo "Non SSL Port Enabled:" ${redis[1]}
echo "SSL Port:" ${redis[3]}
echo "Primary Key:" ${keys[0]}
echo "Secondary Key:" ${keys[1]}
```

Další informace o tomto skriptu najdete v tématu [získat název hostitele, porty a klíče pro Azure Cache pro Redis](../articles/azure-cache-for-redis/scripts/cache-keys-ports.md). Další informace o Azure CLI najdete v části [instalace Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) a [Začínáme s Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).

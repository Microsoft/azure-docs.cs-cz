---
title: zahrnout soubor
description: zahrnout soubor
services: redis-cache
author: wesmc7777
ms.service: cache
ms.topic: include
ms.date: 11/05/2019
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: a737e130d616a67bab28c7c96c0372216a6707af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "73720330"
---
### <a name="retrieve-host-name-ports-and-access-keys-from-the-azure-portal"></a>Načtení názvu hostitele, portů a přístupových klíčů z webu Azure Portal

Chcete-li se připojit k instanci Azure Cache for Redis, klienti mezipaměti potřebují název hostitele, porty a klíč pro mezipaměť. Někteří klienti mohou tyto položky označovat trochu odlišnými názvy. Název hostitele, porty a klíče můžete získat z [webu Azure Portal](https://portal.azure.com).

- Chcete-li získat přístupové klávesy, vyberte z levé navigace v mezipaměti **přístupové klávesy**. 
  
  ![Azure Cache pro klíče Redis](media/redis-cache-access-keys/redis-cache-keys.png)

- Chcete-li získat název hostitele a porty, vyberte v levé navigaci v mezipaměti **možnost Vlastnosti**. Název hostitele je názvu dns * \<>.redis.cache.windows.net*.

  ![Azure Cache pro vlastnosti Redis](media/redis-cache-access-keys/redis-cache-hostname-ports.png)


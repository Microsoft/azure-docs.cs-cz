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
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2020
ms.locfileid: "73720330"
---
### <a name="retrieve-host-name-ports-and-access-keys-from-the-azure-portal"></a>Načtení názvu hostitele, portů a přístupových klíčů z Azure Portal

Aby bylo možné připojit se k mezipaměti Azure pro instanci Redis, klienti mezipaměti potřebují název hostitele, porty a klíč pro mezipaměť. Někteří klienti mohou tyto položky označovat trochu odlišnými názvy. Název hostitele, porty a klíče můžete získat z [Azure Portal](https://portal.azure.com).

- Přístupové klíče získáte tak, že z levé navigační části mezipaměti vyberete **přístupové klíče**. 
  
  ![Azure cache pro klíče Redis](media/redis-cache-access-keys/redis-cache-keys.png)

- Pokud chcete získat název hostitele a porty, z levé navigační možnosti mezipaměti vyberte **vlastnosti**. Název hostitele má formát * \<DNS název>. Redis.cache.Windows.NET*.

  ![Azure cache pro vlastnosti Redis](media/redis-cache-access-keys/redis-cache-hostname-ports.png)


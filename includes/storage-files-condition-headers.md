---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/17/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 0486b595bffd18b06d54e8377b24deab04e2aa93
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66159866"
---
## <a name="error-conditionheadersnotsupported-from-a-web-application-using-azure-files-from-browser"></a>Chyba ConditionHeadersNotSupported z webové aplikace pomocí služby soubory Azure z prohlížeče

Při přístupu k obsahu, které jsou hostované ve službě soubory Azure přes aplikaci, která umožňuje používat podmíněný hlaviček, například webový prohlížeč, přístup se nezdaří, zobrazí chybu ConditionHeadersNotSupported.

![Chyba ConditionHeaderNotSupported](media/storage-files-condition-headers/conditionalerror.png)

### <a name="cause"></a>Příčina

Podmíněné záhlaví se zatím nepodporují. Aplikace jejich implementaci bude muset požádat o celého souboru při každém přístupu k souboru.

### <a name="workaround"></a>Alternativní řešení:

Když se nahraje nový soubor, cache-control vlastnost ve výchozím nastavení je "no-cache". K vynucení aplikaci požádat o soubor pokaždé, když, cache-control vlastnost souboru musí být aktualizováno z hodnoty "no-cache" na "no-cache, no-store must-revalidate". Toho lze dosáhnout pomocí [Průzkumníka služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/).

![Storage explorer obsahu mezipaměti úpravy](media/storage-files-condition-headers/storage-explorer-cache.png)
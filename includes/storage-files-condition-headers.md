---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/04/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 4604616cd4f2d6c75c272586df1331fc405061cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "70737487"
---
## <a name="error-conditionheadersnotsupported-from-a-web-application-using-azure-files-from-browser"></a>Chyba VheadersNotPodporováno z webové aplikace pomocí souborů Azure z prohlížeče

Chyba ConditionHeadersNotSupported nastane při přístupu k obsahu hostovanému v souborech Azure prostřednictvím aplikace, která využívá podmíněné hlavičky, jako je například webový prohlížeč, přístup se nezdaří. Chyba uvádí, že záhlaví podmínky nejsou podporovány.

![Chyba podmíněných záhlaví Azure Files](media/storage-files-condition-headers/conditionalerror.png)

### <a name="cause"></a>Příčina

Podmíněná záhlaví ještě nejsou podporována. Aplikace, které je implementují, budou muset požádat o úplný soubor při každém přístupu k souboru.

### <a name="workaround"></a>Alternativní řešení

Při nahrání nového souboru je vlastnost řízení mezipaměti ve výchozím nastavení "bez mezipaměti". Chcete-li vynutit aplikaci požadovat soubor pokaždé, je třeba aktualizovat vlastnost správce mezipaměti souboru z "no-cache" na "no-cache, no-store, must-validate". Toho lze dosáhnout pomocí [Průzkumníka úložiště Azure](https://azure.microsoft.com/features/storage-explorer/).

![Úprava mezipaměti mezipaměti průzkumníka úložiště pro podmíněné hlavičky Souborů Azure](media/storage-files-condition-headers/storage-explorer-cache.png)
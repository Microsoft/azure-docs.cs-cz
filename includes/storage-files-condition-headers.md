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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "70737487"
---
## <a name="error-conditionheadersnotsupported-from-a-web-application-using-azure-files-from-browser"></a>Chyba ConditionHeadersNotSupported z webové aplikace pomocí služby soubory Azure z prohlížeče

K chybě ConditionHeadersNotSupported dojde při přístupu k obsahu hostovanému v souborech Azure pomocí aplikace, která využívá podmíněná záhlaví, jako je webový prohlížeč, ale přístup se nezdařil. Chyba uvádí, že hlavičky podmínek nejsou podporovány.

![Chyba podmíněných hlaviček souborů Azure](media/storage-files-condition-headers/conditionalerror.png)

### <a name="cause"></a>Příčina

Podmíněné hlavičky ještě nejsou podporované. Aplikace, které je implementují, budou muset požádat o úplný soubor pokaždé, když se k souboru dostanete.

### <a name="workaround"></a>Alternativní řešení

Když se nahraje nový soubor, ve výchozím nastavení vlastnost Control cache je "no-cache". Chcete-li vynutit, aby aplikace vyžadovala soubor pokaždé, je nutné aktualizovat vlastnost řízení mezipaměti souboru z "no-cache" na "no-cache" No-Store ", musí-revalidate". Toho lze dosáhnout pomocí [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/).

![Úprava mezipaměti obsahu Průzkumníka služby Storage pro podmíněná záhlaví souborů Azure](media/storage-files-condition-headers/storage-explorer-cache.png)
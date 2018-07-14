---
title: zahrnout soubor
description: zahrnout soubor
services: batch
author: dlepow
ms.service: batch
ms.topic: include
ms.date: 04/06/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 0dcb608553d4455403c073e34e83bccb81cc64db
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38750081"
---
Úloha spuštěná ve službě Azure Batch může vygenerovat výstupní data při spuštění. Úlohy, výstup, který často je potřeba data pro načtení ukládaná další úkoly v úloze, klientská aplikace, který spustí úlohu, nebo obojí. Úlohy zápis výstupní data do systému souborů výpočetním uzlu služby Batch, ale když ho se znovu připojit bitovou kopii nebo když uzel opustí fond dojde ke ztrátě všech dat na uzlu. Úlohy mohou mít také soubor období uchovávání, po jejímž uplynutí jsou odstraněny soubory vytvořené úlohou. Z těchto důvodů je důležité zachovat výstup úlohy, které budete potřebovat později k úložišti dat, jako [služby Azure Storage](https://docs.microsoft.com/azure/storage/).

Možnosti účtu úložiště ve službě Batch najdete v tématu [Přehled funkcí Batch](../articles/batch/batch-api-basics.md#azure-storage-account).

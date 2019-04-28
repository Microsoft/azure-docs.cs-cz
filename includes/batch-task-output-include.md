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
ms.openlocfilehash: 7cee3f534e4ab4973a87a9c373a5b83aa2ba9ce2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60549936"
---
Úloha spuštěná ve službě Azure Batch může vygenerovat výstupní data při spuštění. Úlohy, výstup, který často je potřeba data pro načtení ukládaná další úkoly v úloze, klientská aplikace, který spustí úlohu, nebo obojí. Úlohy zápis výstupní data do systému souborů výpočetním uzlu služby Batch, ale když ho se znovu připojit bitovou kopii nebo když uzel opustí fond dojde ke ztrátě všech dat na uzlu. Úlohy mohou mít také soubor období uchovávání, po jejímž uplynutí jsou odstraněny soubory vytvořené úlohou. Z těchto důvodů je důležité zachovat výstup úlohy, které budete potřebovat později k úložišti dat, jako [služby Azure Storage](https://docs.microsoft.com/azure/storage/).

Možnosti účtu úložiště ve službě Batch najdete v tématu [Přehled funkcí Batch](../articles/batch/batch-api-basics.md#azure-storage-account).

---
title: zahrnout soubor
description: zahrnout soubor
services: batch
author: laurenhughes
ms.service: batch
ms.topic: include
ms.date: 04/06/2018
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: 7ba4c90811bd8051ed9c307d9f9fa33e08e69dc7
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67175360"
---
Úloha spuštěná ve službě Azure Batch může vygenerovat výstupní data při spuštění. Úlohy, výstup, který často je potřeba data pro načtení ukládaná další úkoly v úloze, klientská aplikace, který spustí úlohu, nebo obojí. Úlohy zápis výstupní data do systému souborů výpočetním uzlu služby Batch, ale když ho se znovu připojit bitovou kopii nebo když uzel opustí fond dojde ke ztrátě všech dat na uzlu. Úlohy mohou mít také soubor období uchovávání, po jejímž uplynutí jsou odstraněny soubory vytvořené úlohou. Z těchto důvodů je důležité zachovat výstup úlohy, které budete potřebovat později k úložišti dat, jako [služby Azure Storage](https://docs.microsoft.com/azure/storage/).

Možnosti účtu úložiště ve službě Batch najdete v tématu [Přehled funkcí Batch](../articles/batch/batch-api-basics.md#azure-storage-account).

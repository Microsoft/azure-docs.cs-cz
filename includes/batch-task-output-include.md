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
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/26/2020
ms.locfileid: "67175360"
---
Úloha spuštěná v Azure Batch může při spuštění vytvořit výstupní data. Výstupní data úlohy se často musí ukládat pro načtení jinými úkoly v úloze, v klientské aplikaci, která úlohu provedla, nebo obojí. Úlohy zapisují výstupní data do systému souborů dávkového výpočetního uzlu, ale všechna data v uzlu jsou ztracena při obnovení obrazu nebo když uzel opustí fond. Úlohy mohou mít také dobu uchovávání souborů, po jejímž uplynutí se soubory vytvořené úlohou odstraní. Z těchto důvodů je důležité zachovat výstup úkolu, který budete potřebovat později pro úložiště dat, jako je například [Azure Storage](https://docs.microsoft.com/azure/storage/).

Možnosti účtu úložiště ve službě Batch najdete v tématu [Přehled funkcí Batch](../articles/batch/batch-api-basics.md#azure-storage-account).

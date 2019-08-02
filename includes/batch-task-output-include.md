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
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "67175360"
---
Úloha spuštěná v Azure Batch může při spuštění vytvořit výstupní data. Výstupní data úlohy se často musí ukládat pro načtení jinými úkoly v úloze, v klientské aplikaci, která úlohu provedla, nebo obojí. Úlohy zapisují výstupní data do systému souborů dávkového výpočetního uzlu, ale všechna data v uzlu jsou ztracena při obnovení obrazu nebo když uzel opustí fond. Úlohy mohou mít také dobu uchovávání souborů, po jejímž uplynutí se soubory vytvořené úlohou odstraní. Z těchto důvodů je důležité zachovat výstup úkolu, který budete potřebovat později pro úložiště dat, jako je například [Azure Storage](https://docs.microsoft.com/azure/storage/).

Možnosti účtu úložiště ve službě Batch najdete v tématu [Přehled funkcí Batch](../articles/batch/batch-api-basics.md#azure-storage-account).

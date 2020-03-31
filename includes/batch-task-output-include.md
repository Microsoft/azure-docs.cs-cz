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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67175360"
---
Úloha spuštěná v Azure Batch může při spuštění vytvářet výstupní data. Výstupní data úlohy často musí být uložena pro načtení jinými úkoly v úloze, klientskou aplikací, která úlohu provedla, nebo obojím. Úlohy zapisují výstupní data do systému souborů výpočetního uzlu Batch, ale všechna data v uzlu budou ztracena, když je znovu zobrazena nebo když uzel opustí fond. Úkoly mohou mít také dobu uchování souborů, po které jsou odstraněny soubory vytvořené úkolem. Z těchto důvodů je důležité zachovat výstup úlohy, který budete později potřebovat do úložiště dat, jako je [Azure Storage](https://docs.microsoft.com/azure/storage/).

Možnosti účtu úložiště ve službě Batch najdete v tématu [Přehled funkcí Batch](../articles/batch/batch-api-basics.md#azure-storage-account).

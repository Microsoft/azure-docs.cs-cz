---
title: zahrnout soubor
description: zahrnout soubor
services: batch
author: JnHs
ms.service: batch
ms.topic: include
ms.date: 04/06/2018
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 019e8db54c1cfd9f436f880b8ddbb9bfa31c50bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96025467"
---
Úloha spuštěná v Azure Batch může při spuštění vytvořit výstupní data. Výstupní data úlohy se často musí ukládat pro načtení jinými úkoly v úloze, v klientské aplikaci, která úlohu provedla, nebo obojí. Úlohy zapisují výstupní data do systému souborů dávkového výpočetního uzlu, ale všechna data v uzlu jsou ztracena při obnovení obrazu nebo když uzel opustí fond. Úlohy mohou mít také dobu uchovávání souborů, po jejímž uplynutí se soubory vytvořené úlohou odstraní. Z těchto důvodů je důležité zachovat výstup úkolu, který budete potřebovat později pro úložiště dat, jako je například [Azure Storage](../articles/storage/index.yml).

Možnosti účtu úložiště ve Batch najdete v tématu [účty Batch a účty Azure Storage](../articles/batch/accounts.md#azure-storage-accounts).
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
ms.openlocfilehash: eaa76153fe96b5fd41166b20770e0a969aa9260d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "83797130"
---
Úloha spuštěná v Azure Batch může při spuštění vytvořit výstupní data. Výstupní data úlohy se často musí ukládat pro načtení jinými úkoly v úloze, v klientské aplikaci, která úlohu provedla, nebo obojí. Úlohy zapisují výstupní data do systému souborů dávkového výpočetního uzlu, ale všechna data v uzlu jsou ztracena při obnovení obrazu nebo když uzel opustí fond. Úlohy mohou mít také dobu uchovávání souborů, po jejímž uplynutí se soubory vytvořené úlohou odstraní. Z těchto důvodů je důležité zachovat výstup úkolu, který budete potřebovat později pro úložiště dat, jako je například [Azure Storage](https://docs.microsoft.com/azure/storage/).

Možnosti účtu úložiště ve Batch najdete v tématu [účty Batch a účty Azure Storage](../articles/batch/accounts.md#azure-storage-accounts).

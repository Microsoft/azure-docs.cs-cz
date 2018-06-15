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
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2018
ms.locfileid: "31532494"
---
Úloha spuštění v Azure Batch může nevytvořila výstupní data, když je spuštěna. Úloha výstupní data často potřebují k uložení pro načtení další úlohy pro úlohu klientskou aplikaci, která provést úlohu, nebo obojí. Úlohy zapsat výstupní data do systému souborů Batch výpočetním uzlu, ale když ho je obnovit z Image nebo když uzel opustí fond dojde ke ztrátě všech dat na uzlu. Úlohy mohou mít i dobu uchovávání souboru, po který jsou odstraněny soubory vytvořené úlohou. Z těchto důvodů je důležité zachovat výstup úlohy, které budete potřebovat později do úložiště dat, jako [Azure Storage](https://docs.microsoft.com/azure/storage/).

Možnosti účtu úložiště ve službě Batch, najdete v článku [přehled funkcí Batch](../articles/batch/batch-api-basics.md#azure-storage-account).

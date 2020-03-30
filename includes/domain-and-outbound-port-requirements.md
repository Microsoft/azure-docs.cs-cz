---
title: zahrnout soubor
description: zahrnout soubor
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: abnarain
ms.openlocfilehash: 6af6795fefb41f1d8f2b56e0aa1fb367fc18cee2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "74559296"
---
| Názvy domén                  | Odchozí porty | Popis                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | Vyžadované runtime integrace s vlastním hostitelem pro připojení ke službám přesunu dat v Azure Data Factory. |
| `*.frontend.clouddatahub.net` | 443            | Vyžadováno runtime matné integrace s vlastním hostitelem pro připojení ke službě Data Factory. |
| `download.microsoft.com`    | 443            | Vyžadováno runtimem integrace s vlastním hostitelem pro stahování aktualizací. Pokud jste zakázali automatickou aktualizaci, můžete tuto doménu přeskočit. |
| `*.core.windows.net`          | 443            | Používá se runtime integrace s vlastním hostitelem pro připojení k účtu úložiště Azure při použití funkce [fázované kopie.](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy) |
| `*.database.windows.net`      | 1433           | Povinné pouze při kopírování z nebo do Azure SQL Database nebo Azure SQL Data Warehouse a volitelné jinak. Pomocí funkce fázované kopie zkopírujte data do databáze SQL nebo datového skladu SQL bez otevření portu 1433. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Povinné pouze při kopírování z nebo do Azure Data Lake Store a volitelné jinak. |

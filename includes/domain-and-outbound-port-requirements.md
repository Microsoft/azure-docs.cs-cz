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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "74559296"
---
| Názvy domén                  | Odchozí porty | Popis                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | Požadováno místním prostředím Integration runtime pro připojení k pohybovým službám dat v Azure Data Factory. |
| `*.frontend.clouddatahub.net` | 443            | Požadováno místním prostředím Integration runtime pro připojení ke službě Data Factory. |
| `download.microsoft.com`    | 443            | Požadováno místním prostředím Integration runtime pro stahování aktualizací. Pokud jste zakázali automatickou aktualizaci, můžete tuto doménu přeskočit konfigurací. |
| `*.core.windows.net`          | 443            | Používá se v místním prostředí Integration runtime pro připojení k účtu úložiště Azure při použití funkce [dvoufázové kopírování](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy) . |
| `*.database.windows.net`      | 1433           | Vyžadováno pouze při kopírování z nebo do Azure SQL Database nebo Azure SQL Data Warehouse a volitelné jinak. Pomocí funkce dvoufázové kopírování můžete kopírovat data do SQL Database nebo SQL Data Warehouse bez otevření portu 1433. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Vyžadováno pouze při kopírování z nebo do Azure Data Lake Store a volitelné jinak. |

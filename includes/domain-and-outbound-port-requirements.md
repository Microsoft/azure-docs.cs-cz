---
title: zahrnout soubor
description: zahrnout soubor
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: abnarain
ms.openlocfilehash: f634ebb4d8c3f443b5d843b0093c955b384a9f5f
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72285617"
---
| Názvy domén                  | Odchozí porty | Popis                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | Požadováno místním prostředím Integration runtime pro připojení k pohybovým službám dat v Data Factory. |
| `*.frontend.clouddatahub.net` | 443            | Požadováno místním prostředím Integration runtime pro připojení ke službě Data Factory. |
| `download.microsoft.com`    | 443            | Požadováno místním prostředím Integration runtime pro stahování aktualizací. Pokud jste zakázali automatickou aktualizaci, můžete to vynechat. |
| `*.core.windows.net`          | 443            | Používá se v místním prostředí Integration runtime pro připojení k účtu úložiště Azure při použití funkce [dvoufázové kopírování](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy) . |
| `*.database.windows.net`      | 1433           | Volitelné Vyžaduje se při kopírování z nebo do Azure SQL Database nebo Azure SQL Data Warehouse. Pomocí funkce dvoufázové kopírování zkopírujte data do Azure SQL Database nebo Azure SQL Data Warehouse bez otevření portu 1433. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Volitelné Vyžaduje se při kopírování z nebo do Azure Data Lake Store. |

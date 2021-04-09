---
title: zahrnout soubor
description: zahrnout soubor
author: lrtoyou1223
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: lle
ms.openlocfilehash: d24e8957dc63024a46495e8a66bad7dbb3790f38
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100389486"
---
| Názvy domén                  | Odchozí porty | Description                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.core.windows.net`          | 443            | Používá se v místním prostředí Integration runtime pro připojení k účtu úložiště Azure při použití funkce dvoufázové kopírování. |
| `*.database.windows.net`      | 1433           | Vyžaduje se jenom při kopírování z nebo do Azure SQL Database nebo Azure synapse Analytics a volitelné jinak. Pomocí funkce dvoufázové kopírování můžete kopírovat data do SQL Database nebo Azure synapse Analytics bez nutnosti otevření portu 1433. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Vyžadováno pouze při kopírování z nebo do Azure Data Lake Store a volitelné jinak. |

---
title: zahrnout soubor
description: zahrnout soubor
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: abnarain
ms.openlocfilehash: f7b11f813232f593be3598a87b6f1ad7a57054b0
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "95558530"
---
| Názvy domén                  | Odchozí porty | Popis                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | Vyžadováno místním prostředím Integration runtime pro interaktivní vytváření. |
| `{datafactory}.{region}.datafactory.azure.net`<br> ani `*.frontend.clouddatahub.net` | 443            | Požadováno místním prostředím Integration runtime pro připojení ke službě Data Factory. <br>Pro nově vytvořená Data Factory prosím, najděte plně kvalifikovaný název domény z vašeho místně hostovaného Integration Runtime klíče, který je ve formátu {DataFactory}. {region}. DataFactory. Azure. NET. Pokud v případě staré datové továrny nevidíte plně kvalifikovaný název domény v integrovaném integračním klíči, použijte místo toho *. frontend.clouddatahub.net. |
| `download.microsoft.com`    | 443            | Požadováno místním prostředím Integration runtime pro stahování aktualizací. Pokud jste zakázali automatickou aktualizaci, můžete tuto doménu přeskočit konfigurací. |
| `*.core.windows.net`          | 443            | Používá se v místním prostředí Integration runtime pro připojení k účtu úložiště Azure při použití funkce [dvoufázové kopírování](../articles/data-factory/copy-activity-performance.md#staged-copy) . |
| `*.database.windows.net`      | 1433           | Vyžaduje se jenom při kopírování z nebo do Azure SQL Database nebo Azure synapse Analytics a volitelné jinak. Pomocí funkce dvoufázové kopírování můžete kopírovat data do SQL Database nebo synapse Analytics bez nutnosti otevření portu 1433. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Vyžadováno pouze při kopírování z nebo do Azure Data Lake Store a volitelné jinak. |
---
title: Co se stalo s mým projektem WebJob (Visual Studio Azure Storage)?
description: Popisuje, co se stalo v projektu webové úlohy Azure po připojení k účtu úložiště pomocí připojených služeb sady Visual Studio.
services: storage
author: ghogen
manager: jillfra
ms.assetid: 36ae7ff7-c22c-47eb-b220-049d61618c74
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 17861b7c25dfaf9bc9399e5261cdf2a5b43caf21
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298724"
---
# <a name="what-happened-to-my-webjob-project-visual-studio-azure-storage-connected-service"></a>Co se stalo s mým projektem WebJob (připojená služba Visual Studio Azure Storage)?
## <a name="references-added"></a>Přidané odkazy
Balíček NuGet Azure Storage byl do projektu sady Visual Studio přidán nebo aktualizován.  
Tento balíček přidává následující odkazy .NET:

* **Microsoft. data. Edm**
* **Microsoft. data. OData**
* **Microsoft. data. Services. Client**
* **Microsoft. WindowsAzure. ConfigurationManager**
* **Microsoft. WindowsAzure. Storage**
* **Newtonsoft. JSON**
* **System. data**
* **System. prostor**

## <a name="connection-string-for-azure-storage-added"></a>Připojovací řetězec pro Azure Storage přidán
V souboru App. config projektu se položky **AzureWebJobsStorage** a **AzureWebJobsDashboard** aktualizovaly pomocí připojovacího řetězce a klíče vybraného účtu úložiště.

Další informace najdete v tématu [Azure WebJobs materiály pro dokumentaci](https://go.microsoft.com/fwlink/?linkid=390226).


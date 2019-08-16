---
title: Co se stalo s mým projektem WebJob (připojená služba Visual Studio Azure Storage)? | Dokumenty Microsoft
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
ms.openlocfilehash: c54214e0b919ddaa60403a1c986a144100143577
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2019
ms.locfileid: "69510472"
---
# <a name="what-happened-to-my-webjob-project-visual-studio-azure-storage-connected-service"></a>Co se stalo s mým projektem WebJob (připojená služba Visual Studio Azure Storage)?
## <a name="references-added"></a>Přidané odkazy
Balíček NuGet Azure Storage byl do projektu sady Visual Studio přidán nebo aktualizován.  
Tento balíček přidává následující odkazy .NET:

* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.WindowsAzure.ConfigurationManager**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **System.Data**
* **System.Spatial**

## <a name="connection-string-for-azure-storage-added"></a>Připojovací řetězec pro Azure Storage přidán
V souboru App. config projektu se položky **AzureWebJobsStorage** a **AzureWebJobsDashboard** aktualizovaly pomocí připojovacího řetězce a klíče vybraného účtu úložiště.

Další informace najdete v tématu [Azure WebJobs materiály pro dokumentaci](https://go.microsoft.com/fwlink/?linkid=390226).


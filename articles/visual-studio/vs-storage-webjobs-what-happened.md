---
title: Co se stalo s Moje webová úloha projektu (Visual Studio Azure Storage připojeno service)? | Dokumenty Microsoft
description: Popisuje, co se stalo v projektu webové úlohy Azure po připojení k účtu úložiště pomocí sady Visual Studio připojené služby
services: storage
documentationcenter: ''
author: ghogen
manager: douge
editor: ''
ms.assetid: 36ae7ff7-c22c-47eb-b220-049d61618c74
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-what-happened
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: b7bebd7801e102b9a3173841ce2289ac575cd2e2
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="what-happened-to-my-webjob-project-visual-studio-azure-storage-connected-service"></a>Co se stalo s Moje webová úloha projektu (Visual Studio Azure Storage připojeno service)?
## <a name="references-added"></a>Přidanými referencemi
Balíček NuGet úložiště Azure se přidání nebo aktualizaci v projektu sady Visual Studio.  
Tento balíček přidá následující odkazy na rozhraní .NET:

* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.WindowsAzure.ConfigurationManager**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **System.Data**
* **System.Spatial**

## <a name="connection-string-for-azure-storage-added"></a>Připojovací řetězec pro Azure Storage přidán
V souboru App.config vašeho projektu **AzureWebJobsStorage** a **AzureWebJobsDashboard** položky, byly aktualizovány s vybraným účtem úložiště pro připojovací řetězec a klíč.

Další informace najdete v tématu [zdrojů dokumentace Azure WebJobs](http://go.microsoft.com/fwlink/?linkid=390226).


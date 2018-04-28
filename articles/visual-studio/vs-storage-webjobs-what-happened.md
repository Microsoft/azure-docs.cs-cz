---
title: Co se stalo s Moje webová úloha projektu (Visual Studio Azure Storage připojeno service)? | Dokumenty Microsoft
description: Popisuje, co se stalo v projektu webové úlohy Azure po připojení k účtu úložiště pomocí sady Visual Studio připojené služby
services: storage
author: ghogen
manager: douge
ms.assetid: 36ae7ff7-c22c-47eb-b220-049d61618c74
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: 056ccd572a04a436ff53dda6ae967711c9f9903d
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
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


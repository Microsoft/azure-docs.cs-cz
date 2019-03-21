---
title: Co se stalo s mým projektem webové úlohy (Visual Studio Azure Storage připojená služba)? | Dokumenty Microsoft
description: Popisuje, co se stalo v projektu Azure WebJob po připojení k účtu úložiště pomocí sady Visual Studio připojené služby
services: storage
author: ghogen
manager: douge
ms.assetid: 36ae7ff7-c22c-47eb-b220-049d61618c74
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: fa152d8b88254a35d00b91537bf1001ea1130e57
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "57884638"
---
# <a name="what-happened-to-my-webjob-project-visual-studio-azure-storage-connected-service"></a>Co se stalo s mým projektem webové úlohy (Visual Studio Azure Storage připojená služba)?
## <a name="references-added"></a>Přidanými referencemi
Balíček NuGet pro Azure Storage k přidání nebo aktualizaci projektu sady Visual Studio.  
Tento balíček přidává následující odkazy na rozhraní .NET:

* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.WindowsAzure.ConfigurationManager**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **System.Data**
* **System.Spatial**

## <a name="connection-string-for-azure-storage-added"></a>Připojovací řetězec pro přidání služby Azure Storage
V souboru App.config vašeho projektu **AzureWebJobsStorage** a **AzureWebJobsDashboard** položky byly aktualizovány s připojovací řetězec a klíč účtu úložiště.

Další informace najdete v tématu [prostředků dokumentace Azure WebJobs](https://go.microsoft.com/fwlink/?linkid=390226).


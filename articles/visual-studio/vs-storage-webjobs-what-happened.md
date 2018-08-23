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
ms.openlocfilehash: cf8b7849a603d5c1304846ab243478bb6afd5c18
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/18/2018
ms.locfileid: "42055051"
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

Další informace najdete v tématu [prostředků dokumentace Azure WebJobs](http://go.microsoft.com/fwlink/?linkid=390226).


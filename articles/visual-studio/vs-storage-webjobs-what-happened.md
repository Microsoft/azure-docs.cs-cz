---
title: Co se stalo s mým projektem WebJob (Visual Studio Azure Storage)?
description: Popisuje, co se stalo v projektu Azure WebJob po připojení k účtu úložiště pomocí připojených služeb Visual Studia
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72298724"
---
# <a name="what-happened-to-my-webjob-project-visual-studio-azure-storage-connected-service"></a>Co se stalo s mým projektem WebJob (služba připojení Visual Studio Azure Storage)?
## <a name="references-added"></a>Přidané odkazy
Balíček Azure Storage NuGet byl přidán nebo aktualizován v projektu sady Visual Studio.  
Tento balíček přidává následující odkazy .NET:

* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.data.Services.client**
* **Microsoft.WindowsAzure.ConfigurationManager**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **System.Data**
* **System.Spatial**

## <a name="connection-string-for-azure-storage-added"></a>Připojovací řetězec pro Azure Storage přidán
V souboru App.config vašeho projektu byly položky **AzureWebJobsStorage** a **AzureWebJobsDashboard** aktualizovány s připojovacím řetězcem a klíčem vybraného účtu úložiště.

Další informace najdete v [tématu Azure WebJobs dokumentace zdrojů](https://go.microsoft.com/fwlink/?linkid=390226).


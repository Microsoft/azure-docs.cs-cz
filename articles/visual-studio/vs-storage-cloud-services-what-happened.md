---
title: Co se stalo s mým projektem cloudové služby? | Dokumenty Microsoft
description: Popisuje, co se stane v projektu cloudové služby, po připojení k účtu služby Azure storage pomocí sady Visual Studio připojené služby
services: storage
author: ghogen
manager: douge
ms.assetid: ca0ea68d-f417-4ce8-9413-40d76f69cdea
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: e823bc16c500dfee44312a774d5e3bd6622e5fae
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/18/2018
ms.locfileid: "42056811"
---
# <a name="what-happened-to-my-cloud-services-project-visual-studio-azure-storage-connected-service"></a>Co se stalo s projektem cloudových služeb (Visual Studio Azure Storage připojená služba)?
## <a name="references-added"></a>Přidanými referencemi
Balíček NuGet pro úložiště Azure byl přidán do projektu sady Visual Studio.  
Tento balíček přidává následující odkazy na rozhraní .NET:

* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.WindowsAzure.Configuration**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **System.Data**
* **System.Spatial**

## <a name="connection-string-for-azure-storage-added"></a>Připojovací řetězec pro přidání služby Azure Storage
Elementy byly vytvořeny pomocí připojovacího řetězce a klíč účtu úložiště. Změny byly provedeny následující soubory:

* **ServiceDefinition.csdef**
* **ServiceConfiguration.Cloud.cscfg**
* **ServiceConfiguration.Local.cscfg**


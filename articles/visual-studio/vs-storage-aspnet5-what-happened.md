---
title: Co se stalo s mým projektem ASP.NET 5 (Visual Studio připojené služby) | Dokumentace Microsoftu
description: Popisuje, co se stane po připojení k účtu služby Azure storage v projektu Visual Studio ASP.NET 5 pomocí sady Visual Studio připojené služby
services: storage
author: ghogen
manager: douge
ms.assetid: e7caa9fa-c780-45eb-a546-299fc1c68455
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: 71a95e1974cbcec9afcc3337eb37275532e1b527
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "57999709"
---
# <a name="what-happened-to-my-aspnet-5-project-visual-studio-azure-storage-connected-services"></a>Co se stalo s mým projektem ASP.NET 5 (Visual Studio Azure Storage připojené služby)?
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

Také balíček NuGet **Microsoft.Framework.Configuration.Json** byl přidán.

## <a name="connection-string-for-azure-storage-added"></a>Připojovací řetězec pro přidání služby Azure Storage
V souboru config.json váš projekt byl vytvořen element připojovací řetězec a klíč účtu úložiště.

Další informace najdete v tématu [ASP.NET 5](https://www.asp.net/vnext).


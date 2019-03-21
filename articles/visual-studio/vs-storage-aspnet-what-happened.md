---
title: Co se stalo s mým projektem ASP.NET? | Dokumenty Microsoft
description: Popisuje, co se stane po přidání služby Azure Storage do projektu aplikace ASP.NET pomocí sady Visual Studio připojené služby
services: storage
author: ghogen
manager: douge
ms.assetid: e1fe1b6d-4e3d-476d-8b2f-f7ade050515e
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: e0e065b23581f297ee4ae2288a6e437da461a19f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "57998354"
---
# <a name="what-happened-to-my-aspnet-project-visual-studio-azure-storage-connected-service"></a>Co se stalo s mým projektem ASP.NET (Visual Studio Azure Storage připojená služba)?
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
V souboru web.config vašeho projektu pochází element připojovací řetězec a klíč účtu úložiště.

Další informace najdete v tématu [ASP.NET](https://www.asp.net).


---
title: Co se stalo s projektu ASP.NET 5 (Visual Studio připojené služby) | Microsoft Docs
description: Popisuje, co se stane po připojení k účtu úložiště Azure v projektu Visual Studio ASP.NET 5 pomocí sady Visual Studio připojené služby
services: storage
documentationcenter: ''
author: ghogen
manager: douge
editor: ''
ms.assetid: e7caa9fa-c780-45eb-a546-299fc1c68455
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-what-happened
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: 16da7e7a2d61cffb95d22a85669c8f91f28ae476
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="what-happened-to-my-aspnet-5-project-visual-studio-azure-storage-connected-services"></a>Co se stalo s projektu ASP.NET 5 (Visual Studio Azure Storage připojeno services)?
## <a name="references-added"></a>Přidanými referencemi
Balíček NuGet úložiště Azure byl přidán do projektu sady Visual Studio.  
Tento balíček přidá následující odkazy na rozhraní .NET:

* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.WindowsAzure.Configuration**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **System.Data**
* **System.Spatial**

Navíc balíček NuGet **Microsoft.Framework.Configuration.Json** byla přidána.

## <a name="connection-string-for-azure-storage-added"></a>Připojovací řetězec pro Azure Storage přidán
V souboru config.json projektu byl vytvořen element připojovací řetězec a klíč účtu vybrané úložiště.

Další informace najdete v tématu [ASP.NET 5](http://www.asp.net/vnext).


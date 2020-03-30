---
title: Co se stalo s mým projektem ASP.NET?
description: Popisuje, co se stane po přidání Služby Azure Storage do projektu ASP.NET pomocí připojených služeb Visual Studia.
services: storage
author: ghogen
manager: jillfra
ms.assetid: e1fe1b6d-4e3d-476d-8b2f-f7ade050515e
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 4374955d6d51c1ae44c211aaa93d0b5e8930fe5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72300039"
---
# <a name="what-happened-to-my-aspnet-project-visual-studio-azure-storage-connected-service"></a>Co se stalo s mým projektem ASP.NET (služba připojení Visual Studio Azure Storage)?
## <a name="references-added"></a>Přidané odkazy
Balíček Azure Storage NuGet byl přidán do projektu Sady Visual Studio.  
Tento balíček přidává následující odkazy .NET:

* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.data.Services.client**
* **Microsoft.WindowsAzure.Configuration**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **System.Data**
* **System.Spatial**

## <a name="connection-string-for-azure-storage-added"></a>Připojovací řetězec pro Azure Storage přidán
V souboru web.config vašeho projektu byl vytvořen prvek s připojovacím řetězcem a klíčem vybraného účtu úložiště.

Další informace naleznete [v tématu ASP.NET](https://www.asp.net).


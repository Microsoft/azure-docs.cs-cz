---
title: Co se stalo s mým projektem ASP.NET? | Dokumenty Microsoft
description: Popisuje, co se stane po přidání Azure Storage do projektu ASP.NET pomocí připojených služeb sady Visual Studio.
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
ms.openlocfilehash: 74db2e1e15c282f4393715c67561b2ed61bbcdfb
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2019
ms.locfileid: "69512066"
---
# <a name="what-happened-to-my-aspnet-project-visual-studio-azure-storage-connected-service"></a>Co se stalo s mým projektem ASP.NET (připojená služba sady Visual Studio Azure Storage)?
## <a name="references-added"></a>Přidané odkazy
Do projektu Visual studia se přidal balíček NuGet Azure Storage.  
Tento balíček přidává následující odkazy .NET:

* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.WindowsAzure.Configuration**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **System.Data**
* **System.Spatial**

## <a name="connection-string-for-azure-storage-added"></a>Připojovací řetězec pro Azure Storage přidán
V souboru Web. config vašeho projektu byl vytvořen element s připojovacím řetězcem a klíčem vybraného účtu úložiště.

Další informace najdete v tématu [ASP.NET](https://www.asp.net).


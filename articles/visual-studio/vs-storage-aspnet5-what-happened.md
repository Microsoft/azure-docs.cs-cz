---
title: Co se stalo s mým projektem ASP.NET 5 (připojené služby sady Visual Studio) | Microsoft Docs
description: Popisuje, co se stane po připojení k účtu služby Azure Storage v projektu Visual Studio ASP.NET 5 pomocí připojených služeb sady Visual Studio.
services: storage
author: ghogen
manager: jillfra
ms.assetid: e7caa9fa-c780-45eb-a546-299fc1c68455
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: e29c0302ecd703cb02199df95892e24917baf8e8
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2019
ms.locfileid: "69510772"
---
# <a name="what-happened-to-my-aspnet-5-project-visual-studio-azure-storage-connected-services"></a>Co se stalo s mým projektem ASP.NET 5 (připojené služby sady Visual Studio Azure Storage)?
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

Také byl přidán balíček NuGet **Microsoft. Framework. Configuration. JSON** .

## <a name="connection-string-for-azure-storage-added"></a>Připojovací řetězec pro Azure Storage přidán
V souboru config. JSON projektu byl vytvořen element s připojovacím řetězcem a klíčem vybraného účtu úložiště.

Další informace najdete v tématu [ASP.NET 5](https://www.asp.net/vnext).


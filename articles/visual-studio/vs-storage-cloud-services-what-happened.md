---
title: Co se stalo s mým projektem cloudové služby? | Dokumenty Microsoft
description: Popisuje, co se stane v projektu Cloud Services po připojení k účtu služby Azure Storage pomocí připojených služeb sady Visual Studio.
services: storage
author: ghogen
manager: jillfra
ms.assetid: ca0ea68d-f417-4ce8-9413-40d76f69cdea
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: d96e523b6e4e060eaef33de0f961e7020e5dfb57
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2019
ms.locfileid: "69510634"
---
# <a name="what-happened-to-my-cloud-services-project-visual-studio-azure-storage-connected-service"></a>Co se stalo s projektem Cloud Services (připojená služba Visual Studio Azure Storage)?
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
Prvky se vytvořily s připojovacím řetězcem a klíčem vybraného účtu úložiště. V následujících souborech byly provedeny změny:

* **ServiceDefinition.csdef**
* **ServiceConfiguration.Cloud.cscfg**
* **ServiceConfiguration.Local.cscfg**


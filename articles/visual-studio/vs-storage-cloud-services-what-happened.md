---
title: Co se stalo s mým projektem cloudové služby?
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
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ac5a635b687e51ddd34899717497548296c2fc23
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298759"
---
# <a name="what-happened-to-my-cloud-services-project-visual-studio-azure-storage-connected-service"></a>Co se stalo s projektem Cloud Services (připojená služba Visual Studio Azure Storage)?
## <a name="references-added"></a>Přidané odkazy
Do projektu Visual studia se přidal balíček NuGet Azure Storage.  
Tento balíček přidává následující odkazy .NET:

* **Microsoft. data. Edm**
* **Microsoft. data. OData**
* **Microsoft. data. Services. Client**
* **Microsoft. WindowsAzure. Configuration**
* **Microsoft. WindowsAzure. Storage**
* **Newtonsoft. JSON**
* **System. data**
* **System. prostor**

## <a name="connection-string-for-azure-storage-added"></a>Připojovací řetězec pro Azure Storage přidán
Prvky se vytvořily s připojovacím řetězcem a klíčem vybraného účtu úložiště. V následujících souborech byly provedeny změny:

* **ServiceDefinition. csdef**
* **ServiceConfiguration. Cloud. cscfg**
* **ServiceConfiguration. Local. cscfg**


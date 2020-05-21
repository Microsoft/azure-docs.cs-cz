---
title: Poznámky k verzi
description: Poznámky k verzi pro Azure synapse Analytics (pracovní prostory)
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 514694dc2e3f06db2fb80f6b3ba0106343be11d8
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83658502"
---
# <a name="azure-synapse-analytics-preview-release-notes"></a>Poznámky k verzi Azure synapse Analytics (Preview)

Tento článek popisuje omezení a problémy s Azure synapse Analytics (pracovní prostory). Související informace najdete v tématu [co je Azure synapse Analytics (pracovní prostory)](overview-what-is.md) .

[!INCLUDE [preview](includes/note-preview.md)]

## <a name="azure-synapse-workspaces"></a>Azure synapse (pracovní prostory) 

### <a name="azure-synapse-cli"></a>Rozhraní příkazového řádku Azure synapse

- Problém a dopad na zákazníky: pracovní prostory vytvořené sadou SDK nemůžou spustit synapse Studio.

- Alternativní řešení: proveďte následující kroky: 
  1.    Vytvořte pracovní prostor spuštěním `az synapse workspace create` .
  2.    Extrahujte spravované ID identity spuštěním `$identity=$(az synapse workspace show --name {workspace name}  --resource-group {resource group name} --query "identity.principalId")` .
  3.    Přidejte pracovní prostor jako roli do účtu úložiště spuštěním ` az role assignment create --role "Storage Blob Data Contributor" --assignee-object-id {identity } --scope {storage account resource id}` .
  4.    Přidejte pravidlo brány firewall spuštěním ` az synapse firewall-rule create --name allowAll --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255 ` .

## <a name="next-steps"></a>Další kroky

* [Vytvoření pracovního prostoru](quickstart-create-workspace.md)
* [Použití synapse studia](quickstart-synapse-studio.md)
* [Vytvoření fondu SQL](quickstart-create-sql-pool-portal.md)
* [Používání SQL na vyžádání](quickstart-sql-on-demand.md)
* [Vytvoření fondu Apache Spark](quickstart-create-apache-spark-pool-portal.md)
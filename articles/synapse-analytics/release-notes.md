---
title: Poznámky k verzi
description: Poznámky k vydání pro Azure Synapse Analytics (pracovní prostory)
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: c1b5b9ac5d7c3f04dd3ae2e843425a5ead0d4c07
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423856"
---
# <a name="azure-synapse-analytics-preview-release-notes"></a>Poznámky k verzi Azure Synapse Analytics (preview)

Tento článek popisuje omezení a problémy s Azure Synapse Analytics (pracovní prostory). Související informace najdete v tématu [Co je Azure Synapse Analytics (pracovní prostory)](overview-what-is.md)

[!INCLUDE [preview](includes/note-preview.md)]

## <a name="azure-synapse-workspaces"></a>Azure Synapse (pracovní prostory) 

### <a name="azure-synapse-cli"></a>Azure Synapse CLI

- Problém a dopad na zákazníka: Pracovní prostory vytvořené sadou SDK nemohou spustit Synapse Studio

- Řešení: Proveďte následující kroky: 
  1.    Vytvořte pracovní `az synapse workspace create`prostor spuštěním 2.    Extrahování id spravované identity spuštěním`$identity=$(az synapse workspace show --name {workspace name}  --resource-group {resource group name} --query "identity.principalId")`
  3.    Přidání pracovního prostoru jako role do účtu úložiště spuštěním` az role assignment create --role "Storage Blob Data Contributor" --assignee-object-id {identity } --scope {storage account resource id}`
  4.    Přidání pravidla brány firewall spuštěním` az synapse firewall-rule create --name allowAll --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255 `

## <a name="next-steps"></a>Další kroky

* [Vytvoření pracovního prostoru](quickstart-create-workspace.md)
* [Použití Synapse Studio](quickstart-synapse-studio.md)
* [Vytvoření fondu SQL](quickstart-create-sql-pool.md)
* [Použití sql na vyžádání](quickstart-sql-on-demand.md)
* [Vytvoření fondu Apache Spark](quickstart-create-apache-spark-pool.md)
---
title: 'Řešení potíží: pracovní prostory vytvořené sadou SDK nemůžou spustit synapse Studio.'
description: Postup řešení pracovních prostorů vytvořených sadou SDK nemůže spustit synapse Studio
author: julieMSFT
ms.author: jrasnick
ms.topic: troubleshooting
ms.service: synapse-analytics
ms.subservice: workspace
ms.date: 11/24/2020
ms.openlocfilehash: f5d0aae975d476da47510a1f2fd164cbc5f32945
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466172"
---
# <a name="troubleshoot-azure-synapse-analytics-workspaces-created-using-sdk"></a>Řešení potíží s pracovními prostory Azure synapse Analytics vytvořenými pomocí sady SDK

Tento článek popisuje kroky pro řešení potíží při spouštění synapse studia z pracovního prostoru synapse, který byl vytvořen pomocí sady SDK (Software Development Kit).


## <a name="prerequisites"></a>Předpoklady

- Pracovní prostor synapse se vytvořil pomocí sady SDK.

## <a name="workaround"></a>Alternativní řešení

Pokud chcete spustit synapse Studio z pracovního prostoru vytvořeného sadou SDK, proveďte následující kroky: 
  1.    Vytvořte pracovní prostor spuštěním `az synapse workspace create` .
  2.    Extrahujte spravované ID identity spuštěním `$identity=$(az synapse workspace show --name {workspace name}  --resource-group {resource group name} --query "identity.principalId")` .
  3.    Udělte roli Přispěvatel dat objektu BLOB úložiště do spravovaného účtu úložiště identity spuštěním ` az role assignment create --role "Storage Blob Data Contributor" --assignee-object-id {identity } --scope {storage account resource id}.`
  4.    Přidejte pravidlo brány firewall spuštěním ` az synapse firewall-rule create --name allowAll --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255 ` .

## <a name="next-steps"></a>Další kroky

* [Co je Azure synapse](../overview-what-is.md)
* [Začínáme](../get-started.md)

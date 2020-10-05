---
title: 'Poznámky k verzi: Azure synapse Analytics (Preview z pracovních prostorů)'
description: Poznámky k verzi pro Azure synapse Analytics (verze Preview pracovních prostorů)
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: f2050bf671af35df4faec8b0b1d53f4c10a9075e
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "90031666"
---
# <a name="azure-synapse-analytics-workspaces-preview-release-notes"></a>Zpráva k vydání verze pro Azure synapse Analytics (pracovní prostory ve verzi Preview)

Tento článek popisuje omezení a problémy s Azure synapse Analytics (pracovní prostory). Související informace najdete v tématu [co je Azure synapse Analytics (pracovní prostory)](overview-what-is.md) .

[!INCLUDE [preview](includes/note-preview.md)]

## <a name="azure-cli"></a>Azure CLI

- Problém a dopad na zákazníky: pracovní prostory vytvořené sadou SDK nemůžou spustit synapse Studio.

- Alternativní řešení: proveďte následující kroky: 
  1.    Vytvořte pracovní prostor spuštěním `az synapse workspace create` .
  2.    Extrahujte spravované ID identity spuštěním `$identity=$(az synapse workspace show --name {workspace name}  --resource-group {resource group name} --query "identity.principalId")` .
  3.    Přidejte pracovní prostor jako roli do účtu úložiště spuštěním ` az role assignment create --role "Storage Blob Data Contributor" --assignee-object-id {identity } --scope {storage account resource id}` .
  4.    Přidejte pravidlo brány firewall spuštěním ` az synapse firewall-rule create --name allowAll --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255 ` .

## <a name="next-steps"></a>Další kroky

* [Co je Azure synapse](overview-what-is.md)
* [Začínáme](get-started.md)
* [Nejčastější dotazy](overview-faq.md)

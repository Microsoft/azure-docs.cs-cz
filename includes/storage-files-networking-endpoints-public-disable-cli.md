---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 6/2/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 195caa6f8d7c00c741741fbf80a77bd7fe5579b0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84464970"
---
Následující příkaz rozhraní příkazového řádku zakáže veškerý provoz do veřejného koncového bodu účtu úložiště. Všimněte si, že tento příkaz má `-bypass` Parametr nastavený na `AzureServices` . Tím umožníte důvěryhodným službám, jako je například Azure File Sync, získat přístup k účtu úložiště prostřednictvím veřejného koncového bodu.

```bash
# This assumes $storageAccountResourceGroupName and $storageAccountName 
# are still defined from the beginning of this guide.
az storage account update \
    --resource-group $storageAccountResourceGroupName \
    --name $storageAccountName \
    --bypass "AzureServices" \
    --default-action "Deny" \
    --output none
```
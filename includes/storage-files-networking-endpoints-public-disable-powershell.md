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
ms.openlocfilehash: a0a9bc29c3e20a025fb2c46a71c2f134c37bee04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "84464976"
---
Následující příkaz PowerShellu odmítne veškerý provoz do veřejného koncového bodu účtu úložiště. Všimněte si, že tento příkaz má `-Bypass` Parametr nastavený na `AzureServices` . Tím umožníte důvěryhodným službám, jako je například Synchronizace souborů Azure, získat přístup k účtu úložiště prostřednictvím veřejného koncového bodu.

```PowerShell
# This assumes $storageAccount is still defined from the beginning of this of this guide.
$storageAccount | Update-AzStorageAccountNetworkRuleSet `
        -DefaultAction Deny `
        -Bypass AzureServices `
        -WarningAction SilentlyContinue `
        -ErrorAction Stop | `
    Out-Null
```
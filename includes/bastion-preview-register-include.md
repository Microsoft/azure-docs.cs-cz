---
title: zahrnout soubor
description: zahrnout soubor
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 05/28/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: da4177fd54c0d8777f15175cea3a74a8b01c0954
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305264"
---
1. Ujistěte se, že jste přihlášeni ke svému účtu Azure a používáte předplatné, pro který chcete připojit pro tuto verzi preview. V následujícím příkladu použijte k registraci:

    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowBastionHost -ProviderNamespace Microsoft.Network
    ```
2.  Registrovat předplatné ještě jednou s *Microsoft.Network* obor názvů poskytovatele.

    ```azurepowershell-interactive
    Register-AzResourceProvider -ProviderNamespace Microsoft.Network
    ````
3. Použijte následující příkaz a ověřte, zda *AllowBastionHost* funkce je ve vašem předplatném zaregistrovaný:

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.Network
    ````

    Může trvat několik minut, než registraci dokončit.

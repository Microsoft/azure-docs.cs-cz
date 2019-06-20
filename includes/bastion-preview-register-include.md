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
ms.openlocfilehash: d05d425cc9bfb206207801f15a25e17d60dc0aaf
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67191743"
---
1. Ujistěte se, že jste přihlášeni ke svému účtu Azure a používáte předplatné, pro který chcete připojit pro tuto verzi preview. V následujícím příkladu použijte k registraci:

    ```azurepowershell-interactive
    Register-AzureRmProviderFeature -FeatureName AllowBastionHost -ProviderNamespace Microsoft.Network
    ```
2.  Registrovat předplatné ještě jednou s *Microsoft.Network* obor názvů poskytovatele.

    ```azurepowershell-interactive
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
    ````
3. Použijte následující příkaz a ověřte, zda *AllowBastionHost* funkce je ve vašem předplatném zaregistrovaný:

    ```azurepowershell-interactive
    Get-AzureRmProviderFeature -ProviderNamespace Microsoft.Network
    ````

    Může trvat několik minut, než registraci dokončit.
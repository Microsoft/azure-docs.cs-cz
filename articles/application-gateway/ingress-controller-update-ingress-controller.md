---
title: Upgrade řadiče příchozího přenosu dat pomocí helmy
description: Tento článek obsahuje informace o tom, jak upgradovat příchozí přenos dat aplikační brány pomocí helmu.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 3903ccd1c15765d06cd1794a40567e2c70062538
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73795902"
---
# <a name="how-to-upgrade-application-gateway-ingress-controller-using-helm"></a>Postup upgradu kontroleru příchozího přenosu dat služby Application Gateway s využitím Helmu 

Řadič příchozího přenosu dat aplikační brány Azure pro Kubernetes (AGIC) lze upgradovat pomocí úložiště Helm hostovaného ve službě Azure Storage.

Než zahájíme postup upgradu, ujistěte se, že jste přidali požadované úložiště:

- Zobrazte si aktuálně přidané repozitáře Helm u:

    ```bash
    helm repo list
    ```

- Přidejte repo AGIC s:

    ```bash
    helm repo add \
        application-gateway-kubernetes-ingress \
        https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    ```

## <a name="upgrade"></a>Upgrade

1. Aktualizujte úložiště AGIC Helm a získejte nejnovější verzi:

    ```bash
    helm repo update
    ```

1. Zobrazení dostupných verzí `application-gateway-kubernetes-ingress` grafu:

    ``` bash
    helm search -l application-gateway-kubernetes-ingress
    ```

    Odpověď vzorku:

    ```bash
    NAME                                                    CHART VERSION   APP VERSION     DESCRIPTION
    application-gateway-kubernetes-ingress/ingress-azure    0.7.0-rc1       0.7.0-rc1       Use Azure Application Gateway as the ingress for an Azure...
    application-gateway-kubernetes-ingress/ingress-azure    0.6.0           0.6.0           Use Azure Application Gateway as the ingress for an Azure...
    ```

    Nejnovější dostupná verze z výše uvedeného seznamu je:`0.7.0-rc1`

1. Zobrazení aktuálně nainstalovaných grafů helmu:

    ```bash
    helm list
    ```

    Odpověď vzorku:

    ```bash
    NAME            REVISION        UPDATED                         STATUS  CHART                   APP VERSION     NAMESPACE
    odd-billygoat   22              Fri Jun 21 15:56:06 2019        FAILED  ingress-azure-0.7.0-rc1 0.7.0-rc1       default
    ```

    Instalace grafu Helm z ukázkové `odd-billygoat`odpovědi výše je pojmenována . Tento název použijeme pro zbytek příkazů. Název skutečného nasazení se bude s největší pravděpodobností lišit.

1. Upgradujte nasazení helmu na novou verzi:

    ```bash
    helm upgrade \
        odd-billygoat \
        application-gateway-kubernetes-ingress/ingress-azure \
        --version 0.9.0-rc2
    ```

## <a name="rollback"></a>Vrácení zpět

Pokud nasazení helmu nezdaří, můžete vrátit zpět na předchozí verzi.

1. Získejte poslední známé číslo verze v pořádku:

    ```bash
    helm history odd-billygoat
    ```

    Ukázkový výstup:

    ```bash
    REVISION        UPDATED                         STATUS          CHART                   DESCRIPTION
    1               Mon Jun 17 13:49:42 2019        DEPLOYED        ingress-azure-0.6.0     Install complete
    2               Fri Jun 21 15:56:06 2019        FAILED          ingress-azure-xx        xxxx
    ```

    Ze ukázkového výstupu `helm history` příkazu to vypadá, že `odd-billygoat` poslední úspěšné nasazení našeho bylo revize`1`

1. Návrat k poslední úspěšné revizi:

    ```bash
    helm rollback odd-billygoat 1
    ```

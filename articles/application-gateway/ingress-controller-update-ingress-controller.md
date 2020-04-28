---
title: Upgrade řadiče pro příchozí přenos dat pomocí Helm
description: Tento článek poskytuje informace o tom, jak upgradovat Application Gateway příchozího přenosu dat pomocí Helm.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 3903ccd1c15765d06cd1794a40567e2c70062538
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2020
ms.locfileid: "73795902"
---
# <a name="how-to-upgrade-application-gateway-ingress-controller-using-helm"></a>Postup upgradu kontroleru příchozího přenosu dat služby Application Gateway s využitím Helmu 

Kontroler služby Azure Application Gateway příchozího přenosu pro Kubernetes (AGIC) se dá upgradovat pomocí úložiště Helm hostovaného v Azure Storage.

Než zahájíte postup upgradu, ujistěte se, že jste přidali požadované úložiště:

- Zobrazení aktuálně přidaných úložišť Helm pomocí:

    ```bash
    helm repo list
    ```

- Přidejte úložiště AGIC pomocí:

    ```bash
    helm repo add \
        application-gateway-kubernetes-ingress \
        https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    ```

## <a name="upgrade"></a>Upgrade

1. Pokud chcete získat nejnovější verzi, aktualizujte úložiště AGIC Helm:

    ```bash
    helm repo update
    ```

1. Zobrazit dostupné verze `application-gateway-kubernetes-ingress` grafu:

    ``` bash
    helm search -l application-gateway-kubernetes-ingress
    ```

    Ukázková odpověď:

    ```bash
    NAME                                                    CHART VERSION   APP VERSION     DESCRIPTION
    application-gateway-kubernetes-ingress/ingress-azure    0.7.0-rc1       0.7.0-rc1       Use Azure Application Gateway as the ingress for an Azure...
    application-gateway-kubernetes-ingress/ingress-azure    0.6.0           0.6.0           Use Azure Application Gateway as the ingress for an Azure...
    ```

    Nejnovější dostupná verze ze seznamu výše je:`0.7.0-rc1`

1. Zobrazení aktuálně nainstalovaných grafů Helm:

    ```bash
    helm list
    ```

    Ukázková odpověď:

    ```bash
    NAME            REVISION        UPDATED                         STATUS  CHART                   APP VERSION     NAMESPACE
    odd-billygoat   22              Fri Jun 21 15:56:06 2019        FAILED  ingress-azure-0.7.0-rc1 0.7.0-rc1       default
    ```

    Instalace grafu Helm z výše uvedené odpovědi je pojmenována `odd-billygoat`. Tento název použijeme pro zbytek příkazů. Váš skutečný název nasazení se pravděpodobně bude lišit.

1. Upgradujte nasazení Helm na novou verzi:

    ```bash
    helm upgrade \
        odd-billygoat \
        application-gateway-kubernetes-ingress/ingress-azure \
        --version 0.9.0-rc2
    ```

## <a name="rollback"></a>Návrat

Pokud by nasazení Helm nebylo úspěšné, můžete vrátit zpět na předchozí verzi.

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

    Z ukázkového výstupu `helm history` příkazu vypadá jako poslední úspěšné nasazení naší `odd-billygoat` revize.`1`

1. Vrátit zpět poslední úspěšnou revizi:

    ```bash
    helm rollback odd-billygoat 1
    ```

---
title: Upgrade řadiče pro příchozí přenos dat pomocí Helm
description: Tento článek poskytuje informace o tom, jak upgradovat Application Gateway příchozího přenosu dat pomocí Helm.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 10/23/2019
ms.author: caya
ms.openlocfilehash: 6ed73a2172e09e7255447b4467698670c95b63af
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513234"
---
# <a name="how-to-upgrade-application-gateway-ingress-controller-using-helm"></a>Postup upgradu Application Gatewayho kontroleru příchozího přenosu dat pomocí Helm 

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

## <a name="upgrade"></a>Aktualizace

1. Pokud chcete získat nejnovější verzi, aktualizujte úložiště AGIC Helm:

    ```bash
    helm repo update
    ```

1. Zobrazit dostupné verze grafu `application-gateway-kubernetes-ingress`:

    ``` bash
    helm search -l application-gateway-kubernetes-ingress
    ```

    Ukázková odpověď:

    ```bash
    NAME                                                    CHART VERSION   APP VERSION     DESCRIPTION
    application-gateway-kubernetes-ingress/ingress-azure    0.7.0-rc1       0.7.0-rc1       Use Azure Application Gateway as the ingress for an Azure...
    application-gateway-kubernetes-ingress/ingress-azure    0.6.0           0.6.0           Use Azure Application Gateway as the ingress for an Azure...
    ```

    Nejnovější dostupná verze ze seznamu výše je: `0.7.0-rc1`

1. Zobrazení aktuálně nainstalovaných grafů Helm:

    ```bash
    helm list
    ```

    Ukázková odpověď:

    ```bash
    NAME            REVISION        UPDATED                         STATUS  CHART                   APP VERSION     NAMESPACE
    odd-billygoat   22              Fri Jun 21 15:56:06 2019        FAILED  ingress-azure-0.7.0-rc1 0.7.0-rc1       default
    ```

    Instalace grafu Helm z výše uvedené odpovědi má název `odd-billygoat`. Tento název použijeme pro zbytek příkazů. Váš skutečný název nasazení se pravděpodobně bude lišit.

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

    Z ukázkového výstupu příkazu `helm history` vypadá jako poslední úspěšné nasazení našeho `odd-billygoat` revize `1`

1. Vrátit zpět poslední úspěšnou revizi:

    ```bash
    helm rollback odd-billygoat 1
    ```

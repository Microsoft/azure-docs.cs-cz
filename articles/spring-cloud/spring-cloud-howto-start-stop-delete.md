---
title: Spuštění, zastavení a odstranění cloudové aplikace Azure ve jarním prostředí | Microsoft Docs
description: Jak spustit, zastavit a odstranit cloudovou aplikaci Azure pro jaře
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 479daa9a94247ada30c54a9c5df3471035765087
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90908272"
---
# <a name="start-stop-and-delete-your-azure-spring-cloud-application"></a>Spuštění, zastavení a odstranění cloudové aplikace Azure pro jaře

**Tento článek se týká:** ✔️ Java ✔️ C #

Tato příručka vysvětluje, jak změnit stav aplikace v jaře cloudu Azure pomocí Azure Portal nebo rozhraní příkazového řádku Azure.

## <a name="using-the-azure-portal"></a>Použití webu Azure Portal

Po nasazení aplikace můžete spustit, zastavit a odstranit pomocí Azure Portal.

1. V Azure Portal přejdete do své instance služby jarní cloudová služba Azure.
1. Vyberte kartu **řídicí panel aplikace** .
1. Vyberte aplikaci, jejíž stav chcete změnit.
1. Na stránce **Přehled** této aplikace vyberte **Spustit/zastavit**, **restartovat**nebo **Odstranit**.

## <a name="using-the-azure-cli"></a>Použití Azure CLI

> [!NOTE]
> Můžete použít volitelné parametry a nakonfigurovat výchozí nastavení pomocí Azure CLI. Další informace o rozhraní příkazového řádku Azure najdete v [naší referenční dokumentaci](/cli/azure/ext/spring-cloud/spring-cloud?view=azure-cli-latest&preserve-view=true).  

Nejdřív nainstalujte rozšíření Azure jaře Cloud pro rozhraní příkazového řádku Azure, a to následujícím způsobem:

```azurecli
az extension add --name spring-cloud
```

Pak vyberte některou z těchto operací Azure CLI:

* Spuštění aplikace:

    ```azurecli
    az spring-cloud app start -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Zastavení aplikace:

    ```azurecli
    az spring-cloud app stop -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Restartování aplikace:

    ```azurecli
    az spring-cloud app restart -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Odstranění aplikace:

    ```azurecli
    az spring-cloud app delete -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

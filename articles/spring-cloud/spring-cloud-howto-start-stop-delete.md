---
title: Jak spustit, zastavit a odstranit cloudovou aplikaci Azure pro jaře | Microsoft Docs
description: Jak spustit, zastavit a odstranit cloudovou aplikaci Azure pro jaře
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: jeconnoc
ms.openlocfilehash: 9f537ab425428728137e04713e434d8dc09e065a
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607725"
---
# <a name="how-to-start-stop-and-delete-your-azure-spring-cloud-application"></a>Jak spustit, zastavit a odstranit cloudovou aplikaci Azure pro jaře

Tato příručka vysvětluje, jak změnit stav aplikace v jarním cloudu Azure pomocí Azure Portal nebo rozhraní příkazového řádku.

## <a name="using-the-azure-portal"></a>Použití webu Azure Portal

Jakmile máte nasazenou aplikaci, můžete ji **Spustit**, **zastavit**a **Odstranit** pomocí Azure Portal.

1. V Azure Portal přejdete do své instance služby jarní cloudová služba Azure.
1. Vyberte kartu **řídicí panel aplikace** .
1. Vyberte aplikaci, jejíž stav chcete změnit.
2. Na stránce **Přehled** této aplikace vyhledejte tlačítka pro **spuštění/zastavení**, **restartování**a **odstranění** aplikace.

## <a name="using-the-azure-cli"></a>Použití Azure CLI

> [!NOTE]
> Můžete použít volitelné parametry a nakonfigurovat výchozí nastavení pomocí Azure CLI. Další informace o najdete v naší [referenční dokumentaci](spring-cloud-cli-reference.md).  

Instalace jarního cloudového rozšíření pro rozhraní příkazového řádku Azure CLI:

```azurecli
az extension add --name spring-cloud
```

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

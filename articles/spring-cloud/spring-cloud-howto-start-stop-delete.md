---
title: Spuštění, zastavení a odstranění aplikace Azure Spring Cloud | Dokumenty společnosti Microsoft
description: Jak spustit, zastavit a odstranit aplikaci Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: brendm
ms.openlocfilehash: daa549e248668add54530e90174134c4e0059b3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76276825"
---
# <a name="start-stop-and-delete-your-azure-spring-cloud-application"></a>Spuštění, zastavení a odstranění aplikace Azure Spring Cloud

Tato příručka vysvětluje, jak změnit stav aplikace v Azure Spring Cloud pomocí portálu Azure nebo Azure CLI.

## <a name="using-the-azure-portal"></a>Použití webu Azure Portal

Po nasazení aplikace, můžete spustit, zastavit a odstranit pomocí portálu Azure.

1. Přejděte na instanci služby Azure Spring Cloud na webu Azure Portal.
1. Vyberte kartu **Řídicí panel aplikace.**
1. Vyberte aplikaci, jejíž stav chcete změnit.
1. Na stránce **Přehled** pro tuto aplikaci vyberte **Spustit/zastavit**, **Restartovat**nebo **Odstranit**.

## <a name="using-the-azure-cli"></a>Použití Azure CLI

> [!NOTE]
> Můžete použít volitelné parametry a nakonfigurovat výchozí hodnoty pomocí rozhraní příkazového řádku Azure. Další informace o Azure CLI si přečtěte [naši referenční dokumentaci](spring-cloud-cli-reference.md).  

Nejprve nainstalujte rozšíření Azure Spring Cloud pro Azure CLI takto:

```azurecli
az extension add --name spring-cloud
```

Dále vyberte některou z těchto operací azure cli:

* Spuštění aplikace:

    ```azurecli
    az spring-cloud app start -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Ukončení aplikace:

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

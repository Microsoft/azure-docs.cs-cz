---
title: Jak spustit, zastavit a odstranit cloudovou aplikaci Azure pro jaře | Microsoft Docs
description: Jak spustit, zastavit a odstranit cloudovou aplikaci Azure pro jaře
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/07/2019
ms.author: v-vasuke
ms.openlocfilehash: f7f76644d13c20704d2c3bd908176ac452df2a20
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72039081"
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

* Spuštění aplikace:
    ```Azure CLI
    az spring-cloud app start -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Zastavení aplikace:
    ```Azure CLI
    az spring-cloud app stop -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Restartování aplikace:
    ```Azure CLI
    az spring-cloud app restart -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Odstranění aplikace:
    ```Azure CLI
    az spring-cloud app delete -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

---
title: Rychlý Start – Nastavení serveru pro konfiguraci jarního cloudu Azure
description: V této části najdete popis nastavení serveru pro konfiguraci jarního cloudu Azure pro nasazení aplikací.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/03/2020
ms.custom: devx-track-java
ms.openlocfilehash: 47f74b551919177b13f5a72d6eedeae3c77b9f14
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2020
ms.locfileid: "88951910"
---
# <a name="quickstart-set-up-azure-spring-cloud-configuration-server"></a>Rychlý Start: nastavení serveru pro konfiguraci jarního cloudu Azure

Azure pružinový konfigurační server pro Cloud je centralizovaná služba Konfigurace distribuovaných systémů. Používá připojenou vrstvu úložiště, která aktuálně podporuje místní úložiště, Git a podverzi.  Nastavte konfigurační server pro nasazení aplikací mikroslužeb do jarního cloudu Azure.

## <a name="prerequisites"></a>Předpoklady

* [Nainstalovat JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
* [Registrace předplatného Azure](https://azure.microsoft.com/free/)
* Volitelné [Nainstalujte rozhraní příkazového řádku Azure CLI 2.0.67 nebo vyšší verze](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) a nainstalujte rozšíření pro jarní cloud Azure pomocí příkazu: `az extension add --name spring-cloud`
* Volitelné [Instalace Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) a [přihlášení](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)

## <a name="azure-spring-cloud-config-server-procedures"></a>Postupy pro konfiguraci serveru v cloudu pro Azure pružiny

#### <a name="portal"></a>[Azure Portal](#tab/Azure-portal)

Následující postup nastaví konfigurační server pomocí Azure Portal k nasazení [ukázky Piggymetrics](spring-cloud-quickstart-sample-app-introduction.md).

1. Přejít na stránku **Přehled** služby a vyberte možnost **konfigurační server**.

2. V části **výchozí úložiště** nastavte možnost **URI** na " https://github.com/Azure-Samples/piggymetrics-config ".

3. Výběrem možnosti **Použít** změny uložte.

    ![Snímek obrazovky s portálem ASC](media/spring-cloud-quickstart-launch-app-portal/portal-config.png)

#### <a name="cli"></a>[Rozhraní příkazového řádku](#tab/Azure-CLI)

Následující postup používá rozhraní příkazového řádku Azure CLI k nastavení konfiguračního serveru pro nasazení [ukázky Piggymetrics](spring-cloud-quickstart-sample-app-introduction.md).

Nastavte konfiguraci – server s umístěním úložiště Git pro projekt:

```azurecli
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/Azure-Samples/piggymetrics-config
```

---

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Sestavování a nasazování aplikací](spring-cloud-quickstart-deploy-apps.md)

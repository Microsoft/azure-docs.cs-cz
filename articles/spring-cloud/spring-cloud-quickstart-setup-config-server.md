---
title: Rychlý Start – Nastavení serveru pro konfiguraci jarního cloudu Azure
description: V této části najdete popis nastavení serveru pro konfiguraci jarního cloudu Azure pro nasazení aplikací.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 09/08/2020
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 639672bdeff2f833c280a041e497197286c9ff24
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90885695"
---
# <a name="quickstart-set-up-azure-spring-cloud-configuration-server"></a>Rychlý Start: nastavení serveru pro konfiguraci jarního cloudu Azure

Azure jarní cloudový konfigurační server je centralizovaná služba Konfigurace distribuovaných systémů. Používá připojenou vrstvu úložiště, která aktuálně podporuje místní úložiště, Git a podverzi. V tomto rychlém startu nastavíte konfigurační server tak, aby získal data z úložiště Git.

::: zone pivot="programming-language-csharp"

## <a name="prerequisites"></a>Požadavky

* Dokončete předchozí rychlý Start v této sérii: [zřízení Azure jaře Cloud Service](spring-cloud-quickstart-provision-service-instance.md).

## <a name="azure-spring-cloud-config-server-procedures"></a>Postupy pro konfiguraci serveru v cloudu pro Azure pružiny

Spuštěním následujícího příkazu nastavte konfiguraci – server s umístěním úložiště Git pro projekt. Nahraďte `<service instance name>` názvem služby, kterou jste vytvořili dříve. Výchozí hodnota pro název instance služby, kterou jste nastavili v předchozím rychlém startu, nefunguje s tímto příkazem.

```azurecli
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples --search-paths steeltoe-sample/config
```

Tento příkaz oznamuje konfiguračnímu serveru, aby vyhledal konfigurační data ve složce [steeltoe-Sample/config](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/steeltoe-sample/config) ukázkového úložiště aplikace. Vzhledem k tomu, že název aplikace, která získá konfigurační data `planet-weather-provider` , je soubor, který se bude používat, [Planet-Weather-Provider. yml](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/blob/master/steeltoe-sample/config/planet-weather-provider.yml).

::: zone-end

::: zone pivot="programming-language-java"
Azure pružinový konfigurační server pro Cloud je centralizovaná služba Konfigurace distribuovaných systémů. Používá připojenou vrstvu úložiště, která aktuálně podporuje místní úložiště, Git a podverzi.  Nastavte konfigurační server pro nasazení aplikací mikroslužeb do jarního cloudu Azure.

## <a name="prerequisites"></a>Požadavky

* [Nainstalovat JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable&preserve-view=true)
* [Registrace předplatného Azure](https://azure.microsoft.com/free/)
* Volitelné [Nainstalujte rozhraní příkazového řádku Azure CLI 2.0.67 nebo vyšší verze](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) a nainstalujte rozšíření pro jarní cloud Azure pomocí příkazu: `az extension add --name spring-cloud`
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
::: zone-end

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v úmyslu pokračovat k dalšímu rychlému startu v této sérii, tento krok přeskočte.

V těchto rychlých startech jste vytvořili prostředky Azure, které budou nadále účtovat poplatky, pokud zůstanou ve vašem předplatném. Pokud nechcete pokračovat k dalšímu rychlému startu a neočekáváte, že tyto prostředky budete potřebovat v budoucnu, odstraňte skupinu prostředků pomocí portálu nebo spuštěním následujícího příkazu v Cloud Shell:

```azurecli
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

V dřívějším rychlém startu jste také nastavili výchozí název skupiny prostředků. Pokud nechcete pokračovat dalším rychlým startem, vymažte tuto výchozí hodnotu spuštěním následujícího příkazu rozhraní příkazového řádku:

```azurecli
az configure --defaults group=
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Sestavování a nasazování aplikací](spring-cloud-quickstart-deploy-apps.md)

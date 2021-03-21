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
ms.openlocfilehash: b82c9b21b9ccde7f7d5ee3e2b1c8c845a82bf459
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102213415"
---
# <a name="quickstart-set-up-azure-spring-cloud-configuration-server"></a>Rychlý Start: nastavení serveru pro konfiguraci jarního cloudu Azure

Azure jarní cloudový konfigurační server je centralizovaná služba Konfigurace distribuovaných systémů. Používá připojenou vrstvu úložiště, která aktuálně podporuje místní úložiště, Git a podverzi. V tomto rychlém startu nastavíte konfigurační server tak, aby získal data z úložiště Git.

::: zone pivot="programming-language-csharp"

## <a name="prerequisites"></a>Předpoklady

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

## <a name="prerequisites"></a>Předpoklady

* [Nainstalovat JDK 8](/java/azure/jdk/)
* [Registrace předplatného Azure](https://azure.microsoft.com/free/)
* Volitelné [Nainstalujte rozhraní příkazového řádku Azure CLI 2.0.67 nebo vyšší verze](/cli/azure/install-azure-cli) a nainstalujte rozšíření pro jarní cloud Azure pomocí příkazu: `az extension add --name spring-cloud`
* Volitelné [Instalace Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) a [přihlášení](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)

## <a name="azure-spring-cloud-config-server-procedures"></a>Postupy pro konfiguraci serveru v cloudu pro Azure pružiny

#### <a name="portal"></a>[Azure Portal](#tab/Azure-portal)

Následující postup nastaví konfigurační server pomocí Azure Portal k nasazení [ukázky Piggymetrics](spring-cloud-quickstart-sample-app-introduction.md).

1. Přejít na stránku **Přehled** služby a vyberte možnost **konfigurační server**.

2. V části **výchozí úložiště** nastavte možnost **URI** na " https://github.com/Azure-Samples/piggymetrics-config ".

3. Klikněte na tlačítko **ověřit**.

    ![Přejít na konfigurační server](media/spring-cloud-quickstart-launch-app-portal/portal-config.png)

4. Po dokončení ověřování klikněte na **použít** , aby se změny uložily.

    ![Ověřování konfiguračního serveru](media/spring-cloud-quickstart-launch-app-portal/validate-complete.png)

5. Aktualizace konfigurace může trvat několik minut.
 
    ![Aktualizace konfiguračního serveru](media/spring-cloud-quickstart-launch-app-portal/updating-config.png) 

6. Po dokončení konfigurace byste měli obdržet oznámení.

#### <a name="cli"></a>[Rozhraní příkazového řádku](#tab/Azure-CLI)

Následující postup používá rozhraní příkazového řádku Azure CLI k nastavení konfiguračního serveru pro nasazení [ukázky Piggymetrics](spring-cloud-quickstart-sample-app-introduction.md).

Nastavte konfiguraci – server s umístěním úložiště Git pro projekt:

```azurecli
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/Azure-Samples/piggymetrics-config
```
---
::: zone-end

> [!TIP]
> Pokud používáte privátní úložiště pro konfigurační server, přečtěte si náš [kurz o nastavení ověřování](./spring-cloud-tutorial-config-server.md).

## <a name="troubleshooting-of-azure-spring-cloud-config-server"></a>Řešení potíží s konfiguračním serverem cloudu Azure na jaře

Následující postup vysvětluje, jak řešit potíže s nastavením konfiguračního serveru.

1. V Azure Portal přejdete na stránku **Přehled** služby a vyberte **protokoly**. 
1. Vyberte **dotazy** a **Zobrazte protokoly aplikací, které obsahují "Error" nebo "Exception" terms**. 
1. Klikněte na **Spustit**. 
1. Pokud v protokolech zjistíte chybu **Java. lang. illegalStateException** , znamená to, že jarní cloudová služba nemůže najít vlastnosti z konfiguračního serveru.

    [![Spustit dotaz na ](media/spring-cloud-quickstart-setup-config-server/setup-config-server-query.png) portálu ASC](media/spring-cloud-quickstart-setup-config-server/setup-config-server-query.png)

1. Přejít na stránku **Přehled** služby.
1. Vyberte **Diagnostikovat a řešit problémy**. 
1. Vyberte možnost detektor **konfiguračního serveru** .

    [![Problémy s ](media/spring-cloud-quickstart-setup-config-server/setup-config-server-diagnose.png) diagnostikou portálu ASC](media/spring-cloud-quickstart-setup-config-server/setup-config-server-diagnose.png)

3. Klikněte na tlačítko **kontrolu stavu konfiguračního serveru**.

    [![Genie ](media/spring-cloud-quickstart-setup-config-server/setup-config-server-genie.png) portálu ASC](media/spring-cloud-quickstart-setup-config-server/setup-config-server-genie.png)

4. Kliknutím na **stav konfiguračního serveru** zobrazíte další podrobnosti z detektoru.

    [![Stav ](media/spring-cloud-quickstart-setup-config-server/setup-config-server-health-status.png) portálu ASC](media/spring-cloud-quickstart-setup-config-server/setup-config-server-health-status.png)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili prostředky Azure, které budou nadále účtovat poplatky, pokud zůstanou ve vašem předplatném. Pokud nechcete pokračovat k dalšímu rychlému startu, přečtěte si téma [vyčištění prostředků](spring-cloud-quickstart-logs-metrics-tracing.md#clean-up-resources). V opačném případě přejděte k dalšímu rychlému startu:

> [!div class="nextstepaction"]
> [Sestavování a nasazování aplikací](spring-cloud-quickstart-deploy-apps.md)
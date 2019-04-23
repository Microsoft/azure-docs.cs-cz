---
title: Integrace s Azure spravovaných identit | Dokumentace Microsoftu
description: Další informace o použití Azure spravované identity k ověření prostřednictvím a získat přístup ke konfiguraci aplikací Azure
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: na
ms.topic: conceptual
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: 84dddf5ed9c31e8585480319af189d31e39163e5
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2019
ms.locfileid: "59998987"
---
# <a name="tutorial-integrate-with-azure-managed-identities"></a>Kurz: Integrace s Azure spravované identity

Azure Active Directory [spravovaných identit](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) pomáhají zjednodušit správu tajných kódů pro cloudové aplikace. Spravovanou identitu můžete nastavit váš kód používat instanční objekt, který byl vytvořen pro službu Azure compute, na kterých se spouští. Použití spravované identity namísto samostatné přihlašovací údaje uložené v Azure Key Vault nebo řetězec připojení k místní. 

Konfigurace aplikace pro Azure a jeho .NET Core, .NET a Java Spring klientské knihovny jsou dostupné podpory identita spravované služby je součástí. I když není nutné jeho použití, MSI se eliminuje potřeba přístupový token, který obsahuje tajných kódů. Váš kód obsahuje znát jenom koncový bod služby pro konfiguraci služby app ukládat, aby bylo možné k němu přístup. Vložte tuto adresu URL do kódu přímo bez obav vystavení jakékoli tajný klíč.

Tento kurz ukazuje, jak můžete využít výhod MSI pro přístup k aplikaci konfigurace. Sestaví ve webové aplikaci zavedený rychlých startech. Než budete pokračovat, dokončete [vytvoření aplikace ASP.NET Core s konfigurací aplikace](./quickstart-aspnet-core-app.md) první.

Provést kroky v tomto kurzu můžete použít libovolný editor kódu. [Visual Studio Code](https://code.visualstudio.com/) skvělou možnost je k dispozici ve Windows, macOS a Linux platformy.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Udělení přístupu spravované identity pro konfiguraci aplikací.
> * Konfigurace aplikace pro použití spravované identity při připojování k konfigurace aplikace.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete:

* [.NET core SDK](https://www.microsoft.com/net/download/windows).
* [Azure Cloud Shell nakonfigurované](https://docs.microsoft.com/azure/cloud-shell/quickstart).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="add-a-managed-identity"></a>Přidat spravované identity

Nastavení spravovaných identit na portálu, nejprve vytvořte aplikaci jako za normálních okolností a pak povolte funkci.

1. Vytvořte aplikaci [webu Azure portal](https://aka.ms/azconfig/portal) obvyklým způsobem. Na portálu přejděte do něj.

2. Přejděte dolů k položce **nastavení** skupině v levém podokně a vyberte **Identity**.

3. Na **přiřazenou systémem** přepněte **stav** k **na** a vyberte **Uložit**.

    ![Nastavte spravovanou identitu ve službě App Service](./media/set-managed-identity-app-service.png)

## <a name="grant-access-to-app-configuration"></a>Udělit přístup ke konfiguraci aplikací

1. V [webu Azure portal](https://aka.ms/azconfig/portal)vyberte **všechny prostředky** a vybrat úložiště konfigurace aplikace, kterou jste vytvořili v tomto rychlém startu.

2. Vyberte **Řízení přístupu (IAM)**.

3. Na **zkontrolovat přístup** kartu, vyberte možnost **přidat** v **přidat přiřazení role** karty uživatelského rozhraní.

4. V části **Role**vyberte **Přispěvatel**. V části **přiřadit přístup k**vyberte **služby App Service** pod **systém přiřadil spravovanou identitu**.

5. V části **předplatné**, vyberte své předplatné Azure. Vyberte prostředek služby App Service pro vaši aplikaci.

6. Vyberte **Uložit**.

    ![Přidat spravované identity](./media/add-managed-identity.png)

## <a name="use-a-managed-identity"></a>Použití spravované identity

1. Otevřít *appsettings.json*a přidejte následující skript. Nahraďte  *\<service_endpoint >*, včetně závorkách a s adresou URL k úložišti konfigurace aplikace:

    ```json
    "AppConfig": {
        "Endpoint": "<service_endpoint>"
    }
    ```

2. Otevřít *Program.cs*a aktualizovat `CreateWebHostBuilder` metodu nahrazením `config.AddAzureAppConfiguration()` metody.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options =>
                    options.ConnectWithManagedIdentity(settings["AppConfig:Endpoint"]));
            })
            .UseStartup<Startup>();
    ```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-from-local-git"></a>Nasazení z místního Gitu

Nejjednodušší způsob, jak povolit místní nasazení Gitu pro svou aplikaci pomocí Kudu server sestavení je chcete použít Azure Cloud Shell.

### <a name="configure-a-deployment-user"></a>Konfigurace uživatele nasazení

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Povolení místního Gitu pomocí Kudu

Pokud chcete povolit místní nasazení Gitu pro svou aplikaci pomocí Kudu sestavovací server, spusťte [ `az webapp deployment source config-local-git` ](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) ve službě Cloud Shell.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Pokud chcete místo toho vytvořte aplikaci s podporou Git, spusťte [ `az webapp create` ](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) ve službě Cloud Shell s `--deployment-local-git` parametru.

```azurecli-interactive
az webapp create --name <app_name> --resource-group <group_name> --plan <plan_name> --deployment-local-git
```

`az webapp create` Příkaz nabízí podobný následující výstup:

```json
Local git is configured with url of 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="deploy-your-project"></a>Nasazení projektu

Zpět v _okně místního terminálu_ přidejte vzdálené úložiště Azure do místního úložiště Git. Nahraďte  _\<url >_ adresou URL vzdáleného úložiště Git, které jste získali z [povolit Git pro vaši aplikaci](#enable-local-git-with-kudu).

```bash
git remote add azure <url>
```

Nasaďte aplikaci do vzdáleného úložiště Azure pomocí následujícího příkazu. Po zobrazení výzvy k zadání hesla, zadejte heslo, kterou jste vytvořili v [konfigurace uživatele nasazení](#configure-a-deployment-user). Nepoužívejte hesla, které používáte k přihlášení k webu Azure portal.

```bash
git push azure master
```

Automatizace specifické pro modul runtime ve výstupu, jako je MSBuild pro technologii ASP.NET, může se zobrazit `npm install` pro Node.js, a `pip install` pro Python.

### <a name="browse-to-the-azure-web-app"></a>Přechod do webové aplikace Azure

Přejděte do webové aplikace pomocí prohlížeče pro ověření, že obsah nasazení.

```bash
http://<app_name>.azurewebsites.net
```

![aplikace spuštěná ve službě App Service](../app-service/media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png)

## <a name="use-managed-identity-in-other-languages"></a>Použití spravované identity v jiných jazycích

Poskytovatelé konfigurace aplikace pro rozhraní .NET Framework a jazyka Java Spring mají i integrovanou podporu pro spravovanou identitu. V těchto případech použijte koncový bod adresy URL vašeho úložiště konfigurace aplikace místo jeho úplný připojovací řetězec při konfiguraci poskytovatele. Například aplikace konzoly rozhraní .NET Framework v tomto rychlém startu vytvořili, zadejte následující nastavení ve *App.config* souboru:

```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>

    <configBuilders>
        <builders>
            <add name="MyConfigStore" mode="Greedy" endpoint="${Endpoint}" type="Microsoft.Configuration.ConfigurationBuilders.AzureAppConfigurationBuilder, Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration" />
            <add name="Environment" mode="Greedy" type="Microsoft.Configuration.ConfigurationBuilders.EnvironmentConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Environment" />
        </builders>
    </configBuilders>

    <appSettings configBuilders="Environment,MyConfigStore">
        <add key="AppName" value="Console App Demo" />
        <add key="Endpoint" value ="Set via an environment variable - for example, dev, test, staging, or production endpoint." />
    </appSettings>
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Ukázky rozhraní příkazového řádku](./cli-samples.md)

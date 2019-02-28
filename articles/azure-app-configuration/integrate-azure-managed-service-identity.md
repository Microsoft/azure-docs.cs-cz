---
title: Kurz pro integraci se službou identit spravovaných Azure | Dokumentace Microsoftu
description: V tomto kurzu se dozvíte, jak využívat Azure spravované identity k ověření prostřednictvím a získat přístup ke konfiguraci aplikací Azure
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: na
ms.topic: tutorial
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: 874522b6b4ca3739e0736d4f70f76bb82cad25f9
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2019
ms.locfileid: "56957347"
---
# <a name="tutorial-integrate-with-azure-managed-identities"></a>Kurz: Integrace s Azure spravované identity

Azure Active Directory [identit spravovaných](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) pomáhá zjednodušit správu tajných kódů pro cloudové aplikace. Spravovanou identitu můžete nastavit váš kód používat instanční objekt, který se vytvořil pro službu Azure compute, na kterých se spouští, namísto samostatné přihlašovací údaje uložené v Azure Key Vault nebo místní připojovací řetězec. Konfigurace aplikace pro Azure a .NET Core, .NET a Java Spring klientské knihovny jsou dostupné je integrovanou podporu MSI. Přestože není nutné jeho použití, MSI se eliminuje potřeba přístupový token, který obsahuje tajných kódů. Váš kód obsahuje pouze znát koncový bod služby pro úložiště konfigurace aplikace k němu přistupovat a tuto adresu URL můžete vložit do kódu přímo bez obav vystavení jakékoli tajný klíč.

Tento kurz ukazuje, jak můžete využít výhod MSI pro přístup k aplikaci konfigurace. Sestaví ve webové aplikaci zavedený rychlých startech. Kompletní [vytvoření aplikace ASP.NET Core s konfigurací aplikace](./quickstart-aspnet-core-app.md) první předtím, než budete pokračovat.

K dokončení kroků v tomto kurzu můžete použít libovolný editor kódu. Skvělou volbou je však editor [Visual Studio Code](https://code.visualstudio.com/), který je dostupný pro platformy Windows, macOS a Linux.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Udělení přístupu spravované identity pro konfiguraci aplikací
> * Konfigurace aplikace pro použití spravované identity při připojování ke konfiguraci aplikací

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete:

* [Sada .NET Core SDK](https://www.microsoft.com/net/download/windows)
* [Nakonfigurovaná služba Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="add-a-managed-identity"></a>Přidat spravované identity

Nastavení spravovaných identit na portálu, nejprve vytvoříte aplikaci jako za normálních okolností a pak povolte funkci.

1. Vytvořte aplikaci [webu Azure portal](https://aka.ms/azconfig/portal) běžným způsobem. Přejděte na ni na portálu.

2. Přejděte dolů k položce **nastavení** skupině v levém navigačním panelu a vyberte **Identity**.

3. V rámci **přiřazenou systémem** přepněte **stav** k **na** a klikněte na tlačítko **Uložit**.

    ![Nastavte spravovanou identitu ve službě App Service](./media/set-managed-identity-app-service.png)

## <a name="grant-access-to-app-configuration"></a>Udělit přístup ke konfiguraci aplikací

1. V [webu Azure portal](https://aka.ms/azconfig/portal), klikněte na tlačítko **všechny prostředky** a úložišti konfigurace aplikace, kterou jste vytvořili v rychlém startu.

2. Vyberte **řízení přístupu (IAM)**.

3. V rámci **zkontrolovat přístup** klikněte na tlačítko **přidat** v **přidat přiřazení role** karty uživatelského rozhraní.

4. Nastavte **Role** bude *Přispěvatel* a **přiřadit přístup k** bude *služby App Service* (v části *přiřazenou systémem spravované Identita*).

5. Nastavte **předplatné** k vašemu předplatnému Azure a vyberte prostředek služby App Service pro aplikace.

6. Klikněte na **Uložit**.

    ![Přidat spravované identity](./media/add-managed-identity.png)

## <a name="use-a-managed-identity"></a>Použití spravované identity

1. Otevřít *appsettings.json*, přidejte následující a nahraďte *< service_endpoint >* (včetně závorek) s adresou URL k úložišti konfigurace aplikace:

    ```json
    "AppConfig": {
        "Endpoint": "<service_endpoint>"
    }
    ```

2. Otevřít *Program.cs* a aktualizovat `CreateWebHostBuilder` metodu nahrazením `config.AddAzureAppConfiguration()` metody.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(o => o.ConnectWithManagedIdentity(settings["AppConfig:Endpoint"]));
            })
            .UseStartup<Startup>();
    ```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-from-local-git"></a>Nasazení z místního Gitu

Nejjednodušší způsob, jak povolit místní nasazení Gitu pro svou aplikaci pomocí Kudu server sestavení je použití Cloud Shell.

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

`az webapp create` Příkazu by vám měl dát něco podobného následující výstup:

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

Nasaďte aplikaci do vzdáleného úložiště Azure pomocí následujícího příkazu. Po zobrazení výzvy k zadání hesla se ujistěte, že zadáváte heslo, které jste vytvořili v části [Konfigurace uživatele nasazení](#configure-a-deployment-user), ne heslo, se kterým se přihlašujete na web Azure Portal.

```bash
git push azure master
```

Automatizace specifické pro modul runtime ve výstupu, jako je MSBuild pro technologii ASP.NET, může se zobrazit `npm install` pro Node.js, a `pip install` pro Python.

### <a name="browse-to-the-azure-web-app"></a>Přechod do webové aplikace Azure

Přejděte do webové aplikace k ověření, že je nasazení obsahu pomocí prohlížeče.

```bash
http://<app_name>.azurewebsites.net
```

![aplikace spuštěná ve službě App Service](../app-service/media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png)

## <a name="use-managed-identity-in-other-languages"></a>Použití spravované identity v jiných jazycích

Poskytovatelé konfigurace aplikace pro rozhraní .NET Framework a jazyka Java Spring mají i integrovanou podporu pro spravovanou identitu. V těchto případech jednoduše použijete koncový bod adresy URL vašeho úložiště konfigurace aplikace namísto jeho úplný připojovací řetězec při konfiguraci poskytovatele. Například pro aplikace konzoly rozhraní .NET Framework v tomto rychlém startu vytvořili, zadejte následující nastavení *App.config* souboru:

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

V tomto kurzu jste přidali do Azure se identita spravované služby pro přístup ke konfiguraci aplikací pro zjednodušení a zlepšení Správa přihlašovacích údajů pro vaši aplikaci. Další informace o použití konfigurací aplikací, i nadále ukázky Azure CLI.

> [!div class="nextstepaction"]
> [Ukázky rozhraní příkazového řádku](./cli-samples.md)

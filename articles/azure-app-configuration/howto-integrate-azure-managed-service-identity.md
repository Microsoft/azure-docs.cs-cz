---
title: Integrace se spravovanými identitami Azure | Microsoft Docs
description: Zjistěte, jak pomocí spravovaných identit Azure ověřit pomocí a získat přístup ke konfiguraci aplikací Azure.
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
ms.openlocfilehash: 3a5517c31cdac0bf6f5ea386a8614d15521d4479
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72035535"
---
# <a name="integrate-with-azure-managed-identities"></a>Integrace se spravovanými identitami Azure

Azure Active Directory [spravované identity](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) vám pomůžou zjednodušit správu tajných kódů pro vaši cloudovou aplikaci. Se spravovanou identitou můžete nastavit kód pro použití instančního objektu vytvořeného pro výpočetní službu Azure, na které běží. Místo samostatného přihlašovacího údaje uloženého v Azure Key Vault nebo v místním připojovacím řetězci použijete spravovanou identitu. 

Konfigurace aplikací Azure a její klientské knihovny pro navýšení .NET Core, .NET a Java jsou integrované s podporou identity spravované služby (MSI). I když ho použít nemusíte, instalační služba MSI eliminuje potřebu přístupového tokenu, který obsahuje tajné klíče. Váš kód má přístup k úložišti konfigurace aplikace jenom pomocí koncového bodu služby. Tuto adresu URL můžete do kódu vložit přímo, aniž byste museli vystavit jakýkoliv tajný klíč.

V tomto kurzu se dozvíte, jak můžete využít výhod služby MSI pro přístup ke konfiguraci aplikací. Sestavuje se ve webové aplikaci představené v rychlých startech. Než budete pokračovat, dokončete nejprve [Vytvoření aplikace ASP.NET Core s konfigurací aplikace](./quickstart-aspnet-core-app.md) .

Kromě toho tento kurz volitelně ukazuje, jak můžete použít MSI ve spojení s Key Vault odkazy na konfiguraci aplikace. Díky tomu můžete hladce přistupovat k tajným klíčům uloženým v Key Vault a také konfiguračním hodnotám v konfiguraci aplikace. Pokud chcete tuto funkci prozkoumat, dokončete nejprve [použití Key Vault odkazů ASP.NET Core](./use-key-vault-references-dotnet-core.md) .

K provedení kroků v tomto kurzu můžete použít libovolný editor kódu. [Visual Studio Code](https://code.visualstudio.com/) je vynikající možnost dostupná na platformách Windows, MacOS a Linux.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Udělte spravované identitě přístup ke konfiguraci aplikace.
> * Nakonfigurujte svou aplikaci tak, aby používala spravovanou identitu, když se připojíte ke konfiguraci aplikace.
> * Volitelně můžete aplikaci nakonfigurovat tak, aby používala spravovanou identitu, když se k Key Vault připojíte prostřednictvím konfigurace aplikace Key Vault odkazem.

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto kurzu potřebujete:

* [.NET Core SDK](https://www.microsoft.com/net/download/windows).
* [Azure Cloud Shell nakonfigurovaný](https://docs.microsoft.com/azure/cloud-shell/quickstart).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="add-a-managed-identity"></a>Přidat spravovanou identitu

Pokud chcete na portálu nastavit spravovanou identitu, musíte nejdřív vytvořit aplikaci jako normální a pak tuto funkci povolit.

1. Vytvořte instanci App Services v [Azure Portal](https://portal.azure.com) běžným způsobem. Přejít na něj na portálu.

2. Přejděte dolů do skupiny **Nastavení** v levém podokně a vyberte možnost **Identita**.

3. Na kartě **přiřazené systémem** přepněte **stav** na **zapnuto** a vyberte **Uložit**.

4. Pokud se zobrazí výzva k povolení spravované identity přiřazené systémem, odpovězte na **Ano** .

    ![Nastavení spravované identity v App Service](./media/set-managed-identity-app-service.png)

## <a name="grant-access-to-app-configuration"></a>Udělení přístupu ke konfiguraci aplikace

1. V [Azure Portal](https://portal.azure.com)vyberte **všechny prostředky** a vyberte úložiště konfigurace aplikace, které jste vytvořili v rychlém startu.

1. Vyberte **Řízení přístupu (IAM)** .

1. Na kartě **kontrolovat přístup** vyberte **Přidat** v uživatelském rozhraní karty **Přidat přiřazení role** .

1. V části **role**vyberte **Přispěvatel**. V části **přiřadit přístup k**vyberte **App Service** v části **spravovaná identita přiřazená systémem**.

1. V části **předplatné**vyberte své předplatné Azure. Vyberte prostředek App Service pro vaši aplikaci.

1. Vyberte **Save** (Uložit).

    ![Přidat spravovanou identitu](./media/add-managed-identity.png)

1. Volitelné: Pokud chcete Key Vault taky udělit přístup, postupujte podle pokynů v části [zajištění Key Vaultho ověřování pomocí spravované identity](https://docs.microsoft.com/azure/key-vault/managed-identity).

## <a name="use-a-managed-identity"></a>Použití spravované identity

1. Najděte adresu URL úložiště konfigurace aplikace tak, že přejdete na obrazovku konfigurace v Azure Portal a pak kliknete na kartu **přístupové klíče** .

1. Otevřete *appSettings. JSON*a přidejte následující skript. Nahraďte *\<service_endpoint >* , včetně závorek, adresou URL vašeho úložiště konfigurace aplikace. 

    ```json
    "AppConfig": {
        "Endpoint": "<service_endpoint>"
    }
    ```

1. Pokud chcete získat přístup pouze k hodnotám uloženým přímo v konfiguraci aplikace, otevřete *program.cs*a aktualizujte metodu `CreateWebHostBuilder` nahrazením metody `config.AddAzureAppConfiguration()`.

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

1. Pokud chcete použít hodnoty konfigurace aplikace a také Key Vault odkazy, otevřete *program.cs*a aktualizujte metodu `CreateWebHostBuilder`, jak je znázorněno níže. Tím se vytvoří nový `KeyVaultClient` pomocí `AzureServiceTokenProvider` a předá tento odkaz volání metody `UseAzureKeyVault`.

    ```csharp
        public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
                    KeyVaultClient kvClient = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
                    
                    config.AddAzureAppConfiguration(options => options.ConnectWithManagedIdentity(settings["AppConfig:Endpoint"])).UseAzureKeyVault(kvClient));
                })
                .UseStartup<Startup>();
    ```

    Nyní máte přístup k Key Vault odkazům stejně jako jakýkoli jiný konfigurační klíč aplikace. Poskytovatel konfigurace použije `KeyVaultClient`, který jste nakonfigurovali k ověřování Key Vault a načtení hodnoty.

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-from-local-git"></a>Nasazení z místního Gitu

Nejjednodušší způsob, jak povolit místní nasazení Git pro vaši aplikaci pomocí serveru Kudu Build, je použít Azure Cloud Shell.

### <a name="configure-a-deployment-user"></a>Konfigurace uživatele nasazení

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Povolení místního Gitu pomocí Kudu
Pokud pro vaši aplikaci nemáte místní úložiště Git, budete ho muset inicializovat. Pokud to chcete provést, spusťte následující příkazy z adresáře projektu vaší aplikace:

```cmd
git init
git add .
git commit -m "Initial version"
```

Pokud chcete povolit místní nasazení Git pro vaši aplikaci pomocí serveru Kudu Build, spusťte v Cloud Shell [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) .

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Pokud chcete místo toho vytvořit aplikaci s povoleným Git, spusťte [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) v Cloud shell s parametrem `--deployment-local-git`.

```azurecli-interactive
az webapp create --name <app_name> --resource-group <group_name> --plan <plan_name> --deployment-local-git
```

Příkaz `az webapp create` vám poskytne něco podobného následujícímu výstupu:

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

Zpět v _okně místního terminálu_ přidejte vzdálené úložiště Azure do místního úložiště Git. Nahraďte _\<url >_ adresou URL vzdáleného úložiště Git, kterou jste získali z [Povolení Git pro vaši aplikaci](#enable-local-git-with-kudu).

```bash
git remote add azure <url>
```

Nasaďte aplikaci do vzdáleného úložiště Azure pomocí následujícího příkazu. Jakmile budete vyzváni k zadání hesla, zadejte heslo, které jste vytvořili v části [Konfigurace uživatele nasazení](#configure-a-deployment-user). Nepoužívejte heslo, které používáte k přihlášení do Azure Portal.

```bash
git push azure master
```

Ve výstupu se může zobrazit automatizace specifická pro modul runtime, jako je například MSBuild pro ASP.NET, `npm install` pro Node. js a `pip install` pro Python.

### <a name="browse-to-the-azure-web-app"></a>Přechod do webové aplikace Azure

Přejděte do webové aplikace pomocí prohlížeče, abyste ověřili, že je obsah nasazený.

```bash
http://<app_name>.azurewebsites.net
```

![Aplikace spuštěná v App Service](../app-service/media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png)

## <a name="use-managed-identity-in-other-languages"></a>Použití spravované identity v jiných jazycích

Poskytovatelé konfigurace aplikací pro .NET Framework a Java pružiny mají také integrovanou podporu pro spravovanou identitu. V těchto případech použijte koncový bod adresy URL vašeho úložiště konfigurace aplikace namísto úplného připojovacího řetězce při konfiguraci poskytovatele. Například pro konzolovou aplikaci .NET Framework vytvořenou v rychlém startu zadejte následující nastavení v souboru *App. config* :

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

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Ukázky rozhraní příkazového řádku](./cli-samples.md)

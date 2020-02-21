---
title: Integrace se spravovanými identitami Azure
description: Zjistěte, jak pomocí spravovaných identit Azure ověřit pomocí a získat přístup ke konfiguraci aplikací Azure.
ms.service: azure-app-configuration
author: lisaguthrie
ms.topic: conceptual
ms.date: 12/29/2019
ms.author: lcozzens
ms.openlocfilehash: 2cdeb0d513230cac5d03f85f2189f15c818798fd
ms.sourcegitcommit: 0a9419aeba64170c302f7201acdd513bb4b346c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/20/2020
ms.locfileid: "77500407"
---
# <a name="integrate-with-azure-managed-identities"></a>Integrace se spravovanými identitami Azure

Azure Active Directory [spravované identity](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) vám pomůžou zjednodušit správu tajných kódů pro vaši cloudovou aplikaci. Se spravovanou identitou může váš kód používat instanční objekt, který byl vytvořen pro službu Azure, na které běží. Místo samostatného přihlašovacího údaje uloženého v Azure Key Vault nebo v místním připojovacím řetězci použijete spravovanou identitu. 

Konfigurace aplikace Azure a její klientské knihovny pro navýšení .NET Core, .NET Framework a Java mají do nich vestavěnou podporu spravovaných identit. I když ho použít nemusíte, spravovaná identita eliminuje potřebu přístupového tokenu, který obsahuje tajné klíče. Váš kód má přístup k úložišti konfigurace aplikace jenom pomocí koncového bodu služby. Tuto adresu URL můžete do kódu vložit přímo, aniž byste museli vystavit jakýkoliv tajný klíč.

V tomto kurzu se dozvíte, jak můžete využít spravovanou identitu pro přístup ke konfiguraci aplikací. Sestavuje se ve webové aplikaci představené v rychlých startech. Než budete pokračovat, dokončete nejprve [Vytvoření aplikace ASP.NET Core s konfigurací aplikace](./quickstart-aspnet-core-app.md) .

V tomto kurzu se také dozvíte, jak můžete použít spravovanou identitu ve spojení s Key Vault odkazy na konfiguraci aplikace. S jedinou spravovanou identitou můžete bezproblémově přistupovat k oběma tajným klíčům z Key Vault a konfiguračních hodnot z konfigurace aplikace. Pokud chcete tuto funkci prozkoumat, dokončete nejprve [použití Key Vault odkazů ASP.NET Core](./use-key-vault-references-dotnet-core.md) .

K provedení kroků v tomto kurzu můžete použít libovolný editor kódu. [Visual Studio Code](https://code.visualstudio.com/) je vynikající možnost dostupná na platformách Windows, MacOS a Linux.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Udělte spravované identitě přístup ke konfiguraci aplikace.
> * Nakonfigurujte svou aplikaci tak, aby používala spravovanou identitu, když se připojíte ke konfiguraci aplikace.
> * Volitelně můžete aplikaci nakonfigurovat tak, aby používala spravovanou identitu, když se k Key Vault připojíte prostřednictvím konfigurace aplikace Key Vault odkazem.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete:

* [.NET Core SDK](https://www.microsoft.com/net/download/windows).
* [Azure Cloud Shell nakonfigurovaný](https://docs.microsoft.com/azure/cloud-shell/quickstart).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="add-a-managed-identity"></a>Přidat spravovanou identitu

Pokud chcete na portálu nastavit spravovanou identitu, musíte nejdřív vytvořit aplikaci a pak ji povolit.

1. Vytvořte instanci App Services v [Azure Portal](https://portal.azure.com) běžným způsobem. Přejít na něj na portálu.

1. Přejděte dolů do skupiny **Nastavení** v levém podokně a vyberte možnost **Identita**.

1. Na kartě **přiřazené systémem** přepněte **stav** na **zapnuto** a vyberte **Uložit**.

1. Pokud se zobrazí výzva k povolení spravované identity přiřazené systémem, odpovězte na **Ano** .

    ![Nastavení spravované identity v App Service](./media/set-managed-identity-app-service.png)

## <a name="grant-access-to-app-configuration"></a>Udělení přístupu ke konfiguraci aplikace

1. V [Azure Portal](https://portal.azure.com)vyberte **všechny prostředky** a vyberte úložiště konfigurace aplikace, které jste vytvořili v rychlém startu.

1. Vyberte **Řízení přístupu (IAM)** .

1. Na kartě **kontrolovat přístup** vyberte **Přidat** v uživatelském rozhraní karty **Přidat přiřazení role** .

1. V části **role**vyberte **čtečka konfiguračních dat aplikace**. V části **přiřadit přístup k**vyberte **App Service** v části **spravovaná identita přiřazená systémem**.

1. V části **předplatné**vyberte své předplatné Azure. Vyberte prostředek App Service pro vaši aplikaci.

1. Vyberte **Save** (Uložit).

    ![Přidat spravovanou identitu](./media/add-managed-identity.png)

1. Volitelné: Pokud chcete Key Vault taky udělit přístup, postupujte podle pokynů v části [zajištění Key Vaultho ověřování pomocí spravované identity](https://docs.microsoft.com/azure/key-vault/managed-identity).

## <a name="use-a-managed-identity"></a>Použití spravované identity

1. Přidejte odkaz na balíček *Azure. identity* :

    ```cli
    dotnet add package Azure.Identity
    ```

1. Najděte koncový bod do úložiště konfigurace aplikace. Tato adresa URL je uvedena na kartě **přístupové klíče** pro úložiště v Azure Portal.

1. Otevřete *appSettings. JSON*a přidejte následující skript. Nahraďte *\<service_endpoint >* včetně závorek s adresou URL vašeho úložiště konfigurace aplikace. 

    ```json
    "AppConfig": {
        "Endpoint": "<service_endpoint>"
    }
    ```

1. Otevřete *program.cs*a přidejte odkaz na `Azure.Identity` a `Microsoft.Azure.Services.AppAuthentication` obory názvů:

    ```csharp-interactive
    using Azure.Identity;
    ```

1. Pokud chcete získat přístup pouze k hodnotám uloženým přímo v konfiguraci aplikace, aktualizujte metodu `CreateWebHostBuilder` nahrazením metody `config.AddAzureAppConfiguration()`.

    > [!IMPORTANT]
    > `CreateHostBuilder` nahrazuje `CreateWebHostBuilder` v .NET Core 3,0.  Vyberte správnou syntaxi na základě vašeho prostředí.

    ### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

    ```csharp
        public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential()));
                })
                .UseStartup<Startup>();
    ```

    ### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

    ```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential()));
                })
                .UseStartup<Startup>());
    ```
    ---

1. Pokud chcete použít konfigurační hodnoty aplikace i odkazy na Key Vault, aktualizujte *program.cs* , jak je znázorněno níže. Tento kód vytvoří nový `KeyVaultClient` pomocí `AzureServiceTokenProvider` a předá tento odkaz voláním metody `UseAzureKeyVault`.

    ### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

    ```csharp
            public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
                WebHost.CreateDefaultBuilder(args)
                    .ConfigureAppConfiguration((hostingContext, config) =>
                    {
                        var settings = config.Build();
                        AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
                        KeyVaultClient kvClient = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
                        
                        config.AddAzureAppConfiguration(options => options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential()).UseAzureKeyVault(kvClient));
                    })
                    .UseStartup<Startup>();
    ```

    ### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

    ```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                        AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
                        KeyVaultClient kvClient = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
                        
                        config.AddAzureAppConfiguration(options => options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential()).UseAzureKeyVault(kvClient));
                    })
                    .UseStartup<Startup>());
    ```
    ---

    Nyní máte přístup k Key Vault odkazům stejně jako jakýkoli jiný konfigurační klíč aplikace. Poskytovatel konfigurace použije `KeyVaultClient`, které jste nakonfigurovali k ověřování, aby Key Vault a načetl hodnotu.

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="deploy-from-local-git"></a>Nasazení z místního Gitu

Nejjednodušší způsob, jak povolit místní nasazení Git pro vaši aplikaci pomocí serveru Kudu Build, je použít [Azure Cloud Shell](https://shell.azure.com).

### <a name="configure-a-deployment-user"></a>Konfigurace uživatele nasazení

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Povolení místního Gitu pomocí Kudu
Pokud pro vaši aplikaci nemáte místní úložiště Git, budete ho muset inicializovat. Pokud chcete inicializovat místní úložiště Git, spusťte následující příkazy z adresáře projektu vaší aplikace:

```cmd
git init
git add .
git commit -m "Initial version"
```

Pokud chcete povolit místní nasazení Git pro vaši aplikaci s Kudu Build serverem, spusťte [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) v Cloud Shell.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Tento příkaz vám poskytne něco podobného následujícímu výstupu:

```json
{
  "url": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git"
}
```

### <a name="deploy-your-project"></a>Nasazení projektu

V _okně místního terminálu_přidejte vzdálené úložiště Azure do místního úložiště Git. Nahraďte _adresu url\<>_ adresou URL vzdáleného úložiště Git, kterou jste získali v části [Povolení místního Gitu pomocí Kudu](#enable-local-git-with-kudu).

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

Poskytovatelé konfigurace aplikací pro .NET Framework a Java pružiny mají také integrovanou podporu pro spravovanou identitu. Když nakonfigurujete některého z těchto zprostředkovatelů, můžete použít koncový bod adresy URL vašeho obchodu místo jeho úplného připojovacího řetězce. 

Například můžete aktualizovat konzolovou aplikaci .NET Framework vytvořenou v rychlém startu a zadat v souboru *App. config* následující nastavení:

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
V tomto kurzu jste přidali spravovanou identitu Azure, abyste zjednodušili přístup ke konfiguraci aplikací a vylepšili správu přihlašovacích údajů pro vaši aplikaci. Další informace o tom, jak používat konfiguraci aplikací, najdete v ukázkách Azure CLI.

> [!div class="nextstepaction"]
> [Ukázky rozhraní příkazového řádku](./cli-samples.md)

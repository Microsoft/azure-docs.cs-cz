---
title: Ověřování pomocí spravovaných identit Azure
titleSuffix: Azure App Configuration
description: Ověření konfigurace aplikací Azure pomocí spravovaných identit Azure
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 2/25/2020
ms.openlocfilehash: bf97a1eae758778efc8d800666af4a5fcb574429
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80056838"
---
# <a name="integrate-with-azure-managed-identities"></a>Integrace se spravovanými identitami Azure

[Spravované identity služby](../active-directory/managed-identities-azure-resources/overview.md) Azure Active Directory zjednodušují správu tajných klíčů pro vaši cloudovou aplikaci. Se spravovanou identitou může váš kód použít instanční objekt vytvořený pro službu Azure, na které běží. Místo samostatného pověření uloženého v úložišti klíčů Azure nebo v místním připojovacím řetězci se používá spravovaná identita. 

Azure App Configuration a jeho klientské knihovny .NET Core, .NET Framework a Java Spring mají integrovanou podporu identit, která je součástí. I když není nutné ji používat, spravovaná identita eliminuje potřebu přístupového tokenu, který obsahuje tajné klíče. Váš kód může přistupovat k úložišti konfigurace aplikací pouze pomocí koncového bodu služby. Tuto adresu URL můžete vložit přímo do kódu bez vystavení jakéhokoli tajného klíče.

Tento článek ukazuje, jak můžete využít spravované identity pro přístup ke konfiguraci aplikace. Staví na webové aplikaci zavedené v rychlých startech. Než budete pokračovat, [nejprve vytvořte aplikaci ASP.NET Core s konfigurací aplikace.](./quickstart-aspnet-core-app.md)

Tento článek také ukazuje, jak můžete použít spravovanou identitu ve spojení s odkazy na trezor klíčů konfigurace aplikace. Díky jedné spravované identitě můžete bez problémů přistupovat k tajným klíčům z trezoru klíčů i konfiguračním hodnotám z konfigurace aplikace. Pokud chcete tuto možnost prozkoumat, [nejprve dokončete použití odkazů trezoru klíčů pomocí ASP.NET jádra.](./use-key-vault-references-dotnet-core.md)

Můžete použít libovolný editor kódu k tomu kroky v tomto kurzu. [Visual Studio Code](https://code.visualstudio.com/) je vynikající volba dostupná na platformách Windows, macOS a Linux.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Udělte spravovanou identitu přístup ke konfiguraci aplikace.
> * Nakonfigurujte aplikaci tak, aby při připojení ke konfiguraci aplikace používala spravovanou identitu.
> * Volitelně můžete aplikaci nakonfigurovat tak, aby při připojení ke službě Trezor klíčů používala spravovanou identitu prostřednictvím odkazu na trezor konfiguračního klíče aplikace.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete:

* [Sada SDK jádra .NET](https://www.microsoft.com/net/download/windows).
* [Azure Cloud Shell nakonfigurován](https://docs.microsoft.com/azure/cloud-shell/quickstart).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="add-a-managed-identity"></a>Přidání spravované identity

Chcete-li nastavit spravovanou identitu na portálu, nejprve vytvořte aplikaci a poté tuto funkci povolte.

1. Vytvořte instanci služby App Services na [webu Azure Portal](https://portal.azure.com) obvyklým způsobem. Jdi na to na portál.

1. V levém podokně přejděte dolů do skupiny **Nastavení** a vyberte **Identita**.

1. Na kartě **Systém přiřazený** přepněte **stav** **na Zapnuto** a vyberte **Uložit**.

1. Odpověď **Ano** po zobrazení výzvy k povolení spravované identity přiřazené systému.

    ![Nastavení spravované identity ve službě App Service](./media/set-managed-identity-app-service.png)

## <a name="grant-access-to-app-configuration"></a>Udělení přístupu ke konfiguraci aplikace

1. Na [webu Azure Portal](https://portal.azure.com)vyberte **Všechny prostředky** a vyberte úložiště konfigurace aplikací, které jste vytvořili v rychlém startu.

1. Vyberte **řízení přístupu (IAM)**.

1. Na kartě **Zkontrolovat přístup** vyberte **Přidat** v uzdu přidat kartu **přiřazení role.**

1. V části **Role**vyberte **Aplikaci Configuration Data Reader**. V části **Přiřadit přístup k možnostem**vyberte **Službu Aplikace** v části Systém **přiřazená spravovaná identita**.

1. V části **Předplatné**vyberte předplatné Azure. Vyberte prostředek služby App Service pro vaši aplikaci.

1. Vyberte **Uložit**.

    ![Přidání spravované identity](./media/add-managed-identity.png)

1. Volitelné: Pokud chcete udělit přístup k trezoru klíčů také, postupujte podle pokynů v [poskytnout ověřování trezoru klíčů se spravovanou identitou](https://docs.microsoft.com/azure/key-vault/managed-identity).

## <a name="use-a-managed-identity"></a>Použití spravované identity

1. Přidejte odkaz na balíček *Azure.Identity:*

    ```cli
    dotnet add package Azure.Identity
    ```

1. Najděte koncový bod do úložiště konfigurace aplikací. Tato adresa URL je uvedená na kartě **Přístupové klíče** pro úložiště na webu Azure Portal.

1. Otevřete *soubor appsettings.json*a přidejte následující skript. Nahraďte * \<service_endpoint>*, včetně závorek, adresou URL obchodu konfigurace aplikací. 

    ```json
    "AppConfig": {
        "Endpoint": "<service_endpoint>"
    }
    ```

1. Otevřete *Program.cs*a přidejte `Azure.Identity` `Microsoft.Azure.Services.AppAuthentication` odkaz na obory názvů a:

    ```csharp-interactive
    using Azure.Identity;
    ```

1. Pokud chcete získat přístup pouze k hodnotám `CreateWebHostBuilder` uloženým přímo `config.AddAzureAppConfiguration()` v konfiguraci aplikace, aktualizujte metodu nahrazením metody.

    > [!IMPORTANT]
    > `CreateHostBuilder`nahrazuje `CreateWebHostBuilder` v rozhraní .NET Core 3.0.  Vyberte správnou syntaxi na základě vašeho prostředí.

    ### <a name="net-core-2x"></a>[.NET Jádro 2.x](#tab/core2x)

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

    ### <a name="net-core-3x"></a>[.NET Jádro 3.x](#tab/core3x)

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

1. Chcete-li použít hodnoty konfigurace aplikace i odkazy na trezor klíčů, aktualizujte *Program.cs,* jak je znázorněno níže. Tento kód vytvoří `KeyVaultClient` nový `AzureServiceTokenProvider` pomocí a předá tento `UseAzureKeyVault` odkaz na volání metody.

    ### <a name="net-core-2x"></a>[.NET Jádro 2.x](#tab/core2x)

    ```csharp
            public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
                WebHost.CreateDefaultBuilder(args)
                    .ConfigureAppConfiguration((hostingContext, config) =>
                    {
                        var settings = config.Build();
                        var credentials = new ManagedIdentityCredential();

                        config.AddAzureAppConfiguration(options =>
                        {
                            options.Connect(new Uri(settings["AppConfig:Endpoint"]), credentials)
                                    .ConfigureKeyVault(kv =>
                                    {
                                        kv.SetCredential(credentials);
                                    });
                        });
                    })
                    .UseStartup<Startup>();
    ```

    ### <a name="net-core-3x"></a>[.NET Jádro 3.x](#tab/core3x)

    ```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
                    {
                        var settings = config.Build();
                        var credentials = new ManagedIdentityCredential();

                        config.AddAzureAppConfiguration(options =>
                        {
                            options.Connect(new Uri(settings["AppConfig:Endpoint"]), credentials)
                                    .ConfigureKeyVault(kv =>
                                    {
                                        kv.SetCredential(credentials);
                                    });
                        });
                    })
                    .UseStartup<Startup>());
    ```
    ---

    Nyní můžete přistupovat k odkazům trezoru klíčů stejně jako jakýkoli jiný konfigurační klíč aplikace. Zprostředkovatel konfigurace použije konfiguraci, `KeyVaultClient` kterou jste nakonfigurovali k ověření trezoru klíčů a načtení hodnoty.

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="deploy-from-local-git"></a>Nasazení z místního Gitu

Nejjednodušší způsob, jak povolit místní nasazení Gitu pro vaši aplikaci se serverem sestavení Kudu, je použít [Azure Cloud Shell](https://shell.azure.com).

### <a name="configure-a-deployment-user"></a>Konfigurace uživatele nasazení

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Povolení místního Gitu pomocí Kudu
Pokud pro vaši aplikaci nemáte místní úložiště git, budete ho muset inicializovat. Chcete-li inicializovat místní úložiště git, spusťte z adresáře projektu aplikace následující příkazy:

```cmd
git init
git add .
git commit -m "Initial version"
```

Pokud chcete povolit lokální nasazení Gitu pro vaši [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) aplikaci se serverem pro sestavení Kudu, spouštějte je v Cloud Shellu.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Tento příkaz poskytuje něco podobného následujícímu výstupu:

```json
{
  "url": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git"
}
```

### <a name="deploy-your-project"></a>Nasazení projektu

V _okně místního terminálu_přidejte do místního úložiště Git dálkové ovládání Azure. Nahraďte _ \<url>_ url ovladače Git, který jste dostali z [Povolit místní Git s Kudu](#enable-local-git-with-kudu).

```bash
git remote add azure <url>
```

Nasaďte aplikaci do vzdáleného úložiště Azure pomocí následujícího příkazu. Po zobrazení výzvy k zadání hesla zadejte heslo, které jste vytvořili, v [části Konfigurace uživatele nasazení](#configure-a-deployment-user). Nepoužívejte heslo, které používáte k přihlášení k portálu Azure.

```bash
git push azure master
```

Ve výstupu se může zobrazit automatizace specifická pro modul `npm install` runtime, například MSBuild pro ASP.NET, Node.js a `pip install` pro Python.

### <a name="browse-to-the-azure-web-app"></a>Přechod do webové aplikace Azure

Přejděte do webové aplikace pomocí prohlížeče a ověřte, zda je obsah nasazený.

```bash
http://<app_name>.azurewebsites.net
```

## <a name="use-managed-identity-in-other-languages"></a>Použití spravované identity v jiných jazycích

Poskytovatelé konfigurace aplikací pro rozhraní .NET Framework a Java Spring mají také integrovanou podporu spravované identity. Při konfiguraci jednoho z těchto zprostředkovatelů můžete použít koncový bod URL vašeho obchodu namísto jeho úplného připojovacího řetězce. 

Můžete například aktualizovat konzolovou aplikaci rozhraní .NET Framework vytvořenou v rychlém startu a zadat následující nastavení v souboru *App.config:*

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
V tomto kurzu jste přidali spravovanou identitu Azure, abyste zjednodušili přístup ke konfiguraci aplikací a zlepšili správu přihlašovacích údajů pro vaši aplikaci. Další informace o tom, jak používat konfiguraci aplikací, pokračujte ukázkami azure cli.

> [!div class="nextstepaction"]
> [Ukázky rozhraní příkazového řádku](./cli-samples.md)

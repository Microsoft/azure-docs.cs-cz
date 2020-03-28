---
title: Kurz pro použití odkazů na konfigurační klíč Azure V aplikaci ASP.NET | Dokumenty společnosti Microsoft
description: V tomto kurzu se dozvíte, jak používat odkazy azure app konfigurace trezoru klíčů z aplikace ASP.NET Core
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 01/21/2020
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: eceb4a9d4e0cc84166280f30b094b82088f53a4a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79475302"
---
# <a name="tutorial-use-key-vault-references-in-an-aspnet-core-app"></a>Kurz: Použití odkazů na trezor klíčů v aplikaci ASP.NET Core

V tomto kurzu se dozvíte, jak používat službu Konfigurace aplikací Azure společně s Azure Key Vault. Konfigurace aplikací a trezor klíčů jsou doplňkové služby používané vedle sebe ve většině nasazení aplikací.

Konfigurace aplikace pomáhá využívat služby společně vytvořením klíčů, které odkazují na hodnoty uložené v trezoru klíčů. Když konfigurace aplikace vytvoří takové klíče, ukládá identifikátory URI hodnoty trezoru klíčů spíše než hodnoty samotné.

Vaše aplikace používá zprostředkovatele klienta Konfigurace aplikace k načtení odkazů trezoru klíčů, stejně jako u všech ostatních klíčů uložených v konfiguraci aplikace. V tomto případě hodnoty uložené v konfiguraci aplikace jsou identifikátory URI, které odkazují na hodnoty v trezoru klíčů. Nejedná se o hodnoty nebo pověření trezoru klíčů. Vzhledem k tomu, že zprostředkovatel klienta rozpozná klíče jako odkazy trezoru klíčů, používá trezor klíčů k načtení jejich hodnot.

Vaše aplikace je zodpovědná za správné ověření konfigurace aplikace a trezoru klíčů. Tyto dvě služby nekomunikují přímo.

Tento kurz ukazuje, jak implementovat odkazy trezoru klíčů ve vašem kódu. Staví na webové aplikaci zavedené v rychlých startech. Než budete pokračovat, nejprve [dokončete vytvoření aplikace ASP.NET Jádro pomocí konfigurace aplikace.](./quickstart-aspnet-core-app.md)

Můžete použít libovolný editor kódu k tomu kroky v tomto kurzu. [Visual Studio Code](https://code.visualstudio.com/) je například editor kódu pro různé platformy, který je k dispozici pro operační systémy Windows, macOS a Linux.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte konfigurační klíč aplikace, který odkazuje na hodnotu uloženou v trezoru klíčů.
> * Získejte přístup k hodnotě tohoto klíče z webové aplikace ASP.NET Core.

## <a name="prerequisites"></a>Požadavky

Než začnete tento kurz, nainstalujte sadu [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-vault"></a>Vytvoření trezoru

1. V levém horním rohu portálu Azure vyberte možnost **Vytvořit prostředek:**

    ![Výstup po dokončení vytvoření trezoru klíčů](./media/quickstarts/search-services.png)
1. Do vyhledávacího pole zadejte **Trezor klíčů**.
1. V seznamu výsledků vyberte **trezory kláves** vlevo.
1. V **trezorech klíčů**vyberte **Přidat**.
1. Vpravo v **tématu Vytvořit trezor klíčů**zadejte následující informace:
    - Výběrem **možnosti Předplatné** vyberte předplatné.
    - Ve **skupině prostředků**vyberte Vytvořit **nový** a zadejte název skupiny prostředků.
    - V **názvu trezoru klíčů**je vyžadován jedinečný název. V tomto kurzu zadejte **Contoso-vault2**.
    - V rozevíracím seznamu **Oblast** zvolte umístění.
1. Ostatní možnosti **úložiště klíčů** ponechte s jejich výchozími hodnotami.
1. Vyberte **Vytvořit**.

V tomto okamžiku je váš účet Azure jediný oprávněný pro přístup k tomuto novému trezoru.

![Výstup po dokončení vytvoření trezoru klíčů](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Přidání tajného klíče do služby Key Vault

Chcete-li do trezoru přidat tajný klíč, musíte provést pouze několik dalších kroků. V takovém případě přidejte zprávu, kterou můžete použít k testování načítání trezoru klíčů. Zpráva se nazývá **Zpráva**a uložíte do ní hodnotu "Hello from Key Vault".

1. Na stránkách vlastností trezoru klíčů vyberte **položku Tajné položky**.
1. Vyberte **Generovat/importovat**.
1. V **podokně Vytvořit tajný klíč** zadejte následující hodnoty:
    - **Možnosti nahrávání**: Zadejte **ručně**.
    - **Název**: Zadejte **zprávu**.
    - **Hodnota**: Zadejte **Hello z trezoru klíčů**.
1. Ponechte ostatní **Vytvořte tajné** vlastnosti s jejich výchozí hodnoty.
1. Vyberte **Vytvořit**.

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Přidání odkazu trezoru klíčů do konfigurace aplikace

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Vyberte **Všechny prostředky**a pak vyberte instanci úložiště konfigurace aplikace, kterou jste vytvořili v rychlém startu.

1. Vyberte **Průzkumník konfigurace**.

1. Vyberte **+ Vytvořit** > **odkaz na trezor klíčů**a zadejte následující hodnoty:
    - **Klíč**: Vyberte **TestApp:Nastavení:KeyVaultMessage**.
    - **Popisek**: Ponechejte tuto hodnotu prázdnou.
    - **Odběr**, **Skupina prostředků**a **Trezor klíčů**: Zadejte hodnoty odpovídající hodnotám v trezoru klíčů, který jste vytvořili v předchozí části.
    - **Tajný klíč**: Vyberte tajný klíč s názvem **Zpráva,** který jste vytvořili v předchozí části.

## <a name="connect-to-key-vault"></a>Připojení k trezoru klíčů

1. V tomto kurzu použijete instanční objekt pro ověřování trezoru klíčů. Chcete-li vytvořit tento instanční objekt, použijte příkaz [AZ az sp az azure Azure cli:](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)

    ```azurecli
    az ad sp create-for-rbac -n "http://mySP" --sdk-auth
    ```

    Tato operace vrátí řadu párů klíč/hodnota:

    ```console
    {
    "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
    "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
    "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
    "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
    }
    ```

1. Spuštěním následujícího příkazu umožníte instančnímu objektu přístup k trezoru klíčů:

    ```cmd
    az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
    ```

1. Přidejte proměnné prostředí pro uložení hodnot *clientId*, *clientSecret*a *tenantId*.

    #### <a name="windows-command-prompt"></a>[Příkazový řádek systému Windows](#tab/cmd)

    ```cmd
    setx AZURE_CLIENT_ID <clientId-of-your-service-principal>
    setx AZURE_CLIENT_SECRET <clientSecret-of-your-service-principal>
    setx AZURE_TENANT_ID <tenantId-of-your-service-principal>
    ```

    #### <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```PowerShell
    $Env:AZURE_CLIENT_ID = <clientId-of-your-service-principal>
    $Env:AZURE_CLIENT_SECRET = <clientSecret-of-your-service-principal>
    $Env:AZURE_TENANT_ID = <tenantId-of-your-service-principal>
    ```

    #### <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    export AZURE_CLIENT_ID = <clientId-of-your-service-principal>
    export AZURE_CLIENT_SECRET = <clientSecret-of-your-service-principal>
    export AZURE_TENANT_ID = <tenantId-of-your-service-principal>
    ```

    ---

    > [!NOTE]
    > Tato pověření trezoru klíčů se používají pouze v rámci vaší aplikace. Aplikace se ověřuje přímo do trezoru klíčů s těmito pověřeními. Nikdy nejsou předány službě Konfigurace aplikace.

1. Restartujte terminál a načtěte tyto nové proměnné prostředí.

## <a name="update-your-code-to-use-a-key-vault-reference"></a>Aktualizace kódu pro použití odkazu trezoru klíčů

1. Přidejte odkaz na požadované balíčky NuGet spuštěním následujícího příkazu:

    ```dotnetcli
    dotnet add package Microsoft.Azure.KeyVault
    dotnet add package Azure.Identity
    ```

1. Otevřete *Program.cs*a přidejte odkazy na následující požadované balíčky:

    ```csharp
    using Microsoft.Azure.KeyVault;
    using Azure.Identity;
    ```

1. Aktualizujte `CreateWebHostBuilder` metodu pro použití `config.AddAzureAppConfiguration` konfigurace aplikace voláním metody. Zahrňte `ConfigureKeyVault` tuto možnost a předejte správná pověření do trezoru klíčů.

    #### <a name="net-core-2x"></a>[.NET Jádro 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                            .ConfigureKeyVault(kv =>
                            {
                                kv.SetCredential(new DefaultAzureCredential());
                            });
                });
            })
            .UseStartup<Startup>();
    ```

    #### <a name="net-core-3x"></a>[.NET Jádro 3.x](#tab/core3x)

    ```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                            .ConfigureKeyVault(kv =>
                            {
                                kv.SetCredential(new DefaultAzureCredential());
                            });
                });
            })
            .UseStartup<Startup>());
    ```

1. Když inicializujete připojení ke konfiguraci aplikace, nastavíte `ConfigureKeyVault` připojení k trezoru klíčů voláním metody. Po inicializaci můžete přistupovat k hodnotám odkazů trezoru klíčů stejným způsobem jako k hodnotám běžných klíčů konfigurace aplikace.

    Chcete-li tento proces zobrazit v akci, otevřete *soubor Index.cshtml* ve složce **Zobrazení** > **domů.** Nahraďte jeho obsah následujícím kódem:

    ```html
    @using Microsoft.Extensions.Configuration
    @inject IConfiguration Configuration

    <style>
        body {
            background-color: @Configuration["TestApp:Settings:BackgroundColor"]
        }
        h1 {
            color: @Configuration["TestApp:Settings:FontColor"];
            font-size: @Configuration["TestApp:Settings:FontSize"]px;
        }
    </style>

    <h1>@Configuration["TestApp:Settings:Message"]
        and @Configuration["TestApp:Settings:KeyVaultMessage"]</h1>
    ```

    Přístup k hodnotě odkazu Key Vault **TestApp:Settings:KeyVaultMessage** stejným způsobem jako pro konfigurační hodnotu **TestApp:Settings:Message**.

## <a name="build-and-run-the-app-locally"></a>Sestavení a spuštění aplikace místně

1. Chcete-li vytvořit aplikaci pomocí rozhraní PŘÍKAZU .NET Core CLI, spusťte v příkazovém prostředí následující příkaz:

    ```dotnetcli
    dotnet build
    ```

1. Po dokončení sestavení spusťte webovou aplikaci místně pomocí následujícího příkazu:

    ```dotnetcli
    dotnet run
    ```

1. Otevřete okno prohlížeče a `http://localhost:5000`přejděte na stránku , což je výchozí adresa URL webové aplikace hostované místně.

    ![Spuštění místní aplikace rychlého startu](./media/key-vault-reference-launch-local.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili klíč konfigurace aplikace, který odkazuje na hodnotu uloženou v trezoru klíčů. Chcete-li se dozvědět, jak přidat identitu spravované služby Azure, která zjednodušuje přístup ke konfiguraci aplikací a trezoru klíčů, pokračujte dalším kurzem.

> [!div class="nextstepaction"]
> [Integrace spravované identity](./howto-integrate-azure-managed-service-identity.md)

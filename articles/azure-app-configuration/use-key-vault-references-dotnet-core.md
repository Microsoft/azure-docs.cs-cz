---
title: Kurz použití konfigurace aplikace Azure Key Vault odkazy v aplikaci ASP.NET Core | Microsoft Docs
description: V tomto kurzu se naučíte používat Key Vault odkazy na konfiguraci aplikací Azure z aplikace ASP.NET Core.
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/08/2020
ms.author: alkemper
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 37bc7fbcd366455668d5316e45ffbf79127a49f3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99981218"
---
# <a name="tutorial-use-key-vault-references-in-an-aspnet-core-app"></a>Kurz: použití odkazů Key Vault v aplikaci ASP.NET Core

V tomto kurzu se naučíte používat službu Azure App Configuration Service společně s Azure Key Vault. Konfigurace aplikace a Key Vault jsou doplňkové služby, které jsou používány souběžně ve většině nasazení aplikace.

Konfigurace aplikací pomáhá používat služby dohromady vytvořením klíčů, které odkazují na hodnoty uložené v Key Vault. Když konfigurace aplikace tyto klíče vytvoří, uloží identifikátory URI Key Vault hodnoty místo samotných hodnot.

Vaše aplikace používá poskytovatele klienta konfigurace aplikace k načtení Key Vault odkazů, stejně jako u všech ostatních klíčů uložených v konfiguraci aplikace. V tomto případě hodnoty uložené v konfiguraci aplikace jsou identifikátory URI, které odkazují na hodnoty v Key Vault. Nejsou Key Vault hodnoty ani přihlašovací údaje. Vzhledem k tomu, že poskytovatel klienta rozpozná klíče jako odkazy Key Vault, používá Key Vault k načtení jejich hodnot.

Vaše aplikace zodpovídá za to, že se správně ověřuje jak konfigurace aplikace, tak i Key Vault. Tyto dvě služby nekomunikují přímo.

V tomto kurzu se dozvíte, jak implementovat Key Vault odkazy v kódu. Sestavuje se ve webové aplikaci představené v rychlých startech. Než budete pokračovat, dokončete nejprve [Vytvoření aplikace ASP.NET Core s konfigurací aplikace](./quickstart-aspnet-core-app.md) .

K provedení kroků v tomto kurzu můžete použít libovolný editor kódu. Například [Visual Studio Code](https://code.visualstudio.com/) je editor kódu pro různé platformy, který je k dispozici pro operační systémy Windows, MacOS a Linux.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte konfigurační klíč aplikace, který odkazuje na hodnotu uloženou v Key Vault.
> * Přístup k hodnotě tohoto klíče z ASP.NET Core webové aplikace.

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem, nainstalujte [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-vault"></a>Vytvoření trezoru

1. V levém horním rohu Azure Portal vyberte možnost **vytvořit prostředek** :

    ![Snímek obrazovky ukazuje možnost vytvořit prostředek v Azure Portal.](./media/quickstarts/search-services.png)
1. Do vyhledávacího pole zadejte **Key Vault**.
1. V seznamu výsledků vyberte na levé straně **trezory klíčů** .
1. V **trezorech klíčů** vyberte **Přidat**.
1. Na pravé straně v části **Vytvoření trezoru klíčů** zadejte následující informace:
    - Vyberte **předplatné** a zvolte předplatné.
    - V případě **skupiny prostředků** vyberte **vytvořit novou** a zadejte název skupiny prostředků.
    - V **názvu trezoru klíčů** je vyžadován jedinečný název. Pro tento kurz zadejte **Contoso-vault2**.
    - V rozevíracím seznamu **oblast** vyberte umístění.
1. Ostatní možnosti **Vytvoření trezoru klíčů** ponechte výchozí hodnoty.
1. Vyberte **Vytvořit**.

V tomto okamžiku je váš účet Azure jediným autorizovaným oprávněním pro přístup k tomuto novému trezoru.

![Snímek obrazovky se zobrazí v trezoru klíčů.](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Přidání tajného klíče do služby Key Vault

Pokud chcete do trezoru přidat tajný klíč, musíte provést několik dalších kroků. V takovém případě přidejte zprávu, kterou můžete použít k otestování Key Vault načítání. Zpráva se nazývá **zpráva** a v ní uložíte hodnotu Hello z Key Vault.

1. Na stránkách vlastností Key Vault vyberte **tajné klíče**.
1. Vyberte **Generovat/importovat**.
1. V podokně **vytvořit tajný klíč** zadejte následující hodnoty:
    - **Možnosti nahrání**: zadejte **Ruční**.
    - **Název**: zadejte **zprávu**.
    - **Hodnota**: zadejte **hello z Key Vault**.
1. Ponechte druhé **vytvořit vlastnosti tajného klíče** s výchozími hodnotami.
1. Vyberte **Vytvořit**.

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Přidat odkaz Key Vault do konfigurace aplikace

1. Přihlaste se na [Azure Portal](https://portal.azure.com). Vyberte **všechny prostředky** a pak vyberte instanci úložiště konfigurace aplikace, kterou jste vytvořili v rychlém startu.

1. Vyberte **Průzkumník konfigurace**.

1. Vyberte **+ vytvořit**  >  **odkaz na Trezor klíčů** a pak zadejte následující hodnoty:
    - **Klíč**: vyberte **TestApp: Settings: KeyVaultMessage**.
    - **Popisek**: Nechte tuto hodnotu prázdnou.
    - **Předplatné**, **Skupina prostředků** a **Trezor klíčů**: zadejte hodnoty odpovídající klíčům v trezoru klíčů, který jste vytvořili v předchozí části.
    - **Tajný kód**: vyberte tajný kód s názvem **zpráva** , kterou jste vytvořili v předchozí části.

## <a name="connect-to-key-vault"></a>Připojení k Key Vault

1. V tomto kurzu použijete k ověřování Key Vault instanční objekt. Tento instanční objekt vytvoříte pomocí příkazu Azure CLI [AZ AD SP Create-for-RBAC](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) :

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

1. Spusťte následující příkaz, který instančnímu objektu umožní přístup k trezoru klíčů:

    ```cmd
    az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
    ```

1. Přidejte proměnné prostředí pro uložení hodnot *ClientID*, *clientSecret* a *tenantId*.

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
    > Tyto přihlašovací údaje Key Vault jsou používány pouze v rámci vaší aplikace. Vaše aplikace se ověřuje přímo Key Vault s těmito přihlašovacími údaji. Nejsou nikdy předány do služby konfigurace aplikace.

1. Pokud chcete načíst tyto nové proměnné prostředí, restartujte terminál.

## <a name="update-your-code-to-use-a-key-vault-reference"></a>Aktualizace kódu pro použití odkazu na Key Vault

1. Přidejte odkaz na požadované balíčky NuGet spuštěním následujícího příkazu:

    ```dotnetcli
    dotnet add package Azure.Identity
    ```

1. Otevřete *program. cs* a přidejte odkazy na následující požadované balíčky:

    ```csharp
    using Azure.Identity;
    ```

1. Aktualizujte `CreateWebHostBuilder` metodu pro použití konfigurace aplikace voláním `config.AddAzureAppConfiguration` metody. Zahrňte `ConfigureKeyVault` možnost a předejte správné přihlašovací údaje vašemu Key Vault.

    #### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

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

    #### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

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

1. Po inicializaci připojení k konfiguraci aplikace nastavíte připojení k Key Vault voláním `ConfigureKeyVault` metody. Po inicializaci můžete přistupovat k hodnotám Key Vault odkazů stejným způsobem jako při přístupu k hodnotám běžných konfiguračních klíčů aplikací.

    Chcete-li zobrazit tento proces v akci, otevřete *index. cshtml* v   >  **domovské** složce zobrazení. Jeho obsah nahraďte následujícím kódem.

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

    Přistupujete k hodnotě Key Vault reference **TestApp: Settings: KeyVaultMessage** stejným způsobem jako hodnota konfigurace **TestApp: Settings: Message**.

## <a name="build-and-run-the-app-locally"></a>Místní sestavení a spuštění aplikace

1. Pokud chcete aplikaci vytvořit pomocí .NET Core CLI, spusťte v příkazovém prostředí následující příkaz:

    ```dotnetcli
    dotnet build
    ```

1. Po dokončení sestavení použijte následující příkaz pro místní spuštění webové aplikace:

    ```dotnetcli
    dotnet run
    ```

1. Otevřete okno prohlížeče a pro `http://localhost:5000` webovou aplikaci hostovanou místně použijte výchozí adresu URL.

    ![Spuštění místní aplikace v rychlém startu](./media/key-vault-reference-launch-local.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili konfigurační klíč aplikace, který odkazuje na hodnotu uloženou v Key Vault. Pokud se chcete dozvědět, jak přidat identitu služby spravovanou v Azure, která zjednodušuje přístup k konfiguraci aplikací a Key Vault, pokračujte k dalšímu kurzu.

> [!div class="nextstepaction"]
> [Spravovaná integrace identit](./howto-integrate-azure-managed-service-identity.md)

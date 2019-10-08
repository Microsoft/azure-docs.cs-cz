---
title: Kurz použití konfigurace aplikace Azure Key Vault odkazy v aplikaci ASP.NET Core | Microsoft Docs
description: V tomto kurzu se naučíte používat Key Vault odkazy na konfiguraci aplikací Azure z aplikace ASP.NET Core.
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
ms.date: 10/07/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: a14cb3035c159c82df44f686da7f7b78ef942943
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72035855"
---
# <a name="tutorial-use-key-vault-references-in-an-aspnet-core-app"></a>Kurz: použití odkazů Key Vault v aplikaci ASP.NET Core

V tomto kurzu se naučíte používat službu Azure App Configuration Service společně s Azure Key Vault. Jedná se o doplňkové služby, které budou ve většině nasazení aplikací použity vedle sebe. Pro usnadnění jejich použití vám konfigurace aplikace umožňuje vytvořit klíče, které odkazují na hodnoty uložené v Key Vault. Když to uděláte, konfigurace aplikace uloží identifikátor URI do Key Vault hodnoty místo samotné hodnoty. Vaše aplikace načítá hodnotu tohoto klíče pomocí poskytovatele klienta konfigurace aplikace, stejně jako jakýkoli jiný klíč uložený v konfiguraci aplikace. Poskytovatel klienta ho rozpoznává jako odkaz na Key Vault a volá Key Vault, aby se načetla hodnota. Vaše aplikace zodpovídá za to, že se správně ověřuje jak konfigurace aplikace, tak i Key Vault. Tyto dvě služby nekomunikují přímo.

V tomto kurzu se dozvíte, jak můžete implementovat Key Vault odkazy v kódu. Sestavuje se ve webové aplikaci představené v rychlých startech. Než budete pokračovat, dokončete nejprve [Vytvoření aplikace ASP.NET Core s konfigurací aplikace](./quickstart-aspnet-core-app.md) .

K provedení kroků v tomto kurzu můžete použít libovolný editor kódu. [Visual Studio Code](https://code.visualstudio.com/) je vynikající možnost, která je dostupná na platformách Windows, MacOS a Linux.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření konfiguračního klíče aplikace, který odkazuje na hodnotu uloženou v Key Vault
> * Přístup k hodnotě tohoto klíče z ASP.NET Core webové aplikace

## <a name="prerequisites"></a>Předpoklady

K provedení tohoto kurzu nainstalujte [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-vault"></a>Vytvoření trezoru

1. Vyberte možnost **Vytvořit prostředek** v levém horním rohu webu Azure Portal.

    ![Výstup po dokončení vytváření služby Key Vault](./media/quickstarts/search-services.png)
2. Do vyhledávacího pole zadejte **Key Vault**.
3. V seznamu výsledků zvolte **Key Vault**.
4. V části Key Vault zvolte **Vytvořit**.
5. V části **Vytvořit trezor klíčů** zadejte následující informace:
    - **Název:** Je potřeba zadat jedinečný název. V tomto rychlém startu používáme **Contoso-vault2**. 
    - **Předplatné:** Zvolte předplatné.
    - V části **Skupina prostředků**vyberte **vytvořit novou** a zadejte název skupiny prostředků.
    - V rozevírací nabídce **Umístění** zvolte umístění.
    - U ostatních možností ponechte jejich výchozí hodnoty.
6. Po zadání výše uvedených informací vyberte **Vytvořit**.

V tomto okamžiku je váš účet Azure jediným autorizovaným oprávněním pro přístup k tomuto novému trezoru.

![Výstup po dokončení vytváření služby Key Vault](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Přidání tajného klíče do služby Key Vault

Pokud chcete do trezoru přidat tajný klíč, stačí provést několik dalších kroků. V tomto případě přidáme zprávu, kterou můžeme použít k otestování Key Vault načítání. Zpráva se nazývá **zpráva** a ukládáme hodnotu **hello z Key Vault** .

1. Na stránkách Key Vault vlastnosti vyberte **tajné**kódy.
1. Klikněte na **Vygenerovat/importovat**.
1. Na obrazovce **Vytvoření tajného kódu** zvolte následující hodnoty:
    - **Možnosti nahrání:** Ruční
    - **Název**: zpráva
    - **Hodnota**: Hello z Key Vault
    - U ostatních hodnot ponechte jejich výchozí nastavení. Klikněte na **Vytvořit**.

## <a name="add-a-key-vault-reference-to-app-config"></a>Přidat odkaz Key Vault do konfigurace aplikace

1. Přihlaste se na web [Azure Portal](https://portal.azure.com). Vyberte **všechny prostředky**a vyberte instanci úložiště konfigurace aplikace, kterou jste vytvořili v rychlém startu.

1. Klikněte na **Průzkumník konfigurace** .

1. Klikněte na **+ vytvořit** > **odkaz trezoru klíčů** a vyberte následující hodnoty:
    - **Klíč**: TestApp: nastavení: KeyVaultMessage
    - **Popisek**: ponechte prázdné.
    - **Předplatné**, **Skupina prostředků**, **Trezor klíčů**: vyberte možnosti odpovídající Key Vault, který jste vytvořili v předchozí části.
    - **Tajný kód**: vyberte tajný kód nazvaný **zpráva** , kterou jste vytvořili v předchozí části.

## <a name="connect-to-key-vault"></a>Připojení k Key Vault

1. V tomto kurzu použijete instanční objekt pro ověřování do trezoru klíčů. Tento instanční objekt vytvoříte pomocí příkazu Azure CLI [AZ AD SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) :

    ```azurecli
    az ad sp create-for-rbac -n "http://mySP" --sdk-auth
    ```

    Tato operace vrátí řadu párů klíč/hodnota. 

    ```console
    {
    "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
    "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
    "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
    "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "activeDirectoryGraphResourceId": "https://graph.windows.net/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
    }
    ```

1. Spusťte následující příkaz, který objektu služby povolí přístup k trezoru klíčů:

        az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey

1. Přidání tajných kódů pro *ClientID* a *ClientSecret* do správce tajných klíčů. Tyto příkazy musí být spuštěny ve stejném adresáři jako soubor *. csproj* .

        dotnet user-secrets set ConnectionStrings:KeyVaultClientId <clientId-of-your-service-principal>        
        dotnet user-secrets set ConnectionStrings:KeyVaultClientSecret <clientSecret-of-your-service-principal>

> [!NOTE]
> Tyto přihlašovací údaje Key Vault jsou používány pouze v rámci vaší aplikace. Vaše aplikace se ověřuje přímo Key Vault s těmito přihlašovacími údaji. Nejsou nikdy předány do služby konfigurace aplikace.

## <a name="update-your-code-to-use-a-key-vault-reference"></a>Aktualizace kódu pro použití odkazu na Key Vault

1. Otevřete *program.cs*a přidejte odkazy na požadované balíčky.

    ```csharp
    using Microsoft.Azure.KeyVault;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

1. Aktualizujte metodu `CreateWebHostBuilder` pro použití konfigurace aplikace voláním metody `config.AddAzureAppConfiguration()`. Zahrňte možnost `UseAzureKeyVault` a předejte do svého Key Vault nový odkaz `KeyVaultClient`.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                KeyVaultClient kvClient = new KeyVaultClient(async (authority, resource, scope) =>
                {
                    var adCredential = new ClientCredential(settings["ConnectionStrings:KeyVaultClientId"], settings["ConnectionStrings:KeyVaultClientSecret"]);
                    var authenticationContext = new AuthenticationContext(authority, null);
                    return (await authenticationContext.AcquireTokenAsync(resource, adCredential)).AccessToken;
                });

                config.AddAzureAppConfiguration(options => {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                            .UseAzureKeyVault(kvClient); });
            })
            .UseStartup<Startup>();
    ```

1. Až předáte odkaz *KeyVaultClient* na metodu `UseAzureKeyVault` při inicializaci připojení k konfiguraci aplikace, můžete získat přístup k hodnotám Key Vault odkazů stejným způsobem jako při přístupu k hodnotám konfiguračních klíčů běžné aplikace. Chcete-li zobrazit tento proces v akci, otevřete *index. cshtml* v zobrazeních > domovském adresáři. Nahraďte jeho obsah následujícím kódem:

    ```html
    @using Microsoft.Extensions.Configuration
    @inject IConfiguration Configuration

    <style>
        body {
            background-color: @Configuration["TestApp:Settings:BackgroundColor"]
        }
        h1 {
            color: @Configuration["TestApp:Settings:FontColor"];
            font-size: @Configuration["TestApp:Settings:FontSize"];
        }
    </style>

    <h1>@Configuration["TestApp:Settings:Message"]
        and @Configuration["TestApp:Settings:KeyVaultMessage"]</h1>
    ```

    Přistupujete k hodnotě Key Vault reference *TestApp: Settings: KeyVaultMessage* stejným způsobem jako konfigurační hodnota *TestApp: nastavení: zpráva*

## <a name="build-and-run-the-app-locally"></a>Místní sestavení a spuštění aplikace

1. Pokud chcete aplikaci vytvořit pomocí .NET Core CLI, spusťte v příkazovém prostředí následující příkaz:

        dotnet build

2. Po úspěšném dokončení sestavení spusťte následující příkaz pro místní spuštění webové aplikace:

        dotnet run

3. Otevřete okno prohlížeče a vyberte `http://localhost:5000`, což je výchozí adresa URL pro webovou aplikaci hostovanou místně.

    ![Spuštění aplikace pro rychlý Start – místní](./media/key-vault-reference-launch-local.png)


## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste přidali identitu spravované služby Azure, abyste zjednodušili přístup ke konfiguraci aplikací a vylepšili správu přihlašovacích údajů pro vaši aplikaci. Další informace o tom, jak používat konfiguraci aplikací, najdete v ukázkách Azure CLI.

> [!div class="nextstepaction"]
> [Ukázky rozhraní příkazového řádku](./cli-samples.md)

---
title: Kurz – použití trezoru klíčů Azure s virtuálním počítačem windows v rozhraní .NET | Dokumenty společnosti Microsoft
description: V tomto kurzu nakonfigurujete základní aplikaci ASP.NET pro čtení tajného klíče z trezoru klíčů.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: 6ba78a44af7beb9b5b79aa1a87e08f5a82589cce
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422862"
---
# <a name="tutorial-use-azure-key-vault-with-a-windows-virtual-machine-in-net"></a>Kurz: Použití azure key vaultu s virtuálním počítačem s Windows v rozhraní .NET

Azure Key Vault vám pomůže chránit tajné klíče, jako jsou klíče rozhraní API, připojovací řetězce databáze, které potřebujete pro přístup k vašim aplikacím, službám a prostředkům IT.

V tomto kurzu se dozvíte, jak získat konzolové aplikace pro čtení informací z Azure Key Vault. Chcete-li tak učinit, použijte spravované identity pro prostředky Azure. 

V tomto kurzu získáte informace o následujících postupech:

> [!div class="checklist"]
> * Vytvořte skupinu prostředků.
> * Vytvoření trezoru klíčů
> * Přidejte tajný klíč do trezoru klíčů.
> * Načtení tajného klíče z trezoru klíčů
> * Vytvořte virtuální počítač Azure.
> * Povolte [spravovanou identitu](../../active-directory/managed-identities-azure-resources/overview.md) virtuálního počítače.
> * Přiřaďte oprávnění k identitě virtuálního aplikace.

Než začnete, přečtěte si [základní koncepty trezoru klíčů](basic-concepts.md). 

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Požadavky

Pro Windows, Mac a Linux:
  * [Git](https://git-scm.com/downloads)
  * Tento kurz vyžaduje, abyste místní spouštění příkazového příkazového odložení příkazového odlokala. Musíte mít nainstalovanou verzi Azure CLI verze 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade rozhraní příkazového řádku, přečtěte si téma [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli).

## <a name="about-managed-service-identity"></a>Informace o funkci Identita spravované služby

Azure Key Vault ukládá přihlašovací údaje bezpečně, takže nejsou zobrazeny ve vašem kódu. Je však nutné ověřit azure key vault načíst klíče. K ověření trezoru klíčů potřebujete pověření. Je to klasické dilema bootstrap. Identita spravované služby (MSI) řeší tento problém poskytnutím _identity zaváděcí pasti,_ která zjednodušuje proces.

Když povolíte MSI pro službu Azure, jako jsou virtuální počítače Azure, Azure App Service nebo Azure Functions, Azure vytvoří [instanční objekt .](basic-concepts.md) MSI to pro instanci služby ve službě Azure Active Directory (Azure AD) a vloží pověření instancí instancí. 

![MSI](../media/MSI.png)

Dále chcete-li získat přístupový token, váš kód volá místní službu metadat, která je k dispozici na prostředek Azure. K ověření služby Azure Key Vault váš kód používá přístupový token, který získá z místního koncového bodu MSI. 

## <a name="create-resources-and-assign-permissions"></a>Vytvoření zdrojů a přiřazení oprávnění

Než začnete kódovat, musíte vytvořit nějaké prostředky, vložit tajný klíč do trezoru klíčů a přiřadit oprávnění.

### <a name="sign-in-to-azure"></a>Přihlášení k Azure

Pokud se chcete k Azure přihlásit pomocí azure cli, zadejte:

```azurecli
az login
```

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az-group-create). 

Tento příklad vytvoří skupinu prostředků v umístění v USA – západ:

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Vaše nově vytvořená skupina prostředků bude použita v průběhu tohoto kurzu.

### <a name="create-a-key-vault-and-populate-it-with-a-secret"></a>Vytvoření trezoru klíčů a jeho vyplnění tajným klíčem

Vytvořte trezor klíčů ve skupině prostředků poskytnutím příkazu [az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) s následujícími informacemi:

* Název trezoru klíčů: řetězec 3 až 24 znaků, který může obsahovat pouze čísla (0-9), písmena (a-z, A-Z) a pomlčky (-)
* Název skupiny prostředků
* Umístění: **Západní USA**

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
V tomto okamžiku je váš účet Azure jediný, který je oprávněn provádět operace v tomto novém trezoru klíčů.

Nyní přidejte tajný klíč do trezoru klíčů pomocí příkazu [az keyvault secret set](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set)


Chcete-li vytvořit tajný klíč v trezoru klíčů s názvem **AppSecret**, zadejte následující příkaz:

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Tento tajný klíč ukládá hodnotu **MySecret**.

### <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače
Vytvořte virtuální počítač pomocí jedné z následujících metod:

* [The Azure CLI](../../virtual-machines/windows/quick-create-cli.md)
* [PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
* [Azure Portal](../../virtual-machines/windows/quick-create-portal.md)

### <a name="assign-an-identity-to-the-vm"></a>Přiřazení identity k virtuálnímu virtuálnímu mněmu
Vytvořte systémem přiřazenou identitu pro virtuální počítač pomocí [příkazu přiřazení identity az vm:](/cli/azure/vm/identity?view=azure-cli-latest#az-vm-identity-assign)

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Všimněte si systémově přiřazené identity, která je zobrazena v následujícím kódu. Výstup předchozího příkazu by byl: 

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

### <a name="assign-permissions-to-the-vm-identity"></a>Přiřazení oprávnění k identitě virtuálního aplikace
Přiřaďte dříve vytvořená oprávnění identity k trezoru klíčů pomocí příkazu [az keyvault set-policy:](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy)

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

### <a name="sign-in-to-the-virtual-machine"></a>Přihlášení k virtuálnímu počítači

Pokud se chcete přihlásit k virtuálnímu počítači, postupujte podle pokynů v části [Připojit a přihlaste se k virtuálnímu počítači Azure se systémem Windows](../../virtual-machines/windows/connect-logon.md).

## <a name="set-up-the-console-app"></a>Nastavení konzolové aplikace

Vytvořte konzolovou aplikaci a `dotnet` nainstalujte požadované balíčky pomocí příkazu.

### <a name="install-net-core"></a>Instalace .NET Core

Chcete-li nainstalovat jádro .NET Core, přejděte na stránku [ke stažení rozhraní .NET.](https://www.microsoft.com/net/download)

### <a name="create-and-run-a-sample-net-app"></a>Vytvoření a spuštění ukázkové aplikace .NET

Otevřete příkazový řádek.

"Hello World" můžete vytisknout do konzoly spuštěním následujících příkazů:

```console
dotnet new console -o helloworldapp
cd helloworldapp
dotnet run
```

### <a name="install-the-packages"></a>Instalace balíčků

V okně konzoly nainstalujte balíčky .NET potřebné pro tento rychlý start:

```console
dotnet add package System.IO;
dotnet add package System.Net;
dotnet add package System.Text;
dotnet add package Newtonsoft.Json;
dotnet add package Newtonsoft.Json.Linq;
```

## <a name="edit-the-console-app"></a>Úprava aplikace konzoly

Otevřete soubor *Program.cs* a přidejte tyto balíčky:

```csharp
using System;
using System.IO;
using System.Net;
using System.Text;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
```

Upravte soubor třídy tak, aby obsahoval kód v následujícím třístupňovém procesu:

1. Načíst token z místního koncového bodu MSI na virtuálním počítači. Tím také načte token z Azure AD.
2. Předejte token do trezoru klíčů a pak přineste svůj tajný klíč. 
3. Přidejte název a tajný název úschovny do požadavku.

```csharp
 class Program
    {
        static void Main(string[] args)
        {
            // Step 1: Get a token from the local (URI) Managed Service Identity endpoint, which in turn fetches it from Azure AD
            var token = GetToken();

            // Step 2: Fetch the secret value from your key vault
            System.Console.WriteLine(FetchSecretValueFromKeyVault(token));
        }

        static string GetToken()
        {
            WebRequest request = WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net");
            request.Headers.Add("Metadata", "true");
            WebResponse response = request.GetResponse();
            return ParseWebResponse(response, "access_token");
        }
        
        static string FetchSecretValueFromKeyVault(string token)
        {
            //Step 3: Add the vault name and secret name to the request.
            WebRequest kvRequest = WebRequest.Create("https://<YourVaultName>.vault.azure.net/secrets/<YourSecretName>?api-version=2016-10-01");
            kvRequest.Headers.Add("Authorization", "Bearer "+  token);
            WebResponse kvResponse = kvRequest.GetResponse();
            return ParseWebResponse(kvResponse, "value");
        }

        private static string ParseWebResponse(WebResponse response, string tokenName)
        {
            string token = String.Empty;
            using (Stream stream = response.GetResponseStream())
            {
                StreamReader reader = new StreamReader(stream, Encoding.UTF8);
                String responseString = reader.ReadToEnd();

                JObject joResponse = JObject.Parse(responseString);    
                JValue ojObject = (JValue)joResponse[tokenName];             
                token = ojObject.Value.ToString();
            }
            return token;
        }
    }
```

Předchozí kód ukazuje, jak provést operace s Azure Key Vault ve virtuálním počítači windows.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nejsou potřeba, odstraňte virtuální počítač a trezor klíčů.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Rozhraní API REST trezoru klíčů Azure](https://docs.microsoft.com/rest/api/keyvault/)

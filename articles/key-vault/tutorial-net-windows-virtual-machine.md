---
title: Kurz – použití Azure Key Vault s virtuálním počítačem s Windows v .NET | Microsoft Docs
description: V tomto kurzu nakonfigurujete aplikaci ASP.NET Core pro čtení tajného klíče z vašeho trezoru klíčů.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: e2e730c623b6ee802938b930c4874f6c4920cc64
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934406"
---
# <a name="tutorial-use-azure-key-vault-with-a-windows-virtual-machine-in-net"></a>Kurz: Použití Azure Key Vault s virtuálním počítačem s Windows v .NET

Azure Key Vault pomáhá chránit tajné kódy, jako jsou klíče rozhraní API, databázové připojovací řetězce, které potřebujete pro přístup k aplikacím, službám a prostředkům IT.

V tomto kurzu se naučíte, jak získat konzolovou aplikaci pro čtení informací z Azure Key Vault. Uděláte to tak, že použijete spravované identity pro prostředky Azure. 

V tomto kurzu získáte informace o následujících postupech:

> [!div class="checklist"]
> * Vytvořte skupinu prostředků.
> * Vytvoření trezoru klíčů
> * Přidejte tajný klíč do trezoru klíčů.
> * Načtení tajného klíče z trezoru klíčů
> * Vytvořte virtuální počítač Azure.
> * Povolte [spravovanou identitu](../active-directory/managed-identities-azure-resources/overview.md) pro virtuální počítač.
> * Přiřaďte oprávnění k identitě virtuálního počítače.

Než začnete, přečtěte si téma [Key Vault Basic koncepty](key-vault-whatis.md#basic-concepts). 

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Požadavky

Pro Windows, Mac a Linux:
  * [Git](https://git-scm.com/downloads)
  * Tento kurz vyžaduje, abyste spouštěli Azure CLI místně. Musíte mít nainstalovanou verzi Azure CLI 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade rozhraní příkazového řádku, přečtěte si téma [Instalace Azure CLI 2.0](https://review.docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="about-managed-service-identity"></a>Informace o funkci Identita spravované služby

Azure Key Vault ukládá pověření bezpečně, takže se ve vašem kódu nezobrazují. K načtení klíčů ale musíte ověřit, Azure Key Vault. K ověření Key Vault budete potřebovat přihlašovací údaje. Je to klasický spouštěcí dilematem. Identita spravované služby (MSI) Tento problém řeší tím, že poskytuje _identitu_ spuštění, která zjednodušuje proces.

Když povolíte MSI pro službu Azure, například Azure Virtual Machines, Azure App Service nebo Azure Functions, vytvoří [Služba Azure instanční objekt](key-vault-whatis.md#basic-concepts). Služba MSI to provede pro instanci služby v Azure Active Directory (Azure AD) a vloží do této instance přihlašovací údaje instančního objektu. 

![MSI](media/MSI.png)

Pokud chcete získat přístupový token, váš kód volá místní službu metadat, která je k dispozici v prostředku Azure. K ověření u Azure Key Vault služby používá váš kód přístupový token, který získá z místního koncového bodu MSI. 

## <a name="create-resources-and-assign-permissions"></a>Vytváření prostředků a přiřazování oprávnění

Než začnete s psaním kódu, potřebujete vytvořit nějaké prostředky, vložit do trezoru klíčů tajný klíč a přiřadit oprávnění.

### <a name="sign-in-to-azure"></a>Přihlášení k Azure

Pokud se chcete přihlásit k Azure pomocí rozhraní příkazového řádku Azure, zadejte:

```azurecli
az login
```

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az-group-create). 

Tento příklad vytvoří skupinu prostředků v umístění Západní USA:

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Vaše nově vytvořená skupina prostředků se použije v celém rámci tohoto kurzu.

### <a name="create-a-key-vault-and-populate-it-with-a-secret"></a>Vytvoření trezoru klíčů a jeho naplnění pomocí tajného klíče

Vytvořte Trezor klíčů ve skupině prostředků zadáním příkazu AZ Key [trezor Create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) s následujícími informacemi:

* Název trezoru klíčů: řetězec na 3 až 24 znaků, který může obsahovat jenom číslice (0-9), písmena (a-z, A-Z) a spojovníky (-).
* Název skupiny prostředků
* Umístění: **Západní USA**

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
V tomto okamžiku je váš účet Azure jediným z nich, který je autorizovaný k provádění operací s tímto novým trezorem klíčů.

Nyní do trezoru klíčů přidejte tajný klíč pomocí příkazu [AZ Key trezor tajné sady](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set) .


Pokud chcete v trezoru klíčů s názvem **AppSecret**vytvořit tajný klíč, zadejte následující příkaz:

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Tento tajný kód ukládá hodnotu **MySecret**.

### <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače
Vytvořte virtuální počítač pomocí jedné z následujících metod:

* [Azure CLI](../virtual-machines/windows/quick-create-cli.md)
* [PowerShell](../virtual-machines/windows/quick-create-powershell.md)
* [Azure Portal](../virtual-machines/windows/quick-create-portal.md)

### <a name="assign-an-identity-to-the-vm"></a>Přiřazení identity k virtuálnímu počítači
Vytvořte pro virtuální počítač identitu přiřazenou systémem pomocí příkazu [AZ VM identity Assign](/cli/azure/vm/identity?view=azure-cli-latest#az-vm-identity-assign) :

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Poznamenejte si identitu přiřazenou systémem, která se zobrazí v následujícím kódu. Výstup předchozího příkazu by byl: 

```azurecli
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

### <a name="assign-permissions-to-the-vm-identity"></a>Přiřazení oprávnění k identitě virtuálního počítače
Přiřaďte dříve vytvořená oprávnění identity k trezoru klíčů pomocí příkazu [AZ Key trezor set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) :

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

### <a name="sign-in-to-the-virtual-machine"></a>Přihlaste se k virtuálnímu počítači.

Pokud se chcete přihlásit k virtuálnímu počítači, postupujte podle pokynů v tématu [připojení a přihlášení k virtuálnímu počítači Azure s Windows](../virtual-machines/windows/connect-logon.md).

## <a name="set-up-the-console-app"></a>Nastavení konzolové aplikace

Vytvořte konzolovou aplikaci a nainstalujte požadované balíčky pomocí `dotnet` příkazu.

### <a name="install-net-core"></a>Instalace .NET Core

Pokud chcete nainstalovat .NET Core, navštivte stránku [soubory ke stažení pro .NET](https://www.microsoft.com/net/download) .

### <a name="create-and-run-a-sample-net-app"></a>Vytvoření a spuštění ukázkové aplikace .NET

Otevřete příkazový řádek.

Hello World můžete do konzoly vytisknout spuštěním následujících příkazů:

```console
dotnet new console -o helloworldapp
cd helloworldapp
dotnet run
```

### <a name="install-the-packages"></a>Nainstalovat balíčky

 V okně konzoly nainstalujte balíčky .NET vyžadované pro tento rychlý Start:

 ```console
dotnet add package System.IO;
dotnet add package System.Net;
dotnet add package System.Text;
dotnet add package Newtonsoft.Json;
dotnet add package Newtonsoft.Json.Linq;
```

## <a name="edit-the-console-app"></a>Upravit konzolovou aplikaci

Otevřete soubor *program.cs* a přidejte tyto balíčky:

```csharp
using System;
using System.IO;
using System.Net;
using System.Text;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
```

Upravte soubor třídy tak, aby obsahoval kód v následujícím dvou krocích procesu:

1. Načte token z místního koncového bodu MSI na virtuálním počítači. Tím se taky načte token z Azure AD.
1. Předejte token do trezoru klíčů a potom tento klíč načtěte. 

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

Předchozí kód ukazuje, jak provádět operace s Azure Key Vault na virtuálním počítači s Windows.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte virtuální počítač a trezor klíčů.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Azure Key Vault REST API](https://docs.microsoft.com/rest/api/keyvault/)

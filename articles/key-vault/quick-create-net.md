---
title: Rychlý Start – Azure Key Vault klientskou knihovnu pro .NET
description: Poskytuje kritéria formátování a obsahu pro psaní rychlých startů pro klientské knihovny Azure SDK.
author: msmbaldwin
ms.author: mbaldwin
ms.date: 05/20/2019
ms.service: key-vault
ms.topic: quickstart
ms.openlocfilehash: d24323996e222caf6456372cbc65681d2055c3db
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996643"
---
# <a name="quickstart-azure-key-vault-client-library-for-net"></a>Rychlý start: Klientská knihovna Azure Key Vault pro .NET

Začněte s klientskou knihovnou Azure Key Vault pro .NET. Postupujte podle následujících kroků a nainstalujte balíček a vyzkoušejte ukázkový kód pro základní úlohy.

Azure Key Vault pomáhá chránit kryptografické klíče a tajné klíče používané cloudovými aplikacemi a službami. Pomocí klientské knihovny Key Vault pro .NET:

- Zvyšte zabezpečení a kontrolu nad klíči a hesly.
- Vytvářejte a importujte šifrovací klíče během několika minut.
- Snižte latenci díky škálování cloudu a globální redundanci.
- Zjednodušte a automatizujte úlohy pro certifikáty SSL/TLS.
- Použijte ověřený HSM úrovně 2 FIPS 140-2.

[Dokumentace k referenční dokumentaci](/dotnet/api/overview/azure/key-vault?view=azure-dotnet) | rozhraní API balíček[zdrojového kódu](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/KeyVault) | knihovny[(NuGet)](https://www.nuget.org/packages/Microsoft.Azure.KeyVault/)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [.NET Core 2,1 SDK nebo novější](https://dotnet.microsoft.com/download/dotnet-core/2.1).
* Rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) nebo [Azure PowerShell](/powershell/azure/overview)

V tomto rychlém startu se `dotnet`předpokládá, že používáte příkazy [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)a Windows v terminálu Windows (například [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-6), [Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6)nebo [Azure Cloud Shell](https://shell.azure.com/)).

## <a name="setting-up"></a>Nastavení

### <a name="create-new-net-console-app"></a>Vytvořit novou konzolovou aplikaci .NET

Vytvořte novou aplikaci .NET Core v upřednostňovaném editoru nebo integrovaném vývojovém prostředí (IDE).

V okně konzoly pomocí `dotnet new` příkazu vytvořte novou konzolovou aplikaci s názvem. `akv-dotnet`


```console
dotnet new console -n akvdotnet
```

Změňte adresář na nově vytvořenou složku aplikace. Aplikaci můžete vytvořit pomocí:

```console
dotnet build
```

Výstup sestavení by neměl obsahovat žádná upozornění ani chyby.

```console
Build succeeded.
 0 Warning(s)
 0 Error(s)
```

### <a name="install-the-package"></a>Instalace balíčku

V okně konzoly nainstalujte Azure Key Vault klientskou knihovnu pro .NET:

```console
dotnet add package Microsoft.Azure.KeyVault
```

Pro tento rychlý Start budete muset nainstalovat i tyto balíčky:

```console
dotnet add package System.Threading.Tasks
dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory
dotnet add package Microsoft.Azure.Management.ResourceManager.Fluent
```

### <a name="create-a-resource-group-and-key-vault"></a>Vytvoření skupiny prostředků a trezoru klíčů

V tomto rychlém startu se používá předem vytvořený Trezor klíčů Azure. Trezor klíčů můžete vytvořit podle kroků v [rychlém startu Azure CLI](quick-create-cli.md), [Azure PowerShell rychlý Start](quick-create-powershell.md)nebo v [rychlém startu Azure Portal](quick-create-portal.md). Případně můžete jednoduše spustit příkazy rozhraní příkazového řádku Azure CLI.

> [!Important]
> Každý Key Vault musí mít jedinečný název. Následující příklad vytvoří Key Vault s názvem *myKV*, ale je nutné, abyste si pojmenovali něco jiného a použili tento název v rámci tohoto rychlého startu.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

### <a name="create-a-service-principal"></a>Vytvoření instančního objektu

Nejjednodušší způsob, jak ověřit cloudovou aplikaci .NET, je spravovaná identita; Podrobnosti najdete v tématu [ověřování služby – služba Azure Key Vault používání rozhraní .NET](service-to-service-authentication.md) . V zájmu zjednodušení ale v tomto rychlém startu se vytvoří Konzolová aplikace .NET. Ověřování desktopové aplikace pomocí Azure vyžaduje použití instančního objektu.
Vytvořte zásadu služby pomocí příkazu Azure CLI [AZ AD SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) :

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

Poznamenejte si clientId a clientSecret, jak je budeme používat v níže uvedeném kroku [ověření do trezoru klíčů](#authenticate-to-your-key-vault) .

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>Udělte instančnímu objektu přístup k vašemu trezoru klíčů.

Vytvořte zásady přístupu pro váš Trezor klíčů, který uděluje oprávnění vašemu instančnímu objektu předáním příkazu [AZ klíčového trezoru set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) . Udělte instančnímu objektu získání, seznam a nastavení oprávnění pro klíče a tajné kódy.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

## <a name="object-model"></a>Objektový model

Klientská knihovna Azure Key Vault pro .NET umožňuje správu klíčů a souvisejících prostředků, jako jsou certifikáty a tajné klíče. Následující ukázka kódu vám ukáže, jak nastavit tajný klíč a načíst tajný kód.

Celá Konzolová aplikace je k dispozici na adrese https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/akvdotnet.

## <a name="code-examples"></a>Příklady kódu

### <a name="add-directives"></a>Přidat direktivy

Do horní části kódu přidejte následující direktivy:

[!code-csharp[Directives](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=directives)]

### <a name="authenticate-to-your-key-vault"></a>Ověřování pro váš Trezor klíčů

Tento rychlý Start .NET spoléhá na proměnné prostředí pro ukládání přihlašovacích údajů, které by neměly být vloženy do kódu. 

Před sestavením a spuštěním aplikace `setx` použijte příkaz pro `akvClientId`nastavení proměnných prostředí,, `akvTenantId`a `akvClientSecret` `akvSubscriptionId` na hodnoty, které jste si poznamenali výše.

```console
setx akvClientId <your-clientID>

setx akvClientSecret <your-clientSecret>
````

Pokaždé, když `setx`zavoláte, byste měli získat odpověď na úspěch: Zadaná hodnota byla uložena. "

Přiřaďte tyto proměnné prostředí řetězcům v kódu a pak ověřte svoji aplikaci jejich předáním do [třídy KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient):

[!code-csharp[Authentication](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=authentication)]

### <a name="save-a-secret"></a>Uložení tajného klíče

Teď, když je vaše aplikace ověřená, můžete do trezoru klíčů vložit tajný klíč pomocí [metody SetSecretAsync](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync) , která vyžaduje adresu URL vašeho trezoru klíčů, který je ve formuláři `https://<your-unique-keyvault-name>.vault.azure.net/secrets/`. Také vyžaduje název tajného kódu – používáme "mySecret".  Tyto řetězce možná budete chtít přiřadit proměnným pro opakované použití.

[!code-csharp[Set secret](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=setsecret)]

Můžete ověřit, jestli je tajný kód nastavený pomocí příkazu [AZ klíčů trezor tajného zobrazení](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) :

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Načtení tajného kódu

Nyní můžete načíst dříve nastavenou hodnotu pomocí [metody GetSecretAsync](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync)

[!code-csharp[Get secret](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=getsecret)]

Váš tajný kód se teď uloží `keyvaultSecret.Value;`jako.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání trezoru klíčů a odpovídající skupiny prostředků použít Azure CLI nebo Azure PowerShell.

```azurecli
az group delete -g "myResourceGroup" -l "EastUS" 
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste vytvořili Trezor klíčů, uložili tajný klíč a získali tento tajný klíč. Podívejte se na [celou konzolovou aplikaci v GitHubu](https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/akvdotnet).

Další informace o Key Vault a o tom, jak je integrovat s vašimi aplikacemi, najdete dál v článcích níže.

- Implementace [ověřování služby-služba pro Azure Key Vault pomocí .NET](service-to-service-authentication.md)
- Přečtěte si [přehled Azure Key Vault](key-vault-overview.md)
- Další informace najdete v [příručce pro vývojáře Azure Key Vault](key-vault-developers-guide.md) .
- Další informace o [klíčích, tajných klíčích a certifikátech](about-keys-secrets-and-certificates.md)
- Kontrola [Azure Key Vault osvědčených postupů](key-vault-best-practices.md)

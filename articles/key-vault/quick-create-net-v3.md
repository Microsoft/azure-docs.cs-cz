---
title: Úvodní příručka – klientská knihovna Azure Key Vault pro rozhraní .NET (SDK v3)
description: Zjistěte, jak vytvořit, načíst a odstranit tajné klíče z trezoru klíčů Azure pomocí knihovny klienta .NET (v3)
author: msmbaldwin
ms.author: mbaldwin
ms.date: 11/05/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: 67dedfce4be81fdf686918a310b89cf463ed389a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79457249"
---
# <a name="quickstart-azure-key-vault-client-library-for-net-sdk-v3"></a>Úvodní příručka: Klientská knihovna Azure Key Vault pro rozhraní .NET (SDK v3)

Začínáme s klientskou knihovnou Azure Key Vault pro rozhraní .NET. Podle následujících kroků nainstalujte balíček a vyzkoušejte ukázkový kód pro základní úkoly.

> [!NOTE]
> Tento rychlý start používá verzi klienta Microsoft.Azure.KeyVault verze verze v3.0.4. Chcete-li použít nejaktuálnější verzi klientské knihovny Trezor klíčů, přečtěte si část [klientská knihovna Azure Key Vault pro rozhraní .NET (SDK v4).](quick-create-net.md) 

Azure Key Vault pomáhá chránit kryptografické klíče a tajné klíče používané cloudovými aplikacemi a službami. Pomocí klientské knihovny Trezor klíčů pro rozhraní .NET můžete:

- Zvyšte zabezpečení a kontrolu nad klíči a hesly.
- Vytvořte a importujte šifrovací klíče během několika minut.
- Snižte latenci díky cloudovému škálování a globální redundanci.
- Zjednodušte a automatizujte úlohy pro certifikáty TLS/SSL.
- Použijte fips 140-2 úroveň 2 ověřené hmeony.

[Referenční dokumentace](/dotnet/api/overview/azure/key-vault?view=azure-dotnet) | rozhraní[API Balíček zdrojového kódu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault) | [knihovny (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.KeyVault/)

> [!NOTE]
> Každý trezor klíčů musí mít jedinečný název. V následujících příkladech nahraďte <> názvu trezoru s jedinečným klíčem.


## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [vytvořte si ho zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Sada [.NET Core 2.1 SDK nebo novější](https://dotnet.microsoft.com/download/dotnet-core/2.1).
* [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) nebo [Azure PowerShell](/powershell/azure/overview)

Tento rychlý start předpokládá, `dotnet`že používáte příkazy [, Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)a Windows v terminálu Windows (například [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-6), Windows [PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6)nebo [Azure Cloud Shell).](https://shell.azure.com/)

## <a name="setting-up"></a>Nastavení

### <a name="create-new-net-console-app"></a>Vytvoření nové aplikace konzoly ROZHRANÍ .NET

V okně konzoly `dotnet new` vytvořte pomocí příkazu novou konzolovou `akv-dotnet`aplikaci .NET s názvem .


```console
dotnet new console -n akvdotnet
```

Změňte adresář do nově vytvořené složky aplikace. Aplikaci můžete sestavit pomocí:

```console
dotnet build
```

Výstup sestavení by měl obsahovat žádná upozornění nebo chyby.

```console
Build succeeded.
 0 Warning(s)
 0 Error(s)
```

### <a name="install-the-package"></a>Instalace balíčku

V okně konzoly nainstalujte klientskou knihovnu Azure Key Vault pro rozhraní .NET:

```console
dotnet add package Microsoft.Azure.KeyVault
```

Pro tento rychlý start budete muset nainstalovat také následující balíčky:

```console
dotnet add package System.Threading.Tasks
dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory
dotnet add package Microsoft.Azure.Management.ResourceManager.Fluent
```

### <a name="create-a-resource-group-and-key-vault"></a>Vytvoření skupiny prostředků a trezoru klíčů

Tento rychlý start používá předem vytvořený trezor klíčů Azure. Trezor klíčů můžete vytvořit podle kroků v [rychlém startu Azure CLI](quick-create-cli.md), [rychlém startu Azure PowerShellu](quick-create-powershell.md)nebo [rychlém startu na webu Azure Portal](quick-create-portal.md). Případně můžete jednoduše spustit příkazy Azure CLI níže.

> [!Important]
> Každý trezor klíčů musí mít jedinečný název. V následujících příkladech nahraďte <> názvu trezoru s jedinečným klíčem.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

### <a name="create-a-service-principal"></a>Vytvoření instančního objektu

Nejjednodušší způsob ověření aplikace .NET na principu shluků je se spravovanou identitou. podrobnosti najdete [v tématu Použití spravované identity služby App Service pro přístup k azure key vault](managed-identity.md) pro podrobnosti. Z důvodu jednoduchosti však tento rychlý start vytvoří aplikaci konzoly .NET. Ověření desktopové aplikace pomocí Azure vyžaduje použití instančního objektu a zásad řízení přístupu.

Vytvořte princip služby pomocí příkazu Azure CLI [az az ad sp create-for-rbac:](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)

```azurecli
az ad sp create-for-rbac -n "http://mySP" --sdk-auth
```

Tato operace vrátí řadu párů klíč / hodnota. 

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

Poznamenejte si clientId a clientSecret, jak budeme používat v [ověření do trezoru klíčů](#authenticate-to-your-key-vault) krok níže.

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>Poskytnutí přístupu k objektu služeb trezoru klíčů

Vytvořte zásady přístupu pro trezor klíčů, který uděluje oprávnění k instančnímu objektu klienta příkazu [az keyvault set-policy.](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) Udělte instančníobjekt get, list a nastavit oprávnění pro klíče i tajné klíče.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

## <a name="object-model"></a>Objektový model

Klientská knihovna Azure Key Vault pro rozhraní .NET umožňuje spravovat klíče a související prostředky, jako jsou certifikáty a tajné klíče. Ukázky kódu níže vám ukáže, jak nastavit tajný klíč a načíst tajný klíč.

Celá konzolová aplikace https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/akvdotnetje k dispozici na adrese .

## <a name="code-examples"></a>Příklady kódu

### <a name="add-directives"></a>Přidání směrnic

Na začátek kódu přidejte následující direktivy:

[!code-csharp[Directives](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=directives)]

### <a name="authenticate-to-your-key-vault"></a>Ověření do trezoru klíčů

Tento rychlý start rozhraní .NET spoléhá na proměnné prostředí pro ukládání pověření, která by neměla být uvedena v kódu. 

Před sestavením a spuštěním `setx` aplikace nastavte `akvClientId`pomocí `akvClientSecret` `akvTenantId`příkazu `akvSubscriptionId` proměnné , , a prostředí na výše uvedené hodnoty.

```console
setx akvClientId <your-clientID>

setx akvClientSecret <your-clientSecret>
````

Pokaždé, když `setx`zavoláte , měli byste dostat odpověď na "ÚSPĚCH: Zadaná hodnota byla uložena."

Přiřaďte tyto proměnné prostředí řetězcům v kódu a potom ověřte aplikaci jejich předáním [do třídy KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient):

[!code-csharp[Authentication](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=authentication)]

### <a name="save-a-secret"></a>Uložení tajného klíče

Nyní, když je vaše aplikace ověřena, můžete vložit tajný klíč do trezoru klíčů pomocí [metody SetSecretAsync](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync) To vyžaduje adresu URL trezoru klíčů, který je ve formuláři `https://<your-unique-keyvault-name>.vault.azure.net/secrets/`. To také vyžaduje název pro tajemství - používáme "mySecret". 

[!code-csharp[Set secret](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=setsecret)]

Pomocí příkazu [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) můžete ověřit, zda byl tajný klíč nastaven:

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Načtení tajného klíče

Nyní můžete načíst dříve nastavenou hodnotu metodou [GetSecretAsync.](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync)

[!code-csharp[Get secret](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=getsecret)]

Vaše tajemství je `keyvaultSecret.Value;`nyní uloženo jako .

## <a name="clean-up-resources"></a>Vyčištění prostředků

Když už nepotřebujete, můžete použít Azure CLI nebo Azure PowerShell odebrat trezor klíčů a odpovídající skupinu prostředků.

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili trezor klíčů, uložili tajný klíč a získali tento tajný klíč. Podívejte se na [celou konzolovou aplikaci na GitHubu](https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/akvdotnet).

Chcete-li se dozvědět více o trezoru klíčů a o tom, jak jej integrovat s aplikacemi, pokračujte v následujících článcích.

- Implementace [ověřování mezi službami do trezoru klíčů Azure pomocí rozhraní .NET](service-to-service-authentication.md)
- Přečtěte si [přehled trezoru klíčů Azure](key-vault-overview.md)
- Podívejte se na [průvodce vývojářem azure key vaultu](key-vault-developers-guide.md)
- Informace o [klíčích, tajných kódech a certifikátech](about-keys-secrets-and-certificates.md)
- Kontrola [doporučených postupů azure key vaultu](key-vault-best-practices.md)

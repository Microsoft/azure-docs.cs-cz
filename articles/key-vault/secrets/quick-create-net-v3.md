---
title: Rychlý Start – Azure Key Vault Klientská knihovna pro .NET (sada SDK V3)
description: Naučte se vytvářet, načítat a odstraňovat tajné klíče z trezoru klíčů Azure pomocí klientské knihovny .NET (V3).
author: msmbaldwin
ms.author: mbaldwin
ms.date: 11/05/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: 056dc7bbe29c3c963818ee2fca626be4e2be9645
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2020
ms.locfileid: "82983478"
---
# <a name="quickstart-azure-key-vault-client-library-for-net-sdk-v3"></a>Rychlý Start: Klientská knihovna Azure Key Vault pro .NET (sada SDK V3)

Začněte s klientskou knihovnou Azure Key Vault pro .NET. Postupujte podle následujících kroků a nainstalujte balíček a vyzkoušejte ukázkový kód pro základní úlohy.

> [!NOTE]
> V tomto rychlém startu se používá verze klientské knihovny Microsoft. Azure. 3.0.4 ve verzi v. Chcete-li použít nejaktuálnější verzi klientské knihovny Key Vault, přečtěte si téma [Azure Key Vault Klientská knihovna pro .NET (sada SDK v4)](quick-create-net.md). 

Azure Key Vault pomáhá chránit kryptografické klíče a tajné klíče používané cloudovými aplikacemi a službami. Pomocí klientské knihovny Key Vault pro .NET:

- Zvyšte zabezpečení a kontrolu nad klíči a hesly.
- Vytvářejte a importujte šifrovací klíče během několika minut.
- Snižte latenci díky škálování cloudu a globální redundanci.
- Zjednodušte a automatizujte úlohy pro certifikáty TLS/SSL.
- Použijte ověřený HSM úrovně 2 FIPS 140-2.

[Dokumentace k referenční dokumentaci](/dotnet/api/overview/azure/key-vault?view=azure-dotnet) | rozhraní API balíček[zdrojového kódu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault) | knihovny[(NuGet)](https://www.nuget.org/packages/Microsoft.Azure.KeyVault/)

> [!NOTE]
> Každý Trezor klíčů musí mít jedinečný název. V následujících příkladech nahraďte <název trezoru klíčů jedinečných> s názvem vašeho trezoru klíčů.


## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [.NET Core 3,1 SDK nebo novější](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* Rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) nebo [Azure PowerShell](/powershell/azure/overview)

V tomto rychlém startu se `dotnet`předpokládá, že používáte příkazy [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)a Windows v terminálu Windows (například [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-6), [Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6)nebo [Azure Cloud Shell](https://shell.azure.com/)).

## <a name="setting-up"></a>Nastavení

### <a name="create-new-net-console-app"></a>Vytvořit novou konzolovou aplikaci .NET

V okně konzoly pomocí `dotnet new` příkazu vytvořte novou konzolovou aplikaci .NET s názvem. `akv-dotnet`


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
> Každý Trezor klíčů musí mít jedinečný název. V následujících příkladech nahraďte <název trezoru klíčů jedinečných> s názvem vašeho trezoru klíčů.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

### <a name="create-a-service-principal"></a>Vytvoření instančního objektu

Nejjednodušší způsob, jak ověřit cloudovou aplikaci .NET, je spravovaná identita; Podrobnosti najdete v tématu [použití spravované identity App Service pro přístup k Azure Key Vault](../general/managed-identity.md) . 

V zájmu zjednodušení ale v tomto rychlém startu se vytvoří Konzolová aplikace .NET, která vyžaduje použití instančního objektu a zásad řízení přístupu. Vaše zásada služby vyžaduje jedinečný název ve formátu "http://&lt;my-Unique-Service-princip-name&gt;".

Vytvořte zásadu služby pomocí příkazu Azure CLI [AZ AD SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) :

```azurecli
az ad sp create-for-rbac -n "http://&lt;my-unique-service-principle-name&gt;" --sdk-auth
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

Před sestavením a spuštěním aplikace `setx` použijte příkaz pro nastavení proměnných prostředí `akvClientId`,, `akvClientSecret` `akvTenantId`a `akvSubscriptionId` na hodnoty, které jste si poznamenali výše.

**Windows**

```console
setx akvClientId "<your-clientID>"
setx akvClientSecret "<your-clientSecret>"
```

**Linux**

```bash
export akvClientId = "<your-clientID>"
export akvClientSecret = "<your-clientSecret>"
```

**MacOS**

```bash
export akvClientId = "<your-clientID>"
export akvClientSecret = "<your-clientSecret>"
```

Přiřaďte tyto proměnné prostředí řetězcům v kódu a pak ověřte svoji aplikaci jejich předáním do [třídy KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient):

[!code-csharp[Authentication](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=authentication)]

### <a name="save-a-secret"></a>Uložení tajného klíče

Teď, když je vaše aplikace ověřená, můžete do trezoru klíčů vložit tajný klíč pomocí [metody SetSecretAsync](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync) , která vyžaduje adresu URL vašeho trezoru klíčů, který je ve formuláři `https://<your-unique-keyvault-name>.vault.azure.net/secrets/`. Také vyžaduje název tajného kódu – používáme "mySecret". 

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
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili Trezor klíčů, uložili tajný klíč a získali tento tajný klíč. Podívejte se na [celou konzolovou aplikaci v GitHubu](https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/akvdotnet).

Další informace o Key Vault a o tom, jak je integrovat s vašimi aplikacemi, najdete dál v článcích níže.

- Implementace [ověřování služby-služba pro Azure Key Vault pomocí .NET](../general/service-to-service-authentication.md)
- Přečtěte si [přehled Azure Key Vault](../general/overview.md)
- Další informace najdete v [příručce pro vývojáře Azure Key Vault](../general/developers-guide.md) .
- Kontrola [Azure Key Vault osvědčených postupů](../general/best-practices.md)

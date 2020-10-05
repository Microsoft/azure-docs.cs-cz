---
title: Rychlý Start – Azure Key Vault Klientská knihovna pro .NET (sada SDK V3)
description: Naučte se vytvářet, načítat a odstraňovat tajné klíče z trezoru klíčů Azure pomocí klientské knihovny .NET (V3).
author: msmbaldwin
ms.author: mbaldwin
ms.date: 11/05/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: 2e5f3357baa774c9690e079a82b11d2c651ebe9b
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "87078880"
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

[Referenční dokumentace k](/dotnet/api/overview/azure/key-vault?view=azure-dotnet)  |  rozhraní API [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault)  |  [Balíček (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.KeyVault/)

> [!NOTE]
> Každý Trezor klíčů musí mít jedinečný název. V následujících příkladech nahraďte <název trezoru klíčů jedinečných> s názvem vašeho trezoru klíčů.


## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [.NET Core 3,1 SDK nebo novější](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* Rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) nebo [Azure PowerShell](/powershell/azure/)

V tomto rychlém startu se předpokládá, že používáte `dotnet` příkazy [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)a Windows v terminálu Windows (například [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-6), [Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6)nebo [Azure Cloud Shell](https://shell.azure.com/)).

## <a name="setting-up"></a>Nastavení

### <a name="create-new-net-console-app"></a>Vytvořit novou konzolovou aplikaci .NET

V okně konzoly pomocí `dotnet new` příkazu vytvořte novou konzolovou aplikaci .NET s názvem `akv-dotnet` .


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

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

### <a name="create-a-service-principal"></a>Vytvoření instančního objektu

[!INCLUDE [Create a service principal](../../../includes/key-vault-sp-creation.md)]

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>Udělte instančnímu objektu přístup k vašemu trezoru klíčů.

[!INCLUDE [Give the service principal access to your key vault](../../../includes/key-vault-sp-kv-access.md)]

## <a name="object-model"></a>Objektový model

Klientská knihovna Azure Key Vault pro .NET umožňuje správu klíčů a souvisejících prostředků, jako jsou certifikáty a tajné klíče. Následující ukázka kódu vám ukáže, jak nastavit tajný klíč a načíst tajný kód.

Celá Konzolová aplikace je k dispozici na adrese https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/akvdotnet .

## <a name="code-examples"></a>Příklady kódu

### <a name="add-directives"></a>Přidat direktivy

Do horní části kódu přidejte následující direktivy:

[!code-csharp[Directives](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=directives)]

### <a name="authenticate-to-your-key-vault"></a>Ověřování pro váš Trezor klíčů

Tento rychlý Start .NET spoléhá na proměnné prostředí pro ukládání přihlašovacích údajů, které by neměly být vloženy do kódu. 

Před sestavením a spuštěním aplikace použijte `setx` příkaz pro nastavení `akvClientId` `akvClientSecret` `akvTenantId` proměnných prostředí,, a `akvSubscriptionId` na hodnoty, které jste si poznamenali výše.

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

Teď, když je vaše aplikace ověřená, můžete do trezoru klíčů vložit tajný klíč pomocí [metody SetSecretAsync](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync) , která vyžaduje adresu URL vašeho trezoru klíčů, který je ve formuláři `https://<your-unique-keyvault-name>.vault.azure.net/secrets/` . Také vyžaduje název tajného kódu – používáme "mySecret". 

[!code-csharp[Set secret](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=setsecret)]

Můžete ověřit, jestli je tajný kód nastavený pomocí příkazu [AZ klíčů trezor tajného zobrazení](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) :

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Načtení tajného kódu

Nyní můžete načíst dříve nastavenou hodnotu pomocí [metody GetSecretAsync](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync)

[!code-csharp[Get secret](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=getsecret)]

Váš tajný kód se teď uloží jako `keyvaultSecret.Value;` .

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

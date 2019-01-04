---
title: 'Rychlý start: Nastavení a načtení tajného klíče ze služby Azure Key Vault pomocí webové aplikace node - Azure Key Vault | Dokumentace Microsoftu'
description: 'Rychlý start: Nastavení a načtení tajného klíče ze služby Azure Key Vault pomocí webové aplikace .NET'
services: key-vault
author: prashanthyv
manager: sumedhb
ms.service: key-vault
ms.topic: quickstart
ms.date: 01/02/2019
ms.author: barclayn
ms.custom: mvc
ms.openlocfilehash: 20d47ecaea8ce393f60cba93c3dbcf7ca4a076c8
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2019
ms.locfileid: "54002599"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-by-using-a-net-web-app"></a>Rychlý start: Nastavení a načtení tajného klíče ze služby Azure Key Vault pomocí webové aplikace .NET

V tomto rychlém startu si projdete nezbytné kroky pro využití webové aplikace Azure k načtení informací ze služby Azure Key Vault pomocí spravovaných identit pro prostředky Azure. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření trezoru klíčů
> * Uložení tajného klíče v trezoru klíčů
> * Načtení tajného klíče z trezoru klíčů
> * Vytvoření webové aplikace Azure
> * Povolení [identity spravované služby](../active-directory/managed-identities-azure-resources/overview.md) pro webovou aplikaci
> * Udělení požadovaných oprávnění k načtení dat z trezoru klíčů pro webovou aplikaci

Než budeme pokračovat, přečtěte si [základní koncepty](key-vault-whatis.md#basic-concepts).

>[!NOTE]
>Služba Key Vault je centrální úložiště pro ukládání tajných klíčů prostřednictvím kódu programu. Aby to bylo možné, aplikace a uživatelé se nejprve musí ve službě Key Vault ověřit, tedy předložit tajný klíč. Za účelem dodržení osvědčených postupů zabezpečení je potřeba tento první tajný klíč pravidelně obměňovat. 
>
>Při použití [identit spravovaných služeb pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md) se aplikacím spouštěným v Azure udělí identita, kterou automaticky spravuje Azure. To vám pomůže vyřešit *problém se zavedením tajného klíče*, abyste uživatelům a aplikacím umožnili dodržovat osvědčené postupy bez starostí o obměňování prvního tajného klíče.

## <a name="prerequisites"></a>Požadavky

* Ve Windows:
  * [Visual Studio 2017 verze 15.7.3 nebo novější](https://www.microsoft.com/net/download/windows) s následujícími úlohami:
    * Vývoj pro ASP.NET a web
    * Vývoj aplikací pro různé platformy pomocí rozhraní .NET Core
  * [Sada .NET Core SDK 2.1 nebo novější](https://www.microsoft.com/net/download/windows)

* Na počítači Mac:
  * Přečtěte si, [Co je nového v sadě Visual Studio pro Mac](https://visualstudio.microsoft.com/vs/mac/).

* Všechny platformy:
  * Git ([stáhnout](https://git-scm.com/downloads)).
  * Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.
  * [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) verze 2.0.4 nebo novější. K dispozici pro Windows, Mac a Linux.

## <a name="log-in-to-azure"></a>Přihlášení k Azure

Pokud se chcete přihlásit k Azure pomocí Azure CLI, zadejte:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az-group-create). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Vyberte název skupiny prostředků a nahraďte zástupný text.
Následující příklad vytvoří skupinu prostředků v umístění USA – východ:

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "East US"
```

V tomto článku se používá skupina prostředků, kterou jste právě vytvořili.

## <a name="create-a-key-vault"></a>Vytvořte trezor klíčů

Dále ve skupině prostředků, kterou jste vytvořili v předchozím kroku, vytvoříte trezor klíčů. Zadejte tyto informace:

* Název trezoru klíčů: Název musí být řetězec dlouhý 3 až 24 znaků a musí obsahovat pouze (0-9, a – z, A-Z a -).
* Název skupiny prostředků.
* Umístění: **USA – východ**.

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "East US"
```

V tuto chvíli je váš účet Azure jediným účtem s oprávněním provádět jakékoli operace s tímto novým trezorem.

## <a name="add-a-secret-to-the-key-vault"></a>Přidání tajného klíče do trezoru klíčů

Tajný klíč přidáváme proto, abychom ukázali, jak to funguje. Mohli byste ukládat připojovací řetězec SQL nebo jakékoli jiné informace, které potřebujete zabezpečeně uchovávat a současně zpřístupnit vaší aplikaci.

Zadáním následujících příkazů vytvořte v trezoru klíčů tajný klíč **AppSecret**. V tomto tajném klíči bude uložená hodnota **MySecret**.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Pokud chcete zobrazit hodnotu v tajném kódu jako prostý text:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

Tento příkaz zobrazí informace o tajném klíči, včetně identifikátoru URI. Po dokončení těchto kroků byste měli mít identifikátor URI pro tajný klíč v trezoru klíčů. Poznamenejte si tyto informace. Budete je potřebovat později.

## <a name="clone-the-repo"></a>Klonování úložiště

Naklonováním úložiště vytvořte místní kopii, kde můžete upravit zdroj. Spusťte následující příkaz:

```
git clone https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart.git
```

## <a name="open-and-edit-the-solution"></a>Otevření a úprava řešení

Upravte soubor program.cs, aby se ukázka spustila s konkrétním názvem vašeho trezoru klíčů:

1. Přejděte do složky key-vault-dotnet-core-quickstart.
2. V sadě Visual Studio 2017 otevřete soubor key-vault-dotnet-core-quickstart.sln.
3. Otevřete soubor Program.cs a nahraďte zástupný text *YourKeyVaultName* názvem trezoru klíčů, který jste předtím vytvořili.

Toto řešení používá knihovny NuGet [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) a [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault).

## <a name="run-the-app"></a>Spuštění aplikace

V hlavní nabídce sady Visual Studio 2017 vyberte **Ladit** > **Spustit bez ladění**. Jakmile se zobrazí prohlížeč, přejděte na stránku **O aplikaci**. Zobrazí se hodnota tajného klíče **AppSecret**.

## <a name="publish-the-web-application-to-azure"></a>Publikování webové aplikace do Azure

Publikujte tuto aplikaci do Azure, abyste ji mohli zobrazit živě jako webovou aplikaci a abyste viděli, že můžete načíst hodnotu tajného klíče:

1. V sadě Visual Studio vyberte projekt **key-vault-dotnet-core-quickstart**.
2. Vyberte **Publikovat** > **Spustit**.
3. Vytvořte novou službu **App Service** a pak vyberte **Publikovat**.
4. Změňte název aplikace na **keyvaultdotnetcorequickstart**.
5. Vyberte **Vytvořit**.

>[!VIDEO https://sec.ch9.ms/ch9/e93d/a6ac417f-2e63-4125-a37a-8f34bf0fe93d/KeyVault_high.mp4]

## <a name="enable-a-managed-identity-for-the-web-app"></a>Povolení spravované identity pro webovou aplikaci

Azure Key Vault nabízí možnost bezpečného ukládání přihlašovacích údajů a dalších klíčů a tajných kódů, ale váš kód se musí ověřit ve službě Key Vault, aby je mohl načíst. [Přehled spravovaných identit pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md) tuto překážku usnadňuje tím, že dává službám Azure v Azure Active Directory (Azure AD) automaticky spravovanou identitu. Tuto identitu můžete použít k ověření pro jakoukoli službu, která podporuje ověřování Azure AD, včetně služby Key Vault, aniž byste ve vašem kódu museli mít přihlašovací údaje.

1. Vraťte se k Azure CLI.
2. Spusťte příkaz assign-identity a vytvořte identitu pro tuto aplikaci:

   ```azurecli
   az webapp identity assign --name "keyvaultdotnetcorequickstart" --resource-group "<YourResourceGroupName>"
   ```

>[!NOTE]
>Tento příkaz v tomto postupu je ekvivalentem přechodu na portál a přepnutí nastavení **Identita / Přiřazeno systémem** na hodnotu **Zapnuto** ve vlastnostech webové aplikace.

## <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>Přiřazení oprávnění ke čtení tajných kódů ze služby Key Vault vaší aplikaci

Poznamenejte si výstup, který se zobrazí, když publikujete aplikaci do Azure. Měl by mít následující formát:
        
        {
          "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "SystemAssigned"
        }
        
Potom spusťte tento příkaz, ve kterém použijete název vašeho trezoru klíčů a hodnotu **PrincipalId**:

```azurecli

az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get list

```

Když teď spustíte aplikaci, měla by se zobrazit načtená hodnota vašeho taného klíče. Ve výše uvedeném příkazu udělujete identitě spravované služby App Service oprávnění k operacím **get** a **list** ve službě Key Vault.

## <a name="next-steps"></a>Další postup

* [Další informace o službě Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)
* [Sada Azure SDK pro .NET](https://github.com/Azure/azure-sdk-for-net)
* [Referenční informace k rozhraní Azure REST API](https://docs.microsoft.com/rest/api/keyvault/)

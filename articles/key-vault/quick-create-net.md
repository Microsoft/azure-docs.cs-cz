---
title: Rychlý start Azure – Konfigurace webové aplikace Azure pro nastavení a načtení tajného klíče ze služby Key Vault | Microsoft Docs
description: Rychlý start, který ukazuje, jak nakonfigurovat aplikaci ASP.Net Core pro nastavení a načtení tajného klíče ze služby Key Vault
services: key-vault
author: prashanthyv
manager: sumedhb
ms.service: key-vault
ms.topic: quickstart
ms.date: 07/24/2018
ms.author: barclayn
ms.custom: mvc
ms.openlocfilehash: 8b5624ae3083d92213b4ee919dc0860bf5ff4ab7
ms.sourcegitcommit: fc5555a0250e3ef4914b077e017d30185b4a27e6
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/03/2018
ms.locfileid: "39480198"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-a-net-web-app"></a>Rychlý start: Nastavení a načtení tajného klíče ze služby Azure Key Vault pomocí webové aplikace .NET

V tomto rychlém startu si projdete nezbytné kroky pro využití webové aplikace Azure k načtení informací z trezoru klíčů pomocí identit spravované služby. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření trezoru klíčů
> * Uložení tajného kódu ve službě Key Vault
> * Načtení tajného kódu ze služby Key Vault
> * Vytvoření webové aplikace Azure
> * [Povolení identit spravované služby](../active-directory/managed-service-identity/overview.md)
> * Udělení požadovaných oprávnění k načtení dat ze služby Key Vault pro webovou aplikaci

Než budeme pokračovat, přečtěte si [základní koncepty](key-vault-whatis.md#basic-concepts), hlavně informace o [identitě spravované služby](../active-directory/managed-service-identity/overview.md).

## <a name="prerequisites"></a>Požadavky

* Ve Windows:
  * [Visual Studio 2017 verze 15.7.3 nebo novější](https://www.microsoft.com/net/download/windows) s následujícími úlohami:
    * Vývoj pro ASP.NET a web
    * Vývoj aplikací pro různé platformy pomocí rozhraní .NET Core
  * [Sada .NET Core SDK 2.1 nebo novější](https://www.microsoft.com/net/download/windows)

* Na počítači Mac:
  * https://visualstudio.microsoft.com/vs/mac/

* Všechny platformy:
  * Z [této stránky](https://git-scm.com/downloads) si stáhněte GIT.
  * Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.
  * [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Potřebujete Azure CLI verze 2.0.4 nebo novější. K dispozici pro Windows, Mac a Linux.

## <a name="login-to-azure"></a>Přihlášení k Azure

   Pokud se chcete přihlásit k Azure pomocí rozhraní příkazového řádku, můžete zadat:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az-group-create). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Vyberte název skupiny prostředků a nahraďte zástupný text.
Následující příklad vytvoří skupinu prostředků *<YourResourceGroupName>* v umístění *eastus*.

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "East US"
```

V tomto článku se používá skupina prostředků, kterou jste právě vytvořili.

## <a name="create-an-azure-key-vault"></a>Vytvoření služby Azure Key Vault

Dále ve skupině prostředků vytvořené v předchozím kroku vytvoříte službu Key Vault. Zadejte tyto informace:

* Název trezoru – **tady vyberte název trezoru klíčů**. Název trezoru klíčů musí být řetězec dlouhý 3–24 znaků a může obsahovat jenom znaky 0–9, a–z, A–Z a -.
* Název skupiny prostředků – **tady vyberte název skupiny prostředků**.
* Umístění – **USA – východ**.

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "East US"
```

V tuto chvíli je váš účet Azure jediným účtem s oprávněním provádět jakékoli operace s tímto novým trezorem.

## <a name="add-a-secret-to-key-vault"></a>Přidání tajného klíče do služby Key Vault

Tajný klíč přidáváme proto, abychom ukázali, jak to funguje. Mohli byste ukládat připojovací řetězec SQL nebo jakékoli jiné informace, které potřebujete zabezpečeně uchovávat a současně zpřístupnit vaší aplikaci. V tomto kurzu budeme heslo označovat jako **AppSecret** a budeme v něm ukládat hodnotu **MySecret**.

Zadáním následujících příkazů vytvořte ve službě Key Vault tajný kód **AppSecret**, který bude uchovávat hodnotu **MySecret**:

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Pokud chcete zobrazit hodnotu v tajném kódu jako prostý text:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

Tento příkaz zobrazí informace o tajném kódu, včetně identifikátoru URI. Po dokončení těchto kroků byste měli mít URI pro tajný kód ve službě Azure Key Vault. Poznamenejte si tyto informace. Budete je potřebovat později.

## <a name="clone-the-repo"></a>Klonování úložiště

Spuštěním následujícího příkazu naklonujte úložiště, abyste získali místní kopii umožňující úpravu zdroje:

```
git clone https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart.git
```

## <a name="open-and-edit-the-solution"></a>Otevření a úprava řešení

Upravte soubor program.cs, abyste mohli spustit ukázku s konkrétním názvem trezoru klíčů.

1. Přejděte do složky key-vault-dotnet-core-quickstart.
2. V sadě Visual Studio 2017 otevřete soubor key-vault-dotnet-core-quickstart.sln.
3. Otevřete soubor Program.cs a nahraďte zástupný text <YourKeyVaultName> názvem trezoru klíčů, který jste předtím vytvořili.

Toto řešení používá knihovny NuGet [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) a [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault).

## <a name="run-the-app"></a>Spuštění aplikace

V hlavní nabídce sady Visual Studio 2017 zvolte Ladit > Spustit bez ladění. Jakmile se zobrazí prohlížeč, přejděte na stránku O aplikaci. Zobrazí se hodnota pro AppSecret.

## <a name="publish-the-web-application-to-azure"></a>Publikování webové aplikace do Azure

Tuto aplikaci publikujeme do Azure, abychom ji mohli zobrazit živě jako webovou aplikace a zjistili, jestli načítá tajnou hodnotu.

1. V sadě Visual Studio vyberte projekt **key-vault-dotnet-core-quickstart**.
2. Vyberte **Publikovat** a potom **Spustit**.
3. Vytvořte novou službu **App Service** a vyberte **Publikovat**.
4. Změňte název aplikace na „keyvaultdotnetcorequickstart“.
5. Vyberte **Vytvořit**.

>[!VIDEO https://sec.ch9.ms/ch9/e93d/a6ac417f-2e63-4125-a37a-8f34bf0fe93d/KeyVault_high.mp4]

## <a name="enable-managed-service-identities-msi"></a>Povolení identit spravované služby (MSI)

Azure Key Vault nabízí možnost bezpečného ukládání přihlašovacích údajů a dalších klíčů a tajných kódů, ale váš kód se musí ověřit ve službě Azure Key Vault, aby je mohl načíst. Identita spravované služby (MSI) to usnadňuje tím, že poskytuje službám Azure automaticky spravovanou identitu v Azure Active Directory (Azure AD). Tuto identitu můžete použít k ověření pro jakoukoli službu, která podporuje ověřování Azure AD, včetně služby Key Vault, aniž byste ve vašem kódu museli mít přihlašovací údaje.

1. Vraťte se k Azure CLI.
2. Spusťte příkaz assign-identity a vytvořte identitu pro tuto aplikaci:

```azurecli
az webapp identity assign --name "keyvaultdotnetcorequickstart" --resource-group "<YourResourceGroupName>"
```

>[!NOTE]
>Tento příkaz je ekvivalentem přechodu na portál a nastavení **Identity spravované služby** na hodnotu **Zapnuto** ve vlastnostech webové aplikace.

## <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>Přiřazení oprávnění ke čtení tajných kódů ze služby Key Vault vaší aplikaci

Poznamenejte si výstup, který se zobrazí, když [publikujete aplikaci do Azure][]. Měl by mít následující formát:
        
        {
          "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "SystemAssigned"
        }
        
Potom spusťte příkaz, ve kterém použijete název vašeho trezoru klíčů a zkopírovanou hodnotu PrincipalId:

```azurecli

az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get

```

## <a name="next-steps"></a>Další kroky

* [Domovská stránka služby Azure Key Vault](https://azure.microsoft.com/services/key-vault/)
* [Dokumentace ke službě Azure Key Vault](https://docs.microsoft.com/azure/key-vault/)
* [Sada Azure SDK pro .NET](https://github.com/Azure/azure-sdk-for-net)
* [Reference k rozhraní Azure REST API](https://docs.microsoft.com/rest/api/keyvault/)

---
title: Rychlý Start – použití Azure Key Vault tajných klíčů v pracovních postupech akcí GitHubu
description: Použití Key Vault tajných klíčů v akcích GitHubu k automatizaci pracovních postupů vývoje softwaru
author: juliakm
ms.custom: github-actions-azure
ms.author: jukullam
ms.date: 11/24/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: 9509f84b14a42180189a529282b5db348deab279
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95920281"
---
# <a name="use-key-vault-secrets-in-github-actions-workflows"></a>Použití Key Vault tajných kódů v pracovních postupech akcí GitHubu

Používejte Key Vault tajných klíčů ve svých [akcích GitHubu](https://help.github.com/en/articles/about-github-actions) a bezpečně ukládejte hesla a další tajné klíče v trezoru klíčů Azure. Přečtěte si další informace o [Key Vault](../general/overview.md). 

Díky akcím Key Vault a GitHubu máte výhodou nástrojů pro správu centralizovaných tajných klíčů a všech výhod akcí GitHubu. Akce GitHubu jsou sadou funkcí v GitHubu pro automatizaci pracovních postupů vývoje softwaru. Pracovní postupy můžete nasadit na stejném místě, kam ukládáte kód a spolupracujete na žádostech o přijetí změn a potížích. 


## <a name="prerequisites"></a>Požadavky 
- Účet GitHub. Pokud ho ještě nemáte, zaregistrujte se [zdarma](https://github.com/join).  
- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Aplikace Azure připojená k úložišti GitHub. V tomto příkladu [se k Azure App Service používají kontejnery nasazení](https://docs.microsoft.com/azure/developer/javascript/tutorial-vscode-docker-node-01). 
- Trezor klíčů Azure.  Azure Key Vault můžete vytvořit pomocí Azure Portal, Azure CLI nebo Azure PowerShell.

## <a name="workflow-file-overview"></a>Přehled souboru pracovního postupu

Pracovní postup je definovaný souborem YAML (. yml) v `/.github/workflows/` cestě v úložišti. Tato definice obsahuje různé kroky a parametry, které tvoří pracovní postup.

Soubor má ověřování pomocí akcí GitHubu dvě části:

|Sekce  |Úlohy  |
|---------|---------|
|**Authentication** | 1. Definujte instanční objekt. <br /> 2. Vytvořte tajný klíč GitHubu. <br /> 3. Přidejte přiřazení role. |
|**Key Vault** | 1. Přidejte akci trezoru klíčů. <br /> 2. odkaz na tajný kód trezoru klíčů |

## <a name="authentication"></a>Authentication

[Instanční objekt](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) můžete vytvořit pomocí příkazu [AZ AD SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true) v rozhraní příkazového [řádku Azure CLI](/cli/azure/). Spusťte tento příkaz s [Azure Cloud Shell](https://shell.azure.com/) v Azure Portal nebo vyberte tlačítko **vyzkoušet** .

```azurecli-interactive
   az ad sp create-for-rbac --name {myApp} --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/{MyResourceGroup} --sdk-auth
```

V předchozím příkladu Nahraďte zástupné symboly IDENTIFIKÁTORem vašeho předplatného a názvem skupiny prostředků. Zástupný symbol nahraďte `myApp` názvem vaší aplikace. Výstupem je objekt JSON s přihlašovacími údaji přiřazení role, které poskytují přístup k vaší App Service aplikaci, která je podobná níže. Zkopírujte tento objekt JSON pro pozdější verzi. Tyto oddíly budete potřebovat jenom s `clientId` `clientSecret` hodnotami,, `subscriptionId` a `tenantId` . 

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

### <a name="configure-the-github-secret"></a>Konfigurace tajného kódu GitHubu

Vytváření tajných kódů pro přihlašovací údaje Azure, skupinu prostředků a odběry. 

1. V [GitHubu](https://github.com/)přejděte do úložiště.

1. Vyberte **nastavení > tajných klíčů > nový tajný kód**.

1. Do pole hodnota tajného klíče vložte celý výstup JSON z příkazu Azure CLI. Zadejte název tajného klíče `AZURE_CREDENTIALS` .

1. Zkopírujte hodnotu `clientId` pro pozdější použití. 

### <a name="add-a-role-assignment"></a>Přidat přiřazení role 
 
Je potřeba udělit přístup k instančnímu objektu Azure, abyste měli přístup k trezoru klíčů pro `get` operace a `list` . Pokud to neuděláte, nebudete moct použít instanční objekt. 

Nahraďte `keyVaultName` názvem vašeho trezoru klíčů a `clientIdGUID` hodnotou vaší `clientId` . 

```azurecli-interactive
    az keyvault set-policy -n {keyVaultName} --secret-permissions get list --spn {clientIdGUID}
```

## <a name="use-the-azure-key-vault-action"></a>Použití akce trezoru klíčů Azure

Pomocí [Akce trezoru klíčů Azure](https://github.com/marketplace/actions/azure-key-vault-get-secrets)můžete načíst jeden nebo více tajných kódů z instance trezoru klíčů Azure a využít je ve svých pracovních postupech pro akce GitHubu.

Načtená tajná data jsou nastavena jako výstupy a také jako proměnné prostředí. Proměnné jsou automaticky maskovány při jejich tisku do konzoly nebo do protokolů.

```yaml
    - uses: Azure/get-keyvault-secrets@v1
      with:
        keyvault: "my Vault" # name of key vault in Azure portal
        secrets: 'mySecret'  # comma separated list of secret keys to fetch from key vault 
      id: myGetSecretAction # ID for secrets that you will reference
```

Pokud chcete v pracovním postupu použít Trezor klíčů, potřebujete akci trezoru klíčů a odkaz na tuto akci. 

V tomto příkladu je pojmenován Trezor klíčů `containervault` . Do prostředí se přidají dvě tajné klíče trezoru klíčů, a to pomocí akce trezoru klíčů – `containerPassword` a `containerUsername` . 

Hodnoty trezoru klíčů se později odkazují v úloze přihlášení Docker s předponou `steps.myGetSecretAction.outputs` . 

```yaml
name: Example key vault flow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - uses: actions/checkout@v2
    - uses: Azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - uses: Azure/get-keyvault-secrets@v1
      with: 
        keyvault: "containervault"
        secrets: 'containerPassword, containerUsername'
      id: myGetSecretAction
    - uses: azure/docker-login@v1
      with:
        login-server: myregistry.azurecr.io
        username: ${{ steps.myGetSecretAction.outputs.containerUsername }}
        password: ${{ steps.myGetSecretAction.outputs.containerPassword }}
    - run: |
        docker build . -t myregistry.azurecr.io/myapp:${{ github.sha }}
        docker push myregistry.azurecr.io/myapp:${{ github.sha }}     
    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'myapp'
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        images: 'myregistry.azurecr.io/myapp:${{ github.sha }}'
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Když už není potřeba vaše aplikace Azure, úložiště GitHub a trezor klíčů, vyčistěte prostředky, které jste nasadili, odstraněním skupiny prostředků pro aplikaci, úložiště GitHubu a trezoru klíčů.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o Azure Key Vault](../general/overview.md)

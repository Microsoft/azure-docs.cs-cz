---
title: Nasazení šablon Správce prostředků pomocí akcí GitHubu
description: Popisuje způsob nasazení Azure Resource Manager šablon (šablon ARM) pomocí akcí GitHubu.
ms.topic: conceptual
ms.date: 10/13/2020
ms.custom: github-actions-azure, devx-track-azurecli
ms.openlocfilehash: 564a21d565fb80eba605eece95562a809a93246f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103471921"
---
# <a name="deploy-arm-templates-by-using-github-actions"></a>Nasazení šablon ARM pomocí akcí GitHubu

[Akce GitHubu](https://docs.github.com/en/actions) je sada funkcí v GitHubu pro automatizaci pracovních postupů vývoje softwaru na stejném místě, kam ukládáte kód a spolupracujete na žádostech o přijetí změn a problémech.

Použijte [akci nasadit Azure Resource Manager šablonu](https://github.com/marketplace/actions/deploy-azure-resource-manager-arm-template) k automatizaci nasazení Azure Resource Manager šablony (šablony ARM) do Azure.

## <a name="prerequisites"></a>Předpoklady

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Účet GitHub. Pokud ho ještě nemáte, zaregistrujte se [zdarma](https://github.com/join).
    - Úložiště GitHub pro uložení šablon Správce prostředků a souborů pracovního postupu. Pokud ho chcete vytvořit, přečtěte si téma [Vytvoření nového úložiště](https://docs.github.com/en/github/creating-cloning-and-archiving-repositories/creating-a-new-repository).


## <a name="workflow-file-overview"></a>Přehled souboru pracovního postupu

Pracovní postup je definovaný souborem YAML (. yml) v `/.github/workflows/` cestě v úložišti. Tato definice obsahuje různé kroky a parametry, které tvoří pracovní postup.

Soubor má dvě části:

|Sekce  |Úlohy  |
|---------|---------|
|**Authentication** | 1. Definujte instanční objekt. <br /> 2. Vytvořte tajný klíč GitHubu. |
|**Nasazení** | 1. Nasaďte šablonu Správce prostředků. |

## <a name="generate-deployment-credentials"></a>Generovat přihlašovací údaje nasazení


[Instanční objekt](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) můžete vytvořit pomocí příkazu [AZ AD SP Create-for-RBAC](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) v rozhraní příkazového [řádku Azure CLI](/cli/azure/). Spusťte tento příkaz s [Azure Cloud Shell](https://shell.azure.com/) v Azure Portal nebo vyberte tlačítko **vyzkoušet** .

Vytvořte skupinu prostředků, pokud ji ještě nemáte.

```azurecli-interactive
    az group create -n {MyResourceGroup} -l {location}
```

Zástupný symbol nahraďte `myApp` názvem vaší aplikace.

```azurecli-interactive
   az ad sp create-for-rbac --name {myApp} --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/{MyResourceGroup} --sdk-auth
```

V předchozím příkladu Nahraďte zástupné symboly IDENTIFIKÁTORem vašeho předplatného a názvem skupiny prostředků. Výstupem je objekt JSON s přihlašovacími údaji přiřazení role, které poskytují přístup k vaší App Service aplikaci, která je podobná níže. Zkopírujte tento objekt JSON pro pozdější verzi. Tyto oddíly budete potřebovat jenom s `clientId` `clientSecret` hodnotami,, `subscriptionId` a `tenantId` .

```output
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> Je vždy dobrým zvykem udělit minimální přístup. Obor v předchozím příkladu je omezený na skupinu prostředků.



## <a name="configure-the-github-secrets"></a>Konfigurace tajných kódů GitHubu

Musíte vytvořit tajné kódy pro přihlašovací údaje Azure, skupinu prostředků a odběry.

1. V [GitHubu](https://github.com/)přejděte do úložiště.

1. Vyberte **nastavení > tajných klíčů > nový tajný kód**.

1. Do pole hodnota tajného klíče vložte celý výstup JSON z příkazu Azure CLI. Zadejte název tajného klíče `AZURE_CREDENTIALS` .

1. Vytvořte další tajný kód s názvem `AZURE_RG` . Přidejte název skupiny prostředků do pole hodnota tajného klíče (příklad: `myResourceGroup` ).

1. Vytvořte další tajný kód s názvem `AZURE_SUBSCRIPTION` . Přidejte ID předplatného do pole hodnota tajného kódu (příklad: `90fd3f9d-4c61-432d-99ba-1273f236afa2` ).

## <a name="add-resource-manager-template"></a>Přidat šablonu Správce prostředků

Přidejte šablonu Správce prostředků do úložiště GitHub. Tato šablona vytvoří účet úložiště.

```url
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Soubor můžete umístit kamkoli do úložiště. Ukázka pracovního postupu v další části předpokládá, že se soubor šablony jmenuje **azuredeploy.jsv** a je uložený v kořenovém adresáři úložiště.

## <a name="create-workflow"></a>Vytvoření pracovního postupu

Soubor pracovního postupu musí být uložený ve složce **. GitHub/Workflows** v kořenovém adresáři úložiště. Přípona souboru pracovního postupu může být buď **. yml** nebo **. yaml**.

1. Z vašeho úložiště GitHub v horní nabídce vyberte **Akce** .
1. Vyberte **nový pracovní postup**.
1. Vyberte **nastavit pracovní postup sami**.
1. Pokud upřednostňujete jiný název než **Main. yml**, přejmenujte soubor pracovního postupu. Příklad: **deployStorageAccount. yml**.
1. Obsah souboru YML nahraďte následujícím kódem:

    ```yml
    on: [push]
    name: Azure ARM
    jobs:
      build-and-deploy:
        runs-on: ubuntu-latest
        steps:

          # Checkout code
        - uses: actions/checkout@main

          # Log into Azure
        - uses: azure/login@v1
          with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}

          # Deploy ARM template
        - name: Run ARM deploy
          uses: azure/arm-deploy@v1
          with:
            subscriptionId: ${{ secrets.AZURE_SUBSCRIPTION }}
            resourceGroupName: ${{ secrets.AZURE_RG }}
            template: ./azuredeploy.json
            parameters: storageAccountType=Standard_LRS

          # output containerName variable from template
        - run: echo ${{ steps.deploy.outputs.containerName }}
    ```
    > [!NOTE]
    > Místo toho můžete v akci nasazení ARM zadat soubor parametrů formátu JSON (příklad: `.azuredeploy.parameters.json` ).

    První část souboru pracovního postupu obsahuje:

    - **Name (název**): název pracovního postupu.
    - **zapnuto**: název událostí GitHubu, které aktivují pracovní postup. Pracovní postup se aktivuje, když je v hlavní větvi událost push, která upravuje aspoň jeden ze dvou zadaných souborů. Tyto dva soubory jsou pracovní postup a soubor šablony.

1. Vyberte **Start commit** (Spustit zápis).
1. Vyberte **potvrdit přímo do hlavní větve**.
1. Vyberte **Potvrdit nový soubor** (nebo **potvrďte změny**).

Vzhledem k tomu, že pracovní postup je nakonfigurován tak, aby se aktivoval buď pomocí souboru pracovního postupu, nebo aktualizovaného souboru šablony, pracovní postup se spustí hned po potvrzení změn.

## <a name="check-workflow-status"></a>Kontrolovat stav pracovního postupu

1. Vyberte kartu **Akce** . V seznamu se zobrazí pracovní postup **Vytvoření deployStorageAccount. yml** . Spuštění pracovního postupu trvá 1-2 minut.
1. Vyberte pracovní postup, který chcete otevřít.
1. V nabídce vyberte **Spustit nasazení ARM** a ověřte nasazení.

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud už vaše skupina prostředků a úložiště nepotřebujete, vyčistěte prostředky, které jste nasadili, odstraněním skupiny prostředků a úložiště GitHubu.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření první šablony ARM](./template-tutorial-create-first-template.md)

> [!div class="nextstepaction"]
> [Seznámení s modulem: Automatizace nasazení šablon ARM pomocí akcí GitHubu](/learn/modules/deploy-templates-command-line-github-actions/)

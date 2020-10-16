---
title: Nasazení šablon Správce prostředků pomocí akcí GitHubu
description: Popisuje způsob nasazení Azure Resource Manager šablon pomocí akcí GitHubu.
ms.topic: conceptual
ms.date: 10/13/2020
ms.custom: github-actions-azure,subject-armqs
ms.openlocfilehash: b5852a65b4ed3c7cc73352fed37eeff035f8563c
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92106786"
---
# <a name="deploy-azure-resource-manager-templates-by-using-github-actions"></a>Nasazení šablon Azure Resource Manager pomocí akcí GitHubu

[Akce GitHubu](https://help.github.com/actions/getting-started-with-github-actions/about-github-actions) je sada funkcí v GitHubu pro automatizaci pracovních postupů vývoje softwaru na stejném místě, kam ukládáte kód a spolupracujete na žádostech o přijetí změn a problémech.

Použijte [akci nasadit Azure Resource Manager šablonu](https://github.com/marketplace/actions/deploy-azure-resource-manager-arm-template) k automatizaci nasazení Správce prostředků šablony do Azure. 

## <a name="prerequisites"></a>Předpoklady

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Účet GitHub. Pokud ho ještě nemáte, zaregistrujte se [zdarma](https://github.com/join).  
    - Úložiště GitHub pro uložení šablon Správce prostředků a souborů pracovního postupu. Pokud ho chcete vytvořit, přečtěte si téma [Vytvoření nového úložiště](https://help.github.com/en/enterprise/2.14/user/articles/creating-a-new-repository).


## <a name="workflow-file-overview"></a>Přehled souboru pracovního postupu

Pracovní postup je definovaný souborem YAML (. yml) v `/.github/workflows/` cestě v úložišti. Tato definice obsahuje různé kroky a parametry, které tvoří pracovní postup.

Soubor má dvě části:

|Sekce  |Úlohy  |
|---------|---------|
|**Authentication** | 1. Definujte instanční objekt. <br /> 2. Vytvořte tajný klíč GitHubu. |
|**Nasazení** | 1. Nasaďte šablonu Správce prostředků. |

## <a name="generate-deployment-credentials"></a>Generovat přihlašovací údaje nasazení


[Instanční objekt](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) můžete vytvořit pomocí příkazu [AZ AD SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true) v rozhraní příkazového [řádku Azure CLI](/cli/azure/). Spusťte tento příkaz s [Azure Cloud Shell](https://shell.azure.com/) v Azure Portal nebo vyberte tlačítko **vyzkoušet** .

Zástupný symbol nahraďte `myApp` názvem vaší aplikace. 

```azurecli-interactive
   az ad sp create-for-rbac --name {myApp} --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} --sdk-auth
```

V předchozím příkladu Nahraďte zástupné symboly IDENTIFIKÁTORem vašeho předplatného a názvem skupiny prostředků. Výstupem je objekt JSON s přihlašovacími údaji přiřazení role, které poskytují přístup k vaší App Service aplikaci, která je podobná níže. Zkopírujte tento objekt JSON pro pozdější verzi.

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

1. Vytvořte další tajný kód s názvem `AZURE_RG` . Přidejte název vaší skupiny prostředků do pole hodnota tajného klíče. 

1. Vytvořte další tajný kód s názvem `AZURE_SUBSCRIPTION` . Přidejte své ID předplatného do pole hodnota tajného klíče. 

## <a name="add-resource-manager-template"></a>Přidat šablonu Správce prostředků

Přidejte šablonu Správce prostředků do úložiště GitHub. Tato šablona vytvoří účet úložiště.

```url
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Soubor můžete umístit kamkoli do úložiště. Ukázka pracovního postupu v další části předpokládá, že se soubor šablony jmenuje **azuredeploy.jsv**a je uložený v kořenovém adresáři úložiště.

## <a name="create-workflow"></a>Vytvořit pracovní postup

Soubor pracovního postupu musí být uložený ve složce **. GitHub/Workflows** v kořenovém adresáři úložiště. Přípona souboru pracovního postupu může být buď **. yml** nebo **. yaml**.

1. Z vašeho úložiště GitHub v horní nabídce vyberte **Akce** .
1. Vyberte **nový pracovní postup**.
1. Vyberte **nastavit pracovní postup sami**.
1. Pokud upřednostňujete jiný název než **Main. yml**, přejmenujte soubor pracovního postupu. Příklad: **deployStorageAccount. yml**.
1. Obsah souboru YML nahraďte následujícím:

    ```yml
    on: [push]
    name: Azure ARM
    jobs:
      build-and-deploy:
        runs-on: ubuntu-latest
        steps:

          # Checkout code
        - uses: actions/checkout@master

          # Log into Azure
        - uses: azure/login@v1
          with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}
     
          # Deploy ARM template
        - uses: azure/arm-deploy@v1
        - name: Run ARM deploy
          with:
            subscriptionId: ${{ secrets.AZURE_SUBSCRIPTION }}
            resourceGroupName: ${{ secrets.AZURE_RG }}
            template: ./azuredeploy.json
            parameters: storageAccountType=Standard_LRS
        
          # output containerName variable from template
        - run: echo ${{ steps.deploy.outputs.containerName }}
    ```

    První část souboru pracovního postupu obsahuje:

    - **Name (název**): název pracovního postupu.
    - **zapnuto**: název událostí GitHubu, které aktivují pracovní postup. Pracovní postup se aktivuje, když je v hlavní větvi událost push, která upravuje aspoň jeden ze dvou zadaných souborů. Tyto dva soubory jsou pracovní postup a soubor šablony.

1. Vyberte **Spustit potvrzení**.
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
> [Vytvoření první šablony ARM](/azure/azure-resource-manager/templates/template-tutorial-create-first-template)

---
title: Nasazení šablon Správce prostředků pomocí akcí GitHubu
description: Popisuje způsob nasazení Azure Resource Manager šablon pomocí akcí GitHubu.
ms.topic: conceptual
ms.date: 07/02/2020
ms.openlocfilehash: 313354499901bc69ec6e00f0ba7c385065cae615
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85854736"
---
# <a name="deploy-azure-resource-manager-templates-by-using-github-actions"></a>Nasazení šablon Azure Resource Manager pomocí akcí GitHubu

[Akce GitHubu](https://help.github.com/en/actions) umožňují vytvářet vlastní pracovní postupy pro životní cyklus vývoje softwaru přímo v úložišti GitHubu, kde jsou uložené vaše šablony Azure Resource Manager (ARM). [Pracovní postup](https://help.github.com/actions/reference/workflow-syntax-for-github-actions) je definován pomocí souboru YAML. Pracovní postupy mají jednu nebo více úloh s každou úlohou obsahující sadu kroků, které provádějí jednotlivé úlohy. Kroky mohou spustit příkazy nebo použít akci. Můžete vytvořit vlastní akce nebo použít akce, které sdílí [komunita GitHub](https://github.com/marketplace?type=actions) , a podle potřeby je přizpůsobit. Tento článek popisuje, jak pomocí [Akce CLI Azure](https://github.com/marketplace/actions/azure-cli-action) nasadit šablony Správce prostředků.

Akce CLI Azure má dvě závislé akce:

- **[Rezervace](https://github.com/marketplace/actions/checkout)**: rezervujte své úložiště, aby mohl pracovní postup získat přístup k libovolné zadané správce prostředků šabloně.
- **[Přihlášení Azure](https://github.com/marketplace/actions/azure-login)**: Přihlaste se pomocí přihlašovacích údajů Azure.

Základní pracovní postup pro nasazení šablony Správce prostředků může mít tři kroky:

1. Podívejte se na soubor šablony.
2. Přihlaste se k Azure.
3. Nasazení šablony Správce prostředků

## <a name="prerequisites"></a>Požadavky

K uložení šablon Správce prostředků a souborů pracovního postupu potřebujete úložiště GitHub. Pokud ho chcete vytvořit, přečtěte si téma [Vytvoření nového úložiště](https://help.github.com/en/enterprise/2.14/user/articles/creating-a-new-repository).

## <a name="configure-deployment-credentials"></a>Konfigurace přihlašovacích údajů pro nasazení

Akce přihlášení k Azure používá instanční objekt k ověřování v Azure. Objekt zabezpečení pracovního postupu CI/CD obvykle potřebuje předdefinované právo přispěvatele, aby bylo možné nasadit prostředky Azure.

Následující skript Azure CLI ukazuje, jak ve skupině prostředků Azure vygenerovat instanční objekt Azure s oprávněními přispěvatele. Tato skupina prostředků je místo, kde pracovní postup nasadí prostředky definované v šabloně Správce prostředků.

```azurecli
$projectName="[EnterAProjectName]"
$location="centralus"
$resourceGroupName="${projectName}rg"
$appName="http://${projectName}"
$scope=$(az group create --name $resourceGroupName --location $location --query 'id')
az ad sp create-for-rbac --name $appName --role Contributor --scopes $scope --sdk-auth
```

Upravte hodnotu **$ProjectName** a **$Location** ve skriptu. Název skupiny prostředků je název projektu s připojeným **RG** . V pracovním postupu musíte zadat název skupiny prostředků.

Skript výstupuje objekt JSON podobný tomuto:

```json
{
   "clientId": "<GUID>",
   "clientSecret": "<GUID>",
   "subscriptionId": "<GUID>",
   "tenantId": "<GUID>",
   (...)
}
```

Zkopírujte výstup JSON a uložte ho jako tajný kód GitHubu do vašeho úložiště GitHub. Pokud ještě nemáte úložiště, podívejte se na [předpoklady](#prerequisites) .

1. V úložišti GitHub vyberte kartu **Nastavení** .
1. V nabídce vlevo vyberte **tajná klíčová** okna.
1. Zadejte tyto hodnoty:

    - **Název**: AZURE_CREDENTIALS
    - **Hodnota**: (vložit výstup JSON)
1. Vyberte **Přidat tajný klíč**.

Je nutné zadat název tajného kódu v pracovním postupu.

## <a name="add-resource-manager-template"></a>Přidat šablonu Správce prostředků

Přidejte šablonu Správce prostředků do úložiště GitHub. Pokud ho nemáte, můžete použít následující šablonu. Šablona vytvoří účet úložiště.

```url
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Soubor můžete umístit kamkoli do úložiště. Ukázka pracovního postupu v další části předpokládá, že se soubor šablony jmenuje **azuredeploy.jsv**a je uložený ve složce s názvem **Templates** v kořenovém adresáři úložiště.

## <a name="create-workflow"></a>Vytvořit pracovní postup

Soubor pracovního postupu musí být uložený ve složce **. GitHub/Workflows** v kořenovém adresáři úložiště. Přípona souboru pracovního postupu může být buď **. yml** nebo **. yaml**.

Můžete buď vytvořit soubor pracovního postupu, a pak ho vložit do úložiště nebo ho odeslat do úložiště nebo použít následující postup:

1. Z vašeho úložiště GitHub v horní nabídce vyberte **Akce** .
1. Vyberte **nový pracovní postup**.
1. Vyberte **nastavit pracovní postup sami**.
1. Pokud upřednostňujete jiný název než **Main. yml**, přejmenujte soubor pracovního postupu. Příklad: **deployStorageAccount. yml**.
1. Obsah souboru YML nahraďte následujícím:

    ```yml
    name: Deploy ARM Template

    on:
      push:
        branches:
          - master
        paths:
          - ".github/workflows/deployStorageAccount.yml"
          - "templates/azuredeploy.json"

    jobs:
      deploy-storage-account-template:
        runs-on: ubuntu-latest
        steps:
          - name: Checkout source code
            uses: actions/checkout@master

          - name: Login to Azure
            uses: azure/login@v1
            with:
              creds: ${{ secrets.AZURE_CREDENTIALS }}


          - name: Deploy ARM Template
            uses: azure/CLI@v1
            with:
              inlineScript: |
                az deployment group create --resource-group myResourceGroup --template-file ./templates/azuredeploy.json
    ```

    Soubor pracovního postupu má tři části:

    - **Name (název**): název pracovního postupu.
    - **zapnuto**: název událostí GitHubu, které aktivují pracovní postup. Pracovní postup se aktivuje, když je v hlavní větvi událost push, která upravuje aspoň jeden ze dvou zadaných souborů. Tyto dva soubory jsou pracovní postup a soubor šablony.

        > [!IMPORTANT]
        > Ověřte, že se tyto dva soubory a jejich cesty shodují.
    - **úlohy**: spuštění pracovního postupu se skládá z jedné nebo více úloh. K dispozici je pouze jedna úloha s názvem **Deploy-Storage-Account-Template**.  Tato úloha má tři kroky:

        - **Vyjmutí zdrojového kódu**
        - **Přihlaste se k Azure**.

            > [!IMPORTANT]
            > Ověřte, že se název tajného klíče shoduje s tím, co jste uložili do úložiště. Viz [Konfigurace pověření nasazení](#configure-deployment-credentials).
        - **Nasaďte šablonu ARM**. Nahraďte hodnotu **resourceGroupName**.  Pokud jste použili skript Azure CLI v [přihlašovacích údajích konfigurace nasazení](#configure-deployment-credentials), vygenerovaný název skupiny prostředků je název projektu s připojenou **RG** . Ověřte hodnotu **templateLocation**.

1. Vyberte **Spustit potvrzení**.
1. Vyberte **potvrdit přímo do hlavní větve**.
1. Vyberte **Potvrdit nový soubor** (nebo **potvrďte změny**).

Vzhledem k tomu, že pracovní postup je nakonfigurován tak, aby se aktivoval buď pomocí souboru pracovního postupu, nebo aktualizovaného souboru šablony, pracovní postup se spustí hned po potvrzení změn.

## <a name="check-workflow-status"></a>Kontrolovat stav pracovního postupu

1. Vyberte kartu **Akce** . V seznamu se zobrazí pracovní postup **Vytvoření deployStorageAccount. yml** . Spuštění pracovního postupu trvá 1-2 minut.
1. Vyberte pracovní postup, který chcete otevřít.
1. V nabídce vlevo vyberte **nasadit-Storage-Account-Template** (název úlohy). Název úlohy je definovaný v pracovním postupu.
1. Vyberte **nasadit šablonu ARM** (název kroku) a rozbalte ji. Můžete zobrazit odpověď REST API.

## <a name="next-steps"></a>Další kroky

Podrobný kurz, který vás provede procesem vytvoření šablony, najdete v tématu [kurz: vytvoření a nasazení první šablony ARM](template-tutorial-create-first-template.md).

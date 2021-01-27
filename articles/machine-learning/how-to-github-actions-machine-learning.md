---
title: GitHub Actions pro CI/CD
titleSuffix: Azure Machine Learning
description: Přečtěte si, jak vytvořit pracovní postup akcí GitHubu pro výuku modelu na Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: juliakm
ms.author: jukullam
ms.date: 10/19/2020
ms.topic: conceptual
ms.custom: github-actions-azure
ms.openlocfilehash: f5abeee3b1e1e44f5ce0423fc7f8170644d74508
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880470"
---
# <a name="use-github-actions-with-azure-machine-learning"></a>Použití akcí GitHubu s Azure Machine Learning

Začněte s [akcemi GitHubu](https://docs.github.com/en/actions) , které vám pomohou naučit model na Azure Machine Learning. 

> [!NOTE]
> Akce GitHubu pro Azure Machine Learning jsou k dispozici tak, jak jsou, a společnost Microsoft je plně nepodporuje. Pokud narazíte na problémy s určitou akcí, otevřete problém pro akci v úložišti. Například pokud narazíte na problém s akcí AML-Deploy, nahlaste problém v [https://github.com/Azure/aml-deploy]( https://github.com/Azure/aml-deploy) úložišti.

## <a name="prerequisites"></a>Požadavky 

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Účet GitHub. Pokud ho ještě nemáte, zaregistrujte se [zdarma](https://github.com/join).  

## <a name="workflow-file-overview"></a>Přehled souboru pracovního postupu

Pracovní postup je definovaný souborem YAML (. yml) v `/.github/workflows/` cestě v úložišti. Tato definice obsahuje různé kroky a parametry, které tvoří pracovní postup.

Soubor obsahuje čtyři části:

|Sekce  |Úlohy  |
|---------|---------|
|**Authentication** | 1. Definujte instanční objekt. <br /> 2. Vytvořte tajný klíč GitHubu. |
|**Připojit** | 1. Připojte se k pracovnímu prostoru Machine Learning. <br /> 2. Připojte se k cíli výpočtů. |
|**Spustit** | 1. odešlete školicí běh. |
|**Nasazení** | 1. zaregistrujte model v registru Azure Machine Learning. 1. Nasazení modelu |

## <a name="create-repository"></a>Vytvořit úložiště

Vytvořte nové úložiště z [operace ml v rámci akce GitHubu a šablony Azure Machine Learning](https://github.com/machine-learning-apps/ml-template-azure). 

1. Otevřete [šablonu](https://github.com/machine-learning-apps/ml-template-azure) na GitHubu. 
2. Vyberte **Použít tuto šablonu**. 

    :::image type="content" source="media/how-to-github-actions-machine-learning/gh-actions-use-template.png" alt-text="Vyberte použít tuto šablonu.":::
3. Vytvoří nové úložiště ze šablony. Nastavte název úložiště na `ml-learning` nebo název podle vlastního výběru. 


## <a name="generate-deployment-credentials"></a>Generovat přihlašovací údaje nasazení

[Instanční objekt](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) můžete vytvořit pomocí příkazu [AZ AD SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true) v rozhraní příkazového [řádku Azure CLI](/cli/azure/). Spusťte tento příkaz s [Azure Cloud Shell](https://shell.azure.com/) v Azure Portal nebo vyberte tlačítko **vyzkoušet** .

```azurecli-interactive
az ad sp create-for-rbac --name "myML" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name> \
                            --sdk-auth
```

V předchozím příkladu Nahraďte zástupné symboly IDENTIFIKÁTORem vašeho předplatného, názvem skupiny prostředků a názvem aplikace. Výstupem je objekt JSON s přihlašovacími údaji přiřazení role, které poskytují přístup k vaší App Service aplikaci, která je podobná níže. Zkopírujte tento objekt JSON pro pozdější verzi.

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

## <a name="configure-the-github-secret"></a>Konfigurace tajného kódu GitHubu

1. V [GitHubu](https://github.com/)přejděte do úložiště, vyberte **Nastavení > tajných kódů > přidejte nový tajný kód**.

2. Do pole hodnota tajného klíče vložte celý výstup JSON z příkazu Azure CLI. Zadejte název tajného klíče `AZURE_CREDENTIALS` .

## <a name="connect-to-the-workspace"></a>Připojení k pracovnímu prostoru

Pomocí [akce pracovní prostor Azure Machine Learning](https://github.com/marketplace/actions/azure-machine-learning-workspace) se připojte k pracovnímu prostoru Azure Machine Learning. 

```yaml
    - name: Connect/Create Azure Machine Learning Workspace
      id: aml_workspace
      uses: Azure/aml-workspace@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
```

Ve výchozím nastavení akce očekává `workspace.json` soubor. Pokud má váš soubor JSON jiný název, můžete ho zadat `parameters_file` vstupním parametrem. Pokud soubor neexistuje, vytvoří se nový s názvem úložiště.


```yaml
    - name: Connect/Create Azure Machine Learning Workspace
      id: aml_workspace
      uses: Azure/aml-workspace@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          parameters_file: "alternate_workspace.json"
```
Akce zapíše vlastnosti Azure Resource Manager pracovního prostoru (ARM) do konfiguračního souboru, který bude vybrán všemi budoucími akcemi GitHubu Azure Machine Learning. Soubor je uložen do `GITHUB_WORKSPACE/aml_arm_config.json` . 

## <a name="connect-to-a-compute-target-in-azure-machine-learning"></a>Připojení k cíli výpočtů v Azure Machine Learning

Použijte [akci Azure Machine Learning COMPUTE](https://github.com/Azure/aml-compute) pro připojení k cíli výpočtů v Azure Machine Learning.  Pokud cíl výpočtů existuje, akce se k němu připojí. V opačném případě bude tato akce vytvářet nový cíl výpočtů. [Akce COMPUTE AML](https://github.com/Azure/aml-compute) podporuje jenom výpočetní cluster Azure ml a službu Azure Kubernetes Service (AKS). 

```yaml
    - name: Connect/Create Azure Machine Learning Compute Target
      id: aml_compute_training
      uses: Azure/aml-compute@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
```
## <a name="submit-training-run"></a>Odeslání trénovacího běhu

Pomocí [akce Azure Machine Learning školení](https://github.com/Azure/aml-run) můžete odeslat ScriptRun, Estimator nebo kanál, který se má Azure Machine Learning. 

```yaml
    - name: Submit training run
      id: aml_run
      uses: Azure/aml-run@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
```

## <a name="register-model-in-registry"></a>Registrovat model v registru

Použijte [akci modelu Azure Machine Learning registru](https://github.com/Azure/aml-registermodel) k registraci modelu pro Azure Machine Learning.

```yaml
    - name: Register model
      id: aml_registermodel
      uses: Azure/aml-registermodel@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          run_id:  ${{ steps.aml_run.outputs.run_id }}
          experiment_name: ${{ steps.aml_run.outputs.experiment_name }}
```

## <a name="deploy-model-to-azure-machine-learning-to-aci"></a>Nasazení modelu do Azure Machine Learning ACI

K nasazení modelu a vytvoření koncového bodu pro model použijte [akci nasazení Azure Machine Learning](https://github.com/Azure/aml-deploy) . K nasazení do služby Azure Kubernetes můžete použít taky nasazení Azure Machine Learning. Podívejte se na [Tento ukázkový pracovní postup](https://github.com/Azure-Samples/mlops-enterprise-template) pro model, který se nasazuje do služby Azure Kubernetes.

```yaml
    - name: Deploy model
      id: aml_deploy
      uses: Azure/aml-deploy@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          model_name:  ${{ steps.aml_registermodel.outputs.model_name }}
          model_version: ${{ steps.aml_registermodel.outputs.model_version }}

```

## <a name="complete-example"></a>Kompletní příklad

Proveďte výuku modelu a nasaďte ho do Azure Machine Learning. 

```yaml
# Actions train a model on Azure Machine Learning
name: Azure Machine Learning training and deployment
on:
  push:
    branches:
      - master
    # paths:
    #   - 'code/*'
jobs:
  train:
    runs-on: ubuntu-latest
    steps:
    # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
    - name: Check Out Repository
      id: checkout_repository
      uses: actions/checkout@v2
        
    # Connect or Create the Azure Machine Learning Workspace
    - name: Connect/Create Azure Machine Learning Workspace
      id: aml_workspace
      uses: Azure/aml-workspace@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
    
    # Connect or Create a Compute Target in Azure Machine Learning
    - name: Connect/Create Azure Machine Learning Compute Target
      id: aml_compute_training
      uses: Azure/aml-compute@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
    
    # Submit a training run to the Azure Machine Learning
    - name: Submit training run
      id: aml_run
      uses: Azure/aml-run@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}

    # Register model in Azure Machine Learning model registry
    - name: Register model
      id: aml_registermodel
      uses: Azure/aml-registermodel@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          run_id:  ${{ steps.aml_run.outputs.run_id }}
          experiment_name: ${{ steps.aml_run.outputs.experiment_name }}

    # Deploy model in Azure Machine Learning to ACI
    - name: Deploy model
      id: aml_deploy
      uses: Azure/aml-deploy@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          model_name:  ${{ steps.aml_registermodel.outputs.model_name }}
          model_version: ${{ steps.aml_registermodel.outputs.model_version }}

```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už vaše skupina prostředků a úložiště nepotřebujete, vyčistěte prostředky, které jste nasadili, odstraněním skupiny prostředků a úložiště GitHubu. 

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření a spuštění kanálů strojového učení s Azure Machine Learning SDK](./how-to-create-machine-learning-pipelines.md)
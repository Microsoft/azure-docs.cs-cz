---
title: Použití akcí GitHubu ke zpřístupnění aktualizací kódu v Azure Functions
description: Naučte se používat akce GitHubu k definování pracovního postupu pro sestavování a nasazování projektů Azure Functions v GitHubu.
author: ahmedelnably
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: aelnably
ms.openlocfilehash: 8e9e1189c3eb9de273926645ad0d4cfde5ba1c49
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2019
ms.locfileid: "71260043"
---
# <a name="continuous-delivery-by-using-github-action"></a>Průběžné doručování pomocí akce GitHubu

[Akce GitHubu](https://github.com/features/actions) vám umožní definovat pracovní postup, který automaticky vytvoří a nasadí kód Functions do aplikace Function App v Azure. 

> [!IMPORTANT]  
> Akce GitHubu jsou momentálně ve verzi beta. [Abyste se mohli připojit ke službě Preview](https://github.com/features/actions) pomocí svého účtu GitHubu, musíte se nejdřív zaregistrovat.

[Pracovní postup](https://help.github.com/articles/about-github-actions#workflow) v akcích GitHub je automatizovaný proces, který definujete v úložišti GitHub. Tento proces oznamuje GitHubu, jak sestavit a nasadit projekt Function App na GitHubu. 

Pracovní postup je definovaný souborem YAML (. yml) v `/.github/workflows/` cestě v úložišti. Tato definice obsahuje různé kroky a parametry, které tvoří pracovní postup. 

V případě pracovního postupu Azure Functions má soubor tři části: 

| Section | Úlohy |
| ------- | ----- |
| **Autentizace** | <ol><li>Definujte instanční objekt.</li><li>Vytvořte tajný klíč GitHubu.</li></ol>|  
| **Sestavení** | <ol><li>Nastavte prostředí.</li><li>Sestavte aplikaci Function App.</li></ol> |
| **Nasazení** | <ol><li>Nasaďte aplikaci Function App.</li></ol>| 

## <a name="create-a-service-principal"></a>Vytvoření instančního objektu

[Instanční objekt](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) můžete vytvořit pomocí příkazu [AZ AD SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) v rozhraní příkazového [řádku Azure CLI](/cli/azure/). Tento příkaz můžete spustit pomocí [Azure Cloud Shell](https://shell.azure.com) v Azure Portal nebo tak, že vyberete tlačítko **vyzkoušet** .

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.Web/sites/<APP_NAME> --sdk-auth
```

V tomto příkladu Nahraďte zástupné symboly v prostředku ID vašeho předplatného, skupiny prostředků a názvu aplikace Function App. Výstupem jsou přihlašovací údaje přiřazení role, které poskytují přístup k vaší aplikaci Function App. Zkopírujte tento objekt JSON, který můžete použít k ověření z GitHubu.

> [!IMPORTANT]
> Je vždy dobrým zvykem udělit minimální přístup. To je důvod, proč je obor v předchozím příkladu omezený na konkrétní aplikaci Function App, a ne na celou skupinu prostředků.

## <a name="configure-the-github-secret"></a>Konfigurace tajného kódu GitHubu

1. V [GitHubu](https://github.com)přejděte do úložiště, vyberte **Nastavení** > **tajné klíče** > **Přidat nový tajný kód**.

    ![Přidat tajný kód](media/functions-how-to-github-actions/add-secret.png)

1. Použijte `AZURE_CREDENTIALS` pro **název** a zkopírovaný výstup příkazu pro **hodnotu**a pak vyberte **Přidat tajný klíč**. 

GitHub se teď může ověřit pro vaši aplikaci Function App v Azure.

## <a name="set-up-the-environment"></a>Nastavení prostředí 

Nastavení prostředí je možné provést pomocí jedné z akcí nastavení publikování.

|Jazyk | Akce nastavení |
|---------|---------|
|**.NET**     | `actions/setup-dotnet` |
|**Java**    | `actions/setup-java` |
|**JavaScript**     | `actions/setup-node` |
|**Python**   | `actions/setup-python` |

Následující příklady znázorňují část pracovního postupu, která nastavuje prostředí pro různé podporované jazyky:

**JavaScript**

```yaml
    - name: 'Login via Azure CLI'
      uses: Azure/actions/login@master
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
```

**Python**

```yaml
    - name: 'Login via Azure CLI'
      uses: Azure/actions/login@master
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Python 3.6
      uses: actions/setup-python@v1
      with:
        python-version: 3.6
```

**.NET**

```yaml
    - name: 'Login via Azure CLI'
      uses: Azure/actions/login@master
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Dotnet 2.2.300
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: '2.2.300'
```

**Java**

```yaml
    - name: 'Login via Azure CLI'
      uses: Azure/actions/login@master
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        # If your pom.xml <maven.compiler.source> version is not in 1.8.x
        # Please change the Java version to match the version in pom.xml <maven.compiler.source>
        java-version: '1.8.x'
```

## <a name="build-the-function-app"></a>Sestavení aplikace Function App

To závisí na jazyku a jazycích podporovaných nástrojem Azure Functions, Tato část by měla být standardním postupem sestavení každého jazyka.

Následující příklady znázorňují část pracovního postupu, který vytváří aplikaci Function App v různých podporovaných jazycích.:

**JavaScript**

```yaml
    - name: 'Run npm'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please change your directory for npm in pushd
        pushd .
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
```

**Python**

```yaml
    - name: 'Run pip'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please change your directory for pip in pushd
        pushd .
        python -m pip install --upgrade pip
        pip install -r requirements.txt --target=".python_packages/lib/python3.6/site-packages"
        popd
```

**.NET**

```yaml
    - name: 'Run dotnet build'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please consider using pushd to change your path
        pushd .
        dotnet build --configuration Release --output ./output
        popd
```

**Java**

```yaml
    - name: 'Run mvn'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please change your directory for maven build in pushd
        pushd . ./POM_ARTIFACT_ID
        mvn clean package
        mvn azure-functions:package
        popd
```

## <a name="deploy-the-function-app"></a>Nasazení aplikace funkcí

K nasazení kódu do aplikace Function App budete muset použít `Azure/functions-action` akci. Tato akce má dva parametry:

|Parametr |Vysvětlení  |
|---------|---------|
|**_název aplikace_** | Závaznou Název vaší aplikace Function App |
|_**název slotu**_ | Volitelné Název [slotu nasazení](functions-deployment-slots.md) , na který chcete nasadit. Slot už musí být definovaný ve vaší aplikaci Function App. |


V následujícím příkladu je použita verze 1 z `functions-action`:

```yaml
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: PLEASE_REPLACE_THIS_WITH_YOUR_FUNCTION_APP_NAME
```

## <a name="next-steps"></a>Další kroky

Pokud chcete zobrazit úplný pracovní postup. yaml, podívejte se na jeden ze souborů na webu [Azure Actions Workflow Samples úložiště](https://github.com/Azure/actions-workflow-samples) , které mají `functionapp` název. Tyto ukázky můžete použít jako výchozí bod pro pracovní postup.

> [!div class="nextstepaction"]
> [Další informace o akcích GitHubu](https://help.github.com/en/articles/about-github-actions)

---
title: Použití akcí GitHubu ke zpřístupnění aktualizací kódu v Azure Functions
description: Naučte se používat akce GitHubu k definování pracovního postupu pro sestavování a nasazování projektů Azure Functions v GitHubu.
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/16/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 02f5399e89900a438fb94f973c497a54dc05cfee
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/14/2020
ms.locfileid: "88210172"
---
# <a name="continuous-delivery-by-using-github-action"></a>Průběžné doručování pomocí akce GitHubu

[Akce GitHubu](https://github.com/features/actions) vám umožní definovat pracovní postup, který automaticky vytvoří a nasadí kód Functions do aplikace Function App v Azure. 

[Pracovní postup](https://help.github.com/articles/about-github-actions#workflow) v akcích GitHub je automatizovaný proces, který definujete v úložišti GitHub. Tento proces oznamuje GitHubu, jak sestavit a nasadit projekt Function App na GitHubu. 

Pracovní postup je definovaný souborem YAML (. yml) v `/.github/workflows/` cestě v úložišti. Tato definice obsahuje různé kroky a parametry, které tvoří pracovní postup. 

V případě pracovního postupu Azure Functions má soubor tři části: 

| Sekce | Úlohy |
| ------- | ----- |
| **Authentication** | <ol><li>Definujte instanční objekt.</li><li>Stáhnout profil publikování.</li><li>Vytvořte tajný klíč GitHubu.</li></ol>|
| **Sestavení** | <ol><li>Nastavte prostředí.</li><li>Sestavte aplikaci Function App.</li></ol> |
| **Nasazení** | <ol><li>Nasaďte aplikaci Function App.</li></ol>|

> [!NOTE]
> Pokud se rozhodnete použít profil publikování pro ověřování, nemusíte vytvářet instanční objekt.

## <a name="create-a-service-principal"></a>Vytvoření instančního objektu

[Instanční objekt](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) můžete vytvořit pomocí příkazu [AZ AD SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) v rozhraní příkazového [řádku Azure CLI](/cli/azure/). Tento příkaz můžete spustit pomocí [Azure Cloud Shell](https://shell.azure.com) v Azure Portal nebo tak, že vyberete tlačítko **vyzkoušet** .

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.Web/sites/<APP_NAME> --sdk-auth
```

V tomto příkladu Nahraďte zástupné symboly v prostředku ID vašeho předplatného, skupiny prostředků a názvu aplikace Function App. Výstupem jsou přihlašovací údaje přiřazení role, které poskytují přístup k vaší aplikaci Function App. Zkopírujte tento objekt JSON, který můžete použít k ověření z GitHubu.

> [!IMPORTANT]
> Je vždy dobrým zvykem udělit minimální přístup. To je důvod, proč je obor v předchozím příkladu omezený na konkrétní aplikaci Function App, a ne na celou skupinu prostředků.

## <a name="download-the-publishing-profile"></a>Stáhnout profil publikování

Chcete-li stáhnout profil publikování aplikace Function App:

1. Vyberte stránku **Přehled** aplikace Function App a pak vyberte **získat profil publikování**.

   :::image type="content" source="media/functions-how-to-github-actions/get-publish-profile.png" alt-text="Stáhnout profil publikování":::

1. Uložte a zkopírujte obsah souboru nastavení publikování.

## <a name="configure-the-github-secret"></a>Konfigurace tajného kódu GitHubu

1. V [GitHubu](https://github.com)přejděte do úložiště, vyberte **Nastavení**  >  **tajné klíče**  >  **Přidat nový tajný kód**.

   :::image type="content" source="media/functions-how-to-github-actions/add-secret.png" alt-text="Přidat tajný kód":::

1. Přidejte nový tajný kód.

   * Pokud používáte instanční objekt, který jste vytvořili pomocí Azure CLI, použijte `AZURE_CREDENTIALS` **název**. Pak vložte zkopírovaný výstup objektu JSON pro **hodnotu**a vyberte **Přidat tajný klíč**.
   * Pokud používáte profil publikování, použijte `SCM_CREDENTIALS` pro **název**. Pak použijte obsah souboru profilu publikování pro **hodnotu**a vyberte **Přidat tajný kód**.

GitHub se teď může ověřit pro vaši aplikaci Function App v Azure.

## <a name="set-up-the-environment"></a>Nastavení prostředí 

Nastavení prostředí se provádí pomocí akce nastavení publikování pro konkrétní jazyk.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Následující příklad ukazuje část pracovního postupu, který používá `actions/setup-node` akci pro nastavení prostředí:

```yaml
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
```

# <a name="python"></a>[Python](#tab/python)

Následující příklad ukazuje část pracovního postupu, který používá `actions/setup-python` akci pro nastavení prostředí:

```yaml
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Python 3.6
      uses: actions/setup-python@v1
      with:
        python-version: 3.6
```

# <a name="c"></a>[C#](#tab/csharp)

Následující příklad ukazuje část pracovního postupu, který používá `actions/setup-dotnet` akci pro nastavení prostředí:

```yaml
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Dotnet 2.2.300
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: '2.2.300'
```

# <a name="java"></a>[Java](#tab/java)

Následující příklad ukazuje část pracovního postupu, který používá  `actions/setup-java` akci pro nastavení prostředí:

```yaml
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        # If your pom.xml <maven.compiler.source> version is not in 1.8.x
        # Please change the Java version to match the version in pom.xml <maven.compiler.source>
        java-version: '1.8.x'
```
---

## <a name="build-the-function-app"></a>Sestavení aplikace Function App

To závisí na jazyku a jazycích podporovaných nástrojem Azure Functions, Tato část by měla být standardním postupem sestavení každého jazyka.

Následující příklad ukazuje část pracovního postupu, který vytváří aplikaci Function App, která je specifická pro jazyk:

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

# <a name="python"></a>[Python](#tab/python)

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

# <a name="c"></a>[C#](#tab/csharp)

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

# <a name="java"></a>[Java](#tab/java)

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
---

## <a name="deploy-the-function-app"></a>Nasazení aplikace funkcí

K nasazení kódu do aplikace Function App budete muset použít `Azure/functions-action` akci. Tato akce má dva parametry:

|Parametr |Vysvětlení  |
|---------|---------|
|**_název aplikace_** | Závaznou Název vaší aplikace Function App |
|_**název slotu**_ | Volitelné Název [slotu nasazení](functions-deployment-slots.md) , na který chcete nasadit. Slot už musí být definovaný ve vaší aplikaci Function App. |


V následujícím příkladu je použita verze 1 z `functions-action` :

```yaml
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: PLEASE_REPLACE_THIS_WITH_YOUR_FUNCTION_APP_NAME
```

## <a name="next-steps"></a>Další kroky

Pokud chcete zobrazit úplný soubor Workflow. yaml, přečtěte si jeden ze souborů na webu [pracovní postup akcí Azure GitHubu](https://aka.ms/functions-actions-samples) , který má `functionapp` název. Tyto ukázky můžete použít jako výchozí bod pro pracovní postup.

> [!div class="nextstepaction"]
> [Další informace o GitHub Actions](https://help.github.com/en/articles/about-github-actions)

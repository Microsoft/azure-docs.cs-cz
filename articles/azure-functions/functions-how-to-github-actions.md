---
title: Použití akcí GitHubu ke zpřístupnění aktualizací kódu v Azure Functions
description: Naučte se používat akce GitHubu k definování pracovního postupu pro sestavování a nasazování projektů Azure Functions v GitHubu.
author: ahmedelnably
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: aelnably
ms.openlocfilehash: c34847577b7e83228fafad431f541497be9a21ae
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769145"
---
# <a name="continuous-delivery-by-using-github-action"></a>Průběžné doručování pomocí akce GitHubu

[Akce GitHubu](https://github.com/features/actions) vám umožní definovat pracovní postup, který automaticky vytvoří a nasadí kód Functions do aplikace Function App v Azure. 

[Pracovní postup](https://help.github.com/articles/about-github-actions#workflow) v akcích GitHub je automatizovaný proces, který definujete v úložišti GitHub. Tento proces oznamuje GitHubu, jak sestavit a nasadit projekt Function App na GitHubu. 

Pracovní postup je definovaný souborem YAML (. yml) v cestě `/.github/workflows/` ve vašem úložišti. Tato definice obsahuje různé kroky a parametry, které tvoří pracovní postup. 

V případě pracovního postupu Azure Functions má soubor tři části: 

| Sekce | Úlohy |
| ------- | ----- |
| **Ověřování** | <ol><li>Definujte instanční objekt.</li><li>Stáhnout profil publikování.</li><li>Vytvořte tajný klíč GitHubu.</li></ol>|
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

Profil publikování aplikace Function App si můžete stáhnout tak, že přejdete na stránku **Přehled** vaší aplikace a kliknete na **získat profil publikování**.

   ![Stáhnout profil publikování](media/functions-how-to-github-actions/get-publish-profile.png)

Zkopírujte obsah souboru.

## <a name="configure-the-github-secret"></a>Konfigurace tajného kódu GitHubu

1. V [GitHubu](https://github.com)přejděte do úložiště, vyberte **Nastavení** > **tajných** kódů > **přidejte nový tajný kód**.

   ![Přidat tajný kód](media/functions-how-to-github-actions/add-secret.png)

1. Přidejte nový tajný kód.

   * Pokud používáte instanční objekt, který jste vytvořili pomocí Azure CLI, použijte pro **název**`AZURE_CREDENTIALS`. Pak vložte zkopírovaný výstup objektu JSON pro **hodnotu**a vyberte **Přidat tajný klíč**.
   * Pokud používáte profil publikování, použijte pro **název**`SCM_CREDENTIALS`. Pak použijte obsah souboru profilu publikování pro **hodnotu**a vyberte **Přidat tajný kód**.

GitHub se teď může ověřit pro vaši aplikaci Function App v Azure.

## <a name="set-up-the-environment"></a>Nastavení prostředí 

Nastavení prostředí se provádí pomocí akce nastavení publikování pro konkrétní jazyk.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Následující příklad ukazuje část pracovního postupu, který používá akci `actions/setup-node` k nastavení prostředí:

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

# <a name="pythontabpython"></a>[Python](#tab/python)

Následující příklad ukazuje část pracovního postupu, který používá akci `actions/setup-python` k nastavení prostředí:

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

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Následující příklad ukazuje část pracovního postupu, který používá akci `actions/setup-dotnet` k nastavení prostředí:

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

# <a name="javatabjava"></a>[Java](#tab/java)

Následující příklad ukazuje část pracovního postupu, který používá akci `actions/setup-java` k nastavení prostředí:

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
---

## <a name="build-the-function-app"></a>Sestavení aplikace Function App

To závisí na jazyku a jazycích podporovaných nástrojem Azure Functions, Tato část by měla být standardním postupem sestavení každého jazyka.

Následující příklad ukazuje část pracovního postupu, který vytváří aplikaci Function App, která je specifická pro jazyk:

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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

# <a name="pythontabpython"></a>[Python](#tab/python)

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

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

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

# <a name="javatabjava"></a>[Java](#tab/java)

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

K nasazení kódu do aplikace Function App budete muset použít akci `Azure/functions-action`. Tato akce má dva parametry:

|Parametr |Vysvětlení  |
|---------|---------|
|**_název aplikace_** | Závaznou Název vaší aplikace Function App |
|_**název slotu**_ | Volitelné Název [slotu nasazení](functions-deployment-slots.md) , na který chcete nasadit. Slot už musí být definovaný ve vaší aplikaci Function App. |


V následujícím příkladu je použita verze 1 `functions-action`:

```yaml
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: PLEASE_REPLACE_THIS_WITH_YOUR_FUNCTION_APP_NAME
```

## <a name="next-steps"></a>Další kroky

Pokud chcete zobrazit kompletní pracovní postup. yaml, podívejte se na jeden ze souborů na webu [Azure Actions Workflow Samples](https://aka.ms/functions-actions-samples) , který má v názvu `functionapp`. Tyto ukázky můžete použít jako výchozí bod pro pracovní postup.

> [!div class="nextstepaction"]
> [Další informace o akcích GitHubu](https://help.github.com/en/articles/about-github-actions)

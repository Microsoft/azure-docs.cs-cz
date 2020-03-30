---
title: Provádění aktualizací kódu ve funkcích Azure pomocí akcí GitHubu
description: Zjistěte, jak pomocí akcí GitHubu definovat pracovní postup k vytváření a nasazování projektů Azure Functions na GitHubu.
author: craigshoemaker
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: cshoe
ms.openlocfilehash: dd74fd5c38e5a8800d2092afc1db1b412b126861
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77649904"
---
# <a name="continuous-delivery-by-using-github-action"></a>Průběžné doručování pomocí akce GitHub

[Akce GitHubu](https://github.com/features/actions) umožňují definovat pracovní postup pro automatické sestavení a nasazení kódu funkcí pro aplikaci Azure. 

V akcích GitHubu je [pracovní postup](https://help.github.com/articles/about-github-actions#workflow) automatizovaný proces, který definujete v úložišti GitHubu. Tento proces říká GitHubu, jak sestavit a nasadit projekt aplikace pro funkce na GitHubu. 

Pracovní postup je definován souborem YAML (.yml) v `/.github/workflows/` cestě v úložišti. Tato definice obsahuje různé kroky a parametry, které tvoří pracovní postup. 

Pro pracovní postup Azure Functions má soubor tři části: 

| Sekce | Úlohy |
| ------- | ----- |
| **Ověřování** | <ol><li>Definujte instanční objekt.</li><li>Stáhněte si profil publikování.</li><li>Vytvořte tajný klíč GitHubu.</li></ol>|
| **Sestavení** | <ol><li>Nastavte prostředí.</li><li>Vytvořte aplikaci funkcí.</li></ol> |
| **Nasadit** | <ol><li>Nasaďte aplikaci funkce.</li></ol>|

> [!NOTE]
> Pokud se rozhodnete použít profil publikování k ověřování, není nutné vytvářet instanční objekt.

## <a name="create-a-service-principal"></a>Vytvoření instančního objektu

[Instanční objekt](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) můžete vytvořit pomocí příkazu [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) v [příkazovém příkazu Azure CLI](/cli/azure/). Tento příkaz můžete spustit pomocí [Azure Cloud Shell](https://shell.azure.com) na webu Azure Portal nebo výběrem tlačítka Try **It.**

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.Web/sites/<APP_NAME> --sdk-auth
```

V tomto příkladu nahraďte zástupné symboly v prostředku ID předplatného, skupinou prostředků a názvem aplikace funkce. Výstup je pověření přiřazení role, která poskytuje přístup k aplikaci funkce. Zkopírujte tento objekt JSON, který můžete použít k ověření z GitHubu.

> [!IMPORTANT]
> Je vždy dobrým zvykem udělit minimální přístup. To je důvod, proč obor v předchozím příkladu je omezena na konkrétní funkce aplikace a ne celou skupinu prostředků.

## <a name="download-the-publishing-profile"></a>Stažení profilu publikování

Profil publikování aplikace funkce si můžete stáhnout tak, že přejdete na stránku **Přehled** aplikace a kliknete na **Získat profil publikování**.

   ![Stáhnout profil publikování](media/functions-how-to-github-actions/get-publish-profile.png)

Zkopírujte obsah souboru.

## <a name="configure-the-github-secret"></a>Konfigurace tajného klíče GitHubu

1. V [GitHubu](https://github.com)přejděte do svého úložiště a vyberte **Nastavení** > **Tajných kódů** > **Přidat nový tajný klíč**.

   ![Přidat tajný klíč](media/functions-how-to-github-actions/add-secret.png)

1. Přidejte nové tajemství.

   * Pokud používáte instanční objekt, který jste vytvořili `AZURE_CREDENTIALS` pomocí příkazového příkazu k webu Azure, použijte pro **název**. Potom vložte zkopírovaný výstup objektu JSON pro **hodnotu**a vyberte **Přidat tajný klíč**.
   * Pokud používáte profil publikování, `SCM_CREDENTIALS` použijte pro **název**. Potom použijte obsah souboru profilu publikování pro **hodnotu**a vyberte **Přidat tajný klíč**.

GitHub teď můžou ověřit vaši funkční aplikaci v Azure.

## <a name="set-up-the-environment"></a>Nastavení prostředí 

Nastavení prostředí se provádí pomocí akce nastavení publikování specifické pro jazyk.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Následující příklad ukazuje část pracovního postupu, která používá `actions/setup-node` akci k nastavení prostředí:

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

# <a name="python"></a>[Python](#tab/python)

Následující příklad ukazuje část pracovního postupu, která používá `actions/setup-python` akci k nastavení prostředí:

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

# <a name="c"></a>[C #](#tab/csharp)

Následující příklad ukazuje část pracovního postupu, která používá `actions/setup-dotnet` akci k nastavení prostředí:

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

# <a name="java"></a>[Java](#tab/java)

Následující příklad ukazuje část pracovního postupu, která používá `actions/setup-java` akci k nastavení prostředí:

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

## <a name="build-the-function-app"></a>Vytvoření aplikace funkcí

To závisí na jazyku a pro jazyky podporované funkce Azure, tato část by měla být standardní kroky sestavení každého jazyka.

Následující příklad ukazuje část pracovního postupu, která vytváří aplikaci funkce, která je specifická pro jazyk:

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

# <a name="c"></a>[C #](#tab/csharp)

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

Chcete-li nasadit kód do aplikace funkce, `Azure/functions-action` budete muset použít akci. Tato akce má dva parametry:

|Parametr |Vysvětlení  |
|---------|---------|
|**_název aplikace_** | (Povinné) Název aplikace funkce. |
|_**název slotu**_ | (Nepovinné) Název [slotu nasazení,](functions-deployment-slots.md) do kterého chcete nasadit. Slot musí být již definován ve vaší aplikaci funkce. |


Následující příklad používá verzi `functions-action`1 :

```yaml
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: PLEASE_REPLACE_THIS_WITH_YOUR_FUNCTION_APP_NAME
```

## <a name="next-steps"></a>Další kroky

Chcete-li zobrazit kompletní pracovní postup .yaml, podívejte se na jeden ze `functionapp` souborů v [ukázkách pracovního postupu Azure GitHub akce,](https://aka.ms/functions-actions-samples) které mají v názvu. Tyto ukázky můžete použít jako výchozí bod pro váš pracovní postup.

> [!div class="nextstepaction"]
> [Další informace o akcích GitHubu](https://help.github.com/en/articles/about-github-actions)

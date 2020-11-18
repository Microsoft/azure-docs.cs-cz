---
title: Použití GitHub Actions k nasazení statického webu do Azure Storage
description: Azure Storage statického hostování webu s akcemi GitHubu
author: juliakm
ms.service: storage
ms.topic: how-to
ms.author: jukullam
ms.reviewer: dineshm
ms.date: 09/11/2020
ms.subservice: blobs
ms.custom: devx-track-javascript, github-actions-azure, devx-track-azurecli
ms.openlocfilehash: 3adbc0a2d55a2adc2ab7c1f82b0a358542eefc2a
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/18/2020
ms.locfileid: "94842443"
---
# <a name="set-up-a-github-actions-workflow-to-deploy-your-static-website-in-azure-storage"></a>Nastavte pracovní postup akcí GitHubu pro nasazení statického webu v Azure Storage

Začněte s [akcemi GitHubu](https://docs.github.com/en/actions) pomocí pracovního postupu k nasazení statické lokality do účtu služby Azure Storage. Jakmile nastavíte pracovní postup akcí GitHubu, budete moct svůj web automaticky nasadit do Azure z GitHubu, když provedete změny v kódu vašeho webu.

> [!NOTE]
> Pokud používáte službu [Azure Static Web Apps](https://docs.microsoft.com/azure/static-web-apps/), nemusíte ručně nastavit pracovní postup akcí GitHubu.
> Statická Web Apps Azure pro vás automaticky vytvoří pracovní postup akcí GitHubu. 

## <a name="prerequisites"></a>Předpoklady

Předplatné Azure a účet GitHubu. 

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Úložiště GitHub s vaším statickým kódem webu Pokud nemáte účet GitHubu, [Zaregistrujte se zdarma](https://github.com/join).  
- Pracovní statický web hostovaný v Azure Storage. Naučte se [hostovat statický web v Azure Storage](storage-blob-static-website-how-to.md). Chcete-li postupovat podle tohoto příkladu, měli byste také nasadit [Azure CDN](static-website-content-delivery-network.md).

> [!NOTE]
> Je běžné používat síť Content Delivery Network (CDN) k omezení latence pro uživatele po celém světě a ke snížení počtu transakcí na účet úložiště. Nasazení statického obsahu do cloudové služby úložiště může snížit nutnost potenciálně nákladné výpočetní instance. Další informace najdete v tématu [model hostování statického obsahu](/azure/architecture/patterns/static-content-hosting).

## <a name="generate-deployment-credentials"></a>Generovat přihlašovací údaje nasazení

[Instanční objekt](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) můžete vytvořit pomocí příkazu [AZ AD SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true) v rozhraní příkazového [řádku Azure CLI](/cli/azure/). Spusťte tento příkaz s [Azure Cloud Shell](https://shell.azure.com/) v Azure Portal nebo vyberte tlačítko **vyzkoušet** .

Zástupný symbol nahraďte `myStaticSite` názvem vaší lokality hostované v Azure Storage. 

```azurecli-interactive
   az ad sp create-for-rbac --name {myStaticSite} --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} --sdk-auth
```

V předchozím příkladu Nahraďte zástupné symboly IDENTIFIKÁTORem vašeho předplatného a názvem skupiny prostředků. Výstupem je objekt JSON s přihlašovacími údaji přiřazení role, které poskytují přístup k vašemu účtu úložiště podobně jako v následujícím příkladu. Zkopírujte tento objekt JSON pro pozdější verzi.

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
> Je vždy dobrým zvykem udělit minimální přístup. Obor v předchozím příkladu je omezený na konkrétní App Service aplikaci, a ne na celou skupinu prostředků.

## <a name="configure-the-github-secret"></a>Konfigurace tajného kódu GitHubu

1. V [GitHubu](https://github.com/)přejděte do úložiště.

1. Vyberte **nastavení > tajných klíčů > nový tajný kód**.

1. Do pole hodnota tajného klíče vložte celý výstup JSON z příkazu Azure CLI. Zadejte název tajného kódu jako `AZURE_CREDENTIALS` .

    Když později nakonfigurujete soubor pracovního postupu, použijete tajný klíč pro vstup `creds` Akce přihlášení do Azure. Například:

    ```yaml
    - uses: azure/login@v1
    with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

## <a name="add-your-workflow"></a>Přidat pracovní postup

1. Přejít na **Akce** pro úložiště GitHub. 

    :::image type="content" source="media/storage-blob-static-website/storage-blob-github-actions-header.png" alt-text="Položka nabídky akce GitHubu":::

1. Vyberte **nastavit pracovní postup sami**. 

1. Odstraňte vše po `on:` části souboru pracovního postupu. Zbývající pracovní postup může vypadat například takto: 

    ```yaml
    name: CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]
    ```

1. Přejmenujte pracovní postup `Blob storage website CI` a přidejte akce rezervace a přihlášení. Tyto akce vyřadí kód lokality a ověří ho pomocí Azure pomocí `AZURE_CREDENTIALS` tajného klíče GitHubu, který jste vytvořili dříve. 

    ```yaml
    name: Blob storage website CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]

    jobs:
      build:
        runs-on: ubuntu-latest
        steps:            
        - uses: actions/checkout@v2
        - uses: azure/login@v1
          with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

1. Použijte akci Azure CLI a nahrajte svůj kód do úložiště objektů BLOB a vyprázdnit koncový bod CDN. V případě `az storage blob upload-batch` nahraďte zástupný text názvem svého účtu úložiště. Skript se nahraje do `$web` kontejneru. V případě `az cdn endpoint purge` Nahraďte zástupné symboly názvem vašeho profilu CDN, názvem koncového bodu CDN a skupinou prostředků.

    ```yaml
        - name: Upload to blob storage
        uses: azure/CLI@v1
        with:
            azcliversion: 2.0.72
            inlineScript: |
                az storage blob upload-batch --account-name <STORAGE_ACCOUNT_NAME> -d '$web' -s .
        - name: Purge CDN endpoint
        uses: azure/CLI@v1
        with:
            azcliversion: 2.0.72
            inlineScript: |
            az cdn endpoint purge --content-paths  "/*" --profile-name "CDN_PROFILE_NAME" --name "CDN_ENDPOINT" --resource-group "RESOURCE_GROUP"
    ``` 

1. Dokončete pracovní postup přidáním akce k odhlášení Azure. Toto je dokončený pracovní postup. Soubor se zobrazí ve `.github/workflows` složce vašeho úložiště.

    ```yaml
   name: Blob storage website CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]

    jobs:
    build:
        runs-on: ubuntu-latest
        steps:
        - uses: actions/checkout@v2
        - name: Azure Login
        uses: azure/login@v1
        with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}    
        - name: Azure CLI script
        uses: azure/CLI@v1
        with:
            azcliversion: 2.0.72
            inlineScript: |
                az storage blob upload-batch --account-name <STORAGE_ACCOUNT_NAME> -d '$web' -s .
        - name: Azure CLI script
        uses: azure/CLI@v1
        with:
            azcliversion: 2.0.72
            inlineScript: |
            az cdn endpoint purge --content-paths  "/*" --profile-name "CDN_PROFILE_NAME" --name "CDN_ENDPOINT" --resource-group "RESOURCE_GROUP"
            # Azure logout 
        - name: logout
          run: |
                az logout
    ```

## <a name="review-your-deployment"></a>Kontrola nasazení

1. Přejít na **Akce** pro úložiště GitHub. 

1. Otevřením prvního výsledku zobrazíte podrobné protokoly spuštění pracovního postupu. 
 
    :::image type="content" source="../media/index/github-actions-run.png" alt-text="Protokol spuštění akcí GitHubu":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

Když už svůj statický web a úložiště GitHubu nepotřebujete, vyčistěte prostředky, které jste nasadili, odstraněním skupiny prostředků a vašeho úložiště GitHub. 

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Seznamte se se službou Azure static Web Apps](https://docs.microsoft.com/azure/static-web-apps/)

---
title: Nasazení instance kontejneru pomocí akce GitHub
description: Konfigurace akce GitHubu, která automatizuje kroky k sestavení, nabízení a nasazování image kontejneru do instancí kontejneru Azure
ms.topic: article
ms.date: 03/18/2020
ms.custom: ''
ms.openlocfilehash: 13397cee8197afc65b93c587ae1505e59cfdebc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258035"
---
# <a name="configure-a-github-action-to-create-a-container-instance"></a>Konfigurace akce GitHubu pro vytvoření instance kontejneru

[Akce GitHubu](https://help.github.com/actions/getting-started-with-github-actions/about-github-actions) je sada funkcí v GitHubu pro automatizaci pracovních postupů vývoje softwaru na stejném místě, kde ukládáte kód a spolupracujete na žádostech o přijetí vlajek a problémech.

Pomocí akce [Deploy to Azure Container Instances](https://github.com/azure/aci-deploy) GitHub můžete automatizovat nasazení kontejneru do instancí kontejneru Azure. Akce umožňuje nastavit vlastnosti pro instanci kontejneru podobné těm v příkazu [az kontejneru vytvořit.][az-container-create]

Tento článek ukazuje, jak nastavit pracovní postup v úložišti GitHub, který provádí následující akce:

* Vytvoření bitové kopie z dockerového souboru
* Zasunutí bitové kopie do registru kontejnerů Azure
* Nasazení image kontejneru do instance kontejneru Azure

Tento článek ukazuje dva způsoby nastavení pracovního postupu:

* Nakonfigurujte pracovní postup sami v úložišti GitHub pomocí akce Nasadit do azure kontejneru a další akce.  
* Použijte `az container app up` příkaz v rozšíření [Nasazení do Azure](https://github.com/Azure/deploy-to-azure-cli-extension) v azure cli. Tento příkaz zjednodušuje vytváření pracovního postupu GitHubu a kroky nasazení.

> [!IMPORTANT]
> Akce GitHub pro instance kontejnerů Azure je aktuálně ve verzi preview. Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití][terms-of-use]. Některé aspekty této funkce se můžou před zveřejněním změnit.

## <a name="prerequisites"></a>Požadavky

* **Účet GitHub** – vytvořte si https://github.com účet, pokud ho ještě nemáte.
* **Azure CLI** – k dokončení kroků nastavení příkazového příkazu Azure můžete použít Azure Cloud Shell nebo místní instalaci azure cli. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].
* **Registr kontejnerů Azure** – pokud ho nemáte, vytvořte registr kontejnerů Azure na úrovni Basic pomocí [azure cli](../container-registry/container-registry-get-started-azure-cli.md), [portálu Azure](../container-registry/container-registry-get-started-portal.md)nebo jiných metod. Poznamenejte si skupinu prostředků použitou pro nasazení, která se používá pro pracovní postup GitHubu.

## <a name="set-up-repo"></a>Nastavení repo

* Příklady v tomto článku pomocí GitHubu roztáhněte následující úložiště:https://github.com/Azure-Samples/acr-build-helloworld-node

  Toto repo obsahuje Dockerfile a zdrojové soubory k vytvoření kontejneru image malé webové aplikace.

  ![Snímek obrazovky s tlačítkem forku (zvýrazněným) v GitHubu](../container-registry/media/container-registry-tutorial-quick-build/quick-build-01-fork.png)

* Ujistěte se, že akce je povolena pro vaše úložiště. Přejděte do rozteč úložiště a vyberte**Akce** **nastavení** > . V **části Akce oprávnění**, ujistěte se, že **povolit místní akce a akce třetích stran pro toto úložiště je vybrána.**

## <a name="configure-github-workflow"></a>Konfigurace pracovního postupu GitHubu

### <a name="create-service-principal-for-azure-authentication"></a>Vytvoření instančního objektu pro ověřování Azure

V pracovním postupu GitHubu je potřeba dodat přihlašovací údaje Azure k ověření na Azure CLI. Následující příklad vytvoří instanční objekt s rolí přispěvatele vymezenou pro skupinu prostředků pro registr kontejneru.

Nejprve získejte ID prostředku skupiny prostředků. Nahraďte název skupiny v následujícím příkazu [az group show:][az-acr-show]

```azurecli
groupId=$(az group show \
  --name <resource-group-name> \
  --query id --output tsv)
```

K vytvoření instančního objektu použijte [az ad sp create-for-rbac:][az-ad-sp-create-for-rbac]

```azurecli
az ad sp create-for-rbac \
  --scope $groupId \
  --role Contributor \
  --sdk-auth
```

Výstup se podobá tomuto:

```json
{
  "clientId": "xxxx6ddc-xxxx-xxxx-xxx-ef78a99dxxxx",
  "clientSecret": "xxxx79dc-xxxx-xxxx-xxxx-aaaaaec5xxxx",
  "subscriptionId": "xxxx251c-xxxx-xxxx-xxxx-bf99a306xxxx",
  "tenantId": "xxxx88bf-xxxx-xxxx-xxxx-2d7cd011xxxx",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "activeDirectoryGraphResourceId": "https://graph.windows.net/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

Uložte výstup JSON, protože se používá v pozdějším kroku. Všimněte si také `clientId`, které je třeba aktualizovat instanční objekt v další části.

### <a name="update-service-principal-for-registry-authentication"></a>Aktualizovat instanční objekt pro ověřování registru

Aktualizujte pověření primárního objektu služby Azure, abyste povolili oprávnění pro nabízenou a vyžádat v registru kontejneru. Tento krok umožňuje pracovnímu postupu GitHub použít instanční objekt k [ověření pomocí registru kontejneru](../container-registry/container-registry-auth-service-principal.md). 

Získejte ID prostředku registru kontejnerů. Nahraďte název registru v následujícím příkazu [az acr show:][az-acr-show]

```azurecli
registryId=$(az acr show \
  --name <registry-name> \
  --query id --output tsv)
```

Pomocí [přiřazení role az vytvořit][az-role-assignment-create] přiřadit roli AcrPush, která poskytuje push a pull přístup k registru. Nahraďte ID klienta instančního objektu:

```azurecli
az role assignment create \
  --assignee <ClientId> \
  --scope $registryId \
  --role AcrPush
```

### <a name="save-credentials-to-github-repo"></a>Uložení přihlašovacích údajů do úložiště GitHub

1. V uzpěřovacím rozhraní GitHubpřejděte do rozdvojeného úložiště a vyberte**Tajné kódy** **nastavení** > . 

1. Výběrem **možnosti Přidat nový tajný klíč** přidáte následující tajné klíče:

|Tajný kód  |Hodnota  |
|---------|---------|
|`AZURE_CREDENTIALS`     | Celý výstup JSON od vytvoření hlavního servisního objektu |
|`REGISTRY_LOGIN_SERVER`   | Název přihlašovacího serveru registru (všechna malá písmena). Příklad: *myregistry.azure.cr.io*        |
|`REGISTRY_USERNAME`     |  Výstup `clientId` z výstupu JSON z vytvoření hlavního servisního objektu       |
|`REGISTRY_PASSWORD`     |  Výstup `clientSecret` z výstupu JSON z vytvoření hlavního servisního objektu |
| `RESOURCE_GROUP` | Název skupiny prostředků, kterou jste použili k oboru instančního objektu. |

### <a name="create-workflow-file"></a>Vytvořit soubor pracovního postupu

1. V uzpěj githubu vyberte **Akce** > **nový pracovní postup**.
1. Vyberte **Nastavit pracovní postup sami**.
1. V **části Upravit nový soubor**vložte následující obsah YAML, abyste přepsali ukázkový kód. Přijměte `main.yml`výchozí název souboru nebo zadejte název souboru, který zvolíte.
1. Vyberte **Spustit potvrzení**, volitelně poskytněte krátký a rozšířený popis potvrzení a vyberte **Potvrdit nový soubor**.

```yml
on: [push]
name: Linux_Container_Workflow

jobs:
    build-and-deploy:
        runs-on: ubuntu-latest
        steps:
        # checkout the repo
        - name: 'Checkout GitHub Action'
          uses: actions/checkout@master
          
        - name: 'Login via Azure CLI'
          uses: azure/login@v1
          with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}
        
        - name: 'Build and push image'
          uses: azure/docker-login@v1
          with:
            login-server: ${{ secrets.REGISTRY_LOGIN_SERVER }}
            username: ${{ secrets.REGISTRY_USERNAME }}
            password: ${{ secrets.REGISTRY_PASSWORD }}
        - run: |
            docker build . -t ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}
            docker push ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}

        - name: 'Deploy to Azure Container Instances'
          uses: 'azure/aci-deploy@v1'
          with:
            resource-group: ${{ secrets.RESOURCE_GROUP }}
            dns-name-label: ${{ secrets.RESOURCE_GROUP }}${{ github.run_number }}
            image: ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}
            registry-login-server: ${{ secrets.REGISTRY_LOGIN_SERVER }}
            registry-username: ${{ secrets.REGISTRY_USERNAME }}
            registry-password: ${{ secrets.REGISTRY_PASSWORD }}
            name: aci-sampleapp
            location: 'west us'
```

### <a name="validate-workflow"></a>Ověřit pracovní postup

Po potvrzení souboru pracovního postupu se aktivuje pracovní postup. Chcete-li zkontrolovat průběh pracovního postupu, přejděte do**oblasti Pracovní postupy** **akcí** > . 

![Zobrazit průběh pracovního postupu](./media/container-instances-github-action/github-action-progress.png)

Informace o zobrazení stavu a výsledků jednotlivých kroků pracovního postupu najdete v [tématu Správa spuštění pracovního postupu.](https://help.github.com/actions/configuring-and-managing-workflows/managing-a-workflow-run)

Po dokončení pracovního postupu získáte informace o instanci kontejneru s názvem *aci-sampleapp* spuštěním příkazu [az container show.][az-container-show] Nahraďte název skupiny prostředků: 

```azurecli
az container show \
  --resource-group <resource-group-name> \
  --name aci-sampleapp \
  --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" \
  --output table
```

Výstup se podobá tomuto:

```console
FQDN                                   ProvisioningState
---------------------------------      -------------------
aci-action01.westus.azurecontainer.io  Succeeded
```

Po zřízení instance přejděte do hlavního název stránky kontejneru v prohlížeči a zobrazte spuštěnou webovou aplikaci.

![Spuštění webové aplikace v prohlížeči](./media/container-instances-github-action/github-action-container.png)

## <a name="use-deploy-to-azure-extension"></a>Použití rozšíření Nasazení do Azure

Případně můžete ke konfiguraci pracovního postupu použít [rozšíření Deploy to Azure](https://github.com/Azure/deploy-to-azure-cli-extension) v rozhraní příkazového příkazu Konfiguraci Azure. Příkaz `az container app up` v rozšíření přebírá vstupní parametry od vás k nastavení pracovního postupu pro nasazení do Azure Container Instances. 

Pracovní postup vytvořený rozhraním příkazového příkazu Azure je podobný pracovnímu postupu, který můžete [vytvořit ručně pomocí GitHubu](#configure-github-workflow).

### <a name="additional-prerequisite"></a>Další předpoklad

Kromě [předpokladů](#prerequisites) a [nastavení repo](#set-up-repo) pro tento scénář je potřeba nainstalovat **rozšíření Nasazení do Azure** pro Azure CLI.

Spusťte příkaz [add extension az][az-extension-add] pro instalaci rozšíření:

```azurecli
az extension add \
  --name deploy-to-azure
```

Informace o hledání, instalaci a správě rozšíření najdete v tématu [Použití rozšíření s Azure CLI](/cli/azure/azure-cli-extensions-overview).

### <a name="run-az-container-app-up"></a>Spusťte `az container app up`.

Chcete-li spustit příkaz [aplikace az kontejneru][az-container-app-up] nahoru, zadejte minimálně:

* Název registru kontejnerů Azure, například *myregistry*
* Adresa URL úložiště GitHub, například`https://github.com/<your-GitHub-Id>/acr-build-helloworld-node`

Ukázkový příkaz:

```azurecli
az container app up \
  --acr myregistry \
  --repository https://github.com/myID/acr-build-helloworld-node
```

### <a name="command-progress"></a>Průběh příkazu

* Po zobrazení výzvy zadejte přihlašovací údaje githubu nebo zadejte [token osobního přístupu GitHubu](https://help.github.com/github/authenticating-to-github/creating-a-personal-access-token-for-the-command-line) (PAT), který má *úložiště* a *uživatelské* obory k ověření pomocí registru. Pokud zadáte přihlašovací údaje GitHub, příkaz vytvoří PAT pro vás.

* Příkaz vytvoří klíče pro repo pracovního postupu:

  * Pověření hlavního povinného kontinuu azure
  * Přihlašovací údaje pro přístup k registru kontejnerů Azure

* Po potvrzení souboru pracovního postupu do repo se aktivuje pracovní postup. 

Výstup se podobá tomuto:

```console
[...]
Checking in file github/workflows/main.yml in the Github repository myid/acr-build-helloworld-node
Creating workflow...
GitHub Action Workflow has been created - https://github.com/myid/acr-build-helloworld-node/runs/515192398
GitHub workflow completed.
Workflow succeeded
Your app is deployed at:  http://acr-build-helloworld-node.eastus.azurecontainer.io:8080/
```

### <a name="validate-workflow"></a>Ověřit pracovní postup

Pracovní postup nasazuje instanci kontejneru Azure se základním názvem úložiště GitHub, v tomto případě *acr-build-helloworld-node*. V prohlížeči můžete přejít na odkaz, který je k dispozici, a zobrazit tak spuštěnou webovou aplikaci. Pokud vaše aplikace naslouchá na jiném portu než 8080, zadejte to místo toho v adrese URL.

Pokud chcete zobrazit stav pracovního postupu a výsledky jednotlivých kroků v rozhraní GitHub, přečtěte si část [Správa spuštění pracovního postupu](https://help.github.com/actions/configuring-and-managing-workflows/managing-a-workflow-run).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Instanci kontejneru zastavte pomocí příkazu [az container delete][az-container-delete]:

```azurecli
az container delete \
  --name <instance-name>
  --resource-group <resource-group-name>
```

Chcete-li odstranit skupinu prostředků a všechny prostředky v ní, spusťte příkaz [delete skupiny az:][az-group-delete]

```azurecli
az group delete \
  --name <resource-group-name>
```

## <a name="next-steps"></a>Další kroky

Další akce pro automatizaci pracovního postupu vývoje najdete na [webu GitHub Marketplace.](https://github.com/marketplace?type=actions)


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->

[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-show]: /cli/azure/group#az-group-show
[az-group-delete]: /cli/azure/group#az-group-delete
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-container-create]: /cli/azure/container#az-container-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-container-show]: /cli/azure/container#az-container-show
[az-container-delete]: /cli/azure/container#az-container-delete
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-container-app-up]: /cli/azure/ext/deploy-to-azure/container/app#ext-deploy-to-azure-az-container-app-up

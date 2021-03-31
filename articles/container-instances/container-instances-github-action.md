---
title: Akce nasazení instance kontejneru podle GitHubu
description: Nakonfigurujte akci GitHubu, která automatizuje kroky k sestavení, vložení a nasazení image kontejneru pro Azure Container Instances
ms.topic: article
ms.date: 08/20/2020
ms.custom: github-actions-azure, devx-track-azurecli
ms.openlocfilehash: 1409d8fc1430cd9bf67bd735d9826a74979d495b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98762963"
---
# <a name="configure-a-github-action-to-create-a-container-instance"></a>Konfigurace akce GitHubu pro vytvoření instance kontejneru

[Akce GitHubu](https://docs.github.com/en/actions) je sada funkcí v GitHubu pro automatizaci pracovních postupů vývoje softwaru na stejném místě, kam ukládáte kód a spolupracujete na žádostech o přijetí změn a problémech.

K automatizaci nasazení jednoho kontejneru do Azure Container Instances použijte akci [nasadit do Azure Container Instances](https://github.com/azure/aci-deploy) GitHubu. Akce umožňuje nastavit vlastnosti pro instanci kontejneru podobným způsobem jako v příkazu [AZ Container Create][az-container-create] .

Tento článek popisuje, jak nastavit pracovní postup v úložišti GitHub, který provádí následující akce:

* Vytvoření image z souboru Dockerfile
* Vložení image do služby Azure Container Registry
* Nasazení image kontejneru do instance kontejneru Azure

Tento článek ukazuje dva způsoby, jak nastavit pracovní postup:

* [Konfigurace pracovního postupu GitHubu](#configure-github-workflow) – vytvoření pracovního postupu v úložišti GitHub pomocí akce nasadit pro Azure Container Instances akci a další akce.  
* [Použijte rozšíření CLI](#use-deploy-to-azure-extension) – použijte `az container app up` příkaz v rozšíření [nasazení do Azure](https://github.com/Azure/deploy-to-azure-cli-extension) v rozhraní příkazového řádku Azure CLI. Tento příkaz zjednodušuje vytváření pracovního postupu GitHubu a kroků nasazení.

> [!IMPORTANT]
> Akce GitHub pro Azure Container Instances je momentálně ve verzi Preview. Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití][terms-of-use]. Některé aspekty této funkce se můžou před zveřejněním změnit.

## <a name="prerequisites"></a>Požadavky

* **Účet GitHub** – vytvořte účet https://github.com , pokud ho ještě nemáte.
* **Azure CLI** – k dokončení kroků rozhraní PŘÍKAZového řádku Azure můžete použít Azure Cloud Shell nebo místní instalaci rozhraní PŘÍKAZového řádku Azure. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].
* **Azure Container Registry** – Pokud ho ještě nemáte, vytvořte službu Azure Container Registry na úrovni Basic pomocí rozhraní příkazového [řádku Azure](../container-registry/container-registry-get-started-azure-cli.md), [Azure Portal](../container-registry/container-registry-get-started-portal.md)nebo jiných metod. Poznamenejte si skupinu prostředků použitou pro nasazení, která se používá pro pracovní postup GitHubu.

## <a name="set-up-repo"></a>Nastavení úložiště

* V příkladech v tomto článku použijte GitHub pro rozvětvení následujícího úložiště: https://github.com/Azure-Samples/acr-build-helloworld-node

  Toto úložiště obsahuje souboru Dockerfile a zdrojové soubory pro vytvoření image kontejneru malé webové aplikace.

  ![Snímek obrazovky s tlačítkem forku (zvýrazněným) v GitHubu](../container-registry/media/container-registry-tutorial-quick-build/quick-build-01-fork.png)

* Ujistěte se, že jsou pro vaše úložiště povolené akce. Přejděte do rozvětvené úložiště a vyberte   >  **Akce** nastavení. V **oprávnění akce** zajistěte, aby byla vybrána **možnost Povolit místní akce a akci třetích stran pro toto úložiště** .

## <a name="configure-github-workflow"></a>Konfigurace pracovního postupu GitHubu

### <a name="create-service-principal-for-azure-authentication"></a>Vytvoření instančního objektu pro ověřování Azure

V pracovním postupu GitHubu je potřeba zadat přihlašovací údaje Azure pro ověření v Azure CLI. Následující příklad vytvoří instanční objekt s rolí přispěvatele vymezenou pro skupinu prostředků vašeho registru kontejneru.

Nejprve Získejte ID prostředku vaší skupiny prostředků. Název skupiny nahraďte následujícím příkazem [AZ Group show][az-group-show] :

```azurecli
groupId=$(az group show \
  --name <resource-group-name> \
  --query id --output tsv)
```

K vytvoření instančního objektu použijte [příkaz AZ AD SP Create-for-RBAC][az-ad-sp-create-for-rbac] :

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

Výstup JSON uložte, protože se používá v pozdějším kroku. Všimněte si také, že je `clientId` potřeba aktualizovat instanční objekt v další části.

### <a name="update-service-principal-for-registry-authentication"></a>Aktualizace instančního objektu pro ověřování registru

Aktualizujte přihlašovací údaje instančního objektu tak, aby umožňovaly přístup push a pull do vašeho registru kontejneru. Tento krok umožňuje pracovnímu postupu GitHubu používat instanční objekt k [ověřování pomocí registru kontejnerů](../container-registry/container-registry-auth-service-principal.md) a nasdílení a stažení Image Docker. 

Získejte ID prostředku vašeho registru kontejneru. Název registru nahraďte následujícím příkazem [AZ ACR show][az-acr-show] :

```azurecli
registryId=$(az acr show \
  --name <registry-name> \
  --query id --output tsv)
```

Pomocí [AZ role Assignment Create][az-role-assignment-create] můžete přiřadit roli AcrPush, která umožňuje přístup k registru push a Pull. Nahraďte ID klienta objektu služby:

```azurecli
az role assignment create \
  --assignee <ClientId> \
  --scope $registryId \
  --role AcrPush
```

### <a name="save-credentials-to-github-repo"></a>Uložení přihlašovacích údajů do úložiště GitHub

1. V uživatelském rozhraní GitHubu přejděte do rozvětvené úložiště a vyberte **Nastavení**  >  **tajné klíče**. 

1. Chcete-li přidat následující tajné klíče, vyberte možnost **Přidat nový tajný kód** :

|Tajný kód  |Hodnota  |
|---------|---------|
|`AZURE_CREDENTIALS`     | Celý výstup JSON z kroku pro vytvoření instančního objektu |
|`REGISTRY_LOGIN_SERVER`   | Název přihlašovacího serveru vašeho registru (bez malých písmen). Příklad: *myregistry.azurecr.IO*        |
|`REGISTRY_USERNAME`     |  `clientId`Z výstupu JSON z vytváření instančního objektu       |
|`REGISTRY_PASSWORD`     |  `clientSecret`Z výstupu JSON z vytváření instančního objektu |
| `RESOURCE_GROUP` | Název skupiny prostředků, kterou jste použili k určení oboru instančního objektu |

### <a name="create-workflow-file"></a>Vytvořit soubor pracovního postupu

1. V uživatelském rozhraní GitHubu vyberte **Akce**  >  **nový pracovní postup**.
1. Vyberte **nastavit pracovní postup sami**.
1. V části **Upravit nový soubor** vložte následující obsah YAML k přepsání ukázkového kódu. Přijměte výchozí název souboru `main.yml` nebo zadejte název souboru, který zvolíte.
1. Vyberte možnost **Spustit potvrzení**, volitelně zadejte krátké a rozšířené popisy potvrzení a vyberte **Potvrdit nový soubor**.

```yml
on: [push]
name: Linux_Container_Workflow

jobs:
    build-and-deploy:
        runs-on: ubuntu-latest
        steps:
        # checkout the repo
        - name: 'Checkout GitHub Action'
          uses: actions/checkout@main
          
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

Po potvrzení souboru pracovního postupu se spustí pracovní postup. Pokud chcete zkontrolovat průběh pracovního postupu, přejděte na **Akce**  >  **pracovní postupy**. 

![Zobrazit průběh pracovního postupu](./media/container-instances-github-action/github-action-progress.png)

Informace o zobrazení stavu a výsledků jednotlivých kroků v pracovním postupu najdete v tématu [zobrazení historie spuštění pracovního postupu](https://docs.github.com/en/actions/managing-workflow-runs/viewing-workflow-run-history) . Pokud pracovní postup nedokončíte, přečtěte si téma [zobrazení protokolů pro diagnostiku selhání](https://docs.github.com/en/actions/managing-workflow-runs/using-workflow-run-logs#viewing-logs-to-diagnose-failures).

Po úspěšném dokončení pracovního postupu Získejte informace o instanci kontejneru s názvem *ACI-dotazů* spuštěním příkazu [AZ Container show][az-container-show] . Nahraďte název vaší skupiny prostředků: 

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

Po zřízení instance přejděte do plně kvalifikovaného názvu domény kontejneru v prohlížeči, abyste viděli běžící webovou aplikaci.

![Spuštění webové aplikace v prohlížeči](./media/container-instances-github-action/github-action-container.png)

## <a name="use-deploy-to-azure-extension"></a>Použití nasazení do rozšíření Azure

Případně můžete nakonfigurovat pracovní postup pomocí [rozšíření nasazení do Azure](https://github.com/Azure/deploy-to-azure-cli-extension) v rozhraní příkazového řádku Azure CLI. `az container app up`Příkaz v rozšíření přebírá vstupní parametry od vás, abyste mohli nastavit pracovní postup pro nasazení na Azure Container Instances. 

Pracovní postup vytvořený pomocí Azure CLI je podobný pracovnímu postupu, který můžete [vytvořit ručně pomocí GitHubu](#configure-github-workflow).

### <a name="additional-prerequisite"></a>Další požadavky

Kromě [požadavků](#prerequisites) a [Nastavení úložiště](#set-up-repo) pro tento scénář musíte nainstalovat  **rozšíření nasazení do Azure** pro rozhraní příkazového řádku Azure CLI.

Pro instalaci rozšíření spusťte příkaz [AZ Extension Add][az-extension-add] .

```azurecli
az extension add \
  --name deploy-to-azure
```

Informace o hledání, instalaci a správě rozšíření najdete v tématu [použití rozšíření pomocí Azure CLI](/cli/azure/azure-cli-extensions-overview).

### <a name="run-az-container-app-up"></a>Spuštěním příkazu `az container app up`

Pokud chcete spustit příkaz [AZ Container App up][az-container-app-up] , zadejte minimálně:

* Název vašeho registru služby Azure Container Registry, například *myregistry*
* Adresa URL vašeho úložiště GitHubu, například `https://github.com/<your-GitHub-Id>/acr-build-helloworld-node`

Vzorový příkaz:

```azurecli
az container app up \
  --acr myregistry \
  --repository https://github.com/myID/acr-build-helloworld-node
```

### <a name="command-progress"></a>Průběh příkazu

* Po zobrazení výzvy zadejte svoje přihlašovací údaje k GitHubu nebo poskytněte [token pro GitHubu](https://docs.github.com/en/github/authenticating-to-github/creating-a-personal-access-token) , který má *úložiště* a *uživatelské* obory k ověření s vaším účtem GitHubu. Pokud zadáte přihlašovací údaje GitHubu, příkaz vytvoří PAT za vás. Postupujte podle dalších výzev a nakonfigurujte pracovní postup.

* Příkaz vytvoří tajné klíče úložiště pro pracovní postup:

  * Přihlašovací údaje instančního objektu pro rozhraní příkazového řádku Azure
  * Přihlašovací údaje pro přístup ke službě Azure Container Registry

* Po potvrzení souboru pracovního postupu příkazem do úložiště se spustí pracovní postup. 

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

Pokud chcete zobrazit stav pracovního postupu a výsledky jednotlivých kroků v uživatelském rozhraní GitHubu, přečtěte si téma [zobrazení historie spuštění pracovního postupu](https://docs.github.com/en/actions/managing-workflow-runs/viewing-workflow-run-history).

### <a name="validate-workflow"></a>Ověřit pracovní postup

Pracovní postup nasadí instanci kontejneru Azure se základním názvem vašeho úložiště GitHub, v tomto případě *ACR-Build-HelloWorld-Node*. Po úspěšném dokončení pracovního postupu Získejte informace o instanci kontejneru s názvem *ACR-Build-HelloWorld-Node* spuštěním příkazu [AZ Container show][az-container-show] . Nahraďte název vaší skupiny prostředků: 

```azurecli
az container show \
  --resource-group <resource-group-name> \
  --name acr-build-helloworld-node \
  --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" \
  --output table
```

Výstup se podobá tomuto:

```console
FQDN                                                   ProvisioningState
---------------------------------                      -------------------
acr-build-helloworld-node.westus.azurecontainer.io     Succeeded
```

Po zřízení instance přejděte do plně kvalifikovaného názvu domény kontejneru v prohlížeči, abyste viděli běžící webovou aplikaci.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Instanci kontejneru zastavte pomocí příkazu [az container delete][az-container-delete]:

```azurecli
az container delete \
  --name <instance-name>
  --resource-group <resource-group-name>
```

Pokud chcete odstranit skupinu prostředků a všechny její prostředky, spusťte příkaz [AZ Group Delete][az-group-delete] :

```azurecli
az group delete \
  --name <resource-group-name>
```

## <a name="next-steps"></a>Další kroky

Projděte si [Web GitHub Marketplace](https://github.com/marketplace?type=actions) , kde najdete další akce pro automatizaci pracovního postupu vývoje.


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

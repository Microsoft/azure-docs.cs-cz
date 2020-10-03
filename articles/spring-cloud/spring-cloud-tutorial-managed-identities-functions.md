---
title: Kurz – spravovaná identita k vyvolání Azure Functions z aplikace založené na jarním cloudu Azure
description: Použití spravované identity k vyvolání Azure Functions z aplikace Azure Spring Cloud
author: MarkGardner
ms.author: margard
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 07/10/2020
ms.openlocfilehash: 44268bf1b7805ece8de4a3499a7d53fc851af142
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2020
ms.locfileid: "91664983"
---
# <a name="tutorial-use-a-managed-identity-to-invoke-azure-functions-from-an-azure-spring-cloud-app"></a>Kurz: použití spravované identity k vyvolání Azure Functions ze jarní cloudové aplikace Azure

V tomto článku se dozvíte, jak vytvořit spravovanou identitu pro aplikaci Azure jaře Cloud a použít ji k vyvolání funkcí aktivovaných protokolem HTTP.

Azure Functions i App Services mají integrovanou podporu ověřování Azure Active Directory (Azure AD). Díky použití této integrované funkce ověřování společně se spravovanými identitami pro jarní cloud Azure můžeme vyvolávat služby RESTful s využitím moderní sémantiky OAuth. Tato metoda nevyžaduje uložení tajných kódů v kódu a poskytuje podrobnější ovládací prvky pro řízení přístupu k externím prostředkům. 


## <a name="prerequisites"></a>Požadované součásti

* [Registrace předplatného Azure](https://azure.microsoft.com/free/)
* [Instalace rozhraní příkazového řádku Azure CLI 2.0.67 nebo vyšší verze](https://docs.microsoft.com/cli/azure/install-azure-cli)
* [Nainstalujte Maven 3,0 nebo novější.](https://maven.apache.org/download.cgi)
* [Nainstalovat verzi Azure Functions Core Tools 3.0.2009 nebo novější](https://docs.microsoft.com/azure/azure-functions/functions-run-local#install-the-azure-functions-core-tools)


## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků
Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Pomocí příkazu [AZ Group Create vytvořte](/cli/azure/group#az-group-create)skupinu prostředků, která bude obsahovat aplikaci Function App i jarní Cloud.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```


## <a name="create-a-function-app"></a>Vytvoření aplikace funkcí
Pokud chcete vytvořit aplikaci Function App, musíte nejdřív vytvořit účet záložního úložiště, a to pomocí příkazu [AZ Storage Account Create](/cli/azure/storage/account#az-storage-account-create):

> [!Important]
> Každá aplikace Function App a účet úložiště musí mít jedinečný název. V následujících příkladech nahraďte <functionapp-Name> názvem vaší aplikace Function App a <Your-storageaccount-Name> názvem vašeho účtu úložiště.

```azurecli-interactive
az storage account create --name <your-storageaccount-name> --resource-group myResourceGroup --location eastus --sku Standard_LRS
```

Po vytvoření účtu úložiště můžete vytvořit aplikaci Function App.

```azurecli-interactive
az functionapp create --name <your-functionapp-name> --resource-group myResourceGroup --consumption-plan-location eastus --os-type windows --runtime node --storage-account <your-storageaccount-name> --functions-version 3
```

Poznamenejte si vrácené **názvy hostitelů**, které budou ve formátu "https://<Your-functionapp-Name>. azurewebsites.NET". Použije se v následujícím kroku.


## <a name="enable-azure-active-directory-authentication"></a>Povolit ověřování Azure Active Directory

Přistoupit k nově vytvořené aplikaci Function App z [Azure Portal](https://portal.azure.com) a v nabídce nastavení vyberte ověřování/autorizace. Povolte App Service ověřování a nastavte "akce, která se má provést, když se žádost neověřuje" na "přihlášení pomocí Azure Active Directory". Toto nastavení zajistí, že všechny neověřené požadavky budou odepřeny (odpověď 401).

![Nastavení ověřování, které zobrazuje Azure Active Directory jako výchozí zprostředkovatel](media/spring-cloud-tutorial-managed-identities-functions/function-auth-config-1.jpg)

V části zprostředkovatelé ověřování vyberte Azure Active Directory a nakonfigurujte registraci aplikace. Po výběru režimu expresní správy se v tenantovi Azure AD automaticky vytvoří registrace aplikace se správnou konfigurací.

![Zprostředkovatel Azure Active Directory nastavený na režim expresní správy](media/spring-cloud-tutorial-managed-identities-functions/function-auth-config-2.jpg)

Jakmile nastavení uložíte, aplikace Function App se restartuje a všechny následné požadavky budou vyzváné k přihlášení prostřednictvím služby Azure AD. To, jestli se neověřené požadavky mají nyní odmítat, můžete otestovat tak, že přejdete na kořenovou adresu URL aplikace Function App (vrácený ve výstupu **názvů hostitelů** v kroku výše). Měli byste se přesměrovat na přihlašovací obrazovku vaší organizace Azure AD.


## <a name="create-an-http-triggered-function"></a>Vytvoření funkce aktivované protokolem HTTP

V prázdném místním adresáři vytvořte novou aplikaci Function App a přidejte funkci aktivovanou protokolem HTTP.

```console
func init --worker-runtime node
func new --template HttpTrigger --name HttpTrigger
```

Ve výchozím nastavení používají funkce k zabezpečení koncových bodů HTTP ověřování na základě klíčů. Vzhledem k tomu, že povolíme ověřování Azure AD pro zabezpečení přístupu k funkcím, chceme [nastavit úroveň ověřování funkce na anonymní](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook-trigger#secure-an-http-endpoint-in-production).

```json function.json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      ...
    }
  ]
}
```

Aplikaci je teď možné publikovat do instance [aplikace Function App](#create-a-function-app) vytvořené v předchozím kroku.

```console
func azure functionapp publish <your-functionapp-name>
```

Výstup příkazu Publikovat by měl vypsat adresu URL vaší nově vytvořené funkce.

```output
Deployment completed successfully.
Syncing triggers...
Functions in <your-functionapp-name>:
    HttpTrigger - [httpTrigger]
        Invoke url: https://<your-functionapp-name>.azurewebsites.net/api/httptrigger
```


## <a name="create-azure-spring-cloud-service-and-app"></a>Vytvoření cloudové služby a aplikace Azure na jaře
Po instalaci jarního cloudového rozšíření vytvořte pomocí příkazu Azure CLI instanci cloudu Azure jaře `az spring-cloud create` . 

```azurecli-interactive
az extension add --name spring-cloud
az spring-cloud create --name mymsispringcloud --resource-group myResourceGroup --location eastus
```

Následující příklad vytvoří aplikaci s názvem `msiapp` se spravovanou identitou přiřazenou systémem, jak je požadováno `--assign-identity` parametrem.

```azurecli
az spring-cloud app create --name "msiapp" --service "mymsispringcloud" --resource-group "myResourceGroup" --is-public true --assign-identity
```

## <a name="build-sample-spring-boot-app-to-invoke-the-function"></a>Sestavit ukázkovou aplikaci pro spouštění pružin pro vyvolání funkce

Tato ukázka vyvolá funkci aktivovanou protokolem HTTP, která nejprve požaduje přístupový token z [koncového bodu MSI](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-use-vm-token#get-a-token-using-http) a pomocí tohoto tokenu ověří požadavek HTTP funkce.

1. Naklonujte vzorový projekt. 

    ```console
    git clone https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples.git
    ```

2. Ve vlastnostech aplikace zadejte identifikátor URI funkce a název aktivační události. 

    ```bash
    cd Azure-Spring-Cloud-Samples/managed-identity-function
    vim src/main/resources/application.properties
    ```

    Pokud chcete používat spravovanou identitu pro aplikace Azure pro jarní Cloud, přidejte vlastnosti s následujícím obsahem do *Src/Main/Resources/Application. Properties*.

    ```
    azure.function.uri=https://<your-functionapp-name>.azurewebsites.net
    azure.function.triggerPath=httptrigger
    ```

3. Zabalit ukázkovou aplikaci. 

    ```console
    mvn clean package
    ```

4. Nyní Nasaďte aplikaci do Azure pomocí příkazu Azure CLI  `az spring-cloud app deploy` . 

    ```azurecli
    az spring-cloud app deploy  --name "msiapp" --service "mymsispringcloud" --resource-group "myResourceGroup" --jar-path target/sc-managed-identity-function-sample-0.1.0.jar
    ```

5. K otestování vaší aplikace se dostanete ke veřejnému koncovému bodu nebo testovacímu bodu. 

    ```console
    curl https://mymsispringcloud-msiapp.azuremicroservices.io/func/springcloud
    ```

    V textu odpovědi se zobrazí následující zpráva.
    ```output
    Function Response: Hello, springcloud. This HTTP triggered function executed successfully.
    ```
    
    Můžete zkusit předat jiné hodnoty funkci změnou parametru cesty.

## <a name="next-steps"></a>Další kroky

* [Postup povolení spravované identity přiřazené systémem pro aplikaci Azure jaře Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-howto-enable-system-assigned-managed-identity)
* [Další informace o spravovaných identitách pro prostředky Azure](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [Konfigurace klientské aplikace démona pro volání služba-služba](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad#configure-a-daemon-client-application-for-service-to-service-calls)

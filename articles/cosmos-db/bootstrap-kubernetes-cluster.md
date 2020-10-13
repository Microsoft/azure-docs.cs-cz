---
title: Jak používat Azure Kubernetes s Azure Cosmos DB
description: Naučte se, jak spustit cluster Kubernetes v Azure, který používá Azure Cosmos DB (Preview).
author: SnehaGunda
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/06/2019
ms.author: sngun
ms.openlocfilehash: b9e6e1388465542e9fb3ac69540c981a1fd70d4b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91570124"
---
# <a name="how-to-use-azure-kubernetes-with-azure-cosmos-db-preview"></a>Jak používat Azure Kubernetes s Azure Cosmos DB (Preview)

Rozhraní etcd API v Azure Cosmos DB umožňuje používat Azure Cosmos DB jako úložiště back-endu pro Azure Kubernetes. Azure Cosmos DB implementuje etcd přenosový protokol, který umožňuje serverům rozhraní API primárního uzlu používat Azure Cosmos DB stejným způsobem, jako by měl přístup k lokálně instalované etcd. rozhraní etcd API v Azure Cosmos DB je aktuálně ve verzi Preview. Pokud používáte Azure Cosmos etcd API jako záložní úložiště pro Kubernetes, získáte následující výhody: 

* Není potřeba ručně konfigurovat a spravovat etcd.
* Vysoká dostupnost etcd, zaručená Cosmos (99,99% v jedné oblasti, 99,999% v několika oblastech).
* Elastická škálovatelnost etcd
* Zabezpečení ve výchozím nastavení & Enterprise připraveno.
* Špičkové komplexní Slay v oboru.

Další informace o rozhraní etcd API v Azure Cosmos DB najdete v článku [Přehled](etcd-api-introduction.md) . V tomto článku se dozvíte, jak pomocí [modulu Azure Kubernetes Engine](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md) (AKS-Engine) vytvořit cluster Kubernetes v Azure, který používá [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/) namísto lokálně nainstalovaného a nakonfigurovaného etcd. 

## <a name="prerequisites"></a>Požadavky

1. Nainstalujte nejnovější verzi rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true). Můžete stáhnout Azure CLI specifické pro váš operační systém a nainstalovat.

1. Nainstalujte [nejnovější verzi](https://github.com/Azure/aks-engine/releases) modulu Azure Kubernetes Engine. Pokyny k instalaci pro různé operační systémy jsou k dispozici na stránce [Azure Kubernetes Engine](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md#install-aks-engine) . Pouze potřebujete kroky z části **Instalace modulu AKS** v propojeném dokumentu. Po stažení rozbalte soubor zip.

   Modul Azure Kubernetes Engine (**AKS-Engine**) generuje šablony Azure Resource Manager pro clustery Kubernetes v Azure. Vstup do AKS-Engine je definiční soubor clusteru, který popisuje požadovaný cluster, včetně nástroje Orchestrator, features a agentů. Struktura vstupních souborů je podobná veřejnému rozhraní API pro službu Azure Kubernetes.

1. Rozhraní etcd API v Azure Cosmos DB je momentálně ve verzi Preview. Zaregistrujte se a použijte verzi Preview na adrese: https://aka.ms/cosmosetcdapi-signup . Po odeslání formuláře bude vaše předplatné moct používat rozhraní etcd API Azure Cosmos. 

## <a name="deploy-the-cluster-with-azure-cosmos-db"></a>Nasazení clusteru s Azure Cosmos DB

1. Otevřete okno příkazového řádku a přihlaste se k Azure pomocí následujícího příkazu:

   ```azurecli-interactive
   az login 
   ```

1. Pokud máte více než jedno předplatné, přepněte do předplatného, které bylo povoleno pro Azure Cosmos DB rozhraní etcd API. Pomocí následujícího příkazu můžete přepnout na požadované předplatné:

   ```azurecli-interactive
   az account set --subscription "<Name of your subscription>"
   ```
1. Potom vytvořte novou skupinu prostředků, do které nasadíte prostředky, které cluster Azure Kubernetes vyžaduje. Nezapomeňte vytvořit skupinu prostředků v oblasti "centralus". Není nutné, aby skupina prostředků byla v oblasti "centralus", ale rozhraní API pro Azure Cosmos etcd je aktuálně k dispozici k nasazení v oblasti "centralus". Proto je vhodné, aby byl cluster Kubernetes společně umístěn s instancí Cosmos etcd:

   ```azurecli-interactive
   az group create --name <Name> --location "centralus"
   ```

1. Dále vytvořte instanční objekt pro cluster Azure Kubernetes, aby mohl komunikovat s prostředky, které jsou součástí stejné skupiny prostředků. Instanční objekt můžete vytvořit pomocí rozhraní příkazového řádku Azure CLI, PowerShellu nebo Azure Portal, v tomto příkladu budete mít příkaz CLI a vytvoříte ho.

   ```azurecli-interactive
   az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<Your_Azure_subscription_ID>/resourceGroups/<Your_resource_group_name>"
   ```
   Tento příkaz vypíše Podrobnosti objektu služby, například:
   
   ```cmd
   Retrying role assignment creation: 1/36
   {
     "appId": "8415a4e9-4f83-46ca-a704-107457b2e3ab",
     "displayName": "azure-cli-2019-04-19-19-01-46",
     "name": "http://azure-cli-2019-04-19-19-01-46",
     "password": "102aecd3-5e37-4f3d-8738-2ac348c2e6a7",
     "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
   }
   ```
   
   Poznamenejte si pole **appId** a **Password** , protože tyto parametry použijete v dalších krocích. 

1. Z příkazového řádku přejděte do složky, ve které je umístěný spustitelný soubor modulu Azure Kubernetes. Například na příkazovém řádku můžete přejít do složky jako:

   ```cmd
   cd "\aks-engine-v0.36.3-windows-amd64\aks-engine-v0.36.3-windows-amd64"
   ```

1. Otevřete textový editor podle vlastního výběru a definujte šablonu Správce prostředků, která nasadí cluster Azure Kubernetes pomocí rozhraní API Azure Cosmos DB etcd. Zkopírujte následující definici JSON do textového editoru a soubor uložte jako `apiModel.json` :

   ```json

   {
    "apiVersion": "vlabs",
    "properties": {
        "orchestratorProfile": {
            "orchestratorType": "Kubernetes",
            "kubernetesConfig": {
                "useManagedIdentity": false
            }
        },
        "masterProfile": {
            "count": 1,
            "dnsPrefix": "",
            "vmSize": "Standard_D2_v3",
            "cosmosEtcd": true
        },
        "agentPoolProfiles": [
            {
                "name": "agent",
                "count": 1,
                "vmSize": "Standard_D2_v3",
                "availabilityProfile": "AvailabilitySet"
            }
        ],
        "linuxProfile": {
            "adminUsername": "azureuser",
            "ssh": {
                "publicKeys": [
                    {
                        "keyData": ""
                    }
                ]
            }
        }
    }
   }
   ```

   V souboru definice JSON nebo clusteru je parametr klíče, který se má poznamenat **"cosmosEtcd": true**. Tento parametr je ve vlastnostech "profilech masterprofile" a označuje, že nasazení bude používat rozhraní API Azure Cosmos etcd místo regulárního etcd. 

1. Nasaďte cluster Azure Kubernetes, který používá Azure Cosmos DB, s následujícím příkazem:

   ```cmd
   aks-engine deploy \
     --subscription-id <Your_Azure_subscription_ID> \
     --client-id <Service_ principal_appId> \
     --client-secret <Service_ principal_password> \
     --dns-prefix <Region_unique_dns_name > \
     --location centralus \
     --resource-group <Resource_Group_Name> \
     --api-model <Fully_qualified_path_to_the_template_file>  \
     --force-overwrite
   ```

   Modul Azure Kubernetes využívá definici clusteru, která popisuje požadovaný tvar, velikost a konfiguraci Azure Kubernetes. V rámci definice clusteru je možné povolit několik funkcí. V tomto příkladu budete používat následující parametry:

   * **ID předplatného:** ID předplatného Azure, které má povolené Azure Cosmos DB rozhraní API etcd
   * **ID klienta:** Identifikátor appId objektu služby `appId`V kroku 4 se vrátil jako výstup.
   * **Tajný kód klienta:** Heslo objektu služby nebo náhodně generované heslo. Tato hodnota byla vrácena jako výstup v parametru Password v kroku 4. 
   * **pole dnsprefix:** Název DNS jedinečné pro oblast. Tato hodnota bude tvořit část názvu hostitele (příklad hodnot je-myprod1, fázování).
   * **umístění:**  Umístění, do kterého se má cluster nasadit, momentálně se podporuje jenom "centralus".

   > [!Note]
   > Rozhraní Azure Cosmos etcd API je teď k dispozici jenom pro nasazení v oblasti "centralus". 
 
   * **model rozhraní API:** Plně kvalifikovaná cesta k souboru šablony.
   * **Vynutit – přepsat:** Tato možnost slouží k automatickému přepsání existujících souborů ve výstupním adresáři.
 
   Následující příkaz ukazuje příklad nasazení:

   ```cmd
   aks-engine deploy \
     --subscription-id 1234fc61-1234-1234-1234-be1234d12c1b \
     --client-id 1234a4e9-4f83-46ca-a704-107457b2e3ab \
     --client-secret 123aecd3-5e37-4f3d-8738-2ac348c2e6a7 \
     --dns-prefix aks-sg-test \
     --location centralus \
     --api-model "C:\Users\demouser\Downloads\apiModel.json"  \
     --force-overwrite
   ```

## <a name="verify-the-deployment"></a>Ověření nasazení

Dokončení nasazení šablony trvá několik minut. Po úspěšném dokončení nasazení se zobrazí následující výstup do příkazového řádku:

```cmd
WARN[0006] apimodel: missing masterProfile.dnsPrefix will use "aks-sg-test"
WARN[0006] --resource-group was not specified. Using the DNS prefix from the apimodel as the resource group name: aks-sg-test
INFO[0025] Starting ARM Deployment (aks-sg-test-546247491). This will take some time...
INFO[0587] Finished ARM Deployment (aks-sg-test-546247491). Succeeded
```

Skupina prostředků teď obsahuje prostředky, jako jsou virtuální počítače, účet Azure Cosmos (etcd API), virtuální síť, skupinu dostupnosti a další prostředky vyžadované clusterem Kubernetes. 

Název účtu Azure Cosmos bude odpovídat zadané předponě DNS připojené k k8s. Váš účet Azure Cosmos se automaticky zřídí s databází s názvem **EtcdDB** a kontejnerem s názvem **EtcdData**. Kontejner bude ukládat všechna data související s etcd. Kontejner se zřídí s určitým počtem jednotek žádostí. [propustnost můžete škálovat (zvýšit nebo snížit)](scaling-throughput.md) na základě vašich úloh. 

## <a name="next-steps"></a>Další kroky

* Naučte [se pracovat s Azure Cosmos Database, kontejnery a položkami](databases-containers-items.md) .
* Informace o tom, jak [optimalizovat zřízené náklady na propustnost](optimize-cost-throughput.md)


---
title: Jak používat Azure Kubernetes s Azure Cosmos DB
description: Přečtěte si, jak zavést cluster Kubernetes v Azure, který používá Azure Cosmos DB (preview)
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: sngun
ms.openlocfilehash: 9dbbc914580d8d80a3f9b7d730574e24b44827c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70093731"
---
# <a name="how-to-use-azure-kubernetes-with-azure-cosmos-db-preview"></a>Jak používat Azure Kubernetes s Azure Cosmos DB (preview)

Rozhraní ETCD API v Azure Cosmos DB umožňuje používat Azure Cosmos DB jako back-endové úložiště pro Azure Kubernetes. Azure Cosmos DB implementuje připojený mnoství, které umožňuje serverům rozhraní API hlavního uzlu používat Azure Cosmos DB stejně jako by měl přístup k místně nainstalovanému etcd. etcd API v Azure Cosmos DB je aktuálně ve verzi preview. Když používáte Rozhraní API Azure Cosmos etcd jako záložní úložiště pro Kubernetes, získáte následující výhody: 

* Není třeba ručně konfigurovat a spravovat atd.
* Vysoká dostupnost etcd, zaručená společností Cosmos (99,99 % v jednotlivých regionech, 99,999 % ve více oblastech).
* Elastická škálovatelnost etcd.
* Zabezpečte ve výchozím nastavení & připraveni k podniku.
* Komplexní smlouvy SLA v oboru.

Další informace o rozhraní API etcd v Azure Cosmos DB najdete v článku [přehledu.](etcd-api-introduction.md) Tento článek ukazuje, jak používat [Azure Kubernetes Engine](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md) (aks-engine) k zavádění clusteru Kubernetes v Azure, který používá [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/) namísto místně nainstalované a nakonfigurované etcd. 

## <a name="prerequisites"></a>Požadavky

1. Nainstalujte nejnovější verzi [příkazového příkazového příkazu k webu Azure](/cli/azure/install-azure-cli?view=azure-cli-latest). Můžete si stáhnout Azure CLI specifické pro váš operační systém a nainstalovat.

1. Nainstalujte [nejnovější verzi](https://github.com/Azure/aks-engine/releases) Azure Kubernetes Engine. Pokyny k instalaci pro různé operační systémy jsou k dispozici na stránce [Azure Kubernetes Engine.](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md#install-aks-engine) Stačí jen kroky z **instalace AKS Engine** části propojeného dokumentu. Po stažení extrahujte soubor zip.

   Azure Kubernetes Engine **(aks-engine)** generuje šablony Azure Resource Manager pro clustery Kubernetes v Azure. Vstup do aks-engine je soubor definice clusteru, který popisuje požadovaný cluster, včetně orchestrator, funkce a agenti. Struktura vstupních souborů je podobná veřejnému rozhraní API pro službu Azure Kubernetes.

1. Rozhraní API etcd v Azure Cosmos DB je aktuálně ve verzi preview. Zaregistrujte se k použití https://aka.ms/cosmosetcdapi-signupverze preview na adrese: . Po odeslání formuláře bude vaše předplatné na seznamu povolených, aby se používalo rozhraní Azure Cosmos etcd API. 

## <a name="deploy-the-cluster-with-azure-cosmos-db"></a>Nasazení clusteru pomocí Azure Cosmos DB

1. Otevřete okno příkazového řádku a přihlaste se do Azure pomocí následujícího příkazu:

   ```azurecli-interactive
   az login 
   ```

1. Pokud máte více než jedno předplatné, přepněte na předplatné, které bylo na seznamu povolených pro Azure Cosmos DB etcd ROZHRANÍ API. Na požadované předplatné můžete přepnout pomocí následujícího příkazu:

   ```azurecli-interactive
   az account set --subscription "<Name of your subscription>"
   ```
1. Dále vytvořte novou skupinu prostředků, kde budete nasazovat prostředky vyžadované clusterem Azure Kubernetes. Ujistěte se, že vytvořit skupinu prostředků v oblasti "centralus". Není povinné pro skupinu prostředků být v oblasti "centralus", ale Azure Cosmos etcd rozhraní API je aktuálně k dispozici pro nasazení v oblasti "centralus" pouze. Takže je nejlepší mít cluster Kubernetes, který má být umístěn společně s instancí Cosmos etcd:

   ```azurecli-interactive
   az group create --name <Name> --location "centralus"
   ```

1. Dále vytvořte instanční objekt pro cluster Azure Kubernetes tak, aby mohl komunikovat s prostředky, které jsou součástí stejné skupiny prostředků. Můžete vytvořit instanční objekt pomocí Azure CLI, PowerShell nebo portál Azure, v tomto příkladu budete cli k jeho vytvoření.

   ```azurecli-interactive
   az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<Your_Azure_subscription_ID>/resourceGroups/<Your_resource_group_name>"
   ```
   Tento příkaz vyproduje podrobnosti instančního objektu, například:
   
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
   
   Poznamenejte si **appId** a **heslo** pole, jak budete používat tyto parametry v dalších krocích. 

1. Z příkazového řádku přejděte do složky, kde je umístěn spustitelný soubor Azure Kubernetes Engine. Na příkazovém řádku můžete například přejít do složky jako:

   ```cmd
   cd "\aks-engine-v0.36.3-windows-amd64\aks-engine-v0.36.3-windows-amd64"
   ```

1. Otevřete textový editor podle vašeho výběru a definujte šablonu Správce prostředků, která nasazuje cluster Azure Kubernetes pomocí rozhraní Azure Cosmos DB etcd. Zkopírujte následující definici JSON do textového `apiModel.json`editoru a uložte soubor jako:

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

   V definičním souboru JSON/cluster je klíčovým parametrem, který je třeba si **uvědomit, "cosmosEtcd": true**. Tento parametr je ve vlastnostech "masterProfile" a označuje nasazení pro použití rozhraní API Azure Cosmos etcd namísto běžné etcd. 

1. Nasazení clusteru Azure Kubernetes, který používá Azure Cosmos DB s následujícím příkazem:

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

   Azure Kubernetes Engine spotřebovává definici clusteru, která popisuje požadovaný tvar, velikost a konfiguraci Azure Kubernetes. Existuje několik funkcí, které lze povolit prostřednictvím definice clusteru. V tomto příkladu použijete následující parametry:

   * **id předplatného:** ID předplatného Azure, které má povolenou rozhraní API Azure Cosmos DB etcd.
   * **klient-id:** Id aplikačního objektu instančního objektu. Byl `appId` vrácen jako výstup v kroku 4.
   * **Tajný klíč klienta:** Heslo instančního objektu nebo náhodně generované heslo. Tato hodnota byla vrácena jako výstup v parametru 'password' v kroku 4. 
   * **dnsPrefix:** Jedinečný název DNS v oblasti. Tato hodnota bude součástí názvu hostitele (příklad hodnoty jsou- myprod1, staging).
   * **umístění:**  Umístění, kde by měl být cluster nasazen, je aktuálně podporováno pouze "centralus".

   > [!Note]
   > Azure Cosmos etcd rozhraní API je aktuálně k dispozici k nasazení v oblasti "centralus" pouze. 
 
   * **api-model:** Plně kvalifikovaná cesta k souboru šablony.
   * **přepsání síly:** Tato možnost se používá k automatickému přepsání existujících souborů ve výstupním adresáři.
 
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

Nasazení šablony trvá několik minut. Po úspěšném dokončení nasazení se v příkazovém řádku zobrazí následující výstup:

```cmd
WARN[0006] apimodel: missing masterProfile.dnsPrefix will use "aks-sg-test"
WARN[0006] --resource-group was not specified. Using the DNS prefix from the apimodel as the resource group name: aks-sg-test
INFO[0025] Starting ARM Deployment (aks-sg-test-546247491). This will take some time...
INFO[0587] Finished ARM Deployment (aks-sg-test-546247491). Succeeded
```

Skupina prostředků teď obsahuje prostředky, jako je virtuální počítač, účet Azure Cosmos (etcd API), virtuální síť, sada dostupnosti a další prostředky vyžadované clusteru Kubernetes. 

Název účtu Azure Cosmos bude odpovídat zadané předponě DNS připojené k8s. Váš účet Azure Cosmos se automaticky zřídí databází s názvem **EtcdDB** a kontejnerem s názvem **EtcdData**. Kontejner uloží všechna data související s etcd. Kontejner je zřízen s určitým počtem jednotek požadavků a můžete [škálovat (zvýšit nebo snížit) propustnost](scaling-throughput.md) na základě vašeho pracovního vytížení. 

## <a name="next-steps"></a>Další kroky

* Zjistěte, jak [pracovat s databází, kontejnery a položkami Azure Cosmos](databases-containers-items.md)
* Naučte se optimalizovat náklady na [zřízenou propustnost](optimize-cost-throughput.md)


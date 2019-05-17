---
title: Jak používat Kubernetes v Azure pomocí služby Azure Cosmos DB
description: Zjistěte, jak spustit cluster Kubernetes v Azure, která používá službu Azure Cosmos DB (preview)
author: SnehaGunda
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/06/2019
ms.author: sngun
ms.openlocfilehash: 43aa0956ef1f44fa5705800ff2b424608ec75499
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2019
ms.locfileid: "65795614"
---
# <a name="how-to-use-azure-kubernetes-with-azure-cosmos-db-preview"></a>Jak používat Kubernetes v Azure pomocí služby Azure Cosmos DB (preview)

Etcd rozhraní API ve službě Azure Cosmos DB umožňuje používat službu Azure Cosmos DB jako back-endu úložiště pro Kubernetes v Azure. Azure Cosmos DB implementuje přenosový protokol etcd, což umožňuje hlavní uzel rozhraní API serverů k používání služby Azure Cosmos DB, stejně jako ji by přístup k místně instalované etcd. etcd rozhraní API ve službě Azure Cosmos DB je aktuálně ve verzi preview. Pokud používáte Azure Cosmos etcd rozhraní API jako záložní úložiště pro Kubernetes, získáte následující výhody: 

* Už není potřeba ručně konfigurovat a spravovat etcd.
* Vysoká dostupnost etcd zaručeny Cosmos (99,99 % v jedné oblasti, ve více oblastech 99,999 %).
* Elastické škálovatelnosti etcd.
* Zabezpečte tím, že výchozí & připraveno pro podniky.
* Špičkové, komplexní smlouvy SLA.

Další informace o etcd rozhraní API ve službě Azure Cosmos DB, najdete v článku [přehled](etcd-api-introduction.md) článku. V tomto článku se dozvíte, jak používat [modul Azure Kubernetes](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md) (aks-engine) ke spuštění clusteru Kubernetes v Azure, která používá [služby Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/) místo etcd místně nainstalovaná a nakonfigurovaná. 

## <a name="prerequisites"></a>Požadavky

1. Nainstalujte nejnovější verzi [rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli?view=azure-cli-latest). Můžete stáhnout Azure CLI, které jsou specifické pro váš operační systém a nainstalovat.

1. Nainstalujte [v0.32.3](https://github.com/Azure/aks-engine/releases/tag/v0.32.3) verze modulu Azure Kubernetes. Pokyny pro různé operační systémy jsou k dispozici v [modul Azure Kubernetes](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md#install-aks-engine) stránky. Potřebujete pouze kroky z **nainstalovat modul AKS** část propojené dokumentu. Po stažení, extrahuje soubor zip.

   Modul Azure Kubernetes (**aks-engine**) generuje šablon Azure Resource Manageru pro clustery Kubernetes v Azure. Vstup pro aks modul je soubor definice clusteru, které popisují požadovanou clusteru, včetně nástroje orchestrator, funkce a agenty. Struktura vstupních souborů je podobný veřejné rozhraní API pro službu Azure Kubernetes Service.

1. Etcd rozhraní API ve službě Azure Cosmos DB je aktuálně ve verzi preview. Zaregistrujte a použijte verzi preview v: https://aka.ms/cosmosetcdapi-signup. Po odeslání formuláře, vaše předplatné bude používat Azure Cosmos etcd rozhraní API na seznamu povolených. 

## <a name="deploy-the-cluster-with-azure-cosmos-db"></a>Nasazení clusteru pomocí služby Azure Cosmos DB

1. Otevřete okno příkazového řádku a přihlaste se do Azure pomocí následujícího příkazu:

   ```azurecli-interactive
   az login 
   ```

1. Pokud máte více než jedno předplatné, přepněte na předplatné, které bylo povolené pro službu Azure Cosmos DB etcd rozhraní API. Můžete přepnout na požadované předplatné, pomocí následujícího příkazu:

   ```azurecli-interactive
   az account set --subscription "<Name of your subscription>"
   ```
1. Dále vytvořte novou skupinu prostředků, kde budete nasazovat prostředky vyžadované clusteru Kubernetes v Azure. Ujistěte se, že chcete vytvořit skupinu prostředků v oblasti "centralus". Není to povinné pro skupinu prostředků, ale bude v oblasti "centralus", je aktuálně k dispozici pro nasazení v oblasti "centralus" pouze Azure Cosmos etcd rozhraní API. Proto je vhodné mít cluster Kubernetes, bude umístěna společně s instancí etcd Cosmos:

   ```azurecli-interactive
   az group create --name <Name> --location "centralus"
   ```

1. Dále vytvořte instanční objekt pro cluster Kubernetes v Azure tak, aby mohl komunikovat s prostředky, které jsou součástí stejné skupiny prostředků. Můžete vytvořit instanční objekt služby pomocí rozhraní příkazového řádku Azure, Powershellu nebo webu Azure portal, v tomto příkladu, které budete rozhraní příkazového řádku k jeho vytvoření.

   ```azurecli-interactive
   az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<Your_Azure_subscription_ID>/resourceGroups/<Your_resource_group_name>"
   ```
   Tento příkaz vypíše podrobnosti instančního objektu, například:
   
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
   
   Poznamenejte si **appId** a **heslo** pole, protože budete používat tyto parametry v dalších krocích. 

1. Z příkazového řádku přejděte do složky, ve kterém je modul Azure Kubernetes spustitelný soubor umístěn. Například na příkazovém řádku můžete přejděte do složky jako:

   ```cmd
   cd "\aks-engine-v0.32.3-windows-amd64\aks-engine-v0.32.3-windows-amd64"
   ```

1. Otevřete textový editor podle vašeho výběru a definujte šablony Resource Manageru, který se nasadí cluster Kubernetes v Azure pomocí služby Azure Cosmos DB etcd rozhraní API. Zkopírujte následující definici JSON do textového editoru a uložte soubor jako `apiModel.json`:

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

   V souboru definice JSON/clusteru, je parametr klíče si uvědomit **"cosmosEtcd": true**. Tento parametr je ve vlastnostech "profilech masterProfile" a informuje o nasazení rozhraní API služby Azure Cosmos etcd nahrazujícím regulární etcd. 

1. Nasazení clusteru Kubernetes v Azure, která používá službu Azure Cosmos DB pomocí následujícího příkazu:

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

   Modul Azure Kubernetes využívá definici clusteru, který popisuje požadovaný tvar, velikost a konfigurace Azure Kubernetes. Existuje několik funkcí, které se dá nastavit pomocí definice clusteru. V tomto příkladu budete používat následující parametry:

   * **id předplatného:** ID předplatného Azure, který má rozhraní API služby Azure Cosmos DB etcd povolena.
   * **id klienta:** ID aplikace instančního objektu. `appId` Byla vrácena jako výstup v kroku 4.
   * **Tajný klíč klienta:** Heslo instančního objektu nebo náhodně generované heslo. Tato hodnota byla vrácena jako výstup v parametru 'password' v kroku 4. 
   * **dnsPrefix:** Oblasti jedinečný název DNS. Tato hodnota je součástí názvu hostitele (například hodnoty jsou – myprod1, pracovní).
   * **Umístění:**  Umístění, kde clusteru by mělo být nasazená momentálně se podporuje jenom "centralus" je nepodporuje.

   > [!Note]
   > Rozhraní API služby Azure Cosmos etcd je aktuálně k dispozici pro nasazení v oblasti "centralus" pouze. 
 
   * **api-model:** Plně kvalifikovaná cesta k souboru šablony.
   * **vynucení přepsání:** Tato možnost se používá k automatickému přepsání existujících souborů ve výstupním adresáři.
 
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

Nasazení šablony trvá několik minut. Po úspěšném dokončení nasazení se zobrazí následující výstup v příkazovém řádku příkazy:

```cmd
WARN[0006] apimodel: missing masterProfile.dnsPrefix will use "aks-sg-test"
WARN[0006] --resource-group was not specified. Using the DNS prefix from the apimodel as the resource group name: aks-sg-test
INFO[0025] Starting ARM Deployment (aks-sg-test-546247491). This will take some time...
INFO[0587] Finished ARM Deployment (aks-sg-test-546247491). Succeeded
```

Skupina prostředků, jako teď obsahuje prostředky – virtuální počítač Azure Cosmos účtu (etcd rozhraní API), virtuální sítě, dostupnosti, a další prostředky vyžadované clusteru Kubernetes. 

Název účtu Azure Cosmos se bude shodovat s k8s zadanou předponu DNS. Váš účet Azure Cosmos automaticky zřídí databázi s názvem **EtcdDB** a kontejner s názvem **EtcdData**. Kontejner bude ukládat všechny etcd související data. Kontejner je zřízený s počtem jednotek žádosti a můžete [škálování (zvětšit nebo zmenšit) propustnost](scaling-throughput.md) na základě vašich úloh. 

## <a name="next-steps"></a>Další postup

* Zjistěte, jak [pracovat s databází Azure Cosmos, kontejnery a položek](databases-containers-items.md)
* Zjistěte, jak [optimalizaci nákladů na zřízená propustnost](optimize-cost-throughput.md)


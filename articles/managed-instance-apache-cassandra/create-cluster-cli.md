---
title: Rychlý Start – použití rozhraní příkazového řádku k vytvoření spravované instance Azure pro cluster Apache Cassandra
description: Pomocí tohoto rychlého startu můžete vytvořit spravovanou instanci Azure pro cluster Apache Cassandra pomocí Azure CLI.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/15/2021
ms.openlocfilehash: b719310a331044df363efcc6b79be323faf49247
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105562099"
---
# <a name="quickstart-create-an-azure-managed-instance-for-apache-cassandra-cluster-using-azure-cli-preview"></a>Rychlý Start: vytvoření spravované instance Azure pro cluster Apache Cassandra pomocí rozhraní příkazového řádku Azure (Preview)

Spravovaná instance Azure pro Apache Cassandra poskytuje automatizované operace nasazení a škálování pro spravovaná Open Source datacentra Apache Cassandra. Tato služba vám pomůže zrychlit hybridní scénáře a snížit průběžnou údržbu.

> [!IMPORTANT]
> Spravovaná instance Azure pro Apache Cassandra je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Tento rychlý Start ukazuje, jak pomocí příkazů Azure CLI vytvořit cluster se službou Azure Managed instance pro Apache Cassandra. Také ukazuje vytvoření datového centra a škálování uzlů v rámci datového centra nahoru nebo dolů.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) s připojením k místnímu nebo místnímu prostředí. Další informace o připojení k místnímu prostředí do Azure najdete v článku věnovaném [připojení místní sítě k Azure](/azure/architecture/reference-architectures/hybrid-networking/) .

* Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!IMPORTANT]
> Tento článek vyžaduje Azure CLI verze 2.17.1 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="create-a-managed-instance-cluster"></a><a id="create-cluster"></a>Vytvoření clusteru spravované instance

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com/).

1. V Azure CLI nastavte své ID předplatného:

   ```azurecli-interactive
   az account set -s <Subscription_ID>
   ```

1. V dalším kroku vytvořte ve skupině prostředků Virtual Network s vyhrazenou podsítí:

   ```azurecli-interactive
   az network vnet create -n <VNet_Name> -l eastus2 -g <Resource_Group_Name> --subnet-name <Subnet Name>
   ```

1. Použijte některá zvláštní oprávnění pro Virtual Network a podsíť, které vyžaduje spravovaná instance. Použijte `az role assignment create` příkaz, nahraďte `<subscription ID>` , `<resource group name>` , `<VNet name>` a `<subnet name>` s příslušnými hodnotami:

   ```azurecli-interactive
   az role assignment create --assignee e5007d2c-4b13-4a74-9b6a-605d99f03501 --role 4d97b98b-1d4f-4787-a291-c67834d212e7 --scope /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>/subnets/<subnet name>
   ```

   > [!NOTE]
   > `assignee`Hodnoty a `role` v předchozím příkazu jsou pevné hodnoty. Tyto hodnoty zadejte přesně tak, jak je uvedeno v příkazu. V takovém případě se to nepovede, takže při vytváření clusteru dojde k chybám. Pokud narazíte na chyby při spuštění tohoto příkazu, možná nemáte oprávnění k jeho spuštění, kontaktujte svého správce a požádejte ho o oprávnění.

1. Potom vytvořte cluster nově vytvořeným Virtual Network pomocí příkazu [AZ Managed-Cassandra cluster Create](/cli/azure/ext/cosmosdb-preview/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#ext_cosmosdb_preview_az_managed_cassandra_cluster_create) . Spusťte následující příkaz s hodnotou `delegatedManagementSubnetId` proměnné:

   > [!NOTE]
   > Hodnota proměnné, kterou poskytnete `delegatedManagementSubnetId` níže, je přesně stejná jako hodnota `--scope` , kterou jste zadali v příkazu výše:

   ```azurecli-interactive
   resourceGroupName='<Resource_Group_Name>'
   clusterName='<Cluster_Name>'
   location='eastus2'
   delegatedManagementSubnetId='/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>/subnets/<subnet name>'
   initialCassandraAdminPassword='myPassword'
    
   az managed-cassandra cluster create \
      --cluster-name $clusterName \
      --resource-group $resourceGroupName \
      --location $location \
      --delegated-management-subnet-id $delegatedManagementSubnetId \
      --initial-cassandra-admin-password $initialCassandraAdminPassword \
      --debug
   ```

1. Nakonec vytvořte datacentrum pro cluster se třemi uzly pomocí příkazu [AZ Managed-Cassandra datadatacenter Create](/cli/azure/ext/cosmosdb-preview/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#ext_cosmosdb_preview_az_managed_cassandra_datacenter_create) :

   ```azurecli-interactive
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
    
   az managed-cassandra datacenter create \
      --resource-group $resourceGroupName \
      --cluster-name $clusterName \
      --data-center-name $dataCenterName \
      --data-center-location $dataCenterLocation \
      --delegated-subnet-id $delegatedManagementSubnetId \
      --node-count 3 
   ```

1. Pokud chcete horizontální navýšení kapacity nebo horizontální navýšení kapacity uzlů v datovém centru, spusťte příkaz [AZ Managed-Cassandra Datacenter Update](/cli/azure/ext/cosmosdb-preview/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#ext_cosmosdb_preview_az_managed_cassandra_datacenter_update) . Změňte hodnotu `node-count` parametru na požadovanou hodnotu:

   ```azurecli-interactive
   resourceGroupName='<Resource_Group_Name>'
   clusterName='<Cluster Name>'
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
    
   az managed-cassandra datacenter update \
      --resource-group $resourceGroupName \
      --cluster-name $clusterName \
      --data-center-name $dataCenterName \
      --node-count 9 
   ```

## <a name="connect-to-your-cluster"></a>Připojení ke clusteru

Spravovaná instance Azure pro Apache Cassandra nevytváří uzly s veřejnými IP adresami. Pokud se chcete připojit ke svému nově vytvořenému clusteru Cassandra, musíte v rámci virtuální sítě vytvořit jiný prostředek. Tímto prostředkem může být aplikace nebo virtuální počítač s nainstalovaným Open Source dotazovacím nástrojem Apache [CQLSH](https://cassandra.apache.org/doc/latest/tools/cqlsh.html) . K nasazení virtuálního počítače s Ubuntu můžete použít [šablonu správce prostředků](https://azure.microsoft.com/resources/templates/101-vm-simple-linux/) . Po nasazení se k počítači připojte pomocí SSH a nainstalujte CQLSH, jak je znázorněno v následujících příkazech:

```bash
# Install default-jre and default-jdk
sudo apt update
sudo apt install openjdk-8-jdk openjdk-8-jre

# Install the Cassandra libraries in order to get CQLSH:
echo "deb http://www.apache.org/dist/cassandra/debian 311x main" | sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list
curl https://downloads.apache.org/cassandra/KEYS | sudo apt-key add -
sudo apt-get update
sudo apt-get install cassandra

# Export the SSL variables:
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false

# Connect to CQLSH (replace <IP> with the private IP addresses of the nodes in your Datacenter):
host=("<IP>" "<IP>" "<IP>")
cqlsh $host 9042 -u cassandra -p cassandra --ssl
```

## <a name="troubleshooting"></a>Řešení potíží

Pokud při použití oprávnění k vašemu Virtual Network dojde k chybě, například *nelze najít uživatele nebo instanční objekt v databázi grafu pro ' e5007d2c-4b13-4a74-9b6a-605d99f03501 '*, můžete stejné oprávnění použít ručně z Azure Portal. Chcete-li použít oprávnění z portálu, přejděte do podokna **řízení přístupu (IAM)** ve stávající virtuální síti a přidejte přiřazení role pro "Azure Cosmos DB" do role "správce sítě". Pokud se při hledání "Azure Cosmos DB" zobrazí dvě položky, přidejte obě položky, jak je znázorněno na následujícím obrázku: 

   :::image type="content" source="./media/create-cluster-cli/apply-permissions.png" alt-text="Použít oprávnění" lightbox="./media/create-cluster-cli/apply-permissions.png" border="true":::

> [!NOTE] 
> Přiřazení role Azure Cosmos DB se používá jenom pro účely nasazení. Spravovaná instance Azure spravovaná pro Apache Cassandra nemá žádné back-endové závislosti na Azure Cosmos DB.  

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete `az group delete` k odebrání skupiny prostředků, spravované instance a všech souvisejících prostředků použít příkaz:

```azurecli-interactive
az group delete --name <Resource_Group_Name>
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak vytvořit spravovanou instanci Azure pro cluster Apache Cassandra pomocí Azure CLI. Teď můžete začít pracovat s clusterem:

> [!div class="nextstepaction"]
> [Nasazení clusteru spravovaného Apache Spark s využitím Azure Databricks](deploy-cluster-databricks.md)
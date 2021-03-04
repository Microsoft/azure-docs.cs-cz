---
title: Rychlý Start – konfigurace hybridního clusteru se spravovanou instancí Azure pro Apache Cassandra
description: V tomto rychlém startu se dozvíte, jak nakonfigurovat hybridní cluster se službou Azure Managed instance pro Apache Cassandra.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.openlocfilehash: dac59fb5262cc55acfbabedd304913fc7ac57751
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101748525"
---
# <a name="quickstart-configure-a-hybrid-cluster-with-azure-managed-instance-for-apache-cassandra-preview"></a>Rychlý Start: Konfigurace hybridního clusteru se spravovanou instancí Azure pro Apache Cassandra (Preview)

Spravovaná instance Azure pro Apache Cassandra poskytuje automatizované operace nasazení a škálování pro spravovaná Open Source datacentra Apache Cassandra. Tato služba vám pomůže zrychlit hybridní scénáře a snížit průběžnou údržbu.

> [!IMPORTANT]
> Spravovaná instance Azure pro Apache Cassandra je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Tento rychlý Start ukazuje použití příkazů Azure CLI ke konfiguraci hybridního clusteru. Pokud máte existující datová centra v místním nebo místním prostředí, můžete pomocí spravované instance Azure pro Apache Cassandra přidat do tohoto clusteru další datová centra a udržovat je.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* Tento článek vyžaduje Azure CLI verze 2.12.1 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

* [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) s připojením k místnímu nebo místnímu prostředí. Další informace o připojení k místnímu prostředí do Azure najdete v článku věnovaném [připojení místní sítě k Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/) .

## <a name="configure-a-hybrid-cluster"></a><a id="create-account"></a>Konfigurace hybridního clusteru

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) a přejděte k prostředku Virtual Network.

1. Otevřete kartu **podsítě** a vytvořte novou podsíť. Další informace o polích ve formuláři **Přidat podsíť** najdete v [Virtual Network](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet) článku:

   :::image type="content" source="./media/configure-hybrid-cluster/subnet.png" alt-text="Přidejte novou podsíť do svého Virtual Network." lightbox="./media/configure-hybrid-cluster/subnet.png" border="true":::
    <!-- ![image](./media/configure-hybrid-cluster/subnet.png) -->

1. Teď budeme u virtuální sítě a podsítě, které Cassandra Managed instance vyžaduje, použít několik zvláštních oprávnění pomocí Azure CLI. Nejdřív musíme najít `Resource ID` pro vaši stávající virtuální síť. Zkopírujte výstup hodnoty z tohoto příkazu pro pozdější verzi `Resource ID` .

   ```azurecli-interactive
    # discover the vnet id
    az network vnet show -n <your VNet name> -g <Resource Group Name> --query "id" --output tsv
   ```

1. Teď použijeme speciální oprávnění a projdeme výstup předchozího příkazu jako parametr oboru:

   ```azurecli-interactive
    az role assignment create --assignee e5007d2c-4b13-4a74-9b6a-605d99f03501 --role 4d97b98b-1d4f-4787-a291-c67834d212e7 --scope <Resource ID>
   ```
    > [!NOTE]
    > `assignee` `role` Výše uvedené hodnoty a jsou pevné zásady služby a identifikátory rolí v uvedeném pořadí. 

1. Dále nakonfigurujeme prostředky pro náš hybridní cluster. Vzhledem k tomu, že už máte cluster, název clusteru tady bude jenom logickým prostředkem, který identifikuje název vašeho existujícího clusteru. Při definování `clusterName` a `clusterNameOverride` proměnných v následujícím skriptu nezapomeňte použít název vašeho existujícího clusteru.

   Budete také potřebovat počáteční a certifikát veřejné klientské certifikáty (Pokud jste nakonfigurovali veřejný a privátní klíč na koncovém bodu Cassandra) a Gossip certifikáty vašeho existujícího clusteru. Pro definování proměnné budete taky muset použít ID prostředku, které jste zkopírovali výše `delegatedManagementSubnetId` .

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster-legal-name'
   clusterNameOverride='cassandra-hybrid-cluster-illegal-name'
   location='eastus2'
   delegatedManagementSubnetId='<Resource ID>'
    
   # You can override the cluster name if the original name is not legal for an Azure resource:
   # overrideClusterName='ClusterNameIllegalForAzureResource'
   # the default cassandra version will be v3.11
    
   az managed-cassandra cluster create \
      --cluster-name $clusterName \
      --resource-group $resourceGroupName \
      --location $location \
      --delegated-management-subnet-id $delegatedManagementSubnetId \
      --external-seed-nodes 10.52.221.2,10.52.221.3,10.52.221.4
      --client-certificates 'BEGIN CERTIFICATE-----\n...PEM format..\n-----END CERTIFICATE-----','BEGIN CERTIFICATE-----\n...PEM format...\n-----END CERTIFICATE-----' \
      --external-gossip-certificates 'BEGIN CERTIFICATE-----\n...PEM format 1...\n-----END CERTIFICATE-----','BEGIN CERTIFICATE-----\n...PEM format 2...\n-----END CERTIFICATE-----'
   ```

    > [!NOTE]
    > Měli byste znát, kde jsou zachovány stávající veřejné a/nebo Gossip certifikáty. Pokud si nejste jistí, měli byste být schopni spustit `keytool -list -keystore <keystore-path> -rfc -storepass <password>` pro tisk certifikátů. 

1. Po vytvoření prostředku clusteru spusťte následující příkaz, který zobrazí podrobnosti o nastavení clusteru:

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster'
    
   az managed-cassandra cluster show \
       --cluster-name $clusterName \
       --resource-group $resourceGroupName \
   ```

1. Předchozí příkaz vrátí informace o prostředí spravované instance. Budete potřebovat certifikáty Gossip, abyste je mohli nainstalovat na uzly ve svém stávajícím datacentru. Na následujícím snímku obrazovky vidíte výstup předchozího příkazu a formát certifikátů:

   :::image type="content" source="./media/configure-hybrid-cluster/show-cluster.png" alt-text="Získejte podrobnosti o certifikátu z clusteru." lightbox="./media/configure-hybrid-cluster/show-cluster.png" border="true":::
    <!-- ![image](./media/configure-hybrid-cluster/show-cluster.png) -->

1. V dalším kroku vytvořte nové datové centrum v hybridním clusteru. Nezapomeňte nahradit hodnoty proměnných v podrobnostech o clusteru:

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster'
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
   delegatedSubnetId= '<Resource ID>'
    
   az managed-cassandra datacenter create \
       --resource-group $resourceGroupName \
       --cluster-name $clusterName \
       --data-center-name $dataCenterName \
       --data-center-location $dataCenterLocation \
       --delegated-subnet-id $delegatedSubnetId \
       --node-count 9 
   ```

1. Teď, když se vytvoří nové datové centrum, spusťte příkaz show Datacenter a zobrazte jeho podrobnosti:

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster'
   dataCenterName='dc1'
    
   az managed-cassandra datacenter show \
       --resource-group $resourceGroupName \
       --cluster-name $clusterName \
       --data-center-name $dataCenterName 
   ```

1. Předchozí příkaz vypíše počáteční uzly nového datacentra. Přidejte uzly počátečního datového centra do konfigurace stávajícího datacentra v rámci souboru *Cassandra. yaml* . A nainstalujte certifikáty Managed instance Gossip, které jste shromáždili dříve:

   :::image type="content" source="./media/configure-hybrid-cluster/show-datacenter.png" alt-text="Získejte podrobnosti o datovém centru." lightbox="./media/configure-hybrid-cluster/show-datacenter.png" border="true":::
    <!-- ![image](./media/configure-hybrid-cluster/show-datacenter.png) -->

    > [!NOTE]
    > Pokud chcete přidat více datových center, můžete opakovat výše uvedené kroky, ale potřebujete pouze počáteční uzly. 

1. Nakonec pomocí následujícího dotazu CQL aktualizujte strategii replikace v jednotlivých prostorech, aby zahrnovala všechna datová centra v rámci clusteru:

   ```bash
   ALTER KEYSPACE "ks" WITH REPLICATION = {'class': 'NetworkTopologyStrategy', ‘on-premise-dc': 3, ‘managed-instance-dc': 3};
   ```
   Je také nutné aktualizovat tabulky hesel:

   ```bash
    ALTER KEYSPACE "system_auth" WITH REPLICATION = {'class': 'NetworkTopologyStrategy', ‘on-premise-dc': 3, ‘managed-instance-dc': 3}
   ```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete nadále používat tento cluster spravované instance, odstraňte jej pomocí následujících kroků:

1. V nabídce na levé straně Azure Portal vyberte **skupiny prostředků**.
1. V seznamu vyberte skupinu prostředků, kterou jste vytvořili pro tento rychlý Start.
1. V podokně s **přehledem** skupiny prostředků vyberte **Odstranit skupinu prostředků**.
3. V dalším okně zadejte název skupiny prostředků, kterou chcete odstranit, a pak vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak vytvořit hybridní cluster pomocí Azure CLI a spravované instance Azure pro Apache Cassandra. Teď můžete začít pracovat s clusterem.

> [!div class="nextstepaction"]
> [Správa spravované instance Azure pro prostředky Apache Cassandra pomocí Azure CLI](manage-resources-cli.md)
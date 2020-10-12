---
title: Nasazení spravovaného clusteru Service Fabric (Preview)
description: V tomto kurzu nasadíte Service Fabric spravovaný cluster pro testování.
ms.topic: tutorial
ms.date: 08/27/2020
ms.custom: references_regions
ms.openlocfilehash: c7ed1a8fceeddecb942edb541c6112492a6e5a2d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91410436"
---
# <a name="tutorial-deploy-a-service-fabric-managed-cluster-preview"></a>Kurz: nasazení spravovaného clusteru Service Fabric (Preview)

V této sérii kurzů si probereme následující:

> [!div class="checklist"]
> * Postup nasazení spravovaného clusteru Service Fabric 
> * [Postup horizontálního navýšení kapacity Service Fabric spravovaného clusteru](tutorial-managed-cluster-scale.md)
> * [Postup přidání a odebrání uzlů ve spravovaném clusteru s Service Fabric](tutorial-managed-cluster-add-remove-node-type.md)
> * [Postup nasazení aplikace na spravovaný Cluster Service Fabric](tutorial-managed-cluster-deploy-app.md)

Tato část řady se zabývá těmito postupy:

> [!div class="checklist"]
> * Připojení k účtu Azure
> * Vytvoření nové skupiny prostředků
> * Nasazení spravovaného clusteru Service Fabric
> * Přidání typu primárního uzlu do clusteru

## <a name="prerequisites"></a>Předpoklady

Než začnete s tímto kurzem:

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

* Nainstalujte [sadu Service Fabric SDK a modul prostředí PowerShell](service-fabric-get-started.md).

* Nainstalujte [Azure PowerShell 4.7.0](https://docs.microsoft.com/powershell/azure/release-notes-azureps?view=azps-4.7.0&preserve-view=true#azservicefabric) (nebo novější).

## <a name="connect-to-your-azure-account"></a>Připojení k účtu Azure

Nahraďte `<your-subscription>` řetězcem předplatného pro váš účet Azure a připojte:

```powershell
Login-AzAccount
Set-AzContext -SubscriptionId <your-subscription>

```

## <a name="create-a-new-resource-group"></a>Vytvoření nové skupiny prostředků

Dále vytvořte skupinu prostředků pro spravovaný Cluster Service Fabric a nahraďte `<your-rg>` a `<location>` názvem požadované skupiny a umístění.

> [!NOTE]
> Mezi podporované oblasti veřejné verze Preview patří `centraluseuap` ,,,, a `eastus2euap` `eastasia` `northeurope` `westcentralus` `eastus2` .

```powershell
$resourceGroup = "myResourceGroup"
$location = "EastUS2"

New-AzResourceGroup -Name $resourceGroup -Location $location
```

## <a name="deploy-a-service-fabric-managed-cluster"></a>Nasazení spravovaného clusteru Service Fabric

### <a name="create-a-service-fabric-managed-cluster"></a>Vytvoření spravovaného clusteru Service Fabric

V tomto kroku vytvoříte Service Fabric spravovaný cluster pomocí příkazu New-AzServiceFabricManagedCluster PowerShellu. Následující příklad vytvoří ve skupině prostředků s názvem myResourceGroup cluster s názvem myCluster. Tato skupina prostředků se vytvořila v předchozím kroku v oblasti eastus2.

Pro tento krok zadejte vlastní hodnoty pro následující parametry:

* **Název clusteru**: Zadejte jedinečný název pro svůj cluster, například *mysfcluster*.
* **Heslo správce**: zadejte heslo pro správce, které se má použít pro protokol RDP na podkladových virtuálních počítačích v clusteru.
* **Kryptografický otisk klientského certifikátu**: zadejte kryptografický otisk klientského certifikátu, který chcete použít pro přístup k vašemu clusteru. Pokud certifikát nemáte, postupujte podle pokynů v [nastavení a načtěte certifikát](https://docs.microsoft.com/azure/key-vault/certificates/quick-create-portal) pro vytvoření certifikátu podepsaného svým držitelem.
* **SKU clusteru**: určete [typ Service Fabric spravovaný cluster](overview-managed-cluster.md#service-fabric-managed-cluster-skus) pro nasazení. *Základní* Clustery SKU jsou určeny pouze pro testovací nasazení a neumožňují přidání ani odebrání typu uzlu.

```powershell
$clusterName = "<unique cluster name>"
$password = "Password4321!@#" | ConvertTo-SecureString -AsPlainText -Force
$clientThumbprint = "<certificate thumbprint>"
$clusterSku = "Standard"

New-AzServiceFabricManagedCluster -ResourceGroupName $resourceGroup -Location $location -ClusterName $clusterName -ClientCertThumbprint $clientThumbprint -ClientCertIsAdmin -AdminPassword $password -Sku $clusterSKU -Verbose
```

### <a name="add-a-primary-node-type-to-the-service-fabric-managed-cluster"></a>Přidat typ primárního uzlu do spravovaného clusteru Service Fabric

V tomto kroku přidáte typ primárního uzlu do clusteru, který jste právě vytvořili. Každý Service Fabric cluster musí mít alespoň jeden primární typ uzlu.

Pro tento krok zadejte vlastní hodnoty pro následující parametry:

* **Název typu uzlu**: Zadejte jedinečný název pro typ uzlu, který se má přidat do clusteru, například "NT1".

> [!NOTE]
> Pokud je typ uzlu přidaný jako první nebo pouze typ uzlu v clusteru, je nutné použít primární vlastnost.

```powershell
$nodeType1Name = "NT1"

New-AzServiceFabricManagedNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName -Name $nodeType1Name -Primary -InstanceCount 5
```

Dokončení tohoto příkazu může trvat několik minut.

## <a name="validate-the-deployment"></a>Ověření nasazení

### <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

Až se nasazení dokončí, najděte Service Fabric Explorer hodnotu na stránce Přehled prostředku spravovaného clusteru Service Fabric na portálu. Po zobrazení výzvy k zadání certifikátu použijte certifikát, pro který se poskytl kryptografický otisk klienta v příkazu PowerShellu.

## <a name="next-steps"></a>Další kroky

V tomto kroku jsme vytvořili a nasadili náš první Service Fabric spravovaný cluster. Další informace o tom, jak škálovat cluster, najdete v těchto tématech:

> [!div class="nextstepaction"]
> [Horizontální navýšení kapacity Service Fabric spravovaného clusteru](tutorial-managed-cluster-scale.md)

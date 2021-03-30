---
title: Nasazení spravovaného clusteru Service Fabric (Preview) pomocí Azure Resource Manager
description: Naučte se vytvořit spravovaný Cluster Service Fabric se šablonou Azure Resource Manager
ms.topic: quickstart
ms.date: 09/28/2020
ms.custom: references_regions
ms.openlocfilehash: 079e8e3e39bd306332abb43fa2ae1f8cc04e305b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91410486"
---
# <a name="quickstart-deploy-a-service-fabric-managed-cluster-preview-with-an-azure-resource-manager-template"></a>Rychlý Start: nasazení spravovaného clusteru Service Fabric (Preview) pomocí šablony Azure Resource Manager

Service Fabric spravované clustery představují vývoj modelu prostředků clusteru Azure Service Fabric, který zjednodušuje možnosti správy nasazení a clusterů. Service Fabric spravované clustery jsou plně zapouzdřený prostředek, který umožňuje nasadit clusterový prostředek s jedním Service Fabric a nemusíte nasazovat všechny podkladové prostředky, které tvoří Cluster Service Fabric. Tento článek popisuje, jak nasadit Service Fabric spravovaný cluster pro testování v Azure pomocí šablony Azure Resource Manager (šablona ARM).

Cluster základních SKU se třemi uzly nasazeným v tomto kurzu se dá použít jenom pro instruktážní účely (místo produkčních úloh). Další informace najdete v tématu  [Service Fabric spravovaných SKU clusteru](overview-managed-cluster.md#service-fabric-managed-cluster-skus).

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto rychlým startem:

* Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je z [ukázek Azure – Service Fabric šablon clusteru](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Basic-SKU-1-NT).

## <a name="create-a-client-certificate"></a>Vytvoření klientského certifikátu

Service Fabric spravované clustery používají klientský certifikát jako klíč pro řízení přístupu. Pokud již máte certifikát klienta, který byste chtěli použít pro řízení přístupu ke clusteru, můžete tento krok přeskočit.

Pokud potřebujete vytvořit nový certifikát klienta, postupujte podle kroků v části [set a načtěte certifikát z Azure Key Vault](../key-vault/certificates/quick-create-portal.md).

Poznamenejte si kryptografický otisk certifikátu, protože to bude potřeba k nasazení šablony v dalším kroku.

## <a name="deploy-the-template"></a>Nasazení šablony

1. Vyberte následující obrázek a přihlaste se k Azure a otevřete šablonu.

      [![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fservice-fabric-cluster-templates%2Fmaster%2FSF-Managed-Basic-SKU-1-NT%2Fazuredeploy.json)

2. Vyberte nebo zadejte následující hodnoty.

    Pro tento rychlý Start zadejte vlastní hodnoty pro následující parametry šablony:

    * **Předplatné**: vyberte předplatné Azure.
    * **Skupina prostředků**: vyberte **vytvořit novou**. Zadejte jedinečný název pro skupinu prostředků, třeba *myResourceGroup*, a pak zvolte **OK**.
    * **Umístění**: vyberte umístění, například **eastus2**. Podporované oblasti pro Service Fabric spravované clustery ve verzi Preview zahrnují,,,, a `centraluseuap` `eastus2euap` `eastasia` `northeurope` `westcentralus` `eastus2` .
    * **Název clusteru**: Zadejte jedinečný název pro svůj cluster, například *mysfcluster*.
    * **Uživatelské jméno správce**: zadejte název správce, který se bude používat pro protokol RDP na podkladových virtuálních počítačích v clusteru.
    * **Heslo správce**: zadejte heslo pro správce, které se má použít pro protokol RDP na podkladových virtuálních počítačích v clusteru.
    * **Kryptografický otisk klientského certifikátu**: zadejte kryptografický otisk klientského certifikátu, který chcete použít pro přístup k vašemu clusteru. Pokud certifikát nemáte, postupujte podle pokynů v [nastavení a načtěte certifikát](../key-vault/certificates/quick-create-portal.md) pro vytvoření certifikátu podepsaného svým držitelem.
    * **Název typu uzlu**: Zadejte jedinečný název typu uzlu, například *NT1*.
    * **Souhlasím s podmínkami a ujednáními uvedenými nahoře**: zaškrtněte toto políčko, pokud chcete souhlasit. 

3. Vyberte **Koupit**.

4. Nasazení clusteru spravovaného Service Fabric může trvat několik minut. Než přejdete k dalším krokům, počkejte, než se nasazení dokončí úspěšně.

## <a name="validate-the-deployment"></a>Ověření nasazení

### <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

Až se nasazení dokončí, najděte Service Fabric Explorer hodnotu ve výstupu a otevřete adresu ve webovém prohlížeči a zobrazte svůj cluster v Service Fabric Explorer. Po zobrazení výzvy k zadání certifikátu použijte certifikát, pro který se v šabloně zadal kryptografický otisk klienta.

> [!NOTE]
> Výstup nasazení můžete najít na webu Azure Portal na kartě nasazení skupiny prostředků.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků pro váš Service Fabric spravovaný cluster. Odstranění skupiny prostředků přes portál:

1. Do *vyhledávacího* pole v horní části portálu zadejte název vaší skupiny prostředků. Jakmile se ve výsledcích hledání zobrazí skupina prostředků použitá v tomto rychlém startu, vyberte ji.
2. Vyberte **Odstranit skupinu prostředků**.
3. Do pole **ZADEJTE NÁZEV SKUPINY PROSTŘEDKŮ:** zadejte název vaší skupiny prostředků a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili spravovaný Cluster Service Fabric. Další informace o tom, jak škálovat cluster, najdete v těchto tématech:

> [!div class="nextstepaction"]
> [Horizontální navýšení kapacity Service Fabric spravovaného clusteru](tutorial-managed-cluster-scale.md)

---
title: Nejčastější dotazy k Azure Red Hat OpenShift | Dokumentace Microsoftu
description: Tady najdete odpovědi na běžné dotazy týkající se Microsoft Azure Red Hat OpenShift
services: container-service
author: tylermsft
ms.author: twhitney
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.openlocfilehash: 77e0e11582808901b10877d0d9284637145aa6f2
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65078667"
---
# <a name="azure-red-hat-openshift-faq"></a>Azure Red Hat OpenShift – nejčastější dotazy

Tento článek se zabývá nejčastější dotazy (FAQ) o Microsoft Azure Red Hat OpenShift.

## <a name="which-azure-regions-are-supported"></a>Které oblasti Azure podporují?

Zobrazit [podporovaných prostředků](supported-resources.md#azure-regions) seznam globálních oblastí, kde je podporován Azure Red Hat OpenShift.

## <a name="can-i-deploy-a-cluster-into-an-existing-virtual-network"></a>Můžete nasadit cluster do existující virtuální sítě?

Ano. Při vytváření clusteru, můžete nasadit cluster Azure Red Hat OpenShift do existující virtuální sítě. Zobrazit [připojení virtuální sítě clusteru pro existující virtuální síť ](tutorial-create-cluster.md#optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network) podrobnosti.

## <a name="what-cluster-operations-are-available"></a>Jaké operace clusteru jsou k dispozici?

Můžete pouze vertikálně navýšit nebo snížit počet výpočetních uzlů. Žádné úpravy povoleno `Microsoft.ContainerService/openShiftManagedClusters` prostředek po jeho vytvoření. Maximální počet výpočetních uzlů je omezený na 20.

## <a name="what-virtual-machine-sizes-can-i-use"></a>Jaké velikosti virtuálních počítačů můžete použít?

Zobrazit [velikostí virtuálních počítačů Azure Red Hat OpenShift](supported-resources.md#virtual-machine-sizes) seznam velikostí virtuálních počítačů můžete použít s clusterem Azure Red Hat OpenShift.

## <a name="is-data-on-my-cluster-encrypted"></a>Jsou data v clusteru šifrovaná?

Ve výchozím nastavení je šifrování v klidovém stavu. Platforma Azure Storage automaticky šifruje vaše data před uložením a dešifruje data před načítání. Zobrazit [šifrování služby Azure Storage pro neaktivní uložená data](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) podrobnosti.

## <a name="can-i-use-prometheusgrafana-to-monitor-containers-and-manage-capacity"></a>Můžete použít Prometheus/Grafana monitorování kontejnerů a spravovat kapacity?

Ne, není v současné době.

## <a name="is-the-docker-registry-available-externally-so-i-can-use-tools-such-as-jenkins"></a>Registr Dockeru je k dispozici, externě, takže lze použít nástroje, jako je Jenkins?

Registr Dockeru je k dispozici `https://docker-registry.apps.<clustername>.<region>.azmosa.io/` však není k dispozici garance odolnosti silné úložiště. Můžete také použít [Azure Container Registry](https://azure.microsoft.com/services/container-registry/).

## <a name="is-cross-namespace-networking-supported"></a>Je podporována mezi – obor názvů sítě?

Zákazníků a správců jednotlivých projektů můžete upravit sítě mezi – obor názvů (včetně odepření ho) na jednotlivých projektů pomocí `NetworkPolicy` objekty.

## <a name="can-an-admin-manage-users-and-quotas"></a>Správce může spravovat uživatele a kvóty?

Ano. Správce Azure Red Hat OpenShift mohou spravovat uživatele a kvóty kromě přístupu všech uživatelů vytvoří projekty.

## <a name="can-i-restrict-a-cluster-to-only-certain-azure-ad-users"></a>Můžete omezit cluster jenom na určité uživatele Azure AD?

Ano. Můžete omezit které Azure AD uživatel může přihlásit ke clusteru pomocí konfigurace aplikace Azure AD. Podrobnosti najdete v tématu [jak: Omezit sadu uživatelů vaší aplikace](https://docs.microsoft.com/azure/active-directory/develop/howto-restrict-your-app-to-a-set-of-users)

## <a name="can-a-cluster-have-compute-nodes-across-multiple-azure-regions"></a>Cluster můžete mít výpočetní uzly ve víc oblastech Azure?

Ne. Všechny uzly v clusteru služby Azure Red Hat OpenShift musí pocházet ze stejné oblasti Azure.

## <a name="are-master-and-infrastructure-nodes-abstracted-away-as-they-are-with-azure-kubernetes-service-aks"></a>Hlavní server a infrastruktury uzly abstrahují hned jak se s Azure Kubernetes Service (AKS)?

Ne. Všechny prostředky, včetně předlohy clusteru spustit ve vašem předplatném zákazníka. Tyto typy prostředků jsou umístěny ve skupině prostředků jen pro čtení.

---
title: Připojení ke službě Azure Kubernetes – databáze Azure pro MySQL
description: Další informace o propojení služby Azure Kubernetes se službou Azure Database for MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 9e020d34b6cfb8117ccff1114cc938c966126b77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537274"
---
# <a name="connecting-azure-kubernetes-service-and-azure-database-for-mysql"></a>Připojení služby Azure Kubernetes service a databáze Azure pro MySQL

Služba Azure Kubernetes Service (AKS) poskytuje spravovaný cluster Kubernetes, který můžete používat v Azure. Níže jsou uvedeny některé možnosti, které je třeba zvážit při použití AKS a Azure Database for MySQL společně k vytvoření aplikace.


## <a name="accelerated-networking"></a>Urychlení sítě
Použijte zrychlené síťové virtuální počítače v clusteru AKS. Když je na virtuálním počítači povolena zrychlená síť, je nižší latence, snížená nervozita a snížené využití procesoru na virtuálním počítači. Přečtěte si další informace o tom, jak funguje zrychlená síť, podporované verze operačního systému a podporované instance virtuálních počítačů pro [Linux](../virtual-network/create-vm-accelerated-networking-cli.md).

Od listopadu 2018 AKS podporuje zrychlené vytváření sítí na těchto podporovaných instancích virtuálních počítačů. Akcelerované sítě je ve výchozím nastavení povolena na nových clusterech AKS, které používají tyto virtuální počítače.

Můžete potvrdit, zda váš cluster AKS zrychlil síť:
1. Přejděte na portál Azure a vyberte cluster AKS.
2. Vyberte kartu Properties (Vlastnosti).
3. Zkopírujte název **skupiny prostředků infrastruktury**.
4. Pomocí vyhledávacího panelu portálu vyhledejte a otevřete skupinu prostředků infrastruktury.
5. Vyberte virtuální ho virtuálního virtuálního materiálu v této skupině prostředků.
6. Přejděte na kartu **Síť** virtuálního zařízení.
7. Zkontrolujte, zda je **akcelerovaná síť** povolena.

Nebo prostřednictvím azure cli pomocí následujících dvou příkazů:
```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query "nodeResourceGroup"
```
Výstupem bude vygenerovaná skupina prostředků, kterou AKS vytvoří a obsahuje síťové rozhraní. Vezměte název "nodeResourceGroup" a použijte jej v dalším příkazu. **EnableAcceleratedNetworking** bude pravda nebo nepravda:
```azurecli
az network nic list --resource-group nodeResourceGroup -o table
```

## <a name="open-service-broker-for-azure"></a>Technologie Open Service Broker for Azure 
[Open Service Broker pro Azure](https://github.com/Azure/open-service-broker-azure/blob/master/README.md) (OSBA) vám umožňuje zřazovat služby Azure přímo z Kubernetes nebo Cloud Foundry. Jedná se o implementaci [rozhraní API pro služby Open Service Broker](https://www.openservicebrokerapi.org/) pro Azure.

Pomocí osba můžete vytvořit azure databázi pro server MySQL a svázat ji s clusterem AKS pomocí rodného jazyka Kubernetes. Přečtěte si, jak používat OSBA a Azure Database for MySQL společně na [stránce OSBA GitHub](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/mysql.md). 



## <a name="next-steps"></a>Další kroky
- [Vytvoření clusteru služby Azure Kubernetes](../aks/kubernetes-walkthrough.md)
- Naučte se [instalovat WordPress z grafu Helm pomocí OSBA a Azure Database for MySQL](../aks/integrate-azure.md)

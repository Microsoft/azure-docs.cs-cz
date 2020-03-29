---
title: Připojení ke službě Azure Kubernetes – databáze Azure pro PostgreSQL – jeden server
description: Další informace o propojení služby Azure Kubernetes Service (AKS) s Databází Azure pro PostgreSQL – jeden server
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.date: 5/6/2019
ms.topic: conceptual
ms.openlocfilehash: 46aa411826dd3ea578a2d98b0fe631ab0a12ef4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74769876"
---
# <a name="connecting-azure-kubernetes-service-and-azure-database-for-postgresql---single-server"></a>Připojení služby Azure Kubernetes service a databáze Azure pro PostgreSQL – jeden server

Služba Azure Kubernetes Service (AKS) poskytuje spravovaný cluster Kubernetes, který můžete používat v Azure. Níže jsou uvedeny některé možnosti, které je třeba zvážit při použití AKS a Azure Database pro PostgreSQL společně k vytvoření aplikace.


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

S OSBA můžete vytvořit azure databázi pro PostgreSQL server a svázat ji s vaším clusterem AKS pomocí nativního jazyka Kubernetes. Přečtěte si, jak používat OSBA a Azure Database for PostgreSQL společně na [stránce OSBA GitHub](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/postgresql.md). 


## <a name="connection-pooling"></a>Sdružování připojení
Sdružovací služba připojení minimalizuje náklady a čas spojený s vytvářením a zavíráním nových připojení k databázi. Fond je kolekce připojení, které lze znovu použít. 

Existuje několik připojení poolers můžete použít s PostgreSQL. Jedním z nich je [PgBouncer](https://pgbouncer.github.io/). V registru kontejnerů Microsoft poskytujeme lehký kontejnerizovaný PgBouncer, který lze použít v postranním vozíku pro fond připojení z AKS do databáze Azure pro PostgreSQL. Navštivte [stránku centra dockeru,](https://hub.docker.com/r/microsoft/azureossdb-tools-pgbouncer/) kde se dozvíte, jak získat přístup k této bitové kopii a jak ji používat. 


## <a name="next-steps"></a>Další kroky
-  [Vytvoření clusteru služby Azure Kubernetes](../aks/kubernetes-walkthrough.md)

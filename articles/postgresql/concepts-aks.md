---
title: Připojení k Azure Kubernetes Service – Azure Database for PostgreSQL-Single server
description: Seznamte se s připojením služby Azure Kubernetes Service (AKS) s Azure Database for PostgreSQLm na jeden server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.date: 5/6/2019
ms.topic: conceptual
ms.openlocfilehash: 46aa411826dd3ea578a2d98b0fe631ab0a12ef4a
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74769876"
---
# <a name="connecting-azure-kubernetes-service-and-azure-database-for-postgresql---single-server"></a>Připojení služby Azure Kubernetes a serveru Azure Database for PostgreSQL-Single server

Služba Azure Kubernetes Service (AKS) poskytuje spravovaný cluster Kubernetes, který můžete použít v Azure. Níže jsou uvedeny některé možnosti, které je třeba vzít v úvahu při použití AKS a Azure Database for PostgreSQL dohromady k vytvoření aplikace.


## <a name="accelerated-networking"></a>Urychlení sítě
V clusteru AKS použijte urychlené základní virtuální počítače s podporou sítě. Když je na virtuálním počítači povolené urychlení sítě, dojde k nižší latenci, snížení kolísání a snížení využití procesoru na VIRTUÁLNÍm počítači. Přečtěte si další informace o tom, jak akcelerovaná síť funguje, podporované verze operačního systému a podporované instance virtuálních počítačů pro [Linux](../virtual-network/create-vm-accelerated-networking-cli.md).

Od listopadu 2018 podporuje AKS urychlené síťové služby na těchto podporovaných instancích virtuálních počítačů. Akcelerované síťové služby jsou ve výchozím nastavení povolené v nových clusterech AKS, které tyto virtuální počítače používají.

Můžete potvrdit, jestli cluster AKS má urychlené síťové služby:
1. Přejít na Azure Portal a vybrat cluster AKS.
2. Vyberte kartu Vlastnosti.
3. Zkopírujte název **skupiny prostředků infrastruktury**.
4. Pomocí panelu hledání na portálu vyhledejte a otevřete skupinu prostředků infrastruktury.
5. Vyberte virtuální počítač v této skupině prostředků.
6. Přejít na kartu **síť** virtuálního počítače.
7. Potvrďte, jestli je povolená možnost **akcelerované síťové služby** .

Nebo přes rozhraní příkazového řádku Azure CLI pomocí následujících dvou příkazů:
```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query "nodeResourceGroup"
```
Výstupem bude vygenerovaná skupina prostředků, kterou AKS vytvoří, obsahující síťové rozhraní. Použijte název "nodeResourceGroup" a použijte ho v dalším příkazu. **EnableAcceleratedNetworking** bude buď true, nebo false:
```azurecli
az network nic list --resource-group nodeResourceGroup -o table
```

## <a name="open-service-broker-for-azure"></a>Technologie Open Service Broker for Azure 
[Open Service Broker pro Azure](https://github.com/Azure/open-service-broker-azure/blob/master/README.md) (OSBA) vám umožní zřídit služby Azure přímo z Kubernetes nebo Cloud Foundry. Jedná se o [Open Service Broker implementaci rozhraní API](https://www.openservicebrokerapi.org/) pro Azure.

Pomocí OSBA můžete vytvořit server Azure Database for PostgreSQL a vytvořit jeho propojení s clusterem AKS pomocí nativního jazyka Kubernetes. Přečtěte si, jak používat OSBA a Azure Database for PostgreSQL společně na [stránce GitHubu OSBA](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/postgresql.md). 


## <a name="connection-pooling"></a>Sdružování připojení
Pooler připojení minimalizuje náklady a čas spojený s vytvářením a uzavíráním nových připojení k databázi. Fond je kolekce připojení, která se dají znovu použít. 

S PostgreSQL můžete používat víc fondů připojení. Jedna z těchto [PgBouncer](https://pgbouncer.github.io/). V Microsoft Container Registry poskytujeme odlehčenou kontejnerovou PgBounceri, která se dá použít v postranním vozíku pro vytvoření fondu připojení z AKS k Azure Database for PostgreSQL. Navštivte [stránku Docker Hub](https://hub.docker.com/r/microsoft/azureossdb-tools-pgbouncer/) , kde se dozvíte, jak tento obrázek získat a jak ho používat. 


## <a name="next-steps"></a>Další kroky
-  [Vytvoření clusteru služby Azure Kubernetes](../aks/kubernetes-walkthrough.md)

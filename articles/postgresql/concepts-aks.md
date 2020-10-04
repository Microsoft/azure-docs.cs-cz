---
title: Připojení k Azure Kubernetes Service – Azure Database for PostgreSQL-Single server
description: Seznamte se s připojením služby Azure Kubernetes Service (AKS) s Azure Database for PostgreSQLm na jeden server.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.date: 07/14/2020
ms.topic: conceptual
ms.openlocfilehash: 9b7da2fcc1310f03f894e048089658f25be3a149
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2020
ms.locfileid: "91708845"
---
# <a name="connecting-azure-kubernetes-service-and-azure-database-for-postgresql---single-server"></a>Připojení služby Azure Kubernetes a serveru Azure Database for PostgreSQL-Single server

Služba Azure Kubernetes Service (AKS) poskytuje spravovaný cluster Kubernetes, který můžete použít v Azure. Níže jsou uvedeny některé možnosti, které je třeba vzít v úvahu při použití AKS a Azure Database for PostgreSQL dohromady k vytvoření aplikace.


## <a name="accelerated-networking"></a>Urychlení sítě
V clusteru AKS použijte urychlené základní virtuální počítače s podporou sítě. Když je na virtuálním počítači povolené urychlení sítě, dojde k nižší latenci, snížení kolísání a snížení využití procesoru na VIRTUÁLNÍm počítači. Přečtěte si další informace o tom, jak akcelerovaná síť funguje, podporované verze operačního systému a podporované instance virtuálních počítačů pro [Linux](../virtual-network/create-vm-accelerated-networking-cli.md).

Od listopadu 2018 podporuje AKS urychlené síťové služby na těchto podporovaných instancích virtuálních počítačů. Akcelerované síťové služby jsou ve výchozím nastavení povolené v nových clusterech AKS, které tyto virtuální počítače používají.

Můžete potvrdit, jestli cluster AKS má urychlené síťové služby:
1. Přejít na Azure Portal a vybrat cluster AKS.
2. Vyberte kartu Properties (Vlastnosti).
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


## <a name="connection-pooling"></a>Sdružování připojení
Pooler připojení minimalizuje náklady a čas spojený s vytvářením a uzavíráním nových připojení k databázi. Fond je kolekce připojení, která se dají znovu použít. 

S PostgreSQL můžete používat víc fondů připojení. Jedna z těchto [PgBouncer](https://pgbouncer.github.io/). V Microsoft Container Registry poskytujeme odlehčenou kontejnerovou PgBounceri, která se dá použít v postranním vozíku pro vytvoření fondu připojení z AKS k Azure Database for PostgreSQL. Navštivte [stránku Docker Hub](https://hub.docker.com/r/microsoft/azureossdb-tools-pgbouncer/) , kde se dozvíte, jak tento obrázek získat a jak ho používat. 


## <a name="next-steps"></a>Další kroky
-  [Vytvoření clusteru služby Azure Kubernetes Service](../aks/kubernetes-walkthrough.md)

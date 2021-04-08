---
title: Připojení ke službě Azure Kubernetes – Azure Database for MySQL
description: Seznamte se s připojením služby Azure Kubernetes pomocí Azure Database for MySQL
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 07/14/2020
ms.openlocfilehash: 5c4a5f5d792a60ed3fef07797fdbdfa0c9cfb8fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94534326"
---
# <a name="connecting-azure-kubernetes-service-and-azure-database-for-mysql"></a>Připojení služby Azure Kubernetes a Azure Database for MySQL

Služba Azure Kubernetes Service (AKS) poskytuje spravovaný cluster Kubernetes, který můžete použít v Azure. Níže jsou uvedeny některé možnosti, které je třeba vzít v úvahu při použití AKS a Azure Database for MySQL dohromady k vytvoření aplikace.


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


## <a name="next-steps"></a>Další kroky
- [Vytvoření clusteru služby Azure Kubernetes Service](../aks/kubernetes-walkthrough.md)
- Naučte se [instalovat WordPress z grafu Helm pomocí OSBA a Azure Database for MySQL](../aks/index.yml)
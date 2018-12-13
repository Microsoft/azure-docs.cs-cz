---
title: Připojení Azure Kubernetes Service (AKS) se službou Azure Database for MySQL
description: Další informace o připojení služby Azure Kubernetes s využitím Azure Database for MySQL
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 11/28/2018
ms.openlocfilehash: baba85aeb800dd8effe3be295b2149179604b41d
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53164836"
---
# <a name="connecting-azure-kubernetes-service-and-azure-database-for-mysql"></a>Připojení Azure Kubernetes Service a Azure Database for MySQL

Azure Kubernetes Service (AKS) poskytuje spravovaného clusteru Kubernetes, který můžete použít v Azure. V následující tabulce jsou některé možnosti, které je třeba zvážit při používání AKS a Azure Database for MySQL společně k vytvoření aplikace.


## <a name="accelerated-networking"></a>Urychlení sítě
Použití urychlení sítě povolené základní virtuální počítače v clusteru AKS. Pokud akcelerovaných síťových služeb je povoleno na virtuálním počítači, je nižší latenci, zmenšit zpoždění a snížit využití procesoru na virtuálním počítači. Další informace o akcelerovaných síťových funguje, podporované verze operačního systému a podporované instance virtuálních počítačů pro [Linux](../virtual-network/create-vm-accelerated-networking-cli.md).

Od listopadu 2018 AKS podporuje akcelerované síťové služby na tyto podporované instance virtuálních počítačů. Ve výchozím nastavení nových clusterů AKS, které používají tyto virtuální počítače je povolená akcelerované síťové služby.

Můžete ověřit, zda váš cluster AKS akcelerovanými síťovými službami:
1. Přejděte na web Azure Portal a vyberte svůj cluster AKS.
2. Vyberte na kartě Vlastnosti.
3. Zkopírujte název **skupina prostředků infrastruktury**.
4. Najděte a otevřete skupinu prostředků infrastruktury pomocí panelu hledání portálu.
5. Vyberte virtuální počítač v příslušné skupině prostředků.
6. Přejděte do Virtuálního počítače **sítě** kartu.
7. Ověřte, zda **akcelerované síťové služby** "Zapnutý."


## <a name="open-service-broker-for-azure"></a>Technologie Open Service Broker for Azure 
[Otevřete Service Broker for Azure](https://github.com/Azure/open-service-broker-azure/blob/master/README.md) (OSBA) umožňuje zřizovat služby Azure přímo z Kubernetes nebo Cloud Foundry. Jde [otevřená rozhraní API služby Service Broker](https://www.openservicebrokerapi.org/) implementace pro Azure.

Osba můžete vytvořit serveru Azure Database for MySQL a svázat cluster AKS pomocí Kubernetes' rodném jazyce. Další informace o tom, jak používat OSBA a Azure Database for MySQL společně na [stránku Githubu OSBA](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/mysql.md). 



## <a name="next-steps"></a>Další postup
- [Vytvoření clusteru Azure Kubernetes Service](../aks/kubernetes-walkthrough.md)
- Zjistěte, jak [instalace Wordpressu z grafu helmu využitím OSBA a Azure Database for MySQL](../aks/integrate-azure.md)

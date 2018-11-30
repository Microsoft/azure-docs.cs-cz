---
title: Připojení Azure Kubernetes Service (AKS) se službou Azure Database for PostgreSQL
description: Další informace o připojení služby Azure Kubernetes s využitím Azure Database for PostgreSQL
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.date: 11/27/2018
ms.topic: article
ms.openlocfilehash: 86474fe612fb93f3a5853f9fea98eb9ab2dd00e5
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2018
ms.locfileid: "52336807"
---
# <a name="connecting-azure-kubernetes-service-and-azure-database-for-postgresql"></a>Připojení Azure Kubernetes Service a Azure Database for PostgreSQL

Azure Kubernetes Service (AKS) poskytuje spravovaného clusteru Kubernetes, který můžete použít v Azure. V následující tabulce jsou některé možnosti, které je třeba zvážit při použití společně AKS a Azure Database for postgresql – vytvoření aplikace.


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

Osba můžete vytvořit serveru Azure Database for PostgreSQL a svázat cluster AKS pomocí Kubernetes' rodném jazyce. Další informace o tom, jak používat OSBA a Azure Database for PostgreSQL společně na [stránku Githubu OSBA](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/postgresql.md). 


## <a name="connection-pooling"></a>Sdružování připojení
Pro sdružování připojení minimalizuje náklady a čas přidružený k vytváření a uzavírání nová připojení k databázi. Fond je kolekce připojení, které je možné využít znovu. 

Existuje více poolers připojení, která vám pomůže s PostgreSQL. Jedním z nich je [PgBouncer](https://pgbouncer.github.io/). V registru kontejneru Microsoft zajišťuje zjednodušené kontejnerizovaných PgBouncer, který lze použít v sajdkáru k fondu připojení z AKS ke službě Azure Database for PostgreSQL. Přejděte [stránka centra dockeru](https://hub.docker.com/r/microsoft/azureossdb-tools-pgbouncer/) se naučíte přistupovat a používat tuto bitovou kopii. 


## <a name="next-steps"></a>Další postup
-  [Vytvoření clusteru Azure Kubernetes Service](../aks/kubernetes-walkthrough.md)

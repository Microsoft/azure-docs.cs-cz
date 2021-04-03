---
title: Vysoká dostupnost pro kontejnery Azure SQL Edge – Azure SQL Edge
description: Další informace o vysoké dostupnosti pro kontejnery Azure SQL Edge
keywords: SQL Edge, kontejnery, vysoká dostupnost
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 56b9f06547f737bc05d573f98ce1dbac2ba48758
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "90935220"
---
# <a name="high-availability-for-azure-sql-edge-containers"></a>Vysoká dostupnost pro kontejnery Azure SQL Edge

Vytvářejte a spravujte instance Azure SQL Edge nativně v Kubernetes. Nasaďte Azure SQL Edge do kontejnerů Docker spravovaných přes [Kubernetes](https://kubernetes.io/). V Kubernetes se může kontejner s instancí Azure SQL Edge automaticky obnovit v případě, že uzel clusteru nebude úspěšný. Image kontejneru SQL Edge se dá nakonfigurovat pomocí deklarace identity trvalého svazku Kubernetes (PVC). Kubernetes monitoruje proces Azure SQL Edge v kontejneru. Pokud se proces, pod, kontejner nebo uzel nezdaří, Kubernetes automaticky zavede jinou instanci a znovu se připojí k úložišti.

## <a name="azure-sql-edge-containers-on-kubernetes"></a>Kontejnery Azure SQL Edge na Kubernetes

Kubernetes 1,6 a novější má podporu pro [*třídy úložiště*](https://kubernetes.io/docs/concepts/storage/storage-classes/), [*trvalé deklarace svazků*](https://kubernetes.io/docs/concepts/storage/storage-classes/#persistentvolumeclaims).

V této konfiguraci Kubernetes hraje roli produktu Orchestrator kontejneru. 

![Diagram Azure SQL Edge v clusteru Kubernetes](media/deploy-kubernetes/kubernetes-sql-edge.png)

V předchozím diagramu `azure-sql-edge` je kontejner v poli [pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/). Kubernetes orchestruje prostředky v clusteru. [Sada replik](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/) zajišťuje, že se po selhání uzlu automaticky obnoví uzel pod. Aplikace se připojují ke službě. V tomto případě služba představuje nástroj pro vyrovnávání zatížení, který hostuje IP adresu, která zůstává stejná i po selhání `azure-sql-edge` .

V následujícím diagramu `azure-sql-edge` se kontejner nezdařil. Nástroj Orchestrator Kubernetes garantuje správný počet zdravých instancí v sadě replik a spustí nový kontejner podle konfigurace. Nástroj Orchestrator spustí nový uzel pod stejným uzlem a `azure-sql-edge` znovu se připojí ke stejnému trvalému úložišti. Služba se připojí k znovu vytvořenému `azure-sql-edge` .

![Azure SQL Edge v clusteru Kubernetes po selhání pod](media/deploy-kubernetes/kubernetes-sql-edge-after-pod-fail.png)

V následujícím diagramu `azure-sql-edge` se nezdařil uzel hostující kontejner. Nástroj Orchestrator spustí nový uzel pod jiným uzlem a `azure-sql-edge` znovu se připojí ke stejnému trvalému úložišti. Služba se připojí k znovu vytvořenému `azure-sql-edge` .

![Azure SQL Edge v clusteru Kubernetes po selhání uzlu](media/deploy-kubernetes/kubernetes-sql-edge-after-node-fail.png).

Postup vytvoření kontejneru v Kubernetes najdete v tématu [nasazení kontejneru Azure SQL Edge v Kubernetes](deploy-Kubernetes.md) .

## <a name="next-steps"></a>Další kroky

Postup nasazení kontejnerů Azure SQL Edge ve službě Azure Kubernetes Service (AKS) najdete v následujících článcích:
- [Nasazení kontejneru Edge Azure SQL v Kubernetes](deploy-Kubernetes.md)
- [Machine Learning a uměle inteligentní informace s ONNXem v SQL Edge](onnx-overview.md).
- [Vytvoření kompletního řešení IoT pomocí SQL Edge pomocí IoT Edge](tutorial-deploy-azure-resources.md).
- [Streamování dat ve službě Azure SQL Edge](stream-data.md)
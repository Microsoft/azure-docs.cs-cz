---
title: Migrace na aktualizované API služby Azure Batch AI | Dokumentace Microsoftu
description: Postup aktualizace Azure Batch AI kódu a skriptů pro pracovní prostor a experimentovat prostředky
services: batch-ai
documentationcenter: na
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 06/08/2018
ms.author: danlep
ROBOTS: NOINDEX
ms.openlocfilehash: 75a9a5e9bafd62b320397c00ef6574b7536d9e09
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2018
ms.locfileid: "53407776"
---
# <a name="migrate-to-the-updated-batch-ai-api"></a>Migrace na aktualizované rozhraní API služby Batch AI

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

V rozhraní REST API verze 2018-05-01 služby Batch AI a souvisejících sad SDK služby Batch AI a nástroje byly zavedeny nové funkce a významné změny.

Pokud používáte předchozí verzi rozhraní API služby Batch AI, tento článek vysvětluje, jak upravit vašeho kódu a skriptů pro práci s nové rozhraní API. 

## <a name="whats-changing"></a>Co se mění?

V reakci na zpětnou vazbu od zákazníků jsme se odebrání omezení počtu úloh a usnadnit tak spravovat prostředky služby Batch AI. Nové rozhraní API představuje dva nové prostředky, *pracovní prostor* a *experimentovat*. Úlohy související školení v rámci experimentu shromažďování a uspořádání všech souvisejících prostředků služby Batch AI (clustery souborových serverů, experimenty, úlohy) v pracovním prostoru.  

* **Pracovní prostor** -kolekci nejvyšší úrovně všech typů prostředků služby Batch AI. Clusterů a souborových serverů jsou obsaženy v pracovním prostoru. Pracovní prostory obvykle samostatné pracovní patřící do různých skupin nebo projekty. Například můžete mít vývojář a zkušební pracovní prostor. Bude pravděpodobně nutné pouze omezený počet pracovních prostorů na předplatné. 

* **Experiment** -sadu související úlohy, které jde dotazovat a spravovaných společně. Experiment můžete například použijte k seskupení všechny úlohy, které se provádí jako součást ladění úklidu hyperparametrické. 

Následující obrázek ukazuje hierarchii příklad prostředků. 

![](./media/migrate-to-new-api/batch-ai-resource-hierarchy.png)

## <a name="monitor-and-manage-existing-resources"></a>Monitorování a Správa existujících prostředků
V každé skupině prostředků, ve které jste již vytvořili clustery, úlohy nebo souborové servery Batch AI, služba Batch AI vytvoří pracovní prostor s názvem `migrated-<region>` (například `migrated-eastus`) a experimentu s názvem `migrated`. Pro přístup k dříve vytvořené úlohy, clustery nebo souborové servery, budete muset použít migrované pracovní prostor a experimentovat. 

### <a name="portal"></a>Portál 
Přístup k dříve vytvořené úlohy, clusterů a souborových serverů pomocí portálu, vyberte nejdřív `migrated-<region>` pracovního prostoru. Jakmile vyberete pracovní prostor, proveďte operace, jako je změna velikosti nebo odstranění clusteru a zobrazení stavu úlohy a výstupy. 

### <a name="sdks"></a>Sady SDK 
Přístup k úlohy, clusterů a souborových serverů se dříve vytvořených prostřednictvím sad SDK služby Batch AI, zadejte název pracovního prostoru a experimentovat parametr name. 

Pokud používáte Python SDK, důležité změny jsou uvedeny v následujících příkladech. Změny jsou podobné jako u jiných SDK služby Batch AI. 


#### <a name="get-old-cluster"></a>Získání původní cluster 

```python
cluster = client.clusters.get(resource_group_name, 'migrated-<region>', cluster_name)
```

#### <a name="delete-old-cluster"></a>Odstranění původního clusteru 

```python
client.clusters.delete(resource_group_name, 'migrated-<region>', cluster_name)
```

#### <a name="get-old-file-server"></a>Získání staré souborového serveru 

```python
cluster = client.fileservers.get(resource_group_name, 'migrated-<region>', fileserver_name)
```

#### <a name="delete-old-file-server"></a>Odstranit staré souborového serveru  

```python
client.fileservers.delete(resource_group_name, 'migrated-<region>', fileserver_name)
``` 


#### <a name="get-old-job"></a>Získání staré úlohy 

```python
cluster = client.jobs.get(resource_group_name, 'migrated-<region>', 'migrated', job_name)
```

#### <a name="delete-old-job"></a>Odstranit staré úlohy

```python
client.jobs.delete(resource_group_name, 'migrated-<region>', 'migrated', job_name)
```
 
### <a name="azure-cli"></a>Azure CLI 
 
Při použití rozhraní příkazového řádku Azure pro přístup k dříve vytvořené úlohy, clustery nebo souborové servery, použijte `-w` a `-e` parametry zadat pracovní prostor a experimentovat názvy. 


#### <a name="get-old-cluster"></a>Získání původní cluster

```azurecli
az batchai cluster show -g resource-group-name -w migrated-<region> -n cluster-name
```


#### <a name="delete-old-cluster"></a>Odstranění původního clusteru 

```azurecli
az batchai cluster delete -g resource-group-name -w migrated-<region> -n cluster-name
```

#### <a name="get-old-file-server"></a>Získání staré souborového serveru

```azurecli
az batchai file-server show -g resource-group-name -w migrated-<region> -n fileserver-name
```


#### <a name="delete-old-file-server"></a>Odstranit staré souborového serveru 

```azurecli
az batchai file-server delete -g resource-group-name -w migrated-<region> -n fileserver-name
``` 


#### <a name="get-old-job"></a>Získání staré úlohy

```azurecli
az batchai job show -g resource-group-name -w migrated-<region> -e migrated -n job-name
```


#### <a name="delete-old-job"></a>Odstranit staré úlohy 

```azurecli
az batchai job delete -g resource-group-name -w migrated-<region> -e migrated -n job-name
``` 

## <a name="create-batch-ai-resources"></a>Vytvořit prostředky služby Batch AI 
 
Pokud pracujete s některou ze stávajících sad SDK služby Batch AI, můžete nadále používat k vytváření prostředků služby Batch AI (úloh, clustery nebo souborové servery) bez změn kódu. Ale po upgradu na novou sadu SDK, budete muset provést následující změny.
 
### <a name="create-workspace"></a>Vytvoření pracovního prostoru 
V závislosti na vašem scénáři můžete vytvořit pracovní prostor ručně jednorázové prostřednictvím portálu nebo rozhraní příkazového řádku. Pokud používáte rozhraní příkazového řádku, vytvořte pracovní prostor pomocí následujícího příkazu: 

```azurecli
az batchai workspace create -g resource-group-name -n workspace-name
```

### <a name="create-experiment"></a>Vytvoření experimentu 


V závislosti na vašem scénáři můžete vytvořit experiment ručně jednorázové prostřednictvím portálu nebo rozhraní příkazového řádku. Pokud používáte rozhraní příkazového řádku, vytvoření experimentu pomocí následujícího příkazu: 

```azurecli
az batchai experiment create -g resource-group-name -w workspace-name -n experiment-name

```

### <a name="create-clusters-file-servers-and-jobs"></a>Vytvoření clusterů souborových serverů a úloh
 
Pokud používáte portál k vytvoření úlohy, clustery nebo souborové servery, na portálu vás provede během vytváření prostředí pro zadejte název pracovního prostoru a experimentovat parametr name.

Vytvoření úlohy, clustery nebo souborové servery prostřednictvím aktualizované sady SDK, zadejte parametr název pracovního prostoru. Pokud používáte Python SDK, důležité změny jsou uvedeny v následujících příkladech. Nahraďte *workspace_name* a *experiment_name* se pracovní prostor a experiment, který jste vytvořili dříve. Změny jsou podobné jako u jiných SDK služby Batch AI. 


#### <a name="create-cluster"></a>Vytvoření clusteru 

```python
_ = client.clusters.create(resource_group_name, workspace_name, cluster_name, cluster_create_parameters).result()
```

#### <a name="create-file-server"></a>Vytvoření souborového serveru 

```python
_ = client.fileservers.create(resource_group_name, workspace_name, fileserver_name, fileserver_create_parameters).result()
```

#### <a name="create-job"></a>Vytvoření úlohy 

```python
_ = client.jobs.create(resource_group_name, workspace_name, experiment_name, job_name job_create_parameters).result()
```


## <a name="next-steps"></a>Další postup

* Přečtěte si referenční informace rozhraní API služby Batch AI: [Rozhraní příkazového řádku](/cli/azure/batchai), [.NET](/dotnet/api/overview/azure/batchai), [Java](/java/api/overview/azure/batchai), [Node.js](/javascript/api/overview/azure/batchai), [Python](/python/api/overview/azure/batchai), a [REST](/rest/api/batchai)
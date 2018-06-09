---
title: Migrovat aktualizované Azure Batch AI API | Microsoft Docs
description: Postup aktualizace Azure Batch AI kódů a skriptů pomocí pracovního prostoru a Experimentujte prostředky
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
ms.openlocfilehash: 94db54f35b7871407368b174536dc6454775779c
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "35249255"
---
# <a name="migrate-to-the-updated-batch-ai-api"></a>Migrovat aktualizované Batch API AI

V nadcházejících Batch AI REST API verze 2018-05-01 a související SDK služby Batch AI a nástroje jsou uvedena významné změny a nové funkce.

Pokud jste použili předchozí verzi rozhraní API služby Batch AI, tento článek vysvětluje, jak upravit kód a skripty pro práci s novým rozhraním API. Tyto změny proveďte až po nové rozhraní API je k dispozici.

## <a name="whats-changing"></a>Co je změna?

V reakci na názory zákazníků jsme se odebírá omezení počtu úloh a usnadnit tak spravovat prostředky Batch AI. Nové rozhraní API představuje dva nové prostředky, *prostoru* a *experimentovat*. Shromažďování školení související úlohy v rámci experimentu a uspořádat všechny související prostředky Batch AI (clusterů souborových serverů, experimenty, úlohy) v pracovním prostoru.  

* **Pracovní prostor** -nejvyšší úrovně kolekce všechny typy prostředků Batch AI. Clusterů a souborových serverů jsou obsaženy v pracovním prostoru. Pracovní prostory obvykle samostatné pracovní patřící do různých skupin nebo projekty. Například můžete mít vývoj a testovací prostoru. Bude pravděpodobně nutné jenom omezený počet pracovních prostorů jedno předplatné. 

* **Experiment** -kolekce souvisejících úloh, které mohou být dotazována a spravují dohromady. Experimentu můžete například použijte k seskupení všechny úlohy, které se provádí v rámci vyladění oblouku hyper parametr. 

Následující obrázek znázorňuje příklad hierarchie prostředků. 

![](./media/migrate-to-new-api/batch-ai-resource-hierarchy.png)

## <a name="monitor-and-manage-existing-resources"></a>Sledování a správě existujících prostředků
V každé skupině prostředků, kde jste již vytvořili Batch AI clustery, úlohy nebo souborové servery, bude služba Batch AI vytvořit pracovní prostor s názvem `migrated-<region>` (například `migrated-eastus`) a experimentu s názvem `migrated`. Pro přístup k dřív vytvořených úloh, clustery nebo souborové servery, musíte použít migrované pracovní prostor a Experimentujte. 

### <a name="portal"></a>Portál 
Přístup k dřív vytvořených úloh, clusterů a souborových serverů pomocí portálu, vyberte nejdřív `migrated-<region>` pracovního prostoru. Vyberte pracovní prostor a proveďte operace, jako je například změna velikosti nebo odstranění clusteru a zobrazit stav úlohy a výstupy. 

### <a name="sdks"></a>Sady SDK 
Přístup k úlohy, clusterů a souborových serverů, které dříve vytvořené prostřednictvím sady SDK služby Batch AI, zadejte název pracovního prostoru a experimentovat název parametry. 

Pokud používáte sady SDK pro Python, v následujících příkladech jsou zobrazeny příslušné změny. Změny se podobají jiné AI SDK služby Batch. 


#### <a name="get-old-cluster"></a>Získat původního clusteru 

```python
cluster = client.clusters.get(resource_group_name, 'migrated-<region>', cluster_name)
```

#### <a name="delete-old-cluster"></a>Odstranění původního clusteru 

```python
client.clusters.delete(resource_group_name, 'migrated-<region>', cluster_name)
```

#### <a name="get-old-file-server"></a>Získat staré souborového serveru 

```python
cluster = client.fileservers.get(resource_group_name, 'migrated-<region>', fileserver_name)
```

#### <a name="delete-old-file-server"></a>Odstranit staré souborového serveru  

```python
client.fileservers.delete(resource_group_name, 'migrated-<region>', fileserver_name)
``` 


#### <a name="get-old-job"></a>Získat staré úlohu 

```python
cluster = client.jobs.get(resource_group_name, 'migrated-<region>', 'migrated', job_name)
```

#### <a name="delete-old-job"></a>Odstranit staré úlohy

```python
client.jobs.delete(resource_group_name, 'migrated-<region>', 'migrated', job_name)
```
 
### <a name="azure-cli"></a>Azure CLI 
 
Při použití rozhraní příkazového řádku Azure k přístupu vytvořili úlohy, clusterům nebo souborové servery, `-w` a `-e` parametry zadejte prostoru a experimentovat názvy. 


#### <a name="get-old-cluster"></a>Získat původního clusteru

```azurecli
az batchai cluster show -g resource-group-name -w migrated-<region> -n cluster-name
```


#### <a name="delete-old-cluster"></a>Odstranění původního clusteru 

```azurecli
az batchai cluster delete -g resource-group-name -w migrated-<region> -n cluster-name
```

#### <a name="get-old-file-server"></a>Získat staré souborového serveru

```azurecli
az batchai fileserver show -g resource-group-name -w migrated-<region> -n fileserver-name
```


#### <a name="delete-old-file-server"></a>Odstranit staré souborového serveru 

```azurecli
az batchai fileserver delete -g resource-group-name -w migrated-<region> -n fileserver-name
``` 


#### <a name="get-old-job"></a>Získat staré úlohu

```azurecli
az batchai fileserver show -g resource-group-name -w migrated-<region> -e migrated -n job-name
```


#### <a name="delete-old-job"></a>Odstranit staré úlohy 

```azurecli
az batchai fileserver delete -g resource-group-name -w migrated-<region> -e migrated -n job-name
``` 

## <a name="create-batch-ai-resources"></a>Vytvořit prostředky Batch AI 
 
Pokud používáte některou existující sady SDK AI Batch, můžete použít k vytvoření dávky AI prostředky (úlohy, clusterům nebo souborové servery) bez provedení změn kódu. Ale po upgradu na novou sadu SDK, musíte provést následující změny.
 
### <a name="create-workspace"></a>Vytvořit pracovní prostor 
V závislosti na scénáři můžete vytvořit pracovní prostor ručně jednorázové prostřednictvím portálu nebo rozhraní příkazového řádku. Pokud používáte rozhraní příkazového řádku, vytvořte pracovní prostor pomocí následujícího příkazu: 

```azurecli
az batchai workspace create -g resource-group-name -n workspace-name
```

### <a name="create-experiment"></a>Vytvoření experimentu 


V závislosti na scénáři můžete vytvořit experimentu ručně jednorázové prostřednictvím portálu nebo rozhraní příkazového řádku. Pokud používáte rozhraní příkazového řádku, vytvořte nový experiment, pomocí následujícího příkazu: 

```azurecli
az batchai experiment create -g resource-group-name -w workspace-name -n experiment-name

```

### <a name="create-clusters-file-servers-and-jobs"></a>Vytváření clusterů souborových serverů a úloh
 
Pokud používáte portál k vytvoření úlohy, clusterům nebo souborové servery, na portálu vám pomohou při vytváření prostředí a zadejte název pracovního prostoru experimentovat název parametry.

K vytvoření úlohy, clusterům nebo souborové servery prostřednictvím aktualizované SDK, zadejte parametr název pracovního prostoru. Pokud používáte sady SDK pro Python, v následujících příkladech jsou zobrazeny příslušné změny. Nahraďte *workspace_name* a *experiment_name* s pracovní prostor a experimentu, který jste vytvořili dříve. Změny se podobají jiné AI SDK služby Batch. 


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

* V tématu referenční dokumentace rozhraní API AI Batch: [rozhraní příkazového řádku](/cli/azure/batchai), [.NET](/dotnet/api/overview/azure/batchai), [Java](/java/api/overview/azure/batchai), [Node.js](/javascript/api/overview/azure/batchai), [Python](/python/api/overview/azure/batchai)a [REST](/rest/api/batchai)
---
title: Jak zvýšit odolnost
titleSuffix: Azure Machine Learning
description: Naučte se, jak zajistit, aby se prostředky související s Azure Machine Learning lépe odolné vůči výpadkům pomocí konfigurace s vysokou dostupností.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 07/16/2020
ms.openlocfilehash: 4f2bf239e1157f5c927c4449857ad5f7793ccb49
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87097225"
---
# <a name="increase-the-resiliency-of-azure-machine-learning"></a>Zvýšit odolnost Azure Machine Learning

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Naučte se, jak zvýšit odolnost prostředků souvisejících s Azure Machine Learning pomocí konfigurací s vysokou dostupností. Služby Azure, na kterých Azure Machine Learning závisí, se dají nakonfigurovat pro vysokou dostupnost. Tento článek obsahuje informace o tom, jaké služby je možné nakonfigurovat pro vysokou dostupnost, a odkazy na informace o konfiguraci těchto prostředků.

> [!NOTE]
> Azure Machine Learning sám o sobě nenabízí možnost zotavení po havárii.

## <a name="understand-azure-services-for-azure-machine-learning"></a>Principy služeb Azure pro Azure Machine Learning

Azure Machine Learning závisí na několika službách Azure a má několik vrstev. Některé z nich jsou zřízené v rámci předplatného (zákazníka). Zodpovídáte za konfiguraci vysoké dostupnosti těchto služeb. Některé jsou vytvořené v rámci předplatného Microsoft a spravují se od Microsoftu.

* **Azure Machine Learning infrastruktura**: prostředí spravované Microsoftem pro Azure Machine Learning pracovní prostor.

* **Přidružené prostředky**: prostředky zřízené ve vašem předplatném během vytváření pracovního prostoru Azure Machine Learning. Zahrnují Azure Storage, Azure Key Vault, Azure Container Registry (ACR) a App Insights. Zodpovídáte za nastavení vysoké dostupnosti pro tyto prostředky.
  * Výchozí úložiště obsahuje data, jako je model, školení a data protokolů a datovou sadu.
  * Key Vault má přihlašovací údaje pro úložiště, ACR a úložiště dat.
  * ACR má Docker image pro školení a Inferencing prostředí.
  * App Insights slouží k monitorování Azure Machine Learning.

* **Výpočetní prostředky**: prostředky, které vytvoříte po nasazení pracovního prostoru. Můžete například vytvořit výpočetní instanci nebo výpočetní cluster pro výuku modelu strojového učení.
  * Výpočetní instance a výpočetní cluster: prostředí pro vývoj modelů spravovaných Microsoftem.
  * Další prostředky: Jedná se o výpočetní prostředky, které je možné připojit k Azure Machine Learning, jako je Azure Kubernetes Service (AKS), Azure Databricks, Azure Container Instances (ACI) a HDInsight. Zodpovídáte za nastavení vysoké dostupnosti.

* **Další úložiště dat**: Azure Machine Learning můžou připojit další úložiště dat, jako jsou Azure Storage, Azure Data Lake Storage a Azure SQL Database pro školení dat.  Jsou v rámci vašeho předplatného a zodpovídáte za nastavení vysoké dostupnosti.

Následující tabulka uvádí, které služby spravuje Microsoft, které jsou spravované vámi a které jsou ve výchozím nastavení vysoce dostupné:

| Služba | Spravuje | HA ve výchozím nastavení |
| ----- | ----- | ----- |
| **Azure Machine Learning infrastruktura** | Partnerský vztah Microsoftu | |
| **Přidružené prostředky** |
| Azure Storage | Vy | |
| Azure Key Vault | Vy | ✓ |
| Azure Container Registry | Vy | |
| Application Insights | Vy | Není k dispozici |
| **Výpočetní prostředky** |
| Instance COMPUTE | Partnerský vztah Microsoftu |  |
| Výpočetní cluster | Partnerský vztah Microsoftu |  |
| Další prostředky, jako je služba Azure Kubernetes, <br>Azure Databricks, instance kontejneru Azure, Azure HDInsight | Vy |  |
| **Další úložiště dat** , například Azure Storage, Azure SQL Database<br> Azure Database for PostgreSQL, Azure Database for MySQL, <br>Azure Databricks systému souborů | Vy | |

Informace ve zbývající části tohoto dokumentu použijte k získání informací o akcích potřebných k tomu, aby byly jednotlivé služby vysoce dostupné.

## <a name="associated-resources"></a>Přidružené prostředky

> [!IMPORTANT]
> Azure Machine Learning nepodporuje převzetí služeb při selhání ve výchozím účtu úložiště pomocí geograficky redundantního úložiště (GRS) nebo geograficky redundantního úložiště (GZRS) nebo geograficky redundantního úložiště s přístupem pro čtení (RA-GRS) nebo geograficky redundantního úložiště s přístupem pro čtení (RA-GZRS).

Ujistěte se, že nastavení vysoké dostupnosti každého prostředku s těmito informacemi.

* **Azure Storage**: konfiguraci nastavení vysoké dostupnosti najdete v tématu [Azure Storage redundance](https://docs.microsoft.com/azure/storage/common/storage-redundancy).
* **Azure Key Vault**: poskytuje výchozí službu vysoké dostupnosti a není nutná žádná akce uživatele.  Viz [dostupnost a redundance Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/disaster-recovery-guidance).
* **Azure Container Registry**: vyberte SKU úrovně Premium pro geografickou replikaci. Podívejte [se na geografickou replikaci v Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication).
* **Application Insights**: neposkytuje nastavení vysoké dostupnosti. Dobu uchovávání dat a podrobnosti můžete upravit v [Application Insights shromažďování dat, uchovávání a ukládání](https://docs.microsoft.com/azure/azure-monitor/app/data-retention-privacy#how-long-is-the-data-kept).

## <a name="compute-resources"></a>Výpočetní prostředky

Ujistěte se, že je nastavení vysoké dostupnosti každého prostředku pod dokumentací.

* **Služba Azure Kubernetes**: Podívejte se na [osvědčené postupy pro zajištění kontinuity podnikových procesů a zotavení po havárii ve službě Azure KUBERNETES Service (AKS)](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region) a [vytvořte cluster služby Azure Kubernetes (AKS), který používá zóny dostupnosti](https://docs.microsoft.com/azure/aks/availability-zones). Pokud byl cluster AKS vytvořen pomocí nástroje Azure Machine Learning (pomocí studia, sady SDK nebo rozhraní příkazového řádku), vysoká dostupnost mezi oblastmi není podporována.
* **Azure Databricks**: Přečtěte si téma [místní zotavení po havárii pro clustery Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/howto-regional-disaster-recovery).
* **Instance kontejneru Azure**: ACI Orchestrator zodpovídá za převzetí služeb při selhání. Viz [Azure Container Instances a orchestrace kontejnerů](https://docs.microsoft.com/azure/container-instances/container-instances-orchestrator-relationship).
* **Azure HDInsight**: Podívejte se [na služby vysoké dostupnosti podporované službou Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-high-availability-components).

## <a name="additional-data-stores"></a>Další úložiště dat

Ujistěte se, že je nastavení vysoké dostupnosti každého prostředku pod dokumentací.

* **Kontejner objektů blob Azure/sdílená složka Azure/Azure Data Lake Gen2**: stejné jako výchozí úložiště.
* **Azure Data Lake Gen1**:[pokyny pro zotavení po havárii pro data v Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-disaster-recovery-guidance).
* **Azure SQL Database**: Přečtěte si informace o [vysoké dostupnosti a Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-high-availability).
* **Azure Database for PostgreSQL**: viz [Koncepty vysoké dostupnosti v Azure Database for PostgreSQL na jednom serveru](https://docs.microsoft.com/azure/postgresql/concepts-high-availability).
* **Azure Database for MySQL**: Přečtěte si [vysvětlení kontinuity podnikových aplikací v Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/concepts-business-continuity).
* **Systém souborů datacihly**: Přečtěte si téma [místní zotavení po havárii pro clustery Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/howto-regional-disaster-recovery).

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

Pokud zadáte vlastní klíč (klíč spravovaný zákazníkem) pro nasazení Azure Machine Learning pracovního prostoru, Cosmos DB se taky zřídí v rámci vašeho předplatného. V takovém případě zodpovídáte za jeho vysokou dostupnost. Zobrazit [vysokou dostupnost pomocí Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/high-availability)

## <a name="next-steps"></a>Další kroky

K nasazení Azure Machine Learning s přidruženými prostředky s nastavením vysoké dostupnosti použijte [šablonu Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced).
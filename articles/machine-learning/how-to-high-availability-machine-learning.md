---
title: Odolnost & vysoké dostupnosti
titleSuffix: Azure Machine Learning
description: Přečtěte si, jak zvýšit odolnost vašich Azure Machine Learningch prostředků proti výpadkům pomocí konfigurace s vysokou dostupností.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 09/16/2020
ms.openlocfilehash: 64665c0b1e32970f29233f5abdd6b2d2d020a6b2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90897509"
---
# <a name="increase-azure-machine-learning-resiliency"></a>Zvýšit odolnost Azure Machine Learning



V tomto článku se dozvíte, jak zvýšit odolnost vašich Microsoft Azure Machine Learningch prostředků pomocí konfigurací s vysokou dostupností. Můžete nakonfigurovat služby Azure, na kterých Azure Machine Learning závisí na zajištění vysoké dostupnosti. Tento článek popisuje služby, které můžete konfigurovat pro vysokou dostupnost, a odkazy na Další informace o konfiguraci těchto prostředků.

> [!NOTE]
> Azure Machine Learning sám o sobě nenabízí možnost zotavení po havárii.

## <a name="understand-azure-services-for-azure-machine-learning"></a>Principy služeb Azure pro Azure Machine Learning

Azure Machine Learning závisí na několika službách Azure a má několik vrstev. Některé z těchto služeb jsou zřízené v rámci předplatného (zákazníka). Zodpovídáte za konfiguraci těchto služeb s vysokou dostupností. Další služby se vytvářejí v rámci předplatného Microsoft a spravované Microsoftem. 

Služby Azure zahrnují:

* **Azure Machine Learning infrastruktura**: prostředí spravované Microsoftem pro Azure Machine Learning pracovní prostor.

* **Přidružené prostředky**: prostředky zřízené ve vašem předplatném během vytváření pracovního prostoru Azure Machine Learning. Mezi tyto prostředky patří Azure Storage, Azure Key Vault, Azure Container Registry a Application Insights. Zodpovídáte za konfiguraci nastavení vysoké dostupnosti pro tyto prostředky.
  * Výchozí úložiště obsahuje data, jako je model, školení a data protokolů a datovou sadu.
  * Key Vault má přihlašovací údaje pro úložiště Azure Storage, Container Registry a data.
  * Container Registry má image Docker pro školení a Inferencing prostředí.
  * Application Insights slouží k monitorování Azure Machine Learning.

* **Výpočetní prostředky**: prostředky, které vytvoříte po nasazení pracovního prostoru. Můžete například vytvořit výpočetní instanci nebo výpočetní cluster pro výuku Machine Learningho modelu.
  * Výpočetní instance a výpočetní cluster: prostředí pro vývoj modelů spravovaných Microsoftem.
  * Další materiály: výpočetní prostředky Microsoftu, které se dají připojit k Azure Machine Learning, jako je Azure Kubernetes Service (AKS), Azure Databricks, Azure Container Instances a Azure HDInsight. Zodpovídáte za konfiguraci nastavení vysoké dostupnosti pro tyto prostředky.

* **Další úložiště dat**: Azure Machine Learning můžou připojit další úložiště dat, jako jsou Azure Storage, Azure Data Lake Storage a Azure SQL Database pro školení dat.  Tato úložiště dat se zřídí v rámci vašeho předplatného. Zodpovídáte za konfiguraci jejich nastavení vysoké dostupnosti.

Následující tabulka uvádí, které služby Azure spravuje Microsoft, které spravujete vy a které jsou ve výchozím nastavení vysoce dostupné.

| Služba | Spravuje | Vysoká dostupnost ve výchozím nastavení |
| ----- | ----- | ----- |
| **Azure Machine Learning infrastruktura** | Partnerský vztah Microsoftu | |
| **Přidružené prostředky** |
| Azure Storage | Vy | |
| Key Vault | Vy | ✓ |
| Container Registry | Vy | |
| Application Insights | Vy | Není k dispozici |
| **Výpočetní prostředky** |
| Instance služby Compute | Partnerský vztah Microsoftu |  |
| Výpočtový cluster | Partnerský vztah Microsoftu |  |
| Jiné výpočetní prostředky, jako je AKS, <br>Azure Databricks, Container Instances, HDInsight | Vy |  |
| **Další úložiště dat** , například Azure Storage, SQL Database<br> Azure Database for PostgreSQL, Azure Database for MySQL, <br>Azure Databricks systému souborů | Vy | |

Zbývající část tohoto článku popisuje akce, které je třeba provést, aby byly jednotlivé služby vysoce dostupné.

## <a name="associated-resources"></a>Přidružené prostředky

> [!IMPORTANT]
> Azure Machine Learning nepodporuje výchozí převzetí služeb při selhání v účtu úložiště pomocí geograficky redundantního úložiště (GRS), geograficky redundantního úložiště (GZRS), geograficky redundantního úložiště s přístupem pro čtení (RA-GRS) nebo geograficky redundantního úložiště s přístupem pro čtení (RA-GZRS).

Nezapomeňte nakonfigurovat nastavení vysoké dostupnosti každého prostředku, a to tak, že odkazujete na následující dokumentaci:

* **Azure Storage**: Konfigurace nastavení s vysokou dostupností najdete v tématu [Azure Storage redundance](https://docs.microsoft.com/azure/storage/common/storage-redundancy).
* **Key Vault**: Key Vault ve výchozím nastavení poskytuje vysokou dostupnost a nevyžaduje žádnou akci uživatele.  Viz [dostupnost a redundance Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/disaster-recovery-guidance).
* **Container Registry**: pro geografickou replikaci vyberte možnost registru Premium. Podívejte [se na geografickou replikaci v Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication).
* **Application Insights**: Application Insights neposkytuje nastavení vysoké dostupnosti. Pokud chcete upravit dobu uchovávání dat a podrobnosti, přečtěte si téma [shromažďování, uchovávání a ukládání dat v Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/data-retention-privacy#how-long-is-the-data-kept).

## <a name="compute-resources"></a>Výpočetní prostředky

Nezapomeňte nakonfigurovat nastavení vysoké dostupnosti každého prostředku, a to tak, že odkazujete na následující dokumentaci:

* **Služba Azure Kubernetes**: Podívejte se na [osvědčené postupy pro zajištění kontinuity podnikových procesů a zotavení po havárii ve službě Azure KUBERNETES Service (AKS)](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region) a [vytvořte cluster služby Azure Kubernetes (AKS), který používá zóny dostupnosti](https://docs.microsoft.com/azure/aks/availability-zones). Pokud byl cluster AKS vytvořen pomocí Azure Machine Learning Studio, sady SDK nebo rozhraní příkazového řádku, vysoká dostupnost mezi oblastmi není podporována.
* **Azure Databricks**: Přečtěte si téma [místní zotavení po havárii pro clustery Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/howto-regional-disaster-recovery).
* **Container Instances**: za převzetí služeb při selhání zodpovídá Orchestrator. Viz [Azure Container Instances a orchestrace kontejnerů](https://docs.microsoft.com/azure/container-instances/container-instances-orchestrator-relationship).
* **HDInsight**: viz [služby vysoké dostupnosti podporované službou Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-high-availability-components).

## <a name="additional-data-stores"></a>Další úložiště dat

Nezapomeňte nakonfigurovat nastavení vysoké dostupnosti každého prostředku, a to tak, že odkazujete na následující dokumentaci:

* **Kontejner objektů blob Azure/soubory Azure/Data Lake Storage Gen2**: stejné jako výchozí úložiště
* **Data Lake Storage Gen1**: Další informace najdete v tématu [o zásadách vysoké dostupnosti a zotavení po havárii pro data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-disaster-recovery-guidance).
* **SQL Database**: Přečtěte si informace [o vysoké dostupnosti pro Azure SQL Database a SQL Managed instance](https://docs.microsoft.com/azure/sql-database/sql-database-high-availability).
* **Azure Database for PostgreSQL**: viz [Koncepty vysoké dostupnosti v Azure Database for PostgreSQL na jednom serveru](https://docs.microsoft.com/azure/postgresql/concepts-high-availability).
* **Azure Database for MySQL**: Přečtěte si [vysvětlení kontinuity podnikových aplikací v Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/concepts-business-continuity).
* **Azure Databricks systém souborů**: Přečtěte si téma [místní zotavení po havárii pro Azure Databricks clustery](https://docs.microsoft.com/azure/azure-databricks/howto-regional-disaster-recovery).

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

Pokud zadáte vlastní klíč spravovaný zákazníkem k nasazení Azure Machine Learning pracovního prostoru, Azure Cosmos DB se taky zřídí v rámci vašeho předplatného. V takovém případě zodpovídáte za konfiguraci nastavení vysoké dostupnosti. Podívejte [se na vysokou dostupnost pomocí Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/high-availability).

## <a name="next-steps"></a>Další kroky

K nasazení Azure Machine Learning s přidruženými prostředky s nastavením vysoké dostupnosti použijte [šablonu Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced).

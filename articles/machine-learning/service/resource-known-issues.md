---
title: Známé problémy a řešení potíží pro službu Azure Machine Learning
description: Získat seznam známých problémů, řešení a řešení potíží
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.component: core
ms.topic: article
ms.date: 09/24/2018
ms.openlocfilehash: 27e73bc75c5f04190bad3dab49c1d46782982a18
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47034129"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning-service"></a>Známé problémy a řešení problémů služby Azure Machine Learning
 
Tento článek vám pomůže najít a opravit chyby nebo při použití služby Azure Machine Learning došlo k selhání. 


## <a name="databricks-and-azure-machine-learning"></a>Databricks a Azure Machine Learning

**Doporučení clusteru Databricks:** 

Vytvoření clusteru Azure Databricks jako v4.x Python 3. Doporučujeme, abyste clusteru vysokou souběžnosti.
 
**Sada SDK AML Chyba instalace v Databricks při instalaci dalších balíčků** některé balíčky, jako například `psutil upgrade libs`, může způsobit konflikty. Aby nedocházelo k chybám instalace, instalace balíčků zmrazení lib verzí. Tento problém je související s Databricks a nesouvisí s AML SDK. Příklad:
```
pstuil cryptography==1.5 pyopenssl==16.0.0 ipython=2.2.0
```


## <a name="gather-diagnostics-information"></a>Shromážděte diagnostické informace
V některých případech může být užitečné, pokud může poskytnout diagnostické informace, pokud s žádostí o pomoc. Zde je, kde live soubory protokolu:

## <a name="resource-quotas"></a>Kvóty prostředků

Další informace o [kvóty prostředků](how-to-manage-quotas.md) můžete setkat při práci se službou Azure Machine Learning.

## <a name="get-more-support"></a>Získat další podporu.

Můžete odesílat žádosti o podporu a získat pomoc od technické podpory, fóra a další. [Víc se uč...](support-for-aml-services.md)

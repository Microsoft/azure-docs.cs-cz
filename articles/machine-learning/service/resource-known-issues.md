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
ms.openlocfilehash: d84040dc440c373ae9bae6dbac7a95109a387ba7
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47162742"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning-service"></a>Známé problémy a řešení problémů služby Azure Machine Learning
 
Tento článek vám pomůže najít a opravit chyby nebo při použití služby Azure Machine Learning došlo k selhání. 


## <a name="databricks"></a>Databricks

Problémy s Databricks a Azure Machine Learning.

1. Doporučení clusteru Databricks:
   
   Vytvoření clusteru Azure Databricks jako v4.x Python 3. Doporučujeme, abyste clusteru vysokou souběžnosti.
 
1. Při instalaci dalších balíčků AML SDK nainstalovat selhání v Databricks.

   Některé balíčky, jako například `psutil upgrade libs`, může způsobit konflikty. Aby nedocházelo k chybám instalace, instalace balíčků zmrazení lib verzí. Tento problém je související s Databricks a nesouvisí s AML SDK. Příklad:
   ```python
   pstuil cryptography==1.5 pyopenssl==16.0.0 ipython=2.2.0
   ```

## <a name="gather-diagnostics-information"></a>Shromážděte diagnostické informace
V některých případech může být užitečné, pokud může poskytnout diagnostické informace, pokud s žádostí o pomoc. Zde je, kde live soubory protokolu:

## <a name="resource-quotas"></a>Kvóty prostředků

Další informace o [kvóty prostředků](how-to-manage-quotas.md) můžete setkat při práci se službou Azure Machine Learning.

## <a name="get-more-support"></a>Získat další podporu.

Můžete odesílat žádosti o podporu a získat pomoc od technické podpory, fóra a další. [Víc se uč...](support-for-aml-services.md)

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
ms.date: 10/01/2018
ms.openlocfilehash: d4910eb3dfacb46efe3f85aea3a441bdaaeb1392
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2018
ms.locfileid: "48236408"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning-service"></a>Známé problémy a řešení problémů služby Azure Machine Learning
 
Tento článek vám pomůže najít a opravit chyby nebo při použití služby Azure Machine Learning došlo k selhání. 

## <a name="image-building-failure"></a>Chyba vytváření bitové kopie

Obrázek po nasazení webové služby vytvářet selhání. Alternativním řešením je přidat "pynacl == 1.2.1" jako pip závislosti systému Conda v souboru konfigurace image.  

## <a name="pipelines"></a>Kanály
Dochází k chybě při volání metody PythonScriptStep několikrát za sebou beze změny skriptu nebo parametry. Alternativním řešením je znovu sestavit PipelineData objektu.

## <a name="fpgas"></a>FPGA
Nebude moct nasazovat modely na FPGA, dokud si vyžádáte a byla schválena pro FPGA kvótu. Chcete-li požádat o přístup, vyplňte formulář žádosti o kvóty: https://aka.ms/aml-real-time-ai

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

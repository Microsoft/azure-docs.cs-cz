---
title: Známé problémy a řešení potíží
titleSuffix: Azure Machine Learning service
description: Získat seznam známých problémů, řešení a řešení potíží pro službu Azure Machine Learning.
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.subservice: core
ms.topic: article
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: c327d973170a4556471663c3bea9dcae9b5794fb
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55238607"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning-service"></a>Známé problémy a řešení problémů služby Azure Machine Learning

Tento článek vám pomůže najít a opravit chyby nebo při použití služby Azure Machine Learning došlo k selhání.

## <a name="sdk-installation-issues"></a>Problémy při instalaci sady SDK

**Chybová zpráva: Nelze odinstalovat 'PyYAML.**

Azure Machine Learning pro Python SDK: PyYAML je projektem organizace nainstalovaná distutils. Proto jsme nelze určit přesné soubory, které patří k němu v případě částečné odinstalovat. Pokud chcete pokračovat v instalaci sady SDK při tato chyba se ignoruje, použijte:
```Python
pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
```

## <a name="trouble-creating-azure-machine-learning-compute"></a>Problémy s vytvářením, Azure Machine Learning Compute

Je vzácné pravděpodobné, že někteří uživatelé, kteří si vytvořili jejich pracovního prostoru Azure Machine Learning z portálu Azure portal před verze GA nemusí být možné vytvořit Azure Machine Learning Compute v daném pracovním prostoru. Můžete zvýšit žádost o podporu na službu nebo vytvořit nový pracovní prostor prostřednictvím portálu nebo pomocí sady SDK pro odblokování sami okamžitě.

## <a name="image-building-failure"></a>Chyba vytváření bitové kopie

Obrázek po nasazení webové služby vytvářet selhání. Alternativním řešením je přidat "pynacl == 1.2.1" jako pip závislosti systému Conda v souboru konfigurace image.

## <a name="deployment-failure"></a>Nasazení se nezdařilo.

Pokud zjistíte "DaskOnBatch:context_managers. DaskOnBatch ", 'setup.py']' ukončila s < Signals.SIGKILL: 9 >-SKU pro virtuální počítače použité ve vašem nasazení, které s větší pamětí a změňte prosím.

## <a name="fpgas"></a>FPGA
Nebude moct nasazovat modely na FPGA, dokud si vyžádáte a byla schválena pro FPGA kvótu. Chcete-li požádat o přístup, vyplňte formulář žádosti o kvóty: https://aka.ms/aml-real-time-ai

## <a name="databricks"></a>Databricks

Problémy s Databricks a Azure Machine Learning.

1. Při instalaci dalších balíčků AML SDK nainstalovat selhání v Databricks.

   Některé balíčky, jako například `psutil`, může způsobit konflikty. Aby nedocházelo k chybám instalace, instalace balíčků zmrazení lib verzí. Tento problém souvisí s Databricks a není službou Azure Machine Learning SDK – které mohou nastat ho pomocí jiných knihoven příliš. Příklad:
   ```python
   pstuil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
   ```
   Alternativně můžete použít skripty init, pokud je zachovat otočena směrem problémů s instalací s knihoven Pythonu. Tento přístup není oficiálně podporovaných přístup. Můžete se podívat do [tohoto dokumentu](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts).

2. Při použití automatického Machine Learning v Databricks, pokud chcete zrušit běh a začněte nový experiment spustit, restartujte prosím vaše Azure Databricks pro cluster.

3. V případě, že máte ml nastavení automatické > show_output 10 iterací nastavte prosím hodnotu false, při odesílání spuštění.


## <a name="azure-portal"></a>portál Azure
Pokud přejdete přímo na váš pracovní prostor z sdílet odkaz ze sady SDK nebo na portálu zobrazit, nebudete moct zobrazit stránka s přehledem normální s informace o předplatném v rozšíření. Nebudete také moci přepnout do jiného pracovního prostoru. Pokud je potřeba jiný pracovní prostor zobrazit, alternativním řešením je přejít přímo na [webu Azure portal](https://portal.azure.com) a vyhledejte název pracovního prostoru.

## <a name="diagnostic-logs"></a>Diagnostické protokoly
V některých případech může být užitečné, pokud může poskytnout diagnostické informace, pokud s žádostí o pomoc.
Zde je, kde live soubory protokolu:

## <a name="resource-quotas"></a>Kvóty prostředků

Další informace o [kvóty prostředků](how-to-manage-quotas.md) můžete setkat při práci se službou Azure Machine Learning.

## <a name="get-more-support"></a>Získat další podporu.

Můžete odesílat žádosti o podporu a získat pomoc od technické podpory, fóra a další. [Víc se uč...](support-for-aml-services.md)

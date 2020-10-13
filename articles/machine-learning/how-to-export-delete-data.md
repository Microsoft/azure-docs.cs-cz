---
title: Exportovat nebo odstranit data pracovního prostoru
titleSuffix: Azure Machine Learning
description: Naučte se exportovat nebo odstranit svůj pracovní prostor pomocí rozhraní API pro Azure Machine Learning Studio, CLI, SDK a ověřovaného rozhraní REST API.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: jmartens
author: lobrien
ms.author: laobri
ms.date: 04/24/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: e847f62c2ae3d1d68c39685a38b67e1d0ada8c2f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91251010"
---
# <a name="export-or-delete-your-machine-learning-service-workspace-data"></a>Export nebo odstranění dat pracovního prostoru služby Machine Learning



V Azure Machine Learning můžete data pracovního prostoru exportovat nebo odstranit buď pomocí grafického rozhraní portálu nebo sady Python SDK. Tento článek popisuje obě možnosti.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="control-your-workspace-data"></a>Řízení dat pracovního prostoru

Data v produktu uložená pomocí Azure Machine Learning jsou k dispozici pro export a odstranění. Můžete exportovat a odstranit pomocí Azure Machine Learning studia, CLI a sady SDK. K datům telemetrie se dá dostat prostřednictvím portálu ochrany osobních údajů Azure. 

V Azure Machine Learning se osobní údaje skládají z informací o uživatelích v historii spuštění. 

## <a name="delete-high-level-resources-using-the-portal"></a>Odstranění prostředků vysoké úrovně pomocí portálu

Když vytváříte pracovní prostor, Azure vytvoří v rámci skupiny prostředků několik prostředků:

- Samotný pracovní prostor
- Účet úložiště
- Registr kontejneru
- Instance Application Insights
- Trezor klíčů

Tyto prostředky je možné odstranit tak, že je vyberete ze seznamu a zvolíte **Odstranit** . 

:::image type="content" source="media/how-to-export-delete-data/delete-resource-group-resources.png" alt-text="Snímek obrazovky portálu se zvýrazněnou ikonou odstranit":::

Dokumenty historie spuštění, které mohou obsahovat osobní informace o uživateli, jsou uloženy v účtu úložiště v úložišti objektů BLOB v podsložkách `/azureml` . Data můžete stáhnout a odstranit z portálu.

:::image type="content" source="media/how-to-export-delete-data/storage-account-folders.png" alt-text="Snímek obrazovky portálu se zvýrazněnou ikonou odstranit":::

## <a name="export-and-delete-machine-learning-resources-using-azure-machine-learning-studio"></a>Export a odstranění prostředků strojového učení pomocí Azure Machine Learning studia

Azure Machine Learning Studio nabízí jednotný přehled prostředků strojového učení, například poznámkových blocích, datových sad, modelů a experimentů. Azure Machine Learning Studio zvýrazňuje zachovávání záznamu vašich dat a experimentů. Výpočetní prostředky, jako jsou kanály a výpočetní prostředky, je možné odstranit pomocí prohlížeče. U těchto prostředků přejděte k příslušnému prostředku a vyberte **Odstranit**. 

Datové sady je možné odregistrovat a experimenty lze archivovat, ale tyto operace data neodstraňují. Aby bylo možné data zcela odebrat, datové sady a data spuštění je nutné odstranit na úrovni úložiště. Odstranění na úrovni úložiště se provádí pomocí portálu, jak je popsáno výše.

Školicí artefakty si můžete stáhnout z experimentálních běhů pomocí studia. Vyberte **experiment** a **Spusťte** , ve kterém vás zajímáte. Vyberte **výstup + protokoly** a přejděte ke konkrétním artefaktům, které chcete stáhnout. Vyberte **...** a **Stáhnout**.

Registrovaný model si můžete stáhnout tak, že přejdete na požadovaný **model** a zvolíte **Stáhnout**. 

:::image type="contents" source="media/how-to-export-delete-data/model-download.png" alt-text="Snímek obrazovky portálu se zvýrazněnou ikonou odstranit":::

## <a name="export-and-delete-resources-using-the-python-sdk"></a>Export a odstranění prostředků pomocí sady Python SDK

Výstupy konkrétního běhu si můžete stáhnout pomocí: 

```python
# Retrieved from Azure Machine Learning web UI
run_id = 'aaaaaaaa-bbbb-cccc-dddd-0123456789AB'
experiment = ws.experiments['my-experiment']
run = next(run for run in ex.get_runs() if run.id == run_id)
metrics_output_port = run.get_pipeline_output('metrics_output')
model_output_port = run.get_pipeline_output('model_output')

metrics_output_port.download('.', show_progress=True)
model_output_port.download('.', show_progress=True)
```

Následující prostředky strojového učení je možné odstranit pomocí sady Python SDK: 

| Typ | Volání funkce | Poznámky | 
| --- | --- | --- |
| `Workspace` | [`delete`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py&preserve-view=true#&preserve-view=truedelete-delete-dependent-resources-false--no-wait-false-) | Použijte `delete-dependent-resources` k kaskádové odstranění |
| `Model` | [`delete`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=truedelete--) | | 
| `ComputeTarget` | [`delete`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py&preserve-view=true#&preserve-view=truedelete--) | |
| `WebService` | [`delete`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py&preserve-view=true) | | 


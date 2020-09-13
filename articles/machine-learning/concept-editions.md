---
title: Edice Azure Machine Learning Enterprise a Basic
description: Přečtěte si o rozdílech mezi edicemi Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: j-martens
ms.author: jmartens
ms.date: 06/11/2020
ms.openlocfilehash: ab8a49f62735a47c4ccc9fa488eed60088cd9c20
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "89658597"
---
# <a name="enterprise-preview-and-basic-editions-of-azure-machine-learning"></a>Enterprise (Preview) a Basic – edice Azure Machine Learning 

Azure Machine Learning nabízí dvě edice přizpůsobené potřebám strojového učení. Tyto edice určují, které nástroje Machine Learning jsou dostupné vývojářům a odborníkům přes data z pracovního prostoru.

## <a name="choose-an-edition"></a>Volba edice

Edici přiřadíte pokaždé, když vytvoříte pracovní prostor. Zákazníci se za tuto dobu účtují za náklady, které se týkají výpočtů a dalších prostředků Azure. Naučte se [Spravovat náklady na Azure Machine Learning](concept-plan-manage-cost.md).

Přečtěte si, jak [upgradovat základní pracovní prostor na edici Enterprise (Preview)](how-to-manage-workspace.md#upgrade). 

## <a name="whats-in-each-edition"></a>Co je v jednotlivých edicích

### <a name="data-for-machine-learning-capabilities"></a>Data pro funkce Machine Learning  

| Možnosti                     | Edice                 |
|------------------------------------------------------------------------------------|:-----------:|
| Popisace: [vytváření a Správa označování projektů](tutorial-labeling.md) v studiu (Web)                                                | Vše                     |
| Popisování: popisek v studiu (Web)                                    | Vše                     |
| Popisace: použití privátních zaměstnanců                               | Vše                     |
| Označování: [klasifikace obrázků s podporou ml a detekce objektů](how-to-label-images.md)                  | Pouze edice Enterprise |
| Datové sady + úložiště dat: vytvoření a správa v Pythonu                       | Vše                     |
| Datové sady + úložiště dat: vytvoření a správa v studiu (Web)                         | Vše                     |
| Posun: zobrazení a Správa monitorování datových sad v Pythonu                           | Vše                     |
| Posun: zobrazení a Správa monitorování datových sad v nástroji Studio (Web)                            | Pouze edice Enterprise |


<br/>
<br/>

### <a name="automated-training-capabilities-automl"></a>Možnosti automatizovaného školení (AutoML)

| Možnosti    | Edice                 |
|------------------------------------------------------------------------------------|:-----------:|
| Vytváření a spouštění [AutoML experimentů v poznámkových blocích](how-to-configure-auto-train.md)               | Vše                     |
| Vytváření a spouštění  [AutoML experimentů v studiu (Web)](how-to-use-automated-ml-for-ml-models.md)   | Pouze edice Enterprise |
| Špičkové možnosti prognózování AutoML v oboru             | Pouze edice Enterprise |
| Podpora hloubkového učení a dalších pokročilých učících | Pouze edice Enterprise |
| Klasifikace a regresní úlohy pro velké objemy dat (až 100 GB)                     | Pouze edice Enterprise |


<br/>
<br/>

### <a name="responsible-machine-learning"></a>Odpovědná Machine Learning

| Možnosti    | Edice                 |
|------------------------------------------------------------------------------------|:-----------:|
| [Světlost modelu](how-to-machine-learning-interpretability-automl.md)                                              | Vše                     |
| [Rozdílové soukromí](how-to-differential-privacy.md)                          | Vše                     |
| Vlastní značky pro implementaci datových listů    | Vše                     |
| Spravedlivá integrace AzureML                                      | Vše                     |

<br/>
<br/>


### <a name="build-and-train-capabilities"></a>Možnosti sestavení a výuky

| Možnosti    | Edice                 |
|------------------------------------------------------------------------------------|:-----------:|
| Integrace Visual Studio Code                                                     | Vše                     |
| Posílení učení                                                             | Vše                     |
| Uživatelské rozhraní experimentování                                                                 | Vše                     |
| Integrace Jupyter, JupyterLab                                                    | Vše                     |
| Podpora sady Python SDK                                                                 | Vše                     |
| Podpora sady R SDK                                                                      | Vše                     |
| Kanály ML: vytvoření, spuštění a publikování v Pythonu                           | Vše                     |
| Kanály ML: vytváření, úpravy a odstraňování naplánovaných běhů kanálů v Pythonu| Vše                     |
| Kanály ML: vytvoření koncových bodů kanálu v sadě Python SDK                                   | Vše                     |
| Kanály ML: zobrazení podrobností o spuštění v nástroji Studio (Web)                                              | Vše                     |
| Kanály ML: vytvoření, spuštění, vizualizace a publikování v Návrháři                  | Pouze edice Enterprise |
| Kanály ML: vytvoření koncových bodů kanálu v Návrháři | Pouze edice Enterprise |
| Spravované výpočetní instance pro integrované poznámkové bloky                                 | Vše                     |


<br/>
<br/>

### <a name="deployment-and-model-management-capabilities"></a>Možnosti správy nasazení a modelu

| Možnosti                            | Edice                 |
|------------------------------------------------------------------------------------|:-----------:|
| Rozšíření Azure DevOps pro Machine Learning a Azure ML CLI                 | Vše                     |
| [Integrace Event Gridu](how-to-use-event-grid.md)                                                             | Vše                     |
| Integrace Azure Stream Analytics s Azure Machine Learning                       | Vše                     |
| Vytvoření kanálů ML v sadě SDK                                                         | Vše                     |
| Batch Inferencing                                                                  | Vše                     |
| Modely s hardwarovou akcelerací založené na FPGA                                             | Vše                     |
| Profilace modelu                                                                    | Vše                     |

<br/>
<br/>

### <a name="security-governance-and-control-capabilities"></a>Možnosti zabezpečení, řízení a řízení

| Možnosti     | Edice                 |
|------------------------------------------------------------------------------------|:-----------:|
| Podpora [řízení přístupu na základě role Azure (Azure RBAC)](how-to-assign-roles.md)                                           | Vše                     |
| Podpora [Virtual Network (VNET)](how-to-secure-training-vnet.md) pro výpočetní prostředky                                         | Vše                     |
| Bodování ověřování koncového bodu                                                    | Vše                     |
| [Privátní odkaz na pracovní prostor](how-to-configure-private-link.md)                                                            | Vše                     |
| [Správa kvót](how-to-manage-quotas.md) napříč pracovními prostory                                                 | Pouze edice Enterprise |

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, co je k dispozici na [stránce Přehled a ceny Azure Machine Learning Edition](https://azure.microsoft.com/pricing/details/machine-learning/). 

Přečtěte si, jak [upgradovat základní pracovní prostor na edici Enterprise](how-to-manage-workspace.md#upgrade). 

---
title: Omezení služby
titleSuffix: Azure Machine Learning
description: Omezení služby pro plánování kapacity a maximální limity pro žádosti a odpovědi pro Azure Machine Learning.
services: machine-learning
author: andscho
ms.author: andscho
ms.reviewer: mldocs
ms.topic: reference
ms.service: machine-learning
ms.subservice: core
ms.date: 12/21/2020
ms.openlocfilehash: 132c4b223b99f7110cd4553bb0dd93f1e58f4cdd
ms.sourcegitcommit: a89a517622a3886b3a44ed42839d41a301c786e0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/22/2020
ms.locfileid: "97733293"
---
# <a name="service-limits-in-azure-machine-learning"></a>Omezení služby v Azure Machine Learning

V této části jsou uvedené základní kvóty a prahové hodnoty omezování v Azure Machine Learning.

## <a name="workspaces"></a>Pracovní prostory
| Omezení | Hodnota |
| --- | --- |
| Název pracovního prostoru | 2-32 znaků |

## <a name="runs"></a>Běží
| Omezení | Hodnota |
| --- | --- |
| Spuštění na pracovní prostor | 10 milionů |
| RunId/ParentRunId | 256 znaků |
| DataContainerId | 261 znaků |
| DisplayName |256 znaků|
| Popis |5 000 znaků|
| Počet vlastností |50 |
| Délka klíče vlastnosti |100 znaků |
| Délka hodnoty vlastnosti |1 000 znaků |
| Počet značek |50 |
| Délka klíče značky |100 |
| Délka hodnoty značky |1 000 znaků |
| CancelUri / CompleteUri / DiagnosticsUri |1 000 znaků |
| Délka chybové zprávy |3 000 znaků |
| Délka zprávy upozornění |300 znaků |
| Počet vstupních datových sad |200 |
| Počet výstupních datových sad |20 |


## <a name="metrics"></a>Metriky
| Omezení | Hodnota |
| --- | --- |
| Názvy metrik na spuštění |50|
| Řádky metriky na název metriky |10 milionů|
| Sloupce na řádek metriky |15|
| Délka názvu sloupce metriky |255 znaků |
| Délka hodnoty sloupce metriky |255 znaků |
| Počet odeslaných řádků metriky na dávku | 250 |

> [!NOTE]
> Pokud jste dosáhli limitu názvů metrik na spuštění, protože formátujete proměnné na název metriky, zvažte místo toho použití metriky řádku, kde jeden sloupec je hodnota proměnné a druhý sloupec je hodnota metriky.

## <a name="artifacts"></a>Artifacts

| Omezení | Hodnota |
| --- | --- |
| Počet artefaktů na spuštění |10 milionů|
| Maximální délka cesty artefaktu |5 000 znaků |

## <a name="limit-increases"></a>Zvýšení limitu
Některá omezení se dají pro jednotlivé pracovní prostory zvýšit tím, že se [obrátíte na podporu](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/). 

## <a name="next-steps"></a>Další kroky

- [Konfigurace Azure Machine Learningho prostředí](how-to-configure-environment.md)

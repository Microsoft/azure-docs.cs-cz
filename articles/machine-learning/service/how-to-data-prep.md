---
title: Příprava dat pomocí Machine Learning Data Prep SDK pro Python – Azure
description: Další informace o načtení dat z různých formátů, aby byla lépe použitelná transformace a zápisu dat do umístění pro vlastní modely a přístup k použití sady Azure Machine Learning Data Prep SDK pro Python.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: cforbe
author: cforbe
manager: cgronlun
ms.reviewer: jmartens
ms.date: 09/24/2018
ms.openlocfilehash: 83373f5d6e4a2227bcafdbc4b25b686b0b8d651d
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2018
ms.locfileid: "48867183"
---
# <a name="prepare-data-for-modeling-with-azure-machine-learning"></a>Příprava dat pro modelování s Azure Machine Learning
 
Příprava dat je důležitou součástí pracovního postupu machine learning. Modely budou přesné a efektivní, pokud mají přístup k vyčištění dat ve formátu, který usnadňuje využívání. 

Můžete připravit vaše data v Pythonu pomocí [sady SDK služby Azure Machine Learning Data Prep](https://docs.microsoft.com/python/api/overview/azure/dataprep?view=azure-dataprep-py). 

## <a name="data-preparation-pipeline"></a>Kanál pro přípravu dat

Přípravné kroky hlavní data jsou:

1. [Načtení dat](how-to-load-data.md), což může být v různých formátech
2. [Transformace](how-to-transform-data.md) ho do použitelnější struktury
3. [Zápis](how-to-write-data.md) tato data do umístění dostupného vašich modelů

![Proces přípravy dat.](./media/concept-data-preparation/data-prep-process.png)

## <a name="next-steps"></a>Další postup
Kontrola [Poznámkový blok příklad](https://github.com/Microsoft/MSDataPrepDocs/blob/master/Scenarios/GettingStarted/getting-started.ipynb) přípravy dat pomocí sady SDK pro Azure Machine Learning Data Prep.

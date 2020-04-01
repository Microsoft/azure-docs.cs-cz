---
title: Příklad poznámkových bloků Jupyter
titleSuffix: Azure Machine Learning
description: Najděte a použijte příklady poznámkových bloků Jupyter k prozkoumání Pythonu Azure Machine Learning Python pro SDK.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: 7242b82ee5c43878a33731bd1f02b685020f22b0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "78673619"
---
# <a name="explore-azure-machine-learning-with-jupyter-notebooks"></a>Prozkoumejte Azure Machine Learning s notebooky Jupyter

[Příklad úložiště poznámkových bloků Azure Machine Learning](https://github.com/azure/machinelearningnotebooks) obsahuje nejnovější ukázky Azure Machine Learning Python SDK. Tyto notebooky Juypter jsou navrženy tak, aby vám pomohly prozkoumat sdk a sloužit jako modely pro vaše vlastní projekty strojového učení.

Tento článek ukazuje, jak získat přístup k úložišti z následujících prostředí:

- [Výpočetní instance Azure Machine Learning](#notebookvm)
- [Přineste si vlastní notebook server](#byo)
- [Virtuální počítač pro datové vědy](#dsvm)

> [!NOTE]
> Po naklonování úložiště najdete výukové poznámkové bloky ve složce **s kurzy** a poznámkové bloky specifické pro funkce ve složce **azureml použití.**

<a name="notebookvm"></a>
## <a name="get-samples-on-azure-machine-learning-compute-instance"></a>Získání ukázky výpočetní instance Azure Machine Learning

Nejjednodušší způsob, jak začít s ukázkami, je dokončit [kurz: Instalační prostředí a pracovní prostor](tutorial-1st-experiment-sdk-setup.md). Po dokončení budete mít předinstalovaný vyhrazený notebook s sadou SDK a ukázkovým úložištěm. Není nutné stahovat ani zapisovávat.

<a name="byo"></a>

## <a name="get-samples-on-your-notebook-server"></a>Získání ukázek na notebookovém serveru

Pokud si chcete přinést vlastní notebookový server pro místní rozvoj, postupujte takto:

[!INCLUDE [aml-your-server](../../includes/aml-your-server.md)]

Tyto pokyny nainstalují základní balíčky sady SDK nezbytné pro poznámkové bloky rychlého startu a výukového programu. Jiné ukázkové poznámkové bloky mohou vyžadovat instalaci dalších součástí. Další informace najdete [v tématu Instalace sady Azure Machine Learning SDK pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/install).

<a name="dsvm"></a>
## <a name="get-samples-on-dsvm"></a>Získejte ukázky na DSVM

Virtuální počítač pro datové vědy (DSVM) je přizpůsobená image virtuálního počítače vytvořená speciálně pro zpracování datové vědy. Pokud [vytvoříte dsvm](how-to-configure-environment.md#dsvm), Sada SDK a notebook server jsou nainstalovány a nakonfigurovány pro vás. Stále však budete muset vytvořit pracovní prostor a klonovat ukázkové úložiště.

[!INCLUDE [aml-dsvm-server](../../includes/aml-dsvm-server.md)]

## <a name="next-steps"></a>Další kroky

Prozkoumejte [ukázkové poznámkové bloky](https://aka.ms/aml-notebooks) a zjistěte, co azure machine learning umí, nebo vyzkoušejte tyto kurzy:

- [Trénování a nasazování modelu klasifikace obrázků pomocí MNIST](tutorial-train-models-with-aml.md)

- [Příprava dat a použití automatizovaného strojového učení k trénování regresního modelu pomocí datové sady taxi služby NYC](tutorial-auto-train-models.md)

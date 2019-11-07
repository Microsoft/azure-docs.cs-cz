---
title: Příklad poznámkových bloků Jupyter
titleSuffix: Azure Machine Learning
description: Vyhledejte a použijte příklady Jupyter poznámkových bloků pro zkoumání Azure Machine Learning Pythonu pro sadu SDK.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: 16066ceeb7196bb181d144cc9529d6db627ed828
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582517"
---
# <a name="explore-azure-machine-learning-with-jupyter-notebooks"></a>Prozkoumejte Azure Machine Learning pomocí poznámkových bloků Jupyter

[Ukázkové Azure Machine Learning úložiště poznámkových bloků](https://github.com/azure/machinelearningnotebooks) obsahuje nejnovější ukázky sady SDK pro Azure Machine Learning Python. Tyto poznámkové bloky Juypter jsou navržené tak, aby vám pomohly prozkoumat sadu SDK a sloužit jako modely pro vlastní projekty machine learningu.

V tomto článku se dozvíte, jak získat přístup k úložišti z následujících prostředí:

- [Virtuální počítač s Azure Machine Learning poznámkového bloku](#notebookvm)
- [Přineste si vlastní server poznámkového bloku](#byo)
- [Data Science Virtual Machine](#dsvm)

> [!NOTE]
> Po klonování úložiště najdete poznámkové bloky kurzu ve složce **kurzy** a poznámkových blocích specifických pro konkrétní funkce ve složce **How-to-AzureML** .

<a name="notebookvm"></a>
## <a name="get-samples-on-azure-machine-learning-notebook-vm"></a>Získat ukázky na virtuálním počítači s Azure Machine Learning poznámkového bloku

Nejjednodušší způsob, jak začít s ukázkami, je projít si [kurz: nastavení prostředí a pracovního prostoru](tutorial-1st-experiment-sdk-setup.md). Po dokončení budete mít k dispozici vyhrazený server poznámkového bloku předem načtený pomocí sady SDK a ukázkového úložiště. Nepotřebujete žádné soubory ke stažení nebo instalaci.

<a name="byo"></a>

## <a name="get-samples-on-your-notebook-server"></a>Získání ukázek na serveru poznámkového bloku

Pokud chcete použít vlastní server poznámkového bloku pro místní vývoj, postupujte podle těchto kroků:

[!INCLUDE [aml-your-server](../../../includes/aml-your-server.md)]

Tyto pokyny instalují základní balíčky sady SDK, které jsou nezbytné pro rychlé zprovoznění a poznámkové bloky kurzu. Další ukázkové poznámkové bloky můžou vyžadovat instalaci dalších součástí. Další informace najdete v tématu [instalace sady Azure Machine Learning SDK pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/install).

<a name="dsvm"></a>
## <a name="get-samples-on-dsvm"></a>Získání ukázek na DSVM

Data Science Virtual Machine (DSVM) je přizpůsobená image virtuálního počítače vytvořená speciálně pro účely datové vědy. Pokud [vytvoříte DSVM](how-to-configure-environment.md#dsvm), nainstaluje se a nakonfiguruje server SDK a notebook. Stále ale budete muset vytvořit pracovní prostor a naklonovat ukázkové úložiště.

[!INCLUDE [aml-dsvm-server](../../../includes/aml-dsvm-server.md)]

## <a name="next-steps"></a>Další kroky

Prozkoumejte [ukázkové poznámkové bloky](https://aka.ms/aml-notebooks) , abyste zjistili, co Azure Machine Learning může dělat, nebo vyzkoušejte tyto kurzy:

- [Výuka a nasazení modelu klasifikace obrázků pomocí MNIST ručně zapsaných](tutorial-train-models-with-aml.md)

- [Příprava dat a použití automatizovaného strojového učení k výuce regresního modelu s taxislužby datovou sadou NYC](tutorial-auto-train-models.md)

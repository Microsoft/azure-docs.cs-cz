---
title: Příklad aplikace Jupyter notebook
titleSuffix: Azure Machine Learning service
description: Vyhledejte a použijte příklady Jupyter poznámkových bloků k prozkoumání sady SDK služby Azure Machine Learning Service Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.date: 07/31/2019
ms.custom: seodec18
ms.openlocfilehash: 14962b936d1c09a6c50daa7bec460ce11dbefe5d
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860395"
---
# <a name="explore-azure-machine-learning-service-with-jupyter-notebooks"></a>Prozkoumejte Azure Machine Learning službu pomocí poznámkových bloků Jupyter

[Ukázkové Azure Machine Learning úložiště poznámkových bloků](https://github.com/azure/machinelearningnotebooks) obsahuje nejnovější ukázky sady SDK pro Azure Machine Learning Python. Tyto poznámkové bloky Juypter jsou navržené tak, aby vám pomohly prozkoumat sadu SDK a sloužit jako modely pro vlastní projekty machine learningu.

V tomto článku se dozvíte, jak získat přístup k úložišti z následujících prostředí:

- [Virtuální počítač s Azure Machine Learning poznámkového bloku](#notebookvm)
- [Přineste si vlastní server poznámkového bloku](#byo)
- [Data Science Virtual Machine](#dsvm)

> [!NOTE]
> Po klonování úložiště najdete poznámkové bloky kurzu ve složce **kurzy** a poznámkových blocích specifických pro konkrétní funkce ve složce **How-to-AzureML** .

<a name="notebookvm"></a>
## <a name="get-samples-on-azure-machine-learning-notebook-vm"></a>Získat ukázky na virtuálním počítači s Azure Machine Learning poznámkového bloku

Nejjednodušší způsob, jak začít s ukázkami, je provést tento [kurz: Nastavte prostředí a pracovní](tutorial-1st-experiment-sdk-setup.md)prostor. Po dokončení budete mít k dispozici vyhrazený server poznámkového bloku předem načtený pomocí sady SDK a ukázkového úložiště. Nepotřebujete žádné soubory ke stažení nebo instalaci.

<a name="byo"></a>

## <a name="get-samples-on-your-notebook-server"></a>Získání ukázek na serveru poznámkového bloku

Pokud chcete použít vlastní server poznámkového bloku pro místní vývoj, postupujte podle těchto kroků:

[!INCLUDE [aml-your-server](../../../includes/aml-your-server.md)]

Tyto pokyny instalují základní balíčky sady SDK, které jsou nezbytné pro rychlé zprovoznění a poznámkové bloky kurzu. Další ukázkové poznámkové bloky můžou vyžadovat instalaci dalších součástí. Další informace najdete v tématu [instalace sady Azure Machine Learning SDK pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/install).

<a name="dsvm"></a>
## <a name="get-samples-on-dsvm"></a>Získání ukázek na DSVM

Data Science Virtual Machine (DSVM) je přizpůsobená image virtuálního počítače vytvořená speciálně pro účely datové vědy. Pokud [vytvoříte DSVM](how-to-configure-environment.md#dsvm), nainstaluje se a nakonfiguruje server SDK a notebook. Stále ale budete muset vytvořit pracovní prostor a naklonovat ukázkové úložiště.

[!INCLUDE [aml-dsvm-server](../../../includes/aml-dsvm-server.md)]

## <a name="next-steps"></a>Další postup

Prozkoumejte [ukázkové poznámkové bloky](https://aka.ms/aml-notebooks) , abyste zjistili, co může služba Azure Machine Learning provést, nebo vyzkoušejte tyto kurzy:

- [Trénování a nasadit model klasifikace obrázků s mnist ručně](tutorial-train-models-with-aml.md)

- [Příprava dat a na základě automatizovaných machine learningu k natrénování modelu regrese s datovou sadou NYC taxislužby města](tutorial-auto-train-models.md)

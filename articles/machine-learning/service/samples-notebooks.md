---
title: Příklad aplikace Jupyter notebook
titleSuffix: Azure Machine Learning service
description: Vyhledejte a použijte příklad poznámkové bloky Jupyter a prozkoumejte služby Azure Machine Learning SDK pro Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.date: 05/29/2019
ms.custom: seodec18
ms.openlocfilehash: ea4d5a807c25ea0406b49dac8a83ef1a34e0e8b3
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/30/2019
ms.locfileid: "66391806"
---
# <a name="use-jupyter-notebooks-to-explore-azure-machine-learning-service"></a>Použití poznámkových bloků Jupyter a prozkoumejte službu Azure Machine Learning

[Poznámkových bloků Azure Machine Learning úložiště](https://github.com/azure/machinelearningnotebooks) obsahuje nejnovější ukázky Azure Machine Learning Python SDK. Tyto poznámkové bloky Juypter jsou navržené tak, které vám pomohou prozkoumat sady SDK a bude sloužit jako model pro vlastní strojového učení projekty.

V tomto článku se dozvíte, jak získat přístup k úložišti z následujících prostředích:

- [Azure Machine Learning poznámkového bloku virtuálního počítače](#azure-machine-learning-notebook-vm)
- [Přeneste svůj vlastní server poznámkového bloku](#bring-your-own-jupyter-notebook-server)
- [Virtuální počítač pro datové vědy](#data-science-virtual-machine)
- [Azure Notebooks](#azure-notebooks)

> [!NOTE]
> Jakmile jste naklonovali úložiště, najdete v kurzu poznámkové bloky **kurzy** složky a konkrétní funkce poznámkových bloků v **postupy-k-použití azureml** složky.

## <a name="azure-machine-learning-notebook-vm"></a>Azure Machine Learning poznámkového bloku virtuálního počítače

Nejjednodušší způsob, jak začít pracovat s ukázkami se k dokončení [rychlý start založené na cloudu Poznámkový blok](quickstart-run-cloud-notebook.md). Po dokončení budete mít vyhrazené Poznámkový blok serveru už načtené v sadě SDK a ukázkové úložiště. Žádné soubory ke stažení nebo instalace.

## <a name="bring-your-own-jupyter-notebook-server"></a>Přeneste svůj vlastní server poznámkového bloku Jupyter

Pokud chcete přenést svůj vlastní server poznámkového bloku pro místní vývoj, postupujte podle těchto kroků:

[!INCLUDE [aml-your-server](../../../includes/aml-your-server.md)]

Tyto pokyny nainstalujte základní sadu SDK balíčky potřebné pro rychlý start a kurzů poznámkových bloků. Další ukázkové poznámkové bloky mohou vyžadovat instalaci dalších součástí. Další informace najdete v tématu [nainstalovat sadu SDK Azure Machine Learning pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/install).

## <a name="data-science-virtual-machine"></a>Virtuální počítač pro datové vědy

Na Data virtuálního počítače VĚDY je přizpůsobená image virtuálního počítače vytvořená speciálně pro datových věd. Pokud jste [vytvořit DSVM](how-to-configure-environment.md#dsvm), instalace a konfigurace pro vás server SDK a poznámkového bloku. Ale bude stále potřeba vytvořit pracovní prostor a naklonujte ukázkové úložiště.

[!INCLUDE [aml-dsvm-server](../../../includes/aml-dsvm-server.md)]

## <a name="azure-notebooks"></a>Azure Notebooks

Na [poznámkových bloků Azure](https://notebooks.azure.com/), instalace a konfigurace pro vás server SDK a poznámkového bloku. Poznámkových bloků Azure poskytuje plně spravovanou, zjednodušené Poznámkový blok prostředí zkoumat.

Pro přístup k ukázkové úložiště na poznámkových bloků Azure, přejděte do pracovního prostoru Azure Machine Learning prostřednictvím [webu Azure portal](https://portal.azure.com). Z **přehled** vyberte **začít pracovat v poznámkových bloků Azure**.

## <a name="next-steps"></a>Další postup

Prozkoumejte [ukázkové poznámkové bloky](https://aka.ms/aml-notebooks) Pokud chcete zjistit, co Azure Machine Learning service můžete provést nebo zkuste tyto kurzy:

- [Trénování a nasadit model klasifikace obrázků s mnist ručně](tutorial-train-models-with-aml.md)

- [Příprava dat a na základě automatizovaných machine learningu k natrénování modelu regrese s datovou sadou NYC taxislužby města](tutorial-data-prep.md)
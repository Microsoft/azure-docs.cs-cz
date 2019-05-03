---
title: Příklad aplikace Jupyter notebook
titleSuffix: Azure Machine Learning service
description: Vyhledejte a použijte příklad poznámkové bloky Jupyter a prozkoumejte službu Azure Machine Learning v Pythonu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: cd88fd85ce6d18287c700a54e42b6237a42ea5c9
ms.sourcegitcommit: eea74d11a6d6ea6d187e90e368e70e46b76cd2aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2019
ms.locfileid: "65035380"
---
# <a name="use-jupyter-notebooks-to-explore-azure-machine-learning-service"></a>Použití poznámkových bloků Jupyter a prozkoumejte službu Azure Machine Learning

Pro usnadnění práce jsme vyvinuli řadu poznámkové bloky Jupyter Python, která vám pomůže prozkoumat službu Azure Machine Learning. 

Zjistěte, jak používat službu s dokumentací na tomto webu a tyto poznámkové bloky můžete přizpůsobit pro vaše konkrétní potřeby. 

Použijte jednu z následujících možností spustit server poznámkového bloku se tyto ukázkové poznámkové bloky.  Jakmile je server spuštěn, Najít kurz poznámkových bloků v **kurzy** složky, nebo si prohlédněte různých funkcí v **postupy-k-použití azureml** složky.

## <a name="a-managed-cloud-notebook-server"></a>Server poznámkového bloku spravované cloudové

Je snadné začít s vlastním serverem založené na cloudu poznámkového bloku. Ukázkový poznámkový blok a [Azure Machine Learning SDK pro Python](https://aka.ms/aml-sdk) jsou již nainstalovány a nakonfigurována pro vás, když vytvoříte tento prostředek v cloudu.  

[!INCLUDE [aml-azure-notebooks](../../../includes/aml-azure-notebooks.md)]

* Ukázky jsou k dispozici na webové stránce poznámkového bloku.

## <a name="a-data-science-virtual-machine-dsvm"></a>Virtuální počítač pro datové vědy (DSVM)

[Azure Machine Learning SDK pro Python](https://aka.ms/aml-sdk) a server poznámkového bloku již nainstalované a nakonfigurované pro vás na DSVM. 

Poté co [vytvořit DSVM](how-to-configure-environment.md#dsvm), pomocí následujících kroků na datové VĚDY spouštění poznámkových bloků.

[!INCLUDE [aml-dsvm-server](../../../includes/aml-dsvm-server.md)]

## <a name="your-own-jupyter-notebook-server"></a>Váš vlastní server poznámkového bloku Jupyter

Pomocí těchto kroků můžete vytvořit místní aplikace Jupyter Notebook server ve vašem počítači.

[!INCLUDE [aml-your-server](../../../includes/aml-your-server.md)]

Pokyny k instalaci budou instalovat balíčky potřebujete možnost spouštět poznámkové bloky rychlý start a kurzů.  Další ukázkové poznámkové bloky může vyžadovat instalaci dalších součástí.  Další informace o těchto součástech naleznete v tématu [nainstalovat sadu SDK Azure Machine Learning pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/install).

## <a name="azure-notebooks"></a>Azure Notebooks

Ukázkové poznámkové bloky a [Azure Machine Learning SDK pro Python](https://aka.ms/aml-sdk) jsou již nainstalován a nakonfigurován pro vás na [poznámkových bloků Azure](https://notebooks.azure.com/). Instalace a aktualizace budoucí jsou automaticky spravovány prostřednictvím služby Azure.

Použití [webu Azure portal](https://portal.azure.com) Začínáme s Azure poznámkových bloků.  Otevřete pracovní prostor a od **přehled** vyberte **začít pracovat v poznámkových bloků Azure**.

## <a name="next-steps"></a>Další postup

+ Prozkoumejte ukázkové poznámkové bloky pro službu Azure Machine Learning v tomto úložišti Githubu: https://aka.ms/aml-notebooks

Vyzkoušejte tyto kurzy:
+ [Trénování a nasadit model klasifikace obrázků s mnist ručně](tutorial-train-models-with-aml.md)

+ [Příprava dat a na základě automatizovaných machine learningu k natrénování modelu regrese s datovou sadou NYC taxislužby města](tutorial-data-prep.md)
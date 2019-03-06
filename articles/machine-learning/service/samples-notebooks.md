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
ms.openlocfilehash: 961983aad0775f9b9d728269e8a57137ff508f02
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2019
ms.locfileid: "57451786"
---
# <a name="use-jupyter-notebooks-to-explore-azure-machine-learning-service"></a>Použití poznámkových bloků Jupyter a prozkoumejte službu Azure Machine Learning

Pro usnadnění práce jsme vyvinuli řadu poznámkové bloky Jupyter Python, která vám pomůže prozkoumat službu Azure Machine Learning. 

Zjistěte, jak používat službu s dokumentací na tomto webu a tyto poznámkové bloky můžete přizpůsobit pro vaše konkrétní potřeby. 

Použijte jednu z následujících možností spustit server poznámkového bloku se tyto ukázkové poznámkové bloky.  Jakmile je server spuštěn, Najít kurz poznámkových bloků v **kurzy** složky, nebo si prohlédněte různých funkcí v **postupy-k-použití azureml** složky.


## <a name="try-azure-notebooks-free-jupyter-notebooks-in-the-cloud"></a>Vyzkoušejte Azure poznámkových bloků: Bezplatné poznámkové bloky Jupyter v cloudu

Je snadné začít s poznámkovými bloky Azure! [Azure Machine Learning SDK pro Python](https://aka.ms/aml-sdk) je již nainstalován a nakonfigurován pro vás na [poznámkových bloků Azure](https://notebooks.azure.com/). Instalace a aktualizace budoucí jsou automaticky spravovány prostřednictvím služby Azure.
  
[!INCLUDE [aml-azure-notebooks](../../../includes/aml-azure-notebooks.md)]


## <a name="use-a-data-science-virtual-machine-dsvm"></a>Použít virtuální počítač pro datové vědy (DSVM)

[Azure Machine Learning SDK pro Python](https://aka.ms/aml-sdk) a server poznámkového bloku již nainstalované a nakonfigurované pro vás na DSVM. 

Poté co [vytvořit DSVM](how-to-configure-environment.md#dsvm), pomocí následujících kroků na datové VĚDY spouštění poznámkových bloků.

[!INCLUDE [aml-dsvm-server](../../../includes/aml-dsvm-server.md)]


## <a name="use-your-own-jupyter-notebook-server"></a>Použít vlastní server poznámkového bloku Jupyter

Pomocí těchto kroků můžete vytvořit místní aplikace Jupyter Notebook server ve vašem počítači.

[!INCLUDE [aml-your-server](../../../includes/aml-your-server.md)]

Rychlý start pokyny budou instalovat balíčky potřebujete možnost spouštět poznámkové bloky rychlý start a kurzů.  Další ukázkové poznámkové bloky může vyžadovat instalaci dalších součástí.  Další informace o těchto součástech naleznete v tématu [nainstalovat sadu SDK Azure Machine Learning pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/install).

<a name="automated-ml-setup"></a>

## <a name="automated-machine-learning-setup"></a>Automatizované strojového učení instalační program 

_Tento postup platí jenom pro poznámkové bloky v **how-to-use-azureml/automated-machine-learning** složky._

Přestože můžete použít některou z výše uvedených možností, můžete také nainstalovat prostředí a vytvořit pracovní prostor ve stejnou dobu v následujících pokynech. 

1. Nainstalujte [Mini conda](https://conda.io/miniconda.html). Zvolte 3.7 nebo vyšší. Postupujte podle pokynů k instalaci. 
   >[!NOTE]
   >Pokud je verze 4.4.10 nebo pozdější, můžete použít existující systém conda. Použití `conda -V` k zobrazení verze. Verze systému conda můžete aktualizovat pomocí příkazu: `conda update conda`. Není nutné k instalaci mini conda konkrétně.

1. Stažení ukázkové poznámkové bloky od [Githubu](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning
) jako zip a rozbalit obsah do místního adresáře. Poznámkové bloky automatizovaná strojového učení se v `how-to-use-azureml/automated-machine-learning` složky.

1. Nastavení nové prostředí Conda. 
   1. Otevřete řádek Conda na místním počítači.
   
   1. Přejděte k souborům, které jste extrahovali do místního počítače.
   
   1. Otevřít **automatizované machine-learning** složky.
   
   1. Spustit `automl_setup.cmd` conda řádku pro Windows, nebo `.sh` soubor pro váš operační systém. K provedení může trvat asi 10 minut.

      Instalační skript:
      + Vytvoří nové prostředí conda
      + Nainstaluje potřebné balíčky
      + Nakonfiguruje widgetu
      + Spuštění poznámkového bloku jupyter
      
   >[!NOTE]
   > Skript používá název prostředí conda jako volitelný parametr. Je výchozí název prostředí conda `azure_automl`. Přesný příkaz závisí na operačním systému. To je užitečné, pokud vytváříte nové prostředí nebo upgrade na novou verzi. Například můžete použít "automl_setup.cmd azure_automl_sandbox" Vytvoření azure_automl_sandbox název prostředí. 
      
1. Po dokončení skriptu, zobrazí se domovská stránka Poznámkový blok Jupyter v prohlížeči.

1. Přejděte do umístění, kam jste uložili poznámkových bloků. 

1. Otevřete složku, automatické machine-learning a potom ho otevřete **configuration.ipynb** poznámkového bloku. 

1. Spusťte všechny buňky v poznámkovém bloku, zaregistrujte poskytovatele prostředků služby Machine Learning a vytvoření pracovního prostoru.

Nyní jste připraveni k otevření a spuštění poznámkové bloky uložené na místním počítači.


## <a name="next-steps"></a>Další postup

Prozkoumejte [poznámkových bloků úložiště GitHub pro službu Azure Machine Learning](https://aka.ms/aml-notebooks)

Vyzkoušejte tyto kurzy:
+ [Trénování a nasadit model klasifikace obrázků s mnist ručně](tutorial-train-models-with-aml.md)

+ [Příprava dat a na základě automatizovaných machine learningu k natrénování modelu regrese s datovou sadou NYC taxislužby města](tutorial-data-prep.md)

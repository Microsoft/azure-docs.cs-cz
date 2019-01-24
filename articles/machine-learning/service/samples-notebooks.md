---
title: Příklad aplikace Jupyter notebook
titleSuffix: Azure Machine Learning service
description: Vyhledejte a použijte příklad poznámkové bloky Jupyter a prozkoumejte službu Azure Machine Learning v Pythonu.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: sample
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 6befe3a3fee80dd65fd3ac5be241c558707224e6
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54811090"
---
# <a name="use-jupyter-notebooks-to-explore-azure-machine-learning-service"></a>Použití poznámkových bloků Jupyter a prozkoumejte službu Azure Machine Learning


Pro usnadnění práce jsme vyvinuli řadu poznámkové bloky Jupyter Python, která vám pomůže prozkoumat službu Azure Machine Learning. 

Zjistěte, jak používat službu s dokumentací na tomto webu a tyto poznámkové bloky můžete přizpůsobit pro vaše konkrétní potřeby. 

## <a name="prerequisite"></a>Požadavek

Dokončení [rychlý start Python pro Azure Machine Learning](quickstart-get-started.md) k vytvoření pracovního prostoru a spuštění poznámkových bloků Azure.

## <a name="try-azure-notebooks-free-jupyter-notebooks-in-the-cloud"></a>Vyzkoušejte Azure poznámkových bloků: Bezplatné poznámkové bloky Jupyter v cloudu

Je snadné začít s poznámkovými bloky Azure! [Azure Machine Learning SDK pro Python](https://aka.ms/aml-sdk) je již nainstalován a nakonfigurován pro vás na [poznámkových bloků Azure](https://notebooks.azure.com/). Instalace a aktualizace budoucí jsou automaticky spravovány prostřednictvím služby Azure.
  
+ Ke spuštění **základní kurz poznámkových bloků**:
  1. Přejděte na [poznámkových bloků Azure](https://notebooks.azure.com/).
    
  1. Najít **kurzy** složky **Začínáme** knihovny, které jste vytvořili během požadovaných součástí tohoto rychlého startu.
    
  1. Otevřete Poznámkový blok, který chcete spustit.
    
+ Ke spuštění **jiných poznámkových bloků**:

  1. [Importovat ukázkové poznámkové bloky](https://aka.ms/aml-clone-azure-notebooks) do poznámkových bloků Azure.

  1. Přidáte konfigurační soubor pracovního prostoru do knihovny pomocí některé z těchto metod:
     + Kopírování **config.json** soubor **Začínáme** klonovat knihovny do nové knihovny.

     + Vytvořit nový pracovní prostor pomocí kódu v [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb).
    
  1. Otevřete Poznámkový blok, který chcete spustit.     


## <a name="use-a-data-science-virtual-machine-dsvm"></a>Použít virtuální počítač pro datové vědy (DSVM)

[Azure Machine Learning SDK pro Python](https://aka.ms/aml-sdk) a server poznámkového bloku již nainstalované a nakonfigurované pro vás na DSVM. Pomocí těchto kroků spouštění poznámkových bloků.

1. [Vytvoření DSVM](how-to-configure-environment.md#dsvm).

1. Naklonujte [úložiště GitHub](https://aka.ms/aml-notebooks).

1. Přidáte konfigurační soubor pracovního prostoru do knihovny pomocí některé z těchto metod:
    * Kopírovat **aml_config\config.json** soubor vytvořený pomocí tohoto rychlého startu požadovaných součástí na klonovaný adresář.

    * Vytvořit nový pracovní prostor pomocí kódu v [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb).

1. Spusťte server poznámkového bloku v naklonovaném adresáři.

## <a name="use-your-own-jupyter-notebook-server"></a>Použít vlastní server poznámkového bloku Jupyter

Pomocí těchto kroků můžete vytvořit místní aplikace Jupyter Notebook server ve vašem počítači.

1. Ujistěte se, že jste dokončili požadované rychlý start, do které jste nainstalovali se sadami SDK služby Azure Machine Learning.

1. Naklonujte [úložiště GitHub](https://aka.ms/aml-notebooks).

1. Přidáte konfigurační soubor pracovního prostoru do knihovny pomocí některé z těchto metod:
    * Kopírovat **aml_config\config.json** soubor vytvořený pomocí tohoto rychlého startu požadovaných součástí na klonovaný adresář.
    
    * Vytvořit nový pracovní prostor pomocí kódu v [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb).

1. Spusťte server poznámkového bloku v naklonovaném adresáři.

1. Přejděte do složky obsahující poznámkového bloku.

1. Otevřete poznámkový blok.

<a name="auto"></a>

## <a name="automated-ml-setup"></a>Automatická instalace ML 

**Tento postup platí jenom pro poznámkové bloky v `automated-machine-learning` složky.**

Přestože můžete použít některou z výše uvedených možností, můžete také nainstalovat prostředí a vytvořit pracovní prostor ve stejnou dobu v následujících pokynech. 

1. Nainstalujte [Mini conda](https://conda.io/miniconda.html). Zvolte 3.7 nebo vyšší. Postupujte podle pokynů k instalaci. 
   >[!NOTE]
   >Pokud je verze 4.4.10 nebo pozdější, můžete použít existující systém conda. Použití `conda -V` k zobrazení verze. Verze systému conda můžete aktualizovat pomocí příkazu: `conda update conda`. Není nutné k instalaci mini conda konkrétně.

1. Stažení ukázkové poznámkové bloky od [Githubu](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning
) jako zip a rozbalit obsah do místního adresáře. Poznámkové bloky automatizovaná strojového učení se v `how-to-use-azureml/automated-machine-learning` složky.

1. Nastavení nové prostředí Conda. 
   1. Otevřete řádek Conda na místním počítači.
   
   1. Přejděte k souborům, které jste extrahovali do místního počítače.
   
   1. Otevřít `automated-machine-learning` složky.
   
   1. Spustit `automl_setup.cmd` conda řádku pro Windows, nebo `.sh` soubor pro váš operační systém. K provedení může trvat asi 10 minut.

      Instalační skript:
      + Vytvoří nové prostředí conda
      + Nainstaluje potřebné balíčky
      + Nakonfiguruje widgetu
      + Spuštění poznámkového bloku jupyter
      
   >[!NOTE]
   > Skript používá název prostředí conda jako volitelný parametr. Je výchozí název prostředí conda `azure_automl`. Přesný příkaz závisí na operačním systému. To je užitečné, pokud vytváříte nové prostředí nebo upgrade na novou verzi. Například můžete použít "automl_setup.cmd azure_automl_sandbox" k vytvoření azure_automl_sandbox evironment název. 
      
1. Po dokončení skriptu, zobrazí se domovská stránka Poznámkový blok Jupyter v prohlížeči.

1. Přejděte do umístění, kam jste uložili poznámkových bloků. 

1. Otevřete složku, automatické machine-learning a potom ho otevřete `configuration.ipynb` poznámkového bloku. 

1. Spusťte všechny buňky v poznámkovém bloku, zaregistrujte poskytovatele prostředků služby Machine Learning a vytvoření pracovního prostoru.

Nyní jste připraveni k otevření a spuštění poznámkové bloky uložené na místním počítači.


## <a name="next-steps"></a>Další postup

Prozkoumejte [poznámkových bloků úložiště GitHub pro službu Azure Machine Learning](https://aka.ms/aml-notebooks)

Vyzkoušejte tyto kurzy:
+ [Trénování a nasadit model klasifikace obrázků s mnist ručně](tutorial-train-models-with-aml.md)

+ [Příprava dat a na základě automatizovaných machine learningu k natrénování modelu regrese s datovou sadou NYC taxislužby města](tutorial-data-prep.md)

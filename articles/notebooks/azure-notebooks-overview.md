---
title: Přehled poznámkových bloků Azure | Dokumentace Microsoftu
description: Spouštějte poznámkové bloky Jupyter v cloudu s využitím bezplatné služby Azure poznámkových bloků.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 9cea5a8e-c52d-4bdc-9e4a-cecdc1ad02c1
ms.service: notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: a84f71c2656c73867ee076d4b993026558a0a504
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52853045"
---
# <a name="overview-of-azure-notebooks"></a>Přehled Azure poznámkové bloky

Poznámkových bloků Azure je bezplatná služba hostovaná vyvíjet a spouštět poznámkové bloky Jupyter v cloudu s žádná instalace. [Jupyter](http://jupyter.org/) (dříve IPython) je opensourcový projekt, který vám umožní snadno kombinovat text Markdownu, spustitelného kódu, trvalá data, grafiky a vizualizace na jedné, které se dají sdílet plátno, *Poznámkový blok* (image poskytuje jupyter.org):

[![](http://jupyter.org/assets/jupyterpreview.png "Poznámkové bloky Jupyter")](http://jupyter.org/assets/jupyterpreview.png#lightbox)

Z důvodu této výkonnou kombinaci vysvětlující text, kód a grafické Jupyter získal oblibu pro mnoho účelů, včetně data science instrukce, čištění dat a transformace, číselná simulace, statistické modelování a vývoj modely strojového učení.

## <a name="hassle-free-experience"></a>Bezproblémové prostředí

Poznámkových bloků Azure vám umožní začít rychle na vytváření prototypů, datové vědy, vědecký výzkum nebo se program Pythonu:

- Mezi odborníky přes data má okamžitý přístup k úplné prostředí Anaconda s žádná instalace.
- Učitel může poskytnout bezproblémové prostředí Pythonu pro studenty.
- Skládání můžete poskytnout lajk Přednáška nebo webinář bez nutnosti účastníků, který můžete utratit za 45 minut. instalace softwaru.
- Vývojář nebo tuto možnost můžete použít poznámkových bloků jako zápisník rychlý kód.

Poznámkové bloky stát ještě výkonnější, když uživatelé mohou spolupracovat na nich prostřednictvím prohlížeče přístupné cloudovou službu jako je poznámkových bloků Azure (ve verzi Preview). V cloudu uživatelé nemusí instalace Jupyteru místně nebo starat zachování prostředí. Cloud také usnadňuje sdílení poznámkových bloků (a přidružené datové soubory) s jiným autorizovaným uživatelům zabránit komplikacím sdílení poznámkových bloků externí prostředky, jako je úložiště správy zdrojového kódu. Pomocí poznámkových bloků Azure uživatelé mohou také poznámkových bloků kopii (nebo "klonování") do své vlastní účet pro úpravy nebo experimentování, což je užitečné zejména pro účely instrukce.

Protože poznámkových bloků Azure je obecný kód pro vytváření, spouštění a sdílení platformy, můžete ho pro řadu různých scénářů:

- Přečtěte si nový programovací jazyk – zkuste použít jeden z [frontpage kurzy](https://notebooks.azure.com/Microsoft/projects/samples/html/Introduction%20to%20Python.ipynb)
- Další informace pro datové vědy – zkuste [Jake VanderPlas knihy](https://notebooks.azure.com/jakevdp/projects/PythonDataScienceHandbook)
- [Představuje kurz](https://notebooks.azure.com/garth-wells/projects/CUED-IA-Computing-Michaelmas) pro stovky studentů
- Dejte webinář online nebo na konferenci nějaký produkt, aniž by bylo nutné na instalaci 
- Povolit uživatelům Githubu přímo načíst a možnost spouštět poznámkové bloky ve [vytváření Odznáček spuštění Githubu](https://notebooks.azure.com/help/projects/sharing/create-a-github-badge)
- Poskytnout [jako prezentací PowerPoint](https://notebooks.azure.com/help/jupyter-notebooks/slides) ve kterém je spustitelný kód ve snímcích!

Stručně řečeno poznámkových bloků Azure vám pomůže provádět práci efektivněji a tím dosáhnout více.

> [!Note]
> Další informace o Jupyter samotný najdete na [jupyter.org](http://jupyter.org/) a [Jupyter dokumentaci](http://jupyter-notebook.readthedocs.io/en/latest/).

## <a name="pricing-and-quotas"></a>Ceny a kvóty

Poznámkových bloků Azure je bezplatná služba, ale každý projekt je omezena na 4 GB paměti a 1 GB dat, aby se zabránilo zneužití. Oprávněným uživatelům, které tyto limity překročit najdete v článku v rámci kontroly Captcha k pokračování ve spouštění poznámkových bloků.

Uvolnit všechna omezení, postupu přidružte svůj účet s předplatným Azure, které vám umožní vybrat výpočetní úrovně premium.

## <a name="available-kernels-and-environments"></a>Dostupná jádra a prostředí

Každý Poznámkový blok jupyter vyberete jádra (to znamená, běhové prostředí), který se používá ke spuštění jakékoli buňky kódu. Poznámkových bloků Azure podporuje následujícími jádry:

- Python 2.7 + Anaconda2 5.3.0
- Python 3.6 + Anaconda3 5.3.0
- Python 3.5 + Anaconda3-4.2.0 (bude zastaralé)
- R 3.3 + Microsoft R otevřete 3.3.0
- F#4.1.9

Poznámkových bloků Azure obsahuje také další balíčky nad rámec základní distribuce. Jádrech Pythonu, například obsahovat numpy, balíčky pandas, scikit-informace rozostření knihoven a matplotlib.

Můžete také upravit projekt pro vytvoření prostředí pro všechny poznámkových bloků v daném projektu. Další informace najdete v tématu [rychlý start: vytvoření projektu s vlastním prostředím](quickstart-create-jupyter-notebook-project-environment.md).

Kromě základní distribuce poznámkových bloků Azure dodává s předinstalovanou s mnoha dalších balíčků, které jsou užitečné pro odborníky přes data. Můžete také nainstalovat vlastní balíčky pomocí typickým procesem pro jednotlivé jazyky.

## <a name="pre-configured-jupyter-extensions"></a>Předem nakonfigurované rozšíření Jupyter

Poznámkových bloků Azure je předem nakonfigurovaný s těmito příponami Jupyter:

- [PŘEKONÁNÍ](https://github.com/damianavila/RISE): A Jupyter prezentace rozšíření (označované také jako live_reveal). Další informace najdete v tématu [spustit Poznámkový blok prezentace](present-jupyter-notebooks-slideshow.md).
- [JupyterLab](https://github.com/jupyterlab/jupyterlab): úplné výpočetní prostředí pro práci s poznámkovými bloky Jupyter.
- [Altair](https://github.com/ellisonbg/altair): deklarativní statistické vizualizace knihovny pro Python.
- [BQPlot](https://github.com/bloomberg/bqplot): interaktivní zobrazování framework pro aplikace Jupyter notebook.
- [IpyWidgets](https://github.com/jupyter-widgets/ipywidgets): interaktivní HTML pomůcky pro poznámkové bloky Jupyter.

## <a name="issues-and-getting-help"></a>Problémy a získání nápovědy

Protože poznámkových bloků Azure je stále ve verzi Preview, může docházet k službě dočasných výpadků, které mohou být častější nebo trvalé déle než ostatní služby Azure. Některé funkce mohou být neúplné nebo obsahují chyby.

V současné době doporučujeme nepoužívat poznámkových bloků Azure verze Preview pro důležité podnikové aplikace, nebo citlivých poznámkových bloků a data.

Do diskuse o poznámkových bloků Azure, založte problém na [úložiště GitHub](https://github.com/Microsoft/AzureNotebooks/issues).

## <a name="next-steps"></a>Další postup  

- [Prozkoumejte ukázkové poznámkové bloky](azure-notebooks-samples.md)

- Šablony rychlý start:

  - [Vytvářejte a sdílejte poznámkového bloku](quickstart-create-share-jupyter-notebook.md)
  - [Klonovat poznámkového bloku](quickstart-clone-jupyter-notebook.md)
  - [Migrovat místní aplikace Jupyter notebook](quickstart-migrate-local-jupyter-notebook.md)
  - [Použít vlastní prostředí](quickstart-create-jupyter-notebook-project-environment.md)
  - [Přihlaste se a nastavit ID uživatele](quickstart-sign-in-azure-notebooks.md)

- Kurzy:

  - [Vytvoření a spuštění poznámkového bloku](tutorial-create-run-jupyter-notebook.md  )

- Články s návody:
  
  - [Vytvoření a klonování projekty](create-clone-jupyter-notebooks.md)
  - [Konfigurace a správa projektů](configure-manage-azure-notebooks-projects.md)
  - [Instalace balíčků z v rámci poznámkového bloku](install-packages-jupyter-notebook.md)
  - [K dispozici prezentace](present-jupyter-notebooks-slideshow.md)
  - [Práce s datovými soubory](work-with-project-data-files.md)
  - [Přístup k prostředkům data](access-data-resources-jupyter-notebooks.md)
  - [Použití Azure Machine Learning Services](use-machine-learning-services-jupyter-notebooks.md)

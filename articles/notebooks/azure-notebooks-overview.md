---
title: Přehled Azure poznámkové bloky
description: Spouštějte poznámkové bloky Jupyter v cloudu s využitím bezplatné služby poznámkových bloků Azure, kde se vyžaduje bez instalace nebo konfigurace.
ms.topic: overview
ms.date: 04/05/2019
ms.openlocfilehash: b3dfa6cec962809fad1a03f5100c55315ff6a9ad
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277682"
---
# <a name="overview-of-azure-notebooks"></a>Přehled Azure poznámkové bloky

Azure Notebooks je bezplatná hostovaná služba pro vývoj a spouštění poznámkových bloků Jupyter v cloudu, která nevyžaduje instalaci. [Jupyter](https://jupyter.org/) (dříve IPython) je open source projekt, který umožňuje snadno kombinovat Markdownu text, spustitelný kód, trvalá data, grafiky a vizualizace na jediném, dostupném plátnu, *poznámkovém bloku* (SMS, kde se nachází Jupyter.org):

[![příklady poznámkových bloků Jupyter](https://jupyter.org/assets/jupyterpreview.png)](https://jupyter.org/assets/jupyterpreview.png#lightbox)

Z důvodu této výkonnou kombinaci vysvětlující text, kód a grafické Jupyter získal oblibu pro mnoho účelů, včetně data science instrukce, čištění dat a transformace, číselná simulace, statistické modelování a vývoj modely strojového učení.

## <a name="hassle-free-experience"></a>Bezproblémové prostředí

Poznámkových bloků Azure vám umožní začít rychle na vytváření prototypů, datové vědy, vědecký výzkum nebo se program Pythonu:

- Mezi odborníky přes data má okamžitý přístup k úplné prostředí Anaconda s žádná instalace.
- Učitel může poskytnout bezproblémové prostředí Pythonu pro studenty.
- Skládání můžete poskytnout lajk Přednáška nebo webinář bez nutnosti účastníků, který můžete utratit za 45 minut. instalace softwaru.
- Vývojář nebo tuto možnost můžete použít poznámkových bloků jako zápisník rychlý kód.

Poznámkové bloky stát ještě výkonnější, když uživatelé mohou spolupracovat na nich prostřednictvím prohlížeče přístupné cloudovou službu jako je poznámkových bloků Azure (ve verzi Preview). V cloudu uživatelé nemusí instalace Jupyteru místně nebo starat zachování prostředí. Cloud také usnadňuje sdílení poznámkových bloků (a přidružené datové soubory) s jiným autorizovaným uživatelům zabránit komplikacím sdílení poznámkových bloků externí prostředky, jako je úložiště správy zdrojového kódu. Pomocí poznámkových bloků Azure uživatelé mohou také poznámkových bloků kopii (nebo "klonování") do své vlastní účet pro úpravy nebo experimentování, což je užitečné zejména pro účely instrukce.

Protože poznámkových bloků Azure je obecný kód pro vytváření, spouštění a sdílení platformy, můžete ho pro řadu různých scénářů:

- Naučte se nový programovací jazyk – Vyzkoušejte si jeden z [kurzů pro FrontPage](https://notebooks.azure.com/Microsoft/projects/samples/html/Introduction%20to%20Python.ipynb) .
- Naučte se data věda – Vyzkoušejte si [Jake VanderPlas Book](https://notebooks.azure.com/jakevdp/projects/PythonDataScienceHandbook)
- [Učení kurzu](https://notebooks.azure.com/garth-wells/projects/CUED-IA-Computing-Michaelmas) pro stovky studentů
- Dejte webinář online nebo na konferenci nějaký produkt, aniž by bylo nutné na instalaci 
- Povolit uživatelům GitHubu přímé načítání a spouštění poznámkových bloků [vytvořením spouštěcího znaku GitHubu](https://notebooks.azure.com/help/projects/sharing/create-a-github-badge)
- Poskytněte [aplikaci PowerPoint jako prezentace](https://notebooks.azure.com/help/jupyter-notebooks/slides) , kde je kód na snímcích spustitelný.

Stručně řečeno poznámkových bloků Azure vám pomůže provádět práci efektivněji a tím dosáhnout více.

> [!Note]
> Další informace o samotné Jupyter najdete na [Jupyter.org](https://jupyter.org/) a v [dokumentaci Jupyter](https://jupyter-notebook.readthedocs.io/en/latest/).

## <a name="pricing-and-quotas"></a>Ceny a kvóty

Azure Notebooks je bezplatná služba, ale každý projekt je omezený na 4 GB paměti a 1 GB dat, aby nedocházelo k zneužití. Oprávněným uživatelům, které tyto limity překročit najdete v článku v rámci kontroly Captcha k pokračování ve spouštění poznámkových bloků.

Pokud chcete uvolnit všechna omezení, přihlaste se Azure Notebooks pomocí účtu s použitím Azure Active Directory (například firemní účet). Pokud je tento účet přidružený k předplatnému Azure, můžete se připojit k libovolným instancím Azure Data Science Virtual Machine v rámci tohoto předplatného. Další informace najdete v tématu [Správa a konfigurace projektů – výpočetní vrstva](configure-manage-azure-notebooks-projects.md#compute-tier).

U notebookových serverů je zaručeno, že bude mít maximálně 8 hodin. Ve většině případů váš kontejner nepodléhá tomuto limitu a i nadále běží déle než tento čas, ale dlouhotrvající relace může být občas vypnuta kvůli stabilitě systému.

## <a name="available-kernels-and-environments"></a>Dostupná jádra a prostředí

Každý Poznámkový blok jupyter vyberete jádra (to znamená, běhové prostředí), který se používá ke spuštění jakékoli buňky kódu. Poznámkových bloků Azure podporuje následujícími jádry:

- Python 2.7 + Anaconda2 5.3.0
- Python 3.6 + Anaconda3 5.3.0
- Python 3.5 + Anaconda3-4.2.0 (bude zastaralé)
- R 3.4.1 + Microsoft R Open 3.4.1
- F#4.1.9

Poznámkových bloků Azure obsahuje také další balíčky nad rámec základní distribuce. Jádrech Pythonu, například obsahovat numpy, balíčky pandas, scikit-informace rozostření knihoven a matplotlib.

Můžete také upravit projekt pro vytvoření prostředí pro všechny poznámkových bloků v daném projektu. Další informace najdete v tématu [rychlý Start: vytvoření projektu s vlastním prostředím](quickstart-create-jupyter-notebook-project-environment.md).

Kromě základní distribuce poznámkových bloků Azure dodává s předinstalovanou s mnoha dalších balíčků, které jsou užitečné pro odborníky přes data. Můžete také nainstalovat vlastní balíčky pomocí typickým procesem pro jednotlivé jazyky.

## <a name="pre-configured-jupyter-extensions"></a>Předem nakonfigurované rozšíření Jupyter

Poznámkových bloků Azure je předem nakonfigurovaný s těmito příponami Jupyter:

- [Zvýšení](https://github.com/damianavila/RISE): rozšíření prezentace Jupyter (označované také jako live_reveal). Další informace najdete v tématu [spuštění prezentace poznámkového bloku](present-jupyter-notebooks-slideshow.md).
- [JupyterLab](https://github.com/jupyterlab/jupyterlab): úplné výpočetní prostředí pro práci s poznámkovým blokem Jupyter.
- [Altair](https://github.com/ellisonbg/altair): deklarativní statistickou vizualizaci knihovny pro Python.
- [BQPlot](https://github.com/bloomberg/bqplot): interaktivní rozhraní pro vykreslení pro notebooky Jupyter.
- [IpyWidgets](https://github.com/jupyter-widgets/ipywidgets): interaktivní widgety HTML pro notebooky Jupyter.

## <a name="issues-and-getting-help"></a>Problémy a získání nápovědy

Protože poznámkových bloků Azure je stále ve verzi Preview, může docházet k službě dočasných výpadků, které mohou být častější nebo trvalé déle než ostatní služby Azure. Některé funkce mohou být neúplné nebo obsahují chyby.

V současné době doporučujeme nepoužívat poznámkových bloků Azure verze Preview pro důležité podnikové aplikace, nebo citlivých poznámkových bloků a data.

Pokud chcete diskutovat o Azure Notebooks, zapište problém do [úložiště GitHub](https://github.com/Microsoft/AzureNotebooks/issues).

## <a name="next-steps"></a>Další kroky  

- [Prozkoumat ukázkové poznámkové bloky](azure-notebooks-samples.md)

- Šablony rychlý start:

  - [Vytvoření a sdílení poznámkového bloku](quickstart-create-share-jupyter-notebook.md)
  - [Klonovat Poznámkový blok](quickstart-clone-jupyter-notebook.md)
  - [Migrace místního poznámkového bloku Jupyter](quickstart-migrate-local-jupyter-notebook.md)
  - [Použití vlastního prostředí](quickstart-create-jupyter-notebook-project-environment.md)
  - [Přihlaste se a nastavte ID uživatele.](quickstart-sign-in-azure-notebooks.md)

- Kurzy:

  - [Vytvoření a spuštění poznámkového bloku](tutorial-create-run-jupyter-notebook.md  )

- Články s návody:
  
  - [Vytváření a klonování projektů](create-clone-jupyter-notebooks.md)
  - [Konfigurace a správa projektů](configure-manage-azure-notebooks-projects.md)
  - [Instalace balíčků z poznámkového bloku](install-packages-jupyter-notebook.md)
  - [Prezentace prezentace](present-jupyter-notebooks-slideshow.md)
  - [Práce s datovými soubory](work-with-project-data-files.md)
  - [Přístup k datovým prostředkům](access-data-resources-jupyter-notebooks.md)
  - [Použít Azure Machine Learning](use-machine-learning-services-jupyter-notebooks.md)

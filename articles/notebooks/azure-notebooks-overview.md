---
title: Přehled Azure Notebooks Preview
description: Spusťte poznámkové bloky Jupyter v cloudu pomocí bezplatné služby Azure Notebooks Preview, kde není potřeba žádné nastavení nebo konfigurace.
ms.topic: overview
ms.date: 04/05/2019
ms.openlocfilehash: d59faaf85a1fdbad776e954e974bafa300f93d85
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/18/2020
ms.locfileid: "94845131"
---
# <a name="overview-of-azure-notebooks-preview"></a>Přehled Azure Notebooks Preview

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Azure Notebooks je bezplatná hostovaná služba pro vývoj a spouštění poznámkových bloků Jupyter v cloudu, která nevyžaduje instalaci. [Jupyter](https://jupyter.org/) (dříve IPython) je open source projekt, který umožňuje snadno kombinovat Markdownu text, spustitelný kód, trvalá data, grafiky a vizualizace na jediném dostupném plátnu s názvem, který se označuje jako *Poznámkový blok* (obrazový soubor Jupyter.org):

[![Příklady poznámkových bloků Jupyter](https://jupyter.org/assets/jupyterpreview.png)](https://jupyter.org/assets/jupyterpreview.png#lightbox)

Z důvodu této výkonné kombinace kódu, grafiky a vysvětlujícího textu se Jupyter stala oblíbená pro spoustu použití, včetně instrukcí pro datové vědy, čištění a transformace dat, numerické simulace, statistického modelování a vývoje modelů strojového učení.

## <a name="hassle-free-experience"></a>Bezproblémové prostředí

Azure Notebooks vám pomůže rychle začít pracovat na vytváření prototypů, datové vědy, vědecký výzkum nebo učení programu Pythonu:

- Vědecký odborník na data má okamžitý přístup k plnému prostředí Anaconda bez instalace.
- Učitel může k studentům poskytnout bezproblémové prostředí Pythonu.
- Předvádějící může vést jako rozhovor nebo webinář, aniž by vyžádala účastníky o útratu 45 minut instalace softwaru.
- Vývojář nebo Hobbyist může používat poznámkové bloky jako rychlý kód scratchpad.

Poznámkové bloky se ještě výkonnější, když je můžou lidé na nich spolupracovat prostřednictvím cloudové služby přístupné v prohlížeči, jako je Azure Notebooks (ve verzi Preview). V cloudu uživatelé nepotřebují instalovat Jupyter místně nebo se k němu netýkat při údržbě prostředí. Cloud také usnadňuje sdílení poznámkových bloků (a přidružených datových souborů) s jinými autorizovanými uživateli, což vyloučí komplikace sdílení poznámkových bloků prostřednictvím externích prostředků, jako jsou úložiště pro správu zdrojového kódu. S Azure Notebooks mohou uživatelé také zkopírovat (neboli "klonovat") poznámkové bloky do vlastního účtu pro změny nebo experimentování, což je zvláště užitečné pro účely instrukcí.

Vzhledem k tomu, že Azure Notebooks je obecná platforma pro vytváření, spouštění a sdílení kódu, můžete ji použít pro mnoho různých scénářů:

- Naučte se nový programovací jazyk – Vyzkoušejte si jeden z [kurzů pro FrontPage](https://notebooks.azure.com/Microsoft/projects/samples/html/Introduction%20to%20Python.ipynb) .
- Naučte se data věda – Vyzkoušejte si [Jake VanderPlas Book](https://notebooks.azure.com/jakevdp/projects/PythonDataScienceHandbook)
- [Učení kurzu](https://notebooks.azure.com/garth-wells/projects/CUED-IA-Computing-Michaelmas) pro stovky studentů
- Poskytněte webinář online nebo na konferenci bez doby útraty při instalaci. 
- Povolit uživatelům GitHubu přímé načítání a spouštění poznámkových bloků [vytvořením spouštěcího znaku GitHubu](https://notebooks.azure.com/help/projects/sharing/create-a-github-badge)
- Poskytněte [aplikaci PowerPoint jako prezentace](https://notebooks.azure.com/help/jupyter-notebooks/slides) , kde je kód na snímcích spustitelný.

V krátkém Azure Notebooks vám pomůže dosáhnout efektivnější práce a tím i další.

> [!Note]
> Další informace o samotné Jupyter najdete na [Jupyter.org](https://jupyter.org/) a v [dokumentaci Jupyter](https://jupyter-notebook.readthedocs.io/en/latest/).

## <a name="pricing-and-quotas"></a>Ceny a kvóty

Azure Notebooks je bezplatná služba, ale každý projekt je omezený na 4 GB paměti a 1 GB dat, aby nedocházelo k zneužití. Legitimní uživatelé, kteří překračují tato omezení, uvidí CAPTCHA výzvu pro pokračování ve spouštění poznámkových bloků.

Pokud chcete uvolnit všechna omezení, přihlaste se Azure Notebooks pomocí účtu s použitím Azure Active Directory (například firemní účet). Pokud je tento účet přidružený k předplatnému Azure, můžete se připojit k libovolným instancím Azure Data Science Virtual Machine v rámci tohoto předplatného. Další informace najdete v tématu [Správa a konfigurace projektů – výpočetní vrstva](configure-manage-azure-notebooks-projects.md#compute-tier).

U notebookových serverů je zaručeno, že bude mít maximálně 8 hodin. Ve většině případů váš kontejner nepodléhá tomuto limitu a i nadále běží déle než tento čas, ale dlouhotrvající relace může být občas vypnuta kvůli stabilitě systému.

## <a name="available-kernels-and-environments"></a>Dostupná jádra a prostředí

Pro každý Poznámkový blok vyberete jádro (to znamená běhové prostředí), které se používá ke spouštění libovolných buněk s kódem. Azure Notebooks podporuje následující jádra:

- Python 2,7 + Anaconda2-5.3.0
- Python 3,6 + Anaconda3-5.3.0
- Python 3,5 + Anaconda3-4.2.0 (bude zastaralé)
- R 3.4.1 + Microsoft R Open 3.4.1
- 4.1.9 F #

Azure Notebooks také zahrnuje další balíčky přesahující základní distribuce. Jádry Pythonu zahrnují například knihovny numpy, PANDAS, scikit-učení, matplotlib a rozostření.

Můžete také přizpůsobit projekt a vytvořit prostředí pro všechny poznámkové bloky v daném projektu. Další informace najdete v tématu [rychlý Start: vytvoření projektu s vlastním prostředím](quickstart-create-jupyter-notebook-project-environment.md).

Kromě základních distribucí Azure Notebooks přináší předinstalované množství dalších balíčků, které jsou užitečné pro odborníky přes data. Můžete také nainstalovat vlastní balíčky pomocí typického procesu pro jednotlivé jazyky.

## <a name="pre-configured-jupyter-extensions"></a>Předem nakonfigurovaná rozšíření Jupyter

Azure Notebooks je předem nakonfigurovaný s následujícími rozšířeními Jupyter:

- [Zvýšení](https://github.com/damianavila/RISE): rozšíření prezentace Jupyter (označované také jako live_reveal). Další informace najdete v tématu [spuštění prezentace poznámkového bloku](present-jupyter-notebooks-slideshow.md).
- [JupyterLab](https://github.com/jupyterlab/jupyterlab): úplné výpočetní prostředí pro práci s poznámkovým blokem Jupyter.
- [Altair](https://github.com/ellisonbg/altair): deklarativní statistickou vizualizaci knihovny pro Python.
- [BQPlot](https://github.com/bloomberg/bqplot): interaktivní rozhraní pro vykreslení pro notebooky Jupyter.
- [IpyWidgets](https://github.com/jupyter-widgets/ipywidgets): interaktivní widgety HTML pro notebooky Jupyter.

## <a name="issues-and-getting-help"></a>Problémy a získání nápovědě

Vzhledem k tomu, že Azure Notebooks je stále ve verzi Preview, může docházet k dočasným výpadkům, které můžou být častěji nebo delší než jiné služby Azure. Některé funkce můžou být neúplné nebo obsahují chyby.

V současnosti doporučujeme používat Azure Notebooks Preview pro důležité obchodní aplikace nebo citlivé notebooky a data.

Pokud chcete diskutovat o Azure Notebooks, zapište problém do [úložiště GitHub](https://github.com/Microsoft/AzureNotebooks/issues).

## <a name="next-steps"></a>Další kroky  

- [Prozkoumat ukázkové poznámkové bloky](azure-notebooks-samples.md)

- Rychlá zprovoznění:

  - [Vytvoření a sdílení poznámkového bloku](quickstart-create-share-jupyter-notebook.md)
  - [Klonovat Poznámkový blok](quickstart-clone-jupyter-notebook.md)
  - [Migrace místního poznámkového bloku Jupyter](quickstart-migrate-local-jupyter-notebook.md)
  - [Použití vlastního prostředí](quickstart-create-jupyter-notebook-project-environment.md)
  - [Přihlášení a nastavení ID uživatele](quickstart-sign-in-azure-notebooks.md)

- Kurzy:

  - [Vytvoření a spuštění poznámkového bloku](tutorial-create-run-jupyter-notebook.md  )

- Články s postupy:
  
  - [Vytváření a klonování projektů](create-clone-jupyter-notebooks.md)
  - [Konfigurace a správa projektů](configure-manage-azure-notebooks-projects.md)
  - [Instalace balíčků z poznámkového bloku](install-packages-jupyter-notebook.md)
  - [Předložení prezentace](present-jupyter-notebooks-slideshow.md)
  - [Práce s datovými soubory](work-with-project-data-files.md)
  - [Přístup k datovým prostředkům](access-data-resources-jupyter-notebooks.md)
  - [Používání Azure Machine Learningu](../machine-learning/samples-notebooks.md)
---
title: Přehled Azure Notebooks Preview
description: Spusťte poznámkové bloky Jupyter v cloudu pomocí bezplatné služby Azure Notebooks Preview, kde není potřeba žádné nastavení nebo konfigurace.
ms.topic: overview
ms.date: 04/05/2019
ms.openlocfilehash: bd2355bdefcedca5026a25915dc1da55fdc33a36
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2020
ms.locfileid: "75646326"
---
# <a name="overview-of-azure-notebooks-preview"></a>Přehled Azure Notebooks Preview

Azure Notebooks je bezplatná hostovaná služba pro vývoj a spouštění poznámkových bloků Jupyter v cloudu, která nevyžaduje instalaci. [Jupyter](https://jupyter.org/) (dříve IPython) je opensourcový projekt, který vám umožní snadno kombinovat text Markdownu, spustitelného kódu, trvalá data, grafiky a vizualizace na jedné, které se dají sdílet plátno, *Poznámkový blok* (image poskytuje jupyter.org):

[![příklady poznámkových bloků Jupyter](https://jupyter.org/assets/jupyterpreview.png)](https://jupyter.org/assets/jupyterpreview.png#lightbox)

Z důvodu této výkonnou kombinaci vysvětlující text, kód a grafické Jupyter získal oblibu pro mnoho účelů, včetně data science instrukce, čištění dat a transformace, číselná simulace, statistické modelování a vývoj modely strojového učení.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

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
> Další informace o Jupyter samotný najdete na [jupyter.org](https://jupyter.org/) a [Jupyter dokumentaci](https://jupyter-notebook.readthedocs.io/en/latest/).

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

## <a name="next-steps"></a>Další kroky  

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
  - [Použít Azure Machine Learning](use-machine-learning-services-jupyter-notebooks.md)

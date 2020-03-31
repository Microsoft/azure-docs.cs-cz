---
title: Přehled náhledu poznámkových bloků Azure
description: Spouštějte poznámkové bloky Jupyter v cloudu pomocí bezplatné služby Azure Notebooks Preview, kde není vyžadováno žádné nastavení ani konfigurace.
ms.topic: overview
ms.date: 04/05/2019
ms.openlocfilehash: bd2355bdefcedca5026a25915dc1da55fdc33a36
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "75646326"
---
# <a name="overview-of-azure-notebooks-preview"></a>Přehled náhledu poznámkových bloků Azure

Azure Notebooks je bezplatná hostovaná služba pro vývoj a spouštění poznámkových bloků Jupyter v cloudu, která nevyžaduje instalaci. [Jupyter](https://jupyter.org/) (dříve IPython) je open-source projekt, který vám umožní snadno kombinovat Markdown text, spustitelný kód, trvalá data, grafiky a vizualizace na jediné, sharable plátno, *notebook* (obrázek s laskavým svolením jupyter.org):

[![Příklady poznámkových bloků Jupyter](https://jupyter.org/assets/jupyterpreview.png)](https://jupyter.org/assets/jupyterpreview.png#lightbox)

Díky této výkonné kombinaci kódu, grafiky a vysvětlujícího textu se Jupyter stal populárním pro mnoho použití, včetně výuky datových věd, čištění a transformace dat, numerické simulace, statistického modelování a vývoje modely strojového učení.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="hassle-free-experience"></a>Bezproblémový zážitek

Poznámkové bloky Azure vám pomohou rychle začít s vytvářením prototypů, datové vědy, akademického výzkumu nebo učení se naprogramovat Python:

- Datový vědec má okamžitý přístup k plnému prostředí Anaconda bez instalace.
- Učitel může studentům poskytnout bezproblémové prostředí Pythonu.
- Prezentující může dát like talk nebo webinář, aniž by požádal účastníky, aby strávili 45 minut instalací softwaru.
- Vývojář nebo fanda může notebooky používat jako rychlý kód.

Poznámkové bloky se stávají ještě výkonnějšími, když na nich lidé můžou spolupracovat prostřednictvím cloudové služby přístupné prohlížeči, jako jsou poznámkové bloky Azure (ve verzi Preview). V cloudu uživatelé nemusí instalovat Jupyter místně nebo se starat o údržbu prostředí. Cloud také usnadňuje sdílení notebooků (a přidružených datových souborů) s dalšími oprávněnými uživateli, čímž se vyhnete komplikacím při sdílení notebooků prostřednictvím externích prostředků, jako jsou úložiště správy zdrojového kódu. Pomocí poznámkových bloků Azure můžou uživatelé taky kopírovat (nebo "klonovat") poznámkové bloky do vlastního účtu pro úpravy nebo experimentování, což je užitečné zejména pro účely instrukcí.

Vzhledem k tomu, že poznámkové bloky Azure je obecná platforma pro vytváření, spouštění a sdílení kódu, můžete ji použít pro mnoho různých scénářů:

- Naučte se nový programovací jazyk – vyzkoušejte jeden z [výukových programů na titulní stránce](https://notebooks.azure.com/Microsoft/projects/samples/html/Introduction%20to%20Python.ipynb)
- Naučte se datové vědy - zkuste [Knihu Jakea VanderPlase](https://notebooks.azure.com/jakevdp/projects/PythonDataScienceHandbook)
- [Vyučujte kurz](https://notebooks.azure.com/garth-wells/projects/CUED-IA-Computing-Michaelmas) pro stovky studentů
- Podejte webinář online nebo na konferenci, aniž byste trávili čas instalací 
- Umožněte uživatelům GitHubu přímo načítat a spouštět poznámkové bloky [vytvořením odpalovacího odznaku GitHubu](https://notebooks.azure.com/help/projects/sharing/create-a-github-badge)
- Dejte [PowerPoint jako prezentace,](https://notebooks.azure.com/help/jupyter-notebooks/slides) kde kód v snímcích je spustitelný!

Stručně řečeno, poznámkové bloky Azure vám pomohou efektivněji pracovat a tím dosáhnout více.

> [!Note]
> Více informací o jupyteru lze nalézt na [jupyter.org](https://jupyter.org/) a v [dokumentaci Jupyter](https://jupyter-notebook.readthedocs.io/en/latest/).

## <a name="pricing-and-quotas"></a>Ceny a kvóty

Azure Notebooks je bezplatná služba, ale každý projekt je omezen na 4 GB paměti a 1 GB dat, aby se zabránilo zneužití. Legitimní uživatelé, kteří tyto limity překročí, uvidí výzvu Captcha pokračovat ve spouštění poznámkových bloků.

Pokud chcete uvolnit všechna omezení, přihlaste se k poznámkovým blokům Azure pomocí účtu pomocí Azure Active Directory (například firemní účet). Pokud je tento účet přidružený k předplatnému Azure, můžete se připojit k libovolné instance virtuálního počítače Azure Data Science v rámci tohoto předplatného. Další informace naleznete v [tématu Správa a konfigurace projektů – výpočetní úroveň](configure-manage-azure-notebooks-projects.md#compute-tier).

Notebookové servery jsou zaručeně existovat maximálně 8 hodin. Ve většině případů váš kontejner nepodléhá tomuto limitu a nadále běží po této době, ale dlouhodobé relace může být občas vypnutpro stabilitu systému.

## <a name="available-kernels-and-environments"></a>Dostupná jádra a prostředí

Pro každý poznámkový blok vyberete jádro (tj. prostředí runtime), které se používá ke spuštění libovolných buněk kódu. Poznámkové bloky Azure podporují následující jádra:

- Python 2.7 + Anaconda2-5.3.0
- Python 3.6 + Anakonda3-5.3.0
- Python 3.5 + Anaconda3-4.2.0 (bude zastaralé)
- R 3.4.1 + Microsoft R Open 3.4.1
- F# 4.1.9

Poznámkové bloky Azure také obsahují další balíčky nad rámec základních distribucí. Jádra Pythonu například zahrnují knihovny numpy, pandas, scikit-learn, matplotlib a bokeh.

Můžete také přizpůsobit projekt k vytvoření prostředí pro všechny poznámkové bloky v tomto projektu. Další informace naleznete [v tématu Úvodní příručka: Vytvoření projektu s vlastním prostředím](quickstart-create-jupyter-notebook-project-environment.md).

Kromě základních distribucí jsou poznámkové bloky Azure dodávány s předinstalovanými mnoha dalšími balíčky, které jsou užitečné pro datové vědce. Můžete také nainstalovat vlastní balíčky pomocí typického procesu pro každý jazyk.

## <a name="pre-configured-jupyter-extensions"></a>Předkonfigurovaná rozšíření Jupyter

Poznámkové bloky Azure jsou předem nakonfigurované s následujícími rozšířeními Jupyter:

- [RISE](https://github.com/damianavila/RISE): Jupyter Slideshow Extension (také známý jako live_reveal). Další informace naleznete [v tématu Spuštění prezentace poznámkového bloku](present-jupyter-notebooks-slideshow.md).
- [JupyterLab](https://github.com/jupyterlab/jupyterlab): Kompletní výpočetní prostředí pro práci s notebooky Jupyter.
- [Altair](https://github.com/ellisonbg/altair): Deklarativní statistická vizualizační knihovna pro Python.
- [BQPlot](https://github.com/bloomberg/bqplot): Interaktivní vykreslovací rámec pro poznámkové bloky Jupyter.
- [IpyWidgets](https://github.com/jupyter-widgets/ipywidgets): Interaktivní HTML widgety pro Jupyter notebooky.

## <a name="issues-and-getting-help"></a>Problémy a získání nápovědy

Vzhledem k tomu, že poznámkové bloky Azure je stále ve verzi Preview, služba může dojít k dočasné výpadky, které může být častější nebo déle trvající než jiné služby Azure. Některé funkce mohou být neúplné nebo obsahují chyby.

V současné době doporučujeme nepoužívat Azure Notebooks Preview pro důležité podnikové aplikace nebo citlivé poznámkové bloky a data.

Chcete-li diskutovat o vašich otázkách týkajících se poznámkových bloků Azure, zadejte problém v [úložišti GitHub](https://github.com/Microsoft/AzureNotebooks/issues).

## <a name="next-steps"></a>Další kroky  

- [Prozkoumejte ukázkové poznámkové bloky](azure-notebooks-samples.md)

- Rychlá zprovoznění:

  - [Vytvoření a sdílení poznámkového bloku](quickstart-create-share-jupyter-notebook.md)
  - [Klonování poznámkového bloku](quickstart-clone-jupyter-notebook.md)
  - [Migrace místních poznámkových bloků Jupyter](quickstart-migrate-local-jupyter-notebook.md)
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
  - [Používání Azure Machine Learningu](use-machine-learning-services-jupyter-notebooks.md)

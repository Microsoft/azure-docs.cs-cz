---
title: Použití poznámkových bloků se službou Azure Sentinel pro lov v zabezpečení
description: Tento článek popisuje, jak používat poznámkové bloky s možnostmi lovu do Azure Sentinel.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 09/06/2020
ms.openlocfilehash: 43d7a697b3cb013a73a0b14db8ec1758244ae3b9
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2020
ms.locfileid: "97092186"
---
# <a name="use-jupyter-notebook-to-hunt-for-security-threats"></a>Pro bezpečnostní hrozby použít Jupyter Notebook

Základem Azure Sentinel je úložiště dat; kombinuje dotazování s vysokým výkonem, dynamické schéma a škáluje na obrovské objemy dat. Azure Portal a všechny nástroje Sentinel Azure používají pro přístup k tomuto úložišti dat společné rozhraní API. Stejné rozhraní API je také k dispozici pro externí nástroje, jako jsou [Jupyter](https://jupyter.org/) notebooky a Python. I když je možné na portálu provádět mnoho běžných úloh, Jupyter rozšiřuje rozsah toho, co můžete s těmito daty dělat. Kombinuje plnou programovatelnost s velkou kolekcí knihoven pro strojové učení, vizualizaci a analýzu dat. Tyto atributy Jupyter přesvědčivý Nástroj pro vyšetřování a lov zabezpečení.

![Příklad poznámkového bloku](./media/notebooks/sentinel-notebooks-map.png)

Prostředí Jupyter jsme integroval do Azure Portal, což usnadňuje vytváření a spouštění poznámkových bloků pro analýzu dat. Knihovna *Kqlmagic* poskytuje připevňování, který umožňuje převzít dotazy z Sentinel Azure a spustit je přímo v poznámkovém bloku. Dotazy používají [dotazovací jazyk Kusto](https://kusto.azurewebsites.net/docs/kusto/query/index.html). Několik poznámkových bloků vyvinutých analytiky zabezpečení Microsoftu se zabalí do Azure Sentinel. Některé z těchto poznámkových bloků jsou sestaveny pro konkrétní scénář a lze je použít tak, jak jsou. Jiné jsou určené jako vzorky k ilustraci techniků a funkcí, které můžete zkopírovat nebo přizpůsobit pro použití ve vlastních poznámkových blocích. Další poznámkové bloky se můžou importovat taky z GitHubu komunity služby Azure Sentinel.

Integrované prostředí Jupyter používá [Azure Notebooks](https://notebooks.azure.com/) k ukládání, sdílení a spouštění poznámkových bloků. Tyto poznámkové bloky můžete také spustit místně, pokud máte prostředí Pythonu a Jupyter na vašem počítači nebo v jiných prostředích JupterHub, jako je například Azure Databricks.

Poznámkové bloky mají dvě komponenty:

- Rozhraní založené na prohlížeči, kde zadáváte a spouštíte dotazy a kód a kde se zobrazují výsledky spuštění.
- *Jádro* , které zodpovídá za analýzu a provádění samotného kódu.

Jádro poznámkového bloku Azure Sentinel běží na virtuálním počítači Azure (VM). K dispozici je několik možností licencování, které umožňují využívat výkonnější virtuální počítače, pokud vaše notebooky obsahují složité modely strojového učení.

Poznámkové bloky Azure Sentinel používají spoustu oblíbených knihoven Pythonu, jako jsou PANDAS, matplotlib, rozostření a další. Existuje spousta dalších balíčků Pythonu, ze kterých si můžete vybrat, včetně oblastí, jako jsou:

- Vizualizace a grafika
- Zpracování a analýza dat
- Statistiky a číselné výpočty
- Strojové učení a hluboké učení

Také jsme vydali nějaké open source nástroje zabezpečení Jupyter v balíčku s názvem [msticpy](https://github.com/Microsoft/msticpy/). Tento balíček se používá v mnoha zahrnutých poznámkových blocích. Nástroje Msticpy jsou navržené specificky pro pomoc s vytvářením poznámkových bloků pro lov a vyšetřování a aktivně pracujeme na nových funkcích a vylepšeních.

[Úložiště GitHub komunity služby Azure Sentinel](https://github.com/Azure/Azure-Sentinel) je umístění všech budoucích poznámkových bloků služby Azure Sentinel sestavených společností Microsoft nebo, které přispěly od komunity.

Chcete-li použít poznámkové bloky, musíte nejprve vytvořit pracovní prostor Azure Machine Learning (ML).

## <a name="create-an-azure-ml-workspace"></a>Vytvoření pracovního prostoru Azure ML

1. V Azure Portal přejděte na   >    >  **poznámkové bloky** služby Azure Sentinel Threat Management a pak vyberte **Spustit Poznámkový blok**.

    > [!div class="mx-imgBorder"]
    > ![spustit Poznámkový blok pro spuštění pracovního prostoru Azure ml](./media/notebooks/sentinel-notebooks-launch.png)

1. V části **pracovní prostor AzureML** vyberte **vytvořit novou**.

    > [!div class="mx-imgBorder"]
    > ![vytvořit pracovní prostor](./media/notebooks/sentinel-notebooks-azureml-create.png)

1. Na stránce **Machine Learning** zadejte následující informace a pak vyberte **zkontrolovat + vytvořit**.

    |Pole|Popis|
    |--|--|
    |Předplatné|Vyberte předplatné Azure, které chcete použít.|
    |Skupina prostředků|Použijte stávající skupinu prostředků, kterou máte v předplatném, nebo zadejte název a vytvořte novou skupinu prostředků. Skupina prostředků obsahuje související prostředky pro řešení Azure. V tomto příkladu používáme **AzureMLRG**.|
    |Název pracovního prostoru|Zadejte jedinečný název, který identifikuje váš pracovní prostor. V tomto příkladu používáme **testworkspace1**. Názvy musí být v rámci skupiny prostředků jedinečné. Použijte název, který se dá snadno vyvolat a odlišit z pracovních prostorů vytvořených jinými uživateli.|
    |Oblast|Vyberte umístění, které je nejblíže vašim uživatelům a datovým prostředkům, abyste mohli vytvořit pracovní prostor.|
    |Edice pracovního prostoru|V tomto příkladu jako typ pracovního prostoru vyberte **Basic** . Typ pracovního prostoru (Basic & Enterprise) určuje funkce, ke kterým budete mít přístup a ceny.|

    > [!div class="mx-imgBorder"]
    > ![zadat podrobnosti pracovního prostoru](./media/notebooks/sentinel-notebooks-azureml-basics.png)

1. Zkontrolujte informace, ověřte, zda je správná, a pak vyberte **vytvořit** a spusťte nasazení pracovního prostoru.

    > [!div class="mx-imgBorder"]
    > ![zkontrolovat podrobnosti pracovního prostoru](./media/notebooks/sentinel-notebooks-azureml-review.png)

    Vytvoření pracovního prostoru v cloudu může trvat několik minut, než se na stránce **Přehled** zobrazí aktuální stav nasazení.

    > [!div class="mx-imgBorder"]
    > ![nasazení pracovního prostoru](./media/notebooks/sentinel-notebooks-azureml-deploy.png)

Po dokončení nasazení můžete poznámkové bloky spustit v novém pracovním prostoru Azure ML.

> [!div class="mx-imgBorder"]
> ![nasazení pracovního prostoru bylo úspěšné.](./media/notebooks/sentinel-notebooks-azureml-complete.png)

## <a name="launch-a-notebook-using-your-azure-ml-workspace"></a>Spuštění poznámkového bloku pomocí pracovního prostoru Azure ML

1. V Azure Portal přejděte na   >    >  **poznámkové bloky** služby Azure Sentinel Threat Management, kde vidíte poznámkové bloky, které poskytuje Azure Sentinel.

    > [!TIP]
    > Vyberte možnost **průvodci & zpětnou vazbu** a otevřete tak podokno s další nápovědu a pokyny pro poznámkové bloky.
    > ![Zobrazit průvodce pro Poznámkový blok](./media/notebooks/sentinel-azure-notebooks-guides.png)

1. Vyberte jednotlivé poznámkové bloky pro zobrazení jejich popisů, požadovaných datových typů a zdrojů dat.

    > [!div class="mx-imgBorder"]
    > ![Zobrazit podrobnosti poznámkového bloku](./media/notebooks/sentinel-azure-notebooks-view.png)

1. Vyberte Poznámkový blok, který chcete použít, a pak vyberte **Spustit Poznámkový blok** pro klonování a konfiguraci poznámkového bloku do nového projektu Azure Notebooks, který se připojuje k vašemu pracovnímu prostoru Azure Sentinel. Po dokončení procesu se Poznámkový blok otevře v rámci Azure Notebooks, abyste ho mohli spustit.

    > [!div class="mx-imgBorder"]
    > ![vybrat Poznámkový blok](./media/notebooks/sentinel-azure-notebooks-select.png)

1. V části pracovní prostor AzureML vyberte pracovní prostor Azure ML a pak vyberte **Spustit**.

    > [!div class="mx-imgBorder"]
    > ![spustit Poznámkový blok](./media/notebooks/sentinel-azure-notebooks-launch.png)

1. Vyberte výpočetní instanci. Pokud nemáte výpočetní instanci, udělejte toto:
    1. Vyberte znaménko plus (+) pro spuštění průvodce **vytvořením nové instance COMPUTE** .

        > [!div class="mx-imgBorder"]
        > ![Průvodce spuštěním výpočetní instance](./media/notebooks/sentinel-azure-notebooks-compute-wizard.png)

    1. Na stránce **Nová instance COMPUTE** zadejte požadované informace a pak vyberte **vytvořit**.

        > [!div class="mx-imgBorder"]
        > ![vytvořit výpočetní instanci](./media/notebooks/sentinel-azure-notebooks-compute-create.png)

1. Po vytvoření vašeho poznámkového bloku vyberte v každé buňce ikonu spustit, která spustí kód v poznámkových blocích.

    > [!div class="mx-imgBorder"]
    > ![spustit Poznámkový blok](./media/notebooks/sentinel-azure-notebooks-run.png)

Doporučit

- Rychlý úvod k dotazování na data ve službě Azure Sentinel najdete v části [Začínáme poznámkových blocích k Azure ml a průvodci sentinelou v Azure](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/A%20Getting%20Started%20Guide%20For%20Azure%20Sentinel%20ML%20Notebooks.ipynb) .

- Další ukázkové poznámkové bloky najdete v podsložce [**ukázek a**](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/Sample-Notebooks) poznámkových bloků GitHubu. Tyto ukázkové poznámkové bloky byly uloženy s daty, takže je snazší zobrazit zamýšlený výstup. Doporučujeme zobrazit tyto poznámkové bloky v [nbviewer](https://nbviewer.jupyter.org/).

- Podsložka GitHubu [**HowTos**](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/HowTos) obsahuje poznámkové bloky, například: nastavení výchozí verze Pythonu, konfigurace DSVM, vytváření záložek Azure Sentinel z poznámkového bloku a další předměty.

Poskytnuté poznámkové bloky jsou určené jako užitečné nástroje a jako ilustrace a ukázky kódu, které můžete použít při vývoji vlastních poznámkových bloků.

Uvítáme zpětnou vazbu, ať už návrhy, žádosti o funkce, přidané poznámkové bloky, zprávy o chybách nebo vylepšení a přidání do stávajících poznámkových bloků. Pokud chcete vytvořit problém nebo rozvětvení a nahrát příspěvek, přejít na [GitHub komunity služby Azure Sentinel](https://github.com/Azure/Azure-Sentinel) .

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak začít používat Jupyter Notebook ve službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:

- [Proaktivní vylovení hrozeb](hunting.md)
- [Použití záložek k ukládání zajímavých informací při lovu](bookmarks.md)

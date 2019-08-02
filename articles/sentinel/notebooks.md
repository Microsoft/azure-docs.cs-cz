---
title: Možnosti lovu pomocí poznámkových bloků v Azure Sentinel Preview | Microsoft Docs
description: Tento článek popisuje, jak používat poznámkové bloky s možnostmi lovu do Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 6372a7958caf108903321e5ee87ea6bf1a42271c
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2019
ms.locfileid: "68689526"
---
# <a name="use-jupyter-notebooks-to-hunt-for-security-threats"></a>Použití poznámkových bloků Jupyter k prolovení bezpečnostních hrozeb

> [!IMPORTANT]
> Služba Azure Sentinel je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Základem Azure Sentinel je úložiště dat; kombinuje dotazování s vysokým výkonem, dynamické schéma a škálování na obrovské objemy dat. Portál Sentinel Azure a všechny nástroje Sentinel Azure používají pro přístup k tomuto úložišti dat společné rozhraní API. Stejné rozhraní API je také k dispozici pro externí nástroje, jako jsou [Jupyter](https://jupyter.org/) notebooky a Python. I když je možné na portálu provádět mnoho běžných úloh, Jupyter rozšiřuje rozsah toho, co můžete s těmito daty dělat. Kombinuje plnou programovatelnost s velkou kolekcí knihoven pro strojové učení, vizualizaci a analýzu dat. Tyto atributy Jupyter přesvědčivý Nástroj pro vyšetřování a lov zabezpečení.

![Příklad poznámkového bloku](./media/notebooks/sentinel-notebooks-map.png)

Prostředí Jupyter jsme integroval na portál Sentinel Azure, což usnadňuje vytváření a spouštění poznámkových bloků pro analýzu vašich dat. Knihovna *Kqlmagic* poskytuje připevňování, který umožňuje převzít dotazy z Sentinel Azure a spustit je přímo v poznámkovém bloku. Dotazy používají [dotazovací jazyk Kusto](https://kusto.azurewebsites.net/docs/query/index.html). Několik poznámkových bloků vyvinutých analytiky zabezpečení Microsoftu se zabalí do Azure Sentinel. Některé z těchto poznámkových bloků jsou sestaveny pro konkrétní scénář a lze je použít tak, jak jsou. Jiné jsou určené jako vzorky k ilustraci techniků a funkcí, které můžete zkopírovat nebo přizpůsobit pro použití ve vlastních poznámkových blocích. Další poznámkové bloky se můžou importovat taky z GitHubu komunity služby Azure Sentinel.

Integrované prostředí Jupyter používá [Azure Notebooks](https://notebooks.azure.com/) k ukládání, sdílení a spouštění poznámkových bloků. Tyto poznámkové bloky můžete také spustit lokálně (Pokud máte prostředí Pythonu a Jupyter v počítači) nebo v jiných prostředích JupterHub, jako je například Azure Databricks.

Poznámkové bloky mají dvě komponenty:

- rozhraní založené na prohlížeči, kde zadáváte a spouštíte dotazy a kód a kde se zobrazují výsledky spuštění.
- *jádro* , které zodpovídá za analýzu a provádění samotného kódu. 

V Azure Notebooks se toto jádro ve výchozím nastavení spouští na *výpočetním a úložném cloudovém úložišti Azure Free* . Pokud vaše poznámkové bloky obsahují složité modely strojového učení nebo vizualizace, měli byste zvážit použití výkonnějších a vyhrazených výpočetních prostředků, jako jsou třeba [datové vědy Virtual Machines](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/) (DSVM). Poznámkové bloky ve vašem účtu jsou důvěrné, pokud se nerozhodnete je sdílet.

Poznámkové bloky Azure Sentinel používají spoustu oblíbených knihoven Pythonu, jako jsou PANDAS, matplotlib, rozostření a další. Existuje velký počet dalších balíčků Pythonu, ze kterých si můžete vybrat, a to pokrývající oblasti:

- vizualizace a grafika
- zpracování a analýza dat
- statistiky a číselné výpočty
- Machine Learning a obsáhlý Learning

Také jsme vydali nějaké open source nástroje zabezpečení Jupyter v balíčku s názvem [msticpy](https://github.com/Microsoft/msticpy/). Tento balíček se používá v mnoha zahrnutých poznámkových blocích. Nástroje Msticpy jsou navržené specificky pro pomoc s vytvářením poznámkových bloků pro lov a vyšetřování a aktivně pracujeme na nových funkcích a vylepšeních.

Mezi úvodní poznámkové bloky patří:

- **Řízené šetření – výstrahy procesu**: Slouží k rychlému třídění výstrah analýzou aktivity na ovlivněných hostitelích.
- **S asistencí – Průzkumník hostitele Windows**: Umožňuje prozkoumat aktivitu účtu, provádění procesů, síťové aktivity a další události na hostiteli.  
- **S asistencí – Office 365 – prozkoumávání**: Loven pro podezřelou aktivitu Office 365 ve více sadách dat O365.

[Úložiště GitHub komunity služby Azure Sentinel](https://github.com/Azure/Azure-Sentinel) je umístění všech budoucích poznámkových bloků služby Azure Sentinel sestavených společností Microsoft nebo, které přispěly od komunity.

## <a name="run-a-notebook"></a>Spuštění poznámkového bloku

V následujícím příkladu vytvoříme Azure Notebooks projekt na portálu Sentinel Azure a naplníme projekt pomocí poznámkových bloků. Před použitím těchto poznámkových bloků je vhodné vytvořit kopii poznámkového bloku a na kopii pracovat. Práce na kopiích vám umožní bezpečně aktualizovat budoucí verze poznámkových bloků bez přepsání jakýchkoli dat.

1. Na portálu Sentinel Azure klikněte v navigační nabídce na poznámkové **bloky** . Pokud chcete vytvořit nový projekt Azure Notebooks, klikněte na **klonovat poznámkové bloky Azure Sentinel** nebo otevřete existující projekty poznámkových bloků a klikněte na **Přejít do vašich poznámkových bloků**.
  
   ![vybrat poznámkové bloky](./media/notebooks/sentinel-azure-notebooks-home.png)

2. Pokud jste v předchozím kroku zvolili **klonovat poznámkové bloky Azure Sentinel** , zobrazí se následující dialogové okno. Kliknutím na **importovat** naklonujte úložiště GitHub do svého projektu Azure Notebooks. Pokud nemáte existující účet Azure Notebooks, zobrazí se výzva, abyste si ho vytvořili a přihlásili.

   ![Importovat Poznámkový blok](./media/notebooks/sentinel-notebooks-clone.png)

3. Při vytváření nového projektu je nutné pojmenovat projekt – použijte výchozí název nebo zadejte nový. Nekontrolovat **rekurzivní** možnost klonování – Tato možnost se týká propojených úložišť GitHub. Po kliknutí na **Import** začne probíhat klonování obsahu GitHubu, což může trvat několik minut.

   ![Importovat Poznámkový blok](./media/notebooks/sentinel-create-project.png)

4. Otevřete složku **poznámkových bloků** a zobrazte si poznámkové bloky. Každý Poznámkový blok vás provede kroky pro provedení průzkumu nebo šetření. Knihovny a další závislosti, které Poznámkový blok potřebuje, se dají nainstalovat přímo z poznámkového bloku nebo pomocí jednoduché konfigurační procedury. Konfigurace, která se přiřazuje vašemu projektu vašeho poznámkového bloku zpátky k vašemu předplatnému Azure Sentinel, se automaticky zřídí v předchozích krocích. Vaše poznámkové bloky jsou připravené ke spuštění v pracovním prostoru Sentinel služby Azure Sentinel Log Analytics.

   ![Importovat úložiště](./media/notebooks/sentinel-open-notebook1.png)

5. Otevřete Poznámkový blok. Ve výchozím nastavení je vybráno bezplatné výpočetní prostředí pro spuštění poznámkových bloků (zvýrazněno). Pokud jste nakonfigurovali DSVM, který se má použít (viz výše), vyberte DSVM a před otevřením prvního poznámkového bloku ho ověřte. Klikněte na Poznámkový blok a otevřete ho.

   ![vybrat Poznámkový blok](./media/notebooks/sentinel-open-notebook2.png)

6. Výběr verze Pythonu Při prvním otevření poznámkového bloku vás může zobrazit výzva k výběru verze jádra. V takovém případě vyberte jádro, které chcete použít: Python 3,6 nebo novější by měl být vybraným jádrem (v pravém horním rohu okna poznámkového bloku).

   ![vybrat Poznámkový blok](./media/notebooks/sentinel-select-kernel.png)

Rychlý úvod k dotazování na data ve službě Azure Sentinel najdete v poznámkovém bloku getstarted ve složce hlavní poznámkové bloky. [](https://github.com/Azure/Azure-Sentinel/blob/master/Notebooks/Get%20Started.ipynb) Další ukázkové poznámkové bloky najdete v podsložce **ukázek poznámkových bloků** . Ukázkové notebooky byly uloženy s daty, takže je snazší zobrazit zamýšlený výstup (doporučujeme je zobrazit v [nbviewer](https://nbviewer.jupyter.org/)). Složka **HowTos** obsahuje poznámkové bloky, například: nastavení výchozí verze Pythonu, konfigurace DSVM, vytváření záložek Azure Sentinel z poznámkového bloku a další předměty.

Tyto poznámkové bloky jsou určené jako užitečné nástroje a jako ilustrace a ukázky kódu, které můžete použít při vývoji vlastních poznámkových bloků.

Uvítáme zpětnou vazbu, ať už návrhy, žádosti o funkce, přidané poznámkové bloky, zprávy o chybách nebo vylepšení a přidání do stávajících poznámkových bloků. Pokud chcete vytvořit problém nebo rozvětvení a nahrát příspěvek, přejít na [GitHub komunity služby Azure Sentinel](https://github.com/Azure/Azure-Sentinel) .

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak začít používat Jupyter poznámkové bloky v Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:

- [Proaktivní vylovení hrozeb](hunting.md)
- [Použití záložek k ukládání zajímavých informací při lovu](bookmarks.md)

---
title: Možnosti typu pomocí poznámkových bloků ve verzi Preview Sentinelu Azure | Dokumentace Microsoftu
description: Tento článek popisuje postup použití poznámkových bloků s funkcemi Azure Sentinelu typu.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: ae9d52e4a26825e4318a6afb8aadc86ac29fa2b3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60402960"
---
# <a name="use-jupyter-notebooks-to-hunt-for-security-threats"></a>Použití poznámkových bloků Jupyter pro dost možná nejlepší bezpečnostní hrozby

> [!IMPORTANT]
> Azure Sentinel je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Základem Azure Sentinelu je úložiště dat; kombinuje vysoký výkon dotazování, dynamické schéma a škálování pro velké objemy dat svazků. Na portálu Azure Sentinelu a všechny nástroje Azure Sentinelu přístupu k tomuto úložišti dat pomocí běžných rozhraní API. Stejného rozhraní API je také dostupná pro externí nástroje, jako například [Jupyter](https://jupyter.org/) poznámkových bloků a Python. Přestože mnoho běžných úloh se dá provést na portálu, rozšiřuje Jupyter oboru můžete dělat s těmito daty. Kombinuje úplnou možností programování s velkou kolekci knihoven pro machine learning, vizualizace a data analýzy. Tyto atributy zkontrolujte Jupyter tohoto nástroje pro zkoumání zabezpečení a typu.

![Příklad poznámkového bloku](./media/notebooks/sentinel-nb-mapandtimeline.png)

Integrovali jsme prostředí Jupyter na portál Azure Sentinelu usnadňuje vytvoření a provedení poznámkových bloků a analyzoval vaše data. *Kqlmagic* knihovna poskytuje lepidlo, které vám umožní využít dotazy z Sentinelu Azure a spouštět je přímo do poznámkového bloku. Použití dotazů [Kusto dotazovací jazyk](https://kusto.azurewebsites.net/docs/query/index.html). Několik poznámkové bloky, vypracovanou organizací cccppf některé analytikům zabezpečení od Microsoftu, jsou sbaleny s Sentinelu Azure. Některé tyto poznámkové bloky jsou vytvořené pro konkrétní scénář a může sloužit jako-je. Ostatní jsou určeny jako ukázky pro ilustraci technik a funkce, které můžete zkopírovat nebo přizpůsobit pro použití ve vašich vlastních poznámkových bloků. Další poznámkových bloků může také importovat z komunity Azure Sentinelu Githubu.

Integrované prostředí Jupyter používá [poznámkových bloků Azure](https://notebooks.azure.com/) k ukládání, sdílet a spouštět poznámkové bloky. Tyto poznámkové bloky můžete také spouštět místně (když na počítači máte prostředí Pythonu a Jupyter) nebo v jiných prostředích JupterHub, jako je Azure Databricks.

Poznámkové bloky mají dvě součásti:

- rozhraní založené na prohlížeči, kde zadejte a spouštění dotazů a kód, a kde se zobrazují výsledky spuštění.
- *jádra* , který je zodpovědné za parsování a spuštění samotný kód. 

V poznámkových bloků Azure, tento jádra běží na Azure *bezplatné cloudové výpočty a úložiště* ve výchozím nastavení. Pokud vašich poznámkových bloků zahrnutí modely komplexních strojového učení nebo vizualizace byste měli zvážit použití výkonnější, vyhrazené výpočetní prostředky, jako například [virtuální počítače pro datové vědy](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/) (DSVM). Poznámkové bloky ve vašem účtu zachovány privátní, pokud budete chtít sdílet.

Poznámkových bloků Azure Sentinelu použít mnoho Oblíbené knihovny jazyka Python, jako je například balíčky pandas, matplotlib, rozostření a dalších. Existuje spousta další balíčky Python si můžete vybírat, pokrývá oblasti, jako například:

- vizualizace a grafiky
- zpracování dat a analýza
- statistiky a numerické výpočty
- Strojové učení a hloubkového učení

Vydali jsme také některé nástroje zabezpečení Jupyter open source v balíčku s názvem [msticpy](https://github.com/Microsoft/msticpy/). Tento balíček se používá v mnoha zahrnuté poznámkových bloků. Msticpy nástroje jsou navrženy speciálně pro s vytvářením poznámkových bloků pro typu a šetření a aktivně pracujeme na nových funkcí a vylepšení.

Počáteční poznámkových bloků patří:

- **Šetření – proces výstrahy na základě**: Umožňuje rychle posoudit upozornění díky analýze činnost v ovlivněných hostitelích.
- **Na základě typu – Průzkumník Windows hostitele**: Umožňuje prozkoumat aktivity účtu, spuštění procesu, síťovou aktivitou a dalších událostí na hostiteli.  
- **Na základě typu – Office 365 zkoumání**: Nejlepší pro podezřelé aktivity Office 365 v několika datových sad O365.

[Úložiště GitHub Sentinelu komunity Azure](https://github.com/Azure/Azure-Sentinel) je umístění pro všechny budoucí poznámkových bloků Azure Sentinelu vytvořené microsoftem nebo přispět od komunity.

## <a name="run-a-notebook"></a>Spuštění poznámkového bloku

V následujícím příkladu vytvoříme projekt poznámkových bloků Azure z portálu Azure Sentinelu sestavování projektu s poznámkovými bloky. Než začnete používat tyto poznámkové bloky, je vhodné vytvořit kopii Poznámkový blok a pracovat na kopii. Práce na kopie umožňuje bezpečně aktualizovat na budoucí verze poznámkových bloků, aniž byste museli přepsat data.

1. Na portálu Azure Sentinelu, klikněte na tlačítko **poznámkových bloků** v navigační nabídce. Chcete-li vytvořit nový projekt poznámkových bloků Azure, klikněte na tlačítko **Sentinelu poznámkových bloků Azure klonování** nebo otevřete existující poznámkových bloků projekty kliknutím **přejít na vašich poznámkových bloků**.
  
   ![Vyberte poznámkové bloky](./media/notebooks/sentinel-az-notebooks-home.png)

2. Pokud jste zvolili **Sentinelu poznámkových bloků Azure klonování** v předchozím kroku, zobrazí se následující dialogové okno. Klikněte na tlačítko **Import** naklonujte úložiště GitHub do svého projektu poznámkových bloků Azure. Pokud nemáte účet Azure poznámkových bloků, zobrazí se výzva k vytvoření a přihlaste se.

   ![Import poznámkového bloku](./media/notebooks/sentinel-nb-signin-and-clone.png)

3. Při vytváření nového projektu, budete muset projekt pojmenujte – použijte výchozí název nebo typ v nové. Nezaškrtávejte políčko **klonování rekurzivně** možnost – tuto možnost odkazuje na propojenou úložiště GitHub. Kliknutím na **Import** začne klonování obsahu GitHub, což může trvat několik minut.

   ![Import poznámkového bloku](./media/notebooks/sentinel-create-nb-project.png)

4. Otevřít **poznámkových bloků** složky poznámkových bloků. Každý Poznámkový blok vás provede kroky pro provádění hunt nebo šetření. Knihovny a další závislosti vyžadované Poznámkový blok lze nainstalovat z poznámkového bloku samotné nebo prostřednictvím procedury jednoduchou konfiguraci. Konfigurace, která spojují projektu Poznámkový blok zpět k vašemu předplatnému Azure Sentinelu automaticky zřídí v předchozích krocích. Poznámkové bloky jsou připravené ke spuštění pracovního prostoru Log Analytics Sentinelu Azure.

   ![Import úložiště](./media/notebooks/sentinel-open-notebook1.png)

5. Otevřete Poznámkový blok. Bezplatnými výpočetními funkcemi je standardně vybraná možnost spouštět poznámkové bloky (zvýrazněno). Pokud jste nakonfigurovali DSVM použití (viz výše), vyberte datové VĚDY a ověřit před otevřením první poznámkového bloku. Klikněte na poznámkového bloku otevřete.

   ![Vyberte poznámkového bloku](./media/notebooks/sentinel-open-notebook2.png)

6. Výběr verze Pythonu. Při prvním otevření poznámkového bloku, můžete být vyzváni k výběru verze jádra. Pokud tomu tak není, vyberte jádra použít následujícím způsobem. Python 3.6 nebo novější musí být vybraný jádra (v horním pravém rohu okna Poznámkový blok).

   ![Vyberte poznámkového bloku](./media/notebooks/sentinel-select-kernel.png)

Rychlý úvod k dotazování na data v Azure Sentinelu, podívejte se na [GetStarted](https://github.com/Azure/Azure-Sentinel/blob/master/Notebooks/Get%20Started.ipynb) poznámkového bloku ve složce hlavní poznámkových bloků. Další ukázkové poznámkové bloky najdete v **ukázkové poznámkové bloky** podsložky. Ukázkové poznámkové bloky se uložily. s daty, aby bylo snazší zjistit zamýšlený výstup (doporučujeme je v zobrazení [nbviewer](https://nbviewer.jupyter.org/)). **HowTos** složka obsahuje poznámkové bloky s popisem, například: nastavení výchozí verze Python, konfigurace DSVM, vytváření Sentinelu Azure záložky z poznámkového bloku a dalších tématech.

Tyto poznámkové bloky jsou určeny jako obou užitečných nástrojů a obrázky a ukázky kódu, které můžete použít při vývoji vlastních poznámkových bloků.

Vítáme zpětnou vazbu, zda máte nějaké návrhy, žádosti o funkce, kterými přispěli poznámkových bloků, zpráv o chybách nebo vylepšení a dodatky k existující poznámkových bloků. Přejděte [Azure Sentinelu komunitě GitHub](https://github.com/Azure/Azure-Sentinel) vytvoření problému nebo rozvětvení a nahrát příspěvek.

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jak chcete začít používat aplikace Jupyter notebook v Azure Sentinelu. Další informace o Azure Sentinelu, naleznete v následujících článcích:

- [Proaktivně hunt hrozby](hunting.md)
- [Použití záložek ke uložit zajímavé informace při typu](bookmarks.md)
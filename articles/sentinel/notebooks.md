---
title: Používání poznámkových bloků s Azure Sentinelem pro účely hledání zabezpečení
description: Tento článek popisuje, jak používat poznámkové bloky s možnostmi lovu Azure Sentinelu.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 11/25/2019
ms.openlocfilehash: 84b72a71ed2de910bce44b0c3f3309782f096680
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77581833"
---
# <a name="use-jupyter-notebooks-to-hunt-for-security-threats"></a>Používejte poznámkové bloky Jupyter k hledání bezpečnostních hrozeb

Základem Azure Sentinelu je úložiště dat; kombinuje vysoce výkonné dotazování, dynamické schéma a škálování na masivní datové svazky. Portál Azure a všechny nástroje Azure Sentinelu používají pro přístup k tomuto úložišti dat společné rozhraní API. Stejné rozhraní API je k dispozici také pro externí nástroje, jako jsou poznámkové bloky [Jupyter](https://jupyter.org/) a Python. Zatímco mnoho běžných úkolů lze provádět na portálu, Jupyter rozšiřuje rozsah, co můžete dělat s těmito daty. Kombinuje plnou programovatelnost s obrovskou sbírkou knihoven pro strojové učení, vizualizaci a analýzu dat. Tyto atributy dělají jupyter přesvědčivý nástroj pro bezpečnostní vyšetřování a lov.

![ukázkový poznámkový blok](./media/notebooks/sentinel-notebooks-map.png)

Prostředí Jupyter jsme integrovali do portálu Azure, takže můžete snadno vytvářet a spouštět poznámkové bloky pro analýzu dat. Knihovna *Kqlmagic* poskytuje lepidlo, které umožňuje přijímat dotazy z Azure Sentinelu a spouštět je přímo do poznámkového bloku. Dotazy používají [dotazovací jazyk Kusto](https://kusto.azurewebsites.net/docs/query/index.html). Několik poznámkových bloků vyvinutých některými analytiky zabezpečení microsoftu je zabaleno s Azure Sentinelem. Některé z těchto poznámkových bloků jsou vytvořeny pro konkrétní scénář a lze je použít tak, jak jsou. Jiné jsou určeny jako ukázky pro ilustraci technik a funkcí, které můžete kopírovat nebo přizpůsobit pro použití ve vlastních poznámkových blocích. Ostatní poznámkové bloky se taky mohou importovat z githubu komunity Azure Sentinelu.

Integrované prostředí Jupyter používá [poznámkové bloky Azure](https://notebooks.azure.com/) k ukládání, sdílení a spouštění poznámkových bloků. Tyto poznámkové bloky můžete také spustit místně, pokud máte v počítači prostředí Pythonu a Jupyter nebo v jiných prostředích JupterHub, jako jsou Azure Databricks.

Poznámkové bloky mají dvě součásti:

- Rozhraní založené na prohlížeči, kde zadáte a spustíte dotazy a kód a kde jsou zobrazeny výsledky spuštění.
- *Jádro,* které je zodpovědné za analýzu a spuštění samotného kódu. 

V poznámkových blocích Azure se toto jádro ve výchozím nastavení spouští na Azure *Free Cloud Compute and Storage*. Pokud vaše poznámkové bloky obsahují složité modely strojového učení nebo vizualizace, zvažte použití výkonnějších a efektivnějších výpočetních prostředků, jako jsou [virtuální počítače pro datové vědy](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/) (DSVM). Poznámkové bloky ve vašem účtu jsou soukromé, pokud se je nerozhodnete sdílet.

Poznámkové bloky Azure Sentinelu používají mnoho oblíbených knihoven Pythonu, jako jsou pandy, matplotlib, bokeh a další. Existuje mnoho dalších balíčků Pythonu, ze kterých si můžete vybrat, pokrývající oblasti, jako jsou:

- Vizualizace a grafika
- Zpracování a analýza dat
- Statistika a numerické výpočty
- Strojové učení a hluboké učení

Také jsme vydali některé open-source Jupyter bezpečnostní nástroje v balíčku s názvem [msticpy](https://github.com/Microsoft/msticpy/). Tento balíček se používá v mnoha zahrnutých noteboocích. Nástroje Msticpy jsou navrženy speciálně pro vytváření notebooků pro lov a vyšetřování a aktivně pracujeme na nových funkcích a vylepšeních.

Mezi počáteční poznámkové bloky patří:

- **Řízené šetření – výstrahy procesů**: Umožňuje rychle třídí výstrahy analýzou aktivity na postiženém hostiteli nebo hostitelích.
- **Sledování s průvodcem – Průzkumník hostitelů systému Windows**: Umožňuje prozkoumat aktivitu účtu, spuštění procesů, aktivity v síti a další události na hostiteli.
- **Sledování asistencí – Office365-Exploring**: Hledání podezřelých aktivit Office 365 ve více datových sadách Office 365.

[Úložiště GitHub komunity Azure Sentinel uvažuje](https://github.com/Azure/Azure-Sentinel) umístění pro všechny budoucí poznámkové bloky Azure Sentinelu vytvořené společností Microsoft nebo přispěly z komunity.

Pokud chcete poznámkové bloky používat, musíte mít účet poznámkových bloků Azure. Další informace najdete [v tématu Úvodní příručka: Přihlaste se a nastavte ID uživatele](https://docs.microsoft.com/azure/notebooks/quickstart-sign-in-azure-notebooks) z dokumentace k poznámkovým blokům Azure. K vytvoření tohoto účtu můžete použít možnost **Zaregistrovat se pro poznámkové bloky Azure** na panelu příkazů v **Azure Sentinelu – poznámkové bloky**:

> [!div class="mx-imgBorder"]
>![Registrace pro poznámkové bloky Azure](./media/notebooks/sentinel-azure-sign-up-azure-notebooks.png)

Poznámkový blok můžete spustit přímo z Azure Sentinelu nebo naklonovat všechny poznámkové bloky Azure Sentinelu do nového projektu poznámkových bloků Azure.

## <a name="run-a-notebook-from-azure-sentinel"></a>Spuštění poznámkového bloku z Azure Sentinelu
 
1. Na webu Azure najdete v > **poznámkovébloky****pro správu hrozeb** **Azure Sentinelu** > , kde uvidíte poznámkové bloky, které Azure Sentinel poskytuje. 

2. Vyberte jednotlivé poznámkové bloky, které chcete přečíst jejich popisy, požadované datové typy a zdroje dat. Například:
    
    > [!div class="mx-imgBorder"]
    > ![spuštění poznámkového bloku](./media/notebooks/sentinel-azure-notebooks-launch.png)

3. Vyberte poznámkový blok, který chcete použít, a pak vyberte **Spustit poznámkový blok (preview),** chcete-li klonovat a nakonfigurovat poznámkový blok do nového projektu poznámkových bloků Azure, který se připojí k vašemu pracovnímu prostoru Azure Sentinelu. Po dokončení procesu se poznámkový blok otevře v poznámkových blocích Azure, abyste ho měli spustit.

## <a name="clone-azure-sentinel-notebooks-to-a-new-azure-notebooks-project"></a>Klonování poznámkových bloků Azure Sentinel u nového projektu poznámkových bloků Azure

Tento postup vytvoří projekt poznámkových bloků Azure pro vás, který obsahuje poznámkové bloky Azure Sentinelu. Pak můžete poznámkové bloky spustit tak, jak jsou, nebo je provést změnami a pak je spustit.

1. Na webu Azure najděte do > **poznámkových bloků** **správy hrozeb** **Azure Sentinel** > a na panelu příkazů vyberte **Klonovat poznámkové bloky:**
  
    > [!div class="mx-imgBorder"]
    >![Klonovat poznámkové bloky, volba](./media/notebooks/sentinel-azure-clone-notebooks.png)

2. Když se zobrazí následující dialogové okno, vyberte **Importovat** pro klonování úložiště GitHub do projektu poznámkových bloků Azure. Pokud nemáte existující účet Poznámkových bloků Azure, budete vyzváni k jeho vytvoření a přihlášení.

   ![Import poznámkového bloku](./media/notebooks/sentinel-notebooks-clone.png)

3. V dialogovém okně **Nahrát úložiště GitHub** nevybíresně nevybírejte **Clone,** protože tato možnost odkazuje na propojená úložiště GitHub. Pro název projektu použijte výchozí název nebo zadejte nový. Pak klikněte na **Import** a začněte klonovat obsah GitHubu, jehož dokončení může trvat několik minut.

   ![Import poznámkového bloku](./media/notebooks/sentinel-create-project.png)

4. Otevřete právě vytvořený projekt a pak otevřete složku **Poznámkové bloky,** abyste viděli poznámkové bloky. Například:

   ![Import ovat repo](./media/notebooks/sentinel-open-notebook1.png)

Pak můžete spustit poznámkové bloky z poznámkových bloků Azure. Pokud se chcete vrátit k těmto poznámkovým blokům z Azure Sentinelu, vyberte **Přejít na poznámkové bloky** z panelu příkazů v **Azure Sentinelu – poznámkové bloky**:

> [!div class="mx-imgBorder"]
>![Přejít na možnost Poznámkové bloky](./media/notebooks/sentinel-azure-to-go-notebooks.png)


## <a name="using-notebooks-to-hunt"></a>Používání poznámkových bloků k lovu

Každý zápisník vás provede kroky pro provedení lovu nebo vyšetřování. Knihovny a další závislosti potřebné pro poznámkový blok lze nainstalovat ze samotného poznámkového bloku nebo pomocí jednoduchého postupu konfigurace. Konfigurace, která váže projekt poznámkového bloku zpět k předplatnému Azure Sentinelu, se automaticky zřídí v předchozích krocích.

1. Pokud ještě nejste v poznámkových blocích Azure, můžete použít možnost **Přejít na poznámkové bloky** z panelu příkazů v **Azure Sentinelu – poznámkové bloky**:
    
    > [!div class="mx-imgBorder"]
    >![Přejít na možnost Poznámkové bloky](./media/notebooks/sentinel-azure-to-go-notebooks.png)
    
    V poznámkových blocích Azure vyberte **Moje projekty**, pak projekt, který obsahuje poznámkové bloky Azure Sentinelu, a nakonec složku **Poznámkové bloky.**
    
2. Než otevřete poznámkový blok, uvědomte si, že ve výchozím nastavení je pro spuštění poznámkových bloků vybrána možnost Bezplatné výpočetní prostředky:
    
   ![vybrat poznámkový blok](./media/notebooks/sentinel-open-notebook2.png)
    
    Pokud jste nakonfigurovali virtuální počítače pro datové vědy (DSVM), které mají být používány, jak je vysvětleno v úvodu, vyberte DSVM a ověřte před otevřením prvního poznámkového bloku. 

3. Vyberte poznámkový blok, který chcete otevřít.
    
    Při prvním otevření poznámkového bloku můžete být vyzváni k výběru verze jádra. Pokud se nezobrazí výzva, můžete vybrat verzi jádra z **jádra** >  **Změnit jádro**a pak vybrat verzi, která je alespoň 3.6. Vybraná verze jádra se zobrazí v pravém horním rohu okna notebooku:
    
   ![vybrat poznámkový blok](./media/notebooks/sentinel-select-kernel.png)

4. Než provedete změny v poznámkovém bloku, který jste stáhli, je vhodné vytvořit kopii původního poznámkového bloku a pracovat na kopii. Chcete-li to provést, vyberte **soubor** > **vytvořit kopii**. Práce na kopiích umožňuje bezpečně aktualizovat na budoucí verze poznámkových bloků bez přepsání dat.
    
    Nyní jste připraveni spustit nebo upravit vybraný poznámkový blok.

Doporučení:

- Rychlý úvod k dotazování na data v Azure Sentinelu nahlédnout do poznámkového bloku [GetStarted](https://github.com/Azure/Azure-Sentinel/blob/master/Notebooks/Get%20Started.ipynb) v hlavní složce **Poznámkové bloky.** 

- Další ukázkové poznámkové bloky najdete v podsložce **Ukázkové poznámkové** bloky. Tyto ukázkové poznámkové bloky byly uloženy s daty, takže je snadnější vidět zamýšlený výstup. Doporučujeme tyto notebooky zobrazit v [nbvieweru](https://nbviewer.jupyter.org/). 

- Složka **HowTos** obsahuje poznámkové bloky popisující například: Nastavení výchozí verze Pythonu, konfigurace DSVM, vytváření záložek Azure Sentinel z poznámkového bloku a dalších předmětů.

Poskytnuté poznámkové bloky jsou určeny jako užitečné nástroje a jako ilustrace a ukázky kódu, které můžete použít při vývoji vlastních poznámkových bloků.

Uvítáme zpětnou vazbu, ať už návrhy, žádosti o funkce, přispěl poznámkové bloky, hlášení chyb nebo vylepšení a dodatky k existující poznámkové bloky. Přejděte na [GitHub komunity Azure Sentinelu,](https://github.com/Azure/Azure-Sentinel) abyste vytvořili problém nebo rozvinu a nahráli příspěvek.

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli, jak začít používat poznámkové bloky Jupyter v Azure Sentinelu. Další informace o Azure Sentinelu najdete v následujících článcích:

- [Proaktivně lovit hrozby](hunting.md)
- [Použití záložek pro ukládání zajímavých informací při lovu](bookmarks.md)

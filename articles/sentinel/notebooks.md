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
ms.date: 11/25/2019
ms.openlocfilehash: bf63d5c8cb46fd791508af40dcefd7b39d4ba9de
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "83652024"
---
# <a name="use-jupyter-notebooks-to-hunt-for-security-threats"></a>Použití poznámkových bloků Jupyter k prolovení bezpečnostních hrozeb

Základem Azure Sentinel je úložiště dat; kombinuje dotazování s vysokým výkonem, dynamické schéma a škálování na obrovské objemy dat. Azure Portal a všechny nástroje Sentinel Azure používají pro přístup k tomuto úložišti dat společné rozhraní API. Stejné rozhraní API je také k dispozici pro externí nástroje, jako jsou [Jupyter](https://jupyter.org/) notebooky a Python. I když je možné na portálu provádět mnoho běžných úloh, Jupyter rozšiřuje rozsah toho, co můžete s těmito daty dělat. Kombinuje plnou programovatelnost s velkou kolekcí knihoven pro strojové učení, vizualizaci a analýzu dat. Tyto atributy Jupyter přesvědčivý Nástroj pro vyšetřování a lov zabezpečení.

![Příklad poznámkového bloku](./media/notebooks/sentinel-notebooks-map.png)

Prostředí Jupyter jsme integroval do Azure Portal, což usnadňuje vytváření a spouštění poznámkových bloků pro analýzu dat. Knihovna *Kqlmagic* poskytuje připevňování, který umožňuje převzít dotazy z Sentinel Azure a spustit je přímo v poznámkovém bloku. Dotazy používají [dotazovací jazyk Kusto](https://kusto.azurewebsites.net/docs/query/index.html). Několik poznámkových bloků vyvinutých analytiky zabezpečení Microsoftu se zabalí do Azure Sentinel. Některé z těchto poznámkových bloků jsou sestaveny pro konkrétní scénář a lze je použít tak, jak jsou. Jiné jsou určené jako vzorky k ilustraci techniků a funkcí, které můžete zkopírovat nebo přizpůsobit pro použití ve vlastních poznámkových blocích. Další poznámkové bloky se můžou importovat taky z GitHubu komunity služby Azure Sentinel.

Integrované prostředí Jupyter používá [Azure Notebooks](https://notebooks.azure.com/) k ukládání, sdílení a spouštění poznámkových bloků. Tyto poznámkové bloky můžete také spustit místně, pokud máte prostředí Pythonu a Jupyter na vašem počítači nebo v jiných prostředích JupterHub, jako je například Azure Databricks.

Poznámkové bloky mají dvě komponenty:

- Rozhraní založené na prohlížeči, kde zadáváte a spouštíte dotazy a kód a kde se zobrazují výsledky spuštění.
- *Jádro* , které zodpovídá za analýzu a provádění samotného kódu. 

V Azure Notebooks ve výchozím nastavení toto jádro běží na *výpočetním a úložném cloudovém úložišti Azure Free*. Pokud vaše notebooky obsahují složité modely a vizualizace strojového učení, zvažte použití výkonnějších a vyhrazených výpočetních prostředků, jako jsou třeba [datové vědy Virtual Machines](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/) (DSVM). Poznámkové bloky ve vašem účtu jsou důvěrné, pokud se nerozhodnete je sdílet.

Poznámkové bloky Azure Sentinel používají spoustu oblíbených knihoven Pythonu, jako jsou PANDAS, matplotlib, rozostření a další. Existuje spousta dalších balíčků Pythonu, ze kterých si můžete vybrat, včetně oblastí, jako jsou:

- Vizualizace a grafika
- Zpracování a analýza dat
- Statistiky a číselné výpočty
- Machine Learning a obsáhlý Learning

Také jsme vydali nějaké open source nástroje zabezpečení Jupyter v balíčku s názvem [msticpy](https://github.com/Microsoft/msticpy/). Tento balíček se používá v mnoha zahrnutých poznámkových blocích. Nástroje Msticpy jsou navržené specificky pro pomoc s vytvářením poznámkových bloků pro lov a vyšetřování a aktivně pracujeme na nových funkcích a vylepšeních.

Mezi úvodní poznámkové bloky patří:

- **Řízené šetření – výstrahy procesu**: umožňuje rychlou třídění výstrah analýzou aktivity na ovlivněných hostitelích nebo hostitelích.
- **Prostředí s asistencí – Průzkumník hostitelů Windows**: umožňuje prozkoumat aktivitu účtu, provádění procesů, aktivitu sítě a další události na hostiteli.
- **S asistencí – Office 365-prozkoumávání**: loven pro podezřelou aktivitu Office 365 ve více sadách Office 365 sady dat.

[Úložiště GitHub komunity služby Azure Sentinel](https://github.com/Azure/Azure-Sentinel) je umístění všech budoucích poznámkových bloků služby Azure Sentinel sestavených společností Microsoft nebo, které přispěly od komunity.

Chcete-li používat poznámkové bloky, musíte mít účet Azure Notebooks. Další informace najdete v tématu [rychlý Start: přihlášení a nastavení ID uživatele](https://docs.microsoft.com/azure/notebooks/quickstart-sign-in-azure-notebooks) z dokumentace k Azure Notebooks. Pokud chcete vytvořit tento účet, můžete na panelu příkazů v části **Azure Sentinel-poznámkové bloky**použít možnost **zaregistrovat se Azure Notebooks** :

> [!div class="mx-imgBorder"]
>![Registrace Azure Notebooks možnosti](./media/notebooks/sentinel-azure-sign-up-azure-notebooks.png)

Můžete spustit Poznámkový blok přímo z Azure Sentinel nebo klonovat všechny poznámkové bloky Azure Sentinel do nového projektu Azure Notebooks.

## <a name="run-a-notebook-from-azure-sentinel"></a>Spuštění poznámkového bloku z Azure Sentinel
 
1. V Azure Portal přejděte na **Azure Sentinel**  >  **Threat management**  >  **poznámkové bloky**služby Azure Sentinel Threat Management, kde vidíte poznámkové bloky, které poskytuje Azure Sentinel. 

2. Vyberte jednotlivé poznámkové bloky pro čtení jejich popisů, požadovaných datových typů a zdrojů dat. Příklad:
    
    > [!div class="mx-imgBorder"]
    > ![spustit Poznámkový blok](./media/notebooks/sentinel-azure-notebooks-launch.png)

3. Vyberte Poznámkový blok, který chcete použít, a pak vyberte **Spustit Poznámkový blok (Preview)** a naklonujte a nakonfigurujte ho do nového Azure Notebooks projektu, který se připojuje k vašemu pracovnímu prostoru Azure Sentinel. Po dokončení procesu se Poznámkový blok otevře v rámci Azure Notebooks, abyste ho mohli spustit.

## <a name="clone-azure-sentinel-notebooks-to-a-new-azure-notebooks-project"></a>Klonovat poznámkové bloky Sentinel Azure do nového projektu Azure Notebooks

Tento postup vytvoří Azure Notebooks projekt, který obsahuje poznámkové bloky Sentinel Azure. Pak můžete poznámkové bloky spustit tak, jak jsou, nebo je změnit a pak je spustit.

1. V Azure Portal přejděte na **Azure Sentinel**  >  **Threat management**  >  **poznámkové bloky** služby Azure Sentinel Threat Management a pak na panelu příkazů vyberte **klonovat poznámkové bloky** :
  
    > [!div class="mx-imgBorder"]
    >![Možnost klonování poznámkových bloků](./media/notebooks/sentinel-azure-clone-notebooks.png)

2. Když se zobrazí následující dialogové okno, vyberte **importovat** a naklonujte úložiště GitHub do projektu Azure Notebooks. Pokud nemáte existující účet Azure Notebooks, zobrazí se výzva, abyste si ho vytvořili a přihlásili.

   ![Importovat Poznámkový blok](./media/notebooks/sentinel-notebooks-clone.png)

3. V dialogovém okně **nahrát úložiště GitHubu** nevybírejte **rekurzivní rekurzivní** operace, protože tato možnost odkazuje na propojené úložiště GitHub. Jako název projektu použijte výchozí název nebo zadejte nový. Pak kliknutím na **importovat** zahajte klonování obsahu GitHubu, což může trvat několik minut, než se dokončí.

   ![Importovat Poznámkový blok](./media/notebooks/sentinel-create-project.png)

4. Otevřete projekt, který jste právě vytvořili, a pak otevřete složku **poznámkové bloky** a zobrazte si poznámkové bloky. Příklad:

   ![Importovat úložiště](./media/notebooks/sentinel-open-notebook1.png)

Pak můžete spustit poznámkové bloky z Azure Notebooks. Pokud se chcete do těchto poznámkových bloků vrátit z Azure Sentinel, vyberte v příkazovém řádku v části **Azure Sentinel-poznámkové**bloky **Přejít na poznámkové bloky** :

> [!div class="mx-imgBorder"]
>![Přejít k možnosti poznámkových bloků](./media/notebooks/sentinel-azure-to-go-notebooks.png)


## <a name="using-notebooks-to-hunt"></a>Použití poznámkových bloků ke ke loven

Každý Poznámkový blok vás provede kroky pro provedení průzkumu nebo šetření. Knihovny a další závislosti, které Poznámkový blok potřebuje, se dají nainstalovat přímo z poznámkového bloku nebo pomocí jednoduché konfigurační procedury. Konfigurace, která se přiřazuje vašemu projektu vašeho poznámkového bloku zpátky k vašemu předplatnému Azure Sentinel, se automaticky zřídí v předchozích krocích.

1. Pokud ještě nejste v Azure Notebooks, můžete použít možnost **Přejít na poznámkové bloky** z panelu příkazů v části **Azure Sentinel-poznámkové bloky**:
    
    > [!div class="mx-imgBorder"]
    >![Přejít k možnosti poznámkových bloků](./media/notebooks/sentinel-azure-to-go-notebooks.png)
    
    V Azure Notebooks vyberte **Moje projekty**, projekt, který obsahuje poznámkové bloky Azure Sentinel, a nakonec složku **poznámkové bloky** .
    
2. Než otevřete Poznámkový blok, mějte na paměti, že ve výchozím nastavení je pro spuštění poznámkových bloků vybráno bezplatné výpočetní prostředí:
    
   ![vybrat Poznámkový blok](./media/notebooks/sentinel-open-notebook2.png)
    
    Pokud jste nakonfigurovali Virtual Machines pro datové vědy (DSVM), jak je vysvětleno v úvodu, před otevřením prvního poznámkového bloku vyberte DSVM a proveďte ověření. 

3. Vyberte Poznámkový blok, který chcete otevřít.
    
    Při prvním otevření poznámkového bloku se může zobrazit výzva k výběru verze jádra. Pokud se vám nezobrazí výzva, můžete vybrat verzi jádra z **Kernel**  >   **jádra změny**jádra a pak vybrat verzi, která bude aspoň 3,6. Vybraná verze jádra se zobrazí v pravém horním rohu okna poznámkového bloku:
    
   ![vybrat Poznámkový blok](./media/notebooks/sentinel-select-kernel.png)

4. Předtím, než provedete všechny změny v poznámkovém bloku, který jste stáhli, je vhodné vytvořit kopii původního poznámkového bloku a na kopii pracovat. Provedete to tak, že vyberete **soubor**  >  **vytvořit kopii**. Práce na kopiích vám umožní bezpečně aktualizovat budoucí verze poznámkových bloků bez přepsání jakýchkoli dat.
    
    Teď jste připraveni spustit nebo upravit vybraný Poznámkový blok.

Doporučit

- Rychlý úvod k dotazování na data ve službě Azure Sentinel najdete v poznámkovém bloku [getstarted](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/345cf9f7c8f6137f5af4593a3f9d7568acd6cbc2/DeprecatedNotebooks/Get%20Started.ipynb) ve složce hlavní **poznámkové bloky** . 

- Další ukázkové poznámkové bloky najdete v podsložce **ukázek poznámkových bloků** . Tyto ukázkové poznámkové bloky byly uloženy s daty, takže je snazší zobrazit zamýšlený výstup. Doporučujeme zobrazit tyto poznámkové bloky v [nbviewer](https://nbviewer.jupyter.org/). 

- Složka **HowTos** obsahuje poznámkové bloky, například: nastavení výchozí verze Pythonu, konfigurace DSVM, vytváření záložek Azure Sentinel z poznámkového bloku a další předměty.

Poskytnuté poznámkové bloky jsou určené jako užitečné nástroje a jako ilustrace a ukázky kódu, které můžete použít při vývoji vlastních poznámkových bloků.

Uvítáme zpětnou vazbu, ať už návrhy, žádosti o funkce, přidané poznámkové bloky, zprávy o chybách nebo vylepšení a přidání do stávajících poznámkových bloků. Pokud chcete vytvořit problém nebo rozvětvení a nahrát příspěvek, přejít na [GitHub komunity služby Azure Sentinel](https://github.com/Azure/Azure-Sentinel) .

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak začít používat Jupyter poznámkové bloky v Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:

- [Proaktivní vylovení hrozeb](hunting.md)
- [Použití záložek k ukládání zajímavých informací při lovu](bookmarks.md)

---
title: Spuštění poznámkových bloků Jupyter ve vašem pracovním prostoru
titleSuffix: Azure Machine Learning
description: Přečtěte si, jak spustit Poznámkový blok Jupyter, aniž byste opustili pracovní prostor v Azure Machine Learning Studiu.
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 01/19/2021
ms.openlocfilehash: 5748bf3d428102e296067dc5d1927ba487d575bc
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102518717"
---
# <a name="run-jupyter-notebooks-in-your-workspace"></a>Spuštění poznámkových bloků Jupyter ve vašem pracovním prostoru

Přečtěte si, jak spustit poznámkové bloky Jupyter přímo ve vašem pracovním prostoru v Azure Machine Learning Studiu. I když můžete spustit [Jupyter](https://jupyter.org/) nebo [JupyterLab](https://jupyterlab.readthedocs.io), můžete také upravit a spustit poznámkové bloky, aniž byste museli opustit pracovní prostor.

Informace o tom, jak vytvářet a spravovat soubory, včetně poznámkových bloků, najdete v tématu [Vytvoření a Správa souborů ve vašem pracovním prostoru](how-to-manage-files.md).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://aka.ms/AMLFree).
* Machine Learning pracovní prostor. Další informace najdete v tématu [Vytvoření pracovního prostoru Azure Machine Learning](how-to-manage-workspace.md).

## <a name="edit-a-notebook"></a>Úprava poznámkového bloku

Pokud chcete upravit Poznámkový blok, otevřete libovolný Poznámkový blok umístěný v části **uživatelské soubory** v pracovním prostoru. Klikněte na buňku, kterou chcete upravit.  Pokud v této části nemáte žádné poznámkové bloky, přečtěte si téma [Vytvoření a Správa souborů ve vašem pracovním prostoru](how-to-manage-files.md).

Poznámkový blok můžete upravovat bez připojení k výpočetní instanci.  Pokud chcete v poznámkovém bloku spustit buňky, vyberte nebo vytvořte výpočetní instanci.  Pokud vyberete zastavenou výpočetní instanci, automaticky se spustí při spuštění první buňky.

Když je spuštěná výpočetní instance, můžete také použít dokončování kódu využívající [technologii IntelliSense](https://code.visualstudio.com/docs/editor/intellisense)v jakémkoli poznámkovém bloku Pythonu.

Na panelu nástrojů poznámkového bloku můžete také spustit Jupyter nebo JupyterLab.  Azure Machine Learning neposkytuje aktualizace a opravují chyby z Jupyter nebo JupyterLab, protože se jedná o open source produkty mimo hranici podpora Microsoftu.

## <a name="focus-mode"></a>Detailní režim

Pomocí detailního režimu můžete rozšířit aktuální zobrazení, abyste se mohli soustředit na aktivní karty. Detailní režim skryje Průzkumníka souborů poznámkových bloků.

1. Na panelu nástrojů okna terminálu vyberte **detailní režim** pro zapnutí detailního režimu. V závislosti na šířce okna se to může nacházet pod položkou nabídky **...** na panelu nástrojů.
1. V detailním režimu se po výběru **standardního zobrazení** vraťte do standardního zobrazení.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/focusmode.gif" alt-text="Přepnout detailní režim nebo standardní zobrazení":::

## <a name="use-intellisense"></a>Používání technologie IntelliSense

[IntelliSense](https://code.visualstudio.com/docs/editor/intellisense) je podpora dokončování kódu, která zahrnuje několik funkcí: seznam členů, informace o parametrech, rychlé informace a dokončování slov. Tyto funkce vám pomůžou získat další informace o kódu, který používáte, sledovat parametry, které píšete, a přidávat volání vlastností a metod s pouze několika klávesami.  

Při psaní kódu použijte kombinaci kláves CTRL + mezerník pro aktivaci technologie IntelliSense.

## <a name="clean-your-notebook-preview"></a>Vyčištění poznámkového bloku (Preview)

> [!IMPORTANT]
> Funkce Shromážděte je aktuálně ve verzi Public Preview.
> Verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

V průběhu vytváření poznámkového bloku obvykle skončíte s buňkami, které jste použili pro zkoumání nebo ladění dat. Funkce *shromáždit* vám pomůže vytvořit čistý Poznámkový blok bez těchto cizích buněk.

1. Spusťte všechny buňky v poznámkovém bloku.
1. Vyberte buňku obsahující kód, který chcete spustit pro nový Poznámkový blok. Například kód, který odesílá experiment, nebo možná kód, který registruje model.
1. Vyberte ikonu **shromáždit** , která se zobrazí na panelu nástrojů buňky.
    :::image type="content" source="media/how-to-run-jupyter-notebooks/gather.png" alt-text="Snímek obrazovky: výběr ikony shromáždění":::
1. Zadejte název nového poznámkového bloku "shromážděného".  

Nový Poznámkový blok obsahuje jenom buňky kódu se všemi buňkami, které jsou potřebné k vytvoření stejných výsledků jako buňky, kterou jste vybrali pro shromažďování.

## <a name="save-and-checkpoint-a-notebook"></a>Uložení a vytvoření kontrolního bodu poznámkového bloku

Azure Machine Learning vytvoří soubor kontrolního bodu při vytváření souboru *ipynb* .

Na panelu nástrojů poznámkového bloku vyberte nabídku a pak **soubor &gt; uložte a kontrolní bod** , abyste ručně uložili Poznámkový blok a přidali do něj soubor kontrolního bodu, který je přidružený k poznámkovému bloku.

:::image type="content" source="media/how-to-run-jupyter-notebooks/file-save.png" alt-text="Snímek obrazovky s nástrojem pro uložení v panelu nástrojů Poznámkový blok":::

Každý Poznámkový blok se automaticky uloží každých 30 sekund. Automatické ukládání aktualizuje pouze počáteční soubor *ipynb* , nikoli soubor kontrolního bodu.
 
Výběrem **kontrolního bodu** v nabídce Poznámkový blok vytvořte pojmenovaný kontrolní bod a obnovte Poznámkový blok na uložený kontrolní bod.

## <a name="export-a-notebook"></a>Export poznámkového bloku

Na panelu nástrojů pro Poznámkový blok vyberte nabídku a pak **exportujte jako** a exportujte Poznámkový blok jako kterýkoli z podporovaných typů:

* Poznámkový blok
* Python
* HTML
* LaTeX

:::image type="content" source="media/how-to-run-jupyter-notebooks/export-notebook.png" alt-text="Export poznámkového bloku do počítače":::

Exportovaný soubor je uložen ve vašem počítači.

## <a name="run-a-notebook-or-python-script"></a>Spuštění poznámkového bloku nebo skriptu Pythonu

Pokud chcete spustit Poznámkový blok nebo skript Pythonu, musíte se nejdřív připojit ke spuštěné [výpočetní instanci](concept-compute-instance.md).

* Pokud nemáte výpočetní instanci, vytvořte ji pomocí těchto kroků:

    1. Na panelu nástrojů poznámkového bloku nebo skriptu napravo od rozevírací nabídky výpočty vyberte **+ Nový výpočet**. V závislosti na velikosti obrazovky to může být umístěno v nabídce **...** .
        :::image type="content" source="media/how-to-run-jupyter-notebooks/new-compute.png" alt-text="Vytvoření nového výpočetního prostředí":::
    1. Pojmenujte výpočetní prostředky a vyberte **Velikost virtuálního počítače**. 
    1. Vyberte **Vytvořit**.
    1. Instance COMPUTE je automaticky připojena k souboru.  Nyní můžete spouštět buňky poznámkového bloku nebo skriptu Pythonu pomocí nástroje nalevo od instance Compute.

* Pokud máte zastavenou výpočetní instanci, vyberte možnost  **Spustit COMPUTE** napravo od rozevíracího seznamu výpočty. V závislosti na velikosti obrazovky to může být umístěno v nabídce **...** .

    :::image type="content" source="media/how-to-run-jupyter-notebooks/start-compute.png" alt-text="Spustit výpočetní instanci":::

Pouze můžete zobrazit a použít výpočetní instance, které vytvoříte.  Vaše **uživatelské soubory** jsou uložené odděleně od virtuálního počítače a sdílí se mezi všemi výpočetními instancemi v pracovním prostoru.

### <a name="view-logs-and-output"></a>Zobrazit protokoly a výstup

Pomocí [widgetů poznámkového bloku](/python/api/azureml-widgets/azureml.widgets) můžete zobrazit průběh běhu a protokolů. Pomůcka je asynchronní a poskytuje aktualizace až do dokončení školení. Pomůcky Azure Machine Learning jsou také podporovány v Jupyter a JupterLab.

:::image type="content" source="media/how-to-run-jupyter-notebooks/jupyter-widget.png" alt-text="Snímek obrazovky: widget poznámkového bloku Jupyter ":::

## <a name="explore-variables-in-the-notebook"></a>Prozkoumat proměnné v poznámkovém bloku

Na panelu nástrojů Poznámkový blok použijte nástroj **Průzkumník proměnných** k zobrazení názvu, typu, délky a vzorových hodnot pro všechny proměnné, které byly vytvořeny v poznámkovém bloku.

:::image type="content" source="media/how-to-run-jupyter-notebooks/variable-explorer.png" alt-text="Snímek obrazovky: Nástroj Průzkumník proměnných":::

Vyberte nástroj pro zobrazení okna Průzkumníka proměnných.

:::image type="content" source="media/how-to-run-jupyter-notebooks/variable-explorer-window.png" alt-text="Snímek obrazovky: okno Průzkumníka proměnných":::

## <a name="navigate-with-a-toc"></a>Navigace pomocí obsahu

Na panelu nástrojů pro Poznámkový blok použijte nástroj  **obsah** k zobrazení nebo skrytí obsahu.  Spustí Markdownu buňku s nadpisem, který se má přidat do obsahu. Kliknutím na položku v tabulce se posuňte k této buňce v poznámkovém bloku.  

:::image type="content" source="media/how-to-run-jupyter-notebooks/table-of-contents.png" alt-text="Snímek obrazovky: obsah v poznámkovém bloku":::

## <a name="change-the-notebook-environment"></a>Změna prostředí poznámkového bloku

Panel nástrojů Poznámkový blok umožňuje změnit prostředí, na kterém Poznámkový blok běží.  

Tyto akce nezmění stav poznámkového bloku ani hodnoty jakýchkoli proměnných v poznámkovém bloku:

|Akce  |Výsledek  |
|---------|---------| --------|
|Zastavit jádro     |  Zastaví všechny spuštěné buňky. Při spuštění buňky se jádro automaticky restartuje. |
|Přejít do jiného oddílu pracovního prostoru     |     Běžící buňky jsou zastaveny. |

Tyto akce resetují stav poznámkového bloku a obnoví všechny proměnné v poznámkovém bloku.

|Akce  |Výsledek  |
|---------|---------| --------|
| Změna jádra | Poznámkový blok používá nové jádro |
| Přepnout výpočetní prostředky    |     Poznámkový blok automaticky používá nové výpočetní prostředky. |
| Resetovat výpočetní výkon | Spustí se znovu při pokusu o spuštění buňky. |
| Zastavit výpočetní výkon     |    Nespustí se žádné buňky.  |
| Otevření poznámkového bloku v Jupyter nebo JupyterLab     |    Poznámkový blok se otevřel na nové kartě.  |

## <a name="add-new-kernels"></a>Přidat nové jádra

[Pomocí terminálu ](how-to-access-terminal.md#add-new-kernels) vytvořte a přidejte do své výpočetní instance nové jádro. Poznámkový blok automaticky vyhledá všechny jádra Jupyter nainstalované na připojené výpočetní instanci.

Pomocí rozevíracího seznamu jádro na pravé straně můžete změnit některý z nainstalovaných jader.  


### <a name="status-indicators"></a>Indikátory stavu

Indikátor vedle rozevíracího seznamu **COMPUTE** zobrazuje jeho stav.  Stav je zobrazen také v rozevíracím seznamu.  

|Barva |Stav výpočtů |
|---------|---------| 
| Green | Spuštěná výpočetní prostředí |
| Red |Výpočet se nezdařil | 
| Black | Služba COMPUTE byla zastavena |
|  Světle modrá |Výpočetní prostředí pro vytváření, spouštění, restartování a nastavování |
|  Les |Výpočetní odstranění, zastavení |

Indikátor vedle rozevíracího seznamu **jádro** zobrazuje jeho stav.

|Barva |Stav jádra |
|---------|---------|
|  Green |Připojení jádra, nečinné, zaneprázdněné|
|  Les |Jádro není připojeno. |

## <a name="find-compute-details"></a>Najít podrobnosti o COMPUTE

Podrobnosti o vašich výpočetních instancích najdete na stránce **COMPUTE** v [studiu](https://ml.azure.com).

## <a name="useful-keyboard-shortcuts"></a>Užitečné klávesové zkratky
Podobně jako Jupyter poznámkové bloky mají modální uživatelské rozhraní Azure Machine Learning Studio poznámkách. Klávesnice funguje jinak v závislosti na tom, v jakém režimu je buňka poznámkového bloku. Azure Machine Learning Studio poznámkové bloky podporují pro danou buňku kódu následující dva režimy: režim příkazů a režim úprav.

### <a name="command-mode-shortcuts"></a>Klávesové zkratky v režimu příkazu

Buňka je v režimu příkazu, když není k dispozici žádný textový kurzor, který je vyzván k zadání. Když je buňka v režimu příkazu, můžete Poznámkový blok upravit jako celek, ale ne psát do jednotlivých buněk. Stisknutím `ESC` nebo pomocí myši vyberte mimo oblast editoru buňky režim příkazu.  Levý okraj aktivní buňky je modrý a plný a jeho tlačítko **Spustit** je modré.

   :::image type="content" source="media/how-to-run-jupyter-notebooks/command-mode.png" alt-text="Buňka poznámkového bloku v režimu příkazu ":::

| Zástupce                      | Description                          |
| ----------------------------- | ------------------------------------|
| Enter                         | Přechod do režimu úprav             |        
| Shift + Enter                 | Spustit buňku, vyberte níže         |     
| Ctrl/Command + ENTER       | Spustit buňku                            |
| ALT + ENTER                   | Spustit buňku, Vložit buňku kódu níže    |
| Ctrl/Command + Alt + Enter | Spustit buňku, Vložit buňku Markdownu pod|
| ALT + R                       | Spustit vše      |                       
| Y                             | Převést buňku na kód    |                         
| M                             | Převést buňku na Markdownu  |                       
| Nahoru/K                          | Vybrat buňku výše    |               
| Dolů/J                        | Vybrat buňku níže    |               
| A                             | Vložit buňku kódu nad  |            
| B                             | Vložit buňku kódu níže   |           
| Ctrl/Command + Shift + A   | Vložit buňku Markdownu výše    |      
| Ctrl/Command + Shift + B   | Vložit buňku Markdownu pod   |       
| ×                             | Vyjmout vybranou buňku    |               
| C                             | Kopírovat vybranou buňku   |               
| Shift + V                     | Vložit vybranou buňku výše           |
| V                             | Vložit vybranou buňku níže    |       
| D D                           | Odstranit vybranou buňku|                
| O                             | Přepnout výstup         |              
| SHIFT + O                     | Přepnout posouvání výstupu   |          
| I JSEM                           | Jádro přerušení |                   
| 0 0                           | Restartovat jádro |                     
| SHIFT + MEZERNÍK                 | Posunout nahoru  |                         
| Místo                         | Posunout dolů|
| Karta                           | Změnit zaměření na další položku, která se zaměřuje na výběr (když je zakázané depeše tabulátoru)|
| Ctrl/Command + S           | Uložit Poznámkový blok |                      
| 1                             | Změnit na H1|                       
| 2                             | Změnit na H2|                        
| 3                             | Změnit na H3|                        
| 4                             | Změnit na H4 |                       
| 5                             | Změnit na H5 |                       
| 6                             | Změnit na h6 |                       

### <a name="edit-mode-shortcuts"></a>Klávesové zkratky v režimu úprav

Režim úprav je označen textovým kurzorem, který vás vyzve k zadání v oblasti editoru. Když je buňka v režimu úprav, můžete zadat text do buňky. Stisknutím `Enter` nebo pomocí myši přejděte na oblast editoru buňky a vyberte režim úprav. Levý okraj aktivní buňky je zelený a šrafování a jeho tlačítko **Spustit** je zelené. V režimu úprav se také zobrazí výzva kurzoru v buňce.

   :::image type="content" source="media/how-to-run-jupyter-notebooks/edit-mode.png" alt-text="Buňka poznámkového bloku v režimu úprav":::

Pomocí následujících klávesových zkratek můžete snáze procházet a spouštět kód v Azure Machine Learning poznámkových blocích v režimu úprav.

| Zástupce                      | Description|                                     
| ----------------------------- | ----------------------------------------------- |
| Escape                        | Přejít do režimu příkazu|  
| Ctrl/Command + mezerník       | Aktivace technologie IntelliSense |
| Shift + Enter                 | Spustit buňku, vyberte níže |                         
| Ctrl/Command + ENTER       | Spustit buňku  |                                      
| ALT + ENTER                   | Spustit buňku, Vložit buňku kódu níže  |              
| Ctrl/Command + Alt + Enter | Spustit buňku, Vložit buňku Markdownu pod  |          
| ALT + R                       | Spustit všechny buňky     |                              
| Nahoru                            | Přesunout kurzor nahoru nebo předchozí buňku    |             
| Dolů                          | Přesunout kurzor dolů nebo další buňka |                  
| Ctrl/Command + S           | Uložit Poznámkový blok   |                                
| Ctrl/Command + up          | Přejít na začátek buňky   |                             
| Ovládací prvek/příkaz + šipka dolů        | Přejít na konec buňky |                                 
| Karta                           | Dokončení nebo odsazení kódu (Pokud je povolené depeše na kartě) |
| Ctrl/Command + M           | Povolit/zakázat soutisk karty  |                       
| Ctrl/Command +]           | Rážce |                                         
| Control/Command + [           | Zmenšit odsazení  |                                        
| Ovládací prvek/příkaz + A           | Vybrat vše|                                      
| Ctrl/Command + Z           | Zpět |                                           
| Ctrl/Command + Shift + Z   | Opakovat |                                           
| Ctrl/Command + Y           | Opakovat |                                           
| Ovládací prvek/příkaz + Home        | Přejít na začátek buňky|                                
| Ovládací prvek/příkaz + end         | Přejít na konec buňky   |                               
| Ctrl/Command + šipka vlevo        | Přejít o jedno slovo doleva |                               
| Ctrl/Command + šipka vpravo       | Přejít o jedno slovo doprava |                              
| Ctrl/Command + Backspace   | Odstranit slovo před |                             
| Control/Command + Delete      | Odstranit slovo za |                              
| Ovládací prvek/příkaz +/           | Přepnout komentář na buňku

## <a name="troubleshooting"></a>Řešení potíží

* Pokud se nemůžete připojit k poznámkovému bloku, ujistěte se, že komunikace webového soketu **není zakázaná** . Aby funkce COMPUTE instance Jupyter fungovala, musí být povolená komunikace webového soketu. Ujistěte se prosím, že vaše síť povoluje připojení pomocí protokolu WebSocket k *. instances.azureml.net a *. instances.azureml.ms. 

* Když je instance služby COMPUTE nasazená v pracovním prostoru privátního propojení, dá se [k ní dostat jenom z virtuální sítě](https://docs.microsoft.com/azure/machine-learning/how-to-secure-training-vnet#compute-instance). Pokud používáte vlastní soubor DNS nebo hostitele, přidejte položku pro < název instance >. < oblasti >. instances.azureml.ms s privátní IP adresou privátního koncového bodu pracovního prostoru. Další informace najdete v článku o [vlastním serveru DNS](./how-to-custom-dns.md?tabs=azure-cli) .
    
## <a name="next-steps"></a>Další kroky

* [Spuštění prvního experimentu](tutorial-1st-experiment-sdk-train.md)
* [Zálohování úložiště souborů pomocí snímků](../storage/files/storage-snapshots-files.md)
* [Práce v zabezpečených prostředích](./how-to-secure-training-vnet.md#compute-instance)

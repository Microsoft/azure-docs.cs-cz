---
title: Výuka – vytvoření a spuštění poznámkového bloku Jupyter – náhled poznámkových bloků Azure
description: Zjistěte, jak vytvořit a spustit poznámkový blok Jupyter ve verzi Azure Notebooks Preview, který ukazuje proces lineární regrese v datové vědě.
ms.topic: tutorial
ms.date: 01/11/2019
ms.openlocfilehash: 8a1c13f41ef1588b040b3540b852d83764c6ce79
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75660813"
---
# <a name="tutorial-create-and-run-a-jupyter-notebook-with-python"></a>Kurz: vytvoření a spuštění notebooku Jupyter s Pythonem

Tento kurz vás provede procesem použití poznámkových bloků Azure k vytvoření úplného poznámkového bloku Jupyter, který ukazuje jednoduchou lineární regresi. V průběhu tohoto kurzu se seznámíte s uzu poznámkového bloku Jupyter, které zahrnuje vytváření různých buněk, spuštění buněk a prezentaci poznámkového bloku jako prezentaci.

Dokončený poznámkový blok najdete na [GitHubu – ukázky poznámkových bloků Azure](https://github.com/Microsoft/AzureNotebooks/tree/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps). Tento kurz však začíná novým projektem a prázdným poznámkovým blokem, takže můžete začít vytvářet krok za krokem.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření poznámkového bloku projektu s ukázkovými daty
> * Použití rozhraní poznámkového bloku k vytvoření různých typů buněk
> * Spuštění poznámkového bloku
> * Uložení poznámkového bloku
> * Ladění poznámkového bloku v kódu Visual Studia

## <a name="create-the-project"></a>Vytvoření projektu

1. Přejděte na [Poznámkové bloky Azure](https://notebooks.azure.com) a přihlaste se. (Podrobnosti najdete [v tématu Úvodní příručka – přihlášení k poznámkovým blokům Azure).](quickstart-sign-in-azure-notebooks.md)

1. Na stránce veřejného profilu vyberte **moje projekty** v horní části stránky:

    ![Odkaz Moje projekty v horní části okna prohlížeče](media/quickstarts/my-projects-link.png)

1. Na stránce **Moje projekty** vyberte + Nový projekt (klávesová **zkratka:** n); tlačítko se může **+** zobrazit pouze tak, jako by bylo okno prohlížeče úzké:

    ![Příkaz Nový projekt na stránce Moje projekty](media/quickstarts/new-project-command.png)

1. Ve vyskakovacím okně **Vytvořit nový projekt,** které se zobrazí, zadejte nebo nastavte následující podrobnosti a pak vyberte **Vytvořit**:

   - **Název projektu**: Příklad lineární regrese - Cvrčkové cvrlikání
   - **ID projektu**: příklad lineární regrese
   - **Veřejný projekt**: (schváleno)
   - **Vytvoření README.md**: (vymazáno)

1. Po chvíli vás poznámkové bloky Azure přenese k novému projektu.

## <a name="create-the-data-file"></a>Vytvoření datového souboru

Lineární regresní model, který vytvoříte v poznámkovém bloku, nakreslí data ze souboru v projektu nazvaného *cricket_chirps.csv*. Tento soubor můžete vytvořit buď zkopírováním z [GitHubu – ukázky poznámkových bloků Azure](https://github.com/Microsoft/AzureNotebooks/tree/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps), nebo zadáním dat přímo. Následující části popisují oba přístupy.

### <a name="upload-the-data-file"></a>Nahrání datového souboru

1. Na řídicím panelu projektu v poznámkových blocích Azure vyberte **Nahrát** > **z adresy URL.**
1. Ve vyskakovacím okno zadejte do **adresy URL souboru** následující adresu URL a *v adrese cricket_chirps.csv* v **souboru Název**položky a vyberte možnost **Hotovo**.

    ```url
    https://raw.githubusercontent.com/Microsoft/AzureNotebooks/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps/cricket_chirps.csv
    ```

1. Soubor *cricket_chirps.csv* by se nyní měl zobrazit v seznamu souborů projektu:

    ![Nově vytvořený soubor CSV zobrazený v seznamu souborů projektu](media/tutorial/csv-file-in-project.png)

### <a name="create-a-file-from-scratch"></a>Vytvoření souboru od začátku

1. Na řídicím panelu projektu v poznámkových blocích Azure vyberte **+ Nový** > **prázdný soubor.**
1. V seznamu souborů projektu se zobrazí pole. Zadejte *cricket_chirps.csv* a stiskněte Enter.
1. Klepněte pravým tlačítkem myši na *soubor cricket_chirps.csv* a vyberte **příkaz Upravit soubor**.
1. Do editoru, který se zobrazí, zadejte následující data:

    ```csv
    Chirps/Minute,Temperature
    20,88.6
    16,71.6
    19.8,93.3
    18.4,84.3
    17.1,80.6
    15.5,75.2
    14.7,69.7
    17.1,82
    15.4,69.4
    16.2,83.3
    15,79.6
    17.2,82.6
    16,80.6
    17,83.5
    14.4,76.3
    ```

1. Vyberte **Uložit soubor,** chcete-li soubor uložit a vrátit se na řídicí panel projektu.

## <a name="install-project-level-packages"></a>Instalace balíčků na úrovni projektu

V poznámkovém bloku můžete vždy `!pip install` použít příkazy jako v buňce kódu k instalaci požadovaných balíčků. Tyto příkazy jsou však spuštěny při každém spuštění buněk kódu poznámkového bloku a může trvat značnou dobu. Z tohoto důvodu můžete místo toho nainstalovat balíčky na úrovni projektu pomocí souboru. `requirements.txt`

1. Pomocí procesu popsaného v části [Vytvoření souboru od začátku](#create-a-file-from-scratch) vytvořte soubor pojmenovaný `requirements.txt` s následujícím obsahem:

    ```text
    matplotlib==3.0.0
    numpy==1.15.0
    pandas==0.23.4
    scikit-learn==0.20.0
    ```

    Chcete-li také `requirements.txt` nahrát soubor z místního počítače, jak je popsáno v části [Nahrání datového souboru](#upload-the-data-file).

1. Na řídicím panelu projektu vyberte **Nastavení projektu**.
1. Ve vyskakovacím čísle vyberte kartu **Životní prostředí** a pak vyberte **+Přidat**.
1. V prvním rozevíracím ovládacím prvku (operaci) v **části Kroky instalace prostředí**zvolte **Requirements.txt**.
1. V druhém rozevíracím ovládacím prvku (název souboru) zvolte *requirements.txt* (soubor, který jste vytvořili).
1. Ve třetím rozevíracím ovládacím prvku (verze Pythonu) zvolte **Python verze 3.6**.
1. Vyberte **Uložit**.

![Karta Prostředí Nastavení projektu určující soubor requirements.txt](media/tutorial/tutorial-requirements-txt.png)

S tímto krokem instalace na místě, všechny poznámkový blok, který spustíte v projektu bude spuštěn v prostředí, kde jsou nainstalovány tyto balíčky.

## <a name="create-and-run-a-notebook"></a>Vytvoření a spuštění poznámkového bloku

S připraveným datovým souborem a sadou prostředí projektu můžete nyní vytvořit a otevřít poznámkový blok.

1. Na řídicím panelu projektu vyberte **+ Nový** > **poznámkový blok**.
1. Ve vyskakovacím řádku zadejte *Příklad lineární regrese - Kriket Cvrlikání Chirps.ipynb* pro **Název položky**, zvolte **Python 3.6** pro jazyk a pak vyberte **Nový**.
1. Až se nový poznámkový blok zobrazí v seznamu souborů, vyberte ho, aby se poznámkový blok spustil. Automaticky se otevře nová karta prohlížeče.
1. Vzhledem k tomu, že máte soubor *requirements.txt* v nastavení prostředí, zobrazí se zpráva "Čekání na dokončení přípravy kontejneru." Chcete-li zprávu zavřít a pokračovat v práci v poznámkovém bloku, můžete vybrat **OK.** nelze však spustit buňky kódu, dokud není prostředí plně nastaveno.
1. Poznámkový blok se otevře v rozhraní Jupyter s jednou prázdnou buňkou kódu jako výchozí.

    [![Počáteční zobrazení nového poznámkového bloku v poznámkových blocích Azure](media/tutorial/tutorial-new-notebook.png)](media/tutorial/tutorial-new-notebook.png#lightbox)

## <a name="tour-the-notebook-interface"></a>Prohlídka rozhraní notebooku

Když je poznámkový blok spuštěný, můžete přidat kód a buňky Markdownu, spustit tyto buňky a spravovat funkci poznámkového bloku. Za prvé, nicméně, to stojí za to tatá pár minut, abyste se seznámili s rozhraním. Chcete-li získat **Help** > úplnou dokumentaci, vyberte příkaz**Nápověda k poznámkovému bloku nápovědy.**

V horní části okna se zobrazí následující položky:

(A) Název poznámkového bloku, který můžete upravit kliknutím.
(B) Tlačítka pro navigaci na obsahující projekt a řídicí panel projektů, které otevírají nové karty ve vašem prohlížeči.
(C) Nabídka s příkazy pro práci s poznámkovým blokem.
(D) panel nástrojů se zkratkami pro běžné operace.
(E) editační plátno obsahující buňky.
(F) indikátor toho, zda je poznámkový blok důvěryhodný (výchozí hodnota není **důvěryhodná).**
(G) jádro použité ke spuštění notebooku spolu s indikátorem aktivity.

[![Primární oblasti uživatelského rozhraní rozhraní Jupyter](media/tutorial/tutorial-notebook-ui.png)](media/tutorial/tutorial-notebook-ui.png#lightbox)

Jupyter poskytuje vestavěnou prohlídku primárních prvků uživatelského rozhraní. Prohlídku můžete spustit tak, že vyberete příkaz**Prohlídka uživatelského rozhraní** **nápovědy** > a proklikáte vyskakovací okno.

Skupiny příkazů nabídky jsou následující:

| Nabídka | Popis |
| --- | --- |
| File | Příkazy pro správu souboru poznámkového bloku, včetně příkazů pro vytváření a kopírování poznámkových bloků, zobrazení náhledu tisku a stahování poznámkového bloku v různých formátech. |
| Upravit | Typické příkazy pro vyjmutí, kopírování a vkládání buněk, hledání a nahrazování hodnot, správu příloh buněk a vkládání obrazů.  |
| Zobrazení | Příkazy pro řízení viditelnosti různých částí jupyterského ui. |
| Vložit | Příkazy pro vložení nové buňky nad nebo pod aktuální buňku. Tyto příkazy se používají často při vytváření poznámkového bloku. |
| Mobilní telefon | Různé **příkazy Spustit** spustit spustit jednu nebo více buněk v různých kombinacích. Příkazy **Typ buňky** mění typ buňky mezi **kódem**, **Markdownem**a **nezpracovaným příkazem NBConvert** (prostý text). Příkazy **Aktuální výstupy** a **Všechny výstupy** řídí způsob zobrazení výstupu z kódu spuštění a obsahují příkaz pro vymazání všech výstupů. |
| Kernel | Příkazy pro správu způsobu spouštění kódu v jádře spolu se **změnou jádra** pro změnu jazyka nebo verze Pythonu použité ke spuštění poznámkového bloku. |
| Data | Příkazy pro nahrávání a stahování souborů z projektu nebo relace. Viz [Práce s datovými soubory projektu](work-with-project-data-files.md) |
| Widgety | Příkazy pro správu [widgetů Jupyter](https://ipywidgets.readthedocs.io/en/stable/examples/Widget%20Basics.html), které poskytují další funkce pro vizualizaci, mapování a vykreslování.|
| Nápověda | Příkazy, které poskytují nápovědu a dokumentaci pro rozhraní Jupyter. |

Většina příkazů na panelu nástrojů má ekvivalentní příkazy nabídky. Výjimkou je **prezentace Enter/Edit RISE**, která je popsána v [poznámkových blocích Sdílení a prezentace](present-jupyter-notebooks-slideshow.md).

Několik těchto příkazů se používá při vyplňujepoznámku v následujících oddílech.

## <a name="create-a-markdown-cell"></a>Vytvoření buňky Markdown

1. Klikněte na první prázdnou buňku zobrazenou na plátně poznámkového bloku. Ve výchozím nastavení je buňka typu **Code,** což znamená, že je navržena tak, aby obsahovala runnable kód pro vybrané jádro (Python, R nebo F#). Aktuální text se zobrazí v rozevíracím řádku textu na panelu nástrojů:

    ![Rozevírací panel nástrojů Text buňky](media/tutorial/tutorial-cell-type-drop-down.png)

1. Změňte typ buňky na **Markdown** pomocí rozevíracího panelu nástrojů; případně použijte příkaz nabídky**Markdown** **typu** >  **buňky:** > 

    ![Příkaz příkaz Příkaz pro typ buňky](media/tutorial/tutorial-cell-type-menu.png)

1. Kliknutím do buňky zahájíte úpravy a zadejte následující Markdown:

    ```markdown
    # Example Linear Regression

    This notebook contains a walkthrough of a simple linear regression. The data, obtained from [college.cengage.com](https://college.cengage.com/mathematics/brase/understandable_statistics/7e/students/datasets/slr/frames/frame.html), relates the rate of cricket chirps to temperature from *The Song of Insects*, by Dr. G. W. Pierce, Harvard College Press.

    In this example we're using the count of chirps per minute as the independent varible to then predict the dependent variable, temperature. In short, we're using a little data science to make ourselves a cricket thermometer. (You could also reverse the data and use temperature to predict the number of chirps, but it's more fun to use crickets as the thermometer itself!)

    The methods shown in this example follow what's presented in the Udemy course, [Machine Learning A to Z](https://www.udemy.com/machinelearning/learn/v4/).

    A lovely aspect of Notebooks is that you can use Markdown cells to explain what the code is doing rather than code comments. There are several benefits to doing so:

    - Markdown allows for richer text formatting, like *italics*, **bold**, `inline code`, hyperlinks, and headers.
    - Markdown cells automatically word wrap whereas code cells do not. Code comments typically use explicit line breaks for formatting, but that's not necessary in Markdown.
    - Using Markdown cells makes it easier to run the Notebook as a slide show.
    - Markdown cells help you remove lengthy comments from the code, making the code easier to scan.

    When you run a code cell, Jupyter executes the code; when you run a Markdown cell, Jupyter renders all the formatting into text that's suitable for presentation.
    ```

1. Chcete-li vykreslit Markdown do HTML prohlížeče, vyberte příkaz **Spustit** na panelu nástrojů nebo použijte příkaz **Buňky** > **spustit buňky.** Kód Markdown pro formátování a odkazy se nyní zobrazuje tak, jak očekáváte v prohlížeči.

1. Když spustíte poslední buňku v poznámkovém bloku, Jupyter automaticky vytvoří novou buňku pod buňkou, kterou jste spustili. Do této buňky vložte další markdown opakováním kroků v této části s následujícím Markdownem:

    ```markdown
    ## Install packages using pip or conda

    Because the code in your notebook likely uses some Python packages, you need to make sure the Notebook environment contains those packages. You can do this directly within the notebook in a code block that contains the appropriate pip or conda commands prefixed by `!`:

    \```
    !pip install <pkg name>

    !conda install <pkg name> -y
    \```
    ```

1. Chcete-li markdown znovu upravit, poklepejte na vykreslenou buňku. Chcete-li po provedení změn znovu vykreslit kód HTML, spusťte buňku.

## <a name="create-a-code-cell-with-commands"></a>Vytvoření buňky kódu s příkazy

Jak vysvětlila předchozí buňka Markdown, můžete zahrnout příkazy přímo do poznámkového bloku. Příkazy můžete použít k instalaci balíčků, spuštění curl nebo wget k načtení dat nebo cokoli jiného. Jupyter notebooky efektivně běží v rámci virtuálního počítače Linux, takže máte plný linuxový příkaz nastavit pro práci s.

1. Zadejte níže uvedené příkazy do buňky kódu, která se objevila po použití **spuštění** na předchozí buňce Markdown. Pokud novou buňku nevidíte, vytvořte ji pomocí **vložit** > **buňku pod** nebo použijte **+** tlačítko na panelu nástrojů.

    ```bash
    !pip install numpy
    !pip install matplotlib
    !pip install pandas
    !pip install sklearn
    ```

1. Před spuštěním buňky vytvořte **+** novou buňku s tlačítkem na panelu nástrojů, nastavte ji na Markdown a zadejte následující vysvětlení:

    ```markdown
    Note that when you run a code block that contains install commands, and also those with `import` statements, it make take the notebooks a little time to complete the task. To the left of the code block you see `In [*]` to indicate that execution is happening. The Notebook's kernel on the upper right also shows a filled-in circle to indicate "busy."
    ```

1. Vyberte příkaz**Spustit vše** **v buňkách,** > chcete-li spustit všechny buňky v poznámkovém bloku. Všimněte si, že markdownbuňky vykreslují jako HTML a příkaz spustit v jádře, a dodržovat indikátor jádra, jak je popsáno v Markdown sám:

    ![Indikátor zaneprázdněnpro jádro notebooku](media/tutorial/tutorial-kernel-busy.png)

1. Také trvá trochu času pro `pip install` všechny příkazy ke spuštění a protože jste již nainstalovali tyto balíčky v prostředí projektu (a protože jsou také zahrnuty v poznámkových bloků Azure ve výchozím nastavení), zobrazí se mnoho zpráv, které čtou, "Požadavek již splněna." Celý tento výstup může být vizuálně rušivý, takže vyberte tuto buňku (pomocí jediného kliknutí) a potom pomocí**přepínače Výstupy** > **Toggle** **buněk** > skryjte výstup. Můžete také použít **příkaz Vymazat** ve stejné podnabídce k úplnému odebrání výstupu.

    Příkaz **Přepnout** skryje pouze nejnovější výstup z buňky; Pokud buňku spustíte znovu, výstup se znovu zobrazí.

1. Vzhledem k tomu, že balíčky `! pip install` jsou nainstalovány v prostředí projektu, zakomentujte příkazy pomocí `#`; tímto způsobem mohou zůstat v notebooku jako instruktážní materiál, ale nebude trvat žádný čas ke spuštění a nebude produkovat zbytečný výstup. V takovém případě udržování komentovaných příkazů v poznámkovém bloku také označuje závislosti poznámkového bloku.

    ```bash
    # !pip install numpy
    # !pip install matplotlib
    # !pip install pandas
    # !pip install sklearn
    ```

## <a name="create-the-remaining-cells"></a>Vytvoření zbývajících buněk

Chcete-li naplnit zbytek poznámkového bloku, dále vytvořte řadu buněk Markdown a kódu. Pro každou buňku uvedenou níže nejprve vytvořte novou buňku, pak nastavte typ a vložte obsah.

I když po vytvoření jednotlivých buněk můžete poznámkový blok spustit, je zajímavé spustit každou buňku při jeho vytváření. Ne všechny buňky zobrazují výstup; Pokud nevidíte žádné chyby, předpokládejme, že buňka běžela normálně.

Každá buňka kódu závisí na kódu, který byl spuštěn v předchozích buňkách, a pokud zanedbáváte spuštění jedné z buněk, mohou pozdější buňky způsobit chyby. Pokud zjistíte, že jste zapomněli spustit buňku, zkuste před spuštěním aktuální **buňky** > použít buňku**Spustit vše výše.**

Pokud se zobrazí neočekávané výsledky (což pravděpodobně budete!), Zkontrolujte, zda je každá buňka nastavena na "Kód" nebo "Markdown" podle potřeby. Například chyba "Neplatná syntaxe" obvykle nastane, když jste zadali Markdown do buňky Kódu.

1. Markdown buňka:

    ```markdown
    ## Import packages and prepare the dataset

    In this example we're using numpy, pandas, and matplotlib. Data is in the file cricket_chirps.csv. Because this file is in the same project as this present Notebook, we can just load it using a relative pathname.
    ```

1. Kódová buňka; při spuštění zobrazí obsah tabulky jako výstup. Výstup můžete potlačit zaostenem příkazu. `print`

    ```python
    import numpy as np
    import pandas as pd

    dataset = pd.read_csv('cricket_chirps.csv')
    print(dataset)
    X = dataset.iloc[:, :-1].values  # Matrix of independent variables -- remove the last column in this data set
    y = dataset.iloc[:, 1].values    # Matrix of dependent variables -- just the last column (1 == 2nd column)
    ```

    > [!Note]
    > Může se zobrazit upozornění z tohoto kódu o "numpy.dtype velikost změněna"; varování lze bezpečně ignorovat.

1. Markdown buňka:

    ```markdown
    Next, split the dataset into a Training set (2/3rds) and Test set (1/3rd). We don't need to do any feature scaling because there is only one column of independent variables, and packages typically do scaling for you.
    ```

1. Kódová buňka; při spuštění nemá tato buňka žádný výstup.

    ```python
    from sklearn.model_selection import train_test_split

    X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 1/3, random_state = 0)
    ```

1. Markdown buňka:

    ```markdown
    ## Fit the data to the training set

    "Fitting" the data to a training set means making the line that describes the relationship between the independent and the dependent variables. With a simple data set like we're using here, you can visualize the line on a simple x-y plot: the x-axis is the independent variable (chirp count in this example), and the y-axis is the independent variable (temperature). Fitting the data means plotting all the points in the training set, then drawing the best-fit line through that data.

    With two independent variables you can imagine a three-dimensional plot with a line fitted to the data. At three or more independent variables, however, it's no longer easy to visualize the fit, but you get the idea. In the end, it's all just mathematics, which a computer can handle easily without having to form a mental picture!

    The regressor's `fit` method here creates the line, which algebraically is of the form `y = x*b1 + b0`, where b1 is the coefficient or slope of the line (which you can get to through `regressor.coef_`), and b0 is the intercept of the line at x=0 (which you can get to through `regressor.intercept_`).
    ```

1. Kódová buňka; při spuštění tato buňka `LinearRegression(copy_X=True, fit_intercept=True, n_jobs=None,normalize=False)`zobrazuje výstup . .

    ```python
    from sklearn.linear_model import LinearRegression

    regressor = LinearRegression()    # This object is the regressor, that does the regression
    regressor.fit(X_train, y_train)   # Provide training data so the machine can learn to predict using a learned model.
    ```

1. Markdown buňka:

    ```markdown
    ## Predict the results

    With the regressor in hand, we can predict the test set results using its `predict` method. That method takes a vector of independent variables for which you want predictions.

    Because the regressor is fit to the data by virtue of `coef_` and `intercept_`, a prediction is the result of `coef_ * x + intercept_`. (Indeed, `predict(0)` returns `intercept_` and `predict(1)` returns `intercept_ + coef_`.)

    In the code, the `y_test` matrix (from when we split the set) contains the real observations. `y_pred` assigned here contains the predictions for the same `X_test` inputs. It's not expected that the test or training points exactly fit the regression; the regression is trying to find the model that we can use to make predictions with new observations of the independent variables.
    ```

1. Kódová buňka; při spuštění tato buňka `[79.49588055 75.98873911 77.87719989 80.03544077 75.17939878]`zobrazuje výsledky jako .

    ```python
    y_pred = regressor.predict(X_test)
    print(y_pred)
    ```

1. Markdown buňka:

    ```markdown
    It's interesting to think that all the "predictions" we use in daily life, like weather forecasts, are just regression models of some sort working with various data sets. Those models are much more complicated than what's shown here, but the idea is the same.

    Knowing how predictions work help us understand that the actual observations we would collect in the moment will always be somewhat off from the predictions: the predictions fit exactly to the model, whereas the observations typically won't.

    Of course, such systems feed new observations back into the dataset to continually improve the model, meaning that predictions should get more accurate over time.

    The challenge is determining what data to actually use. For example, with weather, how far back in time do you go? How have weather patterns been changing decade by decade? In any case, something like weather predictions will be doing things hour by hour, day by day, for things like temperature, precipitation, winds, cloud cover, etc. Radar and other observations are of course fed into the model and the predictions are reduced to mathematics.
    ```

1. Markdown buňka:

    ```markdown
    ## Visualize the results

    The following code generates a plot: green dots are training data, red dots are test data, blue dots are predictions. Gray line is the regression itself. You see that all the blue dots are exactly on the line, as they should be, because the predictions exactly fit the model (the line).
    ```

1. Kódová buňka; při spuštění tato buňka vytvoří grafický obrázek. Pokud nevidíte vykreslení poprvé (a místo toho viz "Obrázek velikost 640x480 s 1 osami"), spusťte buňku znovu.

    ```python
    import matplotlib.pyplot as plt

    plt.scatter(X_train, y_train, color = 'green')
    plt.scatter(X_test, y_test, color = 'red')   
    plt.scatter(X_test, y_pred, color = 'blue')  # The predicted temperatures of the same X_test input.
    plt.plot(X_train, regressor.predict(X_train), color = 'gray')
    plt.title('Temperature based on chirp count')
    plt.xlabel('Chirps/minute')
    plt.ylabel('Temperature')
    plt.show()
    ```

    ![Vykreslení výstupu z kódu matplotlib](media/tutorial/tutorial-plot-output.png)

1. Markdown buňka:

    ```markdown
    ## Closing comments

    At the end of the day, when you create a model, you use training data. Then you start feeding test data (real observations) to see how well the model actually works. You may find that the model is a little inaccurate over time, in which case you retrain the model with some new data. Retraining the model means you're creating a new fit line that's used for predictions.

    This regression can be used to examine the variability of the relationship between temperature and chirp count. Of course, if the model proves too inaccurate (that is, the predictions aren't very good), then it suggests that we might need to introduce more independent variables like humidity, time of year, latitude, amount of moonlight, and so on. If you have such data, you can do separate lines regressions for each independent variable, and then do multiple regressions with combinations of independent variables. 

    Again, once you are working with more than one or two independent variables, it's much easier to use machine learning to crunch the numbers than to try to visualize it graphically.
    ```

## <a name="clear-outputs-and-rerun-all-cells"></a>Vymazat výstupy a znovu spustit všechny buňky

Po provedení kroků v předchozí části naplnit celý poznámkový blok, jste vytvořili oba část spuštěný kód v kontextu úplné houště na lineární regrese. Tato přímá kombinace kódu a textu je jednou z velkých výhod notebooků!

Zkuste znovu spustit celý poznámkový blok:

1. Vymažte všechna data relace jádra a výstup všech buněk výběrem**možnosti Restart** **jádra** > & Clear Output . Tento příkaz je vždy dobrý ke spuštění po dokončení poznámkového bloku, abyste se ujistili, že jste nevytvořili žádné podivné závislosti mezi buňkami kódu.

1. Znovu spusťte poznámkový blok pomocí funkce Spustit vše v > **buňky**. **Cell** Všimněte si, že indikátor jádra je vyplněn, když je spuštěn kód.

    Pokud máte nějaký kód, který běží příliš dlouho nebo jinak uvízne, můžete zastavit jádro pomocí příkazu**Přerušení** **jádra.** > 

1. Projděte poznámkový blok a prohlédněte si výsledky. (Pokud se vykreslení znovu nezobrazí, znovu spusťte tuto buňku.)

## <a name="save-halt-and-close-the-notebook"></a>Uložení, zastavení a zavření poznámkového bloku

Během úprav poznámkového bloku můžete uložit jeho aktuální stav pomocí příkazu **Uložit soubor** > **a kontrolní bod** nebo tlačítka Uložit na panelu nástrojů. "Kontrolní bod" vytvoří snímek, který můžete kdykoli během relace vrátit. Kontrolní body umožňují provést řadu experimentálních změn, a pokud tyto změny nefungují, můžete se vrátit k kontrolnímu bodu pomocí příkazu **Vrátit** > **soubor do kontrolního bodu.** Alternativní přístup je vytvořit další buňky a komentovat jakýkoli kód, který nechcete spustit; tak či onak funguje.

Příkaz Vytvořit**kopii** **souboru** > můžete také kdykoli použít k vytvoření kopie aktuálního stavu poznámkového bloku do nového souboru v projektu. Tato kopie se automaticky otevře na nové kartě prohlížeče.

Až budete hotovi s poznámkovým blokem, použijte příkaz**Zavřít a zastavit** **soubor,** > který zavře poznámkový blok a vypne jádro, které ho spouštělo. Poznámkové bloky Azure pak automaticky zavře kartu prohlížeče.

## <a name="debug-notebooks-using-visual-studio-code"></a>Ladění poznámkových bloků pomocí kódu sady Visual Studio

Pokud se buňky kódu v poznámkovém bloku nechovají očekávaným způsobem, může se zobrazit chyby kódu nebo jiné vady. Však jiné `print` než pomocí příkazů zobrazit hodnotu proměnných, typické prostředí Jupyter nenabízí žádné zařízení ladění.

Naštěstí si můžete stáhnout soubor *.ipynb* notebooku a pak jej otevřít v kódu Visual Studia pomocí rozšíření Python. Přípona přímo importuje poznámkový blok jako jeden soubor kódu a zachová buňky Markdownu v komentářích. Po importu poznámkového bloku můžete pomocí ladicího programu kódu visual studia procházet kód, nastavit zarážky, zkontrolovat stav a tak dále. Po provedení oprav kódu pak exportujete soubor *.ipynb* z kódu Sady Visual Studio a nahrajete ho zpět do poznámkových bloků Azure.

Další informace naleznete v [tématu Ladění poznámkového bloku Jupyter](https://code.visualstudio.com/docs/python/jupyter-support#debug-a-jupyter-notebook) v dokumentaci ke kódu sady Visual Studio.

Viz také [Visual Studio Code – podpora Jupyter](https://code.visualstudio.com/docs/python/jupyter-support) pro další funkce Visual Studio kód pro poznámkové bloky Jupyter.

## <a name="next-steps"></a>Další kroky

- [Prozkoumejte ukázkové poznámkové bloky](azure-notebooks-samples.md)

Články s postupy:

- [Vytváření a klonování projektů](create-clone-jupyter-notebooks.md)
- [Konfigurace a správa projektů](configure-manage-azure-notebooks-projects.md)
- [Instalace balíčků z poznámkového bloku](install-packages-jupyter-notebook.md)
- [Předložení prezentace](present-jupyter-notebooks-slideshow.md)
- [Práce s datovými soubory](work-with-project-data-files.md)
- [Přístup k datovým prostředkům](access-data-resources-jupyter-notebooks.md)
- [Používání Azure Machine Learningu](use-machine-learning-services-jupyter-notebooks.md)

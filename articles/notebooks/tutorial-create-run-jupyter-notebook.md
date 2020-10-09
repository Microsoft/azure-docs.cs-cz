---
title: Kurz – vytvoření a spuštění Jupyter Notebook-Azure Notebooks Preview
description: Naučte se, jak vytvořit a spustit Jupyter Notebook v Azure Notebooks Preview, které demonstrují proces lineární regrese v oblasti datové vědy.
ms.topic: tutorial
ms.date: 01/11/2019
ms.custom: devx-track-python
ms.openlocfilehash: cb8be1618de7e95a7096e4ea9dec78bf4aa277f6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88589247"
---
# <a name="tutorial-create-and-run-a-jupyter-notebook-with-python"></a>Kurz: vytvoření a spuštění Jupyter Notebook pomocí Pythonu

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Tento kurz vás provede procesem použití Azure Notebooks k vytvoření kompletní Jupyter Notebook, která ukazuje jednoduchou lineární regresi. V průběhu tohoto kurzu se seznámíte s uživatelským rozhraním Jupyter Notebook, které zahrnuje vytváření různých buněk, spouštění buněk a prezentování poznámkového bloku jako prezentace.

Dokončený Poznámkový blok najdete na [ukázkách Azure Notebooks GitHubu](https://github.com/Microsoft/AzureNotebooks/tree/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps). Tento kurz ale začíná novým projektem a prázdným poznámkovým blokem, abyste se mohli setkat s jeho vytvořením krok za krokem.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření poznámkového bloku projektu s některými ukázkovými daty
> * Použití rozhraní poznámkového bloku k vytvoření nejrůznějších typů buněk
> * Spuštění poznámkového bloku
> * Uložení poznámkového bloku
> * Ladění poznámkového bloku v Visual Studio Code

## <a name="create-the-project"></a>Vytvoření projektu

1. Přejít na [Azure Notebooks](https://notebooks.azure.com) a přihlásit se. (Podrobnosti najdete v tématu [rychlý Start – přihlášení k Azure Notebooks](quickstart-sign-in-azure-notebooks.md)).

1. Na stránce veřejný profil vyberte v horní části stránky **Moje projekty** :

    ![Odkaz Moje projekty v horní části okna prohlížeče](media/quickstarts/my-projects-link.png)

1. Na stránce **Moje projekty** vyberte **+ Nový projekt** (Klávesová zkratka: n); tlačítko se může zobrazit jenom v **+** případě, že je okno prohlížeče úzké:

    ![Nový projekt na stránce Moje projekty](media/quickstarts/new-project-command.png)

1. V místní nabídce **vytvořit nový projekt** , která se zobrazí, zadejte nebo nastavte následující podrobnosti a pak vyberte **vytvořit**:

   - **Název projektu**: příklad lineární regrese – Cricket chirps
   - **ID projektu**: lineární regrese – příklad
   - **Veřejný projekt**: (nezaškrtnuto)
   - **Vytvořit Readme.MD**: (nezaškrtnuto)

1. Po chvíli Azure Notebooks přejít k novému projektu.

## <a name="create-the-data-file"></a>Vytvořit datový soubor

Model lineární regrese, který vytvoříte v poznámkovém bloku, nakreslí data ze souboru v projektu s názvem *cricket_chirps.csv*. Tento soubor můžete vytvořit buď tak, že ho zkopírujete ze [vzorků Azure Notebooks GitHubu](https://github.com/Microsoft/AzureNotebooks/tree/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps)nebo přímo zadáním dat. V následujících částech jsou popsány oba přístupy.

### <a name="upload-the-data-file"></a>Nahrát datový soubor

1. Na řídicím panelu projektu v Azure Notebooks vyberte **Odeslat**  >  **z adresy URL** .
1. V místní nabídce zadejte do pole **Adresa URL souboru** následující adresu url a *cricket_chirps.csv* v poli **název souboru**a potom vyberte **Hotovo**.

    ```url
    https://raw.githubusercontent.com/Microsoft/AzureNotebooks/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps/cricket_chirps.csv
    ```

1. Soubor *cricket_chirps.csv* by se teď měl zobrazit v seznamu souborů vašeho projektu:

    ![Nově vytvořený soubor CSV zobrazený v seznamu soubor projektu](media/tutorial/csv-file-in-project.png)

### <a name="create-a-file-from-scratch"></a>Vytvoření nového souboru

1. Na řídicím panelu projektu v Azure Notebooks vyberte **+ Nový**  >  **prázdný soubor** .
1. Pole se zobrazí v seznamu souborů projektu. Zadejte *cricket_chirps.csv* a stiskněte klávesu ENTER.
1. Klikněte pravým tlačítkem na *cricket_chirps.csv* a vyberte **Upravit soubor**.
1. V editoru, který se zobrazí, zadejte následující data:

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

1. Vyberte **Uložit soubor** a uložte soubor a vraťte se na řídicí panel projekt.

## <a name="install-project-level-packages"></a>Instalovat balíčky na úrovni projektu

V rámci poznámkového bloku můžete vždy použít příkazy jako `!pip install` v buňce kódu k instalaci požadovaných balíčků. Tyto příkazy se ale spustí pokaždé, když spustíte buňky kódu poznámkového bloku a může trvat značnou dobu. Z tohoto důvodu můžete místo toho instalovat balíčky na úrovni projektu pomocí `requirements.txt` souboru.

1. Pomocí postupu popsaného v části [Vytvoření souboru od začátku](#create-a-file-from-scratch) vytvořte soubor `requirements.txt` s názvem s následujícím obsahem:

    ```text
    matplotlib==3.0.0
    numpy==1.15.0
    pandas==0.23.4
    scikit-learn==0.20.0
    ```

    `requirements.txt`Pokud dáváte přednost, můžete soubor nahrát z místního počítače, jak je popsáno v tématu [odeslání datového souboru](#upload-the-data-file).

1. Na řídicím panelu projekt vyberte **nastavení projektu**.
1. V místní nabídce, která se zobrazí, vyberte kartu **prostředí** a pak vyberte **+ Přidat**.
1. V prvním ovládacím prvku rozevíracího seznamu (operace) v části **nastavení prostředí**vyberte možnost **Requirements.txt**.
1. V druhém ovládacím prvku rozevírací seznam (název souboru) vyberte možnost *requirements.txt* (soubor, který jste vytvořili).
1. V třetím ovládacím prvku rozevíracího seznamu (verze Pythonu) vyberte **Python verze 3,6**.
1. Vyberte **Uložit**.

![Karta prostředí nastavení projektu určující soubor requirements.txt](media/tutorial/tutorial-requirements-txt.png)

V tomto kroku nastavení se všechny poznámkové bloky spuštěné v projektu spustí v prostředí, ve kterém jsou tyto balíčky nainstalované.

## <a name="create-and-run-a-notebook"></a>Vytvoření a spuštění poznámkového bloku

S připraveným datovým souborem a nastavením prostředí projektu teď můžete vytvořit a otevřít poznámkový blok.

1. Na řídicím panelu projekt vyberte **+ Nový**  >  **Poznámkový blok**.
1. V překryvném okně zadejte *příklad lineární regrese – Cricket chirps. ipynb* pro **položku název položky**, zvolte **Python 3,6** pro jazyk a pak vyberte **Nový**.
1. Po zobrazení nového poznámkového bloku v seznamu soubor vyberte ho a spusťte Poznámkový blok. Automaticky se otevře nová karta prohlížeče.
1. Vzhledem k tomu, že máte soubor *requirements.txt* v nastavení prostředí, zobrazí se zpráva, že čekáte na dokončení přípravy kontejneru. Kliknutím na **tlačítko OK** můžete zprávu Zavřít a pokračovat v práci na poznámkovém bloku. Nemůžete však spouštět buňky kódu, dokud nebude prostředí zcela nastavené.
1. Poznámkový blok se otevře v rozhraní Jupyter s jednou prázdnou buňkou kódu jako výchozí.

    [![Počáteční zobrazení nového poznámkového bloku v Azure Notebooks](media/tutorial/tutorial-new-notebook.png)](media/tutorial/tutorial-new-notebook.png#lightbox)

## <a name="tour-the-notebook-interface"></a>Prohlídka rozhraní poznámkového bloku

Pomocí poznámkového bloku, který běží, můžete přidat kód a Markdownu buňky, spouštět tyto buňky a spravovat operace poznámkového bloku. Nejdřív ale stačí pár minut seznámit se s rozhraním. Úplnou dokumentaci **získáte pomocí**  >  příkazu nabídky**help poznámkového bloku** .

V horní části okna se zobrazí následující položky:

(A) název vašeho poznámkového bloku, který můžete upravit kliknutím na.
(B) tlačítka pro přechod na obsahující projekt a řídicí panel projekty, které otevřou nové karty v prohlížeči.
(C) nabídka s příkazy pro práci s poznámkovým blokem.
(D) panel nástrojů s zástupci pro běžné operace.
(E) plátno pro úpravy obsahující buňky.
(F) indikátor, zda je Poznámkový blok důvěryhodný (výchozí není **důvěryhodný**).
(G) jádro používané ke spuštění poznámkového bloku spolu s indikátorem aktivity.

[![Primární oblasti uživatelského rozhraní rozhraní Jupyter](media/tutorial/tutorial-notebook-ui.png)](media/tutorial/tutorial-notebook-ui.png#lightbox)

Jupyter poskytuje vestavěnou prohlídku primárních prvků uživatelského rozhraní. Spusťte prohlídku výběrem příkazu **help**  >  **User Interface Tour** a kliknutím na překryvné okno.

Skupiny příkazů nabídky jsou následující:

| Nabídka | Popis |
| --- | --- |
| File | Příkazy pro správu souboru poznámkového bloku, včetně příkazů pro vytváření a kopírování poznámkových bloků, zobrazení náhledu tisku a stažení poznámkového bloku v nejrůznějších formátech. |
| Edit | Typické příkazy pro vyjímání, kopírování a vkládání buněk, hledání a nahrazování hodnot, správu příloh buněk a vkládání obrázků.  |
| Zobrazit | Příkazy pro řízení viditelnosti různých částí uživatelského rozhraní Jupyter. |
| Vložit | Příkazy pro vložení nové buňky nad nebo pod aktuální buňku. Tyto příkazy použijete často při vytváření poznámkového bloku. |
| Mobilní telefon | Různé příkazy **spuštění** spouštějí jednu nebo více buněk v různých kombinacích. Příkazy **typu buňky** mění typ buňky mezi **kódem**, **Markdownu**a **nezpracovaným NBConvert** (prostý text). Příkazy **aktuální výstupy** a **všechny výstupy** řídí způsob, jakým se zobrazuje výstup z kódu spuštění, a obsahuje příkaz pro vymazání všech výstupů. |
| jádro | Příkazy pro správu způsobu spouštění kódu v jádru spolu s **jádrem Change** pro změnu jazyka nebo verze Pythonu používané ke spuštění poznámkového bloku. |
| Data | Příkazy pro nahrání a stažení souborů z projektu nebo relace. Viz [práce s datovými soubory projektu](work-with-project-data-files.md) |
| Widgety | Příkazy pro správu [widgetů Jupyter](https://ipywidgets.readthedocs.io/en/stable/examples/Widget%20Basics.html), které poskytují další možnosti pro vizualizaci, mapování a vykreslování.|
| Nápověda | Příkazy, které poskytují nápovědu a dokumentaci pro rozhraní Jupyter. |

Většina příkazů na panelu nástrojů má ekvivalentní příkazy nabídky. Jedinou výjimkou je, že je k dispozici **prezentace pro vložení nebo úpravu**, která je popsána v [poznámkových blocích pro sdílení](present-jupyter-notebooks-slideshow.md)

K naplnění poznámkového bloku v následujících částech použijete několik těchto příkazů.

## <a name="create-a-markdown-cell"></a>Vytvoření buňky Markdownu

1. Klikněte do první prázdné buňky zobrazené na plátně poznámkového bloku. Ve výchozím nastavení je buňka typem **kódu** , což znamená, že je navržena tak, aby obsahovala kód spustitelný pro vybrané jádro (Python, R nebo F #). Aktuální typ je zobrazen v rozevíracím seznamu typ na panelu nástrojů:

    ![Rozevírací seznam panelu nástrojů typu buňky](media/tutorial/tutorial-cell-type-drop-down.png)

1. Pomocí rozevíracího seznamu na panelu nástrojů změňte typ buňky na **Markdownu** ; Alternativně použijte **Cell**  >  příkaz Markdownu nabídky**typ buňky**  >  **Markdown** :

    ![Příkaz nabídky pro typ buňky](media/tutorial/tutorial-cell-type-menu.png)

1. Klikněte na buňku pro zahájení úprav a pak zadejte následující Markdownu:

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

1. Chcete-li vykreslit Markdownu do HTML pro prohlížeč, vyberte příkaz **Spustit** na panelu nástrojů nebo použijte příkaz buňky **Cell**  >  **Run** . Kód Markdownu pro formátování a propojení se teď zobrazuje podle očekávání v prohlížeči.

1. Když na poznámkovém bloku spustíte poslední buňku, Jupyter automaticky vytvoří novou buňku pod tu, kterou jste spustili. Do této buňky vložte více Markdownu zopakováním kroků v této části s následujícím Markdownu:

    ```markdown
    ## Install packages using pip or conda

    Because the code in your notebook likely uses some Python packages, you need to make sure the Notebook environment contains those packages. You can do this directly within the notebook in a code block that contains the appropriate pip or conda commands prefixed by `!`:

    \```
    !pip install <pkg name>

    !conda install <pkg name> -y
    \```
    ```

1. Pokud chcete znovu upravit Markdownu, dvakrát klikněte na vykreslenou buňku. Chcete-li po provedení změn znovu vykreslit kód HTML, spusťte buňku.

## <a name="create-a-code-cell-with-commands"></a>Vytvoření buňky kódu pomocí příkazů

Jak je vysvětleno v předchozí buňce Markdownu, můžete zahrnout příkazy přímo do poznámkového bloku. Příkazy můžete použít k instalaci balíčků, spuštění oblého nebo wget k načtení dat nebo cokoli jiného. Jupyter poznámkové bloky se efektivně spouštějí v rámci virtuálního počítače se systémem Linux, takže máte k dispozici úplný příkaz pro systém Linux, se kterým pracujete.

1. Níže uvedené příkazy zadejte v buňce kódu, která se zobrazila po použití **příkazu Spustit** na předchozí buňce Markdownu. Pokud nevidíte novou buňku, vytvořte ji s **vloženou**  >  **buňkou** vložit nebo použijte **+** tlačítko na panelu nástrojů.

    ```bash
    !pip install numpy
    !pip install matplotlib
    !pip install pandas
    !pip install sklearn
    ```

1. Před spuštěním této buňky vytvořte novou buňku pomocí **+** tlačítka na panelu nástrojů, nastavte ji na Markdownu a zadejte následující vysvětlení:

    ```markdown
    Note that when you run a code block that contains install commands, and also those with `import` statements, it make take the notebooks a little time to complete the task. To the left of the code block you see `In [*]` to indicate that execution is happening. The Notebook's kernel on the upper right also shows a filled-in circle to indicate "busy."
    ```

1. Výběrem **buňky**  >  **Spustit vše** zobrazíte všechny buňky v poznámkovém bloku. Všimněte si, že se buňky Markdownu vykreslují jako HTML a příkaz se spustí v jádru a sledujte indikátor jádra, jak je popsáno v samotném Markdownu:

    ![Indikátor zaneprázdněnosti pro jádro poznámkového bloku](media/tutorial/tutorial-kernel-busy.png)

1. I když se všechny `pip install` příkazy spouštějí a protože jste už tyto balíčky nainstalovali do prostředí projektu (a protože jsou taky zahrnuté ve výchozím nastavení Azure Notebooks), zobrazí se spousta zpráv, které si přečtou požadavek, který je už spokojen. Všechny tyto výstupy je možné vizuálně odvolávat, takže vyberte tuto buňku (pomocí jediného kliknutí) a pak použijte **Cell**  >  **přepínač výstupy**buňky buňky  >  **Toggle** pro skrytí výstupu. Výstup můžete zcela odebrat také pomocí příkazu **clear** v této nabídce.

    Příkaz **přepínání** skrývá pouze poslední výstup z buňky; Pokud znovu spustíte tuto buňku, výstup se znovu zobrazí.

1. Vzhledem k tomu, že balíčky jsou nainstalovány v prostředí projektu, přihlaste se k `! pip install` příkazům pomocí příkazu `#` ; tímto způsobem mohou zůstat v poznámkovém bloku jako instruktážní materiál, ale neprojeví se v jakémkoli čase a nevzniká zbytečný výstup. V takovém případě zachováte příkazy s komentářem v poznámkovém bloku také závislosti poznámkového bloku.

    ```bash
    # !pip install numpy
    # !pip install matplotlib
    # !pip install pandas
    # !pip install sklearn
    ```

## <a name="create-the-remaining-cells"></a>Vytvoření zbývajících buněk

K naplnění zbytku poznámkového bloku můžete dál vytvořit řadu Markdownu a buněk kódu. Pro každou buňku uvedenou níže vytvořte novou buňku a pak nastavte typ a vložte ho do obsahu.

I když můžete počkat na spuštění poznámkového bloku po vytvoření každé buňky, je zajímavou pro spuštění každé buňky při jejím vytváření. Ne všechny buňky zobrazují výstup; Pokud se nezobrazí žádné chyby, Předpokládejme, že buňka běžela normálně.

Každá buňka kódu závisí na kódu, který byl spuštěn v předchozích buňkách, a pokud ponecháte spustit jednu z buněk, mohou další buňky způsobit chyby. Pokud zjistíte, že jste zapomněli spustit buňku, zkuste před spuštěním aktuální buňky použít **buňku**, která je  >  dřív**spuštěna** .

Pokud se zobrazí neočekávané výsledky (což pravděpodobně budete mít!), zkontrolujte, zda je v případě potřeby každá buňka nastavena na "Code" nebo "Markdownu". Například chyba "Neplatná syntaxe" obvykle nastane, pokud jste zadali Markdownu do buňky kódu.

1. Buňka Markdownu:

    ```markdown
    ## Import packages and prepare the dataset

    In this example we're using numpy, pandas, and matplotlib. Data is in the file cricket_chirps.csv. Because this file is in the same project as this present Notebook, we can just load it using a relative pathname.
    ```

1. Buňka kódu; Při spuštění zobrazuje obsah tabulky jako výstup. Výstup můžete potlačit zadáním komentáře k `print` příkazu.

    ```python
    import numpy as np
    import pandas as pd

    dataset = pd.read_csv('cricket_chirps.csv')
    print(dataset)
    X = dataset.iloc[:, :-1].values  # Matrix of independent variables -- remove the last column in this data set
    y = dataset.iloc[:, 1].values    # Matrix of dependent variables -- just the last column (1 == 2nd column)
    ```

    > [!Note]
    > Může se zobrazit upozornění z tohoto kódu o změně velikosti numpy. dtype. upozornění je možné ignorovat.

1. Buňka Markdownu:

    ```markdown
    Next, split the dataset into a Training set (2/3rds) and Test set (1/3rd). We don't need to do any feature scaling because there is only one column of independent variables, and packages typically do scaling for you.
    ```

1. Buňka kódu; Při spuštění nemá tato buňka žádný výstup.

    ```python
    from sklearn.model_selection import train_test_split

    X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 1/3, random_state = 0)
    ```

1. Buňka Markdownu:

    ```markdown
    ## Fit the data to the training set

    "Fitting" the data to a training set means making the line that describes the relationship between the independent and the dependent variables. With a simple data set like we're using here, you can visualize the line on a simple x-y plot: the x-axis is the independent variable (chirp count in this example), and the y-axis is the independent variable (temperature). Fitting the data means plotting all the points in the training set, then drawing the best-fit line through that data.

    With two independent variables you can imagine a three-dimensional plot with a line fitted to the data. At three or more independent variables, however, it's no longer easy to visualize the fit, but you get the idea. In the end, it's all just mathematics, which a computer can handle easily without having to form a mental picture!

    The regressor's `fit` method here creates the line, which algebraically is of the form `y = x*b1 + b0`, where b1 is the coefficient or slope of the line (which you can get to through `regressor.coef_`), and b0 is the intercept of the line at x=0 (which you can get to through `regressor.intercept_`).
    ```

1. Buňka kódu; Při spuštění se v této buňce zobrazí výstup `LinearRegression(copy_X=True, fit_intercept=True, n_jobs=None,normalize=False)` .

    ```python
    from sklearn.linear_model import LinearRegression

    regressor = LinearRegression()    # This object is the regressor, that does the regression
    regressor.fit(X_train, y_train)   # Provide training data so the machine can learn to predict using a learned model.
    ```

1. Buňka Markdownu:

    ```markdown
    ## Predict the results

    With the regressor in hand, we can predict the test set results using its `predict` method. That method takes a vector of independent variables for which you want predictions.

    Because the regressor is fit to the data by virtue of `coef_` and `intercept_`, a prediction is the result of `coef_ * x + intercept_`. (Indeed, `predict(0)` returns `intercept_` and `predict(1)` returns `intercept_ + coef_`.)

    In the code, the `y_test` matrix (from when we split the set) contains the real observations. `y_pred` assigned here contains the predictions for the same `X_test` inputs. It's not expected that the test or training points exactly fit the regression; the regression is trying to find the model that we can use to make predictions with new observations of the independent variables.
    ```

1. Buňka kódu; v případě spuštění zobrazí tato buňka výsledky jako `[79.49588055 75.98873911 77.87719989 80.03544077 75.17939878]` .

    ```python
    y_pred = regressor.predict(X_test)
    print(y_pred)
    ```

1. Buňka Markdownu:

    ```markdown
    It's interesting to think that all the "predictions" we use in daily life, like weather forecasts, are just regression models of some sort working with various data sets. Those models are much more complicated than what's shown here, but the idea is the same.

    Knowing how predictions work help us understand that the actual observations we would collect in the moment will always be somewhat off from the predictions: the predictions fit exactly to the model, whereas the observations typically won't.

    Of course, such systems feed new observations back into the dataset to continually improve the model, meaning that predictions should get more accurate over time.

    The challenge is determining what data to actually use. For example, with weather, how far back in time do you go? How have weather patterns been changing decade by decade? In any case, something like weather predictions will be doing things hour by hour, day by day, for things like temperature, precipitation, winds, cloud cover, etc. Radar and other observations are of course fed into the model and the predictions are reduced to mathematics.
    ```

1. Buňka Markdownu:

    ```markdown
    ## Visualize the results

    The following code generates a plot: green dots are training data, red dots are test data, blue dots are predictions. Gray line is the regression itself. You see that all the blue dots are exactly on the line, as they should be, because the predictions exactly fit the model (the line).
    ```

1. Buňka kódu; Při spuštění tato buňka vytvoří grafický graf. Pokud se vykreslení nezobrazí poprvé (a místo toho se zobrazí "obrázek 640 × 1 osy"), spusťte tuto buňku znovu.

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

    ![Vykreslit výstup z matplotlib kódu](media/tutorial/tutorial-plot-output.png)

1. Buňka Markdownu:

    ```markdown
    ## Closing comments

    At the end of the day, when you create a model, you use training data. Then you start feeding test data (real observations) to see how well the model actually works. You may find that the model is a little inaccurate over time, in which case you retrain the model with some new data. Retraining the model means you're creating a new fit line that's used for predictions.

    This regression can be used to examine the variability of the relationship between temperature and chirp count. Of course, if the model proves too inaccurate (that is, the predictions aren't very good), then it suggests that we might need to introduce more independent variables like humidity, time of year, latitude, amount of moonlight, and so on. If you have such data, you can do separate lines regressions for each independent variable, and then do multiple regressions with combinations of independent variables. 

    Again, once you are working with more than one or two independent variables, it's much easier to use machine learning to crunch the numbers than to try to visualize it graphically.
    ```

## <a name="clear-outputs-and-rerun-all-cells"></a>Vymazat výstupy a znovu spustit všechny buňky

Po provedení kroků v předchozím oddílu k naplnění celého poznámkového bloku jste v kontextu úplného kurzu lineární regrese vytvořili jak běh kódu. Tato přímá kombinace kódu a textu je jednou z skvělých výhod poznámkových bloků!

Zkuste teď znovu spustit celý Poznámkový blok:

1. Vymažte všechna data relace jádra a veškerý výstup z buňky výběrem možnosti **Kernel**  >  **restartování jádra & vymazat výstup**. Tento příkaz je vždycky dobrý, aby se spouštěl po dokončení poznámkového bloku, abyste měli jistotu, že jste nevytvořili žádné neobvyklé závislosti mezi buňkami kódu.

1. Spusťte Poznámkový blok znovu pomocí **buňky**  >  **Run All**. Všimněte si, že indikátor jádra je vyplněn, zatímco je spuštěný kód.

    Pokud máte nějaký kód, který se spouští příliš dlouho nebo jinak se zablokuje, můžete zastavit jádro pomocí příkazu pro **Kernel**  >  **přerušení** jádra.

1. Posuňte se přes Poznámkový blok a Projděte si výsledky. (Pokud se graf znovu nezobrazí, spusťte tuto buňku znovu.)

## <a name="save-halt-and-close-the-notebook"></a>Uložení, zastavení a zavření poznámkového bloku

Během úprav poznámkového bloku můžete jeho aktuální stav uložit pomocí příkazu Uložit **soubor**  >  **a kontrolní bod** nebo tlačítko Uložit na panelu nástrojů. Kontrolní bod vytvoří snímek, ke kterému se můžete kdykoli vrátit během relace. Kontrolní body umožňují provést řadu experimentálních změn, a pokud tyto změny nefungují, můžete se vrátit k kontrolnímu bodu pomocí příkazu vrátit **se změnami**  >  **do kontrolního bodu** . Alternativním přístupem je vytvořit nadbytečné buňky a komentovat libovolný kód, který nechcete spouštět; Jak funguje.

**File**  >  K vytvoření kopie aktuálního stavu poznámkového bloku do nového souboru v projektu můžete použít také příkaz soubor**vytvořit kopii** . Toto kopírování se automaticky otevře na nové kartě prohlížeče.

Až budete s poznámkovým blokem hotovi **File**, použijte  >  příkaz**Zavřít a zastavit** soubor, který zavře Poznámkový blok a ukončí jádro, na kterém je spuštěný. Azure Notebooks pak automaticky zavře kartu prohlížeče.

## <a name="debug-notebooks-using-visual-studio-code"></a>Ladění poznámkových bloků pomocí Visual Studio Code

Pokud se buňky kódu v poznámkovém bloku nechovají očekávaným způsobem, může dojít k chybám kódu nebo jiným VADM. Avšak jiné než použití `print` příkazů k zobrazení hodnoty proměnných, typické prostředí Jupyter nenabízí žádné ladicí funkce.

Naštěstí si můžete stáhnout soubor *. ipynb* poznámkového bloku a pak ho otevřít v Visual Studio Code pomocí rozšíření Python. Rozšíření přímo importuje Poznámkový blok jako jeden soubor kódu a zachovává vaše Markdownu buňky v komentářích. Po importu poznámkového bloku můžete pomocí ladicího programu Visual Studio Code krokovat kód, nastavit zarážky, kontrolovat stav a tak dále. Po provedení oprav kódu pak exportujte soubor *. ipynb* z Visual Studio Code a nahrajte ho zpátky do Azure Notebooks.

Další informace naleznete v tématu [ladění Jupyter notebook](https://code.visualstudio.com/docs/python/jupyter-support#debug-a-jupyter-notebook) v dokumentaci k Visual Studio Code.

Další funkce Visual Studio Code pro notebooky Jupyter také najdete v tématu [podpora Visual Studio Code-Jupyter](https://code.visualstudio.com/docs/python/jupyter-support) .

## <a name="next-steps"></a>Další kroky

- [Prozkoumat ukázkové poznámkové bloky](azure-notebooks-samples.md)

Články s postupy:

- [Vytváření a klonování projektů](create-clone-jupyter-notebooks.md)
- [Konfigurace a správa projektů](configure-manage-azure-notebooks-projects.md)
- [Instalace balíčků z poznámkového bloku](install-packages-jupyter-notebook.md)
- [Předložení prezentace](present-jupyter-notebooks-slideshow.md)
- [Práce s datovými soubory](work-with-project-data-files.md)
- [Přístup k datovým prostředkům](access-data-resources-jupyter-notebooks.md)
- [Používání Azure Machine Learningu](use-machine-learning-services-jupyter-notebooks.md)

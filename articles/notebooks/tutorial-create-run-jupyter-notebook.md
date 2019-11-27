---
title: Kurz – vytvoření a spuštění poznámkového bloku Jupyter v Azure
description: Postup vytvoření spustit Poznámkový blok Jupyter v poznámkových bloků Azure, který znázorňuje proces lineární regrese v datové vědy.
ms.topic: tutorial
ms.date: 01/11/2019
ms.openlocfilehash: 2c151cb0de2855856e92d9de07ad7dabfda2f55b
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277413"
---
# <a name="tutorial-create-and-run-a-jupyter-notebook-with-python"></a>Kurz: vytvoření a spuštění poznámkového bloku Jupyter s využitím Pythonu

Tento kurz vás provede procesem vytvoření kompletní Poznámkový blok Jupyter, který ukazuje jednoduchý lineární regrese pomocí poznámkových bloků Azure. V tomto kurzu se můžete seznámit s Poznámkový blok Jupyter uživatelské rozhraní, které zahrnuje vytvoření různé buňky s buňky a nabízí ten samý poznámkového bloku jako prezentaci.

Dokončený Poznámkový blok najdete na [ukázkách Azure Notebooks GitHubu](https://github.com/Microsoft/AzureNotebooks/tree/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps). Tento kurz, ale začíná nový projekt a prázdný poznámkový blok, může docházet, vytvoříte ho krok za krokem.

## <a name="create-the-project"></a>Vytvoření projektu

1. Přejít na [Azure Notebooks](https://notebooks.azure.com) a přihlásit se. (Podrobnosti najdete v tématu [rychlý Start – přihlášení k Azure Notebooks](quickstart-sign-in-azure-notebooks.md)).

1. Na stránce veřejný profil vyberte v horní části stránky **Moje projekty** :

    ![Moje projekty odkaz horní části okna prohlížeče](media/quickstarts/my-projects-link.png)

1. Na stránce **Moje projekty** vyberte **+ Nový projekt** (Klávesová zkratka: n); tlačítko se může zobrazit jenom jako **+** , pokud je okno prohlížeče úzké:

    ![Nový projekt – příkaz na stránce Mé projekty](media/quickstarts/new-project-command.png)

1. V místní nabídce **vytvořit nový projekt** , která se zobrazí, zadejte nebo nastavte následující podrobnosti a pak vyberte **vytvořit**:

    - **Název projektu**: příklad lineární regrese – Cricket chirps
    - **ID projektu**: lineární regrese – příklad
    - **Veřejný projekt**: (nezaškrtnuto)
    - **Vytvořit Readme.MD**: (nezaškrtnuto)

1. Po chvíli se poznámkových bloků Azure přejde do nového projektu.

## <a name="create-the-data-file"></a>Vytvoření datového souboru

Model lineární regrese, který vytvoříte v poznámkovém bloku, nakreslí data ze souboru v projektu s názvem *cricket_chirps. csv*. Tento soubor můžete vytvořit buď tak, že ho zkopírujete ze [vzorků Azure Notebooks GitHubu](https://github.com/Microsoft/AzureNotebooks/tree/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps)nebo přímo zadáním dat. Následující části popisují oba přístupy.

### <a name="upload-the-data-file"></a>Odeslat datový soubor

1. Na řídicím panelu projektu v Azure Notebooks vyberte **odeslat** > **z adresy URL** .
1. V místní nabídce zadejte do pole **Adresa URL souboru** následující adresu URL a v části **název souboru** *cricket_chirps. csv* a potom vyberte **Hotovo**.

    ```url
    https://raw.githubusercontent.com/Microsoft/AzureNotebooks/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps/cricket_chirps.csv
    ```

1. Soubor *cricket_chirps. csv* by se teď měl zobrazit v seznamu souborů vašeho projektu:

    ![Nově vytvořený soubor CSV zobrazující v seznamu souboru projektu](media/tutorial/csv-file-in-project.png)

### <a name="create-a-file-from-scratch"></a>Vytvořte soubor úplně od začátku

1. Na řídicím panelu projektu v Azure Notebooks vyberte **+ nový** > **prázdný soubor** .
1. Pole se zobrazí v seznamu souboru projektu. Zadejte *cricket_chirps. csv* a stiskněte klávesu ENTER.
1. Klikněte pravým tlačítkem na *cricket_chirps. csv* a vyberte **Upravit soubor**.
1. V editoru, který se zobrazí zadejte následující údaje:

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

## <a name="install-project-level-packages"></a>Instalace balíčků na úrovni projektu

V rámci poznámkového bloku můžete vždy použít příkazy jako `!pip install` v buňce kódu k instalaci požadovaných balíčků. Tyto příkazy jsou však spustit pokaždé, když spustíte buňky poznámkovém bloku kódu a může být časově náročné. Z tohoto důvodu můžete místo toho instalovat balíčky na úrovni projektu pomocí souboru `requirements.txt`.

1. Pomocí postupu popsaného v části [Vytvoření souboru od začátku vytvořte](#create-a-file-from-scratch) soubor s názvem `requirements.txt` s následujícím obsahem:

    ```text
    matplotlib==3.0.0
    numpy==1.15.0
    pandas==0.23.4
    scikit-learn==0.20.0
    ```

    Soubor `requirements.txt` můžete také odeslat z místního počítače, pokud dáváte přednost, jak je popsáno v tématu [odeslání datového souboru](#upload-the-data-file).

1. Na řídicím panelu projekt vyberte **nastavení projektu**.
1. V místní nabídce, která se zobrazí, vyberte kartu **prostředí** a pak vyberte **+ Přidat**.
1. V prvním ovládacím prvku rozevíracího seznamu (operace) v části **nastavení prostředí**vyberte **požadavky. txt**.
1. V druhém ovládacím prvku rozevírací seznam (název souboru) vyberte soubor *požadavky. txt* (soubor, který jste vytvořili).
1. V třetím ovládacím prvku rozevíracího seznamu (verze Pythonu) vyberte **Python verze 3,6**.
1. Vyberte **Uložit**.

![Na kartě nastavení prostředí projektu soubor requirements.txt, který zadáte](media/tutorial/tutorial-requirements-txt.png)

Tento krok instalace na místě se všechny Poznámkový blok, který spustíte v projektu běžet v prostředí kde tyto balíčky nainstalují.

## <a name="create-and-run-a-notebook"></a>Vytvoření a spuštění poznámkového bloku

Připraveno datový soubor a nastavení prostředí projektu můžete teď vytvořit a otevřete Poznámkový blok.

1. Na řídicím panelu projekt vyberte **+ nový** > **Poznámkový blok**.
1. V překryvném okně zadejte *příklad lineární regrese – Cricket chirps. ipynb* pro **položku název položky**, zvolte **Python 3,6** pro jazyk a pak vyberte **Nový**.
1. Poté, co nového poznámkového bloku se zobrazí v seznamu souborů, vyberte ji a spuštění poznámkového bloku. Automaticky se otevře na nové kartě prohlížeče.
1. Vzhledem k tomu, že máte soubor *. txt s požadavky* v nastavení prostředí, zobrazí se zpráva "čeká se na dokončení přípravy kontejneru". Kliknutím na **tlačítko OK** můžete zprávu Zavřít a pokračovat v práci na poznámkovém bloku. Nemůžete však spouštět buňky kódu, dokud nebude prostředí zcela nastavené.
1. Poznámkový blok se otevře v rozhraní Jupyter s buňky jeden prázdný kód jako výchozí.

    [![počáteční zobrazení nového poznámkového bloku v Azure Notebooks](media/tutorial/tutorial-new-notebook.png)](media/tutorial/tutorial-new-notebook.png#lightbox)

## <a name="tour-the-notebook-interface"></a>Přehled rozhraní poznámkového bloku

Pomocí poznámkového bloku spuštěna můžete přidat kód a buňky Markdownu, spusťte tyto buňky a můžete spravovat činnost poznámkového bloku. Nejprve je však vhodné trvá několik minut se seznámit s rozhraním. Úplnou dokumentaci získáte příkazem nabídky Help ** > ** **Poznámkový blok** .

V horní části okna se zobrazí následující položky:

(A) název v poznámkovém bloku, který můžete upravit kliknutím.
(B) tlačítek přejděte na projekt a řídicím panelu projekty, které otevřete nových kartách v prohlížeči.
Nabídka (C) A s příkazy pro práci s poznámkového bloku.
(D) panel nástrojů, pomocí klávesové zkratky pro běžné operace.
(E) úpravy plátna obsahující buňky.
(F) indikátor, zda je Poznámkový blok důvěryhodný (výchozí není **důvěryhodný**).
(G) jádru používá ke spouštění spolu s indikátorem aktivity poznámkového bloku.

[![oblasti primárního uživatelského rozhraní rozhraní Jupyter](media/tutorial/tutorial-notebook-ui.png)](media/tutorial/tutorial-notebook-ui.png#lightbox)

Jupyter poskytuje integrované si základní prvky uživatelského rozhraní. Spusťte prohlídku tak, že vyberete příkaz **Prohlídka uživatelského rozhraní** > **help** a kliknete na automaticky otevíraná okna.

Skupiny příkazů nabídky jsou následující:

| Nabídka | Popis |
| --- | --- |
| File | Příkazy pro správu soubor poznámkového bloku, včetně příkazů při vytváření a kopírování poznámkových bloků, zobrazit náhled před tiskem a stáhnout Poznámkový blok v různých formátech. |
| Upravit | Typické příkazy Vyjmout, kopírovat a vkládat buňky, najít a nahradit hodnoty, spravovat přílohy buňky a vkládat obrázky.  |
| Zobrazení | Příkazy pro řízení viditelnosti různé části uživatelského rozhraní aplikace Jupyter. |
| Vložit | Příkazů vložte novou buňku nad nebo pod aktuální buňky. Můžete použít tyto příkazy často při vytvoření poznámkového bloku. |
| Buňky | Různé příkazy **spuštění** spouštějí jednu nebo více buněk v různých kombinacích. Příkazy **typu buňky** mění typ buňky mezi **kódem**, **Markdownu**a **nezpracovaným NBConvert** (prostý text). Příkazy **aktuální výstupy** a **všechny výstupy** řídí způsob, jakým se zobrazuje výstup z kódu spuštění, a obsahuje příkaz pro vymazání všech výstupů. |
| Jádra | Příkazy pro správu způsobu spouštění kódu v jádru spolu s **jádrem Change** pro změnu jazyka nebo verze Pythonu používané ke spuštění poznámkového bloku. |
| Data | Příkazy pro nahrávání a stahování souborů z projektu nebo relace. Viz [práce s datovými soubory projektu](work-with-project-data-files.md) |
| pomůcky | Příkazy pro správu [widgetů Jupyter](https://ipywidgets.readthedocs.io/en/stable/examples/Widget%20Basics.html), které poskytují další možnosti pro vizualizaci, mapování a vykreslování.|
| Nápověda | Příkazy, které poskytují nápovědy a dokumentace pro rozhraní Jupyter. |

Většina příkazů na panelu nástrojů má ekvivalentní příkazy. Jedinou výjimkou je, že je k dispozici **prezentace pro vložení nebo úpravu**, která je popsána v [poznámkových blocích pro sdílení](present-jupyter-notebooks-slideshow.md)

Použijete některé z těchto příkazů jako naplnit je Poznámkový blok v následujících částech.

## <a name="create-a-markdown-cell"></a>Vytváření Markdownu buňky

1. Klikněte do první prázdné buňky, zobrazí na plátně poznámkového bloku. Ve výchozím nastavení je buňka typem **kódu** , což znamená, že je navržena tak, aby obsahovala kód spustitelný pro vybrané jádro (Python, R F#nebo). Aktuální typ je zobrazen v typu rozevíracího seznamu na panelu nástrojů:

    ![Buňka nástrojů rozevírací nabídky typu](media/tutorial/tutorial-cell-type-drop-down.png)

1. Pomocí rozevíracího seznamu na panelu nástrojů změňte typ buňky na **Markdownu** ; případně **použijte > buňky** **typ** buňky > příkazu nabídky **Markdownu** :

    ![Příkaz nabídky typ buňky](media/tutorial/tutorial-cell-type-menu.png)

1. Klikněte na buňku, kterou chcete začít upravovat, a pak zadejte následující Markdown:

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

1. Chcete-li vykreslit Markdownu do HTML pro prohlížeč, vyberte příkaz **Spustit** na panelu nástrojů nebo použijte **příkaz > ** **Spustit buňky** . Kód Markdownu pro formátování a odkazy teď budou zobrazovat jako očekáváte, že je v prohlížeči.

1. Při spuštění poslední buňky v poznámkovém bloku Jupyter automaticky vytvoří novou buňku pod ta, kterou jste spustili. Vložte další Markdownu do této buňce opakováním kroků v této části s následující Markdown:

    ```markdown
    ## Install packages using pip or conda

    Because the code in your notebook likely uses some Python packages, you need to make sure the Notebook environment contains those packages. You can do this directly within the notebook in a code block that contains the appropriate pip or conda commands prefixed by `!`:

    \```
    !pip install <pkg name>

    !conda install <pkg name> -y
    \```
    ```

1. Chcete-li upravit Markdown znovu, klikněte dvakrát na vykresleného buňky. K vykreslení HTML znovu po provedení změn, spusťte na buňku.

## <a name="create-a-code-cell-with-commands"></a>Vytvořit buňku kódu pomocí příkazů

Jak jsme vysvětlili předcházejí buňky Markdown, můžete zahrnout příkazy přímo v poznámkovém bloku. Příkazy můžete použít k instalaci balíčků, spusťte nástroj curl nebo wget pro načtení dat nebo cokoli jiného. Poznámkové bloky Jupyter se efektivně provozovat v rámci virtuálního počítače s Linuxem, abyste získali úplný Linuxový příkaz nastaven pro práci s.

1. Níže uvedené příkazy zadejte v buňce kódu, která se zobrazila po použití **příkazu Spustit** na předchozí buňce Markdownu. Pokud nevidíte novou buňku, vytvořte ji s **vložením** > **Vložit buňku níže** nebo použijte tlačítko **+** na panelu nástrojů.

    ```bash
    !pip install numpy
    !pip install matplotlib
    !pip install pandas
    !pip install sklearn
    ```

1. Před spuštěním této buňky vytvořte novou buňku s tlačítkem **+** na panelu nástrojů, nastavte ji na Markdownu a zadejte následující vysvětlení:

    ```markdown
    Note that when you run a code block that contains install commands, and also those with `import` statements, it make take the notebooks a little time to complete the task. To the left of the code block you see `In [*]` to indicate that execution is happening. The Notebook's kernel on the upper right also shows a filled-in circle to indicate "busy."
    ```

1. Vyberte **buňku** > **spuštění příkazu Spustit vše** pro spuštění všech buněk v poznámkovém bloku. Všimněte si, že buňky Markdownu vykreslují jako HTML a pomocí příkazu Spustit v jádru a sledujte indikátor jádra, jak je popsáno v Markdownu, samotný:

    ![Indikátor zaneprázdnění pro jádra poznámkového bloku](media/tutorial/tutorial-kernel-busy.png)

1. I když se všechny `pip install` příkazy spouštějí a protože jste už tyto balíčky v prostředí projektu nainstalovali (a protože jsou taky zahrnuté ve výchozím nastavení Azure Notebooks), zobrazí se celá řada zpráv, které si přečtou požadavek, který už vyhovuje. Celý výstup může být vizuálně rušivý, takže vyberte tento prodej (pomocí jediného kliknutí) a pak **použijte > ** **výstupy buněk** > **přepínač** pro skrytí výstupu. Výstup můžete zcela odebrat také pomocí příkazu **clear** v této nabídce.

    Příkaz **přepínání** skrývá pouze poslední výstup z buňky; Pokud znovu spustíte tuto buňku, výstup se znovu zobrazí.

1. Vzhledem k tomu, že balíčky jsou nainstalovány v prostředí projektu, odkomentujte `! pip install` příkazy pomocí `#`; tímto způsobem můžou zůstat v poznámkovém bloku jako instruktážní materiál, ale nebudete je muset spouštět a nebudete mít k disdobu zbytečné výstupy. V takovém případě uchovávání komentářem příkazy v poznámkovém bloku také určuje závislosti poznámkového bloku.

    ```bash
    # !pip install numpy
    # !pip install matplotlib
    # !pip install pandas
    # !pip install sklearn
    ```

## <a name="create-the-remaining-cells"></a>Vytvořit zbývající buňky

K vyplnění zbývající části Poznámkový blok, můžete pak vytvořit sérii Markdown a buňky s kódem. Pro každou buňku uvedených níže nejprve vytvoříte novou buňku, pak nastavte typ, pak vložte obsah.

I když můžete počkat a po vytvoření každé buňce Poznámkový blok spustit, je zajímavé pro spuštění jednotlivých buněk při vytváření. Ne všechny buňky zobrazit výstup; Pokud nevidíte všechny chyby, se předpokládá, že buňka spustil normálně.

Každá buňka kód závisí na kódu, který je spuštěn v předchozí buňky a Pokud opomenete ke spuštění jednu z buněk, novější buňky může způsobit chyby. Pokud zjistíte, že jste zapomněli spustit buňku, zkuste použít **buňku** > **Spustit vše výše** před spuštěním aktuální buňky.

Pokud se zobrazí neočekávaným výsledkům (což je pravděpodobně bude!), zkontrolujte, že každá buňka je nastavena na "Kód" nebo "Markdownu" podle potřeby. Například chybu "Neplatná syntaxe" obvykle dochází, když jste zadali Markdownu do buňky kódu.

1. Buňka markdown:

    ```markdown
    ## Import packages and prepare the dataset

    In this example we're using numpy, pandas, and matplotlib. Data is in the file cricket_chirps.csv. Because this file is in the same project as this present Notebook, we can just load it using a relative pathname.
    ```

1. Buňky kódu; Při spuštění se zobrazí obsah tabulky jako výstup. Výstup můžete potlačit zadáním komentáře k příkazu `print`.

    ```python
    import numpy as np
    import pandas as pd

    dataset = pd.read_csv('cricket_chirps.csv')
    print(dataset)
    X = dataset.iloc[:, :-1].values  # Matrix of independent variables -- remove the last column in this data set
    y = dataset.iloc[:, 1].values    # Matrix of dependent variables -- just the last column (1 == 2nd column)
    ```

    > [!Note]
    > Může se zobrazit upozornění z tohoto kódu o "velikost numpy.dtype změnit"; upozornění můžete ignorovat.

1. Buňka markdown:

    ```markdown
    Next, split the dataset into a Training set (2/3rds) and Test set (1/3rd). We don't need to do any feature scaling because there is only one column of independent variables, and packages typically do scaling for you.
    ```

1. Buňky kódu; Při spuštění této buňce nemá žádný výstup.

    ```python
    from sklearn.model_selection import train_test_split

    X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 1/3, random_state = 0)
    ```

1. Buňka markdown:

    ```markdown
    ## Fit the data to the training set

    "Fitting" the data to a training set means making the line that describes the relationship between the independent and the dependent variables. With a simple data set like we're using here, you can visualize the line on a simple x-y plot: the x-axis is the independent variable (chirp count in this example), and the y-axis is the independent variable (temperature). Fitting the data means plotting all the points in the training set, then drawing the best-fit line through that data.

    With two independent variables you can imagine a three-dimensional plot with a line fitted to the data. At three or more independent variables, however, it's no longer easy to visualize the fit, but you get the idea. In the end, it's all just mathematics, which a computer can handle easily without having to form a mental picture!

    The regressor's `fit` method here creates the line, which algebraically is of the form `y = x*b1 + b0`, where b1 is the coefficient or slope of the line (which you can get to through `regressor.coef_`), and b0 is the intercept of the line at x=0 (which you can get to through `regressor.intercept_`).
    ```

1. Buňka kódu; Po spuštění se v této buňce zobrazí výstup `LinearRegression(copy_X=True, fit_intercept=True, n_jobs=None,normalize=False)`.

    ```python
    from sklearn.linear_model import LinearRegression

    regressor = LinearRegression()    # This object is the regressor, that does the regression
    regressor.fit(X_train, y_train)   # Provide training data so the machine can learn to predict using a learned model.
    ```

1. Buňka markdown:

    ```markdown
    ## Predict the results

    With the regressor in hand, we can predict the test set results using its `predict` method. That method takes a vector of independent variables for which you want predictions.

    Because the regressor is fit to the data by virtue of `coef_` and `intercept_`, a prediction is the result of `coef_ * x + intercept_`. (Indeed, `predict(0)` returns `intercept_` and `predict(1)` returns `intercept_ + coef_`.)

    In the code, the `y_test` matrix (from when we split the set) contains the real observations. `y_pred` assigned here contains the predictions for the same `X_test` inputs. It's not expected that the test or training points exactly fit the regression; the regression is trying to find the model that we can use to make predictions with new observations of the independent variables.
    ```

1. Buňka kódu; Při spuštění se v této buňce zobrazí výsledky, jako `[79.49588055 75.98873911 77.87719989 80.03544077 75.17939878]`.

    ```python
    y_pred = regressor.predict(X_test)
    print(y_pred)
    ```

1. Buňka markdown:

    ```markdown
    It's interesting to think that all the "predictions" we use in daily life, like weather forecasts, are just regression models of some sort working with various data sets. Those models are much more complicated than what's shown here, but the idea is the same.

    Knowing how predictions work help us understand that the actual observations we would collect in the moment will always be somewhat off from the predictions: the predictions fit exactly to the model, whereas the observations typically won't.

    Of course, such systems feed new observations back into the dataset to continually improve the model, meaning that predictions should get more accurate over time.

    The challenge is determining what data to actually use. For example, with weather, how far back in time do you go? How have weather patterns been changing decade by decade? In any case, something like weather predictions will be doing things hour by hour, day by day, for things like temperature, precipitation, winds, cloud cover, etc. Radar and other observations are of course fed into the model and the predictions are reduced to mathematics.
    ```

1. Buňka markdown:

    ```markdown
    ## Visualize the results

    The following code generates a plot: green dots are training data, red dots are test data, blue dots are predictions. Gray line is the regression itself. You see that all the blue dots are exactly on the line, as they should be, because the predictions exactly fit the model (the line).
    ```

1. Buňky kódu; Při spuštění, vytvoří tato buňka Grafický diagram. Pokud nemáte čas vykreslení první (a místo toho najdete v článku "Velikost obrázku 640 × 480 měřiče s 1 osy"), spusťte znovu buňku.

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

    ![Diagram výstup z matplotlib kódu](media/tutorial/tutorial-plot-output.png)

1. Buňka markdown:

    ```markdown
    ## Closing comments

    At the end of the day, when you create a model, you use training data. Then you start feeding test data (real observations) to see how well the model actually works. You may find that the model is a little inaccurate over time, in which case you retrain the model with some new data. Retraining the model means you're creating a new fit line that's used for predictions.

    This regression can be used to examine the variability of the relationship between temperature and chirp count. Of course, if the model proves too inaccurate (that is, the predictions aren't very good), then it suggests that we might need to introduce more independent variables like humidity, time of year, latitude, amount of moonlight, and so on. If you have such data, you can do separate lines regressions for each independent variable, and then do multiple regressions with combinations of independent variables. 

    Again, once you are working with more than one or two independent variables, it's much easier to use machine learning to crunch the numbers than to try to visualize it graphically.
    ```

## <a name="clear-outputs-and-rerun-all-cells"></a>Vymazat výstupy a znovu spusťte všechny buňky

Po provedení kroků v předchozí části a naplnit celý poznámkový blok, vytvořili jste i část spouštění kódu v rámci úplný kurz týkající se lineární regrese. Přímé kombinací kód a text je jedním z velké výhody poznámkových bloků!

Zkuste znovu spustit v celé Poznámkový blok:

1. Vymažte všechna data relace jádra a veškerý výstup z buňky, a to tak, že vyberete **jádro** > **restartovat & Vymazat výstup**. Tento příkaz je vždy vhodné spustit, když jste dokončili Poznámkový blok, pro jistotu, že jste ještě nevytvořili žádné neobvyklé závislosti mezi buňkami v kódu.

1. Spusťte Poznámkový blok znovu pomocí **buňky** > **Spustit vše**. Všimněte si, že indikátor jádra je vyplněna je spuštěný kód.

    Pokud máte nějaký kód, který se spouští příliš dlouho nebo jinak se zablokuje, můžete zastavit jádro pomocí příkazu **kernel** > **Interrupt** .

1. Projděte si ho na podívejte se na výsledky. (Pokud znovu vykreslení nezobrazí, spusťte znovu buňka.)

## <a name="save-halt-and-close-the-notebook"></a>Uložte, zastavení a zavřete poznámkový blok

Během úprav poznámkového bloku můžete jeho aktuální stav uložit pomocí příkazu **soubor** > **Uložit a kontrolní bod** nebo tlačítko Uložit na panelu nástrojů. "Kontrolního bodu" vytvoří snímek, který můžete se vrátit k kdykoli během relace. Kontrolní body umožňují provést řadu experimentálních změn, a pokud tyto změny nefungují, **můžete se vrátit** k kontrolnímu bodu pomocí příkazu > **vrátit zpět ke** kontrolnímu bodu. Alternativním přístupem je vytvoření další buňky a Odkomentujte veškerý kód, který nechcete spuštění. v obou případech funguje.

Můžete také použít **soubor** > **vytvořit kopii** kdykoli a vytvořit kopii aktuálního stavu poznámkového bloku do nového souboru v projektu. Tuto kopii automaticky se otevře na nové kartě prohlížeče.

Po dokončení práce s poznámkovým blokem použijte **příkaz** > **Zavřít a zastavit** , který zavře Poznámkový blok a ukončí jádro, na kterém je spuštěný. Poznámkových bloků Azure klikněte na kartu prohlížeče se automaticky zavře.

## <a name="debug-notebooks-using-visual-studio-code"></a>Ladění pomocí Visual Studio Code poznámkové bloky

Pokud kód buňky v poznámkovém bloku není chovají způsobem, jakým jste očekávali, můžete mít kód chyby nebo jiné chyby. Nicméně Kromě použití příkazů `print` k zobrazení hodnoty proměnných, typické prostředí Jupyter nenabízí žádné ladicí pomůcky.

Naštěstí si můžete stáhnout soubor *. ipynb* poznámkového bloku a pak ho otevřít v Visual Studio Code pomocí rozšíření Python. Rozšíření přímo importuje Poznámkový blok jako jeden soubor kódu a zachovává vaše Markdownu buňky v komentářích. Po importu poznámkového bloku můžete pomocí ladicího programu Visual Studio Code krokovat kód, nastavit zarážky, kontrolovat stav a tak dále. Po provedení oprav kódu pak exportujte soubor *. ipynb* z Visual Studio Code a nahrajte ho zpátky do Azure Notebooks.

Další informace najdete v tématu [ladění Jupyter poznámkového bloku](https://code.visualstudio.com/docs/python/jupyter-support#debug-a-jupyter-notebook) v dokumentaci k Visual Studio Code.

Další funkce Visual Studio Code pro notebooky Jupyter také najdete v tématu [podpora Visual Studio Code-Jupyter](https://code.visualstudio.com/docs/python/jupyter-support) .

## <a name="next-steps"></a>Další kroky

- [Prozkoumat ukázkové poznámkové bloky](azure-notebooks-samples.md)

Články s návody:

- [Vytváření a klonování projektů](create-clone-jupyter-notebooks.md)
- [Konfigurace a správa projektů](configure-manage-azure-notebooks-projects.md)
- [Instalace balíčků z poznámkového bloku](install-packages-jupyter-notebook.md)
- [Prezentace prezentace](present-jupyter-notebooks-slideshow.md)
- [Práce s datovými soubory](work-with-project-data-files.md)
- [Přístup k datovým prostředkům](access-data-resources-jupyter-notebooks.md)
- [Použít Azure Machine Learning](use-machine-learning-services-jupyter-notebooks.md)

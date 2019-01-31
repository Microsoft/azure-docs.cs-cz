---
title: Přístup k datovým sadám pomocí Pythonu klientské knihovny - vědecké zpracování týmových dat
description: Instalace a použití klientské knihovny pro Python k přístupu a bezpečně spravovat Azure Machine Learning data z místního prostředí Pythonu.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: bf0e679ab46752d71ba4f5ef2b014e0cb2b4c6ad
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55471993"
---
# <a name="access-datasets-with-python-using-the-azure-machine-learning-python-client-library"></a>Přístup k datovým sadám pomocí Pythonu a klientské knihovny služby Azure Machine Learning pro Python
Klientská knihovna Python pro Microsoft Azure Machine Learning ve verzi preview můžete povolit zabezpečený přístup k vaší datové sady Azure Machine Learning z místního prostředí Pythonu a umožňuje vytváření a Správa datových sad v pracovním prostoru.

Toto téma obsahuje pokyny o tom, jak:

* Nainstalujte klientské knihovny pro Python pro Machine Learning
* přístup a nahrajte datové sady, včetně informací o tom, jak získat autorizaci pro přístup k datové sady Azure Machine Learning z vašeho místního prostředí Pythonu
* přístup k zprostředkující datovým sadám z experimentů
* Použití klientské knihovny pro Python vytvářet výčty sad dat, přístupu k metadatům, přečíst obsah datové sady, vytvořit nové datové sady a aktualizaci stávajících objektech datových sad

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="prerequisites"></a>Požadavky
Klientská knihovna Python pro prošel testováním v následujících prostředích:

* Windows, Mac a Linux
* Python 2.7, 3.3 a 3.4

Je závislý na následujících balíčků:

* požadavků
* Python dateutil
* pandas

Doporučujeme použít distribuci jazyka Python, jako [Anaconda](http://continuum.io/downloads#all) nebo [zápoje](https://store.enthought.com/downloads/), které pocházejí s využitím Pythonu, IPython a nainstalovat tři výše uvedených balíčků. I když IPython není bezpodmínečně nutné, je skvělé prostředí pro zpracování a interaktivní vizualizace dat.

### <a name="installation"></a>Postup instalace Klientská knihovna Python pro Azure Machine Learning
Klientská knihovna Python pro Azure Machine Learning musí také nainstalovaná dokončit úlohy popsané v tomto tématu. Je k dispozici [indexu balíčků Pythonu](https://pypi.python.org/pypi/azureml). Ho Pokud chcete nainstalovat ve vašem prostředí Pythonu, spusťte následující příkaz z vašeho místního prostředí Pythonu:

    pip install azureml

Alternativně můžete stáhnout a nainstalovat ze zdroje na [Githubu](https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python).

    python setup.py install

Pokud máte na svém počítači nainstalovaný git, můžete nainstalovat přímo z úložiště git pip:

    pip install git+https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python.git


## <a name="datasetAccess"></a>Používání fragmentů kódu Studio pro přístup k datové sady
Klientská knihovna Python poskytuje programový přístup k stávajících objektech datových sad z experimenty, které byly spuštěny.

Z webového rozhraní Studio můžete vytvořit fragmenty kódu, které zahrnují všechny informace potřebné ke stažení a deserializovat datové sady jako pandas DataFrame objekty na svém místním počítači.

### <a name="security"></a>Zabezpečení pro přístup k datům
Fragmenty kódu Studio k dispozici pro použití s klientské knihovny pro Python obsahuje id pracovního prostoru a autorizační token. Tyto poskytují úplný přístup k vašemu pracovnímu prostoru a musí být chráněn, jako je heslo.

Z bezpečnostních důvodů funkce fragmentu kódu je k dispozici pouze pro uživatele, kteří mají jejich role nastavit jako **vlastníka** pro pracovní prostor. Vaše role se zobrazí v Azure Machine Learning studia na **uživatelé** stránky **nastavení**.

![Zabezpečení][security]

Pokud vaše role není nastaven jako **vlastníka**, můžete buď vyžádat se pozvat jako vlastníka, nebo požádejte vlastníka pracovního prostoru, kde přinášejí fragmentu kódu.

Získat autorizační token, proveďte jednu z následujících akcí:

* Požádejte o token od vlastníka. Vlastníci dostanete ze stránky nastavení jejich pracovního prostoru v nástroji Studio jejich autorizačních tokenech. Vyberte **nastavení** v levém podokně a klepněte na **AUTORIZAČNÍCH TOKENECH** zobrazíte primární a sekundární tokeny. Přestože primární nebo sekundární autorizačních tokenech je možné ve fragmentu kódu, se doporučuje vlastníky sdílet jenom sekundární autorizačních tokenech.

![Autorizačních tokenech](./media/python-data-access/ml-python-access-settings-tokens.png)

* Požádejte o povýšen na roli vlastníka. Aktuální vlastník pracovního prostoru musí k tomu, odeberte nejprve můžete z pracovního prostoru a poté znovu pozvat, abyste k němu jako vlastníka.

Jakmile vývojáři získají id pracovního prostoru a autorizační token, budou mít možnost si přístup k pracovním prostoru pomocí fragmentu kódu bez ohledu na jejich role.

Tokeny ověřování se spravují na **AUTORIZAČNÍCH TOKENECH** stránky **nastavení**. Můžete je obnovit, ale tento postup odebere přístup k předchozí token.

### <a name="accessingDatasets"></a>Přístup k datovým sadám z místní aplikace v Pythonu
1. V nástroji Machine Learning Studio, klikněte na tlačítko **datových sad** na navigačním panelu na levé straně.
2. Vyberte datovou sadu, kterou chcete získat přístup. Můžete vybrat některý z datové sady z **Moje datové sady** seznamu nebo z **UKÁZKY** seznamu.
3. Dolní panel nástrojů, klikněte na **generovat kód přístupu k datům**. Pokud jsou data ve formátu, který je kompatibilní s klientské knihovny pro Python, toto tlačítko je zakázané.
   
    ![Datové sady][datasets]
4. Vyberte fragment kódu z okna, který se zobrazí a zkopírujte do schránky.
   
    ![Data přístupový kód tlačítko Generovat][dataset-access-code]
5. Vložte kód do poznámkového bloku vaše místní aplikace v Pythonu.
   
    ![Kód vložte do poznámkového bloku][ipython-dataset]

## <a name="accessingIntermediateDatasets"></a>Přístup k zprostředkující datovým sadám z experimenty Machine Learning
Po spuštění experimentu v nástroji Machine Learning Studio je možné pracovat s zprostředkující datové sady z uzlů výstupní moduly. Zprostředkující datové sady jsou data, která byla vytvořena a používá se pro zprostředkující kroky při byl spuštěn nástroj modelu.

Zprostředkující datové sady je možný za předpokladu, formát dat je kompatibilní s klientské knihovny pro Python.

Podporují se následující formáty (konstanty pro ty jsou `azureml.DataTypeIds` třídy):

* Ve formátu prostého textu
* GenericCSV
* GenericTSV
* GenericCSVNoHeader
* GenericTSVNoHeader

Podržením ukazatele nad uzlem výstup modulu můžete určit formát. Zobrazí se spolu s název uzlu v popisku.

Některé moduly, například [rozdělení] [ split] modul, výstupní formát s názvem `Dataset`, není podporována klientské knihovny pro Python.

![Formát datové sady][dataset-format]

Je potřeba použít převod modulu, například [převést na sdílený svazek clusteru][convert-to-csv], chcete-li získat výstup do podporovaného formátu.

![Formát GenericCSV][csv-format]

Následující postup ukazuje příklad, který vytvoří experiment, spustí jej a přistupuje k zprostředkující datové sady.

1. Vytvoření nového experimentu.
2. Vložit **datovou sadu pro dospělé binární klasifikace příjmů sčítání** modulu.
3. Vložit [rozdělení] [ split] modulu a připojit svůj vstup do modulu výstupní datovou sadu.
4. Vložit [převést na sdílený svazek clusteru] [ convert-to-csv] modulu a vstup do jednoho z připojení [rozdělení] [ split] výstupy modulů.
5. Uložte experiment, spusťte ji a počkejte na dokončení.
6. Klikněte na uzel výstup [převést na sdílený svazek clusteru] [ convert-to-csv] modulu.
7. Jakmile se zobrazí v místní nabídce, vyberte **generovat kód přístupu k datům**.
   
    ![Kontextová nabídka][experiment]
8. Vyberte fragment kódu a zkopírujte do schránky z okna, která se zobrazí.
   
    ![Generovat kód pro přístup k místní nabídce][intermediate-dataset-access-code]
9. Vložte kód v poznámkovém bloku.
   
    ![Kód vložte do poznámkového bloku][ipython-intermediate-dataset]
10. Můžete vizualizovat data pomocí matplotlib. Zobrazí se v histogramu, věk sloupce:
    
    ![Histogram][ipython-histogram]

## <a name="clientApis"></a>Klientská knihovna Python pro Machine Learning umožňuje přístup, číst, vytvářet a spravovat datové sady
### <a name="workspace"></a>Pracovní prostor
Pracovní prostor je vstupním bodem pro klientské knihovny pro Python. Zadejte `Workspace` třída s atributem id pracovního prostoru a autorizační token k vytvoření instance:

    ws = Workspace(workspace_id='4c29e1adeba2e5a7cbeb0e4f4adfb4df',
                   authorization_token='f4f3ade2c6aefdb1afb043cd8bcf3daf')


### <a name="enumerate-datasets"></a>Vytvářet výčty sad dat
Vytvořit výčet všech datových sad v daném pracovním prostoru:

    for ds in ws.datasets:
        print(ds.name)

Chcete získat výčet jenom uživatel vytvořil datové sady:

    for ds in ws.user_datasets:
        print(ds.name)

Chcete získat výčet jenom příklad datové sady:

    for ds in ws.example_datasets:
        print(ds.name)

Datová sada je přístupná po názvu (což je velká a malá písmena):

    ds = ws.datasets['my dataset name']

Nebo můžete k němu přístup podle indexu:

    ds = ws.datasets[0]


### <a name="metadata"></a>Metadata
Datové sady mají metadat, kromě obsah. (Zprostředkující datové sady jsou výjimkou z tohoto pravidla a nemají žádná metadata.)

Některé hodnoty metadat jsou přiřazeny uživatelem v okamžiku vytvoření:

    print(ds.name)
    print(ds.description)
    print(ds.family_id)
    print(ds.data_type_id)

Ostatní hodnoty přiřazené službou Azure ML jsou:

    print(ds.id)
    print(ds.created_date)
    print(ds.size)

Zobrazit `SourceDataset` třídy pro další informace o dostupných metadat.

### <a name="read-contents"></a>Přečtěte si obsah
Fragmenty kódu, které jsou automaticky poskytované Machine Learning Studio stáhnout a datovou sadu, která objekt pandas DataFrame deserializovat. Používá se k tomu `to_dataframe` metody:

    frame = ds.to_dataframe()

Pokud chcete stáhnout nezpracovaná data a provádět sami deserializace, který je možnost. V tuto chvíli Toto je jediná možnost pro formáty, jako je například "ARFF", které nelze deserializovat klientské knihovny pro Python.

Pro daný obsah přečíst jako text:

    text_data = ds.read_as_text()

K načtení obsahu jako binární soubor:

    binary_data = ds.read_as_binary()

Můžete také pouze otevřít datový proud obsahu:

    with ds.open() as file:
        binary_data_chunk = file.read(1000)


### <a name="create-a-new-dataset"></a>Vytvoření nové datové sady
Klientská knihovna Python vám umožní nahrát datové sady z programu Pythonu. Tyto datové sady jsou pak k dispozici pro použití ve vašem pracovním prostoru.

Pokud máte data v pandas DataFrame, použijte následující kód:

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_dataframe(
        dataframe=frame,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

Pokud vaše data již serializován, můžete použít:

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_raw_data(
        raw_data=raw_data,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

Klientská knihovna Python je schopen serializovat pandas DataFrame do následujících formátů (konstanty pro ty jsou `azureml.DataTypeIds` třídy):

* Ve formátu prostého textu
* GenericCSV
* GenericTSV
* GenericCSVNoHeader
* GenericTSVNoHeader

### <a name="update-an-existing-dataset"></a>Aktualizovat existující datovou sadu
Pokud se pokusíte odeslat novou datovou sadu s názvem, který odpovídá existující datovou sadu, měli byste obdržet chybu konflikt.

Pokud chcete aktualizovat existující datovou sadu, musíte nejprve získat odkaz na existující datovou sadu:

    dataset = ws.datasets['existing dataset']

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Pak pomocí `update_from_dataframe` k serializaci a nahraďte jeho obsah datové sady v Azure:

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(frame2)

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Pokud chcete serializovat data do jiného formátu, zadejte hodnotu pro volitelný `data_type_id` parametru.

    from azureml import DataTypeIds

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        data_type_id=DataTypeIds.GenericTSV,
    )

    print(dataset.data_type_id) # 'GenericTSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Volitelně můžete nastavit nový popis tak, že zadáte hodnotu `description` parametru.

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        description='data up to feb 2015',
    )

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to feb 2015'

Volitelně můžete nastavit nový název tak, že zadáte hodnotu `name` parametru. Od této chvíle budete načte datovou sadu pomocí nového názvu. Následující kód aktualizuje data, název a popis.

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        name='existing dataset v2',
        description='data up to feb 2015',
    )

    print(dataset.data_type_id)                    # 'GenericCSV'
    print(dataset.name)                            # 'existing dataset v2'
    print(dataset.description)                     # 'data up to feb 2015'

    print(ws.datasets['existing dataset v2'].name) # 'existing dataset v2'
    print(ws.datasets['existing dataset'].name)    # IndexError

`data_type_id`, `name` a `description` parametry jsou volitelné a jejich předchozí hodnotu ve výchozím nastavení. `dataframe` Vždy parametr je povinný.

Pokud vaše data již serializován, použijte `update_from_raw_data` místo `update_from_dataframe`. Pokud vám stačí pouze předat `raw_data` místo `dataframe`, funguje podobným způsobem.

<!-- Images -->
[security]:./media/python-data-access/security.png
[dataset-format]:./media/python-data-access/dataset-format.png
[csv-format]:./media/python-data-access/csv-format.png
[datasets]:./media/python-data-access/datasets.png
[dataset-access-code]:./media/python-data-access/dataset-access-code.png
[ipython-dataset]:./media/python-data-access/ipython-dataset.png
[experiment]:./media/python-data-access/experiment.png
[intermediate-dataset-access-code]:./media/python-data-access/intermediate-dataset-access-code.png
[ipython-intermediate-dataset]:./media/python-data-access/ipython-intermediate-dataset.png
[ipython-histogram]:./media/python-data-access/ipython-histogram.png


<!-- Module References -->
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/


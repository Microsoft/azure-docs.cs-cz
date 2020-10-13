---
title: Přístup k datovým sadám pomocí klientské knihovny Python – vědecký proces týmového zpracování dat
description: Nainstalujte a použijte klientskou knihovnu Python pro přístup k datům Azure Machine Learning zabezpečeně z místního prostředí Pythonu a pro jejich správu.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, devx-track-python, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 6c8e6fee2b9f01b8d7ab48990760aa4c4d6e11b2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91361496"
---
# <a name="access-datasets-with-python-using-the-azure-machine-learning-python-client-library"></a>Přístup k datovým sadám pomocí Pythonu a klientské knihovny služby Azure Machine Learning pro Python
Náhled klientské knihovny Microsoft Azure Machine Learning Pythonu může povolit zabezpečený přístup k vašim datovým sadám Azure Machine Learning z místního prostředí Pythonu a umožňuje vytváření a správu datových sad v pracovním prostoru.

V tomto tématu najdete pokyny k těmto akcím:

* Instalace klientské knihovny Machine Learning Python
* přístup k datovým sadám a jejich nahrávání, včetně pokynů, jak získat autorizaci pro přístup k Azure Machine Learning datovým sadám z místního prostředí Pythonu
* přístup k mezilehlé datové sadě z experimentů
* použití klientské knihovny Pythonu k zobrazení výčtu datových sad, přístupu k metadatům, čtení obsahu datové sady, vytváření nových datových sad a aktualizaci existujících datových sad

## <a name="prerequisites"></a><a name="prerequisites"></a>Požadavky
Knihovna klienta Python byla testována v následujících prostředích:

* Windows, Mac a Linux
* Python 2,7, 3,3 a 3,4

Má závislost na následujících balíčcích:

* požádal
* Python – dateutil
* pandas

Doporučujeme použít distribuci Pythonu, jako je [Anaconda](https://www.anaconda.com/) nebo [zápoje](https://store.enthought.com/downloads/), která se dodává s Pythonem, IPython a třemi balíčky uvedenými výše. I když IPython není bezpodmínečně nutné, jedná se o Skvělé prostředí pro interaktivní práci a vizualizaci dat.

### <a name="how-to-install-the-azure-machine-learning-python-client-library"></a><a name="installation"></a>Postup instalace klientské knihovny Azure Machine Learning Pythonu
Nainstalujte klientskou knihovnu Azure Machine Learning Python pro dokončení úkolů popsaných v tomto tématu. Tato knihovna je k dispozici z [indexu balíčku Pythonu](https://pypi.python.org/pypi/azureml). Pokud ho chcete nainstalovat do prostředí Pythonu, spusťte následující příkaz z místního prostředí Pythonu:

```console
pip install azureml
```

Případně si můžete stáhnout a nainstalovat ze zdrojů na [GitHubu](https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python).

```console
python setup.py install
```

Pokud máte v počítači nainstalovaný Git, můžete k instalaci přímo z úložiště Git použít PIP:

```console
pip install git+https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python.git
```

## <a name="use-code-snippets-to-access-datasets"></a><a name="datasetAccess"></a>Použití fragmentů kódu pro přístup k datovým sadám
Knihovna klienta Python poskytuje programový přístup k vašim existujícím datovým sadám z experimentů, které byly spuštěny.

Z webového rozhraní Azure Machine Learning Studio (Classic) můžete vygenerovat fragmenty kódu, které zahrnují všechny nezbytné informace ke stažení a deserializaci datových sad jako objekty dataframe PANDAS na místním počítači.

### <a name="security-for-data-access"></a><a name="security"></a>Zabezpečení přístupu k datům
Fragmenty kódu, které poskytuje Azure Machine Learning Studio (Classic) pro použití s knihovnou klienta Python, zahrnují ID pracovního prostoru a autorizační token. Poskytují úplný přístup k vašemu pracovnímu prostoru a musí být chráněni jako heslo.

Z bezpečnostních důvodů je funkce fragmentu kódu dostupná jenom uživatelům, kteří mají roli nastavenou jako **vlastník** pracovního prostoru. Vaše role se zobrazí v Azure Machine Learning Studio (Classic) na stránce **Uživatelé** v části **Nastavení**.

![Snímek obrazovky zobrazuje nastavení na stránce Uživatelé Azure Machine Learning Studio.][security]

Pokud vaše role není nastavená jako **vlastník**, můžete buď požádat o pozvání jako vlastník, nebo požádat vlastníka pracovního prostoru, aby vám poskytl fragment kódu.

K získání autorizačního tokenu si můžete vybrat jednu z těchto možností:

* Požádat o token od vlastníka. Vlastníci mají přístup ke svým autorizačním tokenům ze stránky nastavení svého pracovního prostoru v Azure Machine Learning Studio (Classic). V levém podokně vyberte **Nastavení** a pro zobrazení primárních a sekundárních tokenů klikněte na **autorizační tokeny** . I když je možné použít primární nebo sekundární autorizační tokeny ve fragmentu kódu, doporučuje se, aby vlastníci sdíleli jenom sekundární autorizační tokeny.

   ![Autorizační tokeny](./media/python-data-access/ml-python-access-settings-tokens.png)

* Požádat o zvýšení úrovně role vlastníka: aktuální vlastník pracovního prostoru musí nejdřív odebrat z pracovního prostoru a potom ho znovu pozvat jako vlastníka.

Po získání ID pracovního prostoru a autorizačního tokenu můžou vývojáři získat přístup k pracovnímu prostoru pomocí fragmentu kódu bez ohledu na jejich roli.

Autorizační tokeny se spravují na stránce **AUTORIZAČNÍ tokeny** v **Nastavení**. Můžete je znovu vygenerovat, ale tato procedura odvolá přístup k předchozímu tokenu.

### <a name="access-datasets-from-a-local-python-application"></a><a name="accessingDatasets"></a>Přístup k datovým sadám z místní aplikace Pythonu
1. V Machine Learning Studio (Classic) klikněte na **datové sady** na navigačním panelu vlevo.
2. Vyberte datovou sadu, ke které se chcete dostat. Můžete vybrat libovolnou datovou sadu ze seznamu **Moje datové sady** nebo ze seznamu **ukázek** .
3. V dolním panelu nástrojů klikněte na možnost **generovat kód pro přístup k datům**. Pokud jsou data ve formátu nekompatibilním s klientskou knihovnou Pythonu, toto tlačítko je zakázané.
   
    ![Snímek obrazovky ukazuje datovou sadu s kódem pro přístup k datům.][datasets]
4. V okně, které se zobrazí, vyberte fragment kódu a zkopírujte ho do schránky.
   
    ![Tlačítko pro generování kódu pro přístup k datům][dataset-access-code]
5. Vložte kód do poznámkového bloku vaší místní aplikace v Pythonu.
   
    ![Vložení kódu do poznámkového bloku][ipython-dataset]

## <a name="access-intermediate-datasets-from-machine-learning-experiments"></a><a name="accessingIntermediateDatasets"></a>Přístup k mezilehlé datové sadě z Machine Learning experimentů
Po spuštění experimentu v Machine Learning Studio (Classic) je možné získat přístup k mezilehlé datové sadě z výstupních uzlů modulů. Mezilehlé datové sady jsou data, která byla vytvořena a použita pro mezilehlé kroky při spuštění nástroje modelu.

K pokročilým datovým sadám je možné přidružit, pokud je formát dat kompatibilní s klientskou knihovnou Pythonu.

Podporovány jsou následující formáty (konstanty pro tyto formáty jsou ve `azureml.DataTypeIds` třídě):

* PlainText
* GenericCSV
* GenericTSV
* GenericCSVNoHeader
* GenericTSVNoHeader

Formát můžete určit tak, že najedete myší na uzel výstupu modulu. Zobrazuje se spolu s názvem uzlu v popisu tlačítka.

Některé moduly, například [rozdělený][split] modul, výstup do formátu s názvem `Dataset` , který není podporován knihovnou klienta Python.

![Formát datové sady][dataset-format]

Chcete-li získat výstup do podporovaného formátu, je nutné použít převodový modul, například [převést na sdílený svazek clusteru][convert-to-csv].

![Formát GenericCSV][csv-format]

Následující kroky ukazují příklad, který vytváří experiment, spouští ho a přistupuje k mezilehlé datové sadě.

1. Vytvořte nový experiment.
2. Vloží modul **datové sady binární klasifikace pro příjem z dospělého** .
3. Vložte [rozdělený][split] modul a připojte jeho vstup k výstupu modulu DataSet.
4. Vložte modul [převést do sdíleného svazku clusteru][convert-to-csv] a připojte jeho vstup k jednomu z výstupů [rozděleného][split] modulu.
5. Uložte experiment, spusťte ho a počkejte, než se úloha dokončí.
6. Klikněte na uzel výstup v modulu [Převod do sdíleného svazku clusteru][convert-to-csv] .
7. Jakmile se zobrazí místní nabídka, vyberte možnost **generovat kód pro přístup k datům**.
   
    ![Místní nabídka][experiment]
8. Vyberte fragment kódu a zkopírujte ho do schránky z okna, které se zobrazí.
   
    ![Generovat přístupový kód z kontextové nabídky][intermediate-dataset-access-code]
9. Vložte kód do poznámkového bloku.
   
    ![Vložit kód do poznámkového bloku][ipython-intermediate-dataset]
10. Data můžete vizualizovat pomocí matplotlib. Zobrazí se v histogramu pro sloupec věk:
    
    ![Histogram][ipython-histogram]

## <a name="use-the-machine-learning-python-client-library-to-access-read-create-and-manage-datasets"></a><a name="clientApis"></a>Použití klientské knihovny Machine Learning Python pro přístup k datovým sadám, jejich čtení, vytváření a správě
### <a name="workspace"></a>Pracovní prostor
Pracovní prostor je vstupním bodem pro knihovnu klienta Python. Zadejte `Workspace` třídu s ID pracovního prostoru a autorizačním tokenem pro vytvoření instance:

```python
ws = Workspace(workspace_id='4c29e1adeba2e5a7cbeb0e4f4adfb4df',
               authorization_token='f4f3ade2c6aefdb1afb043cd8bcf3daf')
```

### <a name="enumerate-datasets"></a>Zobrazení výčtu datových sad
Zobrazení výčtu všech datových sad v daném pracovním prostoru:

```python
for ds in ws.datasets:
    print(ds.name)
```

Chcete-li vytvořit výčet pouze datových sad vytvořených uživatelem:

```python
for ds in ws.user_datasets:
    print(ds.name)
```

Chcete-li vytvořit výčet pouze ukázkových datových sad:

```python
for ds in ws.example_datasets:
    print(ds.name)
```

Můžete získat přístup k datové sadě podle názvu (rozlišuje velká a malá písmena):

```python
ds = ws.datasets['my dataset name']
```

Nebo k němu máte přístup podle indexu:

```python
ds = ws.datasets[0]
```

### <a name="metadata"></a>Metadata
Datové sady mají kromě obsahu i metadata. (Mezilehlé datové sady jsou výjimkou z tohoto pravidla a nemají žádná metadata.)

Některé hodnoty metadat jsou přiřazeny uživatelem v době vytváření:

* `print(ds.name)`
* `print(ds.description)`
* `print(ds.family_id)`
* `print(ds.data_type_id)`

Jiné jsou hodnoty přiřazené službou Azure ML:

* `print(ds.id)`
* `print(ds.created_date)`
* `print(ds.size)`

Další informace `SourceDataset` o dostupných metadatech najdete ve třídě.

### <a name="read-contents"></a>Číst obsah
Fragmenty kódu, které poskytuje Machine Learning Studio (Classic), automaticky stáhnou a deserializovat datovou sadu do objektu PANDAS dataframe. To se provádí pomocí `to_dataframe` metody:

```python
frame = ds.to_dataframe()
```

Pokud si přejete stáhnout nezpracovaná data a provést deserializaci sami, jedná se o možnost. V tuto chvíli je to jediná možnost pro formáty, jako je například ' ARFF ', kterou nelze deserializovat v knihovně klienta Python.

Čtení obsahu jako textu:

```python
text_data = ds.read_as_text()
```

Čtení obsahu jako binárního souboru:

```python
binary_data = ds.read_as_binary()
```

Můžete také otevřít datový proud pro obsah:

```python
with ds.open() as file:
    binary_data_chunk = file.read(1000)
```

### <a name="create-a-new-dataset"></a>Vytvořit novou datovou sadu
Knihovna klienta Python umožňuje nahrávat datové sady z programu Pythonu. Tyto datové sady jsou pak k dispozici pro použití ve vašem pracovním prostoru.

Pokud máte data v PANDAS dataframe, použijte následující kód:

```python
from azureml import DataTypeIds

dataset = ws.datasets.add_from_dataframe(
    dataframe=frame,
    data_type_id=DataTypeIds.GenericCSV,
    name='my new dataset',
    description='my description'
)
```

Pokud jsou vaše data už serializovaná, můžete použít:

```python
from azureml import DataTypeIds

dataset = ws.datasets.add_from_raw_data(
    raw_data=raw_data,
    data_type_id=DataTypeIds.GenericCSV,
    name='my new dataset',
    description='my description'
)
```

Knihovna klienta Pythonu je schopná serializovat PANDAS dataframe do následujících formátů (konstanty pro tyto jsou ve `azureml.DataTypeIds` třídě):

* PlainText
* GenericCSV
* GenericTSV
* GenericCSVNoHeader
* GenericTSVNoHeader

### <a name="update-an-existing-dataset"></a>Aktualizovat existující datovou sadu
Pokud se pokusíte nahrát novou datovou sadu s názvem, který se shoduje s existující datovou sadou, měla by se zobrazit chyba konfliktu.

Chcete-li aktualizovat existující datovou sadu, musíte nejprve získat odkaz na existující datovou sadu:

```python
dataset = ws.datasets['existing dataset']

print(dataset.data_type_id) # 'GenericCSV'
print(dataset.name)         # 'existing dataset'
print(dataset.description)  # 'data up to jan 2015'
```

Pak použijte `update_from_dataframe` k serializaci a nahrazení obsahu datové sady v Azure:

```python
dataset = ws.datasets['existing dataset']

dataset.update_from_dataframe(frame2)

print(dataset.data_type_id) # 'GenericCSV'
print(dataset.name)         # 'existing dataset'
print(dataset.description)  # 'data up to jan 2015'
```

Pokud chcete data serializovat v jiném formátu, zadejte hodnotu volitelného `data_type_id` parametru.

```python
from azureml import DataTypeIds

dataset = ws.datasets['existing dataset']

dataset.update_from_dataframe(
    dataframe=frame2,
    data_type_id=DataTypeIds.GenericTSV,
)

print(dataset.data_type_id) # 'GenericTSV'
print(dataset.name)         # 'existing dataset'
print(dataset.description)  # 'data up to jan 2015'
```

Volitelně můžete nastavit nový popis zadáním hodnoty `description` parametru.

```python
dataset = ws.datasets['existing dataset']

dataset.update_from_dataframe(
    dataframe=frame2,
    description='data up to feb 2015',
)

print(dataset.data_type_id) # 'GenericCSV'
print(dataset.name)         # 'existing dataset'
print(dataset.description)  # 'data up to feb 2015'
```

Volitelně můžete nastavit nový název zadáním hodnoty `name` parametru. Od této chvíle načtěte datovou sadu pouze pomocí nového názvu. Následující kód aktualizuje data, název a popis.

```python
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
```

`data_type_id`Parametry a `name` `description` jsou volitelné a výchozí jejich předchozí hodnota. `dataframe`Parametr je vždy vyžadován.

Pokud jsou vaše data už serializovaná, použijte `update_from_raw_data` místo `update_from_dataframe` . Pokud pouze předáte `raw_data` místo  `dataframe` , funguje podobným způsobem.

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


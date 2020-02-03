---
title: Zkoumání dat ve službě Azure blob storage pomocí knihovny pandas - vědecké zpracování týmových dat
description: Popisuje, jak zkoumat data, která je uložená v kontejneru objektů blob v Azure pomocí Pythonu balíčku pandas.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 29011760a94a05020150ceddeba4303b87c2f610
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722182"
---
# <a name="explore-data-in-azure-blob-storage-with-pandas"></a>Zkoumání dat ve službě Azure blob storage pomocí knihovny pandas

Tento článek popisuje, jak prozkoumat data uložená v kontejneru objektů BLOB v Azure pomocí balíčku Pythonu [PANDAS](https://pandas.pydata.org/) .

Tento úkol je krok v rámci [vědeckého procesu týmového zpracování dat](overview.md).

## <a name="prerequisites"></a>Předpoklady
Tento článek předpokládá, že máte:

* Vytvoření účtu služby Azure storage. Pokud potřebujete pokyny, přečtěte si téma [Vytvoření účtu Azure Storage](../../storage/common/storage-account-create.md) .
* Vaše data uložená v účtu úložiště objektů blob v Azure. Pokud potřebujete pokyny, přečtěte si téma [přesun dat do a z Azure Storage](../../storage/common/storage-moving-data.md)

## <a name="load-the-data-into-a-pandas-dataframe"></a>Načtení dat do pandas DataFrame
K prozkoumání a manipulaci s datovou sadu, ho musíte nejprve stáhnout na zdroj objektu blob do místního souboru, který lze načíst v pandas DataFrame. Tady jsou kroky pro tento postup:

1. Stáhněte si data z objektu blob Azure s následující ukázkou kódu Pythonu pomocí Blob service. Nahraďte konkrétní hodnoty proměnné v následujícím kódu:

```python
from azure.storage.blob import BlockBlobService
import tables

STORAGEACCOUNTNAME= <storage_account_name>
STORAGEACCOUNTKEY= <storage_account_key>
LOCALFILENAME= <local_file_name>
CONTAINERNAME= <container_name>
BLOBNAME= <blob_name>

#download from blob
t1=time.time()
blob_service=BlockBlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
t2=time.time()
print(("It takes %s seconds to download "+blobname) % (t2 - t1))
```

1. Načtení dat do pandas DataFrame ze staženého souboru.

```python
# LOCALFILE is the file path
dataframe_blobdata = pd.read_csv(LOCALFILE)
```

Nyní jste připraveni na zkoumání dat a generovat funkce pro tuto datovou sadu.

## <a name="blob-dataexploration"></a>Příklady průzkumu dat pomocí PANDAS
Tady je pár příkladů, jak zkoumat data pomocí pandas:

1. Kontrola **počtu řádků a sloupců**

```python
print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape
```

1. **Zkontrolujte** první nebo poslední **řádky** v následující datové sadě:

```python
dataframe_blobdata.head(10)

dataframe_blobdata.tail(10)
```

1. Ověřte, že se **datový typ** každého sloupce importoval pomocí následujícího ukázkového kódu.

```python
for col in dataframe_blobdata.columns:
    print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype
```

1. Ověřte **základní statistiky** pro sloupce v sadě dat následujícím způsobem.

```python
dataframe_blobdata.describe()
```

1. Podívejte se na počet položek pro každou hodnotu sloupce, které následujícím způsobem

```python
dataframe_blobdata['<column_name>'].value_counts()
```

1. **Počítat chybějící hodnoty** oproti skutečnému počtu položek v každém sloupci pomocí následujícího ukázkového kódu

```python
miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
print miss_num
```

1. Pokud v datech **chybí hodnoty** pro určitý sloupec, můžete je odstranit takto:

```python
dataframe_blobdata_noNA = dataframe_blobdata.dropna()
dataframe_blobdata_noNA.shape
```

Dalším způsobem, jak nahradit chybějících hodnot je ve funkci režimu:

```python
dataframe_blobdata_mode = dataframe_blobdata.fillna(
    {'<column_name>': dataframe_blobdata['<column_name>'].mode()[0]})
```

1. Vytvoření grafu **histogramu** pomocí proměnlivého počtu přihrádek k vykreslení distribuce proměnné

```python
dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')

np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
```

1. Podívejte se na **korelace** mezi proměnnými pomocí scatterplot nebo pomocí integrované funkce Correlation.

```python
# relationship between column_a and column_b using scatter plot
plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])

# correlation between column_a and column_b
dataframe_blobdata[['<column_a>', '<column_b>']].corr()
```

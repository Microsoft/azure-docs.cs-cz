---
title: Pomocí Azure Machine Learning Data přípravy rozšiřitelnost Python | Microsoft Docs
description: Tento dokument obsahuje přehled a příklady podrobné informace o použití kód Python rozšířit funkce Příprava dat
services: machine-learning
author: euangMS
ms.author: euang
ms.service: machine-learning
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 05/09/2018
ms.openlocfilehash: 6363d39b2dfbd36ccebff6780e35caf58ca84dda
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="data-preparations-python-extensions"></a>Rozšíření dat přípravy Python
Azure Machine Learning Data přípravy jako způsob vyplnění funkce mezery mezi integrované funkce, zahrnuje rozšíření na více úrovních. V tomto dokumentu jsme popisují rozšiřitelnost prostřednictvím skript v jazyce Python. 

## <a name="custom-code-steps"></a>Kroky vlastního kódu 
Data přípravy má následující vlastní kroky, kde uživatelé můžete napsat kód:

* Přidání sloupců
* Rozšířený filtr
* Transformace toku dat
* Transformace oddílu

## <a name="code-block-types"></a>Typy blok kódu 
Pro každý z těchto kroků jsme podporují dva typy kódu bloku. Nejprve podporujeme výraz úplné Python, který je provést, protože je. Za druhé podporujeme modul Python, kde voláme určitou funkci známé podpisem kódu, který zadáte.

Například můžete přidat nový sloupec, který vypočítá protokol jiný sloupec v následujících dvou způsobů:

Výraz 

```python    
    math.log(row["Score"])
```

Modul 
    
```python
def newvalue(row): 
        return math.log(row["Score"])
```


Transformace přidat sloupec v režimu modulu očekává funkci s názvem `newvalue` který přijímá řádek proměnné a vrátí hodnotu pro sloupec. Tento modul může obsahovat jakékoliv množství kód Python s další funkce, importy atd.

Podrobnosti o každém bodě rozšíření jsou popsané v následujících částech. 

## <a name="imports"></a>Importy 
Pokud použijete typ bloku výrazu, můžete přesto přidat **importovat** příkazů do vašeho kódu. Všechny musí být seskupeny do nejvyšší řádků kódu.

Opravte 

```python
import math 
import numpy 
math.log(row["Score"])
```
 

Chyba  

```python
import math  
math.log(row["Score"])  
import numpy
```
 
 
Pokud použijete typ bloku modul, můžete podle všechny normální Python pravidla pro používání **importovat** příkaz. 

## <a name="default-imports"></a>Výchozí importy
Následující importy jsou vždy zahrnuty a dá se použít v kódu. Nemusíte je znovu importovat. 

```python
import math  
import numbers  
import datetime  
import re  
import pandas as pd  
import numpy as np  
import scipy as sp
```
  

## <a name="install-new-packages"></a>Instalace nové balíčky
Pokud chcete použít balíček, který není ve výchozím nastavení nainstalovaná, musíte nejprve nainstalovat do prostředí, které používá Data přípravy. Tato instalace je třeba provést na místním počítači a na všechny výpočty cíle, které chcete spustit na.

K instalaci vaší balíčků výpočetní cíli, budete muset upravit conda_dependencies.yml soubor umístěný ve složce aml_config v kořenovém adresáři projektu.

### <a name="windows"></a>Windows 
A vyhledejte umístění v systému Windows, najít konkrétní aplikaci instalaci Pythonu a jeho adresáře skripty. Výchozí umístění je:  

`C:\Users\<user>\AppData\Local\AmlWorkbench\Python\Scripts` 

Spusťte některý z následujících příkazů: 

`conda install <libraryname>` 

nebo 

`pip install <libraryname> `

### <a name="mac"></a>Mac 
A vyhledejte umístění na Macu, najít konkrétní aplikaci instalaci Pythonu a jeho adresáře skripty. Výchozí umístění je: 

`/Users/<user>/Library/Caches/AmlWorkbench/Python/bin` 

Spusťte některý z následujících příkazů: 

`./conda install <libraryname>`

nebo 

`./pip install <libraryname>`

## <a name="use-custom-modules"></a>Použijte vlastní moduly
V toku dat transformace (skript) uveďte následující kód Python

```python
import sys
sys.path.append(*<absolute path to the directory containing UserModule.py>*)

from UserModule import ExtensionFunction1
df = ExtensionFunction1(df)
```

Přidat sloupce (skript), nastavte typ bloku kódu = modul a zadejte následující kód Python

```python 
import sys
sys.path.append(*<absolute path to the directory containing UserModule.py>*)

from UserModule import ExtensionFunction2

def newvalue(row):
    return ExtensionFunction2(row)
```
Pro spuštění různých kontextech (místní, Docker, Spark), přejděte na absolutní cesta na správném místě. Můžete použít "os.getcwd() + relativePath" ho najít.


## <a name="column-data"></a>Datové sloupce 
Sloupec dat je přístupná z řádku pomocí zápisu s tečkou nebo zápis klíč hodnota. Názvy sloupců, které obsahují mezery nebo speciální znaky, není přístupná pomocí zápisu s tečkou. `row` Proměnná by měla být vždy definována v obou režimech rozšíření Python (modulu a výraz). 

Příklady 

```python
    row.ColumnA + row.ColumnB  
    row["ColumnA"] + row["ColumnB"]
```

## <a name="add-column"></a>Přidání sloupců 
### <a name="purpose"></a>Účel
Bod rozšíření přidat sloupec umožňuje zapisovat Python k výpočtu nový sloupec. Kód, který můžete psát má přístup k úplné řádek. Je nutné vrátit novou hodnotu sloupce pro každý řádek. 

### <a name="how-to-use"></a>Způsob použití
Tento bod rozšíření můžete přidat pomocí bloku přidat sloupec (skript). Je k dispozici na nejvyšší úrovni **transformace** nabídky, stejně jako na **sloupec** kontextové nabídky. 

### <a name="syntax"></a>Syntaxe
Výraz

```python
    math.log(row["Score"])
```

Modul 

```python
def newvalue(row):  
     return math.log(row["Score"])
```
 

## <a name="advanced-filter"></a>Rozšířený filtr
### <a name="purpose"></a>Účel 
Bod rozšíření Upřesnit filtr umožňuje psát vlastní filtr. Máte přístup k celý řádek, a váš kód musí vracet hodnotu True (včetně řádek), nebo hodnotu NEPRAVDA (vyloučení řádek). 

### <a name="how-to-use"></a>Způsob použití
Tento bod rozšíření můžete přidat pomocí bloku Advanced Filter (skript). Je k dispozici na nejvyšší úrovni **transformace** nabídky. 

### <a name="syntax"></a>Syntaxe

Výraz

```python
    row["Score"] > 95
```

Modul  

```python
def includerow(row):  
    return row["Score"] > 95
```
 

## <a name="transform-dataflow"></a>Transformace toku dat
### <a name="purpose"></a>Účel 
Bod rozšíření transformace toku dat umožňuje zcela transformace datového toku. Máte přístup k Pandas dataframe, který obsahuje všechny sloupce a řádky, které jste zpracování. Váš kód musí vracet Pandas dataframe s nová data. 

>[!NOTE]
>V Python je všechna data, která mají být načtena do paměti v Pandas dataframe, pokud se používá toto rozšíření. 
>
>V Spark všechna data se shromažďují do jednoho pracovního uzlu. Pokud data je velký, pracovní může mít nedostatek paměti. Pečlivě dodržujte.

### <a name="how-to-use"></a>Způsob použití 
Tento bod rozšíření můžete přidat pomocí bloku toku transformace dat (skript). Je k dispozici na nejvyšší úrovni **transformace** nabídky. 
### <a name="syntax"></a>Syntaxe 

Výraz

```python
    df['index-column'] = range(1, len(df) + 1)  
    df = df.reset_index()
```
 

Modul 

```python
def transform(df):  
    df['index-column'] = range(1, len(df) + 1)  
    df = df.reset_index()  
    return df
```
  

## <a name="transform-partition"></a>Transformace oddílu  
### <a name="purpose"></a>Účel 
Bodem transformace oddílu rozšíření umožňuje transformovat oddíl datového toku. Máte přístup k Pandas dataframe, který obsahuje všechny sloupce a řádky, pro tento oddíl. Váš kód musí vracet Pandas dataframe s nová data. 

>[!NOTE]
>V Python budete muset zvýšit s jedním oddílem nebo více oddílů, v závislosti na velikosti vaše data. V Spark kterými pracujete dataframe, která obsahuje data pro oddíl v dané pracovního uzlu. V obou případech nelze předpokládat, že máte přístup k celé datové sady. 


### <a name="how-to-use"></a>Způsob použití
Tento bod rozšíření můžete přidat pomocí bloku transformace oddílu (skript). Je k dispozici na nejvyšší úrovni **transformace** nabídky. 

### <a name="syntax"></a>Syntaxe 

Výraz 

```python
    df['partition-id'] = index  
    df['index-column'] = range(1, len(df) + 1)  
    df = df.reset_index()
```
 

Modul 

```python
def transform(df, index):
    df['partition-id'] = index
    df['index-column'] = range(1, len(df) + 1)
    df = df.reset_index()
    return df
```


## <a name="datapreperror"></a>DataPrepError  
### <a name="error-values"></a>Chybové hodnoty  
Při přípravě dat existuje koncept chybové hodnoty. 

Je možné setkat chybové hodnoty v vlastní kód Python. Jsou instance třídy Python s názvem `DataPrepError`. Tato třída zabalí výjimka Python a má několik vlastností. Vlastnosti obsahují informace o chybě, která došlo k chybě při zpracování původní hodnotu, stejně jako původní hodnotu. 


### <a name="datapreperror-class-definition"></a>Definice třídy DataPrepError
```python 
class DataPrepError(Exception): 
    def __bool__(self): 
        return False 
``` 
Vytvoření DataPrepError v rámci přípravy Python Data obvykle vypadá takto: 
```python 
DataPrepError({ 
   'message':'Cannot convert to numeric value', 
   'originalValue': value, 
   'exceptionMessage': e.args[0], 
   '__errorCode__':'Microsoft.DPrep.ErrorValues.InvalidNumericType' 
}) 
``` 
#### <a name="how-to-use"></a>Způsob použití 
Je možné při spuštění Python okamžiku rozšíření pro generování DataPrepErrors jako návratové hodnoty pomocí předchozí metody vytvoření. Je mnohem vyšší pravděpodobnost, že jsou DataPrepErrors došlo při zpracování dat na místě rozšíření. V tomto okamžiku vlastní kód Python je potřeba zpracovat DataPrepError jako platný datový typ.

#### <a name="syntax"></a>Syntaxe 
Výraz 
```python 
    if (isinstance(row["Score"], DataPrepError)): 
        row["Score"].originalValue 
    else: 
        row["Score"] 
``` 
```python 
    if (hasattr(row["Score"], "originalValue")): 
        row["Score"].originalValue 
    else: 
        row["Score"] 
``` 
Modul 
```python 
def newvalue(row): 
    if (isinstance(row["Score"], DataPrepError)): 
        return row["Score"].originalValue 
    else: 
        return row["Score"] 
``` 
```python 
def newvalue(row): 
    if (hasattr(row["Score"], "originalValue")): 
        return row["Score"].originalValue 
    else: 
        return row["Score"] 
```  

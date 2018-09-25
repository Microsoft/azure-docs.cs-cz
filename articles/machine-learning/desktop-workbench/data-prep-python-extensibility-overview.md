---
title: Rozšíření Pythonu pomocí služby Azure Machine Learning Data přípravy | Dokumentace Microsoftu
description: Tento dokument obsahuje přehled a podrobné příklady toho, jak použít kód Python k rozšíření funkčnosti přípravu dat
services: machine-learning
author: euangMS
ms.author: euang
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 05/09/2018
ROBOTS: NOINDEX
ms.openlocfilehash: eceeeb30331031c51e5208e301441d17096b4a00
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46972976"
---
# <a name="data-preparations-python-extensions"></a>Rozšíření Pythonu přípravy dat.

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 


Jako způsob vyplnění funkčností mezi integrované funkce Příprava dat aplikace Azure Machine Learning obsahuje rozšíření na více úrovních. V tomto dokumentu jsme popisují rozšiřitelnost prostřednictvím skript Pythonu. 

## <a name="custom-code-steps"></a>Kroky vlastního kódu 
Příprava dat má vlastní takto kde uživatelé můžou zadat kód:

* Přidat sloupec
* Rozšířený filtr
* Transformace toku dat
* Transformace oddílu

## <a name="code-block-types"></a>Typy bloků kódu 
Pro každý z těchto kroků podporuje dva typy bloků kódu. Nejprve podporujeme úplné výraz Pythonu, který je proveden, protože je. Za druhé podporuje modul Pythonu, kde voláme určitou funkci známé podpisem v kódu, které zadáte.

Například můžete přidat nový sloupec, který vypočítá protokolu jiného sloupce v následujících dvou způsobů:

Výraz 

```python    
    math.log(row["Score"])
```

Modul 
    
```python
def newvalue(row): 
        return math.log(row["Score"])
```


Přidat sloupec transformace v režimu modulu očekává, že se najít funkci s názvem `newvalue` , který přijímá proměnnou řádků a vrátí hodnotu pro sloupec. Tento modul může obsahovat libovolné množství kódu v Pythonu s další funkce, importy, atd.

Podrobnosti o jednotlivých rozšiřovacího bodu jsou popsány v následujících částech. 

## <a name="imports"></a>Importy 
Pokud používáte typ bloku výrazu, můžete přesto přidat **importovat** příkazy do vašeho kódu. Všichni musí být seskupeny na horní řádky kódu.

Opravit 

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
 
 
Pokud používáte modul typ bloku, můžete postupovat podle všech běžných pravidel Python pro použití **importovat** příkazu. 

## <a name="default-imports"></a>Výchozí importy
Následující importy jsou vždy součástí a použitelné ve vašem kódu. Není nutné je znovu importovat. 

```python
import math  
import numbers  
import datetime  
import re  
import pandas as pd  
import numpy as np  
import scipy as sp
```
  

## <a name="install-new-packages"></a>Nainstalovat nové balíčky
Použít balíček, který není nainstalovaný ve výchozím nastavení, musíte nejprve nainstalovat do prostředí, která používá Data přípravy. Tato instalace je potřeba udělat v místním počítači a na všech cílových výpočetních prostředí, které chcete spouštět.

Pokud chcete nainstalovat balíčky v cílové výpočetní prostředí, je třeba upravit conda_dependencies.yml soubor umístěný ve složce Nástroje aml_config v kořenovém adresáři vašeho projektu.

### <a name="windows"></a>Windows 
K vyhledání umístění na Windows, najdete instalaci konkrétní aplikace Python a jeho adresář skripty. Výchozí umístění je:  

`C:\Users\<user>\AppData\Local\AmlWorkbench\Python\Scripts` 

Spusťte některý z následujících příkazů: 

`conda install <libraryname>` 

nebo 

`pip install <libraryname> `

### <a name="mac"></a>Mac 
K vyhledání umístění na počítači Mac, najdete instalaci konkrétní aplikace Python a jeho adresář skripty. Výchozí umístění je: 

`/Users/<user>/Library/Caches/AmlWorkbench/Python/bin` 

Spusťte některý z následujících příkazů: 

`./conda install <libraryname>`

nebo 

`./pip install <libraryname>`

## <a name="use-custom-modules"></a>Použití vlastních modulů
Napište následující kód Pythonu v Transform Dataflow (Script)

```python
import sys
sys.path.append(*<absolute path to the directory containing UserModule.py>*)

from UserModule import ExtensionFunction1
df = ExtensionFunction1(df)
```

Přidat sloupec (skript), nastavte typ bloku kódu = modul a zadejte následující kód Pythonu

```python 
import sys
sys.path.append(*<absolute path to the directory containing UserModule.py>*)

from UserModule import ExtensionFunction2

def newvalue(row):
    return ExtensionFunction2(row)
```
Pro spuštění různých kontextech (místní, Docker, Spark), přejděte na absolutní cesta na správném místě. Můžete chtít použít "os.getcwd() relativePath plus (+) a vyhledejte jej.


## <a name="column-data"></a>Sloupec data 
Sloupec data přístupná z řádku pomocí zápisu s tečkou nebo zápis klíč hodnota. Názvy sloupců, které obsahují mezery ani speciální znaky nelze přistupovat pomocí zápisu s tečkou. `row` Byste vždy měli definovat proměnnou v obou režimech rozšíření Pythonu (modulu a výraz). 

Příklady 

```python
    row.ColumnA + row.ColumnB  
    row["ColumnA"] + row["ColumnB"]
```

## <a name="add-column"></a>Přidat sloupec 
### <a name="purpose"></a>Účel
Přidat sloupec rozšiřovací bod umožňuje zapsat Python k výpočtu nový sloupec. Kód, který napíšete má přístup k celý řádek. Je potřeba vrátit hodnotu nový sloupec pro každý řádek. 

### <a name="how-to-use"></a>Způsob použití
Tento rozšiřovací bod můžete přidat pomocí bloku Add Column (Script). Je k dispozici na na nejvyšší úrovni **transformace** nabídky, stejně jako na **sloupec** kontextové nabídky. 

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
Rozšířený filtr rozšiřovací bod umožňuje psát vlastní filtr. Máte přístup k celým řádkem, a musí kód vrátit hodnotu True (včetně řádek), nebo hodnotu NEPRAVDA (vyloučit řádek). 

### <a name="how-to-use"></a>Způsob použití
Tento rozšiřovací bod můžete přidat pomocí bloku Rozšířený filtr (skript). Je k dispozici na na nejvyšší úrovni **transformace** nabídky. 

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
Transformace toku dat rozšiřovacího bodu vám umožňuje zcela transformovat tok dat. Máte přístup k Pandas dataframe, který obsahuje všechny sloupce a řádky, které jste zpracování. Váš kód musí vracet Pandas dataframe s novými daty. 

>[!NOTE]
>V jazyce Python je všechna data mají být načtena do paměti v Pandas dataframe, pokud se používá toto rozšíření. 
>
>Ve Sparku všechna data jsou shromažďována do jednoho pracovního uzlu. Pokud jsou data velmi velké, pracovník může dojít volné paměti. Používejte opatrně.

### <a name="how-to-use"></a>Způsob použití 
Tento rozšiřovací bod můžete přidat pomocí bloku Transform Dataflow (Script). Je k dispozici na na nejvyšší úrovni **transformace** nabídky. 
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
Rozšiřovací bod transformace oddíl umožňuje transformovat oddílu datového toku. Máte přístup k Pandas dataframe, který obsahuje všechny sloupce a řádky pro tento oddíl. Váš kód musí vracet Pandas dataframe s novými daty. 

>[!NOTE]
>V jazyce Python může se skončit s jedním oddílem nebo více oddílů, v závislosti na velikosti dat. Ve Sparku kterou pracujete datový rámec, který obsahuje data pro oddíl na daný pracovního uzlu. V obou případech nelze předpokládat, že máte přístup k celé datové sady. 


### <a name="how-to-use"></a>Způsob použití
Tento rozšiřovací bod můžete přidat pomocí bloku transformace oddílu (skript). Je k dispozici na na nejvyšší úrovni **transformace** nabídky. 

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
### <a name="error-values"></a>Chybových hodnot  
Příprava dat existuje koncept chybových hodnot. 

Je možné se setkat se chybové hodnoty ve vlastním kódu Pythonu. Jsou instancemi třídy Python s názvem `DataPrepError`. Tato třída zabalí výjimka Python a má několik vlastností. Všechny vlastnosti obsahují informace o chybě, ke které došlo při zpracování na původní hodnotu, stejně jako na původní hodnotu. 


### <a name="datapreperror-class-definition"></a>Definice třídy DataPrepError
```python 
class DataPrepError(Exception): 
    def __bool__(self): 
        return False 
``` 
Vytvoření DataPrepError v rámci Python přípravy dat obecně vypadá takto: 
```python 
DataPrepError({ 
   'message':'Cannot convert to numeric value', 
   'originalValue': value, 
   'exceptionMessage': e.args[0], 
   '__errorCode__':'Microsoft.DPrep.ErrorValues.InvalidNumericType' 
}) 
``` 
#### <a name="how-to-use"></a>Způsob použití 
Je možné při spuštění Pythonu na rozšiřovacím bodem ke generování DataPrepErrors jako návratové hodnoty s použitím předchozí způsob vytvoření. Je mnohem pravděpodobnější, že jsou DataPrepErrors došlo při zpracování dat v rozšiřovacím bodem. V tuto chvíli musí zpracovat DataPrepError jako platný datový typ. vlastního kódu Python.

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

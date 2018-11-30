---
title: Příprava dat pomocí Machine Learning Data Prep SDK pro Python – Azure
description: Další informace o načtení dat z různých formátů, aby byla lépe použitelná transformace a zápisu dat do umístění pro vlastní modely a přístup k použití sady Azure Machine Learning Data Prep SDK pro Python.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: cforbe
author: cforbe
manager: cgronlun
ms.reviewer: jmartens
ms.date: 11/27/2018
ms.openlocfilehash: 91d0f3565db484504a67a3b6ae0989b9291cd24f
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52446419"
---
# <a name="prepare-data-for-modeling-with-azure-machine-learning"></a>Příprava dat pro modelování s Azure Machine Learning

V tomto článku se dozvíte o případy použití a jedinečné funkce sady SDK pro Azure Machine Learning Data Prep. Příprava dat je nejdůležitější část služby machine learning pracovního postupu. Data z reálného světa často nefunguje, nekonzistentní nebo nejde použít jako trénovacích dat bez významného čištění a transformace. Oprava chyby a anomálie v nezpracovaných dat a vytváření nových funkcí, které jsou relevantní pro problém, který se snažíte vyřešit, zvýší přesnost modelu. Sada SDK byla navržena jako zkušenosti uživatelů z dalších běžných dat přípravu knihoven, při nabídky výhod pro klíčové scénáře a udržování vzájemná funkční spolupráce s těmito knihovnami.

Můžete připravit vaše data v Pythonu pomocí [sady SDK služby Azure Machine Learning Data Prep](https://aka.ms/data-prep-sdk).

## <a name="azure-machine-learning-data-prep-sdk"></a>Azure Machine Learning přípravy dat sady SDK

[Sady SDK služby Azure Machine Learning Data Prep](https://aka.ms/data-prep-sdk) je knihovna Python, která nabízí:

* Inteligentní čas ukládání transformace, jako je přibližných shod seskupení, odvozené sloupec podle příkladu, automaticky rozdělení, inteligentní číst soubor a doprava – nerovné zpracování schématu.
* Jedno rozhraní API, která funguje na malé data místně nebo velkých objemů dat v cloudu, s **několik bez jediné změny kódu**.
* Možnost více efektivně škálovat na jednom počítači prostřednictvím datových proudů přístup ke zpracování dat, nikoli načtení do paměti.

### <a name="install-the-sdk"></a>Instalace sady SDK

Instalace sady SDK v prostředí Python pomocí následujícího příkazu.

```shell
pip install azureml-dataprep
```

Použijte následující kód k importu balíčku.

```python
import azureml.dataprep as dprep
```

### <a name="examples-and-reference"></a>Příklady a referenční informace

Další informace o modulech a funkce této sady SDK najdete v tématu [referenční dokumentace sady SDK pro Data Prep](https://aka.ms/data-prep-sdk).

Následující příklady zvýrazněte některé jedinečné funkce sady SDK, včetně:

* Automatické rozpoznání typu
* Inteligentní transformace
* Souhrnné statistiky
* Funkce víc prostředí


#### <a name="automatic-file-type-detection"></a>Automatické rozpoznání typu

Použití `smart_read_file()` funkce načtěte svá data bez nutnosti zadávat typ souboru. Tato funkce automaticky rozpozná a analyzuje typ souboru.

```python
dataflow = dprep.smart_read_file(path="<your-file-path>")
```

#### <a name="intelligent-transforms"></a>Inteligentní transformace

Použití sady SDK k rozdělení a odvození sloupce podle příkladu a odvození k automatizaci vytváření funkcí. Předpokládá, že jste pole v toku dat objektu volat `datetime` s hodnotou `2018-09-15 14:30:00`.

Chcete-li automaticky rozdělit `datetime` pole, zavolejte následující funkce.

```python
new_dataflow = dataflow.split_column_by_example(source_column="datetime")
```

Definováním není parametr příkladu, bude automaticky rozdělí funkci `datetime` pole do dvou nových polí `datetime_1` a `datetime_2`. Výsledné hodnoty jsou `2018-09-15` a `14:30:00`v uvedeném pořadí. Je také možné zadat vzor příkladu a sady SDK bude předpovídat a provedení zamýšlené transformace. Pomocí stejných `datetime` objektu, následující kód vytvoří nový sloupec `datetime_weekday` pro den v týdnu, na základě zadané příkladu.

```python
new_dataflow = dataflow.derive_column_by_example(
        source_columns="datetime", 
        new_column_name="datetime_weekday", 
        example_data=[("2009-01-04 10:12:00", "Sunday"), ("2013-08-22 17:00:00", "Thursday")]
    )
```

#### <a name="summary-statistics"></a>Souhrnné statistiky

Můžete vygenerovat rychlé souhrnné statistiky pro tok dat s jeden řádek kódu. Tato metoda poskytuje pohodlný způsob, jak porozumět datům a jak se distribuuje.

```python
dataflow.get_profile()
```

Výsledkem volání této funkce na objekt datového toku bude výstup jako v následující tabulce.

![Souhrnná statistika výstupu](./media/concept-data-preparation/output-example.png)

## <a name="multiple-environment-compatibilities"></a>Více prostředí kompatibility

Tato sada SDK nabízí také pro objekty toku dat být serializován a otevřít v *jakékoli* prostředí Pythonu. Prostředí, ve kterém je otevřen může být jiné než prostředí, kde je uložen. Tato funkce umožňuje snadný přenos mezi prostředí Pythonu a snadné integraci s modely Azure Machine Learning.

Chcete-li uložit tok dat objektů použijte následující kód.

```python
package = dprep.Package([dataflow_1, dataflow_2])
package.save("<your-local-path>")
```

Načtení seznamu objektů toku dat a znovu otevřít váš balíček v jakémkoli prostředí použijte následující kód.

```python
package = dprep.Package.open("<your-local-path>")
dataflow_list = package.dataflows
```

## <a name="data-preparation-pipeline"></a>Kanál pro přípravu dat

Pokud chcete zobrazit podrobné příklady a kód pro jednotlivé kroky přípravy, použijte následující návody:

1. [Načtení dat](how-to-load-data.md), což může být v různých formátech
2. [Transformace](how-to-transform-data.md) ho do použitelnější struktury
3. [Zápis](how-to-write-data.md) tato data do umístění dostupného vašich modelů

![Proces přípravy dat.](./media/concept-data-preparation/data-prep-process.png)

## <a name="next-steps"></a>Další postup

Kontrola [Poznámkový blok příklad](https://github.com/Microsoft/AMLDataPrepDocs/tree/master/tutorials/getting-started/getting-started.ipynb) přípravy dat pomocí sady SDK pro Azure Machine Learning Data Prep.

Azure Machine Learning Data Prep SDK [referenční dokumentaci](https://docs.microsoft.com/python/api/overview/azure/dataprep/intro?view=azure-dataprep-py).

---
title: Spouštění skriptů Pythonu machine learning
titleSuffix: Azure Machine Learning Studio
description: Zjistěte, jak pomocí Pythonu v Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/12/2019
ms.openlocfilehash: 4b4f3877b56752756050de0af226571ac2a93293
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60750604"
---
# <a name="execute-python-machine-learning-scripts-in-azure-machine-learning-studio"></a>Spouštění skriptů strojového učení v Pythonu v nástroji Azure Machine Learning Studio

Python je cenným nástrojem v hrudníku nástroj řadu odborníků přes data. Používá se v každé fázi typické machine learning pracovních postupech, včetně zkoumání dat, funkce extrakce, trénování modelu a ověření a nasazení.

Tento článek popisuje, jak můžete použít modul Execute Python Script při použití kódu Python v experimenty Azure Machine Learning Studio a webové služby.

## <a name="using-the-execute-python-script-module"></a>Pomocí modulu Execute Python Script

Primární rozhraní pro Python v sadě Studio je prostřednictvím [Execute Python Script] [ execute-python-script] modulu. Přijímá až tři vstupů a vytvoří až dva výstupy, podobně jako [spustit skript jazyka R] [ execute-r-script] modulu. Kód v Pythonu se zadá do pole parametru pomocí speciálně s názvem vstupního bodu funkce volána `azureml_main`.

![Spusťte modul skriptu Pythonu](./media/execute-python-scripts/execute-machine-learning-python-scripts-module.png)

![Ukázkový kód pythonu v modulu parametr pole](./media/execute-python-scripts/embedded-machine-learning-python-script.png)

### <a name="input-parameters"></a>Vstupní parametry

Vstupy pro modul Python jsou vystaveny jako Pandas datových rámců. `azureml_main` Funkce přijímá maximálně dva volitelné DataFrames Pandas jako parametry.

Mapování mezi vstupní porty a parametry funkce je poziční:

- První připojených vstupního portu je namapována na první parametr funkce.
- Druhého vstupu (Pokud je připojení) se mapuje na druhý parametr funkce.
- Se používá k třetím vstupem [importovat další moduly Pythonu](#import-modules).

Podrobnější sémantika jak vstupních portů se namapují na parametry `azureml_main` níže jsou uvedené funkce.

![Konfigurace vstupních portů a výsledný podpis Pythonu](./media/execute-python-scripts/python-script-inputs-mapped-to-parameters.png)

### <a name="output-return-values"></a>Výstup návratové hodnoty

`azureml_main` Funkce musí vracet jeden Pandas DataFrame zabalen do Python [pořadí](https://docs.python.org/2/c-api/sequence.html) jako řazené kolekce členů, seznam nebo pole NumPy. První prvek Tato sekvence vrátí první výstupní port modulu. Druhý výstupní port modulu se používá pro [vizualizace](#visualizations) a nevyžaduje návratovou hodnotu. Toto schéma je uveden níže.

![Mapování vstupní porty pro parametry a vracet hodnotu na výstupní port](./media/execute-python-scripts/map-of-python-script-inputs-outputs.png)

## <a name="translation-of-input-and-output-data-types"></a>Překlad vstupních a výstupních datových typů

Datové sady Studio nejsou stejná jako Panda datových rámců. V důsledku toho se převedou na Pandas DataFrame vstupní datové sady v nástroji Studio a výstupních datových rámců převedou zpět do datové sady Studio. Během tohoto procesu převodu se také provést následující převody:

 **Python datový typ** | **Proces převodu Studio** |
| --- | --- |
| Řetězce a numerické hodnoty| Přeložit, protože je |
| Pandas 'NA' | Přeložen jako "chybí hodnota. |
| Index vektorů | Nepodporovaná * |
| Názvy sloupců non-string | Volání `str` na názvy sloupců |
| Duplicitní názvy sloupců | Přidáte číselnou příponou: (1), (2), (3), a tak dále.

**Všechny snímky vstupní data v Pythonu – funkce mají vždy 64-bit číselný index od 0 do počtu řádků odečte 1.*

## <a id="import-modules"></a>Import existující moduly skriptu Pythonu

Back-endu ke spuštění Pythonu je založen na [Anaconda](https://store.continuum.io/cshop/anaconda/), vědecké distribuci jazyka Python nejběžněji používané. Obsahuje blízko 200 nejběžnější balíčky Pythonu používaných pro datově orientovaných úlohy. Studio aktuálně nepodporuje použití systémy správy balíčků, jako je Pip nebo Conda, instalaci a správě externí knihovny.  Pokud zjistíte potřeba začlenit další knihovny, použijte jako vodítko následující scénář.

Běžným případem použití je začlenit stávající skripty Python do Studio experimentů. [Execute Python Script] [ execute-python-script] modul přijímá souboru zip, který obsahuje moduly Pythonu na třetí vstupní port. Soubor je odblokujte rozhraním spuštění za běhu a obsah se přidají do knihovny cesta k interpretu Pythonu. `azureml_main` Vstupní bod funkce pak můžete importovat tyto moduly přímo. 

Jako příklad vezměte v úvahu souboru Hello.py obsahující jednoduchou funkci "Hello, World".

![Uživatelem definované funkce v souboru Hello.py](./media/execute-python-scripts/figure4.png)

V dalším kroku vytvoříme soubor Hello.zip obsahující Hello.py:

![Soubor ZIP, který obsahuje uživatelský kód v Pythonu](./media/execute-python-scripts/figure5.png)

Nahrání souboru zip jako datovou sadu do sady Studio. Pak vytvoření a spuštění experimentu, který používá kódu Python v souboru Hello.zip připojením na třetí vstupní port **Execute Python Script** modulu, jak je znázorněno na následujícím obrázku.

![Ukázkový experiment s Hello.zip jako vstup do modulu Execute Python Script](./media/execute-python-scripts/figure6a.png)

![Uživatelský kód v Pythonu nahrání souboru zip](./media/execute-python-scripts/figure6b.png)

Modul výstup ukazuje, že soubor zip nezabalené a které funkce `print_hello` byl spuštěn.

![Výstup modulu zobrazující uživatelem definované funkce](./media/execute-python-scripts/figure7.png)

## <a name="accessing-azure-storage-blobs"></a>Přístup k objektům BLOB Azure Storage

Můžete přístup k datům uloženým v účtu služby Azure Blob Storage pomocí těchto kroků:

1. Stáhněte si [balíček Azure Blob Storage pro Python](https://azuremlpackagesupport.blob.core.windows.net/python/azure.zip) místně.
1. Nahrání souboru zip do pracovního sadě Studio prostoru jako datová sada.
1. Vytvoření objektu BlobService s `protocol='http'`

```
from azure.storage.blob import BlockBlobService

# Create the BlockBlockService that is used to call the Blob service for the storage account
block_blob_service = BlockBlobService(account_name='account_name', account_key='account_key', protocol='http')
```

1. Zakázat **vyžadovat zabezpečený přenos** ve službě Storage **konfigurace** na kartě nastavení

![Zakázání zabezpečeného přenosu na webu Azure Portal](./media/execute-python-scripts/disable-secure-transfer-required.png)

## <a name="operationalizing-python-scripts"></a>Až po zprovoznění skriptů Pythonu

Žádné [Execute Python Script] [ execute-python-script] moduly používané v hodnoticí experimentu jsou volány při publikovat jako webovou službu. Například následující obrázek ukazuje bodovací experiment, který obsahuje kód pro vyhodnocení jeden výraz Pythonu.

![Pracovní prostor Studio pro webovou službu](./media/execute-python-scripts/figure3a.png)

![Výraz Pandas v Pythonu](./media/execute-python-scripts/python-script-with-python-pandas.png)

Webové služby vytvořené z tohoto experimentu by provádět následující akce:

1. Využijte výraz Pythonu jako vstupní (jako řetězec)
1. Odeslat výraz Pythonu k interpretu Pythonu
1. Vrátí tabulku obsahující výraz a vyhodnocený výsledek.

## <a id="visualizations"></a>Práce s vizualizacemi

Může být vrácen grafy vytvořené pomocí MatplotLib [Execute Python Script][execute-python-script]. Ale vykreslení přesměrování automaticky do bitové kopie jsou při použití jazyka R. Proto uživatel musí explicitně uložit všechny grafy na soubory PNG.

Ke generování bitových kopií z MatplotLib, je nutné provést následující kroky:

1. Přepněte na back-end "%{AGG/" od rendereru na základě Qt výchozí.
1. Vytvořte nový objekt obrázek.
1. Získejte na ose a generovat všechna vykreslení do něj.
1. Uložte obrázek PNG souboru.

Tento proces je znázorněno na následujících obrázcích, které vytvářejí bodový diagram matice pomocí funkce scatter_matrix v Pandas.

![Kód pro uložení hodnoty MatplotLib bitové kopie](./media/execute-python-scripts/figure-v1-8.png)

![Klikněte na vizualizovat na modul Execute Python Script zobrazíte na obrázcích](./media/execute-python-scripts/figure-v2-9a.png)

![Pro ukázkový experiment pomocí kódu Python grafy vizualizace](./media/execute-python-scripts/figure-v2-9b.png)

Je možné vrátit více obrázky se ukládají do různých imagí. Modul runtime Studio vybere všechny bitové kopie a zřetězí za účelem vizualizace.

## <a name="advanced-examples"></a>Pokročilé příklady

Anaconda prostředí nainstalovaná v sadě Studio obsahuje běžné balíčky, jako je například NumPy, SciPy a Učte se Scikits. Tyto balíčky můžete efektivně použít při zpracování dat ve službě machine learning kanálu.

Například následující experiment a skript ilustrují použití skupiny stromů studentů v Scikits další vypočítat skóre význam funkce pro datovou sadu. Skóre je možné provést výběr pod dohledem funkcí než jsou předány do jiného modelu.

Tady je funkce Python slouží k výpočtu skóre význam a pořadí funkcí podle skóre:

![Funkce pořadí funkcí podle skóre](./media/execute-python-scripts/figure8.png)

Následujícího experimentu pak vypočítá a vrátí skóre význam funkcí v datové sadě "Pima území v Indickém diabetem" v nástroji Azure Machine Learning Studio:

![Experimentujte pořadí funkcí v datové sadě Pima území v Indickém diabetem pomocí Pythonu](./media/execute-python-scripts/figure9a.png)

![Vizualizace výstupu modulu Execute Python Script](./media/execute-python-scripts/figure9b.png)

## <a name="limitations"></a>Omezení

[Execute Python Script] [ execute-python-script] modulu aktuálně má následující omezení:

### <a name="sandboxed-execution"></a>Spuštění v izolovaném prostoru

Modulu runtime Pythonu je aktuálně v izolovaném prostoru a neumožňuje přístup k síti nebo místního systému souborů trvalé způsobem. Všechny soubory, které jsou uloženy v místním počítači jsou izolované a odstranit po dokončení modulu. Kód Pythonu, nemá přístup k většině adresáře na počítači, na kterém poběží, s výjimkou v aktuálním adresáři a jeho podadresářích.

### <a name="lack-of-sophisticated-development-and-debugging-support"></a>Nedostatek sofistikované vývojářské a podpora ladění

Modul Pythonu v současné době nepodporuje funkce integrovaného vývojového prostředí, jako je například technologie intellisense a ladění. Také pokud modul selže v době běhu, úplné trasování zásobníku Python je k dispozici. Ale je třeba zobrazit ve výstupu protokolu pro modul. Doporučujeme nyní vývoj a ladění skriptů v Pythonu v prostředí, jako je například IPython a poté ji naimportujte kód do modulu.

### <a name="single-data-frame-output"></a>Single – datový rámec výstupu

Vstupní bod Python je povolená jenom k vrácení jednoho datového rámce jako výstup. Není aktuálně možné vrátit zpět do modulu runtime Studio libovolného Python objekty, jako jsou trénované modely přímo. Stejně jako [spustit skript jazyka R][execute-r-script], který má stejné omezení, je možné v mnoha případech pickle objekty do bajtového pole a vrátí, který v rámci datového rámce.

### <a name="inability-to-customize-python-installation"></a>Nemožnost k přizpůsobení instalace Pythonu

V současné době je jediný způsob, jak přidat vlastní moduly Pythonu prostřednictvím mechanismu souboru zip je popsáno výše. Když je vhodná pro malé moduly, je náročné pro velké moduly (zejména moduly pomocí nativních knihoven DLL) nebo velký počet modulů.

## <a name="next-steps"></a>Další postup

Další informace naleznete ve [Středisku pro vývojáře Python](https://azure.microsoft.com/develop/python/).

<!-- Module References -->
[execute-python-script]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-python-script
[execute-r-script]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-r-script
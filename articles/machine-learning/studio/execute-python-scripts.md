---
title: Spouštění skriptů Pythonu
titleSuffix: ML Studio (classic) - Azure
description: Přečtěte si, jak pomocí modulu Execute Python Script používat kód Pythonu v experimentech machine learningového studia (klasické) experimenty a webové služby.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/12/2019
ms.openlocfilehash: c79f6bd63fa5d8d8c6b22ff271d8ca513a94fd64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218088"
---
# <a name="execute-python-machine-learning-scripts-in-azure-machine-learning-studio-classic"></a>Spuštění skriptů pro strojové učení v Pythonu v Azure Machine Learning Studio (klasické)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Python je cenným nástrojem v truhle nástrojů mnoha datových vědců. Používá se v každé fázi typických pracovních postupů strojového učení, včetně zkoumání dat, extrakce funkcí, školení a ověřování modelů a nasazení.

Tento článek popisuje, jak můžete použít modul Spouštět Python Script k použití kódu Pythonu ve vašem Azure Machine Learning Studio (klasické) experimenty a webové služby.

## <a name="using-the-execute-python-script-module"></a>Použití modulu Spustit skript Pythonu

Primární rozhraní pythonu ve studiu (klasické) je přes modul [Spustit pythonskript.][execute-python-script] Přijímá až tři vstupy a vytváří až dva výstupy, podobně jako [modul Execute R Script.][execute-r-script] Kód Pythonu je zadán do pole parametrů prostřednictvím `azureml_main`speciálně pojmenované funkce vstupního bodu s názvem .

![Spuštění modulu Skript Pythonu](./media/execute-python-scripts/execute-machine-learning-python-scripts-module.png)

![Ukázkový kód pythonu v poli parametrů modulu](./media/execute-python-scripts/embedded-machine-learning-python-script.png)

### <a name="input-parameters"></a>Vstupní parametry

Vstupy do modulu Python jsou vystaveny jako Pandas DataFrames. Funkce `azureml_main` přijímá jako parametry až dva volitelné pandas DataFrames.

Mapování mezi vstupními porty a parametry funkce je poziční:

- První připojený vstupní port je mapován na první parametr funkce.
- Druhý vstup (je-li připojen) je mapován na druhý parametr funkce.
- Třetí vstup se používá k [importu dalších modulů Pythonu](#import-modules).

Podrobnější sémantiku, jak se vstupní porty namapují na parametry `azureml_main` funkce, jsou uvedeny níže.

![Tabulka konfigurací vstupního portu a výsledný podpis Pythonu](./media/execute-python-scripts/python-script-inputs-mapped-to-parameters.png)

### <a name="output-return-values"></a>Výstupní vrácené hodnoty

Funkce `azureml_main` musí vrátit jeden Pandas DataFrame zabalené v [pythonu sekvence,](https://docs.python.org/2/c-api/sequence.html) jako je například n-tice, seznam nebo NumPy pole. První prvek této sekvence je vrácen a první výstupní port modulu. Druhý výstupní port modulu se používá pro [vizualizace](#visualizations) a nevyžaduje vrácenou hodnotu. Toto schéma je uvedeno níže.

![Mapování vstupních portů na parametry a vrácená hodnota na výstupní port](./media/execute-python-scripts/map-of-python-script-inputs-outputs.png)

## <a name="translation-of-input-and-output-data-types"></a>Překlad vstupních a výstupních datových typů

Datové sady studio nejsou stejné jako Panda DataFrames. Výsledkem je, že vstupní datové sady ve studiu (klasické) jsou převedeny na Pandas DataFrame a výstupní datarámce jsou převedeny zpět na datové sady Studio (klasické). Během tohoto procesu převodu jsou také provedeny následující překlady:

 **Datový typ Pythonu** | **Postup překladu studia** |
| --- | --- |
| Řetězce a číselné číslo| Přeloženo tak, jak je |
| Pandy 'NA' | Přeloženo jako Chybějící hodnota. |
| Vektory indexu | Bez podpory* |
| Názvy sloupců bez řetězce | Volání `str` na názvy sloupců |
| Duplicitní názvy sloupců | Přidejte číselnou příponu: (1), (2), (3) a tak dále.

**Všechny rámce vstupních dat ve funkci Pythonu mají vždy 64bitový číselný index od 0 do počtu řádků mínus 1.*

## <a name="importing-existing-python-script-modules"></a><a id="import-modules"></a>Import existujících modulů skriptů Pythonu

Back-end používaný ke spuštění Pythonu je založen na [Anakondě](https://www.anaconda.com/distribution/), široce používané vědecké distribuci Pythonu. Dodává se s téměř 200 nejběžnějšími balíčky Pythonu používanými v úlohách zaměřených na data. Studio (klasické) v současné době nepodporuje použití systémů pro správu balíčků, jako je Pip nebo Conda, k instalaci a správě externích knihoven.  Pokud zjistíte, že je třeba začlenit další knihovny, použijte následující scénář jako vodítko.

Běžným případem použití je začlenění existujících skriptů Pythonu do experimentů Studia (klasické). Modul [Execute Python Script][execute-python-script] přijímá soubor zip obsahující moduly Pythonu na třetím vstupním portu. Soubor je rozbalený prováděcím rámcem za běhu a obsah je přidán do cesty knihovny interpretu Pythonu. Funkce `azureml_main` vstupního bodu pak může importovat tyto moduly přímo. 

Jako příklad zvažte Hello.py soubor obsahující jednoduchou funkci "Hello, World".

![Uživatelem definovaná funkce v Hello.py souboru](./media/execute-python-scripts/figure4.png)

Dále vytvoříme soubor Hello.zip, který obsahuje Hello.py:

![SOUBOR ZIP obsahující uživatelem definovaný kód Pythonu](./media/execute-python-scripts/figure5.png)

Nahrajte soubor zip jako datovou sadu do studia (klasické). Potom vytvořte a spusťte experiment, který používá kód Pythonu v souboru Hello.zip tak, že jej připojíte ke třetímu vstupnímu portu modulu **Execute Python Script,** jak je znázorněno na následujícím obrázku.

![Ukázkový experiment s Hello.zip jako vstupdo modulu Spustit skript Pythonu](./media/execute-python-scripts/figure6a.png)

![Uživatelem definovaný kód Pythonu nahraný jako soubor zip](./media/execute-python-scripts/figure6b.png)

Výstup modulu ukazuje, že soubor zip byl nezabalen a že funkce `print_hello` byla spuštěna.

![Výstup modulu znázorňující uživatelem definovanou funkci](./media/execute-python-scripts/figure7.png)

## <a name="accessing-azure-storage-blobs"></a>Přístup k objektům BLOB úložiště Azure

K datům uloženým v účtu úložiště objektů blob Azure můžete přistupovat pomocí těchto kroků:

1. Stáhněte si [balíček azure blob storage pro Python](https://azuremlpackagesupport.blob.core.windows.net/python/azure.zip) místně.
1. Nahrajte soubor zip do pracovního prostoru studia (klasické) jako datovou sadu.
1. Vytvoření objektu BlobService pomocí`protocol='http'`

```
from azure.storage.blob import BlockBlobService

# Create the BlockBlockService that is used to call the Blob service for the storage account
block_blob_service = BlockBlobService(account_name='account_name', account_key='account_key', protocol='http')
```

1. Zakázání **zabezpečeného přenosu vyžadovaného** na kartě **Nastavení konfigurace** úložiště

![Zakázat zabezpečení přenosu požadované na webu Azure Portal](./media/execute-python-scripts/disable-secure-transfer-required.png)

## <a name="operationalizing-python-scripts"></a>Zprovoznění skriptů Pythonu

Všechny [moduly Spouštění skriptů Pythonu][execute-python-script] použité v experimentu hodnocení se nazývají při publikování jako webová služba. Například na obrázku níže ukazuje bodování experiment, který obsahuje kód k vyhodnocení jednoho výrazu Pythonu.

![Studiopracovní prostor pro webovou službu](./media/execute-python-scripts/figure3a.png)

![Výraz Pandpythona pythonu](./media/execute-python-scripts/python-script-with-python-pandas.png)

Webová služba vytvořená z tohoto experimentu by přijala následující akce:

1. Vzít výraz Pythonu jako vstup (jako řetězec)
1. Odeslání výrazu Pythonu překladaci Pythonu
1. Vrátí tabulku obsahující výraz i vyhodnocený výsledek.

## <a name="working-with-visualizations"></a><a id="visualizations"></a>Práce s vizualizacemi

Parcely vytvořené pomocí MatplotLib mohou být vráceny [skriptem Execute Python][execute-python-script]. Obrázky však nejsou automaticky přesměrovány na obrázky tak, jak jsou při použití R. Takže uživatel musí explicitně uložit všechny obrázky do souborů PNG.

Chcete-li generovat obrázky z MatplotLib, musíte provést následující kroky:

1. Přepněte back-end na "AGG" z výchozího vykreslovače založeného na Qt.
1. Vytvořte nový objekt obrázku.
1. Získejte osu a vygenerujte do ní všechny obrázky.
1. Uložte obrázek do souboru PNG.

Tento proces je znázorněn na následujících obrázcích, které vytvářejí matici bodového grafu pomocí funkce scatter_matrix v pandách.

![Kód pro uložení obrázků MatplotLib do obrázků](./media/execute-python-scripts/figure-v1-8.png)

![Kliknutím na vizualizovat v modulu Spustit skript Pythonu zobrazíte čísla.](./media/execute-python-scripts/figure-v2-9a.png)

![Vizualizace parcel pro ukázkový experiment pomocí kódu Pythonu](./media/execute-python-scripts/figure-v2-9b.png)

Je možné vrátit více číslic jejich uložením do různých obrázků. Studio (klasické) runtime vyzvedne všechny obrázky a zřetězí je pro vizualizaci.

## <a name="advanced-examples"></a>Pokročilé příklady

Prostředí Anaconda nainstalované ve studiu (klasické) obsahuje běžné balíčky jako NumPy, SciPy a Scikits-Learn. Tyto balíčky lze efektivně použít pro zpracování dat v kanálu strojového učení.

Například následující experiment a skript ilustrují použití studentů souborů v Scikits-Learn vypočítat skóre důležitosti funkce pro datovou sadu. Skóre lze použít k provedení výběru funkcí pod dohledem před tím, než jsou vloženy do jiného modelu.

Zde je funkce Pythonu, která slouží k výpočtu skóre důležitosti a pořadí funkcí na základě skóre:

![Funkce pro hodnocení funkcí podle skóre](./media/execute-python-scripts/figure8.png)

Následující experiment pak vypočítá a vrátí důležitost skóre funkcí v datové sadě "Pima indian diabetes" v Azure Machine Learning Studio (klasické):

![Experimentovat s hodnocením funkcí v datové sadě Pima Indian Diabetes pomocí Pythonu](./media/execute-python-scripts/figure9a.png)

![Vizualizace výstupu modulu Execute Python Script](./media/execute-python-scripts/figure9b.png)

## <a name="limitations"></a>Omezení

Modul [Spustit skript Pythonu][execute-python-script] má aktuálně následující omezení:

### <a name="sandboxed-execution"></a>Spuštění v izolovaném prostoru

Modul runtime Pythonu je aktuálně v izolovaném prostoru a trvalým způsobem neumožňuje přístup k síti nebo místnímu systému souborů. Všechny soubory uložené místně jsou izolovány a odstraněny po dokončení modulu. Kód Pythonu nemá přístup k většině adresářů v počítači, ve kterých běží, což je výjimka, která je aktuálním adresářem a jeho podadresáři.

### <a name="lack-of-sophisticated-development-and-debugging-support"></a>Nedostatečná podpora vývoje a ladění

Modul Pythonu v současné době nepodporuje funkce IDE, jako je například intellisense a ladění. Také pokud modul selže za běhu, úplné trasování zásobníku Pythonu je k dispozici. Ale musí být zobrazenve výstupním protokolu pro modul. V současné době doporučujeme vyvíjet a ladit skripty Pythonu v prostředí, jako je IPython a potom importovat kód do modulu.

### <a name="single-data-frame-output"></a>Výstup jednoho datového rámce

Vstupní bod Pythonu je povoleno vrátit pouze jeden datový rámec jako výstup. V současné době není možné vrátit libovolné objekty Pythonu, jako jsou trénované modely přímo zpět do modulu runtime studio (classic). Stejně jako [spustit Skript R][execute-r-script], který má stejné omezení, je možné v mnoha případech nakládat objekty do bajtového pole a pak vrátit, že uvnitř datového rámce.

### <a name="inability-to-customize-python-installation"></a>Neschopnost přizpůsobit instalaci Pythonu

V současné době je jediný způsob, jak přidat vlastní moduly Pythonu, prostřednictvím mechanismu zip file popsaného výše. I když je to možné pro malé moduly, je to těžkopádné pro velké moduly (zejména moduly s nativními knihovnami DLL) nebo velký počet modulů.

## <a name="next-steps"></a>Další kroky

Další informace naleznete ve [Středisku pro vývojáře Python](https://azure.microsoft.com/develop/python/).

<!-- Module References -->
[execute-python-script]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-python-script
[execute-r-script]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-r-script

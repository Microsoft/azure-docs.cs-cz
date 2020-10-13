---
title: 'ML Studio (Classic): spouštění skriptů Pythonu – Azure'
description: Naučte se používat modul spouštěného skriptu Pythonu pro použití kódu Pythonu v Machine Learning Studio (klasických) experimentech a webových službách.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: devx-track-python, previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/12/2019
ms.openlocfilehash: 5c728b219168f61f7f791b7db280a701ff216985
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91362380"
---
# <a name="execute-python-machine-learning-scripts-in-azure-machine-learning-studio-classic"></a>Spouštění skriptů strojového učení v jazyce Python v Azure Machine Learning Studio (Classic)

**platí pro:** ![ Platí pro. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (Classic) ![ neplatí pro.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../compare-azure-ml-to-studio-classic.md)  


Python je cenným nástrojem v nástroji pro řadu vědeckých dat. Používá se v každé fázi typických pracovních postupů strojového učení, včetně průzkumu dat, extrakce funkcí, školení modelů a ověřování a nasazení.

Tento článek popisuje, jak pomocí modulu spouštěného skriptu Pythonu používat v Azure Machine Learning Studio (klasické) experimenty a webové služby kód Pythonu.

## <a name="using-the-execute-python-script-module"></a>Použití modulu spuštění skriptu Pythonu

Primární rozhraní pro Python v studiu (Classic) se provádí pomocí modulu [spouštění skriptu Pythonu][execute-python-script] . Akceptuje až tři vstupy a vytvoří až dva výstupy, podobně jako modul [spuštění skriptu jazyka R][execute-r-script] . Kód Pythonu je zadán do pole parametru prostřednictvím speciálně pojmenované funkce vstupního bodu `azureml_main` .

![Spustit modul Python Script](./media/execute-python-scripts/execute-machine-learning-python-scripts-module.png)

![Ukázka kódu Pythonu v poli parametrů modulu](./media/execute-python-scripts/embedded-machine-learning-python-script.png)

### <a name="input-parameters"></a>Vstupní parametry

Vstupy modulu Pythonu se zveřejňují jako Pandaselné datarámce. `azureml_main`Funkce přijímá až dva volitelné PANDAS Dataframes jako parametry.

Mapování mezi vstupními porty a parametry funkce jsou pozice:

- První připojený vstupní port je namapován na první parametr funkce.
- Druhý vstup (Pokud je připojen) je namapován na druhý parametr funkce.
- Třetí vstup se používá k [importu dalších modulů Pythonu](#import-modules).

Podrobnější sémantika způsobu, jakým jsou vstupní porty mapovány na parametry `azureml_main` funkce, jsou uvedeny níže.

![Tabulka vstupních konfigurací portů a výsledný podpis v jazyce Python](./media/execute-python-scripts/python-script-inputs-mapped-to-parameters.png)

### <a name="output-return-values"></a>Výstupní návratové hodnoty

`azureml_main`Funkce musí vracet jeden PANDAS dataframe zabalený v rámci [sekvence](https://docs.python.org/2/c-api/sequence.html) Pythonu, jako je například řazená kolekce členů, seznam nebo pole numpy. První prvek této sekvence se vrátí na první výstupní port modulu. Druhý výstupní port modulu se používá pro [vizualizace](#visualizations) a nevyžaduje návratovou hodnotu. Toto schéma je uvedené níže.

![Mapování vstupních portů na parametry a návratovou hodnotu na výstupní port](./media/execute-python-scripts/map-of-python-script-inputs-outputs.png)

## <a name="translation-of-input-and-output-data-types"></a>Překlad vstupních a výstupních datových typů

Datové sady studia nejsou stejné jako v případě Panda dataframes. V důsledku toho se vstupní datové sady v studiu (Classic) převedou na PANDAS dataframe a výstupní datové snímky se převedou zpátky na datové sady studia (Classic). Během tohoto procesu převodu jsou provedeny také následující překlady:

 **Datový typ Pythonu** | **Postup překladu studia** |
| --- | --- |
| Řetězce a číslice| Přeloženo tak, jak je |
| PANDAS ' NEDEF ' | Přeloženo jako chybějící hodnota |
| Vektory indexu | Neplatné |
| Názvy sloupců bez řetězců | Zavolat `str` na názvy sloupců |
| Duplicitní názvy sloupců | Přidat číselnou příponu: (1), (2), (3) atd.

**Všechny vstupní datové rámce ve funkci Pythonu mají vždycky 64 bitů číselného indexu od 0 do počtu řádků minus 1.*

## <a name="importing-existing-python-script-modules"></a><a id="import-modules"></a>Importují se existující moduly skriptu Pythonu.

Back-end používaný ke spuštění Pythonu vychází z [Anaconda](https://www.anaconda.com/distribution/), široce používaného vědecké distribuce Pythonu. Obsahuje téměř 200 nejčastějších balíčků Python používaných v úlohách orientovaných na data. Studio (Classic) v současné době nepodporuje pro instalaci a správu externích knihoven použití systémů správy balíčků, jako je PIP nebo conda.  Pokud potřebujete přidat další knihovny, použijte jako vodítko následující scénář.

Běžným případem použití je zahrnutí stávajících skriptů Pythonu do studia (klasických) experimentů. Modul [spuštění skriptu Pythonu][execute-python-script] přijímá soubor zip, který obsahuje moduly Pythonu na třetím vstupním portu. Soubor je v době běhu extrahován rozhraním a obsah se přidá do cesty knihovny interpretu Pythonu. `azureml_main`Funkce vstupního bodu pak může tyto moduly importovat přímo. 

Například zvažte, že soubor Hello.py obsahující jednoduchou funkci "Hello, World".

![Uživatelsky definovaná funkce v souboru Hello.py](./media/execute-python-scripts/figure4.png)

V dalším kroku vytvoříme soubor Hello.zip, který obsahuje Hello.py:

![Soubor ZIP obsahující kód Pythonu definovaný uživatelem](./media/execute-python-scripts/figure5.png)

Nahrajte soubor ZIP jako datovou sadu do studia (Classic). Pak vytvořte a spusťte experiment, který používá kód Pythonu v souboru Hello.zip tím, že ho připojíte k třetímu vstupnímu portu **spouštěného modulu skriptu Pythonu** , jak je znázorněno na následujícím obrázku.

![Ukázka experimentu s Hello.zip jako vstup pro spuštění modulu skriptu Pythonu](./media/execute-python-scripts/figure6a.png)

![Uživatelsky definovaný kód Pythonu nahraný jako soubor zip](./media/execute-python-scripts/figure6b.png)

Výstup modulu ukazuje, že se soubor zip rozbalí a že je funkce `print_hello` spuštěná.

![Výstup modulu zobrazující uživatelsky definovanou funkci](./media/execute-python-scripts/figure7.png)

## <a name="accessing-azure-storage-blobs"></a>Přístup k objektům blob Azure Storage

K datům uloženým v účtu Azure Blob Storage můžete přistupovat pomocí těchto kroků:

1. Stáhněte si [balíček Azure Blob Storage pro Python](https://azuremlpackagesupport.blob.core.windows.net/python/azure.zip) místně.
1. Nahrajte soubor zip do pracovního prostoru studia (Classic) jako datovou sadu.
1. Vytvoření objektu BlobService pomocí `protocol='http'`

```
from azure.storage.blob import BlockBlobService

# Create the BlockBlockService that is used to call the Blob service for the storage account
block_blob_service = BlockBlobService(account_name='account_name', account_key='account_key', protocol='http')
```

1. Zakázat **zabezpečený přenos vyžadovaný** na kartě nastavení **Konfigurace** úložiště

![Zakázat zabezpečený přenos vyžadovaný v Azure Portal](./media/execute-python-scripts/disable-secure-transfer-required.png)

## <a name="operationalizing-python-scripts"></a>Zprovozňování skripty Pythonu

Při publikování jako webové služby se zavolají všechny moduly [spouštění skriptu Pythonu][execute-python-script] používané v experimentu bodování. Například následující obrázek ukazuje experiment bodování, který obsahuje kód pro vyhodnocení jednoho výrazu v Pythonu.

![Pracovní prostor Studio pro webovou službu](./media/execute-python-scripts/figure3a.png)

![Výraz Python PANDAS](./media/execute-python-scripts/python-script-with-python-pandas.png)

Webová služba vytvořená z tohoto experimentu by probrala tyto akce:

1. Převést výraz Pythonu jako vstup (jako řetězec)
1. Poslat výraz Pythonu Překladači Pythonu
1. Vrátí tabulku obsahující jak výraz, tak vyhodnocený výsledek.

## <a name="working-with-visualizations"></a><a id="visualizations"></a>Práce s vizualizacemi

Vykreslení vytvořená pomocí MatplotLib může být vráceno [skriptem Execute Python][execute-python-script]. Vykresluje se ale při použití jazyka R automaticky nepřesměrují na obrázky. Proto uživatel musí explicitně uložit všechny vykreslení do souborů PNG.

K vygenerování imagí z MatplotLib je třeba provést následující kroky:

1. Přepněte back-end na "AGG" ze výchozího zobrazovacího modulu na bázi QT.
1. Vytvoří nový objekt obrázku.
1. Získejte osu a vygenerujte do ní všechny.
1. Uložte obrázek do souboru PNG.

Tento proces je znázorněný v následujících obrázcích, které vytvoří matici typu bodový graf pomocí funkce scatter_matrix v PANDAS.

![Kód pro uložení MatplotLibch obrázků do imagí](./media/execute-python-scripts/figure-v1-8.png)

![Pokud si chcete zobrazit tyto údaje, klikněte na vizualizovat v modulu spuštění skriptu Pythonu.](./media/execute-python-scripts/figure-v2-9a.png)

![Vizualizace pro ukázkový experiment pomocí kódu Pythonu](./media/execute-python-scripts/figure-v2-9b.png)

Je možné vrátit více hodnot tak, že je uložíte do různých imagí. Studio (Classic) runtime vybírá všechny obrázky a zřetězuje je pro vizualizaci.

## <a name="advanced-examples"></a>Rozšířené příklady

Prostředí Anaconda nainstalované v studiu (Classic) obsahuje běžné balíčky, jako je NumPy, SciPy a Scikits – informace. Tyto balíčky je možné efektivně využít ke zpracování dat v kanálu strojového učení.

Například následující experiment a skript ilustrují použití učících v Scikits-Learn k výpočtu skóre důležitosti funkcí pro datovou sadu. Skóre lze použít k provedení výběru funkcí pod dohledem před jejich odesláním do jiného modelu.

Tady je funkce Pythonu, která slouží k výpočtu skóre důležitosti a objednání funkcí na základě skóre:

![Funkce pro hodnocení funkcí podle skóre](./media/execute-python-scripts/figure8.png)

Následující experiment pak vypočítá a vrátí hodnocení důležitosti funkcí v datové sadě "Pima indických diabetes" v Azure Machine Learning Studio (Classic):

![Experimentování k funkcím Rank v Pima datové sadě indických diabetes pomocí Pythonu](./media/execute-python-scripts/figure9a.png)

![Vizualizace výstupu modulu spuštění skriptu Pythonu](./media/execute-python-scripts/figure9b.png)

## <a name="limitations"></a>Omezení

Modul [spuštění skriptu Pythonu][execute-python-script] má v současné době tato omezení:

### <a name="sandboxed-execution"></a>Spuštění v izolovaném prostoru

Modul runtime Pythonu je aktuálně v izolovaném prostoru (sandbox) a trvale neumožňuje přístup k síti nebo místnímu systému souborů. Všechny místně uložené soubory se po dokončení modulu izolují a odstraní. Kód Pythonu nemůže přistupovat ke většině adresářů v počítači, na kterém je spuštěný, a výjimkou je aktuální adresář a jeho podadresáře.

### <a name="lack-of-sophisticated-development-and-debugging-support"></a>Nedostatek sofistikované podpory pro vývoj a ladění

Modul Python v současné době nepodporuje funkce rozhraní IDE, jako je IntelliSense a ladění. V případě, že dojde k chybě modulu za běhu, je k dispozici kompletní trasování zásobníku Pythonu. Ale musí se zobrazit ve výstupním protokolu pro modul. V současnosti doporučujeme vyvíjet a ladit skripty v jazyce Python v prostředí, jako je IPython, a pak kód importovat do modulu.

### <a name="single-data-frame-output"></a>Výstup s jedním datovým snímkem

Vstupní bod Pythonu smí vracet pouze jeden datový rámec jako výstup. V současné době není možné vracet libovolné objekty Pythonu, jako jsou například školené modely, přímo zpět do modulu runtime studia (Classic). Stejně jako spouštěcí [skript jazyka R][execute-r-script], který má stejné omezení, je možné v mnoha případech do rozevíracích objektů do pole bajtů a pak vracet v rámci datového rámce.

### <a name="inability-to-customize-python-installation"></a>Nemožnost přizpůsobení instalace Pythonu

V současné době je jediným způsobem, jak přidat vlastní moduly Pythonu, prostřednictvím mechanismu souboru ZIP popsaného výše. I když je to pro malé moduly vhodné, je náročné pro velké moduly (zejména moduly s nativními knihovnami DLL) nebo velký počet modulů.

## <a name="next-steps"></a>Další kroky

Další informace naleznete ve [Středisku pro vývojáře Python](https://azure.microsoft.com/develop/python/).

<!-- Module References -->
[execute-python-script]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-python-script
[execute-r-script]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-r-script

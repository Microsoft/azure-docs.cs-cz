---
title: 'Postupy: Vytvoření konfigurace s rozsahem sady prostředků'
description: Přečtěte si, jak vytvořit pravidlo konfigurace s vymezeným oborem nastavení a přepsat tak, jak se prostředky seskupí do sad prostředků.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 02/17/2021
ms.openlocfilehash: 517b07eecdbc63754f46fcf1051bf5b987dbc20e
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "100654201"
---
# <a name="create-scoped-resource-set-configuration-rules"></a>Vytvořit pravidla konfigurace sady prostředků s vymezeným oborem

Systémy zpracování dat ve velkém měřítku obvykle ukládají jednu tabulku na disk jako více souborů. Tento koncept je reprezentován v Azure dosah pomocí sad prostředků. Sada prostředků je jeden objekt v katalogu dat, který představuje velký počet prostředků v úložišti. Další informace najdete v tématu [porozumění sadám prostředků](concept-resource-sets.md).

Při kontrole účtu úložiště používá Azure dosah sadu definovaných vzorů k určení, jestli je skupina prostředků sada prostředků. V některých případech nemusí seskupení sad prostředků Azure dosah přesně odrážet vaše datové podstaty. Pravidla sady prostředků s vymezeným oborem vám umožní přizpůsobit nebo přepsat způsob, jakým Azure dosah zjistí, které prostředky se seskupují jako sady prostředků a jak se zobrazují v katalogu.

## <a name="how-to-create-a-scoped-resource-set-configuration"></a>Postup vytvoření konfigurace s rozsahem sady prostředků

Pomocí následujících kroků vytvořte novou konfiguraci sady prostředků s vymezeným oborem:

1. Přejít do centra pro správu. V nabídce vyberte **množiny prostředků s vymezeným oborem** . Kliknutím na **+ Nový** vytvořte novou sadu pravidel konfigurace.
        :::image type="content" source="media/how-to-scoped-resource-sets/create-new-scoped-resource-set-rule.png" alt-text="Vytvořit nové pravidlo sady prostředků s vymezeným oborem" border="true":::

1. Zadejte obor konfigurace sady prostředků s vymezeným oborem. Vyberte typ účtu úložiště a název účtu úložiště, na kterém chcete vytvořit pravidlo. Každá sada pravidel se aplikuje relativně k oboru cesty ke složce určenému v poli **cesta ke složce** . 
        :::image type="content" source="media/how-to-scoped-resource-sets/create-new-scoped-resource-set-scope.png" alt-text="Vytvořit nové pravidlo sady prostředků s vymezeným oborem" border="true":::

1. Pokud chcete zadat pravidlo pro obor konfigurace, vyberte **+ nové pravidlo**.
1. Chcete-li vytvořit pravidlo, zadejte do následujících polí:
    1. **Název pravidla:** Název pravidla konfigurace. Toto pole nemá žádný vliv na prostředky, na které se pravidlo vztahuje.
    1. **Kvalifikovaný název:** Kvalifikovaná cesta, která používá kombinaci textu, dynamického přetvoření a statických záprvků pro porovnávání prostředků s pravidlem konfigurace. Tato cesta je relativní vzhledem k oboru pravidla konfigurace. Podrobné pokyny k určení kvalifikovaných názvů najdete v části [syntaxe](#syntax) níže. 
    1. **Zobrazovaný název:** Zobrazovaný název assetu. Toto pole je volitelné. Pomocí prostého textu a statických odzpůsobování můžete přizpůsobit způsob zobrazení prostředku v katalogu. Podrobnější pokyny najdete níže v části [syntaxe](#syntax) .
    1. **Neseskupovat jako sadu prostředků:** Pokud je povoleno, odpovídající prostředek se nebude seskupovat do sady prostředků. 
        :::image type="content" source="media/how-to-scoped-resource-sets/scoped-resource-set-rule-example.png" alt-text="Vytvořit nové pravidlo sady prostředků s vymezeným oborem" border="true"::: 
1. Uložte pravidlo kliknutím na **Přidat**. 

## <a name="scoped-resource-set-syntax"></a><a name="syntax"></a> Syntaxe sady prostředků s vymezeným oborem

Při vytváření pravidel pro sadu prostředků s vymezeným oborem použijte následující syntaxi k určení, která pravidla prostředků se vztahují na.

### <a name="static-replacers-single-brackets"></a>Statické přeapostrofy (samostatné závorky)

Jednoduché závorky se používají jako **dynamické** měnící se v pravidle sady prostředků s vymezeným oborem. Zadejte dynamickou Replacer v kvalifikovaném názvu pomocí formátu `{<replacerName:<replacerType>}` . V případě, že se shodují, dynamické oddálení se používají jako podmínka seskupení, která označuje, že prostředky by měly být reprezentovány jako sada prostředků. Pokud jsou prostředky seskupeny do sady prostředků, bude mít kvalifikovaná cesta nastavená na `{replacerName}` místo, kde byl zadán parametr replacer.

Například pokud dva prostředky `folder1/file-1.csv` a `folder2/file-2.csv` odpovídají pravidlu `{folder:string}/file-{NUM:int}.csv` , sada prostředků bude jedinou entitou `{folder}/file-{NUM}.csv` .

#### <a name="special-case-dynamic-replacers-when-not-grouping-into-resource-set"></a>Zvláštní případ: dynamické přeseskupování, pokud se neseskupují do sady prostředků

Pokud je pro pravidlo sady prostředků s vymezeným oborem povolená možnost *Neseskupovat jako sadu prostředků* , název Replacer je volitelné pole. `{:<replacerType>}` je platná syntaxe. Například `file-{:int}.csv` by úspěšně odpovídaly pro `file-1.csv` a `file-2.csv` a vytvořily dva různé prostředky namísto sady prostředků.

### <a name="static-replacers-double-brackets"></a>Statické zálomené objekty (Dvojité závorky)

Dvojité závorky se používají jako **statické** objekty pro nastavení v kvalifikovaném názvu pravidla nastaveného na vymezené prostředky. Zadejte statický Replacer v kvalifikovaném názvu pomocí formátu `{{<replacerName>:<replacerType>}}` . Pokud odpovídá, každá sada jedinečných statických hodnot Replacer vytvoří jiné seskupení sady prostředků.

Například pokud dva prostředky `folder1/file-1.csv` a `folder2/file-2.csv` odpovídají pravidlu `{{folder:string}}/file-{NUM:int}.csv` , vytvoří se dvě sady prostředků `folder1/file-{NUM}.csv` a `folder2/file-{NUM}.csv` .

Statické přejmenování se dají použít k určení zobrazovaného názvu prostředku, který odpovídá pravidlu sady prostředků nastavenému v oboru. Použití `{{<replacerName>}}` v poli zobrazovaný název pravidla bude používat odpovídající hodnotu v názvu assetu.

### <a name="available-replacement-types"></a>Dostupné typy nahrazení

Níže jsou uvedeny dostupné typy, které lze použít ve statických a dynamických prodruzích:

| Typ | Struktura |
| ---- | --------- |
| řetězec | Řada 1 nebo více znaků Unicode včetně oddělovačů, jako jsou mezery. |
| int | Řada 1 nebo více 0-9 znaků ASCII, může být 0 (např. 0001). |
| guid | Řada 32 nebo 8-4-4-4-12 řetězcové reprezentace identifikátoru UUID jako defineddefa v https://tools.ietf.org/html/rfc4122 |
| date | Řada 6 nebo 8 0-9 znaků ASCII s volitelnou oddělovači: RRRRMMDD, rrrr-mm-dd, rrmmdd, RR-mm-dd, zadaný v https://tools.ietf.org/html/rfc3339 |
| time | Řada 4 nebo 6 0-9 znaků ASCII s volitelnou oddělovači: HHmm, HH: mm, HHmmss, HH: mm: SS zadané v https://tools.ietf.org/html/rfc3339 |
| časové razítko | Řada 12 nebo 14 0-9 znaků ASCII s volitelnou oddělovači: RRRR-MM-ddTHH: mm, yyyyMMddhhmm, rrrr-MM-ddTHH: mm: SS, rrrrmmddhhmmss zadaná v https://tools.ietf.org/html/rfc3339 |
| boolean | Může obsahovat hodnotu true nebo false, nerozlišuje velká a malá písmena. |
| číslo | Řada 0 nebo více 0-9 ASCII znaků, může být 0 s předponou (např. 0001) následovaných volitelně tečkou. a řada 1 nebo více 0-9 znaků ASCII, může být 0 přípona (např. 100) | 
| soustavy | Řada 1 nebo více znaků ASCII ze sady 0-1 a a-F, hodnota může být 0 s předponou |
| locale | Řetězec, který odpovídá syntaxi zadané v https://tools.ietf.org/html/rfc5646 |

## <a name="order-of-scoped-resource-set-rules-getting-applied"></a>Pořadí použití pravidel sady prostředků s vymezeným oborem

Níže je uvedené pořadí operací při aplikování pravidel sady prostředků s vymezeným oborem:

1. Pokud se prostředek shoduje se dvěma pravidly, bude mít priorita přesnější rozsah. Pravidla v oboru `container/folder` se například použijí před pravidly v oboru `container` . 
1. Pořadí pravidel v rámci určitého oboru. To se dá upravit v uživatelském prostředí.
1. Pokud Asset nevyhovuje žádnému zadanému pravidlu, použijí se výchozí nastavení heuristiky sady prostředků.

## <a name="examples"></a>Příklady

### <a name="example-1"></a>Příklad 1

Extrakce dat SAP do úplných a rozdílových zátěží

*Vstupy*

Spis
-   `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/13/saptable_customer_20200101_20200102_01.txt`
-   `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/13/saptable_customer_20200101_20200102_02.txt`
-   `https://myazureblob.blob.core.windows.net/bar/customer/delta/2020/01/15/saptable_customer_20200101_20200102_01.txt`
-   `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/17/saptable_customer_20200101_20200102_01.txt`
-   `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/17/saptable_customer_20200101_20200102_02.txt`


*Pravidlo sady prostředků s vymezeným oborem*

**Rozsah:**https://myazureblob.blob.core.windows.net/bar/

**Zobrazovaný název:** Externí zákazník

**Kvalifikovaný název:**`customer/{extract:string}/{year:int}/{month:int}/{day:int}/saptable_customer_{date_from:date}_{date_to:time}_{sequence:int}.txt`

**Sada prostředků:** true

*Výstup*

Jeden prostředek sady prostředků

**Zobrazovaný název:** Externí zákazník

**Kvalifikovaný název:**`https://myazureblob.blob.core.windows.net/bar/customer/{extract}/{year}/{month}/{day}/saptable_customer_{date_from}_{date_to}_{sequence}.txt`

### <a name="example-2"></a>Příklad 2

Data IoT ve formátu Avro

*Vstupy*

Spis
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/02-01-2020/22:33:22-001.avro`
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

*Pravidla sady prostředků s vymezeným oborem*

**Rozsah:**https://myazureblob.blob.core.windows.net/bar/

Pravidlo 1

**Zobrazovaný název:** ' Machine-89 '

**Kvalifikovaný název:**`raw/machinename-89/{date:date}/{time:time}-{id:int}.avro`

**Sada prostředků:** true

Pravidlo 2

**Zobrazovaný název:** ' Machine-90 '

**Kvalifikovaný název:**`raw/machinename-90/{date:date}/{time:time}-{id:int}.avro`

**Sada prostředků: true**

*Výstupy*

2 sady prostředků 

Sada prostředků 1

**Zobrazovaný název:** machine-89

**Kvalifikovaný název:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/{date}/{time}-{id}.avro`

Sada prostředků 2

**Zobrazovaný název:** machine-90

**Kvalifikovaný název:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/{date}/{time}-{id}.avro`

### <a name="example-3"></a>Příklad 3

Data IoT ve formátu Avro

*Vstupy*

Spis
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`
-   `https://myazureblob.blob.core.windows.netbar/raw/machinename-89/02-01-2020/22:33:22-001.avro`
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

*Pravidlo sady prostředků s vymezeným oborem*

**Rozsah:**https://myazureblob.blob.core.windows.net/bar/

**Zobrazovaný název:** ' Machine-{{MachineID}} '

**Kvalifikovaný název:**`raw/machinename-{{machineid:int}}/{date:date}/{time:time}-{id:int}.avro`

**Sada prostředků:** true

*Výstupy*

Sada prostředků 1

**Zobrazovaný název:** machine-89

**Kvalifikovaný název:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/{date}/{time}-{id}.avro`

Sada prostředků 2

**Zobrazovaný název:** machine-90

**Kvalifikovaný název:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/{date}/{time}-{id}.avro`

### <a name="example-4"></a>Příklad 4

Neseskupovat do sad prostředků

*Vstupy*

Spis
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/02-01-2020/22:33:22-001.avro`
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

*Pravidlo sady prostředků s vymezeným oborem*

**Rozsah:**https://myazureblob.blob.core.windows.net/bar/

**Zobrazovaný název:** ' Machine-{{MachineID}} '

**Kvalifikovaný název:**`raw/machinename-{{machineid:int}}/{{:date}}/{{:time}}-{{:int}}.avro`

**Sada prostředků:** false

*Výstupy*

4 jednotlivé prostředky

Asset 1

**Zobrazovaný název:** machine-89

**Kvalifikovaný název:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`

Asset 2

**Zobrazovaný název:** machine-89

**Kvalifikovaný název:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`

Asset 3

**Zobrazovaný název:** machine-89

**Kvalifikovaný název:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/02-01-2020/22:33:22-001.avro`

Asset 4

**Zobrazovaný název:** machine-90

**Kvalifikovaný název:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

## <a name="next-steps"></a>Další kroky

Začněte [registrací a kontrolou Azure Data Lake účtu úložiště Gen2](register-scan-adls-gen2.md).
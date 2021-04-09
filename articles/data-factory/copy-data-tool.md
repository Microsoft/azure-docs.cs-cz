---
title: Nástroj Kopírování dat Azure Data Factory
description: Poskytuje informace o nástroji Kopírování dat v uživatelském rozhraní Azure Data Factory
author: dearandyxu
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/17/2020
ms.author: yexu
ms.openlocfilehash: 18d61c7f51547a524e64fc44c98e70e0f5bc2752
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100372990"
---
# <a name="copy-data-tool-in-azure-data-factory"></a>Nástroj Kopírování dat ve službě Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Nástroj Azure Data Factory Kopírování dat usnadňuje a optimalizuje proces ingestování dat do data Lake, což je obvykle první krok v rámci kompletního scénáře integrace dat.  Šetří čas, zejména při prvním použití Azure Data Factory k ingestování dat ze zdroje dat. Mezi výhody použití tohoto nástroje patří:

- Pokud používáte nástroj Azure Data Factory Kopírování dat, nepotřebujete pochopit definice Data Factory pro propojené služby, datové sady, kanály, aktivity a triggery. 
- Tok nástroje Kopírování dat je intuitivní pro načítání dat do data Lake. Nástroj automaticky vytvoří všechny nezbytné Data Factory prostředky ke zkopírování dat z vybraného zdrojového úložiště dat do vybraného cílového úložiště dat nebo úložiště dat jímky. 
- Nástroj Kopírování dat vám pomůže ověřit data, která se ingestují v době vytváření, což vám pomůže vyhnout se případným chybám na začátku.
- Pokud potřebujete implementovat složitou obchodní logiku pro načtení dat do data Lake, můžete i nadále upravovat Data Factory prostředky vytvořené nástrojem Kopírování dat pomocí vytváření aktivit v Data Factory uživatelském rozhraní. 

Následující tabulka uvádí informace o tom, kdy použít nástroj Kopírování dat vs. vytváření aktivit na základě Data Factory uživatelského rozhraní: 

| Nástroj pro kopírování dat | Vytváření obsahu na aktivitu (aktivita kopírování) |
| -------------- | -------------------------------------- |
| Chcete jednoduše vytvořit úlohu načítání dat bez učení o Azure Data Factory entitách (propojené služby, datové sady, kanály atd.) | Chcete implementovat komplexní a flexibilní logiku pro načítání dat do jezera. |
| Chcete rychle načíst velký počet artefaktů dat do data Lake. | Chcete zřetězit aktivitu kopírování s následnými aktivitami pro čištění nebo zpracování dat. |

Chcete-li spustit nástroj Kopírování dat, klikněte na dlaždici **kopírování dat** na domovské stránce objektu pro vytváření dat.

![Stránka Začínáme – odkaz na nástroj Kopírování dat](./media/doc-common-process/get-started-page.png)


## <a name="intuitive-flow-for-loading-data-into-a-data-lake"></a>Intuitivní tok pro načítání dat do data Lake
Tento nástroj umožňuje snadno přesouvat data z nejrůznějších zdrojů do cílových míst v řádu minut pomocí intuitivního toku:  

1. Nakonfigurujte nastavení pro **zdroj**.
2. Nakonfigurujte nastavení pro **cíl**. 
3. Konfigurace **upřesňujících nastavení** pro operaci kopírování, jako je například mapování sloupců, nastavení výkonu a nastavení odolnosti proti chybám. 
4. Zadejte **plán** pro úlohu načítání dat. 
5. Zkontrolujte **souhrn** Data Factory entit, které se mají vytvořit. 
6. **Upravte** kanál tak, aby podle potřeby aktualizoval nastavení aktivity kopírování. 

   Nástroj je navržený s velkými objemy dat od začátku, s podporou pro různé typy dat a objektů. Můžete ji použít k přesunu stovek složek, souborů nebo tabulek. Nástroj podporuje i automatické mapování dat, zachytávání schématu a automatické mapování a filtrování dat.

![Nástroj pro kopírování dat](./media/copy-data-tool/copy-data-tool.png)

## <a name="automatic-data-preview"></a>Náhled automatických dat
Můžete zobrazit náhled částí dat z vybraného zdrojového úložiště dat, což vám umožní ověřit kopírovaná data. Kromě toho, pokud jsou zdrojová data v textovém souboru, nástroj Kopírování dat analyzuje textový soubor a automaticky detekuje oddělovače řádků a sloupců a schéma.

![Nastavení souboru](./media/copy-data-tool/file-format-settings.png)

Po zjištění:

![Zjištěná nastavení souboru a verze Preview](./media/copy-data-tool/after-detection.png)

## <a name="schema-capture-and-automatic-mapping"></a>Zachytávání schématu a automatické mapování
Schéma zdroje dat nesmí být stejné jako schéma cíle dat v mnoha případech. V tomto scénáři je nutné mapovat sloupce ze zdrojového schématu na sloupce z cílového schématu.

Nástroj Kopírování dat monitoruje a učí chování při mapování sloupců mezi zdrojovými a cílovými obchody. Po výběru jednoho nebo několika sloupců ze zdrojového úložiště dat a jejich mapování na cílové schéma nástroj Kopírování dat spustí analýzu vzoru pro páry sloupců, které jste vybrali z obou stran. Pak použije stejný vzor pro zbývající sloupce. Proto vidíte, že všechny sloupce byly namapovány na cíl způsobem, který požadujete pouze po několika kliknutích.  Pokud nejste spokojeni s volbou mapování sloupců, které poskytuje nástroj Kopírování dat Tool, můžete ho ignorovat a pak pokračovat v ručním mapování sloupců. Mezitím se nástroj Kopírování dat neustále učí a aktualizuje vzor a nakonec dosáhne správného vzoru mapování sloupců, které chcete dosáhnout. 

> [!NOTE]
> Pokud tabulka v cílovém úložišti neexistuje, kopíruje data z SQL Server nebo Azure SQL Database do služby Azure synapse Analytics, Kopírování dat nástroj podporuje automatické vytváření tabulky pomocí zdrojového schématu. 

## <a name="filter-data"></a>Filtrování dat
Můžete filtrovat zdrojová data a vybrat pouze data, která je třeba zkopírovat do úložiště dat jímky. Filtrování omezuje objem dat, která mají být zkopírována do úložiště dat jímky, a tím zvyšuje propustnost operace kopírování. Nástroj Kopírování dat poskytuje flexibilní způsob, jak filtrovat data v relační databázi pomocí dotazovacího jazyka SQL nebo souborů ve složce objektů BLOB v Azure. 

### <a name="filter-data-in-a-database"></a>Filtrování dat v databázi
Následující snímek obrazovky ukazuje dotaz SQL pro filtrování dat.

![Filtrování dat v databázi](./media/copy-data-tool/filter-data-in-database.png)

### <a name="filter-data-in-an-azure-blob-folder"></a>Filtrování dat ve složce Azure Blob
K kopírování dat ze složky můžete použít proměnné v cestě ke složce. Podporované proměnné jsou: **{year}**, **{month}**, **{Day}**, **{Hour}** a **{minute}**. Příklad: inputfolder/{Year}/{Month}/{Day}. 

Předpokládejme, že máte vstupní složky v následujícím formátu: 

```
2016/03/01/01
2016/03/01/02
2016/03/01/03
...
```

Klikněte na tlačítko **Procházet** pro **soubor nebo složku**, vyhledejte jednu z těchto složek (například 2016->03->01->02) a klikněte na **zvolit**. V textovém poli by se měla zobrazit 2016/03/01/02. 

Pak nahraďte **2016** **řetězcem**{ **year}**, **03** a **{month}**, **01** s **{Day}** a **02** a stiskněte klávesu **TAB** . Měli byste vidět rozevírací seznamy a vybrat formát pro tyto čtyři proměnné:

![Filtrovat soubor nebo složku](./media/copy-data-tool/filter-file-or-folder.png)

Nástroj Kopírování dat generuje parametry pomocí výrazů, funkcí a systémových proměnných, které lze použít k reprezentaci {year}, {month}, {Day}, {hour} a {minut} při vytváření kanálu.

## <a name="scheduling-options"></a>Možnosti plánování
Operaci kopírování můžete spustit jednou nebo podle plánu (každou hodinu, každý den atd.). Tyto možnosti se dají použít pro konektory v různých prostředích, včetně místních, cloudových a místních desktopových zařízení. 

Jednorázová operace kopírování umožňuje přesun dat ze zdroje do cíle pouze jednou. Platí pro data libovolné velikosti a veškerého podporovaného formátu. Naplánované kopírování umožňuje kopírovat data při opakování, které zadáte. Ke konfiguraci naplánované kopie můžete použít bohatá nastavení (například opakování, časový limit a upozornění).

![Možnosti plánování](./media/copy-data-tool/scheduling-options.png)


## <a name="next-steps"></a>Další kroky
Vyzkoušejte tyto kurzy, které používají nástroj Kopírování dat:

- [Rychlý Start: vytvoření datové továrny pomocí nástroje Kopírování dat](quickstart-create-data-factory-copy-data-tool.md)
- [Kurz: kopírování dat v Azure pomocí nástroje Kopírování dat](tutorial-copy-data-tool.md) 
- [Kurz: kopírování místních dat do Azure pomocí nástroje Kopírování dat](tutorial-hybrid-copy-data-tool.md)

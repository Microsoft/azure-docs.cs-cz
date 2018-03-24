---
title: Kopírování dat nástroje Azure Data Factory | Microsoft Docs
description: Poskytuje informace o nástroji pro kopírování dat v Azure Data Factory uživatelského rozhraní
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.date: 01/10/2018
ms.author: jingwang
ms.openlocfilehash: b82ee060ff3f25e7a92c85114d457ecb349159b3
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="copy-data-tool-in-azure-data-factory"></a>Nástroj pro kopírování dat v Azure Data Factory
Nástroj Azure Data Factory kopírování dat usnadňuje a optimalizuje proces příjem dat do úložiště data lake, což je většinou první krok v případě integrace pomocí dat začátku do konce.  Ho šetří čas, zejména při použití Azure Data Factory pro načítání dat ze zdroje dat pro první. Některé z výhod použití tohoto nástroje jsou:

- Pokud používáte nástroj Azure Data Factory kopie dat, třeba nerozumíte definice služby Data Factory propojené služby, datové sady, kanály, aktivity a aktivační události. 
- Tok nástroj pro kopírování dat je intuitivní pro načítání dat do úložiště data lake. Nástroj automaticky vytvoří všechny potřebné prostředky služby Data Factory ke zkopírování dat z vybrané zdrojové úložiště dat do úložiště dat vybrané cílové/jímky. 
- Nástroj pro kopírování dat umožňuje ověřit data, která je právě konzumována v době vytváření, což vám usnadní vyhnout se všechny potenciální chyby na začátku sám sebe.
- Pokud potřebujete implementovat komplexní obchodní logiku pro načtení dat do úložiště data lake, můžete upravit stále Data Factory prostředky vytvořené nástrojem pro kopírování dat pomocí vytváření za aktivitu v uživatelském rozhraní Data Factory. 

Následující tabulka obsahuje pokyny k použití nástroje kopírování dat a vytváření za aktivitu v uživatelském rozhraní objektu pro vytváření dat: 

| Nástroj pro kopírování dat | Za vytváření aktivity (aktivita kopírování) |
| -------------- | -------------------------------------- |
| Chcete snadno vytvářet dat načítání úloh bez informací o entit služby Azure Data Factory (propojené služby, datové sady, kanály atd.) | Chcete implementovat komplexní a flexibilní logiku pro načítání dat do lake. |
| Chcete rychle načíst velký počet artefaktů dat do úložiště data lake. | Chcete zřetězit aktivitou kopírování pomocí následné aktivity pro čištění nebo zpracování dat. |

Chcete-li spustit nástroj pro kopírování dat, klikněte na tlačítko **kopírovat Data** na domovské stránce objektu pro vytváření dat dlaždici.

![Stránka Začínáme Get - odkaz na nástroj pro kopírování dat](./media/copy-data-tool/get-started-page.png)


## <a name="intuitive-flow-for-loading-data-into-a-data-lake"></a>Intuitivní tok pro načítání dat do úložiště data lake
Tento nástroj umožňuje snadno přesunout data z různých zdrojů a cílů v minutách s tok intuitivní:  

1. Nakonfigurujte nastavení pro **zdroj**.
2. Nakonfigurujte nastavení pro **cílový**. 
3. Konfigurace **upřesňující nastavení** operace kopírování například mapování sloupců, nastavení výkon a odolnost proti chybám nastavení. 
4. Zadejte **plán** pro data načítání úloh. 
5. Zkontrolujte **souhrnné** z entit služby Data Factory, který se má vytvořit. 
6. **Upravit** kanálu se aktualizovat nastavení pro aktivitu kopírování, podle potřeby. 

 Nástroj je určen s velkým objemem dat v paměti od začátku, s podporou pro různé data a typy objektů. Můžete ho přesunout stovky složky, soubory nebo tabulky. Nástroj podporuje náhled dat, schématu zachycení a automatické mapování a filtrování dat i.

![Nástroj pro kopírování dat](./media/copy-data-tool/copy-data-tool.png)

## <a name="automatic-data-preview"></a>Náhled automatické dat
Část dat z vybrané zdrojové úložiště dat, které umožňuje ověřit data, která se kopíruje můžete zobrazit náhled. Kromě toho pokud je zdrojová data do textového souboru, nástroj pro kopírování dat analyzuje textový soubor automaticky zjišťovat oddělovače řádků a sloupců a schéma.

![Nastavení souboru](./media/copy-data-tool/file-format-settings.png)

Po zjišťování:

![Nastavení zjištěného souboru a verze preview](./media/copy-data-tool/after-detection.png)

## <a name="schema-capture-and-automatic-mapping"></a>Zachycení schémat a automatické mapování
Schématu zdroje dat nemůže být stejný jako schéma cílové data v mnoha případech. V tomto scénáři budete muset mapování sloupců ze schématu zdroje na sloupce ze schématu cílové.

Nástroj pro kopírování dat monitoruje a zjišťuje vaše chování při mapování sloupců mezi zdrojovým a cílovým úložišti. Po vyberte jeden nebo několik sloupců ze zdrojového úložiště dat a jejich namapování na cílové schéma, spustí se nástroj pro kopírování dat k analýze vzor pro dvojice sloupce, které jste vybrali z obou stranách. Potom se vztahuje stejného vzoru se zbytkem sloupce. Proto byste vidět všechny sloupce, které nebyly namapovány do cílového umístění způsobem, který má být bezprostředně za několik kliknutí.  Pokud nejste s volba mapování sloupců poskytuje nástroj pro kopírování dat, můžete ho ignorovat a pokračovat ručně mapování sloupců. Nástroj pro kopírování dat současně neustále zjišťuje a aktualizuje vzoru a nakonec dosáhne správné vzor pro mapování sloupců, které chcete dosáhnout. 

> [!NOTE]
> Při kopírování dat z SQL serveru nebo databáze SQL Azure do Azure SQL Data Warehouse, pokud tabulka neexistuje v cílové úložiště, zkopírujte Data nástroj podporuje vytváření tabulky automaticky pomocí schématu zdroje. 

## <a name="filter-data"></a>Filtrování dat
Můžete filtrovat zdrojová data a vyberte pouze data, která je možné zkopírovat do úložiště dat jímky. Filtrování snižuje objem dat, který se má zkopírovat do úložiště dat podřízený a proto zvyšuje propustnost kopírování. Nástroj pro kopírování dat poskytuje flexibilní způsob, jak filtrování dat v relační databázi pomocí dotazu jazyka SQL nebo soubory ve složce aplikace objektů blob v Azure. 

### <a name="filter-data-in-a-database"></a>Filtrování dat v databázi
Následující snímek obrazovky ukazuje příkazu jazyka SQL data filtrovat.

![Filtrování dat v databázi](./media/copy-data-tool/filter-data-in-database.png)

### <a name="filter-data-in-an-azure-blob-folder"></a>Filtrování dat v Azure blob složce
Proměnné v cestě ke složce můžete použít ke zkopírování dat ze složky. Podporované proměnné jsou: **{year}**, **{month}**, **{day}**, **{hodinu}**, a **{minutu}**. Příklad: inputfolder / {year} / {month} / {day}. 

Předpokládejme, že máte vstupní složky v následujícím formátu: 

```
2016/03/01/01
2016/03/01/02
2016/03/01/03
...
```

Klikněte na tlačítko **Procházet** tlačítko pro **souboru nebo složky**, přejděte do jednoho z těchto složek (například 2016 -> 03 -> 01 -> 02) a klikněte na tlačítko **zvolte**. Měli byste vidět 2016/03/01/02 v textovém poli. 

Potom nahraďte **2016** s **{year}**, **03** s **{month}**, **01** s **{day}** , a **02** s **{hodinu}**a stiskněte klávesu **kartě** klíč. Měli byste vidět rozevíracích seznamech vyberte formát pro tyto čtyři proměnné:

![Filtr souboru nebo složky](./media/copy-data-tool/filter-file-or-folder.png)

Nástroj pro kopírování dat generuje parametry s výrazy, funkce a systémové proměnné, které lze použít k reprezentování {year}, {month}, {day}, {hodinu} a {minut} při vytváření kanálu. Další informace najdete v tématu [jak pro čtení nebo zápis dat rozdělena na oddíly](how-to-read-write-partitioned-data.md) článku.

## <a name="scheduling-options"></a>Možnosti plánování
Operace kopírování můžete spustit jednou nebo podle plánu (každou hodinu, denně, a tak dále). Tyto možnosti můžete použít pro konektory různých prostředích, včetně místní, cloud a místní desktop. 

Operace kopírování jednorázové umožňuje přesun dat ze zdroje do cíle pouze jednou. Platí pro data libovolné velikosti a jakýkoli podporovaný formát. Naplánované kopie umožňuje kopírovat data na opakování, které zadáte. Bohaté nastavení (např. Zkuste to znovu, vypršení časového limitu a upozornění) můžete použít ke konfiguraci naplánované kopírování.

![Možnosti plánování](./media/copy-data-tool/scheduling-options.png)


## <a name="next-steps"></a>Další postup
Zkuste tyto kurzy, které používají nástroj pro kopírování dat:

- [Rychlý úvod: Vytvořte objekt pro vytváření dat pomocí nástroje kopírování dat](quickstart-create-data-factory-copy-data-tool.md)
- [Kurz: kopírování dat v Azure pomocí nástroje kopírování dat](tutorial-copy-data-tool.md) 
- [Kurz: kopírování místně dat do Azure pomocí nástroje kopírování dat](tutorial-hybrid-copy-data-tool.md)

---
title: Nástroj pro kopírování dat Azure Data Factory | Dokumentace Microsoftu
description: Poskytuje informace o nástroje pro kopírování dat v uživatelské rozhraní služby Azure Data Factory
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/18/2018
ms.author: yexu
ms.openlocfilehash: 107687c785433f81870449d1445136b5148a4d2c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "58101750"
---
# <a name="copy-data-tool-in-azure-data-factory"></a>Nástroj pro kopírování dat v Azure Data Factory
Nástroje pro kopírování dat Azure Data Factory usnadňuje a nastavení proces optimalizuje tím ingestuje dat do data lake, což je obvykle prvním krokem při scénáři integrace data začátku do konce.  Šetří čas, zejména při použití služby Azure Data Factory k ingestování dat ze zdroje dat poprvé. Zde jsou některé z výhod používání tohoto nástroje:

- Pokud používáte nástroj pro kopírování dat Azure Data Factory, třeba nerozumíte definice služby Data Factory propojené služby, datové sady, kanály, aktivit a triggerů. 
- Tok nástroje pro kopírování dat je intuitivní pro načítání dat do služby data lake. Nástroj automaticky vytváří všechny potřebné prostředky Data Factory pro kopírování dat z vybrané zdrojové úložiště dat do úložiště dat vybraný cíl/jímka. 
- Nástroje pro kopírování dat umožňuje ověření dat, který se ingestuje v době vytváření obsahu, který umožňuje vyhnout se všechny potenciální chyby na začátku samotný.
- Pokud potřebujete implementovat komplexní obchodní logiku pro načtení dat do data lake, můžete stále upravovat prostředky služby Data Factory vytvořil nástroj pro kopírování dat pomocí vytváření za aktivitu v uživatelském rozhraní Data Factory. 

Následující tabulka obsahuje pokyny k použití nástroje pro kopírování dat a vytváření za aktivit v uživatelském rozhraní Data Factory: 

| Nástroj pro kopírování dat | Za aktivitu (aktivita kopírování) pro tvorbu |
| -------------- | -------------------------------------- |
| Chcete snadno vytvářet data načítání úloh bez získání informací o entity služby Azure Data Factory (propojené služby, datové sady, kanály atd.) | Chcete implementovat komplexní a flexibilní logiku pro načítání dat do jezera. |
| Chcete rychle načíst velký počet artefaktů dat do data lake. | Chcete aktivitou kopírování pomocí další aktivity pro čištění nebo zpracování dat vzájemné zřetězení. |

Chcete-li spustit nástroj pro kopírování dat, klikněte na tlačítko **kopírování dat** dlaždici na domovské stránce vaší datové továrně.

![Stránka Začínáme Get - odkaz na nástroj pro kopírování dat](./media/copy-data-tool/get-started-page.png)


## <a name="intuitive-flow-for-loading-data-into-a-data-lake"></a>Intuitivní tok pro načítání dat do služby data lake
Tento nástroj umožňuje snadno přesunout data z nejrůznějších zdrojů do cílů během několika minut s využitím intuitivního flow:  

1. Konfigurovat nastavení **zdroj**.
2. Konfigurovat nastavení **cílové**. 
3. Konfigurace **upřesňující nastavení** pro operaci kopírování, jako jsou mapování sloupců, nastavení výkonu a nastavení odolnosti proti chybám. 
4. Zadejte **plán** úloh s načítáním dat. 
5. Kontrola **souhrnu** z entity služby Data Factory, který se má vytvořit. 
6. **Upravit** kanálu tak, aby podle potřeby aktualizujte nastavení aktivity kopírování. 

   Nástroj je navržená s velkými objemy dat v paměti od začátku, díky podpoře různorodých a typy objektů. Můžete ho přesunout stovky složky, soubory nebo tabulky. Nástroj podporuje automatické dat ve verzi preview, zachycení schématu a automatické mapování a filtrování dat také.

![Nástroj pro kopírování dat](./media/copy-data-tool/copy-data-tool.png)

## <a name="automatic-data-preview"></a>Automatické dat ve verzi preview
Můžete zobrazit náhled spolu s daty z vybrané zdrojového úložiště dat, která vám pomůže ověřit data, která je kopírování. Kromě toho pokud se zdrojová data jsou v textovém souboru, nástroje pro kopírování dat analyzuje textový soubor, automaticky zjišťovat oddělovače řádků a sloupců a schéma.

![Nastavení souboru](./media/copy-data-tool/file-format-settings.png)

Po zjištění:

![Nastavení zjištěného souboru a ve verzi preview](./media/copy-data-tool/after-detection.png)

## <a name="schema-capture-and-automatic-mapping"></a>Zachycení schémat a automatické mapování
Schématu zdroje dat nemůže být stejný jako schéma cíl dat v mnoha případech. V tomto scénáři budete muset namapovat sloupce ze schématu zdroje na sloupce ze schématu cíle.

Nástroje pro kopírování dat monitoruje a učí chování při mapování sloupců mezi zdrojovým a cílovým úložištěm. Po výběru jedné nebo několika sloupců ze zdrojového úložiště dat a jejich namapování na cílové schéma, spustí nástroj pro kopírování dat k analýze vzor pro dvojice sloupec, který výběru z obou stran. Potom použije stejný vzor pro zbývající sloupce. Proto se zobrazí všechny sloupce, které byly namapovány na cílovém způsobem, který chcete bezprostředně po několika kliknutími.  Pokud si nejste spokojeni s výběrem mapování sloupců, které poskytuje nástroje pro kopírování dat, můžete ji ignorovat a pokračovat s ruční mapování sloupců. Nástroje pro kopírování dat mezitím, neustále se učí a aktualizuje vzor a nakonec dosáhne správné vzor pro mapování sloupců, které chcete dosáhnout. 

> [!NOTE]
> Při kopírování dat z Azure SQL Database nebo SQL serveru do Azure SQL Data Warehouse, pokud tabulka neexistuje v cílovém úložišti, nástroje pro kopírování dat podporuje vytváření tabulky automaticky pomocí schématu zdroje. 

## <a name="filter-data"></a>Filtrování dat
Můžete filtrovat zdrojová data a vyberte pouze data, která se musí zkopírovat do úložiště dat jímky. Filtrování snižuje objem dat, které se mají zkopírovat do úložiště dat jímky a proto zvyšuje propustnost operace kopírování. Nástroj pro kopírování dat poskytuje flexibilní způsob, jak filtrovat data v relační databázi pomocí dotazovacího jazyka SQL, nebo soubory ve složce objektů blob v Azure. 

### <a name="filter-data-in-a-database"></a>Filtrování dat v databázi
Následující snímek obrazovky ukazuje dotaz SQL pro filtrování data.

![Filtrování dat v databázi](./media/copy-data-tool/filter-data-in-database.png)

### <a name="filter-data-in-an-azure-blob-folder"></a>Filtrování dat v složce objektů blob v Azure
Proměnné v cesta ke složce můžete použít ke kopírování dat ze složky. Podporované proměnné jsou: **{year}**, **{month}**, **{day}**, **{hour}**, a **{minute}**. Příklad: inputfolder / {year} / {month} / {day}. 

Předpokládejme, že mají vstupní složky v následujícím formátu: 

```
2016/03/01/01
2016/03/01/02
2016/03/01/03
...
```

Klikněte na tlačítko **Procházet** tlačítko pro **souboru nebo složky**, přejděte do jednoho z těchto složek (třeba 2016 -> 03 -> 02-01 >) a klikněte na tlačítko **zvolit**. Měli byste vidět 2016/03/01/02 v textovém poli. 

Potom nahraďte **2016** s **{year}**, **03** s **{month}**, **01** s **{day}** , a **02** s **{hour}** a stiskněte klávesu **kartu** klíč. Měli byste vidět rozevíracích seznamech vyberte formát pro tyto čtyři proměnné:

![Filtr souboru nebo složky](./media/copy-data-tool/filter-file-or-folder.png)

Nástroje pro kopírování dat generuje parametry s výrazy, funkce a systémové proměnné, které lze použít k reprezentaci {year}, {month} {day}, {hour} a {minute} při vytváření kanálu. Další informace najdete v tématu [jak číst nebo zapisovat data rozdělit na oddíly](how-to-read-write-partitioned-data.md) článku.

## <a name="scheduling-options"></a>Možnosti plánování
Operace kopírování můžete spustit jednou nebo podle plánu (každou hodinu, každý den, a tak dále). Tyto možnosti můžete použít pro konektory v různých prostředích, včetně místní, Cloudová a místní pracovní plocha. 

Operace kopírování jednorázové umožňuje přesun dat ze zdroje do cíle pouze jednou. Platí pro data libovolné velikosti a jakýkoli podporovaný formát. Plánované kopírování umožňuje kopírovat data na opakování, které zadáte. Bohaté možnosti nastavení (např. Zkuste to znovu, vypršení časového limitu a upozornění) můžete použít ke konfiguraci plánované kopírování.

![Možnosti plánování](./media/copy-data-tool/scheduling-options.png)


## <a name="next-steps"></a>Další postup
Vyzkoušejte tyto kurzy, které používají nástroje pro kopírování dat:

- [Rychlý start: vytvoření datové továrny pomocí nástroje pro kopírování dat](quickstart-create-data-factory-copy-data-tool.md)
- [Kurz: kopírování dat v Azure pomocí nástroje pro kopírování dat](tutorial-copy-data-tool.md) 
- [Kurz: kopírování místních dat do Azure pomocí nástroje pro kopírování dat](tutorial-hybrid-copy-data-tool.md)

---
title: Kopírovat datový nástroj Azure Data Factory
description: Obsahuje informace o nástroji Kopírovat data v uzu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: anandsub
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/18/2018
ms.author: yexu
ms.openlocfilehash: df078673aed60086a88961ff64f9bfa596d96346
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414090"
---
# <a name="copy-data-tool-in-azure-data-factory"></a>Nástroj Kopírování dat ve službě Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Nástroj Azure Data Factory Copy Data usnadňuje a optimalizuje proces ingestování dat do datového jezera, což je obvykle prvním krokem v komplexním scénáři integrace dat.  Šetří čas, zejména když používáte Azure Data Factory k prvnímu ingestování dat ze zdroje dat. Některé z výhod používání tohoto nástroje jsou:

- Při použití nástroje Azure Data Factory Copy Data Data Data Není nutné porozumět definicím Datové továrny pro propojené služby, datové sady, kanály, aktivity a aktivační události. 
- Tok kopírovat data nástroj je intuitivní pro načítání dat do datového jezera. Nástroj automaticky vytvoří všechny potřebné prostředky Data Factory ke kopírování dat z vybraného zdrojového úložiště dat do vybraného úložiště dat cíle/jímky. 
- Nástroj Kopírovat data pomáhá ověřit data, která jsou ingestována v době vytváření, což vám pomůže vyhnout se případným chybám na samotném začátku.
- Pokud potřebujete implementovat komplexní obchodní logiku pro načtení dat do datového jezera, můžete stále upravovat prostředky Data Factory vytvořené nástrojem Kopírovat data pomocí vytváření podle aktivity v unovém uzlině datové továrny. 

Následující tabulka obsahuje pokyny, kdy použít nástroj kopírování dat vs. vytváření na aktivitu v uzdu Factory: 

| Nástroj pro kopírování dat | Vytváření podle aktivity (aktivita kopírování) |
| -------------- | -------------------------------------- |
| Chcete snadno vytvořit úlohu načítání dat bez učení o entitách Azure Data Factory (propojené služby, datové sady, kanály atd.) | Chcete implementovat komplexní a flexibilní logiku pro načítání dat do jezera. |
| Chcete rychle načíst velký počet datových artefaktů do datového jezera. | Chcete zřetězit aktivitu kopírování s následnými aktivitami pro čištění nebo zpracování dat. |

Chcete-li spustit nástroj Kopírovat data, klepněte na dlaždici **Kopírovat data** na domovské stránce datové továrny.

![Stránka Začínáme – odkaz na nástroj Kopírovat data](./media/copy-data-tool/get-started-page.png)


## <a name="intuitive-flow-for-loading-data-into-a-data-lake"></a>Intuitivní tok pro načítání dat do datového jezera
Tento nástroj umožňuje snadno přesunout data z široké škály zdrojů do destinací během několika minut s intuitivním tokem:  

1. Konfigurace nastavení **zdroje**.
2. Konfigurace nastavení **cíle**. 
3. Nakonfigurujte **upřesňující nastavení** pro operaci kopírování, jako je mapování sloupců, nastavení výkonu a nastavení odolnosti proti chybám. 
4. Zadejte **plán** úlohy načítání dat. 
5. Zkontrolujte **souhrn** entit Factory dat, které mají být vytvořeny. 
6. Podle potřeby **upravte** kanál a aktualizujte nastavení aktivity kopírování. 

   Nástroj je navržen s ohledem na velké objemy dat od začátku s podporou různých typů dat a objektů. Můžete ji použít k přesunutí stovek složek, souborů nebo tabulek. Nástroj podporuje automatický náhled dat, sběr schématu a automatické mapování a filtrování dat.

![Nástroj pro kopírování dat](./media/copy-data-tool/copy-data-tool.png)

## <a name="automatic-data-preview"></a>Automatický náhled dat
Můžete zobrazit náhled části dat z vybraného zdrojového úložiště dat, což umožňuje ověřit data, která se kopírují. Kromě toho, pokud jsou zdrojová data v textovém souboru, nástroj Kopírovat data analyzuje textový soubor, aby automaticky detekoval oddělovače řádků a sloupců a schéma.

![Nastavení souborů](./media/copy-data-tool/file-format-settings.png)

Po zjištění:

![Zjištěné nastavení souboru a náhled](./media/copy-data-tool/after-detection.png)

## <a name="schema-capture-and-automatic-mapping"></a>Zachycení schématu a automatické mapování
Schéma zdroje dat nemusí být stejné jako schéma cíle dat v mnoha případech. V tomto scénáři je třeba mapovat sloupce ze zdrojového schématu do sloupců z cílového schématu.

Nástroj Kopírovat data monitoruje a učí se vaše chování při mapování sloupců mezi zdrojovými a cílovými úložišti. Po výběru jednoho nebo několika sloupců ze zdrojového úložiště dat a jejich mapování na cílové schéma začne nástroj Kopírovat data analyzovat vzorek pro dvojice sloupců, které jste vybrali z obou stran. Potom použije stejný vzorek na zbytek sloupců. Proto se zobrazí všechny sloupce byly mapovány na cíl způsobem, který chcete hned po několika kliknutí.  Pokud nejste spokojeni s volbou mapování sloupců poskytovanou nástrojem Kopírovat data, můžete jej ignorovat a pokračovat ručním mapováním sloupců. Mezitím nástroj Kopírovat data neustále učí a aktualizuje vzor a nakonec dosáhne správného vzoru pro mapování sloupců, kterého chcete dosáhnout. 

> [!NOTE]
> Při kopírování dat z SQL Server nebo Azure SQL Database do Azure SQL Data Warehouse, pokud tabulka neexistuje v cílovém úložišti, nástroj Kopírovat data podporuje vytvoření tabulky automaticky pomocí zdrojového schématu. 

## <a name="filter-data"></a>Filtrování dat
Můžete filtrovat zdrojová data a vybrat pouze data, která je třeba zkopírovat do úložiště dat jímky. Filtrování snižuje objem dat, která mají být zkopírována do úložiště dat jímky a proto zvyšuje propustnost operace kopírování. Nástroj Kopírovat data poskytuje flexibilní způsob filtrování dat v relační databázi pomocí dotazovacího jazyka SQL nebo souborů ve složce objektů blob Azure. 

### <a name="filter-data-in-a-database"></a>Filtrování dat v databázi
Následující snímek obrazovky ukazuje dotaz SQL pro filtrování dat.

![Filtrování dat v databázi](./media/copy-data-tool/filter-data-in-database.png)

### <a name="filter-data-in-an-azure-blob-folder"></a>Filtrování dat ve složce objektů blob Azure
Ke kopírování dat ze složky můžete použít proměnné v cestě ke složce. Podporované proměnné jsou: **{year}**, **{month}**, **{day}**, **{hour}** a **{minute}**. Příklad: inputfolder/{year}/{month}/{day}. 

Předpokládejme, že máte vstupní složky v následujícím formátu: 

```
2016/03/01/01
2016/03/01/02
2016/03/01/03
...
```

Klepněte na tlačítko **Procházet** **pro soubor nebo složku**, vyhledejte jednu z těchto složek (například 2016->03->01->02) a klepněte na tlačítko **Vybrat**. V textovém poli byste měli vidět 2016/03/01/02. 

Potom nahraďte **2016** **{year}**, **03** s **{month}**, **01** s **{day}** a **02** s **{hour}** a stiskněte klávesu **Tab.** Měli byste vidět rozevírací seznamy pro výběr formátu pro tyto čtyři proměnné:

![Filtrovat soubor nebo složku](./media/copy-data-tool/filter-file-or-folder.png)

Nástroj Kopírovat data generuje parametry s výrazy, funkcemi a systémovými proměnnými, které lze použít k reprezentaci {year}, {month}, {day}, {hour} a {minute} při vytváření kanálu.

## <a name="scheduling-options"></a>Možnosti plánování
Operaci kopírování můžete spustit jednou nebo podle plánu (každou hodinu, denně a tak dále). Tyto možnosti lze použít pro konektory v různých prostředích, včetně místní, cloud a místní desktop. 

Jednorázová operace kopírování umožňuje přesun dat ze zdroje do cíle pouze jednou. Vztahuje se na data libovolné velikosti a podporovaného formátu. Naplánovaná kopie umožňuje kopírovat data při opakování, které zadáte. K konfiguraci naplánované kopie můžete použít rozšířené nastavení (například opakování, časový čas a výstrahy).

![Možnosti plánování](./media/copy-data-tool/scheduling-options.png)


## <a name="next-steps"></a>Další kroky
Vyzkoušejte tyto kurzy, které používají nástroj Kopírovat data:

- [Úvodní příručka: vytvoření datové továrny pomocí nástroje Kopírovat data](quickstart-create-data-factory-copy-data-tool.md)
- [Kurz: kopírování dat v Azure pomocí nástroje Kopírovat data](tutorial-copy-data-tool.md) 
- [Kurz: kopírování místních dat do Azure pomocí nástroje Kopírovat data](tutorial-hybrid-copy-data-tool.md)

---
title: 'Importovat data: odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Naučte se používat modul import dat ve službě Azure Machine Learning k načtení dat do kanálu strojového učení z existujících cloudových datových služeb.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: fef7d686479b24b0402ab6f1e6990df74231b8d6
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693140"
---
# <a name="import-data-module"></a>Importovat data modul

Tento článek popisuje modul vizuálního rozhraní (Preview) pro službu Azure Machine Learning.

Pomocí tohoto modulu můžete načíst data do kanálu strojového učení z existujících cloudových datových služeb.  

Nejprve vyberte typ cloudového úložiště, ze kterého čtete, a dokončete další nastavení. Až nadefinujete požadovaná data a připojíte se ke zdroji, [importujte data](./import-data.md) datový typ každého sloupce na základě hodnot, které obsahuje, a načte data do svého pracovního prostoru Azure Machine Learning. Výstupem [importu dat](./import-data.md) je datová sada, která se dá použít s libovolným kanálem.

  
Pokud se vaše zdrojová data změní, můžete datovou sadu aktualizovat a přidat nová data tak, že znovu spustíte [importovaná data](./import-data.md). Pokud ale nechcete znovu číst ze zdroje při každém spuštění kanálu, vyberte možnost **použít výsledky v mezipaměti** na hodnotu true. Pokud je vybrána tato možnost, modul zkontroluje, zda kanál dříve běžel pomocí stejné možnosti zdroje a stejného vstupu. Pokud se najde předchozí spuštění, místo opětovného načtení dat ze zdroje se použijí data v mezipaměti.
 

## <a name="data-sources"></a>Zdroje dat

Modul Import dat podporuje následující zdroje dat. Kliknutím na odkazy zobrazíte podrobné pokyny a příklady použití jednotlivých zdrojů dat. 
 
Pokud si nejste jistí, jak nebo kam byste měli ukládat data, přečtěte si tento průvodce o běžných datových scénářích v procesu datové vědy: [scénáře pro pokročilou analýzu v Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-plan-sample-scenarios). 


|Zdroj dat| Použít s|
|-----------|-----------|  
|[Webová adresa URL přes HTTP](./import-from-web-url-via-http.md)|Získat data hostovaná na webové adrese URL, která používá protokol HTTP a která byla k dispozici ve formátech CSV, TSV, ARFF nebo SvmLight|  
|[Import z Azure Blob Storage](./import-from-azure-blob-storage.md) |Získat data uložená ve službě Azure Blob Service|  
|[Importovat z Azure SQL Database](./import-from-azure-sql-database.md) |Získat data z Azure SQL Database|

## <a name="how-to-configure-import-data"></a>Jak konfigurovat importovaná data
 
1. Přidejte do svého kanálu modul **Import dat** . Tento modul můžete najít v kategorii **vstup a výstup dat** v rozhraní.

1. Klikněte na **zdroj dat**a vyberte typ cloudového úložiště, ze kterého čtete. 

    Další nastavení závisí na zvoleném typu úložiště a na tom, jestli je úložiště zabezpečené nebo ne. Možná budete muset zadat název účtu, typ souboru nebo přihlašovací údaje. Některé zdroje nevyžadují ověřování. pro jiné možná budete muset znát název účtu, klíč nebo název kontejneru.

1. Vyberte možnost **použít výsledky v mezipaměti** , pokud chcete datovou sadu ukládat do mezipaměti pro opakované spuštění.

    Za předpokladu, že se v parametrech modulu nezměnily žádné další změny, kanál načte data pouze při prvním spuštění modulu a potom použije verzi datové sady uloženou v mezipaměti.

    Zrušte výběr této možnosti, pokud potřebujete znovu načíst data při každém spuštění kanálu.

1. Spuštění kanálu

    Když import dat načte data do rozhraní, odvodí se datový typ každého sloupce na základě hodnot, které obsahuje, buď číselného, nebo kategorií.

    - Pokud je hlavička k dispozici, záhlaví se použije k pojmenování sloupců výstupní datové sady.

    - Pokud v datech nejsou žádná existující záhlaví sloupců, generují se nové názvy sloupců pomocí formátu Sloupec1, col2,... , coln*.

## <a name="results"></a>Výsledky

Po dokončení importu klikněte na výstupní datovou sadu a vyberte **vizualizovat** , abyste viděli, jestli se data úspěšně importovala.

Pokud chcete data uložit pro opětovné použití, neimportujte novou sadu dat při každém spuštění kanálu, klikněte pravým tlačítkem na výstup a vyberte **Uložit jako datovou sadu**. Vyberte název datové sady. Uložená datová sada uchovává data v době uložení a data se po opětovném spuštění kanálu neaktualizují, i když se datová sada v kanálu změní. To může být užitečné při pořizování snímků dat.

Po importu dat možná budete potřebovat další přípravy na modelování a analýzu:


- Pomocí možnosti [Upravit metadata](./edit-metadata.md) můžete změnit názvy sloupců, zpracovat sloupec jako jiný datový typ nebo označit, že některé sloupce jsou popisky nebo funkce.

- Pomocí [Vybrat sloupce v datové sadě](./select-columns-in-dataset.md) můžete vybrat podmnožinu sloupců pro transformaci nebo použití při modelování. Transformované nebo odebrané sloupce lze snadno znovu připojit k původní datové sadě pomocí modulu [Přidat sloupce](./add-columns.md) .  

- Pomocí [oddílu a vzorku](./partition-and-sample.md) rozdělte datovou sadu, proveďte vzorkování nebo získejte horních n řádků.

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning služby. 
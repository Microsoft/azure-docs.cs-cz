---
title: 'Import dat: Odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Další informace o použití modulu Import dat ve službě Azure Machine Learning k načtení dat do experimentu machine learningu od stávajících cloudových datových služeb.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ed51c4e7b6c7d226c7827d1ba00bc96a7be1e6b0
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028302"
---
# <a name="import-data-module"></a>Import modulu dat

Tento článek popisuje modulu rozhraní visual (preview) pro službu Azure Machine Learning.

Tento modul slouží k načtení dat do experimentu machine learningu od stávajících cloudových datových služeb.  
V modulu teď nabízí průvodce můžete vybrat možnost úložiště a vybrat z existujících předplatných a účtů můžete rychle nakonfigurovat všechny možnosti. Je potřeba upravit existující datové připojení? Žádný problém; Průvodce načte všechny podrobnosti o předchozí konfiguraci tak, že není nutné znovu spustit od začátku. 
  
Po definování data a připojit ke zdroji, [Import dat](./import-data.md) odvodí typ dat každého sloupce na základě hodnot obsahuje a načte data do pracovního prostoru Azure Machine Learning. Výstup [Import dat](./import-data.md) je datová sada, která lze použít s jakékoli experimentu.

  
Pokud vaše zdrojová data mění, můžete aktualizovat datovou sadu a přidat nová data opětovným spuštěním [Import dat](./import-data.md). Nicméně, pokud už nechcete znovu načíst ze zdroje pokaždé, když spuštění experimentu, vyberte **použití mezipaměti výsledky** možnost na hodnotu TRUE. Pokud je vybraná tato možnost, modul kontroluje, zda experimentu byla spuštěna dříve pomocí stejného zdroje a stejné možnosti vstupu. Pokud se najde předchozího spuštění, se používá data v mezipaměti, namísto načíst data ze zdroje.
 

## <a name="data-sources"></a>Zdroje dat

Modul pro Import dat podporuje následující zdroje dat. Kliknutím na odkazy pro podrobné pokyny a příklady použití každý datový zdroj. 
 
Pokud si nejste jisti, jak a kde by ukládání dat, najdete v článku tohoto průvodce, abyste běžných scénářů data v vědecké zpracování dat:  [Scénáře pro pokročilé analýzy ve službě Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-plan-sample-scenarios). 


|Zdroj dat| Použití s|
|-----------|-----------|  
|[Adresa URL webu přes protokol HTTP](./import-from-web-url-via-http.md)|Získat data, která je hostovaná na adresu URL webu, který používá protokol HTTP a, který byl poskytnut ve formátu CSV, TSV, ARFF nebo SvmLight|  
|[Import ze služby Azure Blob Storage](./import-from-azure-blob-storage.md) |Získat data, která je uložená ve službě Azure blob service|  

## <a name="how-to-use-import-data"></a>Jak používat Import dat
 
1. Přidat **Import dat** modulu do experimentu. V tomto modulu můžete najít **datový vstup a výstup** kategorie v rozhraní.

2. Klikněte na tlačítko **spusťte Průvodce importem dat** konfigurace zdroje dat pomocí průvodce.

    Průvodce získá název účtu a přihlašovací údaje a pomáhají konfigurovat další možnosti. Pokud upravujete stávající konfigurace, načte aktuální hodnoty první.

3. Pokud nechcete použít průvodce, klikněte na tlačítko **zdroj dat**a zvolte typ při čtení z cloudového úložiště. 

    Další nastavení závisí na typ úložiště zvolíte, a zda je nebo není zabezpečené úložiště. Může být potřeba zadat název účtu, typ souboru nebo přihlašovací údaje. Některé zdroje nevyžadují ověřování pro ostatní uživatele můžete potřebovat znát název účtu, klíč nebo název kontejneru.

4. Vyberte **použití mezipaměti výsledky** možnost, pokud chcete datovou sadu pro další použití v po sobě jdoucích spuštění do mezipaměti.

    Za předpokladu, že nebyly žádné další změny parametry modulu, experiment načte data pouze první čas modulu je spuštěn a poté používá verze uložené v mezipaměti datové sady.

    Tuto volbu použijte, pokud je potřeba znovu načíst data při každém spuštění testu.

5. Spusťte experiment.

    Při importu dat načte data do rozhraní, odvodí typ dat každého sloupce, podle hodnoty, které obsahuje, číselné nebo kategorií.

    - Pokud se nachází záhlaví, záhlaví se používá k sloupce výstupní datovou sadu.

    - Pokud nejsou žádné existující záhlaví sloupce v datech, nové názvy sloupců jsou generovány pomocí formátu Sloupec1, Sloupec2... , coln *.

## <a name="results"></a>Výsledky

Po dokončení importu, klikněte na výstupní datovou sadu a vyberte **vizualizovat** zobrazíte, pokud data byla úspěšně importována.

Pokud chcete uložit data pro opakované použití, ne importovat nové sady dat pokaždé, když spuštění experimentu, klikněte pravým tlačítkem na výstupu a vyberte **uložit jako datovou sadu**. Vyberte název datové sady. Uložené datová sada zachová data v době ukládání a dat není aktualizován při experiment znovu spuštěn, i když se změní datovou sadu v experimentu. To může být užitečná pro pořizování snímků dat.

Po importu dat, může být nutné některé další přípravy pro modelování a analýzy:


- Použití [upravit Metadata](./edit-metadata.md) změnit názvy sloupců, zpracování sloupců jako jiný datový typ, nebo označuje, že některé sloupce je popisky nebo funkce.

- Použít [výběr sloupců v datové sadě](./select-columns-in-dataset.md) vybrat podmnožinu sloupců, aby transformoval a použít v modelu. Transformovaná nebo odebrání sloupců můžete snadno se znovu vstoupit do původní datové sady s použitím [přidat sloupce](./add-columns.md) modulu.  

- Použití [rozdělení a vzorky](./partition-and-sample.md) rozdělit datovou sadu, provádět vzorkování nebo získat prvních n řádků.

## <a name="next-steps"></a>Další postup

Zobrazit [sada modulů, které jsou k dispozici](module-reference.md) do služby Azure Machine Learning. 
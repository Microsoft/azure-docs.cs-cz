---
title: Importovat z Azure SQL Database
titleSuffix: Azure Machine Learning service
description: Naučte se používat modul import dat v Azure Machine Learningm vizuálním rozhraní k získání dat z Azure SQL Database.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 374776088d93813f39122f2018b00466c55d2b6e
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2019
ms.locfileid: "72694607"
---
# <a name="import-from-azure-sql-database"></a>Importovat z Azure SQL Database

Tento článek popisuje, jak použít modul [importovat data](import-data.md) v Azure Machine Learning vizuální rozhraní k získání dat z Azure SQL Database.  

Chcete-li importovat data z databáze, je nutné zadat název serveru i název databáze a příkaz jazyka SQL definující tabulku, zobrazení nebo dotaz.  

Obecně platí, že ukládání dat v databázích Azure je dražší než používání tabulek nebo objektů BLOB v Azure. V závislosti na typu předplatného se můžou také omezit množství dat, která se dají ukládat v databázi. U SQL Azureé databáze se ale neúčtují žádné poplatky za transakce, takže tato možnost je ideální pro rychlý přístup k menšímu množství často používaných informací, jako jsou tabulky vyhledávání dat nebo datové slovníky.

Ukládání dat v databázi Azure je také preferované, pokud potřebujete být schopni filtrovat data před jejich čtením, nebo pokud chcete uložit předpovědi nebo metriky zpět do databáze pro vytváření sestav.

## <a name="how-to-import-data-from-azure-sql-database"></a>Import dat z Azure SQL Database

1. Přidejte do svého kanálu modul [Import dat](import-data.md) . Tento modul můžete najít ve vizuálním rozhraní v kategorii vstup a výstup dat.

1. V případě **zdroje dat**vyberte možnost **Azure SQL Database**.

1. Nastavte následující možnosti, které jsou specifické pro Azure SQL Database.

    **Název databázového serveru**: zadejte název serveru, který je vygenerovaný Azure. Obvykle má `<generated_identifier>.database.windows.net` formuláře.

    **Název databáze**: zadejte název existující databáze na serveru, který jste zadali.

    **Název uživatelského účtu serveru**: zadejte uživatelské jméno účtu, který má oprávnění pro přístup k databázi.

    **Heslo k uživatelskému účtu serveru**: zadejte heslo pro zadaný uživatelský účet.

    **Databázový dotaz**: zadejte nebo vložte příkaz SQL, který popisuje data, která chcete číst. Vždy ověřte příkaz SQL a ověřte výsledky dotazu předem pomocí nástroje, jako je například Visual Studio Průzkumník serveru nebo SQL Server Data Tools.

1. Pokud se pro datovou sadu, kterou načtete Azure Machine Learning, neočekává mezi spuštěním kanálu, vyberte možnost **použít výsledky v mezipaměti** .

    Pokud je vybrána možnost neexistují žádné jiné změny parametrů modulu, kanál při prvním spuštění modulu načte data a potom použije verzi datové sady uloženou v mezipaměti.

    Pokud chcete datovou sadu znovu načíst pro každou iteraci kanálu, zrušte výběr této možnosti. Datová sada se znovu načte ze zdroje pokaždé, když se v [importu dat](import-data.md)změní parametry.

1. Spuštění kanálu

    Jelikož [Import dat](import-data.md) načte data do vizuálního rozhraní, může se v závislosti na typech dat použitých ve zdrojové databázi provést i některé implicitní převody typu.

## <a name="results"></a>Výsledky

Po dokončení importu klikněte na výstupní datovou sadu a vyberte **vizualizovat** , abyste viděli, jestli se data úspěšně importovala.

Volitelně můžete datovou sadu a její metadata změnit pomocí nástrojů v rámci vizuálního rozhraní:

- Pomocí možnosti [Upravit metadata](edit-metadata.md) Změňte názvy sloupců, převeďte sloupec na jiný datový typ nebo určete, které sloupce jsou popisky nebo funkce.

- Pomocí [Vybrat sloupce v datové sadě](select-columns-in-dataset.md) vyberte podmnožinu sloupců.

- Pomocí [oddílu a vzorku](partition-and-sample.md) oddělte datovou sadu podle kritérií nebo získejte horních n řádků.

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning služby. 

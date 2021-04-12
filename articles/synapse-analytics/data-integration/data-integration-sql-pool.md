---
title: Ingestování dat do vyhrazeného fondu SQL
description: Naučte se ingestovat data do vyhrazeného fondu SQL ve službě Azure synapse Analytics.
services: synapse-analytics
author: djpmsft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 11/03/2020
ms.author: daperlov
ms.reviewer: jrasnick
ms.openlocfilehash: a02abff712fc26f653307108fcc3bb284444d0c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101676643"
---
# <a name="ingest-data-into-a-dedicated-sql-pool"></a>Ingestování dat do vyhrazeného fondu SQL

V tomto článku se dozvíte, jak ingestovat data z Azure Data Lake účtu úložiště Gen 2 do vyhrazeného fondu SQL ve službě Azure synapse Analytics.

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure**: Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.
* **Účet úložiště Azure**: Azure Data Lake Storage Gen 2 použijete jako *zdrojové* úložiště dat. Pokud nemáte účet úložiště, přečtěte si článek [vytvoření Azure Storage účtu](../../storage/common/storage-account-create.md) , kde najdete kroky pro jeho vytvoření.
* **Azure synapse Analytics**: jako úložiště dat *jímky* použijete vyhrazený fond SQL. Pokud nemáte instanci Azure synapse Analytics, přečtěte si téma [vytvoření vyhrazeného fondu SQL](../../azure-sql/database/single-database-create-quickstart.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) , kde najdete kroky pro jeho vytvoření.

## <a name="create-linked-services"></a>Vytvoření propojených služeb

V Azure synapse Analytics je propojená služba, kde můžete definovat informace o připojení k ostatním službám. V této části přidáte propojenou službu Azure synapse Analytics a Azure Data Lake Storage Gen2.

1. Otevřete uživatelské prostředí Azure synapse Analytics a na kartě **Spravovat** .
1. V části **externí připojení** vyberte **propojené služby**.
1. Chcete-li přidat propojenou službu, vyberte možnost **Nový**.
1. V seznamu Vyberte dlaždici Azure Data Lake Storage Gen2 a vyberte **pokračovat**.
1. Zadejte přihlašovací údaje pro ověření. Typy ověřování aktuálně podporují klíč účtu, instanční objekt a spravovanou identitu. Vyberte test připojení a ověřte správnost vašich přihlašovacích údajů. Po dokončení vyberte **Vytvořit**.
1. Opakujte kroky 3-5, ale místo Azure Data Lake Storage Gen2 vyberte dlaždici Azure synapse Analytics a zadejte do odpovídajících přihlašovacích údajů pro připojení. Pro Azure synapse Analytics se v současné době podporuje ověřování SQL, spravovaná identita a instanční objekt.

## <a name="create-pipeline"></a>Vytvoření kanálu

Kanál obsahuje logický tok pro spuštění sady aktivit. V této části vytvoříte kanál s aktivitou kopírování, která ingestuje data z ADLS Gen2 do vyhrazeného fondu SQL.

1. Přejít na kartu **integrace** . Vyberte ikonu plus vedle záhlaví kanály a vyberte **kanál**.
1. V části **přesunout a transformovat** v podokně aktivity přetáhněte **Kopírovat data** na plátno kanálu.
1. Vyberte aktivitu kopírování a přejít na kartu **zdroj** . Pokud chcete vytvořit novou zdrojovou datovou sadu, vyberte **Nový** .
1. Jako úložiště dat vyberte Azure Data Lake Storage Gen2 a vyberte pokračovat.
1. Jako formát vyberte DelimitedText a vyberte pokračovat.
1. V podokně nastavit vlastnosti vyberte propojenou službu ADLS, kterou jste vytvořili. Zadejte cestu k souboru zdrojových dat a určete, zda má první řádek záhlaví. Schéma můžete importovat z úložiště souborů nebo z ukázkového souboru. Po dokončení vyberte OK.
1. Přejít na kartu **jímka** . Vyberte **Nový** , chcete-li vytvořit novou datovou sadu jímky.
1. Jako úložiště dat vyberte Azure synapse Analytics a vyberte pokračovat.
1. V podokně nastavit vlastnosti vyberte propojenou službu Azure synapse Analytics, kterou jste vytvořili. Pokud píšete do existující tabulky, vyberte ji z rozevíracího seznamu. V opačném případě klikněte na příkaz **Upravit** a zadejte nový název tabulky. Po dokončení vyberte OK.
1. Pokud vytváříte tabulku, povolte **automaticky vytvořit tabulku** v poli možnost tabulky.

## <a name="debug-and-publish-pipeline"></a>Ladění a publikování kanálu

Po dokončení konfigurace kanálu můžete spustit ladění před publikováním artefaktů, abyste ověřili, jestli je vše správné.

1. K ladění kanálu vyberte na panelu nástrojů **Ladit**. Na kartě **Výstup** v dolní části okna se zobrazí stav spuštění kanálu. 
1. Po úspěšném spuštění kanálu klikněte na horním panelu nástrojů na **publikovat vše**. Tato akce publikuje entity (datové sady a kanály), které jste vytvořili ve službě synapse Analytics.
1. Počkejte, dokud se nezobrazí zpráva **Publikování proběhlo úspěšně**. Pokud chcete zobrazit oznamovací zprávy, vyberte v pravém horním rohu tlačítko zvonku. 


## <a name="trigger-and-monitor-the-pipeline"></a>Aktivace a monitorování kanálu

V tomto kroku ručně aktivujete kanál publikovaný v předchozím kroku. 

1. Vyberte **Přidat aktivační událost** na panelu nástrojů a pak vyberte **aktivovat nyní**. Na stránce **Spuštění kanálu** vyberte **Dokončit**.  
1. Přejít na kartu **monitorování** umístěnou na levém bočním panelu. Zobrazí se stav ručně aktivovaného spuštění kanálu. Pomocí odkazů ve sloupci **Akce** můžete zobrazit podrobnosti o aktivitě a spustit kanál znovu.
1. Pokud se chcete podívat na spuštění aktivit, která souvisí se spuštěním kanálu, vyberte odkaz **Zobrazit spuštění aktivit** ve sloupci **Akce**. V tomto příkladu je k dispozici pouze jedna aktivita, takže se v seznamu zobrazí pouze jedna položka. Podrobnosti o operaci kopírování zobrazíte výběrem odkazu **Podrobnosti** (ikona brýlí) ve sloupci **Akce**. Vyberte možnost **spuštění kanálu** v horní části a vraťte se do zobrazení spuštění kanálu. Jestliže chcete zobrazení aktualizovat, vyberte **Aktualizovat**.
1. Ověřte, že vaše data jsou správně napsaná ve vyhrazeném fondu SQL.


## <a name="next-steps"></a>Další kroky

Další informace o integraci dat pro Azure synapse Analytics najdete v článku ingestování [dat do Azure Data Lake Storage Gen2 ](data-integration-data-lake.md) článku.
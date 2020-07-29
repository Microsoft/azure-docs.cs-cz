---
title: Ingestování do Azure Data Lake Storage Gen2
description: Naučte se ingestovat data do Azure Data Lake Storage Gen2 ve službě Azure synapse Analytics
services: synapse-analytics
author: djpmsft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: daperlov
ms.reviewer: jrasnick
ms.openlocfilehash: 7b844bcf45417fefc7dd78a26d5dae0b2ce03249
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "82982900"
---
# <a name="ingesting-data-into-azure-data-lake-storage-gen2"></a>Ingestování dat do Azure Data Lake Storage Gen2 

V tomto článku se dozvíte, jak ingestovat data z jednoho umístění do druhého v účtu úložiště Azure Data Lake Gen 2 (Azure Data Lake Gen 2) pomocí služby Azure synapse Analytics.

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure**: Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.
* **Účet Azure Storage**: Azure Data Lake Gen 2 použijete jako *zdrojové* úložiště dat. Pokud nemáte účet úložiště, přečtěte si článek [vytvoření Azure Storage účtu](../../storage/blobs/data-lake-storage-quickstart-create-account.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) , kde najdete kroky pro jeho vytvoření.

## <a name="create-linked-services"></a>Vytvoření propojených služeb

V Azure synapse Analytics je propojená služba, kde můžete definovat informace o připojení k ostatním službám. V této části přidáte Azure synapse Analytics a Azure Data Lake Gen 2 jako propojené služby.

1. Otevřete uživatelské prostředí Azure synapse Analytics a na kartě **Spravovat** .
1. V části **externí připojení**vyberte **propojené služby**.
1. Chcete-li přidat propojenou službu, klikněte na tlačítko **Nový**.
1. V seznamu Vyberte dlaždici Azure Data Lake Storage Gen2 a klikněte na **pokračovat**.
1. Zadejte přihlašovací údaje pro ověření. Typy ověřování aktuálně podporují klíč účtu, instanční objekt a spravovanou identitu. Kliknutím na test připojení ověřte správnost vašich přihlašovacích údajů. 
1. Po dokončení klikněte na **vytvořit** .

## <a name="create-pipeline"></a>Vytvoření kanálu

Kanál obsahuje logický tok pro spuštění sady aktivit. V této části vytvoříte kanál s aktivitou kopírování, která ingestuje data z Azure Data Lake Gen 2 do fondu SQL.

1. Přejděte na kartu **Orchestration** . klikněte na ikonu plus vedle záhlaví kanály a vyberte **kanál**.
1. V části **přesunout a transformovat** v podokně aktivity přetáhněte **Kopírovat data** na plátno kanálu.
1. Klikněte na aktivitu kopírování a přejděte na kartu **zdroj** . Kliknutím na **Nový** vytvořte novou zdrojovou datovou sadu.
1. Jako úložiště dat vyberte Azure Data Lake Storage Gen2 a klikněte na pokračovat.
1. Jako formát vyberte DelimitedText a klikněte na pokračovat.
1. V podokně nastavit vlastnosti vyberte propojenou službu ADLS, kterou jste vytvořili. Zadejte cestu k souboru zdrojových dat a určete, zda má první řádek záhlaví. Schéma můžete importovat z úložiště souborů nebo z ukázkového souboru. Po dokončení klikněte na OK.
1. Přejděte na kartu **jímka** . Kliknutím na **Nový** vytvořte novou datovou sadu jímky.
1. Jako úložiště dat vyberte Azure Data Lake Storage Gen2 a klikněte na pokračovat.
1. Jako formát vyberte DelimitedText a klikněte na pokračovat.
1. V podokně nastavit vlastnosti vyberte propojenou službu ADLS, kterou jste vytvořili. Zadejte cestu ke složce, do které chcete zapisovat data. Po dokončení klikněte na OK.

## <a name="debug-and-publish-pipeline"></a>Ladění a publikování kanálu

Po dokončení konfigurace kanálu můžete spustit ladění před publikováním artefaktů, abyste ověřili, jestli je vše správné.

1. K ladění kanálu vyberte na panelu nástrojů **Ladit**. Na kartě **Výstup** v dolní části okna se zobrazí stav spuštění kanálu. 
1. Po úspěšném spuštění kanálu klikněte na horním panelu nástrojů na **publikovat vše**. Tato akce publikuje entity (datové sady a kanály), které jste vytvořili ve službě synapse Analytics.
1. Počkejte, dokud se nezobrazí zpráva **Publikování proběhlo úspěšně**. Chcete-li zobrazit oznamovací zprávy, klikněte na tlačítko zvonku vpravo nahoře. 


## <a name="trigger-and-monitor-the-pipeline"></a>Aktivace a monitorování kanálu

V tomto kroku ručně aktivujete kanál publikovaný v předchozím kroku. 

1. Vyberte **Přidat aktivační událost** na panelu nástrojů a pak vyberte **aktivovat nyní**. Na stránce **Spuštění kanálu** vyberte **Dokončit**.  
1. Přejít na kartu **monitorování** umístěnou na levém bočním panelu. Zobrazí se stav ručně aktivovaného spuštění kanálu. Pomocí odkazů ve sloupci **Akce** můžete zobrazit podrobnosti o aktivitě a spustit kanál znovu.
1. Pokud se chcete podívat na spuštění aktivit, která souvisí se spuštěním kanálu, vyberte odkaz **Zobrazit spuštění aktivit** ve sloupci **Akce**. V tomto příkladu je k dispozici pouze jedna aktivita, takže se v seznamu zobrazí pouze jedna položka. Podrobnosti o operaci kopírování zobrazíte výběrem odkazu **Podrobnosti** (ikona brýlí) ve sloupci **Akce**. Vyberte možnost **spuštění kanálu** v horní části a vraťte se do zobrazení spuštění kanálu. Jestliže chcete zobrazení aktualizovat, vyberte **Aktualizovat**.
1. Ověřte, že vaše data jsou ve fondu SQL správně napsaná.


## <a name="next-steps"></a>Další kroky

Další informace o integraci dat pro synapse Analytics najdete v článku ingestování [dat do fondu SQL](data-integration-sql-pool.md) .

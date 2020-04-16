---
title: Ingestování do Azure Data Lake Storage Gen2 v Azure Synapse Analytics
description: Zjistěte, jak ingestovat data do Azure Data Lake Storage Gen2 v Azure Synapse Analytics
services: synapse-analytics
author: djpmsft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: daperlov
ms.reviewer: jrasnick
ms.openlocfilehash: 4d7d7be523749797e5dbce0e50c307fc682974f2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430575"
---
# <a name="ingesting-data-into-azure-data-lake-storage-gen2"></a>Ingestování dat do Azure Data Lake Storage Gen2 

V tomto článku se dozvíte, jak ingestovat data z jednoho umístění do druhého v účtu úložiště Azure Data Lake Gen 2 (Azure Data Lake Gen 2) pomocí Azure Synapse Analytics.

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure**: Pokud nemáte předplatné Azure, vytvořte [si bezplatný účet Azure,](https://azure.microsoft.com/free/) než začnete.
* **Účet Azure Storage**: Azure Data Lake Gen 2 používáte jako *zdrojové* úložiště dat. Pokud nemáte účet úložiště, najdete v [tématu vytvoření účtu Azure Storage](../../storage/blobs/data-lake-storage-quickstart-create-account.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) pro kroky k jeho vytvoření.

## <a name="create-linked-services"></a>Vytvoření propojených služeb

V Azure Synapse Analytics, propojené služby je místo, kde definujete informace o připojení k jiným službám. V této části přidáte Azure Synapse Analytics a Azure Data Lake Gen 2 jako propojené služby.

1. Otevřete u UX Azure Synapse Analytics a přejděte na kartu **Spravovat.**
1. V části **Externí připojení**vyberte **Propojené služby**.
1. Chcete-li přidat propojenou službu, klepněte na tlačítko **Nový**.
1. Vyberte dlaždici Azure Data Lake Storage Gen2 ze seznamu a klikněte na **Pokračovat**.
1. Zadejte ověřovací pověření. Klíč účtu, instanční objekt a spravovaná identita jsou aktuálně podporované typy ověřování. Chcete-li ověřit správnost pověření, klepněte na tlačítko Testovat připojení. 
1. Po dokončení klikněte na **Vytvořit.**

## <a name="create-pipeline"></a>Vytvoření kanálu

Kanál obsahuje logický tok pro provádění sady aktivit. V této části vytvoříte kanál obsahující aktivitu kopírování, která ingestuje data z Azure Data Lake Gen 2 do fondu SQL.

1. Přejděte na kartu **Orchestrovat.** Klikněte na ikonu plus vedle záhlaví kanálů a vyberte **Pipeline**.
1. V části **Přesunout a transformovat** v podokně aktivit **přetáhněte kopírovat data** na plátno kanálu.
1. Klikněte na aktivitu kopírování a přejděte na kartu **Zdroj.** **New**
1. Vyberte Azure Data Lake Storage Gen2 jako úložiště dat a klikněte na pokračovat.
1. Jako formát vyberte Oddělovač textu a klepněte na pokračovat.
1. V podokně vlastností sady vyberte propojenou službu ADLS, kterou jste vytvořili. Určete cestu k souboru zdrojových dat a určete, zda má první řádek záhlaví. Schéma můžete importovat z úložiště souborů nebo z ukázkového souboru. Po dokončení klepněte na tlačítko OK.
1. Přejděte na kartu **Jímka.** Kliknutím na **Nový** vytvořte novou datovou sadu jímky.
1. Vyberte Azure Data Lake Storage gen2 jako úložiště dat a klikněte na pokračovat.
1. Jako formát vyberte Oddělovač textu a klepněte na pokračovat.
1. V podokně vlastností sady vyberte propojenou službu ADLS, kterou jste vytvořili. Zadejte cestu ke složce, do které chcete zapisovat data. Po dokončení klepněte na tlačítko OK.

## <a name="debug-and-publish-pipeline"></a>Ladění a publikování kanálu

Po dokončení konfigurace kanálu můžete před publikováním artefaktů spustit ladění a ověřit, zda je vše správné.

1. K ladění kanálu vyberte na panelu nástrojů **Ladit**. Na kartě **Výstup** v dolní části okna se zobrazí stav spuštění kanálu. 
1. Jakmile bude kanál úspěšně spuštěn, vyberte v horním panelu nástrojů **možnost Publikovat vše**. Tato akce publikuje entity (datové sady a kanály), které jste vytvořili ve službě Synapse Analytics.
1. Počkejte, dokud se nezobrazí zpráva **Publikování proběhlo úspěšně**. Chcete-li zobrazit oznámení, klikněte na tlačítko zvonek v pravém horním rohu. 


## <a name="trigger-and-monitor-the-pipeline"></a>Spuštění a sledování potrubí

V tomto kroku ručně aktivujete kanál publikovaný v předchozím kroku. 

1. Na panelu nástrojů vyberte **Přidat aktivační událost** a pak vyberte **Aktivovat nyní**. Na stránce **Spuštění kanálu** vyberte **Dokončit**.  
1. Přejděte na kartu **Monitor** umístěnou na levém postranním panelu. Zobrazí se stav ručně aktivovaného spuštění kanálu. Pomocí odkazů ve sloupci **Akce** můžete zobrazit podrobnosti o aktivitě a spustit kanál znovu.
1. Pokud se chcete podívat na spuštění aktivit, která souvisí se spuštěním kanálu, vyberte odkaz **Zobrazit spuštění aktivit** ve sloupci **Akce**. V tomto příkladu je pouze jedna aktivita, takže se v seznamu zobrazí pouze jedna položka. Podrobnosti o operaci kopírování zobrazíte výběrem odkazu **Podrobnosti** (ikona brýlí) ve sloupci **Akce**. Vyberte **Spuštění kanálu** v horní části a vraťte se do zobrazení Spuštění kanálu. Jestliže chcete zobrazení aktualizovat, vyberte **Aktualizovat**.
1. Ověřte, zda jsou data správně zapsána ve fondu SQL.


## <a name="next-steps"></a>Další kroky

Další informace o integraci dat pro Synapse Analytics najdete v tématu [ingestování dat do](data-integration-sql-pool.md) článku fondu SQL.

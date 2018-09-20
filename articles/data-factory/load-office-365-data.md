---
title: Načtení dat z Office 365 pomocí služby Azure Data Factory | Dokumentace Microsoftu
description: Použití Azure Data Factory pro kopírování dat z Office 365
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: jingwang
ms.openlocfilehash: db002f152fb792ab1327cba3487021c567f39260
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46368634"
---
# <a name="load-data-from-office-365-by-using-azure-data-factory"></a>Načtení dat z Office 365 pomocí služby Azure Data Factory

V tomto článku se dozvíte, jak pomocí služby Data Factory _načtěte data z Office 365 do úložiště objektů Blob v Azure_. Můžete postupovat podle podobných kroků ke zkopírování dat do Azure Data Lake Gen1 a Gen2. Odkazovat na [článku konektor Office 365](connector-office-365.md) na kopírování dat z Office 365 obecně.

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. V nabídce vlevo vyberte **nový** > **Data a analýzy** > **služby Data Factory**: 
   
   ![Vytvoření nové datové továrny](./media/load-office-365-data/new-azure-data-factory-menu.png)
2. V **nová datová továrna** zadejte hodnoty pro pole, která jsou zobrazena na následujícím obrázku:
      
   ![Stránka Nová datová továrna](./media/load-office-365-data/new-azure-data-factory.png)
 
    * **Název**: Zadejte globálně jedinečný název pro službu Azure data factory. Pokud se zobrazí chyba "název objektu pro vytváření dat \"LoadFromOffice365Demo\" není k dispozici," Zadejte jiný název datové továrny. Můžete například použít název  _**vaše_jméno**_**LoadFromOffice365Demo**. Zkuste znovu vytvořit datovou továrnu. Pravidla pojmenování artefaktů služby Data Factory najdete v tématu [Data Factory – pravidla pojmenování](naming-rules.md).
    * **Předplatné**: vyberte své předplatné Azure, ve kterém chcete datovou továrnu vytvořit. 
    * **Skupina prostředků**: z rozevíracího seznamu vyberte existující skupinu prostředků, nebo **vytvořit nový** možnost a zadejte název skupiny prostředků. Informace o skupinách prostředků najdete v článku [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/resource-group-overview.md).  
    * **Verze**: vyberte **V2**.
    * **Umístění**: Vyberte umístění datové továrny. V rozevíracím seznamu se zobrazí pouze podporovaná umístění. Úložiště dat, které jsou používané datovou továrnou můžou být v jiných umístěních a oblastech. Úložiště těchto dat patří Azure Data Lake Store, Azure Storage, Azure SQL Database a tak dále.

3. Vyberte **Vytvořit**.
4. Po vytvoření se, přejděte do služby data factory. Zobrazí **služby Data Factory** domovskou stránku, jak je znázorněno na následujícím obrázku:
   
   ![Domovská stránka datové továrny](./media/load-office-365-data/data-factory-home-page.png)

5. Vyberte **vytvořit a monitorovat** dlaždice aplikace pro integraci dat v na samostatné kartě.

## <a name="create-a-pipeline"></a>Vytvoření kanálu

1. Na stránce "Začínáme" vyberte **vytvořit kanál**.
 
    ![Vytvoření kanálu](./media/load-office-365-data/create-pipeline-entry.png)

2. V **kartu Obecné** kanálu zadejte "CopyPipeline" **název** kanálu.

3. V panelu nástrojů aktivity > přesouvat a transformovat kategorie > přetáhnout myší **aktivita kopírování** z panelu nástrojů na plochu návrháře kanálu. Zadejte "CopyFromOffice365ToBlob" jako název aktivity.

### <a name="configure-source"></a>Konfigurace zdroje

1. Přejděte do kanálu > **kartu zdroj**, klikněte na tlačítko **+ nová** vytvořte zdrojovou datovou sadu. 

2. V okně Nová datová sada vyberte **Office 365**a pak vyberte **Dokončit**.

    ![Nová datová sada Office 365](./media/load-office-365-data/new-office-365-dataset.png)
 
3. Se zobrazí na nové kartě otevřít pro datovou sadu Office 365. Na **kartu Obecné** v dolní části okna vlastnosti, zadejte "SourceOffice365Dataset" pro název.

    ![Datová sada konfigurace Office 365, obecné](./media/load-office-365-data/config-office-365-dataset-general.png)
 
4. Přejděte **kartu připojení** v okně Vlastnosti. Vedle textového pole propojené služby, klikněte na tlačítko **+ nová**.
 
    ![Připojení k datové sadě konfigurace Office 365](./media/load-office-365-data/config-office-365-dataset-connection.png)

5. V okně Nová propojená služba jako název zadejte "Office365LinkedService", zadejte ID instančního objektu a klíč instančního objektu a pak vyberte Uložit na propojenou službu nasadíte.

    ![Nová služba propojená Office 365](./media/load-office-365-data/new-office-365-linked-service.png)
 
6. Po vytvoření propojené služby se vrátíte do nastavení datové sady. Vedle "Table" zvolte šipku dolů a rozbalte seznam dostupných datových sad Office 365 a zvolte možnost "BasicDataSet_v0. Contact_v0 "z rozevíracího seznamu:

    ![Datová sada tabulky konfigurace Office 365](./media/load-office-365-data/config-office-365-dataset-table.png)
 
7. Přejděte **karty schématu** okno vlastností a vyberte **importovat schéma**.  Všimněte si, že se zobrazí schémat a ukázkové hodnoty pro datovou sadu kontaktu.

    ![Konfigurace Office 365 schéma datové sady](./media/load-office-365-data/config-office-365-dataset-schema.png)

8. Nyní přejděte zpět do kanálu > Karta zdroje, zkontrolujte, že je vybrané SourceBlobDataset.
 
### <a name="configure-sink"></a>Konfigurace jímky

1. Přejděte do kanálu > **karta jímka**a vyberte **+ nová** vytvořte datovou sadu jímky.
 
2. V okně Nová datová sada Všimněte si, že pouze podporovaného cíle jsou vybrána při kopírování z Office 365. Vyberte **Azure Blob Storage**a pak vyberte **Dokončit**.  V tomto kurzu zkopírujte data Office 365 do služby Azure Blob Storage.

    ![Nová datová sada objektu Blob](./media/load-office-365-data/new-blob-dataset.png)

4. Na **kartu Obecné** okna vlastností, do pole Název zadejte "OutputBlobDataset".

5. Přejděte **kartu připojení** v okně Vlastnosti. Vedle textového pole propojenou službu vyberte **+ nová**.

    ![Připojení k datové sadě objektů Blob v konfiguraci](./media/load-office-365-data/config-blob-dataset-connection.png) 

6. V okně Nová propojená služba jako název zadejte "AzureStorageLinkedService", vyberte "Service Principal" z rozevíracího seznamu metod ověřování, zadejte koncový bod služby, ID objektu zabezpečení Tenanta služby a klíč instančního objektu služby a vyberte Uložit do Nasaďte propojenou službu.  Přečtěte si [tady](connector-azure-blob-storage.md#service-principal-authentication) pro nastavení ověřování instančních objektů Azure Blob Storage.

    ![Nová služba propojená objektů Blob](./media/load-office-365-data/new-blob-linked-service.png)

7. Po vytvoření propojené služby se vrátíte do nastavení datové sady. Vedle cesta k souboru, vyberte **Procházet** výstupní složku, ve kterém budou extrahována data Office 365 na vybrat.  V části "Nastavení formátu souboru", vedle formát souboru, zvolte možnost "**formátu JSON**" a vedle vzor souborů, zvolte možnost "**sadu objektů**".

    ![Cesta objektu Blob konfigurace datové sady a formátu](./media/load-office-365-data/config-blob-dataset-path-and-format.png) 

8. Vraťte se do kanálu > karta jímka, zkontrolujte, že je vybrané OutputBlobDataset.

## <a name="validate-the-pipeline"></a>Ověření kanálu

Vyberte **Ověřit** z panelu nástrojů a kanál ověřte.

Uvidíte také kód JSON přidružený k kanálu klepnutím na kód v pravém horním rohu.

## <a name="publish-the-pipeline"></a>Publikování kanálu

V horním panelu nástrojů vyberte **Publikovat vše**. Touto akcí publikujete vytvořené entity (datové sady a kanály) do služby Data Factory.

![Publikovat změny](./media/load-office-365-data/publish-changes.png) 

## <a name="trigger-the-pipeline-manually"></a>Ruční aktivace kanálu

Vyberte na panelu nástrojů **Aktivační událost** a potom vyberte **Aktivovat**. Na stránce spuštění kanálu, vyberte **Dokončit**. 

## <a name="monitor-the-pipeline"></a>Monitorování kanálu

Přejděte **kartu monitorování** na levé straně. Zobrazí se stav ručně aktivovaného spuštění kanálu. Pomocí odkazů ve **sloupci Akce** zobrazíte podrobnosti o aktivitě a spustit kanál znovu.

![Monitorování kanálu](./media/load-office-365-data/pipeline-monitoring.png) 

Pokud chcete zobrazit spuštění aktivit související se spuštěním kanálu, vyberte **zobrazit spuštění aktivit** odkaz ve sloupci Akce. Tento příklad obsahuje pouze jednu aktivitu, takže se v seznamu zobrazí pouze jedna položka. Podrobnosti o operaci kopírování, vyberte **odkaz podrobnosti (ikona brýlí)** ve sloupci Akce.

![Monitorování aktivity](./media/load-office-365-data/activity-monitoring.png) 

Pokud je to poprvé, kdy požadujete data pro tento kontext (kombinace dat, které tabulky se přístup, které cílového účtu je načítají do data a které identity uživatele je vidět data žádost o přístup), zobrazí se aktivita kopírování stav jako "**probíhá**", a pouze v případě, že kliknete na odkaz "Details" v části Akce se zobrazí stav jako "**RequesetingConsent**".  Člen skupiny schvalovatel přístupu dat musí schválit žádost v Privileged Access Management extrakce dat mohl pokračovat.

_Stav jako žádost o souhlas:_
![podrobnosti o spuštění aktivit – žádost o souhlas](./media/load-office-365-data/activity-details-request-consent.png) 

_Stav jako extrahování dat:_

![Podrobnosti o spuštění aktivit – extrahování dat](./media/load-office-365-data/activity-details-extract-data.png) 

Jakmile souhlasu je k dispozici extrakce dat bude pokračovat a po nějaké době, se zobrazí spuštění kanálu jako dokončené.

![Monitorování kanálu – úspěšné](./media/load-office-365-data/pipeline-monitoring-succeeded.png) 

Teď přejděte na cíl úložiště objektů Blob v Azure a ověřte, že data Office 365 extrahovali ve formátu JSON.

## <a name="next-steps"></a>Další postup

Přejděte k další informace o podpoře Azure SQL Data Warehouse v následujícím článku: 

> [!div class="nextstepaction"]
>[Konektor Office 365](connector-office-365.md)
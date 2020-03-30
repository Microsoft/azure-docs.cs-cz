---
title: Načítání dat z Office 365 pomocí Azure Data Factory
description: Kopírování dat z Office 365 pomocí Azure Data Factory
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: jingwang
ms.openlocfilehash: cd2b530375beb3e8fa1f79e004f4f1ac7fd4d0bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443914"
---
# <a name="load-data-from-office-365-by-using-azure-data-factory"></a>Načítání dat z Office 365 pomocí Azure Data Factory

Tento článek ukazuje, jak používat data načítání Datové továrny _z Office 365 do úložiště objektů blob Azure_. Můžete postupovat podle podobných kroků ke kopírování dat do Azure Data Lake Gen1 nebo Gen2. Článek o kopírování dat z Office 365 obecně najdete článek o [konektoru Office](connector-office-365.md) 365.

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. V levé nabídce vyberte Vytvořit**datovou továrnu****Analytics** >  **:** >  
   
   ![Výběr datové továrny v podokně Nový](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Na stránce **Nová továrna dat** zadejte hodnoty pro pole, která jsou zobrazena na následujícím obrázku:
      
   ![Stránka Nová datová továrna](./media/load-office-365-data/new-azure-data-factory.png)
 
    * **Název:** Zadejte globálně jedinečný název pro vaši datovou továrnu Azure. Pokud se zobrazí chyba "Název datové továrny *LoadFromOffice365Demo* není k dispozici", zadejte jiný název pro datové továrny. Můžete například použít název _**yourname**_**LoadFromOffice365Demo**. Zkuste znovu vytvořit datovou továrnu. Pravidla pojmenování artefaktů služby Data Factory najdete v tématu [Data Factory – pravidla pojmenování](naming-rules.md).
    * **Předplatné:** Vyberte předplatné Azure, ve kterém chcete vytvořit datové továrny. 
    * **Skupina prostředků**: V rozevíracím seznamu vyberte existující skupinu prostředků nebo vyberte možnost **Vytvořit nový** a zadejte název skupiny prostředků. Informace o skupinách prostředků najdete v článku [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/management/overview.md).  
    * **Verze**: Vyberte **V2**.
    * **Umístění**: Vyberte umístění pro datové továrny. V rozevíracím seznamu se zobrazí pouze podporovaná umístění. Úložiště dat, které jsou používány factory může být v jiných umístěních a oblastech. Mezi tato úložiště dat patří Azure Data Lake Store, Azure Storage, Azure SQL Database a tak dále.

3. Vyberte **Vytvořit**.
4. Po dokončení vytváření přejděte do datové továrny. Zobrazí se domovská stránka **Data Factory,** jak je znázorněno na následujícím obrázku:
   
   ![Domovská stránka objektu pro vytváření dat](./media/load-office-365-data/data-factory-home-page.png)

5. Výběrem dlaždice **Author & Monitor** (Vytvořit a monitorovat) otevřete na samostatné kartě aplikaci pro integraci dat.

## <a name="create-a-pipeline"></a>Vytvoření kanálu

1. Na stránce "Začínáme" vyberte **Vytvořit kanál**.
 
    ![Vytvoření kanálu](./media/load-office-365-data/create-pipeline-entry.png)

2. Na **kartě Obecné** pro kanál zadejte "CopyPipeline" pro **název** kanálu.

3. V poli Nástroje aktivity > Kategorie Přesunout & Transformace > přetáhnout **aktivitu Kopírování** z panelu nástrojů na povrch návrháře kanálu. Zadejte jako název aktivity "CopyFromOffice365ToBlob".

### <a name="configure-source"></a>Konfigurace zdroje

1. Přejděte na **kartu**> kanálu Zdroj , kliknutím na **+ Nový** vytvořte zdrojovou datovou sadu. 

2. V okně Nová datová sada vyberte **Office 365**a pak vyberte **Pokračovat**.
 
3. Nyní se nacházíte na kartě konfigurace **Edit** aktivity kopírování.

    ![Konfigurace datové sady Office 365 obecně](./media/load-office-365-data/transition-to-edit-dataset.png)
 
4. Zobrazí se nová karta otevřená pro datovou sadu Office 365. Na **kartě Obecné** v dolní části okna Vlastnosti zadejte pro Název hodnotu SourceOffice365Dataset.
 
5. Přejděte na **kartu Připojení** v okně Vlastnosti. Vedle textového pole Propojená služba klikněte na **+ Nový**.

6. V okně Nová propojená služba zadejte jako název "Office365LinkedService", zadejte ID instančního objektu a klíč instančního objektu služby, pak otestujte připojení a vyberte **Vytvořit** pro nasazení propojené služby.

    ![Nová propojená služba Office 365](./media/load-office-365-data/new-office-365-linked-service.png)
 
7. Po vytvoření propojené služby se vrátíte do nastavení datové sady. Vedle **tabulky**zvolte šipku dolů, chcete-li rozbalit seznam dostupných datových sad Office 365, a zvolte "BasicDataSet_v0." Message_v0" z rozevíracího seznamu:

    ![Konfigurace tabulky datových sad Office 365](./media/load-office-365-data/edit-dataset.png)

8. Teď se vraťte na**kartu Zdroj** **kanálu** > a pokračujte v konfiguraci dalších vlastností pro extrakci dat Office 365.  Obor uživatele a filtr uživatelského oboru jsou volitelné predikáty, které můžete definovat k omezení dat, která chcete extrahovat z Office 365. Jak tato nastavení nakonfigurujete, najdete v části [Vlastnosti datové sady Office 365.](https://docs.microsoft.com/azure/data-factory/connector-office-365#dataset-properties)

9. Musíte zvolit jeden z filtrů data a zadat hodnoty počátečního a koncového času.

10. Kliknutím na kartu **Importovat schéma** importujte schéma datové sady Zprávy.

    ![Konfigurační schéma datové sady Office 365](./media/load-office-365-data/edit-source-properties.png)

### <a name="configure-sink"></a>Konfigurace jímky

1. Přejděte na kartu potrubí > **jímky**a vyberte **+ Nový,** chcete-li vytvořit datovou sadu jímky.
 
2. V okně Nová datová sada si všimněte, že při kopírování z Office 365 jsou vybrány pouze podporované cíle. Vyberte **Azure Blob Storage**, vyberte Binární formát a pak vyberte **Pokračovat**.  V tomto kurzu zkopírujete data Office 365 do úložiště objektů blob Azure.

3. Kliknutím na **tlačítko Upravit** vedle datové sady Azure Blob Storage pokračujte v konfiguraci dat.

4. Na **kartě Obecné** v okně Vlastnosti zadejte do pole Název "OutputBlobDataset".

5. Přejděte na **kartu Připojení** v okně Vlastnosti. Vyberte **+ Nová** vedle textového pole Propojená služba.

6. V okně Nová propojená služba zadejte jako název "AzureStorageLinkedService", v rozevíracím seznamu metod ověřování vyberte "Instanční objekt", vyplňte koncový bod služby, tenanta, ID instančního objektu a hlavní ho klíče služby a pak vyberte Uložit do propojené služby.  Tady [najdete,](connector-azure-blob-storage.md#service-principal-authentication) jak nastavit ověřování instančního objektu pro Azure Blob Storage.

    ![Nová propojená služba objektů blob](./media/load-office-365-data/configure-blob-linked-service.png)


## <a name="validate-the-pipeline"></a>Ověření kanálu

Vyberte **Ověřit** z panelu nástrojů a kanál ověřte.

Můžete také zobrazit kód JSON přidružený k kanálu kliknutím na kód v pravém horním.

## <a name="publish-the-pipeline"></a>Publikování kanálu

V horním panelu nástrojů vyberte **Publikovat vše**. Touto akcí publikujete vytvořené entity (datové sady a kanály) do služby Data Factory.

![Publikování změn](./media/load-office-365-data/publish-changes.png) 

## <a name="trigger-the-pipeline-manually"></a>Ruční aktivace kanálu

Na panelu nástrojů vyberte **Přidat aktivační událost** a pak vyberte **Aktivovat nyní**. Na stránce Spuštění kanálu vyberte **Dokončit**. 

## <a name="monitor-the-pipeline"></a>Monitorování kanálu

Vlevo přejděte na kartu **Monitorování**. Zobrazí se stav ručně aktivovaného spuštění kanálu. Pomocí odkazů ve sloupci **Akce** můžete zobrazit podrobnosti o aktivitě a spustit kanál znovu.

![Monitorování kanálu](./media/load-office-365-data/pipeline-status.png) 

Pokud se chcete podívat na spuštění aktivit, která souvisí se spuštěním kanálu, vyberte odkaz **Zobrazit spuštění aktivit** ve sloupci Akce. Tento příklad obsahuje pouze jednu aktivitu, takže se v seznamu zobrazí pouze jedna položka. Podrobnosti o operaci kopírování zobrazíte výběrem odkazu **Podrobnosti** (ikona brýlí) ve sloupci Akce.

![Sledovat aktivitu](./media/load-office-365-data/activity-status.png) 

Pokud je to poprvé, kdy požadujete data pro tento kontext (kombinace, která tabulka dat je přístup, který cílový účet je data, která jsou načítána do a která identita uživatele provádí žádost o přístup k datům), zobrazí se stav aktivity kopírování jako **Probíhá**a pouze po klepnutí na odkaz Podrobnosti v části Akce se zobrazí stav **RequesetingConsent**.  Člen skupiny schvalovatel přístupu k datům musí schválit požadavek v privilegovaném přístupu managementu před extrakce dat může pokračovat.

_Stav jako žádost o souhlas:_
![Podrobnosti o spuštění aktivity - žádost o souhlas](./media/load-office-365-data/activity-details-request-consent.png) 

_Stav jako extrahování dat:_

![Podrobnosti spuštění aktivity – extrahování dat](./media/load-office-365-data/activity-details-extract-data.png) 

Jakmile je souhlas poskytnut, extrakce dat bude pokračovat a po určité době se spuštění kanálu zobrazí jako úspěšné.

![Kanál monitoru - úspěšné](./media/load-office-365-data/pipeline-monitoring-succeeded.png) 

Teď přejděte na cílové úložiště objektů blob Azure a ověřte, že data Office 365 byla extrahována v binárním formátu.

## <a name="next-steps"></a>Další kroky

Přejdete k následujícímu článku, kde se dozvíte o podpoře Datového skladu Azure SQL: 

> [!div class="nextstepaction"]
>[Konektor Office 365](connector-office-365.md)
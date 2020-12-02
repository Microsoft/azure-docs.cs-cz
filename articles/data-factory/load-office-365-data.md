---
title: Načtení dat ze sady Office 365 pomocí Azure Data Factory
description: Použití Azure Data Factory ke kopírování dat z Office 365
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
ms.openlocfilehash: 05f336af62bc0869249d5b32700ea3515ac5994f
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96459705"
---
# <a name="load-data-from-office-365-by-using-azure-data-factory"></a>Načtení dat ze sady Office 365 pomocí Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

V tomto článku se dozvíte, jak pomocí Data Factory _načíst data z Office 365 do úložiště objektů BLOB v Azure_. Pomocí podobných kroků můžete zkopírovat data do Azure Data Lake Gen1 nebo Gen2. V [článku konektor office 365](connector-office-365.md) najdete obecné informace o kopírování dat z Office 365.

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. V nabídce vlevo vyberte **vytvořit**  >  **Analytics**  >  **Data Factory** analýzy prostředků: 
   
   ![Výběr datové továrny v podokně Nový](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Na stránce **Nová datová továrna** zadejte hodnoty pro pole, která jsou zobrazena na následujícím obrázku:
      
   ![Stránka Nová datová továrna](./media/load-office-365-data/new-azure-data-factory.png)
 
    * **Název**: zadejte globálně jedinečný název pro objekt pro vytváření dat Azure. Pokud se zobrazí chyba "název objektu pro vytváření dat *LoadFromOffice365Demo* není k dispozici", zadejte jiný název pro datovou továrnu. Můžete například použít název _**Your**_**LoadFromOffice365Demo**. Zkuste vytvořit datovou továrnu znovu. Pravidla pojmenování artefaktů služby Data Factory najdete v tématu [Data Factory – pravidla pojmenování](naming-rules.md).
    * **Předplatné**: vyberte předplatné Azure, ve kterém chcete vytvořit datovou továrnu. 
    * **Skupina prostředků**: v rozevíracím seznamu vyberte existující skupinu prostředků nebo vyberte možnost **vytvořit novou** a zadejte název skupiny prostředků. Informace o skupinách prostředků najdete v článku [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/management/overview.md).  
    * **Verze**: Vyberte **V2**.
    * **Umístění**: vyberte umístění pro datovou továrnu. V rozevíracím seznamu se zobrazí pouze podporovaná umístění. Úložiště dat, která služba Data Factory používá, můžou být v jiných umístěních a oblastech. Mezi Tato úložiště dat patří Azure Data Lake Store, Azure Storage, Azure SQL Database a tak dále.

3. Vyberte **Vytvořit**.
4. Až se vytváření dokončí, přejdete do vaší datové továrny. Zobrazí se Domovská stránka **Data Factory** , jak je znázorněno na následujícím obrázku:
   
   ![Domovská stránka objektu pro vytváření dat](./media/load-office-365-data/data-factory-home-page.png)

5. Výběrem dlaždice **Author & Monitor** (Vytvořit a monitorovat) otevřete na samostatné kartě aplikaci pro integraci dat.

## <a name="create-a-pipeline"></a>Vytvoření kanálu

1. Na stránce Začínáme vyberte **vytvořit kanál**.
 
    ![Vytvoření kanálu](./media/load-office-365-data/create-pipeline-entry.png)

2. Na **kartě Obecné** pro kanál zadejte **název** kanálu "CopyPipeline".

3. V poli nástroje aktivity > přesunout & transformovat kategorii > přetáhněte **aktivitu kopírování** z panelu nástrojů na plochu návrháře kanálu. Jako název aktivity zadejte "CopyFromOffice365ToBlob".

### <a name="configure-source"></a>Konfigurace zdroje

1. Přejděte na **kartu zdroj**> kanálu a kliknutím na **+ Nový** vytvořte zdrojovou datovou sadu. 

2. V okně Nová datová sada vyberte možnost **Office 365** a potom vyberte **pokračovat**.
 
3. Nyní jste na kartě Konfigurace aktivity kopírování. Pokud chcete pokračovat v konfiguraci dat, klikněte na tlačítko **Upravit** vedle datové sady Office 365.

    ![Obecná konfigurace Office 365 datová sada](./media/load-office-365-data/transition-to-edit-dataset.png)
 
4. Zobrazí se nová karta, která je otevřená pro sadu Office 365 DataSet. Na **kartě Obecné** v dolní části okno vlastnosti jako název zadejte "SourceOffice365Dataset".
 
5. Přejít na **kartu připojení** okno Vlastnosti. Vedle textového pole propojená služba klikněte na **+ Nový**.

6. V okně Nová propojená služba jako název zadejte "Office365LinkedService", zadejte ID instančního objektu a klíč instančního objektu, pak otestujte připojení a vyberte **vytvořit** , aby se nasadila propojená služba.

    ![Nová propojená služba Office 365](./media/load-office-365-data/new-office-365-linked-service.png)
 
7. Po vytvoření propojené služby se vrátíte do nastavení datové sady. Vedle pole **tabulka** vyberte šipku dolů a rozbalte seznam dostupných datových sad sady Office 365 a vyberte možnost "BasicDataSet_v0. Message_v0 v rozevíracím seznamu:

    ![Konfigurační tabulka sady Office 365](./media/load-office-365-data/edit-dataset.png)

8. Teď se vraťte na **pipeline**  >  **kartu zdroj** kanálu a pokračujte v konfiguraci dalších vlastností pro extrakci dat Office 365.  Uživatelský rozsah a filtr oboru uživatele jsou volitelné predikáty, které můžete definovat k omezení dat, která chcete vyjmout z Office 365. Postup při konfiguraci těchto nastavení najdete v části [Vlastnosti datové sady Office 365](./connector-office-365.md#dataset-properties) .

9. Je nutné zvolit jeden z filtrů data a zadat počáteční a koncové hodnoty času.

10. Klikněte na kartu **importovat schéma** a importujte schéma pro datovou sadu zpráv.

    ![Konfigurace schématu datové sady Office 365](./media/load-office-365-data/edit-source-properties.png)

### <a name="configure-sink"></a>Konfigurace jímky

1. Pokud chcete vytvořit datovou sadu jímky, otevřete **kartu**> kanálu a vyberte **+ Nová** .
 
2. V okně Nová datová sada si všimněte, že při kopírování ze sady Office 365 jsou vybrány pouze podporované cíle. Vyberte **Azure Blob Storage**, vyberte binární formát a pak vyberte **pokračovat**.  V tomto kurzu zkopírujte data Office 365 do Blob Storage Azure.

3. Pokud chcete pokračovat v konfiguraci dat, klikněte na tlačítko **Upravit** vedle datové sady služby Azure Blob Storage.

4. Na **kartě obecné** okno Vlastnosti v poli název zadejte "OutputBlobDataset".

5. Přejít na **kartu připojení** okno Vlastnosti. Vyberte **+ Nová** vedle textového pole Propojená služba.

6. V okně Nová propojená služba zadejte "AzureStorageLinkedService" jako název, v rozevíracím seznamu metod ověřování vyberte "instanční objekt", vyplňte koncový bod služby, tenant, ID instančního objektu a klíč instančního objektu a potom vyberte Uložit a nasaďte propojenou službu.  Postup nastavení ověřování instančního objektu pro Azure Blob Storage najdete [tady](connector-azure-blob-storage.md#service-principal-authentication) .

    ![Nová propojená služba objektů BLOB](./media/load-office-365-data/configure-blob-linked-service.png)


## <a name="validate-the-pipeline"></a>Ověření kanálu

Vyberte **Ověřit** z panelu nástrojů a kanál ověřte.

Kliknutím na kód v pravém horním rohu můžete také zobrazit kód JSON přidružený k kanálu.

## <a name="publish-the-pipeline"></a>Publikování kanálu

Na horním panelu nástrojů vyberte **publikovat vše**. Touto akcí publikujete vytvořené entity (datové sady a kanály) do služby Data Factory.

![Publikování změn](./media/load-office-365-data/publish-changes.png) 

## <a name="trigger-the-pipeline-manually"></a>Ruční aktivace kanálu

Vyberte **Přidat aktivační událost** na panelu nástrojů a pak vyberte **aktivovat nyní**. Na stránce Spuštění kanálu vyberte **Dokončit**. 

## <a name="monitor-the-pipeline"></a>Monitorování kanálu

Vlevo přejděte na kartu **Monitorování**. Zobrazí se stav ručně aktivovaného spuštění kanálu. Pomocí odkazů ve sloupci **Akce** můžete zobrazit podrobnosti o aktivitě a spustit kanál znovu.

![Monitorování kanálu](./media/load-office-365-data/pipeline-status.png) 

Pokud se chcete podívat na spuštění aktivit, která souvisí se spuštěním kanálu, vyberte odkaz **Zobrazit spuštění aktivit** ve sloupci Akce. Tento příklad obsahuje pouze jednu aktivitu, takže se v seznamu zobrazí pouze jedna položka. Podrobnosti o operaci kopírování zobrazíte výběrem odkazu **Podrobnosti** (ikona brýlí) ve sloupci Akce.

![Aktivita monitorování](./media/load-office-365-data/activity-status.png) 

Pokud se jedná o data pro tento kontext poprvé (kombinace toho, ke které tabulce dat se přistupuje, na jehož cílovém účtu se načítají data a jakou identitu uživatele vytváří požadavek na přístup k datům), zobrazí se stav aktivity kopírování jako probíhající a jenom když kliknete na odkaz Podrobnosti v části **akce, zobrazí** se stav jako **RequesetingConsent**.  Člen skupiny schvalovatelů přístupu k datům musí schválit žádost v Privileged Access Management předtím, než může pokračovat v extrakci dat.

_Stav jako požadavek souhlasu:_ 
 ![ Podrobnosti spuštění aktivity – souhlas s žádostí](./media/load-office-365-data/activity-details-request-consent.png) 

_Stav při extrakci dat:_

![Podrobnosti o spuštění aktivity – extrakce dat](./media/load-office-365-data/activity-details-extract-data.png) 

Po zadání souhlasu bude extrakce dat pokračovat a po nějaké době se spuštění kanálu zobrazí jako úspěšné.

![Monitorování kanálu – úspěšné](./media/load-office-365-data/pipeline-monitoring-succeeded.png) 

Nyní přejdete do cílového Azure Blob Storage a ověříte, že data Office 365 byla extrahována v binárním formátu.

## <a name="next-steps"></a>Další kroky

V následujícím článku se dozvíte o podpoře Azure synapse Analytics: 

> [!div class="nextstepaction"]
>[Konektor Office 365](connector-office-365.md)